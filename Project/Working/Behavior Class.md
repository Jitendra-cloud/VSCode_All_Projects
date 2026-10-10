# ABAP RAP Behavior Handler – PayBlock

## Class Definition

```abap
CLASS lhc_PayBlock DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.

    METHODS get_instance_authorizations FOR INSTANCE AUTHORIZATION
      IMPORTING keys REQUEST requested_authorizations FOR PayBlock RESULT result.

    METHODS pbf FOR MODIFY
      IMPORTING keys FOR ACTION PayBlock~pbf.

    METHODS unblock FOR MODIFY
      IMPORTING keys FOR ACTION PayBlock~unblock RESULT result.

    METHODS process FOR MODIFY
      IMPORTING keys FOR ACTION PayBlock~process.

ENDCLASS.
```

## Class Implementation

```abap
CLASS lhc_PayBlock IMPLEMENTATION.

  METHOD get_instance_authorizations.
  ENDMETHOD.

  METHOD pbf.
    MODIFY ENTITIES OF ZPB_I_2A_PAY_BLOCK_LY4 IN LOCAL MODE
      ENTITY PayBlock
      UPDATE FIELDS ( p_block )
      WITH VALUE #(
        FOR key IN keys
        (
          %tky = key-%param
          p_block = COND abap_boolean(
            WHEN key-%param-pbf = abap_true
            THEN abap_true
            ELSE abap_false
          )
          %control-p_block = if_abap_behv=>mk-on
        )
      )
      FAILED failed
      REPORTED reported.
  ENDMETHOD.

  METHOD unblock.
  ENDMETHOD.

  METHOD process.

    DATA lo_processor TYPE REF TO zcl_pb_processor.

*   Authorization check (currently commented out)
*
*   AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA'
*     ID 'ACTVT' FIELD '01'.
*
*   IF sy-subrc <> 0.
*     AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA'
*       ID 'ACTVT' FIELD '02'.
*   ENDIF.
*
*   IF sy-subrc <> 0.
*     LOOP AT keys ASSIGNING FIELD-SYMBOL(<key>).
*
*       APPEND VALUE #(
*         %tky = <key>-%cid
*       ) TO failed-payblock.
*
*       APPEND VALUE #(
*         %tky = <key>-%cid
*         %msg = new_message(
*           id       = 'ZPB'
*           number   = '001'
*           severity = if_abap_behv_message=>severity-error
*           v1       = 'You are not Authorized for Process for Block/Unblock'
*         )
*       ) TO reported-payblock.
*
*     ENDLOOP.
*
*     RETURN.
*   ENDIF.

*--------------------------------------------------------------------*
* Read selected PayBlock records
*--------------------------------------------------------------------*

    READ ENTITIES OF zpb_i_2a_pay_block_ly4 IN LOCAL MODE
      ENTITY PayBlock
      ALL FIELDS WITH CORRESPONDING #( keys )
      RESULT DATA(lt_data)
      FAILED DATA(lt_failed)
      REPORTED DATA(lt_reported).

*--------------------------------------------------------------------*
* Read action parameters and initialize processor
*--------------------------------------------------------------------*

    DATA(ls_param) = keys[ 1 ]-%param.

    lo_processor = NEW zcl_pb_processor(
      iv_p_pbf    = ls_param-pbf
      iv_p_jv     = ls_param-jv
      iv_p_dbf    = ls_param-dbf
      iv_p_daf    = ls_param-dbf              " Change: currently uses DBF
      iv_p_dbf_cl = ls_param-dbf_cl
      iv_p_dbf_op = ls_param-dbf_op
      iv_p_daf_cl = ls_param-daf_cl
      iv_p_daf_op = ls_param-daf_op
    ).

*--------------------------------------------------------------------*
* Process selected documents
*--------------------------------------------------------------------*

    LOOP AT lt_data ASSIGNING FIELD-SYMBOL(<data>).

      DATA(ls_data) = CORRESPONDING zpb_i_2a_pay_block_ly4( <data> ).

      lo_processor->process(
        CHANGING
          cs_data = ls_data
      ).

*--------------------------------------------------------------------*
* Update RAP entity
*--------------------------------------------------------------------*

      MODIFY ENTITIES OF zpb_i_2a_pay_block_ly4 IN LOCAL MODE
        ENTITY PayBlock
        UPDATE FIELDS (
          status
          message
          releasecomment
          releasedon
          releasedat
          releasedby
          jvrevcompcode
          jvrevdocnum
          jvrevyear
          jvrevcomment
          jvreversedon
          jvreversedat
          jvreversedby
          splityear
        )
        WITH VALUE #(
          (
            %tky = <data>-%tky

            status          = <data>-status
            message         = <data>-message
            releasecomment  = <data>-releasecomment
            releasedon      = <data>-releasedon
            releasedat      = <data>-releasedat
            releasedby      = <data>-releasedby

            jvrevcompcode   = <data>-jvrevcompcode
            jvrevdocnum     = <data>-jvrevdocnum
            jvrevyear       = <data>-jvrevyear
            jvrevcomment    = <data>-jvrevcomment
            jvreversedon    = <data>-jvreversedon
            jvreversedat    = <data>-jvreversedat
            jvreversedby    = <data>-jvreversedby

            splityear       = <data>-splityear
          )
        ).

*--------------------------------------------------------------------*
* Return result (currently commented out)
*--------------------------------------------------------------------*

*     APPEND VALUE #(
*       %tky = <data>-%tky
*     ) TO result.

    ENDLOOP.

  ENDMETHOD.

ENDCLASS.
```

## Notes

- The code has been formatted for Markdown readability; the ABAP logic has been preserved.
- In `process`, `iv_p_daf` currently receives `ls_param-dbf`, as in the supplied code. Confirm whether this should instead be `ls_param-daf`.
- The `pbf` action assigns `%tky = key-%param`. Confirm that the action parameter structure is intended to contain the entity's technical key in `%param`; in many RAP action implementations, the entity key is taken from the action key structure.
- `keys[ 1 ]-%param` assumes at least one action key is present. If the action can be called with an empty key table, add a guard before accessing index 1.
- `unblock` and `get_instance_authorizations` are currently empty methods.
