# ABAP Class: `ZCL_PB_PROCESSOR`

This document contains the formatted ABAP source provided for `ZCL_PB_PROCESSOR`.

## Class Definition

```abap
CLASS zcl_pb_processor DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC.

  PUBLIC SECTION.

    METHODS constructor
      IMPORTING
        iv_p_pbf    TYPE abap_boolean
        iv_p_jv     TYPE abap_boolean
        iv_p_dbf    TYPE abap_boolean
        iv_p_daf    TYPE abap_boolean
        iv_p_dbf_cl TYPE abap_boolean
        iv_p_dbf_op TYPE abap_boolean
        iv_p_daf_cl TYPE abap_boolean
        iv_p_daf_op TYPE abap_boolean
        iv_p_block  TYPE abap_boolean OPTIONAL
        iv_p_split  TYPE abap_boolean OPTIONAL
        iv_p_deb_n  TYPE abap_boolean OPTIONAL.

    METHODS process
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4.

  PROTECTED SECTION.

  PRIVATE SECTION.

    CONSTANTS:
      BEGIN OF mcs_doc_status,
        unprocessed             TYPE z2a_t_pay_block-status VALUE '',
        blocked                 TYPE z2a_t_pay_block-status VALUE 'B',
        split                   TYPE z2a_t_pay_block-status VALUE 'S',
        released                TYPE z2a_t_pay_block-status VALUE 'R',
        ireleased               TYPE z2a_t_pay_block-status VALUE 'I',
        matched                 TYPE z2a_t_pay_block-status VALUE 'M',
        exempted                TYPE z2a_t_pay_block-status VALUE 'X',
        waiting_for_filing_date TYPE z2a_t_pay_block-status VALUE 'W',
        debit_note_posted       TYPE z2a_t_pay_block-status VALUE 'D',
      END OF mcs_doc_status,

      BEGIN OF mcs_vendor_block_type,
        exempted TYPE z2a_t_pay_block-vendor_block_type VALUE 'X',
        block    TYPE z2a_t_pay_block-vendor_block_type VALUE 'B',
        split    TYPE z2a_t_pay_block-vendor_block_type VALUE 'S',
      END OF mcs_vendor_block_type,

      BEGIN OF mcs_yes_no,
        yes TYPE z2a_t_pay_block-due_date_before_filing VALUE 'Y',
        no  TYPE z2a_t_pay_block-due_date_before_filing VALUE 'N',
      END OF mcs_yes_no.

    DATA:
      lt_grc TYPE STANDARD TABLE OF zgrc_control,
      ls_grc TYPE zgrc_control,
      grc    TYPE i.

    DATA:
      mrt_matched    TYPE RANGE OF zpb_i_2a_pay_block_ly4-reconciliationsection,
      mrt_mismatched TYPE RANGE OF zpb_i_2a_pay_block_ly4-reconciliationsection,
      mrt_exempted   TYPE RANGE OF zpb_i_2a_pay_block_ly4-reconciliationsection,
      lv_mess        TYPE c LENGTH 200.

    DATA:
      mv_p_pbf    TYPE abap_boolean,
      mv_p_jv     TYPE abap_boolean,
      mv_p_dbf    TYPE abap_boolean,
      mv_p_daf    TYPE abap_boolean,
      mv_p_dbf_cl TYPE abap_boolean,
      mv_p_dbf_op TYPE abap_boolean,
      mv_p_daf_cl TYPE abap_boolean,
      mv_p_daf_op TYPE abap_boolean,
      mv_p_block  TYPE abap_boolean,
      mv_p_split  TYPE abap_boolean,
      mv_p_deb_n  TYPE abap_boolean.

    METHODS process_unprocessed
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4.

    METHODS process_blocked
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4.

    METHODS process_dbf
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4.

    METHODS process_daf
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4.

    METHODS release_document
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4
      RETURNING
        VALUE(rv_released) TYPE abap_boolean.

    METHODS process_daf_open
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4.

    METHODS process_dbf_open
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4.

    METHODS block_open_document
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4
      RETURNING
        VALUE(rv_blocked) TYPE abap_boolean.

    METHODS toggle_payment_block
      IMPORTING
        iv_doc_num   TYPE bkpf-belnr
        iv_comp_code TYPE bkpf-bukrs
        iv_fis_year  TYPE bkpf-gjahr
      EXPORTING
        ev_message TYPE string
      RETURNING
        VALUE(rv_toggled) TYPE abap_boolean.

    METHODS fill_reco_status_range.

    METHODS post_debit_note
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4
      RETURNING
        VALUE(rv_posted) TYPE abap_boolean.

    METHODS perform_grc_check
      IMPORTING
        iv_vendor TYPE lifnr
      EXPORTING
        ev_score   TYPE zcy_grc_score-gstr1_score
        ev_message TYPE string
      RETURNING
        VALUE(rv_result) TYPE char1.

    METHODS get_log
      EXPORTING
        ev_date TYPE sydatum
        ev_time TYPE syuzeit
        ev_user TYPE string.

    METHODS post_jv
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4
      RETURNING
        VALUE(rv_posted) TYPE abap_boolean.

    METHODS reverse_jv
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4
      RETURNING
        VALUE(rv_reversed) TYPE abap_boolean.

    METHODS reverse_debit_note
      CHANGING
        cs_data TYPE zpb_i_2a_pay_block_ly4
      RETURNING
        VALUE(rv_reversed) TYPE abap_boolean.

    METHODS is_document_open
      IMPORTING
        iv_doc_num   TYPE bkpf-belnr
        iv_comp_code TYPE bkpf-bukrs
        iv_fis_year  TYPE bkpf-gjahr
      RETURNING
        VALUE(rv_open) TYPE abap_boolean.

    METHODS reverse_document
      IMPORTING
        iv_doc_num   TYPE bkpf-belnr
        iv_comp_code TYPE bkpf-bukrs
        iv_fis_year  TYPE bkpf-gjahr
      EXPORTING
        ev_reversed_doc TYPE bkpf-belnr
        ev_fis_year     TYPE bkpf-gjahr
        ev_message      TYPE string
      RETURNING
        VALUE(rv_reversed) TYPE abap_boolean.

    METHODS check_document_posted
      IMPORTING
        iv_doc_num   TYPE bkpf-belnr
        iv_comp_code TYPE bkpf-bukrs
        iv_fis_year  TYPE bkpf-gjahr
      RETURNING
        VALUE(rv_posted) TYPE abap_boolean.

    METHODS get_fiscal_year
      IMPORTING
        iv_bukrs TYPE bukrs
        iv_budat TYPE budat
      RETURNING
        VALUE(rv_gjahr) TYPE gjahr.

    METHODS process_bapi_return
      IMPORTING
        it_return TYPE bapiret2_t
      EXPORTING
        ev_message TYPE string
      RETURNING
        VALUE(rv_success) TYPE abap_boolean.

    METHODS is_document_in_bsik
      IMPORTING
        iv_bukrs TYPE bukrs
        iv_belnr TYPE belnr_d
        iv_gjahr TYPE gjahr
      RETURNING
        VALUE(rv_exists) TYPE abap_boolean.

ENDCLASS.
```

## Class Implementation

```abap
CLASS zcl_pb_processor IMPLEMENTATION.

  METHOD constructor.

    mv_p_pbf    = iv_p_pbf.
    mv_p_jv     = iv_p_jv.
    mv_p_dbf    = iv_p_dbf.
    mv_p_daf    = iv_p_daf.
    mv_p_dbf_cl = iv_p_dbf_cl.
    mv_p_dbf_op = iv_p_dbf_op.
    mv_p_daf_cl = iv_p_daf_cl.
    mv_p_daf_op = iv_p_daf_op.

    mv_p_block = iv_p_block.
    mv_p_split = iv_p_split.
    mv_p_deb_n = iv_p_deb_n.

    fill_reco_status_range( ).

  ENDMETHOD.

  METHOD process.

    CASE cs_data-status.
      WHEN mcs_doc_status-unprocessed
        OR mcs_doc_status-waiting_for_filing_date
        OR mcs_doc_status-exempted.

        process_unprocessed(
          CHANGING
            cs_data = cs_data
        ).

      WHEN mcs_doc_status-blocked
        OR mcs_doc_status-split
        OR mcs_doc_status-debit_note_posted.

        process_blocked(
          CHANGING
            cs_data = cs_data
        ).

      WHEN mcs_doc_status-matched
        OR mcs_doc_status-released.
        " No processing required for these statuses.

      WHEN OTHERS.
        cs_data-message = 'Document status is not valid for processing'.
    ENDCASE.

  ENDMETHOD.

  METHOD process_blocked.

    " Release may mean:
    " 1. Unblock in case of B or S.
    " 2. Reverse in case of JV (with B or S) and D.

    DATA:
      lv_released TYPE abap_boolean,
      ls_bsik     TYPE bsik.

    " What if the vendor becomes exempted after the document is blocked/split?
    IF cs_data-vendorblocktype = mcs_vendor_block_type-exempted.

      lv_released = release_document(
        CHANGING
          cs_data = cs_data
      ).

    ELSE.

      " Document follows the normal blocked/split/debit-note-posted flow.
      IF to_upper( cs_data-reconciliationsection ) IN mrt_matched[].

        lv_released = release_document(
          CHANGING
            cs_data = cs_data
        ).

        IF is_document_in_bsik(
             iv_bukrs = CONV #( cs_data-custom9 )
             iv_belnr = CONV #( cs_data-custom1 )
             iv_gjahr = CONV #( cs_data-custom4 )
           ).

          " Document exists in the relevant open-item check.

        ELSE.

          reverse_debit_note(
            CHANGING
              cs_data = cs_data
            RECEIVING
              rv_reversed = DATA(lv_reversed)
          ).

          IF lv_reversed = abap_true.

            get_log(
              IMPORTING
                ev_date = cs_data-debitnotereversedon
                ev_time = cs_data-debitnotereversedat
                ev_user = DATA(lv_user)
            ).

            cs_data-debitnotereversedby = lv_user.

          ELSE.

            cs_data-releasecomment = cs_data-debitnoterevcomment.

          ENDIF.

        ENDIF.
      ENDIF.
    ENDIF.

  ENDMETHOD.

  METHOD is_document_in_bsik.

    rv_exists = abap_false.

    SELECT SINGLE belnr
      FROM bsak
      WHERE bukrs = @iv_bukrs
        AND belnr = @iv_belnr
        AND gjahr = @iv_gjahr
        AND zlspr <> 'Y'
      INTO @DATA(lv_belnr).

    IF sy-subrc = 0.
      rv_exists = abap_true.
    ENDIF.

  ENDMETHOD.

  METHOD process_daf.

    IF cs_data-ClearingDate IS NOT INITIAL.

      IF mv_p_daf_cl = abap_true.
        process_daf_open(
          CHANGING
            cs_data = cs_data
        ).
      ENDIF.

    ELSE.

      IF mv_p_daf_op = abap_true.
        process_daf_open(
          CHANGING
            cs_data = cs_data
        ).
      ENDIF.

    ENDIF.

  ENDMETHOD.

  METHOD process_dbf.

    IF cs_data-clearingdate IS NOT INITIAL.

      IF mv_p_dbf_cl = abap_true.
        process_dbf_open(
          CHANGING
            cs_data = cs_data
        ).
      ENDIF.

    ELSE.

      IF mv_p_dbf_op = abap_true.
        process_dbf_open(
          CHANGING
            cs_data = cs_data
        ).
      ENDIF.

    ENDIF.

  ENDMETHOD.

  METHOD process_unprocessed.

    IF cs_data-VendorBlockType <> mcs_vendor_block_type-exempted
       AND cs_data-status = mcs_doc_status-exempted.

      cs_data-status = mcs_doc_status-unprocessed.

    ELSEIF cs_data-status = mcs_doc_status-exempted.

      RETURN.

    ENDIF.

    IF cs_data-DueDateBeforeFiling = mcs_yes_no-yes.

      IF mv_p_dbf = abap_true.
        process_dbf(
          CHANGING
            cs_data = cs_data
        ).
      ENDIF.

    ELSE.

      IF mv_p_daf = abap_true.
        process_daf(
          CHANGING
            cs_data = cs_data
        ).
      ENDIF.

    ENDIF.

  ENDMETHOD.

  METHOD release_document.

    CLEAR rv_released.

    CASE cs_data-status.

      WHEN mcs_doc_status-blocked.

        toggle_payment_block(
          EXPORTING
            iv_doc_num   = |{ CONV bkpf-belnr( cs_data-custom1 ) ALPHA = IN }|
            iv_comp_code = CONV #( cs_data-custom9 )
            iv_fis_year  = CONV #( cs_data-custom4 )
          IMPORTING
            ev_message   = DATA(lv_message)
          RECEIVING
            rv_toggled   = rv_released
        ).

        cs_data-ReleaseComment = lv_message.

      WHEN mcs_doc_status-split.

        toggle_payment_block(
          EXPORTING
            iv_doc_num   = |{ cs_data-DebitNoteNum ALPHA = IN }|
            iv_comp_code = cs_data-SplitCompCode
            iv_fis_year  = cs_data-splityear
          IMPORTING
            ev_message   = lv_message
          RECEIVING
            rv_toggled   = rv_released
        ).

        cs_data-ReleaseComment = lv_message.

      WHEN mcs_doc_status-debit_note_posted.

        toggle_payment_block(
          EXPORTING
            iv_doc_num   = |{ cs_data-DebitNoteNum ALPHA = IN }|
            iv_comp_code = cs_data-debitnotecomp
            iv_fis_year  = cs_data-debitnoteyear
          IMPORTING
            ev_message   = lv_message
          RECEIVING
            rv_toggled   = rv_released
        ).

        cs_data-ReleaseComment = lv_message.

    ENDCASE.

    IF rv_released = abap_true.
      cs_data-status  = mcs_doc_status-released.
      cs_data-message = TEXT-014.
    ELSE.
      cs_data-message = TEXT-013.
    ENDIF.

  ENDMETHOD.

  METHOD fill_reco_status_range.

    CLEAR:
      mrt_matched[],
      mrt_mismatched[],
      mrt_exempted[].

    " Fill status ranges from customizing.
    SELECT 'I' AS sign,
           'EQ' AS option,
           reco_status AS low
      FROM zcy_t_2a_recstat
      WHERE category = 'M'
      INTO TABLE @mrt_matched[].

    LOOP AT mrt_matched ASSIGNING FIELD-SYMBOL(<ls_matched>).
      <ls_matched>-low = to_upper( <ls_matched>-low ).
    ENDLOOP.

    SELECT 'I' AS sign,
           'EQ' AS option,
           reco_status AS low
      FROM zcy_t_2a_recstat
      WHERE category = 'B'
      INTO TABLE @mrt_mismatched[].

    LOOP AT mrt_mismatched ASSIGNING FIELD-SYMBOL(<ls_mismatched>).
      <ls_mismatched>-low = to_upper( <ls_mismatched>-low ).
    ENDLOOP.

    SELECT 'I' AS sign,
           'EQ' AS option,
           reco_status AS low
      FROM zcy_t_2a_recstat
      WHERE category = 'X'
      INTO TABLE @mrt_exempted[].

    LOOP AT mrt_exempted ASSIGNING FIELD-SYMBOL(<ls_exempted>).
      <ls_exempted>-low = to_upper( <ls_exempted>-low ).
    ENDLOOP.

    IF mrt_matched[] IS INITIAL OR mrt_mismatched[] IS INITIAL.
*     RAISE EXCEPTION TYPE lcx_generic
*       MESSAGE ID '00' TYPE mcs_msg_type-error NUMBER '001'
*       WITH TEXT-009.
      RETURN.
    ENDIF.

  ENDMETHOD.

  METHOD process_dbf_open.

    IF sy-datum >= cs_data-expectedfilingdate
       OR cs_data-filingdate IS NOT INITIAL
       OR mv_p_pbf = abap_true
       OR cs_data-qblock IS NOT INITIAL.

      IF to_upper( cs_data-reconciliationsection ) IN mrt_mismatched[].

        DATA(lv_blocked) = block_open_document(
          CHANGING
            cs_data = cs_data
        ).

      ENDIF.

      IF to_upper( cs_data-reconciliationsection ) IN mrt_matched[].
        cs_data-status = mcs_doc_status-matched.
      ENDIF.

    ELSE.

      " Filing date is in the future.
      IF to_upper( cs_data-reconciliationsection ) IN mrt_matched[].

        cs_data-status = mcs_doc_status-matched.

      ELSE.

        DATA(due_check) = sy-datum.
        due_check = sy-datum + 1.

        IF cs_data-grccheckapplicable = mcs_yes_no-yes.

          DATA:
            lv_grc_score   TYPE zcy_grc_score-gstr1_score,
            lv_grc_message TYPE string.

          perform_grc_check(
            EXPORTING
              iv_vendor  = cs_data-vendor
            IMPORTING
              ev_score   = lv_grc_score
              ev_message = lv_grc_message
            RECEIVING
              rv_result  = cs_data-grccheckresult
          ).

          cs_data-GrcScore        = lv_grc_score.
          cs_data-GrcCheckComment = lv_grc_message.

          get_log(
            IMPORTING
              ev_date = cs_data-grccheckedon
              ev_time = cs_data-grccheckedat
              ev_user = DATA(lv_user)
          ).

          cs_data-grccheckedby = lv_user.
          grc = cs_data-grcscore.

          CLEAR ls_grc.

          READ TABLE lt_grc INTO ls_grc INDEX 1.

          IF ( grc <= ls_grc-grc_block1 OR grc IS INITIAL )
             AND cs_data-mindk IS INITIAL.

            lv_blocked = block_open_document(
              CHANGING
                cs_data = cs_data
            ).

          ELSEIF grc > ls_grc-grc_block2
             AND grc <= ls_grc-grc_block3.

            cs_data-status = mcs_doc_status-waiting_for_filing_date.

          ELSE.

            cs_data-status = mcs_doc_status-waiting_for_filing_date.

          ENDIF.

        ELSE.

          cs_data-status = mcs_doc_status-waiting_for_filing_date.

        ENDIF.
      ENDIF.
    ENDIF.

  ENDMETHOD.

  METHOD process_daf_open.

    " Has the filing date passed?
    CLEAR ls_grc.

    IF cs_data-mindk = '6' OR cs_data-mindk = '7'.
      cs_data-qblock = 'X'.
    ENDIF.

    READ TABLE lt_grc INTO ls_grc INDEX 1.

    IF sy-datlo >= cs_data-expectedfilingdate
       OR mv_p_pbf = abap_true
       OR cs_data-qblock = 'X'.

      IF to_upper( cs_data-reconciliationsection ) IN mrt_mismatched[].

        DATA(lv_blocked) = block_open_document(
          CHANGING
            cs_data = cs_data
        ).

      ENDIF.

      IF to_upper( cs_data-reconciliationsection ) IN mrt_matched[].
        cs_data-status = mcs_doc_status-matched.
      ENDIF.

    ELSE.

      " Filing date is in the future.
      cs_data-status = mcs_doc_status-waiting_for_filing_date.

    ENDIF.

  ENDMETHOD.

  METHOD block_open_document.

    CLEAR rv_blocked.

    DATA lv_jv_applicable TYPE abap_boolean.

    CASE cs_data-vendorblocktype.

      WHEN mcs_vendor_block_type-exempted.

        cs_data-status = mcs_doc_status-exempted.

      WHEN mcs_vendor_block_type-block.

        toggle_payment_block(
          EXPORTING
            iv_doc_num   = |{ CONV bkpf-belnr( cs_data-custom1 ) ALPHA = IN }|
            iv_comp_code = CONV #( cs_data-custom9 )
            iv_fis_year  = CONV #( cs_data-custom4 )
          IMPORTING
            ev_message   = DATA(lv_block_message)
          RECEIVING
            rv_toggled   = rv_blocked
        ).

        cs_data-blockcomment = lv_block_message.

        IF rv_blocked = abap_true.

          cs_data-status = mcs_doc_status-blocked.

          get_log(
            IMPORTING
              ev_date = cs_data-blockedon
              ev_time = cs_data-blockedat
              ev_user = DATA(lv_user)
          ).

          cs_data-blockedby = lv_user.
          cs_data-message  = 'Document blocked successfully'.

          IF mv_p_jv = abap_true AND mv_p_block = abap_true.
            lv_jv_applicable = abap_true.
          ENDIF.

        ELSE.

          cs_data-message = 'Document blocking failed'.

        ENDIF.

      WHEN mcs_vendor_block_type-split.

        post_debit_note(
          CHANGING
            cs_data = cs_data
          RECEIVING
            rv_posted = DATA(lv_dn_posted)
        ).

        IF lv_dn_posted = abap_true.

          cs_data-status = mcs_doc_status-debit_note_posted.

          get_log(
            IMPORTING
              ev_date = cs_data-debitnotepostedon
              ev_time = cs_data-debitnotepostedat
              ev_user = DATA(lv_debit)
          ).

          cs_data-debitnotepostedby = lv_debit.
          cs_data-message = 'Document processed successfully'.

        ENDIF.

      WHEN OTHERS.

        cs_data-message = 'Invalid vendor block type'.

    ENDCASE.

    IF lv_jv_applicable = abap_true.

      post_jv(
        CHANGING
          cs_data = cs_data
        RECEIVING
          rv_posted = DATA(lv_jv_posted)
      ).

      IF lv_jv_posted = abap_true.

        get_log(
          IMPORTING
            ev_date = cs_data-jvpostedon
            ev_time = cs_data-jvpostedat
            ev_user = DATA(lv_post)
        ).

        cs_data-jvpostedby = lv_post.

        IF cs_data-message <> 'Document blocking failed'.
          cs_data-message = 'Document processed successfully'.
        ENDIF.

      ELSE.

        cs_data-message = 'Document processing failed'.

      ENDIF.
    ENDIF.

  ENDMETHOD.

  METHOD check_document_posted.

    rv_posted = abap_false.

    DO 10 TIMES.

      CALL FUNCTION 'ENQUE_SLEEP'
        EXPORTING
          seconds        = 1
        EXCEPTIONS
          system_failure = 1
          OTHERS         = 2.

      SELECT SINGLE @abap_true
        FROM bkpf
        WHERE bukrs = @iv_comp_code
          AND belnr = @iv_doc_num
          AND gjahr = @iv_fis_year
        INTO @rv_posted.

      IF rv_posted = abap_true.
        RETURN.
      ENDIF.

    ENDDO.

  ENDMETHOD.

  METHOD get_fiscal_year.

    CLEAR rv_gjahr.

    CALL FUNCTION 'FI_PERIOD_DETERMINE'
      EXPORTING
        i_budat        = iv_budat
        i_bukrs        = iv_bukrs
      IMPORTING
        e_gjahr        = rv_gjahr
      EXCEPTIONS
        fiscal_year    = 1
        period         = 2
        period_version = 3
        posting_period = 4
        special_period = 5
        version        = 6
        posting_date   = 7
        OTHERS         = 8.

    IF sy-subrc <> 0.
      CLEAR rv_gjahr.
    ENDIF.

  ENDMETHOD.

  METHOD is_document_open.

    rv_open = abap_false.

    SELECT SINGLE @abap_true
      FROM bseg
      WHERE bukrs = @iv_comp_code
        AND belnr = @iv_doc_num
        AND gjahr = @iv_fis_year
        AND augbl = @space
      INTO @rv_open.

  ENDMETHOD.

  METHOD toggle_payment_block.

    CONSTANTS lc_pay_block TYPE bseg-zlspr VALUE 'Q'.

    DATA:
      lt_accchg    TYPE STANDARD TABLE OF accchg,
      ls_acc_item  TYPE bseg,
      lv_new_block TYPE bseg-zlspr.

    CLEAR:
      ev_message,
      rv_toggled.

    " Read the accounting document line items.
    SELECT *
      FROM bseg
      WHERE bukrs = @iv_comp_code
        AND belnr = @iv_doc_num
        AND gjahr = @iv_fis_year
      INTO TABLE @DATA(lt_acc_items).

    IF lt_acc_items IS INITIAL.
      ev_message = |{ iv_comp_code }-{ iv_doc_num }-{ iv_fis_year } does not exist|.
      RETURN.
    ENDIF.

    " Prefer the special G/L item, as in the legacy method.
    READ TABLE lt_acc_items INTO ls_acc_item
      WITH KEY umskz = 'A'.

    " Otherwise, select the vendor credit item.
    IF sy-subrc <> 0.
      READ TABLE lt_acc_items INTO ls_acc_item
        WITH KEY koart = 'K'
                 shkzg = 'H'.
    ENDIF.

    IF ls_acc_item IS INITIAL.
      ev_message = |{ iv_comp_code }-{ iv_doc_num }-{ iv_fis_year } - Cannot find suitable item for payment block|.
      RETURN.
    ENDIF.

    " Toggle payment block Q.
    IF ls_acc_item-zlspr = lc_pay_block.
      CLEAR lv_new_block.
    ELSE.
      lv_new_block = lc_pay_block.
    ENDIF.

    lt_accchg = VALUE #(
      (
        fdname = 'ZLSPR'
        oldval = ls_acc_item-zlspr
        newval = lv_new_block
      )
    ).

    CALL FUNCTION 'FI_DOCUMENT_CHANGE'
      EXPORTING
        i_obzei = ls_acc_item-buzei
        i_bukrs = ls_acc_item-bukrs
        i_belnr = ls_acc_item-belnr
        i_gjahr = ls_acc_item-gjahr
      TABLES
        t_accchg = lt_accchg
      EXCEPTIONS
        no_reference         = 1
        no_document          = 2
        many_documents       = 3
        wrong_input          = 4
        overwrite_creditcard = 5
        error_message        = 6
        OTHERS               = 7.

    IF sy-subrc <> 0.
      MESSAGE ID sy-msgid
              TYPE sy-msgty
              NUMBER sy-msgno
              WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4
              INTO ev_message.
      RETURN.
    ENDIF.

    rv_toggled = abap_true.
    ev_message = 'Payment block updated successfully'.

  ENDMETHOD.

  METHOD post_debit_note.
    " TODO: Implementation not supplied.
  ENDMETHOD.

  METHOD perform_grc_check.
    " TODO: Implementation not supplied.
  ENDMETHOD.

  METHOD get_log.

    CLEAR:
      ev_date,
      ev_time,
      ev_user.

    GET TIME.

    ev_date = sy-datlo.
    ev_time = sy-timlo.

    SELECT SINGLE name_text
      FROM v_usr_name
      WHERE bname = @sy-uname
      INTO @DATA(lv_name).

    ev_user = COND string(
      WHEN lv_name IS NOT INITIAL
      THEN |{ sy-uname } - { lv_name }|
      ELSE sy-uname
    ).

  ENDMETHOD.

  METHOD post_jv.
    " TODO: Implementation not supplied.
  ENDMETHOD.

  METHOD process_bapi_return.

    CLEAR:
      ev_message,
      rv_success.

    IF it_return IS INITIAL.
      ev_message = 'No return messages received'.
      RETURN.
    ENDIF.

    " Check for abort messages first.
    READ TABLE it_return INTO DATA(ls_return)
      WITH KEY type = 'A'.

    IF sy-subrc = 0.
      ev_message = ls_return-message.
      RETURN.
    ENDIF.

    " Check for error messages.
    DATA(lt_errors) = it_return.
    DELETE lt_errors WHERE type <> 'E'.

    IF lt_errors IS NOT INITIAL.

      DATA(lv_error_count) = lines( lt_errors ).

      READ TABLE lt_errors INTO ls_return INDEX lv_error_count.

      ev_message = ls_return-message.
      RETURN.

    ENDIF.

    " No abort or error message found.
    READ TABLE it_return INTO ls_return INDEX 1.

    ev_message = ls_return-message.
    rv_success = abap_true.

  ENDMETHOD.

  METHOD reverse_debit_note.

    CLEAR rv_reversed.

    DATA lv_message TYPE string.

    IF is_document_open(
         iv_doc_num   = CONV #( cs_data-debitnotenum )
         iv_comp_code = CONV #( cs_data-debitnotecomp )
         iv_fis_year  = CONV #( cs_data-debitnoteyear )
       ).

      rv_reversed = reverse_document(
        EXPORTING
          iv_doc_num   = CONV #( cs_data-debitnotenum )
          iv_comp_code = CONV #( cs_data-debitnotecomp )
          iv_fis_year  = CONV #( cs_data-debitnoteyear )
        IMPORTING
          ev_reversed_doc = cs_data-debitnoterevnum
          ev_fis_year     = cs_data-debitnoterevyear
          ev_message      = lv_message
      ).

      cs_data-debitnoterevcomment = lv_message.

      IF rv_reversed = abap_true.
        cs_data-debitnoterevcomp = cs_data-debitnotecomp.
      ENDIF.

    ELSE.

      cs_data-debitnoterevcomment = 'Document is cleared; reversal is not possible'.

    ENDIF.

  ENDMETHOD.

  METHOD reverse_document.

    CONSTANTS lc_rev_reason TYPE bapiacrev-reason_rev VALUE '04'.

    DATA:
      lv_post_date TYPE bkpf-budat,
      lt_bdc_msg   TYPE STANDARD TABLE OF bdcmsgcoll,
      lt_return    TYPE STANDARD TABLE OF bapiret2.

    CLEAR:
      ev_reversed_doc,
      ev_fis_year,
      ev_message,
      rv_reversed,
      lv_post_date,
      lt_bdc_msg,
      lt_return.

    " Reverse the FI document using the existing FB08 wrapper.
    CALL FUNCTION 'ZCY_FI_FM_CALL_FB08'
      EXPORTING
        i_bukrs   = iv_comp_code
        i_belnr   = iv_doc_num
        i_gjahr   = iv_fis_year
        i_stgrd   = lc_rev_reason
        i_no_auth = abap_true
      IMPORTING
        e_budat = lv_post_date
      TABLES
        et_messtab = lt_bdc_msg
      EXCEPTIONS
        not_possible  = 1
        error_message = 2
        OTHERS        = 3.

    IF sy-subrc <> 0.
      MESSAGE ID sy-msgid
              TYPE sy-msgty
              NUMBER sy-msgno
              WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4
              INTO ev_message.
      RETURN.
    ENDIF.

    IF lt_bdc_msg IS INITIAL.
      ev_message = 'FB08 returned no messages; reversal not confirmed'.
      RETURN.
    ENDIF.

    CALL FUNCTION 'CONVERT_BDCMSGCOLL_TO_BAPIRET2'
      TABLES
        imt_bdcmsgcoll = lt_bdc_msg
        ext_return     = lt_return.

    " Check whether the reversal success message is present.
    READ TABLE lt_return INTO DATA(ls_success)
      WITH KEY type   = 'S'
               id     = '312'
               number = 'F5'.

    IF sy-subrc = 0.

      rv_reversed = abap_true.

      ev_reversed_doc = CONV bkpf-belnr(
        |{ CONV belnr_d( ls_success-message_v1 ) ALPHA = IN }|
      ).

      ev_fis_year = get_fiscal_year(
        iv_bukrs = iv_comp_code
        iv_budat = lv_post_date
      ).

    ELSE.

      READ TABLE lt_return INTO DATA(ls_error)
        WITH KEY type = 'E'.

      IF sy-subrc = 0.
        ev_message = ls_error-message.
      ELSE.
        ev_message = 'Reversal success could not be confirmed'.
      ENDIF.

    ENDIF.

  ENDMETHOD.

  METHOD reverse_jv.

    rv_reversed = abap_false.

    CLEAR:
      cs_data-jvrevdocnum,
      cs_data-jvrevyear,
      cs_data-jvrevcomment.

    " Check whether the JV document exists and is open.
    IF cs_data-jvdocnum IS INITIAL.
      cs_data-jvrevcomment = 'JV document number is missing'.
      RETURN.
    ENDIF.

    IF is_document_open(
         iv_doc_num   = CONV #( cs_data-jvdocnum )
         iv_comp_code = cs_data-jvcompcode
         iv_fis_year  = cs_data-jvyear
       ) = abap_true.

      " Open document: reverse using FB08.
      reverse_document(
        EXPORTING
          iv_doc_num   = CONV #( cs_data-jvdocnum )
          iv_comp_code = cs_data-jvcompcode
          iv_fis_year  = cs_data-jvyear
        IMPORTING
          ev_reversed_doc = cs_data-jvrevdocnum
          ev_fis_year     = cs_data-jvrevyear
          ev_message      = DATA(lv_message)
        RECEIVING
          rv_reversed     = rv_reversed
      ).

      cs_data-jvrevcomment = lv_message.

      IF rv_reversed = abap_true.
        cs_data-jvrevcompcode = cs_data-jvcompcode.
      ENDIF.

    ELSE.

      " Cleared JV reversal is not implemented in the legacy source.
      cs_data-jvrevcomment = 'JV is cleared; reversal is not implemented'.

    ENDIF.

  ENDMETHOD.

ENDCLASS.
```

## Important Review Notes

The source has been formatted for readability. The following items are worth checking in the ABAP system before activation or production use:

1. **`is_document_in_bsik` reads `BSAK`.** The method name suggests vendor open items (`BSIK`), but the SQL reads `BSAK`, which is normally the vendor cleared-items index. Confirm whether that is intentional.
2. **`process_daf` calls `process_daf_open` for both branches.** The `ClearingDate` condition currently selects the `mv_p_daf_cl` or `mv_p_daf_op` flag, but both paths call the same method. Confirm that this is intended.
3. **`iv_p_daf` assignment in the handler:** in the previously supplied handler, `iv_p_daf` was populated from `ls_param-dbf`. Confirm whether it should use `ls_param-daf`.
4. **Unused or incomplete methods:** `post_debit_note`, `perform_grc_check`, and `post_jv` are empty in the supplied source. `unblock` and `get_instance_authorizations` in the behavior handler were also empty in the earlier code.
5. **Optional constructor parameters:** `iv_p_block`, `iv_p_split`, and `iv_p_deb_n` are optional. When omitted, their values will be initial; confirm that this matches the intended action behavior.
6. **Unused declarations / values:** `ls_bsik`, `lv_mess`, `mrt_exempted`, and `due_check` appear unused or not fully used in the supplied implementation.
7. **Payment-block toggle:** `toggle_payment_block` toggles the selected line item between payment block `Q` and blank. Verify the intended line-item selection and the use of `FI_DOCUMENT_CHANGE` in your target SAP release.

These are review observations, not changes to the business logic. The Markdown file preserves the supplied implementation's behavior as closely as possible while formatting the source.
