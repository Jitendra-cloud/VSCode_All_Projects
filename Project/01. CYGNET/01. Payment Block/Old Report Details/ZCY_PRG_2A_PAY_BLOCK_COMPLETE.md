# ZCY_PRG_2A_PAY_BLOCK — Complete SAP ABAP Source

The six uploaded text files are combined below in the same report/include order. The ABAP source is preserved and wrapped in syntax-highlighted `abap` Markdown blocks.

## 00. zcy_prg_2a_pay_block

```abap
REPORT zcy_prg_2a_pay_block.

INCLUDE zcy_inc_2a_pay_block_lcl_def1.    " Local class definitions
INCLUDE zcy_inc_2a_pay_block_sel_scr1.    " Selection screen
INCLUDE zcy_inc_2a_pay_block_lcl_imp1.    " Local class implementations
INCLUDE zcy_inc_2a_pay_block_main1.       " Main processing
INCLUDE zcy_inc_2a_pay_block_pbo_pai1.    " Screen flow logic
```

## 01. ZCY_INC_2A_PAY_BLOCK_LCL_DEF

```abap
*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*
* Objective                 : Hold SAP GST amount via JV               *
* Business Vertical         : <All Verticals JSW>                      *
* Plant/Location Specific   : All Plants                               *
* Department Name           : <Purchase/PPC/Store/FI>                  *
* Department Head Name/Email: < abc@xyz.com>                           *
* SAP Module                : <SD/MM/FI>                               *
* Technical Consultant      : Avinash Maity                             *
* Functional Consultant     : Pratik                                   *
* CR#                       : < S 9000054361 >                         *
* Object ID                 : < PPRP0XXX>                              *
* Creation Date             : <12/12/2025>                             *
*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*
* Change History                                                       *
*----------------------------------------------------------------------*
* Version |   Date    |  User    |   TR number   | Description         *
*----------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&  Include           ZCY_INC_2A_PAY_BLOCK_LCL_DEF
*&---------------------------------------------------------------------*
*--------------------------------------------------------------------*
* Local Class Definition
*--------------------------------------------------------------------*
" Generic exception class
CLASS lcx_generic DEFINITION
  INHERITING FROM cx_static_check.

  PUBLIC SECTION.
    INTERFACES:
      if_t100_message,
      if_t100_dyn_msg.
ENDCLASS.

CLASS lcl_app DEFINITION
  CREATE PRIVATE
  FINAL.

  PUBLIC SECTION.
    CONSTANTS:
      mc_save_all TYPE kkblo_layout-save VALUE 'A',

      BEGIN OF mcs_msg_type,
        success     TYPE syst-msgty VALUE 'S',
        information TYPE syst-msgty VALUE 'I',
        warning     TYPE syst-msgty VALUE 'W',
        abend       TYPE syst-msgty VALUE 'A',
        error       TYPE syst-msgty VALUE 'E',
        unknown     TYPE syst-msgty VALUE 'X',
      END OF mcs_msg_type.

    CLASS-DATA:
      mv_doc_date TYPE bkpf-bldat,
      mv_init     TYPE abap_bool,
      ms_variant  TYPE disvariant.

    CLASS-METHODS:
      layout_variant_selection,
      check_layout_variant_existence,
      initialize_layout_variant,
      sel_screen_pbo,
      sel_screen_pai,

      get_instance
        RETURNING
          VALUE(ro_app) TYPE REF TO lcl_app.

    METHODS:
      init_alv_screen,

      process
        RAISING lcx_generic,

      process_user_command
        IMPORTING
          VALUE(iv_ucomm) LIKE sy-ucomm,

      on_hotspot_click FOR EVENT hotspot_click OF cl_gui_alv_grid
        IMPORTING e_row_id e_column_id es_row_no,

      exit.

  PRIVATE SECTION.
    TYPES:
      mrtty_doc_date TYPE RANGE OF zcy_str_2a_pay_block-documentdate,

      BEGIN OF msty_message,
        number TYPE bapiret2-number,
        id     TYPE bapiret2-id,
      END OF msty_message.

    CLASS-DATA mo_app TYPE REF TO lcl_app.
    DATA:CO_date TYPE sy-datum.

    CONSTANTS:
      BEGIN OF mcs_doc_status,
        unprocessed             TYPE zcy_str_2a_pay_block-status VALUE '',
        blocked                 TYPE zcy_str_2a_pay_block-status VALUE 'B',
        split                   TYPE zcy_str_2a_pay_block-status VALUE 'S',
        released                TYPE zcy_str_2a_pay_block-status VALUE 'R',
        ireleased               TYPE zcy_str_2a_pay_block-status VALUE 'I',
        matched                 TYPE zcy_str_2a_pay_block-status VALUE 'M',
        exempted                TYPE zcy_str_2a_pay_block-status VALUE 'X',
        waiting_for_filing_date TYPE zcy_str_2a_pay_block-status VALUE 'W',
        debit_note_posted       TYPE zcy_str_2a_pay_block-status VALUE 'D',
      END OF mcs_doc_status,

      BEGIN OF mcs_vendor_block_type,
        exempted TYPE zcy_str_2a_pay_block-vendor_block_type VALUE 'X',
        block    TYPE zcy_str_2a_pay_block-vendor_block_type VALUE 'B',
        split    TYPE zcy_str_2a_pay_block-vendor_block_type VALUE 'S',
      END OF mcs_vendor_block_type,

      BEGIN OF mcs_yes_no,
        yes TYPE zcy_str_2a_pay_block-due_date_before_filing VALUE 'Y',
        no  TYPE zcy_str_2a_pay_block-due_date_before_filing VALUE 'N',
      END OF mcs_yes_no,

      BEGIN OF mcs_grc_result,
        success TYPE zcy_str_2a_pay_block-grc_check_result VALUE 'S',
        failed  TYPE zcy_str_2a_pay_block-grc_check_result VALUE 'F',
      END OF mcs_grc_result,

      BEGIN OF mcs_reco_status_cat,
        matched    TYPE zcy_t_2a_recstat-category VALUE 'M',
        mismatched TYPE zcy_t_2a_recstat-category VALUE 'B',
        exempted   TYPE zcy_t_2a_recstat-category VALUE 'X',
      END OF mcs_reco_status_cat,

      BEGIN OF mcs_dc_ind,
        debit  TYPE shkzg VALUE 'S',
        credit TYPE shkzg VALUE 'H',
      END OF mcs_dc_ind,

      BEGIN OF mcs_ucomm,
        back     TYPE syst-ucomm VALUE '&F03',
        exit     TYPE syst-ucomm VALUE '&F15',
        cancel   TYPE syst-ucomm VALUE '&F12',
        process  TYPE syst-ucomm VALUE '&PROCESS',
        transfer TYPE syst-ucomm VALUE '&TRANSFER',
        block    TYPE syst-ucomm VALUE '&UNBLOCK1',
        blockp   TYPE syst-ucomm VALUE '&BLOCK',
      END OF mcs_ucomm,

      BEGIN OF mcs_acc_type,
        vendor   TYPE bseg-koart VALUE 'K',
        customer TYPE bseg-koart VALUE 'D',
        gl       TYPE bseg-koart VALUE 'S',
      END OF mcs_acc_type,

      mc_split_spl_gl_ind TYPE bseg-umskz VALUE 'A'.

    DATA:
      mo_alv  TYPE REF TO cl_gui_alv_grid,
      ms_data TYPE zcy_str_2a_pay_block,
      mt_data LIKE STANDARD TABLE OF ms_data,
      lt_grc  TYPE STANDARD TABLE OF zgrc_control,
      ls_grc  TYPE  zgrc_control,
      ok      TYPE sy-ucomm,
      ok_auth TYPE char1,
      grc     TYPE i,
      BEGIN OF ms_jv_applicability,
        blocked    TYPE abap_bool,
        split      TYPE abap_bool,
        debit_note TYPE abap_bool,
      END OF ms_jv_applicability,

      BEGIN OF ms_doc_clearing_state,
        cleared TYPE abap_bool,
        open    TYPE abap_bool,
      END OF ms_doc_clearing_state,

      BEGIN OF ms_processing_options,
        wait_for_filing   TYPE abap_bool,
        jv_applicability  LIKE ms_jv_applicability,
        due_before_filing LIKE ms_doc_clearing_state,
        due_after_filing  LIKE ms_doc_clearing_state,
      END OF ms_processing_options.

    DATA:
      mrt_matched    LIKE RANGE OF ms_data-reconciliationsection,
      mrt_mismatched LIKE RANGE OF ms_data-reconciliationsection,
      mrt_exempted   LIKE RANGE OF ms_data-reconciliationsection.


    DATA:
      mt_vendor_block_type TYPE SORTED TABLE OF zcy_t_2a_blk_typ
        WITH UNIQUE KEY primary_key
          COMPONENTS
            vendor,

      BEGIN OF ms_vendor_data,
        vendor TYPE lfa1-lifnr,
        name   TYPE lfa1-name1,
        stcd3  TYPE lfa1-stcd3,
        brsch  TYPE lfa1-brsch,
        brtxt  TYPE t016t-brtxt,
      END OF ms_vendor_data,

      mt_vendor_data LIKE SORTED TABLE OF ms_vendor_data
        WITH UNIQUE KEY primary_key
          COMPONENTS
            vendor,

      BEGIN OF ms_vendor_data_m,
        bukrs  TYPE lfb1-bukrs,
        vendor TYPE lfa1-lifnr,
        mindk  TYPE lfb1-mindk,
*        STCD3  TYPE lfa1-stcd3,
      END OF ms_vendor_data_m,

      mt_vendor_data_m LIKE STANDARD TABLE OF  ms_vendor_data_m,

      BEGIN OF ms_acc_doc,
        bukrs TYPE bseg-bukrs,
        belnr TYPE bseg-belnr,
        gjahr TYPE bseg-gjahr,
      END OF ms_acc_doc,

      mt_acc_doc LIKE STANDARD TABLE OF ms_acc_doc,

      BEGIN OF ms_acc_data,
        bukrs    TYPE bseg-bukrs,
        belnr    TYPE bseg-belnr,
        gjahr    TYPE bseg-gjahr,
        bldat    TYPE bkpf-bldat,
        budat    TYPE bkpf-budat,
        cpudt    TYPE bkpf-cpudt,
        xblnr    TYPE bkpf-xblnr,
        bktxt    TYPE bkpf-bktxt,
        awtyp    TYPE bkpf-awtyp,
        awkey    TYPE bkpf-awkey,
        awsys    TYPE bkpf-awsys,
        blart    TYPE bkpf-blart,
        glvor    TYPE bkpf-glvor,
        buzei    TYPE bseg-buzei,
        buzid    TYPE bseg-buzid,
        augdt    TYPE bseg-augdt,
        dmbtr    TYPE bseg-dmbtr,
        zfbdt    TYPE bseg-zfbdt,
        zbd1t    TYPE bseg-zbd1t,
        zbd2t    TYPE bseg-zbd2t,
        zbd3t    TYPE bseg-zbd3t,
        koart    TYPE bseg-koart,
        shkzg    TYPE bseg-shkzg,
        lifnr    TYPE bseg-lifnr,
        kunnr    TYPE bseg-kunnr,
        rebzg    TYPE bseg-rebzg,
        zuonr    TYPE bseg-zuonr,
        sgtxt    TYPE bseg-sgtxt,
        mwskz    TYPE bseg-mwskz,
        zlspr    TYPE bseg-zlspr,
        umskz    TYPE bseg-umskz,
        prctr    TYPE bseg-prctr,
        gsber    TYPE bseg-gsber,
        kostl    TYPE bseg-kostl,
        bupla    TYPE bseg-bupla,
        secco    TYPE bseg-secco,
        hsn_sac  TYPE bseg-hsn_sac,
        gst_part TYPE bseg-gst_part,
        augbl    TYPE bseg-augbl,
        zterm    TYPE bseg-zterm,
*        BLINE_DATE    TYPE BSEG-BLINE_DATE,
      END OF ms_acc_data,

      mt_acc_data   LIKE SORTED TABLE OF ms_acc_data
        WITH UNIQUE KEY primary_key
          COMPONENTS
            bukrs
            belnr
            gjahr
            buzei
        WITH NON-UNIQUE SORTED KEY sec_key
          COMPONENTS
            bukrs
            belnr
            gjahr
            koart
        WITH NON-UNIQUE SORTED KEY line_type_key  ##TABKEY[SEC_KEY][PRIMARY_KEY]
          COMPONENTS
            bukrs
            belnr
            gjahr
            buzid,

      mt_acc_data_k LIKE TABLE OF ms_acc_data,


      BEGIN OF ms_tax_data,
        bukrs TYPE bset-bukrs,
        belnr TYPE bset-belnr,
        gjahr TYPE bset-gjahr,
        buzei TYPE bset-buzei,
        mwskz TYPE bset-mwskz,
        txgrp TYPE bset-txgrp,
        taxps TYPE bset-taxps,
        ktosl TYPE bset-ktosl,
        kschl TYPE bset-kschl,
        kbetr TYPE bset-kbetr,
        hwbas TYPE bset-hwbas,
        hwste TYPE bset-hwste,
        shkzg TYPE bset-shkzg,
        hkont TYPE bset-hkont,
      END OF ms_tax_data,

      BEGIN OF ms_bsik,
        bukrs TYPE bsik-bukrs,
        gjahr TYPE bsik-gjahr,
        belnr TYPE bsik-belnr,
        buzei TYPE bsik-buzei,
        zlspr TYPE bsik-zlspr,
        zterm TYPE bsik-zterm,
        augbl TYPE bsik-augbl,
        dmbtr TYPE bsik-dmbtr,
      END OF ms_bsik,
      lt_bsik     LIKE STANDARD TABLE OF ms_bsik,
      lt_tvarc    TYPE STANDARD TABLE OF tvarvc,

      mt_tax_data LIKE SORTED TABLE OF ms_tax_data
        WITH UNIQUE KEY primary_key
          COMPONENTS
            bukrs
            belnr
            gjahr
            buzei
        WITH NON-UNIQUE SORTED KEY sec_key
          COMPONENTS
            bukrs
            belnr
            gjahr.

    METHODS:
      set_globals
        RAISING
          lcx_generic,

      prepare_data
        RAISING lcx_generic,

      get_db_data
        RAISING lcx_generic,

      get_2a_data
        RAISING lcx_generic,

      get_acc_data,
      complete_data,

      fill_acc_data
        CHANGING
          cs_data LIKE ms_data,

      fill_status_relevant_data
        CHANGING
          cs_data LIKE ms_data,

      determne_exp_filing_date
        IMPORTING
          iv_doc_date               LIKE ms_data-documentdate
          iv_vendor                 LIKE ms_data-vendor
        RETURNING
          VALUE(rv_exp_filing_date) LIKE ms_data-expected_filing_date,

      derive_status_icon
        CHANGING
          cs_data LIKE ms_data,

      get_domain_values
        IMPORTING
          iv_domain_name         TYPE dd07l-domname
        RETURNING
          VALUE(rt_domain_value) TYPE dd07v_tab,

      fill_status_independent_data
        CHANGING
          cs_data LIKE ms_data,

      get_char_date_range
        RETURNING
          VALUE(rt_date) TYPE mrtty_doc_date,

      display_alv,

      fill_reco_status_range
        RAISING
          lcx_generic,

      process_documents
        RETURNING
          VALUE(rv_processed) TYPE abap_bool,

      process_documents_blk
        RETURNING
          VALUE(rv_processed) TYPE abap_bool,

      process_documents_blk1
        RETURNING
          VALUE(rv_processed) TYPE abap_bool,

      check_prev_proc_consistency
        RETURNING
          VALUE(rv_processed) TYPE abap_bool,

      process_unprocessed
        CHANGING
          cs_data LIKE ms_data,

      process_dbf
        CHANGING
          cs_data LIKE ms_data,

      process_dbf_cleared
        CHANGING
          cs_data LIKE ms_data,

      process_dbf_open
        CHANGING
          cs_data LIKE ms_data,

      process_daf
        CHANGING
          cs_data LIKE ms_data,

      process_daf_cleared
        CHANGING
          cs_data LIKE ms_data,

      process_daf_open
        CHANGING
          cs_data LIKE ms_data,

      block_open_document
        CHANGING
          cs_data           LIKE ms_data
        RETURNING
          VALUE(rv_blocked) TYPE abap_bool,

      toggle_payment_block
        IMPORTING
          iv_doc_num        TYPE bkpf-belnr
          iv_comp_code      TYPE bkpf-bukrs
          iv_fis_year       TYPE bkpf-gjahr
        EXPORTING
          ev_message        LIKE ms_data-block_comment
        RETURNING
          VALUE(rv_toggled) TYPE abap_bool,

      perform_grc_check
        IMPORTING
          iv_vendor        TYPE bseg-lifnr
        EXPORTING
          ev_score         LIKE ms_data-grc_score
          ev_message       LIKE ms_data-grc_check_comment
        RETURNING
          VALUE(rv_result) LIKE ms_data-grc_check_result,

      split_document
        IMPORTING
          iv_doc_num      TYPE bkpf-belnr
          iv_comp_code    TYPE bkpf-bukrs
          iv_fis_year     TYPE bkpf-gjahr
          iv_vendor       TYPE bseg-lifnr
          iv_gst_amt      LIKE ms_data-fi_tax_value
        EXPORTING
          ev_comp_code    LIKE ms_data-split_comp_code
          ev_doc_num      LIKE ms_data-split_gst_doc_num
          ev_fis_year     LIKE ms_data-split_year
          ev_message      LIKE ms_data-split_comment
        RETURNING
          VALUE(rv_split) TYPE abap_bool,

      post_jv
        IMPORTING
          iv_doc_num       TYPE bkpf-belnr
          iv_comp_code     TYPE bkpf-bukrs
          iv_fis_year      TYPE bkpf-gjahr
          iv_gst_amount    LIKE ms_data-fi_tax_value
        EXPORTING
          ev_comp_code     LIKE ms_data-jv_comp_code
          ev_doc_num       LIKE ms_data-jv_doc_num
          ev_fis_year      LIKE ms_data-jv_year
          ev_message       LIKE ms_data-jv_comment
        RETURNING
          VALUE(rv_posted) TYPE abap_bool,

      block_cleared_document
        CHANGING
          cs_data           LIKE ms_data
        RETURNING
          VALUE(rv_blocked) TYPE abap_bool,

      block_cleared_document_bk
        CHANGING
          cs_data           LIKE ms_data
        RETURNING
          VALUE(rv_blocked) TYPE abap_bool,

      post_debit_note
        IMPORTING
          iv_doc_num       TYPE bkpf-belnr
          iv_comp_code     TYPE bkpf-bukrs
          iv_fis_year      TYPE bkpf-gjahr
          iv_vendor        TYPE bseg-lifnr
          iv_gst_amount    LIKE ms_data-fi_tax_value
        EXPORTING
          ev_comp_code     LIKE ms_data-debit_note_comp
          ev_doc_num       LIKE ms_data-debit_note_num
          ev_fis_year      LIKE ms_data-debit_note_year
          ev_message       LIKE ms_data-debit_note_comment
        RETURNING
          VALUE(rv_posted) TYPE abap_bool,

      process_blocked
        CHANGING
          cs_data LIKE ms_data,

      release_document
        CHANGING
          cs_data            LIKE ms_data
        RETURNING
          VALUE(rv_released) TYPE abap_bool,

      reverse_debit_note
        IMPORTING
          iv_doc_num         TYPE bkpf-belnr
          iv_comp_code       TYPE bkpf-bukrs
          iv_fis_year        TYPE bkpf-gjahr
        EXPORTING
          ev_comp_code       LIKE ms_data-debit_note_rev_comp
          ev_doc_num         LIKE ms_data-debit_note_rev_num
          ev_fis_year        LIKE ms_data-debit_note_rev_year
          ev_message         LIKE ms_data-debit_note_rev_comment
        RETURNING
          VALUE(rv_reversed) TYPE abap_bool,

      reverse_jv
        IMPORTING
          iv_doc_num         TYPE bkpf-belnr
          iv_comp_code       TYPE bkpf-bukrs
          iv_fis_year        TYPE bkpf-gjahr
        EXPORTING
          ev_comp_code       LIKE ms_data-jv_rev_comp_code
          ev_doc_num         LIKE ms_data-jv_rev_doc_num
          ev_fis_year        LIKE ms_data-jv_rev_year
          ev_message         LIKE ms_data-jv_rev_comment
        RETURNING
          VALUE(rv_reversed) TYPE abap_bool,

      reverse_document
        IMPORTING
          iv_doc_num         TYPE bkpf-belnr
          iv_comp_code       TYPE bkpf-bukrs
          iv_fis_year        TYPE bkpf-gjahr
        EXPORTING
          ev_comp_code       TYPE bkpf-bukrs
          ev_doc_num         TYPE bkpf-belnr
          ev_fis_year        TYPE bkpf-gjahr
          ev_message         LIKE ms_data-release_comment
        RETURNING
          VALUE(rv_reversed) TYPE abap_bool,

      transfer_to_gst_hold_jv
        RETURNING
          VALUE(rv_processed) TYPE abap_bool,

      update_db
        IMPORTING
          is_data           LIKE ms_data
        RETURNING
          VALUE(rv_updated) TYPE abap_bool,

      get_log
        EXPORTING
          ev_date TYPE syst-datlo
          ev_time TYPE syst-timlo
          ev_user TYPE text80,

      get_fiscal_year
        IMPORTING
          iv_comp_code          TYPE bkpf-bukrs
          iv_date               TYPE syst-datlo DEFAULT sy-datlo
        RETURNING
          VALUE(rv_fiscal_year) TYPE bkpf-gjahr,

      is_document_open
        IMPORTING
          iv_doc_num     TYPE bkpf-belnr
          iv_comp_code   TYPE bkpf-bukrs
          iv_fis_year    TYPE bkpf-gjahr
        RETURNING
          VALUE(rv_open) TYPE abap_bool,

      check_document_posted
        IMPORTING
          iv_doc_num       TYPE bkpf-belnr
          iv_comp_code     TYPE bkpf-bukrs
          iv_fis_year      TYPE bkpf-gjahr
        RETURNING
          VALUE(rv_posted) TYPE abap_bool,

      process_bapi_return
        IMPORTING
          is_success_msg    TYPE msty_message
          it_return         TYPE bapiret2_t
        EXPORTING
          es_return         TYPE bapiret2
        RETURNING
          VALUE(rv_success) TYPE abap_bool,

      get_processing_options
        RETURNING
          VALUE(rv_processing_options) LIKE ms_data-processing_options,

      set_grid_fcat
        RETURNING
          VALUE(rt_fcat) TYPE lvc_t_fcat,

      set_grid_layout
        RETURNING
          VALUE(rs_layout) TYPE lvc_s_layo,

      set_grid_toolbar_excluding
        RETURNING
          VALUE(rt_toolbar_excluding) TYPE ui_functions,

      set_grid_editable,

      set_grid_event_handlers,

      toolbar FOR EVENT toolbar OF cl_gui_alv_grid
        IMPORTING
          e_interactive
          e_object
          sender,

      user_command FOR EVENT user_command OF cl_gui_alv_grid
        IMPORTING
          e_ucomm
          sender,

      data_changed FOR EVENT data_changed OF cl_gui_alv_grid
        IMPORTING
          er_data_changed
          e_onf4
          e_onf4_after
          e_onf4_before
          e_ucomm
          sender,

      data_changed_finished FOR EVENT data_changed_finished OF cl_gui_alv_grid
        IMPORTING
          e_modified
          et_good_cells
          sender,

      after_user_command FOR EVENT after_user_command OF cl_gui_alv_grid
        IMPORTING
          e_ucomm
          e_not_processed
          e_saved
          sender,

      refresh_grid,
      flush,
      select_all,
      deselect_all.
ENDCLASS.

CLASS lcl_main DEFINITION
  CREATE PUBLIC
  FINAL.

  PUBLIC SECTION.
    CLASS-METHODS: start.
ENDCLASS.
```

## 02. ZCY_INC_2A_PAY_BLOCK_SEL_SCR

```abap
*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*

* Objective                 : Hold SAP GST amount via JV               *

* Business Vertical         : <All Verticals JSW>                      *

* Plant/Location Specific   : All Plants                               *

* Department Name           : <Purchase/PPC/Store/FI>                  *

* Department Head Name/Email: < abc@xyz.com>                           *

* SAP Module                : <SD/MM/FI>                               *

* Technical Consultant      : Paresh Patel                             *

* Functional Consultant     : Pratik                                   *

* CR#                       : < S 9000054361 >                         *

* Object ID                 : < PPRP0XXX>                              *

* Creation Date             : <12/12/2025>                             *

*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*

* Change History                                                       *

*----------------------------------------------------------------------*

* Version |   Date    |  User    |   TR number   | Description         *

*----------------------------------------------------------------------*

*&---------------------------------------------------------------------*

*&  Include           ZCY_INC_2A_PAY_BLOCK_SEL_SCR

*&---------------------------------------------------------------------*

*--------------------------------------------------------------------*

* Selection Screen

*--------------------------------------------------------------------*

TABLES: BKPF,ZCY_TAB_RRP_E,ZCY_STR_2A_PAY_BLOCK,BSEG.

SELECTION-SCREEN BEGIN OF BLOCK SEL WITH FRAME TITLE TEXT-001.

SELECT-OPTIONS S_DATE FOR LCL_APP=>MV_DOC_DATE NO-DISPLAY.

SELECT-OPTIONS S_DATE1 FOR LCL_APP=>MV_DOC_DATE.

SELECT-OPTIONS S_RECO FOR LCL_APP=>MV_DOC_DATE.

SELECT-OPTIONS S_BELNR FOR BKPF-BELNR.

SELECT-OPTIONS S_GJAHR FOR BKPF-GJAHR.

SELECT-OPTIONS S_BUKRS FOR BKPF-BUKRS OBLIGATORY.

SELECT-OPTIONS S_BUPLA FOR BSEG-BUPLA OBLIGATORY.

SELECT-OPTIONS S_BILLFR FOR ZCY_TAB_RRP_E-BILLFROMGSTIN.

SELECT-OPTIONS S_BILLTO FOR ZCY_TAB_RRP_E-BILLTOGSTIN.

SELECT-OPTIONS S_PER FOR ZCY_TAB_RRP_E-RETURNPERIOD NO-EXTENSION NO INTERVALS NO-DISPLAY.

SELECT-OPTIONS S_DOC FOR ZCY_TAB_RRP_E-DOCUMENTNUMBER.

SELECT-OPTIONS S_DOC_ST FOR ZCY_STR_2A_PAY_BLOCK-STATUS NO INTERVALS.

SELECT-OPTIONS S_BUDAT FOR LCL_APP=>MV_DOC_DATE.

SELECTION-SCREEN END OF BLOCK SEL.

SELECTION-SCREEN BEGIN OF BLOCK OPT WITH FRAME TITLE TEXT-004.

PARAMETERS:

  " pbf = process before filing

  P_PBF TYPE CHECKBOX AS CHECKBOX DEFAULT ABAP_TRUE,

  P_JV  TYPE CHECKBOX AS CHECKBOX USER-COMMAND JV DEFAULT ABAP_FALSE.

SELECTION-SCREEN BEGIN OF BLOCK JV WITH FRAME TITLE TEXT-005.

PARAMETERS:

  P_BLOCK TYPE CHECKBOX AS CHECKBOX MODIF ID JV DEFAULT ABAP_TRUE,

  P_SPLIT TYPE CHECKBOX AS CHECKBOX MODIF ID JV DEFAULT ABAP_TRUE,

  " deb_n = debit note

  P_DEB_N TYPE CHECKBOX AS CHECKBOX MODIF ID JV DEFAULT ABAP_TRUE.

SELECTION-SCREEN END OF BLOCK JV.

" dbf = due before filing

PARAMETERS P_DBF TYPE CHECKBOX AS CHECKBOX USER-COMMAND DBF DEFAULT ABAP_TRUE.

SELECTION-SCREEN BEGIN OF BLOCK DBF WITH FRAME TITLE TEXT-007.

PARAMETERS:

  " dbf_cl = cleared

  P_DBF_CL TYPE CHECKBOX AS CHECKBOX MODIF ID DBF DEFAULT ABAP_TRUE,

  " dbf_op = open

  P_DBF_OP TYPE CHECKBOX AS CHECKBOX MODIF ID DBF DEFAULT ABAP_TRUE.

SELECTION-SCREEN END OF BLOCK DBF.

" daf = due after filing

PARAMETERS P_DAF TYPE CHECKBOX AS CHECKBOX USER-COMMAND DAF DEFAULT ABAP_TRUE.

SELECTION-SCREEN BEGIN OF BLOCK DAF WITH FRAME TITLE TEXT-007.

PARAMETERS:

  " daf_cl = cleared

  P_DAF_CL TYPE CHECKBOX AS CHECKBOX MODIF ID DAF DEFAULT ABAP_TRUE,

  " daf_op = open

  P_DAF_OP TYPE CHECKBOX AS CHECKBOX MODIF ID DAF DEFAULT ABAP_TRUE.

SELECTION-SCREEN END OF BLOCK DAF.

SELECTION-SCREEN END OF BLOCK OPT.

SELECTION-SCREEN BEGIN OF BLOCK LAY WITH FRAME TITLE TEXT-002.

PARAMETERS: P_VAR TYPE DISVARIANT-VARIANT.

SELECTION-SCREEN END OF BLOCK LAY.

PARAMETERS:MAT TYPE CHAR1 AS CHECKBOX.

PARAMETERS:MISSMAT TYPE CHAR1 AS CHECKBOX.

PARAMETERS:YEL TYPE CHAR1 AS CHECKBOX.

PARAMETERS:DBN TYPE CHAR1 AS CHECKBOX.

PARAMETERS:REL TYPE CHAR1 AS CHECKBOX.

PARAMETERS:ALL TYPE CHAR1 AS CHECKBOX DEFAULT 'X'.
```

## 03. ZCY_INC_2A_PAY_BLOCK_LCL_IMP

```abap
*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*
* Objective                 : Hold SAP GST amount via JV               *
* Business Vertical         : <All Verticals JSW>                      *
* Plant/Location Specific   : All Plants                               *
* Department Name           : <Purchase/PPC/Store/FI>                  *
* Department Head Name/Email: < abc@xyz.com>                           *
* SAP Module                : <SD/MM/FI>                               *
* Technical Consultant      : Paresh Patel                             *
* Functional Consultant     : Pratik                                   *
* CR#                       : < S 9000054361 >                         *
* Object ID                 : < PPRP0XXX>                              *
* Creation Date             : <12/12/2025>                             *
*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*
* Change History                                                       *
*----------------------------------------------------------------------*
* Version |   Date    |  User    |   TR number   | Description         *
*----------------------------------------------------------------------*
*&---------------------------------------------------------------------*
*&  Include           ZCY_INC_2A_PAY_BLOCK_LCL_IMP
*&---------------------------------------------------------------------*
*--------------------------------------------------------------------*
* Local Class Implementation
*--------------------------------------------------------------------*
CLASS LCL_APP IMPLEMENTATION.
  METHOD LAYOUT_VARIANT_SELECTION.
    DATA LV_EXIT TYPE C LENGTH 1.

    CLEAR LV_EXIT.
    MS_VARIANT-REPORT = SY-REPID.

    CALL FUNCTION 'LVC_VARIANT_F4'
      EXPORTING
        IS_VARIANT    = MS_VARIANT
        I_SAVE        = MC_SAVE_ALL
      IMPORTING
        E_EXIT        = LV_EXIT
        ES_VARIANT    = MS_VARIANT
      EXCEPTIONS
        NOT_FOUND     = 1
        PROGRAM_ERROR = 2
        OTHERS        = 3.
    IF SY-SUBRC <> 0.
      MESSAGE ID SY-MSGID TYPE MCS_MSG_TYPE-SUCCESS NUMBER SY-MSGNO
        WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
    ELSE.
      IF LV_EXIT NE ABAP_TRUE AND MS_VARIANT IS NOT INITIAL.
        P_VAR = MS_VARIANT-VARIANT.
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD CHECK_LAYOUT_VARIANT_EXISTENCE.
    CONSTANTS:
      BEGIN OF LCS_SELSCR_UCOMM,
        EXEC_ONLINE TYPE SYST-UCOMM VALUE 'ONLI',
        EXEC_IN_BG  TYPE SYST-UCOMM VALUE 'SJOB',
      END OF LCS_SELSCR_UCOMM.

    IF SY-UCOMM EQ LCS_SELSCR_UCOMM-EXEC_ONLINE
      OR SY-UCOMM = LCS_SELSCR_UCOMM-EXEC_IN_BG.

      IF P_VAR IS NOT INITIAL.
        MS_VARIANT-REPORT = SY-REPID.
        MS_VARIANT-VARIANT = P_VAR.

        CALL FUNCTION 'LVC_VARIANT_EXISTENCE_CHECK'
          EXPORTING
            I_SAVE        = MC_SAVE_ALL
          CHANGING
            CS_VARIANT    = MS_VARIANT
          EXCEPTIONS
            WRONG_INPUT   = 1
            NOT_FOUND     = 2
            PROGRAM_ERROR = 3
            OTHERS        = 4.
        IF SY-SUBRC <> 0 OR MS_VARIANT IS INITIAL.
* Implement suitable error handling here
          CLEAR MS_VARIANT.
          SET CURSOR FIELD 'P_VAR'.

          MESSAGE ID SY-MSGID TYPE MCS_MSG_TYPE-ERROR NUMBER SY-MSGNO
            WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
        ENDIF.
      ELSE.
        INITIALIZE_LAYOUT_VARIANT( ).
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD INITIALIZE_LAYOUT_VARIANT.
    CLEAR MS_VARIANT.
    MS_VARIANT-REPORT = SY-REPID.

    CALL FUNCTION 'LVC_VARIANT_DEFAULT_GET'
      EXPORTING
        I_SAVE        = MC_SAVE_ALL
      CHANGING
        CS_VARIANT    = MS_VARIANT
      EXCEPTIONS
        WRONG_INPUT   = 1
        NOT_FOUND     = 2
        PROGRAM_ERROR = 3
        OTHERS        = 4.
    IF SY-SUBRC <> 0.
* Implement suitable error handling here
    ELSE.
      IF MS_VARIANT IS NOT INITIAL.
        P_VAR = MS_VARIANT-VARIANT.
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD SEL_SCREEN_PBO.
    CONSTANTS:
      BEGIN OF LCS_SCR_GRP,
        JV                LIKE SCREEN-GROUP1 VALUE 'JV',
        DUE_BEFORE_FILING LIKE SCREEN-GROUP1 VALUE 'DBF',
        DUE_AFTER_FILING  LIKE SCREEN-GROUP1 VALUE 'DAF',
      END OF LCS_SCR_GRP,

      BEGIN OF LCS_SCR_STATUS,
        ACTIVE   LIKE SCREEN-ACTIVE VALUE '1',
        INACTIVE LIKE SCREEN-ACTIVE VALUE '0',
      END OF LCS_SCR_STATUS.

    LOOP AT SCREEN INTO DATA(LS_SCREEN).
      IF LS_SCREEN-GROUP1 = LCS_SCR_GRP-JV.
        IF P_JV = ABAP_TRUE.
          LS_SCREEN-ACTIVE = LCS_SCR_STATUS-ACTIVE.
        ELSE.
          LS_SCREEN-ACTIVE = LCS_SCR_STATUS-INACTIVE.
        ENDIF.
      ENDIF.
      IF LS_SCREEN-GROUP1 = LCS_SCR_GRP-DUE_BEFORE_FILING.
        IF P_DBF = ABAP_TRUE.
          LS_SCREEN-ACTIVE = LCS_SCR_STATUS-ACTIVE.
        ELSE.
          LS_SCREEN-ACTIVE = LCS_SCR_STATUS-INACTIVE.
        ENDIF.
      ENDIF.
      IF LS_SCREEN-GROUP1 = LCS_SCR_GRP-DUE_AFTER_FILING.
        IF P_DAF = ABAP_TRUE.
          LS_SCREEN-ACTIVE = LCS_SCR_STATUS-ACTIVE.
        ELSE.
          LS_SCREEN-ACTIVE = LCS_SCR_STATUS-INACTIVE.
        ENDIF.
      ENDIF.
      MODIFY SCREEN FROM LS_SCREEN.
    ENDLOOP.
  ENDMETHOD.

  METHOD SEL_SCREEN_PAI.
    CONSTANTS:
      BEGIN OF LCS_SELSCR_UCOMM,
        JV                TYPE SYST-UCOMM VALUE 'JV',
        DUE_BEFORE_FILING TYPE SYST-UCOMM VALUE 'DBF',
        DUE_AFTER_FILING  TYPE SYST-UCOMM VALUE 'DAF',
      END OF LCS_SELSCR_UCOMM.

    CASE SY-UCOMM.
      WHEN LCS_SELSCR_UCOMM-JV.
        P_BLOCK = P_JV.
        P_SPLIT = P_JV.
        P_DEB_N = P_JV.
      WHEN LCS_SELSCR_UCOMM-DUE_BEFORE_FILING.
        P_DBF_CL = P_DBF.
        P_DBF_OP = P_DBF.
      WHEN LCS_SELSCR_UCOMM-DUE_AFTER_FILING.
        P_DAF_CL = P_DAF.
        P_DAF_OP = P_DAF.
      WHEN OTHERS.
    ENDCASE.
    IF S_DATE1[] IS INITIAL AND S_RECO[] IS INITIAL AND S_BUDAT[] IS INITIAL.
      MESSAGE 'Please enter Document Date/Reco.Date/Posting Date' TYPE 'E' DISPLAY LIKE 'I'.
    ENDIF.
  ENDMETHOD.

  METHOD GET_INSTANCE.
    IF MO_APP IS NOT BOUND.
      MO_APP = NEW #( ).
    ENDIF.
    RO_APP = MO_APP.
  ENDMETHOD.

  METHOD PROCESS.
    SET_GLOBALS( ).
    PREPARE_DATA( ).

    IF SY-BATCH = ABAP_TRUE.
      SELECT_ALL( ).
      PROCESS_DOCUMENTS( ).
    ELSE.
      DISPLAY_ALV( ).
    ENDIF.
  ENDMETHOD.

  METHOD SET_GLOBALS.
    SY-TCODE = 'ZGST_HOLD'.
    CLEAR MS_PROCESSING_OPTIONS.
    MS_PROCESSING_OPTIONS =
      VALUE #( WAIT_FOR_FILING   = XSDBOOL( P_PBF = ABAP_FALSE )
               JV_APPLICABILITY  = COND #( WHEN P_JV = ABAP_TRUE
                                           THEN VALUE #( BLOCKED    = P_BLOCK
                                                         SPLIT      = P_SPLIT
                                                         DEBIT_NOTE = P_DEB_N ) )
               DUE_BEFORE_FILING = COND #( WHEN P_DBF = ABAP_TRUE
                                           THEN VALUE #( CLEARED = P_DBF_CL
                                                         OPEN    = P_DBF_OP ) )
               DUE_AFTER_FILING  = COND #( WHEN P_DAF = ABAP_TRUE
                                           THEN VALUE #( CLEARED = P_DAF_CL
                                                         OPEN    = P_DAF_OP ) ) ).

    FILL_RECO_STATUS_RANGE( ).
  ENDMETHOD.

  METHOD INIT_ALV_SCREEN.
    DATA LT_EXCLUDE_FCODE LIKE STANDARD TABLE OF SY-UCOMM.

    SET PF-STATUS 'STANDARD1' EXCLUDING LT_EXCLUDE_FCODE.
    SET TITLEBAR 'TITLE'.
  ENDMETHOD.

  METHOD PREPARE_DATA.
    CLEAR MT_DATA.

    GET_DB_DATA( ).
    COMPLETE_DATA( ).
  ENDMETHOD.

  METHOD GET_DB_DATA.
    GET_2A_DATA( ).
    GET_ACC_DATA( ).
  ENDMETHOD.

  METHOD GET_2A_DATA.
*    data(lrt_date) = get_char_date_range( ). " replaced by enhanced open SQL
    DATA: DUE TYPE SY-DATUM.
    DATA: EXP_DT TYPE SY-DATUM.
    EXP_DT = SY-DATUM - 1.
    DUE = SY-DATUM + 1.

    TYPES: IT_RANGE TYPE RANGE OF ZCY_DET_RECONCILIATIONSECTION.
    DATA: IS_RECO TYPE LINE OF IT_RANGE.
    DATA: IT_RECO TYPE  IT_RANGE.
    DATA: MSG TYPE STRING.

    TYPES: IT_RANGE1 TYPE RANGE OF ZCY_DET_2A_PAY_BLOCK_STATUS.
    DATA: IS_RECO1 TYPE LINE OF IT_RANGE1.
    DATA: IT_RECO1 TYPE  IT_RANGE1.

    SELECT BUKRS, BRANCH INTO TABLE @DATA(T_BUPLA) FROM J_1BBRANCH WHERE BUKRS IN @S_BUKRS AND BRANCH IN @S_BUPLA.

    LOOP AT T_BUPLA INTO DATA(WA_BUPLA).
      AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
      ID 'BUKRS' FIELD WA_BUPLA-BUKRS
      ID 'BUPLA' FIELD WA_BUPLA-BRANCH
      ID 'ACTVT' FIELD '03'.
      IF SY-SUBRC <> 0.
        CLEAR: MSG.
        CONCATENATE 'You are not Authorized to view the selected Company Code/Business Place Data:' WA_BUPLA-BRANCH INTO MSG SEPARATED BY SPACE.
        MESSAGE MSG TYPE 'E'.
      ENDIF.
      CLEAR: WA_BUPLA.
    ENDLOOP.

    IF MAT = 'X'.
      IS_RECO-LOW = 'MATCHED'.
      IS_RECO-SIGN = 'I'.
      IS_RECO-OPTION = 'EQ'.
      APPEND IS_RECO TO IT_RECO..
    ENDIF.
    IF MISSMAT = 'X'.
      IS_RECO-SIGN = 'I'.
      IS_RECO-OPTION = 'EQ'.
      IS_RECO-LOW = 'PRONLY'.
      APPEND IS_RECO TO IT_RECO..
    ENDIF.
    IF YEL = 'X'.
      IS_RECO-SIGN = 'I'.
      IS_RECO-OPTION = 'EQ'.
      IS_RECO-LOW = 'MATCHEDBYTOLERANCE'.
      APPEND IS_RECO TO IT_RECO..
      IS_RECO-LOW = 'NEARMATCHED'.
      APPEND IS_RECO TO IT_RECO.
    ENDIF.

    IF DBN = 'X'.
      IS_RECO1-SIGN = 'I'.
      IS_RECO1-OPTION = 'EQ'.
      IS_RECO1-LOW = 'D'.
      APPEND IS_RECO1 TO IT_RECO1..
    ENDIF.
    IF REL = 'X'.
      IS_RECO1-SIGN = 'I'.
      IS_RECO1-OPTION = 'EQ'.
      IS_RECO1-LOW = 'R'.
      APPEND IS_RECO1 TO IT_RECO1.
      IS_RECO1-SIGN = 'I'.
      IS_RECO1-OPTION = 'EQ'.
      IS_RECO1-LOW = 'I'.
      APPEND IS_RECO1 TO IT_RECO1.
    ENDIF.

    IF ALL = 'X'.
      REFRESH : IT_RECO.
      REFRESH : IT_RECO1.
    ENDIF.


    " sequence is important! b~* then a~*
    IF S_DATE1[] IS NOT INITIAL.
      SELECT B~*,
             A~*,
             ( A~IGSTAMOUNT + A~CGSTAMOUNT + A~SGSTAMOUNT ) AS TOTALTAX
        FROM ZCY_TAB_RRP_E AS A
        LEFT OUTER JOIN ZCY_T_2A_PAY_BLK AS B
        ON  "a~financialyear  = b~financialyear
            A~RETURNPERIOD   = B~RETURNPERIOD
        AND A~LOCATIONGSTIN  = B~LOCATIONGSTIN
        AND A~DOCUMENTNUMBER = B~DOCUMENTNUMBER
        AND A~DOCUMENTDATE   = B~DOCUMENTDATE
        AND A~CUSTOM1        = B~CUSTOM1
        AND A~CUSTOM4        = B~CUSTOM4
        AND A~CUSTOM9        = B~CUSTOM9
        WHERE A~DOCUMENTDATE IN @S_DATE1[]
        AND A~REC_DATE IN @S_RECO
        AND A~CUSTOM1 <> @SPACE
        AND A~CUSTOM4 <> @SPACE
        AND A~CUSTOM9 <> @SPACE
        AND A~DOCUMENTNUMBER IN @S_DOC
        AND A~CUSTOM1 IN @S_BELNR
        AND A~CUSTOM4 IN @S_GJAHR
        AND A~CUSTOM9 IN @S_BUKRS
        AND A~CUSTOM20 IN @S_BUPLA
        AND A~BILLFROMGSTIN IN @S_BILLFR
        AND A~BILLTOGSTIN IN @S_BILLTO
        AND A~CUSTOM2 IN @S_BUDAT
*      AND A~RETURNPERIOD IN @S_PER
        AND A~RECONCILIATIONSECTION IN @IT_RECO
*        AND B~STATUS IN @S_DOC_ST
        ORDER BY A~CUSTOM1, A~CUSTOM4, A~CUSTOM9 ASCENDING
      INTO CORRESPONDING FIELDS OF TABLE @MT_DATA.
    ELSE.
      SELECT B~*,
             A~*,
             ( A~IGSTAMOUNT + A~CGSTAMOUNT + A~SGSTAMOUNT ) AS TOTALTAX
        FROM ZCY_TAB_RRP_E AS A
        LEFT OUTER JOIN ZCY_T_2A_PAY_BLK AS B
        ON  "a~financialyear  = b~financialyear
            A~RETURNPERIOD   = B~RETURNPERIOD
        AND A~LOCATIONGSTIN  = B~LOCATIONGSTIN
        AND A~DOCUMENTNUMBER = B~DOCUMENTNUMBER
        AND A~DOCUMENTDATE   = B~DOCUMENTDATE
        AND A~CUSTOM1        = B~CUSTOM1
        AND A~CUSTOM4        = B~CUSTOM4
        AND A~CUSTOM9        = B~CUSTOM9
        WHERE ( B~PAYMENT_DUE_DATE = @DUE OR A~REC_DATE IN @S_RECO
        OR CAST(
                CONCAT(
                  CONCAT(
                    SUBSTRING( A~FILINGDATE, 7, 4 ),
                    SUBSTRING( A~FILINGDATE, 4, 2 ) ),
                  SUBSTRING( A~FILINGDATE, 1, 2 ) ) AS DATS ) = @SY-DATUM OR ( B~EXPECTED_FILING_DATE = @SY-DATUM OR B~EXPECTED_FILING_DATE = @EXP_DT )  )
        AND A~DOCUMENTDATE IN @S_DATE1[]
        AND A~CUSTOM1 <> @SPACE
        AND A~CUSTOM4 <> @SPACE
        AND A~CUSTOM9 <> @SPACE
        AND A~DOCUMENTNUMBER IN @S_DOC
        AND A~CUSTOM1 IN @S_BELNR
        AND A~CUSTOM4 IN @S_GJAHR
        AND A~CUSTOM9 IN @S_BUKRS
        AND A~CUSTOM20 IN @S_BUPLA
        AND A~BILLFROMGSTIN IN @S_BILLFR
        AND A~BILLTOGSTIN IN @S_BILLTO
*      AND A~RETURNPERIOD IN @S_PER
        AND A~RECONCILIATIONSECTION IN @IT_RECO
        AND A~CUSTOM2 IN @S_BUDAT
*        AND B~STATUS IN @S_DOC_ST
        ORDER BY A~CUSTOM1, A~CUSTOM4, A~CUSTOM9 ASCENDING
      INTO CORRESPONDING FIELDS OF TABLE @MT_DATA.
    ENDIF.

    IF IT_RECO1[] IS NOT INITIAL.
      DELETE MT_DATA WHERE NOT STATUS IN IT_RECO1.
    ENDIF.

    IF S_DOC_ST[] IS NOT INITIAL.
      DELETE MT_DATA WHERE NOT STATUS IN S_DOC_ST.
    ENDIF.

    " for upper case comparison
    LOOP AT MT_DATA INTO DATA(LS_DATA).
      DATA(LV_INDEX) = SY-TABIX.
      IF TO_UPPER( LS_DATA-RECONCILIATIONSECTION ) IN MRT_EXEMPTED[].
        DELETE MT_DATA INDEX LV_INDEX.
      ENDIF.

      CLEAR:
        LS_DATA,
        LV_INDEX.
    ENDLOOP.

    IF MT_DATA IS NOT INITIAL.
      DELETE ADJACENT DUPLICATES FROM MT_DATA
        COMPARING FINANCIALYEAR
                  RETURNPERIOD
                  LOCATIONGSTIN
                  DOCUMENTNUMBER
                  DOCUMENTDATE
                  CUSTOM1
                  CUSTOM4
                  CUSTOM9.
    ELSE.
      RAISE EXCEPTION TYPE LCX_GENERIC
        MESSAGE ID '00' TYPE MCS_MSG_TYPE-ERROR NUMBER '001'
        WITH TEXT-003.
    ENDIF.
  ENDMETHOD.

  METHOD GET_ACC_DATA.
    CLEAR MT_ACC_DOC.
*    SELECT * FROM ZGRC_CONTROL INTO TABLE LT_GRC.
    " adjust custom fields for type compatibility with standard fields/tables
    LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA>).  " where status = mcs_doc_status-unprocessed.
      IF <LS_DATA>-CUSTOM1 IS NOT INITIAL AND <LS_DATA>-CUSTOM4 IS NOT INITIAL
        AND <LS_DATA>-CUSTOM9 IS NOT INITIAL.
        CLEAR MS_ACC_DOC.
        MS_ACC_DOC = VALUE #( BUKRS = SUBSTRING( VAL = CONDENSE( <LS_DATA>-CUSTOM9 ) LEN = 4 )
                              BELNR = SUBSTRING( VAL = CONDENSE( <LS_DATA>-CUSTOM1 ) LEN = 10 )
                              GJAHR = SUBSTRING( VAL = CONDENSE( <LS_DATA>-CUSTOM4 ) LEN = 4 ) ).

        MS_ACC_DOC-BELNR = |{ MS_ACC_DOC-BELNR ALPHA = IN }|.
        APPEND MS_ACC_DOC TO MT_ACC_DOC.

        <LS_DATA>-CUSTOM1 = MS_ACC_DOC-BELNR.
        <LS_DATA>-CUSTOM9 = MS_ACC_DOC-BUKRS.
        <LS_DATA>-CUSTOM4 = MS_ACC_DOC-GJAHR.
      ENDIF.

      " retrieve data for for all acc documents,
      " so that individual db operations can be avoided
      IF <LS_DATA>-SPLIT_GST_DOC_NUM IS NOT INITIAL.
        APPEND VALUE #( BUKRS = <LS_DATA>-SPLIT_COMP_CODE
                        BELNR = <LS_DATA>-SPLIT_GST_DOC_NUM
                        GJAHR = <LS_DATA>-SPLIT_YEAR ) TO MT_ACC_DOC.
      ENDIF.

      IF <LS_DATA>-JV_DOC_NUM IS NOT INITIAL.
        APPEND VALUE #( BUKRS = <LS_DATA>-JV_COMP_CODE
                        BELNR = <LS_DATA>-JV_DOC_NUM
                        GJAHR = <LS_DATA>-JV_YEAR ) TO MT_ACC_DOC.
      ENDIF.

      IF <LS_DATA>-JV_REV_DOC_NUM IS NOT INITIAL.
        APPEND VALUE #( BUKRS = <LS_DATA>-JV_REV_COMP_CODE
                        BELNR = <LS_DATA>-JV_REV_DOC_NUM
                        GJAHR = <LS_DATA>-JV_REV_YEAR ) TO MT_ACC_DOC.
      ENDIF.

      IF <LS_DATA>-DEBIT_NOTE_NUM IS NOT INITIAL.
        APPEND VALUE #( BUKRS = <LS_DATA>-DEBIT_NOTE_COMP
                        BELNR = <LS_DATA>-DEBIT_NOTE_NUM
                        GJAHR = <LS_DATA>-DEBIT_NOTE_YEAR ) TO MT_ACC_DOC.
      ENDIF.

      IF <LS_DATA>-DEBIT_NOTE_REV_NUM IS NOT INITIAL.
        APPEND VALUE #( BUKRS = <LS_DATA>-DEBIT_NOTE_REV_COMP
                        BELNR = <LS_DATA>-DEBIT_NOTE_REV_NUM
                        GJAHR = <LS_DATA>-DEBIT_NOTE_REV_YEAR ) TO MT_ACC_DOC.
      ENDIF.
    ENDLOOP.

    IF MT_ACC_DOC IS NOT INITIAL.
      SORT MT_ACC_DOC ASCENDING BY BUKRS BELNR GJAHR.
      DELETE ADJACENT DUPLICATES FROM MT_ACC_DOC COMPARING ALL FIELDS.
      DELETE MT_ACC_DOC WHERE BUKRS IS INITIAL
                          AND BELNR IS INITIAL
                          AND GJAHR IS INITIAL.

      IF MT_ACC_DOC IS NOT INITIAL.
        CLEAR MT_ACC_DATA.

        SELECT "A~*, B~*
        A~BUKRS,
        A~BELNR,
        A~GJAHR,
        B~BLDAT,
        B~BUDAT,
        B~CPUDT,
        B~XBLNR,
        B~BKTXT,
        B~AWTYP,
        B~AWKEY,
        B~AWSYS,
        B~BLART,
        B~GLVOR,
        A~BUZEI,
        A~BUZID,
        A~AUGDT,
        A~DMBTR,
        A~ZFBDT,
        A~ZBD1T,
        A~ZBD2T,
        A~ZBD3T,
        A~KOART,
        A~SHKZG,
        A~LIFNR,
        A~REBZG,
        A~ZUONR,
        A~SGTXT,
        A~MWSKZ,
        A~ZLSPR,
        A~UMSKZ,
        A~PRCTR,
        A~GSBER,
        A~KOSTL,
        A~BUPLA,
        A~SECCO,
        A~HSN_SAC,
        A~GST_PART,
        A~AUGBL,
        A~ZTERM
          FROM BSEG AS A
          INNER JOIN BKPF AS B
          ON  A~BUKRS = B~BUKRS
          AND A~BELNR = B~BELNR
          AND A~GJAHR = B~GJAHR
          FOR ALL ENTRIES IN @MT_ACC_DOC
          WHERE A~BUKRS = @MT_ACC_DOC-BUKRS
            AND A~BELNR = @MT_ACC_DOC-BELNR
            AND A~GJAHR = @MT_ACC_DOC-GJAHR
          INTO  TABLE @MT_ACC_DATA. "#EC CI_NO_TRANSFORM "#EC "#EC CI_SORTSEQ

*        DELETE MT_ACC_DATA WHERE LIFNR(2) = 'ZV' OR LIFNR(2) = 'ZC'.    "ADDED BY BHARGAV
*        DELETE MT_ACC_DATA WHERE ZLSPR = 'Y'.
        IF MT_ACC_DATA IS NOT INITIAL.
          SELECT *
            FROM ZCY_T_2A_BLK_TYP
            FOR ALL ENTRIES IN @MT_ACC_DATA
            WHERE  ( VENDOR = @MT_ACC_DATA-LIFNR OR VENDOR = @MT_ACC_DATA-GST_PART )
            INTO TABLE @MT_VENDOR_BLOCK_TYPE.      "#EC CI_NO_TRANSFORM

          " Block type categorisation based on vendor group
          " vendor group can also be maintained in vendor code field of zcy_t_2a_blk_typ
          DATA(LT_VENDOR_BLOCK_TYPE) = MT_VENDOR_BLOCK_TYPE[].

          CLEAR LT_VENDOR_BLOCK_TYPE.
          SELECT A~*,
                 B~LIFNR AS VENDOR      " overwrite acc grp with vendor code
            FROM ZCY_T_2A_BLK_TYP AS A                 "#EC CI_BUFFJOIN
            INNER JOIN LFA1 AS B
            ON A~VENDOR = B~KTOKK       " get all vendor codes for acc groups(vendor field) in zcy_t_2a_blk_typ
            INTO CORRESPONDING FIELDS OF TABLE @LT_VENDOR_BLOCK_TYPE.

          " vendor has higher preference than vendor group
          IF LT_VENDOR_BLOCK_TYPE IS NOT INITIAL.
            LOOP AT LT_VENDOR_BLOCK_TYPE INTO DATA(LS_VEND_BLOCK_TYPE).
              IF LINE_EXISTS( MT_VENDOR_BLOCK_TYPE[ VENDOR = LS_VEND_BLOCK_TYPE-VENDOR ] ).
                DELETE LT_VENDOR_BLOCK_TYPE WHERE VENDOR = LS_VEND_BLOCK_TYPE-VENDOR.
              ELSE.
                " sorted table
                INSERT LS_VEND_BLOCK_TYPE INTO TABLE MT_VENDOR_BLOCK_TYPE.
              ENDIF.
              CLEAR LS_VEND_BLOCK_TYPE.
            ENDLOOP.
          ENDIF.

          CLEAR MT_VENDOR_DATA.
          DATA(LT_ACC_DATA) = MT_ACC_DATA[].
          DELETE LT_ACC_DATA WHERE KOART <> MCS_ACC_TYPE-VENDOR. "#EC CI_SORTSEQ

          IF LT_ACC_DATA IS NOT INITIAL.
            SELECT A~LIFNR AS VENDOR,
                   A~NAME1 AS NAME,
                   A~STCD3 AS STCD3,
                   A~BRSCH,
                   B~BRTXT
              FROM LFA1 AS A
              LEFT OUTER JOIN T016T AS B ON A~BRSCH = B~BRSCH
              FOR ALL ENTRIES IN @LT_ACC_DATA
              WHERE ( A~LIFNR = @LT_ACC_DATA-LIFNR OR A~LIFNR = @LT_ACC_DATA-GST_PART )
              INTO TABLE @MT_VENDOR_DATA.         "#EC "#EC CI_BUFFJOIN

            SELECT BUKRS AS BUKRS,
                   LIFNR AS VENDOR,
                   MINDK AS MINDK
*                   stcd3 as stcd3
           FROM LFB1
           FOR ALL ENTRIES IN @LT_ACC_DATA
           WHERE ( LIFNR = @LT_ACC_DATA-LIFNR OR LIFNR = @LT_ACC_DATA-GST_PART ) AND BUKRS = @LT_ACC_DATA-BUKRS
           INTO TABLE @MT_VENDOR_DATA_M.

            SORT MT_VENDOR_DATA_M BY VENDOR.
          ENDIF.

          CLEAR MT_TAX_DATA.
        ENDIF.
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD COMPLETE_DATA.
    DATA: W_LEN TYPE I.
    IF MT_DATA IS NOT INITIAL.
      IF MT_ACC_DATA IS NOT INITIAL.
        SELECT BUKRS
               GJAHR
               BELNR
               BUZEI
               ZLSPR
               ZTERM
               AUGBL
               DMBTR
          FROM BSAK
           INTO TABLE LT_BSIK
           FOR ALL ENTRIES IN MT_ACC_DATA
              WHERE BELNR = MT_ACC_DATA-BELNR
              AND GJAHR = MT_ACC_DATA-GJAHR
              AND BUKRS = MT_ACC_DATA-BUKRS
              AND ZLSPR NE 'Y'.                    "#EC CI_NO_TRANSFORM
        SORT LT_BSIK BY BUKRS BELNR GJAHR .

      ENDIF.

      SELECT * FROM TVARVC INTO TABLE LT_TVARC WHERE NAME = 'ZCY_PAY_TERM' .

      SELECT PANNO FROM ZCYG_PANNO_EXMP INTO TABLE @DATA(LT_PANNO). "#EC "#EC CI_NOWHERE
      SELECT * FROM ZCY_T_2A_RECSTA1 INTO TABLE @DATA(LT_REASON). "#EC "#EC CI_NOWHERE
      LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA1>).
        <LS_DATA1>-PRECONCILIATIONSECTION = <LS_DATA1>-RECONCILIATIONSECTION.
        IF  <LS_DATA1>-FILINGDATE IS NOT INITIAL  .
*            <LS_DATA1>-RECONCILIATIONSECTION = 'Matched'.
          IF <LS_DATA1>-TAXDIFF < 0 AND <LS_DATA1>-RECONCILIATIONSECTION = 'MISMATCHED'.
            <LS_DATA1>-RECONCILIATIONSECTION = 'MATCHED'.
          ENDIF.
        ELSE.
          <LS_DATA1>-RECONCILIATIONSECTION = 'MISMATCHED'.
        ENDIF.
        IF <LS_DATA1>-RECONCILIATIONSECTION = 'MATCHED' OR <LS_DATA1>-RECONCILIATIONSECTION = 'MATCHEDDUETOTOLERANCE'
          OR <LS_DATA1>-RECONCILIATIONSECTION  = 'NEARMATCHED'.
        ELSE.
          IF  <LS_DATA1>-RECONCILIATIONSECTION = 'MISMATCHED' AND  <LS_DATA1>-WRONGGST IS INITIAL AND <LS_DATA1>-FILINGSTATUS = 'Y' AND <LS_DATA1>-FILINGDATE IS NOT INITIAL  .
*            <LS_DATA1>-RECONCILIATIONSECTION = 'MATCHED'.
          ENDIF.
        ENDIF.

        IF <LS_DATA1>-PRECONCILIATIONSECTION =  'CANCELLED'.
          <LS_DATA1>-RECONCILIATIONSECTION =  'CANCELLED'.
        ENDIF.
        W_LEN = STRLEN( <LS_DATA1>-GSTIN ).
        IF W_LEN >= 12.
          READ TABLE LT_PANNO INTO DATA(LS_PANNO) WITH KEY PANNO = <LS_DATA1>-GSTIN+2(10). "Bill from GSTIN
          IF SY-SUBRC = 0.
            <LS_DATA1>-RECONCILIATIONSECTION =  'NOTRELEVANT'.
          ENDIF.
        ENDIF.
        CLEAR: W_LEN.

        READ TABLE LT_REASON INTO DATA(LS_REASON) WITH KEY RECO_STATUS = <LS_DATA1>-PRECONCILIATIONSECTION
        REASON = <LS_DATA1>-REASON .
        IF SY-SUBRC = 0.
          IF LS_REASON-CATEGORY = 'M'.
            <LS_DATA1>-RECONCILIATIONSECTION = 'MATCHED'.
          ELSEIF LS_REASON-CATEGORY = 'B'..
            <LS_DATA1>-RECONCILIATIONSECTION = 'MISMATCHED'.
          ENDIF.
        ENDIF.
      ENDLOOP.

      LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA>).
        CLEAR <LS_DATA>-MESSAGE.

        " Get the accounting attributes for unprocessed documents
        IF <LS_DATA>-STATUS = MCS_DOC_STATUS-UNPROCESSED
          OR <LS_DATA>-STATUS = MCS_DOC_STATUS-WAITING_FOR_FILING_DATE
          OR <LS_DATA>-STATUS = MCS_DOC_STATUS-EXEMPTED.
          " store current selection criteria
          <LS_DATA>-PROCESSING_OPTIONS = GET_PROCESSING_OPTIONS( ).

          FILL_ACC_DATA(
            CHANGING
              CS_DATA = <LS_DATA> ).

          FILL_STATUS_RELEVANT_DATA(
            CHANGING
              CS_DATA = <LS_DATA> ).
        ENDIF.

        FILL_STATUS_INDEPENDENT_DATA(
          CHANGING
            CS_DATA = <LS_DATA> ).

        " Set document status icon
        DERIVE_STATUS_ICON(
          CHANGING
            CS_DATA = <LS_DATA> ).
      ENDLOOP.
    ENDIF.
  ENDMETHOD.

  METHOD FILL_ACC_DATA.
    IF CS_DATA-CUSTOM1 IS NOT INITIAL AND CS_DATA-CUSTOM4 IS NOT INITIAL
      AND CS_DATA-CUSTOM9 IS NOT INITIAL. " acc doc must be available

      DATA(LS_ACC_DATA) = VALUE #( MT_ACC_DATA[ KEY SEC_KEY
                                                  BUKRS = CS_DATA-CUSTOM9
                                                  BELNR = CS_DATA-CUSTOM1
                                                  GJAHR = CS_DATA-CUSTOM4
                                                  KOART = MCS_ACC_TYPE-VENDOR ] OPTIONAL ).

      DATA(LS_TAX_DATA) = VALUE #( MT_TAX_DATA[ KEY SEC_KEY
                                                  BUKRS = CS_DATA-CUSTOM9
                                                  BELNR = CS_DATA-CUSTOM1
                                                  GJAHR = CS_DATA-CUSTOM4 ] OPTIONAL ).

      DATA(LT_TAX_DATA) = FILTER #( MT_TAX_DATA USING KEY SEC_KEY
                                      WHERE BUKRS = CONV #( CS_DATA-CUSTOM9 )
                                        AND BELNR = CONV #( CS_DATA-CUSTOM1 )
                                        AND GJAHR = CONV #( CS_DATA-CUSTOM4 ) ).

      IF CS_DATA-FI_DOC_VALUE IS INITIAL.
        CS_DATA-FI_DOC_VALUE = LS_ACC_DATA-DMBTR.
      ENDIF.
      IF CS_DATA-BLART IS INITIAL.
        CS_DATA-BLART = LS_ACC_DATA-BLART.
      ENDIF.
      IF CS_DATA-TOTALTAX IS INITIAL.
        CS_DATA-FI_TAX_VALUE = REDUCE #( INIT LV_TAX_VAL TYPE BSET-HWSTE
                                        FOR LS_TAX_ITEM IN LT_TAX_DATA
                                        NEXT LV_TAX_VAL = LV_TAX_VAL + COND #( WHEN LS_TAX_ITEM-SHKZG = MCS_DC_IND-DEBIT
                                                                               THEN LS_TAX_ITEM-HWSTE
                                                                               WHEN LS_TAX_ITEM-SHKZG = MCS_DC_IND-CREDIT

                                                                      THEN ( LS_TAX_ITEM-HWSTE * -1 ) ) ).
      ELSE.
        CS_DATA-FI_TAX_VALUE = CS_DATA-TOTALTAX.
      ENDIF.

      IF CS_DATA-FI_TAX_CODE IS INITIAL.
        CS_DATA-FI_TAX_CODE = LS_TAX_DATA-MWSKZ.
      ENDIF.

      IF CS_DATA-FI_TAX_RATE IS INITIAL.
        CS_DATA-FI_TAX_RATE = REDUCE #( INIT LV_TAX_RATE TYPE BSET-KBETR
                                        FOR LS_TAX_ITEM IN LT_TAX_DATA
                                          WHERE ( TXGRP = LS_TAX_DATA-TXGRP
                                            AND TAXPS = LS_TAX_DATA-TAXPS )
                                        NEXT LV_TAX_RATE = ( LV_TAX_RATE + ( LS_TAX_ITEM-KBETR / 10 ) ) ). "#EC CI_SORTSEQ
      ENDIF.

      IF CS_DATA-VENDOR IS INITIAL.
        CS_DATA-VENDOR = LS_ACC_DATA-LIFNR.
        CS_DATA-GST_PART = LS_ACC_DATA-GST_PART.
      ENDIF.

      IF CS_DATA-PAYMENT_DUE_DATE IS INITIAL.
        CALL FUNCTION 'NET_DUE_DATE_GET'
          EXPORTING
            I_ZFBDT = LS_ACC_DATA-ZFBDT
            I_ZBD1T = LS_ACC_DATA-ZBD1T
            I_ZBD2T = LS_ACC_DATA-ZBD2T
            I_ZBD3T = LS_ACC_DATA-ZBD3T
            I_SHKZG = LS_ACC_DATA-SHKZG
            I_REBZG = LS_ACC_DATA-REBZG
            I_KOART = LS_ACC_DATA-KOART
          IMPORTING
            E_FAEDT = CS_DATA-PAYMENT_DUE_DATE.
      ENDIF.

      IF CS_DATA-CLEARING_DATE IS INITIAL.
        CS_DATA-CLEARING_DATE = LS_ACC_DATA-AUGDT.
      ENDIF.

      IF CS_DATA-CLEARING_NO IS INITIAL.
        CS_DATA-CLEARING_NO = LS_ACC_DATA-AUGBL.
      ENDIF.


    ENDIF.
  ENDMETHOD.

  METHOD FILL_STATUS_RELEVANT_DATA.
    CS_DATA-GSTIN = VALUE #( MT_VENDOR_DATA[ VENDOR = CS_DATA-VENDOR ]-STCD3 OPTIONAL ).
    CS_DATA-GSTIN_PART = VALUE #( MT_VENDOR_DATA[ VENDOR = CS_DATA-GST_PART ]-STCD3 OPTIONAL ).


    CS_DATA-MINDK = VALUE #( MT_VENDOR_DATA_M[ VENDOR = CS_DATA-VENDOR ]-MINDK OPTIONAL ).


    "Remove GST_PART on 07.11.2024
***    IF CS_DATA-GST_PART IS INITIAL.
    DATA(LS_VEND_BLOCK_TYPE) = VALUE #( MT_VENDOR_BLOCK_TYPE[ KEY PRIMARY_KEY
                                                                VENDOR = CS_DATA-VENDOR ] OPTIONAL ).
***    ELSE.
***      LS_VEND_BLOCK_TYPE = VALUE #( MT_VENDOR_BLOCK_TYPE[ KEY PRIMARY_KEY
***                                                                    VENDOR = CS_DATA-GST_PART ] OPTIONAL ).
***    ENDIF.
    "Remove GST_PART on 07.11.2024
    " Do not overwrite vendor block type of previously processed documents?
    " Forced exemption and release will not work in this case in process_documents( )
*    if cs_data-vendor_block_type is initial.
    " default block type = split - if block type is not maintained
    CS_DATA-VENDOR_BLOCK_TYPE = COND #( WHEN LS_VEND_BLOCK_TYPE-VENDOR_BLOCK_TYPE IS INITIAL
                                        THEN MCS_VENDOR_BLOCK_TYPE-SPLIT
                                        ELSE LS_VEND_BLOCK_TYPE-VENDOR_BLOCK_TYPE ).
*    endif.

    IF CS_DATA-FILINGDATE IS NOT INITIAL.
      DATA(LV_ACT_FILING_DATE) =
        CONV SY-DATLO( |{ CS_DATA-FILINGDATE+6(4) }{ CS_DATA-FILINGDATE+3(2) }{ CS_DATA-FILINGDATE+0(2) }| ).
    ENDIF.

    IF CS_DATA-EXPECTED_FILING_DATE IS INITIAL.
      " #ToDo: Verify expected filing date logic
      CS_DATA-EXPECTED_FILING_DATE =
        DETERMNE_EXP_FILING_DATE(
          EXPORTING
            IV_DOC_DATE = CS_DATA-DOCUMENTDATE
            IV_VENDOR   = CS_DATA-VENDOR ).
    ENDIF.

    IF CS_DATA-DUE_DATE_BEFORE_FILING IS INITIAL.
      IF CS_DATA-PAYMENT_DUE_DATE IS NOT INITIAL.
        IF CS_DATA-PAYMENT_DUE_DATE < CS_DATA-EXPECTED_FILING_DATE
          OR CS_DATA-PAYMENT_DUE_DATE < LV_ACT_FILING_DATE.
          CS_DATA-DUE_DATE_BEFORE_FILING = MCS_YES_NO-YES.
        ELSE.
          CS_DATA-DUE_DATE_BEFORE_FILING = MCS_YES_NO-NO.
        ENDIF.
      ENDIF.
    ENDIF.

    IF CS_DATA-CLEARED_BEFORE_FILING IS INITIAL.
      IF CS_DATA-CLEARING_DATE IS NOT INITIAL.
        IF CS_DATA-CLEARING_DATE < CS_DATA-EXPECTED_FILING_DATE
          OR CS_DATA-FILINGDATE IS INITIAL
          OR SY-DATLO < CS_DATA-EXPECTED_FILING_DATE.
          CS_DATA-CLEARED_BEFORE_FILING = MCS_YES_NO-YES.
        ELSE.
          CS_DATA-CLEARED_BEFORE_FILING = MCS_YES_NO-NO.
        ENDIF.
      ENDIF.
    ENDIF.

    IF CS_DATA-GRC_CHECK_APPLICABLE IS INITIAL.
      " GRC check default applicable unless maintained as unapplicable - As per vaibhav
      CS_DATA-GRC_CHECK_APPLICABLE = COND #( WHEN LS_VEND_BLOCK_TYPE-GRC_CHECK_APPLICABLE = MCS_YES_NO-NO
                                             THEN LS_VEND_BLOCK_TYPE-GRC_CHECK_APPLICABLE
                                             ELSE MCS_YES_NO-YES ).
    ENDIF.
  ENDMETHOD.

  METHOD DETERMNE_EXP_FILING_DATE.
    CONSTANTS:
      LC_FILING_DAY TYPE C LENGTH 2 VALUE '14', "#ToDo

      BEGIN OF LCS_FILING_FREQ,
        MONTHLY   TYPE C LENGTH 1 VALUE 'M',
        QUARTERLY TYPE C LENGTH 1 VALUE 'Q',
      END OF LCS_FILING_FREQ.

    DATA:
      LV_QUARTER_START TYPE SYST-DATLO,
      LV_QUARTER_END   TYPE SYST-DATLO,
      LV_FILING_FREQ   TYPE C LENGTH 1 VALUE LCS_FILING_FREQ-MONTHLY.

    CLEAR RV_EXP_FILING_DATE.

    " Based on iv_vendor logic can be set to determine the vendor's
    " GSTR1 filing freq(lv_filing_freq) i.e. Monthly(M) or Quarterly(Q) ------ #ToDo
    " For monthly filing, the exp filing date is 14th of the next
    " month from the doc date. Eg. if doc date = 25.07.22 then
    " exp filing date = 14.08.22
    " For quarterly filing, the exp fil date is 14th of the month
    " immediately after the end of the quarter of the doc date
    " Eg if doc date = 25.07.22, the doc date quarter is July - Sept
    " and the exp filin date = 14.10.2
    " Quarters = Jan - Mar, Apr - June, July - Sept, Oct - Dec

    " conv doc date to int format from DD-MM-YYYY
    SELECT SINGLE * FROM ZCY_GRC_SCORE INTO @DATA(LS_GRC) WHERE VENDOR = @IV_VENDOR.
    IF SY-SUBRC = 0.
      IF LS_GRC-GSTR_FILING_FRQY = 'Monthly'.
        LV_FILING_FREQ = 'M'.
      ELSEIF LS_GRC-GSTR_FILING_FRQY = ''.
        LV_FILING_FREQ = 'M'.
      ELSE.
        LV_FILING_FREQ = 'Q'.

      ENDIF.
    ELSE.
      LV_FILING_FREQ = 'M'.

    ENDIF.
    DATA(LV_DOC_DATE) = IV_DOC_DATE.

    IF LV_FILING_FREQ = LCS_FILING_FREQ-QUARTERLY.
      " 4 quarters per year
      DO 4 TIMES.
        CLEAR:
          LV_QUARTER_START,
          LV_QUARTER_END.

        CALL FUNCTION 'HR_99S_GET_DATES_QUARTER'
          EXPORTING
            IM_QUARTER = SY-INDEX          " quarter identified by loop index
            IM_YEAR    = LV_DOC_DATE+0(4)  " doc date year
          IMPORTING
            EX_BEGDA   = LV_QUARTER_START
            EX_ENDDA   = LV_QUARTER_END.

        IF LV_DOC_DATE BETWEEN LV_QUARTER_START AND LV_QUARTER_END.
          " month following the quarter end
          DATA(LV_NEXT_MONTH_DATE) = CL_RECA_DATE=>ADD_MONTHS_TO_DATE(
                                       EXPORTING
                                         ID_MONTHS = 1
                                         ID_DATE   = LV_QUARTER_END ).

          " document quarter found, exit do loop
          EXIT.
        ENDIF.
      ENDDO.
    ELSE.
      " month following the doc date
      LV_NEXT_MONTH_DATE = CL_RECA_DATE=>ADD_MONTHS_TO_DATE(
                             EXPORTING
                               ID_MONTHS = 1
                               ID_DATE   = LV_DOC_DATE ).
    ENDIF.

    IF LV_NEXT_MONTH_DATE IS NOT INITIAL.
      DATA(LV_NEXT_FILING_DATE) = |{ LV_NEXT_MONTH_DATE+0(6) }{ LC_FILING_DAY }|.
    ENDIF.

*    rv_exp_filing_date = lv_next_filing_date.
    RV_EXP_FILING_DATE = LV_NEXT_FILING_DATE . "expected filing date + 2 days

    RV_EXP_FILING_DATE = RV_EXP_FILING_DATE + 2.

  ENDMETHOD.

  METHOD DERIVE_STATUS_ICON.
    TYPE-POOLS ICON.

    CONSTANTS LC_DOMAIN_NAME TYPE DD07L-DOMNAME VALUE 'ZCY_DOM_2A_PAY_BLOCK_STATUS'.

    " Document processing Status
    CASE CS_DATA-STATUS.
      WHEN MCS_DOC_STATUS-UNPROCESSED.
        CS_DATA-PROCSTAT = ICON_LIGHT_OUT.
      WHEN MCS_DOC_STATUS-BLOCKED.
        CS_DATA-PROCSTAT = ICON_GIS_PAN.
      WHEN MCS_DOC_STATUS-SPLIT.
        CS_DATA-PROCSTAT = ICON_WORKFLOW_CONDITION.
      WHEN MCS_DOC_STATUS-RELEASED.
        CS_DATA-PROCSTAT = ICON_RELEASE.
        CS_DATA-CBOX     = ABAP_FALSE. " No further processing once released
      WHEN MCS_DOC_STATUS-MATCHED.
        CS_DATA-PROCSTAT = ICON_STATUS_OK.
        CS_DATA-CBOX     = ABAP_FALSE. " No further processing once matched
      WHEN MCS_DOC_STATUS-EXEMPTED.
        CS_DATA-PROCSTAT = ICON_CUSTOMER.
      WHEN MCS_DOC_STATUS-WAITING_FOR_FILING_DATE.
        CS_DATA-PROCSTAT = ICON_TIME.
      WHEN MCS_DOC_STATUS-DEBIT_NOTE_POSTED.
        CS_DATA-PROCSTAT = ICON_CONTENT_OBJECT.
      WHEN MCS_DOC_STATUS-IRELEASED.
        CS_DATA-PROCSTAT = ICON_AGENT.
      WHEN OTHERS.
    ENDCASE.

    DATA(LT_DOMAIN_VALUE) = GET_DOMAIN_VALUES( EXPORTING IV_DOMAIN_NAME = LC_DOMAIN_NAME ).

    CS_DATA-STATUS_DESC =
      VALUE #( LT_DOMAIN_VALUE[ DOMVALUE_L = CS_DATA-STATUS ]-DDTEXT OPTIONAL ).

    IF CS_DATA-MESSAGE = TEXT-013.
      CS_DATA-PROCSTAT = ICON_RED_LIGHT.
    ENDIF.

    " Reco status
    IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MATCHED[].
      CS_DATA-RECO_STAT = ICON_GREEN_LIGHT.
    ELSEIF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MISMATCHED[].
      CS_DATA-RECO_STAT = ICON_RED_LIGHT.
    ELSE.
      CS_DATA-RECO_STAT = ICON_YELLOW_LIGHT.
    ENDIF.
  ENDMETHOD.

  METHOD GET_DOMAIN_VALUES.
    CLEAR RT_DOMAIN_VALUE.

    IF IV_DOMAIN_NAME IS NOT INITIAL.
      CALL FUNCTION 'DD_DOMVALUES_GET'
        EXPORTING
          DOMNAME        = IV_DOMAIN_NAME
          TEXT           = ABAP_TRUE
          LANGU          = SY-LANGU
        TABLES
          DD07V_TAB      = RT_DOMAIN_VALUE
        EXCEPTIONS
          WRONG_TEXTFLAG = 1
          OTHERS         = 2.
      IF SY-SUBRC <> 0.                                "#EC FM_SUBRC_OK
      ENDIF.
    ENDIF.
  ENDMETHOD.

METHOD FILL_STATUS_INDEPENDENT_DATA.
    CONSTANTS LC_DOMAIN_NAME TYPE DD07L-DOMNAME VALUE 'ZCY_DOM_BLOCK_TYPE'.

    CS_DATA-VENDOR_NAME = VALUE #( MT_VENDOR_DATA[ VENDOR = CS_DATA-VENDOR ]-NAME OPTIONAL ).
    CS_DATA-MSME = VALUE #( MT_VENDOR_DATA[ VENDOR = CS_DATA-VENDOR ]-BRTXT OPTIONAL ).

    DATA(LT_DOMAIN_VALUE) = GET_DOMAIN_VALUES( EXPORTING IV_DOMAIN_NAME = LC_DOMAIN_NAME ).

    CS_DATA-BLOCK_TYPE_DESC =
      VALUE #( LT_DOMAIN_VALUE[ DOMVALUE_L = CS_DATA-VENDOR_BLOCK_TYPE ]-DDTEXT OPTIONAL ).

    READ TABLE LT_BSIK INTO DATA(LS_CLR) WITH KEY BUKRS = CS_DATA-CUSTOM9 BELNR = CS_DATA-CUSTOM1 GJAHR = CS_DATA-CUSTOM4
    BINARY SEARCH.
    IF SY-SUBRC = 0.
      CS_DATA-CLEARING_NO = LS_CLR-AUGBL.
    ELSE.
      CLEAR:CS_DATA-CLEARING_NO.
*CS_DATA-CLEARING_NO = ls_clr-AUGB.
    ENDIF.

  ENDMETHOD.

  METHOD GET_CHAR_DATE_RANGE.
    DATA:
      LV_DAY   TYPE C LENGTH 2,
      LV_MONTH TYPE C LENGTH 2,
      LV_YEAR  TYPE C LENGTH 4.

    CLEAR RT_DATE[].

    IF S_DATE[] IS NOT INITIAL.
      SELECT DISTINCT DOCUMENTDATE
        FROM ZCY_TAB_RRP_E
        INTO TABLE @DATA(LT_DOC_DATE).                  "#EC CI_NOWHERE

      IF LT_DOC_DATE IS NOT INITIAL.
        LOOP AT LT_DOC_DATE INTO DATA(LS_DOC_DATE).
          CLEAR:
            LV_DAY,
            LV_MONTH,
            LV_YEAR.

          SPLIT LS_DOC_DATE-DOCUMENTDATE AT '-' INTO LV_DAY LV_MONTH LV_YEAR.
          DATA(LV_DATE) =
            CONV SYST-DATLO( |{ LV_YEAR }{ LV_MONTH ALPHA = IN }{ LV_DAY ALPHA = IN }| ).

          IF LV_DATE IN S_DATE[].
            APPEND VALUE #( SIGN = 'I' OPTION = 'EQ' LOW = LS_DOC_DATE-DOCUMENTDATE )
              TO RT_DATE[].
          ENDIF.

          CLEAR LS_DOC_DATE.
        ENDLOOP.
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD DISPLAY_ALV.
    CONSTANTS LC_SAVE_ALL TYPE KKBLO_LAYOUT-SAVE VALUE 'A'.

    IF MO_ALV IS NOT BOUND.
      IF NOT CL_GUI_ALV_GRID=>OFFLINE( ).
        CREATE OBJECT MO_ALV
          EXPORTING
            I_PARENT          = CL_GUI_CONTAINER=>DEFAULT_SCREEN
          EXCEPTIONS
            ERROR_CNTL_CREATE = 1
            ERROR_CNTL_INIT   = 2
            ERROR_CNTL_LINK   = 3
            ERROR_DP_CREATE   = 4
            OTHERS            = 5.
      ENDIF.

      IF MO_ALV IS BOUND.
        " generate fieldcatalog for the grid
        DATA(LT_FCAT) = SET_GRID_FCAT( ).

        " set up layout and display settings for the grid
        DATA(LS_LAYOUT) = SET_GRID_LAYOUT( ).
        DELETE LT_FCAT WHERE FIELDNAME = 'GST_PART'.
        DELETE LT_FCAT WHERE FIELDNAME = 'GSTIN_PART'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPTRADENAME'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPREVERSECHARGE'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPTRANSACTIONTYPE'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPTAXPAYERTYPE'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPRATE'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPITCAVAILABILITY'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPRETURNPERIOD'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPISAMENDMENT'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPAVAILABLEINGSTR2B'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPGSTR2BRETURNPERIOD'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPAVAILABLEINGSTR98A'.
        DELETE LT_FCAT WHERE FIELDNAME = 'CPITCCLAIMMONTH'.
        DELETE LT_FCAT WHERE FIELDNAME = 'BILLFROMTRADENAME'.

        " exclude additional editing options from toolbar
        DATA(LT_TOOLBAR_EXCLUDING) = SET_GRID_TOOLBAR_EXCLUDING( ).
        DATA: LS_VARIANT TYPE DISVARIANT.
        LS_VARIANT-REPORT = SY-REPID.
        MO_ALV->SET_TABLE_FOR_FIRST_DISPLAY(
          EXPORTING
            IS_LAYOUT   = LS_LAYOUT
            IS_VARIANT  = LS_VARIANT
            I_SAVE      = LC_SAVE_ALL
            I_DEFAULT   = ABAP_TRUE
            IT_TOOLBAR_EXCLUDING = LT_TOOLBAR_EXCLUDING
          CHANGING
            IT_FIELDCATALOG = LT_FCAT
            IT_OUTTAB       = MT_DATA
          EXCEPTIONS
            INVALID_PARAMETER_COMBINATION = 1
            PROGRAM_ERROR                 = 2
            TOO_MANY_LINES                = 3
            OTHERS                        = 4 ).

        " tell the grid to support functions for a editable mode
        SET_GRID_EDITABLE( ).

        " set up handlers for grid specific events
        SET_GRID_EVENT_HANDLERS( ).

        CALL SCREEN '0100'.
      ENDIF.
    ELSE.
      REFRESH_GRID( ).
    ENDIF.
  ENDMETHOD.
  METHOD SET_GRID_FCAT.
    CONSTANTS LC_STRUCT_NAME TYPE DD02L-TABNAME VALUE 'ZCY_STR_2A_PAY_BLOCK'.

    CLEAR RT_FCAT.
    CALL FUNCTION 'LVC_FIELDCATALOG_MERGE'
      EXPORTING
        I_STRUCTURE_NAME       = LC_STRUCT_NAME
      CHANGING
        CT_FIELDCAT            = RT_FCAT
      EXCEPTIONS
        INCONSISTENT_INTERFACE = 1
        PROGRAM_ERROR          = 2
        OTHERS                 = 3.
    IF SY-SUBRC <> 0.
* Implement suitable error handling here
    ENDIF.

    IF RT_FCAT IS NOT INITIAL.
      LOOP AT RT_FCAT ASSIGNING FIELD-SYMBOL(<LS_FCAT>).
        <LS_FCAT>-NO_OUT = ABAP_FALSE.
        <LS_FCAT>-NO_ZERO = ABAP_TRUE.
        <LS_FCAT>-COLDDICTXT = 'M'.
        <LS_FCAT>-KEY = ABAP_FALSE.
        <LS_FCAT>-EMPHASIZE = SPACE.
        CASE <LS_FCAT>-FIELDNAME.
          WHEN 'MANDT'.
            <LS_FCAT>-NO_OUT = ABAP_TRUE.
            <LS_FCAT>-TECH   = ABAP_TRUE.
          WHEN 'PROCSTAT'.
            <LS_FCAT>-SCRTEXT_L  = |Processing Status|.
            <LS_FCAT>-SCRTEXT_M  = |Proc. Status|.
            <LS_FCAT>-SCRTEXT_S  = |ProcStat|.
            <LS_FCAT>-ICON       = ABAP_TRUE.
            <LS_FCAT>-FIX_COLUMN = ABAP_TRUE.       " alternative for salv set_key_fixation
            <LS_FCAT>-COLDDICTXT = 'S'.
            <LS_FCAT>-COL_POS    = 1.
          WHEN 'CBOX'.
            <LS_FCAT>-SCRTEXT_L  = |Selection Status|.
            <LS_FCAT>-SCRTEXT_M  = |Selected?|.
            <LS_FCAT>-SCRTEXT_S  = |Select|.
            <LS_FCAT>-FIX_COLUMN = ABAP_TRUE.
            <LS_FCAT>-EDIT       = ABAP_TRUE.
            <LS_FCAT>-CHECKBOX   = ABAP_TRUE.
            <LS_FCAT>-COLDDICTXT = 'S'.
            <LS_FCAT>-COL_POS    = 1.
          WHEN 'REMARK'.
            <LS_FCAT>-SCRTEXT_L  = |Remark|.
            <LS_FCAT>-SCRTEXT_M  = |Remark|.
            <LS_FCAT>-SCRTEXT_S  = |Remark|.
            <LS_FCAT>-EDIT       = ABAP_TRUE.
            <LS_FCAT>-COLDDICTXT = 'S'.
            <LS_FCAT>-COL_POS    = 1.

          WHEN 'RECO_STAT'.
            <LS_FCAT>-SCRTEXT_L  = |Reconcilation Status|.
            <LS_FCAT>-SCRTEXT_M  = |Reco. Status|.
            <LS_FCAT>-SCRTEXT_S  = |Reco Stat|.
            <LS_FCAT>-ICON       = ABAP_TRUE.
            <LS_FCAT>-FIX_COLUMN = ABAP_TRUE.       " alternative for salv set_key_fixation
            <LS_FCAT>-COLDDICTXT = 'S'.
            <LS_FCAT>-COL_POS    = 1.
          WHEN 'FINANCIALYEAR'.
            <LS_FCAT>-SCRTEXT_L = |2A Fiscal Year|.
            <LS_FCAT>-SCRTEXT_M = |2A Fiscal Year|.
            <LS_FCAT>-SCRTEXT_S = |2A FY|.
          WHEN 'RETURNPERIOD'.
            <LS_FCAT>-SCRTEXT_L = |Return Period|.
            <LS_FCAT>-SCRTEXT_M = |Return Period|.
            <LS_FCAT>-SCRTEXT_S = |Ret Prd|.
          WHEN 'LOCATIONGSTIN'.
            <LS_FCAT>-SCRTEXT_L = |Location GSTIN|.
            <LS_FCAT>-SCRTEXT_M = |Location GSTIN|.
            <LS_FCAT>-SCRTEXT_S = |Loc GSTIN|.
*            <LS_FCAT>-LZERO = 'X'.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'DOCUMENTNUMBER'.
            <LS_FCAT>-SCRTEXT_L = |Invoice Reference|.
            <LS_FCAT>-SCRTEXT_M = |Invoice Ref.|.
            <LS_FCAT>-SCRTEXT_S = |Inv Ref|.
          WHEN 'DOCUMENTDATE'.
            <LS_FCAT>-SCRTEXT_L = |Document Date|.
            <LS_FCAT>-SCRTEXT_M = |Document Date|.
            <LS_FCAT>-SCRTEXT_S = |Doc Date|.
          WHEN 'CUSTOM1'.
            <LS_FCAT>-SCRTEXT_L = |FI Document Number|.
            <LS_FCAT>-SCRTEXT_M = |FI Document Num|.
            <LS_FCAT>-SCRTEXT_S = |FI Doc Num|.
            <LS_FCAT>-HOTSPOT = 'X'.
          WHEN 'CUSTOM9'.
            <LS_FCAT>-SCRTEXT_L = |FI Company Code|.
            <LS_FCAT>-SCRTEXT_M = |FI Company Code|.
            <LS_FCAT>-SCRTEXT_S = |FI Comp Code|.
          WHEN 'CUSTOM4'.
            <LS_FCAT>-SCRTEXT_L = |FI Fiscal Year|.
            <LS_FCAT>-SCRTEXT_M = |FI Fiscal Year|.
            <LS_FCAT>-SCRTEXT_S = |FI FY|.
          WHEN 'DOCUMENTVALUE'.
            <LS_FCAT>-SCRTEXT_L = |Document Value|.
            <LS_FCAT>-SCRTEXT_M = |Document Value|.
            <LS_FCAT>-SCRTEXT_S = |Doc Val|.
          WHEN 'PUSHSTATUS'.
            <LS_FCAT>-SCRTEXT_L = |Push Status|.
            <LS_FCAT>-SCRTEXT_M = |Push Status|.
            <LS_FCAT>-SCRTEXT_S = |Push Sts|.
            <LS_FCAT>-NO_OUT    = ABAP_TRUE.
          WHEN 'PUSHDATE'.
            <LS_FCAT>-SCRTEXT_L = |Push Date|.
            <LS_FCAT>-SCRTEXT_M = |Push Date|.
            <LS_FCAT>-SCRTEXT_S = |Push Date|.
            <LS_FCAT>-NO_OUT    = ABAP_TRUE.
          WHEN 'RECONCILIATIONSECTION'.
            <LS_FCAT>-SCRTEXT_L  = |Reconciliation Status|.
            <LS_FCAT>-SCRTEXT_M  = |Reco Status|.
            <LS_FCAT>-SCRTEXT_S  = |Reco Sts|.
            <LS_FCAT>-COL_POS    = 1.
            <LS_FCAT>-FIX_COLUMN = ABAP_TRUE.
          WHEN 'PRECONCILIATIONSECTION'.
            <LS_FCAT>-SCRTEXT_L  = |Portal Reconciliation Status|.
            <LS_FCAT>-SCRTEXT_M  = |Portal Reco Status|.
            <LS_FCAT>-SCRTEXT_S  = |Portal Reco Sts|.
            <LS_FCAT>-COL_POS    = 1.
            <LS_FCAT>-FIX_COLUMN = ABAP_TRUE.

          WHEN 'REASON'.
            <LS_FCAT>-SCRTEXT_L = |Reco Status Reason|.
            <LS_FCAT>-SCRTEXT_M = |Reco Reason|.
            <LS_FCAT>-SCRTEXT_S = |Reco Rsn|.
            <LS_FCAT>-NO_OUT    = ABAP_TRUE.
          WHEN 'FILINGDATE'.
            <LS_FCAT>-SCRTEXT_L = |Filing Date|.
            <LS_FCAT>-SCRTEXT_M = |Filing Date|.
            <LS_FCAT>-SCRTEXT_S = |Fil. Date|.
          WHEN 'FILINGRETURNPERIOD|'.
            <LS_FCAT>-SCRTEXT_L = |Filing Return Period|.
            <LS_FCAT>-SCRTEXT_M = |Fil Ret Period|.
            <LS_FCAT>-SCRTEXT_S = |F. Ret Prd|.
          WHEN 'FILINGSTATUS'.
            <LS_FCAT>-SCRTEXT_L = |Filing Status|.
            <LS_FCAT>-SCRTEXT_M = |Filing Status|.
            <LS_FCAT>-SCRTEXT_S = |Fil. Sts|.
          WHEN 'RATE'.
            <LS_FCAT>-SCRTEXT_L = |Tax Rate|.
            <LS_FCAT>-SCRTEXT_M = |Tax Rate|.
            <LS_FCAT>-SCRTEXT_S = |Tax Rate|.
          WHEN 'TAXABLEVALUE'.
            <LS_FCAT>-SCRTEXT_L = |Taxable Value|.
            <LS_FCAT>-SCRTEXT_M = |Taxable Value|.
            <LS_FCAT>-SCRTEXT_S = |Taxable V.|.
          WHEN 'IGSTAMOUNT'.
            <LS_FCAT>-SCRTEXT_L = |IGST Amount|.
            <LS_FCAT>-SCRTEXT_M = |IGST Amount|.
            <LS_FCAT>-SCRTEXT_S = |IGST Amt|.
          WHEN 'CGSTAMOUNT'.
            <LS_FCAT>-SCRTEXT_L = |CGST Amount|.
            <LS_FCAT>-SCRTEXT_M = |CGST Amount|.
            <LS_FCAT>-SCRTEXT_S = |CGST Amt|.
          WHEN 'SGSTAMOUNT'.
            <LS_FCAT>-SCRTEXT_L = |SGST Amount|.
            <LS_FCAT>-SCRTEXT_M = |SGST Amount|.
            <LS_FCAT>-SCRTEXT_S = |SGST Amt|.
          WHEN 'TOTALTAX'.
            <LS_FCAT>-SCRTEXT_L = |Total Tax|.
            <LS_FCAT>-SCRTEXT_M = |Total Tax|.
            <LS_FCAT>-SCRTEXT_S = |Tot Tax|.
          WHEN 'REC_DATE'.
            <LS_FCAT>-SCRTEXT_L = |Reconciliation Date|.
            <LS_FCAT>-SCRTEXT_M = |Reco Date|.
            <LS_FCAT>-SCRTEXT_S = |Reco Date|.
          WHEN 'REC_TIME'.
            <LS_FCAT>-SCRTEXT_L = |Reconciliation Time|.
            <LS_FCAT>-SCRTEXT_M = |Reco Time|.
            <LS_FCAT>-SCRTEXT_S = |Reco Time|.
          WHEN 'FI_DOC_VALUE'.
            <LS_FCAT>-SCRTEXT_L = |FI Document Value|.
            <LS_FCAT>-SCRTEXT_M = |FI Doc Value|.
            <LS_FCAT>-SCRTEXT_S = |FI Doc Val|.
*          when 'FI_TAX_VALUE'.
*            <ls_fcat>-scrtext_l = |ITC FI Tax Amount|.
*            <ls_fcat>-scrtext_m = |ITC FI Tax Amount|.
*            <ls_fcat>-scrtext_s = |ITC FI Tax Amt|.
          WHEN 'FI_TAX_VALUE'.
            <LS_FCAT>-SCRTEXT_L = |Cred.FI Tax Amount|.
            <LS_FCAT>-SCRTEXT_M = |Cred. Tax Amount|.
            <LS_FCAT>-SCRTEXT_S = |Cred.FI Tax Amt|.
          WHEN 'FI_TAX_CODE'.
            <LS_FCAT>-SCRTEXT_L = |FI Tax Code|.
            <LS_FCAT>-SCRTEXT_M = |FI Tax Code|.
            <LS_FCAT>-SCRTEXT_S = |FI Tax Cd|.
          WHEN 'FI_TAX_RATE'.
            <LS_FCAT>-SCRTEXT_L = |FI Tax Rate|.
            <LS_FCAT>-SCRTEXT_M = |FI Tax Rate|.
            <LS_FCAT>-SCRTEXT_S = |FI Tax Rt|.
          WHEN 'STATUS'.
            <LS_FCAT>-NO_OUT = ABAP_TRUE.
          WHEN 'STATUS_DESC'.
            <LS_FCAT>-SCRTEXT_L = |Document Processing Status|.
            <LS_FCAT>-SCRTEXT_M = |Document Status|.
            <LS_FCAT>-SCRTEXT_S = |Doc Stat|.
          WHEN 'VENDOR_NAME'.
            <LS_FCAT>-SCRTEXT_L = |Vendor Name|.
            <LS_FCAT>-SCRTEXT_M = |Vendor Name|.
            <LS_FCAT>-SCRTEXT_S = |Vend Name|.
          WHEN 'VENDOR_BLOCK_TYPE'.
            <LS_FCAT>-NO_OUT = ABAP_TRUE.
          WHEN 'BLOCK_TYPE_DESC'.
            <LS_FCAT>-SCRTEXT_L = |Vendor Block Type|.
            <LS_FCAT>-SCRTEXT_M = |Vend Block Type|.
            <LS_FCAT>-SCRTEXT_S = |Block Type|.
          WHEN 'CLEARING_DATE'.
            <LS_FCAT>-SCRTEXT_L = |Clearing Date|.
            <LS_FCAT>-SCRTEXT_M = |Clearing Date|.
            <LS_FCAT>-SCRTEXT_S = |Clrg Date|.
          WHEN 'GSTIN_PART'.
            <LS_FCAT>-SCRTEXT_L = |GSTIN_PART GSTIN|.
            <LS_FCAT>-SCRTEXT_M = |GSTIN_PART GSTIN|.
            <LS_FCAT>-SCRTEXT_S = |GSTIN_PART GSTIN|.
          WHEN 'DEBIT_NOTE_NUM'.
            <LS_FCAT>-HOTSPOT = 'X'.
          WHEN 'MESSAGE'.
            <LS_FCAT>-COL_POS    = 1.
            <LS_FCAT>-FIX_COLUMN = ABAP_TRUE.
          WHEN 'PROCESSING_OPTIONS'.
            <LS_FCAT>-NO_OUT = ABAP_TRUE.
          WHEN 'GSTIN'.
            <LS_FCAT>-SCRTEXT_L = |Bill From GSTIN|.
            <LS_FCAT>-SCRTEXT_M = |Bill From GSTIN|.
            <LS_FCAT>-SCRTEXT_S = |Bill From GSTIN|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'WRONGGST'.
            <LS_FCAT>-SCRTEXT_L = |Wrong Location GSTIN|.
            <LS_FCAT>-SCRTEXT_M = |Wrong Loc. GSTIN|.
            <LS_FCAT>-SCRTEXT_S = |Wrong Loc. GSTIN|.
          WHEN 'DOCUMENTTYPE'.
            <LS_FCAT>-SCRTEXT_L = |Document Type|.
            <LS_FCAT>-SCRTEXT_M = |Document Type|.
            <LS_FCAT>-SCRTEXT_S = |Doc.Type|.
          WHEN 'CPGSTIN'.
            <LS_FCAT>-SCRTEXT_L = |CP GSTIN|.
            <LS_FCAT>-SCRTEXT_M = |CP GSTIN|.
            <LS_FCAT>-SCRTEXT_S = |CP GSTIN|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPLEGALNAME'.
            <LS_FCAT>-SCRTEXT_L = |CP Legalname|.
            <LS_FCAT>-SCRTEXT_M = |CP Legalname|.
            <LS_FCAT>-SCRTEXT_S = |CP Legalname|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPDOCUMENTNUMBER'.
            <LS_FCAT>-SCRTEXT_L = |CP Document No.|.
            <LS_FCAT>-SCRTEXT_M = |CP Document No.|.
            <LS_FCAT>-SCRTEXT_S = |CP Doc.No.|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPDOCUMENTDATE'.
            <LS_FCAT>-SCRTEXT_L = |CP Document Date|.
            <LS_FCAT>-SCRTEXT_M = |CP Document Date|.
            <LS_FCAT>-SCRTEXT_S = |CP Doc.Dt.|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPVALUE'.
            <LS_FCAT>-SCRTEXT_L = |CP Value|.
            <LS_FCAT>-SCRTEXT_M = |CP Value|.
            <LS_FCAT>-SCRTEXT_S = |CP Value|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPPOS'.
            <LS_FCAT>-SCRTEXT_L = |CP pos|.
            <LS_FCAT>-SCRTEXT_M = |CP pos|.
            <LS_FCAT>-SCRTEXT_S = |CP pos|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPTAXABLEVALUE'.
            <LS_FCAT>-SCRTEXT_L = |CP Taxable Value|.
            <LS_FCAT>-SCRTEXT_M = |CP Taxable Value|.
            <LS_FCAT>-SCRTEXT_S = |CP Taxable Value|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPIGSTAMOUNT'.
            <LS_FCAT>-SCRTEXT_L = |CP IGST Amount|.
            <LS_FCAT>-SCRTEXT_M = |CP IGST Amount|.
            <LS_FCAT>-SCRTEXT_S = |CP IGST Amount|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPCGSTAMOUNT'.
            <LS_FCAT>-SCRTEXT_L = |CP CGST Amount|.
            <LS_FCAT>-SCRTEXT_M = |CP CGST Amount|.
            <LS_FCAT>-SCRTEXT_S = |CP CGST Amount|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPSGSTAMOUNT'.
            <LS_FCAT>-SCRTEXT_L = |CP SGST Amount|.
            <LS_FCAT>-SCRTEXT_M = |CP SGST Amount|.
            <LS_FCAT>-SCRTEXT_S = |CP SGST Amount|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPCESSAMOUNT'.
            <LS_FCAT>-SCRTEXT_L = |CP Cess Amount|.
            <LS_FCAT>-SCRTEXT_M = |CP Cess Amount|.
            <LS_FCAT>-SCRTEXT_S = |CP Cess Amount|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPFILINGSTATUS'.
            <LS_FCAT>-SCRTEXT_L = |CP Filing Status|.
            <LS_FCAT>-SCRTEXT_M = |CP Filing Status|.
            <LS_FCAT>-SCRTEXT_S = |CP Filing Status|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPFILINGDATE'.
            <LS_FCAT>-SCRTEXT_L = |CP Filing Date|.
            <LS_FCAT>-SCRTEXT_M = |CP Filing Date|.
            <LS_FCAT>-SCRTEXT_S = |CP Filing Date|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN 'CPFILINGRETURNPERIOD'.
            <LS_FCAT>-SCRTEXT_L = |CP Filing Return Period|.
            <LS_FCAT>-SCRTEXT_M = |CP Filing Return Period|.
            <LS_FCAT>-SCRTEXT_S = |CP Filing Return Period|.
            <LS_FCAT>-NO_ZERO = ABAP_FALSE.
          WHEN OTHERS.
        ENDCASE.
      ENDLOOP.
    ENDIF.
  ENDMETHOD.

  METHOD SET_GRID_LAYOUT.
    IF MO_ALV IS BOUND.
      CLEAR RS_LAYOUT.
      RS_LAYOUT-CWIDTH_OPT = ABAP_TRUE.          " alternative for salv set_optimize
      RS_LAYOUT-NO_ROWMARK = ABAP_TRUE.
      RS_LAYOUT-SEL_MODE   = ABAP_FALSE.         " alternative for salv set_selection_mode
      RS_LAYOUT-NO_KEYFIX  = ABAP_TRUE.
*****      RS_LAYOUT-BOX_FNAME  = 'CBOX'.
*      rs_layout-zebra      = abap_true.
    ENDIF.
  ENDMETHOD.

  METHOD SET_GRID_TOOLBAR_EXCLUDING.
    REFRESH RT_TOOLBAR_EXCLUDING.
    RT_TOOLBAR_EXCLUDING = VALUE #( ( CL_GUI_ALV_GRID=>MC_FC_LOC_COPY_ROW )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_DELETE_ROW )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_APPEND_ROW )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_INSERT_ROW )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_MOVE_ROW )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_COPY )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_CUT )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_PASTE )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_PASTE_NEW_ROW )
                                    ( CL_GUI_ALV_GRID=>MC_FC_LOC_UNDO )
                                    ( CL_GUI_ALV_GRID=>MC_FC_INFO )
                                    ( CL_GUI_ALV_GRID=>MC_FC_GRAPH ) ).
  ENDMETHOD.

  METHOD SET_GRID_EDITABLE.
    CHECK MO_ALV IS BOUND.
    MO_ALV->SET_READY_FOR_INPUT(
      EXPORTING
        I_READY_FOR_INPUT = 1 ).

    MO_ALV->REGISTER_EDIT_EVENT(
      EXPORTING
        I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVT_ENTER
      EXCEPTIONS
        OTHERS = 1 ).

    MO_ALV->REGISTER_EDIT_EVENT(
      EXPORTING
        I_EVENT_ID = CL_GUI_ALV_GRID=>MC_EVT_MODIFIED
      EXCEPTIONS
        OTHERS = 1 ).
  ENDMETHOD.

  METHOD SET_GRID_EVENT_HANDLERS.
    IF MO_ALV IS BOUND.
      SET HANDLER TOOLBAR FOR MO_ALV.
      SET HANDLER USER_COMMAND FOR MO_ALV.
      SET HANDLER AFTER_USER_COMMAND FOR MO_ALV.
      SET HANDLER DATA_CHANGED FOR MO_ALV.
      SET HANDLER ON_HOTSPOT_CLICK FOR MO_ALV.
      SET HANDLER DATA_CHANGED_FINISHED FOR MO_ALV.

      MO_ALV->SET_TOOLBAR_INTERACTIVE( ). " raises toolbar event
    ENDIF.
  ENDMETHOD.

  METHOD TOOLBAR.
    IF SENDER IS BOUND.
      INSERT VALUE #( FUNCTION  = |&SEL_A|
                      BUTN_TYPE = '0'
                      ICON      = ICON_SELECT_ALL
                      QUICKINFO = |Select All| ) INTO E_OBJECT->MT_TOOLBAR INDEX 1.

      INSERT VALUE #( FUNCTION  = |&DSEL_A|
                      BUTN_TYPE = '0'
                      ICON      = ICON_DESELECT_ALL
                      QUICKINFO = |Deselect All| ) INTO E_OBJECT->MT_TOOLBAR INDEX 2.

      INSERT VALUE #( FUNCTION  = |&&SEP08|
                      BUTN_TYPE = '3' ) INTO E_OBJECT->MT_TOOLBAR INDEX 3.
    ENDIF.
  ENDMETHOD.

  METHOD USER_COMMAND.
    IF SENDER IS BOUND AND SENDER = MO_ALV.
      CASE E_UCOMM.
        WHEN '&SEL_A'.
          SELECT_ALL( ).
          REFRESH_GRID( ).
        WHEN '&DSEL_A'.
          DESELECT_ALL( ).
          REFRESH_GRID( ).
        WHEN OTHERS.
      ENDCASE.
    ENDIF.
  ENDMETHOD.

  METHOD DATA_CHANGED.
    LOOP AT ER_DATA_CHANGED->MT_GOOD_CELLS INTO DATA(LS_GOOD_CELL).
      ASSIGN MT_DATA[ LS_GOOD_CELL-ROW_ID ] TO FIELD-SYMBOL(<LS_DATA>).
      IF <LS_DATA> IS ASSIGNED.
        IF LS_GOOD_CELL-VALUE IS NOT INITIAL.
          CASE LS_GOOD_CELL-FIELDNAME.
            WHEN 'CBOX'.
              IF <LS_DATA>-STATUS = MCS_DOC_STATUS-MATCHED
                OR <LS_DATA>-STATUS = MCS_DOC_STATUS-RELEASED.
                ER_DATA_CHANGED->ADD_PROTOCOL_ENTRY(
                  EXPORTING
                    I_FIELDNAME = LS_GOOD_CELL-FIELDNAME
                    I_ROW_ID = LS_GOOD_CELL-ROW_ID
                    I_TABIX = LS_GOOD_CELL-TABIX
                    I_MSGNO = '001'
                    I_MSGID = '00'
                    I_MSGTY = 'E'
                    I_MSGV1 = |Selected document is already processed| ).
              ENDIF.
            WHEN OTHERS.
          ENDCASE.
        ENDIF.
      ENDIF.
      CLEAR:
        LS_GOOD_CELL.
    ENDLOOP.

    IF ER_DATA_CHANGED->MT_PROTOCOL IS NOT INITIAL.
      ER_DATA_CHANGED->DISPLAY_PROTOCOL(
        EXPORTING
          I_DISPLAY_TOOLBAR  = ABAP_TRUE
          I_OPTIMIZE_COLUMNS = ABAP_TRUE ).
      IF LINE_EXISTS( ER_DATA_CHANGED->MT_PROTOCOL[ MSGTY = 'E' ] )
        OR LINE_EXISTS( ER_DATA_CHANGED->MT_PROTOCOL[ MSGTY = 'A' ] ).
        REFRESH_GRID( ). " removes the changes done by the user in case of an error
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD DATA_CHANGED_FINISHED.
    IF SENDER IS BOUND AND SENDER = MO_ALV.
      LOOP AT ET_GOOD_CELLS INTO DATA(LS_GOOD_CELL).
        ASSIGN MT_DATA[ LS_GOOD_CELL-ROW_ID ] TO FIELD-SYMBOL(<LS_DATA>).
        IF <LS_DATA> IS ASSIGNED.
          CASE LS_GOOD_CELL-FIELDNAME.
*            when 'FIELDNAME'
*              change other fields of <ls_data> as required based on the value of FIELDNAME
*              refresh_grid( ).
            WHEN OTHERS.
          ENDCASE.
        ENDIF.
        CLEAR LS_GOOD_CELL.
      ENDLOOP.
    ENDIF.
  ENDMETHOD.

  METHOD AFTER_USER_COMMAND.
    IF SENDER IS BOUND AND SENDER = MO_ALV.
      CASE E_UCOMM.
        WHEN '&REFRESH'.
          DATA LV_ANSWER TYPE C LENGTH 1.
          CLEAR LV_ANSWER.
          CALL FUNCTION 'POPUP_TO_CONFIRM'
            EXPORTING
              TITLEBAR              = |Refresh confirmation|
              TEXT_QUESTION         = |Current selections/changes will be lost and | &&
                                      |data will be re-fetched from DB. Proceed?|
              TEXT_BUTTON_1         = |Proceed|
              ICON_BUTTON_1         = 'ICON_OKAY'
              ICON_BUTTON_2         = 'ICON_CANCEL'
              DISPLAY_CANCEL_BUTTON = ABAP_TRUE
              POPUP_TYPE            = 'ICON_MESSAGE_QUESTION'
            IMPORTING
              ANSWER                = LV_ANSWER
            EXCEPTIONS
              TEXT_NOT_FOUND        = 1
              OTHERS                = 2.
          IF SY-SUBRC <> 0.
*    Implement suitable error handling here
          ENDIF.
          IF LV_ANSWER EQ '1'.
            LCL_MAIN=>START( ).
          ENDIF.
        WHEN OTHERS.
      ENDCASE.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_USER_COMMAND.
    IF MO_ALV IS BOUND.
      MO_ALV->CHECK_CHANGED_DATA(
        IMPORTING
          E_VALID = DATA(LV_VALID) ).
      OK = IV_UCOMM.
      CASE IV_UCOMM.
        WHEN MCS_UCOMM-BACK OR MCS_UCOMM-EXIT OR MCS_UCOMM-CANCEL.
          EXIT( ).
          RETURN.
        WHEN MCS_UCOMM-PROCESS.
          AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
          ID 'ACTVT' FIELD '01'.
          IF SY-SUBRC <> 0.
            AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
            ID 'ACTVT' FIELD '02'.
            IF SY-SUBRC <> 0.
              MESSAGE 'You are not Authorized for Process for Block/Unblock' TYPE 'E'.
            ENDIF.
          ENDIF.
          DATA(LV_PROCESSED) = PROCESS_DOCUMENTS( ).
        WHEN MCS_UCOMM-TRANSFER.
          AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
          ID 'ACTVT' FIELD '01'.
          IF SY-SUBRC <> 0.
            AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
            ID 'ACTVT' FIELD '02'.
            IF SY-SUBRC <> 0.
              MESSAGE 'You are not Authorized for Process for Block/Unblock' TYPE 'E'.
            ENDIF.
          ENDIF.
          LV_PROCESSED = TRANSFER_TO_GST_HOLD_JV( ).
        WHEN MCS_UCOMM-BLOCK.
          AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
          ID 'ACTVT' FIELD '01'.
          IF SY-SUBRC <> 0.
            AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
            ID 'ACTVT' FIELD '02'.
            IF SY-SUBRC <> 0.
              MESSAGE 'You are not Authorized for Process for Block/Unblock' TYPE 'E'.
            ENDIF.
          ENDIF.
          LV_PROCESSED = PROCESS_DOCUMENTS_BLK( ).
        WHEN MCS_UCOMM-BLOCKP.
          AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
          ID 'ACTVT' FIELD '01'.
          IF SY-SUBRC <> 0.
            AUTHORITY-CHECK OBJECT 'ZCYG_BUPLA' ##AUTH_FLD_MISSING
            ID 'ACTVT' FIELD '02'.
            IF SY-SUBRC <> 0.
              MESSAGE 'You are not Authorized for Process for Block/Unblock' TYPE 'E'.
            ENDIF.
          ENDIF.
          LV_PROCESSED = PROCESS_DOCUMENTS_BLK1( ).

        WHEN OTHERS.
      ENDCASE.

      IF LV_PROCESSED = ABAP_TRUE.
        COMMIT WORK AND WAIT.
      ENDIF.
    ENDIF.

    REFRESH_GRID( ).
  ENDMETHOD.

  METHOD FILL_RECO_STATUS_RANGE.
    CLEAR:
      MRT_MATCHED[],
      MRT_MISMATCHED[],
      MRT_EXEMPTED[].

    " #ToDo
    " Fill status ranges
    SELECT 'I'  AS SIGN,
           'EQ' AS OPTION,
           RECO_STATUS AS LOW
      FROM ZCY_T_2A_RECSTAT
      WHERE CATEGORY = @MCS_RECO_STATUS_CAT-MATCHED
      INTO TABLE @MRT_MATCHED[].

    LOOP AT MRT_MATCHED ASSIGNING FIELD-SYMBOL(<LS_MATCHED>).
      <LS_MATCHED>-LOW = TO_UPPER( <LS_MATCHED>-LOW ).
    ENDLOOP.

    SELECT 'I'  AS SIGN,
           'EQ' AS OPTION,
           RECO_STATUS AS LOW
      FROM ZCY_T_2A_RECSTAT
      WHERE CATEGORY = @MCS_RECO_STATUS_CAT-MISMATCHED
      INTO TABLE @MRT_MISMATCHED[].

    LOOP AT MRT_MISMATCHED ASSIGNING FIELD-SYMBOL(<LS_MISMATCHED>).
      <LS_MISMATCHED>-LOW = TO_UPPER( <LS_MISMATCHED>-LOW ).
    ENDLOOP.

    SELECT 'I'  AS SIGN,
           'EQ' AS OPTION,
           RECO_STATUS AS LOW
      FROM ZCY_T_2A_RECSTAT
      WHERE CATEGORY = @MCS_RECO_STATUS_CAT-EXEMPTED
      INTO TABLE @MRT_EXEMPTED[].

    LOOP AT MRT_EXEMPTED ASSIGNING FIELD-SYMBOL(<LS_EXEMPTED>).
      <LS_EXEMPTED>-LOW = TO_UPPER( <LS_EXEMPTED>-LOW ).
    ENDLOOP.

    IF MRT_MATCHED[] IS INITIAL OR MRT_MISMATCHED[] IS INITIAL.
      RAISE EXCEPTION TYPE LCX_GENERIC
        MESSAGE ID '00' TYPE MCS_MSG_TYPE-ERROR NUMBER '001'
        WITH TEXT-009.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DOCUMENTS.
    CLEAR RV_PROCESSED.



*    RV_PROCESSED = CHECK_PREV_PROC_CONSISTENCY( ).
    SELECT SINGLE * FROM TVARVC INTO @DATA(LC_DATE) WHERE NAME = 'ZCYG_CUTOFF_DATE'.
    IF SY-SUBRC = 0.
      CO_DATE = LC_DATE-LOW.
    ENDIF.

    LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA>) WHERE CBOX = ABAP_TRUE AND DOCUMENTTYPE <> 'CRN'
      AND CUSTOM2 GE CO_DATE. "15.05.2024
      GET_LOG(
        IMPORTING
          EV_DATE = <LS_DATA>-LAST_CHECKED_ON
          EV_TIME = <LS_DATA>-LAST_CHECKED_AT
          EV_USER = <LS_DATA>-LAST_CHECKED_BY ).

      IF TO_UPPER( <LS_DATA>-RECONCILIATIONSECTION ) NOT IN MRT_MATCHED[]
        AND TO_UPPER( <LS_DATA>-RECONCILIATIONSECTION ) NOT IN MRT_MISMATCHED[].
        <LS_DATA>-MESSAGE = TEXT-010.
      ENDIF.
      IF <LS_DATA>-BLART = 'FR' OR  <LS_DATA>-BLART = 'RE'.
      ELSE.
*        ms_processing_options-wait_for_filing = 'X'.
*        <ls_data>-QBLOCK = 'X'. " time being disable quick block
      ENDIF.
      " Document status based processing
      CASE <LS_DATA>-STATUS.
        WHEN MCS_DOC_STATUS-UNPROCESSED
          OR MCS_DOC_STATUS-WAITING_FOR_FILING_DATE
          OR MCS_DOC_STATUS-EXEMPTED.

          PROCESS_UNPROCESSED( CHANGING CS_DATA = <LS_DATA> ).

        WHEN MCS_DOC_STATUS-BLOCKED
          OR MCS_DOC_STATUS-SPLIT
          OR MCS_DOC_STATUS-DEBIT_NOTE_POSTED.

          PROCESS_BLOCKED( CHANGING CS_DATA = <LS_DATA> ).

        WHEN MCS_DOC_STATUS-MATCHED
          OR MCS_DOC_STATUS-RELEASED.
          " These documents cannot be selected,
          " so this is a unreachable/faux condition(placeholder)
        WHEN OTHERS.
      ENDCASE.

      " set status icons based on updated document status
      " will have no effect if there's no change in the status
      DERIVE_STATUS_ICON(
        CHANGING
          CS_DATA = <LS_DATA> ).

      " update DB irrespective of processing status/result
      RV_PROCESSED = UPDATE_DB( EXPORTING IS_DATA = <LS_DATA> ).
    ENDLOOP.

    IF SY-SUBRC = 4.
      MESSAGE TEXT-006 TYPE MCS_MSG_TYPE-SUCCESS
        DISPLAY LIKE MCS_MSG_TYPE-ERROR.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DOCUMENTS_BLK.
    CLEAR RV_PROCESSED.

*    RV_PROCESSED = CHECK_PREV_PROC_CONSISTENCY( ).

    LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA>) WHERE CBOX = ABAP_TRUE AND DOCUMENTTYPE <> 'CRE'.
      GET_LOG(
        IMPORTING
          EV_DATE = <LS_DATA>-LAST_CHECKED_ON
          EV_TIME = <LS_DATA>-LAST_CHECKED_AT
          EV_USER = <LS_DATA>-LAST_CHECKED_BY ).
      IF <LS_DATA>-REMARK IS INITIAL.
        MESSAGE TEXT-018 TYPE 'E' DISPLAY LIKE 'E'.
*        exit.
      ENDIF.
*
      " Document status based processing
      CASE <LS_DATA>-STATUS.
        WHEN MCS_DOC_STATUS-UNPROCESSED
          OR MCS_DOC_STATUS-WAITING_FOR_FILING_DATE
          OR MCS_DOC_STATUS-EXEMPTED.

*          process_unprocessed( changing cs_data = <ls_data> ).

        WHEN MCS_DOC_STATUS-BLOCKED
          OR MCS_DOC_STATUS-SPLIT
          OR MCS_DOC_STATUS-DEBIT_NOTE_POSTED.

          PROCESS_BLOCKED( CHANGING CS_DATA = <LS_DATA> ).

        WHEN MCS_DOC_STATUS-MATCHED
          OR MCS_DOC_STATUS-RELEASED.
          " These documents cannot be selected,
          " so this is a unreachable/faux condition(placeholder)
        WHEN OTHERS.
      ENDCASE.

      " set status icons based on updated document status
      " will have no effect if there's no change in the status
      DERIVE_STATUS_ICON(
        CHANGING
          CS_DATA = <LS_DATA> ).

      " update DB irrespective of processing status/result
      RV_PROCESSED = UPDATE_DB( EXPORTING IS_DATA = <LS_DATA> ).
    ENDLOOP.

    IF SY-SUBRC = 4.
      MESSAGE TEXT-006 TYPE MCS_MSG_TYPE-SUCCESS
        DISPLAY LIKE MCS_MSG_TYPE-ERROR.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DOCUMENTS_BLK1.
    CLEAR RV_PROCESSED.

*    RV_PROCESSED = CHECK_PREV_PROC_CONSISTENCY( ).

    LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA>) WHERE CBOX = ABAP_TRUE AND DOCUMENTTYPE <> 'CRE'.
      GET_LOG(
        IMPORTING
          EV_DATE = <LS_DATA>-LAST_CHECKED_ON
          EV_TIME = <LS_DATA>-LAST_CHECKED_AT
          EV_USER = <LS_DATA>-LAST_CHECKED_BY ).

      IF TO_UPPER( <LS_DATA>-RECONCILIATIONSECTION ) NOT IN MRT_MATCHED[]
        AND TO_UPPER( <LS_DATA>-RECONCILIATIONSECTION ) NOT IN MRT_MISMATCHED[].
        <LS_DATA>-MESSAGE = TEXT-010.
      ENDIF.

      " Document status based processing
      CASE <LS_DATA>-STATUS.
*        when mcs_doc_status-unprocessed
*          or mcs_doc_status-waiting_for_filing_date
*          or mcs_doc_status-exempted.
        WHEN  MCS_DOC_STATUS-EXEMPTED  .

*
        WHEN MCS_DOC_STATUS-IRELEASED.
          IF IS_DOCUMENT_OPEN(
         EXPORTING
           IV_DOC_NUM   = <LS_DATA>-DEBIT_NOTE_NUM
           IV_COMP_CODE = <LS_DATA>-DEBIT_NOTE_COMP
           IV_FIS_YEAR  = <LS_DATA>-DEBIT_NOTE_YEAR ).
            PROCESS_BLOCKED( CHANGING CS_DATA = <LS_DATA> ).
          ELSE.
            DATA(LV_BLOCKED) = BLOCK_CLEARED_DOCUMENT_BK( CHANGING CS_DATA =  <LS_DATA>  ).
          ENDIF.



        WHEN MCS_DOC_STATUS-MATCHED
          OR MCS_DOC_STATUS-RELEASED.
          " These documents cannot be selected,
          " so this is a unreachable/faux condition(placeholder)
        WHEN OTHERS.
      ENDCASE.

      " set status icons based on updated document status
      " will have no effect if there's no change in the status
      DERIVE_STATUS_ICON(
        CHANGING
          CS_DATA = <LS_DATA> ).

      " update DB irrespective of processing status/result
      RV_PROCESSED = UPDATE_DB( EXPORTING IS_DATA = <LS_DATA> ).
    ENDLOOP.

    IF SY-SUBRC = 4.
      MESSAGE TEXT-006 TYPE MCS_MSG_TYPE-SUCCESS
        DISPLAY LIKE MCS_MSG_TYPE-ERROR.
    ENDIF.
  ENDMETHOD.

  METHOD CHECK_PREV_PROC_CONSISTENCY.
    " This method will process those documents which have 2 processing legs
    " but only the first processing leg was completed in the previous run
    " and there was some processing error in the second leg
    " Eg. For split documents - 1. splitting 2. blocking
    " Splitting is done but the blocking fails. This method with try to
    " re-process the blocking for such split documents
    " Similarly if JV posting or reversal failed, the same will be re-tried.
    " This will continue till the document is completely processed i.e. the
    " processing is consistent with the current status
    CLEAR RV_PROCESSED.

    CL_PROGRESS_INDICATOR=>PROGRESS_INDICATE(
      EXPORTING
        I_TEXT               = TEXT-016
        I_PROCESSED          = 50                        "#EC NUMBER_OK
        I_TOTAL              = 100                       "#EC NUMBER_OK
        I_OUTPUT_IMMEDIATELY = ABAP_TRUE ).

    LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA>).
      GET_LOG(
        IMPORTING
          EV_DATE = <LS_DATA>-LAST_CHECKED_ON
          EV_TIME = <LS_DATA>-LAST_CHECKED_AT
          EV_USER = <LS_DATA>-LAST_CHECKED_BY ).

      " Document status based processing
      " Check and correct partial processing in prev run due to processing error
      CASE <LS_DATA>-STATUS.
        WHEN MCS_DOC_STATUS-BLOCKED.
          " Is there a better identification for JV applicability?
          IF <LS_DATA>-JV_DOC_NUM IS INITIAL AND <LS_DATA>-JV_COMMENT IS NOT INITIAL.
            DATA(LV_POST_JV) = ABAP_TRUE.
          ENDIF.
        WHEN MCS_DOC_STATUS-SPLIT.
          " check block status
          IF <LS_DATA>-BLOCKED_ON IS INITIAL AND <LS_DATA>-BLOCK_COMMENT IS NOT INITIAL.
            " apply payment block
            TOGGLE_PAYMENT_BLOCK(
              EXPORTING
                IV_DOC_NUM   = |{ <LS_DATA>-SPLIT_GST_DOC_NUM ALPHA = IN }|
                IV_COMP_CODE = <LS_DATA>-SPLIT_COMP_CODE
                IV_FIS_YEAR  = <LS_DATA>-SPLIT_YEAR
              IMPORTING
                EV_MESSAGE   = <LS_DATA>-BLOCK_COMMENT
              RECEIVING
                RV_TOGGLED   = DATA(LV_BLOCKED) ).

            IF LV_BLOCKED = ABAP_TRUE.
              GET_LOG(
                IMPORTING
                  EV_DATE = <LS_DATA>-BLOCKED_ON
                  EV_TIME = <LS_DATA>-BLOCKED_AT
                  EV_USER = <LS_DATA>-BLOCKED_BY ).

              <LS_DATA>-MESSAGE = TEXT-014.
            ELSE.
              <LS_DATA>-MESSAGE = TEXT-013.
            ENDIF.
          ENDIF.

          " Was JV applicable and not posted?
          IF <LS_DATA>-JV_DOC_NUM IS INITIAL AND <LS_DATA>-JV_COMMENT IS NOT INITIAL.
            LV_POST_JV = ABAP_TRUE.
          ENDIF.
        WHEN MCS_DOC_STATUS-DEBIT_NOTE_POSTED.
          " Was JV applicable and not posted?
          IF <LS_DATA>-JV_DOC_NUM IS INITIAL AND <LS_DATA>-JV_COMMENT IS NOT INITIAL.
            LV_POST_JV = ABAP_TRUE.
          ENDIF.
        WHEN MCS_DOC_STATUS-RELEASED.
          " Was JV reversed?
          IF <LS_DATA>-JV_DOC_NUM IS NOT INITIAL AND <LS_DATA>-JV_REV_DOC_NUM IS INITIAL.
            DATA(LV_REV_JV) = ABAP_TRUE.
          ENDIF.
        WHEN OTHERS.
      ENDCASE.

      IF LV_POST_JV = ABAP_TRUE.
        POST_JV(
          EXPORTING
            IV_COMP_CODE  = CONV #( <LS_DATA>-CUSTOM9 )
            IV_DOC_NUM    = |{ CONV BKPF-BELNR( <LS_DATA>-CUSTOM1 ) ALPHA = IN }|
            IV_FIS_YEAR   = CONV #( <LS_DATA>-CUSTOM4 )
            IV_GST_AMOUNT = <LS_DATA>-FI_TAX_VALUE
          IMPORTING
            EV_COMP_CODE  = <LS_DATA>-JV_COMP_CODE
            EV_DOC_NUM    = <LS_DATA>-JV_DOC_NUM
            EV_FIS_YEAR   = <LS_DATA>-JV_YEAR
            EV_MESSAGE    = <LS_DATA>-JV_COMMENT
          RECEIVING
            RV_POSTED     = DATA(LV_JV_POSTED) ).

IF LV_JV_POSTED = ABAP_TRUE.
          GET_LOG(
            IMPORTING
              EV_DATE = <LS_DATA>-JV_POSTED_ON
              EV_TIME = <LS_DATA>-JV_POSTED_AT
              EV_USER = <LS_DATA>-JV_POSTED_BY ).

          <LS_DATA>-MESSAGE = TEXT-014.
        ELSE.
          <LS_DATA>-MESSAGE = TEXT-013.
        ENDIF.
      ENDIF.

      IF LV_REV_JV = ABAP_TRUE.
        REVERSE_JV(
          EXPORTING
            IV_DOC_NUM   = |{ <LS_DATA>-JV_DOC_NUM ALPHA = IN }|
            IV_COMP_CODE = <LS_DATA>-JV_COMP_CODE
            IV_FIS_YEAR  = <LS_DATA>-JV_YEAR
          IMPORTING
            EV_COMP_CODE = <LS_DATA>-JV_REV_COMP_CODE
            EV_DOC_NUM   = <LS_DATA>-JV_REV_DOC_NUM
            EV_FIS_YEAR  = <LS_DATA>-JV_REV_YEAR
            EV_MESSAGE   = <LS_DATA>-JV_REV_COMMENT
          RECEIVING
            RV_REVERSED  = DATA(LV_JV_REVERSED) ).

        IF LV_JV_REVERSED = ABAP_TRUE.
          GET_LOG(
            IMPORTING
              EV_DATE = <LS_DATA>-JV_REVERSED_ON
              EV_TIME = <LS_DATA>-JV_REVERSED_AT
              EV_USER = <LS_DATA>-JV_REVERSED_BY ).

          <LS_DATA>-MESSAGE = TEXT-014.
        ELSE.
          <LS_DATA>-MESSAGE = TEXT-013.
        ENDIF.
      ENDIF.

      " set status icons based on updated document status
      " will have no effect if there's no change in the status
      DERIVE_STATUS_ICON(
        CHANGING
          CS_DATA = <LS_DATA> ).

      " update DB irrespective of processing status/result
      RV_PROCESSED = UPDATE_DB( EXPORTING IS_DATA = <LS_DATA> ).

      CLEAR:
        LV_POST_JV,
        LV_JV_POSTED,
        LV_REV_JV,
        LV_JV_REVERSED,
        LV_BLOCKED.
    ENDLOOP.
  ENDMETHOD.

  METHOD PROCESS_UNPROCESSED.
    " special treatment for change in exemption status of the vendor
    IF CS_DATA-STATUS = MCS_DOC_STATUS-EXEMPTED.
      IF CS_DATA-VENDOR_BLOCK_TYPE <> MCS_VENDOR_BLOCK_TYPE-EXEMPTED.
        CS_DATA-STATUS = MCS_DOC_STATUS-UNPROCESSED.
      ELSE.
        " Document remains exempted without change
        " No DB update
        RETURN.
      ENDIF.
    ELSE.
      " Document follows the normal 'unprocessed or waiting for filing date' flow
    ENDIF.
*--------------------------------------------------------------------*
    IF CS_DATA-DUE_DATE_BEFORE_FILING = MCS_YES_NO-YES.
      " Due before filing
      IF P_DBF = ABAP_TRUE. " user option - process due before filing?
        PROCESS_DBF( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
    ELSE.
      " Due after filing
      IF P_DAF = ABAP_TRUE. " user option - process due after filing?
        PROCESS_DAF( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DBF.
    IF CS_DATA-CLEARING_DATE IS NOT INITIAL.
      " Cleared
      IF MS_PROCESSING_OPTIONS-DUE_BEFORE_FILING-CLEARED = ABAP_TRUE.
        PROCESS_DBF_OPEN( CHANGING CS_DATA = CS_DATA ).
*         PROCESS_DBF_CLEARED( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
    ELSE.
      " Open
      IF MS_PROCESSING_OPTIONS-DUE_BEFORE_FILING-OPEN = ABAP_TRUE.
        PROCESS_DBF_OPEN( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DBF_CLEARED.
    " Filing date has passed?
    IF SY-DATLO > CS_DATA-EXPECTED_FILING_DATE
*      OR CS_DATA-FILINGDATE IS NOT INITIAL        " already filed
      OR MS_PROCESSING_OPTIONS-WAIT_FOR_FILING = ABAP_FALSE OR CS_DATA-GRC_SCORE LE LS_GRC-GRC_BLOCK1.
      " Check reco status
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MISMATCHED[].
        DATA(LV_BLOCKED) = BLOCK_CLEARED_DOCUMENT( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MATCHED[].
        CS_DATA-STATUS = MCS_DOC_STATUS-MATCHED.
      ENDIF.
    ELSE.
      " Filing date is in the future
      " Wait fot filing
      CS_DATA-STATUS = MCS_DOC_STATUS-WAITING_FOR_FILING_DATE.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DBF_OPEN.
    " Filing date has passed? --> Rare
*    data: exp TYPE datum.
*    EXP = cs_data-expected_filing_date.
*    if sy-DATUM gt EXP

    IF SY-DATUM GE CS_DATA-EXPECTED_FILING_DATE
*      OR CS_DATA-FILINGDATE IS NOT INITIAL        " already filed
      OR MS_PROCESSING_OPTIONS-WAIT_FOR_FILING = ABAP_FALSE OR CS_DATA-QBLOCK IS NOT INITIAL.
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MISMATCHED[].
        DATA(LV_BLOCKED) = BLOCK_OPEN_DOCUMENT( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MATCHED[].
        CS_DATA-STATUS = MCS_DOC_STATUS-MATCHED.
      ENDIF.
    ELSE.
      " Filing date is in the future
      " Is GRC check applicable to vendor
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MATCHED[].
        CS_DATA-STATUS = MCS_DOC_STATUS-MATCHED.
      ELSE.
        DATA: DUE_CHECK TYPE SY-DATUM.
        DUE_CHECK = SY-DATUM + 1.
        IF CS_DATA-GRC_CHECK_APPLICABLE = MCS_YES_NO-YES.

          PERFORM_GRC_CHECK(
            EXPORTING
              IV_VENDOR  = CS_DATA-VENDOR
            IMPORTING
              EV_SCORE   = CS_DATA-GRC_SCORE
              EV_MESSAGE = CS_DATA-GRC_CHECK_COMMENT
            RECEIVING
              RV_RESULT  = CS_DATA-GRC_CHECK_RESULT ).

*          if cs_data-grc_score is not initial.
          GET_LOG(
            IMPORTING
              EV_DATE = CS_DATA-GRC_CHECKED_ON
              EV_TIME = CS_DATA-GRC_CHECKED_AT
              EV_USER = CS_DATA-GRC_CHECKED_BY ).

*            if cs_data-grc_check_result = mcs_grc_result-success.
*              cs_data-status = mcs_doc_status-waiting_for_filing_date.
          GRC = CS_DATA-GRC_SCORE.
          CLEAR:LS_GRC.
          READ TABLE LT_GRC INTO LS_GRC INDEX 1.

*            endif.
*            if cs_data-grc_score le 95.
          IF ( GRC LE LS_GRC-GRC_BLOCK1 OR  GRC IS  INITIAL ) AND CS_DATA-MINDK IS INITIAL..
            LV_BLOCKED = BLOCK_OPEN_DOCUMENT( CHANGING CS_DATA = CS_DATA ).

          ELSEIF GRC GT LS_GRC-GRC_BLOCK2 AND GRC LE LS_GRC-GRC_BLOCK3.
*
            CS_DATA-STATUS = MCS_DOC_STATUS-WAITING_FOR_FILING_DATE.

          ELSE.
            CS_DATA-STATUS = MCS_DOC_STATUS-WAITING_FOR_FILING_DATE.
          ENDIF.
*
        ELSE.
          " eventually the document will get cleared
          " or date > filing date.
          " accordingly the document will traverse the
          " respective flow
          CS_DATA-STATUS = MCS_DOC_STATUS-WAITING_FOR_FILING_DATE.
        ENDIF.
*    endif.
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DAF.
    IF CS_DATA-CLEARING_DATE IS NOT INITIAL.
      " Cleared
      IF MS_PROCESSING_OPTIONS-DUE_AFTER_FILING-CLEARED = ABAP_TRUE.
*        PROCESS_DAF_CLEARED( CHANGING CS_DATA = CS_DATA ).
        PROCESS_DAF_OPEN( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
    ELSE.
      " Open
      IF MS_PROCESSING_OPTIONS-DUE_AFTER_FILING-OPEN = ABAP_TRUE.
        PROCESS_DAF_OPEN( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DAF_CLEARED.
    " Filing date has passed?
    IF SY-DATLO > CS_DATA-EXPECTED_FILING_DATE
*      OR CS_DATA-FILINGDATE IS NOT INITIAL        " already filed
      OR MS_PROCESSING_OPTIONS-WAIT_FOR_FILING = ABAP_FALSE.
      " Check reco status
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MISMATCHED[].
        DATA(LV_BLOCKED) = BLOCK_CLEARED_DOCUMENT( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MATCHED[].
        CS_DATA-STATUS = MCS_DOC_STATUS-MATCHED.
      ENDIF.
    ELSE.
      " Filing date is in the future  --> Rare
      " Wait fot filing
      CS_DATA-STATUS = MCS_DOC_STATUS-WAITING_FOR_FILING_DATE.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_DAF_OPEN.
    " Filing date has passed?
    CLEAR:LS_GRC.
    IF CS_DATA-MINDK = '6' OR CS_DATA-MINDK  = '7'.
      CS_DATA-QBLOCK = 'X'.
    ENDIF.
    READ TABLE LT_GRC INTO LS_GRC INDEX 1.
    IF SY-DATLO GE CS_DATA-EXPECTED_FILING_DATE
*      OR CS_DATA-FILINGDATE IS NOT INITIAL        " already filed
      OR MS_PROCESSING_OPTIONS-WAIT_FOR_FILING = ABAP_FALSE OR CS_DATA-QBLOCK = 'X'.."OR CS_DATA-GRC_SCORE LE LS_GRC-GRC_BLOCK1 OR CS_DATA-QBLOCK = 'X'..
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MISMATCHED[].
        DATA(LV_BLOCKED) = BLOCK_OPEN_DOCUMENT( CHANGING CS_DATA = CS_DATA ).
      ENDIF.
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MATCHED[].
        CS_DATA-STATUS = MCS_DOC_STATUS-MATCHED.
      ENDIF.
    ELSE.
      " Filing date is in the future
      CS_DATA-STATUS = MCS_DOC_STATUS-WAITING_FOR_FILING_DATE.
    ENDIF.
  ENDMETHOD.

  METHOD BLOCK_OPEN_DOCUMENT.
    CLEAR RV_BLOCKED.

    CASE CS_DATA-VENDOR_BLOCK_TYPE.
      WHEN MCS_VENDOR_BLOCK_TYPE-EXEMPTED.
        CS_DATA-STATUS = MCS_DOC_STATUS-EXEMPTED.
      WHEN MCS_VENDOR_BLOCK_TYPE-BLOCK.
        TOGGLE_PAYMENT_BLOCK(
          EXPORTING
            IV_DOC_NUM   = |{ CONV BKPF-BELNR( CS_DATA-CUSTOM1 ) ALPHA = IN }|
            IV_COMP_CODE = CONV #( CS_DATA-CUSTOM9 )
            IV_FIS_YEAR  = CONV #( CS_DATA-CUSTOM4 )
          IMPORTING
            EV_MESSAGE   = CS_DATA-BLOCK_COMMENT
          RECEIVING
            RV_TOGGLED   = RV_BLOCKED ).

        IF RV_BLOCKED = ABAP_TRUE.
          CS_DATA-STATUS = MCS_DOC_STATUS-BLOCKED.

          GET_LOG(
            IMPORTING
              EV_DATE = CS_DATA-BLOCKED_ON
              EV_TIME = CS_DATA-BLOCKED_AT
              EV_USER = CS_DATA-BLOCKED_BY ).

          CS_DATA-MESSAGE = TEXT-014.

          IF MS_PROCESSING_OPTIONS-JV_APPLICABILITY-BLOCKED = ABAP_TRUE.
            DATA(LV_JV_APPLICABLE) = ABAP_TRUE.
          ENDIF.
        ELSE.
          CS_DATA-MESSAGE = TEXT-013.
        ENDIF.
      WHEN MCS_VENDOR_BLOCK_TYPE-SPLIT.

        POST_DEBIT_NOTE(
         EXPORTING
           IV_COMP_CODE  = CONV #( CS_DATA-CUSTOM9 )
           IV_DOC_NUM    = |{ CONV BKPF-BELNR( CS_DATA-CUSTOM1 ) ALPHA = IN }|
           IV_FIS_YEAR   = CONV #( CS_DATA-CUSTOM4 )
           IV_VENDOR     = CS_DATA-VENDOR
           IV_GST_AMOUNT = CS_DATA-FI_TAX_VALUE
         IMPORTING
           EV_COMP_CODE = CS_DATA-DEBIT_NOTE_COMP
           EV_DOC_NUM   = CS_DATA-DEBIT_NOTE_NUM
           EV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_YEAR
           EV_MESSAGE   = CS_DATA-DEBIT_NOTE_COMMENT
         RECEIVING
           RV_POSTED    = DATA(LV_DN_POSTED) ).

        IF LV_DN_POSTED = ABAP_TRUE.
          CS_DATA-STATUS = MCS_DOC_STATUS-DEBIT_NOTE_POSTED.

          GET_LOG(
            IMPORTING
              EV_DATE = CS_DATA-DEBIT_NOTE_POSTED_ON
              EV_TIME = CS_DATA-DEBIT_NOTE_POSTED_AT
              EV_USER = CS_DATA-DEBIT_NOTE_POSTED_BY ).

          CS_DATA-MESSAGE = TEXT-014.
        ENDIF.
      WHEN OTHERS.
        CS_DATA-MESSAGE = TEXT-008.
    ENDCASE.

    IF LV_JV_APPLICABLE = ABAP_TRUE.
      POST_JV(
        EXPORTING
          IV_COMP_CODE  = CONV #( CS_DATA-CUSTOM9 )
          IV_DOC_NUM    = |{ CONV BKPF-BELNR( CS_DATA-CUSTOM1 ) ALPHA = IN }|
          IV_FIS_YEAR   = CONV #( CS_DATA-CUSTOM4 )
          IV_GST_AMOUNT = CS_DATA-FI_TAX_VALUE
        IMPORTING
          EV_COMP_CODE  = CS_DATA-JV_COMP_CODE
          EV_DOC_NUM    = CS_DATA-JV_DOC_NUM
          EV_FIS_YEAR   = CS_DATA-JV_YEAR
          EV_MESSAGE    = CS_DATA-JV_COMMENT
        RECEIVING
          RV_POSTED     = DATA(LV_JV_POSTED) ).

      IF LV_JV_POSTED = ABAP_TRUE.
        GET_LOG(
          IMPORTING
            EV_DATE = CS_DATA-JV_POSTED_ON
            EV_TIME = CS_DATA-JV_POSTED_AT
            EV_USER = CS_DATA-JV_POSTED_BY ).

        " This is specific for split documents, where the splitting
        " may be successful but blocking fails in which case the
        " message is already set as failed and do not want to overwrite
        " it with success jus because jv is sucssessful as the blocking
        " is still failed
        IF CS_DATA-MESSAGE <> TEXT-013.
          CS_DATA-MESSAGE = TEXT-014.
        ENDIF.
      ELSE.
        CS_DATA-MESSAGE = TEXT-013.
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD BLOCK_CLEARED_DOCUMENT.
    " block in case of cleared is debit note
    CLEAR RV_BLOCKED.

    CASE CS_DATA-VENDOR_BLOCK_TYPE.
        " Is vendor exempted
      WHEN MCS_VENDOR_BLOCK_TYPE-EXEMPTED.
        CS_DATA-STATUS = MCS_DOC_STATUS-EXEMPTED.
      WHEN MCS_VENDOR_BLOCK_TYPE-BLOCK
        OR MCS_VENDOR_BLOCK_TYPE-SPLIT.
        " post a debit note
        POST_DEBIT_NOTE(
          EXPORTING
            IV_COMP_CODE  = CONV #( CS_DATA-CUSTOM9 )
            IV_DOC_NUM    = |{ CONV BKPF-BELNR( CS_DATA-CUSTOM1 ) ALPHA = IN }|
            IV_FIS_YEAR   = CONV #( CS_DATA-CUSTOM4 )
            IV_VENDOR     = CS_DATA-VENDOR
            IV_GST_AMOUNT = CS_DATA-FI_TAX_VALUE
          IMPORTING
            EV_COMP_CODE = CS_DATA-DEBIT_NOTE_COMP
            EV_DOC_NUM   = CS_DATA-DEBIT_NOTE_NUM
            EV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_YEAR
            EV_MESSAGE   = CS_DATA-DEBIT_NOTE_COMMENT
          RECEIVING
            RV_POSTED    = DATA(LV_DN_POSTED) ).

        IF LV_DN_POSTED = ABAP_TRUE.
          CS_DATA-STATUS = MCS_DOC_STATUS-DEBIT_NOTE_POSTED.

          GET_LOG(
            IMPORTING
              EV_DATE = CS_DATA-DEBIT_NOTE_POSTED_ON
              EV_TIME = CS_DATA-DEBIT_NOTE_POSTED_AT
              EV_USER = CS_DATA-DEBIT_NOTE_POSTED_BY ).

          CS_DATA-MESSAGE = TEXT-014.

          IF MS_PROCESSING_OPTIONS-JV_APPLICABILITY-DEBIT_NOTE = ABAP_TRUE.
            POST_JV(
              EXPORTING
                IV_COMP_CODE  = CONV #( CS_DATA-CUSTOM9 )
                IV_DOC_NUM    = |{ CONV BKPF-BELNR( CS_DATA-CUSTOM1 ) ALPHA = IN }|
                IV_FIS_YEAR   = CONV #( CS_DATA-CUSTOM4 )
                IV_GST_AMOUNT = CS_DATA-FI_TAX_VALUE
              IMPORTING
                EV_COMP_CODE  = CS_DATA-JV_COMP_CODE
                EV_DOC_NUM    = CS_DATA-JV_DOC_NUM
                EV_FIS_YEAR   = CS_DATA-JV_YEAR
                EV_MESSAGE    = CS_DATA-JV_COMMENT
              RECEIVING
                RV_POSTED     = DATA(LV_JV_POSTED) ).

            IF LV_JV_POSTED = ABAP_TRUE.
              GET_LOG(
                IMPORTING
                  EV_DATE = CS_DATA-JV_POSTED_ON
                  EV_TIME = CS_DATA-JV_POSTED_AT
                  EV_USER = CS_DATA-JV_POSTED_BY ).

              CS_DATA-MESSAGE = TEXT-014.
            ELSE.
              CS_DATA-MESSAGE = TEXT-013.
            ENDIF.
          ENDIF.
        ELSE.
          CS_DATA-MESSAGE = TEXT-013.
        ENDIF.
      WHEN OTHERS.
        CS_DATA-MESSAGE = TEXT-008.
    ENDCASE.
  ENDMETHOD.

  METHOD BLOCK_CLEARED_DOCUMENT_BK.
    " block in case of cleared is debit note
    CLEAR RV_BLOCKED.

    CASE CS_DATA-VENDOR_BLOCK_TYPE.
        " Is vendor exempted
      WHEN MCS_VENDOR_BLOCK_TYPE-EXEMPTED.
        CS_DATA-STATUS = MCS_DOC_STATUS-EXEMPTED.
      WHEN MCS_VENDOR_BLOCK_TYPE-BLOCK
        OR MCS_VENDOR_BLOCK_TYPE-SPLIT.
        " post a debit note
        POST_DEBIT_NOTE(
          EXPORTING
            IV_COMP_CODE  = CONV #( CS_DATA-CUSTOM9 )
            IV_DOC_NUM    = |{ CONV BKPF-BELNR( CS_DATA-CUSTOM1 ) ALPHA = IN }|
            IV_FIS_YEAR   = CONV #( CS_DATA-CUSTOM4 )
            IV_VENDOR     = CS_DATA-VENDOR
            IV_GST_AMOUNT = CS_DATA-FI_TAX_VALUE
          IMPORTING
            EV_COMP_CODE = CS_DATA-DEBIT_NOTE_COMP
            EV_DOC_NUM   = CS_DATA-DEBIT_NOTE_NUM
            EV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_YEAR
            EV_MESSAGE   = CS_DATA-DEBIT_NOTE_COMMENT
          RECEIVING
            RV_POSTED    = DATA(LV_DN_POSTED) ).

        IF LV_DN_POSTED = ABAP_TRUE.
          CS_DATA-STATUS = MCS_DOC_STATUS-DEBIT_NOTE_POSTED.

          GET_LOG(
            IMPORTING
              EV_DATE = CS_DATA-DEBIT_NOTE_POSTED_ON
              EV_TIME = CS_DATA-DEBIT_NOTE_POSTED_AT
              EV_USER = CS_DATA-DEBIT_NOTE_POSTED_BY ).

          CS_DATA-MESSAGE = TEXT-014.

          IF MS_PROCESSING_OPTIONS-JV_APPLICABILITY-DEBIT_NOTE = ABAP_TRUE.
            POST_JV(
              EXPORTING
                IV_COMP_CODE  = CONV #( CS_DATA-CUSTOM9 )
                IV_DOC_NUM    = |{ CONV BKPF-BELNR( CS_DATA-CUSTOM1 ) ALPHA = IN }|
                IV_FIS_YEAR   = CONV #( CS_DATA-CUSTOM4 )
                IV_GST_AMOUNT = CS_DATA-FI_TAX_VALUE
              IMPORTING
                EV_COMP_CODE  = CS_DATA-JV_COMP_CODE
                EV_DOC_NUM    = CS_DATA-JV_DOC_NUM
                EV_FIS_YEAR   = CS_DATA-JV_YEAR
                EV_MESSAGE    = CS_DATA-JV_COMMENT
              RECEIVING
                RV_POSTED     = DATA(LV_JV_POSTED) ).

            IF LV_JV_POSTED = ABAP_TRUE.
              GET_LOG(
                IMPORTING
                  EV_DATE = CS_DATA-JV_POSTED_ON
                  EV_TIME = CS_DATA-JV_POSTED_AT
                  EV_USER = CS_DATA-JV_POSTED_BY ).

              CS_DATA-MESSAGE = TEXT-014.
            ELSE.
              CS_DATA-MESSAGE = TEXT-013.
            ENDIF.
          ENDIF.
        ELSE.
          CS_DATA-MESSAGE = TEXT-013.
        ENDIF.
      WHEN OTHERS.
        CS_DATA-MESSAGE = TEXT-008.
    ENDCASE.
  ENDMETHOD.

  METHOD POST_DEBIT_NOTE.
    "#ToDo
    CONSTANTS:
      LC_OBJ_TYPE     TYPE BAPIACHE09-OBJ_TYPE   VALUE 'BKPFF',
      LC_OBJ_KEY      TYPE BAPIACHE09-OBJ_KEY    VALUE '$',
      LC_DOC_TYPE     TYPE BAPIACHE09-DOC_TYPE   VALUE 'DS',
      LC_GL_ACCOUNT   TYPE BAPIACGL09-GL_ACCOUNT VALUE '0000602000',      " #ClientSpecific
      LC_CURR_TYPE    TYPE BAPIACCR09-CURR_TYPE  VALUE '00',
      LC_CURRENCY     TYPE BAPIACCR09-CURRENCY   VALUE 'INR',
      " For HSN_SAC implement BADI BADI_ACC_DOCUMENT method CHANGE
      LC_EXT_STRUCT   TYPE BAPIPAREX-STRUCTURE   VALUE '',      " #ClientSpecific
      LCS_SUCCESS_MSG TYPE MSTY_MESSAGE          VALUE '605RW'.

    DATA:
      LS_HEADER        TYPE BAPIACHE09,
      LT_GL            TYPE STANDARD TABLE OF BAPIACGL09,
      LT_ACC_PAY       TYPE STANDARD TABLE OF BAPIACAP09,
      LT_AMOUNT        TYPE STANDARD TABLE OF BAPIACCR09,
      LT_EXT           TYPE STANDARD TABLE OF BAPIPAREX,
      LS_EXT           TYPE  BAPIPAREX,
      LS_ONE           TYPE BAPIACPA09,
      LT_RETURN        TYPE STANDARD TABLE OF BAPIRET2,
      LV_ITEM          TYPE BAPIACGL08-ITEMNO_ACC,
      LV_PROFIT_CENTER TYPE BSEG-PRCTR.
*      LV_COST_CENTER   TYPE BSEG-KOSTL,
*      LV_HSN_SAC       TYPE BSEG-HSN_SAC.

    CLEAR:
      EV_COMP_CODE,
      EV_DOC_NUM,
      EV_FIS_YEAR,
      EV_MESSAGE,
      RV_POSTED,
      LS_HEADER,
      LS_ONE,
      LT_GL,
      LT_ACC_PAY,
      LT_AMOUNT,
      LT_EXT,
      LT_RETURN,
      LV_PROFIT_CENTER.
*      LV_COST_CENTER,
*      LV_HSN_SAC.
    REFRESH:MT_ACC_DATA_K[].

    " post debit note using BDC/BAPI
    " #ToDo
    DATA(LS_ACC_DATA) = VALUE #( MT_ACC_DATA[ KEY SEC_KEY
                                                BUKRS = IV_COMP_CODE
                                                BELNR = IV_DOC_NUM
                                                GJAHR = IV_FIS_YEAR
                                                KOART = MCS_ACC_TYPE-VENDOR ] OPTIONAL ).

    IF LS_ACC_DATA IS NOT INITIAL.
      DATA(LT_ACC_ITEM) = FILTER #( MT_ACC_DATA USING KEY PRIMARY_KEY
                                      WHERE BUKRS = IV_COMP_CODE
                                        AND BELNR = IV_DOC_NUM
                                        AND GJAHR = IV_FIS_YEAR ).
      MT_ACC_DATA_K = FILTER #( MT_ACC_DATA USING KEY PRIMARY_KEY
                                      WHERE BUKRS = IV_COMP_CODE
                                        AND BELNR = IV_DOC_NUM
                                        AND GJAHR = IV_FIS_YEAR ).

      SORT MT_ACC_DATA_K BY BUKRS BELNR GJAHR  DMBTR .

      LOOP AT MT_ACC_DATA_K INTO DATA(LS_ACC_ITEM).
        IF LS_ACC_ITEM-KOART = MCS_ACC_TYPE-VENDOR.
          DATA(LV_ASSIGNMENT) = LS_ACC_ITEM-ZUONR.
          DATA(LV_BUS_PLC)    = LS_ACC_ITEM-BUPLA.
          DATA(LV_SECT_CODE)  = LS_ACC_ITEM-SECCO.

*          IF LS_ACC_ITEM-ZLSPR = 'C'..
          READ TABLE LT_TVARC INTO DATA(LS_TVARC) WITH KEY LOW =  LS_ACC_ITEM-ZTERM.
          IF SY-SUBRC = 0 AND LS_ACC_ITEM-ZLSPR = 'C'.
            DATA(LV_NOT_LINK) = 'X'.
          ELSE.
            DATA(LV_BUZEI)  = LS_ACC_ITEM-BUZEI.
          ENDIF.

          DATA(ZTERM) = LS_ACC_DATA-ZTERM.
        ENDIF.
        IF LS_ACC_ITEM-BKTXT IS NOT INITIAL.

          DATA(BKTXT) = LS_ACC_ITEM-BKTXT.
        ENDIF.
        IF LS_ACC_ITEM-GSBER IS NOT INITIAL.

          DATA(GSBER) = LS_ACC_ITEM-GSBER.
        ENDIF.

        IF LS_ACC_ITEM-PRCTR IS NOT INITIAL.

          LV_PROFIT_CENTER = LS_ACC_ITEM-PRCTR.
        ENDIF.


        CLEAR LS_ACC_ITEM.
      ENDLOOP.
      READ TABLE LT_BSIK INTO DATA(LS_BSIK1) WITH KEY BUKRS = IV_COMP_CODE BELNR = IV_DOC_NUM GJAHR = IV_FIS_YEAR BINARY SEARCH.
      IF SY-SUBRC = 0.
        LV_NOT_LINK = 'X'.
      ENDIF.

      LS_HEADER = VALUE #( OBJ_TYPE   = LC_OBJ_TYPE
                           OBJ_KEY    = LC_OBJ_KEY
                           OBJ_SYS    = LS_ACC_DATA-AWSYS
                           BUS_ACT    = LS_ACC_DATA-GLVOR
                           USERNAME   = SY-UNAME
                           HEADER_TXT = BKTXT
                           COMP_CODE  = IV_COMP_CODE
                           DOC_DATE   = LS_ACC_DATA-BLDAT
*                           doc_date   = sy-datlo
                           PSTNG_DATE = SY-DATLO
                           DOC_TYPE   = LC_DOC_TYPE
                           REF_DOC_NO = LS_ACC_DATA-XBLNR ).

      CLEAR LV_ITEM.

*      LV_ASSIGNMENT = 'DNOT'.
      DATA: MSG TYPE STRING.
*      CONCATENATE 'Mismatch in GSTR2B in' ls_bkpf-BUDAT+4(2) '.' ls_bkpf-BUDAT(4) '-' lv_bus_plc INTO msg.
      MSG = 'GST Hold due to Mismatch/Non Compliance'.
*      CONCATENATE LS_ACC_DATA-XBLNR LS_ACC_DATA-BLDAT INTO MSG SEPARATED BY ','.
*      LV_ASSIGNMENT = IV_DOC_NUM.

      LT_ACC_PAY = VALUE #( ( ITEMNO_ACC    = |{ CONV POSNR_ACC( LV_ITEM + '10' ) ALPHA = IN }|
                              VENDOR_NO     = IV_VENDOR
                              ALLOC_NMBR    = LV_ASSIGNMENT
                              BUSINESSPLACE = LV_BUS_PLC
                              BUS_AREA      = GSBER
                              PROFIT_CTR = LV_PROFIT_CENTER
                              ITEM_TEXT = MSG
                              SECTIONCODE   = LV_SECT_CODE
                              PMNTTRMS = ZTERM
                              PMNT_BLOCK = 'Q'
                              COMP_CODE     = IV_COMP_CODE )
                              ( ITEMNO_ACC    = |{ CONV POSNR_ACC( LV_ITEM + '20' ) ALPHA = IN }|
                              VENDOR_NO     = IV_VENDOR
                              ALLOC_NMBR    = LV_ASSIGNMENT
                              BUSINESSPLACE = LV_BUS_PLC
                              BUS_AREA = GSBER
                              PROFIT_CTR = LV_PROFIT_CENTER
                              ITEM_TEXT = MSG
                              SECTIONCODE   = LV_SECT_CODE
                              PMNT_BLOCK = 'Q'
                              PMNTTRMS = ZTERM
*                              SP_GL_IND  =  'G'
                              COMP_CODE     = IV_COMP_CODE ) ).

      LT_AMOUNT = VALUE #( ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '10' ) ALPHA = IN }|
                             CURR_TYPE  = LC_CURR_TYPE
                             AMT_DOCCUR =  IV_GST_AMOUNT
                             CURRENCY   = LC_CURRENCY )
                           ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '20' ) ALPHA = IN }|
                             CURR_TYPE  = LC_CURR_TYPE
                             AMT_DOCCUR = ( IV_GST_AMOUNT * -1 )
                             CURRENCY   = LC_CURRENCY ) ).
*
      SELECT SINGLE NAME1,NAME2,PSTLZ,ORT01,STCD3,REGIO,BANKN,LAND1 FROM BSEC INTO @DATA(LS_ONE_VEN)
                                                    WHERE BELNR = @IV_DOC_NUM
                                                          AND GJAHR = @IV_FIS_YEAR
                                                          AND BUKRS = @IV_COMP_CODE
                                                          AND XCPDK = 'X'.
      IF SY-SUBRC = 0.
        LS_ONE-NAME = LS_ONE_VEN-NAME1.
        LS_ONE-NAME_2 = LS_ONE_VEN-NAME2.
        LS_ONE-POSTL_CODE = LS_ONE_VEN-PSTLZ.
        LS_ONE-CITY = LS_ONE_VEN-ORT01.
        LS_ONE-TAX_NO_3 = LS_ONE_VEN-STCD3.
        LS_ONE-REGION = LS_ONE_VEN-REGIO.
        LS_ONE-COUNTRY = LS_ONE_VEN-LAND1.

      ENDIF.
      IF LV_NOT_LINK = 'X'.
      ELSE.

        DATA(LV_POSTED_KEY) = VALUE BAPIACHE09-OBJ_KEY( ).
        CLEAR: LS_EXT.
        REFRESH : LT_EXT[].
        LS_EXT-STRUCTURE = 'ZCY_REBZG'.
        LS_EXT-VALUEPART1 = LS_ACC_DATA-BELNR.
        LS_EXT-VALUEPART2 = LS_ACC_DATA-GJAHR.
        LS_EXT-VALUEPART3 = LV_BUZEI.
        APPEND LS_EXT TO LT_EXT.
      ENDIF.

      CALL FUNCTION 'BAPI_ACC_DOCUMENT_POST'
        EXPORTING
          DOCUMENTHEADER = LS_HEADER
          CUSTOMERCPD    = LS_ONE
        IMPORTING
          OBJ_KEY        = LV_POSTED_KEY
        TABLES
          ACCOUNTGL      = LT_GL
          ACCOUNTPAYABLE = LT_ACC_PAY
          CURRENCYAMOUNT = LT_AMOUNT
          EXTENSION2     = LT_EXT
          RETURN         = LT_RETURN.

      PROCESS_BAPI_RETURN(
        EXPORTING
          IS_SUCCESS_MSG = LCS_SUCCESS_MSG
          IT_RETURN      = LT_RETURN
        IMPORTING
          ES_RETURN      = DATA(LS_RETURN)
        RECEIVING
          RV_SUCCESS     = RV_POSTED ).

      IF RV_POSTED = ABAP_TRUE.
        CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
          EXPORTING
            WAIT = ABAP_TRUE.

        EV_DOC_NUM   = |{ LV_POSTED_KEY+0(10) ALPHA = IN }|.
        EV_COMP_CODE = LV_POSTED_KEY+10(4).
        EV_FIS_YEAR  = LV_POSTED_KEY+14(4).

        DATA(LV_POSTED) = CHECK_DOCUMENT_POSTED(
                            EXPORTING
                              IV_COMP_CODE = EV_COMP_CODE
                              IV_DOC_NUM   = EV_DOC_NUM
                              IV_FIS_YEAR  = EV_FIS_YEAR ).

        DATA LT_ACCCHG TYPE STANDARD TABLE OF ACCCHG.
        CLEAR LT_ACCCHG.
        "" Default Key need to be replace with blank on debit item
        SELECT SINGLE ZLSPR FROM BSEG INTO @DATA(BLK) WHERE BELNR =  @EV_DOC_NUM AND BUKRS =  @EV_COMP_CODE
          AND GJAHR = @EV_FIS_YEAR AND SHKZG = 'S'.

        IF BLK IS NOT INITIAL AND SY-SUBRC = 0.
          LT_ACCCHG = VALUE #( ( FDNAME = 'ZLSPR'
                                 OLDVAL = BLK
                                 NEWVAL = ' ' ) ).

          CALL FUNCTION 'FI_DOCUMENT_CHANGE'
            EXPORTING
              I_OBZEI              = '001'
              I_BUKRS              = EV_COMP_CODE
              I_BELNR              = EV_DOC_NUM
              I_GJAHR              = EV_FIS_YEAR
            TABLES
              T_ACCCHG             = LT_ACCCHG
            EXCEPTIONS
              NO_REFERENCE         = 1
              NO_DOCUMENT          = 2
              MANY_DOCUMENTS       = 3
              WRONG_INPUT          = 4
              OVERWRITE_CREDITCARD = 5
              ERROR_MESSAGE        = 6
              OTHERS               = 7.
          IF SY-SUBRC <> 0.
            MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
              WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4 INTO EV_MESSAGE.
          ELSE.
*          RV_TOGGLED = ABAP_TRUE.
          ENDIF.
        ENDIF.


      ELSE.
        EV_MESSAGE = LS_RETURN-MESSAGE.
      ENDIF.
    ELSE.
      EV_MESSAGE = |{ IV_COMP_CODE }-{ IV_DOC_NUM }-{ IV_FIS_YEAR } does not exist|.
    ENDIF.
    CLEAR: BLK.
    CLEAR:BKTXT.
    CLEAR:LV_NOT_LINK.
    CLEAR:ZTERM.
  ENDMETHOD.

  METHOD PERFORM_GRC_CHECK.
    CLEAR:
      EV_SCORE,
      EV_MESSAGE,
      RV_RESULT.
    DATA: GRC1 TYPE I.
    DATA: GRC3 TYPE I.
    SELECT SINGLE * FROM ZCY_GRC_SCORE INTO @DATA(LT_GRC) WHERE VENDOR = @IV_VENDOR.
    IF SY-SUBRC = 0.
      GRC1 = LT_GRC-GSTR1_SCORE.
      GRC3 = LT_GRC-GSTR3B_SCORE.
      IF GRC1 LE GRC3.
        EV_SCORE = LT_GRC-GSTR1_SCORE.
      ELSE.
        EV_SCORE = LT_GRC-GSTR3B_SCORE.
      ENDIF.

    ELSE.
      RV_RESULT = 'F'.
    ENDIF.
    CLEAR:GRC1,GRC3.
    " get grc score using vendor/gstin
    " #ToDo

    " get threshold value

    " if grc_score >= threshold --> grc_result = 'S'
    " else --> grc_result = 'F'
    EV_MESSAGE = TEXT-012.
  ENDMETHOD.

  METHOD SPLIT_DOCUMENT.
    CONSTANTS LCS_SUCCESS_MSG TYPE MSTY_MESSAGE VALUE '312F5'.

    DATA:
      LV_SUBRC   TYPE SYST-SUBRC,
      LT_MESSAGE TYPE STANDARD TABLE OF BDCMSGCOLL,
      LT_RETURN  TYPE BAPIRET2_T.

    CLEAR:
      EV_COMP_CODE,
      EV_DOC_NUM,
      EV_FIS_YEAR,
      EV_MESSAGE,
      RV_SPLIT.

    " spliting bdc
    DATA(LS_ACC_DATA) = VALUE #( MT_ACC_DATA[ KEY SEC_KEY
                                                BUKRS = IV_COMP_CODE
                                                BELNR = IV_DOC_NUM
                                                GJAHR = IV_FIS_YEAR
                                                KOART = MCS_ACC_TYPE-VENDOR ] OPTIONAL ).

    IF LS_ACC_DATA IS NOT INITIAL.
      CLEAR:
        LV_SUBRC,
        LT_MESSAGE.

      CALL FUNCTION 'ZCY_FM_HOLD_GST_AMOUNT'
        EXPORTING
          IV_DOC_DATE    = CONV BDCDATA-FVAL( |{ LS_ACC_DATA-BLDAT DATE = USER }| )
          IV_COMP_CODE   = CONV BDCDATA-FVAL( IV_COMP_CODE )
          IV_POST_DATE   = CONV BDCDATA-FVAL( |{ SY-DATLO DATE = USER }| )
          IV_REFERENCE   = CONV BDCDATA-FVAL( LS_ACC_DATA-XBLNR )
          IV_HEADER_TEXT = CONV BDCDATA-FVAL( LS_ACC_DATA-BKTXT )
          IV_VENDOR_CODE = CONV BDCDATA-FVAL( IV_VENDOR )
          IV_GST_AMOUNT  = CONV BDCDATA-FVAL( CONDENSE( |{ IV_GST_AMT }| ) )
          IV_ASSIGNMENT  = CONV BDCDATA-FVAL( IV_DOC_NUM )
          IV_TEXT        = CONV BDCDATA-FVAL( TEXT-011 )
          IV_DOC_NUM     = CONV BDCDATA-FVAL( IV_DOC_NUM )
        IMPORTING
          EV_SUBRC       = LV_SUBRC
        TABLES
          ET_MESSTAB     = LT_MESSAGE.

      IF LT_MESSAGE IS NOT INITIAL.
        CLEAR LT_RETURN.

        CALL FUNCTION 'CONVERT_BDCMSGCOLL_TO_BAPIRET2'
          TABLES
            IMT_BDCMSGCOLL = LT_MESSAGE
            EXT_RETURN     = LT_RETURN.

        PROCESS_BAPI_RETURN(
          EXPORTING
            IS_SUCCESS_MSG = LCS_SUCCESS_MSG
            IT_RETURN      = LT_RETURN
          IMPORTING
            ES_RETURN      = DATA(LS_RETURN)
          RECEIVING
            RV_SUCCESS     = RV_SPLIT ).
      ENDIF.

      IF RV_SPLIT = ABAP_TRUE.
        EV_COMP_CODE = IV_COMP_CODE.
        EV_DOC_NUM   = |{ CONV BKPF-BELNR( CONDENSE( LS_RETURN-MESSAGE_V1 ) ) ALPHA = IN }|.

        EV_FIS_YEAR  = GET_FISCAL_YEAR(
                         EXPORTING
                           IV_COMP_CODE = IV_COMP_CODE
                           IV_DATE      = SY-DATLO ).

        DATA(LV_POSTED) = CHECK_DOCUMENT_POSTED(
                            EXPORTING
                              IV_COMP_CODE = EV_COMP_CODE
                              IV_DOC_NUM   = EV_DOC_NUM
                              IV_FIS_YEAR  = EV_FIS_YEAR ).

        SELECT A~*, B~*
          FROM BKPF AS A
          INNER JOIN BSEG AS B
          ON  A~BUKRS = B~BUKRS
          AND A~BELNR = B~BELNR
          AND A~GJAHR = B~GJAHR
          WHERE A~BUKRS = @EV_COMP_CODE
            AND A~BELNR = @EV_DOC_NUM
            AND A~GJAHR = @EV_FIS_YEAR
          APPENDING CORRESPONDING FIELDS OF TABLE @MT_ACC_DATA.
      ELSE.
        EV_MESSAGE = LS_RETURN-MESSAGE.
      ENDIF.
    ELSE.
      EV_MESSAGE = |{ IV_COMP_CODE }-{ IV_DOC_NUM }-{ IV_FIS_YEAR } does not exist|.
    ENDIF.
  ENDMETHOD.

  METHOD POST_JV.
    "#ToDo
    CONSTANTS:
      LC_OBJ_TYPE     TYPE BAPIACHE08-OBJ_TYPE VALUE 'BKPFF',
      LC_OBJ_KEY      TYPE BAPIACHE08-OBJ_KEY  VALUE '$',
      LC_DOC_TYPE     TYPE BAPIACHE08-DOC_TYPE VALUE 'SA',
      LC_CURRENCY     TYPE BAPIACCR08-CURRENCY VALUE 'INR',
      LCS_SUCCESS_MSG TYPE MSTY_MESSAGE        VALUE '605RW',

      BEGIN OF LCS_TAX_TYPE,
        INTERSTATE    TYPE BSET-KTOSL VALUE 'JII',
        INTRA_CENTRAL TYPE BSET-KTOSL VALUE 'JIC',
        INTRA_STATE   TYPE BSET-KTOSL VALUE 'JIS',
      END OF LCS_TAX_TYPE,

      BEGIN OF LCS_GL,
        IGST_DEBIT TYPE BAPIACGL08-GL_ACCOUNT VALUE '0000340843',
*        igst_credit type bapiacgl08-gl_account value '',
        CGST_DEBIT TYPE BAPIACGL08-GL_ACCOUNT VALUE '0000340841',
*        cgst_credit type bapiacgl08-gl_account value '',
        SGST_DEBIT TYPE BAPIACGL08-GL_ACCOUNT VALUE '0000340842',
*        sgst_credit type bapiacgl08-gl_account value '',
      END OF LCS_GL.

    DATA:
      LS_HEADER TYPE BAPIACHE08,
      LT_GL     TYPE STANDARD TABLE OF BAPIACGL08,
      LT_AMOUNT TYPE STANDARD TABLE OF BAPIACCR08,
      LT_RETURN TYPE STANDARD TABLE OF BAPIRET2,
      LV_ITEM   TYPE BAPIACGL08-ITEMNO_ACC.

    CLEAR:
      EV_COMP_CODE,
      EV_DOC_NUM,
      EV_FIS_YEAR,
      EV_MESSAGE,
      RV_POSTED,
      LS_HEADER,
      LT_GL,
      LT_AMOUNT,
      LT_RETURN.

    " Post JV using BAPI
    " #ToDo
    DATA(LS_ACC_DATA) = VALUE #( MT_ACC_DATA[ KEY SEC_KEY
                                                BUKRS = IV_COMP_CODE
                                                BELNR = IV_DOC_NUM
                                                GJAHR = IV_FIS_YEAR
                                                KOART = MCS_ACC_TYPE-VENDOR ] OPTIONAL ).

    IF LS_ACC_DATA IS NOT INITIAL.
      DATA(LT_ACC_ITEM) = FILTER #( MT_ACC_DATA USING KEY PRIMARY_KEY
                                      WHERE BUKRS = IV_COMP_CODE
                                        AND BELNR = IV_DOC_NUM
                                        AND GJAHR = IV_FIS_YEAR ).

      DATA(LT_TAX_ITEM) = FILTER #( MT_TAX_DATA USING KEY SEC_KEY
                                      WHERE BUKRS = IV_COMP_CODE
                                        AND BELNR = IV_DOC_NUM
                                        AND GJAHR = IV_FIS_YEAR ).

      LOOP AT LT_ACC_ITEM INTO DATA(LS_ACC_ITEM).
        IF LS_ACC_ITEM-PRCTR IS NOT INITIAL.
          DATA(LV_PROFIT_CENTER) = LS_ACC_ITEM-PRCTR.
          CLEAR LS_ACC_ITEM.
          EXIT.
        ENDIF.
        CLEAR LS_ACC_ITEM.
      ENDLOOP.

      DATA(LV_TAX_TYPE) = VALUE #( LT_TAX_ITEM[ 1 ]-KTOSL OPTIONAL ).

      LS_HEADER = VALUE #( OBJ_TYPE   = LC_OBJ_TYPE
                           OBJ_KEY    = LC_OBJ_KEY
                           OBJ_SYS    = LS_ACC_DATA-AWSYS
                           USERNAME   = SY-UNAME
                           COMP_CODE  = IV_COMP_CODE
                           HEADER_TXT = |{ IV_DOC_NUM }{ IV_FIS_YEAR }|
                           REF_DOC_NO = LS_ACC_DATA-XBLNR
                           DOC_DATE   = LS_ACC_DATA-BLDAT
                           DOC_TYPE   = LC_DOC_TYPE
                           PSTNG_DATE = SY-DATLO ).

      IF LV_TAX_TYPE = LCS_TAX_TYPE-INTERSTATE.
        CLEAR LV_ITEM.
        LT_GL = VALUE #( PSTNG_DATE = SY-DATLO
                         COMP_CODE  = IV_COMP_CODE
                         DOC_TYPE   = LC_DOC_TYPE
                         ALLOC_NMBR = LS_ACC_DATA-ZUONR
                         PROFIT_CTR = LV_PROFIT_CENTER
                         ITEM_TEXT  = TEXT-011
                           ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '10' ) ALPHA = IN }|
                             GL_ACCOUNT = LCS_GL-IGST_DEBIT )
                           ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '20' ) ALPHA = IN }|
                             GL_ACCOUNT = VALUE #( LT_TAX_ITEM[ KTOSL = LCS_TAX_TYPE-INTERSTATE ]-HKONT OPTIONAL ) ) ). "#EC CI_SORTSEQ

        CLEAR LV_ITEM.
        LT_AMOUNT = VALUE #( CURRENCY = LC_CURRENCY
                               ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '10' ) ALPHA = IN }|
                                 AMT_DOCCUR = IV_GST_AMOUNT )
                               ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '20' ) ALPHA = IN }|
                                 AMT_DOCCUR = ( IV_GST_AMOUNT * -1 ) ) ).
      ENDIF.

IF LV_TAX_TYPE = LCS_TAX_TYPE-INTRA_CENTRAL
        OR LV_TAX_TYPE = LCS_TAX_TYPE-INTRA_STATE.

        CLEAR LV_ITEM.
        LT_GL = VALUE #( PSTNG_DATE = SY-DATLO
                         COMP_CODE  = IV_COMP_CODE
                         DOC_TYPE   = LC_DOC_TYPE
                         ALLOC_NMBR = LS_ACC_DATA-ZUONR
                         PROFIT_CTR = LV_PROFIT_CENTER
                         ITEM_TEXT  = TEXT-011
                           ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '10' ) ALPHA = IN }|
                             GL_ACCOUNT = LCS_GL-CGST_DEBIT )
                           ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '20' ) ALPHA = IN }|
                             GL_ACCOUNT = VALUE #( LT_TAX_ITEM[ KTOSL = LCS_TAX_TYPE-INTRA_CENTRAL ]-HKONT OPTIONAL ) ) "#EC CI_SORTSEQ
                           ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '30' ) ALPHA = IN }|
                             GL_ACCOUNT = LCS_GL-SGST_DEBIT )
                           ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '40' ) ALPHA = IN }|
                             GL_ACCOUNT = VALUE #( LT_TAX_ITEM[ KTOSL = LCS_TAX_TYPE-INTRA_STATE ]-HKONT OPTIONAL ) ) ). "#EC CI_SORTSEQ

        CLEAR LV_ITEM.
        LT_AMOUNT = VALUE #( CURRENCY = LC_CURRENCY
                               ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '10' ) ALPHA = IN }|
                                 AMT_DOCCUR = ( IV_GST_AMOUNT / 2 ) )
                               ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '20' ) ALPHA = IN }|
                                 AMT_DOCCUR = ( ( IV_GST_AMOUNT / 2 ) * -1 ) )
                               ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '30' ) ALPHA = IN }|
                                 AMT_DOCCUR = ( IV_GST_AMOUNT / 2 ) )
                               ( ITEMNO_ACC = |{ CONV POSNR_ACC( LV_ITEM + '40' ) ALPHA = IN }|
                                 AMT_DOCCUR = ( ( IV_GST_AMOUNT / 2 ) * -1 ) ) ).
      ENDIF.

      DATA(LV_POSTED_KEY) = VALUE BAPIACHE02-OBJ_KEY( ).

      CALL FUNCTION 'BAPI_ACC_GL_POSTING_POST'
        EXPORTING
          DOCUMENTHEADER = LS_HEADER
        IMPORTING
          OBJ_KEY        = LV_POSTED_KEY
        TABLES
          ACCOUNTGL      = LT_GL
          CURRENCYAMOUNT = LT_AMOUNT
          RETURN         = LT_RETURN.

      PROCESS_BAPI_RETURN(
        EXPORTING
          IS_SUCCESS_MSG = LCS_SUCCESS_MSG
          IT_RETURN      = LT_RETURN
        IMPORTING
          ES_RETURN      = DATA(LS_RETURN)
        RECEIVING
          RV_SUCCESS     = RV_POSTED ).

      IF RV_POSTED = ABAP_TRUE.
        CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
          EXPORTING
            WAIT = ABAP_TRUE.

        EV_DOC_NUM   = |{ LV_POSTED_KEY+0(10) ALPHA = IN }|.
        EV_COMP_CODE = LV_POSTED_KEY+10(4).
        EV_FIS_YEAR  = LV_POSTED_KEY+14(4).

        DATA(LV_POSTED) = CHECK_DOCUMENT_POSTED(
                            EXPORTING
                              IV_COMP_CODE = EV_COMP_CODE
                              IV_DOC_NUM   = EV_DOC_NUM
                              IV_FIS_YEAR  = EV_FIS_YEAR ).
      ELSE.
        EV_MESSAGE = LS_RETURN-MESSAGE.
      ENDIF.
    ELSE.
      EV_MESSAGE = |{ IV_COMP_CODE }-{ IV_DOC_NUM }-{ IV_FIS_YEAR } does not exist|.
    ENDIF.
  ENDMETHOD.

  METHOD PROCESS_BLOCKED.
    " Here Release might mean:
    " 1. Unblock in case of B or S
    " 2. Reversal in case of JV(with B or S) and D

    " What if vendor is exempted after doc is blocked/split?
    IF CS_DATA-VENDOR_BLOCK_TYPE = MCS_DOC_STATUS-EXEMPTED.
      " Force release the document irrespective of current status?
      DATA(LV_RELEASED) = RELEASE_DOCUMENT( CHANGING CS_DATA = CS_DATA ).
    ELSE.
      " Document follows the normal 'blocked/split/dn posted' flow
      IF TO_UPPER( CS_DATA-RECONCILIATIONSECTION ) IN MRT_MATCHED[].
        LV_RELEASED = RELEASE_DOCUMENT( CHANGING CS_DATA = CS_DATA ).
        READ TABLE LT_BSIK INTO DATA(LS_BSIK) WITH KEY BUKRS = CS_DATA-CUSTOM9 BELNR = CS_DATA-CUSTOM1 GJAHR = CS_DATA-CUSTOM4
         BINARY SEARCH.
        IF SY-SUBRC = 0.
        ELSE.
          REVERSE_DEBIT_NOTE(
          EXPORTING
            IV_DOC_NUM   = |{ CS_DATA-DEBIT_NOTE_NUM ALPHA = IN }|
            IV_COMP_CODE = CS_DATA-DEBIT_NOTE_COMP
            IV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_YEAR
          IMPORTING
            EV_COMP_CODE = CS_DATA-DEBIT_NOTE_REV_COMP
            EV_DOC_NUM   = CS_DATA-DEBIT_NOTE_REV_NUM
            EV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_REV_YEAR
            EV_MESSAGE   = CS_DATA-DEBIT_NOTE_REV_COMMENT
          RECEIVING
            RV_REVERSED  = DATA(RV_RELEASED) ).

          IF RV_RELEASED = ABAP_TRUE.
            GET_LOG(
              IMPORTING
                EV_DATE = CS_DATA-DEBIT_NOTE_REVERSED_ON
                EV_TIME = CS_DATA-DEBIT_NOTE_REVERSED_AT
                EV_USER = CS_DATA-DEBIT_NOTE_REVERSED_BY ).
          ELSE.
            CS_DATA-RELEASE_COMMENT = CS_DATA-DEBIT_NOTE_REV_COMMENT.
          ENDIF.
*          CS_DATA-SPLIT_YEAR = CS_DATA-CUSTOM4.

*         no further action
        ENDIF.
      ENDIF.

    ENDIF.
    IF OK = '&UNBLOCK1' OR OK = '&BLOCK'.
      LV_RELEASED = RELEASE_DOCUMENT( CHANGING CS_DATA = CS_DATA ).
      READ TABLE LT_BSIK INTO LS_BSIK WITH KEY BUKRS = CS_DATA-CUSTOM9  BELNR = CS_DATA-CUSTOM1 GJAHR = CS_DATA-CUSTOM4
      BINARY SEARCH.
      IF SY-SUBRC = 0.

      ELSE.
        REVERSE_DEBIT_NOTE(
        EXPORTING
          IV_DOC_NUM   = |{ CS_DATA-DEBIT_NOTE_NUM ALPHA = IN }|
          IV_COMP_CODE = CS_DATA-DEBIT_NOTE_COMP
          IV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_YEAR
        IMPORTING
          EV_COMP_CODE = CS_DATA-DEBIT_NOTE_REV_COMP
          EV_DOC_NUM   = CS_DATA-DEBIT_NOTE_REV_NUM
          EV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_REV_YEAR
          EV_MESSAGE   = CS_DATA-DEBIT_NOTE_REV_COMMENT
        RECEIVING
          RV_REVERSED  = RV_RELEASED ).

        IF RV_RELEASED = ABAP_TRUE.
          GET_LOG(
            IMPORTING
              EV_DATE = CS_DATA-DEBIT_NOTE_REVERSED_ON
              EV_TIME = CS_DATA-DEBIT_NOTE_REVERSED_AT
              EV_USER = CS_DATA-DEBIT_NOTE_REVERSED_BY ).
        ELSE.
          CS_DATA-RELEASE_COMMENT = CS_DATA-DEBIT_NOTE_REV_COMMENT.
        ENDIF.
        CS_DATA-SPLIT_YEAR = CS_DATA-CUSTOM4.

*         no further action
      ENDIF.
    ENDIF.

    IF LV_RELEASED = ABAP_TRUE.
      " placeholder
    ENDIF.
  ENDMETHOD.

  METHOD RELEASE_DOCUMENT.
    CLEAR RV_RELEASED.
    CASE CS_DATA-STATUS.
      WHEN MCS_DOC_STATUS-BLOCKED.

*        cs_data-split_year = cs_data-CUSTOM4.
        TOGGLE_PAYMENT_BLOCK(
          EXPORTING
            IV_DOC_NUM   = |{ CONV BKPF-BELNR( CS_DATA-CUSTOM1 ) ALPHA = IN }|
            IV_COMP_CODE = CONV #( CS_DATA-CUSTOM9 )
            IV_FIS_YEAR  = CONV #( CS_DATA-CUSTOM4 )
          IMPORTING
            EV_MESSAGE   = CS_DATA-RELEASE_COMMENT
          RECEIVING
            RV_TOGGLED   = RV_RELEASED ).
      WHEN MCS_DOC_STATUS-SPLIT .

        TOGGLE_PAYMENT_BLOCK(
          EXPORTING
            IV_DOC_NUM   = |{ CS_DATA-DEBIT_NOTE_NUM ALPHA = IN }|
            IV_COMP_CODE = CS_DATA-SPLIT_COMP_CODE
            IV_FIS_YEAR  = CS_DATA-SPLIT_YEAR
          IMPORTING
            EV_MESSAGE   = CS_DATA-RELEASE_COMMENT
          RECEIVING
            RV_TOGGLED   = RV_RELEASED ).
*        endif.
      WHEN MCS_DOC_STATUS-DEBIT_NOTE_POSTED ..

        TOGGLE_PAYMENT_BLOCK(
          EXPORTING
            IV_DOC_NUM   = |{ CS_DATA-DEBIT_NOTE_NUM ALPHA = IN }|
            IV_COMP_CODE = CS_DATA-DEBIT_NOTE_COMP
            IV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_YEAR
          IMPORTING
            EV_MESSAGE   = CS_DATA-RELEASE_COMMENT
          RECEIVING
            RV_TOGGLED   = RV_RELEASED ).

      WHEN  MCS_DOC_STATUS-IRELEASED.
        IF OK = '&BLOCK'.
          TOGGLE_PAYMENT_BLOCK(
      EXPORTING
        IV_DOC_NUM   = |{ CS_DATA-DEBIT_NOTE_NUM ALPHA = IN }|
        IV_COMP_CODE = CS_DATA-DEBIT_NOTE_COMP
        IV_FIS_YEAR  = CS_DATA-DEBIT_NOTE_YEAR
      IMPORTING
        EV_MESSAGE   = CS_DATA-RELEASE_COMMENT
      RECEIVING
        RV_TOGGLED   = RV_RELEASED ).
        ENDIF.
      WHEN OTHERS.
    ENDCASE.

    " reverse the JV if posted
    IF RV_RELEASED = ABAP_TRUE.
      IF OK = '&UNBLOCK1'.
        CS_DATA-STATUS = MCS_DOC_STATUS-IRELEASED.
      ELSEIF OK = '&BLOCK'.
        CS_DATA-STATUS = MCS_DOC_STATUS-DEBIT_NOTE_POSTED.
      ELSE.
        CS_DATA-STATUS = MCS_DOC_STATUS-RELEASED.
      ENDIF.
      GET_LOG(
        IMPORTING
          EV_DATE = CS_DATA-RELEASED_ON
          EV_TIME = CS_DATA-RELEASED_AT
          EV_USER = CS_DATA-RELEASED_BY ).

      CS_DATA-MESSAGE = TEXT-014.

      IF CS_DATA-JV_DOC_NUM IS NOT INITIAL
        AND CS_DATA-JV_REV_DOC_NUM IS INITIAL.
        REVERSE_JV(
          EXPORTING
            IV_DOC_NUM   = |{ CS_DATA-JV_DOC_NUM ALPHA = IN }|
            IV_COMP_CODE = CS_DATA-JV_COMP_CODE
            IV_FIS_YEAR  = CS_DATA-JV_YEAR
          IMPORTING
            EV_COMP_CODE = CS_DATA-JV_REV_COMP_CODE
            EV_DOC_NUM   = CS_DATA-JV_REV_DOC_NUM
            EV_FIS_YEAR  = CS_DATA-JV_REV_YEAR
            EV_MESSAGE   = CS_DATA-JV_REV_COMMENT
          RECEIVING
            RV_REVERSED  = DATA(LV_JV_REVERSED) ).

        IF LV_JV_REVERSED = ABAP_TRUE.
          GET_LOG(
            IMPORTING
              EV_DATE = CS_DATA-JV_REVERSED_ON
              EV_TIME = CS_DATA-JV_REVERSED_AT
              EV_USER = CS_DATA-JV_REVERSED_BY ).

          CS_DATA-MESSAGE = TEXT-014.
        ELSE.
          CS_DATA-MESSAGE = TEXT-013.
        ENDIF.
      ENDIF.
    ELSE.
      CS_DATA-MESSAGE = TEXT-013.
    ENDIF.
  ENDMETHOD.

  METHOD TOGGLE_PAYMENT_BLOCK.
    CONSTANTS:
      LC_PAY_BLOCK       TYPE BSEG-ZLSPR    VALUE 'Q',
      LC_PAY_BLOCK_FNAME TYPE ACCCHG-FDNAME VALUE 'ZLSPR'.

    DATA LT_ACCCHG TYPE STANDARD TABLE OF ACCCHG.

    CLEAR:
      EV_MESSAGE,
      RV_TOGGLED.

    " Get blocked or to be blocked line item
    DATA(LT_ACC_ITEM) = FILTER #( MT_ACC_DATA USING KEY PRIMARY_KEY
                                    WHERE BUKRS = IV_COMP_CODE
                                      AND BELNR = IV_DOC_NUM
                                      AND GJAHR = IV_FIS_YEAR ).

    IF LT_ACC_ITEM IS NOT INITIAL.
      " Block/release the item using BAPI/BDC
      TRY.
          " existence of specific spl gl ind means that this is a split doc
          DATA(LS_ACC_ITEM) = LT_ACC_ITEM[ UMSKZ = MC_SPLIT_SPL_GL_IND ]. "#EC CI_SORTSEQ
        CATCH CX_SY_ITAB_LINE_NOT_FOUND ##no_handler.
          TRY.
              " full document block/unblock if vendor item found
              LS_ACC_ITEM = LT_ACC_ITEM[ KOART = MCS_ACC_TYPE-VENDOR
                                         SHKZG = MCS_DC_IND-CREDIT ]. "#EC CI_SORTSEQ
            CATCH CX_SY_ITAB_LINE_NOT_FOUND ##no_handler.
              EV_MESSAGE = |{ IV_COMP_CODE }-{ IV_DOC_NUM }-{ IV_FIS_YEAR } - | &&
                           |Cannot find suitable item for payment block|.
          ENDTRY.
      ENDTRY.

      IF LS_ACC_ITEM IS NOT INITIAL.
        CLEAR LT_ACCCHG.
        LT_ACCCHG = VALUE #( ( FDNAME = 'ZLSPR'
                               OLDVAL = LS_ACC_ITEM-ZLSPR
                               NEWVAL = COND BSEG-ZLSPR( WHEN LS_ACC_ITEM-ZLSPR <> LC_PAY_BLOCK
                                                         THEN LC_PAY_BLOCK ) ) ).

        CALL FUNCTION 'FI_DOCUMENT_CHANGE'
          EXPORTING
            I_OBZEI              = LS_ACC_ITEM-BUZEI
            I_BUKRS              = LS_ACC_ITEM-BUKRS
            I_BELNR              = LS_ACC_ITEM-BELNR
            I_GJAHR              = LS_ACC_ITEM-GJAHR
          TABLES
            T_ACCCHG             = LT_ACCCHG
          EXCEPTIONS
            NO_REFERENCE         = 1
            NO_DOCUMENT          = 2
            MANY_DOCUMENTS       = 3
            WRONG_INPUT          = 4
            OVERWRITE_CREDITCARD = 5
            ERROR_MESSAGE        = 6
            OTHERS               = 7.
        IF SY-SUBRC <> 0.
          MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
            WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4 INTO EV_MESSAGE.
        ELSE.
          RV_TOGGLED = ABAP_TRUE.
        ENDIF.
      ENDIF.
    ELSE.
      EV_MESSAGE = |{ IV_COMP_CODE }-{ IV_DOC_NUM }-{ IV_FIS_YEAR } does not exist|.
    ENDIF.
  ENDMETHOD.

  METHOD REVERSE_DEBIT_NOTE.
    CLEAR:
      EV_COMP_CODE,
      EV_DOC_NUM,
      EV_FIS_YEAR,
      EV_MESSAGE,
      RV_REVERSED.

    " Check whether document is open or cleared
    IF IS_DOCUMENT_OPEN(
         EXPORTING
           IV_DOC_NUM   = IV_DOC_NUM
           IV_COMP_CODE = IV_COMP_CODE
           IV_FIS_YEAR  = IV_FIS_YEAR ).

      " Open - Reverse using FB08
      REVERSE_DOCUMENT(
        EXPORTING
          IV_DOC_NUM   = IV_DOC_NUM
          IV_COMP_CODE = IV_COMP_CODE
          IV_FIS_YEAR  = IV_FIS_YEAR
        IMPORTING
          EV_COMP_CODE = EV_COMP_CODE
          EV_DOC_NUM   = EV_DOC_NUM
          EV_FIS_YEAR  = EV_FIS_YEAR
          EV_MESSAGE   = EV_MESSAGE
        RECEIVING
          RV_REVERSED  = RV_REVERSED ).
    ELSE.
      " Cleared - Post a credit note using BAPI/BDC
      " #ToDo
      EV_MESSAGE = TEXT-012.
    ENDIF.

    IF RV_REVERSED = ABAP_TRUE.
      " placeholder
    ENDIF.
  ENDMETHOD.

  METHOD REVERSE_JV.
    CLEAR:
      EV_COMP_CODE,
      EV_DOC_NUM,
      EV_FIS_YEAR,
      EV_MESSAGE,
      RV_REVERSED.

    " Check whether document is open or cleared
    IF IS_DOCUMENT_OPEN(
         EXPORTING
           IV_DOC_NUM   = IV_DOC_NUM
           IV_COMP_CODE = IV_COMP_CODE
           IV_FIS_YEAR  = IV_FIS_YEAR ).

      " Open - Reverse using FB08
      REVERSE_DOCUMENT(
        EXPORTING
          IV_DOC_NUM   = IV_DOC_NUM
          IV_COMP_CODE = IV_COMP_CODE
          IV_FIS_YEAR  = IV_FIS_YEAR
        IMPORTING
          EV_COMP_CODE = EV_COMP_CODE
          EV_DOC_NUM   = EV_DOC_NUM
          EV_FIS_YEAR  = EV_FIS_YEAR
          EV_MESSAGE   = EV_MESSAGE
        RECEIVING
          RV_REVERSED  = RV_REVERSED ).
    ELSE.
      " Cleared - Post reverse JV using BAPI/BDC?
      " #ToDo
      EV_MESSAGE = TEXT-012.
    ENDIF.

    IF RV_REVERSED = ABAP_TRUE.
      " placeholder
    ENDIF.
  ENDMETHOD.

  METHOD REVERSE_DOCUMENT.
    CONSTANTS:
*      lc_obj_type     type bapiacrev-obj_type   value 'BKPFF',
*      lc_obj_key      type bapiacrev-obj_key    value '$',
      LC_REV_REASON   TYPE BAPIACREV-REASON_REV VALUE '04',   " #Client_Specific
*      lcs_success_msg type msty_message         value '605RW'.
      LCS_SUCCESS_MSG TYPE MSTY_MESSAGE         VALUE '312F5',
      LC_MEM_ID       TYPE C LENGTH 10          VALUE 'FBRA'.

    DATA:
      LV_COMP_CODE TYPE BKPF-BUKRS,
      LV_DOC_NUM   TYPE BKPF-BELNR,
      LV_FIS_YEAR  TYPE BKPF-GJAHR,
      LV_POST_DATE TYPE BKPF-BUDAT,
      LS_RFDT      TYPE RFDT,
      LT_BDC_MSG   TYPE STANDARD TABLE OF BDCMSGCOLL,
      LT_RETURN    TYPE STANDARD TABLE OF BAPIRET2.

    CLEAR:
      EV_COMP_CODE,
      EV_DOC_NUM,
      EV_FIS_YEAR,
      EV_MESSAGE,
      RV_REVERSED,
      LV_COMP_CODE,
      LV_DOC_NUM,
      LV_FIS_YEAR,
      LV_POST_DATE,
      LS_RFDT,
      LT_BDC_MSG,
      LT_RETURN.

    " since we are only want the acc header data, pick the GL item as
    " JV document does not have a vendor item
    " GL item is present in all relevant documents
    TRY.
        DATA(LS_ACC_DATA) = VALUE #( MT_ACC_DATA[ KEY SEC_KEY
                                                    BUKRS = IV_COMP_CODE
                                                    BELNR = IV_DOC_NUM
                                                    GJAHR = IV_FIS_YEAR
                                                    KOART = MCS_ACC_TYPE-GL ] ).
      CATCH CX_SY_ITAB_LINE_NOT_FOUND ##no_handler.
        LS_ACC_DATA = VALUE #( MT_ACC_DATA[ KEY SEC_KEY
                                              BUKRS = IV_COMP_CODE
                                              BELNR = IV_DOC_NUM
                                              GJAHR = IV_FIS_YEAR
                                              KOART = MCS_ACC_TYPE-VENDOR ] ).
    ENDTRY.

    IF LS_ACC_DATA IS NOT INITIAL.
      " Reversal BAPI only works for documents previously created through a BAPI
*      data(ls_reversal) = value bapiacrev( obj_type   = lc_obj_type
*                                           obj_key    = lc_obj_key
*                                           obj_sys    = ls_acc_data-awsys
*                                           obj_key_r  = ls_acc_data-awkey
*                                           reason_rev = lc_rev_reason
*                                           pstng_date = sy-datlo ).
*
*      data(lv_reversed_key) = value bkpf-awkey( ).
*      clear lt_return.
*
*      call function 'BAPI_ACC_DOCUMENT_REV_POST'
*        exporting
*          reversal = ls_reversal
*          bus_act  = ls_acc_data-glvor
*        importing
*          obj_key  = lv_reversed_key
*        tables
*          return   = lt_return.

      CALL FUNCTION 'ZCY_FI_FM_CALL_FB08'
        EXPORTING
          I_BUKRS       = IV_COMP_CODE
          I_BELNR       = IV_DOC_NUM
          I_GJAHR       = IV_FIS_YEAR
          I_STGRD       = LC_REV_REASON
          I_NO_AUTH     = ABAP_TRUE
        IMPORTING
          E_BUDAT       = LV_POST_DATE
        TABLES
          ET_MESSTAB    = LT_BDC_MSG
        EXCEPTIONS
          NOT_POSSIBLE  = 1
          ERROR_MESSAGE = 2
          OTHERS        = 3.
      IF SY-SUBRC <> 0.
        MESSAGE ID SY-MSGID TYPE MCS_MSG_TYPE-ERROR NUMBER SY-MSGNO
          WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4 INTO EV_MESSAGE.

        RETURN.
      ELSE.
        IF LT_BDC_MSG IS NOT INITIAL.
          CALL FUNCTION 'CONVERT_BDCMSGCOLL_TO_BAPIRET2'
            TABLES
              IMT_BDCMSGCOLL = LT_BDC_MSG
              EXT_RETURN     = LT_RETURN.

          PROCESS_BAPI_RETURN(
            EXPORTING
              IS_SUCCESS_MSG = LCS_SUCCESS_MSG
              IT_RETURN      = LT_RETURN
            IMPORTING
              ES_RETURN      = DATA(LS_RETURN)
            RECEIVING
              RV_SUCCESS     = RV_REVERSED ).

          IF RV_REVERSED = ABAP_TRUE.
*            call function 'BAPI_TRANSACTION_COMMIT'
*              exporting
*                wait = abap_true.

*            ev_doc_num   = |{ lv_reversed_key+0(10) alpha = in }|.
*            ev_comp_code = lv_reversed_key+10(4).
*            ev_fis_year  = lv_reversed_key+14(4).

            EV_COMP_CODE = IV_COMP_CODE.
            EV_DOC_NUM   = |{ CONV BKPF-BELNR( CONDENSE( LS_RETURN-MESSAGE_V1 ) ) ALPHA = IN }|.

            EV_FIS_YEAR  = GET_FISCAL_YEAR(
                             EXPORTING
                               IV_COMP_CODE = IV_COMP_CODE
                               IV_DATE      = LV_POST_DATE ).

            DATA(LV_POSTED) = CHECK_DOCUMENT_POSTED(
                                EXPORTING
                                  IV_COMP_CODE = EV_COMP_CODE
                                  IV_DOC_NUM   = EV_DOC_NUM
                                  IV_FIS_YEAR  = EV_FIS_YEAR ).
          ELSE.
            EV_MESSAGE = LS_RETURN-MESSAGE.
          ENDIF.
        ENDIF.
      ENDIF.
    ELSE.
      EV_MESSAGE = |{ IV_COMP_CODE }-{ IV_DOC_NUM }-{ IV_FIS_YEAR } does not exist|.
    ENDIF.
  ENDMETHOD.

  METHOD TRANSFER_TO_GST_HOLD_JV.
    CLEAR RV_PROCESSED.

    IF MS_PROCESSING_OPTIONS-JV_APPLICABILITY IS INITIAL.
      MESSAGE TEXT-017 TYPE MCS_MSG_TYPE-INFORMATION
        DISPLAY LIKE MCS_MSG_TYPE-ERROR.

      RETURN.
    ENDIF.

    LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_DATA>) WHERE CBOX = ABAP_TRUE AND DOCUMENTTYPE <> 'CRE'.
      CASE <LS_DATA>-STATUS.
        WHEN MCS_DOC_STATUS-BLOCKED.
          IF MS_PROCESSING_OPTIONS-JV_APPLICABILITY-BLOCKED = ABAP_TRUE.
            DATA(LV_POST_JV) = ABAP_TRUE.
          ENDIF.
        WHEN MCS_DOC_STATUS-SPLIT.
          IF MS_PROCESSING_OPTIONS-JV_APPLICABILITY-SPLIT = ABAP_TRUE.
            LV_POST_JV = ABAP_TRUE.
          ENDIF.
        WHEN MCS_DOC_STATUS-DEBIT_NOTE_POSTED.
          IF MS_PROCESSING_OPTIONS-JV_APPLICABILITY-DEBIT_NOTE = ABAP_TRUE.
            LV_POST_JV = ABAP_TRUE.
          ENDIF.
        WHEN OTHERS.
      ENDCASE.

      IF LV_POST_JV = ABAP_TRUE AND <LS_DATA>-JV_DOC_NUM IS INITIAL.
        DATA(LV_PROCESSABLE) = ABAP_TRUE.

        POST_JV(
          EXPORTING
            IV_COMP_CODE  = CONV #( <LS_DATA>-CUSTOM9 )
            IV_DOC_NUM    = |{ CONV BKPF-BELNR( <LS_DATA>-CUSTOM1 ) ALPHA = IN }|
            IV_FIS_YEAR   = CONV #( <LS_DATA>-CUSTOM4 )
            IV_GST_AMOUNT = <LS_DATA>-FI_TAX_VALUE
          IMPORTING
            EV_COMP_CODE  = <LS_DATA>-JV_COMP_CODE
            EV_DOC_NUM    = <LS_DATA>-JV_DOC_NUM
            EV_FIS_YEAR   = <LS_DATA>-JV_YEAR
            EV_MESSAGE    = <LS_DATA>-JV_COMMENT
          RECEIVING
            RV_POSTED     = DATA(LV_JV_POSTED) ).

        IF LV_JV_POSTED = ABAP_TRUE.
          GET_LOG(
            IMPORTING
              EV_DATE = <LS_DATA>-JV_POSTED_ON
              EV_TIME = <LS_DATA>-JV_POSTED_AT
              EV_USER = <LS_DATA>-JV_POSTED_BY ).

          <LS_DATA>-MESSAGE = TEXT-014.
        ELSE.
          <LS_DATA>-MESSAGE = TEXT-013.
        ENDIF.
      ENDIF.

      " set status icons based on updated document status
      " will have no effect if there's no change in the status
      DERIVE_STATUS_ICON(
        CHANGING
          CS_DATA = <LS_DATA> ).

      " update DB irrespective of processing status/result
      RV_PROCESSED = UPDATE_DB( EXPORTING IS_DATA = <LS_DATA> ).

      CLEAR:
        LV_POST_JV,
        LV_JV_POSTED.
    ENDLOOP.

    IF SY-SUBRC = 4 OR LV_PROCESSABLE = ABAP_FALSE.
      MESSAGE TEXT-006 TYPE MCS_MSG_TYPE-SUCCESS
        DISPLAY LIKE MCS_MSG_TYPE-ERROR.
    ENDIF.
  ENDMETHOD.

  METHOD UPDATE_DB.
    CLEAR RV_UPDATED.

    IF IS_DATA IS NOT INITIAL.
      DATA(LS_2A_PAY_BLK) = CORRESPONDING ZCY_T_2A_PAY_BLK( IS_DATA ).
      MODIFY ZCY_T_2A_PAY_BLK FROM LS_2A_PAY_BLK.
      IF SY-DBCNT <> 0.
        RV_UPDATED = ABAP_TRUE.
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD GET_LOG.
    CLEAR:
      EV_DATE,
      EV_TIME,
      EV_USER.

    GET TIME.

    EV_DATE = SY-DATLO.
    EV_TIME = SY-TIMLO.

    SELECT SINGLE NAME_TEXT
      FROM V_USR_NAME
      WHERE BNAME = @SY-UNAME
      INTO @DATA(LV_NAME).                              "#EC CI_NOORDER

    EV_USER =
      CONDENSE( |{ SY-UNAME }{ COND TEXT80( WHEN LV_NAME IS NOT INITIAL
                                            THEN | - { LV_NAME }| ) }| ).
  ENDMETHOD.

  METHOD GET_FISCAL_YEAR.
    CLEAR RV_FISCAL_YEAR.

    CALL FUNCTION 'FI_PERIOD_DETERMINE'
      EXPORTING
        I_BUDAT        = IV_DATE
        I_BUKRS        = IV_COMP_CODE
      IMPORTING
        E_GJAHR        = RV_FISCAL_YEAR
      EXCEPTIONS
        FISCAL_YEAR    = 1
        PERIOD         = 2
        PERIOD_VERSION = 3
        POSTING_PERIOD = 4
        SPECIAL_PERIOD = 5
        VERSION        = 6
        POSTING_DATE   = 7
        OTHERS         = 8.
    IF SY-SUBRC <> 0.
      " error handling
    ENDIF.
  ENDMETHOD.

  METHOD IS_DOCUMENT_OPEN.

    CLEAR RV_OPEN.
    " Generic logic based on bseg-augbl as suggested by Vaibhav
    SELECT SINGLE @ABAP_TRUE
      FROM BSEG
      WHERE BUKRS = @IV_COMP_CODE
        AND BELNR = @IV_DOC_NUM
        AND GJAHR = @IV_FIS_YEAR
        AND AUGBL = @SPACE
      INTO @RV_OPEN.
  ENDMETHOD.

  METHOD CHECK_DOCUMENT_POSTED.
    CLEAR RV_POSTED.

    DO.
      CALL FUNCTION 'ENQUE_SLEEP'
        EXPORTING
          SECONDS        = 1
        EXCEPTIONS
          SYSTEM_FAILURE = 1
          OTHERS         = 2.
      IF SY-SUBRC <> 0.                                "#EC FM_SUBRC_OK
      ENDIF.

      SELECT SINGLE @ABAP_TRUE
        FROM BKPF
        WHERE BUKRS = @IV_COMP_CODE
          AND BELNR = @IV_DOC_NUM
          AND GJAHR = @IV_FIS_YEAR
        INTO @RV_POSTED.

      IF RV_POSTED = ABAP_TRUE.
        EXIT.
      ENDIF.
    ENDDO.
  ENDMETHOD.

  METHOD PROCESS_BAPI_RETURN.
    CLEAR:
      ES_RETURN,
      RV_SUCCESS.

    IF IT_RETURN IS NOT INITIAL.
      TRY.
          ES_RETURN = IT_RETURN[ TYPE   = MCS_MSG_TYPE-SUCCESS
                                 ID     = IS_SUCCESS_MSG-ID
                                 NUMBER = IS_SUCCESS_MSG-NUMBER ].

          RV_SUCCESS = ABAP_TRUE.
        CATCH CX_SY_ITAB_LINE_NOT_FOUND ##no_handler.
          TRY.
              ES_RETURN = IT_RETURN[ TYPE = MCS_MSG_TYPE-ABEND ].
            CATCH CX_SY_ITAB_LINE_NOT_FOUND ##no_handler.
              TRY.
                  ES_RETURN = IT_RETURN[ TYPE = MCS_MSG_TYPE-ERROR ].

                  " In case of more than 1 error message read the last message
                  DATA(LT_RETURN) = IT_RETURN[].
                  DELETE LT_RETURN WHERE TYPE <> MCS_MSG_TYPE-ERROR.

                  IF LINES( LT_RETURN ) > 1.
                    " last line of the table
                    ES_RETURN =
                      VALUE #( LT_RETURN[ LINES( LT_RETURN ) ] OPTIONAL ).
                  ENDIF.
                CATCH CX_SY_ITAB_LINE_NOT_FOUND ##no_handler.
                  TRY.
                      ES_RETURN = IT_RETURN[ TYPE = MCS_MSG_TYPE-UNKNOWN ].
                    CATCH CX_SY_ITAB_LINE_NOT_FOUND ##no_handler.
                      ES_RETURN = IT_RETURN[ 1 ].
                  ENDTRY.
              ENDTRY.
          ENDTRY.
      ENDTRY.
    ENDIF.
  ENDMETHOD.

  METHOD GET_PROCESSING_OPTIONS.
    CONSTANTS:
      BEGIN OF LCS,
        PROCESS_BEFORE_FILING TYPE STRING VALUE 'PBF',
        JV_APPLICABLE         TYPE STRING VALUE 'JV',
        JV_BLOCK              TYPE STRING VALUE 'B',
        JV_SPLIT              TYPE STRING VALUE 'S',
        JV_DEBIT_NOTE         TYPE STRING VALUE 'D',
        DUE_BEFORE_FILING     TYPE STRING VALUE 'DBF',
        CLEARED               TYPE STRING VALUE 'CL',
        OPEN                  TYPE STRING VALUE 'OP',
        DUE_AFTER_FILING      TYPE STRING VALUE 'DAF',
      END OF LCS.

    CLEAR RV_PROCESSING_OPTIONS.

    RV_PROCESSING_OPTIONS =
      |{ LCS-PROCESS_BEFORE_FILING } = '{ P_PBF }', | &&
      |[ { LCS-JV_APPLICABLE } = '{ P_JV }', |        &&
        |{ LCS-JV_BLOCK } = '{ P_BLOCK }', |          &&
        |{ LCS-JV_SPLIT } = '{ P_SPLIT }', |          &&
        |{ LCS-JV_DEBIT_NOTE } = '{ P_DEB_N }' ], |   &&
      |[ { LCS-DUE_BEFORE_FILING } = '{ P_DBF }', |   &&
        |{ LCS-CLEARED } = '{ P_DBF_CL }', |          &&
        |{ LCS-OPEN } = '{ P_DBF_OP }' ], |           &&
      |[ { LCS-DUE_AFTER_FILING } = '{ P_DAF }', |    &&
        |{ LCS-CLEARED } = '{ P_DAF_CL }', |          &&
        |{ LCS-OPEN } = '{ P_DAF_OP }' ]|.
  ENDMETHOD.

  METHOD REFRESH_GRID.
    IF MO_ALV IS BOUND.
      CL_PROGRESS_INDICATOR=>PROGRESS_INDICATE(
        EXPORTING
          I_TEXT               = TEXT-015
          I_PROCESSED          = 50                      "#EC NUMBER_OK
          I_TOTAL              = 100                     "#EC NUMBER_OK
          I_OUTPUT_IMMEDIATELY = ABAP_TRUE ).

      MO_ALV->REFRESH_TABLE_DISPLAY(
        EXPORTING
          IS_STABLE = VALUE #( ROW = ABAP_TRUE COL = ABAP_TRUE )  " holds cursor position even after refresh
        EXCEPTIONS
          FINISHED = 1
          OTHERS   = 2 ).

      " Note: do NOT use salv -> refresh, as it would redisplay the fullscreen grid and...
      " ...all settings made to grid object in after_refresh event at the start of alv
      " display will be overwritten by salv defaults

      DATA LC_COL_OPTIMISE TYPE SY-UCOMM VALUE '&OPT'.
      " ucomm equivalent for col optimise(cl_gui_alv_grid=>mc_fc_col_optimize)
      MO_ALV->SET_FUNCTION_CODE( CHANGING C_UCOMM = LC_COL_OPTIMISE ).

      FLUSH( ).
    ENDIF.
  ENDMETHOD.

  METHOD ON_HOTSPOT_CLICK.
    IF E_COLUMN_ID = 'DEBIT_NOTE_NUM'.
      READ TABLE MT_DATA INDEX ES_ROW_NO-ROW_ID INTO DATA(LS_FINAL).
      IF SY-SUBRC = 0.
        SET PARAMETER ID 'BLN' FIELD LS_FINAL-DEBIT_NOTE_NUM.
        SET PARAMETER ID 'BUK' FIELD LS_FINAL-DEBIT_NOTE_COMP.
        SET PARAMETER ID 'GJR' FIELD LS_FINAL-DEBIT_NOTE_YEAR.
        CALL TRANSACTION 'FB03' AND SKIP FIRST SCREEN.
      ENDIF.
    ELSEIF E_COLUMN_ID = 'DEBIT_NOTE_REV_NUM'.
      READ TABLE MT_DATA INDEX ES_ROW_NO-ROW_ID INTO LS_FINAL.
      IF SY-SUBRC = 0.
        SET PARAMETER ID 'BLN' FIELD LS_FINAL-DEBIT_NOTE_REV_NUM.
        SET PARAMETER ID 'BUK' FIELD LS_FINAL-DEBIT_NOTE_REV_COMP.
        SET PARAMETER ID 'GJR' FIELD LS_FINAL-DEBIT_NOTE_REV_YEAR.
        CALL TRANSACTION 'FB03' AND SKIP FIRST SCREEN.
      ENDIF.
    ELSEIF E_COLUMN_ID = 'CUSTOM1'.
      READ TABLE MT_DATA INDEX ES_ROW_NO-ROW_ID INTO LS_FINAL.
      IF SY-SUBRC = 0.
        SET PARAMETER ID 'BLN' FIELD LS_FINAL-CUSTOM1.
        SET PARAMETER ID 'BUK' FIELD LS_FINAL-CUSTOM9.
        SET PARAMETER ID 'GJR' FIELD LS_FINAL-CUSTOM4.
        CALL TRANSACTION 'FB03' AND SKIP FIRST SCREEN.
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD EXIT.
    DATA LV_ANSWER TYPE C LENGTH 1.
    CALL FUNCTION 'POPUP_TO_CONFIRM'
      EXPORTING
        TITLEBAR              = |Exit Confirmation|
        TEXT_QUESTION         = |Are you sure you want to exit the program?|
        DISPLAY_CANCEL_BUTTON = ABAP_TRUE
      IMPORTING
        ANSWER                = LV_ANSWER
      EXCEPTIONS
        TEXT_NOT_FOUND        = 1
        OTHERS                = 2.
    IF SY-SUBRC <> 0.
* Implement suitable error handling here
    ENDIF.

    IF LV_ANSWER EQ '1'.
      IF MO_ALV IS BOUND.
        MO_ALV->FREE(
          EXCEPTIONS
            CNTL_ERROR = 1
            CNTL_SYSTEM_ERROR = 2
            OTHERS = 3 ).
      ENDIF.

      FLUSH( ).

      CLEAR:
        MO_ALV,
        MO_APP,
        MT_DATA.

      SET SCREEN 0.
      LEAVE SCREEN.
    ENDIF.
  ENDMETHOD.

  METHOD FLUSH.
    " SAP Doc
*--------------------------------------------------------------------*
    " Send Buffered Automation Queue to Frontend
*--------------------------------------------------------------------*
    " Functionality
*--------------------------------------------------------------------*
    " This method is used to synchronize the automation queue.
    " The buffered operations are then send to the front end using GUI-RFC.
    " On the front end, the automation queue is processed in the order in which it was filled.
    "
    " If an error occurs, an exception is raised. You must catch and handle this error.
    " Since it is not possible to identify the cause of the error from the exception itself,
    " there are tools available in the debugger and the SAPGUI tools to help you:
    " •Debugger: Select the option Automation Controller: Always process requests synchronously.
    " The system then calls the method cl_gui_cfw=>flush automatically after each method called
    " by Automation Controller.
    " •SAPGUI: In the SAPgui settings, under Trace, select Automation.
    " This writes the communication between the application server and Automation Controller
    " to a trace file.
    " This information can then be analyzed.
*--------------------------------------------------------------------*
    " Syntax
    " CALL METHOD cl_gui_cfw=>flush EXCEPTIONS CNTL_SYSTEM_ERROR = 1 CNTL_ERROR = 2.
    " Caution: Do not use any more synchronizations in your program than are really necessary.
    " Each synchronization opens a new RFC connection to SAPGUI.
*--------------------------------------------------------------------*
    " gui commands like the one above(Eg: set cell focus, refresh_table_display) are usually
    " buffered in a queue by the system and dispatched at intervals to the frontend to reduce
    " the number of rfc calls(this process is called synchronisation and the queue is called
    " automation queue). We can force this process of dispatching commands to frontend immediately
    " by using the commands below
    CL_GUI_CFW=>FLUSH( EXCEPTIONS CNTL_ERROR = 1 CNTL_SYSTEM_ERROR = 2 OTHERS = 3 ).
    CL_GUI_CFW=>DISPATCH( IMPORTING RETURN_CODE = DATA(LV_RC) ).
    " sends events associated with above gui actions to respective event handlers
  ENDMETHOD.

  METHOD SELECT_ALL.
    DATA LRT_STATUS TYPE RANGE OF ZCY_STR_2A_PAY_BLOCK-STATUS.

    CLEAR LRT_STATUS[].
    LRT_STATUS[] = VALUE #( SIGN = 'I' OPTION = 'EQ'
                              ( LOW = MCS_DOC_STATUS-MATCHED )
                              ( LOW = MCS_DOC_STATUS-RELEASED ) ).

    "24.05.2024
    IF SY-BATCH = ABAP_TRUE.
      MODIFY MT_DATA FROM VALUE #( CBOX = ABAP_TRUE ) TRANSPORTING CBOX
     WHERE CBOX = ABAP_FALSE
       AND STATUS NOT IN LRT_STATUS[].

    ELSE.

      DATA: I_FILTER_ENTRIES TYPE LVC_T_FIDX,
            GV_SEL_VALID     TYPE CHAR1,
            GV_SEL_TABIX     TYPE SY-TABIX.

      IF MO_ALV IS NOT INITIAL.
        CALL METHOD MO_ALV->CHECK_CHANGED_DATA
          IMPORTING
            E_VALID = GV_SEL_VALID.
      ENDIF.

      IF GV_SEL_VALID EQ 'X'.
        CALL METHOD MO_ALV->GET_FILTERED_ENTRIES
          IMPORTING
            ET_FILTERED_ENTRIES = I_FILTER_ENTRIES.
      ENDIF.
      IF I_FILTER_ENTRIES IS NOT INITIAL.
        LOOP AT MT_DATA ASSIGNING FIELD-SYMBOL(<LS_FLIER>).
          GV_SEL_TABIX = SY-TABIX.
          READ TABLE I_FILTER_ENTRIES FROM GV_SEL_TABIX TRANSPORTING NO FIELDS.
          IF SY-SUBRC IS NOT INITIAL.
            <LS_FLIER>-CBOX = ABAP_TRUE.
          ENDIF.
        ENDLOOP.
      ELSE.
        MODIFY MT_DATA FROM VALUE #( CBOX = ABAP_TRUE ) TRANSPORTING CBOX
        WHERE CBOX = ABAP_FALSE
        AND STATUS NOT IN LRT_STATUS[].
      ENDIF.
    ENDIF.
  ENDMETHOD.

  METHOD DESELECT_ALL.
    MODIFY MT_DATA FROM VALUE #( CBOX = ABAP_FALSE ) TRANSPORTING CBOX WHERE CBOX = ABAP_TRUE.
  ENDMETHOD.

ENDCLASS.

CLASS LCL_MAIN IMPLEMENTATION.
  METHOD START.
    TRY.
        DATA(LO_APP) = LCL_APP=>GET_INSTANCE( ).
        IF LO_APP IS BOUND.
          LO_APP->PROCESS( ).
        ENDIF.
      CATCH CX_ROOT INTO DATA(LOX_ROOT).
        MESSAGE LOX_ROOT->GET_TEXT( ) TYPE LCL_APP=>MCS_MSG_TYPE-SUCCESS
          DISPLAY LIKE LCL_APP=>MCS_MSG_TYPE-ERROR.
        RETURN.
    ENDTRY.
  ENDMETHOD.
ENDCLASS.
```

## 04. ZCY_INC_2A_PAY_BLOCK_MAIN

```abap
*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*

* Objective                 : Hold SAP GST amount via JV               *

* Business Vertical         : <All Verticals JSW>                      *

* Plant/Location Specific   : All Plants                               *

* Department Name           : <Purchase/PPC/Store/FI>                  *

* Department Head Name/Email: < abc@xyz.com>                           *

* SAP Module                : <SD/MM/FI>                               *

* Technical Consultant      : Paresh Patel                             *

* Functional Consultant     : Pratik                                   *

* CR#                       : < S 9000054361 >                         *

* Object ID                 : < PPRP0XXX>                              *

* Creation Date             : <12/12/2025>                             *

*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*

* Change History                                                       *

*----------------------------------------------------------------------*

* Version |   Date    |  User    |   TR number   | Description         *

*----------------------------------------------------------------------*

*&---------------------------------------------------------------------*

*&  Include           ZCY_INC_2A_PAY_BLOCK_MAIN

*&---------------------------------------------------------------------*

*--------------------------------------------------------------------*

* Pre-Selection Screen Events

*--------------------------------------------------------------------*

load-of-program.

  " placeholder

initialization.

  lcl_app=>initialize_layout_variant( ).

*--------------------------------------------------------------------*

* Selection Screen Events

*--------------------------------------------------------------------*

at selection-screen on value-request for p_var.

  lcl_app=>layout_variant_selection( ).

at selection-screen output.

  lcl_app=>sel_screen_pbo( ).

at selection-screen.

  lcl_app=>sel_screen_pai( ).

  lcl_app=>check_layout_variant_existence( ).

*--------------------------------------------------------------------*

* Start Of Selection

*--------------------------------------------------------------------*

start-of-selection.

  lcl_main=>start( ).

*--------------------------------------------------------------------*

* End Of Selection

*--------------------------------------------------------------------*
```

## 05. ZCY_INC_2A_PAY_BLOCK_PBO_PAI

```abap
*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*

* Objective                 : Hold SAP GST amount via JV               *

* Business Vertical         : <All Verticals JSW>                      *

* Plant/Location Specific   : All Plants                               *

* Department Name           : <Purchase/PPC/Store/FI>                  *

* Department Head Name/Email: < abc@xyz.com>                           *

* SAP Module                : <SD/MM/FI>                               *

* Technical Consultant      : Paresh Patel                             *

* Functional Consultant     : Pratik                                   *

* CR#                       : < S 9000054361 >                         *

* Object ID                 : < PPRP0XXX>                              *

* Creation Date             : <12/12/2025>                             *

*&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&*

* Change History                                                       *

*----------------------------------------------------------------------*

* Version |   Date    |  User    |   TR number   | Description         *

*----------------------------------------------------------------------*

*&---------------------------------------------------------------------*

*&  Include           ZCY_INC_2A_PAY_BLOCK_PBO_PAI

*&---------------------------------------------------------------------*

*--------------------------------------------------------------------*

* Screen/Dynpro Events

*--------------------------------------------------------------------*

module pbo_100 output.

  " execute only once, when displaying alv for the first time

  if lcl_app=>mv_init eq abap_false.

    lcl_app=>get_instance( )->init_alv_screen( ).

    lcl_app=>mv_init = abap_true.

  endif.

endmodule.

module pai_100 input.

  data ok_code like sy-ucomm.

  lcl_app=>get_instance( )->process_user_command(

    exporting

      iv_ucomm = ok_code ).

  clear ok_code.

endmodule.

module exit input.

  lcl_app=>get_instance( )->exit( ).

endmodule.
```
