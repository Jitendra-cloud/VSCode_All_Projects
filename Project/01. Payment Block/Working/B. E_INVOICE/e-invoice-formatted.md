# E-Invoice ABAP Class --- `ZCL_INVOICE`

## Overview

This document contains the ABAP source supplied in `e-invoice.txt`,
formatted as Markdown for easier reading and reference. The source code
is preserved; no business logic has been intentionally changed.

## 1. Class Definition

``` abap
CLASS zcl_invoice DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC .

  PUBLIC SECTION.

    INTERFACES if_rap_query_provider .

    TYPES:
      BEGIN OF ty_error_detail,
        code    TYPE string,
        message TYPE string,
        type    TYPE string,
      END OF ty_error_detail .
    TYPES:
      ty_t_error_detail TYPE STANDARD TABLE OF ty_error_detail WITH EMPTY KEY .
    TYPES:
      BEGIN OF ty_range_option,
        sign   TYPE c LENGTH 1,
        option TYPE c LENGTH 2,
        low    TYPE string,
        high   TYPE string,
      END OF ty_range_option .
    TYPES:
      tt_range_option TYPE STANDARD TABLE OF ty_range_option WITH EMPTY KEY .
    TYPES:
      BEGIN OF ty_name_range_pairs,
        name  TYPE string,
        range TYPE tt_range_option,
      END OF ty_name_range_pairs .

    TYPES:
      BEGIN OF ty_challan,
*        documentvalue   TYPE string,
        pos             TYPE zde_pos,
        hsn             TYPE zde_hsn,
        ProductCode     TYPE mseg-matnr,
        itemname        TYPE zde_itemname,
        itemdescription TYPE zde_item_description,
        uqc             TYPE zde_uqc,
        Quantity        TYPE vbrp-meins,
        Rate            TYPE zde_rate11,
        grossamount     TYPE string.
*        taxablevalue    TYPE string,
*        igstamount      TYPE string,
*        cgstamount      TYPE string,
*        sgstamount      TYPE string.
        INCLUDE    TYPE zmm_c_challan.
    TYPES : END OF ty_challan .
    TYPES:
      tt_display_challan TYPE STANDARD TABLE OF ty_challan .
    TYPES:
      BEGIN OF ty_einv,
        pushStatus       TYPE string,
        ackNumber        TYPE string,
        ackDate          TYPE string,
        irn              TYPE string,
        signedInvoice    TYPE string,
        signedQRCode     TYPE string,
        qrCode           TYPE string,
        eInvoiceProvider TYPE string,
        errors           TYPE ty_t_error_detail,
      END OF ty_einv .
    TYPES:
      BEGIN OF ty_vbrk,
        vbeln TYPE vbrk-vbeln,
        fkart TYPE vbrk-fkart,
        vtweg TYPE vbrk-vtweg,
        erdat TYPE vbrk-erdat,
        vbtyp TYPE vbrk-vbtyp,
        fkdat TYPE vbrk-fkdat,
        waerk TYPE vbrk-waerk,
        kunag TYPE vbrk-kunag,
        knumv TYPE vbrk-knumv,
        mwsbk TYPE vbrk-mwsbk,
        netwr TYPE vbrk-netwr,
        bupla TYPE vbrk-bupla,
        bukrs TYPE vbrk-bukrs,
        belnr TYPE vbrk-belnr,
        xblnr TYPE vbrk-xblnr,
        gjahr TYPE vbrk-gjahr,
        kurrf TYPE vbrk-kurrf,
        land1 TYPE vbrk-land1,
        sfakn TYPE vbrk-sfakn,
        ernam TYPE vbrk-ernam,
        awkey TYPE bkpf-awkey,

      END OF ty_vbrk .
    TYPES:
      BEGIN OF ty_vbrk_create,
        BillingDocument     TYPE zsd_t_einv_log-document_number,
        BillingDocumentDate TYPE datum,
        CompanyCode         TYPE bukrs,
        fiscalyear          TYPE gjahr,
      END OF ty_vbrk_create .
    TYPES:
      tt_vbrk1 TYPE STANDARD TABLE OF ty_vbrk .
    TYPES:
      BEGIN OF ty_vbrp,
        vbeln TYPE vbrp-vbeln,
        posnr TYPE vbrp-posnr,
        meins TYPE vbrp-meins,
        vgbel TYPE vbrp-vgbel,
        aubel TYPE vbrp-aubel,
        matnr TYPE vbrp-matnr,
        fkimg TYPE vbrp-fkimg,
        vrkme TYPE vbrp-vrkme,
        netwr TYPE vbrp-netwr,
        charg TYPE vbrp-charg,
        werks TYPE vbrp-werks,
        arktx TYPE vbrp-arktx,
        aupos TYPE vbrp-aupos,
        mwsbp TYPE vbrp-mwsbp,
        kzwi5 TYPE vbrp-kzwi5,
        umvkn TYPE vbrp-umvkn,
        umvkz TYPE vbrp-umvkz,
        kzwi2 TYPE vbrp-kzwi2,
        lgort TYPE vbrp-lgort,
        vstel TYPE vbrp-vstel,
        gsber TYPE vbrp-gsber,
        prctr TYPE vbrp-prctr,
      END OF ty_vbrp .
    TYPES:
      tt_vbrp1 TYPE STANDARD TABLE OF ty_vbrp .
    TYPES:
      BEGIN OF ty_vbak,
        vbeln TYPE vbeln,
        bstnk TYPE bstnk,
        bstdk TYPE bstdk,
      END OF ty_vbak .
    TYPES:
      BEGIN OF ty_vbfa,
        vbelv   TYPE vbeln_von,
        vbeln   TYPE vbeln_nach,
        vbtyp_n TYPE vbtyp_n,
      END OF ty_vbfa .
    TYPES:
      BEGIN OF ty_vbfa1,
        vbeln   TYPE vbeln_nach,
        vbelv   TYPE vbeln_von,
        vbtyp_n TYPE vbtyp_n,
        vbtyp_v TYPE vbtyp_v,
      END OF ty_vbfa1 .
    TYPES:
      tt_vbfa1 TYPE STANDARD TABLE OF ty_vbfa1 .
    TYPES:
      BEGIN OF ty_vbpa,
        vbeln TYPE vbeln,
        posnr TYPE posnr,
        parvw TYPE parvw,
        kunnr TYPE kunnr,
        adrnr TYPE adrnr,
      END OF ty_vbpa .
    TYPES:
      tt_vbpa1 TYPE STANDARD TABLE OF ty_vbpa .
    TYPES:
      BEGIN OF ty_adrc,
        addrnumber TYPE ad_addrnum,
        name1      TYPE ad_name1,
        name2      TYPE ad_name2,
        name3      TYPE ad_name3,
        name4      TYPE ad_name4,
        strsuppl1  TYPE ad_strspp1,
        strsuppl2  TYPE ad_strspp2,
        strsuppl3  TYPE ad_strspp3,
        location   TYPE ad_lctn,
        street     TYPE ad_street,
        city1      TYPE ad_city1,
        country    TYPE land1,
        region     TYPE regio,
        postcode1  TYPE ad_pstcd1,
      END OF ty_adrc .
    TYPES:
      tt_adrc1 TYPE STANDARD TABLE OF ty_adrc .
    TYPES:
      BEGIN OF ty_kna1,
        kunnr TYPE kunnr,
        land1 TYPE land1,
        name1 TYPE name1,
        name2 TYPE name2,
        adrnr TYPE adrnr,
        stcd3 TYPE stcd3,
        regio TYPE kna1-regio,
        stras TYPE stras,
        ort01 TYPE ort01,
        pstlz TYPE pstlz,
      END OF  ty_kna1 .
    TYPES:
      tt_kna1z TYPE STANDARD TABLE OF ty_kna1 .
    TYPES:
      BEGIN OF ty_marc,
        matnr TYPE matnr,
        werks TYPE werks_d,
        steuc TYPE steuc,
      END OF ty_marc .
    TYPES:
      tt_marc1 TYPE STANDARD TABLE OF ty_marc .
    TYPES:
      BEGIN OF ty_makt,
        matnr TYPE matnr,
        maktx TYPE maktx,
      END OF ty_makt .
    TYPES:
      tt_makt1 TYPE STANDARD TABLE OF ty_makt .
    TYPES:
      BEGIN  OF ty_j_1bbranch,
        name   TYPE name,
        gstin  TYPE j_1bbranch-gstin,
        adrnr  TYPE j_1bbranch-adrnr,
        branch TYPE j_1bbranch-branch,
        bukrs  TYPE bukrs,
      END OF ty_j_1bbranch .
    TYPES:
      tt_j_1bbranch TYPE STANDARD TABLE OF ty_j_1bbranch .
    TYPES:
      BEGIN OF ty_final,
        inv_type_code            TYPE string, "Supply type code
        inv_subtyp_code          TYPE string, "Document_type_code
        inv_num                  TYPE string, "Document_Num
        fi_inv_num               TYPE string, "FI Document_Num
        inv_date                 TYPE string, "Document_Date
        irn                      TYPE string, "Document_Date
        irn_success              TYPE string, "IRN Success
        irn_cancel               TYPE string, "IRN Cancel
        invoice_currency_code    TYPE string, "Additional_currency_code
        reversecharge            TYPE string, "Reverse_charge
        igst_applicability       TYPE string, "IgstOnIntra
        inv_start_date           TYPE string, "
        inv_end_date             TYPE string, "
        pre_inv_num              TYPE string, "Preceding_Document_ Number
        pre_inv_date             TYPE string, "Preceding_Document_ Date
        supplier_legal_name      TYPE string, "Supplier_Legal_Name
        supplier_gstin           TYPE string, "Supplier_GSTIN
        supplier_address1        TYPE string, "Supplier_Address1
        supplier_address2        TYPE string, "Supplier_Address2
        supplier_city            TYPE string, "Supplier_Place
        supplier_state           TYPE string, "Supplier_State
        supplier_pincode         TYPE string, "Supplier_Pincode
        billing_name             TYPE string, "Buyer_Name
        billing_gstin            TYPE string, "Buyer_GSTIN
        billing_pos              TYPE string, "POS
        billing_address1         TYPE string, "Buyer_Address1
        billing_address2         TYPE string, "Buyer_Address2
        billing_place            TYPE string, "Buyer_Place
        billing_state            TYPE string, "Buyer_State
        billing_pincode          TYPE string, "Buyer_Pincode
        country_of_export        TYPE string, "Country_of_Export
        shippingto_name          TYPE string, "Shipping To Legal Name
        shippingto_gstin         TYPE string, "Shipping To GSTIN
        shippingto_address1      TYPE string, "Shipping To  Address1
        shippingto_address2      TYPE string, "Shipping To  Address2
        shippingto_place         TYPE string, "Shipping To  Place
        shippingto_pincode       TYPE string, "Shipping To  PIN code
        shippingto_state         TYPE string, "Shipping To_ State Code
        slno                     TYPE string, "Serial Number
        item_description         TYPE string, "Item description
        isservice                TYPE string, "Service
        hsncode                  TYPE string, "HSN code
        batch_num                TYPE string, "Batch_Name
        batchexpirydate          TYPE vfdat, "Batch_Expiry_ Date
        quantity1                TYPE bseg-menge, "Quantity
        freeqty                  TYPE string, "Free  quantity
        uqc                      TYPE string, "UOM
        rate                     TYPE bseg-dmbtr, "Item Rate per unit quantity
        grossamount              TYPE bseg-dmbtr, "Gross  amount
        discountamount           TYPE bseg-dmbtr, "Discount amount
        gst_rate                 TYPE string, "GST rate
        iamt                     TYPE bseg-dmbtr, "IGST Amount as per item
        camt                     TYPE bseg-dmbtr, "CGST Amount as per item
        samt                     TYPE bseg-dmbtr, "SGST/UTGST Amount as per item
        itemtotal                TYPE bseg-dmbtr, "Bill Total Details
        taxable_value            TYPE bseg-dmbtr, "Taxable_value
        igstvalue                TYPE bseg-dmbtr, "IGST Amount as per invoice
        cgstvalue                TYPE bseg-dmbtr, "CGST Amount as per invoice
        sgstvalue                TYPE bseg-dmbtr, "SGST/UTGST Amount as per invoice
        roundoff                 TYPE bseg-dmbtr,
        tot_inv_value_inr        TYPE bseg-dmbtr, "Total_Invoice_Value_INR
        total_invoice_value_fcnr TYPE bseg-dmbtr, "Total_Invoice_Value_FCNR
        tax_scheme               TYPE string, "Tax_Scheme
        total_taxable_value      TYPE bseg-dmbtr, "Total Taxable Value
        item_total_amount        TYPE bseg-dmbtr,
        dncc                     TYPE string,
        tcs                      TYPE string,
        gl_account               TYPE string,
        lgort                    TYPE string,
        vstel                    TYPE string,
        bupla                    TYPE string,
        item_name                TYPE string,
        currency                 TYPE string,
        countrycode              TYPE string,
        cancel_by                TYPE string,
        cancel_date              TYPE string,
        fail                     TYPE string,
        error                    TYPE string,
        ewaybill                 TYPE string,
        custom1                  TYPE string,
        custom2                  TYPE string,
        custom3                  TYPE string,
        custom4                  TYPE string,
        custom5                  TYPE string,
        custom6                  TYPE string,
        custom7                  TYPE string,
        custom8                  TYPE string,
        custom9                  TYPE string,
        custom10                 TYPE string,
        custom11                 TYPE string,
        custom12                 TYPE string,
        custom13                 TYPE string,
        custom14                 TYPE string,
        branch                   TYPE vbrp-werks,
        bukrs                    TYPE vbrk-bukrs,
      END OF ty_final .
    TYPES:
      tt_final TYPE STANDARD TABLE OF ty_final .
    TYPES:
      BEGIN OF ty_output,
        matnr           TYPE j_1iexcdtl-matnr,
        menge           TYPE j_1iexcdtl-menge,
        cgst_rt         TYPE konv-kbetr,
        cgst            TYPE konv-kwert,
        sugst_rt        TYPE konv-kbetr,
        sugst           TYPE konv-kwert,
        igst_rt         TYPE konv-kbetr,
        igst            TYPE konv-kwert,
        vbeln           TYPE vbeln,
        posnr           TYPE posnr,
        werks           TYPE werks_d,
        fkimg           TYPE fkimg,
        vrkme           TYPE vrkme,
        mwsbp           TYPE mwsbp,
        netwr           TYPE netwr,
        netwr_itemlevel TYPE netwr,
        vgbel           TYPE vgbel,
        lgort           TYPE vbrp-lgort,
        vstel           TYPE vbrp-vstel,
        mail            TYPE  adr6-smtp_addr,
      END OF ty_output .
    TYPES:
      tt_output TYPE STANDARD TABLE OF ty_output .
    TYPES:
      BEGIN OF ty_tvarvc,
        sign   TYPE tvarvc-sign,
        option TYPE tvarvc-opti,
        low    TYPE tvarvc-low,
        high   TYPE tvarvc-high,
      END OF ty_tvarvc .
    TYPES:
      tt_tvarvc TYPE STANDARD TABLE OF ty_tvarvc .
    TYPES:
      BEGIN OF ty_marm,
        matnr TYPE matnr,
        meinh TYPE meinh,
        umrez TYPE umrez,
        umren TYPE umren,
      END OF ty_marm .
    TYPES:
      tt_marm1 TYPE STANDARD TABLE OF ty_marm .
    TYPES:
      BEGIN OF ty_likp,
        vbeln TYPE vbeln,
        vstel TYPE vstel,
        lgnum TYPE lgnum,
      END OF ty_likp .
    TYPES:
      tt_likp1 TYPE STANDARD TABLE OF ty_likp .
    TYPES:
      BEGIN OF ty_konv,
        knumv   TYPE konv-knumv,
        kposn   TYPE konv-kposn,
        kschl   TYPE konv-kschl,
        kawrt   TYPE konv-kawrt,
        kbetr   TYPE konv-kbetr,
        kwert   TYPE konv-kwert,
        sakn1   TYPE konv-sakn1,
        kvsl1   TYPE konv-kvsl1,
        gsp_tax TYPE char10,
      END OF ty_konv .
    TYPES:
      tt_konv1 TYPE STANDARD TABLE OF ty_konv .
    TYPES:
      BEGIN OF ty_bkpf_sd,
        bukrs     TYPE  bkpf-bukrs,
        belnr     TYPE  bkpf-belnr,
        gjahr     TYPE  bkpf-gjahr,
        awkey     TYPE bkpf-awkey,
        xblnr_alt TYPE bkpf-xblnr_alt,
      END OF ty_bkpf_sd .
    TYPES:
      tt_bkpf_sd TYPE STANDARD TABLE OF ty_bkpf_sd .
    TYPES:
      BEGIN OF ty_bseg_sd,
        bukrs   TYPE  bseg-bukrs,
        belnr   TYPE  bseg-belnr,
        gjahr   TYPE  bseg-gjahr,
        segment TYPE  bseg-segment,
      END OF ty_bseg_sd .
    TYPES:
      tt_bseg_sd TYPE STANDARD TABLE OF ty_bseg_sd .
    TYPES:
      tt_cond TYPE STANDARD TABLE OF zsd_c_condition_type .
    TYPES:
      tt_uom TYPE STANDARD TABLE OF zsd_c_einv_uom .
    TYPES:
      tt_zeinv_billtype TYPE STANDARD TABLE OF zsd_c_billtype .
    TYPES:
      tt_zcyg_log TYPE STANDARD TABLE OF zsd_t_einv_log .
    TYPES:
      tt_sadr TYPE STANDARD TABLE OF zsd_c_supply_detail .
    TYPES:
      tt_final_global TYPE STANDARD TABLE OF zsd_s_global_st .
    TYPES:
      tt_display TYPE STANDARD TABLE OF zsd_c_einv .
    TYPES:
      tt_lines TYPE STANDARD TABLE OF tline .
    TYPES:
      BEGIN OF ty_vehicle_details,
        transporter_id      TYPE zde_transporter_id,
        transporter_name    TYPE zde_transporter_name,
        transport_mode      TYPE zde_tranport_mode,
        transportdocdate    TYPE string,
        transporter_doc_num TYPE zde_transporter_doc_num,
        vehicle_number      TYPE zde_vehicle_number,
        vehicle_type        TYPE zde_vehicle_type,
      END OF ty_vehicle_details .
    TYPES:
      BEGIN OF ty_final_inward,
        branch                     TYPE j_1bbranch-branch,
        documentnumber             TYPE  char16,
        locationgstin              TYPE  char15,
        rate                       TYPE  char20,
        itemname                   TYPE  char100,
        customitem1                TYPE  char40,
        custom1                    TYPE  char200,
        hsn                        TYPE  char20,
        billfromlegalname          TYPE  char100,
        locationlegalname          TYPE  char200,
        returnperiod               TYPE  char15,
        purpose                    TYPE  char12,
        supplytype                 TYPE  char20,
        irn                        TYPE  char64,
        documenttype               TYPE  char20,
        transactiontype            TYPE  char20,
        autopushorgenerate         TYPE char20,
        documentseriescode         TYPE char10,
        dtatransaction             TYPE char10,
        transactiontypedescription TYPE  char20,
        documentdate               TYPE  char20,
        referencedocumentnumber    TYPE  char16,
        referencedocumentdate      TYPE  char10,
        transactionmode            TYPE  char20,
        billfromgstin              TYPE  char15,
        billfromtradename          TYPE  char100,
        billfrombuildingnumber     TYPE  char50,
        billfrombuildingname       TYPE  char50,
        billfromflatnumber         TYPE  char50,
        billfromaddress1           TYPE  char120,
        billfromaddress2           TYPE  char120,
        billfromcity               TYPE  char50,
        billfromdistrict           TYPE  char50,
        billfromstatecode          TYPE  char20,
        billfrompincode            TYPE  char20,
        billfromphone              TYPE  char10,
        billfromemail              TYPE  char50,
        dispatchfromlegalname      TYPE  char100,
        dispatchfromgstin          TYPE  char20,
        dispatchfromtradename      TYPE  char100,
        dispatchfrombuildingnumber TYPE  char50,
        dispatchfrombuildingname   TYPE  char50,
        dispatchfromflatnumber     TYPE  char50,
        dispatchfromaddress1       TYPE  char120,
        dispatchfromaddress2       TYPE  char120,
        dispatchfromcity           TYPE  char50,
        dispatchfromdistrict       TYPE  char50,
        dispatchfromstatecode      TYPE  char20,
        dispatchfrompincode        TYPE  char20,
        dispatchfromphone          TYPE  char10,
        dispatchfromemail          TYPE  char50,
        billtolegalname            TYPE  char100,
        billtogstin                TYPE  char15,
        billtotradename            TYPE  char100,
        billtobuildingnumber       TYPE  char50,
        billtobuildingname         TYPE  char50,
        billtoflatnumber           TYPE  char50,
        billtoaddress1             TYPE  char120,
        billtoaddress2             TYPE  char120,
        billtocity                 TYPE  char50,
        billtodistrict             TYPE  char50,
        billtostatecode            TYPE  char20,
        billtopincode              TYPE  char20,
        billtophone                TYPE  char10,
        billtoemail                TYPE  char50,
        shiptolegalname            TYPE  char100,
        shiptogstin                TYPE  char15,
        shiptotradename            TYPE  char100,
        shiptobuildingnumber       TYPE  char50,
        shiptobuildingname         TYPE  char50,
        shiptoflatnumber           TYPE  char50,
        shiptoaddress1             TYPE  char120,
        shiptoaddress2             TYPE  char120,
        shiptocity                 TYPE  char50,
        shiptodistrict             TYPE  char50,
        shiptostatecode            TYPE  char20,
        shiptopincode              TYPE  char20,
        shiptophone                TYPE  char10,
        shiptoemail                TYPE  char50,
        paymenttype                TYPE  char20,
        paymentmode                TYPE  char20,
        paymentamount              TYPE  char20,
        paymentdate                TYPE  char10,
        paymentremarks             TYPE  char250,
        paymentterms               TYPE  char200,
        paymentinstruction         TYPE  char200,
        payeename                  TYPE  char100,
        payeeaccountnumber         TYPE  char18,
        paymentamountdue           TYPE  char20,
        paymentduedate             TYPE  char10,
        ifsc                       TYPE  char11,
        accountdetails             TYPE  char50,
        credittransfer             TYPE  char50,
        directdebit                TYPE  char50,
        creditdays                 TYPE  char20,
        creditavaileddate          TYPE  char10,
        creditreversaldate         TYPE  char10,
        documentremarks            TYPE  char100,
        documentdeliverytstartdate TYPE  char50,
        documentdeliveryenddate    TYPE  char50,
        receiptadvicereference     TYPE  char20,
        tenderlotreference         TYPE  char20,
        contractreferencenumber    TYPE  char20,
        externalreference          TYPE  char20,
        projectreferencenumber     TYPE  char20,
        poreferencenumber          TYPE  char20,
        billnumber                 TYPE  char16,
        billdate                   TYPE  char10,
        portcode                   TYPE  char10,
        documentcurrencycode       TYPE  char20,
        documentcurrencyvalue      TYPE  char20,
        origincountry              TYPE  char20,
        pos                        TYPE  char3,
        documentvalue              TYPE  netwr,
        differentialpercentage     TYPE  char20,
        reversecharge              TYPE  char20,
        claimrefund                TYPE  char20,
        underigstact               TYPE  char20,
        refundeligibility          TYPE  char20,
        ecommercegstin             TYPE  char15,
        ecommercepos               TYPE  char20,
        originalgstin              TYPE  char15,
        originaltradename          TYPE  char100,
        originaldocumentnumber     TYPE  char20,
        originaldocumentdate       TYPE  char20,
        originalportcode           TYPE  char20,
        originalecommercegstin     TYPE  char20,
        transporterid              TYPE  char20,
        transportername            TYPE  char100,
        transportdate              TYPE  char20,
        transporttime              TYPE  char20,
        distancelevel              TYPE  char20,
        transportmode              TYPE  char20,
        transportdocumentnumber    TYPE  char20,
        transportdocumentdate      TYPE  char20,
        vehiclenumber              TYPE  char15,
        vehicletype                TYPE  char20,
        toemailaddresses           TYPE  char300,
        tomobilenumbers            TYPE  char50,
        custom2                    TYPE  char40,
        custom3                    TYPE  char40,
        custom4                    TYPE  char40,
        custom5                    TYPE  char40,
        custom6                    TYPE  char40,
        custom7                    TYPE  char40,
        custom8                    TYPE  char40,
        custom9                    TYPE  char40,
        custom10                   TYPE  char200,
        itemdescription            TYPE  char30,
        barcode                    TYPE  char30,
        uqc                        TYPE  char3,
        quantity                   TYPE  char20,
        freequantity               TYPE  char20,
        cessrate                   TYPE  char20,
        statecessrate              TYPE  char20,
        cessnonadvaloremrate       TYPE char20,
        priceperquantity           TYPE  netwr,
        discountamount             TYPE  char20,
        grossamount                TYPE  char20,
        othercharges               TYPE  char20,
        taxablevalue               TYPE  netwr,
        assessablevalue            TYPE  char20,
        igstamount                 TYPE  netwr,
        cgstamount                 TYPE  netwr,
        sgstamount                 TYPE  netwr,
        cessamount                 TYPE  netwr,
        statecessamount            TYPE  char20,
        cessnonadvaloremamount     TYPE char20,
      END OF ty_final_inward .
    TYPES:
      tt_create       TYPE TABLE FOR CREATE zsd_c_einv_main .
    TYPES:
      tt_final_inward TYPE TABLE OF zsd_s_global_st .

    CLASS-DATA gt_update_einv TYPE tt_zcyg_log .

    DATA: lr_xblnr TYPE RANGE OF ZSD_C_bkpf-Xblnr,
          lr_bukrs TYPE RANGE OF ZSD_C_bkpf-Bukrs,
          lr_gjahr TYPE RANGE OF ZSD_C_bkpf-Gjahr.
    DATA: iv_date  TYPE RANGE OF mkpf-bldat .
    DATA: iv_doc   TYPE RANGE OF mkpf-mblnr .
    DATA: iv_doc_s TYPE RANGE OF zsd_t_einv-documentnumber,
          iv_doc_B TYPE RANGE OF zsd_t_einv-custom1.
    DATA: iv_docdate TYPE RANGE OF zsd_t_einv-documentdate .
    DATA: iv_mjahr TYPE RANGE OF mkpf-mjahr .

    METHODS read_text
      IMPORTING
        !iv_id          TYPE thead-tdid
        !iv_language    TYPE thead-tdspras
        !iv_name        TYPE thead-tdname
        !iv_object      TYPE thead-tdobject
      RETURNING
        VALUE(rt_lines) TYPE tlinetab .
    METHODS generate_json_structure
      IMPORTING
        !it_final         TYPE tt_final_global
        !iv_module        TYPE char2 OPTIONAL
        !iv_button        TYPE char10 OPTIONAL
      EXPORTING
        !lt_display       TYPE tt_display
      RETURNING
        VALUE(rv_request) TYPE string .
*        !iv_billingdocument TYPE vbeln
    METHODS get_data_for_sd
      IMPORTING
        !iv_billingdocument     LIKE iv_doc_s OPTIONAL
        !iv_billingREFERENCE    LIKE iv_doc_b OPTIONAL
        !iv_billingdocumentdate LIKE iv_docdate OPTIONAL
      EXPORTING
        !et_final               TYPE tt_final .
    METHODS set_data_for_sd
      IMPORTING
        !it_fkart          TYPE tt_tvarvc OPTIONAL
        !it_pac            TYPE tt_tvarvc OPTIONAL
        !it_cond           TYPE tt_cond
      EXPORTING
        !et_final          TYPE tt_final
      CHANGING
        !ct_exp            TYPE tt_tvarvc
        !ct_vtweg          TYPE tt_tvarvc
        !ct_vbrk           TYPE tt_vbrk1 OPTIONAL
        !ct_vbrp           TYPE tt_vbrp1 OPTIONAL
        !ct_konv           TYPE tt_konv1 OPTIONAL
        !ct_marm           TYPE tt_marm1 OPTIONAL
        !ct_uom            TYPE tt_uom OPTIONAL
        !ct_konv_gl        TYPE tt_konv1 OPTIONAL
        !ct_likp           TYPE tt_likp1 OPTIONAL
        !ct_vbfa1          TYPE tt_vbfa1 OPTIONAL
        !ct_sadr           TYPE tt_sadr OPTIONAL
        !ct_adrc1          TYPE tt_adrc1 OPTIONAL
        !ct_j_1bbranch     TYPE tt_j_1bbranch OPTIONAL
        !ct_vbpa           TYPE tt_vbpa1 OPTIONAL
        !ct_kna1           TYPE tt_kna1z OPTIONAL
        !ct_adrc           TYPE tt_adrc1 OPTIONAL
        !ct_makt           TYPE tt_makt1 OPTIONAL
        !ct_marc           TYPE tt_marc1 OPTIONAL
        !ct_bkpf_sd        TYPE tt_bkpf_sd
        !ct_bseg_sd        TYPE tt_bseg_sd
        !ct_zeinv_billtype TYPE tt_zeinv_billtype .
    METHODS cyg_conversion
      IMPORTING
        !iv_purpose       TYPE string
        !iv_button        TYPE char10 OPTIONAL
      EXPORTING
        !et_final         TYPE tt_final_global
        !lt_display       TYPE tt_display
      CHANGING
        !ct_final         TYPE tt_final
      RETURNING
        VALUE(rv_request) TYPE string .

    TYPES:
      BEGIN OF ty_billing_keys,
        bukrs TYPE bkpf-bukrs,
        xblnr TYPE bkpf-xblnr,
        gjahr TYPE bkpf-gjahr,
      END OF ty_billing_keys .
    TYPES:
      tt_billing_keys TYPE STANDARD TABLE OF ty_billing_keys
                       WITH EMPTY KEY.
    METHODS get_data_for_fi
      IMPORTING
*        !is_billing_header TYPE tt_billing_keys OPTIONAL
        ir_doc      LIKE iv_doc_B OPTIONAL
        ir_xblnr    LIKE lr_xblnr OPTIONAL
        ir_bukrs    LIKE lr_bukrs OPTIONAL
        ir_gjahr    LIKE lr_gjahr OPTIONAL
        ir_doc_date LIKE iv_date OPTIONAL
        !iv_purpose TYPE char10
      EXPORTING
        !lt_display TYPE tt_display
        !et_final   TYPE tt_final_global .
    METHODS call_cpi
      IMPORTING
        !iv_token        TYPE string OPTIONAL
        !iv_url          TYPE string OPTIONAL
        !iv_req_payload  TYPE string OPTIONAL
        !iv_action       TYPE string
        !iv_reference_id TYPE string OPTIONAL
      EXPORTING
        !lv_pdf_xtsring  TYPE xstring
        !ev_response     TYPE string .
    METHODS get_data_for_challan
      IMPORTING
        !iv_billing_document LIKE iv_doc OPTIONAL
        !iv_gjahr            LIKE iv_mjahr OPTIONAL
        !iv_bukrs            TYPE bukrs OPTIONAL
        !iv_document_date    LIKE iv_date OPTIONAL
        !lt_transport_p      TYPE ANY TABLE OPTIONAL
      EXPORTING
        !et_display_table    TYPE tt_display_challan
        !lv_request          TYPE string .
    METHODS get_data_eway_generate_json
      IMPORTING
        !iv_document_number    TYPE xblnr OPTIONAL
        !is_vehicle_details    TYPE ty_vehicle_details
        !iv_module             TYPE char2 OPTIONAL
      RETURNING
        VALUE(rv_request_json) TYPE string .
    METHODS fill_eml_structure
      IMPORTING
        !is_vbrk_create TYPE ty_vbrk_create
        !is_final       TYPE zsd_s_global_st
        !is_irn_det     TYPE ty_einv
        !iv_module      TYPE char2
      EXPORTING
        !et_create      TYPE tt_create .
    METHODS generate_ewaybill .
    METHODS get_partb_eway_json
      IMPORTING
        !iv_document_number    TYPE xblnr
        !is_vehicle_details    TYPE ty_vehicle_details
      RETURNING
        VALUE(rv_request_json) TYPE string .
    METHODS get_data_for_gstr2
      IMPORTING
        !iv_billing_document      TYPE belnr_d OPTIONAL
        !it_billing_document_date TYPE tt_range_option OPTIONAL
      EXPORTING
        !et_gtr2_data             TYPE tt_final_inward .
    METHODS get_data_for_sd_mass
      IMPORTING
        !iv_billingdocument  TYPE vbeln OPTIONAL
        !it_billing_document TYPE tt_range_option
      EXPORTING
        !et_final            TYPE tt_final
        !et_global           TYPE tt_final_global .
    METHODS upload_excel
      IMPORTING
        !it_fkart TYPE tt_tvarvc OPTIONAL
      EXPORTING
        !et_final TYPE tt_final .
    METHODS get_data_for_gstr2_new .
  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.
```

## 2. Class Implementation

``` abap
CLASS zcl_invoice IMPLEMENTATION.

  METHOD cyg_conversion.

    DATA: LV_price TYPE char10.
    DATA: LT_einv_status TYPE STANDARD TABLE OF zeinv_status,
          WA_final1      TYPE zsd_s_global_st,
*       lt_display     type table of zsd_t_einv,
          WA_display     TYPE zsd_c_einv,
          LR_string      TYPE RANGE OF string.

    CONSTANTS: C_expwop TYPE char10 VALUE 'EXPWOP',
               C_b2c    TYPE char10 VALUE 'B2C',
               C_b2b1   TYPE char10 VALUE 'B2B',
               C_expwp  TYPE char10 VALUE 'EXPWP',
               C_legal  TYPE char100 VALUE 'PAGE INDUSTRIES LIMITED'.

    LR_string = VALUE #( ( sign = 'I' option = 'EQ' low = 'EXPWOP' )
                         ( sign = 'I' option = 'EQ' low = 'EXPWP' ) ).

    LOOP AT ct_final INTO DATA(WA_final).
      WA_final1-branch = WA_final-branch.
      LV_price = WA_final-grossamount / WA_final-quantity1.
      WA_final1-supplytype = 'S'.
      WA_final1-transactiontype = WA_final-inv_type_code.
      WA_final1-locationgstin = WA_final-supplier_gstin.
*   WA_final1-purpose = 'EINV'.
      WA_final1-purpose = iv_purpose.
      IF iv_purpose EQ 'EINV' OR iv_purpose EQ 'EINV|OGST'.
        WA_final1-autopushorgenerate = 'EINV'.
      ENDIF.
      WA_final1-cancelled_date = WA_final-cancel_date.
      WA_final1-documenttype = WA_final-inv_subtyp_code.
      WA_final1-documentnumber = WA_final-inv_num.
      WA_final1-documentdate = WA_final-inv_date.
      WA_final1-billfromgstin = WA_final-supplier_gstin.
      WA_final1-billfromlegalname = C_legal. "WA_final-supplier_legal_name.
      WA_final1-billfromaddress1 = WA_final-supplier_address1.
      WA_final1-billfromaddress2 = WA_final-supplier_address2.
      WA_final1-billfromcity = WA_final-supplier_city.
      WA_final1-billfrompincode = WA_final-supplier_pincode.
      WA_final1-billfromstatecode = WA_final-supplier_state.
      WA_final1-dispatchfromgstin = WA_final-supplier_gstin.
      WA_final1-dispatchfromtradename = C_legal.
      WA_final1-dispatchfromadd1 = WA_final-supplier_address1.
      WA_final1-dispatchfromadd2 = WA_final-supplier_address2.
      WA_final1-dispatchfromcity = WA_final-supplier_city.
      WA_final1-dispatchfrompincode = WA_final-supplier_pincode.
      WA_final1-dispatchfromstatecode = WA_final-supplier_state.
      WA_final1-billtolegalname = WA_final-billing_name.
      WA_final1-billtogstin = WA_final-billing_gstin.
      WA_final1-billtoaddress1 = WA_final-billing_address1.
      WA_final1-billtoaddress2 = WA_final-billing_address2.   "Change by Vivek 27.04.2026
      WA_final1-billtocity = WA_final-billing_place.
      WA_final1-billtopincode = WA_final-billing_pincode.
      WA_final1-billtostatecode = WA_final-billing_state.
*   IF WA_final-custom11 <> WA_final-custom13.
      WA_final1-shiptolegalname = WA_final-shippingto_name.
      WA_final1-shiptotradename = WA_final-shippingto_name.
      WA_final1-shiptogstin = WA_final-shippingto_gstin.
      WA_final1-shiptoaddress1 = WA_final-shippingto_address1.
      WA_final1-shiptoaddress2 = WA_final-shippingto_address2.
      WA_final1-shiptocity = WA_final-shippingto_place.
      WA_final1-shiptopincode = WA_final-shippingto_pincode.
      WA_final1-shiptostatecode = WA_final-shippingto_state.
*   ELSE.
*
*     WA_final1-shiptolegalname = ''.
*     WA_final1-shiptotradename = ''.
*     WA_final1-shiptogstin     = ''.
*     WA_final1-shiptoaddress1  = ''.
*     WA_final1-shiptoaddress2  = ''.
*     WA_final1-shiptocity      = ''.
*     WA_final1-shiptopincode   = ''.
*     WA_final1-shiptostatecode = ''.
*
*   ENDIF.
      WA_final1-serialnumber = WA_final-slno.
      WA_final1-destinationcountry = WA_final-countrycode.
      WA_final1-doccurrencycode = WA_final-currency.
      WA_final1-itemname = WA_final-item_name.
      WA_final1-itemdescription = WA_final-item_description.
      WA_final1-hsn = WA_final-hsncode.
      WA_final1-uqc = WA_final-uqc.
      WA_final1-quantity = WA_final-quantity1.
      WA_final1-documentvalue = WA_final-tot_inv_value_inr.
      CONDENSE WA_final1-documentvalue.
      WA_final1-rate = WA_final-gst_rate.
      WA_final1-itemtotal = WA_final-item_total_amount.
      WA_final1-cessnonadvaloremamount = ''.
      WA_final1-othercharges = ''.
      IF WA_final-iamt IS NOT INITIAL.
        WA_final1-igstamount = WA_final-iamt.
      ELSE.
        WA_final1-igstamount = WA_final-igstvalue.
      ENDIF.
      IF WA_final-camt IS NOT INITIAL.
        WA_final1-cgstamount = WA_final-camt.
      ELSE.
        WA_final1-cgstamount = WA_final-cgstvalue.
      ENDIF.
      IF WA_final-samt IS NOT INITIAL.
        WA_final1-sgstamount = WA_final-samt.
      ELSE.
        WA_final1-sgstamount = WA_final-sgstvalue.
      ENDIF.
      IF WA_final-taxable_value IS NOT INITIAL.
        WA_final1-taxablevalue = WA_final-taxable_value.
      ELSE..
        WA_final1-taxablevalue = WA_final-total_taxable_value.
      ENDIF.
      WA_final1-grossamount = WA_final-grossamount.
      WA_final1-cessrate = ''.
      WA_final1-cessnonadvaloremrate = ''.
      WA_final1-priceperquantity = LV_price.
      IF WA_final-billing_state IS NOT INITIAL.
        IF strlen( WA_final-billing_state ) > 2.
          WA_final1-pos = WA_final-billing_state+0(2).
        ELSE.
          WA_final1-pos = WA_final-billing_state.
        ENDIF.
      ENDIF.
      WA_final1-toemailaddress = WA_final-custom14.
      IF WA_final1-transactiontype NE C_expwop AND WA_final1-transactiontype NE C_expwp.
        IF WA_final1-billtogstin IS INITIAL.
          WA_final1-transactiontype = C_b2c.
        ELSE.
          WA_final1-transactiontype = C_b2b1.
        ENDIF.
      ENDIF.
      DATA(LV_sysid) = 'DQ4'.
      IF sy-sysid = 'DS4' OR sy-sysid = LV_sysid.
        IF WA_final1-billfromstatecode = '01'.
          WA_final1-billfromgstin = '01AACPH8447G002'.
          WA_final1-locationgstin = '01AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '02'.
          WA_final1-billfromgstin = '02AACPH8447G002'.
          WA_final1-locationgstin = '02AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '03'.
          WA_final1-billfromgstin = '03AACPH8447G002'.
          WA_final1-locationgstin = '03AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '04'.
          WA_final1-billfromgstin = '04AACPH8447G002'.
          WA_final1-locationgstin = '04AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '05'.
          WA_final1-billfromgstin = '05AACPH8447G002'.
          WA_final1-locationgstin = '05AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '06'.
          WA_final1-billfromgstin = '06AACPH8447G002'.
          WA_final1-locationgstin = '06AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '07'.
          WA_final1-billfromgstin = '07AACPH8447G002'.
          WA_final1-locationgstin = '07AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '08'.
          WA_final1-billfromgstin = '08AACPH8447G002'.
          WA_final1-locationgstin = '08AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '09'.
          WA_final1-billfromgstin = '09AACPH8447G012'.
          WA_final1-locationgstin = '09AACPH8447G012'.
        ELSEIF WA_final1-billfromstatecode = '10'.
          WA_final1-billfromgstin = '10AACPH8447G013'.
          WA_final1-locationgstin = '10AACPH8447G013'.
        ELSEIF WA_final1-billfromstatecode = '11'.
          WA_final1-billfromgstin = '11AACPH8447G014'.
          WA_final1-locationgstin = '11AACPH8447G014'.
        ELSEIF WA_final1-billfromstatecode = '12'.
          WA_final1-billfromgstin = '12AACPH8447G015'.
          WA_final1-locationgstin = '12AACPH8447G015'.
        ELSEIF WA_final1-billfromstatecode = '24'.
          WA_final1-billfromgstin = '24AACPH8447G002'.
          WA_final1-locationgstin = '24AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '27'.
          WA_final1-billfromgstin = '27AACPH8447G002'.
          WA_final1-locationgstin = '27AACPH8447G002'.
        ELSEIF WA_final1-billfromstatecode = '29'.
          WA_final1-billfromgstin = '29AACPH8447G000'.
          WA_final1-locationgstin = '29AACPH8447G000'.
        ELSEIF WA_final1-billfromstatecode = '19'.
          WA_final1-billfromgstin = '19AACPH8447G018'.
          WA_final1-locationgstin = '19AACPH8447G018'.
        ELSEIF WA_final1-billfromstatecode = '33'.
          WA_final1-billfromgstin = '33AACPH8447G016'.
          WA_final1-locationgstin = '33AACPH8447G016'.
        ELSEIF WA_final1-billfromstatecode = '37'.
          WA_final1-billfromgstin = '37AACPH8447G017'.
          WA_final1-locationgstin = '37AACPH8447G017'.
        ENDIF.
      ENDIF.
*      IF WA_final1-billfromaddress1 IS INITIAL.
*        WA_final1-billfromaddress1 = 'DUMMY'.
*      ENDIF.
      WA_final1-company_code = WA_final-bukrs.
      WA_final1-customitem9 = WA_final-custom9.     "WA_final-cgstvalue + WA_final-sgstvalue + WA_final-igstvalue.
      WA_final1-customitem10 = WA_final-vstel.
      WA_final1-customitem6 = WA_final-custom11.
      WA_final1-customitem5 = WA_final-custom12.
      WA_final1-customitem7 = WA_final-custom13.
      WA_final1-customitem1 = WA_final-cgstvalue + WA_final-sgstvalue + WA_final-igstvalue.
      WA_final1-company_code = WA_final-bukrs.
      WA_final1-custom1 = WA_final-custom1.
      WA_final1-custom2 = WA_final-custom2.
      WA_final1-custom3 = WA_final-gl_account.
      WA_final1-custom4 = WA_final-custom4.
      WA_final1-custom5 = WA_final-custom5.
      WA_final1-customitem8 = WA_final-custom10.
      APPEND WA_final1 TO et_final.
      CLEAR: WA_final,WA_final1,LV_price.
    ENDLOOP.

    IF iv_button <> 'GO'.
      rv_request = generate_json_structure( EXPORTING iv_module = 'SD'
                                                      iv_button = iv_button
                                                      it_final = et_final ).
    ENDIF.

* DATA : LR_doc TYPE RANGE OF zsd_c_einv-Documentnumber.
*
* LR_doc = VALUE #( FOR WA_doc IN et_final
*                  ( sign = 'I' option = 'EQ' low = |{ WA_doc-documentnumber ALPHA = IN }| ) ).
*
* SELECT * FROM zsd_c_billing_document
* WHERE BillingDocument IN @LR_doc
* INTO TABLE @DATA(LT_global).
*
* LOOP AT et_final INTO DATA(WA_temp).
*
*   DATA(LV_doc) = COND #( WHEN WA_temp-documentnumber(1) CO '123456789'
*                          THEN |{ '00' }{ WA_temp-documentnumber }|
*                          ELSE WA_temp-documentnumber ).
*
*   READ TABLE LT_global INTO DATA(WA_global) WITH KEY BillingDocument = LV_doc.
*   IF sy-subrc = 0.
*
*     MOVE-CORRESPONDING WA_temp TO WA_display.
*
*     WA_display-irn_status = COND string(
*                            WHEN WA_global-Cancel = 'X' THEN 'IRN Cancelled'
*                            WHEN WA_global-Fail = 'X' THEN 'ERROR !'
*                            WHEN WA_global-Irn IS INITIAL THEN 'IRN Pending'
*                            ELSE 'IRN Generated' ).
*
*     WA_display-irn_icon = COND i(
*                            WHEN WA_global-cancel = 'X' THEN 1
*                            WHEN WA_global-fail = 'X' THEN 1
*                            WHEN WA_global-irn IS INITIAL THEN 2
*                            ELSE 3 ).
*
*     WA_display-Irn = WA_global-Irn.
*     WA_display-Acknumber = WA_global-AckowlegementNumber.
*     WA_display-Ackdate = WA_global-AckDate.
*     WA_display-ErrorDescription = WA_global-EinvError.
*   ELSE.
*
*     WA_display-irn_status = 'IRN Pending'.
*     WA_display-irn_icon = 2.
*     WA_display-ErrorDescription = WA_global-EinvError.
*   ENDIF.
*   WA_display-documentnumber = |{ WA_display-documentnumber ALPHA = IN }|.
*   WA_display-Documentdate = |{ WA_temp-Documentdate+6(4) }{ WA_temp-Documentdate+3(2) }{ WA_temp-Documentdate+0(2) }|.
*
*   CONDENSE: WA_display-Taxablevalue, WA_display-Cgstamount, WA_display-sgstamount, WA_display-igstamount.
*
*   APPEND WA_display TO LT_display.
*
* ENDLOOP.
  ENDMETHOD.




  METHOD get_data_for_fi.
    DATA: wa_final        TYPE zsd_s_global_st,
          wa_final_disp   TYPE zsd_s_global_st,
          lv_var_tabix    TYPE sy-tabix,
          wa_address      TYPE bapiaddr3,
          lt_ret1         TYPE TABLE OF bapiret2,
          lt_address1     TYPE STANDARD TABLE OF bapiaddr3,
          lv_amount_total TYPE netwr,
          lv_igst_total   TYPE netwr,
          lv_sgst_total   TYPE netwr,
          lv_cgst_total   TYPE netwr,
          lv_tabix        TYPE sy-tabix,
          lv_serial_no    TYPE i.
    CONSTANTS: c_sup    TYPE char10 VALUE 'S',
               c_pur    TYPE char10 VALUE 'EINV|OGST',
               c_auto   TYPE char10 VALUE 'EINV',
               c_b2b    TYPE char10 VALUE 'B2B',
               c_b2c    TYPE char10 VALUE 'B2C',
               c_expwop TYPE char10 VALUE 'EXPWOP',
               c_expwp  TYPE char10 VALUE 'EXPWP',
               c_crn    TYPE char10 VALUE 'CRN'.
    SELECT * FROM zfi_t_doctype
      WHERE module_type = 'INVOICE' INTO TABLE @DATA(lt_zeinv_billtype1).
    SELECT *
      FROM zgst_states
      WHERE 1 = 1 INTO TABLE @DATA(lt_gst_states).
    SELECT *
      FROM zsd_t_einv_uom WHERE 1 = 1
      INTO TABLE @DATA(lt_uom).
    SELECT a~bukrs,
           a~belnr,
           a~gjahr,
           a~awkey,
           a~awtyp,
           a~xblnr,
           a~budat,
           a~cpudt,
           a~cputm,
           a~blart,
           a~bldat,
           a~waers,
           a~kursf,
           a~tcode,
           a~usnam,
           a~xreversal
      FROM ZSD_C_bkpf AS a
      JOIN zfi_t_doctype AS b
        ON a~blart = b~blart
      WHERE a~bukrs IN @ir_bukrs
        AND a~belnr IN @ir_doc
        AND a~Xblnr IN @ir_xblnr
        AND a~Bldat IN @ir_doc_date
        AND a~gjahr IN @ir_gjahr
        AND b~module_type = 'INVOICE'
      INTO TABLE @DATA(lt_bkpf).
*  WHERE a~bukrs = @is_billing_header-bukrs
**    AND a~belnr = @is_billing_header-belnr
*    AND a~Xblnr = @is_billing_header-xblnr
*    AND a~Bldat IN @ir_doc_date
*    AND a~gjahr = @is_billing_header-gjahr
*    AND b~module_type = 'INVOICE'
*    INTO TABLE @DATA(LT_BKPF).
    IF lt_bkpf IS NOT INITIAL.
      SORT lt_bkpf BY xblnr.
      LOOP AT lt_bkpf ASSIGNING FIELD-SYMBOL(<wa_bkpf>).
        IF <wa_bkpf>-xblnr = ''.
        ELSE.
          IF <wa_bkpf>-xblnr+0(1) = '0'.
            CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
              EXPORTING
                input  = <wa_bkpf>-xblnr
              IMPORTING
                output = <wa_bkpf>-xblnr.
          ENDIF.
        ENDIF.
      ENDLOOP.
    ENDIF.
    DATA(lt_bkpf_temp) = lt_bkpf.
    CLEAR: lt_bkpf.
    LOOP AT lt_bkpf_temp INTO DATA(wa_bkpf_temp).
      READ TABLE et_final TRANSPORTING NO FIELDS WITH KEY documentnumber = wa_bkpf_temp-xblnr.
      IF sy-subrc = 0.
        CLEAR: wa_bkpf_temp.
        CONTINUE.
      ELSE.
        APPEND wa_bkpf_temp TO lt_bkpf.
        CLEAR: wa_bkpf_temp.
      ENDIF.
    ENDLOOP.
    IF lt_bkpf IS NOT INITIAL.
      SELECT bukrs,
             belnr,
             vbeln,
             gjahr,
             buzei,
             txgrp,
             koart,
             buzid,
             augbl,
             bupla,
             hsn_sac,
             plc_sup,
             gst_part,
             matnr,
             werks,
             wrbtr,
             kunnr,
             xauto,
             gvtyp,
             menge,
             hkont,
             gsber,
             lifnr,
             meins,
             sgtxt,
             dmbtr,
             prctr,
             segment,
             shkzg
        FROM zsd_c_bseg
        FOR ALL ENTRIES IN @lt_bkpf
        WHERE bukrs = @lt_bkpf-bukrs
          AND belnr = @lt_bkpf-belnr
          AND gjahr = @lt_bkpf-gjahr
        INTO TABLE @DATA(lt_bseg_gl).
      IF lt_bseg_gl IS NOT INITIAL.
        DATA(lt_bseg) = lt_bseg_gl.
        DATA(lt_bseg_ba) = lt_bseg_gl.
        DATA(lt_bseg_uom) = lt_bseg_gl.
        DATA(lt_bseg_hsn) = lt_bseg_gl.
        DATA(lt_bseg_temp) = lt_bseg_gl.
        DATA(lt_bseg_temp_lifnr) = lt_bseg_gl.
        SORT lt_bseg BY bukrs belnr gjahr txgrp buzid.
        DELETE ADJACENT DUPLICATES FROM lt_bseg COMPARING txgrp.
        DELETE lt_bseg WHERE buzid = 'T'.
        DELETE lt_bseg WHERE koart = 'D'.
        DELETE lt_bseg WHERE koart = 'K'.
        DELETE lt_bseg_ba WHERE gsber = ''.
        DELETE lt_bseg_uom WHERE meins = ''.
        DELETE lt_bseg_hsn WHERE hsn_sac = ''.
        DELETE lt_bseg_temp WHERE kunnr = ''.
        DELETE lt_bseg_temp_lifnr WHERE lifnr = ''.
      ENDIF.
      IF lt_bseg IS NOT INITIAL.
        SELECT * FROM makt INTO TABLE @DATA(lt_makt)
          FOR ALL ENTRIES IN @lt_bseg
          WHERE matnr = @lt_bseg-matnr
            AND spras = 'E'.
      ENDIF.
      IF lt_bseg_temp IS NOT INITIAL.
        SELECT * FROM kna1 INTO TABLE @DATA(lt_kna1a)
          FOR ALL ENTRIES IN @lt_bseg_temp
          WHERE kunnr EQ @lt_bseg_temp-kunnr.
      ENDIF.
      IF lt_kna1a IS NOT INITIAL.
        SELECT a~addrnumber,
               a~name1,
               a~name2,
               a~city1,
               a~street,
               a~str_suppl1,
               a~str_suppl2,
               a~str_suppl3,
               a~location,
               a~tel_number,
               a~post_code1,
               a~region
          FROM adrc AS a
          INNER JOIN @lt_kna1a AS b
            ON a~addrnumber = b~adrnr
          INTO TABLE @DATA(lt_adrc_fi).
      ENDIF.
      IF lt_bseg IS NOT INITIAL.
        IF lt_bseg_temp_lifnr IS NOT INITIAL.
          SELECT * FROM lfa1 INTO TABLE @DATA(lt_lfa1a)
            FOR ALL ENTRIES IN @lt_bseg_temp_lifnr
            WHERE lifnr EQ @lt_bseg_temp_lifnr-lifnr.
        ENDIF.
        IF lt_lfa1a IS NOT INITIAL.
          SELECT a~addrnumber,
                 a~name1,
                 a~name2,
                 a~city1,
                 a~street,
                 a~str_suppl1,
                 a~str_suppl2,
                 a~str_suppl3,
                 a~location,
                 a~tel_number,
                 a~post_code1,
                 a~region
            FROM adrc AS a
            INNER JOIN @lt_lfa1a AS b
              ON a~addrnumber = b~adrnr
            INTO TABLE @lt_adrc_fi.
        ENDIF.
        SELECT bukrs,
               belnr,
               gjahr,
               buzei,
               txgrp,
               shkzg,
               fwbas,
               fwste,
               kschl,
               kbetr,
               bupla
          FROM bset
          INTO TABLE @DATA(lt_bset)
          FOR ALL ENTRIES IN @lt_bkpf
          WHERE bukrs = @lt_bkpf-bukrs
            AND belnr = @lt_bkpf-belnr
            AND gjahr = @lt_bkpf-gjahr.
        SELECT * FROM j_1bbranch
          INTO TABLE @DATA(lt_j_1bbranch1)
          FOR ALL ENTRIES IN @lt_bseg
          WHERE branch = @lt_bseg-bupla.
        IF lt_j_1bbranch1 IS NOT INITIAL.
          SELECT a~addrnumber,
                 a~name1,
                 a~name2,
                 a~city1,
                 a~street,
                 a~str_suppl1,
                 a~str_suppl2,
                 a~str_suppl3,
                 a~location,
                 a~tel_number,
                 a~post_code1,
                 a~region
            FROM adrc AS a
            INNER JOIN @lt_j_1bbranch1 AS b
              ON a~addrnumber = b~adrnr
            INTO TABLE @DATA(lt_adrc_n).
          SELECT * FROM t001w INTO TABLE @DATA(lt_t001w1)
            FOR ALL ENTRIES IN @lt_j_1bbranch1
            WHERE j_1bbranch = @lt_j_1bbranch1-branch.
        ENDIF.
        SORT: lt_kna1a BY kunnr,
              lt_lfa1a BY lifnr,
              lt_bkpf BY bukrs belnr gjahr,
              lt_bseg BY bukrs belnr gjahr buzei,
              lt_bseg_ba BY bukrs belnr gjahr,
              lt_bset BY bukrs belnr gjahr txgrp,
              lt_j_1bbranch1 BY bukrs branch,
              lt_bseg_temp BY bukrs belnr gjahr,
              lt_bseg_gl BY bukrs belnr gjahr,
              lt_bseg_temp_lifnr BY belnr bukrs gjahr,
              lt_bseg_uom BY bukrs belnr gjahr txgrp buzid.
        CLEAR: lv_serial_no.
        LOOP AT lt_bseg INTO DATA(wa_bseg).
          wa_final-branch = wa_bseg-bupla.
          lv_serial_no = lv_serial_no + 1.
          AT NEW belnr.
            CLEAR: lv_serial_no.
            lv_serial_no = 1.
          ENDAT.
          wa_final-serialnumber = lv_serial_no.
          wa_final-custom1 = wa_bseg-belnr.
          wa_final-supplytype = c_sup.
*       WA_FINAL-purpose = C_PUR.
          wa_final-purpose = iv_purpose.
          IF iv_purpose CS 'EINV'.
            wa_final-autopushorgenerate = c_auto.
          ENDIF.
          wa_final-company_code = wa_bseg-Bukrs.
          wa_final-fiscalyear = wa_bseg-gjahr.
          wa_final-branch = wa_bseg-bukrs.
          wa_final-quantity = wa_bseg-menge.
          wa_final-uqc = wa_bseg-meins.
          wa_final-custom2 = wa_bseg-prctr.
          wa_final-custom3 = wa_bseg-segment.
          wa_final-custom4 = wa_bseg-bupla.
          IF lt_bseg_ba IS NOT INITIAL.
            READ TABLE lt_bseg_ba INTO DATA(wa_bseg_ba) WITH KEY bukrs = wa_bseg-bukrs
                                                                 belnr = wa_bseg-belnr
                                                                 gjahr = wa_bseg-gjahr
                                                                 BINARY SEARCH.
            IF sy-subrc = 0.
              wa_final-custom5 = wa_bseg_ba-gsber.
            ENDIF.
          ENDIF.
          SORT lt_uom BY sap_uom.
          READ TABLE lt_uom INTO DATA(wa_uom) WITH KEY sap_uom = wa_bseg-meins.
          IF sy-subrc = 0.
            wa_final-uqc = wa_uom-gst_uom.
          ELSE.
            wa_final-uqc = wa_bseg-meins.
          ENDIF.
          READ TABLE lt_bkpf INTO DATA(wa_bkpf) WITH KEY bukrs = wa_bseg-bukrs
                                                         belnr = wa_bseg-belnr
                                                         gjahr = wa_bseg-gjahr BINARY SEARCH.
          IF sy-subrc = 0.
            CALL FUNCTION 'BAPI_USER_GET_DETAIL'
              EXPORTING
                username = wa_bkpf-usnam
              IMPORTING
                address  = wa_address
              TABLES
                return   = lt_ret1.
            APPEND wa_address TO lt_address1.
            READ TABLE lt_address1 TRANSPORTING NO FIELDS INDEX 1.
            IF sy-subrc = 0.
              wa_final-toemailaddress = wa_address-e_mail.
            ENDIF.
            SORT lt_zeinv_billtype1 BY blart.
            READ TABLE lt_zeinv_billtype1 INTO DATA(wa_zeinv_billtype1) WITH KEY blart = wa_bkpf-blart.
            IF sy-subrc = 0.
              wa_final-documenttype = wa_zeinv_billtype1-doc_type.
            ENDIF.
            CONCATENATE wa_bkpf-budat+4(2) wa_bkpf-budat+0(4)
              INTO wa_final-returnperiod.
            CONDENSE wa_final-returnperiod.
            wa_final-documentdate = wa_bkpf-bldat.
            wa_final-documentvalue = wa_bseg-wrbtr.
            MOVE wa_bkpf-blart TO wa_final-custom2.
          ENDIF.
          READ TABLE lt_bseg_temp INTO DATA(wa_bseg_temp) WITH KEY bukrs = wa_bseg-bukrs
                                                                   belnr = wa_bseg-belnr
                                                                   gjahr = wa_bseg-gjahr BINARY SEARCH.
          IF sy-subrc = 0.
            wa_final-custom2 = wa_bseg_temp-kunnr.
            READ TABLE lt_kna1a INTO DATA(wa_kna1a) WITH KEY kunnr = wa_bseg_temp-kunnr BINARY SEARCH.
            IF sy-subrc EQ 0.
              wa_final-billtogstin = wa_kna1a-stcd3.
              wa_final-billtotradename = wa_kna1a-name1.
              wa_final-billtolegalname = wa_kna1a-name1.
              wa_final-billtocity = wa_kna1a-ort01.
              wa_final-billtopincode = wa_kna1a-pstlz.
              wa_final-billtoaddress1 = wa_kna1a-stras.
              wa_final-destinationcountry = wa_kna1a-land1.
              wa_final-shiptogstin = wa_kna1a-stcd3.
              wa_final-shiptotradename = wa_kna1a-name1.
              wa_final-shiptolegalname = wa_kna1a-name1.
              wa_final-shiptocity = wa_kna1a-ort01.
              wa_final-shiptopincode = wa_kna1a-pstlz.
              wa_final-shiptoaddress1 = wa_kna1a-stras.
              wa_final-shiptostatecode = wa_final-shiptogstin(2).
              SORT lt_adrc_fi BY addrnumber.
              READ TABLE lt_adrc_fi INTO DATA(wa_adrc_fi) WITH KEY addrnumber = wa_kna1a-adrnr BINARY SEARCH.
              IF sy-subrc = 0.
                CONCATENATE wa_adrc_fi-str_suppl3 wa_adrc_fi-location INTO wa_final-billtoaddress2 SEPARATED BY ''.
                CONCATENATE wa_adrc_fi-str_suppl3 wa_adrc_fi-location INTO wa_final-shiptoaddress2 SEPARATED BY ''.
              ENDIF.
            ENDIF.
          ELSEIF lt_bseg_temp_lifnr IS NOT INITIAL..
            READ TABLE lt_bseg_temp_lifnr INTO DATA(wa_bseg_temp_lifnr) WITH KEY bukrs = wa_bseg-bukrs
                                                                                  belnr = wa_bseg-belnr
                                                                                  gjahr = wa_bseg-gjahr BINARY SEARCH.
            IF sy-subrc = 0.
              READ TABLE lt_lfa1a INTO DATA(wa_lfa1a) WITH KEY lifnr = wa_bseg_temp_lifnr-lifnr BINARY SEARCH.
              MOVE wa_bseg_temp_lifnr-kunnr TO wa_final-custom2.
              wa_final-billtogstin = wa_lfa1a-stcd3.
              wa_final-billtotradename = wa_lfa1a-name1.
              wa_final-billtolegalname = wa_lfa1a-name1.
              wa_final-billtocity = wa_lfa1a-ort01.
              wa_final-billtopincode = wa_lfa1a-pstlz.
              wa_final-billtoaddress1 = wa_lfa1a-stras.
              wa_final-destinationcountry = wa_lfa1a-land1.
              wa_final-shiptogstin = wa_lfa1a-stcd3.
              wa_final-shiptotradename = wa_lfa1a-name1.
              wa_final-shiptolegalname = wa_lfa1a-name1.
              wa_final-shiptocity = wa_lfa1a-ort01.
              wa_final-shiptopincode = wa_lfa1a-pstlz.
              wa_final-shiptoaddress1 = wa_lfa1a-stras.
              wa_final-shiptostatecode = wa_final-shiptogstin(2).
              CLEAR wa_adrc_fi.
              SORT lt_adrc_fi BY addrnumber.
              READ TABLE lt_adrc_fi INTO wa_adrc_fi WITH KEY addrnumber = wa_lfa1a-adrnr BINARY SEARCH.
              IF sy-subrc = 0.
                CONCATENATE wa_adrc_fi-str_suppl3 wa_adrc_fi-location INTO wa_final-billtoaddress2 SEPARATED BY ''.
                CONCATENATE wa_adrc_fi-str_suppl3 wa_adrc_fi-location INTO wa_final-shiptoaddress2 SEPARATED BY ''.
              ENDIF.
            ENDIF.
          ENDIF.
          SORT lt_bseg_gl BY bukrs belnr gjahr buzei koart buzid xauto.
          READ TABLE lt_bseg_gl INTO DATA(wa_bseg_gl) WITH KEY bukrs = wa_bseg-bukrs
                                                                belnr = wa_bseg-belnr
                                                                gjahr = wa_bseg-gjahr
                                                                buzei = wa_bseg-buzei
                                                                koart = 'S'
                                                                buzid = ''
                                                                xauto = '' BINARY SEARCH.
          IF sy-subrc = 0.
            wa_final-custom7 = wa_bseg_gl-hkont.
            CLEAR wa_bseg_gl.
          ENDIF.
          IF wa_final-billtogstin IS INITIAL OR wa_final-billtogstin = 'NA' OR wa_final-billtogstin = 'URD' OR wa_final-billtogstin = '0'.
            wa_final-transactiontype = c_b2c.
          ELSE.
            wa_final-transactiontype = c_b2b.
          ENDIF.
          wa_final-pos = wa_final-billtostatecode = wa_final-billtogstin+0(2).
          READ TABLE lt_gst_states INTO DATA(wa_gst_states) WITH KEY region = wa_kna1a-regio.
          IF sy-subrc = 0.
            wa_final-billtostatecode = wa_gst_states-gst_state_code.
            wa_final-pos = wa_gst_states-gst_state_code.
            wa_final-shiptostatecode = wa_gst_states-gst_state_code.
          ENDIF.
          IF wa_final-billtostatecode IS INITIAL OR wa_final-billtostatecode = 0.
            READ TABLE lt_gst_states INTO wa_gst_states WITH KEY region = wa_lfa1a-regio.
            IF sy-subrc = 0.
              wa_final-billtostatecode = wa_gst_states-gst_state_code.
              wa_final-shiptostatecode = wa_gst_states-gst_state_code.
              wa_final-pos = wa_gst_states-gst_state_code.
            ENDIF.
          ENDIF.
          READ TABLE lt_bset TRANSPORTING NO FIELDS WITH KEY bukrs = wa_bseg-bukrs
                                                             belnr = wa_bseg-belnr
                                                             gjahr = wa_bseg-gjahr
                                                             txgrp = wa_bseg-txgrp BINARY SEARCH.
          IF sy-subrc = 0.
            lv_var_tabix = sy-tabix.
            LOOP AT lt_bset INTO DATA(wa_bset) FROM lv_var_tabix.
              IF wa_bset-bukrs = wa_bseg-bukrs AND wa_bset-belnr = wa_bseg-belnr AND wa_bset-gjahr = wa_bseg-gjahr AND
                 wa_bset-txgrp = wa_bseg-txgrp.
              ELSE.
                CLEAR: wa_bset.
                EXIT.
              ENDIF.
              CASE wa_bset-kschl.
                WHEN 'JOCG'.
                  wa_final-taxablevalue = wa_bset-fwbas.
                  IF wa_bset-shkzg = 'H'.
                    wa_final-cgstamount = wa_final-cgstamount - wa_bset-fwste.
                  ELSE.
                    wa_final-cgstamount = wa_final-cgstamount + wa_bset-fwste.
                  ENDIF.
                  IF wa_final-cgstamount LT '0'.
                    wa_final-cgstamount = wa_final-cgstamount * -1.
                  ENDIF.
                  wa_final-rate = wa_final-rate + ( wa_bset-kbetr / 10 ).
                WHEN 'JOIG'.
                  wa_final-taxablevalue = wa_bset-fwbas.
                  IF wa_bset-shkzg = 'H'.
                    wa_final-igstamount = wa_final-igstamount - wa_bset-fwste.
                  ELSE.
                    wa_final-igstamount = wa_final-igstamount + wa_bset-fwste.
                  ENDIF.
                  IF wa_final-igstamount LT '0'.
                    wa_final-igstamount = wa_final-igstamount * -1.
                  ENDIF.
                  wa_final-rate = wa_final-rate + ( wa_bset-kbetr / 10 ).
                WHEN 'JOSG' OR 'JOUG'.
                  wa_final-taxablevalue = wa_bset-fwbas.
                  IF wa_bset-shkzg = 'H'.
                    wa_final-sgstamount = wa_final-sgstamount - wa_bset-fwste.
                  ELSE.
                    wa_final-sgstamount = wa_final-sgstamount + wa_bset-fwste.
                  ENDIF.
                  IF wa_final-sgstamount LT '0'.
                    wa_final-sgstamount = wa_final-sgstamount * -1.
                  ENDIF.
                  wa_final-rate = wa_final-rate + ( wa_bset-kbetr / 10 ).
              ENDCASE.
              CLEAR: wa_bset.
            ENDLOOP.
          ENDIF.
          READ TABLE lt_j_1bbranch1 INTO DATA(wa_j_1bbranch1) WITH KEY bukrs = wa_bseg-bukrs branch = wa_bseg-bupla BINARY SEARCH.
          IF sy-subrc = 0.
            READ TABLE lt_adrc_n INTO DATA(wa_adrc_n) WITH KEY addrnumber = wa_j_1bbranch1-adrnr.
            IF sy-subrc = 0.
              wa_final-billfromlegalname = wa_adrc_n-name1.
              wa_final-billfromaddress1 = wa_adrc_n-street.
              wa_final-billfromaddress2 = wa_adrc_n-str_suppl3.
              wa_final-billfrompincode = wa_adrc_n-post_code1.
              wa_final-billfromcity = wa_adrc_n-city1..
              wa_final-dispatchfromadd1 = wa_final-billfromaddress1.
              wa_final-dispatchfromadd2 = wa_final-billfromaddress2.
              wa_final-dispatchfromcity = wa_final-billfromcity.
              wa_final-dispatchfrompincode = wa_final-billfrompincode.
              wa_final-dispatchfromtradename = wa_final-billfromlegalname.
            ENDIF.
            IF wa_final-billfromlegalname IS INITIAL.
              SORT lt_t001w1 BY j_1bbranch.
              READ TABLE lt_t001w1 INTO DATA(wa_t001w1) WITH KEY j_1bbranch = wa_j_1bbranch1-branch.
              IF sy-subrc = 0.
                wa_final-billfromlegalname = wa_t001w1-name2.
                wa_final-billfromaddress1 = wa_t001w1-stras.
                wa_final-billfromaddress2 = wa_t001w1-pfach.
                wa_final-billfrompincode = wa_t001w1-pstlz.
                wa_final-billfromcity = wa_t001w1-ort01.
                wa_final-dispatchfromadd1 = wa_final-billfromaddress1.
                wa_final-dispatchfromadd2 = wa_final-billfromaddress2.
                wa_final-dispatchfromcity = wa_final-billfromcity.
                wa_final-dispatchfrompincode = wa_final-billfrompincode.
                wa_final-dispatchfromtradename = wa_final-billfromlegalname.
              ENDIF.
            ENDIF.
            IF wa_final-billfrompincode IS INITIAL OR wa_final-billfromcity IS INITIAL.
              SORT lt_adrc_n BY addrnumber.
              CLEAR wa_adrc_n.
              READ TABLE lt_adrc_n INTO wa_adrc_n WITH KEY addrnumber = wa_j_1bbranch1-adrnr.
              IF sy-subrc = 0.
                wa_final-billfromlegalname = wa_adrc_n-name1.
                wa_final-billfromaddress1 = wa_adrc_n-str_suppl1.
                wa_final-billfrompincode = wa_adrc_n-post_code1.
                wa_final-billfromcity = wa_adrc_n-city1..
                wa_final-dispatchfromadd1 = wa_final-billfromaddress1.
                wa_final-dispatchfromadd2 = wa_final-billfromaddress2.
                wa_final-dispatchfromcity = wa_final-billfromcity.
                wa_final-dispatchfrompincode = wa_final-billfrompincode.
                wa_final-dispatchfromtradename = wa_final-billfromlegalname.
              ENDIF.
            ENDIF.
          ENDIF.
          wa_final-billfromstatecode = wa_j_1bbranch1-gstin+0(2).
          wa_final-dispatchfromstatecode = wa_final-billfromstatecode.
          wa_final-locationgstin = wa_j_1bbranch1-gstin.
          wa_final-billfromgstin = wa_j_1bbranch1-gstin.
          wa_final-dispatchfromgstin = wa_final-billfromgstin.
          wa_final-reversecharge = 'N'.
          IF wa_final-cgstamount EQ '0.00' OR wa_final-cgstamount EQ '0'.
            CLEAR wa_final-cgstamount.
          ENDIF.
          IF wa_final-igstamount EQ '0.00' OR wa_final-igstamount EQ '0'.
            CLEAR wa_final-igstamount.
          ENDIF.
          IF wa_final-igstamount EQ '0.00' OR wa_final-igstamount EQ '0'.
            CLEAR wa_final-igstamount.
          ENDIF.
          CLEAR wa_bkpf.
          READ TABLE lt_bkpf INTO wa_bkpf WITH KEY bukrs = wa_bseg-bukrs
                                                  belnr = wa_bseg-belnr
                                                  gjahr = wa_bseg-gjahr BINARY SEARCH.
          IF wa_bkpf-waers NE 'INR'.
            wa_final-transactiontype = c_expwop.
          ENDIF.
          IF wa_final-transactiontype = c_expwop.
            CLEAR wa_final-igstamount.
            wa_final-igstamount = '0'.
            wa_final-rate = '0'.
          ENDIF.
          IF wa_final-transactiontype = c_expwp.
            wa_final-documentvalue = wa_bseg-wrbtr + wa_final-igstamount.
          ENDIF.
          wa_final-documentvalue = wa_final-cgstamount +
                                   wa_final-sgstamount +
                                   wa_final-igstamount +
                                   wa_final-cessamount +
                                   wa_final-taxablevalue.
          IF wa_bkpf-waers NE 'INR' AND wa_final-transactiontype = c_expwop.
            wa_final-documentvalue = wa_bkpf-kursf * wa_final-documentvalue.
            wa_final-taxablevalue = wa_final-documentvalue.
          ENDIF.
          IF wa_final-billfromgstin = ''.
            CLEAR: wa_final, lv_var_tabix, wa_bset, wa_bkpf, wa_bseg.
            CONTINUE.
          ENDIF.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL-custom3 WITH space.
          MOVE-CORRESPONDING wa_final TO wa_final_disp.
****    ---------------------------------hsn----------------------------------------------------------------*
          IF wa_final_disp-hsn IS INITIAL.
            READ TABLE lt_bseg_hsn INTO DATA(wa_bseg_hsn) WITH KEY bukrs = wa_bseg-bukrs belnr = wa_bseg-belnr gjahr = wa_bseg-gjahr txgrp = wa_bseg-txgrp.
            IF sy-subrc = 0.
              SHIFT wa_bseg_hsn-hsn_sac LEFT DELETING LEADING '0'.
              IF wa_bseg_hsn-hsn_sac(2) = '99'.
                wa_final_disp-hsn = wa_bseg_hsn-hsn_sac.
                wa_final_disp-isservice = 'Y'.
              ELSE.
                wa_final_disp-hsn = wa_bseg_hsn-hsn_sac.
                wa_final_disp-isservice = 'N'.
              ENDIF.
              IF wa_final_disp-itemdescription IS INITIAL.
                wa_final_disp-itemdescription = wa_bseg_hsn-sgtxt.
              ENDIF.
              IF wa_final_disp-custom7 IS INITIAL.
                wa_final_disp-custom7 = wa_bseg_hsn-hkont.
              ENDIF.
            ENDIF.
          ENDIF.
          IF wa_final_disp-hsn IS NOT INITIAL.
            REPLACE ALL OCCURRENCES OF '.' IN wa_final_disp-hsn WITH space.
            CONDENSE wa_final_disp-hsn.
          ENDIF.
          READ TABLE lt_makt INTO DATA(wa_makt) WITH KEY matnr = wa_bseg-matnr.
          IF sy-subrc = 0.
            wa_final_disp-itemdescription = wa_makt-maktx.
          ENDIF.
          CLEAR wa_makt.
          IF wa_final_disp-itemdescription IS INITIAL.
            wa_final_disp-itemdescription = wa_bseg-sgtxt.
          ENDIF.
          wa_final_disp-grossamount = wa_final_disp-taxablevalue.
          wa_final_disp-quantity = wa_bseg-menge.
          wa_final_disp-itemname = wa_bseg-matnr.
          wa_final_disp-uqc = wa_bseg-meins.
          IF wa_final_disp-quantity IS INITIAL.
            READ TABLE lt_bseg_uom INTO DATA(wa_bseg_uom) WITH KEY bukrs = wa_bseg-bukrs belnr = wa_bseg-belnr txgrp = wa_bseg-txgrp gjahr = wa_bseg-gjahr.
            IF sy-subrc = 0.
              IF wa_bseg_uom-meins IS NOT INITIAL.
                wa_final_disp-quantity = wa_bseg_uom-menge.
                wa_final_disp-uqc = wa_bseg_uom-meins.
                CLEAR wa_bseg_uom.
              ENDIF.
            ENDIF.
          ENDIF.
          CONDENSE wa_final_disp-quantity.
          IF wa_final_disp-quantity IS NOT INITIAL.
            IF wa_final_disp-quantity = '0' OR wa_final_disp-quantity = '0.00' OR wa_final_disp-quantity = '0.000'.
              CLEAR: wa_final_disp-quantity.
            ELSE.
              wa_final_disp-priceperquantity = wa_final_disp-grossamount / wa_final_disp-quantity.
            ENDIF.
          ELSE.
            wa_final_disp-priceperquantity = wa_final_disp-taxablevalue.
            IF wa_final-documenttype = 'CRN' OR wa_final-documenttype = 'DBN'.
              wa_final_disp-quantity = ''.
              wa_final_disp-uqc = 'OTH'.
            ENDIF.
          ENDIF.
          IF wa_final_disp-hsn(2) = '99'.
            wa_final_disp-quantity = ''.
            wa_final_disp-uqc = 'OTH'.
          ENDIF.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billfromlegalname WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billtolegalname WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-shiptolegalname WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billfromtradename WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billtotradename WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-itemdescription WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-itemname WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billfromcity WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billtocity WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-custom1 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-custom2 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-custom3 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-custom4 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-custom5 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billtoaddress1 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billfromaddress1 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billtoaddress2 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-billfromaddress2 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-shiptoaddress1 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-shiptoaddress2 WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-shiptocity WITH space.
*       REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN WA_FINAL_DISP-shiptotradename WITH space.
          IF wa_final_disp-transactiontype = c_expwp.
          ELSE.
          ENDIF.
          IF wa_final_disp-documenttype = c_crn.
            wa_final_disp-documentvalue = wa_final_disp-documentvalue * -1.
          ENDIF.
          IF wa_final_disp-priceperquantity < 0.
            wa_final_disp-priceperquantity = wa_final_disp-priceperquantity * -1.
          ENDIF.
          IF wa_final_disp-taxablevalue < 0.
            wa_final_disp-taxablevalue = wa_final_disp-taxablevalue * -1.
          ENDIF.
          IF wa_final_disp-itemtotal < 0.
            wa_final_disp-itemtotal = wa_final_disp-itemtotal * -1.
          ENDIF.
          IF wa_final-igstamount < 0.
            wa_final_disp-igstamount = wa_final-igstamount * -1.
          ENDIF.
          IF wa_final_disp-cgstamount < 0.
            wa_final_disp-cgstamount = wa_final_disp-cgstamount * -1.
          ENDIF.
          IF wa_final_disp-sgstamount < 0.
            wa_final_disp-sgstamount = wa_final_disp-sgstamount * -1.
          ENDIF.
          IF wa_final_disp-grossamount < 0.
            wa_final_disp-grossamount = wa_final_disp-grossamount * -1.
          ENDIF.
          wa_final_disp-itemtotal = wa_final_disp-taxablevalue + wa_final_disp-igstamount + wa_final_disp-cgstamount + wa_final_disp-sgstamount.
          wa_final_disp-doccurrencycode = wa_bkpf-waers.
          wa_final_disp-documentnumber = wa_bkpf-xblnr.
          wa_final_disp-custom1 = wa_bkpf-belnr.
          CONCATENATE wa_final_disp-documentdate+6(2) '-' wa_final_disp-documentdate+4(2) '-' wa_final_disp-documentdate(4) INTO DATA(lv_fi_date).
          CLEAR: wa_final_disp-documentdate.
          wa_final_disp-documentdate = lv_fi_date.
          IF sy-sysid = 'DS4' OR sy-sysid = 'PIQ'.
            IF wa_final_disp-billfromstatecode = '01'.
              wa_final_disp-billfromgstin = '01AACPH8447G002'.
              wa_final_disp-locationgstin = '01AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '02'.
              wa_final_disp-billfromgstin = '02AACPH8447G002'.
              wa_final_disp-locationgstin = '02AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '03'.
              wa_final_disp-billfromgstin = '03AACPH8447G002'.
              wa_final_disp-locationgstin = '03AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '04'.
              wa_final_disp-billfromgstin = '04AACPH8447G002'.
              wa_final_disp-locationgstin = '04AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '05'.
              wa_final_disp-billfromgstin = '05AACPH8447G002'.
              wa_final_disp-locationgstin = '05AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '06'.
              wa_final_disp-billfromgstin = '06AACPH8447G002'.
              wa_final_disp-locationgstin = '06AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '07'.
              wa_final_disp-billfromgstin = '07AACPH8447G002'.
              wa_final_disp-locationgstin = '07AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '08'.
              wa_final_disp-billfromgstin = '08AACPH8447G002'.
              wa_final_disp-locationgstin = '08AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '09'.
              wa_final_disp-billfromgstin = '09AACPH8447G012'.
              wa_final_disp-locationgstin = '09AACPH8447G012'.
            ELSEIF wa_final_disp-billfromstatecode = '10'.
              wa_final_disp-billfromgstin = '10AACPH8447G013'.
              wa_final_disp-locationgstin = '10AACPH8447G013'.
            ELSEIF wa_final_disp-billfromstatecode = '11'.
              wa_final_disp-billfromgstin = '11AACPH8447G014'.
              wa_final_disp-locationgstin = '11AACPH8447G014'.
            ELSEIF wa_final_disp-billfromstatecode = '12'.
              wa_final_disp-billfromgstin = '12AACPH8447G015'.
              wa_final_disp-locationgstin = '12AACPH8447G015'.
            ELSEIF wa_final_disp-billfromstatecode = '24'.
              wa_final_disp-billfromgstin = '24AACPH8447G002'.
              wa_final_disp-locationgstin = '24AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '27'.
              wa_final_disp-billfromgstin = '27AACPH8447G002'.
              wa_final_disp-locationgstin = '27AACPH8447G002'.
            ELSEIF wa_final_disp-billfromstatecode = '29'.
              wa_final_disp-billfromgstin = '29AACPH8447G000'.
              wa_final_disp-locationgstin = '29AACPH8447G000'.
            ELSEIF wa_final_disp-billfromstatecode = '19'.
              wa_final_disp-billfromgstin = '19AACPH8447G018'.
              wa_final_disp-locationgstin = '19AACPH8447G018'.
            ELSEIF wa_final_disp-billfromstatecode = '33'.
              wa_final_disp-billfromgstin = '33AACPH8447G016'.
              wa_final_disp-locationgstin = '33AACPH8447G016'.
            ELSEIF wa_final_disp-billfromstatecode = '37'.
              wa_final_disp-billfromgstin = '37AACPH8447G017'.
              wa_final_disp-locationgstin = '37AACPH8447G017'.
            ENDIF.
          ENDIF.
****    changes by Malay
          READ TABLE lt_bseg_temp INTO DATA(wa_bseg_cust) WITH KEY belnr = wa_bkpf-belnr bukrs = wa_bkpf-bukrs gjahr = wa_bkpf-gjahr BINARY SEARCH.
          IF sy-subrc = 0.
            IF wa_bseg_cust-dmbtr < 0.
              wa_final_disp-documenttype = 'CRN'.
            ENDIF.
          ENDIF.
          wa_final_disp-customitem8 = 'FI'.
          IF wa_final_disp-isservice = 'Y'.
            IF wa_final_disp-uqc IS INITIAL.
              wa_final_disp-uqc = 'OTH'.
            ENDIF.
          ELSEIF wa_final_disp-isservice = 'N'.
            IF wa_final_disp-uqc IS INITIAL.
              wa_final_disp-uqc = 'NOS'.
            ENDIF.
          ENDIF.
          IF wa_final_disp-documenttype = 'CRN' AND wa_bseg-shkzg = 'H'.
            wa_final_disp-custom9 = 'X'.
            wa_final_disp-error_description = 'WRONG DOCUMENT TYPE'.
          ELSEIF wa_final_disp-documenttype = 'DBN' AND wa_bseg-shkzg = 'S'.
            wa_final_disp-custom9 = 'X'.
            wa_final_disp-error_description = 'WRONG DOCUMENT TYPE'.
          ELSEIF wa_final_disp-documenttype = 'INV' AND wa_bseg-shkzg = 'S'.
            wa_final_disp-custom9 = 'X'.
            wa_final_disp-error_description = 'WRONG DOCUMENT TYPE'.
          ENDIF.
          IF wa_final_disp-documentnumber IS INITIAL.
            wa_final_disp-custom9 = 'Y'.
            wa_final_disp-error_description = 'ODN number could not be found for the given document number'.
          ENDIF.
          READ TABLE lt_uom INTO wa_uom WITH KEY sap_uom = wa_final_disp-uqc BINARY SEARCH.
          IF sy-subrc = 0.
            wa_final_disp-uqc = wa_uom-gst_uom.
          ELSE.
            wa_final_disp-uqc = wa_bseg-meins.
          ENDIF.
          APPEND wa_final_disp TO et_final.
***************************
          CLEAR: wa_final, wa_final_disp, lv_var_tabix, wa_bset, wa_bkpf, wa_bseg, wa_bseg_temp.
        ENDLOOP.
      ENDIF.
    ENDIF.
    DELETE et_final WHERE taxablevalue = 0.
    DELETE et_final WHERE documentvalue = 0.
*********FI_Table Logic************************************************************
    IF et_final IS NOT INITIAL.
      DELETE ADJACENT DUPLICATES FROM et_final COMPARING ALL FIELDS.
    ENDIF.
*********************************************************************
    DATA(lt_final_out_temp) = et_final.
    SORT et_final BY documentnumber.
    SORT lt_final_out_temp BY documentnumber.
    IF lt_final_out_temp IS NOT INITIAL.
      LOOP AT lt_final_out_temp INTO DATA(wa_final_out).
        lv_amount_total = lv_amount_total + wa_final_out-itemtotal.
        lv_igst_total = lv_igst_total + wa_final_out-igstamount.
        lv_sgst_total = lv_sgst_total + wa_final_out-sgstamount.
        lv_cgst_total = lv_cgst_total + wa_final_out-cgstamount.
        CLEAR: lv_amount_total.
        AT END OF documentnumber.
          READ TABLE et_final TRANSPORTING NO FIELDS WITH KEY documentnumber = wa_final_out-documentnumber.
          IF sy-subrc = 0.
            lv_tabix = sy-tabix.
            LOOP AT et_final ASSIGNING FIELD-SYMBOL(<wa_final_out>) FROM lv_tabix WHERE
                                    documentnumber = wa_final_out-documentnumber.
              <wa_final_out>-documentvalue = lv_amount_total.
              <wa_final_out>-customitem5 = lv_igst_total.
              <wa_final_out>-customitem6 = lv_sgst_total.
              <wa_final_out>-customitem7 = lv_cgst_total.
              CLEAR: <wa_final_out>-documentvalue.
            ENDLOOP.
            CLEAR: lv_tabix.
          ENDIF.
          CLEAR: lv_amount_total, lv_igst_total, lv_sgst_total, lv_cgst_total.
        ENDAT.
        CLEAR: wa_final_out.
      ENDLOOP.
    ENDIF.
    DATA: lr_doc     TYPE RANGE OF zsd_c_einv_main-Documentnumber,
          wa_display TYPE zsd_c_einv.
    lr_doc = VALUE #( FOR wa_doc IN et_final
                      ( sign = 'I' option = 'EQ' low = wa_doc-documentnumber ) ).
    SELECT * FROM zsd_c_einv_main
      WHERE Documentnumber IN @lr_doc
      INTO TABLE @DATA(lt_global).
    LOOP AT et_final INTO DATA(wa_temp).
      MOVE-CORRESPONDING wa_temp TO wa_display.
      DATA: lv_doc TYPE I_billingdocumentbasic-DocumentReferenceID.
      lv_doc = wa_temp-documentnumber.
*   LV_DOC = |{ WA_TEMP-document_number ALPHA = IN }|.
      READ TABLE lt_global INTO DATA(wa_global) WITH KEY documentnumber = lv_doc.
      IF sy-subrc = 0.
        wa_display-irn_status = COND string(
          WHEN wa_global-Cancel IS NOT INITIAL THEN 'IRN Cancelled'
          WHEN wa_global-Fail = 'X' THEN 'ERROR !'
          WHEN wa_global-Irn IS INITIAL THEN 'IRN Pending'
          ELSE 'IRN Generated' ).
        wa_display-irn_icon = COND i(
          WHEN wa_global-Cancel IS NOT INITIAL THEN 1
          WHEN wa_global-fail = 'X' THEN 1
          WHEN wa_global-irn IS INITIAL THEN 2
          ELSE 3 ).
        wa_display-Irn = wa_global-Irn.
        wa_display-Acknumber = wa_global-AckowlegementNumber.
        wa_display-Ackdate = wa_global-AckDate.
        wa_display-ErrorDescription = wa_global-Error.
      ELSE.
        wa_display-irn_status = 'IRN Pending'.
        wa_display-irn_icon = 2.
        wa_display-ErrorDescription = wa_global-Error.
      ENDIF.
*   READ TABLE t_gt_doc_val INTO s_gs_doc_val WITH KEY document_no = WA_TEMP-document_number.
*   IF sy-subrc = 0.
*     WA_DISPLAY-taxablevalue = s_gs_doc_val-taxable_value.
*     WA_DISPLAY-cgstamount = s_gs_doc_val-cgst.
*     WA_DISPLAY-sgstamount = s_gs_doc_val-sgst.
*     WA_DISPLAY-quantity = s_gs_doc_val-qty.
*     WA_DISPLAY-igstamount = COND #( WHEN s_gs_doc_val-igst IS NOT INITIAL
*                                    THEN s_gs_doc_val-igst
*                                    ELSE ' ' ).
*   ENDIF.
      wa_display-documentnumber = wa_temp-documentnumber.
      wa_display-Documentdate = |{ wa_temp-Documentdate+6(4) }{ wa_temp-Documentdate+3(2) }{ wa_temp-Documentdate+0(2) }|.
*   WA_DISPLAY-Rate = |{ WA_TEMP-items[ 1 ]-rate } { '%' }|.
*   WA_DISPLAY-billingtype = WA_TEMP-items[ 1 ]-custom_item5.
      wa_display-documentvalue = wa_temp-documentvalue.
      wa_display-transactiontype = wa_temp-transactiontype.
      wa_display-documenttype = wa_temp-documenttype.
      wa_display-CompanyCode = wa_temp-company_code.
*
*
*
*   WA_DISPLAY-billfromgstin = WA_TEMP-billfromgstin.
*   WA_DISPLAY-billfromlegalname = WA_TEMP-billfromlegalname.
*   WA_DISPLAY-billfromtradename = WA_TEMP-billfromtradename.
*   WA_DISPLAY-billfromaddress1 = WA_TEMP-billfromaddress1.
*   WA_DISPLAY-billfromaddress2 = WA_TEMP-billfromaddress2.
*   WA_DISPLAY-billfromcity = WA_TEMP-billfromcity.
*   WA_DISPLAY-billfromstatecode = WA_TEMP-billfromstatecode.
*   WA_DISPLAY-billfrompincode = WA_TEMP-billfrompincode.
*   WA_DISPLAY-billfromphone = WA_TEMP-billfromphone.
*   WA_DISPLAY-billfromemail = WA_TEMP-billfromemail.
*   WA_DISPLAY-dispatchfromgstin = WA_TEMP-dispatchfromgstin.
*   WA_DISPLAY-dispatchfromtradename = WA_TEMP-dispatchfromtradename.
*   WA_DISPLAY-dispatchfromadd1 = WA_TEMP-dispatchfromadd1.
*   WA_DISPLAY-dispatchfromadd2 = WA_TEMP-dispatchfromadd2.
*   WA_DISPLAY-dispatchfromcity = WA_TEMP-dispatchfromcity.
*   WA_DISPLAY-dispatchfromstatecode = WA_TEMP-dispatchfromstatecode.
*   WA_DISPLAY-dispatchfrompincode = WA_TEMP-dispatchfrompincode.
**  WA_DISPLAY-dispatchfromphone = WA_TEMP-dispatch_from_pho
**  WA_DISPLAY-dispatchfromemail = WA_TEMP-dispatch_from
*   WA_DISPLAY-billtogstin = WA_TEMP-bill_to_gstin.
*   WA_DISPLAY-billtolegalname = WA_TEMP-bill_to_legal_name.
*   WA_DISPLAY-billtotradename = WA_TEMP-bill_to_trade_name.
*   WA_DISPLAY-billtoaddress1 = WA_TEMP-bill_to_address1.
*   WA_DISPLAY-billtoaddress2 = WA_TEMP-bill_to_address2.
*   WA_DISPLAY-billtocity = WA_TEMP-bill_to_city.
**  WA_DISPLAY-billtodistrict = WA_TEMP-bill_to_
*   WA_DISPLAY-billtostatecode = WA_TEMP-bill_to_state_code.
*   WA_DISPLAY-Pos = WA_TEMP-pos.
*   WA_DISPLAY-billtopincode = WA_TEMP-bill_to_pincode.
*   WA_DISPLAY-billtophone = WA_TEMP-bill_to_phone.
*   WA_DISPLAY-billtoemail = WA_TEMP-bill_to_email.
*   WA_DISPLAY-shiptogstin = WA_TEMP-ship_to_gstin.
*   WA_DISPLAY-shiptolegalname = WA_TEMP-ship_to_legal_name.
*   WA_DISPLAY-shiptotradename = WA_TEMP-ship_to_trade_name.
*   WA_DISPLAY-shiptoaddress1 = WA_TEMP-ship_to_address1.
*   WA_DISPLAY-shiptoaddress2 = WA_TEMP-ship_to_address2.
*   WA_DISPLAY-shiptocity = WA_TEMP-ship_to_city.
*   WA_DISPLAY-shiptostatecode = WA_TEMP-ship_to_state_code.
*   WA_DISPLAY-shiptopincode = WA_TEMP-ship_to_pincode.
**  WA_DISPLAY-shiptophone = WA_TEMP-ship_to_pho
*   WA_DISPLAY-shiptoemail = WA_TEMP-ship_to_
      CONDENSE: wa_display-documentvalue,
                wa_display-Taxablevalue,
                wa_display-Cgstamount,
                wa_display-sgstamount,
                wa_display-igstamount.
      APPEND wa_display TO lt_display.
    ENDLOOP.
  ENDMETHOD.



  METHOD get_data_for_sd.

    DATA: lt_log           TYPE STANDARD TABLE OF zsd_c_einv_main,
          lt_vbrk          TYPE STANDARD TABLE OF ty_vbrk,
          lv_msg1_can(250) TYPE c, "To be removed
          lv_msg1(250)     TYPE c, "to be removed
          lv_day           TYPE int4,
          lt_vbfa1         TYPE tt_vbfa1,
          lt_vbrp          TYPE TABLE OF ty_vbrp,
          lt_marm          TYPE TABLE OF ty_marm,
          lt_vbfa          TYPE TABLE OF ty_vbfa,
          lt_vbak          TYPE TABLE OF ty_vbak,
          lt_vbpa          TYPE TABLE OF ty_vbpa,
          lt_kna1          TYPE TABLE OF ty_kna1,
          lt_konv          TYPE TABLE OF ty_konv,
          wa_konv          TYPE ty_konv,
          wa_konv_gl       TYPE ty_konv,
          lt_konv_gl       TYPE TABLE OF ty_konv,
          lt_marc          TYPE TABLE OF ty_marc,
          lt_makt          TYPE TABLE OF ty_makt,
          lt_adrc          TYPE TABLE OF ty_adrc,
          lt_adrc1         TYPE TABLE OF ty_adrc,
          lt_j_1bbranch    TYPE tt_j_1bbranch,
          lt_vbrk_cancel   TYPE STANDARD TABLE OF ty_vbrk,
          wa_vbrk_cancel   TYPE ty_vbrk,
          lv_index         TYPE sy-tabix.
    CONSTANTS: c_gl    TYPE char10 VALUE 'ZEI_GL'.
    CONSTANTS: c_b2b   TYPE char10 VALUE 'ZB2B_VTWEG'.
    CONSTANTS: c_fkart TYPE char15 VALUE 'ZFKART_GST5'.
    CONSTANTS: c_exp   TYPE char10 VALUE 'ZEXP_VTWEG'.
    CONSTANTS: c_pac   TYPE char10 VALUE 'ZPAC_VTWEG'.
    REFRESH: lt_vbrk.", LT_VBRP.
**** FETCHING BILL TYPE
    SELECT * FROM zsd_c_billtype
              WHERE 1 = 1 INTO TABLE @DATA(lt_zeinv_billtype). "#EC CI_NOWHERE
**** FETCHING CONDITION TYPE
    SELECT * FROM zsd_c_condition_type                  "#EC CI_NOWHERE
               WHERE 1 = 1 INTO TABLE @DATA(lt_cond).
*** GL DATA
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
             INTO TABLE @DATA(lt_zei_gl)
             WHERE name = @c_gl.
*** DIST CHANNEL
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
           INTO TABLE @DATA(lt_vtweg)
           WHERE name = @c_b2b.
**BILLING TYPE
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
         INTO TABLE @DATA(lt_fkart)
         WHERE name = @c_fkart.
**** EXPORT
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
        INTO TABLE @DATA(lt_exp)
        WHERE name = @c_exp.
**** PAC -> UOM
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
      INTO TABLE @DATA(lt_pac)
      WHERE name = @c_pac.
    IF lt_zeinv_billtype IS NOT INITIAL.
      SELECT a~vbeln,a~fkart,a~vtweg,a~erdat,a~vbtyp,a~fkdat,a~waerk,a~kunag,a~knumv,a~mwsbk,a~netwr,a~bupla,a~bukrs,a~xblnr,a~gjahr,a~kurrf,a~land1,a~sfakn,a~ernam
         FROM zsd_c_vbrk AS a
        INNER JOIN @lt_zeinv_billtype AS b
        ON a~fkart = b~fkart
        WHERE xblnr IN @iv_billingdocument
        AND vbeln IN @iv_billingREFERENCE
        AND fkdat IN @iv_billingdocumentdate
        AND  vtweg IN @lt_vtweg
        AND  rfbsk EQ 'C'
        INTO CORRESPONDING FIELDS OF TABLE @lt_vbrk.
    ENDIF.
    LOOP AT lt_vbrk ASSIGNING FIELD-SYMBOL(<fs_vbrk>).
      <fs_vbrk>-awkey = <fs_vbrk>-vbeln.
    ENDLOOP.
    IF lt_vbrk IS NOT INITIAL.
      SELECT a~bukrs, a~belnr, a~gjahr, a~awkey, a~xblnr_alt
        FROM  zsd_c_bkpf AS a
        INNER JOIN @lt_vbrk AS b
        ON a~bukrs = b~bukrs
        AND a~awkey = b~awkey
        AND a~gjahr = b~gjahr
        INTO TABLE @DATA(lt_bkpf_sd).
      IF lt_bkpf_sd IS NOT INITIAL.
        SELECT a~bukrs, a~belnr, a~gjahr, a~segment
          FROM zsd_c_bseg AS a
          INNER JOIN @lt_bkpf_sd AS b
          ON a~bukrs = b~bukrs
          AND a~belnr = b~belnr
          AND a~gjahr = b~gjahr
          INTO TABLE @DATA(lt_bseg_sd).
        DELETE lt_bseg_sd WHERE segment IS INITIAL.
      ENDIF.
      SELECT a~name, a~gstin, a~adrnr, a~branch, a~bukrs
        FROM zsd_c_j_1bbranch AS a
        INNER JOIN @lt_vbrk AS b
        ON a~branch = b~bupla
        AND a~bukrs = b~bukrs
        INTO CORRESPONDING FIELDS OF TABLE @lt_j_1bbranch.
      IF lt_j_1bbranch IS NOT INITIAL.
        SELECT a~addrnumber,a~name1,a~name2,a~name3,a~name4,a~city1,a~PostCode1,a~street,a~strsuppl1,a~strsuppl3,a~country,a~region
          FROM zsd_c_adrc AS a
          INNER JOIN @lt_j_1bbranch AS b
          ON a~addrnumber = b~adrnr
          INTO CORRESPONDING FIELDS OF TABLE @lt_adrc1.
      ENDIF.
      SELECT * FROM zsd_c_einv_uom WHERE 1 = 1
            INTO TABLE @DATA(lt_uom).                   "#EC CI_NOWHERE
      SELECT a~vbeln, a~vbelv, a~vbtyp_n, a~vbtyp_v
        FROM zsd_c_vbfa AS a
        INNER JOIN @lt_vbrk AS b
        ON a~vbeln = b~vbeln
        WHERE ( a~vbtyp_n = 'M' OR a~vbtyp_n = 'N'  ) AND a~vbtyp_v = 'J'
        INTO CORRESPONDING FIELDS OF TABLE @lt_vbfa1.
      SELECT a~vbeln, a~vbelv, a~vbtyp_n, a~vbtyp_v
        FROM zsd_c_vbfa AS a
        INNER JOIN @lt_vbrk AS b
        ON a~vbeln = b~sfakn
        WHERE ( a~vbtyp_n = 'M' OR a~vbtyp_n = 'N'  ) AND a~vbtyp_v = 'J'
        APPENDING TABLE @lt_vbfa1.
      IF lt_vbfa1 IS NOT INITIAL.
        SELECT a~vbeln, a~vbelv, a~vbtyp_n, a~vbtyp_v
          FROM zsd_c_vbfa AS a
          INNER JOIN @lt_vbfa1 AS b
          ON a~vbeln = b~vbelv
          INTO TABLE @DATA(lt_vbfa2).
        SELECT a~vbeln, a~vstel,a~lgnum
          FROM zsd_c_likp AS a
          INNER JOIN @lt_vbfa1 AS b
          ON a~vbeln = b~vbelv
          INTO TABLE @DATA(lt_likp).
      ENDIF.
      IF lt_likp IS NOT INITIAL.
        SELECT a~warehouse, a~shippingpoint, a~address1,a~address2,a~place, a~pincode
          FROM zsd_c_supply_detail AS a
          INNER JOIN @lt_likp AS b
          ON a~warehouse = b~lgnum
          AND a~shippingpoint = b~vstel
          INTO TABLE @DATA(lt_sadr).
      ENDIF.
      SELECT a~vbeln,a~posnr,a~meins,a~vgbel,a~aubel,a~matnr,a~fkimg,a~vrkme,a~netwr,a~charg,a~werks,a~arktx,a~aupos,a~mwsbp,a~kzwi5,a~umvkn,a~umvkz,a~kzwi2,a~lgort,a~vstel,a~gsber,a~prctr
         FROM zsd_c_vbrp AS a
         INNER JOIN @lt_vbrk AS b
         ON a~vbeln = b~vbeln
         WHERE a~fkimg > 0
         INTO CORRESPONDING FIELDS OF TABLE @lt_vbrp.
      IF lt_vbrp IS NOT INITIAL.
        SELECT a~matnr,a~meinh,a~umren
          FROM zsd_c_marm AS a
          INNER JOIN @lt_vbrp AS b
          ON a~matnr = b~matnr
          INTO CORRESPONDING FIELDS OF TABLE @lt_marm.
        SELECT  a~vbelv,a~vbeln,a~vbtyp_n
          FROM zsd_c_vbfa AS a
          INNER JOIN @lt_vbrp AS b
          ON a~vbelv = b~vgbel
          WHERE a~vbtyp_n = '8'
          INTO CORRESPONDING FIELDS OF TABLE @lt_vbfa.
      ENDIF.
      SELECT a~vbeln, a~bstnk,a~bstdk
         FROM zsd_c_vbak AS a
        INNER JOIN @lt_vbrk AS b
        ON a~vbeln = b~vbeln
        INTO CORRESPONDING FIELDS OF TABLE @lt_vbak.
      SELECT a~vbeln,a~posnr,a~parvw,a~kunnr,a~adrnr
              FROM zsd_c_vbpa AS a
             INNER JOIN @lt_vbrk AS b
             ON a~vbeln = b~vbeln
        INTO CORRESPONDING FIELDS OF TABLE @lt_vbpa.
      "ADDED BY YASH
      SELECT a~vbeln,a~posnr,a~parvw,a~kunnr,a~adrnr
              FROM zsd_c_vbpa AS a
             INNER JOIN @lt_vbrk AS b
             ON a~vbeln = b~vbeln
        WHERE b~fkart = 'ZEX'
        APPENDING TABLE @lt_vbpa.
      "END OF YASH
      IF lt_vbpa IS NOT INITIAL.
        SELECT a~kunnr,a~land1,a~name1,a~name2,a~adrnr,a~stcd3,a~regio,a~stras,a~ort01,a~pstlz
          FROM zsd_c_kna1 AS a
          INNER JOIN @lt_vbpa AS b
          ON a~kunnr = b~kunnr
          INTO CORRESPONDING FIELDS OF TABLE @lt_kna1.
      ENDIF.
      IF lt_vbrk IS NOT INITIAL.
        SELECT  a~knumv,a~kposn,a~kschl,a~kawrt,a~kbetr,a~kwert,a~sakn1,a~kvsl1
          FROM zsd_c_prcd_elements AS a
          INNER JOIN @lt_vbrk AS b
          ON a~knumv = b~knumv
          INTO CORRESPONDING FIELDS OF TABLE @lt_konv.
      ENDIF.
*require for GL fetch
      SORT lt_zei_gl BY low.
      LOOP AT lt_konv INTO wa_konv WHERE sakn1 NE ''.
        IF line_exists( lt_zei_gl[ low = wa_konv-kvsl1 ] ).
          MOVE-CORRESPONDING wa_konv TO wa_konv_gl.
          APPEND wa_konv_gl TO lt_konv_gl.
        ENDIF.
        CLEAR: wa_konv,wa_konv_gl.
      ENDLOOP.
      IF lt_vbrp IS NOT INITIAL.
        SELECT a~matnr,a~werks,a~steuc
          FROM zsd_c_marc AS a
          INNER JOIN @lt_vbrp AS b
          ON a~matnr = b~matnr
          AND a~werks = b~werks
          INTO CORRESPONDING FIELDS OF TABLE @lt_marc.
        SELECT a~matnr,
               a~maktx
               FROM zsd_c_makt AS a
               INNER JOIN @lt_vbrp AS b
               ON a~matnr = b~matnr
               WHERE a~spras = 'E'
               INTO CORRESPONDING FIELDS OF TABLE @lt_makt.
      ENDIF.
      IF lt_vbpa IS NOT INITIAL.
        SELECT a~addrnumber,a~name1,a~name2,a~name3,a~name4,a~city1,a~postcode1,a~street,a~strsuppl1,a~strsuppl3,a~country,a~region
          FROM zsd_c_adrc AS a
          INNER JOIN @lt_vbpa AS b
          ON a~addrnumber = b~adrnr
          INTO CORRESPONDING FIELDS OF TABLE @lt_adrc.
        IF lt_kna1 IS NOT INITIAL.
          SELECT a~addrnumber,a~name1,a~name2,a~name3,a~name4,a~city1,a~postcode1,a~street,a~strsuppl1,a~strsuppl3,a~country,a~region
            FROM zsd_c_adrc AS a
            INNER JOIN @lt_kna1 AS b
            ON a~addrnumber = b~adrnr
            APPENDING CORRESPONDING FIELDS OF TABLE @lt_adrc.
        ENDIF.
      ENDIF.
      set_data_for_sd( EXPORTING it_fkart          = lt_fkart
                                 it_pac            = lt_pac
                                 it_cond           = lt_cond
                      IMPORTING  et_final          = et_final
                      CHANGING   ct_exp            = lt_exp
                                 ct_vtweg          = lt_vtweg
                                 ct_vbrk           = lt_vbrk
                                 ct_vbrp           = lt_vbrp
                                 ct_konv           = lt_konv
                                 ct_marm           = lt_marm
                                 ct_uom            = lt_uom
                                 ct_konv_gl        = lt_konv_gl
                                 ct_likp           = lt_likp
                                 ct_vbfa1          = lt_vbfa1
                                 ct_sadr           = lt_sadr
                                 ct_adrc1          = lt_adrc1
                                 ct_j_1bbranch     = lt_j_1bbranch
                                 ct_vbpa           = lt_vbpa
                                 ct_kna1           = lt_kna1
                                 ct_adrc           = lt_adrc
                                 ct_makt           = lt_makt
                                 ct_marc           = lt_marc
                                 ct_bkpf_sd        = lt_bkpf_sd
                                 ct_bseg_sd        = lt_bseg_sd
*                               ct_cyg_prc        = lt_c
                                 ct_zeinv_billtype = lt_zeinv_billtype ).
    ENDIF.
  ENDMETHOD.



  METHOD set_data_for_sd.

    TYPES: BEGIN OF ty_item_gst,
             posnr TYPE vbrp-posnr,
             knumv TYPE vbrk-knumv,
             igst  TYPE konv-kwert,
             cgst  TYPE konv-kwert,
             sgst  TYPE konv-kwert,
           END OF ty_item_gst,
           BEGIN OF ty_final,
             inv_type_code            TYPE string,
             inv_subtyp_code          TYPE string,
             inv_num                  TYPE string,
             fi_inv_num               TYPE string,
             inv_date                 TYPE string,
             irn                      TYPE string,
             irn_success              TYPE string,
             irn_cancel               TYPE string,
             invoice_currency_code    TYPE string,
             reversecharge            TYPE string,
             igst_applicability       TYPE string,
             inv_start_date           TYPE string,
             inv_end_date             TYPE string,
             pre_inv_num              TYPE string,
             pre_inv_date             TYPE string,
             supplier_legal_name      TYPE string,
             supplier_gstin           TYPE string,
             supplier_address1        TYPE string,
             supplier_address2        TYPE string,
             supplier_city            TYPE string,
             supplier_state           TYPE string,
             supplier_pincode         TYPE string,
             billing_name             TYPE string,
             billing_gstin            TYPE string,
             billing_pos              TYPE string,
             billing_address1         TYPE string,
             billing_address2         TYPE string,
             billing_place            TYPE string,
             billing_state            TYPE string,
             billing_pincode          TYPE string,
             country_of_export        TYPE string,
             shippingto_name          TYPE string,
             shippingto_gstin         TYPE string,
             shippingto_address1      TYPE string,
             shippingto_address2      TYPE string,
             shippingto_place         TYPE string,
             shippingto_pincode       TYPE string,
             shippingto_state         TYPE string,
             slno                     TYPE string,
             item_description         TYPE string,
             isservice                TYPE string,
             hsncode                  TYPE string,
             batch_num                TYPE string,
             batchexpirydate          TYPE vfdat,
             quantity1                TYPE bseg-menge,
             freeqty                  TYPE string,
             uqc                      TYPE string,
             rate                     TYPE bseg-dmbtr,
             grossamount              TYPE bseg-dmbtr,
             discountamount           TYPE bseg-dmbtr,
             gst_rate                 TYPE string,
             iamt                     TYPE bseg-dmbtr,
             camt                     TYPE bseg-dmbtr,
             samt                     TYPE bseg-dmbtr,
             itemtotal                TYPE bseg-dmbtr,
             taxable_value            TYPE bseg-dmbtr,
             igstvalue                TYPE bseg-dmbtr,
             cgstvalue                TYPE bseg-dmbtr,
             sgstvalue                TYPE bseg-dmbtr,
             roundoff                 TYPE bseg-dmbtr,
             tot_inv_value_inr        TYPE bseg-dmbtr,
             total_invoice_value_fcnr TYPE bseg-dmbtr,
             tax_scheme               TYPE string,
             total_taxable_value      TYPE bseg-dmbtr,
             item_total_amount        TYPE bseg-dmbtr,
             dncc                     TYPE string,
             tcs                      TYPE string,
             gl_account               TYPE string,
             lgort                    TYPE string,
             vstel                    TYPE string,
             bupla                    TYPE string,
             item_name                TYPE string,
             currency                 TYPE string,
             countrycode              TYPE string,
             cancel_by                TYPE string,
             cancel_date              TYPE string,
             fail                     TYPE string,
             error                    TYPE string,
             ewaybill                 TYPE string,
             custom1                  TYPE string,
             custom2                  TYPE string,
             custom3                  TYPE string,
             custom4                  TYPE string,
             custom5                  TYPE string,
             custom6                  TYPE string,
             custom7                  TYPE string,
             custom8                  TYPE string,
             custom9                  TYPE string,
             custom10                 TYPE string,
             custom11                 TYPE string,
             custom12                 TYPE string,
             custom13                 TYPE string,
             custom14                 TYPE string,
             branch                   TYPE vbrp-werks,
             bukrs                    TYPE vbrk-bukrs,
           END OF ty_final,
           BEGIN OF ty_gst,
             vbeln TYPE vbrk-vbeln,
             cgst  TYPE string,
             sgst  TYPE string,
             igst  TYPE string,
           END OF ty_gst.

    DATA : lv_counter     TYPE i VALUE 0,
           hsn_temp1(20)  TYPE c,
           sdtemp_rate    TYPE kzwi2,
           lv_flag_t(1)   TYPE c,
           lv_kunnr       TYPE kunnr,
           lv_name        TYPE thead-tdname,
           lv_cgst        TYPE konv-kwert,
           lv_igst        TYPE konv-kwert,
           lv_sgust       TYPE konv-kwert,
           lv_cgst_rt     TYPE konv-kbetr,
           lv_sgust_rt    TYPE konv-kbetr,
           lv_kbetr2      TYPE konv-kbetr,
           lv_taxc        TYPE kwert,
           lv_taxi        TYPE kwert,
           lv_round       TYPE string,
           lv_cgst_total  TYPE string,
           lv_sgst_total  TYPE string,
           lv_igst_total  TYPE string,
           lv_kunnr1      TYPE vbpa-kunnr,
           lv_netwr       TYPE netwr,
           wa_item_gst    TYPE ty_item_gst,
           lt_item_gst    TYPE TABLE OF ty_item_gst,
           wa_final       TYPE ty_final,
           wa_vbrk        TYPE ty_vbrk,
           lt_outtab      TYPE STANDARD TABLE OF ty_output,
           wa_outtab      TYPE ty_output,
           wa_konv        TYPE ty_konv,
           wa_address     TYPE bapiaddr3,
           lt_address1    TYPE STANDARD TABLE OF bapiaddr3,
           wa_gst         TYPE ty_gst,
           lt_gst         TYPE TABLE OF ty_gst,
           lt_ret1        TYPE TABLE OF bapiret2,
           lv_vbeln_slno  TYPE vbrp-vbeln,
*        ls_zeinv_billtype TYPE zsd_t_billtype,
           wa_konv_gl     TYPE ty_konv,
           wa_j_1brranch  LIKE LINE OF ct_j_1bbranch,
           wa_vbfa1       TYPE ty_vbfa1,
           wa_uom         LIKE LINE OF ct_uom,
           wa_likp        TYPE ty_likp,
           wa_vbpa        LIKE LINE OF ct_vbpa,
           wa_adrc        LIKE LINE OF ct_adrc,
           wa_marc        LIKE LINE OF ct_marc,
           wa_makt        LIKE LINE OF ct_makt,
           wa_kna1        LIKE LINE OF ct_kna1,
           wa_sadr        LIKE LINE OF ct_sadr,
           wa_adrc1       LIKE LINE OF ct_adrc1,
           lv_tot_inv_val TYPE netwr.
    CONSTANTS: c_cgst     TYPE char10 VALUE 'CGST',
               c_sgst     TYPE char10 VALUE 'SGST',
               c_igst     TYPE char10 VALUE 'IGST',
               c_b2b1     TYPE char10 VALUE 'B2B',
               c_b2c      TYPE char10 VALUE 'B2C',
               c_legal    TYPE char100 VALUE 'PAGE INDUSTRIES LIMITED',
               c_state1   TYPE char10 VALUE '99',
               c_n        TYPE char10 VALUE 'N',
               c_y        TYPE char10 VALUE 'Y',
               c_pac1     TYPE char10 VALUE 'PAC',
               c_pc       TYPE char10 VALUE 'PC',
               c_box      TYPE char10 VALUE 'BOX',
               c_language TYPE thead-tdspras VALUE 'E',
               c_id       TYPE thead-tdid VALUE '0012',
               c_expin    TYPE char10 VALUE '999999',
               c_crn      TYPE char10 VALUE 'CRN',
               c_urp      TYPE char10 VALUE 'URP',
               c_state    TYPE char10 VALUE '96',
               c_expwop   TYPE char10 VALUE 'EXPWOP',
               c_expwp    TYPE char10 VALUE 'EXPWP'.
    SORT ct_vbrp BY vbeln posnr.
    SORT ct_vbrk BY vbeln.
* REFRESH lt_outtab.
* ----------------Prepare outtab----------------------*
    LOOP AT it_cond INTO DATA(wa_cond).
      CLEAR wa_konv.
      wa_konv-gsp_tax = wa_cond-category.
      MODIFY ct_konv FROM wa_konv TRANSPORTING gsp_tax WHERE kschl = wa_cond-conditiontype.
      CLEAR: wa_cond,wa_konv.
    ENDLOOP.
    SORT ct_konv BY knumv kposn gsp_tax.
    SORT ct_marm BY meinh matnr.
    IF ct_vbrp IS NOT INITIAL.
      LOOP AT ct_vbrp INTO DATA(wa_vbrp)."WHERE VBELN = ls_vbrk-VBELN.
        CLEAR wa_vbrk.
        READ TABLE ct_vbrk INTO wa_vbrk WITH KEY vbeln = wa_vbrp-vbeln BINARY SEARCH.
        IF sy-subrc = 0.
          wa_item_gst-posnr = wa_vbrp-posnr.
          wa_item_gst-knumv = wa_vbrk-knumv.
          CLEAR wa_konv.
          READ TABLE ct_konv INTO wa_konv
                                WITH KEY knumv = wa_vbrk-knumv kposn = wa_vbrp-posnr
                                         gsp_tax = c_cgst BINARY SEARCH.
          IF sy-subrc EQ 0.
            wa_item_gst-cgst = wa_konv-kwert.
*         lv_cgst_rt = ( ls_konv-kbetr / 10 ) .
            lv_cgst_rt = wa_konv-kbetr.
            lv_cgst = wa_konv-kbetr + lv_cgst.
            lv_taxc = lv_taxc + wa_konv-kawrt.
            IF wa_konv-kwert IS NOT INITIAL.
              lv_cgst_total = wa_konv-kwert + lv_cgst_total.
            ENDIF.
          ENDIF.
          CLEAR wa_cond.
          CLEAR wa_konv.
          READ TABLE ct_konv INTO wa_konv
                                WITH KEY knumv = wa_vbrk-knumv kposn = wa_vbrp-posnr
                                         gsp_tax = c_sgst BINARY SEARCH.
          IF sy-subrc EQ 0.
            wa_item_gst-sgst = wa_konv-kwert.
*         lv_sgust_rt = ( ls_konv-kbetr / 10 ) .
            lv_sgust_rt = wa_konv-kbetr.
            lv_sgust = wa_konv-kbetr + lv_sgust.
            lv_taxc = lv_taxc + wa_konv-kawrt.
            IF wa_konv-kwert IS NOT INITIAL.
              lv_sgst_total = wa_konv-kwert + lv_sgst_total.
            ENDIF.
          ENDIF.
          CLEAR wa_cond.
          CLEAR wa_konv.
          READ TABLE ct_konv INTO wa_konv
                                 WITH KEY knumv = wa_vbrk-knumv kposn = wa_vbrp-posnr
                                          gsp_tax = c_igst BINARY SEARCH.
          IF sy-subrc EQ 0.
            wa_item_gst-igst = wa_konv-kwert.
            lv_kbetr2 = wa_konv-kbetr.
            lv_igst = wa_konv-kbetr + lv_igst.
            lv_taxi = lv_taxi + wa_konv-kawrt.
            IF wa_konv-kwert IS NOT INITIAL.
              lv_igst_total = wa_konv-kwert + lv_igst_total.
            ENDIF.
          ENDIF.
          CLEAR wa_cond.
          APPEND wa_item_gst TO lt_item_gst.
          CLEAR wa_item_gst.
          wa_outtab-matnr = wa_vbrp-matnr.
          wa_outtab-menge = wa_vbrp-fkimg.
          IF wa_vbrk-fkart EQ 'EF2'.
            READ TABLE ct_marm INTO DATA(wa_marm) WITH KEY matnr = wa_vbrp-matnr meinh = 'PC' BINARY SEARCH.
            IF sy-subrc = 0.
              wa_outtab-menge = wa_vbrp-fkimg * wa_marm-umren.
            ENDIF.
          ENDIF.
          wa_outtab-posnr = wa_vbrp-posnr.
          wa_outtab-vbeln = wa_vbrp-vbeln.
          wa_outtab-werks = wa_vbrp-werks.
          wa_outtab-vrkme = wa_vbrp-vrkme.
          wa_outtab-netwr = wa_vbrp-netwr.
          wa_outtab-netwr_itemlevel = wa_vbrp-netwr.
          wa_outtab-mwsbp = wa_vbrp-mwsbp.
          wa_outtab-vgbel = wa_vbrp-vgbel.
          wa_outtab-cgst = lv_cgst.
          wa_outtab-cgst_rt = lv_cgst_rt.
          wa_outtab-sugst = lv_sgust.
          wa_outtab-igst_rt = lv_kbetr2.
          wa_outtab-igst = lv_igst.
          wa_outtab-sugst_rt = lv_sgust_rt.
          wa_outtab-lgort = wa_vbrp-lgort.
          wa_outtab-vstel = wa_vbrp-vstel.
          CALL FUNCTION 'BAPI_USER_GET_DETAIL'
            EXPORTING
              username = wa_vbrk-ernam
            IMPORTING
              address  = wa_address
            TABLES
              return   = lt_ret1.
          APPEND wa_address TO lt_address1.
          IF sy-subrc = 0.
            wa_final-custom14 = lt_address1[ 1 ]-e_mail.
          ENDIF.
          APPEND wa_outtab TO lt_outtab.
          CLEAR: wa_outtab, wa_konv, wa_vbrp.
          AT END OF vbeln.
            MOVE wa_vbrk-vbeln TO wa_gst-vbeln.
            wa_gst-igst = lv_igst_total.
            MOVE lv_cgst_total TO wa_gst-cgst.
            MOVE lv_sgst_total TO wa_gst-sgst.
            APPEND wa_gst TO lt_gst.
            CLEAR: wa_gst, lv_cgst_total, lv_sgst_total, lv_igst_total.
            CLEAR: wa_vbrk.
            CLEAR: lv_cgst,
               lv_cgst_rt, lv_sgust, lv_sgust, lv_igst,
               wa_outtab-cgst, wa_outtab-cgst_rt, wa_outtab-sugst, wa_outtab-igst_rt,
               wa_outtab-igst, wa_outtab-sugst_rt, lv_taxc, lv_taxi.
            CLEAR: wa_konv, wa_vbrp, wa_vbrk.
          ENDAT.
        ENDIF.
      ENDLOOP.
    ENDIF.
    SORT ct_vbrk BY vbeln.
    SORT ct_vbrp BY vbeln.
    SORT lt_outtab BY vbeln.
    SORT ct_uom BY sapuom.
    SORT ct_konv_gl BY knumv.
    SORT ct_zeinv_billtype BY fkart.
    SORT ct_likp BY vbeln.
    SORT ct_vbfa1 BY vbeln vbtyp_n vbtyp_v.
    SORT ct_sadr BY warehouse shippingpoint.
    SORT ct_likp BY vbeln.
    SORT ct_adrc1 BY addrnumber.
    SORT ct_j_1bbranch BY branch.
    SORT ct_vbpa BY vbeln parvw.
    SORT ct_kna1 BY kunnr.
    SORT ct_adrc BY addrnumber.
    SORT ct_makt BY matnr.
    SORT ct_marc BY matnr werks.
    SORT lt_gst BY vbeln.
    SORT ct_kna1 BY kunnr.
    SORT lt_item_gst BY posnr knumv.
    SORT ct_vtweg BY low.
    SORT ct_bkpf_sd BY bukrs gjahr awkey.
    SORT ct_bseg_sd BY bukrs gjahr belnr.
    SORT ct_exp BY low.
    DELETE ct_konv_gl WHERE sakn1 IS INITIAL.
    IF ct_vbrp IS NOT INITIAL.
      "added by yash
      DATA : lr_werks TYPE RANGE OF vbrp-werks.
      LOOP AT ct_vbrp ASSIGNING FIELD-SYMBOL(<fs_vbrp>).
        IF <fs_vbrp>-werks IS NOT INITIAL.
          APPEND VALUE #( sign = 'I' option = 'EQ' low = <fs_vbrp>-werks ) TO lr_werks.
        ENDIF.
      ENDLOOP.
      SORT lr_werks BY low.
      DELETE ADJACENT DUPLICATES FROM lr_werks COMPARING low.
      "BILL FROM
      SELECT
      c~kunnr,c~land1,c~name1,c~name2,c~adrnr,c~stcd3,c~regio,c~stras,c~ort01,c~pstlz,
      a~addrnumber,a~name3,a~name4,a~city1,a~PostCode1,a~street,a~strsuppl1,strsuppl2,a~strsuppl3,a~country,a~region
      FROM zsd_c_kna1 AS c
      INNER JOIN zsd_c_adrc AS a
      ON a~addrnumber = c~adrnr
      WHERE c~kunnr IN @lr_werks
      INTO TABLE @DATA(lt_billfrom).
      "ended by yash
    ENDIF.
    IF ct_vbrk IS NOT INITIAL.
      SELECT vbeln,
             fkart,
             vtweg,
             erdat,
             vbtyp,
             fkdat,
             waerk,
             kunag,
             knumv,
             mwsbk,
             netwr,
             bupla,
             bukrs,
             xblnr,
             gjahr,
             kurrf,
             land1,
             sfakn FROM zsd_c_vbrk
                   INTO TABLE @DATA(lt_vbrk_can)
                   FOR ALL ENTRIES IN @ct_vbrk
                   WHERE vbeln = @ct_vbrk-sfakn.
      LOOP AT lt_outtab INTO wa_outtab.
        wa_final-lgort = wa_outtab-lgort.
        wa_final-vstel = wa_outtab-vstel.
        IF wa_outtab-vbeln = lv_vbeln_slno.
          lv_counter = lv_counter + 1.
        ELSE.
          lv_counter = 0.
          lv_counter = lv_counter + 1.
        ENDIF.
        MOVE lv_counter TO wa_final-slno.
        CLEAR wa_vbrk.
        READ TABLE ct_vbrk INTO wa_vbrk WITH KEY vbeln = wa_outtab-vbeln BINARY SEARCH.
        IF sy-subrc = 0.
          CLEAR: wa_konv, wa_konv_gl.
          wa_final-custom4 = wa_vbrk-bupla.
          wa_final-custom12 = wa_vbrk-fkart.
          wa_final-custom1 = wa_vbrk-vbeln.
          CLEAR wa_vbrp.
          CLEAR: wa_final-custom2.
          READ TABLE ct_vbrp INTO wa_vbrp WITH KEY vbeln = wa_outtab-vbeln BINARY SEARCH.
          IF sy-subrc = 0.
            wa_final-custom2 = wa_vbrp-werks.
*         ls_final-custom1 = ls_vbrp-vbeln.
*         l_str_final-custom3 = l_str_bseg-segment.
            wa_final-custom5 = wa_vbrp-gsber.
            wa_final-custom4 = wa_vbrk-bupla.
            wa_final-custom10 = 'SD'.
            READ TABLE ct_bkpf_sd INTO DATA(wa_bkpf_sd) WITH KEY bukrs = wa_vbrk-bukrs gjahr = wa_vbrk-gjahr awkey = wa_vbrk-awkey BINARY SEARCH.
            IF sy-subrc = 0.
              IF wa_vbrk-fkart = 'ZG'.
                wa_final-inv_num = wa_bkpf_sd-xblnr_alt.
              ENDIF.
              wa_final-fi_inv_num = wa_bkpf_sd-belnr.
              READ TABLE ct_bseg_sd INTO DATA(wa_bseg_sd) WITH KEY bukrs = wa_bkpf_sd-bukrs gjahr = wa_bkpf_sd-gjahr belnr = wa_bkpf_sd-belnr.
              IF sy-subrc = 0.
                wa_final-custom3 = wa_bseg_sd-segment.
              ENDIF.
            ENDIF.
          ENDIF.
          READ TABLE ct_konv_gl INTO wa_konv_gl
                                     WITH KEY knumv = wa_vbrk-knumv BINARY SEARCH.
          IF sy-subrc = 0.
            wa_final-gl_account = wa_konv_gl-sakn1.
          ENDIF.
          READ TABLE ct_vtweg TRANSPORTING NO FIELDS WITH KEY low = wa_vbrk-vtweg BINARY SEARCH.
          IF sy-subrc = 0.
            wa_final-inv_type_code = c_b2b1.
          ELSE.
            wa_final-inv_type_code = c_b2c.
          ENDIF.
*         --------------Invoice subtype/ Document type-------------------*
*         CLEAR ls_zeinv_billtype.
          READ TABLE ct_zeinv_billtype INTO DATA(wa_zeinv_billtype) WITH KEY fkart = wa_vbrk-fkart BINARY SEARCH.
          IF sy-subrc = 0.
            MOVE wa_zeinv_billtype-doc_type TO wa_final-inv_subtyp_code.
            IF wa_vbrk-fkart = 'ZS1' OR wa_vbrk-fkart = 'S2'.
              wa_final-cancel_date = |{ sy-datum+6(2) } { sy-datum+4(2) } { sy-datum+0(4) }|.
            ENDIF.
          ENDIF.
          CLEAR wa_zeinv_billtype.
*         -------------------Document_Num--------------------------*
          MOVE wa_vbrk-xblnr TO wa_final-inv_num.
          SHIFT wa_final-inv_num LEFT DELETING LEADING '0'.
*         -----------------Document_Date-----------------------*
          CONCATENATE wa_vbrk-fkdat+6(2) wa_vbrk-fkdat+4(2) wa_vbrk-fkdat+0(4)
          INTO wa_final-inv_date SEPARATED BY '-'.
          wa_final-bupla = wa_vbrk-bupla.
          IF wa_final-inv_subtyp_code = c_crn.
            MOVE wa_vbrk-sfakn TO wa_final-pre_inv_num..
            READ TABLE lt_vbrk_can INTO DATA(wa_vbrk_can) WITH KEY vbeln = wa_final-pre_inv_num.
            IF sy-subrc = 0.
              IF wa_vbrk_can-fkdat IS NOT INITIAL.
                CONCATENATE wa_vbrk_can-fkdat+6(2) wa_vbrk_can-fkdat+4(2) wa_vbrk_can-fkdat+0(4)
                INTO wa_final-pre_inv_date SEPARATED BY '/'.
                CLEAR: wa_vbrk_can.
              ENDIF.
            ENDIF.
          ENDIF.
*         ----------------------Supplier details---------------------*
          wa_final-supplier_legal_name = c_legal.
          IF wa_final-inv_subtyp_code = c_crn.
            CLEAR wa_vbfa1.
            READ TABLE ct_vbfa1 INTO wa_vbfa1 WITH KEY vbeln = wa_vbrk-sfakn vbtyp_n = 'M' vbtyp_v = 'J' BINARY SEARCH.
            IF sy-subrc = 0.
              CLEAR wa_likp.
              READ TABLE ct_likp INTO wa_likp WITH KEY vbeln = wa_vbfa1-vbelv BINARY SEARCH.
              IF sy-subrc = 0.
                CLEAR wa_sadr.
                READ TABLE ct_sadr INTO wa_sadr WITH KEY warehouse = wa_likp-lgnum shippingpoint = wa_likp-vstel BINARY SEARCH.
                IF sy-subrc = 0.
                  MOVE wa_sadr-address1 TO wa_final-supplier_address1.
                  MOVE wa_sadr-address2 TO wa_final-supplier_address2.
                  MOVE wa_sadr-place TO wa_final-supplier_city.
                  MOVE wa_sadr-pincode TO wa_final-supplier_pincode.
                ENDIF.
              ENDIF.
            ENDIF.
          ELSE.
            READ TABLE lt_billfrom INTO DATA(wa_billfrom) WITH KEY kunnr = wa_outtab-werks.
            IF sy-subrc = 0.
              wa_final-supplier_address1 = wa_billfrom-street.
              wa_final-supplier_address2 = wa_billfrom-strsuppl1.
              wa_final-supplier_city = wa_billfrom-city1.
              wa_final-supplier_pincode = wa_billfrom-postcode1.
            ENDIF.
*         CLEAR WA_VBFA1.
*         READ TABLE ct_vbfa1 INTO WA_VBFA1 WITH KEY vbeln = WA_VBRK-vbeln vbtyp_n = 'M' vbtyp_v = 'J' BINARY SEARCH.
*         IF sy-subrc = 0.
*           CLEAR WA_LIKP.
*           READ TABLE ct_likp INTO WA_LIKP WITH KEY vbeln = WA_VBFA1-vbelv BINARY SEARCH.
*           IF sy-subrc = 0.
*             WA_FINAL-custom9 = WA_LIKP-lgnum.
*             CLEAR WA_SADR.
*             READ TABLE ct_sadr INTO WA_SADR WITH KEY warehouse = WA_LIKP-lgnum shippingpoint = WA_LIKP-vstel BINARY SEARCH.
*             IF sy-subrc = 0.
*               MOVE WA_SADR-address1 TO WA_FINAL-supplier_address1.
*               MOVE WA_SADR-address2 TO WA_FINAL-supplier_address2.
*               MOVE WA_SADR-place TO WA_FINAL-supplier_city.
*               MOVE WA_SADR-pincode TO WA_FINAL-supplier_pincode.
*             ENDIF.
*           ENDIF.
*         ENDIF.
          ENDIF.
          CLEAR wa_j_1brranch.
          READ TABLE ct_j_1bbranch INTO wa_j_1brranch WITH KEY branch = wa_vbrk-bupla BINARY SEARCH. "bukrs = ls_vbrk-bukrs.
          IF sy-subrc IS INITIAL.
            MOVE wa_j_1brranch-gstin TO wa_final-supplier_gstin.
            CLEAR wa_adrc1.
            READ TABLE ct_adrc1 INTO wa_adrc1 WITH KEY addrnumber = wa_j_1brranch-adrnr BINARY SEARCH.
            IF sy-subrc IS INITIAL.
              IF wa_final-supplier_address1 IS INITIAL.
                MOVE wa_adrc1-street TO wa_final-supplier_address1.
              ENDIF.
              IF wa_final-supplier_pincode IS INITIAL.
                MOVE wa_adrc1-postcode1 TO wa_final-supplier_pincode.
              ENDIF.
              IF wa_final-supplier_city IS INITIAL.
                MOVE wa_adrc1-name2 TO wa_final-supplier_city.
              ENDIF.
              IF wa_final-supplier_city IS INITIAL.
                MOVE wa_adrc1-city1 TO wa_final-supplier_city.
              ENDIF.
              wa_final-supplier_state = wa_adrc1-region.
            ENDIF.
          ENDIF.
*         ---------------------Billing details / recipient details---------------------*
*         IF ls_vbrk-fkart <> 'ZEX'.
          CLEAR wa_vbpa.
          READ TABLE ct_vbpa INTO wa_vbpa WITH KEY vbeln = wa_vbrk-vbeln parvw = 'AG' BINARY SEARCH.
          IF sy-subrc = 0.
            CLEAR lv_kunnr1.
            lv_kunnr1 = wa_vbpa-kunnr.
            wa_final-custom11 = wa_vbpa-kunnr.
            CLEAR wa_kna1.
            READ TABLE ct_kna1 INTO wa_kna1 WITH KEY kunnr = wa_vbpa-kunnr.
            IF sy-subrc IS INITIAL.
              READ TABLE ct_exp TRANSPORTING NO FIELDS WITH KEY low = wa_vbrk-vtweg BINARY SEARCH.
              IF sy-subrc = 0.
                IF wa_outtab-igst IS NOT INITIAL.
                  wa_final-inv_type_code = c_expwp.
                ELSE.
                  wa_final-inv_type_code = c_expwop.
                ENDIF.
                wa_final-currency = wa_vbrk-waerk.
                wa_final-countrycode = wa_kna1-land1.
                wa_final-billing_gstin = c_urp.
                wa_final-billing_pos = c_state.
                wa_final-billing_state = c_state.
              ELSE.
                MOVE wa_kna1-stcd3 TO wa_final-billing_gstin.
                wa_final-billing_pos = wa_kna1-regio.
                wa_final-billing_state = wa_kna1-regio.
              ENDIF.
              lv_kunnr = |{ wa_vbpa-kunnr ALPHA = IN }|.
              lv_name = lv_kunnr.
              DATA(lt_lines) = read_text( iv_id = c_id
                                          iv_language = c_language
                                          iv_name = lv_name
                                          iv_object = 'KNA1' ).
              TRY.
                  wa_final-billing_name = lt_lines[ 1 ]-tdline.
                CATCH cx_sy_itab_line_not_found.
              ENDTRY.
              CLEAR: lv_kunnr,lv_name.
              CLEAR wa_adrc.
              READ TABLE ct_adrc INTO wa_adrc WITH KEY addrnumber = wa_kna1-adrnr BINARY SEARCH.
              IF sy-subrc = 0.
                MOVE wa_adrc-name1 TO wa_final-billing_name.
                MOVE wa_adrc-street TO wa_final-billing_address1.
                MOVE wa_adrc-strsuppl3 TO wa_final-billing_address2.
                MOVE wa_adrc-city1 TO wa_final-billing_place.
                MOVE wa_adrc-postcode1 TO wa_final-billing_pincode.
                READ TABLE ct_exp TRANSPORTING NO FIELDS WITH KEY low = wa_vbrk-vtweg BINARY SEARCH.
                IF sy-subrc = 0.
                  wa_final-billing_pincode = c_expin.
                ENDIF.
              ENDIF.
            ENDIF.
          ENDIF.
*         ELSEIF ls_vbrk-fkart = 'ZEX'.
*           CLEAR WA_VBPA.
*           READ TABLE ct_vbpa INTO WA_VBPA WITH KEY vbeln = WA_VBRK-vbeln parvw = 'AG' BINARY SEARCH.
*           IF sy-subrc = 0.
*             CLEAR LV_KUNNR1.
*             LV_KUNNR1 = WA_VBPA-kunnr.
*             WA_FINAL-custom11 = WA_VBPA-kunnr.
*             CLEAR WA_KNA1.
*             READ TABLE ct_kna1 INTO WA_KNA1 WITH KEY kunnr = WA_VBPA-kunnr.
*             IF sy-subrc IS INITIAL.
*               READ TABLE ct_exp TRANSPORTING NO FIELDS WITH KEY low = WA_VBRK-vtweg BINARY SEARCH.
*               IF sy-subrc = 0.
*                 IF WA_OUTTAB-igst IS NOT INITIAL.
*                   WA_FINAL-inv_type_code = C_EXPWP.
*                 ELSE.
*                   WA_FINAL-inv_type_code = C_EXPWOP.
*                 ENDIF.
*                 WA_FINAL-currency = WA_VBRK-waerk.
*                 WA_FINAL-countrycode = WA_KNA1-land1.
*                 WA_FINAL-billing_gstin = C_URP.
*                 WA_FINAL-billing_pos = C_STATE.
*                 WA_FINAL-billing_state = C_STATE.
*               ELSE.
*                 MOVE WA_KNA1-stcd3 TO WA_FINAL-billing_gstin.
*                 WA_FINAL-billing_pos = WA_KNA1-regio.
*                 WA_FINAL-billing_state = WA_KNA1-regio.
*               ENDIF.
*               LV_KUNNR = |{ WA_VBPA-kunnr ALPHA = IN }|.
*               LV_NAME = LV_KUNNR.
*               LT_LINES = read_text( iv_id = C_ID
*                                     iv_language = C_LANGUAGE
*                                     iv_name = LV_NAME
*                                     iv_object = 'KNA1' ).
*               TRY.
*                   WA_FINAL-billing_name = LT_LINES[ 1 ]-tdline.
*                 CATCH cx_sy_itab_line_not_found.
*               ENDTRY.
*               CLEAR: LV_KUNNR,LV_NAME.
*               CLEAR WA_ADRC.
*               READ TABLE ct_adrc INTO WA_ADRC WITH KEY addrnumber = WA_KNA1-adrnr BINARY SEARCH.
*               IF sy-subrc = 0.
*                 MOVE WA_ADRC-name1 TO WA_FINAL-billing_name.
*                 MOVE WA_ADRC-street TO WA_FINAL-billing_address1.
*                 MOVE WA_ADRC-strsuppl3 TO WA_FINAL-billing_address2.
*                 MOVE WA_ADRC-city1 TO WA_FINAL-billing_place.
*                 MOVE WA_ADRC-postcode1 TO WA_FINAL-billing_pincode.
*                 READ TABLE ct_exp TRANSPORTING NO FIELDS WITH KEY low = WA_VBRK-vtweg BINARY SEARCH.
*                 IF sy-subrc = 0.
*                   WA_FINAL-billing_pincode = C_EXPIN.
*                 ENDIF.
*               ENDIF.
*             ENDIF.
*           ENDIF.
*         ----------------------Country_of_Export----------------------------*
          READ TABLE ct_exp TRANSPORTING NO FIELDS WITH KEY low = wa_vbrk-vtweg BINARY SEARCH.
          IF sy-subrc = 0.
            wa_final-country_of_export = wa_vbrk-land1.
          ENDIF.
*         ----------------------Shipping details-------------------------------*
          CLEAR wa_vbpa.
          READ TABLE ct_vbpa INTO wa_vbpa WITH KEY vbeln = wa_vbrk-vbeln parvw = 'WE' BINARY SEARCH.
          IF sy-subrc = 0.
            lv_kunnr = |{ wa_vbpa-kunnr ALPHA = IN }|.
            lv_name = lv_kunnr.
            REFRESH lt_lines.
            lt_lines = read_text( iv_id = c_id
                                  iv_language = c_language
                                  iv_name = lv_name
                                  iv_object = 'KNA1' ).
            TRY.
                wa_final-shippingto_name = lt_lines[ 1 ]-tdline.
              CATCH cx_sy_itab_line_not_found.
            ENDTRY.
            CLEAR: lv_kunnr,lv_name.
            READ TABLE ct_kna1 INTO wa_kna1 WITH KEY kunnr = wa_vbpa-kunnr BINARY SEARCH.
            IF sy-subrc IS INITIAL.
              wa_final-custom13 = wa_vbpa-kunnr.
              SORT ct_adrc BY addrnumber.
              CLEAR wa_adrc.
              READ TABLE ct_adrc INTO wa_adrc WITH KEY addrnumber = wa_kna1-adrnr BINARY SEARCH.
              IF sy-subrc = 0.
                MOVE wa_adrc-name1 TO wa_final-shippingto_name.
                MOVE wa_adrc-street TO wa_final-shippingto_address1.
                MOVE wa_adrc-strsuppl3 TO wa_final-shippingto_address2.
                MOVE wa_adrc-city1 TO wa_final-shippingto_place.
                MOVE wa_adrc-postcode1 TO wa_final-shippingto_pincode.
                wa_final-shippingto_state = wa_adrc-region.
                READ TABLE ct_exp TRANSPORTING NO FIELDS WITH KEY low = wa_vbrk-vtweg BINARY SEARCH.
                IF sy-subrc = 0.
                  wa_final-shippingto_gstin = c_urp.
                  wa_final-shippingto_state = '97'.
                  wa_final-shippingto_pincode = c_expin.
                ELSE.
                  MOVE wa_kna1-stcd3 TO wa_final-shippingto_gstin.
                ENDIF.
*             READ TABLE ct_exp TRANSPORTING NO FIELDS WITH KEY low = WA_VBRK-vtweg BINARY SEARCH.
*             IF sy-subrc = 0.
*               WA_FINAL-shippingto_state = C_STATE.
*             ENDIF.
              ENDIF.
            ENDIF.
          ENDIF.
*         ENDIF.
*         ---------------------Item Rate /price per unit quantity-------------------*
          sdtemp_rate = wa_outtab-netwr_itemlevel / wa_outtab-menge.
          lv_netwr = sdtemp_rate * wa_vbrk-kurrf.
          wa_final-rate = lv_netwr.
          CLEAR lv_netwr.
          lv_netwr = wa_outtab-netwr_itemlevel * wa_vbrk-kurrf.
          wa_final-taxable_value = lv_netwr.
          CLEAR lv_netwr.
          wa_final-grossamount = wa_final-taxable_value.
          lv_netwr = wa_vbrk-netwr * wa_vbrk-kurrf.
          wa_final-total_taxable_value = lv_netwr.
          CLEAR lv_netwr.
*         -------------------tax value item and invoice level----------------------*
          CLEAR wa_gst.
          READ TABLE lt_gst INTO wa_gst WITH KEY vbeln = wa_vbrk-vbeln BINARY SEARCH.
          IF sy-subrc = 0.
            IF wa_gst-igst IS INITIAL.
              lv_netwr = wa_gst-cgst * wa_vbrk-kurrf.
              wa_final-cgstvalue = lv_netwr.
              CLEAR lv_netwr.
              lv_netwr = wa_gst-sgst * wa_vbrk-kurrf.
              wa_final-sgstvalue = lv_netwr.
              CLEAR lv_netwr.
            ELSE.
              lv_netwr = wa_gst-igst * wa_vbrk-kurrf.
              wa_final-igstvalue = lv_netwr.
              CLEAR lv_netwr.
            ENDIF.
          ENDIF.
          CLEAR wa_item_gst.
          READ TABLE lt_item_gst INTO wa_item_gst WITH KEY posnr = wa_outtab-posnr knumv = wa_vbrk-knumv BINARY SEARCH.
          IF sy-subrc = 0.
            IF wa_item_gst-cgst IS INITIAL.
              lv_netwr = wa_item_gst-igst * wa_vbrk-kurrf.
              wa_final-iamt = lv_netwr.
              CLEAR lv_netwr.
            ELSE.
              lv_netwr = wa_item_gst-cgst * wa_vbrk-kurrf.
              wa_final-camt = lv_netwr.
              CLEAR lv_netwr.
              lv_netwr = wa_item_gst-sgst * wa_vbrk-kurrf.
              wa_final-samt = lv_netwr.
              CLEAR lv_netwr.
            ENDIF.
          ENDIF.
          wa_final-item_total_amount = wa_final-taxable_value + wa_final-iamt + wa_final-camt + wa_final-samt. "Item Total Amount after deducting TCS
          lv_tot_inv_val = wa_final-total_taxable_value + wa_final-cgstvalue + wa_final-sgstvalue + wa_final-igstvalue.
*       WA_FINAL-tot_inv_value_inr = round( val = LV_TOT_INV_VAL dec = 0 ).
          wa_final-tot_inv_value_inr = lv_tot_inv_val.
          wa_final-roundoff = abs( lv_tot_inv_val - round( val = lv_tot_inv_val dec = 0 ) ).
*         ----------------------gst Rate--------------------*
          IF wa_outtab-igst_rt NE 0.
            lv_round = round( val = wa_outtab-igst_rt dec = 0 ).
          ELSE.
            lv_round = round( val = ( wa_outtab-sugst_rt + wa_outtab-cgst_rt ) dec = 0 ).
          ENDIF.
          MOVE lv_round TO wa_final-gst_rate.
          CLEAR lv_round.
          MOVE wa_vbrk-vbeln TO lv_vbeln_slno.
        ENDIF.
*       ----------------------hsn Code-----------------------------*
        CLEAR wa_marc.
        READ TABLE ct_marc INTO wa_marc WITH KEY matnr = wa_outtab-matnr werks = wa_outtab-werks BINARY SEARCH.
        IF sy-subrc = 0.
          wa_final-hsncode = wa_marc-steuc.
          REPLACE ALL OCCURRENCES OF '.' IN wa_final-hsncode WITH space.
          CONDENSE wa_final-hsncode.
        ENDIF.
*       -------------------item DEscription---------------------*
        CLEAR wa_makt.
        READ TABLE ct_makt INTO wa_makt WITH KEY matnr = wa_outtab-matnr BINARY SEARCH.
        IF sy-subrc = 0.
          wa_final-item_description = wa_makt-maktx.
        ENDIF.
        IF wa_outtab-matnr IS NOT INITIAL.
          wa_final-item_name = wa_outtab-matnr+0(18).
        ENDIF.
*       -------------------Service---------------------*
        MOVE wa_final-hsncode TO hsn_temp1.
        hsn_temp1 = hsn_temp1+0(2).
        IF hsn_temp1 = c_state1.
          wa_final-isservice = c_y.
        ELSE.
          wa_final-isservice = c_n.
        ENDIF.
*       ------------Quantity And uom------------------------
        IF wa_final-isservice = c_n.
          MOVE wa_outtab-menge TO wa_final-quantity1. "Quantity
          wa_final-quantity1 = round( val = wa_final-quantity1 dec = 3 ).
          READ TABLE it_pac TRANSPORTING NO FIELDS WITH KEY low = wa_vbrk-vtweg.
          IF sy-subrc = 0.
            READ TABLE ct_marm INTO wa_marm WITH KEY meinh = c_box
                                            matnr = wa_outtab-matnr.
            IF sy-subrc = 0.
              lv_flag_t = abap_true.
            ELSE.
            ENDIF.
            IF lv_flag_t IS NOT INITIAL.
              CLEAR wa_marm.
              READ TABLE ct_marm INTO wa_marm WITH KEY meinh = c_pac1
                                                  matnr = wa_outtab-matnr BINARY SEARCH.
              IF sy-subrc = 0.
                wa_outtab-vrkme = c_pac1..
                wa_final-quantity1 = wa_outtab-menge * wa_marm-umren.
                lv_netwr = wa_final-rate / wa_marm-umren.
                wa_final-rate = lv_netwr.
              ELSE.
                CLEAR: wa_marm.
                READ TABLE ct_marm INTO wa_marm WITH KEY matnr = wa_outtab-matnr
                                                         meinh = c_pc BINARY SEARCH.
                IF sy-subrc = 0.
                  wa_outtab-vrkme = c_pac1.
                  wa_final-quantity1 = wa_outtab-menge * wa_marm-umren.
                  lv_netwr = wa_final-rate / wa_marm-umren.
                  wa_final-rate = lv_netwr.
                ENDIF.
              ENDIF.
              CLEAR wa_marm.
            ENDIF.
            CLEAR: lv_flag_t, lv_netwr.
          ENDIF.
          CLEAR wa_uom.
          READ TABLE ct_uom INTO wa_uom WITH KEY sapuom = wa_outtab-vrkme BINARY SEARCH.
          IF sy-subrc = 0.
            MOVE wa_uom-GstUom TO wa_final-uqc."ST01++
          ENDIF.
        ENDIF.
        READ TABLE it_fkart TRANSPORTING NO FIELDS WITH KEY low = wa_vbrk-fkart.
        IF sy-subrc = 0.
          wa_final-gst_rate = ( ( wa_final-camt + wa_final-samt + wa_final-iamt ) * 100 ) / wa_final-taxable_value.
        ENDIF.
        IF wa_final-inv_type_code IS INITIAL.
          IF wa_final-billing_gstin IS NOT INITIAL.
            wa_final-inv_type_code = c_b2b1.
          ELSE.
            wa_final-inv_type_code = c_b2c.
          ENDIF.
        ENDIF.
        IF wa_final-inv_type_code = c_expwop OR wa_final-inv_type_code = c_expwp.
          IF wa_final-supplier_state NE wa_final-billing_state.
            IF wa_final-rate = '0' AND wa_final-rate = '0.00'.
              IF wa_final-iamt = '0' AND wa_final-iamt = '0.00'.
                wa_final-igstvalue = 0.
                wa_final-cgstvalue = ''.
                wa_final-sgstvalue = ''.
                wa_final-iamt = '0.00'.
                wa_final-camt = ''.
                wa_final-samt = ''.
              ENDIF.
            ENDIF.
          ENDIF.
        ELSEIF wa_final-supplier_state = wa_final-billing_state.
          IF wa_final-rate = '0' AND wa_final-rate = '0.00'.
            IF ( wa_final-camt = '0' AND wa_final-camt IS INITIAL ) AND ( wa_final-samt = '0' AND wa_final-samt IS INITIAL ).
              wa_final-cgstvalue = '0.00'.
              wa_final-sgstvalue = '0.00'.
              wa_final-igstvalue = ''.
              wa_final-camt = '0.00'.
              wa_final-samt = '0.00'.
              wa_final-iamt = ''.
            ENDIF.
          ELSEIF wa_final-supplier_state NE wa_final-billing_state.
            IF wa_final-iamt = '0' AND wa_final-iamt IS INITIAL.
              wa_final-igstvalue = '0.00'.
              wa_final-cgstvalue = ''.
              wa_final-sgstvalue = ''.
              wa_final-iamt = '0.00'.
              wa_final-camt = ''.
              wa_final-samt = ''.
            ENDIF.
          ENDIF.
        ENDIF.
        wa_final-branch = wa_vbrp-werks.
        wa_final-bukrs = wa_vbrk-bukrs.
        APPEND: wa_final TO et_final.
        CLEAR: wa_final, wa_outtab, wa_bkpf_sd, wa_bseg_sd. "LV_NETWR.
      ENDLOOP.
    ENDIF.

  ENDMETHOD.



  METHOD generate_json_structure.

    TYPES: BEGIN OF ty_item,
             serial_number                 TYPE string,
             is_service                    TYPE string,
             hsn                           TYPE string,
             product_code                  TYPE string,
             item_name                     TYPE string,
             item_description              TYPE string,
             nature_of_jw_done             TYPE string,
             barcode                       TYPE string,
             uqc                           TYPE string,
             quantity                      TYPE string,
             free_quantity                 TYPE string,
             loss_unit_of_measure          TYPE string,
             loss_total_quantity           TYPE string,
             rate                          TYPE string,
             cess_rate                     TYPE string,
             state_cess_rate               TYPE string,
             tds_rate                      TYPE string,
             cess_non_advalorem_rate       TYPE string,
             price_per_quantity            TYPE string,
             discount_amount               TYPE string,
             gross_amount                  TYPE string,
             other_charges                 TYPE string,
             taxable_value                 TYPE string,
             pre_tax_value                 TYPE string,
             igst_amount                   TYPE string,
             cgst_amount                   TYPE string,
             sgst_amount                   TYPE string,
             cess_amount                   TYPE string,
             tds_amount                    TYPE string,
             state_cess_amount             TYPE string,
             state_cess_non_adva_lo_amount TYPE string,
             cess_non_advalorem_amount     TYPE string,
             order_line_reference          TYPE string,
             origin_country                TYPE string,
             item_serial_number            TYPE string,
             item_total                    TYPE string,
             doc_value                     TYPE string,
             item_attribute_details        TYPE string,
             tax_type                      TYPE string,
             batch_name_number             TYPE string,
             batch_expiry_date             TYPE string,
             warranty_date                 TYPE string,
             itc_eligibility               TYPE string,
             gst_act_or_rule_section       TYPE string,
             itc_igst_amount               TYPE string,
             itc_cgst_amount               TYPE string,
             itc_sgst_amount               TYPE string,
             itc_cess_amount               TYPE string,
             custom_item1                  TYPE string,
             custom_item2                  TYPE string,
             custom_item3                  TYPE string,
             custom_item4                  TYPE string,
             custom_item5                  TYPE string,
             custom_item6                  TYPE string,
             custom_item7                  TYPE string,
             custom_item8                  TYPE string,
             custom_item9                  TYPE string,
             custom_item10                 TYPE string,
             custom_item11                 TYPE string,
             custom_item12                 TYPE string,
             custom_item13                 TYPE string,
             custom_item14                 TYPE string,
             custom_item15                 TYPE string,
             custom_item16                 TYPE string,
             custom_item17                 TYPE string,
             custom_item18                 TYPE string,
             custom_item19                 TYPE string,
             custom_item20                 TYPE string,
           END OF ty_item.
    TYPES: tt_item TYPE STANDARD TABLE OF ty_item WITH EMPTY KEY.
    TYPES: BEGIN OF ty_data,
             location_gstin                 TYPE string,
             location_name                  TYPE string,
             ref_id                         TYPE string,
             ref_date                       TYPE string,
             return_period                  TYPE string,
             liability_discharge_return_p   TYPE string,
             itc_claim_return_period        TYPE string,
             purpose                        TYPE string,
             auto_push_or_generate          TYPE string,
             supply_type                    TYPE string,
             irn                            TYPE string,
             document_type                  TYPE string,
             credit_note_reason             TYPE string,
             transaction_type               TYPE string,
             transaction_nature             TYPE string,
             transaction_type_description   TYPE string,
             taxpayer_type                  TYPE string,
             document_number                TYPE string,
             document_series_code           TYPE string,
             document_date                  TYPE string,
             cancelled_date                 TYPE string,
             _reference_id                  TYPE string,
             bill_from_gstin                TYPE string,
             bill_from_legal_name           TYPE string,
             bill_from_trade_name           TYPE string,
             bill_from_vendor_code          TYPE string,
             bill_from_address1             TYPE string,
             bill_from_address2             TYPE string,
             bill_from_city                 TYPE string,
             bill_from_state_code           TYPE string,
             bill_from_pincode              TYPE string,
             bill_from_phone                TYPE string,
             bill_from_email                TYPE string,
             dispatch_from_gstin            TYPE string,
             dispatch_from_trade_name       TYPE string,
             dispatch_from_vendor_code      TYPE string,
             dispatch_from_address1         TYPE string,
             dispatch_from_address2         TYPE string,
             dispatch_from_city             TYPE string,
             dispatch_from_state_code       TYPE string,
             dispatch_from_pincode          TYPE string,
             bill_to_gstin                  TYPE string,
             bill_to_legal_name             TYPE string,
             bill_to_trade_name             TYPE string,
             bill_to_vendor_code            TYPE string,
             bill_to_address1               TYPE string,
             bill_to_address2               TYPE string,
             bill_to_city                   TYPE string,
             bill_to_state_code             TYPE string,
             bill_to_pincode                TYPE string,
             bill_to_phone                  TYPE string,
             bill_to_email                  TYPE string,
             ship_to_gstin                  TYPE string,
             ship_to_legal_name             TYPE string,
             ship_to_trade_name             TYPE string,
             ship_to_vendor_code            TYPE string,
             ship_to_address1               TYPE string,
             ship_to_address2               TYPE string,
             ship_to_city                   TYPE string,
             ship_to_state_code             TYPE string,
             ship_to_pincode                TYPE string,
             payment_type                   TYPE string,
             payment_mode                   TYPE string,
             payment_amount                 TYPE string,
             advance_paid_amount            TYPE string,
             payment_date                   TYPE string,
             payment_remarks                TYPE string,
             payment_terms                  TYPE string,
             payment_instruction            TYPE string,
             payee_name                     TYPE string,
             upi_id                         TYPE string,
             payee_account_number           TYPE string,
             payee_merchant_code            TYPE string,
             payment_amount_due             TYPE string,
             payment_due_date               TYPE string,
             ifsc                           TYPE string,
             credit_transfer                TYPE string,
             direct_debit                   TYPE string,
             credit_days                    TYPE string,
             transaction_id                 TYPE string,
             transaction_reference_id       TYPE string,
             transaction_note               TYPE string,
             payment_minimum_amount         TYPE string,
             transaction_reference_url      TYPE string,
             credit_availed_date            TYPE string,
             credit_reversal_date           TYPE string,
             ref_document_remarks           TYPE string,
             ref_document_period_start_date TYPE string,
             ref_document_period_end_date   TYPE string,
             ref_preceding_document_details TYPE string,
             ref_contract_details           TYPE string,
             additional_supporting_document TYPE string,
             bill_number                    TYPE string,
             bill_date                      TYPE string,
             port_code                      TYPE string,
             document_currency_code         TYPE string,
             destination_country            TYPE string,
             export_duty                    TYPE string,
             pos                            TYPE string,
             document_value                 TYPE string,
             document_discount              TYPE string,
             document_other_charges         TYPE string,
             gross_amount                   TYPE string,
             round_off_amount               TYPE string,
             differential_percentage        TYPE string,
             reverse_charge                 TYPE string,
             claim_refund                   TYPE string,
             under_igst_act                 TYPE string,
             gst_act_or_rule_section_h      TYPE string,
             refund_eligibility             TYPE string,
             ecommerce_gstin                TYPE string,
             tds_gstin                      TYPE string,
             pnr_or_unique_number           TYPE string,
             avail_provisional_itc          TYPE string,
             original_gstin                 TYPE string,
             original_state_code            TYPE string,
             original_trade_name            TYPE string,
             original_document_type         TYPE string,
             original_document_number       TYPE string,
             original_document_date         TYPE string,
             original_return_period         TYPE string,
             original_taxable_value         TYPE string,
             original_port_code             TYPE string,
             transport_date_time            TYPE string,
             transporter_id                 TYPE string,
             transporter_name               TYPE string,
             transport_mode                 TYPE string,
             distance                       TYPE string,
             transport_document_number      TYPE string,
             transport_document_date        TYPE string,
             vehicle_number                 TYPE string,
             vehicle_type                   TYPE string,
             to_email_addresses             TYPE string,
             to_mobile_numbers              TYPE string,
             jw_original_document_number    TYPE string,
             jw_original_document_date      TYPE string,
             jw_document_number             TYPE string,
             jw_document_date               TYPE string,
             isd_distribution_gstin         TYPE string,
             custom1                        TYPE string,
             custom2                        TYPE string,
             custom3                        TYPE string,
             custom4                        TYPE string,
             custom5                        TYPE string,
             custom6                        TYPE string,
             custom7                        TYPE string,
             custom8                        TYPE string,
             custom9                        TYPE string,
             custom10                       TYPE string,
             custom11                       TYPE string,
             custom12                       TYPE string,
             custom13                       TYPE string,
             custom14                       TYPE string,
             custom15                       TYPE string,
             custom16                       TYPE string,
             custom17                       TYPE string,
             custom18                       TYPE string,
             custom19                       TYPE string,
             custom20                       TYPE string,
             items                          TYPE tt_item,
           END OF ty_data.
    TYPES: tt_data TYPE STANDARD TABLE OF ty_data WITH EMPTY KEY.
    TYPES: BEGIN OF ty_final_str,
             data TYPE tt_data,
           END OF ty_final_str.
    TYPES: BEGIN OF ty_doc_val,
             document_no    TYPE zmm_c_challan-documentnumber,
             document_value TYPE I_BillingDocumentItemPrcgElmnt-conditionamount,
             taxable_value  TYPE I_BillingDocumentItemPrcgElmnt-conditionamount,
             cgst           TYPE I_BillingDocumentItemPrcgElmnt-conditionamount,
             sgst           TYPE I_BillingDocumentItemPrcgElmnt-conditionamount,
             igst           TYPE I_BillingDocumentItemPrcgElmnt-conditionamount,
             othercharges   TYPE I_BillingDocumentItemPrcgElmnt-conditionamount,
             qty            TYPE i_billingdocumentitem-BillingQuantity,
           END OF ty_doc_val.
    DATA: lt_gt_doc_val TYPE TABLE OF ty_doc_val,
          wa_gs_doc_val TYPE ty_doc_val.
    DATA: wa_request   TYPE ty_data,
          wa_final_req TYPE ty_final_str,
          lv_srno      TYPE posnr_vf,
          lr_string    TYPE RANGE OF string,
          lv_srno_c    TYPE c LENGTH 6.
    lr_string = VALUE #( ( sign = 'I' option = 'EQ' low = 'EXPWOP' )
                         ( sign = 'I' option = 'EQ' low = 'EXPWP' ) ).
    DATA(lt_final_itm) = it_final.
    DATA(lt_final_header) = it_final.
    SORT lt_final_header BY locationgstin documentnumber.
    DELETE ADJACENT DUPLICATES FROM lt_final_header COMPARING documentnumber.
    LOOP AT lt_final_header INTO DATA(wa_final_hdr).
      wa_request = CORRESPONDING #( wa_final_hdr ).
      wa_request-location_gstin = wa_final_hdr-locationgstin.
      wa_request-return_period = wa_final_hdr-returnperiod.
      wa_request-itc_claim_return_period = ' '.
      wa_request-purpose = COND #( WHEN iv_button = 'CHALLAN'
                                   THEN 'EWB'
                                   ELSE wa_final_hdr-purpose ).
      wa_request-auto_push_or_generate = wa_final_hdr-autopushorgenerate.
      wa_request-supply_type = wa_final_hdr-supplytype.
      wa_request-irn = wa_final_hdr-irn.
      wa_request-document_type = wa_final_hdr-documenttype.
      wa_request-transaction_type = wa_final_hdr-transactiontype.
      wa_request-transaction_type_description = wa_final_hdr-transactiontypedescription.
      wa_request-taxpayer_type = wa_final_hdr-taxtype.
      wa_request-document_number = wa_final_hdr-documentnumber.
      wa_request-document_series_code = wa_final_hdr-seriescode.
      IF iv_module EQ 'SD'.
        DATA(lv_series_code) = | { wa_final_hdr-documentnumber ALPHA = OUT } |.
        IF strlen( lv_series_code ) >= 4.
          wa_request-document_series_code = lv_series_code+0(4).
        ENDIF.
        IF iv_button EQ 'GSTR1'.
          wa_request-cancelled_date = wa_final_hdr-cancelled_date.
        ENDIF.
      ENDIF.
      wa_request-document_date = wa_final_hdr-documentdate.
      wa_request-bill_from_gstin = wa_final_hdr-billfromgstin.
*   IF iv_button EQ 'GSTR2'.
*     WA_REQUEST-bill_from_gstin = ''
*   ENDIF.
      wa_request-bill_from_legal_name = wa_final_hdr-billfromlegalname.
      wa_request-bill_from_trade_name = wa_final_hdr-billfromtradename.
      wa_request-bill_from_vendor_code = ''.
      wa_request-bill_from_address1 = wa_final_hdr-billfromaddress1.
      wa_request-bill_from_address2 = wa_final_hdr-billfromaddress2.
      wa_request-bill_from_city  = wa_final_hdr-billfromcity.
      wa_request-bill_from_state_code = wa_final_hdr-billfromstatecode.
      wa_request-bill_from_pincode = wa_final_hdr-billfrompincode.
      wa_request-bill_from_phone = wa_final_hdr-billfromphone.
      wa_request-bill_from_email = wa_final_hdr-billfromemail.
      wa_request-dispatch_from_gstin = wa_final_hdr-dispatchfromgstin.
      wa_request-dispatch_from_trade_name = wa_final_hdr-dispatchfromtradename.
      wa_request-dispatch_from_vendor_code = ''.
      wa_request-dispatch_from_address1 = wa_final_hdr-dispatchfromadd1.
      wa_request-dispatch_from_address2 = wa_final_hdr-dispatchfromadd2.
      wa_request-dispatch_from_city = wa_final_hdr-dispatchfromcity.
      wa_request-dispatch_from_state_code = wa_final_hdr-dispatchfromstatecode.
      wa_request-dispatch_from_pincode = wa_final_hdr-dispatchfrompincode.
      wa_request-bill_to_gstin = wa_final_hdr-billtogstin.
      IF sy-sysid EQ 'DS4' AND ( wa_final_hdr-transactiontype NOT IN lr_string ).
*        wa_request-bill_to_gstin = '24AACPH8447G002'.
        IF iv_button EQ 'GSTR2'.
          wa_request-location_gstin = '24AACPH8447G002'.
        ENDIF.
      ENDIF.
      IF wa_request-transaction_type = 'EXPWOP' OR wa_request-transaction_type = 'EXPWP'.
        wa_request-bill_to_gstin = ''.
      ENDIF.
      wa_request-bill_to_legal_name = wa_final_hdr-billfromlegalname.
      wa_request-bill_to_trade_name = wa_final_hdr-billtotradename.
      wa_request-bill_to_vendor_code = ''.
      wa_request-bill_to_address1 = wa_final_hdr-billtoaddress1.
      wa_request-bill_to_city = wa_final_hdr-billtocity.
      wa_request-bill_to_state_code = wa_final_hdr-billtostatecode.
*      IF sy-sysid EQ 'DS4' AND ( wa_final_hdr-transactiontype NOT IN lr_string ).
*        wa_request-bill_to_state_code = '24'.
*      ENDIF.
      wa_request-bill_to_pincode = wa_final_hdr-billtopincode.
*      IF sy-sysid EQ 'DS4' AND ( wa_final_hdr-transactiontype NOT IN lr_string ).
*        wa_request-bill_to_pincode = '382350'.
*      ENDIF.
      wa_request-bill_to_phone = wa_final_hdr-billtophone.
      wa_request-bill_to_email = wa_final_hdr-billtoemail.
      wa_request-ship_to_gstin = wa_final_hdr-shiptogstin.
*      IF sy-sysid EQ 'DS4' AND wa_request-ship_to_gstin IS NOT INITIAL AND ( wa_final_hdr-transactiontype NOT IN lr_string ).
*        wa_request-ship_to_gstin = '24AABCL5555B1ZW'.
*      ENDIF.
      wa_request-ship_to_legal_name = wa_final_hdr-shiptolegalname.
      wa_request-ship_to_trade_name = wa_final_hdr-shiptotradename.
      wa_request-ship_to_vendor_code  = ''.
      wa_request-ship_to_address1 = wa_final_hdr-shiptoaddress1.
      wa_request-ship_to_address2 = wa_final_hdr-shiptoaddress2.
      wa_request-ship_to_city = wa_final_hdr-shiptocity.
      wa_request-ship_to_state_code = wa_final_hdr-shiptostatecode.
      wa_request-ship_to_pincode = wa_final_hdr-shiptopincode.

      IF wa_request-ship_to_gstin = wa_request-bill_to_gstin.
        CLEAR wa_request-ship_to_gstin.
      ENDIF.

*        IF wa_request-transaction_type = 'EXPWOP' OR wa_request-transaction_type = 'EXPWP'.
*       wa_request-bill_to_state_code  = '96'.
*        wa_request-pos              = '96'.
**        wa_request-supply_type           = '96'.
*        wa_request-bill_to_pincode    = '999999'.
*
*        wa_request-ship_to_state_code  = '97'.
**        wa_request-supply           = '96'.
*        wa_request-ship_to_pincode    = '999999'.
*      ENDIF.

*      IF sy-sysid EQ 'DS4' AND wa_request-ship_to_state_code IS NOT INITIAL AND ( wa_final_hdr-transactiontype NOT IN lr_string ).
*        wa_request-ship_to_state_code = '24'.
*      ENDIF.
*      IF sy-sysid EQ 'DS4' AND wa_request-ship_to_pincode IS NOT INITIAL AND ( wa_final_hdr-transactiontype NOT IN lr_string ).
*        wa_request-ship_to_pincode = '382350'.
*      ENDIF.
      wa_request-payee_name = wa_final_hdr-payeename.
      wa_request-payee_account_number = wa_final_hdr-payeeaccountnumber.
      wa_request-payment_amount_due = ''.
      wa_request-transaction_reference_id = ''.
      wa_request-ref_document_remarks = wa_final_hdr-refdocumentremarks.
      wa_request-ref_document_period_start_date = wa_final_hdr-refdocumentperiodstartdate.
      wa_request-ref_document_period_end_date = wa_final_hdr-refdocumentperiodenddate.
      wa_request-ref_preceding_document_details = wa_final_hdr-refprecedingdocumentdetails.
      wa_request-ref_contract_details = wa_final_hdr-refcontractdetails.
      wa_request-additional_supporting_document = wa_final_hdr-additionalsupportingdocdetails.
      wa_request-bill_number = wa_final_hdr-billnumber.
      wa_request-bill_date = wa_final_hdr-billdate.
      wa_request-port_code = wa_final_hdr-portcode.
      wa_request-document_currency_code = wa_final_hdr-doccurrencycode.
      wa_request-destination_country = wa_final_hdr-destinationcountry.
      wa_request-export_duty = ''.
      wa_request-pos = wa_final_hdr-pos.
      wa_request-document_value = wa_final_hdr-documentvalue + wa_final_hdr-othercharges.
      wa_request-document_other_charges = wa_final_hdr-othercharges.
*   WA_REQUEST-docu = WA_FINAL_HDR-othercharges.
      wa_request-differential_percentage = wa_final_hdr-differentialpercent.
      wa_request-reverse_charge = wa_final_hdr-reversecharge.
      wa_request-claim_refund = wa_final_hdr-claimrefund.
      wa_request-under_igst_act = wa_final_hdr-underigstact.
      wa_request-refund_eligibility = wa_final_hdr-refundeligibility.
      wa_request-ecommerce_gstin = wa_final_hdr-ecommercegstin.
      wa_request-tds_gstin = ' '.
      wa_request-pnr_or_unique_number = ' '.
      wa_request-avail_provisional_itc = ' '.
      wa_request-original_gstin = ' '.
      wa_request-original_state_code = ' '.
      wa_request-original_trade_name = ' '.
      wa_request-original_document_type = wa_final_hdr-originaldocumenttype.
      wa_request-original_document_number = wa_final_hdr-originaldocumebtnumber.
      wa_request-original_document_date = wa_final_hdr-originaldocumentdate.
      wa_request-original_port_code = wa_final_hdr-originalportcode.
      wa_request-transporter_id = wa_final_hdr-transporterid.
      wa_request-transporter_name = wa_final_hdr-transportername.
      wa_request-transport_mode = wa_final_hdr-transportmode.
      wa_request-distance = wa_final_hdr-distancelevel.
      IF wa_final_hdr-distancelevel IS INITIAL.
        CLEAR wa_request-distance.
      ENDIF.
      CONDENSE wa_request-distance.
      wa_request-transport_document_number = wa_final_hdr-transportdocumentnumber.
      CONDENSE wa_request-transport_document_number.
      wa_request-transport_document_date = wa_final_hdr-transportdocdate.
      IF wa_final_hdr-transportdocdate IS INITIAL.
        CLEAR wa_request-transport_document_date.
      ELSE.
      ENDIF.
      CONDENSE wa_request-transport_document_date.
      IF wa_request-transport_document_date IS INITIAL.
        wa_request-transport_document_date = ''.
      ENDIF.
      wa_request-vehicle_number = wa_final_hdr-vehiclenumber.
      CONDENSE wa_request-vehicle_number.
      wa_request-vehicle_type = wa_final_hdr-vehicletype.
      CONDENSE wa_request-vehicle_type.
      wa_request-to_email_addresses = wa_final_hdr-toemailaddress.
      wa_request-to_mobile_numbers = wa_final_hdr-tomobilenumbers.
      IF wa_final_hdr-tomobilenumbers IS INITIAL.
        CLEAR wa_request-to_mobile_numbers.
      ENDIF.
      wa_request-jw_original_document_number = ' '.
      wa_request-jw_original_document_date = ' '.
      wa_request-jw_document_number = ' '.
      wa_request-jw_document_date = ' '.
      wa_request-custom1 = wa_final_hdr-custom1.
      wa_request-custom2 = wa_final_hdr-custom2.
      wa_request-custom3 = wa_final_hdr-custom3.
      wa_request-custom4 = wa_final_hdr-custom4.
      wa_request-custom5 = wa_final_hdr-custom5.
      wa_request-custom6 = wa_final_hdr-custom6.
      wa_request-custom7 = wa_final_hdr-custom7.
      wa_request-custom8 = wa_final_hdr-custom8.
      wa_request-custom9 = wa_final_hdr-custom9.
      wa_request-custom10 = wa_final_hdr-custom10.
      wa_request-document_discount = wa_final_hdr-discountamount.
      wa_request-to_email_addresses = wa_final_hdr-toemailaddress.
      LOOP AT lt_final_itm INTO DATA(wa_final_itm) WHERE documentnumber = wa_final_hdr-documentnumber.
        ASSIGN wa_request-items TO FIELD-SYMBOL(<fs_items>).
        APPEND INITIAL LINE TO <fs_items> ASSIGNING FIELD-SYMBOL(<wa_items>).
        lv_srno = lv_srno + 1.
        lv_srno_c = lv_srno.
        <wa_items>-serial_number = lv_srno_c.
        <wa_items>-is_service = wa_final_itm-isservice.
        <wa_items>-hsn = wa_final_itm-hsn.
        CONDENSE <wa_items>-hsn.
        <wa_items>-product_code = wa_final_itm-itemname.
        CONDENSE <wa_items>-product_code.
        <wa_items>-item_name = wa_final_itm-itemname.
        CONDENSE <wa_items>-item_name.
        <wa_items>-item_description = wa_final_itm-itemdescription.
        CONDENSE <wa_items>-item_description.
*     <WA_ITEMS>-nature_of_jwdone  = ' '.
        <wa_items>-barcode  = wa_final_itm-barcode.
        <wa_items>-uqc  = wa_final_itm-uqc.
        CONDENSE <wa_items>-uqc.
        <wa_items>-quantity  = wa_final_itm-quantity.
        "Start on 13.10.2023 for Qty blank must be 0.
***   IF WA_FINAL_ITM-QUANTITY IS INITIAL.
***     CLEAR <WA_ITEMS>-QUANTITY.
***   ENDIF.
*     IF WA_FINAL_ITM-quantity = '0'.
*       CLEAR: <WA_ITEMS>-quantity.
*     ENDIF.
        "End on 13.10.2023
        CONDENSE <wa_items>-quantity.
        <wa_items>-free_quantity  = wa_final_itm-freequantity.
        IF wa_final_itm-freequantity IS INITIAL.
          CLEAR <wa_items>-free_quantity.
        ENDIF.
        <wa_items>-loss_unit_of_measure  = ' '.
        <wa_items>-loss_total_quantity  = ' '.
        <wa_items>-rate  = wa_final_itm-rate.
        CONDENSE <wa_items>-rate.
        <wa_items>-cess_rate  = wa_final_itm-cessrate.
        CONDENSE <wa_items>-cess_rate.
        <wa_items>-state_cess_rate  = wa_final_itm-statecessrate.
        IF wa_final_itm-statecessrate IS INITIAL.
          CLEAR <wa_items>-state_cess_rate.
        ENDIF.
        CONDENSE <wa_items>-state_cess_rate.
        <wa_items>-cess_non_advalorem_rate  = wa_final_itm-cessnonadvaloremrate.
        IF wa_final_itm-cessnonadvaloremrate IS INITIAL.
          CLEAR <wa_items>-cess_non_advalorem_rate.
        ENDIF.
        <wa_items>-price_per_quantity  = wa_final_itm-priceperquantity.
        IF wa_final_itm-priceperquantity IS INITIAL.
          CLEAR <wa_items>-price_per_quantity.
        ENDIF.
        CONDENSE <wa_items>-price_per_quantity.
        <wa_items>-discount_amount  = ' '.
        CONDENSE <wa_items>-discount_amount.
        IF <wa_items>-discount_amount IS INITIAL.
          CLEAR <wa_items>-discount_amount.
        ENDIF.
        <wa_items>-gross_amount  = wa_final_itm-grossamount.
        IF wa_final_itm-grossamount IS INITIAL.
          CLEAR <wa_items>-gross_amount.
        ENDIF.
        CONDENSE <wa_items>-gross_amount.
        IF wa_final_itm-othercharges IS INITIAL.
          CLEAR <wa_items>-other_charges.
        ENDIF.
        <wa_items>-taxable_value  = wa_final_itm-taxablevalue.
        IF wa_final_itm-taxablevalue IS INITIAL.
          CLEAR <wa_items>-taxable_value.
        ENDIF.
        CONDENSE <wa_items>-taxable_value.
        <wa_items>-pre_tax_value  = wa_final_itm-pretaxvalue.
        IF wa_final_itm-pretaxvalue IS INITIAL.
          CLEAR <wa_items>-pre_tax_value.
        ENDIF.
        <wa_items>-igst_amount  = wa_final_itm-igstamount.
        CONDENSE <wa_items>-igst_amount.
        IF <wa_items>-igst_amount IS INITIAL OR <wa_items>-igst_amount = '0' OR <wa_items>-igst_amount = '0.00'.
          CLEAR <wa_items>-cess_amount.
        ENDIF.
        <wa_items>-cgst_amount  = wa_final_itm-cgstamount.
        CONDENSE <wa_items>-cgst_amount.
        IF <wa_items>-cgst_amount IS INITIAL OR <wa_items>-cgst_amount = '0' OR <wa_items>-cgst_amount = '0.00'.
          CLEAR <wa_items>-cess_amount.
        ENDIF.
        <wa_items>-sgst_amount  = wa_final_itm-sgstamount.
        CONDENSE <wa_items>-sgst_amount.
        IF <wa_items>-sgst_amount IS INITIAL OR <wa_items>-sgst_amount = '0' OR <wa_items>-sgst_amount = '0.00'.
          CLEAR <wa_items>-cess_amount.
        ENDIF.
        <wa_items>-cess_amount  = wa_final_itm-cessamount.
        CONDENSE <wa_items>-cess_amount.
        IF <wa_items>-cess_amount IS INITIAL OR <wa_items>-cess_amount = '0' OR <wa_items>-cess_amount = '0.00'.
          CLEAR <wa_items>-cess_amount.
        ENDIF.
        IF wa_final_hdr-pos = '96' AND
           ( wa_final_hdr-transactiontype = 'EXPWP' OR
             wa_final_hdr-transactiontype = 'EXPWOP' ).
          IF <wa_items>-igst_amount IS INITIAL OR <wa_items>-igst_amount = '0.00'.
            <wa_items>-rate = 0.
            <wa_items>-igst_amount = 0.
          ENDIF.
          CLEAR: <wa_items>-cgst_amount,
                 <wa_items>-sgst_amount,
                 <wa_items>-itc_cgst_amount,
                 <wa_items>-itc_sgst_amount.
        ENDIF.
        IF wa_final_hdr-transactiontype = 'SEZWOP' OR
           wa_final_hdr-transactiontype = 'SEZWP'.
          IF wa_final_itm-rate IS INITIAL OR
             wa_final_itm-rate = '0.00'.
            <wa_items>-rate = 0.
            <wa_items>-igst_amount = 0.
          ENDIF.
          CLEAR: <wa_items>-cgst_amount,
                 <wa_items>-sgst_amount,
                 <wa_items>-itc_sgst_amount,
                 <wa_items>-itc_cgst_amount.
        ENDIF.
        IF wa_final_hdr-transactiontype = 'B2B' OR wa_final_hdr-transactiontype = 'B2C' OR wa_final_hdr-transactiontype = 'CRN' OR wa_final_hdr-transactiontype = 'DBN'.
          IF wa_final_hdr-billfromstatecode NE wa_final_hdr-billtostatecode.
            IF <wa_items>-igst_amount = '0' OR <wa_items>-igst_amount = '0.00' OR <wa_items>-igst_amount = ''.
              CLEAR: <wa_items>-cgst_amount, <wa_items>-sgst_amount.
*           <WA_ITEMS>-igst_amount = '0'.
            ENDIF.
          ELSE.
            IF <wa_items>-cgst_amount = '0' OR <wa_items>-cgst_amount = '0.00' OR <wa_items>-cgst_amount = ''.
              CLEAR <wa_items>-igst_amount.
*           <WA_ITEMS>-cgst_amount = '0'.
*           <WA_ITEMS>-sgst_amount = '0'.
            ENDIF.
          ENDIF.
        ENDIF.
        IF wa_final_hdr-transactiontype = 'B2B' OR wa_final_hdr-transactiontype = 'B2C' OR wa_final_hdr-transactiontype = 'CRN' OR wa_final_hdr-transactiontype = 'DBN'.
          IF wa_final_hdr-billfromstatecode NE wa_final_hdr-billtostatecode.
            IF <wa_items>-igst_amount = '0' OR <wa_items>-igst_amount = '0.00'.
              CLEAR: <wa_items>-cgst_amount, <wa_items>-sgst_amount.
*           <WA_ITEMS>-igst_amount = '0'.
            ENDIF.
            IF <wa_items>-cgst_amount = '0.00' OR <wa_items>-cgst_amount = '0'.
              CLEAR: <wa_items>-cgst_amount, <wa_items>-sgst_amount.
            ENDIF.
          ELSE.
            IF <wa_items>-cgst_amount = '0' OR <wa_items>-cgst_amount = '0.00'.
              CLEAR <wa_items>-igst_amount.
*           <WA_ITEMS>-cgst_amount = '0'.
*           <WA_ITEMS>-sgst_amount = '0'.
            ENDIF.
            IF <wa_items>-igst_amount = '0.00' OR <wa_items>-igst_amount = '0'.
              CLEAR <wa_items>-igst_amount.
            ENDIF.
          ENDIF.
        ENDIF.
        CLEAR wa_gs_doc_val.
        wa_gs_doc_val-document_no = wa_final_itm-documentnumber.
*     WA_GS_DOC_VAL-document_value = WA_FINAL_ITM-documentvalue.
        wa_gs_doc_val-taxable_value = wa_final_itm-grossamount.
        wa_gs_doc_val-cgst = wa_final_itm-cgstamount.
        wa_gs_doc_val-sgst = wa_final_itm-sgstamount.
        wa_gs_doc_val-igst = wa_final_itm-igstamount.
        wa_gs_doc_val-othercharges = wa_final_itm-othercharges.
        wa_gs_doc_val-qty  = wa_final_itm-quantity.
        COLLECT wa_gs_doc_val INTO lt_gt_doc_val.
        CLEAR wa_gs_doc_val.
        <wa_items>-state_cess_amount  = wa_final_itm-statecessamount.
        IF <wa_items>-state_cess_amount IS INITIAL.
          CLEAR <wa_items>-state_cess_amount.
        ENDIF.
        <wa_items>-state_cess_non_adva_lo_amount  = wa_final_itm-statecessnonadvaloremamount.
        IF <wa_items>-state_cess_non_adva_lo_amount IS INITIAL.
          CLEAR <wa_items>-state_cess_non_adva_lo_amount.
        ENDIF.
        <wa_items>-cess_non_advalorem_amount  = wa_final_itm-cessnonadvaloremamount.
        IF <wa_items>-cess_non_advalorem_amount IS INITIAL.
          CLEAR <wa_items>-cess_non_advalorem_amount.
        ENDIF.
        <wa_items>-order_line_reference  = wa_final_itm-orderlinereference.
        <wa_items>-origin_country  = wa_final_itm-origincountry.
        <wa_items>-item_serial_number  = wa_final_itm-itemserialnumber.
        <wa_items>-item_total  = wa_final_itm-itemtotal + wa_final_itm-othercharges.
        IF <wa_items>-item_total IS INITIAL.
          CLEAR <wa_items>-item_total.
        ENDIF.
        CONDENSE <wa_items>-item_total.
        <wa_items>-item_attribute_details  = wa_final_itm-itemattributedetails.
        <wa_items>-tax_type  = wa_final_itm-taxtype.
        <wa_items>-batch_name_number  = wa_final_itm-batchnamenumber.
        <wa_items>-batch_expiry_date  = wa_final_itm-batchexpirydate.
        IF <wa_items>-batch_expiry_date IS INITIAL.
          CLEAR <wa_items>-batch_expiry_date.
        ENDIF.
        <wa_items>-warranty_date  = wa_final_itm-warrantydate.
        IF wa_final_itm-warrantydate IS INITIAL.
          CLEAR <wa_items>-warranty_date.
        ENDIF.
        <wa_items>-itc_eligibility  = wa_final_itm-itceligibility.
        IF wa_final_itm-itceligibility IS INITIAL.
          CLEAR <wa_items>-itc_eligibility.
        ENDIF.
        CONDENSE <wa_items>-itc_eligibility.
        <wa_items>-itc_igst_amount  = wa_final_itm-itcigstamount.
        CONDENSE <wa_items>-itc_igst_amount.
        IF <wa_items>-itc_igst_amount IS INITIAL OR <wa_items>-itc_igst_amount EQ '0' OR <wa_items>-itc_igst_amount EQ '0.00'.
          CLEAR <wa_items>-itc_igst_amount.
        ENDIF.
        <wa_items>-itc_cgst_amount  = wa_final_itm-itccgstamount.
        CONDENSE <wa_items>-itc_cgst_amount.
        IF <wa_items>-itc_cgst_amount IS INITIAL OR <wa_items>-itc_cgst_amount EQ '0' OR <wa_items>-itc_cgst_amount EQ '0.00'.
          CLEAR <wa_items>-itc_cgst_amount.
        ENDIF.
        <wa_items>-itc_sgst_amount  = wa_final_itm-itcsgstamount.
        CONDENSE <wa_items>-itc_sgst_amount.
        IF <wa_items>-itc_sgst_amount IS INITIAL OR <wa_items>-itc_sgst_amount EQ '0' OR <wa_items>-itc_sgst_amount EQ '0.00'.
          CLEAR <wa_items>-itc_sgst_amount.
        ENDIF.
        <wa_items>-itc_cess_amount  = wa_final_itm-itccessamount.
        CONDENSE <wa_items>-itc_cess_amount.
        IF <wa_items>-itc_cess_amount IS INITIAL OR <wa_items>-itc_cess_amount EQ '0' OR <wa_items>-itc_cess_amount EQ '0.00'.
          CLEAR <wa_items>-itc_cess_amount.
        ENDIF.
        <wa_items>-custom_item1  = wa_final_itm-customitem1.
        CONDENSE <wa_items>-custom_item1.
        <wa_items>-custom_item2  = wa_final_itm-customitem2.
        CONDENSE <wa_items>-custom_item2.
        <wa_items>-custom_item3  = wa_final_itm-customitem3.
        CONDENSE <wa_items>-custom_item3.
        <wa_items>-custom_item4  = wa_final_itm-customitem4.
        CONDENSE <wa_items>-custom_item4.
        <wa_items>-custom_item5  = wa_final_itm-customitem5.
        CONDENSE <wa_items>-custom_item5.
        <wa_items>-custom_item6  = wa_final_itm-customitem6.
        CONDENSE <wa_items>-custom_item6.
        <wa_items>-custom_item7  = wa_final_itm-customitem7.
        CONDENSE <wa_items>-custom_item7.
        <wa_items>-custom_item8  = wa_final_itm-customitem8.
        CONDENSE <wa_items>-custom_item8.
        <wa_items>-custom_item9  = wa_final_itm-customitem9.
        CONDENSE <wa_items>-custom_item9.
        <wa_items>-custom_item10  = wa_final_itm-customitem10.
        CONDENSE <wa_items>-custom_item10.
        IF wa_final_hdr-purpose = 'EINV'.
          IF wa_final_hdr-transactiontype = 'EXPWOP' OR wa_final_hdr-transactiontype = 'EXPWP'.
            CLEAR: wa_final_hdr-shiptogstin,
                   wa_final_hdr-shiptolegalname,
                   wa_final_hdr-shiptoaddress1,
                   wa_final_hdr-shiptoaddress2,
                   wa_final_hdr-shiptocity,
                   wa_final_hdr-shiptopincode,
                   wa_final_hdr-shiptostatecode.
          ENDIF.
        ENDIF.
        IF wa_final_hdr-documenttype = 'DBN' OR wa_final_hdr-documenttype = 'CRN'.
          CLEAR: wa_final_hdr-transportername,
                 wa_final_hdr-transporterid,
                 wa_final_hdr-transportmode,
                 wa_final_hdr-transportdocumentnumber,
                 wa_final_hdr-vehiclenumber,
                 wa_final_hdr-vehicletype.
        ENDIF.
        IF wa_final_hdr-purpose = 'EINV'.
          CLEAR: wa_final_hdr-transportername,
                 wa_final_hdr-transporterid,
                 wa_final_hdr-transportmode,
                 wa_final_hdr-transportdocumentnumber,
                 wa_final_hdr-vehiclenumber,
                 wa_final_hdr-vehicletype,
                 wa_final_hdr-distancelevel.
        ENDIF.
        IF wa_final_hdr-transportmode = 'ROAD'.
          IF wa_final_hdr-transportdocumentnumber IS INITIAL.
            CLEAR: wa_final_hdr-transportdocumentnumber, wa_final_hdr-transportdocdate.
          ENDIF.
        ENDIF.
        IF wa_final_hdr-transportmode IS INITIAL.
          CLEAR: wa_final_hdr-transportdocumentnumber, wa_final_hdr-vehicletype, wa_final_hdr-vehiclenumber.
        ELSEIF wa_final_hdr-transportmode NE 'ROAD'.
          IF wa_final_hdr-transporterid IS INITIAL AND wa_final_hdr-transportername IS INITIAL.
            CLEAR: wa_final_hdr-transporterid, wa_final_hdr-transportername.
          ENDIF.
          CLEAR: wa_final_hdr-vehicletype, wa_final_hdr-vehiclenumber.
        ENDIF.
      ENDLOOP.
      IF wa_request IS NOT INITIAL.
        APPEND wa_request TO wa_final_req-data.
        /ui2/cl_json=>serialize( EXPORTING data = wa_final_req
                                           pretty_name = /ui2/cl_json=>pretty_mode-camel_case
                                 RECEIVING r_json = rv_request ).
      ENDIF.
    ENDLOOP.
    DATA: lr_doc     TYPE RANGE OF zsd_c_einv-Documentnumber,
          wa_display TYPE zsd_c_einv.
    lr_doc = VALUE #( FOR wa_doc IN lt_final_header
                      ( sign = 'I' option = 'EQ' low = wa_doc-documentnumber ) ).
    SELECT * FROM zsd_c_billing_document
      WHERE DocumentReferenceID IN @lr_doc
      INTO TABLE @DATA(lt_global).
    LOOP AT wa_final_req-data INTO DATA(wa_temp).
      MOVE-CORRESPONDING wa_temp TO wa_display.
      DATA: lv_doc TYPE I_billingdocumentbasic-DocumentReferenceID.
      lv_doc = wa_temp-document_number.
*   LV_DOC = |{ WA_TEMP-document_number ALPHA = IN }|.
      READ TABLE lt_global INTO DATA(wa_global) WITH KEY DocumentReferenceID = lv_doc.
      IF sy-subrc = 0.
        wa_display-irn_status = COND string(
                                WHEN wa_global-Cancel = 'X' THEN 'IRN Cancelled'
                                WHEN wa_global-Fail = 'X' THEN 'ERROR !'
                                WHEN wa_global-QrCode IS INITIAL THEN 'IRN Pending'
                                ELSE 'IRN Generated' ).
        wa_display-irn_icon = COND i(
                                WHEN wa_global-cancel = 'X' THEN 1
                                WHEN wa_global-fail = 'X' THEN 1
                                WHEN wa_global-QrCode IS INITIAL THEN 2
                                ELSE 3 ).
        wa_display-Irn = wa_global-Irn.
        wa_display-Acknumber = wa_global-AckowlegementNumber.
        wa_display-Ackdate = wa_global-AckDate.
        wa_display-ErrorDescription = wa_global-EinvError.
      ELSE.
        wa_display-irn_status = 'IRN Pending'.
        wa_display-irn_icon = 2.
        wa_display-ErrorDescription = wa_global-EinvError.
      ENDIF.
      READ TABLE lt_gt_doc_val INTO wa_gs_doc_val WITH KEY document_no = wa_temp-document_number.
      IF sy-subrc = 0.
        wa_display-taxablevalue = wa_gs_doc_val-taxable_value.
        wa_display-cgstamount = wa_gs_doc_val-cgst.
        wa_display-sgstamount = wa_gs_doc_val-sgst.
        wa_display-quantity = wa_gs_doc_val-qty.
        wa_display-igstamount = COND #( WHEN wa_gs_doc_val-igst IS NOT INITIAL
                                        THEN wa_gs_doc_val-igst
                                        ELSE ' ' ).
      ENDIF.
      wa_display-documentnumber = wa_temp-document_number.
      wa_display-Documentdate = |{ wa_temp-Document_date+6(4) }{ wa_temp-Document_date+3(2) }{ wa_temp-Document_date+0(2) }|.
      wa_display-Rate = |{ wa_temp-items[ 1 ]-rate } { '%' }|.
      wa_display-billingtype = wa_temp-items[ 1 ]-custom_item5.
      wa_display-documentvalue = wa_temp-document_value.
      wa_display-transactiontype = wa_temp-transaction_type.
      wa_display-documenttype = wa_temp-document_type.
      wa_display-billfromgstin = wa_temp-bill_from_gstin.
      wa_display-billfromlegalname = wa_temp-bill_from_legal_name.
      wa_display-billfromtradename = wa_temp-bill_from_trade_name.
      wa_display-billfromaddress1 = wa_temp-bill_from_address1.
      wa_display-billfromaddress2 = wa_temp-bill_from_address2.
      wa_display-billfromcity = wa_temp-bill_from_city.
      wa_display-billfromstatecode = wa_temp-bill_from_state_code.
      wa_display-billfrompincode = wa_temp-bill_from_pincode.
      wa_display-billfromphone = wa_temp-bill_from_phone.
      wa_display-billfromemail = wa_temp-bill_from_email.
      wa_display-dispatchfromgstin = wa_temp-dispatch_from_gstin.
      wa_display-dispatchfromtradename = wa_temp-dispatch_from_trade_name.
      wa_display-dispatchfromadd1 = wa_temp-dispatch_from_address1.
      wa_display-dispatchfromadd2 = wa_temp-dispatch_from_address2.
      wa_display-dispatchfromcity = wa_temp-dispatch_from_city.
      wa_display-dispatchfromstatecode = wa_temp-dispatch_from_state_code.
      wa_display-dispatchfrompincode = wa_temp-dispatch_from_pincode.
*   WA_DISPLAY-dispatchfromphone = WA_TEMP-dispatch_from_pho
*   WA_DISPLAY-dispatchfromemail = WA_TEMP-dispatch_from
      wa_display-billtogstin = wa_temp-bill_to_gstin.
      wa_display-billtolegalname = wa_temp-bill_to_legal_name.
      wa_display-billtotradename = wa_temp-bill_to_trade_name.
      wa_display-billtoaddress1 = wa_temp-bill_to_address1.
      wa_display-billtoaddress2 = wa_temp-bill_to_address2.
      wa_display-billtocity = wa_temp-bill_to_city.
*   WA_DISPLAY-billtodistrict = WA_TEMP-bill_to_
      wa_display-billtostatecode = wa_temp-bill_to_state_code.
      wa_display-Pos = wa_temp-pos.
      wa_display-billtopincode = wa_temp-bill_to_pincode.
      wa_display-billtophone = wa_temp-bill_to_phone.
      wa_display-billtoemail = wa_temp-bill_to_email.
      wa_display-shiptogstin = wa_temp-ship_to_gstin.
      wa_display-shiptolegalname = wa_temp-ship_to_legal_name.
      wa_display-shiptotradename = wa_temp-ship_to_trade_name.
      wa_display-shiptoaddress1 = wa_temp-ship_to_address1.
      wa_display-shiptoaddress2 = wa_temp-ship_to_address2.
      wa_display-shiptocity = wa_temp-ship_to_city.
      wa_display-shiptostatecode = wa_temp-ship_to_state_code.
      wa_display-shiptopincode = wa_temp-ship_to_pincode.
*   WA_DISPLAY-shiptophone = WA_TEMP-ship_to_pho
*   WA_DISPLAY-shiptoemail = WA_TEMP-ship_to_
      CONDENSE: wa_display-documentvalue,
                wa_display-Taxablevalue,
                wa_display-Cgstamount,
                wa_display-sgstamount,
                wa_display-igstamount.
      APPEND wa_display TO lt_display.
    ENDLOOP.
  ENDMETHOD.


  METHOD call_cpi.
    TRY.
        DATA(lo_http) = NEW zcl_gbl_integration_utility( 'CYGNET' ).
      CATCH zcx_gbl_exception.
    ENDTRY.
    IF lo_http IS BOUND.
      TRY.
          lo_http->get_http_client( EXPORTING i_integrationscenario = 'CYGNET_CPI'
                                    IMPORTING e_client = DATA(lo_client)
                                              e_request = DATA(lo_request) ).
        CATCH zcx_gbl_exception.
      ENDTRY.
      IF lo_request IS BOUND.
        lo_request->set_version( version = if_web_http_request=>co_protocol_version_1_1 ).
        lo_request->set_header_field( i_name = 'Content-Type'
                                      i_value = 'application/json' ).
        lo_request->set_header_field( i_name = 'token'
                                      i_value = '1b369eb0fd7172f895e000f80cf6091f5570123dbe361e8be1918d66e6c5297a' ).
        lo_request->set_header_field( i_name = 'action'
                                      i_value = iv_action ).
        IF iv_reference_id IS NOT INITIAL.
          lo_request->set_header_field( i_name = 'referenceId'
                                        i_value = iv_reference_id ).
        ENDIF.
        TRY.
            IF iv_req_payload IS NOT INITIAL.
              lo_request->set_text( i_text = iv_req_payload ).
            ENDIF.
            DATA(lv_http_response) = lo_client->execute( i_method = if_web_http_client=>post ).
            ev_response = lv_http_response->get_text( ).
            lo_client->close( ).
          CATCH cx_web_http_client_error. " Global Exception Class for HTTP Client for Web Development
          CATCH cx_web_message_error. " Generic Web Development Exception
        ENDTRY.
      ENDIF.
    ENDIF.
  ENDMETHOD.


  METHOD get_data_for_challan.
    TYPES: BEGIN OF ty_t001w_rto_tmp,
             werks      TYPE werks,
             werks1     TYPE kunnr,
             name1      TYPE name1,
             name2      TYPE name2,
             pstlz      TYPE pstlz,
             ort01      TYPE ort01,
             regio      TYPE regio,
             j_1bbranch TYPE j_1bbranch,
           END OF ty_t001w_rto_tmp.
    TYPES: BEGIN OF ty_eway_bill_s,
             branch                  TYPE char100,
             fiscal_year             TYPE char100,
             from_gstin              TYPE char100,
             from_name               TYPE char100,
             from_address1           TYPE char100,
             from_address2           TYPE char100,
             from_place              TYPE char100,
             from_state              TYPE char100,
             return_period           TYPE char100,
             actual_from_state       TYPE char100,
             actual_from_gstin       TYPE char100,
             act_frm_oth_prty        TYPE char100,
             from_postal_code        TYPE char100,
             supply_type             TYPE char100,
             sub_supply_type         TYPE char100,
             sub_supply_description  TYPE char100,
             document_type           TYPE char100,
             document_no             TYPE char100,
             document_date           TYPE char100,
             reference_no            TYPE char100,
             reference_date          TYPE char100,
             transction_type         TYPE char100,
             to_gstin                TYPE char100,
             to_other_party_name     TYPE char100,
             to_address_line1        TYPE char100,
             to_address_line2        TYPE char100,
             to_place                TYPE char100,
             to_state                TYPE char100,
             actual_to_state         TYPE char100,
             actual_to_gstin         TYPE char100,
             act_oth_party_name      TYPE char100,
             to_postal_code          TYPE char100,
             value                   TYPE char100,
             indicate                TYPE char100,
             gstin_ecom              TYPE char100,
             shipping_no             TYPE char100,
             shipping_date           TYPE char100,
             shipping_port_cd        TYPE char100,
             product_name            TYPE char100,
             product_description     TYPE char100,
             hsn                     TYPE char100,
             total_quantity          TYPE char100,
             unit_measure            TYPE char100,
             rate                    TYPE char100,
             cess_rate               TYPE char100,
             cess_non_advalorem_rate TYPE char100,
             tax_value               TYPE char100,
             intr_tax_amt            TYPE char100,
             cent_tax_amt            TYPE char100,
             ut_tax_amt              TYPE char100,
             cess_amt                TYPE char100,
             cess_nonadv_amt         TYPE char100,
             oth_chrg                TYPE char100,
             elig_itc                TYPE char100,
             intr_tax_itc_amt        TYPE char100,
             cent_tax_itc_amt        TYPE char100,
             ut_tax_itc_amt          TYPE char100,
             cess_itc_amt            TYPE char100,
             transporter_id          TYPE char100,
             transporter_name        TYPE char100,
             transport_date          TYPE char100,
             transport_time          TYPE char100,
             dist_level              TYPE char100,
             transport_mode          TYPE char100,
             transport_doc_no        TYPE char100,
             transport_doc_date      TYPE char100,
             vehicle_number          TYPE char100,
             vehicle_type            TYPE char100,
             to_email_address        TYPE char100,
             to_mobile_numbers       TYPE char100,
             eway_bill_no            TYPE char100,
             custom1                 TYPE char100,
             custom2                 TYPE char100,
             custom3                 TYPE char100,
             custom10                TYPE char100,
             bukrs                   TYPE char100,
             document_item           TYPE char100,
             price_per_quantity      TYPE char100,
             display_error           TYPE char200,
           END OF ty_eway_bill_s.
    DATA: lv_item TYPE char10.
    DATA: gv_issue_unit TYPE zsd_t_unitotm-unit_id.
    DATA: gv_receive_unit            TYPE zsd_t_unitotm-unit_id,
          lt_t001w_rto_tmp           TYPE TABLE OF ty_t001w_rto_tmp,
          wa_final                   TYPE zsd_s_global_st,
          wa_display_table           TYPE ty_challan,
          lt_eb_eway_bill_s_disp_rto TYPE STANDARD TABLE OF zsd_s_global_st,
          wa_final_temp              TYPE ty_eway_bill_s,
          lt_e_tab_eway_bill_s       TYPE TABLE OF ty_eway_bill_s,
          lv_gjahr                   TYPE char4,
          wa_t001w_rto_tmp           TYPE ty_t001w_rto_tmp.
    DATA: gv_auart    TYPE zsd_t_unitotm-dauat.
*        TRA_ID      TYPE CHAR16,
*        TRAN_M      TYPE CHAR16,
*        TRA_NAME    TYPE CHAR20,
*        VEH_NO      TYPE CHAR10,
*        VEH_TYP     TYPE CHAR1,
*        LV_TAX_RATE TYPE CHAR5.
    SELECT documentnumber,
           documentdate,
           billfromgstin,
           dispatchfromgstin,
           dispatchfromname,
           dispatchfromadd1,
           dispatchfromadd2,
           dispatchfrompincode,
           dispatchfromcity,
           shiptogstin,
           shiptoadd1,
           shiptoadd2,
           shiptopincode,
           shiptocity,
           referencedocumentnumber,
           documenttype,
           ewb_status,
           einvmodule,
           ewaybillnumber,
           ewaybillvalidupto,
           ewaybillgenerated,
           ewaybillerror,
           ewaybilldistance,
           ewaygen,
           ewayfail,
           ewaycancel,
           ewaycanceldate,
           ewaycancelby,
           ewaycanceltime,
           ewb_icon,
           transporterid,
           transportername,
           transportmode,
           transporterdocdate,
           transporterdocnum,
           vehiclenumber,
           vehicletype
      FROM zmm_c_challan
      INTO TABLE @DATA(lt_einv_log)
      WHERE documentnumber IN @iv_billing_document.
    TYPES: BEGIN OF ty_doc_val,
             document_no    TYPE zmm_c_challan-documentnumber,
             document_value TYPE i_billingdocumentitemprcgelmnt-conditionamount,
             taxable_value  TYPE i_billingdocumentitemprcgelmnt-conditionamount,
             cgst           TYPE i_billingdocumentitemprcgelmnt-conditionamount,
             sgst           TYPE i_billingdocumentitemprcgelmnt-conditionamount,
             igst           TYPE i_billingdocumentitemprcgelmnt-conditionamount,
             othercharges   TYPE i_billingdocumentitemprcgelmnt-conditionamount,
             qty            TYPE i_billingdocumentitem-billingquantity,
           END OF ty_doc_val.
    DATA: lt_gt_doc_val TYPE TABLE OF ty_doc_val,
          wa_gs_doc_val TYPE ty_doc_val.
    SELECT * FROM zsd_t_unitotm INTO TABLE @DATA(lt_unit).
    SELECT * FROM zsd_t_201costcnt INTO TABLE @DATA(lt_cost).
    SELECT * FROM twlad INTO TABLE @DATA(lt_twlad).
    SELECT mblnr, mjahr, cpudt, usnam
      FROM mkpf
       WHERE mblnr IS NOT INITIAL
        AND mjahr IS NOT INITIAL
        AND mblnr IN @iv_billing_document
        AND mjahr IN @iv_gjahr
        AND cpudt IN @iv_document_date
      INTO TABLE @DATA(lt_mkpf_rto).
    IF lt_mkpf_rto IS NOT INITIAL.
      SELECT bname, persnumber, addrnumber
        FROM usr21
        INTO TABLE @DATA(lt_usr21)
        FOR ALL ENTRIES IN @lt_mkpf_rto
        WHERE bname = @lt_mkpf_rto-usnam.
      IF lt_usr21 IS NOT INITIAL.
        SELECT addrnumber, persnumber, smtp_addr
          FROM adr6
          INTO TABLE @DATA(lt_adr6)
          FOR ALL ENTRIES IN @lt_usr21
          WHERE addrnumber = @lt_usr21-addrnumber
            AND persnumber = @lt_usr21-persnumber.
      ENDIF.

      SELECT mblnr, mjahr, zeile, bwart, xauto, matnr, werks, lgort, lifnr, shkzg, menge, ebeln, kostl, umwrk, umlgo, aufnr, bukrs, salk3, urzei, lbkum, dmbtr, meins
        FROM mseg
        INTO TABLE @DATA(lt_mseg_rto)
        FOR ALL ENTRIES IN @lt_mkpf_rto
        WHERE mblnr = @lt_mkpf_rto-mblnr
          AND mjahr = @lt_mkpf_rto-mjahr
          AND xauto = @space.

      DATA: lv_total_dmbtr TYPE mseg-dmbtr.
      SELECT SUM( dmbtr )
        INTO @lv_total_dmbtr
        FROM mseg
        WHERE mblnr IN @iv_billing_document
          AND mjahr IN @iv_gjahr
          AND xauto = @space.
      IF lt_mseg_rto IS NOT INITIAL.
        SELECT ebeln, ebelp, werks, bukrs, lgort
          FROM ekpo
          INTO TABLE @DATA(lt_ekpo)
          FOR ALL ENTRIES IN @lt_mseg_rto
          WHERE ebeln = @lt_mseg_rto-ebeln
            AND werks NE ' '.
        SELECT aufnr, auart
          FROM aufk
          INTO TABLE @DATA(lt_aufk)
          FOR ALL ENTRIES IN @lt_mseg_rto
          WHERE aufnr = @lt_mseg_rto-aufnr.
        SORT lt_aufk BY aufnr.
      ENDIF.
      IF lt_mseg_rto IS NOT INITIAL AND iv_bukrs IS NOT INITIAL.
        DELETE lt_mseg_rto WHERE bukrs NE iv_bukrs.
      ENDIF.
      IF lt_ekpo IS NOT INITIAL.
        SELECT * FROM t001w
          INTO TABLE @DATA(lt_t001w_ekko)
          FOR ALL ENTRIES IN @lt_ekpo
          WHERE werks = @lt_ekpo-werks.
        IF lt_t001w_ekko IS NOT INITIAL.
          SELECT * FROM adrc
            INTO TABLE @DATA(lt_adrc_ekko)
            FOR ALL ENTRIES IN @lt_t001w_ekko
            WHERE addrnumber = @lt_t001w_ekko-adrnr.
        ENDIF.
      ENDIF.
      IF lt_mseg_rto IS NOT INITIAL.
        SELECT * FROM mara
          INTO TABLE @DATA(lt_mara_uqc)
          FOR ALL ENTRIES IN @lt_mseg_rto
          WHERE matnr = @lt_mseg_rto-matnr.
        SELECT * FROM mbew
          INTO TABLE @DATA(lt_mbew)
          FOR ALL ENTRIES IN @lt_mseg_rto
          WHERE matnr = @lt_mseg_rto-matnr
            AND bwkey = @lt_mseg_rto-werks
            AND bwtar = ' '.
        SELECT matnr, werks, steuc
          FROM marc
          INTO TABLE @DATA(lt_marc_hsn)
          FOR ALL ENTRIES IN @lt_mseg_rto
          WHERE matnr = @lt_mseg_rto-matnr
            AND werks = @lt_mseg_rto-werks.
        DATA(lt_mseg_rto_from) = lt_mseg_rto.
        IF lt_mseg_rto_from IS NOT INITIAL.
          SELECT * FROM t001w
            INTO TABLE @DATA(lt_t001_plant)
            FOR ALL ENTRIES IN @lt_mseg_rto_from
            WHERE werks = @lt_mseg_rto_from-werks.
          SELECT * FROM t001w
            APPENDING TABLE @lt_t001_plant
            FOR ALL ENTRIES IN @lt_mseg_rto_from
            WHERE werks = @lt_mseg_rto_from-kostl+1(4).
          SELECT * FROM t001w
            APPENDING TABLE @lt_t001_plant
            FOR ALL ENTRIES IN @lt_mseg_rto_from
            WHERE werks = @lt_mseg_rto_from-umwrk.
          IF lt_t001_plant IS NOT INITIAL.
            SELECT * FROM adrc
              INTO TABLE @DATA(lt_adrc_mseg)
              FOR ALL ENTRIES IN @lt_t001_plant
              WHERE addrnumber = @lt_t001_plant-adrnr.
            SELECT * FROM j_1bbranch
              INTO TABLE @DATA(lt_branch_plant)
              FOR ALL ENTRIES IN @lt_t001_plant
              WHERE branch = @lt_t001_plant-j_1bbranch.
          ENDIF.
        ENDIF.
      ENDIF.
      IF lt_mseg_rto IS NOT INITIAL.
        SELECT * FROM lfa1
          INTO TABLE @DATA(lt_lfa1)
          FOR ALL ENTRIES IN @lt_mseg_rto
          WHERE lifnr = @lt_mseg_rto-lifnr.
        IF lt_lfa1 IS NOT INITIAL.
          SELECT * FROM adrc
            APPENDING TABLE @lt_adrc_mseg
            FOR ALL ENTRIES IN @lt_lfa1
            WHERE addrnumber = @lt_lfa1-adrnr.
        ENDIF.
        SELECT * FROM t001w
          INTO TABLE @DATA(lt_t001w_rto)
          FOR ALL ENTRIES IN @lt_mseg_rto
          WHERE werks = @lt_mseg_rto-werks
             OR werks = @lt_mseg_rto-umwrk.
        IF lt_t001w_rto IS NOT INITIAL.
          SELECT * FROM j_1bbranch
            INTO TABLE @DATA(lt_branch1_plant)
            FOR ALL ENTRIES IN @lt_t001w_rto
            WHERE branch = @lt_t001w_rto-j_1bbranch.
          LOOP AT lt_t001w_rto INTO DATA(wa_t001w_rto).
            MOVE-CORRESPONDING wa_t001w_rto TO wa_t001w_rto_tmp.
            wa_t001w_rto_tmp-werks1 = wa_t001w_rto_tmp-werks.
            wa_t001w_rto_tmp-werks1 = |{ wa_t001w_rto_tmp-werks1 ALPHA = IN }|.
            APPEND wa_t001w_rto_tmp TO lt_t001w_rto_tmp.
            CLEAR wa_t001w_rto_tmp.
          ENDLOOP.
        ENDIF.
        SELECT matnr, maktx
          FROM makt
          INTO TABLE @DATA(lt_makt_rto)
          FOR ALL ENTRIES IN @lt_mseg_rto_from
          WHERE matnr = @lt_mseg_rto_from-matnr
            AND spras = @sy-langu.
      ENDIF.
    ENDIF.
    IF lt_mseg_rto IS NOT INITIAL.
      DATA(lt_mseg_641) = lt_mseg_rto.
      DELETE lt_mseg_641 WHERE xauto NE 'X' AND shkzg NE 'S' AND urzei NE '1'.
    ENDIF.
    IF lt_mseg_rto IS NOT INITIAL.
      DATA(lt_mseg_642) = lt_mseg_rto.
      DELETE lt_mseg_642 WHERE xauto = 'X' AND shkzg = 'S' AND urzei NE '1'.
    ENDIF.
    IF lt_mseg_rto IS NOT INITIAL.
      DATA(lt_mseg_541_542) = lt_mseg_rto.
      DELETE lt_mseg_541_542 WHERE xauto = 'X'.
      DELETE lt_mseg_541_542 WHERE lifnr EQ ' '.
    ENDIF.
    IF lt_mseg_541_542 IS NOT INITIAL.
      SELECT * FROM lfa1
        INTO TABLE @DATA(lt_lfa1_rto)
        FOR ALL ENTRIES IN @lt_mseg_541_542
        WHERE lifnr = @lt_mseg_541_542-lifnr.
      IF lt_lfa1_rto IS NOT INITIAL.
        SELECT * FROM adrc
          INTO TABLE @DATA(lt_adrc_lfa1)
          FOR ALL ENTRIES IN @lt_lfa1_rto
          WHERE addrnumber = @lt_lfa1_rto-adrnr.
      ENDIF.
    ENDIF.
    IF lt_t001w_rto IS NOT INITIAL.
      DATA: lt_a900      TYPE TABLE OF a900,
            lt_konp_igst TYPE TABLE OF konp,
            lt_konp_cgst TYPE TABLE OF konp,
            lt_konp_sgst TYPE TABLE OF konp.
      DATA: lv_igst_per TYPE kbetr,
            lv_cgst_per TYPE kbetr,
            lv_sgst_per TYPE kbetr.
      LOOP AT lt_mseg_rto ASSIGNING FIELD-SYMBOL(<fs_mseg>).
        READ TABLE lt_t001w_rto INTO DATA(wa_src_plant)
          WITH KEY werks = <fs_mseg>-werks.
        READ TABLE lt_t001w_rto INTO DATA(wa_dst_plant)
          WITH KEY werks = <fs_mseg>-umwrk.
        READ TABLE lt_marc_hsn INTO DATA(wa_marc_hsn)
          WITH KEY matnr = <fs_mseg>-matnr
                   werks = <fs_mseg>-werks.
        IF sy-subrc = 0.
          SELECT *
            FROM a980
            INTO TABLE @DATA(lt_a9001)
            WHERE steuc = @wa_marc_hsn-steuc
              AND mwskz = 'G1'.
          IF lt_a9001 IS INITIAL.
            SELECT *
              FROM a900
              INTO CORRESPONDING FIELDS OF TABLE @lt_a9001
              WHERE steuc = @wa_marc_hsn-steuc
                AND mwskz = 'G1'.
          ENDIF.
          IF sy-subrc = 0.
            LOOP AT lt_a9001 INTO DATA(wa_a900).
              SELECT SINGLE kbetr
                FROM konp
                INTO @DATA(lv_igst)
                WHERE knumh = @wa_a900-knumh
                  AND kschl = 'JIIG'.
              IF sy-subrc = 0.
                lv_igst_per = lv_igst / 10.
              ENDIF.
              SELECT SINGLE kbetr
                FROM konp
                INTO @DATA(lv_cgst)
                WHERE knumh = @wa_a900-knumh
                  AND kschl = 'JICG'.
              IF sy-subrc = 0.
                lv_cgst_per = lv_cgst / 10.
              ENDIF.
              SELECT SINGLE kbetr
                FROM konp
                INTO @DATA(lv_sgst)
                WHERE knumh = @wa_a900-knumh
                  AND kschl = 'JISG'.
              IF sy-subrc = 0.
                lv_sgst_per = lv_sgst / 10.
              ENDIF.
            ENDLOOP.
          ENDIF.
        ENDIF.
      ENDLOOP.
    ENDIF.
    SORT lt_adrc_lfa1 BY addrnumber.
    SORT lt_mkpf_rto BY mblnr.
    SORT lt_mseg_rto BY mblnr.
    SORT lt_mseg_rto_from BY mblnr mjahr.
    SORT lt_t001w_rto BY werks.
    SORT lt_t001w_rto_tmp BY werks1.
    SORT lt_lfa1_rto BY lifnr.
    SORT lt_adrc_lfa1 BY addrnumber.
    SORT lt_makt_rto BY matnr.
    SORT lt_t001_plant BY werks.
    SORT lt_adrc_mseg BY addrnumber.
    SORT lt_branch_plant BY bukrs branch.
    SORT lt_marc_hsn BY matnr werks.
    SORT lt_branch1_plant BY branch bukrs.
    SORT lt_ekpo BY ebeln.
    SORT lt_unit BY werks lgort.
    SORT lt_cost BY kostl.
    SORT lt_twlad BY werks lgort.
    SORT lt_aufk BY aufnr.
    SORT lt_mseg_641 BY mblnr.
    SORT lt_mseg_541_542 BY mblnr.
    SORT lt_mbew BY matnr bwkey.
    SORT lt_mara_uqc BY matnr.
    SORT lt_lfa1 BY lifnr.
    DATA: wa_uom TYPE zsd_t_einv_uom.
    DATA: lv_move_value TYPE p DECIMALS 2.
    CLEAR wa_final.
    SORT lt_unit BY werks unit_id dauat lgort.
    SELECT matnr, bwkey, verpr
      FROM mbew
      INTO TABLE @DATA(lt_541_542_value)
      FOR ALL ENTRIES IN @lt_mseg_rto
      WHERE matnr = @lt_mseg_rto-matnr
        AND bwkey = @lt_mseg_rto-werks.
    LOOP AT lt_mseg_rto INTO DATA(wa_mseg_rto) WHERE xauto NE 'X'.
      wa_final-company_code = wa_mseg_rto-bukrs.
      wa_final-custom10 = wa_mseg_rto-bwart.
      lv_move_value = wa_mseg_rto-salk3 / wa_mseg_rto-lbkum.
      CASE wa_mseg_rto-bwart.
        WHEN '201' OR '202'.
          READ TABLE lt_unit INTO DATA(wa_unit)
            WITH KEY werks = wa_mseg_rto-werks lgort = wa_mseg_rto-lgort.
          IF sy-subrc <> 0.
            DATA(lv_chl_msg) = 'Issueing Location is blank, Please update ZUNITOTM Table from PMIC Team'.
          ELSE.
            IF wa_unit-unit_id IS INITIAL.
              lv_chl_msg = 'Issueing Unit is Blank, Please update ZUNITOTM Table from PMIC Team'.
            ELSE.
              gv_issue_unit = wa_unit-unit_id.
              READ TABLE lt_cost INTO DATA(wa_cost)
                WITH KEY kostl = wa_mseg_rto-kostl BINARY SEARCH.
              IF sy-subrc <> 0.
                lv_chl_msg = 'Cost Center is missing, Please update Z201_COSTCENTER Table from PMIC Team'.
              ELSE.
                IF wa_cost-unit_id IS INITIAL.
                  lv_chl_msg = 'Receving Unit Id Missing, Please Update Z201_COSTCENTER Table From PMIC Team'.
                ELSE.
                  gv_receive_unit = wa_cost-unit_id.
                  IF gv_issue_unit = gv_receive_unit.
                    lv_chl_msg = 'EWAYBILL NOT NEEDED'.
                  ENDIF.
                ENDIF.
              ENDIF.
            ENDIF.
          ENDIF.
        WHEN '261' OR '262'.
          READ TABLE lt_unit INTO wa_unit
            WITH KEY werks = wa_mseg_rto-werks lgort = wa_mseg_rto-lgort.
          IF sy-subrc <> 0.
            lv_chl_msg = 'Issuing Location is blank, Please update ZUNITOTM Table from PMIC Team'.
          ELSE.
            IF wa_unit-unit_id IS INITIAL.
              lv_chl_msg = 'Issuing Unit  is Blank, Please update ZUNITOTM Table from PMIC Team'.
            ELSE.
              gv_issue_unit = wa_unit-unit_id.
              READ TABLE lt_aufk INTO DATA(wa_aufk)
                WITH KEY aufnr = wa_mseg_rto-aufnr BINARY SEARCH.
              IF sy-subrc <> 0.
                lv_chl_msg = 'Order Number is not updated with Master'.
              ELSE.
                IF wa_aufk-auart IS INITIAL.
                  lv_chl_msg = 'Order Type is not updated with Master'.
                ELSE.
                  READ TABLE lt_unit INTO wa_unit WITH KEY dauat = wa_aufk-auart.
                  IF sy-subrc = 0.
                    gv_receive_unit = wa_unit-unit_id.
                    IF gv_issue_unit = gv_receive_unit.
                      lv_chl_msg = 'EWAYBILL NOT NEEDED'.
                    ENDIF.
                  ELSE.
                    lv_chl_msg = 'Receving Unit Is not Maintain'.
                  ENDIF.
                ENDIF.
              ENDIF.
            ENDIF.
          ENDIF.
        WHEN '311' OR '312'.
          READ TABLE lt_unit INTO wa_unit
            WITH KEY werks = wa_mseg_rto-werks lgort = wa_mseg_rto-lgort.
          IF sy-subrc <> 0.
            lv_chl_msg = 'Issuing Unit is blank, Please update ZUNITOTM Table from PMIC Team'.
          ELSE.
            IF wa_unit-unit_id IS INITIAL.
              lv_chl_msg = 'Issuing Unit is Blank, Please update ZUNITOTM Table from PMIC Team'.
            ELSE.
              gv_issue_unit = wa_unit-unit_id.
              READ TABLE lt_unit INTO wa_unit
                WITH KEY werks = wa_mseg_rto-werks lgort = wa_mseg_rto-umlgo.
              IF sy-subrc <> 0.
                lv_chl_msg = 'Issuing Unit is missing, Please update Z201_COSTCENTER Table from PMIC Team'.
              ELSE.
                IF wa_unit-unit_id IS INITIAL.
                  lv_chl_msg = 'Order Type Missing, Please Update Z201_COSTCENTER Table From PMIC Team'.
                ELSE.
                  gv_receive_unit = wa_unit-unit_id.
                  IF gv_issue_unit = gv_receive_unit.
                    lv_chl_msg = 'EWAYBILL NOT NEEDED'.
                  ENDIF.
                ENDIF.
              ENDIF.
            ENDIF.
          ENDIF.
      ENDCASE.
      CASE wa_mseg_rto-bwart.
        WHEN '201'.
          TRY.
              DATA(lv_werks) = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr ]-werks.
              DATA(lv_adrnr) = lt_t001_plant[ werks = lv_werks ]-adrnr.
              DATA(wa_adrc) = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billfromaddress1 = wa_adrc-street.
              wa_final-billfromaddress2 = wa_adrc-str_suppl1.
              wa_final-billfromcity = wa_adrc-city1.
              wa_final-billfrompincode = wa_adrc-post_code1.
              wa_final-billfromlegalname = wa_adrc-name1.
              DATA(lv_branch) = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              DATA(wa_branch1) = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billfromgstin = wa_branch1-gstin.
              wa_final-billfromstatecode = wa_branch1-gstin(2).
              CLEAR: lv_adrnr, wa_adrc, lv_branch, wa_branch1.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
          TRY.
              DATA(lv_kostl) = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr ]-kostl+1(4).
              lv_adrnr = lt_t001_plant[ werks = lv_kostl ]-adrnr.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billtoaddress1 = wa_adrc-street.
              wa_final-billtoaddress2 = wa_adrc-str_suppl1.
              wa_final-billtocity = wa_adrc-city1.
              wa_final-billtopincode = wa_adrc-post_code1.
              wa_final-billtolegalname = wa_adrc-name1.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billtogstin = wa_branch1-gstin.
              wa_final-billtostatecode = wa_branch1-gstin(2).
              wa_final-pos = wa_adrc-region.
              CLEAR: lv_werks, lv_kostl, lv_adrnr, wa_adrc, lv_branch, wa_branch1.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
        WHEN '202'.
          TRY.
              lv_werks = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr ]-werks.
              lv_adrnr = lt_t001_plant[ werks = lv_werks ]-adrnr.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billtoaddress1 = wa_adrc-street.
              wa_final-billtoaddress2 = wa_adrc-str_suppl1.
              wa_final-billtocity = wa_adrc-city1.
              wa_final-billtopincode = wa_adrc-post_code1.
              wa_final-billtolegalname = wa_adrc-name1.
              wa_final-pos = wa_adrc-region.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billtogstin = wa_branch1-gstin.
              wa_final-billtostatecode = wa_branch1-gstin(2).
              CLEAR: lv_adrnr, wa_adrc, lv_branch, wa_branch1.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
          TRY.
              lv_kostl = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr ]-kostl+1(4).
              lv_adrnr = lt_t001_plant[ werks = lv_kostl ]-adrnr.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billfromaddress1 = wa_adrc-street.
              wa_final-billfromaddress2 = wa_adrc-str_suppl1.
              wa_final-billfromcity = wa_adrc-city1.
              wa_final-billfrompincode = wa_adrc-post_code1.
              wa_final-billfromlegalname = wa_adrc-name1.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billfromgstin = wa_branch1-gstin.
              wa_final-billfromstatecode = wa_branch1-gstin(2).
              CLEAR: lv_kostl, lv_adrnr, wa_adrc, lv_branch, wa_branch1, lv_werks.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
        WHEN '541' OR '301' OR '641' OR '122' OR '161'.
          TRY.
              lv_werks = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr xauto = '' ]-werks.
              lv_adrnr = lt_t001_plant[ werks = lv_werks ]-adrnr.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billfromaddress1 = wa_adrc-street.
              wa_final-billfromaddress2 = wa_adrc-str_suppl1.
              wa_final-billfromcity = wa_adrc-city1.
              wa_final-billfrompincode = wa_adrc-post_code1.
              wa_final-billfromlegalname = wa_adrc-name1.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billfromgstin = wa_branch1-gstin.
              wa_final-billfromstatecode = wa_branch1-gstin(2).
              CLEAR: lv_adrnr, wa_adrc, lv_branch, wa_branch1.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
          TRY.
              IF wa_mseg_rto-bwart EQ '301' OR wa_mseg_rto-bwart EQ '641'.
                DATA(lv_umwrk) = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr xauto = '' ]-umwrk.
                lv_adrnr = lt_t001w_rto[ werks = lv_umwrk ]-adrnr.
              ELSEIF wa_mseg_rto-bwart EQ '541' OR wa_mseg_rto-bwart EQ '122' OR wa_mseg_rto-bwart EQ '161'.
                DATA(lv_lifnr) = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr ]-lifnr.
                lv_adrnr = lt_lfa1_rto[ lifnr = lv_lifnr ]-adrnr.
              ENDIF.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billtoaddress1 = wa_adrc-street.
              wa_final-billtoaddress2 = wa_adrc-str_suppl1.
              wa_final-billtocity = wa_adrc-city1.
              wa_final-billtopincode = wa_adrc-post_code1.
              wa_final-billtolegalname = wa_adrc-name1.
              wa_final-pos = wa_adrc-region.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billtogstin = wa_branch1-gstin.
              wa_final-billtostatecode = wa_branch1-gstin(2).
              CLEAR: lv_lifnr, lv_adrnr, wa_adrc, lv_branch, wa_branch1, lv_werks, lv_umwrk.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
        WHEN '302' OR '642'.
          TRY.
              lv_werks = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr xauto = '' ]-werks.
              lv_adrnr = lt_t001_plant[ werks = lv_werks ]-adrnr.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billtoaddress1 = wa_adrc-street.
              wa_final-billtoaddress2 = wa_adrc-str_suppl1.
              wa_final-billtocity = wa_adrc-city1.
              wa_final-billtopincode = wa_adrc-post_code1.
              wa_final-billtolegalname = wa_adrc-name1.
              wa_final-pos = wa_adrc-region.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billtogstin = wa_branch1-gstin.
              wa_final-billtostatecode = wa_branch1-gstin(2).
              CLEAR: lv_adrnr, wa_adrc, lv_branch, wa_branch1.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
          TRY.
              IF wa_mseg_rto-bwart EQ '301' OR wa_mseg_rto-bwart EQ '302' OR wa_mseg_rto-bwart EQ '641' OR wa_mseg_rto-bwart EQ '642'.
                lv_umwrk = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr xauto = '' ]-umwrk.
                lv_adrnr = lt_t001w_rto[ werks = lv_umwrk ]-adrnr.
              ELSEIF wa_mseg_rto-bwart EQ '541' OR wa_mseg_rto-bwart EQ '122' OR wa_mseg_rto-bwart EQ '161'.
                lv_lifnr = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr ]-lifnr.
                lv_adrnr = lt_lfa1_rto[ lifnr = lv_lifnr ]-adrnr.
              ENDIF.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billfromaddress1 = wa_adrc-street.
              wa_final-billfromaddress2 = wa_adrc-str_suppl1.
              wa_final-billfromcity = wa_adrc-city1.
              wa_final-billfrompincode = wa_adrc-post_code1.
              wa_final-billfromlegalname = wa_adrc-name1.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billfromgstin = wa_branch1-gstin.
              wa_final-billfromstatecode = wa_branch1-gstin(2).
              CLEAR: lv_lifnr, lv_adrnr, wa_adrc, lv_branch, wa_branch1, lv_werks, lv_umwrk.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
        WHEN '542' OR '123' OR '162'.
          TRY.
              lv_werks = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr xauto = '' ]-werks.
              lv_adrnr = lt_t001_plant[ werks = lv_werks ]-adrnr.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billtoaddress1 = wa_adrc-street.
              wa_final-billtoaddress2 = wa_adrc-str_suppl1.
              wa_final-billtocity = wa_adrc-city1.
              wa_final-billtopincode = wa_adrc-post_code1.
              wa_final-billtolegalname = wa_adrc-name1.
              wa_final-pos = wa_adrc-region.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billtogstin = wa_branch1-gstin.
              wa_final-billtostatecode = wa_branch1-gstin(2).
              CLEAR: lv_adrnr, wa_adrc, lv_branch, wa_branch1.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
          TRY.
              lv_lifnr = lt_mseg_rto[ mblnr = wa_mseg_rto-mblnr mjahr = wa_mseg_rto-mjahr ]-lifnr.
              lv_adrnr = lt_lfa1_rto[ lifnr = lv_lifnr ]-adrnr.
              wa_adrc = lt_adrc_mseg[ addrnumber = lv_adrnr ].
              wa_final-billfromaddress1 = wa_adrc-street.
              wa_final-billfromaddress2 = wa_adrc-str_suppl1.
              wa_final-billfromcity = wa_adrc-city1.
              wa_final-billfrompincode = wa_adrc-post_code1.
              wa_final-billfromlegalname = wa_adrc-name1.
              lv_branch = lt_t001w_rto[ werks = lv_werks ]-j_1bbranch.
              wa_branch1 = lt_branch1_plant[ branch = lv_branch ].
              wa_final-billfromgstin = wa_branch1-gstin.
              wa_final-locationgstin = wa_final-billfromgstin.
              wa_final-billfromstatecode = wa_branch1-gstin(2).
              CLEAR: lv_lifnr, lv_adrnr, wa_adrc, lv_branch, wa_branch1, lv_werks.
            CATCH cx_sy_itab_line_not_found.
          ENDTRY.
      ENDCASE.
      wa_final-locationgstin = COND #( WHEN wa_final-locationgstin IS INITIAL
                                       THEN wa_final-billfromgstin ).
      wa_final-fiscalyear = wa_mseg_rto-mjahr.
      wa_final-distancelevel = '0'.
      DATA: lv_total TYPE mseg-dmbtr.
      wa_final-documentvalue = wa_mseg_rto-dmbtr.
      SELECT SINGLE * FROM zsd_t_einv_uom
        INTO wa_uom
        WHERE sap_uom = wa_mseg_rto-meins.
      wa_final-uqc = wa_uom-gst_uom.
      LOOP AT lt_mseg_rto INTO DATA(wa_mseg_rto2).
        lv_total = lv_total + wa_mseg_rto2-dmbtr.
      ENDLOOP.
      wa_final-grossamount = wa_mseg_rto-dmbtr.
      wa_final-taxablevalue = wa_mseg_rto-dmbtr.
      CLEAR: lv_total.
      wa_final-quantity = wa_mseg_rto-menge.
      wa_final-supplytype = 'S'.
      wa_final-documenttype = 'CHL'.
      wa_final-transactiontype = 'OTH'.
      wa_final-transactiontypedescription = 'OTH'.
      READ TABLE lt_mkpf_rto INTO DATA(wa_mkpf_rto)
        WITH KEY mblnr = wa_mseg_rto-mblnr BINARY SEARCH.
      wa_final-documentnumber = wa_mkpf_rto-mblnr.
      wa_final-fiscalyear = wa_mkpf_rto-mjahr.
      CONCATENATE wa_mkpf_rto-cpudt+6(2) '-' wa_mkpf_rto-cpudt+4(2) '-' wa_mkpf_rto-cpudt(4) '-' INTO wa_final-documentdate.
      wa_final-returnperiod = COND #( WHEN wa_mkpf_rto-cpudt IS NOT INITIAL
                                      THEN |{ wa_mkpf_rto-cpudt+4(2) }{ wa_mkpf_rto-cpudt+0(4) }| ).
      READ TABLE lt_marc_hsn INTO wa_marc_hsn
        WITH KEY matnr = wa_mseg_rto-matnr werks = wa_mseg_rto-werks BINARY SEARCH.
      IF sy-subrc = 0.
        wa_final-hsn = wa_marc_hsn-steuc.
      ENDIF.
      READ TABLE lt_usr21 INTO DATA(wa_usr21)
        WITH KEY bname = wa_mkpf_rto-usnam.
      IF sy-subrc = 0.
        READ TABLE lt_adr6 INTO DATA(wa_ard6)
          WITH KEY addrnumber = wa_usr21-addrnumber persnumber = wa_usr21-persnumber.
        IF sy-subrc = 0.
          wa_final-toemailaddress = wa_ard6-smtp_addr.
        ENDIF.
      ENDIF.
      READ TABLE lt_makt_rto INTO DATA(wa_makt_rto)
        WITH KEY matnr = wa_mseg_rto-matnr BINARY SEARCH.
      IF sy-subrc = 0.
        wa_final-itemname = wa_mseg_rto-matnr.
        wa_final-itemdescription = wa_makt_rto-maktx.
      ENDIF.
      REPLACE ALL OCCURRENCES OF ',' IN wa_final-itemname WITH space.
      REPLACE ALL OCCURRENCES OF '\' IN wa_final-itemname WITH space.
      REPLACE ALL OCCURRENCES OF '`' IN wa_final-itemname WITH space.
      CONDENSE wa_final-taxablevalue.
      IF wa_final-documenttype IS INITIAL.
        wa_final-documenttype = 'OTH'.
      ENDIF.
      IF wa_final-quantity = 0 OR wa_final-quantity IS INITIAL.
        wa_final-quantity = 1.
      ENDIF.
      DATA: lv_rate TYPE p DECIMALS 2.
      lv_rate = ( wa_final-documentvalue / wa_final-quantity ).
      wa_final-priceperquantity = lv_rate.
      IF wa_mseg_rto-bwart = '541' OR wa_mseg_rto-bwart = '542'.
        DATA(lv_value_tmp) = VALUE #( lt_541_542_value[ matnr = wa_mseg_rto-matnr
                                                         bwkey = wa_mseg_rto-werks ]-verpr OPTIONAL ).
        wa_final-taxablevalue = lv_value_tmp * wa_final-quantity.
        wa_final-grossamount = wa_final-taxablevalue.
      ENDIF.
      IF wa_final-billfromstatecode = wa_final-billtostatecode.
        CLEAR lv_igst_per.
      ELSE.
        CLEAR: lv_cgst_per, lv_sgst_per.
      ENDIF.
      IF lv_igst_per IS NOT INITIAL.
        wa_final-rate = lv_igst_per.
        wa_final-igstamount = ( wa_final-taxablevalue * lv_igst_per ) / 100.
      ENDIF.
      IF lv_cgst_per IS NOT INITIAL AND lv_sgst_per IS NOT INITIAL.
        wa_final-cgstamount = ( wa_final-taxablevalue * lv_cgst_per ) / 100.
        wa_final-sgstamount = ( wa_final-taxablevalue * lv_sgst_per ) / 100.
        wa_final-rate = lv_sgst_per + lv_cgst_per.
      ENDIF.
      wa_final-documentvalue = wa_final-taxablevalue + wa_final-igstamount + wa_final-cgstamount + wa_final-sgstamount.
      wa_final-customitem10 = lv_chl_msg.
      CONDENSE: wa_final-documentvalue, wa_final-taxablevalue, wa_final-grossamount, wa_final-igstamount, wa_final-cgstamount, wa_final-sgstamount.
      APPEND wa_final TO lt_eb_eway_bill_s_disp_rto.
      CLEAR: wa_final, lv_chl_msg, wa_mseg_rto, lv_move_value, wa_display_table.
    ENDLOOP.

    DATA:
      ls_dc_item                 TYPE zmm_t_dc_iut,
      ls_dc_header               TYPE zmm_t_dc_iut_hd,
      ls_eb_eway_bill_s_disp_rto TYPE zsd_s_global_st.

     DATA lv_created_at TYPE zmm_t_dc_iut_hd-created_at.

*
    DATA lr_created_at TYPE RANGE OF zmm_t_dc_iut_hd-created_at.

*    LOOP AT iv_document_date ASSIGNING FIELD-SYMBOL(<ls_date>).
*    APPEND VALUE #(
*    sign   = <ls_date>-sign
*    option = <ls_date>-option
*    low    = CONV zmm_t_dc_iut_hd-created_at(
*               |{ <ls_date>-low }0000000| )
*    high   = CONV zmm_t_dc_iut_hd-created_at(
*               |{ <ls_date>-high }2359599| )
*     ) TO lr_created_at.
*    ENDLOOP.
*
    SELECT
      i~dc_num,
      i~dc_item,
      i~from_gstin,
      i~from_address,
      i~from_plant,
      i~to_gstin,
      i~vendor_name,
      i~to_address,
      i~hsn,
      i~to_plant,
      i~uom,
      i~quantity,
      i~rate,
      i~taxable_value,
      i~igst_percent,
      i~cgst_percent,
      i~sgst_percent,
      h~created_at,
      h~taxable_amount,
      pf~regio AS from_regio,
      pf~pstlz AS from_pincode,
      pf~regio AS to_regio,
      pf~pstlz AS to_pincode
      FROM zmm_t_dc_iut AS i
      INNER JOIN zmm_t_dc_iut_hd AS h
      ON h~dc_num = i~dc_num
      LEFT OUTER JOIN lfa1 AS pf
      ON pf~werks = i~from_plant
      WHERE cast(
             substring(
              cast( h~created_at AS CHAR( 17 ) ) , 1, 8
            ) as dats  ) IN @iv_document_date
      INTO TABLE @DATA(ls_dc_dta).
*
*
*
    IF ls_dc_dta IS NOT INITIAL.

*      clear lt_eb_eway_bill_s_disp_rto.
*       REFRESH lt_eb_eway_bill_s_disp_rto.
*       FREE lt_eb_eway_bill_s_disp_rto.
      LOOP AT ls_dc_dta INTO DATA(ls_dc_data).
        CLEAR ls_eb_eway_bill_s_disp_rto.


        ls_eb_eway_bill_s_disp_rto-locationgstin    = ls_dc_data-from_gstin.
        ls_eb_eway_bill_s_disp_rto-purpose          = 'EWB'.
        ls_eb_eway_bill_s_disp_rto-supplytype       = 'S'.
        ls_eb_eway_bill_s_disp_rto-documenttype     = 'CHL'.
        ls_eb_eway_bill_s_disp_rto-transactiontype  = 'OWN'.

        CONVERT TIME STAMP ls_dc_data-created_at
        TIME ZONE sy-zonlo
        INTO DATE DATA(lv_doc_date)
         TIME DATA(lv_doc_time).

         DATA(lv_formatted_date) =
         |{ lv_doc_date+6(2) }-{ lv_doc_date+4(2) }-{ lv_doc_date+0(4) }|.

        ls_eb_eway_bill_s_disp_rto-documentnumber   = ls_dc_data-dc_num.
        ls_eb_eway_bill_s_disp_rto-documentdate     = lv_formatted_date.



        ls_eb_eway_bill_s_disp_rto-billfromgstin     = ls_dc_data-from_gstin.
        ls_eb_eway_bill_s_disp_rto-billfromaddress1  = ls_dc_data-from_address.
        ls_eb_eway_bill_s_disp_rto-billfromstatecode = ls_dc_data-from_regio.
        ls_eb_eway_bill_s_disp_rto-billfrompincode   = ls_dc_data-from_pincode.

        ls_eb_eway_bill_s_disp_rto-billtogstin       = ls_dc_data-to_gstin.
        ls_eb_eway_bill_s_disp_rto-billtotradename   = ls_dc_data-vendor_name.
        ls_eb_eway_bill_s_disp_rto-billtoaddress1    = ls_dc_data-to_address.
        ls_eb_eway_bill_s_disp_rto-billtostatecode   = ls_dc_data-to_regio.
        ls_eb_eway_bill_s_disp_rto-billtopincode     = ls_dc_data-to_pincode.

        ls_eb_eway_bill_s_disp_rto-documentvalue = ls_dc_data-taxable_amount.

        "Item details
        ls_eb_eway_bill_s_disp_rto-hsn           = ls_dc_data-hsn.
        ls_eb_eway_bill_s_disp_rto-uqc           = ls_dc_data-uom.
        ls_eb_eway_bill_s_disp_rto-quantity      = ls_dc_data-quantity.
        ls_eb_eway_bill_s_disp_rto-rate          = ls_dc_data-rate.
        ls_eb_eway_bill_s_disp_rto-taxablevalue  = ls_dc_data-taxable_value.
        ls_eb_eway_bill_s_disp_rto-igstamount    = ls_dc_data-igst_percent.
        ls_eb_eway_bill_s_disp_rto-cgstamount    = ls_dc_data-cgst_percent.
        ls_eb_eway_bill_s_disp_rto-sgstamount    = ls_dc_data-sgst_percent.


        APPEND ls_eb_eway_bill_s_disp_rto
          TO lt_eb_eway_bill_s_disp_rto.
      ENDLOOP.
    ENDIF.

    TYPES: BEGIN OF ty_item,
             serial_number                 TYPE string,
             is_service                    TYPE string,
             hsn                           TYPE c LENGTH 8,
             product_code                  TYPE string,
             item_name                     TYPE string,
             item_description              TYPE string,
             nature_of_jw_done             TYPE string,
             barcode                       TYPE string,
             uqc                           TYPE string,
             quantity                      TYPE string,
             free_quantity                 TYPE string,
             loss_unit_of_measure          TYPE string,
             loss_total_quantity           TYPE string,
             rate                          TYPE string,
             cess_rate                     TYPE string,
             state_cess_rate               TYPE string,
             tds_rate                      TYPE string,
             cess_non_advalorem_rate       TYPE string,
             price_per_quantity            TYPE string,
             discount_amount               TYPE string,
             gross_amount                  TYPE string,
             other_charges                 TYPE string,
             taxable_value                 TYPE string,
             pre_tax_value                 TYPE string,
             igst_amount                   TYPE string,
             cgst_amount                   TYPE string,
             sgst_amount                   TYPE string,
             cess_amount                   TYPE string,
             tds_amount                    TYPE string,
             state_cess_amount             TYPE string,
             state_cess_non_adva_lo_amount TYPE string,
             cess_non_advalorem_amount     TYPE string,
             order_line_reference          TYPE string,
             origin_country                TYPE string,
             item_serial_number            TYPE string,
             item_total                    TYPE string,
             item_attribute_details        TYPE string,
             tax_type                      TYPE string,
             batch_name_number             TYPE string,
             batch_expiry_date             TYPE string,
             warranty_date                 TYPE string,
             itc_eligibility               TYPE string,
             gst_act_or_rule_section       TYPE string,
             itc_igst_amount               TYPE string,
             itc_cgst_amount               TYPE string,
             itc_sgst_amount               TYPE string,
             itc_cess_amount               TYPE string,
             custom_item1                  TYPE string,
             custom_item2                  TYPE string,
             custom_item3                  TYPE string,
             custom_item4                  TYPE string,
             custom_item5                  TYPE string,
             custom_item6                  TYPE string,
             custom_item7                  TYPE string,
             custom_item8                  TYPE string,
             custom_item9                  TYPE string,
             custom_item10                 TYPE string,
             custom_item11                 TYPE string,
             custom_item12                 TYPE string,
             custom_item13                 TYPE string,
             custom_item14                 TYPE string,
             custom_item15                 TYPE string,
             custom_item16                 TYPE string,
             custom_item17                 TYPE string,
             custom_item18                 TYPE string,
             custom_item19                 TYPE string,
             custom_item20                 TYPE string,
           END OF ty_item.
    DATA: lt_item TYPE STANDARD TABLE OF ty_item,
          wa_item TYPE ty_item.
    TYPES: BEGIN OF ty_data,
             location_gstin                 TYPE string,
             location_name                  TYPE string,
             ref_id                         TYPE string,
             ref_date                       TYPE string,
             return_period                  TYPE string,
             liability_discharge_return_p   TYPE string,
             itc_claim_return_period        TYPE string,
             purpose                        TYPE string,
             auto_push_or_generate          TYPE string,
             supply_type                    TYPE string,
             irn                            TYPE string,
             document_type                  TYPE string,
             credit_note_reason             TYPE string,
             transaction_type               TYPE string,
             transaction_nature             TYPE string,
             transaction_type_description   TYPE string,
             taxpayer_type                  TYPE string,
             document_number                TYPE string,
             document_series_code           TYPE string,
             document_date                  TYPE string,
             cancelled_date                 TYPE string,
             _reference_id                  TYPE string,
             bill_from_gstin                TYPE string,
             bill_from_legal_name           TYPE string,
             bill_from_trade_name           TYPE string,
             bill_from_vendor_code          TYPE string,
             bill_from_address1             TYPE string,
             bill_from_address2             TYPE string,
             bill_from_city                 TYPE string,
             bill_from_state_code           TYPE string,
             bill_from_pincode              TYPE string,
             bill_from_phone                TYPE string,
             bill_from_email                TYPE string,
             dispatch_from_gstin            TYPE string,
             dispatch_from_trade_name       TYPE string,
             dispatch_from_vendor_code      TYPE string,
             dispatch_from_address1         TYPE string,
             dispatch_from_address2         TYPE string,
             dispatch_from_city             TYPE string,
             dispatch_from_state_code       TYPE string,
             dispatch_from_pincode          TYPE string,
             bill_to_gstin                  TYPE string,
             bill_to_legal_name             TYPE string,
             bill_to_trade_name             TYPE string,
             bill_to_vendor_code            TYPE string,
             bill_to_address1               TYPE string,
             bill_to_address2               TYPE string,
             bill_to_city                   TYPE string,
             bill_to_state_code             TYPE string,
             bill_to_pincode                TYPE string,
             bill_to_phone                  TYPE string,
             bill_to_email                  TYPE string,
             ship_to_gstin                  TYPE string,
             ship_to_legal_name             TYPE string,
             ship_to_trade_name             TYPE string,
             ship_to_vendor_code            TYPE string,
             ship_to_address1               TYPE string,
             ship_to_address2               TYPE string,
             ship_to_city                   TYPE string,
             ship_to_state_code             TYPE string,
             ship_to_pincode                TYPE string,
             payment_type                   TYPE string,
             payment_mode                   TYPE string,
             payment_amount                 TYPE string,
             advance_paid_amount            TYPE string,
             payment_date                   TYPE string,
             payment_remarks                TYPE string,
             payment_terms                  TYPE string,
             payment_instruction            TYPE string,
             payee_name                     TYPE string,
             upi_id                         TYPE string,
             payee_account_number           TYPE string,
             payee_merchant_code            TYPE string,
             payment_amount_due             TYPE string,
             payment_due_date               TYPE string,
             ifsc                           TYPE string,
             credit_transfer                TYPE string,
             direct_debit                   TYPE string,
             credit_days                    TYPE string,
             transaction_id                 TYPE string,
             transaction_reference_id       TYPE string,
             transaction_note               TYPE string,
             payment_minimum_amount         TYPE string,
             transaction_reference_url      TYPE string,
             credit_availed_date            TYPE string,
             credit_reversal_date           TYPE string,
             ref_document_remarks           TYPE string,
             ref_document_period_start_date TYPE string,
             ref_document_period_end_date   TYPE string,
             ref_preceding_document_details TYPE string,
             ref_contract_details           TYPE string,
             additional_supporting_document TYPE string,
             bill_number                    TYPE string,
             bill_date                      TYPE string,
             port_code                      TYPE string,
             document_currency_code         TYPE string,
             destination_country            TYPE string,
             export_duty                    TYPE string,
             pos                            TYPE string,
             document_value                 TYPE string,
             document_discount              TYPE string,
             document_other_charges         TYPE string,
             gross_amount                   TYPE string,
             round_off_amount               TYPE string,
             differential_percentage        TYPE string,
             reverse_charge                 TYPE string,
             claim_refund                   TYPE string,
             under_igst_act                 TYPE string,
             gst_act_or_rule_section_h      TYPE string,
             refund_eligibility             TYPE string,
             ecommerce_gstin                TYPE string,
             tds_gstin                      TYPE string,
             pnr_or_unique_number           TYPE string,
             avail_provisional_itc          TYPE string,
             original_gstin                 TYPE string,
             original_state_code            TYPE string,
             original_trade_name            TYPE string,
             original_document_type         TYPE string,
             original_document_number       TYPE string,
             original_document_date         TYPE string,
             original_return_period         TYPE string,
             original_taxable_value         TYPE string,
             original_port_code             TYPE string,
             transport_date_time            TYPE string,
             transporter_id                 TYPE string,
             transporter_name               TYPE string,
             transport_mode                 TYPE string,
             distance                       TYPE string,
             transport_document_number      TYPE string,
             transport_document_date        TYPE string,
             vehicle_number                 TYPE string,
             vehicle_type                   TYPE string,
             to_email_addresses             TYPE string,
             to_mobile_numbers              TYPE string,
             jw_original_document_number    TYPE string,
             jw_original_document_date      TYPE string,
             jw_document_number             TYPE string,
             jw_document_date               TYPE string,
             isd_distribution_gstin         TYPE string,
             custom1                        TYPE i_billingdocumentbasic-billingdocument,
             custom2                        TYPE string,
             custom3                        TYPE string,
             custom4                        TYPE string,
             custom5                        TYPE string,
             custom6                        TYPE string,
             custom7                        TYPE string,
             custom8                        TYPE string,
             custom9                        TYPE string,
             custom10                       TYPE string,
             custom11                       TYPE string,
             custom12                       TYPE string,
             custom13                       TYPE string,
             custom14                       TYPE string,
             custom15                       TYPE string,
             custom16                       TYPE string,
             custom17                       TYPE string,
             custom18                       TYPE string,
             custom19                       TYPE string,
             custom20                       TYPE string,
             items                          LIKE lt_item,
           END OF ty_data.
    DATA: lt_data TYPE STANDARD TABLE OF ty_data,
          wa_data LIKE LINE OF lt_data.
    TYPES: BEGIN OF ty_transport,
             documentnumber        TYPE c LENGTH 50,
             documentdate          TYPE c LENGTH 50,
             transportername       TYPE c LENGTH 50,
             transporterid         TYPE c LENGTH 50,
             vehicletype           TYPE c LENGTH 50,
             vehiclenumber         TYPE c LENGTH 50,
             transportmode         TYPE c LENGTH 50,
             transportdocumentdate TYPE c LENGTH 50,
             transportdocumentnum  TYPE c LENGTH 50,
           END OF ty_transport.
    DATA: lt_transport TYPE TABLE OF ty_transport.
    lt_transport = CORRESPONDING #( lt_transport_p ).
    SORT lt_eb_eway_bill_s_disp_rto BY locationgstin documentnumber hsn.
    IF lt_eb_eway_bill_s_disp_rto IS NOT INITIAL.
      DATA lv_docno TYPE c LENGTH 100.
      FIELD-SYMBOLS: <fs_src> TYPE zsd_s_global_st.
      LOOP AT lt_eb_eway_bill_s_disp_rto ASSIGNING <fs_src>.
        IF lv_docno IS INITIAL OR lv_docno <> <fs_src>-documentnumber.
          IF lv_docno IS NOT INITIAL.
            wa_data-items = lt_item.
            APPEND wa_data TO lt_data.
            CLEAR: wa_data, lt_item.
          ENDIF.
          lv_docno = <fs_src>-documentnumber.
          wa_data-document_date = <fs_src>-documentdate.
          wa_data-document_number = |{ <fs_src>-documentnumber ALPHA = OUT }|.
          wa_data-custom1 = |{ <fs_src>-custom1 ALPHA = OUT }|.
          wa_data-custom2 = <fs_src>-custom2.
          wa_data-custom3 = <fs_src>-custom3.
          wa_data-custom4 = <fs_src>-referencedocumentnumber.
          wa_data-custom5 = <fs_src>-custom5.
          wa_data-custom6 = <fs_src>-custom6.
          wa_data-custom7 = <fs_src>-custom7.
          wa_data-custom8 = <fs_src>-custom8.
          wa_data-location_name = ' '.
          wa_data-return_period = <fs_src>-returnperiod.
          wa_data-purpose = 'EWB'.
          wa_data-auto_push_or_generate = 'EWB'.
          wa_data-supply_type = <fs_src>-supplytype.
          wa_data-irn = <fs_src>-irn.
          wa_data-document_type = <fs_src>-documenttype.
          wa_data-transaction_type = <fs_src>-transactiontype.
          wa_data-bill_from_gstin = <fs_src>-billfromgstin.
          wa_data-bill_from_legal_name = <fs_src>-billfromlegalname.
          wa_data-bill_from_trade_name = <fs_src>-billfromtradename.
          wa_data-bill_from_address1 = <fs_src>-billfromaddress1.
          wa_data-bill_from_address2 = <fs_src>-billfromaddress2.
          wa_data-bill_from_city = <fs_src>-billfromcity.
          wa_data-bill_from_state_code = <fs_src>-billfromstatecode.
          wa_data-bill_from_pincode = <fs_src>-billfrompincode.
          wa_data-bill_from_phone = <fs_src>-billfromphone.
          wa_data-bill_from_email = <fs_src>-billfromemail.
          wa_data-bill_from_gstin = <fs_src>-billfromgstin.
          wa_data-location_gstin = COND #( WHEN <fs_src>-locationgstin IS INITIAL
                                           THEN <fs_src>-billfromgstin
                                           ELSE <fs_src>-locationgstin ).
          wa_data-bill_to_gstin = <fs_src>-billtogstin.
          wa_data-bill_to_state_code = <fs_src>-billtostatecode.
          wa_data-bill_to_legal_name = <fs_src>-billtolegalname.
          wa_data-bill_to_trade_name = <fs_src>-billtotradename.
          wa_data-bill_to_address1 = <fs_src>-billtoaddress1.
          wa_data-bill_to_address2 = <fs_src>-billtoaddress2.
          wa_data-bill_to_city = <fs_src>-billtocity.
          wa_data-bill_to_pincode = <fs_src>-billtopincode.
          wa_data-bill_to_phone = <fs_src>-billtophone.
          wa_data-bill_to_email = <fs_src>-billtoemail.
          wa_data-pos = <fs_src>-pos.
          IF <fs_src>-custom9 = <fs_src>-custom10.
            wa_data-ship_to_gstin = ''.
            wa_data-ship_to_legal_name = ''.
            wa_data-ship_to_trade_name = ''.
            wa_data-ship_to_vendor_code = ''.
            wa_data-ship_to_address1 = ''.
            wa_data-ship_to_address2 = ''.
            wa_data-ship_to_city = ''.
            wa_data-ship_to_state_code = ''.
            wa_data-ship_to_pincode = ''.
          ELSE.
            wa_data-ship_to_gstin = COND #( WHEN <fs_src>-shiptogstin IS INITIAL
                                            AND <fs_src>-shiptopincode IS NOT INITIAL
                                            THEN 'URP'
                                            ELSE <fs_src>-shiptogstin ).
            wa_data-ship_to_legal_name = <fs_src>-shiptolegalname.
            wa_data-ship_to_trade_name = <fs_src>-shiptotradename.
            wa_data-ship_to_address1 = <fs_src>-shiptoaddress1.
            wa_data-ship_to_address2 = <fs_src>-shiptoaddress2.
            wa_data-ship_to_city = <fs_src>-shiptocity.
          ENDIF.
          wa_data-dispatch_from_gstin = <fs_src>-dispatchfromgstin.
          wa_data-dispatch_from_trade_name = <fs_src>-dispatchfromtradename.
          wa_data-dispatch_from_address1 = <fs_src>-dispatchfromadd1.
          wa_data-dispatch_from_address2 = <fs_src>-dispatchfromadd2.
          wa_data-dispatch_from_city = <fs_src>-dispatchfromcity.
          wa_data-dispatch_from_state_code = <fs_src>-dispatchfromstatecode.
          wa_data-dispatch_from_pincode = <fs_src>-dispatchfrompincode.
          wa_data-cancelled_date = <fs_src>-cancelled_date.
          wa_data-transaction_type_description = <fs_src>-transactiontypedescription.
          wa_data-taxpayer_type = <fs_src>-taxtype.
          wa_data-ref_document_remarks = <fs_src>-refdocumentremarks.
          wa_data-ref_contract_details = <fs_src>-refcontractdetails.
          wa_data-bill_number = <fs_src>-billnumber.
          wa_data-port_code = <fs_src>-portcode.
          wa_data-destination_country = <fs_src>-destinationcountry.
          wa_data-differential_percentage = <fs_src>-differentialpercent.
          wa_data-transport_date_time = <fs_src>-transportdate.
          wa_data-transporter_id = <fs_src>-transporterid.
          wa_data-transporter_name = <fs_src>-transportername.
          wa_data-transport_mode = <fs_src>-transportmode.
          wa_data-distance = <fs_src>-distancelevel.
          wa_data-transport_document_number = <fs_src>-transportdocumentnumber.
          wa_data-transport_document_date = COND #( WHEN <fs_src>-transportdocdate IS NOT INITIAL
                                                    THEN <fs_src>-transportdocdate+6(2) && '-' && <fs_src>-transportdocdate+4(2) && '-' && <fs_src>-transportdocdate+0(4) ).
          wa_data-vehicle_number = <fs_src>-vehiclenumber.
          wa_data-vehicle_type = <fs_src>-vehicletype.
          wa_data-to_email_addresses = <fs_src>-toemailaddress.
          wa_data-to_mobile_numbers = <fs_src>-tomobilenumbers.
          IF lt_transport IS NOT INITIAL.
            wa_data-transporter_id = lt_transport[ 1 ]-transporterid.
            wa_data-transporter_name = lt_transport[ 1 ]-transportername.
            wa_data-transport_document_number = lt_transport[ 1 ]-transportdocumentnum.
            wa_data-transport_document_date = COND #( WHEN lt_transport[ 1 ]-transportdocumentdate IS NOT INITIAL
                                                      THEN lt_transport[ 1 ]-transportdocumentdate+6(2) && '-' && lt_transport[ 1 ]-transportdocumentdate+4(2) && '-' && lt_transport[ 1 ]-transportdocumentdate+0(4) ).
            wa_data-transport_mode = lt_transport[ 1 ]-transportmode.
            wa_data-vehicle_number = lt_transport[ 1 ]-vehiclenumber.
            wa_data-vehicle_type = lt_transport[ 1 ]-vehicletype.
          ENDIF.
        ENDIF.
        CLEAR wa_item.
        wa_item-serial_number = <fs_src>-serialnumber.
        wa_item-is_service = <fs_src>-isservice.
        wa_item-hsn = <fs_src>-hsn.
        wa_item-item_name = <fs_src>-itemname.
        wa_item-item_description = <fs_src>-itemdescription.
        wa_item-barcode = <fs_src>-barcode.
        wa_item-uqc = <fs_src>-uqc.
        wa_item-quantity = <fs_src>-quantity.
        wa_item-free_quantity = <fs_src>-freequantity.
        wa_item-pre_tax_value = <fs_src>-pretaxvalue.
        wa_item-cess_rate = <fs_src>-cessrate.
        wa_item-state_cess_rate = <fs_src>-statecessrate.
        wa_item-cess_non_advalorem_rate = <fs_src>-cessnonadvaloremamount.
        wa_item-discount_amount = <fs_src>-discountamount.
        wa_item-gross_amount = <fs_src>-grossamount.
        wa_item-taxable_value = <fs_src>-grossamount.
        wa_item-cgst_amount = <fs_src>-cgstamount.
        wa_item-sgst_amount = <fs_src>-sgstamount.
        wa_item-igst_amount = <fs_src>-igstamount.
        wa_item-rate = <fs_src>-rate.
        CONDENSE: wa_item-cgst_amount, wa_item-sgst_amount, wa_item-igst_amount, wa_item-rate,
                  wa_data-bill_from_state_code, wa_data-bill_to_state_code, wa_item-cess_rate,
                  wa_item-state_cess_rate, wa_item-cess_non_advalorem_rate.
        IF wa_item-cgst_amount IS INITIAL OR wa_item-cgst_amount = '0' OR wa_item-cgst_amount = '0.00'.
          CLEAR: wa_item-cgst_amount, wa_item-sgst_amount.
        ELSE.
          CLEAR wa_item-igst_amount.
        ENDIF.
        IF wa_item-rate IS INITIAL OR wa_item-rate = '0.00' OR wa_item-rate = '0'.
          wa_item-rate = 0.
          IF wa_data-bill_from_state_code EQ wa_data-bill_to_state_code.
            CLEAR wa_item-igst_amount.
            wa_item-cgst_amount = '0.00'.
            wa_item-sgst_amount = '0.00'.
          ELSEIF wa_data-bill_from_state_code NE wa_data-bill_to_state_code.
            wa_item-igst_amount = '0.00'.
            CLEAR: wa_item-cgst_amount, wa_item-sgst_amount.
          ENDIF.
        ENDIF.
        wa_item-item_total = wa_item-taxable_value + wa_item-cgst_amount + wa_item-sgst_amount + wa_item-igst_amount + wa_item-other_charges.
        wa_item-price_per_quantity = <fs_src>-priceperquantity.
        wa_item-order_line_reference = <fs_src>-orderlinereference.
        wa_item-origin_country = <fs_src>-origincountry.
        wa_item-item_serial_number = <fs_src>-itemserialnumber.
        wa_item-tax_type = <fs_src>-taxtype.
        wa_item-itc_eligibility = <fs_src>-itceligibility.
        wa_item-itc_cess_amount = <fs_src>-itccessamount.
        wa_item-custom_item1 = <fs_src>-customitem1.
        wa_item-custom_item2 = <fs_src>-customitem2.
        wa_item-custom_item3 = <fs_src>-customitem3.
        wa_item-custom_item4 = <fs_src>-customitem4.
        wa_item-custom_item5 = <fs_src>-customitem5.
        wa_item-custom_item6 = <fs_src>-customitem6.
        wa_item-custom_item7 = <fs_src>-customitem7.
        wa_item-custom_item8 = <fs_src>-customitem8.
        wa_item-custom_item9 = <fs_src>-customitem9.
        wa_item-custom_item10 = <fs_src>-customitem10.
        CONDENSE: wa_item-serial_number, wa_item-quantity, wa_item-price_per_quantity,
                  wa_item-gross_amount, wa_item-taxable_value, wa_item-rate.
        CLEAR wa_gs_doc_val.
        wa_gs_doc_val-document_no = <fs_src>-documentnumber.
        wa_gs_doc_val-taxable_value = <fs_src>-grossamount.
        wa_gs_doc_val-cgst = <fs_src>-cgstamount.
        wa_gs_doc_val-sgst = <fs_src>-sgstamount.
        wa_gs_doc_val-igst = <fs_src>-igstamount.
        wa_gs_doc_val-othercharges = <fs_src>-othercharges.
        wa_gs_doc_val-qty = <fs_src>-quantity.
        COLLECT wa_gs_doc_val INTO lt_gt_doc_val.
        CLEAR wa_gs_doc_val.
        APPEND wa_item TO lt_item.
        CONDENSE: wa_data-document_value, wa_data-gross_amount, wa_data-distance.
      ENDLOOP.
      IF lv_docno IS NOT INITIAL.
        wa_data-items = lt_item.
        APPEND wa_data TO lt_data.
      ENDIF.
      CLEAR wa_data.
      LOOP AT lt_data INTO wa_data.
        READ TABLE lt_einv_log INTO DATA(wa_chl)
          WITH KEY documentnumber = wa_data-document_number.
        IF sy-subrc = 0.
          wa_display_table-ewb_status = COND string(
            WHEN wa_chl-ewaycancel = 'X' THEN 'E-Way Bill Cancelled'
            WHEN wa_chl-ewayfail = 'X' THEN 'ERROR !'
            WHEN wa_chl-ewaybillnumber IS INITIAL THEN 'E-Way Bill Pending'
            ELSE 'E-Way Bill Generated' ).
          wa_display_table-ewb_icon = COND i(
            WHEN wa_chl-ewaycancel = 'X' THEN 1
            WHEN wa_chl-ewayfail = 'X' THEN 1
            WHEN wa_chl-ewaybillnumber IS INITIAL THEN 2
            ELSE 3 ).
          wa_display_table-ewaybillnumber = wa_chl-ewaybillnumber.
          wa_display_table-ewaybillvalidupto = wa_chl-ewaybillvalidupto.
          wa_display_table-transporterid = wa_chl-transporterid.
          wa_display_table-transportername = wa_chl-transportername.
          wa_display_table-transporterdocnum = wa_chl-transporterdocnum.
          wa_display_table-transportmode = wa_chl-transportmode.
          wa_display_table-transporterdocdate = wa_chl-transporterdocdate.
          wa_display_table-vehiclenumber = wa_chl-vehiclenumber.
          wa_display_table-vehicletype = wa_chl-vehicletype.
        ELSE.
          wa_display_table-ewb_status = 'E-Way Bill Pending'.
          wa_display_table-ewb_icon = 2.
          IF lt_transport IS NOT INITIAL.
            wa_display_table-transporterid = lt_transport[ 1 ]-transporterid.
            wa_display_table-transportername = lt_transport[ 1 ]-transportername.
            wa_display_table-transporterdocnum = lt_transport[ 1 ]-transportdocumentnum.
            wa_display_table-transportmode = lt_transport[ 1 ]-transportmode.
            wa_display_table-transporterdocdate = lt_transport[ 1 ]-transportdocumentdate.
            wa_display_table-vehiclenumber = lt_transport[ 1 ]-vehiclenumber.
            wa_display_table-vehicletype = lt_transport[ 1 ]-vehicletype.
          ENDIF.
        ENDIF.
        READ TABLE lt_gt_doc_val INTO wa_gs_doc_val
          WITH KEY document_no = wa_data-document_number.
        IF sy-subrc = 0.
          wa_display_table-taxablevalue = wa_gs_doc_val-taxable_value.
          wa_display_table-cgstamount = wa_gs_doc_val-cgst.
          wa_display_table-sgstamount = wa_gs_doc_val-sgst.
          wa_display_table-quantity = wa_gs_doc_val-qty.
          wa_display_table-igstamount = COND #( WHEN wa_gs_doc_val-igst IS NOT INITIAL
                                                THEN wa_gs_doc_val-igst
                                                ELSE ' ' ).
        ENDIF.
        wa_display_table-documentnumber = wa_data-document_number.
        wa_display_table-documentdate = COND #( WHEN wa_data-document_date IS NOT INITIAL
                                                THEN |{ wa_data-document_date+6(4) }{ wa_data-document_date+3(2) }{ wa_data-document_date+0(2) }| ).
        wa_display_table-documentvalue = wa_data-items[ 1 ]-item_total.
        wa_display_table-referencedocumentnumber = wa_data-custom4.
        wa_display_table-locationgstin = wa_data-location_gstin.
        wa_display_table-documenttype = wa_data-document_type.
        wa_display_table-billfromgstin = wa_data-bill_from_gstin.
        wa_display_table-billfromlegalname = wa_data-bill_from_legal_name.
        wa_display_table-billfromaddress1 = wa_data-bill_from_address1.
        wa_display_table-billfromaddress2 = wa_data-bill_from_address2.
        wa_display_table-billfromcity = wa_data-bill_from_city.
        wa_display_table-billfrompincode = wa_data-bill_from_pincode.
        wa_display_table-billtogstin = wa_data-bill_to_gstin.
        wa_display_table-billtolegalname = wa_data-bill_to_legal_name.
        wa_display_table-billtoaddress1 = wa_data-bill_to_address1.
        wa_display_table-billtoaddress2 = wa_data-bill_to_address2.
        wa_display_table-billtocity = wa_data-bill_to_city.
        wa_display_table-billtopincode = wa_data-bill_to_pincode.
        wa_display_table-pos = wa_data-pos.
        wa_display_table-dispatchfromgstin = wa_data-dispatch_from_gstin.
        wa_display_table-dispatchfromname = wa_data-dispatch_from_trade_name.
        wa_display_table-dispatchfromadd1 = wa_data-dispatch_from_address1.
        wa_display_table-dispatchfromadd2 = wa_data-dispatch_from_address2.
        wa_display_table-dispatchfromcity = wa_data-dispatch_from_city.
        wa_display_table-shiptoadd1 = wa_data-ship_to_address1.
        wa_display_table-shiptoadd2 = wa_data-ship_to_address2.
        wa_display_table-shiptocity = wa_data-ship_to_city.
        wa_display_table-shiptogstin = wa_data-ship_to_gstin.
        wa_display_table-shiptoname = wa_data-ship_to_legal_name.
        wa_display_table-shiptopincode = wa_data-ship_to_pincode.
        APPEND wa_display_table TO et_display_table.
      ENDLOOP.
      CLEAR: wa_final, wa_final_temp.
      LOOP AT lt_eb_eway_bill_s_disp_rto INTO wa_final.
        lv_item = lv_item + 1.
        AT NEW documentnumber.
          CLEAR lv_item.
          lv_item = '1'.
        ENDAT.
        wa_final_temp-branch = wa_final-branch.
        wa_final_temp-return_period = wa_final-returnperiod.
        wa_final_temp-fiscal_year = wa_final-fiscalyear.
        wa_final_temp-from_gstin = wa_final-billfromgstin.
        wa_final_temp-from_name = wa_final-billfromlegalname.
        wa_final_temp-from_address1 = wa_final-billfromaddress1.
        wa_final_temp-from_address2 = wa_final-billfromaddress2.
        wa_final_temp-from_place = wa_final-billfromcity.
        wa_final_temp-from_state = wa_final-billfromstatecode.
        wa_final_temp-from_postal_code = wa_final-billfrompincode.
        wa_final_temp-supply_type = wa_final-supplytype.
        wa_final_temp-document_type = wa_final-documenttype.
        wa_final_temp-transction_type = wa_final-transactiontype.
        wa_final_temp-document_no = wa_final-documentnumber.
        wa_final_temp-document_date = wa_final-documentdate.
        wa_final_temp-to_gstin = wa_final-billtogstin.
        wa_final_temp-to_other_party_name = wa_final-billtolegalname.
        wa_final_temp-to_address_line1 = wa_final-billtoaddress1.
        wa_final_temp-to_address_line2 = wa_final-billtoaddress2.
        wa_final_temp-to_place = wa_final-billtocity.
        wa_final_temp-to_state = wa_final-billtostatecode.
        wa_final_temp-to_postal_code = wa_final-billtopincode.
        wa_final_temp-value = ' '.
        wa_final_temp-product_name = wa_final-itemname.
        wa_final_temp-product_description = wa_final-itemdescription.
        REPLACE ALL OCCURRENCES OF '.' IN wa_final-hsn WITH ' '.
        CONDENSE wa_final-hsn.
        wa_final_temp-hsn = wa_final-hsn.
        wa_final_temp-unit_measure = wa_final-uqc.
        wa_final_temp-rate = wa_final-rate.
        wa_final_temp-cess_rate = wa_final-cessrate.
        wa_final_temp-custom1 = wa_final-custom1.
        wa_final_temp-custom2 = wa_final-custom2.
        wa_final_temp-custom3 = wa_final-custom3.
        wa_final_temp-custom10 = wa_final-custom10.
        wa_final_temp-total_quantity = wa_final-quantity + wa_final_temp-total_quantity.
        wa_final_temp-tax_value = wa_final-taxablevalue + wa_final_temp-tax_value.
        wa_final_temp-intr_tax_amt = wa_final-igstamount + wa_final_temp-intr_tax_amt.
        wa_final_temp-cent_tax_amt = wa_final-cgstamount + wa_final_temp-cent_tax_amt.
        wa_final_temp-ut_tax_amt = wa_final-sgstamount + wa_final_temp-ut_tax_amt.
        wa_final_temp-cess_amt = wa_final-cessamount + wa_final_temp-cess_amt.
        wa_final_temp-cess_non_advalorem_rate = wa_final-cessnonadvaloremrate + wa_final_temp-cess_non_advalorem_rate.
        wa_final_temp-price_per_quantity = wa_final-priceperquantity.
        wa_final_temp-display_error = wa_final-customitem10.
        wa_final_temp-bukrs = wa_final-company_code.
        wa_final_temp-to_email_address = wa_final-toemailaddress.
        wa_final_temp-document_item = lv_item.
        IF lt_transport IS NOT INITIAL.
          wa_final_temp-transporter_id = lt_transport[ 1 ]-transporterid.
        ELSE.
          wa_final_temp-transporter_id = wa_final-transporterid.
        ENDIF.
        IF lt_transport IS NOT INITIAL.
          wa_final_temp-transporter_name = lt_transport[ 1 ]-transportername.
        ELSE.
          wa_final_temp-transporter_name = wa_final-transportername.
        ENDIF.
        IF lt_transport IS NOT INITIAL.
          wa_final_temp-transport_mode = lt_transport[ 1 ]-transportmode.
        ELSE.
          wa_final_temp-transport_mode = wa_final-transportmode.
        ENDIF.
        IF lt_transport IS NOT INITIAL.
          wa_final_temp-vehicle_number = lt_transport[ 1 ]-vehiclenumber.
        ELSE.
          wa_final_temp-vehicle_number = wa_final-vehiclenumber.
        ENDIF.
        IF lt_transport IS NOT INITIAL.
          wa_final_temp-vehicle_type = lt_transport[ 1 ]-vehicletype.
        ELSE.
          wa_final_temp-vehicle_type = wa_final-vehicletype.
        ENDIF.
        CONCATENATE sy-datum+6(2) '-' sy-datum+4(2) '-' sy-datum(4) INTO wa_final_temp-transport_doc_date.
        APPEND wa_final_temp TO lt_e_tab_eway_bill_s.
        CLEAR: wa_final_temp, wa_final.
      ENDLOOP.
      CALL METHOD /ui2/cl_json=>serialize
        EXPORTING
          data        = lt_data
          pretty_name = /ui2/cl_json=>pretty_mode-camel_case
        RECEIVING
          r_json      = DATA(lv_json).
      IF lv_json IS NOT INITIAL.
        REPLACE ALL OCCURRENCES OF 'stateCessNonAdvaloAmount' IN lv_json WITH 'stateCessNonAdvaloremAmount'.
        REPLACE ALL OCCURRENCES OF 'liabilityDischargeReturnP' IN lv_json WITH 'liabilityDischargeReturnPeriod'.
        REPLACE ALL OCCURRENCES OF 'additionalSupportingDocument' IN lv_json WITH 'additionalSupportingDocumentDetails'.
        REPLACE ALL OCCURRENCES OF 'documentValueInForeignCur' IN lv_json WITH 'documentValueInForeignCurrency'.
        REPLACE ALL OCCURRENCES OF 'paymentDueDate' IN lv_json WITH 'PaymentDueDate'.
        REPLACE ALL OCCURRENCES OF 'gstActOrRuleSectionH' IN lv_json WITH 'GSTActOrRuleSection'.
        REPLACE ALL OCCURRENCES OF 'natureOfJwDone' IN lv_json WITH 'natureOfJWDone'.
        REPLACE ALL OCCURRENCES OF 'stateCessNonAdvaLoAmount' IN lv_json WITH 'stateCessNonAdvaloremAmount'.
        lv_json = |{ '{"data": ' }{ lv_json }{ '}' }|.
        lv_request = lv_json.
      ENDIF.
      CLEAR wa_final.
    ENDIF.

  ENDMETHOD.



  METHOD read_text.

    CALL FUNCTION 'READ_TEXT'
      EXPORTING
        client                  = sy-mandt
        id                      = iv_id
        language                = iv_language
        name                    = iv_name
        object                  = 'KNA1'
      TABLES
        lines                   = rt_lines
      EXCEPTIONS
        id                      = 1
        language                = 2
        name                    = 3
        not_found               = 4
        object                  = 5
        reference_check         = 6
        wrong_access_to_archive = 7
        OTHERS                  = 8.

  ENDMETHOD.


  METHOD get_data_eway_generate_json.

    TYPES : BEGIN OF ty_criterias,
              locationgstin  TYPE string,
              locationname   TYPE string,
              documentnumber TYPE string,
              documentdate   TYPE string,
              supplytype     TYPE string,
              documenttype   TYPE string,
              billfromgstin  TYPE string,
              portcode       TYPE string,
            END OF ty_criterias.

    TYPES : tt_criterias TYPE TABLE OF ty_criterias WITH DEFAULT KEY.

    TYPES : BEGIN OF ty_genewb1,
              overwritetransportdetails      TYPE string,
              transporterid                  TYPE string,
              transportername                TYPE string,
              transportmode                  TYPE string,
              transportdocumentnumber        TYPE string,
              transportdocumentdate          TYPE string,
              distance                       TYPE string,
              vehiclenumber                  TYPE string,
              vehicletype                    TYPE string,
              overwriteexportshippingdetails TYPE string,
              shiptoaddress1                 TYPE string,
              shiptoaddress2                 TYPE string,
              shiptocity                     TYPE string,
              shiptostatecode                TYPE string,
              shiptopincode                  TYPE string,
              overwritedispatchdetails       TYPE string,
              dispatchfromtradename          TYPE string,
              dispatchfromaddress1           TYPE string,
              dispatchfromaddress2           TYPE string,
              dispatchfromcity               TYPE string,
              dispatchfromstatecode          TYPE string,
              dispatchfrompincode            TYPE string,
              overwritenotificationdetails   TYPE string,
              toemailaddresses               TYPE string,
              tomobilenumbers                TYPE string,
              criterias                      TYPE tt_criterias,
            END OF ty_genewb1.

    DATA : ls_request TYPE ty_genewb1.


    SELECT document_number,
    custom1,
    document_date,
    billtopincode,
    billfromgstin,
    dispatchfromgstin,
    dispatchfromname,
    dispatchfromadd1,
    dispatchfromadd2,
    dispatchfrompincode,
    dispatchfromcity,
    shiptogstin,
    shiptoadd1,
    shiptoadd2,
    shiptopincode,
    shiptocity,
    shiptostatecode,
    transactiontype,
    referencedocumentnumber,
    documenttype,
    einv_module
    FROM zsd_t_einv_log INTO TABLE @DATA(lt_einv_log) WHERE ewaybill_number EQ ' '
    AND document_number EQ @iv_document_number
*    AND einv_module EQ 'SD'
    AND eway_cancel NE 'X'.

    SELECT  vbeln,
      posnr,
      parvw,
      kunnr,
      lifnr,
      adrnr,
      land1  FROM vbpa INTO TABLE @DATA(lt_vbpa)
      FOR ALL ENTRIES IN @lt_einv_log
      WHERE vbeln = @lt_einv_log-custom1
      AND parvw = 'SP'.

    SELECT lifnr,
           name1,
           name2,
           stcd3,
           stcd5,
           stenr FROM lfa1 INTO TABLE @DATA(lt_lfa1)
           FOR ALL ENTRIES IN @lt_vbpa
           WHERE lifnr =  @lt_vbpa-lifnr.

    SORT lt_vbpa BY vbeln.
    SORT lt_lfa1 BY lifnr.

    LOOP AT lt_einv_log INTO DATA(ls_ewb_stat).

***** TO BE FETCH FROM EINVOICE TABLE *****
      ls_request-overwritetransportdetails = 'Y'.
      ls_request-transportmode = is_vehicle_details-transport_mode..
      IF NOT ls_request-transportmode EQ 'ROAD'.
        ls_request-transportdocumentnumber = is_vehicle_details-transporter_doc_num.
        ls_request-transportdocumentdate = is_vehicle_details-transportdocdate.
      ELSE.
        ls_request-vehiclenumber = is_vehicle_details-vehicle_number.
        ls_request-vehicletype = is_vehicle_details-vehicle_type.
      ENDIF.
      TRY.

          DATA(lv_customer) = lt_vbpa[ vbeln = ls_ewb_stat-document_number ]-lifnr.
          DATA(ls_lfa1) = lt_lfa1[ lifnr = lv_customer ].

          ls_request-transportername = ls_lfa1-name1.
          ls_request-transporterid = ls_lfa1-stenr.
        CATCH cx_sy_itab_line_not_found.

      ENDTRY.
      IF is_vehicle_details-transporter_id IS NOT INITIAL AND is_vehicle_details-transporter_name IS NOT INITIAL.
        ls_request-transporterid = is_vehicle_details-transporter_id.
        ls_request-transportername = is_vehicle_details-transporter_name.
      ENDIF.
      ls_request-distance = '0'.
      ls_request-overwriteexportshippingdetails = 'N'.
*      ls_request-shiptoaddress1 = ls_ewb_stat-shiptoadd1.
*      ls_request-shiptoaddress2 = ls_ewb_stat-shiptoadd2.
*      ls_request-shiptocity = ls_ewb_stat-shiptocity.
*      ls_request-shiptostatecode = ls_ewb_stat-shiptostatecode.
*      IF strlen( ls_ewb_stat-shiptogstin ) > 2 AND ls_request-shiptostatecode IS INITIAL .
*        ls_request-shiptostatecode = ls_ewb_stat-shiptogstin+0(2).
*      ENDIF.
      IF ls_ewb_stat-transactiontype = 'EXPWOP'.
*        ls_request-shiptostatecode = '97'.
        ls_request-distance = '3500'.
      ENDIF.
      IF ls_ewb_stat-billtopincode = ls_ewb_stat-shiptopincode.
        ls_request-distance = '10'.
      ENDIF.
*      ls_request-shiptopincode = ls_ewb_stat-shiptopincode.
      ls_request-overwritedispatchdetails = 'Y'.
      ls_request-dispatchfromtradename = ls_ewb_stat-dispatchfromname.
      ls_request-dispatchfromaddress1 = ls_ewb_stat-dispatchfromadd1.
      ls_request-dispatchfromaddress2 = ls_ewb_stat-dispatchfromadd2.
      ls_request-dispatchfromcity = ls_ewb_stat-dispatchfromcity.
      IF strlen( ls_ewb_stat-dispatchfromgstin ) > 2.
        ls_request-dispatchfromstatecode = ls_ewb_stat-dispatchfromgstin+0(2).
      ENDIF.
      ls_request-dispatchfrompincode = ls_ewb_stat-dispatchfrompincode.

      IF ls_ewb_stat-document_date IS NOT INITIAL.
        ls_request-criterias = VALUE #( ( locationGstin = ls_ewb_stat-billfromgstin
                                        documentNumber = COND #( WHEN ls_ewb_stat-einv_module EQ 'SD'
                                                                 THEN |{ ls_ewb_stat-document_number ALPHA = OUT }|
                                                                 WHEN ls_ewb_stat-einv_module EQ 'FI'
                                                                 THEN |{ ls_ewb_stat-referencedocumentnumber ALPHA = OUT }| )
                                        documentDate =  |{ ls_ewb_stat-document_date+6(2) }-{ ls_ewb_stat-document_date+4(2) }-{ ls_ewb_stat-document_date+0(4) }|
                                        supplyType = 'S'
                                        documentType = ls_ewb_stat-documenttype
                                        billFromGstin = ls_ewb_stat-billfromgstin ) ).
      ELSE.
        ls_request-criterias = VALUE #( ( locationGstin = ls_ewb_stat-billfromgstin
                                          documentNumber = COND #( WHEN ls_ewb_stat-einv_module EQ 'SD'
                                                                   THEN |{ ls_ewb_stat-document_number ALPHA = OUT }|
                                                                   WHEN ls_ewb_stat-einv_module EQ 'FI'
                                                                   THEN |{ ls_ewb_stat-referencedocumentnumber ALPHA = OUT }| )
                                          supplyType = 'S'
                                          documentType = ls_ewb_stat-documenttype
                                          billFromGstin = ls_ewb_stat-billfromgstin ) ).
      ENDIF.


      rv_request_json = /ui2/cl_json=>serialize( data = ls_request ).

    ENDLOOP.

  ENDMETHOD.


  METHOD fill_eml_structure.
    DATA : lt_create  TYPE TABLE FOR CREATE zsd_c_einv_main,
           lv_counter TYPE char10 VALUE '1',
           w_lts      TYPE abp_creation_tstmpl.

    CLEAR:w_lts.
    GET TIME STAMP FIELD w_lts.

    et_create = VALUE #( ( documentnumber                   = is_vbrk_create-BillingDocument
                           documentdate                     = is_vbrk_create-BillingDocumentDate
                           companycode                      = is_vbrk_create-CompanyCode
                           FiscalYear                       = is_vbrk_create-FiscalYear
                           LocationGstin                    = is_final-locationgstin
                           Billfromgstin                    = is_final-billfromgstin
                           Documenttype                     = is_final-documenttype
                           Billfromname                     = is_final-billfromlegalname
                           Billfromadd1                     = is_final-billfromaddress1
                           Billfromadd2                     = is_final-billfromaddress2
                           Billfromcity                     = is_final-billfromcity
                           Billfrompincode                  = is_final-billfrompincode
                           Billtoadd1                       = is_final-billtoaddress1
                           Billtoadd2                       = is_final-billtoaddress2
                           Billtocity                       = is_final-billtocity
                           Billtogstin                      = is_final-billtogstin
                           Billtoname                       = is_final-billtolegalname
                           Billtopincode                    = is_final-billtopincode
                           Dispatchfromgstin                = is_final-dispatchfromgstin
                           Dispatchfromname                 = is_final-billfromlegalname
                           Dispatchfromadd1                 = is_final-dispatchfromadd1
                           Dispatchfromadd2                 = is_final-dispatchfromadd2
                           Dispatchfromcity                 = is_final-dispatchfromcity
                           Dispatchfrompincode              = is_final-dispatchfrompincode
                           Shiptoadd1                       = is_final-shiptoaddress1
                           Shiptoadd2                       = is_final-shiptoaddress2
                           Shiptocity                       = is_final-shiptocity
                           Shiptopincode                    = is_final-shiptopincode
                           Shiptoname                       = is_final-shiptotradename
                           Shiptogstin                      = is_final-shiptogstin
                           Irn                              = is_irn_det-irn
                           QrCode                           = is_irn_det-qrcode
                           AckowlegementNumber              = is_irn_det-acknumber
                           AckDate                          = is_irn_det-ackdate
                           SignedQrcode                     = is_irn_det-signedqrcode
                           EinvModule                       = iv_module
                           Success                          = 'X'
                           fail                             = ''
                           createdby                        = sy-uname
                           createdat                        = w_lts
                           %cid                             = |CID_{ lv_counter }_{ is_vbrk_create-BillingDocument }|
                           %control-DocumentNumber          = if_abap_behv=>mk-on
                           %control-DocumentDate            = if_abap_behv=>mk-on
                           %control-Documenttype            = if_abap_behv=>mk-on
                           %control-CompanyCode             = if_abap_behv=>mk-on
                           %control-fiscalyear              = if_abap_behv=>mk-on
                           %control-EinvError               = if_abap_behv=>mk-on
                           %control-irn                     = if_abap_behv=>mk-on
                           %control-QrCode                  = if_abap_behv=>mk-on
                           %control-AckowlegementNumber     = if_abap_behv=>mk-on
                           %control-AckDate                 = if_abap_behv=>mk-on
                           %control-SignedQrcode            = if_abap_behv=>mk-on
                           %control-Billfromgstin           = if_abap_behv=>mk-on
                           %control-LocationGstin           = if_abap_behv=>mk-on
                           %control-Billfromname            = if_abap_behv=>mk-on
                           %control-Billfromadd1            = if_abap_behv=>mk-on
                           %control-Billfromadd2            = if_abap_behv=>mk-on
                           %control-Billfromcity            = if_abap_behv=>mk-on
                           %control-Billfrompincode         = if_abap_behv=>mk-on
                           %control-Billtoadd1              = if_abap_behv=>mk-on
                           %control-Billtoadd2              = if_abap_behv=>mk-on
                           %control-Billtocity              = if_abap_behv=>mk-on
                           %control-Billtogstin             = if_abap_behv=>mk-on
                           %control-Billtoname              = if_abap_behv=>mk-on
                           %control-Billtopincode           = if_abap_behv=>mk-on
                           %control-Dispatchfromname        = if_abap_behv=>mk-on
                           %control-Dispatchfromadd1        = if_abap_behv=>mk-on
                           %control-Dispatchfromadd2        = if_abap_behv=>mk-on
                           %control-Dispatchfromcity        = if_abap_behv=>mk-on
                           %control-Dispatchfrompincode     = if_abap_behv=>mk-on
                           %control-Shiptoadd1              = if_abap_behv=>mk-on
                           %control-Shiptoadd2              = if_abap_behv=>mk-on
                           %control-Shiptocity              = if_abap_behv=>mk-on
                           %control-Shiptopincode           = if_abap_behv=>mk-on
                           %control-Success                 = if_abap_behv=>mk-on
                           %control-fail                    = if_abap_behv=>mk-on
                           %control-Dispatchfromgstin       = if_abap_behv=>mk-on
                           %control-Shiptogstin             = if_abap_behv=>mk-on
                           %control-EinvModule              = if_abap_behv=>mk-on
                           %control-createdby               = if_abap_behv=>mk-on
                           %control-createdat               = if_abap_behv=>mk-on
                           %control-Shiptoname              = if_abap_behv=>mk-on    ) ).
  ENDMETHOD.


  METHOD generate_ewaybill.
  ENDMETHOD.


  METHOD get_data_for_gstr2.
    TYPES: BEGIN OF ty_bset,
             bukrs   TYPE bukrs,
             belnr   TYPE belnr_d,
             gjahr   TYPE gjahr,
             taxps   TYPE bseg-taxps,
             ktosl   TYPE bset-ktosl,
             kschl   TYPE kschl,
             txgrp   TYPE txgrp,
             buzei   TYPE buzei,
             shkzg   TYPE shkzg,
             fwbas   TYPE fwbas_bses,
             fwste   TYPE fwste,
             kbetr   TYPE kbetr_tax,
             mwskz   TYPE bset-mwskz,
             hkont   TYPE hkont,
             hwbas   TYPE hwbas,
             hwste   TYPE hwste,
             gsp_tax TYPE zsd_t_tax_cond-gsp_tax,
           END OF ty_bset.
    TYPES: BEGIN OF ty_awkey,

             awkey TYPE bkpf-awkey,
             gjahr TYPE  bkpf-gjahr,
             bukrs TYPE  bkpf-bukrs,
           END OF ty_awkey.
    TYPES: BEGIN OF ty_bkpf_bseg_del,
             bukrs    TYPE bkpf-bukrs,
             belnr    TYPE bkpf-belnr,
             gjahr    TYPE bkpf-gjahr,
             bldat    TYPE bkpf-bldat,
             budat    TYPE bkpf-budat,
             blart    TYPE bkpf-blart,
             usnam    TYPE bkpf-usnam,
             xblnr    TYPE bkpf-xblnr,
             stblg    TYPE bkpf-stblg,
             xmwst    TYPE bkpf-xmwst,
             awkey    TYPE bkpf-awkey,
             bktxt    TYPE bkpf-bktxt,
             werks    TYPE bseg-werks,
             buzid    TYPE bseg-buzid,
             lifnr    TYPE bseg-lifnr,
             koart    TYPE bseg-koart,
             ktosl    TYPE bseg-ktosl,
             gsber    TYPE bseg-gsber,
             bupla    TYPE bseg-bupla,
             mwskz    TYPE bseg-mwskz,
             gst_part TYPE bseg-gst_part,
             plc_sup  TYPE bseg-plc_sup,
             hsn_sac  TYPE bseg-hsn_sac,
             zlspr    TYPE bseg-zlspr,
             buzei    TYPE bseg-buzei,
             txbhw    TYPE bseg-txbhw,
*            H_BLART TYPE BSEG-H_BLART,
             shkzg    TYPE bseg-shkzg,
             ebeln    TYPE bseg-ebeln,
             hkont    TYPE bseg-hkont,
           END OF ty_bkpf_bseg_del.
    TYPES: BEGIN OF ty_vbrk,
             vbeln     TYPE vbrk-vbeln,     "Billing Document ,vbrk
             knumv     TYPE vbrk-knumv,     "Number of the document condition vbrp
             fkart     TYPE vbrk-fkart,     "Billing Type ,vbrk
             erzet     TYPE vbrk-erzet,     "Entry time,vbrp
             fktyp     TYPE vbrk-fktyp,     "Billing category VBRK
             vbtyp     TYPE vbrk-vbtyp,     "SD document category  vbrk
             waerk     TYPE vbrk-waerk,
             vkorg     TYPE vbrk-vkorg,     "Sales Organization vbrk
             fkdat     TYPE vbrk-fkdat,     "Billing date for billing index and printout  vbrk
             kurrf     TYPE vbrk-kurrf,     "Exchange rate for FI postings  VBRK
             bukrs     TYPE vbrk-bukrs,     "Company Code   vbrk
             gjahr     TYPE vbrk-gjahr,     "Fiscal Year
             regio     TYPE vbrk-regio,     "Region (State, Province, County) vbrk
             netwr     TYPE vbrk-netwr,     "Net Value in Document Currency  vbrk
             kunrg     TYPE vbrk-kunrg,     "Payer  vbrk
             kunag     TYPE vbrk-kunag,     "Sold-to party  vbrk
             sfakn     TYPE vbrk-sfakn,     "Cancelled billing document number    vbrk
             xblnr     TYPE vbrk-xblnr,     "Reference Document Number  vbrk
             zuonr     TYPE vbrk-zuonr,     "Assignment number  vbrk
             mwsbk     TYPE vbrk-mwsbk,     "Tax amount in document currency vbrk
             fksto     TYPE vbrk-fksto,     "Billing document is cancelled vbrk
             bupla     TYPE vbrk-bupla,     "Business Place vbrk
             rfbsk     TYPE vbrk-rfbsk,
             ernam     TYPE vbrk-ernam,
             posnr     TYPE vbrp-posnr,
             werks     TYPE vbrp-werks,
             aubel     TYPE vbrp-aubel,
             matnr     TYPE vbrp-matnr,
             fkimg     TYPE vbrp-fkimg,
             erdat     TYPE vbrk-erdat,
             vrkme     TYPE vbrp-vrkme,
             arktx     TYPE vbrp-arktx,
             netwrline TYPE vbrp-netwr,     "Net Value in Document Currency  vbrk
             vgbel     TYPE vgbel,
             vgpos     TYPE vgpos,
             kzwi2     TYPE kzwi2,
           END OF ty_vbrk.
    DATA: it_awkey TYPE STANDARD TABLE OF ty_awkey.
    DATA: is_awkey    TYPE  ty_awkey,
          l_var_awkey TYPE char10.
    DATA: lr_koart        TYPE RANGE OF koart,
          lr_gl           TYPE RANGE OF saknr,
          lt_bset         TYPE STANDARD TABLE OF ty_bset,
          ls_final        TYPE zsd_s_global_st,
          gt_final_inward TYPE STANDARD TABLE OF zsd_s_global_st.
    DATA: lv_frmregion TYPE char3.
    DATA: lv_steur TYPE char16.
    DATA: lv_recv_regio TYPE t001w-regio.
    DATA: lv_kbetr TYPE kbetr_kond.

    TYPES: BEGIN OF ty_bseg_excs,
             bukrs TYPE bseg-bukrs,
             belnr TYPE bseg-belnr,
             gjahr TYPE bseg-gjahr,
           END OF ty_bseg_excs.

    DATA: lt_excs         TYPE STANDARD TABLE OF ty_bseg_excs,
          lt_bsec         TYPE STANDARD TABLE OF bsec,
          t_bkpf_bseg_del TYPE STANDARD TABLE OF ty_bkpf_bseg_del.

    CONSTANTS :lc_re    TYPE char2 VALUE 'RE',
               lc_inv   TYPE char3 VALUE 'INV',
               lc_crn   TYPE char3 VALUE 'CRN',
               lc_dbn   TYPE char3 VALUE 'DBN',
               lc_boe   TYPE char3 VALUE 'BOE',
               lc_impg  TYPE char4 VALUE 'IMPG',
               lc_imps  TYPE char4 VALUE 'IMPS',
               lc_b2b   TYPE char3 VALUE 'B2B',
               lc_igst1 TYPE char10 VALUE '0000171760',
               lc_igst2 TYPE char10 VALUE '0000171860',
               lc_igst3 TYPE char10 VALUE '0000171790',
               lc_b2c   TYPE char3 VALUE 'B2C',
               lc_cgst1 TYPE char10 VALUE '0000171840',
               lc_cgst2 TYPE char10 VALUE '0000171740',
               lc_cgst3 TYPE char10 VALUE '0000171770',
               lc_sgst1 TYPE char10 VALUE '0000171850',
               lc_sgst2 TYPE char10 VALUE '0000171750',
               lc_sgst3 TYPE char10 VALUE '0000171780'.



    SELECT * FROM zfi_t_doctype INTO  TABLE @DATA(it_blart) WHERE module_type = 'PURCHASE'.

    "ITC GL Mappin
    SELECT * FROM zsd_c_tvarvc INTO TABLE @DATA(lt_gl) WHERE name = 'ZGSP_GST2_GL'.

    lr_gl = VALUE #( FOR ls_gl IN lt_gl ( sign = 'I' option = 'EQ' low = ls_gl-low ) ).

    LOOP AT lr_gl ASSIGNING FIELD-SYMBOL(<fs_gl>).
      <fs_gl>-low = |{ <fs_gl>-low ALPHA = IN }|.
    ENDLOOP.

    IF iv_billing_document IS NOT INITIAL AND lr_gl IS NOT INITIAL.

      SELECT bukrs,
             belnr,
             gjahr
        FROM bsis_view
        WHERE belnr = @iv_billing_document
        AND hkont IN @lr_gl
         INTO TABLE @DATA(lt_doc).

    ELSEIF lr_gl IS NOT INITIAL AND it_billing_document_date IS NOT INITIAL.
      SELECT bukrs,
           belnr,
           gjahr
      FROM bsis_view
      FOR ALL ENTRIES IN @lr_gl
      WHERE bldat IN @it_billing_document_date
      AND hkont = @lr_gl-low(10)
       INTO TABLE @lt_doc.

      IF lt_doc IS NOT INITIAL.
        SELECT bukrs,
         belnr,
         gjahr APPENDING TABLE @lt_doc
         FROM bsas
         FOR ALL ENTRIES IN @lr_gl
         WHERE hkont = @lr_gl-low(10).
      ENDIF.
    ENDIF.


    IF lt_doc IS NOT INITIAL .
      IF iv_billing_document IS NOT INITIAL.

        SELECT bukrs,
              belnr,
              gjahr,
              monat,
              waers, awkey, xblnr, budat, bldat, blart, kursf, awtyp, xreversal, tcode, stblg, bktxt, usnam
             FROM zsd_c_bkpf
             INTO TABLE @DATA(lt_bkpf)
               FOR ALL ENTRIES IN @lt_doc
             WHERE belnr = @lt_doc-belnr
             AND gjahr = @lt_doc-gjahr
             AND bukrs = @lt_doc-bukrs
             AND belnr = @iv_billing_document.

*      ELSE.
      ELSEIF it_blart IS NOT INITIAL.
*        SELECT bukrs,
*                    belnr,
*                    gjahr,
*                    monat,
*                    waers, awkey, xblnr, budat, bldat, blart, kursf, awtyp, xreversal, tcode, stblg, bktxt, usnam
*                   FROM zsd_c_bkpf
*                   INTO TABLE @lt_bkpf
*                     FOR ALL ENTRIES IN @lt_doc
*                   WHERE belnr = @lt_doc-belnr
*                   AND gjahr = @lt_doc-gjahr
*                   AND bukrs = @lt_doc-bukrs
*                   AND bldat IN @it_billing_document_date.

        SELECT bukrs,
                    belnr,
                    gjahr,
                    monat,
                    waers, awkey, xblnr, budat, bldat, blart, kursf, awtyp, xreversal, tcode, stblg, bktxt, usnam
                   FROM zsd_c_bkpf
                   INTO TABLE @lt_bkpf
                     FOR ALL ENTRIES IN @it_blart
                   WHERE blart = @it_blart-blart.
      ENDIF.

      IF lt_bkpf IS NOT INITIAL.
        SELECT
          bukrs,
          belnr,
          gjahr,
          monat,
          waers,
          awkey,
          xblnr,
          budat,
          bldat,
          blart,
          kursf,
          awtyp,
          xreversal,
          tcode,
          stblg
          FROM zsd_c_bkpf
        INTO TABLE @DATA(lt_bkpf_date)
        FOR ALL ENTRIES IN @lt_bkpf
          WHERE bukrs = @lt_bkpf-bukrs AND
                gjahr = @lt_bkpf-gjahr AND
                belnr = @lt_bkpf-stblg.


      ENDIF.

      SELECT xblnr,
       bukrs,
       belnr,
       gjahr,
       budat,
       bldat,
       blart, bupla, stblg, xrech, ivtyp, lifnr FROM zfi_c_rbkp INTO TABLE @DATA(lt_rbkp)
    FOR ALL ENTRIES IN @lt_bkpf
             WHERE
                   blart = @lc_re
        AND belnr = @lt_bkpf-awkey(10)
        AND gjahr = @lt_bkpf-gjahr.

      SORT lt_bkpf BY xblnr.

      LOOP AT lt_bkpf ASSIGNING FIELD-SYMBOL(<fs_bkpf>).
        <fs_bkpf>-awkey = <fs_bkpf>-awkey+0(10).
      ENDLOOP.

      IF lt_bkpf IS NOT INITIAL.
        SELECT
          xblnr,
          vbeln,
          kunag,
          fkart
          FROM zsd_c_vbrk INTO TABLE @DATA(lt_vbrk) FOR ALL ENTRIES IN @lt_bkpf
          WHERE vbeln = @lt_bkpf-awkey(10)
          AND  fkdat = @lt_bkpf-bldat.
      ENDIF.
      IF lt_vbrk IS NOT INITIAL.
        SORT lt_vbrk BY vbeln .
        DATA(lt_vbrk_tmp) = lt_vbrk.

        SELECT
        vbeln,
        matnr,
        werks,
        aubel,
          fkimg,
          posnr, vrkme
        FROM zsd_c_vbrp INTO   TABLE @DATA(lt_vbrp) FOR ALL ENTRIES IN @lt_vbrk
        WHERE vbeln = @lt_vbrk-vbeln.

        IF lt_vbrp IS NOT INITIAL.
          SELECT
            matnr,
            werks,
            steuc
            FROM zsd_c_marc INTO TABLE @DATA(lt_marc) FOR ALL ENTRIES IN @lt_vbrp
            WHERE matnr = @lt_vbrp-matnr AND
                  werks = @lt_vbrp-werks.
        ENDIF.
      ENDIF.

      SELECT xblnr, bukrs, belnr, gjahr, budat, bldat, blart, stblg
        FROM zfi_c_rbkp INTO TABLE @DATA(lt_rbkp_temp)
        FOR ALL ENTRIES IN @lt_bkpf
        WHERE stblg = @lt_bkpf-awkey(10) AND
                              gjahr = @lt_bkpf-gjahr AND
                             bukrs = @lt_bkpf-bukrs.
      IF lt_rbkp_temp[] IS NOT INITIAL.
        LOOP AT lt_bkpf INTO DATA(ls_bkpf_final).
          READ TABLE lt_rbkp_temp INTO DATA(ls_rbkp_temp) WITH KEY stblg = ls_bkpf_final-awkey(10) bukrs =  ls_bkpf_final-bukrs
          gjahr =  ls_bkpf_final-gjahr.
          IF sy-subrc = 0.
            IF ls_bkpf_final-budat+4(2) = ls_rbkp_temp-budat+4(2).
              DELETE lt_bkpf WHERE awkey(10) =  ls_rbkp_temp-belnr.
              DELETE lt_bkpf WHERE awkey = ls_rbkp_temp-stblg.
              CLEAR : ls_rbkp_temp.
            ENDIF.
          ENDIF.
        ENDLOOP.
      ENDIF.

      IF lt_rbkp[] IS NOT INITIAL.
        SELECT  belnr,
                gjahr,
                buzei,
                ebeln,
                ebelp,
                zekkn,
                matnr,
                bukrs,
                werks,
                wrbtr,
                shkzg,
                mwskz,
                menge,
                bstme,
                pstyp,
                bklas,
                tbtkz,
                lfbnr,
                lfgja,
                lifnr,
                customs_val,
                hsn_sac
                FROM  rseg   INTO TABLE @DATA(lt_rseg) FOR ALL ENTRIES IN @lt_rbkp
                                                  WHERE belnr = @lt_rbkp-belnr
                                                  AND   gjahr = @lt_rbkp-gjahr.
      ENDIF.

      IF lt_rseg IS NOT INITIAL.
        SELECT
        ebeln,
        bukrs,
        bedat,
        bsart
        FROM ekko INTO TABLE @DATA(lt_ekko) FOR ALL ENTRIES IN @lt_rseg
          WHERE bukrs = @lt_rseg-bukrs AND
                ebeln = @lt_rseg-ebeln.

        IF lt_ekko IS NOT INITIAL.

          SELECT
              ebeln,
              ebelp,
              knttp,
              txz01,
              matnr,
              j_1bnbm,
              iprkz
             FROM ekpo INTO  TABLE @DATA(lt_ekpo1)
         FOR ALL ENTRIES IN @lt_ekko
         WHERE ebeln = @lt_ekko-ebeln.

          SELECT ebeln,
                 ebelp,
                 anln1
             FROM ekkn INTO  TABLE @DATA(lt_ekkn)
             FOR ALL ENTRIES IN @lt_ekko
                    WHERE ebeln = @lt_ekko-ebeln.

          SELECT ebeln,
                 ebelp,
                 belnr,
                 gjahr
            FROM ekbe INTO  TABLE @DATA(it_ekbe)
              FOR ALL ENTRIES IN @lt_ekko
           WHERE ebeln = @lt_ekko-ebeln
            AND bwart = '101'.
        ENDIF.

      ENDIF.

      LOOP AT it_ekbe INTO DATA(is_ekbe).
        CONCATENATE is_ekbe-belnr is_ekbe-gjahr INTO is_awkey-awkey.
        is_awkey-gjahr = is_ekbe-gjahr.
        APPEND is_awkey TO it_awkey.

      ENDLOOP.

      IF lt_bkpf IS NOT INITIAL.
        SELECT bukrs,
               belnr,
               gjahr,
               txgrp,
               taxps,
               buzei,
               buzid,
               koart,
               mwskz,
               ktosl,
               matnr,
               werks,
               menge,
               meins,
               bustw,
               bupla,
               lifnr,
               zuonr,
               hsn_sac,
               bschl,
               hkont,
               dmbtr,
               gst_part,
               plc_sup,
               gsber,
               kunnr
               FROM bseg INTO TABLE @DATA(lt_bseg) FOR ALL ENTRIES IN @lt_bkpf
                                               WHERE bukrs = @lt_bkpf-bukrs
                                               AND   belnr = @lt_bkpf-belnr
                                               AND   gjahr = @lt_bkpf-gjahr  ORDER BY PRIMARY KEY.
        IF lt_bseg IS NOT INITIAL.
          SELECT
                   matnr
                   werks
                   steuc
                   FROM marc APPENDING TABLE lt_marc FOR ALL ENTRIES IN lt_bseg
                   WHERE matnr = lt_bseg-matnr.

        ENDIF.

        DATA(lt_bseg_tcs) = lt_bseg.
        DELETE lt_bseg_tcs WHERE ktosl NE 'WIT'.

        IF lt_bseg IS NOT INITIAL.

          DATA(lt_bseg_hsn) = lt_bseg.
          IF lt_bseg_hsn IS NOT INITIAL.
            DELETE lt_bseg_hsn WHERE hsn_sac IS INITIAL.
          ENDIF.

          SORT lt_bseg BY bukrs belnr gjahr txgrp  taxps.

        ENDIF.

        DATA(lt_bseg_lifnr) = lt_bseg.
        lr_koart = VALUE #( ( sign = 'I' option = 'EQ' low = 'K' )
                            ( sign = 'I' option = 'EQ' low = 'D' ) ).

        DELETE lt_bseg_lifnr WHERE koart NOT IN lr_koart.

        IF lt_bseg[] IS NOT INITIAL.
          SELECT matnr, maktx FROM makt INTO TABLE @DATA(lt_makt)
            FOR ALL ENTRIES IN @lt_bseg
            WHERE matnr = @lt_bseg-matnr.

          SELECT * FROM mara INTO TABLE @DATA(it_mara)
            FOR ALL ENTRIES IN @lt_bseg
          WHERE matnr = @lt_bseg-matnr.
        ENDIF.

        SORT it_mara BY matnr.
        "ITC Tax condition mapping.
        SELECT * FROM zsd_t_tax_cond INTO
          TABLE @DATA(it_cond)
          WHERE supply_type = 'P'.

        IF lt_bseg IS NOT INITIAL.
          DATA: lr_cond  TYPE RANGE OF bset-kschl.
          CLEAR: lr_cond.
          LOOP AT it_cond INTO DATA(is_cond).
            lr_cond = VALUE #( BASE lr_cond ( sign = 'I' option = 'EQ' low = is_cond-kschl ) ).
          ENDLOOP.
          SELECT bukrs,
                 belnr,
                 gjahr,
                 taxps,
                 ktosl,
                 kschl,
                 txgrp,
                 buzei,
                 shkzg,
                 fwbas,
                 fwste,
                 kbetr,
                 mwskz,
                 hkont,
                 hwbas,
                 hwste
                 FROM  bset INTO TABLE @lt_bset FOR ALL ENTRIES IN @lt_bseg
                                                  WHERE bukrs = @lt_bseg-bukrs
                                                    AND belnr = @lt_bseg-belnr
                                                    AND gjahr = @lt_bseg-gjahr
                                                    AND  ( kschl IN @lr_cond OR  ktosl IN ('JII','JIC','JIS') ).
*                                                    AND  ( kschl IN @lr_cond OR  KSCHL IN ('JIIG','JICG','JISG',
*                                                                                           'JIIN','JICN','JISN',
*                                                                                           'JJIG','JJIR','JJCG','JIIR','JICR','JJSG','JISR') ).



          LOOP AT lt_bset ASSIGNING FIELD-SYMBOL(<fs_bset>).
            TRY.
                DATA(lv_gsp_tax) = it_cond[ kschl = <fs_bset>-kschl ]-gsp_tax.
                <fs_bset>-gsp_tax = lv_gsp_tax.
              CATCH cx_sy_itab_line_not_found.

            ENDTRY.

          ENDLOOP.

        ENDIF.

        IF lt_bkpf  IS NOT INITIAL.
          DATA(lt_bseg_lifnr_plant) = lt_bseg.
          DELETE lt_bseg_lifnr_plant WHERE werks IS INITIAL.

          IF lt_bseg IS NOT INITIAL.

            DATA(lt_bseg_matnr) = lt_bseg.
            DELETE lt_bseg_matnr WHERE matnr IS INITIAL.
            DELETE lt_bseg WHERE  buzid <> 'T' AND txgrp = '' ." AND HKONT NE LC_IGST1..
            SORT lt_bseg BY bukrs belnr gjahr txgrp taxps.
            DELETE ADJACENT DUPLICATES FROM lt_bseg COMPARING bukrs belnr gjahr txgrp taxps.
          ENDIF.


          SELECT
          werks, name1, regio, j_1bbranch,adrnr
          FROM t001w INTO TABLE @DATA(lt_t001w) FOR ALL ENTRIES IN @lt_bseg_lifnr_plant
            WHERE werks = @lt_bseg_lifnr_plant-werks.

          IF lt_t001w IS NOT INITIAL.
            SELECT bukrs, branch, gstin, name, adrnr
              FROM j_1bbranch
              INTO TABLE @DATA(lt_j_1bbranch_plant) FOR ALL ENTRIES IN @lt_t001w
              WHERE branch = @lt_t001w-j_1bbranch.
          ENDIF.

          IF lt_j_1bbranch_plant IS NOT INITIAL.
            SELECT
            addrnumber,
            name3,
            city1,
            post_code1,
            region,
            street,
            str_suppl1,
            str_suppl2,
            str_suppl3
            FROM adrc INTO TABLE @DATA(lt_bupadrc_plant) FOR ALL ENTRIES IN @lt_j_1bbranch_plant WHERE addrnumber = @lt_j_1bbranch_plant-adrnr.
          ENDIF.

          IF lt_bseg_lifnr IS NOT  INITIAL.
            SELECT bukrs, branch, gstin, name, adrnr FROM j_1bbranch
              INTO TABLE @DATA(lt_j_1bbranch) FOR ALL ENTRIES IN @lt_bseg_lifnr
              WHERE branch = @lt_bseg_lifnr-bupla
              AND bukrs = @lt_bseg_lifnr-bukrs.
            IF sy-subrc = 0.
              SELECT
              addrnumber,
              name3,
              city1,
              post_code1,
              region,
              street,
              str_suppl1,
              str_suppl2,
              str_suppl3
              FROM adrc INTO TABLE @DATA(lt_bupadrc) FOR ALL ENTRIES IN @lt_j_1bbranch WHERE addrnumber = @lt_j_1bbranch-adrnr.
            ENDIF.
          ENDIF.
          IF lt_bseg_lifnr IS NOT INITIAL.
            SELECT lifnr,
           land1,
           name1,
           name2,
           name3,
           name4,
           ort01,
           regio,
           stceg,
           stcd3, adrnr, ktokk FROM lfa1 INTO TABLE @DATA(lt_lfa1) FOR ALL ENTRIES IN @lt_bseg_lifnr
                                                 WHERE ( lifnr = @lt_bseg_lifnr-gst_part OR lifnr = @lt_bseg_lifnr-lifnr ).

            SELECT kunnr,
         land1,
         name1,
         name2,
         name3,
         name4,
         ort01,
         regio,
         stceg,
         stcd3, adrnr,pstlz  FROM kna1 INTO TABLE @DATA(lt_kna1) FOR ALL ENTRIES IN @lt_bseg_lifnr
                                               WHERE (  kunnr = @lt_bseg_lifnr-kunnr ).
          ENDIF.
        ENDIF.
        IF lt_rbkp IS NOT INITIAL.
          SELECT lifnr,
           land1,
           name1,
           name2,
           name3,
           name4,
           ort01,
           regio,
           stceg,
           stcd3 ,adrnr, ktokk FROM lfa1 APPENDING TABLE @lt_lfa1 FOR ALL ENTRIES IN @lt_rbkp
                                                 WHERE ( lifnr = @lt_rbkp-lifnr )." OR lifnr = lt_bseg_lifnr-lifnr ).




        ENDIF.
        SORT lt_lfa1 BY lifnr.
        DELETE ADJACENT DUPLICATES FROM lt_lfa1 COMPARING lifnr.

        IF lt_lfa1 IS NOT INITIAL.
          SELECT  addrnumber,
                  name3,
                  city1,
                  post_code1,
                  region,
                  street,
                  str_suppl1,
                  str_suppl2,
                  str_suppl3
            FROM adrc INTO TABLE @DATA(lt_adrc)  FOR ALL ENTRIES IN @lt_lfa1
            WHERE addrnumber = @lt_lfa1-adrnr.

          SELECT * FROM adr6 INTO TABLE @DATA(lt_adr6)

            FOR ALL ENTRIES IN @lt_lfa1
             WHERE addrnumber = @lt_lfa1-adrnr.
        ENDIF.
      ENDIF.
    ENDIF.
    DATA(lt_bset1) = lt_bset[].
    DELETE lt_bset1 WHERE kschl IS NOT INITIAL.
    SORT lt_bkpf BY bukrs belnr gjahr ."awkey.

    SORT lt_bseg_lifnr BY bukrs belnr gjahr.
    SORT lt_bseg_lifnr_plant BY bukrs belnr gjahr werks.
    SORT lt_lfa1 BY lifnr.
    SORT lt_adrc BY addrnumber.
    SORT lt_adr6 BY addrnumber.
    SORT lt_bupadrc_plant BY addrnumber.
    SORT lt_bseg BY bukrs belnr gjahr ."mwskz.
    SORT lt_j_1bbranch BY bukrs branch gstin.
    SORT lt_j_1bbranch_plant BY bukrs branch gstin.
    SORT lt_bupadrc BY addrnumber.
    SORT lt_t001w BY werks.
    SORT lt_ekko BY ebeln bukrs.
    SORT lt_ekpo1 BY ebeln ebelp .
    SORT lt_rseg BY belnr gjahr buzei.
    SORT lt_rbkp BY belnr gjahr.
    SORT lt_bset BY bukrs belnr gjahr  taxps   gsp_tax .
    SORT lt_bset1 BY bukrs belnr gjahr  taxps   ktosl.
    SORT lt_bseg_matnr BY bukrs belnr buzei txgrp.
    SORT lt_bseg_tcs BY bukrs belnr buzei txgrp.
    SORT lt_makt BY matnr.
    SORT lt_bkpf_date BY bukrs gjahr belnr.
    SORT lt_vbrk BY vbeln .
    SORT lt_kna1 BY kunnr.
    SORT lt_vbrp BY vbeln .
    SORT lt_marc BY matnr werks.



************************    SET DATA  ************************************************

    IF lt_ekpo1[] IS NOT INITIAL.
      SELECT ebeln, ebelp, packno FROM essr INTO TABLE  @DATA(t_essr)
        FOR ALL ENTRIES IN @lt_ekpo1
        WHERE ebeln = @lt_ekpo1-ebeln
          AND ebelp = @lt_ekpo1-ebelp.

      IF t_essr[] IS NOT INITIAL.
        SELECT  packno, sub_packno  FROM esll INTO TABLE @DATA(t_esll1)
          FOR ALL ENTRIES IN @t_essr
          WHERE packno = @t_essr-packno.

        IF t_esll1[] IS NOT INITIAL.
          SELECT packno, sub_packno, srvpos FROM esll INTO TABLE @DATA(t_esll2)
            FOR ALL ENTRIES IN @t_esll1
            WHERE packno = @t_esll1-sub_packno.

          SORT t_essr BY ebeln ebelp.
          SORT t_esll1 BY packno.
          SORT t_esll2 BY packno.

        ENDIF.
      ENDIF.
    ENDIF.

    CLEAR ls_final.

    IF lt_bkpf[] IS NOT INITIAL.
      SELECT * FROM bsec INTO TABLE lt_bsec
         FOR ALL ENTRIES IN lt_bkpf
         WHERE bukrs = lt_bkpf-bukrs AND
               belnr = lt_bkpf-belnr AND
               gjahr = lt_bkpf-gjahr ORDER BY PRIMARY KEY.
    ENDIF.


    DATA: awkey TYPE bkpf-awkey.
    SORT lt_bsec BY bukrs belnr gjahr.
    SORT t_bkpf_bseg_del BY bukrs gjahr xblnr lifnr blart.
    SORT lt_ekpo1 BY ebeln ebelp.
    SORT lt_ekkn BY ebeln ebelp.
    SORT it_ekbe BY ebeln ebelp.

    LOOP AT lt_bkpf  INTO DATA(ls_bkpf).
      IF ls_bkpf-xreversal IS NOT INITIAL.
        READ TABLE lt_bkpf_date INTO DATA(ls_bkpf_date) WITH KEY bukrs = ls_bkpf-bukrs
                                                                 gjahr = ls_bkpf-gjahr
                                                                 belnr = ls_bkpf-stblg BINARY SEARCH.
        IF sy-subrc = 0.
          IF ls_bkpf-budat+4(2) = ls_bkpf_date-budat+4(2).
            CLEAR : ls_bkpf,ls_bkpf_date.
            CONTINUE.
          ENDIF.
        ENDIF.
      ENDIF.


      READ TABLE lt_bseg TRANSPORTING NO FIELDS WITH KEY bukrs = ls_bkpf-bukrs
                                                  belnr = ls_bkpf-belnr
                                                  gjahr = ls_bkpf-gjahr BINARY SEARCH.

      IF sy-subrc = 0.

        DATA(l_var_tabix) = sy-tabix.

        LOOP AT lt_bseg  INTO DATA(ls_bseg) FROM l_var_tabix.

          ls_final-custom3 = ls_bkpf-usnam.
          IF ls_bkpf-bukrs = ls_bseg-bukrs AND ls_bkpf-belnr = ls_bseg-belnr AND ls_bkpf-gjahr = ls_bseg-gjahr.

            ls_final-purpose = 'OGST'.
            ls_final-supplytype = 'P'.
            ls_final-documentnumber = ls_bkpf-xblnr.
            ls_final-documentdate = ls_bkpf-bldat.

            CONCATENATE ls_final-documentdate+6(2) '-' ls_final-documentdate+4(2) '-' ls_final-documentdate+0(4)  INTO ls_final-documentdate.
            ls_final-customitem2 = ls_bkpf-budat.

            CONCATENATE ls_bkpf-budat+6(2) '-' ls_bkpf-budat+4(2) '-' ls_bkpf-budat+0(4)  INTO ls_final-customitem2.
            ls_final-returnperiod = ls_bkpf-bldat.

*            CLEAR:WA_BLART.
            READ TABLE it_blart INTO DATA(wa_blart) WITH KEY blart = ls_bkpf-blart.
            IF sy-subrc = 0.
              ls_final-documenttype = wa_blart-doc_type.
            ENDIF.

            IF ls_bkpf-blart = lc_re .
*              CLEAR:ls_RBKP.
              READ TABLE lt_rbkp INTO DATA(ls_rbkp) WITH KEY  belnr = ls_bkpf-awkey+0(10) gjahr = ls_bkpf-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
                IF ls_rbkp-xrech = 'X'.
                  ls_final-documenttype = lc_inv ."INV'.
                ELSE.
                  ls_final-documenttype = lc_crn."'CRN'.
                ENDIF.
              ENDIF.
            ENDIF.

            READ TABLE lt_bseg_lifnr INTO DATA(ls_bseg_lifnr) WITH KEY bukrs = ls_bkpf-bukrs
                                                                      belnr = ls_bkpf-belnr
                                                                      gjahr = ls_bkpf-gjahr BINARY SEARCH .
            IF sy-subrc NE 0.
              READ TABLE  lt_lfa1 INTO DATA(ls_lfa1) WITH KEY lifnr = ls_rbkp-lifnr BINARY SEARCH .
            ELSE.
              IF ls_bseg_lifnr-gst_part IS NOT INITIAL.
                CLEAR: ls_lfa1.
                READ TABLE  lt_lfa1 INTO ls_lfa1 WITH KEY lifnr = ls_bseg_lifnr-gst_part BINARY SEARCH .
              ELSE.
                CLEAR: ls_lfa1.
                READ TABLE  lt_lfa1 INTO ls_lfa1 WITH KEY lifnr = ls_bseg_lifnr-lifnr BINARY SEARCH .
              ENDIF.
            ENDIF.
            IF sy-subrc = 0.
              ls_final-documentvalue = ls_bseg_lifnr-dmbtr.
              CONDENSE ls_final-documentvalue.
              ls_final-billfromgstin = ls_lfa1-stcd3.
              ls_final-billfromstatecode = ls_lfa1-stcd3(2).
              ls_final-billfromlegalname = ls_lfa1-name1.
              ls_final-custom5 = ls_bseg_lifnr-lifnr.
              READ TABLE lt_adrc INTO DATA(ls_adrc) WITH KEY addrnumber = ls_lfa1-adrnr BINARY SEARCH.
              IF sy-subrc = 0.
                CONCATENATE ls_adrc-street ls_adrc-str_suppl1 INTO ls_final-billfromaddress1.
                CONCATENATE ls_final-billfromaddress1 ls_adrc-str_suppl2 INTO ls_final-billfromaddress1.
                ls_final-billfromaddress2 = ls_adrc-str_suppl3.
                ls_final-billfrompincode = ls_adrc-post_code1.
                READ TABLE lt_adr6 INTO DATA(ls_adr6) WITH KEY addrnumber = ls_lfa1-adrnr BINARY SEARCH.
                IF sy-subrc = 0.
                  ls_final-billtoemail = ls_adr6-smtp_addr.
                ENDIF.
                ls_final-billfromstatecode = ls_adrc-region.
              ENDIF.
            ENDIF.

            ls_final-custom5 =  ls_bseg_lifnr-lifnr.

            READ TABLE  lt_kna1 INTO DATA(ls_kna1) WITH KEY kunnr = ls_bseg_lifnr-kunnr BINARY SEARCH .
            IF sy-subrc = '0'.
              ls_final-billfromgstin = ls_kna1-stcd3.
              ls_final-billfromstatecode = ls_kna1-stcd3(2).
              ls_final-billfromlegalname = ls_kna1-name1.
              ls_final-custom5 = ls_bseg_lifnr-kunnr.
              ls_final-billfromstatecode = ls_kna1-regio.
              ls_final-custom5 =  ls_bseg_lifnr-kunnr.
            ENDIF.

            READ TABLE lt_j_1bbranch INTO DATA(ls_j_1bbranch) WITH KEY bukrs =  ls_bseg_lifnr-bukrs
                                                                     branch = ls_bseg_lifnr-bupla BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-branch  = ls_j_1bbranch-branch.
              ls_final-billtogstin  = ls_j_1bbranch-gstin.
              ls_final-locationgstin  = ls_j_1bbranch-gstin.
              ls_final-billtolegalname   =   ls_j_1bbranch-name.
              READ TABLE lt_bupadrc INTO DATA(ls_bupadrc) WITH KEY addrnumber = ls_j_1bbranch-adrnr BINARY SEARCH.
              IF sy-subrc = 0.
                CONCATENATE ls_bupadrc-street ls_bupadrc-str_suppl1 INTO ls_final-billtoaddress1.
                CONCATENATE ls_bupadrc-str_suppl2 ls_bupadrc-str_suppl3 INTO ls_final-billtoaddress2.
                ls_final-billtocity = ls_bupadrc-city1.
                ls_final-billtopincode = ls_bupadrc-post_code1.
                ls_final-billtostatecode = ls_bupadrc-region.
                ls_final-pos = ls_bupadrc-region.
                ls_final-billtostatecode = ls_final-billtogstin+0(2).
                ls_final-pos = ls_final-billtogstin+0(2).
              ENDIF.

            ELSE.

              READ TABLE lt_bseg_lifnr_plant INTO DATA(ls_bseg_lifnr_plant) WITH KEY
                                                                       bukrs = ls_bkpf-bukrs
                                                                       belnr = ls_bkpf-belnr
                                                                       gjahr = ls_bkpf-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
                READ TABLE lt_t001w INTO DATA(ls_t001w) WITH KEY werks = ls_bseg_lifnr_plant-werks BINARY SEARCH.
                IF sy-subrc = 0.
                  READ TABLE lt_j_1bbranch_plant INTO DATA(ls_j_1bbranch_plant) WITH KEY bukrs =  ls_bkpf-bukrs
                                                                                     branch = ls_t001w-j_1bbranch BINARY SEARCH.

                  IF sy-subrc = 0.
                    ls_final-billtogstin  = ls_j_1bbranch_plant-gstin.
                    ls_final-branch  = ls_j_1bbranch_plant-branch.
                    ls_final-locationgstin  = ls_j_1bbranch_plant-gstin.
                    ls_final-billtolegalname   = ls_j_1bbranch_plant-name.
                    READ TABLE lt_bupadrc_plant INTO DATA(ls_bupadrc_plant) WITH KEY addrnumber = ls_j_1bbranch_plant-adrnr BINARY SEARCH.
                    IF sy-subrc = 0.
                      CONCATENATE ls_bupadrc_plant-street ls_bupadrc_plant-str_suppl1 INTO ls_final-billtoaddress1.
                      CONCATENATE ls_bupadrc_plant-str_suppl2 ls_bupadrc_plant-str_suppl3 INTO ls_final-billtoaddress2.
                      ls_final-billtocity = ls_bupadrc_plant-city1.
                      ls_final-billtopincode = ls_bupadrc_plant-post_code1.
                      ls_final-billtostatecode = ls_bupadrc_plant-region.
                      ls_final-pos = ls_bupadrc_plant-region.
                      ls_final-billtostatecode = ls_final-billtogstin+0(2).
                      ls_final-pos = ls_final-billtogstin+0(2).
                      IF ls_bseg_lifnr_plant-plc_sup IS NOT INITIAL.
                        ls_final-pos  = ls_bseg_lifnr_plant-plc_sup.
                      ENDIF.
                    ENDIF.
                  ENDIF.
                ENDIF.
              ENDIF.
            ENDIF.

            READ TABLE lt_bseg_matnr INTO DATA(ls_bseg_matnr) WITH KEY belnr = ls_bseg-belnr  bukrs = ls_bseg-bukrs gjahr = ls_bseg-gjahr txgrp = ls_bseg-txgrp.
            IF sy-subrc = 0.
              READ TABLE lt_makt INTO DATA(ls_makt) WITH KEY matnr = ls_bseg_matnr-matnr BINARY SEARCH.
              IF sy-subrc = 0.
                ls_final-itemdescription = ls_makt-maktx.
                ls_final-itemname = ls_makt-matnr.
                READ TABLE it_mara INTO DATA(is_mara) WITH KEY matnr = ls_bseg_matnr-matnr BINARY SEARCH.
                IF sy-subrc = 0.
                  ls_final-customitem4 = is_mara-matkl.
                ENDIF.
              ENDIF.
            ENDIF.

            ls_final-customitem1 = ls_bkpf-belnr.
            ls_final-custom1 = ls_bkpf-belnr.
            ls_final-customitem6  = ls_bseg_lifnr-bupla.
            ls_final-customitem3 = ls_bkpf-blart.
            ls_final-customitem7 = ls_bseg-mwskz.

            ls_final-hsn = ls_bseg-hsn_sac.

            IF ls_final-hsn IS INITIAL.
              READ TABLE lt_marc INTO DATA(ls_marc) WITH KEY matnr = ls_makt-matnr.
              IF sy-subrc = 0.
                ls_final-hsn = ls_marc-steuc..
              ENDIF.
            ENDIF.

            ls_final-company_code = ls_bkpf-bukrs.

            DELETE lt_bset WHERE mwskz = 'V0'.

            READ TABLE lt_rseg INTO DATA(ls_rseg) WITH KEY belnr = ls_bkpf-awkey+0(10) gjahr = ls_bkpf-gjahr  buzei = ls_bseg-txgrp BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-hsn = ls_rseg-hsn_sac.
              ls_final-quantity = ls_rseg-menge.
              READ TABLE lt_ekko INTO DATA(ls_ekko) WITH KEY ebeln = ls_rseg-ebeln bukrs = ls_rseg-bukrs BINARY SEARCH.
              ls_final-itemserialnumber =  ls_rseg-matnr.
              READ TABLE lt_ekkn INTO DATA(ls_ekkn) WITH KEY  ebeln = ls_rseg-ebeln ebelp = ls_rseg-ebelp BINARY SEARCH.
              IF sy-subrc = 0.
                ls_final-itemname = ls_ekkn-anln1.
              ENDIF.

              CLEAR is_ekbe.
              READ TABLE it_ekbe INTO is_ekbe WITH KEY  ebeln = ls_rseg-ebeln ebelp = ls_rseg-ebelp BINARY SEARCH.
              IF sy-subrc = 0.
                CLEAR:awkey.
                CONCATENATE is_ekbe-belnr is_ekbe-gjahr INTO awkey.
              ENDIF.

              READ TABLE lt_ekpo1 INTO DATA(ls_ekpo1) WITH KEY ebeln = ls_rseg-ebeln ebelp = ls_rseg-ebelp BINARY SEARCH.
              IF sy-subrc = 0.

                "Service PO Details
                READ TABLE t_essr INTO DATA(wa_essr) WITH KEY ebeln = ls_rseg-ebeln ebelp = ls_rseg-ebelp BINARY SEARCH.
                IF sy-subrc = 0.
                  READ TABLE t_esll1 INTO DATA(wa_esll1) WITH KEY packno = wa_essr-packno BINARY SEARCH.
                  IF sy-subrc = 0.
                    READ TABLE t_esll2 INTO DATA(wa_esll2) WITH KEY packno = wa_esll1-sub_packno BINARY SEARCH.
                    IF sy-subrc = 0.
                      CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
                        EXPORTING
                          input  = wa_esll2-srvpos
                        IMPORTING
                          output = wa_esll2-srvpos.
                      ls_final-itemname = wa_esll2-srvpos.
                    ENDIF.
                  ENDIF.
                ENDIF.

                ls_final-itemdescription = ls_ekpo1-txz01.
              ENDIF.
            ENDIF.

            IF ls_ekko-bsart = 'ZCAP' OR ls_ekko-bsart = 'NC'.
              ls_final-itceligibility = 'CP'.
            ENDIF.
*
*            CLEAR:ls_bset.
            READ TABLE lt_bset INTO DATA(ls_bset) WITH KEY         bukrs = ls_bseg-bukrs
                                                                   belnr = ls_bseg-belnr
                                                                   gjahr = ls_bseg-gjahr
                                                                   taxps = ls_bseg-taxps
                                                                    gsp_tax = 'CGST' BINARY SEARCH..
            IF sy-subrc = 0.
              ls_final-taxablevalue =  ls_bset-hwbas.
              IF ls_bset-shkzg = 'S'.
                ls_final-cgstamount =  ls_bset-hwste. " ls_final-cgst_amount +
              ELSE.
                ls_final-cgstamount =  ls_bset-hwste * -1. "ls_final-cgst_amount +
              ENDIF.
              IF ls_bset-kbetr IS NOT INITIAL.
                ls_final-rate =   ls_final-rate  + ( ls_bset-kbetr /  10 ).
              ENDIF.
            ENDIF.

            CLEAR:ls_bset.
            READ TABLE lt_bset INTO ls_bset WITH KEY bukrs = ls_bseg-bukrs
                                                                          belnr = ls_bseg-belnr
                                                                          gjahr = ls_bseg-gjahr
                                                                          taxps = ls_bseg-taxps
                                                                            gsp_tax = 'SGST' BINARY SEARCH..

            IF sy-subrc = 0.
              IF ls_bset-shkzg = 'S'.
                ls_final-sgstamount =  ls_bset-hwste. " ls_final-sgst_amount +
              ELSE.
                ls_final-sgstamount =   ls_bset-hwste * -1. " ls_final-sgst_amount +
              ENDIF.
              IF  ls_final-sgstamount < 0 .

              ENDIF.
              IF ls_bset-kbetr IS NOT INITIAL.
                ls_final-rate =   ls_final-rate  +   ( ls_bset-kbetr /  10 ).
              ENDIF.
            ENDIF.



            CLEAR:ls_bset.
            READ TABLE lt_bset INTO ls_bset WITH KEY bukrs = ls_bseg-bukrs
                                                                   belnr = ls_bseg-belnr
                                                                   gjahr = ls_bseg-gjahr
                                                                   taxps = ls_bseg-taxps
                                                                   gsp_tax = 'IGST' BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-taxablevalue =  ls_bset-hwbas.
              IF ls_bset-shkzg = 'S'.
                ls_final-igstamount = ls_bset-hwste. " ls_final-igst_amount +
                CONDENSE ls_final-igstamount.
              ELSE.
                ls_final-igstamount =  ls_bset-hwste * -1. " ls_final-igst_Amount -
                CONDENSE ls_final-igstamount.
              ENDIF.
              IF ls_bset-kbetr IS NOT INITIAL.
                ls_final-rate = ( ls_bset-kbetr /  10 ).
                CONDENSE ls_final-rate.
              ENDIF.
            ENDIF.


            CLEAR:ls_bset.
            READ TABLE lt_bset INTO ls_bset WITH KEY         bukrs = ls_bseg-bukrs
                                                                         belnr = ls_bseg-belnr
                                                                         gjahr = ls_bseg-gjahr
                                                                         taxps = ls_bseg-taxps
                                                                          gsp_tax = 'RSGST' BINARY SEARCH.

            IF sy-subrc = 0.

              IF ls_final-taxablevalue IS INITIAL.
                ls_final-taxablevalue =  ls_bset-hwbas.
              ENDIF.
              IF ls_bset-shkzg = 'S'.
                ls_final-sgstamount =  ls_bset-hwste. " ls_final-cgst_amount +
              ELSE.
                ls_final-sgstamount =  ls_bset-hwste * -1. "ls_final-cgst_amount +
              ENDIF.

              IF ls_bset-kbetr < 0.
                ls_bset-kbetr = ls_bset-kbetr * -1.
              ENDIF.

              ls_final-reversecharge = 'Y'.
              ls_final-rate =   ls_final-rate  +   ( ls_bset-kbetr /  10 ).
            ENDIF.

            CLEAR:ls_bset.
            READ TABLE lt_bset INTO ls_bset WITH KEY         bukrs = ls_bseg-bukrs
                                                                           belnr = ls_bseg-belnr
                                                                           gjahr = ls_bseg-gjahr
                                                                           taxps = ls_bseg-taxps
                                                                            gsp_tax =  'RCGST' BINARY SEARCH.
            IF sy-subrc = 0.
              IF ls_final-taxablevalue IS INITIAL.
                ls_final-taxablevalue =  ls_bset-hwbas.
              ENDIF.
              IF ls_bset-shkzg = 'S'.
                ls_final-cgstamount =  ls_bset-hwste. " ls_final-cgst_amount +
              ELSE.
                ls_final-cgstamount =  ls_bset-hwste * -1. "ls_final-cgst_amount +
              ENDIF.
              IF  ls_final-cgstamount < 0 .

              ENDIF.

              IF ls_bset-kbetr < 0.

                ls_bset-kbetr = ls_bset-kbetr * -1.
              ENDIF.

              ls_final-reversecharge = 'Y'.
              ls_final-rate =   ls_final-rate  +   ( ls_bset-kbetr /  10 ).
            ENDIF.

            CLEAR:ls_bset.
            READ TABLE lt_bset INTO ls_bset WITH KEY bukrs = ls_bseg-bukrs
                                                                         belnr = ls_bseg-belnr
                                                                         gjahr = ls_bseg-gjahr
                                                                         taxps = ls_bseg-taxps
                                                                          gsp_tax =  'RIGST' BINARY SEARCH.

            IF sy-subrc = 0.
              IF ls_final-taxablevalue IS INITIAL.
                ls_final-taxablevalue =  ls_bset-hwbas.
              ENDIF.
              IF ls_bset-shkzg = 'S'.
                ls_final-igstamount = ls_bset-hwste. " ls_final-igst_amount +
              ELSE.
                ls_final-igstamount =  ls_bset-hwste * -1. " ls_final-igst_Amount -
              ENDIF.
              IF  ls_final-igstamount < 0 .

              ENDIF.

              IF ls_bset-kbetr < 0.

                ls_bset-kbetr = ls_bset-kbetr * -1.
              ENDIF.

              ls_final-reversecharge = 'Y'.
              ls_final-rate =     ( ls_bset-kbetr /  10 ).
            ENDIF.


            CLEAR:ls_bset.
            READ TABLE lt_bset INTO ls_bset WITH KEY         bukrs = ls_bseg-bukrs
                                                                               belnr = ls_bseg-belnr
                                                                               gjahr = ls_bseg-gjahr
                                                                               taxps = ls_bseg-taxps
                                                                                gsp_tax =  'CESS' BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-cessamount = ls_bset-hwste.
              ls_final-itccessamount = ls_bset-hwste.
            ENDIF.

            CLEAR:ls_bset.
            READ TABLE lt_bset1 INTO ls_bset WITH KEY         bukrs = ls_bseg-bukrs
                                                                   belnr = ls_bseg-belnr
                                                                   gjahr = ls_bseg-gjahr
                                                                   taxps = ls_bseg-taxps
                                                                   ktosl = 'JIC'
                                                                   kschl = '' BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-taxablevalue =  ls_bset-hwbas.
              IF ls_bset-shkzg = 'S'.
                ls_final-cgstamount =  ls_bset-hwste. " ls_final-cgst_amount +
              ELSE.
                ls_final-cgstamount =  ls_bset-hwste * -1. "ls_final-cgst_amount +
              ENDIF.

              ls_final-rate =   ls_final-rate  + ( ls_bset-kbetr /  10 ).
            ENDIF.

            CLEAR:ls_bset.
            READ TABLE lt_bset1 INTO ls_bset WITH KEY         bukrs = ls_bseg-bukrs
                                                                             belnr = ls_bseg-belnr
                                                                             gjahr = ls_bseg-gjahr
                                                                             taxps = ls_bseg-taxps
                                                                             ktosl = 'JIS'
                                                                             kschl = '' BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-taxablevalue =  ls_bset-hwbas.
              IF ls_bset-shkzg = 'S'.
                ls_final-sgstamount =  ls_bset-hwste. " ls_final-cgst_amount +
              ELSE.
                ls_final-sgstamount =  ls_bset-hwste * -1. "ls_final-cgst_amount +
              ENDIF.

              ls_final-rate =   ls_final-rate  + ( ls_bset-kbetr /  10 ).
            ENDIF.

            CLEAR:ls_bset.
            READ TABLE lt_bset1 INTO ls_bset WITH KEY         bukrs = ls_bseg-bukrs
                                                                              belnr = ls_bseg-belnr
                                                                              gjahr = ls_bseg-gjahr
                                                                              taxps = ls_bseg-taxps
                                                                              ktosl = 'JII'
                                                                              kschl = '' BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-taxablevalue =  ls_bset-hwbas.
              IF ls_bset-shkzg = 'S'.
                ls_final-igstamount =  ls_bset-hwste. " ls_final-cgst_amount +
              ELSE.
                ls_final-igstamount =  ls_bset-hwste * -1. "ls_final-cgst_amount +
              ENDIF.

              ls_final-rate =    ( ls_bset-kbetr /  10 ).
            ENDIF.

            IF  ls_final-hsn IS INITIAL.
              READ TABLE lt_bseg_hsn INTO DATA(ls_bseg_hsn) WITH KEY bukrs = ls_bkpf-bukrs
                                                                 belnr = ls_bkpf-belnr
                                                                 gjahr  = ls_bkpf-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
                ls_final-hsn = ls_bseg_hsn-hsn_sac.
              ENDIF.
            ENDIF.
            CLEAR:ls_bset.
            READ TABLE lt_bset INTO ls_bset WITH KEY bukrs = ls_bseg-bukrs
                                                                  belnr = ls_bseg-belnr
                                                                  gjahr = ls_bseg-gjahr
                                                                  taxps = ls_bseg-taxps
                                                                   gsp_tax = 'JIMD' BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-taxablevalue =  ls_bset-hwbas.
              IF ls_bset-shkzg = 'S'.
                ls_final-igstamount = ls_bset-hwste. " ls_final-igst_amount +
              ELSE.
                ls_final-igstamount =  ls_bset-hwste * -1. " ls_final-igst_Amount -
              ENDIF.
              IF  ls_final-igstamount < 0 .

              ENDIF.
              ls_final-rate =  ( ls_bset-kbetr /  10 ).

              ls_final-documenttype = lc_boe ."'BOE'.
              IF ls_final-hsn(2) = '99'.
                ls_final-transactiontype = lc_imps."'IMPS'.
              ELSE.
                ls_final-transactiontype = lc_impg."'IMPG'.
              ENDIF.
            ENDIF.

            CLEAR: ls_makt.
            READ TABLE lt_makt INTO ls_makt WITH KEY matnr = ls_bseg_matnr-matnr BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-itemdescription = ls_makt-maktx.
              ls_final-itemname = ls_makt-matnr.

            ENDIF.

            IF ls_final-quantity IS INITIAL.
              ls_final-quantity = ls_bseg-menge.
            ENDIF.

            CLEAR ls_final-uqc
            .
            IF ls_final-uqc IS INITIAL.
              ls_final-uqc = 'OTH'.
            ENDIF.
            IF ls_final-cgstamount IS NOT INITIAL AND ls_final-sgstamount IS NOT INITIAL.
              CLEAR ls_final-igstamount.
            ENDIF.
            IF ls_bkpf-tcode = 'FB08'.
              ls_final-documenttype = lc_crn." 'CRN'.
            ENDIF.

            IF ls_final-documenttype = lc_crn.
              IF ls_final-taxablevalue < 0.
                ls_final-taxablevalue = ls_final-taxablevalue * -1.
              ENDIF.
              IF ls_final-igstamount < 0.
                ls_final-igstamount = ls_final-igstamount * -1.
              ENDIF.
              IF ls_final-cgstamount < 0.
                ls_final-cgstamount = ls_final-cgstamount * -1.
              ENDIF.
              IF ls_final-sgstamount < 0.
                ls_final-sgstamount = ls_final-sgstamount * -1.
              ENDIF..
              IF ls_final-rate < 0.
                ls_final-rate = ls_final-rate  * -1.
              ENDIF.
              IF ls_final-documentvalue < 0.
                ls_final-documentvalue = ls_final-documentvalue  * -1.
              ENDIF.

            ENDIF.


            IF ls_final-reversecharge = 'Y'.
              IF ls_final-igstamount < 0.
                IF ls_final-taxablevalue > 0.
                  ls_final-taxablevalue = ls_final-taxablevalue * -1.
                ENDIF.
              ENDIF.
              IF ls_final-cgstamount < 0.
                IF ls_final-taxablevalue > 0.
                  ls_final-taxablevalue = ls_final-taxablevalue * -1.
                ENDIF.
              ENDIF.

              IF ls_final-igstamount > 0.
                IF ls_final-taxablevalue < 0.
                  ls_final-taxablevalue = ls_final-taxablevalue * -1.
                ENDIF.
              ENDIF.
              IF ls_final-cgstamount > 0.
                IF ls_final-taxablevalue < 0.
                  ls_final-taxablevalue = ls_final-taxablevalue * -1.
                ENDIF.
              ENDIF.
            ENDIF.

            IF ls_final-reversecharge = ''.
              IF ls_final-igstamount < 0 AND ls_final-taxablevalue > 0.

                ls_final-taxablevalue = ls_final-taxablevalue * -1.
              ENDIF.

              IF ls_final-cgstamount < 0 AND ls_final-taxablevalue > 0.

                ls_final-taxablevalue = ls_final-taxablevalue * -1.
              ENDIF.
            ENDIF.

            IF ls_final-billfromgstin = 'URD'.
              CLEAR:ls_final-billfromgstin.
            ENDIF.
            DATA: ls_bsec LIKE LINE OF lt_bsec.
            READ TABLE lt_bsec INTO ls_bsec WITH KEY bukrs = ls_bkpf-bukrs belnr = ls_bkpf-belnr gjahr = ls_bkpf-gjahr BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-billfromgstin = ls_bsec-stcd3.
              ls_final-billfromstatecode = ls_bsec-stcd3(2).
              ls_final-billfromlegalname = ls_bsec-name1.
              CONCATENATE ls_bsec-stras '' INTO ls_final-billfromaddress1.
              ls_final-billfrompincode = ls_bsec-pstlz.
              ls_final-billfromcity = ls_bsec-ort01.
              ls_final-billfromstatecode = ls_bsec-regio.
            ENDIF.



            IF ls_final-transactiontype IS INITIAL.
              IF ls_final-billfromgstin IS NOT INITIAL.
                ls_final-transactiontype = lc_b2b."'B2B'.
              ELSE.
                ls_final-transactiontype = lc_b2c."'B2C'.
              ENDIF.

            ENDIF.
            IF ls_final-itceligibility <> 'NO'.

              IF ls_final-itcsgstamount IS INITIAL.
                ls_final-itcsgstamount    = ls_final-sgstamount.
              ENDIF.
              IF   ls_final-itccgstamount   IS INITIAL.
                ls_final-itccgstamount  = ls_final-cgstamount.
              ENDIF.
              IF ls_final-itcigstamount IS INITIAL.
                ls_final-itcigstamount  = ls_final-igstamount.
              ENDIF.

            ENDIF.


            CONDENSE ls_final-documentnumber NO-GAPS.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]_' IN ls_final-billfromcity WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billfromcity WITH space.
            CONDENSE ls_final-billfromcity NO-GAPS.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-itemdescription WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-itemdescription WITH space.
            CONDENSE ls_final-itemdescription.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-billfromlegalname WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billfromlegalname WITH space.
            CONDENSE ls_final-billfromlegalname.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-billfromaddress1 WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billfromaddress1 WITH space.
            CONDENSE ls_final-billfromaddress1.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-billfromaddress2 WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billfromaddress2 WITH space.
            CONDENSE ls_final-billfromaddress2.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-billtocity WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtocity WITH space.
            CONDENSE ls_final-billtocity.


            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-billtolegalname WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtolegalname WITH space.
            CONDENSE ls_final-billtolegalname.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-billtoaddress1 WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtoaddress1 WITH space.
            CONDENSE ls_final-billtoaddress1.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-billtoaddress2 WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtoaddress2 WITH space.
            CONDENSE ls_final-billtoaddress2.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN ls_final-billtocity WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtocity WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-hsn WITH space.
            REPLACE ALL OCCURRENCES OF '.' IN ls_final-hsn WITH space.
            CONDENSE ls_final-billtocity.
            CONDENSE ls_final-hsn NO-GAPS.

            ls_final-serialnumber = ls_bseg-txgrp.
            CONDENSE ls_final-quantity.


            READ TABLE lt_bseg_tcs INTO DATA(ls_bseg_tcs)  WITH KEY  belnr = ls_bkpf-belnr  bukrs = ls_bkpf-bukrs  gjahr = ls_bkpf-gjahr BINARY SEARCH.
            IF sy-subrc = 0.
              ls_final-othercharges = ls_bseg_tcs-dmbtr.
            ENDIF.

            IF ls_bkpf-tcode = 'FB08'.
              ls_final-documenttype = lc_crn."'CRN'.
            ENDIF.

            ls_final-custom4 = ls_bkpf-gjahr.
            ls_final-custom9 = ls_bkpf-bukrs.


            CONCATENATE ls_bkpf-budat+4(2) ls_bkpf-budat(4) INTO ls_final-returnperiod.

            IF ls_bset-mwskz IS NOT INITIAL.
              ls_final-customitem7 = ls_bset-mwskz.
            ELSE.
              ls_final-customitem7 = ls_bseg-mwskz.
            ENDIF.

            IF ls_final-documenttype = ''.
              IF ls_final-taxablevalue < 0.
                ls_final-documenttype = lc_crn."'CRN'.
              ELSE.
                ls_final-documenttype = lc_inv."'INV'.
              ENDIF.
            ENDIF.
            IF ls_final-transactiontype = lc_impg."'IMPG'..


            ENDIF.

            ls_final-customitem8 = ls_bseg-gsber.
            ls_final-custom5 = ls_bseg_lifnr-lifnr.

            IF ls_final-igstamount IS INITIAL AND ls_final-cgstamount IS INITIAL AND  ls_final-sgstamount IS INITIAL .
              IF ls_bseg-hkont = lc_igst1 OR ls_bseg-hkont = lc_igst2 OR ls_bseg-hkont = lc_igst3..
                ls_final-igstamount = ls_bseg-dmbtr.
                ls_final-itcigstamount = ls_bseg-dmbtr.
                ls_final-taxablevalue = ls_bseg-dmbtr.
              ENDIF.

              IF ls_bseg-hkont = lc_cgst1 OR ls_bseg-hkont = lc_cgst2 OR ls_bseg-hkont = lc_cgst3..
                ls_final-cgstamount = ls_bseg-dmbtr.
                ls_final-itccgstamount = ls_bseg-dmbtr.
                ls_final-taxablevalue = ls_bseg-dmbtr.
              ENDIF.
              IF ls_bseg-hkont = lc_sgst1 OR ls_bseg-hkont = lc_sgst2 OR ls_bseg-hkont = lc_sgst3..
                ls_final-sgstamount = ls_bseg-dmbtr.
                ls_final-itcsgstamount = ls_bseg-dmbtr.
                ls_final-taxablevalue = ls_bseg-dmbtr.
              ENDIF.
            ENDIF.

            IF ls_final-hsn IS INITIAL.
              READ TABLE lt_ekpo1 INTO DATA(ls_ekpo) WITH KEY ebeln = ls_rseg-ebeln  BINARY SEARCH.
              ls_final-hsn =  ls_ekpo-j_1bnbm.
            ENDIF.
            READ TABLE lt_vbrk INTO DATA(ls_vbrk) WITH KEY vbeln = ls_bkpf-awkey BINARY SEARCH.
            IF ls_vbrk-fkart = 'ZRE' OR ls_vbrk-fkart = 'ZL2'.
            ELSE.
              APPEND ls_final TO gt_final_inward.
              CLEAR: ls_final.
            ENDIF.
          ELSE.
            CLEAR:ls_bseg.
            EXIT.
          ENDIF.


          CLEAR : ls_final,ls_bset,ls_bseg_hsn,ls_bseg_matnr,ls_makt,"ls_bseg_menge,
          ls_bseg_lifnr,ls_lfa1,ls_adrc,ls_bseg,ls_j_1bbranch,ls_bupadrc,
          ls_bseg,ls_ekpo..
        ENDLOOP.
      ENDIF.
      CLEAR : ls_bkpf.
    ENDLOOP.

    DATA(gt_final_inward1) = gt_final_inward.

    REFRESH:gt_final_inward.

    SORT gt_final_inward1  BY documentnumber  billfromlegalname locationgstin  custom1 customitem1 rate uqc billfromlegalname hsn. "IGST_RATE CGST_RATE SGST_RATE.

    LOOP AT gt_final_inward1 INTO DATA(gl_final_inward1).
      ls_final-custom1 = gl_final_inward1-custom1.
      ls_final-customitem1 = gl_final_inward1-customitem1.
      ls_final-itemdescription = gl_final_inward1-itemdescription.
      ls_final-itemname = gl_final_inward1-itemname.
      ls_final-hsn = gl_final_inward1-hsn.
      ls_final-uqc = gl_final_inward1-uqc.
      ls_final-othercharges = gl_final_inward1-othercharges.
      ls_final-custom2 = gl_final_inward1-custom2.
      ls_final-custom3 = gl_final_inward1-custom3.
      ls_final-custom4 = gl_final_inward1-custom4.
      ls_final-custom5 = gl_final_inward1-custom5.
      ls_final-customitem2 = gl_final_inward1-customitem2.
      ls_final-customitem3 = gl_final_inward1-customitem3.
      ls_final-customitem4 = gl_final_inward1-customitem4.
      ls_final-customitem5 = gl_final_inward1-customitem5.
      ls_final-customitem6 = gl_final_inward1-customitem6.
      ls_final-customitem7 = gl_final_inward1-customitem7.
      ls_final-customitem8 = gl_final_inward1-customitem8.
      ls_final-customitem9 = gl_final_inward1-customitem9.
      ls_final-customitem10 = gl_final_inward1-customitem10.
      ls_final-custom6 = gl_final_inward1-custom6.
      ls_final-custom7 = gl_final_inward1-custom7.
      ls_final-custom8 = gl_final_inward1-custom8.
      ls_final-custom9 = gl_final_inward1-custom9.
      ls_final-documentnumber = gl_final_inward1-documentnumber.
      ls_final-documentdate = gl_final_inward1-documentdate.
      ls_final-billtolegalname = gl_final_inward1-billtolegalname.
      ls_final-purpose = gl_final_inward1-purpose.
      ls_final-supplytype = gl_final_inward1-supplytype.
      ls_final-returnperiod = gl_final_inward1-returnperiod.
      ls_final-documenttype = gl_final_inward1-documenttype.
      ls_final-serialnumber = gl_final_inward1-serialnumber.
      ls_final-transactiontype = gl_final_inward1-transactiontype.
      ls_final-pos = gl_final_inward1-pos.
      ls_final-billfromgstin = gl_final_inward1-billfromgstin.
      ls_final-billfromlegalname = gl_final_inward1-billfromlegalname.
      ls_final-billfromaddress1 = gl_final_inward1-billfromaddress1.
      ls_final-billfromaddress2  = gl_final_inward1-billfromaddress2.
      ls_final-billfromcity = gl_final_inward1-billfromcity.
      ls_final-billfromstatecode = gl_final_inward1-billfromstatecode.
      ls_final-billfrompincode = gl_final_inward1-billfrompincode.
      ls_final-locationgstin = gl_final_inward1-locationgstin.
      ls_final-documentvalue = gl_final_inward1-documentvalue.
      ls_final-billtogstin = gl_final_inward1-billtogstin.
      ls_final-billtolegalname = gl_final_inward1-billtolegalname.
      ls_final-billtoaddress1 = gl_final_inward1-billtoaddress1.
      ls_final-billtoaddress2 = gl_final_inward1-billtoaddress2.
      ls_final-billtostatecode  = gl_final_inward1-billtostatecode.
      ls_final-billtocity = gl_final_inward1-billtocity.
      ls_final-billtopincode = gl_final_inward1-billtopincode.
      ls_final-shiptogstin = gl_final_inward1-shiptogstin.
      ls_final-billtoemail = gl_final_inward1-billtoemail.
      ls_final-shiptoaddress1 = gl_final_inward1-shiptoaddress1.
      ls_final-shiptoaddress2 = gl_final_inward1-shiptoaddress2.
      ls_final-quantity = ls_final-quantity + gl_final_inward1-quantity.
      ls_final-taxablevalue = ls_final-taxablevalue + gl_final_inward1-taxablevalue.
      ls_final-discountamount =  gl_final_inward1-discountamount.
      ls_final-cgstamount = ls_final-cgstamount + gl_final_inward1-cgstamount.
      ls_final-igstamount = ls_final-igstamount + gl_final_inward1-igstamount.
      ls_final-sgstamount = ls_final-sgstamount + gl_final_inward1-sgstamount.
      ls_final-rate =   gl_final_inward1-rate.
      ls_final-branch = gl_final_inward1-branch.
      ls_final-itceligibility  = gl_final_inward1-itceligibility.

      IF ls_final-itceligibility IS INITIAL.
        IF ls_final-hsn(2) = '99'.
          ls_final-itceligibility = 'IS'.
        ELSE.
          ls_final-itceligibility = 'IP'.
        ENDIF.
      ENDIF.


      IF    ls_final-itceligibility = 'NO'.
        CLEAR:gl_final_inward1-itcigstamount.
        CLEAR:gl_final_inward1-itcsgstamount.
        CLEAR:gl_final_inward1-itccgstamount.
      ELSE.
      ENDIF.



      ls_final-itccgstamount = ls_final-itccgstamount + gl_final_inward1-itccgstamount.
      ls_final-itcsgstamount = ls_final-itcsgstamount + gl_final_inward1-itcsgstamount.
      ls_final-itcigstamount = ls_final-itcigstamount + gl_final_inward1-itcigstamount.
      ls_final-cessamount = ls_final-cessamount + gl_final_inward1-cessamount.
      ls_final-itccessamount = ls_final-itccessamount + gl_final_inward1-itccessamount.


      ls_final-reversecharge = gl_final_inward1-reversecharge.
      ls_final-company_code = gl_final_inward1-company_code.




      AT END OF hsn.
        IF ls_final-reversecharge = 'Y' OR ls_final-documenttype = 'CRN'.
          IF ls_final-taxablevalue < 0.
            ls_final-taxablevalue = ls_final-taxablevalue * -1.
          ENDIF.
          IF ls_final-igstamount < 0.
            ls_final-igstamount = ls_final-igstamount * -1.
            ls_final-itcigstamount = ls_final-itcigstamount * -1.
          ENDIF.
          IF ls_final-cgstamount < 0.
            ls_final-cgstamount = ls_final-cgstamount * -1.
            ls_final-itccgstamount = ls_final-itccgstamount * -1.
          ENDIF.
          IF ls_final-sgstamount < 0.
            ls_final-sgstamount = ls_final-sgstamount * -1.
            ls_final-itcsgstamount = ls_final-itcsgstamount * -1.
          ENDIF..
          IF ls_final-rate < 0.
            ls_final-rate = ls_final-rate * -1.
          ENDIF..
        ENDIF.


        APPEND ls_final TO gt_final_inward.
        CLEAR ls_final.
      ENDAT.
      CLEAR: gl_final_inward1.
    ENDLOOP.

*-----------------------Internal Tables-----------------------------------------------------------------------------------------------------*
    DATA: lt_vbrk_zl2   TYPE STANDARD TABLE OF ty_vbrk,
          lt_konv       TYPE STANDARD TABLE OF ty_konv,
          lt_konv_zcrm  TYPE STANDARD TABLE OF ty_konv,
          lt_adrc_plant TYPE STANDARD TABLE OF ty_adrc.

*---------------------Internal Structures---------------------------------------------------------------------------------------------------*
    DATA: ls_vbrp       TYPE ty_vbrp,
          ls_vbpa       TYPE ty_vbpa,
          ls_konv       TYPE ty_konv,
          ls_konv_zcrm  TYPE ty_konv,
          ls_adrc1      TYPE ty_adrc,
          ls_adrc_plant TYPE ty_adrc.

    DATA: lv_year  TYPE char4,   "Added by Karthi 20.03.2024
          lv_month TYPE char2,   "Added by Karthi 20.03.2024
          lv_day   TYPE char2.   "Added by Karthi 20.03.2024


*&---------------------------------------------------------------------*
*&    FETCH DATA FROM DB
*&---------------------------------------------------------------------*

*---------SD DATA----------------------------------------------------------------------------------------------------------------------------------------*

    IF lt_vbrk_tmp  IS NOT INITIAL.
      SELECT       vbrk~vbeln,
                   vbrk~knumv,
                   vbrk~fkart,
                   vbrk~erzet,
                   vbrk~fktyp,
                   vbrk~vbtyp,
                   vbrk~waerk,
                   vbrk~vkorg,
                   vbrk~fkdat,
                   vbrk~kurrf,
                   vbrk~bukrs,
                   vbrk~gjahr,
                   vbrk~regio,
                   vbrk~netwr,
                   vbrk~kunrg,
                   vbrk~kunag,
                   vbrk~sfakn,
                   vbrk~xblnr,
                   vbrk~zuonr,
                   vbrk~mwsbk,
                   vbrk~fksto,
                   vbrk~bupla,
                   vbrk~rfbsk,
                   vbrk~ernam,
                   vbrp~posnr,
                   vbrp~werks,
                   vbrp~aubel,
                   vbrp~matnr,
                   vbrp~fkimg,
                   vbrk~erdat,
                   vbrp~vrkme,
                   vbrp~arktx,
                   vbrp~netwr AS netwrp,
                   vbrp~vgbel,
                   vbrp~vgpos,
                   vbrp~kzwi2
                   AS netwrline
                   INTO   TABLE   @DATA(lt_vbrk1)
                   FROM vbrk AS vbrk INNER JOIN vbrp AS vbrp
                   ON vbrk~vbeln = vbrp~vbeln
                     FOR ALL ENTRIES IN @lt_vbrk_tmp
                            WHERE vbrk~vbeln = @lt_vbrk_tmp-vbeln
                            AND vbrk~fkart IN ('ZRE','ZL2')
                                      AND vbrk~fksto = ''.
    ENDIF.

    IF lt_vbrk1[] IS NOT INITIAL.
      DELETE lt_vbrk1 WHERE fkimg IS INITIAL.
      CLEAR: lt_vbrk_zl2[].

      lt_vbrk_zl2[] = lt_vbrk1[].
      DELETE lt_vbrk_zl2 WHERE fkart NE 'ZL2'.
      IF lt_vbrk_zl2[] IS NOT INITIAL.

        SELECT vbelv,
              posnv,
              vbeln,
              posnn,
              vbtyp_n,
              vbtyp_v
              FROM vbfa INTO TABLE @DATA(it_vbfa_zl2)
              FOR ALL ENTRIES IN @lt_vbrk_zl2
              WHERE vbeln =  @lt_vbrk_zl2-vgbel
                AND posnn =  @lt_vbrk_zl2-vgpos
                AND vbtyp_n = 'L'
                AND vbtyp_v = 'H'.

        IF it_vbfa_zl2[] IS NOT INITIAL.
          SELECT vbeln,
                  xblnr,
                  zuonr
             FROM vbak INTO TABLE @DATA(it_vbak)
            FOR ALL ENTRIES IN @it_vbfa_zl2
            WHERE vbeln = @it_vbfa_zl2-vbelv.

        ENDIF.

      ENDIF.
    ENDIF.

    IF lt_vbrk1[] IS NOT INITIAL.
      FIELD-SYMBOLS <ls_vbrk> TYPE ty_vbrk.
      SORT lt_vbrk1 BY xblnr.
      LOOP AT lt_vbrk1 ASSIGNING <ls_vbrk>.
        IF <ls_vbrk>-xblnr = ''.
          CONCATENATE 'PT' <ls_vbrk>-vbeln INTO <ls_vbrk>-xblnr.
          CONDENSE <ls_vbrk>-xblnr.
        ELSE.
          IF <ls_vbrk>-xblnr+0(1) = '0'.
            CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
              EXPORTING
                input  = <ls_vbrk>-xblnr
              IMPORTING
                output = <ls_vbrk>-xblnr.
          ENDIF.
        ENDIF.
      ENDLOOP.

*

      SELECT * FROM mara INTO  TABLE it_mara
        FOR ALL ENTRIES IN  lt_vbrk1
        WHERE matnr =  lt_vbrk1-matnr.

      SORT it_mara BY matnr.
      SELECT  werks,
              j_1bbranch,
              adrnr,
              regio,            "From Adress
              land1,
              stras,
              name2,
              pstlz,
              ort01 INTO TABLE @DATA(lt_t001w1) FROM t001w
                FOR ALL ENTRIES IN @lt_vbrk1
                     WHERE werks = @lt_vbrk1-werks.

*

      IF lt_t001w IS NOT INITIAL.
        SELECT  bukrs                          " From GSTIN. / Location GSTIN
                branch
                gstin  INTO TABLE lt_j_1bbranch
          FROM j_1bbranch
          FOR ALL ENTRIES IN lt_t001w
                       WHERE branch = lt_t001w-j_1bbranch ."AND bukrs IN s_bukrs.

        SELECT    "FETCH SOLD TO PARTY ADDRESS DETAILS
           addrnumber
            name1
            name2
            city1
            street
            str_suppl1
            str_suppl2
            str_suppl3
            location
            tel_number
            post_code1
            region

            FROM adrc INTO TABLE lt_adrc_plant
            FOR ALL ENTRIES IN lt_t001w1
             WHERE addrnumber = lt_t001w1-adrnr AND
                langu = 'E'.
      ENDIF.

      SELECT  kunnr
              land1                      " To Gstin
              adrnr
              telf1
              stcd3
              regio
              name1
              stras
              ort01
              pstlz
              INTO TABLE lt_kna1 FROM kna1
              FOR ALL ENTRIES IN lt_vbrk1
                   WHERE kunnr = lt_vbrk1-kunag.
      IF lt_kna1 IS NOT INITIAL.
        SELECT
           addrnumber,
            name1,
            name2,
            city1,
            street,
            str_suppl1,
            str_suppl2,
            str_suppl3,
            location,
            tel_number,
            post_code1,
            region
          INTO TABLE @DATA(lt_adrc1) FROM adrc
            FOR ALL ENTRIES IN @lt_kna1
                          WHERE addrnumber = @lt_kna1-adrnr.
      ENDIF.

      IF lt_t001w IS NOT INITIAL.

      ENDIF.


      SELECT  matnr werks
              steuc INTO TABLE lt_marc FROM marc
              FOR ALL ENTRIES IN lt_vbrk1
                        WHERE matnr = lt_vbrk1-matnr AND
                              werks = lt_vbrk1-werks.                                                                      " HSN

      SELECT
        bname,
        addrnumber,
        persnumber
          INTO TABLE @DATA(lt_user21) FROM usr21
            FOR ALL ENTRIES IN @lt_vbrk1
                WHERE bname = @lt_vbrk1-ernam.

      IF lt_user21 IS NOT INITIAL.

        SELECT
          addrnumber
          persnumber
          smtp_addr
           INTO TABLE lt_adr6 FROM adr6
              FOR ALL ENTRIES IN lt_user21
                     WHERE   addrnumber = lt_user21-addrnumber
                          AND persnumber = lt_user21-persnumber."code for to mail address

      ENDIF.

      SELECT
        matnr
        maktx FROM makt INTO TABLE lt_makt
        FOR ALL ENTRIES IN lt_vbrk1
        WHERE matnr = lt_vbrk1-matnr.
      SORT lt_vbrk1 BY vbeln posnr knumv.

*      CLEAR: r_cond1[].
*      LOOP AT it_cond1 INTO is_cond1.
*        r_cond1-sign = 'I'.
*        r_cond1-option = 'EQ'.
*        r_cond1-low = is_cond1-kschl.
*        APPEND r_cond1.
*      ENDLOOP.

      SELECT knumv    "Tax Amoount Fetch
            kposn
            kschl
            kawrt
            kbetr
            kwert FROM konv INTO TABLE lt_konv
        FOR ALL ENTRIES IN lt_vbrk1
        WHERE knumv = lt_vbrk1-knumv
              AND kposn = lt_vbrk1-posnr
*              AND kschl IN r_cond1
              AND kinak = ''
        AND kwert NE 0.
      DATA(it_cond1) = it_cond.
      REFRESH it_cond1.
      LOOP AT it_cond1 INTO DATA(is_cond1).
        ls_konv-gsp_tax = is_cond1-gsp_tax.
        MODIFY lt_konv FROM ls_konv TRANSPORTING gsp_tax WHERE kschl = is_cond1-kschl.
        CLEAR: is_cond1.
      ENDLOOP.

      lt_konv_zcrm[] = lt_konv[].
**-------Sorting------------------------------------------------------------------------------------------------------------------------------------*

      SORT lt_vbrk1                BY vbeln knumv fkart.
      SORT lt_vbrp                BY vbeln posnr.
      SORT lt_bseg                BY belnr bukrs txgrp.
      SORT lt_bset                BY belnr bukrs.
      SORT lt_kna1                BY kunnr.
      SORT lt_t001w               BY werks regio.
      SORT lt_adrc1                BY addrnumber.
      SORT lt_adr6                BY addrnumber.
      SORT lt_marc                BY matnr.
      SORT lt_j_1bbranch          BY bukrs branch.
      SORT lt_konv                BY knumv kposn gsp_tax.
      SORT lt_konv_zcrm           BY knumv kposn gsp_tax DESCENDING.
      SORT lt_makt BY matnr.
      SORT it_mara BY matnr.
      SORT lt_adrc_plant BY addrnumber.
      CLEAR:ls_final.
      CLEAR:ls_j_1bbranch.
      IF lt_marc IS NOT INITIAL.
        DELETE lt_marc WHERE steuc IS INITIAL.
      ENDIF.
**------Process and fill Final Internal Table(Main Loop)-------------------------------------------------------------------------------------------------------------------*
      CLEAR: ls_vbrk.
      DATA:lv_serial_no TYPE i.

      LOOP AT lt_vbrk1 INTO DATA(ls_vbrk1).
        ls_final-supplytype = 'P'.

        ls_final-customitem10 = ls_vbrk1-fkdat.

        IF ls_vbrk1-fkart = 'ZRE' OR ls_vbrk1-fkart = 'ZL2'.
          READ TABLE lt_bkpf INTO ls_bkpf_final WITH KEY awkey+0(10) =  ls_vbrk1-vbeln.
          IF sy-subrc = 0.
            ls_final-customitem1 = ls_bkpf_final-belnr.
            ls_final-custom1 = ls_bkpf_final-belnr.
          ENDIF.
        ELSE.
          ls_final-customitem1 = ls_vbrk1-vbeln.
          ls_final-custom1 = ls_vbrk1-vbeln.
        ENDIF.

        lv_serial_no = lv_serial_no + 1.
        AT NEW vbeln.
          CLEAR: lv_serial_no.
          lv_serial_no = 1.
        ENDAT.

        DATA: l_var_condtype TYPE i.
        l_var_condtype = 0.
        CLEAR: ls_konv_zcrm.
        READ TABLE lt_konv_zcrm INTO ls_konv_zcrm WITH KEY knumv = ls_vbrk1-knumv  kposn = ls_vbrk1-posnr.
        IF sy-subrc = 0.
          l_var_tabix = sy-tabix.
          LOOP AT lt_konv_zcrm INTO ls_konv_zcrm FROM l_var_tabix WHERE knumv = ls_vbrk1-knumv AND kposn = ls_vbrk1-posnr.
            IF ls_konv_zcrm-knumv = ls_vbrk1-knumv AND ls_konv_zcrm-kposn = ls_vbrk1-posnr.
            ELSE.
              CLEAR:ls_konv_zcrm.
              EXIT.
            ENDIF.

*
            READ TABLE it_cond1 INTO is_cond1 WITH KEY kschl  = ls_konv_zcrm-kschl..
            IF l_var_condtype = 0.
              CASE is_cond1-gsp_tax.

                WHEN 'CGST'.
                  ls_final-taxablevalue = ls_konv_zcrm-kawrt.
                  ls_final-cgstamount = ls_final-cgstamount + ls_konv_zcrm-kwert.
                  ls_final-rate = ls_final-rate + ( ls_konv_zcrm-kbetr ).

                WHEN 'SGST'.

                  ls_final-taxablevalue = ls_konv_zcrm-kawrt.
                  ls_final-sgstamount = ls_final-sgstamount + ls_konv_zcrm-kwert.
                  ls_final-rate = ls_final-rate + ( ls_konv_zcrm-kbetr  ).

                WHEN  'IGST'.

                  ls_final-taxablevalue = ls_konv_zcrm-kawrt.
                  ls_final-igstamount = ls_final-igstamount + ls_konv_zcrm-kwert.
                  ls_final-rate = ls_final-rate + ( ls_konv_zcrm-kbetr ).
                WHEN 'ZAMP'.
                  ls_final-taxablevalue = ls_konv_zcrm-kwert.

                WHEN 'JOSS'.
                  ls_final-cessamount = ls_final-cessamount + ls_konv_zcrm-kwert.
              ENDCASE.
            ENDIF.
            CLEAR ls_konv_zcrm.
          ENDLOOP.
          CLEAR: l_var_tabix.
        ENDIF.
        ls_final-itccgstamount =  ls_final-cgstamount.
        ls_final-itcsgstamount =  ls_final-sgstamount.
        ls_final-itcigstamount =  ls_final-igstamount.
*
*-------------------------------------------From Data--------------------------------------------------------------------------------------------------------*
        CLEAR: ls_t001w.
        READ TABLE lt_t001w1 INTO ls_t001w WITH KEY werks = ls_vbrk1-werks  BINARY SEARCH.
        IF sy-subrc = 0.
          CLEAR: ls_j_1bbranch.
          READ TABLE lt_j_1bbranch INTO ls_j_1bbranch WITH KEY bukrs = ls_vbrk1-bukrs
                                                                     branch = ls_t001w-j_1bbranch BINARY SEARCH. " Location GSTIN
          IF sy-subrc = 0.
            ls_final-billtogstin                      = ls_j_1bbranch-gstin.
            ls_final-locationgstin                       = ls_j_1bbranch-gstin.
          ENDIF.
          CLEAR: ls_adrc_plant.
          READ TABLE lt_adrc_plant INTO ls_adrc_plant WITH KEY addrnumber = ls_t001w-adrnr BINARY SEARCH.
          IF sy-subrc = 0.

            ls_final-billtocity = ls_adrc_plant-city1.
            ls_final-billtopincode = ls_adrc_plant-postcode1.
          ENDIF.

          ls_final-billtostatecode =  ls_t001w-regio.
          ls_final-pos =  ls_t001w-regio.
        ENDIF.

        CLEAR:  ls_vbpa.

        CLEAR: ls_kna1.
        READ TABLE lt_kna1 INTO ls_kna1 WITH KEY kunnr = ls_vbrk1-kunag BINARY SEARCH.

        IF sy-subrc = 0.
          CLEAR: ls_adrc1.
          READ TABLE lt_adrc1 INTO ls_adrc1 WITH  KEY  addrnumber   = ls_kna1-adrnr BINARY SEARCH.


          ls_final-billfromgstin = ls_kna1-stcd3.
          ls_final-billfromlegalname = ls_kna1-name1.
          ls_final-billfrompincode = ls_kna1-pstlz.

          REPLACE ALL OCCURRENCES OF ',' IN ls_final-billtoaddress1  WITH space.

          IF ls_final-billfromaddress1 IS INITIAL.
            ls_final-billfromaddress1 = ls_adrc1-name1.
          ENDIF.
          REPLACE ALL OCCURRENCES OF ',' IN ls_final-billtoaddress1  WITH space.

          ls_final-billfromstatecode = ls_kna1-regio.
        ENDIF.

        IF ls_final-billfromgstin IS INITIAL OR ls_final-billfromgstin = 'NA' OR ls_final-billfromgstin = 'UNREGISTERED'.
          ls_final-transactiontype = lc_b2c ."'B2C'.
        ELSE.
          ls_final-transactiontype = lc_b2b."'B2B'.
        ENDIF.


        IF ls_final-transactiontype = 'B2C'.

        ENDIF.
        IF ls_final-taxablevalue IS INITIAL.

          IF ls_vbrk1-fkart = 'ZRE' OR ls_vbrk1-fkart = 'ZL2'.
            ls_final-taxablevalue = ls_vbrk1-netwrline.
            ls_final-grossamount  =  ls_final-taxablevalue.
            ls_final-documentvalue   = ls_vbrk1-netwr + ls_vbrk1-mwsbk.
            ls_final-rate  = ( ls_final-itcigstamount / ls_final-taxablevalue ) * 100.
            IF ls_final-rate IS INITIAL.
              ls_final-rate  = ( ( ls_final-itccgstamount + ls_final-itcsgstamount ) / ls_final-taxablevalue ) * 100.
            ENDIF.
            ls_final-rate = round( val = ls_final-rate dec = 0 ).
            CONDENSE ls_final-rate.

          ELSE.
            ls_final-taxablevalue = ls_vbrk1-netwrline.
            ls_final-grossamount  =  ls_final-taxablevalue.
            ls_final-documentvalue   = ls_vbrk1-netwr + ls_vbrk1-mwsbk.
          ENDIF.
        ELSE.
          ls_final-grossamount  =  ls_final-taxablevalue.
        ENDIF.

        READ TABLE lt_user21 INTO DATA(ls_user21) WITH KEY bname = ls_vbrk1-ernam.
        IF sy-subrc = 0.
          READ TABLE lt_adr6 INTO ls_adr6 WITH KEY persnumber = ls_user21-persnumber
                                                  addrnumber = ls_user21-addrnumber.
        ENDIF.
        READ TABLE lt_marc INTO ls_marc WITH KEY matnr = ls_vbrk1-matnr BINARY SEARCH.
        IF sy-subrc = 0.
          ls_final-hsn                       = ls_marc-steuc.         "HSN
          CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
            EXPORTING
              input  = ls_final-hsn
            IMPORTING
              output = ls_final-hsn.

        ENDIF.
        IF ls_final-hsn+0(2) = '99'.
          ls_final-isservice = 'Y'.
        ELSE.
          ls_final-isservice = 'N'.
        ENDIF.
        ls_final-quantity          = ls_vbrk1-fkimg.             "QTY
        ls_final-itemname            = ls_vbrk1-matnr.
        READ TABLE lt_makt INTO ls_makt WITH KEY matnr = ls_vbrk1-matnr BINARY SEARCH.
        IF sy-subrc = 0.
          ls_final-itemdescription = ls_makt-maktx.""product.desc
        ENDIF.

        ls_final-uqc            = 'OTH'.     "UOM  "VRKME

        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-itemname WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-itemdescription WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billfromlegalname WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtolegalname WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-shiptolegalname WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtocity WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-shiptocity WITH space.

        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtoaddress1 WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-shiptoaddress1 WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-billtoaddress2 WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-shiptoaddress2 WITH space.
        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN ls_final-hsn WITH space.
        CONDENSE ls_final-hsn NO-GAPS.
        ls_final-reversecharge                = 'N'.


        IF ls_final-taxablevalue = ''." ls_final-billtogstin = 'UNREGISTERED' OR ls_final-billtogstin = '' .
          CLEAR: ls_final.
          CONTINUE.
        ENDIF.

        IF ls_vbrk1-fkart = 'ZRE'.
          lv_day = ls_vbrk1-zuonr+0(2).
          lv_month = ls_vbrk1-zuonr+3(2).
          lv_year = ls_vbrk1-zuonr+6(4).
          CONCATENATE lv_year lv_month lv_day INTO ls_final-documentdate.

          ls_final-documentnumber             = ls_vbrk1-xblnr.

        ELSEIF ls_vbrk1-fkart = 'ZL2'.
          READ TABLE it_vbfa_zl2 INTO DATA(wa_vbfa_zl2) WITH KEY vbeln = ls_vbrk1-vgbel
                                                            posnn = ls_vbrk1-vgpos.
          IF sy-subrc = 0.
            READ TABLE it_vbak INTO DATA(wa_vbak) WITH KEY  vbeln = wa_vbfa_zl2-vbelv.
            IF sy-subrc = 0.

              lv_day = wa_vbak-zuonr+0(2).
              lv_month = wa_vbak-zuonr+3(2).
              lv_year = wa_vbak-zuonr+6(4).
              CONCATENATE lv_year lv_month lv_day INTO ls_final-documentdate.
              ls_final-documentnumber             = wa_vbak-xblnr.
            ENDIF.
          ENDIF.
        ELSE.
          ls_final-documentdate               = ls_vbrk1-fkdat.
          ls_final-documentnumber             = ls_vbrk1-xblnr.
        ENDIF.

        ls_final-custom1                     = ls_vbrk1-vbeln.
        ls_final-custom4                     = ls_vbrk1-gjahr.
        ls_final-custom9                    = ls_vbrk1-bukrs.
        ls_final-custom2                     = ls_vbrk1-kunag.
        ls_final-custom3                     = ls_t001w-werks.
        ls_final-custom5                     = ls_vbrk1-kunag.
        ls_final-customitem3                  = ls_vbrk1-fkart.
        ls_final-claimrefund                = 'N'.
        ls_final-underigstact              = 'N'.
        ls_final-refundeligibility          = 'N'.
        ls_final-autopushorgenerate          = 'OGST'.
        ls_final-priceperquantity          =  ls_final-taxablevalue / ls_final-quantity.
        ls_final-itemtotal                  =  ls_final-taxablevalue + ls_final-igstamount
                                      + ls_final-cgstamount + ls_final-sgstamount + ls_final-othercharges ."+ ls_FINAL-DOUCUMENTOTHERCHARGES.
        CONCATENATE ls_vbrk1-fkdat+4(2) ls_vbrk1-fkdat+0(4) INTO ls_final-returnperiod.

        ls_final-purpose = 'OGST'.
        ls_final-serialnumber = lv_serial_no.




        IF ls_vbrk1-vbtyp = 'O' .
          ls_final-documenttype = lc_inv ."'INV'.

        ELSEIF ls_vbrk1-vbtyp = 'M'.
          ls_final-documenttype = lc_inv."'INV'.
        ELSEIF  ls_vbrk1-vbtyp = 'P'.
          ls_final-documenttype = lc_dbn."'DBN'.
        ENDIF.


        ls_final-branch = ls_vbrk1-bukrs.

        IF ls_vbrk1-bukrs <> '1000'.
          IF ls_vbrk1-vbeln IS NOT INITIAL.
            ls_final-customitem1 = ls_vbrk1-vbeln.
            ls_final-customitem2 = ls_vbrk1-bukrs.

          ENDIF.
        ENDIF.

        CONCATENATE ls_final-documentdate+6(2) '-' ls_final-documentdate+4(2) '-' ls_final-documentdate+0(4)     INTO ls_final-documentdate.
        IF ls_final-locationgstin = ls_final-billtogstin.

        ENDIF.
        ls_final-customitem6 = ls_vbrk1-bupla.
        IF ls_final-rate IS INITIAL.
          ls_final-rate = 0.
          CONDENSE ls_final-rate.
        ENDIF.


        CONCATENATE ls_final-documentdate+6(2) '-'
                  ls_final-documentdate+4(2) '-'
                  ls_final-documentdate(4)
      INTO ls_final-customitem2.


        READ TABLE it_mara INTO is_mara WITH KEY matnr = ls_vbrk1-matnr BINARY SEARCH.
        IF sy-subrc = 0.
          ls_final-customitem4 = is_mara-matkl.

        ENDIF.
        IF ls_final-hsn(2) = '99'.
          ls_final-itceligibility = 'IS'.
        ELSE.
          ls_final-itceligibility = 'IP'.
        ENDIF.
        IF ls_vbrk1-fkart = 'ZRE' OR ls_vbrk1-fkart = 'ZL2'.   "Added by Karthi 20.03.2024

        ELSE.
          CLEAR: ls_final-rate.
        ENDIF.
        APPEND ls_final TO gt_final_inward.

        CLEAR: ls_vbrk1.


        CLEAR: ls_final,
               ls_vbrk1 ,
               ls_vbrp ,
               ls_vbpa ,
               ls_bseg ,
               ls_bset ,
               ls_kna1 ,
               ls_t001w,
               ls_adrc ,
               ls_adr6 ,
               ls_marc ,

               ls_j_1bbranch.
        CLEAR: ls_konv.
      ENDLOOP.
    ENDIF.

    et_gtr2_data = gt_final_inward.
  ENDMETHOD.


  METHOD get_partb_eway_json.

    TYPES: BEGIN OF tycriteria,
             locationgstin  TYPE string,
             locationname   TYPE string,
             documentnumber TYPE string,
             documentdate   TYPE string,  " DD-MM-YYYY
             supplytype     TYPE string,  " enum
             documenttype   TYPE string,  " enum
             billfromgstin  TYPE string,
             portcode       TYPE string,  " nullable
           END OF tycriteria.

    TYPES: tytcriteria TYPE STANDARD TABLE OF tycriteria WITH DEFAULT KEY.

    TYPES: BEGIN OF ty_transport_request,
             transportmode           TYPE string,  " enum transportMode
             vehiclenumber           TYPE string,
             fromstate               TYPE i,
             fromcity                TYPE string,
             reason                  TYPE string,  " enum reason
             transportdocumentnumber TYPE string,
             transportdocumentdate   TYPE string,  " DD-MM-YYYY
             remarks                 TYPE string,
             vehicletype             TYPE string,  " enum vehicleType
             criterias               TYPE tytcriteria,
           END OF ty_transport_request.

    DATA : ls_request TYPE ty_transport_request.

    SELECT document_number,
    document_date,
    billfromgstin,
    dispatchfromgstin,
    dispatchfromname,
    dispatchfromadd1,
    dispatchfromadd2,
    dispatchfrompincode,
    dispatchfromcity,
    shiptogstin,
    shiptoadd1,
    shiptoadd2,
    shiptopincode,
    shiptocity,
    referencedocumentnumber,
    documenttype,
    einv_module
    FROM zsd_t_einv_log INTO TABLE @DATA(lt_einv_log) WHERE ewaybill_number EQ ' '
    AND document_number EQ @iv_document_number
*    AND einv_module EQ 'SD'
    AND eway_cancel NE 'X'.

    LOOP AT lt_einv_log INTO DATA(ls_ewb_stat).

***** TO BE FETCH FROM EINVOICE TABLE *****
      ls_request-transportmode = is_vehicle_details-transport_mode.
      IF NOT ls_request-transportmode EQ 'ROAD'.
        ls_request-transportdocumentnumber = is_vehicle_details-transporter_doc_num.
        ls_request-transportdocumentdate = is_vehicle_details-transportdocdate.
      ELSE.
        ls_request-vehiclenumber = is_vehicle_details-vehicle_number.
        ls_request-vehicletype = is_vehicle_details-vehicle_type.
      ENDIF.

      ls_request-fromcity = ls_ewb_stat-dispatchfromcity.
      IF strlen( ls_ewb_stat-dispatchfromgstin ) > 2.
        ls_request-fromstate = ls_ewb_stat-dispatchfromgstin+0(2).
      ENDIF.

      IF ls_ewb_stat-document_date IS NOT INITIAL.
        ls_request-criterias = VALUE #( ( locationGstin = ls_ewb_stat-billfromgstin
                                        documentNumber = COND #( WHEN ls_ewb_stat-einv_module EQ 'SD'
                                                                 THEN |{ ls_ewb_stat-document_number ALPHA = OUT }|
                                                                 WHEN ls_ewb_stat-einv_module EQ 'FI'
                                                                 THEN |{ ls_ewb_stat-referencedocumentnumber ALPHA = OUT }| )
                 documentDate =  |{ ls_ewb_stat-document_date+6(2) }-{ ls_ewb_stat-document_date+4(2) }-{ ls_ewb_stat-document_date+0(4) }|
                 supplyType = 'S'
                 documentType = ls_ewb_stat-documenttype
                 billFromGstin = ls_ewb_stat-billfromgstin ) ).
      ELSE.
        ls_request-criterias = VALUE #( ( locationGstin = ls_ewb_stat-billfromgstin
                                          documentNumber = COND #( WHEN ls_ewb_stat-einv_module EQ 'SD'
                                                                   THEN |{ ls_ewb_stat-document_number ALPHA = OUT }|
                                                                   WHEN ls_ewb_stat-einv_module EQ 'FI'
                                                                   THEN |{ ls_ewb_stat-referencedocumentnumber ALPHA = OUT }| )
                                          supplyType = 'S'
                                          documentType = ls_ewb_stat-documenttype
                                          billFromGstin = ls_ewb_stat-billfromgstin ) ).
      ENDIF.


      rv_request_json = /ui2/cl_json=>serialize( data = ls_request ).

    ENDLOOP.
  ENDMETHOD.


  METHOD if_rap_query_provider~select.

    DATA:
      lt_data     TYPE STANDARD TABLE OF zmm_c_challan,
      lv_top      TYPE i,
      lv_skip     TYPE i,
      lv_max_rows TYPE i.
    DATA : lt_sort_criteria TYPE abap_sortorder_tab.

    DATA(lv_request) = io_request->is_data_requested( ).
    DATA(lv_entity) = io_request->get_entity_id( ).

    DATA(lt_sort)   = io_request->get_sort_elements( ).
    IF lt_sort IS NOT INITIAL.
      lt_sort_criteria = VALUE #( FOR ls_sort IN lt_sort
                                ( name = ls_sort-element_name
                                  descending = ls_sort-descending ) ).
    ENDIF.

    " Pagination handling
    DATA(lo_paging) = io_request->get_paging( ).
    lv_top  = io_request->get_paging( )->get_page_size( ).
    lv_skip = io_request->get_paging( )->get_offset( ).

    IF lv_top IS INITIAL.
      lv_max_rows = 1000.   " safety limit
    ELSE.
      lv_max_rows = lv_top.
    ENDIF.

    " -----------------------------
    " 🔹 Mandatory RAP calls
    " -----------------------------
    TRY.
        DATA(lt_filter) = io_request->get_filter( )->get_as_ranges( ).
      CATCH cx_rap_query_filter_no_range.
        CLEAR lt_filter.
    ENDTRY.

    DATA(lt_fields) = io_request->get_requested_elements( ).


    DATA : lr_date   TYPE RANGE OF mkpf-bldat,
           lr_doc    TYPE RANGE OF mkpf-mblnr,
           lr_doc_s  TYPE RANGE OF zsd_c_einv-Documentnumber,
           lr_doc_f  TYPE RANGE OF zsd_c_einv-Documentnumber,
           lr_doc_B  TYPE RANGE OF zsd_c_einv-Custom1,
           lr_date_s TYPE RANGE OF zsd_c_einv-documentdate,
           lr_bukrs  TYPE RANGE OF zsd_c_einv-CompanyCode,
*           lr_gjahr TYPE RANGE OF zsd_c_einv-Fiscalyear,
           lr_year   TYPE RANGE OF mkpf-mjahr.


    lr_doc    = CORRESPONDING #( VALUE #( lt_filter[ name = 'DOCUMENTNUMBER' ]-range OPTIONAL ) ).
    lr_doc_s    = CORRESPONDING #( VALUE #( lt_filter[ name = 'DOCUMENTNUMBER' ]-range OPTIONAL ) ).
    lr_doc_f    = CORRESPONDING #( VALUE #( lt_filter[ name = 'DOCUMENTNUMBER' ]-range OPTIONAL ) ).
    lr_doc_b   = CORRESPONDING #( VALUE #( lt_filter[ name = 'CUSTOM1' ]-range OPTIONAL ) ).
    lr_date   = CORRESPONDING #( VALUE #( lt_filter[ name = 'DOCUMENTDATE' ]-range OPTIONAL ) ).
    lr_date_s   = CORRESPONDING #( VALUE #( lt_filter[ name = 'DOCUMENTDATE' ]-range OPTIONAL ) ).
    lr_year   = CORRESPONDING #( VALUE #( lt_filter[ name = 'FISCALYEAR' ]-range OPTIONAL ) ).

    lr_bukrs   = CORRESPONDING #( VALUE #( lt_filter[ name = 'COMPANYCODE' ]-range OPTIONAL ) ).
*    lr_gjahr   = CORRESPONDING #( VALUE #( lt_filter[ name = 'FISCALYEAR' ]-range OPTIONAL ) ).


    TRY.

        IF lv_entity = 'ZMM_C_CHALLAN'.
          get_data_for_challan( EXPORTING iv_billing_document = lr_doc
                                          iv_gjahr = lr_year
*                                          iv_bukrs = CONV #( lv_bukrs )
                                          iv_document_date = lr_date
*                                          iv_gjahr = CONV #( lv_gjahr )
*                                          iv_bukrs = CONV #( lv_bukrs )
*                                          iv_document_date = lt_document_dates
                                IMPORTING et_display_table = DATA(lt_display) ).

          DELETE lt_display WHERE documentdate IS INITIAL.

          SORT lt_display BY (lt_sort_criteria).

          DATA: lt_global_page LIKE lt_display.

          IF lv_top > 0.
*            LOOP AT lt_display INTO DATA(ls_result)
*            FROM lv_skip + 1 TO lv_skip + lv_top.
*              APPEND ls_result TO lt_global_page.
*            ENDLOOP.

            lt_global_page   = VALUE #( FOR ls_result   IN lt_display
                                       FROM lv_skip + 1 TO lv_skip + lv_top
                                       ( ls_result ) ).

          ELSE.
            lt_global_page = lt_display.
          ENDIF.

          io_response->set_data( lt_global_page ).
          io_response->set_total_number_of_records( lines( lt_display ) ).

        ELSEIF lv_entity = 'ZSD_C_EINV'.

          IF lr_date_s IS INITIAL AND lr_doc_s IS INITIAL AND lr_doc_b IS INITIAL.
            lr_date_s = VALUE #( ( sign = 'I' option = 'BT'
                                   low = |{ sy-datum+0(4) }{ sy-datum+4(2) }{ '01' }|
*                            low =  sy-datum - 10
                                    high = sy-datum ) ).
          ENDIF.

          get_data_for_sd( EXPORTING iv_billingdocument = lr_doc_s
                                     iv_billingreference = lr_doc_b
                                     iv_billingdocumentdate = lr_date_s
                          IMPORTING et_final = DATA(tt_final) ).

          cyg_conversion(
          EXPORTING iv_purpose = ''
                    iv_button = 'GO'
          IMPORTING et_final = DATA(et_final)
          CHANGING ct_final = tt_final ).

          generate_json_structure( EXPORTING it_final = et_final
                                    IMPORTING lt_display = DATA(lt_display1)  ).

          SORT lt_display1 BY (lt_sort_criteria).

          DATA: lt_global_page1 LIKE lt_display1.
          IF lv_top > 0.

            lt_global_page1   = VALUE #( FOR ls_data   IN lt_display1
                              FROM lv_skip + 1 TO lv_skip + lv_top
                              ( ls_data ) ).

*            LOOP AT lt_display1 INTO DATA(ls_result1)
*            FROM lv_skip + 1 TO lv_skip + lv_top.
*              APPEND ls_result1 TO lt_global_page1.
*            ENDLOOP.

          ELSE.
            lt_global_page1 = lt_display1.
          ENDIF.

          io_response->set_data( lt_global_page1 ).
          io_response->set_total_number_of_records( lines( lt_display1 ) ).

        ELSEIF lv_entity = 'ZFI_C_BILLING_DOCUMENT'.

*          IF lr_date_s IS INITIAL AND lr_doc_s IS INITIAL AND lr_doc_b IS INITIAL.
*            lr_date_s = VALUE #( ( sign = 'I' option = 'BT'
*                                   low = |{ sy-datum+0(4) }{ sy-datum+4(2) }{ '01' }|
**                            low =  sy-datum - 10
*                                    high = sy-datum ) ).
*          ENDIF.




          get_data_for_fi( EXPORTING ir_doc  = lr_doc_b
                                     ir_xblnr = lr_doc_f
                                     ir_bukrs = lr_bukrs
                                     ir_doc_date = lr_date_s
                                     ir_gjahr = lr_year
                                     iv_purpose = 'EINV|OGST'
                          IMPORTING lt_display = DATA(tt_final1) ).

          SORT tt_final1 BY (lt_sort_criteria).


          DATA: lt_global_page2 LIKE tt_final1.
          IF lv_top > 0.

            lt_global_page2   = VALUE #( FOR ls_data1   IN tt_final1
                              FROM lv_skip + 1 TO lv_skip + lv_top
                              ( ls_data1 ) ).

          ELSE.
            lt_global_page2 = tt_final1.
          ENDIF.

          io_response->set_data( lt_global_page2 ).
          io_response->set_total_number_of_records( lines( tt_final1 ) ).

        ENDIF.

*        ENDLOOP.
      CATCH cx_sy_itab_line_not_found.
*        get_data_for_challan( EXPORTING iv_gjahr = CONV #( iv_gjahr )
*                                        iv_bukrs = CONV #( lv_bukrs )
*                                        iv_document_date = lt_document_dates
*                              IMPORTING et_display_table = lt_display ).
    ENDTRY.
*      lt_billing_documents = VALUE #( BASE lt_billing_documents FOR ls_range IN  ls_filter-range ( low = ls_range-low ) ).
*


  ENDMETHOD.


  METHOD get_data_for_sd_mass.

    DATA: lt_log           TYPE STANDARD TABLE OF zsd_c_einv_main,
          lt_vbrk          TYPE STANDARD TABLE OF ty_vbrk,
          lv_msg1_can(250) TYPE c, "To be removed
          lv_msg1(250)     TYPE c, "to be removed
          lv_day           TYPE int4,
          lt_vbfa1         TYPE tt_vbfa1,
          lt_vbrp          TYPE TABLE OF ty_vbrp,
          lt_marm          TYPE TABLE OF ty_marm,
          lt_vbfa          TYPE TABLE OF ty_vbfa,
          lt_vbak          TYPE TABLE OF ty_vbak,
          lt_vbpa          TYPE TABLE OF ty_vbpa,
          lt_kna1          TYPE TABLE OF ty_kna1,
          lt_konv          TYPE TABLE OF ty_konv,
          ls_konv          TYPE ty_konv,
          ls_konv_gl       TYPE ty_konv,
          lt_konv_gl       TYPE TABLE OF ty_konv,
          lt_marc          TYPE TABLE OF ty_marc,
          lt_makt          TYPE TABLE OF ty_makt,
          lt_adrc          TYPE TABLE OF ty_adrc,
          lt_adrc1         TYPE TABLE OF ty_adrc,
          lt_j_1bbranch    TYPE tt_j_1bbranch,
          lt_vbrk_cancel   TYPE STANDARD TABLE OF ty_vbrk,
          ls_vbrk_cancel   TYPE ty_vbrk,
          lv_index         TYPE sy-tabix.

    CONSTANTS: lc_gl  TYPE char10 VALUE 'ZEI_GL'.
    CONSTANTS: lc_b2b TYPE char10 VALUE  'ZB2B_VTWEG'.
    CONSTANTS: lc_fkart TYPE char15  VALUE 'ZFKART_GST5'.
    CONSTANTS: lc_exp TYPE char10  VALUE 'ZEXP_VTWEG'.
    CONSTANTS: lc_pac  TYPE char10 VALUE 'ZPAC_VTWEG'.

    REFRESH: lt_vbrk.", lt_vbrp.

**** FETCHING BILL TYPE
    SELECT * FROM zsd_c_billtype
              WHERE 1 = 1 INTO TABLE @DATA(lt_zeinv_billtype). "#EC CI_NOWHERE

**** FETCHING CONDITION TYPE
    SELECT * FROM zsd_c_condition_type                  "#EC CI_NOWHERE
               WHERE 1 = 1 INTO TABLE @DATA(lt_cond).

*** GL DATA
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
             INTO TABLE @DATA(lt_zei_gl)
             WHERE name = @lc_gl.
*** DIST CHANNEL
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
           INTO TABLE @DATA(lt_vtweg)
           WHERE name = @lc_b2b.
**BILLING TYPE
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
         INTO TABLE @DATA(lt_fkart)
         WHERE name = @lc_fkart.
**** EXPORT
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
        INTO TABLE @DATA(lt_exp)
        WHERE name = @lc_exp.
**** PAC -> UOM
    SELECT sign, opti, low, high FROM zsd_c_tvarvc
      INTO TABLE @DATA(lt_pac)
      WHERE name = @lc_pac.

    IF lt_zeinv_billtype IS NOT INITIAL.

      SELECT a~vbeln,a~fkart,a~vtweg,a~erdat,a~vbtyp,a~fkdat,a~belnr,a~waerk,a~kunag,a~knumv,a~mwsbk,a~netwr,a~bupla,a~bukrs,a~xblnr,a~gjahr,a~kurrf,a~land1,a~sfakn,a~ernam
         FROM zsd_c_vbrk AS a
        INNER JOIN @lt_zeinv_billtype AS b
        ON a~fkart = b~fkart
        WHERE vbeln IN @it_billing_document
        AND  vtweg IN @lt_vtweg
        AND  rfbsk EQ 'C'
        INTO CORRESPONDING FIELDS OF TABLE @lt_vbrk.

    ENDIF.

    LOOP AT lt_vbrk ASSIGNING FIELD-SYMBOL(<fs_vbrk>).
      <fs_vbrk>-awkey = <fs_vbrk>-vbeln.
    ENDLOOP.

    IF lt_vbrk IS NOT INITIAL.
      SELECT a~bukrs, a~belnr, a~gjahr, a~awkey, a~xblnr_alt
        FROM  zsd_c_bkpf AS a
        INNER JOIN @lt_vbrk AS b
        ON a~bukrs = b~bukrs
        AND a~awkey = b~awkey
        AND a~gjahr = b~gjahr
        INTO TABLE @DATA(lt_bkpf_sd).

      IF lt_bkpf_sd IS NOT INITIAL.
        SELECT a~bukrs, a~belnr, a~gjahr, a~segment
          FROM zsd_c_bseg AS a
          INNER JOIN @lt_bkpf_sd AS b
          ON a~bukrs = b~bukrs
          AND a~belnr = b~belnr
          AND a~gjahr = b~gjahr
          INTO TABLE @DATA(lt_bseg_sd).

        DELETE lt_bseg_sd WHERE segment IS INITIAL.
      ENDIF.

      SELECT a~name, a~gstin, a~adrnr, a~branch, a~bukrs
        FROM zsd_c_j_1bbranch AS a
        INNER JOIN @lt_vbrk AS b
        ON a~branch = b~bupla
        AND a~bukrs = b~bukrs
        INTO CORRESPONDING FIELDS OF TABLE @lt_j_1bbranch.

      IF lt_j_1bbranch IS NOT INITIAL.

        SELECT a~addrnumber,a~name1,a~name2,a~name3,a~name4,a~city1,a~PostCode1,a~street,a~strsuppl1,a~strsuppl3,a~country,a~region
          FROM zsd_c_adrc AS a
          INNER JOIN @lt_j_1bbranch AS b
          ON a~addrnumber = b~adrnr
          INTO CORRESPONDING FIELDS OF TABLE @lt_adrc1.
      ENDIF.

      SELECT * FROM zsd_c_einv_uom WHERE 1 = 1
            INTO TABLE @DATA(lt_uom).                   "#EC CI_NOWHERE

      SELECT a~vbeln, a~vbelv, a~vbtyp_n, a~vbtyp_v
        FROM zsd_c_vbfa AS a
        INNER JOIN @lt_vbrk AS b
        ON a~vbeln = b~vbeln
        WHERE ( a~vbtyp_n = 'M' OR a~vbtyp_n = 'N'  ) AND a~vbtyp_v = 'J'
        INTO CORRESPONDING FIELDS OF TABLE @lt_vbfa1.

      SELECT a~vbeln, a~vbelv, a~vbtyp_n, a~vbtyp_v
        FROM zsd_c_vbfa AS a
        INNER JOIN @lt_vbrk AS b
        ON a~vbeln = b~sfakn
        WHERE ( a~vbtyp_n = 'M' OR a~vbtyp_n = 'N'  ) AND a~vbtyp_v = 'J'
        APPENDING TABLE @lt_vbfa1.

      IF lt_vbfa1 IS NOT INITIAL.
        SELECT a~vbeln, a~vbelv, a~vbtyp_n, a~vbtyp_v
          FROM zsd_c_vbfa AS a
          INNER JOIN @lt_vbfa1 AS b
          ON a~vbeln = b~vbelv
          INTO TABLE @DATA(lt_vbfa2).

        SELECT a~vbeln, a~vstel,a~lgnum
          FROM zsd_c_likp AS a
          INNER JOIN @lt_vbfa1 AS b
          ON a~vbeln = b~vbelv
          INTO TABLE @DATA(lt_likp).
      ENDIF.

      IF lt_likp IS NOT INITIAL.
        SELECT a~warehouse, a~shippingpoint, a~address1,a~address2,a~place, a~pincode
          FROM zsd_c_supply_detail AS a
          INNER JOIN @lt_likp AS b
          ON a~warehouse = b~lgnum
          AND a~shippingpoint = b~vstel
          INTO TABLE @DATA(lt_sadr).
      ENDIF.

      SELECT a~vbeln,a~posnr,a~meins,a~vgbel,a~aubel,a~matnr,a~fkimg,a~vrkme,a~netwr,a~charg,a~werks,a~arktx,a~aupos,a~mwsbp,a~kzwi5,a~umvkn,a~umvkz,a~kzwi2,a~lgort,a~vstel,a~gsber,a~prctr
         FROM zsd_c_vbrp AS a
         INNER JOIN @lt_vbrk AS b
         ON a~vbeln = b~vbeln
         WHERE a~fkimg > 0
         INTO CORRESPONDING FIELDS OF TABLE @lt_vbrp.

      IF lt_vbrp IS NOT INITIAL.

*        SELECT a~FiscalYear, a~AccountingDocument, a~GLAccount, a~salesdocument, a~salesdocumentitem FROM I_GLAccountLineItemRawData AS a
*          INNER JOIN @lt_vbrk AS b
*          ON a~fiscalyear = b~gjahr AND a~accountingdocument = b~belnr INTO TABLE @DATA(lt_glaccount_lineItem).
*
*        IF lt_glaccount_lineItem IS NOT INITIAL.
*          SELECT a~FiscalYear, a~AccountingDocument, a~GLAccount, a~salesdocument, a~salesdocumentitem FROM @lt_glaccount_lineitem AS a
*            INNER JOIN @lt_vbrp AS b
*            ON a~salesdocument = b~aubel AND a~salesdocumentitem = b~aupos INTO TABLE @DATA(lt_gl_account).
*        ENDIF.

        SELECT a~matnr,a~meinh,a~umren
          FROM zsd_c_marm AS a
          INNER JOIN @lt_vbrp AS b
          ON a~matnr = b~matnr
          INTO CORRESPONDING FIELDS OF TABLE @lt_marm.

        SELECT  a~vbelv,a~vbeln,a~vbtyp_n
          FROM zsd_c_vbfa AS a
          INNER JOIN @lt_vbrp AS b
          ON a~vbelv = b~vgbel
          WHERE a~vbtyp_n = '8'
          INTO CORRESPONDING FIELDS OF TABLE @lt_vbfa.
      ENDIF.

      SELECT a~vbeln, a~bstnk,a~bstdk
         FROM zsd_c_vbak AS a
        INNER JOIN @lt_vbrk AS b
        ON a~vbeln = b~vbeln
        INTO CORRESPONDING FIELDS OF TABLE @lt_vbak.

      SELECT a~vbeln,a~posnr,a~parvw,a~kunnr,a~adrnr
              FROM zsd_c_vbpa AS a
             INNER JOIN @lt_vbrk AS b
             ON a~vbeln = b~vbeln
        INTO CORRESPONDING FIELDS OF TABLE @lt_vbpa.

      IF lt_vbpa IS NOT INITIAL.
        SELECT a~kunnr,a~land1,a~name1,a~name2,a~adrnr,a~stcd3,a~regio,a~stras,a~ort01,a~pstlz
          FROM zsd_c_kna1 AS a
          INNER JOIN @lt_vbpa AS b
          ON a~kunnr = b~kunnr
          INTO CORRESPONDING FIELDS OF TABLE @lt_kna1.
      ENDIF.

      IF lt_vbrk IS NOT INITIAL.

        SELECT  a~knumv,a~kposn,a~kschl,a~kawrt,a~kbetr,a~kwert,a~sakn1,a~kvsl1
          FROM zsd_c_prcd_elements AS a
          INNER JOIN @lt_vbrk AS b
          ON a~knumv = b~knumv
          INTO CORRESPONDING FIELDS OF TABLE @lt_konv.
      ENDIF.
*require for GL fetch
      SORT lt_zei_gl BY low.
      LOOP AT lt_konv INTO ls_konv WHERE sakn1 NE ''.
        IF line_exists( lt_zei_gl[ low = ls_konv-kvsl1 ] ).
          MOVE-CORRESPONDING ls_konv TO ls_konv_gl.
          APPEND ls_konv_gl TO lt_konv_gl.
        ENDIF.
        CLEAR: ls_konv,ls_konv_gl.
      ENDLOOP.


      IF lt_vbrp IS NOT INITIAL.
        SELECT a~matnr,a~werks,a~steuc
          FROM zsd_c_marc AS a
          INNER JOIN @lt_vbrp AS b
          ON a~matnr = b~matnr
          AND a~werks = b~werks
          INTO CORRESPONDING FIELDS OF TABLE @lt_marc.

        SELECT a~matnr,
               a~maktx
               FROM zsd_c_makt AS a
               INNER JOIN @lt_vbrp AS b
               ON a~matnr = b~matnr
               WHERE a~spras = 'E'
               INTO CORRESPONDING FIELDS OF TABLE @lt_makt.
      ENDIF.

      IF lt_vbpa IS NOT INITIAL.
        SELECT a~addrnumber,a~name1,a~name2,a~name3,a~name4,a~city1,a~postcode1,a~street,a~strsuppl1,a~strsuppl3,a~country,a~region
          FROM zsd_c_adrc AS a
          INNER JOIN @lt_vbpa AS b
          ON a~addrnumber = b~adrnr
          INTO CORRESPONDING FIELDS OF TABLE @lt_adrc.

        IF lt_kna1 IS NOT INITIAL.

          SELECT a~addrnumber,a~name1,a~name2,a~name3,a~name4,a~city1,a~postcode1,a~street,a~strsuppl1,a~strsuppl3,a~country,a~region
            FROM zsd_c_adrc AS a
            INNER JOIN @lt_kna1 AS b
            ON a~addrnumber = b~adrnr
            APPENDING CORRESPONDING FIELDS OF TABLE @lt_adrc.
        ENDIF.
      ENDIF.

      set_data_for_sd( EXPORTING it_fkart          = lt_fkart
                                 it_pac            = lt_pac
                                 it_cond           = lt_cond
                       IMPORTING et_final          = et_final
                       CHANGING  ct_exp            = lt_exp
                                 ct_vtweg          = lt_vtweg
                                 ct_vbrk           = lt_vbrk
                                 ct_vbrp           = lt_vbrp
                                 ct_konv           = lt_konv
                                 ct_marm           = lt_marm
                                 ct_uom            = lt_uom
                                 ct_konv_gl        = lt_konv_gl
                                 ct_likp           = lt_likp
                                 ct_vbfa1          = lt_vbfa1
                                 ct_sadr           = lt_sadr
                                 ct_adrc1          = lt_adrc1
                                 ct_j_1bbranch     = lt_j_1bbranch
                                 ct_vbpa           = lt_vbpa
                                 ct_kna1           = lt_kna1
                                 ct_adrc           = lt_adrc
                                 ct_makt           = lt_makt
                                 ct_marc           = lt_marc
                                 ct_bkpf_sd        = lt_bkpf_sd
                                 ct_bseg_sd        = lt_bseg_sd
*                               ct_cyg_prc        = lt_c
                                 ct_zeinv_billtype = lt_zeinv_billtype   ).

      cyg_conversion( EXPORTING iv_purpose = 'EINV'
                      IMPORTING et_final = et_global
                      CHANGING  ct_final = et_final ).



    ENDIF.
  ENDMETHOD.


  METHOD upload_excel.

    TYPES: BEGIN OF ty_upload,
             locationgstin                  TYPE string,
             locationname                   TYPE string,
             returnperiod                   TYPE string,
             liabilitydischargereturnperiod TYPE string,
             itcclaimreturnperiod           TYPE string,
             purpose                        TYPE string,
             autopushorgenerate             TYPE string,
             supplytype                     TYPE string,
             irn                            TYPE string,
             ewaybillnumber                 TYPE string,
             generateddate                  TYPE string,
             delivereddate                  TYPE string,
             validupto                      TYPE string,
             extendedtimes                  TYPE string,
             documenttype                   TYPE string,
             transactiontype                TYPE string,
             taxpayertype                   TYPE string,
             transactionnature              TYPE string,
             transactiontypedescription     TYPE string,
             documentnumber                 TYPE string,
             documentseriescode             TYPE string,
             documentdate                   TYPE string,
             billfromgstin                  TYPE string,
             billfromlegalname              TYPE string,
             billfromtradename              TYPE string,
             billfromvendorcode             TYPE string,
             billfromaddress1               TYPE string,
             billfromaddress2               TYPE string,
             billfromcity                   TYPE string,
             billfromstatecode              TYPE string,
             billfrompincode                TYPE string,
             billfromphone                  TYPE string,
             billfromemail                  TYPE string,
             dispatchfromgstin              TYPE string,
             dispatchfromtradename          TYPE string,
             dispatchfromvendorcode         TYPE string,
             dispatchfromaddress1           TYPE string,
             dispatchfromaddress2           TYPE string,
             dispatchfromcity               TYPE string,
             dispatchfromstatecode          TYPE string,
             dispatchfrompincode            TYPE string,
             billtogstin                    TYPE string,
             billtolegalname                TYPE string,
             billtotradename                TYPE string,
             billtovendorcode               TYPE string,
             billtoaddress1                 TYPE string,
             billtoaddress2                 TYPE string,
             billtocity                     TYPE string,
             billtostatecode                TYPE string,
             billtopincode                  TYPE string,
             billtophone                    TYPE string,
             billtoemail                    TYPE string,
             shiptogstin                    TYPE string,
             shiptolegalname                TYPE string,
             shiptotradename                TYPE string,
             shiptovendorcode               TYPE string,
             shiptoaddress1                 TYPE string,
             shiptoaddress2                 TYPE string,
             shiptocity                     TYPE string,
             shiptostatecode                TYPE string,
             shiptopincode                  TYPE string,
             paymenttype                    TYPE string,
             paymentmode                    TYPE string,
             paymentamount                  TYPE string,
             advancepaidamount              TYPE string,
             paymentdate                    TYPE string,
             paymentremarks                 TYPE string,
             paymentterms                   TYPE string,
             paymentinstruction             TYPE string,
             payeename                      TYPE string,
             payeeaccountnumber             TYPE string,
             paymentamountdue               TYPE string,
             ifsc                           TYPE string,
             credittransfer                 TYPE string,
             directdebit                    TYPE string,
             creditdays                     TYPE string,
             creditavaileddate              TYPE string,
             creditreversaldate             TYPE string,
             refdocumentremarks             TYPE string,
             refdocumentperiodstartdate     TYPE string,
             refdocumentperiodenddate       TYPE string,
             refprecedingdocumentdetails    TYPE string,
             refcontractdetails             TYPE string,
             addisupportingdocudetails      TYPE string,
             billnumber                     TYPE string,
             billdate                       TYPE string,
             portcode                       TYPE string,
             documentcurrencycode           TYPE string,
             destinationcountry             TYPE string,
             exportduty                     TYPE string,
             pos                            TYPE string,
             documentvalue                  TYPE string,
             documentdiscount               TYPE string,
             documentothercharges           TYPE string,
             documentvalueinforeigncurrency TYPE string,
             roundoffamount                 TYPE string,
             differentialpercentage         TYPE string,
             reversecharge                  TYPE string,
             claimrefund                    TYPE string,
             underigstact                   TYPE string,
             refundeligibility              TYPE string,
             ecommercegstin                 TYPE string,
             tdsgstin                       TYPE string,
             pnroruniquenumber              TYPE string,
             availprovisionalitc            TYPE string,
             originalgstin                  TYPE string,
             originalstatecode              TYPE string,
             originaltradename              TYPE string,
             originaldocumenttype           TYPE string,
             originaldocumentnumber         TYPE string,
             originaldocumentdate           TYPE string,
             originalreturnperiod           TYPE string,
             originaltaxablevalue           TYPE string,
             originalportcode               TYPE string,
             transportdatetime              TYPE string,
             transporterid                  TYPE string,
             transportername                TYPE string,
             transportmode                  TYPE string,
             distance                       TYPE string,
             transportdocumentnumber        TYPE string,
             transportdocumentdate          TYPE string,
             vehiclenumber                  TYPE string,
             vehicletype                    TYPE string,
             toemailaddresses               TYPE string,
             tomobilenumbers                TYPE string,
             jworiginaldocumentnumber       TYPE string,
             jworiginaldocumentdate         TYPE string,
             jwdocumentnumber               TYPE string,
             jwdocumentdate                 TYPE string,
             custom1                        TYPE char10,
             custom2                        TYPE string,
             custom3                        TYPE string,
             custom4                        TYPE string,
             custom5                        TYPE string,
             custom6                        TYPE string,
             custom7                        TYPE string,
             custom8                        TYPE string,
             custom9                        TYPE string,
             custom10                       TYPE string,
             serialnumber                   TYPE string,
             isservice                      TYPE string,
             hsn                            TYPE string,
             productcode                    TYPE string,
             itemname                       TYPE string,
             itemdescription                TYPE string,
             natureofjwdone                 TYPE string,
             barcode                        TYPE string,
             uqc                            TYPE string,
             quantity                       TYPE string,
             freequantity                   TYPE string,
             lossunitofmeasure              TYPE string,
             losstotalquantity              TYPE string,
             rate                           TYPE string,
             cessrate                       TYPE string,
             statecessrate                  TYPE string,
             cessnonadvaloremrate           TYPE string,
             priceperquantity               TYPE string,
             discountamount                 TYPE string,
             grossamount                    TYPE string,
             othercharges                   TYPE string,
             taxablevalue                   TYPE string,
             pretaxvalue                    TYPE string,
             igstamount                     TYPE string,
             cgstamount                     TYPE string,
             sgstamount                     TYPE string,
             cessamount                     TYPE string,
             statecessamount                TYPE string,
             statecessnonadvaloremamount    TYPE string,
             cessnonadvaloremamount         TYPE string,
             orderlinereference             TYPE string,
             origincountry                  TYPE string,
             itemserialnumber               TYPE string,
             itemtotal                      TYPE string,
             itemattributedetails           TYPE string,
             taxtype                        TYPE string,
             batchnamenumber                TYPE string,
             batchexpirydate                TYPE string,
             warrantydate                   TYPE string,
             itceligibility                 TYPE string,
             itcigstamount                  TYPE string,
             itccgstamount                  TYPE string,
             itcsgstamount                  TYPE string,
             itccessamount                  TYPE string,
             customitem1                    TYPE string,
             customitem2                    TYPE string,
             customitem3                    TYPE string,
             customitem4                    TYPE string,
             customitem5                    TYPE string,
             customitem6                    TYPE string,
             customitem7                    TYPE string,
             customitem8                    TYPE string,
             customitem9                    TYPE string,
             customitem10                   TYPE string,
             errors                         TYPE string,
             payeemerchantcode              TYPE string,
             transactionid                  TYPE string,
             transactionreferenceid         TYPE string,
             transactionnote                TYPE string,
             paymentminimumamount           TYPE string,
             transactionreferenceurl        TYPE string,
             irngenerationdate              TYPE string,
             autodraftsource                TYPE string,
             isamendment                    TYPE string,
             isavailableingstr2b            TYPE string,
             itcavailability                TYPE string,
             itcunavailabilityreason        TYPE string,
             upiid                          TYPE string,
             isavailableingstr98a           TYPE string,
             cancelreason                   TYPE string,
             gstactorrulesection            TYPE string,
             referenceid                    TYPE string,
             paymentduedate                 TYPE string,
             tdsrate                        TYPE string,
             tdsamount                      TYPE string,

           END OF ty_upload.


  ENDMETHOD.


  METHOD get_data_for_gstr2_new.
**&---------------------------------------------------------------------*
**& Method: get_data_for_gstr2_new
**& Clean Core ATC-Compliant Version
**&---------------------------------------------------------------------*
**& CHANGES FROM ORIGINAL:
**& ---------------------------------------------------------------
**& [ATC-01] SELECT * → explicit field lists
**& [ATC-02] Old OpenSQL → new syntax with @ host-variable escaping
**& [ATC-03] REFRESH → CLEAR
**& [ATC-04] Header-line tables → explicit work areas
**& [ATC-05] COLLECT → manual READ TABLE / MODIFY aggregation
**& [ATC-06] MOVE-CORRESPONDING → CORRESPONDING #( ) operator
**& [ATC-07] FOR ALL ENTRIES guards (IS NOT INITIAL) added
**& [ATC-08] Hardcoded literals → constants
**& [ATC-09] Dead / commented-out code removed
**& [ATC-10] APPEND wa → INSERT wa INTO TABLE itab
**& [ATC-11] Removed double periods (..) and redundant CLEAR
**& [ATC-12] SELECT SINGLE * → SELECT SINGLE with field list
**& [ATC-13] Added ORDER BY PRIMARY KEY where beneficial
**& [ATC-14] Replaced offset/length where practical
**& [ATC-15] Removed implicit table header references
**&---------------------------------------------------------------------*

    TYPES: BEGIN OF ty_bkpf,
             bukrs       TYPE bkpf-bukrs,
             belnr       TYPE bkpf-belnr,
             gjahr       TYPE bkpf-gjahr,
             monat       TYPE bkpf-monat,
             waers       TYPE bkpf-waers,
             awkey       TYPE bkpf-awkey,
             xblnr       TYPE bkpf-xblnr,
             budat       TYPE bkpf-budat,
             bldat       TYPE bkpf-bldat,
             blart       TYPE bkpf-blart,
             kursf       TYPE bkpf-kursf,
             awtyp       TYPE bkpf-awtyp,
             xreversal   TYPE bkpf-xreversal,
             tcode       TYPE tcode,
             stblg       TYPE bkpf-stblg,
             bktxt       TYPE bkpf-bktxt,
             usnam       TYPE bkpf-usnam,
             stgrd       TYPE bkpf-stgrd,
             ppnam       TYPE bkpf-ppnam,
             del_flag(1),
           END OF ty_bkpf.

    TYPES: BEGIN OF ty_rbco_tax,
             belnr      TYPE rbco-belnr,
             gjahr      TYPE rbco-gjahr,
             buzei      TYPE rbco-buzei,
             wrbtr      TYPE rbco-wrbtr,
             sgstamount TYPE rbco-wrbtr,
             cgstamount TYPE rbco-wrbtr,
             igstamount TYPE rbco-wrbtr,
             gsber      TYPE rbco-gsber,
             saknr      TYPE rbco-saknr,
             sgtxt      TYPE rbco-sgtxt,
             tax        TYPE char5,
             rcm(1),
             elig(2),
             igst1      TYPE string,
             cgst1      TYPE string,
             sgst1      TYPE string,
             tax2       TYPE char1,
           END OF ty_rbco_tax.

    TYPES: BEGIN OF ty_rbkp,
             xblnr TYPE rbkp-xblnr,
             bukrs TYPE bukrs,
             belnr TYPE rbkp-belnr,
             gjahr TYPE rbkp-gjahr,
             budat TYPE rbkp-budat,
             bldat TYPE bldat,
             blart TYPE rbkp-blart,
             bupla TYPE rbkp-bupla,
             stblg TYPE rbkp-stblg,
             xrech TYPE rbkp-xrech,
             ivtyp TYPE rbkp-ivtyp,
             lifnr TYPE rbkp-lifnr,
             stjah TYPE rbkp-stjah,
           END OF ty_rbkp.


*    " ── Constants ──────────────────────────────────────────────────────
    CONSTANTS: lc_re      TYPE char2  VALUE 'RE',
               lc_rc      TYPE char2  VALUE 'RC',
               lc_kr      TYPE char2  VALUE 'KR',
               lc_kg      TYPE char2  VALUE 'KG',
               lc_ks      TYPE char2  VALUE 'KS',
               lc_sa      TYPE char2  VALUE 'SA',
               lc_zg      TYPE char2  VALUE 'ZG',
               lc_zm      TYPE char2  VALUE 'ZM',
               lc_ab      TYPE char2  VALUE 'AB',
               lc_ra      TYPE char2  VALUE 'RA',
               lc_sign    TYPE char1  VALUE 'I',
               lc_opt     TYPE char2  VALUE 'EQ',
               lc_del_n   TYPE char1  VALUE 'N',
               lc_wit     TYPE char3  VALUE 'WIT',
               lc_koart_k TYPE char1 VALUE 'K',
               lc_koart_d TYPE char1 VALUE 'D',
               lc_buzid_t TYPE char1 VALUE 'T',
               lc_bewtp_q TYPE char1 VALUE 'Q',
               lc_supply  TYPE char1 VALUE 'P',
               lc_spras   TYPE spras VALUE 'E',
               lc_igst    TYPE char4 VALUE 'igstamount',
               lc_cgst    TYPE char4 VALUE 'cgstamount',
               lc_sgst    TYPE char4 VALUE 'sgstamount',
               lc_jimd    TYPE char4 VALUE 'JIMD',
               lc_rigst   TYPE char5 VALUE 'RIGST',
               lc_rcgst   TYPE char5 VALUE 'RCGST',
               lc_rsgst   TYPE char5 VALUE 'RSGST',
               lc_zitr    TYPE char4 VALUE 'ZITR',
               lc_tax_i   TYPE char1 VALUE 'I',
               lc_tax_c   TYPE char1 VALUE 'C',
               lc_tax_s   TYPE char1 VALUE 'S',
               lc_zo      TYPE char2 VALUE 'ZO',
               lc_zlspr_y TYPE char1 VALUE 'Y',
               lc_tvarvc  TYPE char7 VALUE 'ZTAX_EX',
               lc_seven   TYPE i     VALUE 7,
               lc_ten     TYPE i     VALUE 10.

    " ── Local data ─────────────────────────────────────────────────────
    DATA: lr_mwskz   TYPE RANGE OF mwskz,
          lr_cond    TYPE RANGE OF kschl,
          ls_cond    TYPE RANGE OF kschl,
          l_tab_bkpf TYPE STANDARD TABLE OF ty_bkpf.
    DATA : gt_final_inward TYPE STANDARD TABLE OF zsd_s_global_st.
    " ─────────────────────────────────────────────────────────────────
    " SECTION 1: Setup & Configuration
    " ─────────────────────────────────────────────────────────────────

    " [ATC-01] SELECT * → explicit field list
    " [ATC-02] New OpenSQL with @ escaping
    SELECT name, low
      FROM zsd_c_tvarvc
      INTO TABLE @DATA(lt_mwskz)
      WHERE name = @lc_tvarvc.

    IF sy-subrc = 0.
      " [ATC-04] No header-line – using VALUE with explicit structure
      lr_mwskz = VALUE #( BASE lr_mwskz
        FOR ls_mwskz IN lt_mwskz
        ( sign   = lc_sign
          option = lc_opt
          low    = ls_mwskz-low ) ).
    ENDIF.

    " ITC GL Mapping – purchase supply type
    " [ATC-02] New OpenSQL
    SELECT bukrs, ztax, hkont, supply, rcm, elig
      FROM zfi_t_gl_map
      INTO TABLE @DATA(it_gl)
  WHERE supply = @lc_supply. "#EC CI_NOFIRST  "#EC CI_NO_TRANSFORM  "#EC CI_NOFIELD
    SORT it_gl BY hkont ztax.

    " ─────────────────────────────────────────────────────────────────
    " SECTION 2: Open-Item Document Collection (bsis)
    " ─────────────────────────────────────────────────────────────────


    IF iv_date IS INITIAL.
      " [ATC-07] Guard for FOR ALL ENTRIES
      IF it_gl IS NOT INITIAL.
        SELECT bukrs, belnr, gjahr
          INTO TABLE @DATA(l_tab_doc)
          FROM bsis
          FOR ALL ENTRIES IN @it_gl
          WHERE budat   IN @iv_date
            AND ( hkont  = @it_gl-hkont
               OR blart IN ( @lc_re, @lc_kr, @lc_kg, @lc_ks ) )
            AND mwskz NOT IN @lr_mwskz.            "#EC CI_NO_TRANSFORM
      ENDIF.
    ENDIF.

    " ─────────────────────────────────────────────────────────────────
    " GL Account Description (skat)
    " ─────────────────────────────────────────────────────────────────

    " [ATC-12] SELECT SINGLE * → explicit field list
    DATA : s_bukrs TYPE RANGE OF bukrs.
    SELECT SINGLE bukrs, ktopl
      FROM t001
      INTO @DATA(ls_t001)
      WHERE bukrs IN @s_bukrs.

    IF ls_t001 IS NOT INITIAL.
      SELECT ktopl, saknr, spras, txt20, txt50
        FROM skat
        INTO TABLE @DATA(lt_skat)
        WHERE ktopl = @ls_t001-ktopl
          AND spras = @lc_spras
        ORDER BY PRIMARY KEY.                           "#EC CI_GENBUFF
    ENDIF.

    " ─────────────────────────────────────────────────────────────────
    " SECTION 2b: Cleared-Item Document Collection (bsas)
    " ─────────────────────────────────────────────────────────────────
    DATA: s_date1 TYPE RANGE OF budat,
          s_date  TYPE RANGE OF budat,
          s_bldat TYPE RANGE OF budat,
          s_bupla TYPE RANGE OF bupla,
          s_belnr TYPE RANGE OF belnr_d,
          s_werks TYPE RANGE OF werks_d,
          s_kostl TYPE RANGE OF kostl,
          s_prctr TYPE RANGE OF prctr,
          s_xblnr TYPE RANGE OF xbelnr,
          s_gjahr TYPE RANGE OF gjahr,
          s_blart TYPE RANGE OF blart.

    IF s_date1 IS INITIAL.
      IF it_gl IS NOT INITIAL.
        " [ATC-02] Fully escaped new syntax; APPENDING TABLE with @
        SELECT bukrs, belnr, gjahr
          APPENDING TABLE @l_tab_doc
          FROM bsas
          FOR ALL ENTRIES IN @it_gl
          WHERE budat  IN @s_date
            AND bldat  IN @s_bldat
            AND bukrs  IN @s_bukrs
            AND bupla  IN @s_bupla
            AND belnr  IN @s_belnr
            AND werks  IN @s_werks
            AND kostl  IN @s_kostl
            AND prctr  IN @s_prctr
            AND xblnr  IN @s_xblnr
            AND blart  IN @s_blart
            AND ( hkont = @it_gl-hkont
               OR blart IN ( @lc_re, @lc_kr, @lc_kg, @lc_ks ) )
            AND gjahr  IN @s_gjahr
            AND mwskz NOT IN @lr_mwskz.            "#EC CI_NO_TRANSFORM
      ENDIF.
    ENDIF.

    " ─────────────────────────────────────────────────────────────────
    " SECTION 3: Document Header Enrichment (bkpf / zsd_c_bkpf)
    " ─────────────────────────────────────────────────────────────────

    IF l_tab_doc IS NOT INITIAL OR s_date1 IS NOT INITIAL.

      IF s_date1 IS INITIAL AND l_tab_doc IS NOT INITIAL.

        SELECT bukrs, belnr, gjahr, monat, waers, awkey,
               xblnr, budat, bldat, blart, kursf,
               awtyp, xreversal, tcode, stblg, bktxt,
               usnam, stgrd, ppnam
          FROM bkpf
          INTO TABLE @l_tab_bkpf
          FOR ALL ENTRIES IN @l_tab_doc
          WHERE budat  IN @s_date
            AND bldat  IN @s_bldat
            AND cpudt  IN @s_date1
            AND belnr   = @l_tab_doc-belnr
            AND gjahr   = @l_tab_doc-gjahr
            AND bukrs   = @l_tab_doc-bukrs
            AND bukrs  IN @s_bukrs
            AND belnr  IN @s_belnr
            AND xblnr  IN @s_xblnr
            AND blart  IN @s_blart
            AND gjahr  IN @s_gjahr.                "#EC CI_NO_TRANSFORM

        " [ATC-03] REFRESH → CLEAR
        CLEAR l_tab_doc.

      ELSE.
        " Archive / CPU-date path
        SELECT bukrs, belnr, gjahr, monat, waers, awkey,
               xblnr, budat, bldat, blart, kursf,
               awtyp, xreversal, tcode, stblg, bktxt,
               usnam, stgrd, ppnam
          FROM zsd_c_bkpf
          INTO TABLE @l_tab_bkpf
          WHERE budat  IN @s_date
            AND bldat  IN @s_bldat
            AND cpudt  IN @s_date1
            AND bukrs  IN @s_bukrs
            AND belnr  IN @s_belnr
            AND xblnr  IN @s_xblnr
            AND blart  IN @s_blart
            AND gjahr  IN @s_gjahr.                "#EC CI_NO_TRANSFORM

        IF l_tab_bkpf IS NOT INITIAL.
          SORT l_tab_bkpf BY bukrs belnr gjahr.

          " Cross-check open items
          SELECT bukrs, belnr, gjahr, blart, hkont
            INTO TABLE @DATA(l_tab_doc_b)
            FROM bsis
            FOR ALL ENTRIES IN @l_tab_bkpf
            WHERE bukrs = @l_tab_bkpf-bukrs
              AND belnr = @l_tab_bkpf-belnr
              AND gjahr = @l_tab_bkpf-gjahr.

          " Cross-check cleared items
          SELECT bukrs, belnr, gjahr, blart, hkont
            APPENDING TABLE @l_tab_doc_b
            FROM bsas
            FOR ALL ENTRIES IN @l_tab_bkpf
            WHERE bukrs = @l_tab_bkpf-bukrs
              AND belnr = @l_tab_bkpf-belnr
              AND gjahr = @l_tab_bkpf-gjahr.

          " Mark documents that have relevant GL or doc-type lines
          LOOP AT l_tab_doc_b INTO DATA(ls_doc_b).
            READ TABLE it_gl INTO DATA(ls_gl_b)
              WITH KEY hkont = ls_doc_b-hkont BINARY SEARCH.

            IF sy-subrc <> 0.
              IF ls_doc_b-blart = lc_re OR ls_doc_b-blart = lc_kr
             OR ls_doc_b-blart = lc_kg OR ls_doc_b-blart = lc_ks.
                READ TABLE  l_tab_bkpf ASSIGNING FIELD-SYMBOL(<ls_del1>)
                  WITH KEY bukrs = ls_doc_b-bukrs
                           belnr = ls_doc_b-belnr
                           gjahr = ls_doc_b-gjahr BINARY SEARCH.
                IF sy-subrc = 0.
                  <ls_del1>-del_flag = lc_del_n.
                ENDIF.
              ENDIF.
            ELSE.
              READ TABLE l_tab_bkpf ASSIGNING FIELD-SYMBOL(<ls_del2>)
                WITH KEY bukrs = ls_doc_b-bukrs
                         belnr = ls_doc_b-belnr
                         gjahr = ls_doc_b-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
                <ls_del2>-del_flag = lc_del_n.
              ENDIF.
            ENDIF.
          ENDLOOP.
        ENDIF.

        " Remove documents not flagged as relevant
        DELETE l_tab_bkpf WHERE del_flag = space.

      ENDIF.

      " ─────────────────────────────────────────────────────────────
      " Import document handling (SA / ZG types)
      " ─────────────────────────────────────────────────────────────

      DATA(l_bkpf_imp) = l_tab_bkpf.
      DELETE l_bkpf_imp WHERE NOT ( blart = lc_sa OR blart = lc_zg ).
      DELETE l_bkpf_imp WHERE stblg IS NOT INITIAL.

      DELETE l_tab_bkpf WHERE blart = lc_zm.
      DELETE l_tab_bkpf WHERE blart = lc_ab.

      " ── User name resolution ────────────────────────────────────
      IF l_tab_bkpf IS NOT INITIAL.

        DATA(lt_bkpf_unam) = l_tab_bkpf.
        SORT lt_bkpf_unam BY usnam.
        DELETE ADJACENT DUPLICATES FROM lt_bkpf_unam COMPARING usnam.

        " [ATC-07] Guard added
        IF lt_bkpf_unam IS NOT INITIAL.
          SELECT bname, name_first, name_last
            FROM user_addr
            INTO TABLE @DATA(lt_user_usnam)
            FOR ALL ENTRIES IN @lt_bkpf_unam
            WHERE bname = @lt_bkpf_unam-usnam.
        ENDIF.

        " [ATC-03] CLEAR instead of REFRESH
        CLEAR lt_bkpf_unam.
        lt_bkpf_unam = l_tab_bkpf.
        SORT lt_bkpf_unam BY ppnam.
        DELETE ADJACENT DUPLICATES FROM lt_bkpf_unam COMPARING ppnam.

        IF lt_bkpf_unam IS NOT INITIAL.
          SELECT bname, name_first, name_last
            FROM user_addr
            INTO TABLE @DATA(lt_user_ppnam)
            FOR ALL ENTRIES IN @lt_bkpf_unam
            WHERE bname = @lt_bkpf_unam-ppnam.
        ENDIF.

        CLEAR lt_bkpf_unam.
      ENDIF.

      " ── Import matching (truncate xblnr to 7 chars) ─────────────
      DELETE l_bkpf_imp WHERE xblnr IS INITIAL.

      IF l_bkpf_imp IS NOT INITIAL.
        LOOP AT l_bkpf_imp ASSIGNING FIELD-SYMBOL(<fs_imp>).
          <fs_imp>-xblnr = <fs_imp>-xblnr(lc_seven).
        ENDLOOP.

        " Fetch related RE/RA/KR documents by matching xblnr
        SELECT bukrs, belnr, gjahr, monat, waers, awkey,
               xblnr, budat, bldat, blart, kursf,
               awtyp, xreversal, tcode, stblg, bktxt,
               usnam, stgrd, ppnam
          FROM bkpf
          APPENDING TABLE @l_tab_bkpf
          FOR ALL ENTRIES IN @l_bkpf_imp
          WHERE bukrs  = @l_bkpf_imp-bukrs
            AND xblnr  = @l_bkpf_imp-xblnr
AND blart IN ( @lc_re, @lc_ra, @lc_kr ).           "#EC CI_NO_TRANSFORM
      ENDIF.

      SORT l_tab_bkpf BY belnr gjahr bukrs.
      DELETE ADJACENT DUPLICATES FROM l_tab_bkpf COMPARING belnr gjahr bukrs.

      " Prepare bktxt lookup copy
      DATA(l_tab_bkpf_bktxt) = l_tab_bkpf.
      LOOP AT l_tab_bkpf_bktxt ASSIGNING FIELD-SYMBOL(<fs_xblnr>)
        WHERE blart = lc_sa.
        <fs_xblnr>-xblnr = <fs_xblnr>-xblnr(lc_seven).
      ENDLOOP.

      " ── Reversal document fetch ─────────────────────────────────
      IF l_tab_bkpf IS NOT INITIAL.
        SELECT bukrs, belnr, gjahr, monat, waers, awkey,
               xblnr, budat, bldat, blart, kursf,
               awtyp, xreversal, tcode, stblg, bktxt,
               usnam, stgrd, ppnam
          FROM bkpf
          INTO TABLE @DATA(l_tab_bkpf_date)
          FOR ALL ENTRIES IN @l_tab_bkpf
          WHERE bukrs = @l_tab_bkpf-bukrs
            AND gjahr = @l_tab_bkpf-gjahr
AND belnr = @l_tab_bkpf-stblg.                     "#EC CI_NO_TRANSFORM
      ENDIF.

      " ─────────────────────────────────────────────────────────────
      " SECTION 4: Invoice Verification (rbkp / rseg / rbco)
      " ─────────────────────────────────────────────────────────────

      IF l_tab_bkpf IS NOT INITIAL.
        SELECT xblnr, bukrs, belnr, gjahr, budat, bldat,
          blart, bupla, stblg, xrech, ivtyp, lifnr, stjah
     FROM rbkp
     INTO TABLE @DATA(l_tab_rbkp)
     FOR ALL ENTRIES IN @l_tab_bkpf
     WHERE budat  IN @s_date
       AND bldat  IN @s_bldat
       AND cpudt  IN @s_date1
       AND bukrs  IN @s_bukrs
       AND gjahr   = @l_tab_bkpf-gjahr
       AND blart  IN ( @lc_re, @lc_rc )
       AND bupla  IN @s_bupla
       AND belnr   = @l_tab_bkpf-awkey(10)
AND gjahr   = @l_tab_bkpf-gjahr.                   "#EC CI_NO_TRANSFORM
      ENDIF.

      IF l_tab_rbkp IS NOT INITIAL.
        SELECT belnr, gjahr, buzei, wrbtr, gsber, saknr, sgtxt
          FROM rbco
          INTO TABLE @DATA(l_tab_rbco)
          FOR ALL ENTRIES IN @l_tab_rbkp
          WHERE belnr = @l_tab_rbkp-belnr
   AND gjahr = @l_tab_rbkp-gjahr.                  "#EC CI_NO_TRANSFORM

        SORT l_tab_rbco BY belnr gjahr saknr.
        CONSTANTS lc_gl1 TYPE ska1-saknr VALUE '1404051401'.
        CONSTANTS lc_gl2 TYPE ska1-saknr VALUE '1404051401'.
        CONSTANTS lc_gl3 TYPE ska1-saknr VALUE '1404051401'.

        CONSTANTS lc_gl1_1 TYPE ska1-saknr VALUE '2206552410'.
        CONSTANTS lc_gl2_1 TYPE ska1-saknr VALUE '2206552411'.
        CONSTANTS lc_gl3_1 TYPE ska1-saknr VALUE '2206552412'.

        CONSTANTS lc_gl4 TYPE ska1-saknr VALUE '2206550014'.
        CONSTANTS lc_gl5 TYPE ska1-saknr VALUE '2206552142'.
        " ── Remove superseded GL accounts ─────────────────────────
        DATA lv_del_idx TYPE i.
        LOOP AT l_tab_rbco INTO DATA(l_str_rbco)
WHERE saknr = lc_gl1 OR saknr = lc_gl2 OR saknr = lc_gl3.
          lv_del_idx = sy-tabix.
          DATA(lv_pair_found) = abap_false.

          CASE l_str_rbco-saknr.
            WHEN lc_gl1.
              READ TABLE l_tab_rbco TRANSPORTING NO FIELDS
                WITH KEY belnr = l_str_rbco-belnr
                         gjahr = l_str_rbco-gjahr
                         saknr = lc_gl1_1 BINARY SEARCH.
              IF sy-subrc = 0. lv_pair_found = abap_true. ENDIF.
            WHEN lc_gl2.
              READ TABLE l_tab_rbco TRANSPORTING NO FIELDS
                WITH KEY belnr = l_str_rbco-belnr
                         gjahr = l_str_rbco-gjahr
                         saknr = lc_gl2_1 BINARY SEARCH.
              IF sy-subrc = 0. lv_pair_found = abap_true. ENDIF.
            WHEN lc_gl3.
              READ TABLE l_tab_rbco TRANSPORTING NO FIELDS
                WITH KEY belnr = l_str_rbco-belnr
                         gjahr = l_str_rbco-gjahr
                         saknr = lc_gl3_1 BINARY SEARCH.
              IF sy-subrc = 0. lv_pair_found = abap_true. ENDIF.
          ENDCASE.

          IF lv_pair_found = abap_true.
            DELETE l_tab_rbco INDEX lv_del_idx.
          ENDIF.
        ENDLOOP.

        " ── Map rbco GL accounts to GST tax types & aggregate ─────
        " [ATC-05] COLLECT → manual READ / MODIFY aggregation
        DATA: l_tab_rbco_tax  TYPE TABLE OF ty_rbco_tax,  " adjust type
              l_tab_rbco_tax1 TYPE TABLE OF ty_rbco_tax,
              ls_rbco_agg     TYPE ty_rbco_tax.

        LOOP AT l_tab_rbco ASSIGNING FIELD-SYMBOL(<fs_str_rbco>).
          CLEAR ls_rbco_agg.
          ls_rbco_agg-belnr = <fs_str_rbco>-belnr.
          ls_rbco_agg-gjahr = <fs_str_rbco>-gjahr.

          " Check igstamount
          READ TABLE it_gl INTO DATA(ls_gl_map)
            WITH KEY hkont = <fs_str_rbco>-saknr
                     ztax  = lc_igst BINARY SEARCH.
          IF sy-subrc = 0.
            ls_rbco_agg-igstamount  = <fs_str_rbco>-wrbtr.
            ls_rbco_agg-igst1 = <fs_str_rbco>-saknr.
            ls_rbco_agg-tax2  = lc_tax_i.
            ls_rbco_agg-rcm   = ls_gl_map-rcm.
            ls_rbco_agg-elig  = ls_gl_map-elig.
          ENDIF.

          " Check cgstamount
          READ TABLE it_gl INTO ls_gl_map
            WITH KEY hkont = <fs_str_rbco>-saknr
                     ztax  = lc_cgst BINARY SEARCH.
          IF sy-subrc = 0.
            ls_rbco_agg-cgstamount  = <fs_str_rbco>-wrbtr.
            ls_rbco_agg-cgst1 = <fs_str_rbco>-saknr.
            ls_rbco_agg-tax2  = lc_tax_c.
            ls_rbco_agg-rcm   = ls_gl_map-rcm.
            ls_rbco_agg-elig  = ls_gl_map-elig.
          ENDIF.

          " Check sgstamount
          READ TABLE it_gl INTO ls_gl_map
            WITH KEY hkont = <fs_str_rbco>-saknr
                     ztax  = lc_sgst BINARY SEARCH.
          IF sy-subrc = 0.
            ls_rbco_agg-sgstamount  = <fs_str_rbco>-wrbtr.
            ls_rbco_agg-sgst1 = <fs_str_rbco>-saknr.
            ls_rbco_agg-tax2  = lc_tax_s.
            ls_rbco_agg-rcm   = ls_gl_map-rcm.
            ls_rbco_agg-elig  = ls_gl_map-elig.
          ENDIF.

          " ── Aggregate into tax1 (item-level) ─────────────────
          " [ATC-05] Replaces COLLECT with manual aggregation
          READ TABLE l_tab_rbco_tax1 ASSIGNING FIELD-SYMBOL(<fs_agg1>)
            WITH KEY belnr = ls_rbco_agg-belnr
                     gjahr = ls_rbco_agg-gjahr
                     tax2  = ls_rbco_agg-tax2.
          IF sy-subrc = 0.
            <fs_agg1>-igstamount  = <fs_agg1>-igstamount  + ls_rbco_agg-igstamount.
            <fs_agg1>-cgstamount  = <fs_agg1>-cgstamount  + ls_rbco_agg-cgstamount.
            <fs_agg1>-sgstamount  = <fs_agg1>-sgstamount  + ls_rbco_agg-sgstamount.
            <fs_agg1>-wrbtr = <fs_agg1>-wrbtr + ls_rbco_agg-wrbtr.
          ELSE.
            INSERT ls_rbco_agg INTO TABLE l_tab_rbco_tax1.
          ENDIF.

          " ── Aggregate into tax (header-level) ────────────────
*          IF p_item = space.
*            CLEAR: ls_rbco_agg-igst1,
*                   ls_rbco_agg-cgst1,
*                   ls_rbco_agg-sgst1,
*                   ls_rbco_agg-tax2.
*          ENDIF.

          READ TABLE l_tab_rbco_tax ASSIGNING FIELD-SYMBOL(<fs_agg2>)
            WITH KEY belnr = ls_rbco_agg-belnr
                     gjahr = ls_rbco_agg-gjahr.
          IF sy-subrc = 0.
            <fs_agg2>-igstamount  = <fs_agg2>-igstamount  + ls_rbco_agg-igstamount.
            <fs_agg2>-cgstamount  = <fs_agg2>-cgstamount  + ls_rbco_agg-cgstamount.
            <fs_agg2>-sgstamount  = <fs_agg2>-sgstamount  + ls_rbco_agg-sgstamount.
            <fs_agg2>-wrbtr = <fs_agg2>-wrbtr + ls_rbco_agg-wrbtr.
          ELSE.
            INSERT ls_rbco_agg INTO TABLE l_tab_rbco_tax.
          ENDIF.

          CLEAR ls_gl_map.
        ENDLOOP.

        " ── Backfill GL account numbers at header level ───────────
        DATA: p_item TYPE string.
        IF p_item = space.
          SORT l_tab_rbco_tax1 BY belnr gjahr tax2.
          LOOP AT l_tab_rbco_tax ASSIGNING FIELD-SYMBOL(<fs_rbco>).
            IF <fs_rbco>-cgstamount IS NOT INITIAL OR <fs_rbco>-sgstamount IS NOT INITIAL.
              READ TABLE l_tab_rbco_tax1 INTO DATA(ls_gl)
                WITH KEY belnr = <fs_rbco>-belnr
                         gjahr = <fs_rbco>-gjahr
                         tax2  = lc_tax_c BINARY SEARCH.
              IF sy-subrc = 0.
                <fs_rbco>-cgst1 = ls_gl-cgst1.
                DELETE l_tab_rbco_tax1 INDEX sy-tabix.
              ENDIF.

              READ TABLE l_tab_rbco_tax1 INTO ls_gl
                WITH KEY belnr = <fs_rbco>-belnr
                         gjahr = <fs_rbco>-gjahr
                      tax2  = lc_tax_s BINARY SEARCH.
              IF sy-subrc = 0.
                <fs_rbco>-sgst1 = ls_gl-sgst1.
                DELETE l_tab_rbco_tax1 INDEX sy-tabix.
              ENDIF.
            ENDIF.

            IF <fs_rbco>-igstamount IS NOT INITIAL.
              READ TABLE l_tab_rbco_tax1 INTO ls_gl
                WITH KEY belnr = <fs_rbco>-belnr
                         gjahr = <fs_rbco>-gjahr
                      tax2  = lc_tax_i BINARY SEARCH.
              IF sy-subrc = 0.
                <fs_rbco>-cgst1 = ls_gl-cgst1.
                DELETE l_tab_rbco_tax1 INDEX sy-tabix.
              ENDIF.
            ENDIF.
          ENDLOOP.
        ENDIF.

        DELETE l_tab_rbco_tax WHERE igstamount IS INITIAL
                                AND cgstamount IS INITIAL
                                AND sgstamount IS INITIAL.
      ENDIF.

      SORT l_tab_rbco_tax BY belnr gjahr.

      " ─────────────────────────────────────────────────────────────
      " Build bkpf_final with truncated awkey
      " ─────────────────────────────────────────────────────────────

      " [ATC-06] MOVE-CORRESPONDING → CORRESPONDING
      " [ATC-10] explicit INSERT instead of APPEND
      DATA l_tab_bkpf_final TYPE TABLE OF ty_bkpf. " adjust type

      SORT l_tab_bkpf BY xblnr.
      LOOP AT l_tab_bkpf INTO DATA(l_str_bkpf).
        DATA(l_str_bkpf_temp) = CORRESPONDING ty_bkpf( l_str_bkpf ).
        l_str_bkpf_temp-awkey = l_str_bkpf-awkey(lc_ten).
        INSERT l_str_bkpf_temp INTO TABLE l_tab_bkpf_final.
      ENDLOOP.

      " Build rbkp temp copy
      " [ATC-06] CORRESPONDING instead of MOVE-CORRESPONDING

      TYPES: BEGIN OF ty_rbkp_temp,
               xblnr TYPE rbkp-xblnr,
               bukrs TYPE bukrs,
               belnr TYPE rbkp-belnr,
               gjahr TYPE rbkp-gjahr,
               budat TYPE rbkp-budat,
               bldat TYPE bldat,
               blart TYPE rbkp-blart,
               stblg TYPE stblg,
               stjah TYPE rbkp-stjah,
             END OF ty_rbkp_temp.

      TYPES : ty_t_rbkp_temp TYPE STANDARD TABLE OF ty_rbkp_temp.

      DATA: l_tab_rbkp_temp TYPE STANDARD TABLE OF ty_rbkp_temp.
*      DATA(l_tab_rbkp_temp) = VALUE ty_t_rbkp_temp(
*        FOR ls_rbkp IN l_tab_rbkp
*        ( CORRESPONDING #( ls_rbkp ) ) ).

*      DATA(l_tab_rbkp_temp) = CORRESPONDING ty_rbkp( l_tab_rbkp ).

      " ── Remove duplicate reversal pairs from bkpf_final ─────────
      IF l_tab_rbkp_temp IS NOT INITIAL.
        SORT l_tab_rbkp_temp BY stblg bukrs gjahr.
        LOOP AT l_tab_bkpf_final INTO DATA(l_str_bkpf_final).
          READ TABLE l_tab_rbkp_temp INTO DATA(l_str_rbkp_temp)
            WITH KEY stblg = l_str_bkpf_final-awkey(lc_ten)
                     bukrs = l_str_bkpf_final-bukrs
                  gjahr = l_str_bkpf_final-gjahr BINARY SEARCH.
          IF sy-subrc = 0.
            IF l_str_bkpf_final-budat+4(2) = l_str_rbkp_temp-budat+4(2).
              DELETE l_tab_bkpf_final WHERE awkey(10) = l_str_rbkp_temp-belnr.
              DELETE l_tab_bkpf_final WHERE awkey     = l_str_rbkp_temp-stblg.
            ENDIF.
          ENDIF.
        ENDLOOP.
        SORT l_tab_rbkp_temp BY belnr bukrs gjahr.
      ENDIF.

      " ─────────────────────────────────────────────────────────────
      " rseg (invoice items)
      " ─────────────────────────────────────────────────────────────

      TYPES: BEGIN OF ty_rseg,
               belnr       TYPE rseg-belnr,
               gjahr       TYPE rseg-gjahr,
               buzei       TYPE rseg-buzei,
               ebeln       TYPE rseg-ebeln,
               ebelp       TYPE rseg-ebelp,
               zekkn       TYPE rseg-zekkn,
               matnr       TYPE rseg-matnr,
               bukrs       TYPE rseg-bukrs,
               werks       TYPE rseg-werks,
               wrbtr       TYPE rseg-wrbtr,
               shkzg       TYPE rseg-shkzg,
               mwskz       TYPE rseg-mwskz,
               menge       TYPE rseg-menge,
               meins       TYPE rseg-meins,
               bstme       TYPE rseg-bstme,
               pstyp       TYPE rseg-pstyp,
               bklas       TYPE rseg-bklas,
               tbtkz       TYPE rseg-tbtkz,
               lfbnr       TYPE rseg-lfbnr,
               lfgja       TYPE rseg-lfgja,
               lifnr       TYPE rseg-lifnr,
               customs_val TYPE rseg-customs_val,
               hsn_sac     TYPE rseg-hsn_sac,
               sgtxt       TYPE rseg-sgtxt,
               kschl       TYPE rseg-kschl,
               awkey       TYPE bkpf-awkey,
               ind(1),
             END OF ty_rseg.

      DATA: l_tab_rseg TYPE STANDARD TABLE OF ty_rseg.

      IF l_tab_rbkp IS NOT INITIAL.
        SELECT belnr, gjahr, buzei, ebeln, ebelp, zekkn,
               matnr, bukrs, werks, wrbtr, shkzg, mwskz,
               menge, meins, bstme, pstyp, bklas, tbtkz,
              lfbnr, lfgja, lifnr, customs_val, hsn_sac,
              sgtxt, kschl
         FROM rseg
         INTO TABLE @l_tab_rseg
         FOR ALL ENTRIES IN @l_tab_rbkp
         WHERE belnr = @l_tab_rbkp-belnr
AND gjahr = @l_tab_rbkp-gjahr.                     "#EC CI_NO_TRANSFORM
      ENDIF.

      " ─────────────────────────────────────────────────────────────
      " SECTION 5: Purchase Order Data (ekko / ekpo / ekkn / ekbe)
      " ─────────────────────────────────────────────────────────────

      IF l_tab_rseg IS NOT INITIAL.
        " Prepare clean rseg copy (without pricing conditions)
        DATA(l_tab_rseg1) = l_tab_rseg.
        DELETE l_tab_rseg1 WHERE kschl IS NOT INITIAL.
        SORT l_tab_rseg1 BY bukrs gjahr belnr.

        " Mark line items that have base-item counterparts
        LOOP AT l_tab_rseg ASSIGNING FIELD-SYMBOL(<fs_m>).
          READ TABLE l_tab_rseg1 TRANSPORTING NO FIELDS
            WITH KEY bukrs = <fs_m>-bukrs
                     gjahr = <fs_m>-gjahr
                    belnr = <fs_m>-belnr BINARY SEARCH.
          IF sy-subrc = 0.
            <fs_m>-ind = abap_true.
          ENDIF.
        ENDLOOP.

        " PO header
        SELECT ebeln, bukrs, bedat, bsart, knumv,
               konnr, aedat, lifnr, waers
          FROM ekko
          INTO TABLE @DATA(l_tab_ekko)
          FOR ALL ENTRIES IN @l_tab_rseg
          WHERE bukrs = @l_tab_rseg-bukrs
AND ebeln = @l_tab_rseg-ebeln.                     "#EC CI_NO_TRANSFORM

        IF l_tab_ekko IS NOT INITIAL.
          " PO items
          SELECT ebeln, ebelp, knttp, txz01, matnr,
                 j_1bnbm, iprkz, netwr
            FROM ekpo
            INTO TABLE @DATA(l_tab_ekpo1)
            FOR ALL ENTRIES IN @l_tab_ekko
WHERE ebeln = @l_tab_ekko-ebeln.                   "#EC CI_NO_TRANSFORM

          " PO account assignment
          SELECT ebeln, ebelp, sakto, kostl, ps_psp_pnr
            FROM ekkn
            INTO TABLE @DATA(l_tab_ekkn)
            FOR ALL ENTRIES IN @l_tab_ekko
WHERE ebeln = @l_tab_ekko-ebeln.                   "#EC CI_NO_TRANSFORM

          " WBS elements
          IF l_tab_ekkn IS NOT INITIAL.
            SELECT pspnr, post1
              FROM prps
              INTO TABLE @DATA(l_tab_prps)
              FOR ALL ENTRIES IN @l_tab_ekkn
WHERE pspnr = @l_tab_ekkn-ps_psp_pnr.              "#EC CI_NO_TRANSFORM
            SORT l_tab_prps BY pspnr.
          ENDIF.

          TYPES: BEGIN OF ty_ekbe,
                   ebeln  TYPE ekpo-ebeln,
                   ebelp  TYPE ekpo-ebelp,
                   belnr  TYPE ekbe-belnr,
                   budat  TYPE ekbe-budat,
                   gjahr  TYPE ekbe-gjahr,
                   lfbnr  TYPE ekbe-lfbnr,
                   zekkn  TYPE ekbe-zekkn,
                   packno TYPE eskn-packno,
                 END OF ty_ekbe.
          DATA : it_ekbe TYPE STANDARD TABLE OF ty_ekbe.
          " PO history (GR)
          SELECT ebeln, ebelp, belnr, budat, gjahr,
                 lfbnr, zekkn
            FROM ekbe
            INTO TABLE @it_ekbe
            FOR ALL ENTRIES IN @l_tab_ekko
            WHERE ebeln = @l_tab_ekko-ebeln
AND bewtp = @lc_bewtp_q.                           "#EC CI_NO_TRANSFORM
        ENDIF.
      ENDIF.

      " ─────────────────────────────────────────────────────────────
      " SECTION 5b: FI Line Items (bseg)
      " ─────────────────────────────────────────────────────────────
      DATA: s_lifnr TYPE RANGE OF lifnr.
      IF l_tab_bkpf_final IS NOT INITIAL.

        SELECT bukrs, belnr, gjahr, txgrp, taxps, buzei,
               buzid, koart, mwskz, ktosl, matnr, werks,
               menge, meins, bustw, bupla, lifnr, zuonr,
               hsn_sac, bschl, hkont, dmbtr, gst_part,
             plc_sup, gsber, kunnr, filkd, ebeln, ebelp,
             sgtxt, augbl, augdt, zlspr, prctr, rebzg,
             rebzj, xref3, projk, shkzg
        FROM bseg
        INTO TABLE @DATA(l_tab_bseg)
        FOR ALL ENTRIES IN @l_tab_bkpf_final
        WHERE bukrs = @l_tab_bkpf_final-bukrs
          AND belnr = @l_tab_bkpf_final-belnr
          AND gjahr = @l_tab_bkpf_final-gjahr
          AND ( lifnr    IN @s_lifnr
             OR gst_part IN @s_lifnr )
ORDER BY PRIMARY KEY.                              "#EC CI_NO_TRANSFORM

        IF sy-subrc = 0.
          " Append WBS elements from bseg.projk
          " [ATC-07] Guard before FAE
          IF l_tab_bseg IS NOT INITIAL.
            SELECT pspnr, post1
              FROM prps
            APPENDING TABLE @l_tab_prps
         FOR ALL ENTRIES IN @l_tab_bseg
WHERE pspnr = @l_tab_bseg-projk.                   "#EC CI_NO_TRANSFORM
            SORT l_tab_prps BY pspnr.
          ENDIF.

          " Material document dates via xref3
          DATA(l_tab_bseg_ref) = l_tab_bseg.
          DELETE l_tab_bseg_ref WHERE xref3 IS INITIAL.

          LOOP AT l_tab_bseg_ref ASSIGNING FIELD-SYMBOL(<fs_ref>).
            <fs_ref>-belnr = <fs_ref>-xref3(lc_ten).
            <fs_ref>-gjahr = <fs_ref>-xref3+10(4).
          ENDLOOP.

          IF l_tab_bseg_ref IS NOT INITIAL.
            SELECT mblnr, mjahr, budat
              FROM mkpf
          INTO TABLE @DATA(l_tab_mkpf)
    FOR ALL ENTRIES IN @l_tab_bseg_ref
   WHERE mblnr = @l_tab_bseg_ref-belnr
    AND mjahr = @l_tab_bseg_ref-gjahr.
            SORT l_tab_mkpf BY mblnr mjahr.
          ENDIF.
        ENDIF.

        " Material master (marc)
        IF l_tab_bseg IS NOT INITIAL.
          SELECT matnr, werks, steuc
            FROM marc
   APPENDING TABLE @DATA(l_tab_marc)
   FOR ALL ENTRIES IN @l_tab_bseg
WHERE matnr = @l_tab_bseg-matnr.                   "#EC CI_NO_TRANSFORM
        ENDIF.

        " Withholding-tax line items
        " [ATC-06] CORRESPONDING instead of MOVE-CORRESPONDING
*        DATA l_tab_bseg_tcs TYPE TABLE OF ty_bseg_tcs.
*        LOOP AT l_tab_bseg INTO DATA(ls_bseg_wit) WHERE ktosl = lc_wit.
*          INSERT CORRESPONDING ty_bseg_tcs( ls_bseg_wit ) INTO TABLE l_tab_bseg_tcs.
*        ENDLOOP.

        DATA(l_tab_bseg_tcs) = l_tab_bseg.

        DELETE l_tab_marc WHERE matnr IS INITIAL.

        IF l_tab_bseg IS NOT INITIAL.
          SORT l_tab_bseg BY bukrs belnr gjahr txgrp taxps.
        ENDIF.

        " ── Vendor / customer line extraction ──────────────────────
*        DATA: l_tab_bseg_lifnr TYPE TABLE OF ty_bseg_lifnr,
*              l_tab_val        TYPE TABLE OF ty_val.

        DATA(l_tab_bseg_lifnr) = l_tab_bseg.
        LOOP AT l_tab_bseg INTO DATA(ls_bseg_kd)
 WHERE koart = lc_koart_k OR koart = lc_koart_d.

          IF ls_bseg_kd-filkd IS NOT INITIAL.
            ls_bseg_kd-lifnr = ls_bseg_kd-filkd.
            ls_bseg_kd-kunnr = ls_bseg_kd-filkd.
          ENDIF.


          " [ATC-05] COLLECT → manual aggregation
*          DATA(ls_val) = CORRESPONDING ty_val( ls_bseg_kd ).
*          DATA(ls_val) = ls_bseg_kd.
*          READ TABLE l_tab_val ASSIGNING FIELD-SYMBOL(<fs_val>)
*            WITH KEY bukrs = ls_val-bukrs
*                     gjahr = ls_val-gjahr
*                     belnr = ls_val-belnr.
*          IF sy-subrc = 0.
*            <fs_val>-dmbtr = <fs_val>-dmbtr + ls_val-dmbtr.
*          ELSE.
*            INSERT ls_val INTO TABLE l_tab_val.
*          ENDIF.
        ENDLOOP.
        TYPES: BEGIN OF ty_doc,
                 bukrs TYPE bkpf-bukrs,
                 belnr TYPE bkpf-belnr,
                 gjahr TYPE bkpf-gjahr,
                 dmbtr TYPE bseg-dmbtr,
               END OF ty_doc.

        DATA: l_tab_val TYPE STANDARD TABLE OF ty_doc.
        SORT l_tab_val BY bukrs gjahr belnr.

        " Material descriptions
        IF l_tab_bseg IS NOT INITIAL.
          SELECT matnr, maktx
            FROM makt
     INTO TABLE @DATA(l_tab_makt)
   FOR ALL ENTRIES IN @l_tab_bseg
 WHERE matnr = @l_tab_bseg-matnr.

          SELECT matnr, matkl, mtart
            FROM mara
           INTO TABLE @DATA(it_mara)
      FOR ALL ENTRIES IN @l_tab_bseg
    WHERE matnr = @l_tab_bseg-matnr.
        ENDIF.
        SORT it_mara BY matnr.

        " ─────────────────────────────────────────────────────────
        " SECTION 6: Tax Conditions (bset)
        " ─────────────────────────────────────────────────────────

        IF l_tab_bseg IS NOT INITIAL.
          " [ATC-04] No header-line range table
          CLEAR lr_cond.
          DATA: it_cond TYPE STANDARD TABLE OF zsd_t_tax_cond.
          lr_cond = VALUE #( FOR ls_cond_entry IN it_cond ( sign   = lc_sign
                             option = lc_opt
                             low    = ls_cond_entry-kschl ) ).

          TYPES: BEGIN OF ty_bset,
                   bukrs   TYPE bukrs,
                   belnr   TYPE belnr_d,
                   gjahr   TYPE gjahr,
                   taxps   TYPE bseg-taxps,
                   ktosl   TYPE bset-ktosl,
                   kschl   TYPE kschl,
                   txgrp   TYPE txgrp,
                   buzei   TYPE buzei,
                   shkzg   TYPE shkzg,
                   fwbas   TYPE fwbas_bses,
                   fwste   TYPE fwste,
                   kbetr   TYPE kbetr_tax,
                   mwskz   TYPE bset-mwskz,
                   hkont   TYPE hkont,
                   hwbas   TYPE hwbas,
                   hwste   TYPE hwste,
                   gsp_tax TYPE zsd_t_tax_cond-gsp_tax,
                 END OF ty_bset.

          DATA: l_tab_bset TYPE STANDARD TABLE OF ty_bset.
          SELECT bukrs, belnr, gjahr, taxps, ktosl,
                 kschl, txgrp, buzei, shkzg, fwbas,
           fwste, kbetr, mwskz, hkont, hwbas, hwste
      FROM bset
      INTO TABLE @l_tab_bset
      FOR ALL ENTRIES IN @l_tab_bseg
      WHERE bukrs = @l_tab_bseg-bukrs
        AND belnr = @l_tab_bseg-belnr
        AND gjahr = @l_tab_bseg-gjahr
        AND ( kschl IN @lr_cond
           OR ktosl IN ( 'JII','JIC','JIS','JIM',
'ZSG','ZCG','ZKC','ZKS','ZS1' ) ).                 "#EC CI_NO_TRANSFORM

          " Remove sales GL from RE-type docs
          DELETE l_tab_bset WHERE hkont = lc_gl4.

          " Map condition keys to GST tax type
          LOOP AT it_cond INTO DATA(ls_cond_map) WHERE kschl <> lc_zitr.
            LOOP AT l_tab_bset ASSIGNING FIELD-SYMBOL(<fs_bset_map>)
              WHERE kschl = ls_cond_map-kschl.
              <fs_bset_map>-gsp_tax = ls_cond_map-gsp_tax.
            ENDLOOP.
          ENDLOOP.

          " Special ZITR mapping by ktosl
          LOOP AT l_tab_bset ASSIGNING FIELD-SYMBOL(<fs_bset>) WHERE kschl = lc_zitr.
            CASE <fs_bset>-ktosl.
              WHEN 'JIC'.
                <fs_bset>-gsp_tax = lc_cgst.
              WHEN 'JIS'.
                <fs_bset>-gsp_tax = lc_sgst.
              WHEN 'JII'.
                <fs_bset>-gsp_tax = lc_igst.
              WHEN 'JIM'.
                <fs_bset>-gsp_tax = lc_jimd.
              WHEN 'JRI'.
                <fs_bset>-gsp_tax = lc_rigst.
              WHEN 'JRC'.
                <fs_bset>-gsp_tax = lc_rcgst.
              WHEN 'JRS'.
                <fs_bset>-gsp_tax = lc_rsgst.
            ENDCASE.
          ENDLOOP.
        ENDIF.

        " ─────────────────────────────────────────────────────────
        " SECTION 6b: bseg post-processing & filtering
        " ─────────────────────────────────────────────────────────

        TYPES: BEGIN OF ty_bseg,
                 bukrs    TYPE bseg-bukrs,
                 belnr    TYPE bseg-belnr,
                 gjahr    TYPE bseg-gjahr,
                 buzei    TYPE bseg-buzei,
                 buzid    TYPE bseg-buzid,
                 koart    TYPE bseg-koart,
                 mwskz    TYPE bseg-mwskz,
                 txgrp    TYPE bseg-txgrp,
                 ktosl    TYPE bseg-ktosl,
                 matnr    TYPE bseg-matnr,
                 werks    TYPE bseg-werks,
                 menge    TYPE bseg-menge,
                 meins    TYPE bseg-meins,
                 bustw    TYPE bseg-bustw,
                 taxps    TYPE bseg-taxps,
                 bupla    TYPE bseg-bupla,
                 lifnr    TYPE bseg-lifnr,
                 zuonr    TYPE bseg-zuonr,
                 hsn_sac  TYPE bseg-hsn_sac,
                 bschl    TYPE bseg-bschl,
                 hkont    TYPE bseg-hkont,
                 dmbtr    TYPE bseg-dmbtr,
                 gst_part TYPE bseg-gst_part,
                 plc_sup  TYPE bseg-plc_sup,
                 kunnr    TYPE bseg-kunnr,
                 augbl    TYPE bseg-augbl,
                 augdt    TYPE bseg-augdt,
                 zlspr    TYPE bseg-zlspr,
                 prctr    TYPE bseg-prctr,
                 rebzg    TYPE bseg-rebzg,
                 rebzj    TYPE bseg-rebzj,
                 xref3    TYPE bseg-xref3,
               END OF ty_bseg.

        IF l_tab_bkpf_final IS NOT INITIAL.

          " Plant-level line items
          DATA l_tab_bseg_lifnr_plant TYPE TABLE OF ty_bseg.
          LOOP AT l_tab_bseg INTO DATA(ls_bseg_plant).
            IF ls_bseg_plant-werks IS NOT INITIAL
           OR ls_bseg_plant-bupla IS NOT INITIAL.
              INSERT CORRESPONDING ty_bseg( ls_bseg_plant )
                INTO TABLE l_tab_bseg_lifnr_plant.
            ENDIF.
          ENDLOOP.

          IF l_tab_bseg IS NOT INITIAL.
            DATA(l_tab_bseg_matnr) = l_tab_bseg.
            DATA(l_tab_bseg_tx)    = l_tab_bseg.

            DELETE l_tab_bseg WHERE koart = lc_koart_d.
            DELETE l_tab_bseg WHERE koart = lc_koart_k.
            DELETE l_tab_bseg_matnr WHERE buzid <> lc_buzid_t.
            SORT l_tab_bseg_matnr BY bukrs belnr gjahr.
            DELETE l_tab_bseg_tx WHERE mwskz IS NOT INITIAL.
            DELETE l_tab_bseg_tx WHERE ktosl = lc_wit.
            DELETE l_tab_bseg WHERE buzid = space AND txgrp = space.
            DELETE l_tab_bseg WHERE buzid = lc_buzid_t.

            " Re-add non-tax items that have no counterpart in matnr set
            LOOP AT l_tab_bseg_tx INTO DATA(ls_add).
              READ TABLE l_tab_bseg_matnr TRANSPORTING NO FIELDS
                WITH KEY bukrs = ls_add-bukrs
                         belnr = ls_add-belnr
                         gjahr = ls_add-gjahr BINARY SEARCH.
              IF sy-subrc <> 0.
                INSERT ls_add INTO TABLE l_tab_bseg.
              ENDIF.
            ENDLOOP.

            SORT l_tab_bseg BY bukrs gjahr belnr txgrp taxps buzei.
            DELETE ADJACENT DUPLICATES FROM l_tab_bseg COMPARING ALL FIELDS.
            CLEAR l_tab_bseg_matnr.

            " Assign taxps / txgrp to items missing them
            DATA lv_taxps TYPE bseg-taxps.
            SORT l_tab_bseg BY bukrs belnr gjahr ASCENDING taxps DESCENDING.

            LOOP AT l_tab_bseg ASSIGNING FIELD-SYMBOL(<fs_txgrp>).
              AT NEW belnr.
                lv_taxps = 1.
              ENDAT.
              IF <fs_txgrp>-taxps IS INITIAL.
                <fs_txgrp>-taxps = lv_taxps.
                <fs_txgrp>-txgrp = lv_taxps.
              ENDIF.
              lv_taxps = lv_taxps + 1.
            ENDLOOP.

            " Separate ZO-prefixed ktosl items, then re-merge
            SORT l_tab_bseg BY bukrs belnr gjahr txgrp taxps mwskz buzei.

            DATA l_tab_bseg_rv TYPE TABLE OF ty_bseg.  " adjust type
            LOOP AT l_tab_bseg INTO DATA(ls_bseg_rv) WHERE ktosl(2) = lc_zo.
*              INSERT ls_bseg_rv INTO TABLE l_tab_bseg_rv.
            ENDLOOP.
            DELETE l_tab_bseg WHERE ktosl(2) = lc_zo.

            DELETE ADJACENT DUPLICATES FROM l_tab_bseg
  COMPARING bukrs belnr gjahr txgrp taxps mwskz buzei.

*            INSERT LINES OF l_tab_bseg_rv INTO TABLE l_tab_bseg.
            SORT l_tab_bseg BY bukrs gjahr belnr txgrp taxps buzei.
            DELETE ADJACENT DUPLICATES FROM l_tab_bseg
        COMPARING bukrs belnr gjahr txgrp taxps buzei.
            DELETE ADJACENT DUPLICATES FROM l_tab_bseg COMPARING ALL FIELDS.
          ENDIF.

          " Remove superseded GL lines (same logic as rbco)
          SORT l_tab_bseg BY bukrs gjahr belnr hkont.
          DATA lv_del_idx1 TYPE i.
          LOOP AT l_tab_bseg INTO DATA(l_str_del)
WHERE hkont = lc_gl1 OR hkont = lc_gl2 OR hkont = lc_gl3.
            lv_del_idx1 = sy-tabix.
            DATA(lv_pair_found2) = abap_false.

            CASE l_str_del-hkont.
              WHEN lc_gl1.
                READ TABLE l_tab_bseg TRANSPORTING NO FIELDS
                  WITH KEY bukrs = l_str_del-bukrs
                           gjahr = l_str_del-gjahr
                           belnr = l_str_del-belnr
                           hkont = lc_gl1_1 BINARY SEARCH.
                IF sy-subrc = 0. lv_pair_found2 = abap_true. ENDIF.
              WHEN lc_gl2.
                READ TABLE l_tab_bseg TRANSPORTING NO FIELDS
                  WITH KEY bukrs = l_str_del-bukrs
                           gjahr = l_str_del-gjahr
                           belnr = l_str_del-belnr
                           hkont = lc_gl2_1 BINARY SEARCH.
                IF sy-subrc = 0. lv_pair_found2 = abap_true. ENDIF.
              WHEN lc_gl3.
                READ TABLE l_tab_bseg TRANSPORTING NO FIELDS
                  WITH KEY bukrs = l_str_del-bukrs
                           gjahr = l_str_del-gjahr
                           belnr = l_str_del-belnr
                           hkont = lc_gl3_1 BINARY SEARCH.
                IF sy-subrc = 0. lv_pair_found2 = abap_true. ENDIF.
            ENDCASE.

            IF lv_pair_found2 = abap_true.
              DELETE l_tab_bseg INDEX lv_del_idx1.
            ENDIF.
          ENDLOOP.

          " ─────────────────────────────────────────────────────────
          " SECTION 7: Master Data
          " ─────────────────────────────────────────────────────────

          " Plant master
          IF l_tab_bseg_lifnr_plant IS NOT INITIAL.
            SELECT werks, name1, regio, j_1bbranch, ort01
              FROM t001w
              INTO TABLE @DATA(l_tab_t001w)
              FOR ALL ENTRIES IN @l_tab_bseg_lifnr_plant
WHERE werks = @l_tab_bseg_lifnr_plant-werks.       "#EC CI_NO_TRANSFORM
          ENDIF.

          " GSTIN / branch for plants
          IF l_tab_t001w IS NOT INITIAL.
            SELECT bukrs, branch, gstin, name, adrnr
              FROM j_1bbranch
            INTO TABLE @DATA(l_tab_j_1bbranch_plant)
            FOR ALL ENTRIES IN @l_tab_t001w
WHERE branch = @l_tab_t001w-j_1bbranch.            "#EC CI_NO_TRANSFORM
          ENDIF.

          " GSTIN / branch for business places
          IF l_tab_bseg_lifnr_plant IS NOT INITIAL.
            SELECT bukrs, branch, gstin, name, adrnr
              FROM j_1bbranch
             APPENDING TABLE @l_tab_j_1bbranch_plant
          FOR ALL ENTRIES IN @l_tab_bseg_lifnr_plant
WHERE branch = @l_tab_bseg_lifnr_plant-bupla.      "#EC CI_NO_TRANSFORM
          ENDIF.

          " Plant addresses
          IF l_tab_j_1bbranch_plant IS NOT INITIAL.
            SELECT addrnumber, name3, city1, post_code1,
      region, street, str_suppl1, str_suppl2, str_suppl3
 FROM adrc
 INTO TABLE @DATA(l_tab_bupadrc_plant)
 FOR ALL ENTRIES IN @l_tab_j_1bbranch_plant
WHERE addrnumber = @l_tab_j_1bbranch_plant-adrnr.  "#EC CI_NO_TRANSFORM
          ENDIF.

          " Business place GSTIN
          IF l_tab_bseg_lifnr IS NOT INITIAL.
            SELECT bukrs, branch, gstin, name, adrnr
              FROM j_1bbranch
              INTO TABLE @DATA(l_tab_j_1bbranch)
              FOR ALL ENTRIES IN @l_tab_bseg_lifnr
              WHERE branch = @l_tab_bseg_lifnr-bupla
AND bukrs  = @l_tab_bseg_lifnr-bukrs.              "#EC CI_NO_TRANSFORM

            IF sy-subrc = 0.
              SELECT addrnumber, name3, city1, post_code1, region, street, str_suppl1
                FROM adrc
                INTO TABLE @DATA(l_tab_bupadrc)
                FOR ALL ENTRIES IN @l_tab_j_1bbranch
 WHERE addrnumber = @l_tab_j_1bbranch-adrnr.       "#EC CI_NO_TRANSFORM
            ENDIF.
          ENDIF.

          " Vendor master
          IF l_tab_bseg_lifnr IS NOT INITIAL.
            SELECT lifnr, land1, name1, name2, name3, name4,
                   ort01, regio, stceg, stcd3, adrnr, ktokk
              FROM lfa1
              INTO TABLE @DATA(l_tab_lfa1)
              FOR ALL ENTRIES IN @l_tab_bseg_lifnr
              WHERE lifnr = @l_tab_bseg_lifnr-lifnr
 OR lifnr = @l_tab_bseg_lifnr-gst_part.            "#EC CI_NO_TRANSFORM

            SELECT kunnr, land1, name1, name2, name3, name4,
                   ort01, regio, stceg, stcd3, adrnr
              FROM kna1
              INTO TABLE @DATA(l_tab_kna1)
              FOR ALL ENTRIES IN @l_tab_bseg_lifnr
 WHERE kunnr = @l_tab_bseg_lifnr-kunnr.            "#EC CI_NO_TRANSFORM
          ENDIF.
        ENDIF.

        " Vendor master from rbkp
        IF l_tab_rbkp IS NOT INITIAL.
          SELECT lifnr, land1, name1, name2, name3, name4,
                 ort01, regio, stceg, stcd3, adrnr, ktokk
            FROM lfa1
            APPENDING TABLE @l_tab_lfa1
            FOR ALL ENTRIES IN @l_tab_rbkp
WHERE lifnr = @l_tab_rbkp-lifnr.                   "#EC CI_NO_TRANSFORM
        ENDIF.

        SORT l_tab_lfa1 BY lifnr.
        DELETE ADJACENT DUPLICATES FROM l_tab_lfa1 COMPARING lifnr.

        " Vendor addresses & email
        IF l_tab_lfa1 IS NOT INITIAL.
          SELECT addrnumber, name3, city1, post_code1,
    region, street, str_suppl1, str_suppl2, str_suppl3
FROM adrc
INTO TABLE @DATA(l_tab_adrc)
FOR ALL ENTRIES IN @l_tab_lfa1
WHERE addrnumber = @l_tab_lfa1-adrnr.              "#EC CI_NO_TRANSFORM

          SELECT addrnumber, smtp_addr
            FROM adr6
          INTO TABLE @DATA(l_tab_adr6)
        FOR ALL ENTRIES IN @l_tab_lfa1
WHERE addrnumber = @l_tab_lfa1-adrnr.              "#EC CI_NO_TRANSFORM
        ENDIF.

      ENDIF.
    ENDIF.

    " ─────────────────────────────────────────────────────────────────
    " SECTION 8: Final Sort
    " ─────────────────────────────────────────────────────────────────

    " Prepare bset variant without kschl
    DATA(l_tab_bset1) = l_tab_bset.
    DELETE l_tab_bset1 WHERE kschl IS NOT INITIAL.

    " TDS item clearing removal
    DELETE l_tab_bseg_lifnr WHERE zlspr = lc_zlspr_y.

    " ── Consistent sort order for all downstream processing ────────
    SORT l_tab_bkpf            BY bukrs belnr gjahr.
    SORT l_tab_bkpf_bktxt      BY bukrs xblnr blart bktxt.
    SORT l_tab_bseg_lifnr      BY bukrs belnr gjahr.
    SORT l_tab_bseg_lifnr_plant BY bukrs belnr gjahr werks.
    SORT l_tab_val              BY bukrs gjahr belnr.
    SORT l_tab_lfa1             BY lifnr.
    SORT l_tab_adrc             BY addrnumber.
    SORT l_tab_adr6             BY addrnumber.
    SORT l_tab_bupadrc_plant    BY addrnumber.
    SORT l_tab_bseg             BY bukrs belnr gjahr.
    SORT l_tab_j_1bbranch       BY bukrs branch gstin.
    SORT l_tab_j_1bbranch_plant BY bukrs branch gstin.
    SORT l_tab_bupadrc          BY addrnumber.
    SORT l_tab_t001w            BY werks.
    SORT l_tab_ekko             BY ebeln bukrs.
    SORT l_tab_ekpo1            BY ebeln ebelp.
    SORT l_tab_rseg             BY belnr gjahr ebeln ebelp buzei.
    SORT l_tab_rbkp             BY belnr gjahr.
    SORT l_tab_bset             BY bukrs belnr gjahr taxps gsp_tax mwskz ktosl kschl.
    SORT l_tab_bset1            BY bukrs belnr gjahr mwskz ktosl.
    SORT l_tab_bseg_matnr       BY bukrs belnr gjahr txgrp.
    SORT l_tab_makt             BY matnr.
    SORT l_tab_bkpf_date        BY bukrs gjahr belnr.
    SORT l_tab_kna1             BY kunnr.
    SORT l_tab_marc             BY matnr werks.
    SORT l_tab_rbco_tax         BY belnr gjahr.
    SORT lt_user_ppnam          BY bname.
    SORT lt_user_usnam          BY bname.


*    SELECT * FROM zgsp_cap_po INTO TABLE @DATA(lt_cap). "#EC CI_NOWHERE.
    SELECT * FROM zfi_gsp_cond_hsn INTO TABLE @DATA(lt_hsn). "#EC CI_NOWHERE.
    SELECT * FROM zsd_t_einv_uom INTO  TABLE @DATA(it_uom). "#EC CI_NOWHERE.

    SELECT * FROM t023t INTO TABLE @DATA(lt_t023t)
      WHERE
      spras = 'E'.
    SORT lt_t023t BY matkl.

    IF l_tab_rbkp_temp IS NOT INITIAL.
      SELECT bukrs, belnr, gjahr, bldat, budat,stblg,stjah,bkref FROM rbkp INTO TABLE @DATA(lt_rbkp)
        FOR ALL ENTRIES IN @l_tab_rbkp_temp
        WHERE belnr = @l_tab_rbkp_temp-stblg
        AND gjahr = @l_tab_rbkp_temp-stjah.
      DELETE lt_rbkp WHERE stblg IS INITIAL.

      LOOP AT lt_rbkp ASSIGNING FIELD-SYMBOL(<fs_key>).
        CONCATENATE <fs_key>-belnr <fs_key>-gjahr INTO <fs_key>-bkref.
      ENDLOOP.
      SORT lt_rbkp BY bukrs gjahr belnr.
      IF lt_rbkp IS NOT INITIAL.
        SELECT bukrs,gjahr,belnr,awkey FROM bkpf INTO TABLE @DATA(lt_miro_ref)
          FOR ALL ENTRIES IN @lt_rbkp
          WHERE bukrs = @lt_rbkp-bukrs
          AND gjahr = @lt_rbkp-gjahr
          AND awkey = @lt_rbkp-bkref..
        SORT lt_miro_ref BY bukrs gjahr awkey.
      ENDIF.
    ENDIF.

    IF l_tab_ekko IS NOT INITIAL.
      SELECT ebeln, bedat FROM ekko
        INTO TABLE @DATA(lt_con)
        FOR ALL ENTRIES IN @l_tab_ekko
      WHERE ebeln = @l_tab_ekko-konnr.

      SORT lt_con BY ebeln.
      SELECT lifnr,name1,stcd3 FROM lfa1 INTO TABLE @DATA(lt_po_ven)
        FOR ALL ENTRIES IN @l_tab_ekko
        WHERE lifnr = @l_tab_ekko-lifnr.
      SORT lt_po_ven BY lifnr.
    ENDIF.

    IF l_tab_bseg_lifnr IS NOT INITIAL.
      SELECT bukrs,gjahr,belnr, xblnr, bldat,budat FROM bkpf INTO TABLE @DATA(lt_crn)
        FOR ALL ENTRIES IN @l_tab_bseg_lifnr
        WHERE bukrs = @l_tab_bseg_lifnr-bukrs
        AND belnr = @l_tab_bseg_lifnr-rebzg
        AND gjahr = @l_tab_bseg_lifnr-rebzj.
    ENDIF.

*    SORT it_state BY region.
    SORT lt_hsn BY kschl.
    IF l_tab_ekpo1[] IS NOT INITIAL.
      SELECT ebeln, ebelp, packno FROM essr INTO TABLE  @DATA(t_essr)
        FOR ALL ENTRIES IN @l_tab_ekpo1
        WHERE ebeln = @l_tab_ekpo1-ebeln
          AND ebelp = @l_tab_ekpo1-ebelp.               "#EC CI_GENBUFF
      IF t_essr[] IS NOT INITIAL.
        SELECT  packno, sub_packno  FROM esll INTO TABLE @DATA(t_esll1)
          FOR ALL ENTRIES IN @t_essr
          WHERE packno = @t_essr-packno.
        IF t_esll1[] IS NOT INITIAL.
          SELECT packno, sub_packno, srvpos FROM esll INTO TABLE @DATA(t_esll2)
            FOR ALL ENTRIES IN @t_esll1
            WHERE packno = @t_esll1-sub_packno.
          SORT t_essr BY ebeln ebelp.
          SORT t_esll1 BY packno.
          SORT t_esll2 BY packno."SUB_PACKNO.
        ENDIF.
      ENDIF.
      IF l_tab_ekko[] IS NOT INITIAL.
        SELECT knumv,
               kposn,
               kbetr FROM konv
          INTO TABLE @DATA(lt_cess)
          FOR ALL ENTRIES IN @l_tab_ekko
          WHERE knumv = @l_tab_ekko-knumv
          AND kschl = 'ZGCC'.
        SORT lt_cess BY knumv.
      ENDIF.
    ENDIF.

    SELECT  * FROM zfi_t_taxmap_pur INTO TABLE @DATA(it_tax). "#EC CI_NOWHERE.

    TYPES: BEGIN OF ty_bkpf_bseg_del,
             bukrs    TYPE bkpf-bukrs,
             belnr    TYPE bkpf-belnr,
             gjahr    TYPE bkpf-gjahr,
             bldat    TYPE bkpf-bldat,
             budat    TYPE bkpf-budat,
             blart    TYPE bkpf-blart,
             usnam    TYPE bkpf-usnam,
             xblnr    TYPE bkpf-xblnr,
             stblg    TYPE bkpf-stblg,
             xmwst    TYPE bkpf-xmwst,
             awkey    TYPE bkpf-awkey,
             bktxt    TYPE bkpf-bktxt,
             werks    TYPE bseg-werks,
             buzid    TYPE bseg-buzid,
             lifnr    TYPE bseg-lifnr,
             koart    TYPE bseg-koart,
             ktosl    TYPE bseg-ktosl,
             gsber    TYPE bseg-gsber,
             bupla    TYPE bseg-bupla,
             mwskz    TYPE bseg-mwskz,
             gst_part TYPE bseg-gst_part,
             plc_sup  TYPE bseg-plc_sup,
             hsn_sac  TYPE bseg-hsn_sac,
             zlspr    TYPE bseg-zlspr,
             buzei    TYPE bseg-buzei,
             txbhw    TYPE bseg-txbhw,
             shkzg    TYPE bseg-shkzg,
             ebeln    TYPE bseg-ebeln,
             hkont    TYPE bseg-hkont,
           END OF ty_bkpf_bseg_del.
    DATA: lt_bsec         TYPE STANDARD TABLE OF bsec,
          t_bkpf_bseg_del TYPE STANDARD TABLE OF ty_bkpf_bseg_del.

*    IF isd = ''.
*
    DATA : l_str_final TYPE zsd_s_global_st.
    CLEAR l_str_final.

    IF l_tab_bkpf_final[] IS NOT INITIAL.
      SELECT * FROM bsec INTO TABLE lt_bsec
         FOR ALL ENTRIES IN l_tab_bkpf_final
         WHERE bukrs = l_tab_bkpf_final-bukrs AND
               belnr = l_tab_bkpf_final-belnr AND
               gjahr = l_tab_bkpf_final-gjahr ORDER BY PRIMARY KEY.
    ENDIF.

    IF l_tab_rseg IS NOT INITIAL.
      SELECT * FROM t685t INTO TABLE @DATA(lt_t685t)
        FOR ALL ENTRIES IN @l_tab_rseg
        WHERE kschl = @l_tab_rseg-kschl
        AND spras = 'E'.
      SORT lt_t685t BY kschl.
      DELETE ADJACENT DUPLICATES FROM lt_t685t COMPARING kschl.

      SELECT matnr , bwkey, bklas
        FROM mbew INTO TABLE @DATA(lt_mbew)
        FOR ALL ENTRIES IN @l_tab_rseg
        WHERE matnr = @l_tab_rseg-matnr
        AND bwkey = @l_tab_rseg-werks.
      DELETE lt_mbew WHERE bklas IS INITIAL.
    ENDIF.

    IF l_tab_bseg IS NOT INITIAL.
      SELECT matnr  bwkey  bklas
       FROM mbew APPENDING TABLE lt_mbew
       FOR ALL ENTRIES IN l_tab_bseg
       WHERE matnr = l_tab_bseg-matnr
       AND bwkey = l_tab_bseg-werks.
      DELETE lt_mbew WHERE bklas IS INITIAL.
    ENDIF.
    DELETE ADJACENT DUPLICATES FROM lt_mbew COMPARING ALL FIELDS. "#EC CI_SORTED
    IF lt_mbew IS NOT INITIAL.
      SELECT * FROM t030 INTO TABLE @DATA(lt_t030)
        FOR ALL ENTRIES IN @lt_mbew
        WHERE bklas = @lt_mbew-bklas
        AND ktopl = '1000'
        AND ktosl = 'BSX'.
      DELETE lt_t030 WHERE bklas IS INITIAL.
      SORT lt_mbew BY matnr bwkey.
      SORT lt_t030 BY bklas .
    ENDIF.

    DATA: awkey TYPE bkpf-awkey.
    SORT lt_bsec BY bukrs belnr gjahr.
    SORT t_bkpf_bseg_del BY bukrs gjahr xblnr lifnr blart.
    SORT l_tab_ekpo1 BY ebeln ebelp.
    SORT l_tab_ekkn BY ebeln ebelp.
    SORT it_ekbe BY ebeln ebelp belnr.
*      SORT it_bkpf BY awkey.
    SORT lt_crn BY bukrs gjahr belnr.
    DATA: go_flag(1).
    DATA: go_miro(1).

    IF it_ekbe IS NOT INITIAL.
      LOOP AT it_ekbe ASSIGNING FIELD-SYMBOL(<fs_ekbe>).
        <fs_ekbe>-packno = <fs_ekbe>-lfbnr.
      ENDLOOP.
      SELECT packno,
             zekkn,
             bekkn,
             loekz,
             aedat,
             kflag,
             netwr,
             sakto,
             gsber,
             kostl
             FROM eskn INTO TABLE @DATA(l_tab_eskn)
             FOR ALL ENTRIES IN  @it_ekbe
               WHERE packno = @it_ekbe-packno
                 AND zekkn = @it_ekbe-zekkn.            "#EC CI_GENBUFF

      SORT l_tab_eskn BY packno zekkn.
    ENDIF.

    DATA(l_tab_bseg_50) = l_tab_bseg.
    DELETE l_tab_bseg_50 WHERE bschl NE '50'.
    SORT l_tab_bseg_50 BY bukrs gjahr belnr.
    SORT lt_skat BY saknr.
*      SORT lt_cap  BY bsart.
    SORT lt_t030 BY bklas.
    SORT lt_po_ven BY lifnr.
    SORT lt_t023t BY matkl.
    SORT it_uom BY sap_uom.
    LOOP AT l_tab_bkpf_final INTO l_str_bkpf .
*          CLEAR:l_str_rbkp.
*          CLEAR:l_str_rseg.
*          CLEAR: go_flag.
*          CLEAR:go_miro.

      IF l_str_bkpf-xreversal IS NOT INITIAL.
*            CLEAR l_str_bkpf_date.
        READ TABLE l_tab_bkpf_date INTO DATA(l_str_bkpf_date) WITH KEY bukrs = l_str_bkpf-bukrs
                                                                 gjahr = l_str_bkpf-gjahr
                                                                 belnr = l_str_bkpf-stblg BINARY SEARCH.
        IF sy-subrc = 0.
          IF l_str_bkpf-budat+4(2) = l_str_bkpf_date-budat+4(2).
            CLEAR : l_str_bkpf,l_str_bkpf_date.
            CONTINUE.
          ENDIF.
        ENDIF.
      ENDIF.

      READ TABLE l_tab_bseg TRANSPORTING NO FIELDS WITH KEY bukrs = l_str_bkpf-bukrs
                                                  belnr = l_str_bkpf-belnr
                                                  gjahr = l_str_bkpf-gjahr BINARY SEARCH.

      IF sy-subrc = 0.
        DATA(l_var_tabix) = sy-tabix.

        LOOP AT l_tab_bseg  INTO DATA(l_str_bseg) FROM l_var_tabix.
          IF l_str_bseg-hkont IS NOT INITIAL.
            l_str_final-customitem8 = l_str_bseg-hkont.  "  GL A/c No.

            IF l_str_bkpf-blart = lc_re OR l_str_bkpf-blart = lc_ra." or l_str_bkpf-blart = 'RC'.
            ELSE.
              l_str_final-customitem7 = l_str_bseg-hkont.  "  GL A/c No.
              READ TABLE lt_skat INTO DATA(ls_skat) WITH KEY saknr = l_str_bseg-hkont BINARY SEARCH.
              IF sy-subrc = 0.
                l_str_final-customitem10 = ls_skat-txt50.  " GL A/c Description
              ENDIF.
            ENDIF.
            IF l_str_final-customitem9 IS INITIAL.
              CALL FUNCTION 'CONVERSION_EXIT_ABPSP_OUTPUT'
                EXPORTING
                  input  = l_str_bseg-projk
                IMPORTING
                  output = l_str_final-customitem9.

              CONDENSE l_str_final-customitem9.
              READ TABLE l_tab_prps INTO DATA(l_str_prps) WITH KEY  pspnr = l_str_bseg-projk BINARY SEARCH.

              IF sy-subrc = 0.
                l_str_final-customitem10 = l_str_prps-post1.  " WBS Element Desc
              ENDIF.
            ENDIF.
          ENDIF.

*              l_str_final-cr_rea = l_str_bkpf-stgrd.  " reason for credit debit note
          l_str_final-customitem9 = l_str_bkpf-blart.
          IF lt_user_usnam IS NOT INITIAL.
*                CLEAR : ls_user_usnam.
            READ TABLE lt_user_usnam INTO DATA(ls_user_usnam) WITH KEY bname = l_str_bkpf-usnam BINARY SEARCH.
            IF sy-subrc EQ 0.
*                  CONCATENATE l_str_final-customitem30 '/ Posted by:' ls_user_usnam-name_first ' ' ls_user_usnam-name_last INTO
*                              l_str_final-customitem30 SEPARATED BY space.
            ENDIF.
          ENDIF.
*              IF lt_user_ppnam IS NOT INITIAL.
*                CLEAR : ls_user_usnam.
*                READ TABLE lt_user_ppnam INTO ls_user_usnam WITH KEY bname = l_str_bkpf-ppnam BINARY SEARCH.
*                IF sy-subrc EQ 0.
*                  CONCATENATE l_str_final-customitem30 '/ Parked by:' ls_user_usnam-name_first ' ' ls_user_usnam-name_last INTO
*                              l_str_final-customitem30 SEPARATED BY space.
*                ENDIF.
*              ENDIF.

          l_str_final-custom3 = l_str_bkpf-usnam.
          IF l_str_bkpf-bukrs = l_str_bseg-bukrs AND l_str_bkpf-belnr = l_str_bseg-belnr AND l_str_bkpf-gjahr = l_str_bseg-gjahr.
            l_str_final-purpose = 'OGST'.
            l_str_final-supplytype = 'P'.
            l_str_final-documentnumber = l_str_bkpf-xblnr.
            l_str_final-custom2 = l_str_bkpf-budat.
            l_str_final-customitem3 = l_str_bseg-prctr.
            l_str_final-documentdate = l_str_bkpf-bldat.
            CONCATENATE l_str_final-documentdate+6(2) '-' l_str_final-documentdate+4(2) '-' l_str_final-documentdate+0(4)
                    INTO l_str_final-documentdate.
            l_str_final-returnperiod = l_str_bkpf-bldat.
*

            IF l_str_bkpf-blart = lc_re OR l_str_bkpf-blart = lc_rc .
*                  CLEAR l_str_rbkp.
              CONSTANTS : lc_inv TYPE char3 VALUE 'INV',
                          lc_crn TYPE char3 VALUE 'CRN'.
              READ TABLE l_tab_rbkp INTO DATA(l_str_rbkp) WITH KEY  belnr = l_str_bkpf-awkey+0(10) gjahr = l_str_bkpf-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
                IF l_str_rbkp-xrech = 'X'.
                  l_str_final-documenttype = lc_inv ."INV'.
                ELSE.
                  l_str_final-documenttype = lc_crn."'CRN'.
                ENDIF.
              ENDIF.
*                  l_str_bseg_lifnr-bupla = l_str_rbkp-bupla.
            ENDIF.

            IF l_str_bkpf-stblg IS NOT INITIAL.
              l_str_final-documenttype = 'CAN'.
            ELSEIF l_str_rbkp-stblg IS NOT INITIAL.
              l_str_final-documenttype = 'CAN'.
            ENDIF.


            IF l_str_bkpf-blart = lc_kr .
              l_str_final-documenttype = lc_inv."'CRN'.
            ELSEIF l_str_bkpf-blart = lc_kg.
              l_str_final-documenttype = lc_crn."'CRN'.
            ENDIF.

*                CLEAR l_str_bseg_lifnr.
            READ TABLE l_tab_bseg_lifnr INTO DATA(l_str_bseg_lifnr) WITH KEY bukrs = l_str_bkpf-bukrs
                                                                      belnr = l_str_bkpf-belnr
                                                                      gjahr = l_str_bkpf-gjahr BINARY SEARCH .
            IF sy-subrc = 0.
              READ TABLE lt_crn INTO DATA(ls_crn) WITH KEY bukrs = l_str_bseg_lifnr-bukrs gjahr = l_str_bseg_lifnr-rebzj
              belnr = l_str_bseg_lifnr-rebzg BINARY SEARCH.
              IF sy-subrc = 0.
                l_str_final-referencedocumentnumber = ls_crn-xblnr.
                l_str_final-referencedocumentnumber = ls_crn-belnr.
*                    l_str_final-custom18 = ls_crn-belnr.
                l_str_final-referencedocumentdate = ls_crn-budat.
                l_str_final-custom9 = ls_crn-budat.
                CONCATENATE l_str_final-referencedocumentdate+6(2) '-' l_str_final-referencedocumentdate+4(2) '-'
                l_str_final-referencedocumentdate+0(4)
                    INTO l_str_final-referencedocumentdate.
                l_str_final-custom9 = l_str_final-referencedocumentdate.
              ENDIF.
              READ TABLE l_tab_rbkp_temp INTO l_str_rbkp_temp WITH KEY belnr = l_str_bkpf-awkey(10) bukrs =  l_str_bkpf-bukrs
                                                                       gjahr =  l_str_bkpf-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
                IF l_str_rbkp_temp-stblg IS NOT INITIAL.

                  l_str_final-referencedocumentnumber = l_str_rbkp_temp-stblg.
*
                  READ TABLE lt_rbkp INTO DATA(ls_rbkp) WITH KEY bukrs = l_str_bkpf-bukrs gjahr = l_str_rbkp_temp-stjah
                  belnr = l_str_rbkp_temp-stblg BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-referencedocumentdate = ls_rbkp-budat.
                    CONCATENATE l_str_final-referencedocumentdate+6(2) '-' l_str_final-referencedocumentdate+4(2) '-'
                    l_str_final-referencedocumentdate+0(4)
                        INTO l_str_final-referencedocumentdate.
                    l_str_final-custom9 = l_str_final-referencedocumentdate.
                    CONCATENATE ls_rbkp-belnr ls_rbkp-gjahr INTO DATA(key).
                    READ TABLE lt_miro_ref INTO DATA(ls_miro_ref) WITH KEY bukrs = l_str_bkpf-bukrs gjahr = ls_rbkp-gjahr awkey = key
                    BINARY SEARCH.

                    IF sy-subrc = 0.
                      l_str_final-referencedocumentnumber = ls_miro_ref-belnr.
                      l_str_final-custom8 = ls_miro_ref-belnr.
                    ENDIF.
                  ENDIF.
                ENDIF.
              ENDIF.

              l_str_final-customitem10 = l_str_bseg_lifnr-augbl.
              l_str_final-customitem1 = l_str_bseg_lifnr-augdt.
              l_str_final-custom5 = l_str_bseg_lifnr-lifnr.
*                  l_str_final-vendor = l_str_bseg_lifnr-lifnr.
*                  CLEAR l_str_lfa1.
              READ TABLE  l_tab_lfa1 INTO DATA(l_str_lfa1) WITH KEY lifnr = l_str_bseg_lifnr-lifnr BINARY SEARCH .
              IF l_str_lfa1-lifnr IS NOT INITIAL.
*                    l_str_final-custom11 = l_str_lfa1-lifnr.
*                    l_str_final-custom12 = l_str_lfa1-stcd3.
*                    l_str_final-custom13 = l_str_lfa1-name1.
              ELSE.
*                    l_str_final-custom15 = l_str_bseg_lifnr-kunnr.
*                    READ TABLE  l_tab_kna1 INTO l_str_kna1 WITH KEY kunnr = l_str_bseg_lifnr-kunnr BINARY SEARCH .
                IF sy-subrc = 0.
*                      l_str_final-custom16 = l_str_kna1-stcd3.
*                      l_str_final-custom17 = l_str_kna1-name1.
                ENDIF.
              ENDIF.

              IF sy-subrc = 0.
                READ TABLE  l_tab_lfa1 INTO l_str_lfa1 WITH KEY lifnr = l_str_bseg_lifnr-gst_part BINARY SEARCH .
                IF l_str_bseg_lifnr-gst_part IS NOT INITIAL.
                  l_str_final-custom5 = l_str_bseg_lifnr-gst_part.
                ENDIF.
                l_str_final-billfromgstin = l_str_lfa1-stcd3.
                l_str_final-billfromstatecode = l_str_lfa1-stcd3(2).
                l_str_final-billfromlegalname = l_str_lfa1-name1.
*                    CLEAR l_str_adrc.
                READ TABLE l_tab_adrc INTO DATA(l_str_adrc) WITH KEY addrnumber = l_str_lfa1-adrnr BINARY SEARCH.
                IF sy-subrc = 0.
                  CONCATENATE l_str_adrc-street l_str_adrc-str_suppl1 INTO l_str_final-billfromaddress1.
                  CONCATENATE l_str_final-billfromaddress1 l_str_adrc-str_suppl2 INTO l_str_final-billfromaddress1.
                  l_str_final-billfromaddress2 = l_str_adrc-str_suppl3.
                  l_str_final-billfrompincode = l_str_adrc-post_code1.

                  READ TABLE l_tab_adr6 INTO DATA(l_str_adr6) WITH KEY addrnumber = l_str_lfa1-adrnr BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-billtoemail = l_str_adr6-smtp_addr.
                  ENDIF.
*                      CLEAR wa_state.
*                      READ TABLE it_state INTO DATA(wa_state) WITH KEY region = l_str_adrc-region BINARY SEARCH.
*                      IF sy-subrc = 0.
                  l_str_final-billfromstatecode = l_str_adrc-region.
*                      ENDIF.
                ENDIF.
              ENDIF.
            ENDIF.
            CLEAR l_str_adrc.
            READ TABLE l_tab_val INTO DATA(l_str_val) WITH KEY bukrs = l_str_bkpf-bukrs gjahr = l_str_bkpf-gjahr belnr = l_str_bkpf-belnr
            BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-documentvalue = l_str_val-dmbtr.
            ENDIF.

*                CLEAR l_str_kna1.
            READ TABLE  l_tab_kna1 INTO DATA(l_str_kna1) WITH KEY kunnr = l_str_bseg_lifnr-kunnr BINARY SEARCH .
            IF sy-subrc = '0'.
              l_str_final-billfromgstin = l_str_kna1-stcd3.
              l_str_final-billfromstatecode = l_str_kna1-stcd3(2).
              l_str_final-billfromlegalname = l_str_kna1-name1.
*                  CLEAR wa_state.
*                  READ TABLE it_state INTO wa_state WITH KEY region = l_str_kna1-regio BINARY SEARCH.
*                  IF sy-subrc = 0.
*                    l_str_final-billfromstatecode = wa_state-gst_state_code.
*                  ELSE.
              l_str_final-billfromstatecode = l_str_kna1-regio.
*                  ENDIF.
            ENDIF.

            l_str_final-custom6 =  l_str_bseg_lifnr-werks.

*                CLEAR l_str_j_1bbranch.
            READ TABLE l_tab_j_1bbranch INTO DATA(l_str_j_1bbranch) WITH KEY bukrs =  l_str_bseg_lifnr-bukrs
                                                                     branch = l_str_bseg_lifnr-bupla BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-billtogstin  = l_str_j_1bbranch-gstin.
              l_str_final-branch  = l_str_j_1bbranch-branch.
              l_str_final-locationgstin  = l_str_j_1bbranch-gstin.
              l_str_final-billtolegalname   =   l_str_j_1bbranch-name.
*                  CLEAR l_str_bupadrc.
              READ TABLE l_tab_bupadrc INTO DATA(l_str_bupadrc) WITH KEY addrnumber = l_str_j_1bbranch-adrnr BINARY SEARCH.
              IF sy-subrc = 0.
                CONCATENATE l_str_bupadrc-street l_str_bupadrc-str_suppl1 INTO l_str_final-billtoaddress1.
*                    CONCATENATE l_str_bupadrc-str_suppl2 l_str_bupadrc-str_suppl3 INTO l_str_final-billtoaddress2.
                l_str_final-billtocity = l_str_bupadrc-city1.
                l_str_final-billtopincode = l_str_bupadrc-post_code1.
                l_str_final-billtostatecode = l_str_bupadrc-region.
                l_str_final-pos = l_str_bupadrc-region.
                IF l_str_final-billtogstin+0(2) IS NOT INITIAL.
                  l_str_final-billtostatecode = l_str_final-billtogstin+0(2).
                  l_str_final-pos = l_str_final-billtogstin+0(2).
                ENDIF.
              ENDIF.

            ELSE.
*                  CLEAR l_str_bseg_lifnr_plant.
              READ TABLE l_tab_bseg_lifnr_plant INTO DATA(l_str_bseg_lifnr_plant) WITH KEY
                                                                       bukrs = l_str_bkpf-bukrs
                                                                       belnr = l_str_bkpf-belnr
                                                                       gjahr = l_str_bkpf-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
*                    CLEAR l_str_t001w.
                READ TABLE l_tab_t001w INTO DATA(l_str_t001w) WITH KEY werks = l_str_bseg_lifnr_plant-werks BINARY SEARCH.
                IF sy-subrc = 0.

                ELSE.
*                      CLEAR l_str_j_1bbranch_plant.
                  READ TABLE l_tab_j_1bbranch_plant INTO DATA(l_str_j_1bbranch_plant) WITH KEY bukrs =  l_str_bkpf-bukrs
                                                                                     branch = l_str_bseg-bupla BINARY SEARCH.
                  l_str_t001w-j_1bbranch = l_str_bseg-bupla.
                ENDIF.

                IF sy-subrc = 0.
                  CLEAR l_str_j_1bbranch_plant.
                  READ TABLE l_tab_j_1bbranch_plant INTO l_str_j_1bbranch_plant WITH KEY bukrs =  l_str_bkpf-bukrs
                                                                                     branch = l_str_t001w-j_1bbranch BINARY SEARCH.

                  IF sy-subrc = 0.
                    l_str_final-billtogstin  = l_str_j_1bbranch_plant-gstin.
                    l_str_final-branch  = l_str_t001w-j_1bbranch.
                    l_str_final-locationgstin  = l_str_j_1bbranch_plant-gstin.
                    l_str_final-billtolegalname   = l_str_j_1bbranch_plant-name.
*                        CLEAR l_str_bupadrc_plant.
                    READ TABLE l_tab_bupadrc_plant INTO DATA(l_str_bupadrc_plant) WITH KEY addrnumber = l_str_j_1bbranch_plant-adrnr BINARY SEARCH.
                    IF sy-subrc = 0.
                      CONCATENATE l_str_bupadrc_plant-street l_str_bupadrc_plant-str_suppl1 INTO l_str_final-billtoaddress1.
                      CONCATENATE l_str_bupadrc_plant-str_suppl2 l_str_bupadrc_plant-str_suppl3 INTO l_str_final-billtoaddress2.
                      l_str_final-billtocity = l_str_bupadrc_plant-city1.
                      l_str_final-billtopincode = l_str_bupadrc_plant-post_code1.
                      l_str_final-billtostatecode = l_str_bupadrc_plant-region.
                      l_str_final-pos = l_str_bupadrc_plant-region.
                      l_str_final-billtostatecode = l_str_final-billtogstin+0(2).
                      l_str_final-pos = l_str_final-billtogstin+0(2).
                      IF l_str_bseg_lifnr_plant-plc_sup IS NOT INITIAL.
                        l_str_final-pos  = l_str_bseg_lifnr_plant-plc_sup.
                      ENDIF.
                    ENDIF.
                  ENDIF.
                ENDIF.
              ENDIF.
            ENDIF.

            l_str_final-company_code = l_str_bkpf-bukrs.

*                CLEAR l_str_rseg.
            IF l_str_bseg-txgrp IS NOT INITIAL.
              READ TABLE l_tab_rseg INTO DATA(l_str_rseg) WITH KEY belnr = l_str_bkpf-awkey+0(10) gjahr = l_str_bkpf-gjahr  ebeln = l_str_bseg-ebeln
              ebelp = l_str_bseg-ebelp buzei = l_str_bseg-txgrp BINARY SEARCH.
            ELSE.
              READ TABLE l_tab_rseg INTO l_str_rseg WITH KEY belnr = l_str_bkpf-awkey+0(10) gjahr = l_str_bkpf-gjahr  ebeln = l_str_bseg-ebeln
           ebelp = l_str_bseg-ebelp  BINARY SEARCH.

            ENDIF.
            IF sy-subrc = 0.
*                  l_str_final-customitem35 =  l_str_rseg-customs_val.  " Custom duty
              l_str_final-hsn = l_str_rseg-hsn_sac.
              l_str_final-quantity = l_str_rseg-menge.
              l_str_final-uqc = l_str_rseg-meins.
              l_str_final-itemname = l_str_rseg-matnr.
              l_str_final-itemdescription = l_str_rseg-sgtxt.

              IF l_str_rseg-kschl IS NOT INITIAL.
                CLEAR:l_str_final-quantity.
              ENDIF.
              READ TABLE lt_hsn INTO DATA(ls_hsn) WITH KEY kschl = l_str_rseg-kschl BINARY SEARCH.
              IF sy-subrc = 0.
                l_str_final-hsn = ls_hsn-steuc.
              ENDIF.

*                  CLEAR l_str_ekko.
              READ TABLE l_tab_ekko INTO DATA(l_str_ekko) WITH KEY ebeln = l_str_bseg-ebeln bukrs = l_str_rseg-bukrs BINARY SEARCH.
              IF sy-subrc = 0.
                l_str_final-doccurrencycode = l_str_ekko-waers.
*                    READ TABLE lt_cap INTO DATA(ls_cap) WITH KEY bsart = l_str_ekko-bsart BINARY SEARCH.
*                    IF sy-subrc = 0.
*                      l_str_final-itceligibility = 'cp'.
*                    ENDIF.
*                    l_str_final-con_no = l_str_ekko-konnr.
*                    READ TABLE lt_con INTO DATA(ls_con) WITH KEY ebeln =  l_str_ekko-konnr BINARY SEARCH.
*                    IF sy-subrc = 0.
*                      l_str_final-con_date = ls_con-bedat.
*                    ENDIF.
*                    IF l_str_ekko-bedat IS NOT INITIAL.
*                      PERFORM convert_date USING ls_con-bedat l_str_final-con_date.   " Contract date
*                    ENDIF.

*                    l_str_final-customitem20 = l_str_ekko-aedat.

                READ TABLE lt_cess INTO DATA(ls_cess) WITH KEY knumv = l_str_ekko-knumv BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-cessamount = ls_cess-kbetr.
                  l_str_final-itccessamount = ls_cess-kbetr.
                  DELETE lt_cess INDEX sy-tabix.
                ENDIF.
*                    l_str_final-customitem19 =  l_str_rseg-ebeln.
                l_str_final-itemserialnumber =  l_str_rseg-matnr.

*                    CLEAR:l_str_ekkn.
                READ TABLE l_tab_ekkn INTO DATA(l_str_ekkn) WITH KEY  ebeln = l_str_rseg-ebeln ebelp = l_str_rseg-ebelp BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-customitem7 = l_str_ekkn-sakto.  " G/L Account
                  l_str_final-customitem5 = l_str_ekkn-kostl. " Cost Center
                  READ TABLE lt_skat INTO ls_skat WITH KEY saknr = l_str_ekkn-sakto BINARY SEARCH.
*              " GL A/c Description
*********************** Upto here
                  IF sy-subrc = 0.
*                        l_str_final-customitem16 = ls_skat-txt50.
                  ENDIF.
                  IF l_str_ekkn-ps_psp_pnr IS NOT INITIAL.
                    IF l_str_final-customitem9 IS INITIAL.
                      READ TABLE l_tab_prps INTO l_str_prps WITH KEY  pspnr = l_str_ekkn-ps_psp_pnr BINARY SEARCH.
                      IF sy-subrc = 0.
                        l_str_final-customitem10 = l_str_prps-post1.  " WBS Element Desc
                      ENDIF.
                      " WBS Element

                      CALL FUNCTION 'CONVERSION_EXIT_ABPSP_OUTPUT'
                        EXPORTING
                          input  = l_str_prps-pspnr
                        IMPORTING
                          output = l_str_final-customitem9.

                      CONDENSE l_str_final-customitem9.
                    ENDIF.
                  ENDIF.
                ENDIF.

                READ TABLE lt_mbew INTO DATA(ls_mbew) WITH KEY matnr = l_str_rseg-matnr bwkey = l_str_rseg-werks BINARY SEARCH.
                IF sy-subrc = 0.
                  READ TABLE lt_t030 INTO DATA(ls_t030) WITH KEY bklas = ls_mbew-bklas BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-customitem7 = ls_t030-konts.  " G/L Account
                    READ TABLE lt_skat INTO ls_skat WITH KEY saknr = ls_t030-konts BINARY SEARCH.
*              " GL A/c Description
*********************** Upto here
                    IF sy-subrc = 0.
*                          l_str_final-customitem16 = ls_skat-txt50.
                    ENDIF.
                  ENDIF.
                ENDIF.
              ENDIF.
              IF l_str_rseg-kschl IS NOT INITIAL.
                IF l_str_rseg-ind = ''.
                  l_str_final-itceligibility = 'IS'.
                  READ TABLE lt_t685t INTO DATA(ls_t685t) WITH KEY kschl = l_str_rseg-kschl BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-itemdescription = ls_t685t-vtext.
                    l_str_final-itemname = ls_t685t-vtext.
                  ENDIF.
                  l_str_final-quantity = 1.
                ENDIF.
              ENDIF.
*                  CLEAR is_ekbe.
              READ TABLE it_ekbe INTO DATA(is_ekbe) WITH KEY  ebeln = l_str_rseg-ebeln ebelp = l_str_rseg-ebelp
              belnr = l_str_rseg-belnr BINARY SEARCH.
              IF sy-subrc = 0.
*                    l_str_final-customitem17 = is_ekbe-lfbnr.
*                    l_str_final-customitem18 = is_ekbe-budat.
                CLEAR:awkey.
                CONCATENATE is_ekbe-belnr is_ekbe-gjahr INTO awkey.
              ELSE.
                READ TABLE it_ekbe INTO is_ekbe WITH KEY  ebeln = l_str_rseg-ebeln ebelp = l_str_rseg-ebelp BINARY SEARCH.
                IF sy-subrc = 0.
*                      l_str_final-customitem17 = is_ekbe-lfbnr.
*                      l_str_final-customitem18 = is_ekbe-budat.
                  CLEAR:awkey.
                  CONCATENATE is_ekbe-belnr is_ekbe-gjahr INTO awkey.
                ENDIF.
              ENDIF.

              READ TABLE l_tab_eskn INTO DATA(l_str_eskn) WITH KEY packno = is_ekbe-packno zekkn = is_ekbe-zekkn
              BINARY SEARCH.
              IF sy-subrc = 0.
                l_str_final-customitem7 = l_str_eskn-sakto.  " G/L Account

                READ TABLE lt_skat INTO ls_skat WITH KEY saknr = l_str_eskn-sakto BINARY SEARCH.
*              " GL A/c Description
*********************** Upto here
                IF sy-subrc = 0.
*                      l_str_final-customitem16 = ls_skat-txt50.
                ENDIF.
              ENDIF.
*                  CLEAR l_str_ekpo1.
*                  DATA: lv_rcm TYPE ekpo-zzrcm_fcm.
*                  CLEAR:lv_rcm.
              READ TABLE l_tab_ekpo1 INTO DATA(l_str_ekpo1) WITH KEY ebeln = l_str_ekko-ebeln ebelp = l_str_bseg-ebelp BINARY SEARCH.
              IF sy-subrc = 0.
*                    lv_rcm = l_str_ekpo1-zzrcm_fcm.
                IF l_str_ekko-konnr IS NOT INITIAL.
*                      l_str_final-customitem28 = l_str_ekpo1-netwr.
                ENDIF.
                IF  l_str_final-itemdescription IS INITIAL.
                  l_str_final-itemdescription = l_str_ekpo1-txz01.
                ENDIF.
*                    CLEAR wa_essr.
                READ TABLE t_essr INTO DATA(wa_essr) WITH KEY ebeln = l_str_rseg-ebeln ebelp = l_str_rseg-ebelp BINARY SEARCH.
                IF sy-subrc = 0.
*                      CLEAR wa_esll1.
                  READ TABLE t_esll1 INTO DATA(wa_esll1) WITH KEY packno = wa_essr-packno BINARY SEARCH.
                  IF sy-subrc = 0.
*                        CLEAR wa_esll2.
                    READ TABLE t_esll2 INTO DATA(wa_esll2) WITH KEY packno = wa_esll1-sub_packno BINARY SEARCH.
                    IF sy-subrc = 0.
                      CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
                        EXPORTING
                          input  = wa_esll2-srvpos
                        IMPORTING
                          output = wa_esll2-srvpos.
                      l_str_final-itemname = wa_esll2-srvpos.
                    ENDIF.
                  ENDIF.
                ENDIF.
                IF l_str_final-itemdescription IS INITIAL.
                  l_str_final-itemdescription = l_str_ekpo1-txz01.
                ENDIF.
              ENDIF.
            ENDIF.
*                IF l_str_bkpf-tcode = lc_tcode3.
*                  READ TABLE lt_mbew INTO ls_mbew WITH KEY matnr = l_str_bseg-matnr bwkey = l_str_bseg-werks BINARY SEARCH.
*                  IF sy-subrc = 0.
*                    READ TABLE lt_t030 INTO ls_t030 WITH KEY bklas = ls_mbew-bklas BINARY SEARCH.
*                    IF sy-subrc = 0.
*                      l_str_final-customitem7 = ls_t030-konts.  " G/L Account
*                      READ TABLE lt_skat INTO ls_skat WITH KEY saknr = ls_t030-konts BINARY SEARCH.
**              " GL A/c Description
************************ Upto here
*                      IF sy-subrc = 0.
*                        l_str_final-customitem16 = ls_skat-txt50.
*                      ENDIF.
*                    ENDIF.
*                  ENDIF.
*                ENDIF.
*            endif.

*                CLEAR l_str_bseg_matnr.
            IF  l_str_final-itemname IS INITIAL.
*                  CLEAR l_str_makt.
              READ TABLE l_tab_makt INTO DATA(l_str_makt) WITH KEY matnr = l_str_bseg-matnr BINARY SEARCH.
              IF sy-subrc = 0.
                IF l_str_final-itemdescription IS INITIAL.
                  l_str_final-itemdescription = l_str_makt-maktx.
                  l_str_final-itemname = l_str_makt-matnr.
                ENDIF.
*                    CLEAR is_mara.
              ENDIF.
            ENDIF.
            l_str_final-custom1 = l_str_bkpf-belnr.
*                l_str_final-custom20  = l_str_bseg_lifnr-bupla.
            l_str_final-custom7 = l_str_bkpf-blart.
            l_str_final-customitem2 = l_str_bseg-mwskz.

            IF l_str_final-hsn IS INITIAL.
              IF l_str_bseg-hsn_sac IS INITIAL.
*                    CLEAR l_str_marc.
                IF l_str_final-hsn IS INITIAL.
                  READ TABLE l_tab_marc INTO DATA(l_str_marc) WITH KEY matnr = l_str_makt-matnr BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-hsn = l_str_marc-steuc..
                  ENDIF.
                ENDIF.
              ELSE.
                l_str_final-hsn = l_str_bseg-hsn_sac..
              ENDIF.
            ENDIF.

*                CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO DATA(l_str_bset) WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax = 'cgstamount'  BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-taxablevalue =  l_str_bset-hwbas.
              IF l_str_bset-shkzg = 'S'.
                l_str_final-cgstamount =  l_str_bset-hwste. "
              ELSE.
                l_str_final-cgstamount =  l_str_bset-hwste * -1.
                l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
              ENDIF.

              IF l_str_bset-kbetr IS NOT INITIAL.
                l_str_final-rate =   l_str_final-rate  + ( l_str_bset-kbetr /  10 ).
*                    l_str_final-customitem22 =   ( l_str_bset-kbetr /  10 ).  " cgstamount Rate
              ENDIF.

*                  IF l_str_bset-shkzg = 'H'.
**                    l_str_final-customitem29 = lc_cr.
*                  ELSE.
*                    l_str_final-customitem29 = lc_dr.
*                  ENDIF.

              DELETE  l_tab_bset INDEX sy-tabix.
              IF sy-subrc = 0.
                go_flag = 'X'.
              ENDIF.
              l_str_final-cgstamount = l_str_bset-hkont.
            ENDIF.

            CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax = 'sgstamount'  BINARY SEARCH.

            IF sy-subrc = 0.
              IF l_str_bset-shkzg = 'S'.
                l_str_final-sgstamount =  l_str_bset-hwste.
              ELSE.
                l_str_final-sgstamount =   l_str_bset-hwste * -1.
                l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
              ENDIF.

*                  IF l_str_bset-shkzg = 'H'.
*                    l_str_final-customitem29 = lc_cr.
*                  ELSE.
*                    l_str_final-customitem29 = lc_dr.
*                  ENDIF.

              IF  l_str_final-sgstamount < 0 .
              ENDIF.
              IF l_str_bset-kbetr IS NOT INITIAL.
                l_str_final-rate =   l_str_final-rate  +   ( l_str_bset-kbetr /  10 ).
*                    l_str_final-customitem23 =   ( l_str_bset-kbetr /  10 ).  " sgstamount Rate
              ENDIF.
              DELETE  l_tab_bset INDEX sy-tabix.
              IF sy-subrc = 0.
                go_flag = 'X'.
              ENDIF.
              l_str_final-sgstamount = l_str_bset-hkont.
            ENDIF.

            CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax = 'igstamount'   BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-taxablevalue =  l_str_bset-hwbas.
              IF l_str_bset-shkzg = 'S'.
                l_str_final-igstamount = l_str_bset-hwste.
              ELSE.
                l_str_final-igstamount =  l_str_bset-hwste * -1.
                l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
              ENDIF.

*                  IF l_str_bset-shkzg = 'H'.
*                    l_str_final-customitem29 = lc_cr.
*                  ELSE.
*                    l_str_final-customitem29 = lc_dr.
*                  ENDIF.

              IF l_str_bset-kbetr IS NOT INITIAL.
                l_str_final-rate =    ( l_str_bset-kbetr /  10 ).
*                    l_str_final-customitem24 =   ( l_str_bset-kbetr /  10 ).  " sgstamount Rate
              ENDIF.
              DELETE  l_tab_bset INDEX sy-tabix.
              IF sy-subrc = 0.
                go_flag = 'X'.
              ENDIF.
              IF l_str_bset-hkont = '2206552142'.
                go_miro = 'X'.
              ENDIF.
              l_str_final-igstamount = l_str_bset-hkont.
            ENDIF.

            CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax = 'RSGST'  BINARY SEARCH.

            IF sy-subrc = 0.
              IF l_str_bset-kbetr < 0.
                l_str_bset-kbetr = l_str_bset-kbetr * -1.
              ENDIF.

              l_str_final-reversecharge = 'Y'.

              DELETE  l_tab_bset INDEX sy-tabix.
              IF l_str_final-sgstamount IS INITIAL. "changes for rec RCM GL
                l_str_final-sgstamount = l_str_bset-hkont.
              ENDIF.
            ENDIF.

            CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax =  'RCGST' BINARY SEARCH..
            IF sy-subrc = 0.
              IF l_str_bset-kbetr < 0.
                l_str_bset-kbetr = l_str_bset-kbetr * -1.
              ENDIF.

              l_str_final-reversecharge = 'Y'.
              DELETE  l_tab_bset INDEX sy-tabix.
              IF l_str_final-cgstamount IS INITIAL."changes for rec RCM GL
                l_str_final-cgstamount = l_str_bset-hkont.
              ENDIF.
            ENDIF.

            CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax =  'RIGST'  BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-reversecharge = 'Y'.
              l_str_final-rate =     ( l_str_bset-kbetr /  10 ).
              DELETE  l_tab_bset INDEX sy-tabix.
              IF l_str_final-igstamount IS INITIAL."changes for rec RCM GL
                l_str_final-igstamount = l_str_bset-hkont.
              ENDIF.
            ENDIF.

            CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax =  'CESS_S' BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-cessamount = l_str_bset-hwste.
              l_str_final-cessrate = l_str_bset-kbetr / 10.
              l_str_final-itccessamount = l_str_bset-hwste.
            ENDIF.

            CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax =  'CESS_A' BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-cessnonadvaloremamount = l_str_bset-hwste.
              l_str_final-cessnonadvaloremrate = l_str_bset-kbetr / 10.
            ENDIF.

            CLEAR:l_str_bset.
            READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                           belnr = l_str_bseg-belnr
                                                           gjahr = l_str_bseg-gjahr
                                                           taxps = l_str_bseg-taxps
                                                           gsp_tax = 'JIMD' BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-taxablevalue =  l_str_bset-hwbas.
              IF l_str_bset-shkzg = 'S'.
                l_str_final-igstamount = l_str_bset-hwste.
              ELSE.
                l_str_final-igstamount =  l_str_bset-hwste * -1.
                l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
              ENDIF.
              IF  l_str_final-igstamount < 0 .
              ENDIF.
              l_str_final-rate =  ( l_str_bset-kbetr /  10 ).
              CONSTANTS lc_boe TYPE char3 VALUE 'BOE'.
              CONSTANTS lc_impg TYPE char4 VALUE 'IMPG'.
              CONSTANTS lc_imps TYPE char4 VALUE 'IMPS'.
              l_str_final-documenttype = lc_boe ."'BOE'.
              IF l_str_final-hsn(2) = '99'.
                l_str_final-transactiontype = lc_imps."'IMPS'.
              ELSE.
                l_str_final-transactiontype = lc_impg."'IMPG'.
              ENDIF.
              DELETE  l_tab_bset INDEX sy-tabix.
              l_str_final-igstamount = l_str_bset-hkont.
              IF sy-subrc = 0.
                go_flag = 'X'.
              ENDIF.
              IF l_str_bset-hkont = lc_gl5.
                go_miro = 'X'.
              ENDIF.
            ENDIF.
            IF l_str_final-igstamount IS INITIAL AND   l_str_final-sgstamount IS INITIAL AND   l_str_final-cgstamount IS INITIAL.
              READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                             belnr = l_str_bseg-belnr
                                                             gjahr = l_str_bseg-gjahr
                                                             taxps = l_str_bseg-taxps
                                                             gsp_tax = ''
                                                             mwskz = l_str_bseg-mwskz
                                                             ktosl = 'NVV'
                                                             kschl = 'ZITR'   BINARY SEARCH.
              IF sy-subrc = 0.
                DELETE  l_tab_bset INDEX sy-tabix.
                IF sy-subrc = 0.
                  go_flag = 'X'.
                ENDIF.
                IF l_str_bset-shkzg = 'S'.
*                    L_STR_BSET-HWSTE = L_STR_BSET-HWSTE.
                ELSE.
                  l_str_bset-hwste =  l_str_bset-hwste * -1.
                  l_str_bset-hwbas = l_str_bset-hwbas * -1.
                ENDIF.

*                READ TABLE lt_set INTO DATA(ls_set) INDEX 1.

*                IF l_str_bset-ktosl = 'NVV'.
*                  l_str_final-taxablevalue =  l_str_bset-hwbas.
*                  IF l_str_final-pos = l_str_final-billfromstatecode.
*
*                    IF ls_set-from IS NOT INITIAL AND ls_set-from GE sy-datum.
*                      l_str_final-cgstamount = l_str_bset-hwste.
*                      l_str_final-sgstamount = l_str_bset-hwste .
*                      l_str_final-rate =  ( l_str_bset-kbetr / 10 ) .
*                    ELSE.
*                      l_str_final-cgstamount = l_str_bset-hwste / 2.
*                      l_str_final-sgstamount = l_str_bset-hwste / 2.
*                      l_str_final-taxablevalue =   l_str_final-taxablevalue / 2.
*                      l_str_final-rate =  ( l_str_bset-kbetr / 10 ) * 2.
*                    ENDIF.
*                  ELSE.
*                    IF ls_set-from IS NOT INITIAL AND ls_set-from GE sy-datum.
*                      l_str_final-igstamount = l_str_bset-hwste .
*                      l_str_final-rate = ( l_str_bset-kbetr / 10 ) .
*                    ELSE.
*                      IF l_str_bset-kbetr = '180.00' OR l_str_bset-kbetr = '120.00'
*                      OR l_str_bset-kbetr = '50.00' OR l_str_bset-kbetr = '28.00'.
*                        l_str_final-igstamount = l_str_bset-hwste .
*                        l_str_final-rate = ( l_str_bset-kbetr / 10 ) .
*                      ELSE.
*                        l_str_final-igstamount = l_str_bset-hwste .
*                        l_str_final-taxablevalue =   l_str_final-taxablevalue / 2.
*                        l_str_final-rate = ( l_str_bset-kbetr / 10 ) * 2 .
*                      ENDIF.
*                    ENDIF.
*                  ENDIF.
*                ELSE.
*                ENDIF.
                l_str_final-itceligibility = 'NO'.
              ENDIF.
            ENDIF.

            " Binary search not used as this condition only goes for very exceptional document without TXGRP mapping.
            "same table used with binary data search with diff. where clause.

            IF go_flag = '' .
              READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                             belnr = l_str_bseg-belnr
                                                             gjahr = l_str_bseg-gjahr
                                                             mwskz = l_str_bseg-mwskz
                                                             ktosl = 'JIC' .
              IF sy-subrc = 0.
                l_str_final-taxablevalue =  l_str_bset-hwbas.
                IF l_str_bset-shkzg = 'S'.
                  l_str_final-cgstamount =  l_str_bset-hwste.
                ELSE.
                  l_str_final-cgstamount =  l_str_bset-hwste * -1.
                  l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
                ENDIF.

                l_str_final-rate =   l_str_final-rate  + ( l_str_bset-kbetr /  10 ).
                DELETE  l_tab_bset INDEX sy-tabix.
                l_str_final-cgstamount = l_str_bset-hkont.
              ENDIF.

              READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                             belnr = l_str_bseg-belnr
                                                             gjahr = l_str_bseg-gjahr
                                                             mwskz = l_str_bseg-mwskz
                                                             ktosl = 'JIS'  .
              IF sy-subrc = 0.
                l_str_final-taxablevalue =  l_str_bset-hwbas.
                IF l_str_bset-shkzg = 'S'.
                  l_str_final-sgstamount =  l_str_bset-hwste.
                ELSE.
                  l_str_final-sgstamount =  l_str_bset-hwste * -1.
                  l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
                ENDIF.

                l_str_final-rate =   l_str_final-rate  + ( l_str_bset-kbetr /  10 ).
                DELETE  l_tab_bset INDEX sy-tabix.
                l_str_final-sgstamount = l_str_bset-hkont.
              ENDIF.
*
              CLEAR l_str_bset.
              READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                             belnr = l_str_bseg-belnr
                                                             gjahr = l_str_bseg-gjahr
                                                             mwskz = l_str_bseg-mwskz
                                                             ktosl = 'JII'  .
              IF sy-subrc = 0.
                l_str_final-taxablevalue =  l_str_bset-hwbas.
                IF l_str_bset-shkzg = 'S'.
                  l_str_final-igstamount =  l_str_bset-hwste.
                ELSE.
                  l_str_final-igstamount =  l_str_bset-hwste * -1.
                  l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
                ENDIF.
                l_str_final-rate =    ( l_str_bset-kbetr /  10 ).
                DELETE  l_tab_bset INDEX sy-tabix.
                l_str_final-igstamount = l_str_bset-hkont.
              ENDIF.

              READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                             belnr = l_str_bseg-belnr
                                                             gjahr = l_str_bseg-gjahr
                                                             mwskz = l_str_bseg-mwskz
                                                             ktosl = 'JIR'.
              IF sy-subrc = 0.
                l_str_final-taxablevalue =  l_str_bset-hwbas.
                IF l_str_bset-shkzg = 'S'.
                  l_str_final-igstamount =  l_str_bset-hwste.
                ELSE.
                  l_str_final-igstamount =  l_str_bset-hwste * -1.
                  l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
                ENDIF.
                l_str_final-reversecharge = 'Y'.
                l_str_final-rate =    ( l_str_bset-kbetr /  10 ).
                DELETE  l_tab_bset INDEX sy-tabix.
                l_str_final-igstamount = l_str_bset-hkont.
              ENDIF.

              READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                             belnr = l_str_bseg-belnr
                                                             gjahr = l_str_bseg-gjahr
                                                             mwskz = l_str_bseg-mwskz
                                                             ktosl = 'JOI'  .
              IF sy-subrc = 0.
                l_str_final-taxablevalue =  l_str_bset-hwbas.
                IF l_str_bset-shkzg = 'S'.
                  l_str_final-igstamount =  l_str_bset-hwste.
                ELSE.
                  l_str_final-igstamount =  l_str_bset-hwste * -1.
                  l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
                ENDIF.

                l_str_final-reversecharge = 'Y'.
                l_str_final-rate =    ( l_str_bset-kbetr /  10 ).
                DELETE  l_tab_bset INDEX sy-tabix.
                l_str_final-igstamount = l_str_bset-hkont.
              ENDIF.

              READ TABLE l_tab_bset INTO l_str_bset WITH KEY bukrs = l_str_bseg-bukrs
                                                             belnr = l_str_bseg-belnr
                                                             gjahr = l_str_bseg-gjahr
                                                             ktosl = 'JIM'
                                                             mwskz = l_str_bseg-mwskz.
              IF sy-subrc = 0.
                l_str_final-taxablevalue =  l_str_bset-hwbas.
                IF l_str_bset-shkzg = 'S'.
                  l_str_final-igstamount =  l_str_bset-hwste.
                ELSE.
                  l_str_final-igstamount =  l_str_bset-hwste * -1.
                  l_str_final-taxablevalue =  l_str_bset-hwbas * -1.
                ENDIF.

                l_str_final-rate =    ( l_str_bset-kbetr /  10 ).
                DELETE  l_tab_bset INDEX sy-tabix.
                l_str_final-igstamount = l_str_bset-hkont.

                l_str_final-documenttype = lc_boe ."'BOE'.
                IF l_str_final-hsn(2) = '99'.
                  l_str_final-transactiontype = lc_imps."'IMPS'.
                ELSE.
                  l_str_final-transactiontype = lc_impg."'IMPG'.
                ENDIF.
              ENDIF.
              IF l_str_bset-hkont = lc_gl5.
                go_miro = 'X'.
              ENDIF.
            ENDIF.

*            READ TABLE l_tab_makt INTO l_str_makt WITH KEY matnr = l_str_bseg_matnr-matnr BINARY SEARCH.
*            IF sy-subrc = 0.
*              l_str_final-itemdescription = l_str_makt-maktx.
*              l_str_final-itemname = l_str_makt-matnr.
*
*            ENDIF.

            IF l_str_final-quantity IS INITIAL.
              IF l_str_bseg-ktosl = 'FR2' OR l_str_bseg-ktosl = 'ZRY'.
              ELSE.
                IF l_str_rseg-kschl IS NOT INITIAL.
                ELSE.
                  l_str_final-quantity = l_str_bseg-menge.
                  l_str_final-uqc = l_str_bseg-meins.
                ENDIF.
              ENDIF.
            ENDIF.
            READ TABLE it_uom INTO DATA(ls_uom) WITH KEY sap_uom = l_str_final-uqc BINARY SEARCH.
            IF sy-subrc = 0..
              l_str_final-uqc  = ls_uom-gst_uom.
            ELSE.
              l_str_final-uqc = 'OTH'.
            ENDIF.
*
            IF l_str_final-cgstamount IS NOT INITIAL AND l_str_final-sgstamount IS NOT INITIAL.
              CLEAR l_str_final-igstamount.
            ENDIF.
*
            IF l_str_bkpf-tcode = 'FB08'.
              l_str_final-documenttype = lc_crn." 'CRN'.
            ENDIF.

            IF l_str_final-documenttype = lc_crn.
              IF l_str_final-taxablevalue GE 0.
                l_str_final-taxablevalue = l_str_final-taxablevalue * -1.
              ENDIF.
              IF l_str_final-igstamount GE 0.
                l_str_final-igstamount = l_str_final-igstamount * -1.
              ENDIF.
              IF l_str_final-cgstamount GE 0.
                l_str_final-cgstamount = l_str_final-cgstamount * -1.
              ENDIF.
              IF l_str_final-sgstamount GE 0.
                l_str_final-sgstamount = l_str_final-sgstamount * -1.
              ENDIF..
              IF l_str_final-rate < 0.
                l_str_final-rate = l_str_final-rate  * -1.
              ENDIF.
              IF l_str_final-documentvalue GE 0.
                l_str_final-documentvalue = l_str_final-documentvalue  * -1.
              ENDIF.
            ENDIF.

            IF l_str_final-reversecharge = 'Y'.
              IF l_str_final-igstamount < 0.
                IF l_str_final-taxablevalue > 0.
                  l_str_final-taxablevalue = l_str_final-taxablevalue * -1.
                ENDIF.
              ENDIF.

              IF l_str_final-cgstamount < 0.
                IF l_str_final-taxablevalue > 0.
                  l_str_final-taxablevalue = l_str_final-taxablevalue * -1.
                ENDIF.
              ENDIF.

              IF l_str_final-igstamount > 0.
                IF l_str_final-taxablevalue < 0.
                  l_str_final-taxablevalue = l_str_final-taxablevalue * -1.
                ENDIF.
              ENDIF.

              IF l_str_final-cgstamount > 0.
                IF l_str_final-taxablevalue < 0.
                  l_str_final-taxablevalue = l_str_final-taxablevalue * -1.
                ENDIF.
              ENDIF.

            ENDIF.

            IF l_str_final-reversecharge = ''.
              IF l_str_final-igstamount < 0 AND l_str_final-taxablevalue > 0.
                l_str_final-taxablevalue = l_str_final-taxablevalue * -1.
              ENDIF.

              IF l_str_final-cgstamount < 0 AND l_str_final-taxablevalue > 0.
                l_str_final-taxablevalue = l_str_final-taxablevalue * -1.
              ENDIF.
            ENDIF.

            IF l_str_final-billfromgstin = 'URD'.
              CLEAR:l_str_final-billfromgstin.
            ENDIF.

            DATA: ls_bsec LIKE LINE OF lt_bsec.
            CLEAR ls_bsec.
            READ TABLE lt_bsec INTO ls_bsec WITH KEY bukrs = l_str_bkpf-bukrs belnr = l_str_bkpf-belnr gjahr = l_str_bkpf-gjahr BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-billfromgstin = ls_bsec-stcd3.
              l_str_final-billfromstatecode = ls_bsec-stcd3(2).
              l_str_final-billfromlegalname = ls_bsec-name1.
              l_str_final-custom5 = 'One Time Vendor'.
              CONCATENATE ls_bsec-stras '' INTO l_str_final-billfromaddress1.
              l_str_final-billfrompincode = ls_bsec-pstlz.
              l_str_final-billfromcity = ls_bsec-ort01.
*              CLEAR wa_state1.
*              READ TABLE it_state INTO wa_state1 WITH KEY region = ls_bsec-regio BINARY SEARCH.
*              IF sy-subrc = 0.
              l_str_final-billfromstatecode = ls_bsec-regio.
*              ENDIF.

              IF l_str_bkpf-blart = 'KT'.
                l_str_final-documentnumber = ls_bsec-bkref.
              ENDIF.
            ENDIF.
            CONSTANTS lc_b2b TYPE char3 VALUE 'B2B'.
            CONSTANTS lc_b2c TYPE char3 VALUE 'B2C'.

            IF l_str_final-transactiontype IS INITIAL.
              IF l_str_final-billfromgstin IS NOT INITIAL.
                l_str_final-transactiontype = lc_b2b."'B2B'.
                IF l_str_final-billfromgstin = 'URP'.
                  l_str_final-transactiontype = lc_b2c."'B2C'.
                ENDIF.
              ELSE.
                l_str_final-transactiontype = lc_b2c."'B2C'.
              ENDIF.
            ENDIF.
            IF l_str_final-itceligibility <> 'NO'.
              IF l_str_final-itcsgstamount IS INITIAL.
                l_str_final-itcsgstamount    = l_str_final-sgstamount.
              ENDIF.
              IF   l_str_final-itccgstamount   IS INITIAL.
                l_str_final-itccgstamount  = l_str_final-cgstamount.
              ENDIF.
              IF l_str_final-itcigstamount IS INITIAL.
                l_str_final-itcigstamount  = l_str_final-igstamount.
              ENDIF.
              IF l_str_final-itccessamount IS INITIAL.
                l_str_final-itccessamount  = l_str_final-cessamount.
              ENDIF.
            ENDIF.

            CONDENSE l_str_final-documentnumber NO-GAPS.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]_' IN l_str_final-billfromcity WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billfromcity WITH space.
            CONDENSE l_str_final-billfromcity NO-GAPS.
*            IF p_rad3 = 'X'.
*              REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-itemdescription WITH space.
*
*              REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-itemdescription WITH space.
*              CONDENSE l_str_final-itemdescription.
*            ENDIF.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-billfromlegalname WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billfromlegalname WITH space.
            CONDENSE l_str_final-billfromlegalname.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-billfromaddress1 WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billfromaddress1 WITH space.
            CONDENSE l_str_final-billfromaddress1.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-billfromaddress2 WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billfromaddress2 WITH space.
            CONDENSE l_str_final-billfromaddress2.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-billtocity WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtocity WITH space.
            CONDENSE l_str_final-billtocity.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-billtolegalname WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtolegalname WITH space.
            CONDENSE l_str_final-billtolegalname.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-billtoaddress1 WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtoaddress1 WITH space.
            CONDENSE l_str_final-billtoaddress1.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-billtoaddress2 WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtoaddress2 WITH space.
            CONDENSE l_str_final-billtoaddress2.

            REPLACE ALL OCCURRENCES OF REGEX '["\|!@$%^*():;.""`~?<>+]' IN l_str_final-billtocity WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtocity WITH space.
            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-hsn WITH space.
            CONDENSE l_str_final-billtocity.
            CONDENSE l_str_final-hsn NO-GAPS.

            l_str_final-serialnumber = l_str_bseg-txgrp.
            CONDENSE l_str_final-quantity.

            READ TABLE l_tab_bseg_tcs INTO DATA(ls_bseg_tcs) WITH KEY  belnr = l_str_bkpf-belnr  bukrs = l_str_bkpf-bukrs  gjahr = l_str_bkpf-gjahr BINARY SEARCH.
            IF sy-subrc = 0.
              l_str_final-othercharges = ls_bseg_tcs-dmbtr.
            ENDIF.

            IF l_str_bkpf-tcode = 'FB08'.
              l_str_final-documenttype = lc_crn."'CRN'.
            ENDIF.

            l_str_final-custom4 = l_str_bkpf-gjahr.
            l_str_final-custom9 = l_str_bkpf-bukrs.

            CONCATENATE l_str_bkpf-budat+4(2) l_str_bkpf-budat(4) INTO l_str_final-returnperiod.

            IF l_str_bset-mwskz IS NOT INITIAL.
              l_str_final-customitem2 = l_str_bset-mwskz.
            ELSE.
              l_str_final-customitem2 = l_str_bseg-mwskz.
            ENDIF.
            IF l_str_final-documenttype = ''.
              IF l_str_final-taxablevalue < 0.
                l_str_final-documenttype = lc_crn."'CRN'.
              ELSE.
                l_str_final-documenttype = lc_inv."'INV'.
              ENDIF.
            ENDIF.
            IF l_str_final-transactiontype = lc_impg."'IMPG'..

            ENDIF.

*            IF isd1 = 'X'.
*              l_str_final-itceligibility = 'IS'.
*            ENDIF.

            IF l_str_final-hsn IS INITIAL.
              READ TABLE l_tab_rseg INTO l_str_rseg WITH KEY belnr = l_str_bkpf-awkey+0(10) gjahr = l_str_bkpf-gjahr BINARY SEARCH.
              IF sy-subrc = 0 AND l_str_rseg-hsn_sac IS NOT INITIAL..
                l_str_final-hsn = l_str_rseg-hsn_sac.
              ELSE.
*                CLEAR l_str_ekpo.
                READ TABLE l_tab_ekpo1 INTO DATA(l_str_ekpo) WITH KEY ebeln = l_str_rseg-ebeln  BINARY SEARCH.
                l_str_final-hsn =  l_str_ekpo-j_1bnbm.
              ENDIF.
            ENDIF.
*            IF l_str_final-customitem19 IS INITIAL.
*              READ TABLE l_tab_rseg INTO l_str_rseg WITH KEY belnr = l_str_bkpf-awkey+0(10) gjahr = l_str_bkpf-gjahr BINARY SEARCH.
*              IF sy-subrc = 0.
*                l_str_final-customitem19 = l_str_rseg-ebeln.
*                l_str_final-itemname = l_str_rseg-matnr.
*                READ TABLE l_tab_makt INTO l_str_makt WITH KEY matnr = l_str_rseg-matnr BINARY SEARCH.
*                IF sy-subrc = 0.
*                  l_str_final-itemdescription = l_str_makt-maktx.""product.desc
*                ENDIF.
*                READ TABLE it_ekbe INTO is_ekbe WITH KEY  ebeln = l_str_rseg-ebeln ebelp = l_str_rseg-ebelp BINARY SEARCH.
*                IF sy-subrc = 0.
*                  l_str_final-customitem17 = is_ekbe-belnr.
*                  l_str_final-customitem18 = is_ekbe-budat.
*                  CLEAR:awkey.
*                  CONCATENATE is_ekbe-belnr is_ekbe-gjahr INTO awkey.
*                ENDIF.
*                READ TABLE lt_mbew INTO ls_mbew WITH KEY matnr = l_str_rseg-matnr bwkey = l_str_rseg-werks BINARY SEARCH.
*                IF sy-subrc = 0.
*                  READ TABLE lt_t030 INTO ls_t030 WITH KEY bklas = ls_mbew-bklas BINARY SEARCH.
*                  IF sy-subrc = 0.
*                    l_str_final-customitem7 = ls_t030-konts.  " G/L Account
*                    READ TABLE lt_skat INTO ls_skat WITH KEY saknr = ls_t030-konts.
**              " GL A/c Description
************************ Upto here
*                    IF sy-subrc = 0.
*                      l_str_final-customitem16 = ls_skat-txt50.
*                    ENDIF.
*                  ENDIF.
*                ELSE.
*                  IF l_str_final-customitem7 IS INITIAL.
*                    l_str_final-customitem7 =  l_str_bseg-hkont.
*                  ENDIF.
*                ENDIF.
*              ENDIF.
*              IF l_str_rseg-kschl IS NOT INITIAL.
*                IF l_str_rseg-ind = ''.
*                  l_str_final-itceligibility = 'IS'.
*                  READ TABLE lt_t685t INTO ls_t685t WITH KEY kschl = l_str_rseg-kschl BINARY SEARCH.
*                  IF sy-subrc = 0.
*                    l_str_final-itemdescription = ls_t685t-vtext.
*                    l_str_final-itemname = ls_t685t-vtext.
*                  ENDIF.
*                  l_str_final-quantity = 1.
*                ENDIF.
*              ENDIF.
*            ENDIF.
*            IF l_str_bseg_lifnr-lifnr IS INITIAL AND l_str_bseg_lifnr-kunnr IS INITIAL.
*              IF l_str_bseg-bschl = '50' AND p_entry IS INITIAL.
*                CLEAR : l_str_final,l_str_bset,l_str_bseg_matnr,l_str_makt,
*                 l_str_bseg_lifnr,l_str_lfa1,l_str_adrc,l_str_bseg,l_str_j_1bbranch,l_str_bupadrc,l_str_bseg.
*                CLEAR:l_str_ekpo.
*                CONTINUE.
*              ELSEIF l_str_bseg-bschl = '40'.
*
*              ENDIF.
*            ENDIF.

            IF  l_str_bseg-mwskz IS INITIAL ..
              IF l_str_bkpf-blart = lc_re OR l_str_bkpf-blart = lc_rc.
              ELSE.
                IF  l_str_final-cgstamount IS INITIAL AND l_str_final-sgstamount IS INITIAL
                                                      AND l_str_final-igstamount IS INITIAL.
                  READ TABLE it_gl INTO DATA(ls_gl1) WITH KEY hkont = l_str_bseg-hkont ztax = 'igstamount' BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-igstamount = l_str_bseg-dmbtr.
                    IF l_str_bseg-shkzg = 'H'.
                      l_str_final-igstamount = l_str_final-igstamount * -1.
                      l_str_final-documenttype = lc_crn.
                      l_str_final-igstamount = l_str_bseg-hkont.
                    ENDIF.
                  ENDIF.
                  READ TABLE it_gl INTO ls_gl1 WITH KEY hkont = l_str_bseg-hkont ztax = 'cgstamount' BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-cgstamount = l_str_bseg-dmbtr.
                    IF l_str_bseg-shkzg = 'H'.
                      l_str_final-cgstamount = l_str_final-cgstamount * -1.
                      l_str_final-documenttype = lc_crn.
                      l_str_final-cgstamount = l_str_bseg-hkont.
                    ENDIF.
                  ENDIF.
                  READ TABLE it_gl INTO ls_gl1 WITH KEY hkont = l_str_bseg-hkont ztax = 'sgstamount' BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-sgstamount = l_str_bseg-dmbtr.
                    IF l_str_bseg-shkzg = 'H'.
                      l_str_final-sgstamount = l_str_final-sgstamount * -1.
                      l_str_final-documenttype = lc_crn.
                      l_str_final-sgstamount = l_str_bseg-hkont.
                    ENDIF.
                  ENDIF.
                  IF l_str_final-documenttype = lc_crn.
                    IF l_str_final-taxablevalue GE 0.
                      l_str_final-taxablevalue = l_str_final-taxablevalue * -1.
                    ENDIF.
                    IF l_str_final-igstamount GE 0.
                      l_str_final-igstamount = l_str_final-igstamount * -1.
                    ENDIF.
                    IF l_str_final-cgstamount GE 0.
                      l_str_final-cgstamount = l_str_final-cgstamount * -1.
                    ENDIF.
                    IF l_str_final-sgstamount GE 0.
                      l_str_final-sgstamount = l_str_final-sgstamount * -1.
                    ENDIF..
                    IF l_str_final-rate < 0.
                      l_str_final-rate = l_str_final-rate  * -1.
                    ENDIF.
                    IF l_str_final-documentvalue GE 0.
                      l_str_final-documentvalue = l_str_final-documentvalue  * -1.
                    ENDIF.
                  ENDIF.
                ENDIF.
              ENDIF.
            ENDIF.
            CONDENSE l_str_final-rate.
            IF l_str_final-documentnumber CS 'ISD'.
              l_str_final-taxpayertype = 'ISD'.
              l_str_final-transactiontype = 'ISD'.
              IF l_str_bseg-bupla IS NOT INITIAL.
*                  CLEAR ls_gl.
                READ TABLE it_gl INTO DATA(ls_gl_account) WITH KEY hkont = l_str_bseg-hkont ztax = 'igstamount' BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-igstamount = l_str_bseg-dmbtr.
                  l_str_final-itcigstamount = l_str_bseg-dmbtr.
                  l_str_final-igstamount = l_str_bseg-hkont.
                ENDIF.
                CLEAR ls_gl_account.
                READ TABLE it_gl INTO ls_gl_account WITH KEY hkont = l_str_bseg-hkont ztax = 'cgstamount' BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-cgstamount = l_str_bseg-dmbtr.
                  l_str_final-itccgstamount = l_str_bseg-dmbtr.
                  l_str_final-cgstamount = l_str_bseg-hkont.
                ENDIF.
                READ TABLE it_gl INTO ls_gl_account WITH KEY hkont = l_str_bseg-hkont ztax = 'sgstamount' BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-sgstamount = l_str_bseg-dmbtr.
                  l_str_final-itcsgstamount = l_str_bseg-dmbtr.
                  l_str_final-sgstamount = l_str_bseg-hkont.
                ENDIF.
              ENDIF.

              CLEAR l_str_j_1bbranch_plant.
              READ TABLE l_tab_j_1bbranch_plant INTO l_str_j_1bbranch_plant WITH KEY bukrs =  l_str_bkpf-bukrs
                                                                                 branch = l_str_bseg-bupla BINARY SEARCH.

              IF sy-subrc = 0.
                l_str_final-billtogstin  = l_str_j_1bbranch_plant-gstin.
                l_str_final-branch  = l_str_t001w-j_1bbranch.
                l_str_final-locationgstin  = l_str_j_1bbranch_plant-gstin.
                l_str_final-billtolegalname   = l_str_j_1bbranch_plant-name.
                CLEAR l_str_bupadrc_plant.
                READ TABLE l_tab_bupadrc_plant INTO l_str_bupadrc_plant WITH KEY addrnumber = l_str_j_1bbranch_plant-adrnr BINARY SEARCH.
                IF sy-subrc = 0.
                  CONCATENATE l_str_bupadrc_plant-street l_str_bupadrc_plant-str_suppl1 INTO l_str_final-billtoaddress1.
                  CONCATENATE l_str_bupadrc_plant-str_suppl2 l_str_bupadrc_plant-str_suppl3 INTO l_str_final-billtoaddress2.
                  l_str_final-billtocity = l_str_bupadrc_plant-city1.
                  l_str_final-billtopincode = l_str_bupadrc_plant-post_code1.
                  l_str_final-billtostatecode = l_str_bupadrc_plant-region.
                  l_str_final-pos = l_str_bupadrc_plant-region.
                  l_str_final-billtostatecode = l_str_final-billtogstin+0(2).
                  l_str_final-pos = l_str_final-billtogstin+0(2).
                  IF l_str_bseg_lifnr_plant-plc_sup IS NOT INITIAL.
                    l_str_final-pos  = l_str_bseg_lifnr_plant-plc_sup.
                  ENDIF.
                ENDIF.
                l_str_final-documentnumber = l_str_bseg-zuonr.
              ENDIF.
              READ TABLE l_tab_bseg_50 INTO DATA(l_str_bseg_50) WITH KEY bukrs = l_str_bkpf-bukrs gjahr = l_str_bkpf-gjahr
              belnr = l_str_bseg-belnr BINARY SEARCH.
              IF sy-subrc = 0.
                READ TABLE l_tab_j_1bbranch_plant INTO l_str_j_1bbranch_plant WITH KEY bukrs =  l_str_bkpf-bukrs
                                                                                 branch = l_str_bseg_50-bupla BINARY SEARCH.

                IF sy-subrc = 0.
                  l_str_final-billfromgstin  = l_str_j_1bbranch_plant-gstin.

                  l_str_final-billfromlegalname   = l_str_j_1bbranch_plant-name.
                  CLEAR l_str_bupadrc_plant.
                  READ TABLE l_tab_bupadrc_plant INTO l_str_bupadrc_plant WITH KEY addrnumber = l_str_j_1bbranch_plant-adrnr BINARY SEARCH.
                  IF sy-subrc = 0.
                    CONCATENATE l_str_bupadrc_plant-street l_str_bupadrc_plant-str_suppl1 INTO l_str_final-billfromaddress1.
                    CONCATENATE l_str_bupadrc_plant-str_suppl2 l_str_bupadrc_plant-str_suppl3 INTO l_str_final-billfromaddress2.
                    l_str_final-billfromcity = l_str_bupadrc_plant-city1.
                    l_str_final-billfrompincode = l_str_bupadrc_plant-post_code1.
                    l_str_final-billfromstatecode = l_str_bupadrc_plant-region.
                    l_str_final-billfromstatecode = l_str_final-billtogstin+0(2).
                  ENDIF.
                  l_str_final-documentnumber = l_str_bseg-zuonr.
                ENDIF.
              ENDIF.
              l_str_final-transactiontype = lc_b2b.
              IF l_str_bseg-bschl = '50'." and p_entry is initial.
                CLEAR : l_str_final,l_str_bset,"l_str_bseg_matnr,l_str_makt,
                 l_str_bseg_lifnr,l_str_lfa1,l_str_adrc,l_str_bseg,l_str_j_1bbranch,l_str_bupadrc,l_str_bseg.
                CLEAR:l_str_ekpo.
                CONTINUE.
              ENDIF.

            ELSEIF ( l_str_bseg-mwskz = 'G0' OR l_str_bseg-mwskz = '00' OR l_str_bseg-mwskz = 'V0' ) AND ( l_str_final-rate = '0' OR l_str_final-rate = '' )

              AND l_str_final-sgstamount IS INITIAL AND l_str_final-cgstamount IS INITIAL
              AND l_str_final-igstamount IS INITIAL
                AND l_str_bkpf-blart NE lc_re....
              IF l_str_bkpf-blart = lc_rc.
              ELSE.
                CLEAR:l_str_final-igstamount,l_str_final-cgstamount,l_str_final-sgstamount.
                READ TABLE it_gl INTO ls_gl_account WITH KEY hkont = l_str_bseg-hkont ztax = 'IGST' BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-igstamount = l_str_bseg-dmbtr.
                  l_str_final-igstamount = l_str_bseg-hkont.
                  IF l_str_bseg-shkzg = 'H'.
                    l_str_final-igstamount = l_str_final-igstamount * -1.
                  ENDIF.
                  l_str_final-reversecharge = ls_gl-rcm.
                  IF ls_gl-elig IS NOT INITIAL.
                    l_str_final-itceligibility = ls_gl-elig.
                  ENDIF.
                ENDIF.
                READ TABLE it_gl INTO ls_gl_account WITH KEY hkont = l_str_bseg-hkont ztax = 'CGST' BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-cgstamount = l_str_bseg-dmbtr.
                  l_str_final-cgstamount = l_str_bseg-hkont.
                  IF l_str_bseg-shkzg = 'H'.
                    l_str_final-cgstamount = l_str_final-cgstamount * -1.
                  ENDIF.
                  l_str_final-reversecharge = ls_gl-rcm.
                  IF ls_gl-elig IS NOT INITIAL.
                    l_str_final-itceligibility = ls_gl-elig.
                  ENDIF.
                ENDIF.
                READ TABLE it_gl INTO ls_gl_account WITH KEY hkont = l_str_bseg-hkont ztax = 'SGST' BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-sgstamount = l_str_bseg-dmbtr.
                  l_str_final-sgstamount = l_str_bseg-hkont.
                  IF l_str_bseg-shkzg = 'H'.
                    l_str_final-sgstamount = l_str_final-sgstamount * -1.
                  ENDIF.
                  l_str_final-reversecharge = ls_gl-rcm.
                  IF ls_gl-elig IS NOT INITIAL.
                    l_str_final-itceligibility = ls_gl-elig.
                  ENDIF.
                ENDIF.
              ENDIF.

            ENDIF.
            IF ( l_str_bseg-mwskz = 'G0' OR l_str_bseg-mwskz = 'V0' OR l_str_bseg-mwskz = '' ) AND ( l_str_final-rate = '0' OR l_str_final-rate = '' )
              AND l_str_final-cgstamount IS INITIAL AND l_str_final-sgstamount IS INITIAL
              AND l_str_final-igstamount IS INITIAL.
*
              IF l_str_bseg-buzid = 'W' OR l_str_bseg-buzid = 'F' OR l_str_bseg-buzid = 'S'.
                READ TABLE l_tab_rbco_tax INTO DATA(ls_tax) WITH KEY belnr = l_str_rseg-belnr gjahr = l_str_rseg-gjahr BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-igstamount = ls_tax-igstamount.
                  l_str_final-sgstamount = ls_tax-sgstamount.
                  l_str_final-cgstamount = ls_tax-cgstamount.
                  l_str_final-cgstamount = ls_tax-cgst1.
                  l_str_final-sgstamount = ls_tax-sgst1.
                  l_str_final-igstamount = ls_tax-igst1.
                  l_str_final-reversecharge = ls_tax-rcm.
                  IF ls_tax-elig IS NOT INITIAL.
                    l_str_final-itceligibility = ls_tax-elig.
                  ENDIF.
                  DELETE l_tab_rbco_tax INDEX sy-tabix.
                ENDIF.

                CLEAR: ls_gl.
                READ TABLE it_gl INTO ls_gl_account WITH KEY hkont = l_str_bseg-hkont BINARY SEARCH.
                IF sy-subrc = 0.
                ELSE.
                  l_str_final-taxablevalue = l_str_bseg-dmbtr.
                ENDIF.
*
*
              ENDIF.
            ENDIF.
            IF l_str_final-itceligibility IS INITIAL.
              IF l_str_final-hsn(2) = '99'.
                l_str_final-itceligibility = 'IS'.
              ENDIF.
              IF l_str_final-hsn IS INITIAL.
                l_str_final-itceligibility = 'IS'.
              ENDIF.
            ENDIF.
            IF   l_str_final-itceligibility = 'IS'.
              IF   l_str_final-quantity IS INITIAL.
                l_str_final-quantity = '1'.
              ENDIF.
            ENDIF.
            IF l_str_final-itemdescription IS INITIAL.
              l_str_final-itemdescription = l_str_bseg-sgtxt.
            ENDIF.
            IF l_str_lfa1-land1 NE 'IN'.
              IF l_str_lfa1-land1 IS NOT INITIAL.
                IF l_str_final-reversecharge = 'Y' OR l_str_final-itceligibility = 'IS'.
                  l_str_final-transactiontype = 'IMPS'.
                ENDIF.
              ENDIF.
            ENDIF.
            IF l_str_bkpf-blart = lc_ks.
              l_str_final-documenttype = 'INV'.
            ENDIF.


            CONSTANTS lc_customs1 TYPE lfa1-lifnr VALUE '0010017519'.
            CONSTANTS lc_customs2 TYPE lfa1-lifnr VALUE '0006940465'.
            CONSTANTS lc_customs3 TYPE lfa1-lifnr VALUE '0060000001'.
            IF ( l_str_bseg_lifnr-lifnr = lc_customs1 OR l_str_bseg_lifnr-lifnr = lc_customs2
              OR l_str_bseg_lifnr-lifnr = lc_customs3
              OR l_str_lfa1-name1 CS 'COMMISSIONER OF CUSTOMS' ) AND ( l_str_bkpf-blart = lc_re OR l_str_bkpf-blart = lc_kr ).
              IF l_str_final-doccurrencycode = 'INR' AND l_str_final-billtogstin NE '27AAACJ4323N1ZG' .
                READ TABLE  l_tab_lfa1 INTO l_str_lfa1 WITH KEY lifnr = l_str_bseg_lifnr-lifnr BINARY SEARCH .
                IF sy-subrc = 0.

                  l_str_final-billfromgstin = l_str_lfa1-stcd3.
                  l_str_final-billfromstatecode = l_str_lfa1-stcd3(2).
                  l_str_final-billfromlegalname = l_str_lfa1-name1.
                  CLEAR l_str_adrc.
                  READ TABLE l_tab_adrc INTO l_str_adrc WITH KEY addrnumber = l_str_lfa1-adrnr BINARY SEARCH.
                  IF sy-subrc = 0.
                    CONCATENATE l_str_adrc-street l_str_adrc-str_suppl1 INTO l_str_final-billfromaddress1.
                    CONCATENATE l_str_final-billfromaddress1 l_str_adrc-str_suppl2 INTO l_str_final-billfromaddress1.
                    l_str_final-billfromaddress2 = l_str_adrc-str_suppl3.
                    l_str_final-billfrompincode = l_str_adrc-post_code1.

                    READ TABLE l_tab_adr6 INTO l_str_adr6 WITH KEY addrnumber = l_str_lfa1-adrnr BINARY SEARCH.
                    IF sy-subrc = 0.
                      l_str_final-billtoemail = l_str_adr6-smtp_addr.
                    ENDIF.
*                    CLEAR wa_state.
*                    READ TABLE it_state INTO wa_state WITH KEY region = l_str_adrc-region BINARY SEARCH.
*                    IF sy-subrc = 0.
                    l_str_final-billfromstatecode = l_str_adrc-region.
*                    ENDIF.
                  ENDIF.
                ENDIF.
              ELSE.
                READ TABLE l_tab_bkpf_bktxt INTO DATA(ls_imp) WITH KEY bukrs = l_str_bseg-bukrs  xblnr = l_str_bkpf-xblnr
                blart = lc_sa BINARY SEARCH.
                IF sy-subrc NE '0'.
                  READ TABLE l_tab_bkpf_bktxt INTO ls_imp WITH KEY bukrs = l_str_bseg-bukrs  xblnr = l_str_bkpf-xblnr
                blart = lc_zg bktxt = l_str_bkpf-bktxt BINARY SEARCH.
                ENDIF.

                IF sy-subrc = 0.
                  l_str_final-documentdate = ls_imp-bldat.
                  l_str_final-custom2 = ls_imp-budat.
*                  l_str_final-custom11 = l_str_ekko-lifnr.
                  READ TABLE lt_po_ven INTO DATA(ls_po_ven) WITH KEY lifnr = l_str_ekko-lifnr BINARY SEARCH.
                  IF sy-subrc = 0.
*                    l_str_final-custom13 = ls_po_ven-name1.
*                    l_str_final-custom12 = ls_po_ven-stcd3.
                  ENDIF.

                  CONCATENATE ls_imp-budat+4(2) ls_imp-budat(4) INTO l_str_final-returnperiod.
                  CONCATENATE l_str_final-documentdate+6(2) '-' l_str_final-documentdate+4(2) '-' l_str_final-documentdate+0(4)
                   INTO l_str_final-documentdate.
                  DELETE l_tab_bkpf_final WHERE belnr = ls_imp-belnr AND gjahr = ls_imp-gjahr AND bukrs = ls_imp-bukrs.

                  DELETE  gt_final_inward WHERE custom1 = ls_imp-belnr AND custom4 = ls_imp-gjahr AND custom9 = ls_imp-bukrs
                  AND custom7 = lc_sa..
                  DELETE  gt_final_inward WHERE custom1 = ls_imp-belnr AND custom4 = ls_imp-gjahr AND custom9 = ls_imp-bukrs
                  AND custom7 = lc_zg..
                  l_str_final-documenttype = lc_boe ."'BOE'.
                  IF l_str_final-hsn(2) = '99'.
                    l_str_final-transactiontype = lc_imps."'IMPS'.
                  ELSE.
                    l_str_final-transactiontype = lc_impg."'IMPG'.
                  ENDIF.
                  READ TABLE l_tab_ekpo1 INTO l_str_ekpo WITH KEY ebeln = l_str_rseg-ebeln ebelp = l_str_rseg-ebelp  BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-itemname = l_str_ekpo-txz01.
                    l_str_final-itemdescription = l_str_ekpo-txz01.
                    IF l_str_rseg-kschl = 'ZSWC'.
                    ELSE.
                      l_str_final-quantity = l_str_rseg-menge.
                    ENDIF.
                    IF  l_str_rseg-hsn_sac(2) = '99'.
                      l_str_final-itceligibility = 'IS'.
                    ELSE.
                      l_str_final-itceligibility = 'IP'.
                    ENDIF.
                  ENDIF.
                ELSEIF go_miro = 'X'.
                  l_str_final-documenttype = lc_boe ."'BOE'.
                  IF l_str_final-hsn(2) = '99'.
                    l_str_final-transactiontype = lc_imps."'IMPS'.
                  ELSE.
                    l_str_final-transactiontype = lc_impg."'IMPG'.
                  ENDIF.
                  READ TABLE l_tab_ekpo1 INTO l_str_ekpo WITH KEY ebeln = l_str_rseg-ebeln ebelp = l_str_rseg-ebelp  BINARY SEARCH.
                  IF sy-subrc = 0.
                    l_str_final-itemname = l_str_ekpo-txz01.
                    l_str_final-itemdescription = l_str_ekpo-txz01.
                    IF l_str_rseg-kschl = 'ZSWC'.
                    ELSE.
                      l_str_final-quantity = l_str_rseg-menge.
                    ENDIF.
                    IF  l_str_rseg-hsn_sac(2) = '99'.
                      l_str_final-itceligibility = 'IS'.
                    ELSE.
                      l_str_final-itceligibility = 'IP'.
                    ENDIF.
                  ENDIF.
*                  l_str_final-custom11 = l_str_ekko-lifnr.
                  READ TABLE lt_po_ven INTO ls_po_ven WITH KEY lifnr = l_str_ekko-lifnr BINARY SEARCH.
                  IF sy-subrc = 0.
*                    l_str_final-custom13 = ls_po_ven-name1.
*                    l_str_final-custom12 = ls_po_ven-stcd3.
                  ENDIF.
                ELSE.
                  CLEAR : l_str_final,l_str_bset,"l_str_bseg_matnr,l_str_makt,
                          l_str_bseg_lifnr,l_str_lfa1,l_str_adrc,l_str_bseg,l_str_j_1bbranch,l_str_bupadrc,
                          l_str_bseg.
                  CLEAR:l_str_ekpo.
                  CONTINUE.
                ENDIF.

              ENDIF.
            ENDIF.
            l_str_final-custom8 = l_str_bkpf-bktxt.
            IF  l_str_final-itceligibility NE 'IS'.
              READ TABLE it_mara INTO DATA(is_mara) WITH KEY matnr =  l_str_final-itemname BINARY SEARCH.
              IF sy-subrc = 0.
                READ TABLE lt_t023t INTO DATA(ls_desc) WITH KEY matkl = is_mara-matkl BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-customitem1 = ls_desc-wgbez60.
                ENDIF.
              ENDIF.
            ENDIF.
            IF l_str_rseg-kschl IS NOT INITIAL.
              IF l_str_rseg-ind = ''.
*                CLEAR: l_str_final-customitem17.
*                CLEAR: l_str_final-customitem18.
              ENDIF.
            ENDIF.

            IF l_str_bkpf-tcode = 'MRKO'.
              READ TABLE l_tab_mkpf INTO DATA(l_str_mkpf) WITH KEY mblnr = l_str_bseg-xref3(10) mjahr = l_str_bseg-xref3+10(4) BINARY SEARCH.
              IF sy-subrc = 0..
*                l_str_final-customitem17 = l_str_mkpf-mblnr.
*                l_str_final-customitem18 = l_str_mkpf-budat.
              ENDIF.
            ENDIF.
            IF l_str_final-billfromgstin = l_str_final-billtogstin.
            ELSE.
              IF l_str_final-doccurrencycode IS INITIAL.
                l_str_final-doccurrencycode = l_str_bkpf-waers.
              ENDIF.
              IF l_str_final-documentnumber CS 'ISD'.
                CLEAR: l_str_final-taxablevalue.
              ENDIF.
              IF l_str_final-cgstamount = '1404051401' .
                CLEAR: l_str_final-cgstamount.
              ENDIF.
              IF  l_str_final-sgstamount = '1404051402'.
                CLEAR: l_str_final-sgstamount.
              ENDIF.
              IF  l_str_final-igstamount = '1404051403'.
                CLEAR: l_str_final-igstamount.
              ENDIF.

              IF l_str_final-itceligibility = 'IS'.
                CLEAR:l_str_final-cessamount,l_str_final-itccessamount.
              ENDIF.
*              l_str_final-custom11 = l_str_ekko-lifnr.
*              IF lv_rcm =  'RCM Ded'.
*                l_str_final-reversecharge = 'Y'.
*                l_str_final-itceligibility = 'IS'.
*              ELSEIF lv_rcm =  'FCM Ded'.
*              ELSEIF lv_rcm = 'RCM NonDed'.
*                l_str_final-reversecharge = 'Y'.
*                l_str_final-itceligibility = 'NO'.
*              ELSEIF lv_rcm = 'FCM NonDed'.
*              ENDIF.
              l_str_final-doccurrencycode = l_str_bkpf-waers.
              IF l_str_final-taxpayertype = 'ISD'.
                l_str_final-transactiontype = 'ISD'.
              ENDIF.
              APPEND l_str_final TO gt_final_inward.
            ENDIF.

          ELSE.
            CLEAR:l_str_bseg.
            CLEAR: l_str_final.
            CLEAR:l_str_prps.
            CLEAR:ls_user_usnam.
            EXIT.
          ENDIF.

          CLEAR : l_str_final,l_str_bset,"l_str_bseg_matnr,l_str_makt,
          l_str_bseg_lifnr,l_str_lfa1,l_str_adrc,l_str_bseg,l_str_j_1bbranch,l_str_bupadrc,
          l_str_bseg.
          CLEAR:l_str_ekpo.
          CLEAR:ls_gl.CLEAR:ls_crn.
*          CLEAR:lv_rcm.
        ENDLOOP.
      ENDIF.
      CLEAR : l_str_bkpf.
    ENDLOOP.
    IF s_date IS NOT INITIAL.
      DELETE gt_final_inward WHERE custom2 NOT IN s_date.
    ENDIF.

*    MOVE-CORRESPONDING gt_final_inward TO gt_final_inward1.
*    MOVE-CORRESPONDING gt_final_inward TO gt_final_inward1.


*    LOOP AT gt_final_inward.
*      MOVE-CORRESPONDING gt_final_inward TO gt_final_inward1.
*      APPEND gt_final_inward1.
*    ENDLOOP.
    DATA(gt_final_inward1) = gt_final_inward.

    REFRESH:gt_final_inward[].
    REFRESH:gt_final_inward.
*
    DELETE gt_final_inward1 WHERE taxablevalue IS INITIAL AND igstamount IS INITIAL AND cgstamount IS INITIAL
  AND sgstamount IS INITIAL.

*    IF p_tax = 'X'.
*      DELETE gt_final_inward1 WHERE igstamount IS INITIAL AND cgstamount IS INITIAL AND sgstamount IS INITIAL.
*    ENDIF.

*    SORT gt_final_inward1  BY documentnumber locationgstin  custom1 custom4 custom9  rate   hsn
*    reversecharge.

    LOOP AT gt_final_inward1 INTO DATA(gl_final_inward1).
      l_str_final-custom1 = gl_final_inward1-custom1.
      l_str_final-customitem1 = gl_final_inward1-customitem1.
      l_str_final-itemdescription = gl_final_inward1-itemdescription.
      l_str_final-itemname = gl_final_inward1-itemname.
      l_str_final-hsn = gl_final_inward1-hsn.
      l_str_final-uqc = gl_final_inward1-uqc.
      l_str_final-othercharges = gl_final_inward1-othercharges.
      l_str_final-custom2 = gl_final_inward1-custom2.
      l_str_final-custom3 = gl_final_inward1-custom3.
      l_str_final-custom4 = gl_final_inward1-custom4.
      l_str_final-custom5 = gl_final_inward1-custom5.
*      l_str_final-vendor = gl_final_inward1-vendor.
      l_str_final-custom6 = gl_final_inward1-custom6.
      l_str_final-custom7 = gl_final_inward1-custom7.
      l_str_final-custom8 = gl_final_inward1-custom8.
      l_str_final-custom9 = gl_final_inward1-custom9.
      l_str_final-custom10 = gl_final_inward1-custom10.
*      l_str_final-custom11 = gl_final_inward1-custom11.
*      l_str_final-custom12 = gl_final_inward1-custom12.
*      l_str_final-custom13 = gl_final_inward1-custom13.
*      l_str_final-custom14 = gl_final_inward1-custom2.
*      l_str_final-custom15 = gl_final_inward1-custom15.
*      l_str_final-custom16 = gl_final_inward1-custom16.
*      l_str_final-custom17 = gl_final_inward1-custom17.
*      l_str_final-custom18 = gl_final_inward1-custom18.
*      l_str_final-custom19 = gl_final_inward1-custom19.
*      l_str_final-custom20 = gl_final_inward1-custom20.

      l_str_final-customitem2 = gl_final_inward1-customitem2.
      l_str_final-customitem3 = gl_final_inward1-customitem3.
      l_str_final-customitem5 = gl_final_inward1-customitem5.
      l_str_final-customitem6 = gl_final_inward1-customitem6.
      l_str_final-customitem7 = gl_final_inward1-customitem7.
      l_str_final-customitem8 = gl_final_inward1-customitem8.
      l_str_final-customitem9 = gl_final_inward1-customitem9.
      l_str_final-customitem10 = gl_final_inward1-customitem10.
*      l_str_final-customitem11 = gl_final_inward1-customitem11.
*      l_str_final-customitem12 = gl_final_inward1-customitem12.
*      l_str_final-customitem13 = gl_final_inward1-customitem13.
*      l_str_final-customitem14 = gl_final_inward1-customitem14.
*      l_str_final-customitem15 = gl_final_inward1-customitem15.
*      l_str_final-customitem16 = gl_final_inward1-customitem16.
*      l_str_final-customitem17 = gl_final_inward1-customitem17.
*      l_str_final-customitem18 = gl_final_inward1-customitem18.
*      l_str_final-customitem19 = gl_final_inward1-customitem19.
*      l_str_final-customitem20 = gl_final_inward1-customitem20.
*
*      l_str_final-customitem21 = gl_final_inward1-customitem21.
*      l_str_final-customitem22 = gl_final_inward1-customitem22.
*      l_str_final-customitem23 = gl_final_inward1-customitem23.
*      l_str_final-customitem24 = gl_final_inward1-customitem24.
*      l_str_final-customitem25 = gl_final_inward1-customitem25.
*      l_str_final-customitem26 = gl_final_inward1-customitem26.
*      l_str_final-customitem27 = gl_final_inward1-customitem27.
*      l_str_final-customitem28 = gl_final_inward1-customitem28.
*      l_str_final-customitem29 = gl_final_inward1-customitem29.
*      l_str_final-customitem30 = gl_final_inward1-customitem30.
*      l_str_final-customitem31 = gl_final_inward1-customitem31.
*      l_str_final-customitem32 = gl_final_inward1-customitem32.
*      l_str_final-customitem33 = gl_final_inward1-customitem33.
*      l_str_final-customitem34 = gl_final_inward1-customitem34.
*      l_str_final-customitem35 = gl_final_inward1-customitem35.
*      l_str_final-customitem37 = gl_final_inward1-customitem37.

      l_str_final-branch = gl_final_inward1-branch.
      l_str_final-custom6 = gl_final_inward1-custom6.
      l_str_final-custom7 = gl_final_inward1-custom7.
      l_str_final-custom8 = gl_final_inward1-custom8.
      l_str_final-custom9 = gl_final_inward1-custom9.
      l_str_final-documentnumber = gl_final_inward1-documentnumber.
      l_str_final-customitem4 = gl_final_inward1-documentnumber.
      l_str_final-cancelled_date = gl_final_inward1-cancelled_date.

      REPLACE ALL OCCURRENCES OF REGEX '["\|!@#&$%^''*():;.""`~?<>+_]' IN l_str_final-customitem4 WITH space.
      REPLACE ALL OCCURRENCES OF '\' IN l_str_final-customitem4 WITH ''.
      CONDENSE l_str_final-customitem4 NO-GAPS.

      l_str_final-documentdate = gl_final_inward1-documentdate.
      l_str_final-doccurrencycode = gl_final_inward1-doccurrencycode.
      l_str_final-locationgstin = gl_final_inward1-billtogstin.
      l_str_final-billtolegalname = gl_final_inward1-billtolegalname.
      l_str_final-purpose = gl_final_inward1-purpose.
      l_str_final-supplytype = gl_final_inward1-supplytype.
      l_str_final-returnperiod = gl_final_inward1-returnperiod.
      l_str_final-documenttype = gl_final_inward1-documenttype.
      l_str_final-serialnumber = gl_final_inward1-serialnumber.
      l_str_final-transactiontype = gl_final_inward1-transactiontype.
      l_str_final-pos = gl_final_inward1-pos.
      l_str_final-billfromgstin = gl_final_inward1-billfromgstin.
      l_str_final-billfromlegalname = gl_final_inward1-billfromlegalname.
      l_str_final-billfromaddress1 = gl_final_inward1-billfromaddress1.
      l_str_final-billfromaddress2  = gl_final_inward1-billfromaddress2.
      l_str_final-billfromcity = gl_final_inward1-billfromcity.
      l_str_final-billfromstatecode = gl_final_inward1-billfromstatecode.
      l_str_final-billfrompincode = gl_final_inward1-billfrompincode.
      l_str_final-locationgstin = gl_final_inward1-locationgstin.
      l_str_final-documentvalue = gl_final_inward1-documentvalue.
      l_str_final-billtogstin = gl_final_inward1-billtogstin.
      l_str_final-billtolegalname = gl_final_inward1-billtolegalname.
      l_str_final-billtoaddress1 = gl_final_inward1-billtoaddress1.
      l_str_final-billtoaddress2 = gl_final_inward1-billtoaddress2.
      l_str_final-billtostatecode  = gl_final_inward1-billtostatecode.
      l_str_final-billtocity = gl_final_inward1-billtocity.
      l_str_final-billtopincode = gl_final_inward1-billtopincode.
      l_str_final-shiptogstin = gl_final_inward1-shiptogstin.
      l_str_final-billtoemail = gl_final_inward1-billtoemail.
      l_str_final-shiptoaddress1 = gl_final_inward1-shiptoaddress1.
      l_str_final-shiptoaddress2 = gl_final_inward1-shiptoaddress2.
      l_str_final-referencedocumentnumber = gl_final_inward1-referencedocumentnumber.
      l_str_final-referencedocumentdate = gl_final_inward1-referencedocumentdate.
      l_str_final-quantity = l_str_final-quantity + gl_final_inward1-quantity.
      l_str_final-taxablevalue = l_str_final-taxablevalue + gl_final_inward1-taxablevalue.
      l_str_final-discountamount =  gl_final_inward1-discountamount.
      l_str_final-cgstamount = l_str_final-cgstamount + gl_final_inward1-cgstamount.
      l_str_final-igstamount = l_str_final-igstamount + gl_final_inward1-igstamount.
      l_str_final-sgstamount = l_str_final-sgstamount + gl_final_inward1-sgstamount.
      l_str_final-cessamount = l_str_final-cessamount + gl_final_inward1-cessamount.

      l_str_final-rate =   gl_final_inward1-rate.
      IF l_str_final-rate IS INITIAL.
        l_str_final-rate = 0.
        CONDENSE l_str_final-rate.
      ENDIF.

      l_str_final-itceligibility  = gl_final_inward1-itceligibility.
      l_str_final-igstamount  = gl_final_inward1-igstamount.
      l_str_final-sgstamount  = gl_final_inward1-sgstamount.
      l_str_final-cgstamount  = gl_final_inward1-cgstamount.
      l_str_final-taxpayertype  = gl_final_inward1-taxpayertype.
      IF l_str_final-itceligibility IS INITIAL.
        IF l_str_final-hsn(2) = '99'.
          l_str_final-itceligibility = 'IS'.
        ELSE.
          l_str_final-itceligibility = 'IP'.
        ENDIF.
      ENDIF.

      READ TABLE it_tax INTO DATA(wa_tax) WITH KEY mwskz = gl_final_inward1-customitem2.
      IF sy-subrc = 0.
        IF wa_tax-itc IS NOT INITIAL.
          l_str_final-itceligibility = wa_tax-itc.
        ENDIF.
        gl_final_inward1-reversecharge = wa_tax-rcm.
        IF wa_tax-doc IS NOT INITIAL.
          gl_final_inward1-documenttype = wa_tax-doc.
          IF gl_final_inward1-documenttype = 'BOE'.
            IF l_str_final-hsn(2) = '99'.
              gl_final_inward1-transactiontype = 'IMPS'.
            ELSE.
              gl_final_inward1-transactiontype = 'IMPG'.
            ENDIF.
          ENDIF.
        ENDIF.
      ENDIF.
      IF l_str_final-itceligibility = 'NO'.
        CLEAR:gl_final_inward1-itcigstamount.
        CLEAR:gl_final_inward1-itcsgstamount.
        CLEAR:gl_final_inward1-itccgstamount.
        CLEAR:gl_final_inward1-itccessamount.
      ENDIF.

      l_str_final-itccgstamount = l_str_final-itccgstamount + gl_final_inward1-itccgstamount.
      l_str_final-itcsgstamount = l_str_final-itcsgstamount + gl_final_inward1-itcsgstamount.
      l_str_final-itcigstamount = l_str_final-itcigstamount + gl_final_inward1-itcigstamount.
      l_str_final-itccessamount = l_str_final-itccessamount + gl_final_inward1-itccessamount.

      l_str_final-reversecharge = gl_final_inward1-reversecharge.
      l_str_final-company_code = gl_final_inward1-company_code.

      IF gl_final_inward1-documenttype = 'BOE'.
*        gl_final_inward1-customitem21 = gl_final_inward1-documentnumber.
      ENDIF.

      IF p_item IS INITIAL.
        AT END OF rate.
          IF l_str_final-rate < 0.
            l_str_final-rate = l_str_final-rate * -1.
          ENDIF.
          IF ( l_str_final-rate IS INITIAL OR l_str_final-rate = '0')  AND l_str_final-igstamount IS INITIAL AND l_str_final-cgstamount IS INITIAL
            AND  l_str_final-sgstamount IS INITIAL.
            l_str_final-taxtype = 'NIL'.
          ENDIF.
          IF l_str_final-reversecharge IS INITIAL.
            l_str_final-reversecharge = 'N'.
          ENDIF.
          IF l_str_final-itceligibility = 'IS'.
            CLEAR:l_str_final-cessamount,l_str_final-itccessamount.

          ENDIF.
          APPEND l_str_final TO gt_final_inward.
          CLEAR l_str_final.
        ENDAT.
      ELSE.

        IF l_str_final-rate IS INITIAL AND l_str_final-igstamount IS INITIAL AND l_str_final-cgstamount IS INITIAL
          AND  l_str_final-sgstamount IS INITIAL.
          l_str_final-taxtype = 'NIL'.
        ENDIF.
        IF l_str_final-reversecharge IS INITIAL.
          l_str_final-reversecharge = 'N'.
        ENDIF.
        IF l_str_final-rate < 0.
          l_str_final-rate = l_str_final-rate * -1.
        ENDIF.
        IF l_str_final-itceligibility = 'IS'.
          CLEAR:l_str_final-cessamount,l_str_final-itccessamount.
        ENDIF.

        APPEND l_str_final TO gt_final_inward.
        CLEAR l_str_final.
      ENDIF.
    ENDLOOP.
*    IF p_tax = 'X'.
*      DELETE gt_final_inward WHERE igstamount IS INITIAL AND cgstamount IS INITIAL AND sgstamount IS INITIAL.
*    ENDIF.


*    ENDIF.
*    ENDIF.
    TYPES: BEGIN OF ty_vbrk,
             vbeln     TYPE vbrk-vbeln,     "Billing Document ,vbrk
             knumv     TYPE vbrk-knumv,     "Number of the document condition vbrp
             fkart     TYPE vbrk-fkart,     "Billing Type ,vbrk
             erzet     TYPE vbrk-erzet,     "Entry time,vbrp
             fktyp     TYPE vbrk-fktyp,     "Billing category VBRK
             vbtyp     TYPE vbrk-vbtyp,     "SD document category  vbrk
             waerk     TYPE vbrk-waerk,
             vkorg     TYPE vbrk-vkorg,     "Sales Organization vbrk
             fkdat     TYPE vbrk-fkdat,     "Billing date for billing index and printout  vbrk
             kurrf     TYPE vbrk-kurrf,     "Exchange rate for FI postings  VBRK
             bukrs     TYPE vbrk-bukrs,     "Company Code   vbrk
             gjahr     TYPE vbrk-gjahr,     "Fiscal Year
             regio     TYPE vbrk-regio,     "Region (State, Province, County) vbrk
             netwr     TYPE vbrk-netwr,     "Net Value in Document Currency  vbrk
             kunrg     TYPE vbrk-kunrg,     "Payer  vbrk
             kunag     TYPE vbrk-kunag,     "Sold-to party  vbrk
             sfakn     TYPE vbrk-sfakn,     "Cancelled billing document number    vbrk
             xblnr     TYPE vbrk-xblnr,     "Reference Document Number  vbrk
             zuonr     TYPE vbrk-zuonr,     "Assignment number  vbrk
             mwsbk     TYPE vbrk-mwsbk,     "Tax amount in document currency vbrk
             fksto     TYPE vbrk-fksto,     "Billing document is cancelled vbrk
             bupla     TYPE vbrk-bupla,     "Business Place vbrk
             rfbsk     TYPE vbrk-rfbsk,
             ernam     TYPE vbrk-ernam,
             posnr     TYPE vbrp-posnr,
             werks     TYPE vbrp-werks,
             aubel     TYPE vbrp-aubel,
             matnr     TYPE vbrp-matnr,
             fkimg     TYPE vbrp-fkimg,
             erdat     TYPE vbrk-erdat,
             vrkme     TYPE vbrp-vrkme,
             arktx     TYPE vbrp-arktx,
             netwrline TYPE vbrp-netwr,
             vgbel     TYPE vbrp-vgbel,
             prctr     TYPE vbrp-prctr,
           END OF ty_vbrk,

           BEGIN OF ty_vbrp,
             vbeln TYPE vbrp-vbeln,
             posnr TYPE vbrp-posnr,
             werks TYPE vbrp-werks,
             aubel TYPE vbrp-aubel,
             matnr TYPE vbrp-matnr,
             lmeng TYPE vbrp-lmeng,
             vrkme TYPE vbrp-vrkme,
             arktx TYPE vbrp-arktx,
           END OF ty_vbrp,

           BEGIN OF ty_vbpa,
             vbeln TYPE vbpa-vbeln,
             posnr TYPE vbpa-posnr,
             parvw TYPE vbpa-parvw,
             kunnr TYPE vbpa-kunnr,
             adrnr TYPE vbpa-adrnr,
             land1 TYPE vbpa-land1,
           END OF   ty_vbpa,

           BEGIN OF ty_konv,
             knumv   TYPE konv-knumv,
             kposn   TYPE konv-kposn,
             kschl   TYPE konv-kschl,
             kawrt   TYPE konv-kawrt,
             kbetr   TYPE konv-kbetr,
             kwert   TYPE konv-kwert,
             gsp_tax TYPE char10,
           END OF ty_konv,

           BEGIN OF ty_t001w,
             werks      TYPE  t001w-werks,
             j_1bbranch TYPE  t001w-j_1bbranch,
             adrnr      TYPE  t001w-adrnr,
             regio      TYPE  t001w-regio,
             land1      TYPE  t001w-land1,
             stras      TYPE  t001w-stras,
             name2      TYPE  t001w-name2,
             pstlz      TYPE  t001w-pstlz,
             ort01      TYPE  t001w-ort01,
           END OF ty_t001w,

           BEGIN OF ty_j_1bbranch,
             bukrs  TYPE j_1bbranch-bukrs,
             branch TYPE j_1bbranch-branch,
             gstin  TYPE j_1bbranch-gstin,
             name   TYPE j_1bbranch-name,
             adrnr  TYPE j_1bbranch-adrnr,
           END OF ty_j_1bbranch,

           BEGIN OF ty_adrc,
             addrnumber TYPE adrc-addrnumber,
             name1      TYPE adrc-name1,
             name2      TYPE adrc-name2,
             city1      TYPE adrc-city1,
             street     TYPE adrc-street,
             str_suppl1 TYPE adrc-str_suppl1,
             str_suppl2 TYPE adrc-str_suppl2,
             str_suppl3 TYPE adrc-str_suppl3,
             location   TYPE adrc-location,
             tel_number TYPE adrc-tel_number,
             post_code1 TYPE adrc-post_code1,
             region     TYPE adrc-region,
           END OF ty_adrc,

           BEGIN OF ty_adr6,
             addrnumber TYPE adr6-addrnumber,
             persnumber TYPE adr6-persnumber,
             smtp_addr  TYPE adr6-smtp_addr,
           END OF ty_adr6,

           BEGIN OF ty_marc,
             matnr TYPE marc-matnr,
             werks TYPE marc-werks,
             steuc TYPE marc-steuc,
           END OF ty_marc,

           BEGIN OF ty_t005u,
             bland TYPE t005u-bland,
             bezei TYPE t005u-bezei,
             spras TYPE t005u-spras,
             land1 TYPE t005u-land1,
           END OF ty_t005u,

           BEGIN OF ty_makt,
             matnr TYPE makt-matnr,
             maktx TYPE makt-maktx,
           END OF ty_makt,

           BEGIN OF ty_user21,
             bname      TYPE usr21-bname,
             addrnumber TYPE usr21-addrnumber,
             persnumber TYPE usr21-persnumber,
           END OF ty_user21.

    DATA:
      l_tab_vbrk1       TYPE STANDARD TABLE OF ty_vbrk,
      l_tab_vbrp1       TYPE STANDARD TABLE OF ty_vbrp,
      l_tab_vbpa        TYPE STANDARD TABLE OF ty_vbpa,
      l_tab_konv        TYPE STANDARD TABLE OF ty_konv,
      l_tab_konv_zcrm   TYPE STANDARD TABLE OF ty_konv,
      l_tab_t001w1      TYPE STANDARD TABLE OF ty_t001w,
      l_tab_t005u       TYPE STANDARD TABLE OF ty_t005u,
      l_tab_adrc1       TYPE STANDARD TABLE OF ty_adrc,
      l_tab_adrc_plant1 TYPE STANDARD TABLE OF ty_adrc,
      l_tab_adr61       TYPE STANDARD TABLE OF ty_adr6,
      l_tab_user21      TYPE STANDARD TABLE OF ty_user21.
*---------------------Internal Structures---------------------------------------------------------------------------------------------------*
    DATA: l_str_vbrk1       TYPE ty_vbrk,
          l_str_konv        TYPE ty_konv,
          l_str_konv_zcrm   TYPE ty_konv,
          l_str_adrc1       TYPE ty_adrc,
          l_str_marc1       TYPE ty_marc,
          l_str_j_1bbranch1 TYPE ty_j_1bbranch,
          l_str_makt1       TYPE ty_makt,
          l_str_user21      TYPE ty_user21.
*------------------------------------------------------------------------------------------------------------------------------------------------------*
    IF l_tab_bkpf[] IS NOT INITIAL.

      SELECT     vbrk~vbeln
                 vbrk~knumv
                 vbrk~fkart
                 vbrk~erzet
                 vbrk~fktyp
                 vbrk~vbtyp
                 vbrk~waerk
                 vbrk~vkorg
                 vbrk~fkdat
                 vbrk~kurrf
                 vbrk~bukrs
                 vbrk~gjahr
                 vbrk~regio
                 vbrk~netwr
                 vbrk~kunrg
                 vbrk~kunag
                 vbrk~sfakn
                 vbrk~xblnr
                 vbrk~zuonr
                 vbrk~mwsbk
                 vbrk~fksto
                 vbrk~bupla
                 vbrk~rfbsk
                 vbrk~ernam
                 vbrp~posnr
                 vbrp~werks
                 vbrp~aubel
                 vbrp~matnr
                 vbrp~fkimg
                 vbrk~erdat
                 vbrp~vrkme
                 vbrp~arktx
                 vbrp~netwr AS netwrline
                 vbrp~vgbel
                 vbrp~prctr  APPENDING TABLE  l_tab_vbrk1

                 FROM vbrk AS vbrk INNER JOIN vbrp AS vbrp
                 ON vbrk~vbeln = vbrp~vbeln
                  FOR ALL ENTRIES IN l_tab_bkpf
                  WHERE vbrk~bukrs IN s_bukrs
                    AND vbrk~xblnr = l_tab_bkpf-xblnr
                    AND vbrk~fkart IN ('ZGS1','ZGS2','ZGS3','ZGS4','ZSMP','ZMVJ')
                    AND vbrk~fkdat = l_tab_bkpf-bldat
*                  AND vbrk~erdat IN s_date1
                    AND vbrk~fksto = ''.
    ENDIF.

    IF l_tab_vbrk1[] IS NOT INITIAL.

      SELECT vbeln, posnr, vgbel FROM lips
      INTO TABLE @DATA(lt_lips)
     FOR ALL ENTRIES IN @l_tab_vbrk1
      WHERE vbeln = @l_tab_vbrk1-vgbel.
      SORT lt_lips BY vbeln.
      IF sy-subrc =  0.
        SELECT ebeln , bedat FROM ekko INTO TABLE @DATA(lt_ekko)
          FOR ALL ENTRIES IN @lt_lips
          WHERE ebeln = @lt_lips-vgbel.
        SORT lt_ekko BY ebeln.

      ENDIF.

      SELECT vbelv,vbeln,mjahr,vbtyp_n,bwart FROM vbfa INTO TABLE @DATA(lt_vbfa)
        FOR ALL ENTRIES IN @l_tab_vbrk1
        WHERE vbelv = @l_tab_vbrk1-vgbel
         AND vbtyp_n IN ('i','R')
        AND bwart NE ''..
      SORT lt_vbfa BY vbelv vbtyp_n.



      IF sy-subrc = 0.
        SELECT mblnr mjahr budat  FROM mkpf "RBKW
           INTO TABLE l_tab_mkpf
           FOR ALL ENTRIES IN lt_vbfa
           WHERE mblnr = lt_vbfa-vbeln
           AND mjahr = lt_vbfa-mjahr.

        SORT l_tab_mkpf BY mblnr mjahr.

      ENDIF.

      SELECT matnr  bwkey bklas
        FROM mbew INTO TABLE lt_mbew
        FOR ALL ENTRIES IN l_tab_vbrk1
        WHERE matnr = l_tab_vbrk1-matnr.
      IF sy-subrc = 0.
        DELETE lt_mbew WHERE bklas IS INITIAL.
        IF lt_mbew[] IS NOT INITIAL.
          SELECT * FROM t030 INTO TABLE lt_t030
            FOR ALL ENTRIES IN lt_mbew
            WHERE bklas = lt_mbew-bklas
            AND ktopl = '1000'
            AND ktosl = 'BSX'.
        ENDIF.
        DELETE lt_t030 WHERE bklas IS INITIAL.
        SORT lt_mbew BY matnr bwkey.
        SORT lt_t030 BY bklas .
      ENDIF.

      FIELD-SYMBOLS <fs_str_vbrk> TYPE ty_vbrk.
      SORT l_tab_vbrk1 BY xblnr.
      LOOP AT l_tab_vbrk1 ASSIGNING <fs_str_vbrk>.
        IF <fs_str_vbrk>-xblnr = ''.
          CONCATENATE 'PT' <fs_str_vbrk>-vbeln INTO <fs_str_vbrk>-xblnr.
          CONDENSE <fs_str_vbrk>-xblnr.
        ELSE.
          IF <fs_str_vbrk>-xblnr+0(1) = '0'.
            CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
              EXPORTING
                input  = <fs_str_vbrk>-xblnr
              IMPORTING
                output = <fs_str_vbrk>-xblnr.
          ENDIF.
        ENDIF.
      ENDLOOP.

      SELECT matnr matkl FROM mara INTO  TABLE it_mara
        FOR ALL ENTRIES IN  l_tab_vbrk1
        WHERE matnr =  l_tab_vbrk1-matnr.

      SORT it_mara BY matnr.

      SELECT  werks
              j_1bbranch
              adrnr
              regio
              land1
              stras
              name2
              pstlz
              ort01 APPENDING TABLE l_tab_t001w1 FROM t001w
                FOR ALL ENTRIES IN l_tab_vbrk1
                     WHERE werks = l_tab_vbrk1-werks. "#EC CI_NO_TRANSFORM

      IF l_tab_t001w1 IS NOT INITIAL.
        SELECT  bukrs                          " From GSTIN. / Location GSTIN
                branch
                gstin
                name
                adrnr   APPENDING  TABLE l_tab_j_1bbranch
          FROM j_1bbranch
          FOR ALL ENTRIES IN l_tab_t001w1
                       WHERE branch = l_tab_t001w1-j_1bbranch AND bukrs IN s_bukrs. "#EC CI_NO_TRANSFORM

        SELECT    "FETCH SOLD TO PARTY ADDRESS DETAILS
           addrnumber
            name1
            name2
            city1
            street
            str_suppl1
            str_suppl2
            str_suppl3
            location
            tel_number
            post_code1
            region
                FROM adrc INTO TABLE l_tab_adrc_plant1
          FOR ALL ENTRIES IN l_tab_t001w1
          WHERE addrnumber = l_tab_t001w1-adrnr AND
                langu = 'EN'.                      "#EC CI_NO_TRANSFORM
      ENDIF.

      SELECT  kunnr
              land1                      " To Gstin
              adrnr
              telf1
              stcd3
              regio
              name1
              stras
              ort01
              pstlz
              INTO CORRESPONDING FIELDS OF TABLE l_tab_kna1 FROM kna1
              FOR ALL ENTRIES IN l_tab_vbrk1
                   WHERE kunnr = l_tab_vbrk1-kunag. "#EC CI_NO_TRANSFORM
      IF l_tab_kna1 IS NOT INITIAL.
        SELECT
           addrnumber
            name1
            name2
            city1
            street
            str_suppl1
            str_suppl2
            str_suppl3
            location
            tel_number
            post_code1
            region
          INTO TABLE l_tab_adrc1 FROM adrc
            FOR ALL ENTRIES IN l_tab_kna1
                          WHERE addrnumber = l_tab_kna1-adrnr. "#EC CI_NO_TRANSFORM

      ENDIF.

      SELECT  matnr werks
              steuc INTO TABLE l_tab_marc FROM marc
              FOR ALL ENTRIES IN l_tab_vbrk1
                        WHERE matnr = l_tab_vbrk1-matnr AND
                              werks = l_tab_vbrk1-werks.

      SELECT
        matnr
        maktx FROM makt INTO TABLE l_tab_makt
        FOR ALL ENTRIES IN l_tab_vbrk1
        WHERE matnr = l_tab_vbrk1-matnr.
      SORT l_tab_vbrk1 BY vbeln posnr knumv.

      DATA: r_cond1 TYPE RANGE OF kschl.
      CLEAR: r_cond1[].
      SELECT * FROM zsd_t_tax_cond INTO
        TABLE @DATA(it_cond1)
        WHERE supply_type = 'S'.
      r_cond1 = VALUE #( FOR ls_condition IN it_cond1 ( sign = 'I' option = 'EQ' low = ls_condition-kschl ) ).

      SELECT knumv    "Tax Amoount Fetch
            kposn
            kschl
            kawrt
            kbetr
            kwert FROM konv INTO TABLE l_tab_konv
        FOR ALL ENTRIES IN l_tab_vbrk1
        WHERE knumv = l_tab_vbrk1-knumv
        AND kposn = l_tab_vbrk1-posnr
        AND kschl IN r_cond1
        AND kinak = ''
        AND kwert NE 0.
      LOOP AT it_cond1 INTO DATA(is_cond1).
        l_str_konv-gsp_tax = is_cond1-gsp_tax.
        MODIFY l_tab_konv FROM l_str_konv TRANSPORTING gsp_tax WHERE kschl = is_cond1-kschl.
        CLEAR: is_cond1.
      ENDLOOP.

      l_tab_konv_zcrm[] = l_tab_konv[].

**-------Sorting------------------------------------------------------------------------------------------------------------------------------------*

      SORT l_tab_vbrk1                BY vbeln knumv fkart.
      SORT l_tab_vbrp1                BY vbeln posnr.
      SORT l_tab_vbpa                BY vbeln parvw.
      SORT l_tab_bseg                BY belnr bukrs txgrp.
      SORT l_tab_bset                BY belnr bukrs.
      SORT l_tab_kna1                BY kunnr.
      SORT l_tab_t001w1               BY werks regio.
      SORT l_tab_t005u               BY bland.
      SORT l_tab_adrc1                BY addrnumber.
      SORT l_tab_adr61                BY addrnumber.
      SORT l_tab_marc                BY matnr.
      SORT l_tab_j_1bbranch          BY bukrs branch.
      SORT l_tab_konv                BY knumv kposn gsp_tax.
      SORT l_tab_konv_zcrm           BY knumv kposn gsp_tax ."descending.
      SORT l_tab_makt BY matnr.
      SORT it_mara BY matnr.
      SORT l_tab_adrc_plant1 BY addrnumber.
      CLEAR:l_str_final.
      CLEAR:l_str_j_1bbranch1.
      IF l_tab_marc IS NOT INITIAL.
        DELETE l_tab_marc WHERE steuc IS INITIAL.
      ENDIF.
**------Process and fill Final Internal Table(Main Loop)-------------------------------------------------------------------------------------------------------------------*
      DATA:lv_serial_no TYPE i.
      SORT l_tab_bkpf BY xblnr.
*
      CLEAR:l_str_final.
      SORT l_tab_bkpf BY xblnr.
      LOOP AT l_tab_vbrk1 INTO l_str_vbrk1.
        l_str_final-supplytype = 'P'.
*      l_str_final-customitem21 = l_str_vbrk1-vbeln.
        l_str_final-doccurrencycode = l_str_vbrk1-waerk.

        lv_serial_no = lv_serial_no + 1.
        AT NEW vbeln.
          CLEAR: lv_serial_no.
          lv_serial_no = 1.
        ENDAT.
**-------Tax Calculation -------------------------------------------------------------------------------------------------------------*
        DATA: l_var_condtype TYPE i.
        l_var_condtype = 0.
        CLEAR: l_str_konv_zcrm.
        READ TABLE l_tab_konv_zcrm INTO l_str_konv_zcrm WITH KEY knumv = l_str_vbrk1-knumv  kposn = l_str_vbrk1-posnr BINARY SEARCH.
        IF sy-subrc = 0.
          l_var_tabix = sy-tabix.
          LOOP AT l_tab_konv_zcrm INTO l_str_konv_zcrm FROM l_var_tabix.
            IF l_str_konv_zcrm-knumv = l_str_vbrk1-knumv AND l_str_konv_zcrm-kposn = l_str_vbrk1-posnr.
            ELSE.
              CLEAR:l_str_konv_zcrm.
              EXIT.
            ENDIF.

            READ TABLE it_cond1 INTO is_cond1 WITH KEY kschl  = l_str_konv_zcrm-kschl..
            IF l_var_condtype = 0.
              CASE is_cond1-gsp_tax.

                WHEN 'CGST'.
                  l_str_final-taxablevalue = l_str_konv_zcrm-kawrt.
                  l_str_final-cgstamount = l_str_final-cgstamount + l_str_konv_zcrm-kwert.
                  l_str_final-rate = ( l_str_final-rate + ( l_str_konv_zcrm-kbetr ) / 10 ).
                  CONDENSE l_str_final-rate.
                WHEN 'SGST'.
                  l_str_final-taxablevalue = l_str_konv_zcrm-kawrt.
                  l_str_final-sgstamount = l_str_final-sgstamount + l_str_konv_zcrm-kwert.
                  l_str_final-rate = ( l_str_final-rate + ( l_str_konv_zcrm-kbetr ) / 10 ).
                  CONDENSE l_str_final-rate.
                WHEN  'IGST'.
                  l_str_final-taxablevalue = l_str_konv_zcrm-kawrt.
                  l_str_final-igstamount = l_str_final-igstamount + l_str_konv_zcrm-kwert.
                  l_str_final-rate = ( l_str_final-rate + ( l_str_konv_zcrm-kbetr ) / 10 ).
                  CONDENSE l_str_final-rate.
                WHEN 'ZAMP'.
                  l_str_final-taxablevalue = l_str_konv_zcrm-kwert.
                WHEN 'JOSS'.
                  l_str_final-cessamount = l_str_final-cessamount + l_str_konv_zcrm-kwert.
              ENDCASE.
            ENDIF.
            CLEAR l_str_konv_zcrm.
          ENDLOOP.
          CLEAR: l_var_tabix.
        ENDIF.
        l_str_final-itccgstamount =  l_str_final-cgstamount.
        l_str_final-itcsgstamount =  l_str_final-sgstamount.
        l_str_final-itcigstamount =  l_str_final-igstamount.
        IF l_str_final-itccgstamount IS INITIAL AND l_str_final-itcsgstamount IS INITIAL AND l_str_final-itcigstamount  IS INITIAL.

        ELSE.
*-------------------------------------------From Data--------------------------------------------------------------------------------------------------------*
          CLEAR: l_str_t001w.
          CLEAR: l_str_kna1.
          READ TABLE l_tab_kna1 INTO l_str_kna1 WITH KEY kunnr = l_str_vbrk1-kunag BINARY SEARCH.
          IF sy-subrc = 0.
            CLEAR: l_str_adrc1.
            READ TABLE l_tab_adrc1 INTO l_str_adrc1 WITH  KEY  addrnumber   = l_str_kna1-adrnr BINARY SEARCH.

            l_str_final-billfromgstin = l_str_kna1-stcd3.
            l_str_final-billfromlegalname = l_str_kna1-name1.

            REPLACE ALL OCCURRENCES OF ',' IN l_str_final-billtoaddress1  WITH space.

            IF l_str_final-billfromaddress1 IS INITIAL.
              l_str_final-billfromaddress1 = l_str_adrc1-name1.
            ENDIF.
            REPLACE ALL OCCURRENCES OF ',' IN l_str_final-billfromaddress1  WITH space.

*            READ TABLE it_state INTO wa_state WITH KEY region = l_str_kna1-regio BINARY SEARCH..
*            IF sy-subrc = 0.
            l_str_final-billfromstatecode =  l_str_kna1-regio.
*            ENDIF.

          ENDIF.

          IF l_str_final-billfromgstin IS INITIAL OR l_str_final-billfromgstin = 'NA' OR l_str_final-billfromgstin = 'UNREGISTERED'.
            l_str_final-transactiontype = lc_b2c ."'B2C'.
          ELSE.
            l_str_final-transactiontype = lc_b2b."'B2B'.
          ENDIF.

          IF l_str_final-transactiontype = 'B2C'.

          ENDIF.
          IF l_str_final-taxablevalue IS INITIAL.
            l_str_final-taxablevalue = l_str_vbrk1-netwrline.
            l_str_final-grossamount  =  l_str_final-taxablevalue.
            l_str_final-documentvalue   = l_str_vbrk1-netwr + l_str_vbrk1-mwsbk.
          ELSE.
            l_str_final-grossamount  =  l_str_final-taxablevalue.
          ENDIF.

          READ TABLE l_tab_user21 INTO l_str_user21 WITH KEY bname = l_str_vbrk1-ernam.
          IF sy-subrc = 0.
            READ TABLE l_tab_adr61 INTO l_str_adr6 WITH KEY persnumber = l_str_user21-persnumber
                                                    addrnumber = l_str_user21-addrnumber.
          ENDIF.
          READ TABLE l_tab_marc INTO l_str_marc1 WITH KEY matnr = l_str_vbrk1-matnr BINARY SEARCH.
          IF sy-subrc = 0.
            l_str_final-hsn                       = l_str_marc1-steuc.         "HSN
            CALL FUNCTION 'CONVERSION_EXIT_ALPHA_OUTPUT'
              EXPORTING
                input  = l_str_final-hsn
              IMPORTING
                output = l_str_final-hsn.

          ENDIF.
          IF l_str_final-hsn+0(2) = '99'.
            l_str_final-isservice = 'Y'.
          ELSE.
            l_str_final-isservice = 'N'.
          ENDIF.
          l_str_final-quantity          = l_str_vbrk1-fkimg.             "QTY
          l_str_final-itemname            = l_str_vbrk1-matnr.

          READ TABLE l_tab_makt INTO l_str_makt1 WITH KEY matnr = l_str_vbrk1-matnr BINARY SEARCH.
          IF sy-subrc = 0.
            l_str_final-itemdescription = l_str_makt1-maktx.""product.desc
          ENDIF.

          READ TABLE it_uom INTO ls_uom WITH KEY sap_uom = l_str_vbrk1-vrkme BINARY SEARCH.
          IF sy-subrc = 0..
            l_str_final-uqc  = ls_uom-gst_uom.
          ELSE.
            l_str_final-uqc = 'OTH'.
          ENDIF.


*          IF p_rad3 = 'X'.
*            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-itemdescription WITH space.
*            REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-itemname WITH space.
*          ENDIF.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billfromlegalname WITH space.
*        REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN L_STR_FINAL-BILL_FROM_TRADE_NAME WITH SPACE.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtolegalname WITH space.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-shiptolegalname WITH space.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtocity WITH space.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-shiptocity WITH space.

          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtoaddress1 WITH space.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-shiptoaddress1 WITH space.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-billtoaddress2 WITH space.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-shiptoaddress2 WITH space.
          REPLACE ALL OCCURRENCES OF REGEX '[^[:alnum:][:space:]]' IN l_str_final-hsn WITH space.
          CONDENSE l_str_final-hsn NO-GAPS.
          l_str_final-reversecharge                = 'N'.

          IF l_str_final-taxablevalue = ''.
            CLEAR: l_str_final.
            CONTINUE.
          ENDIF.

          l_str_final-documentdate               = l_str_vbrk1-fkdat.
          l_str_final-documentnumber                 = l_str_vbrk1-xblnr.
          l_str_final-custom1                     = l_str_vbrk1-vbeln.
          l_str_final-custom2                     = l_str_vbrk1-kunag.
          l_str_final-claimrefund                = 'N'.
          l_str_final-underigstact              = 'N'.
          l_str_final-refundeligibility          = 'N'.
          l_str_final-autopushorgenerate          = 'OGST'.
          l_str_final-priceperquantity          =  l_str_final-taxablevalue / l_str_final-quantity.
          l_str_final-itemtotal                  =  l_str_final-taxablevalue + l_str_final-igstamount
                                                     + l_str_final-cgstamount + l_str_final-sgstamount + l_str_final-othercharges ."+ L_STR_FINAL-DOUCUMENTOTHERCHARGES.
          CONCATENATE l_str_vbrk1-fkdat+4(2) l_str_vbrk1-fkdat+0(4) INTO l_str_final-returnperiod.

          l_str_final-purpose = 'OGST'.
          l_str_final-serialnumber = lv_serial_no.

**** Added CRN
          l_str_final-transactionmode = 'REG'.

          IF l_str_vbrk1-vbtyp = 'O' .
            l_str_final-documenttype = lc_inv ."'INV'.
          ELSEIF l_str_vbrk1-vbtyp = 'M'.
            l_str_final-documenttype = lc_inv."'INV'.
          ELSEIF  l_str_vbrk1-vbtyp = 'P'.
            l_str_final-documenttype = 'DBN'.."'DBN'.
          ENDIF.

          IF l_str_vbrk1-bukrs <> '1000'.
            IF l_str_vbrk1-vbeln IS NOT INITIAL.
              l_str_final-customitem1 = l_str_vbrk1-vbeln.
              l_str_final-customitem2 = l_str_vbrk1-bukrs.
            ENDIF.
          ENDIF.

          CONCATENATE l_str_final-documentdate+6(2) '-' l_str_final-documentdate+4(2) '-' l_str_final-documentdate+0(4)
                    INTO l_str_final-documentdate.
          IF l_str_final-locationgstin = l_str_final-billtogstin.

          ENDIF.

          IF l_str_final-rate IS INITIAL.
            l_str_final-rate = 0.
            CONDENSE l_str_final-rate.
          ENDIF.

          IF l_str_final-hsn(2) = '99'.
            l_str_final-itceligibility = 'IS'.
          ELSE.
            l_str_final-itceligibility = 'IP'.
          ENDIF.

          READ TABLE l_tab_bkpf INTO DATA(ls_ref) WITH KEY xblnr = l_str_vbrk1-xblnr BINARY SEARCH.

          IF sy-subrc = 0.
            IF ls_ref-tcode =  'J_1IG_INV' OR ls_ref-tcode =  'ZSD_1IG_AUTO'..
              l_str_final-custom8 = l_str_bkpf-bktxt.
*              l_str_final-cr_rea = l_str_bkpf-stgrd.  "  Reason for credit debit note

              READ TABLE l_tab_bseg INTO DATA(ls_bseg_exp) WITH KEY belnr = ls_ref-belnr bukrs = ls_ref-bukrs
              gjahr = ls_ref-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
              ENDIF.
              l_str_final-custom1 = ls_ref-belnr.
              l_str_final-custom4 = ls_ref-gjahr.
              l_str_final-custom9 = ls_ref-bukrs.
              l_str_final-custom3 = ls_ref-usnam.
              l_str_final-custom7 = ls_ref-blart.
              l_str_final-customitem7 = ls_bseg_exp-hkont.
              l_str_final-customitem3 = ls_bseg_exp-prctr.
              l_str_final-custom6 = ls_bseg_exp-werks.

              CONCATENATE ls_ref-budat+4(2) ls_ref-budat(4) INTO l_str_final-returnperiod.
              l_str_final-custom2 = ls_ref-budat.
              READ TABLE l_tab_bseg_lifnr INTO DATA(ls_bseg_lifnr) WITH KEY bukrs = ls_ref-bukrs belnr = ls_ref-belnr
             gjahr = ls_ref-gjahr BINARY SEARCH.
              IF sy-subrc = 0.
                l_str_final-custom5 = ls_bseg_lifnr-lifnr.
*                l_str_final-vendor = ls_bseg_lifnr-lifnr.
                l_str_final-customitem2 = ls_bseg_lifnr-mwskz.
*                l_str_final-custom20 = ls_bseg_lifnr-bupla.
                READ TABLE  l_tab_lfa1 INTO l_str_lfa1 WITH KEY lifnr = ls_bseg_lifnr-lifnr BINARY SEARCH .
                IF sy-subrc = 0.
                  l_str_final-billfromgstin = l_str_lfa1-stcd3.
                  l_str_final-billfromstatecode = l_str_lfa1-stcd3(2).
                  l_str_final-billfromlegalname = l_str_lfa1-name1.
                  CLEAR l_str_adrc.
                  READ TABLE l_tab_adrc INTO l_str_adrc WITH KEY addrnumber = l_str_lfa1-adrnr BINARY SEARCH.
                  IF sy-subrc = 0.
                    CONCATENATE l_str_adrc-street l_str_adrc-str_suppl1 INTO l_str_final-billfromaddress1.
                    CONCATENATE l_str_final-billfromaddress1 l_str_adrc-str_suppl2 INTO l_str_final-billfromaddress1.
                    l_str_final-billfromaddress2 = l_str_adrc-str_suppl3.
                    l_str_final-billfrompincode = l_str_adrc-post_code1.

                    READ TABLE l_tab_adr6 INTO l_str_adr6 WITH KEY addrnumber = l_str_lfa1-adrnr BINARY SEARCH.
                    IF sy-subrc = 0.
                      l_str_final-billtoemail = l_str_adr6-smtp_addr.
                    ENDIF.
*                    CLEAR wa_state.
*                    READ TABLE it_state INTO wa_state WITH KEY region = l_str_adrc-region BINARY SEARCH.
*                    IF sy-subrc = 0.
                    l_str_final-billfromstatecode = l_str_adrc-region.
*                    ENDIF.
                  ENDIF.
                ENDIF.

                CLEAR: l_str_j_1bbranch1.
                READ TABLE l_tab_j_1bbranch INTO l_str_j_1bbranch1 WITH KEY bukrs = l_str_vbrk1-bukrs
                                                                           branch = ls_bseg_lifnr-bupla BINARY SEARCH. " Location GSTIN
                IF sy-subrc = 0.
                  l_str_final-billtogstin                      = l_str_j_1bbranch1-gstin.
                  l_str_final-locationgstin                       = l_str_j_1bbranch1-gstin.
                ENDIF.

                l_str_final-billtogstin  = l_str_j_1bbranch1-gstin.
                l_str_final-branch  = l_str_j_1bbranch1-branch.
                l_str_final-locationgstin  = l_str_j_1bbranch1-gstin.
                l_str_final-billtolegalname   =   l_str_j_1bbranch1-name.
                CLEAR l_str_bupadrc.
                READ TABLE l_tab_bupadrc INTO l_str_bupadrc WITH KEY addrnumber = l_str_j_1bbranch1-adrnr BINARY SEARCH.
                IF sy-subrc = 0.
                  CONCATENATE l_str_bupadrc-street l_str_bupadrc-str_suppl1 INTO l_str_final-billtoaddress1.
*                  CONCATENATE l_str_bupadrc-str_suppl2 l_str_bupadrc-str_suppl3 INTO l_str_final-billtoaddress2.
                  l_str_final-billtocity = l_str_bupadrc-city1.
                  l_str_final-billtopincode = l_str_bupadrc-post_code1.
                  l_str_final-billtostatecode = l_str_bupadrc-region.
                  l_str_final-pos = l_str_bupadrc-region.
                  IF l_str_final-billtogstin+0(2) IS NOT INITIAL.
                    l_str_final-billtostatecode = l_str_final-billtogstin+0(2).
                    l_str_final-pos = l_str_final-billtogstin+0(2).
                  ENDIF.
                ENDIF.
              ENDIF.

              IF l_str_final-reversecharge IS INITIAL.
                l_str_final-reversecharge = 'N'.
              ENDIF.
              SHIFT l_str_vbrk1-kunrg LEFT DELETING LEADING '0'.

              READ TABLE lt_mbew INTO ls_mbew WITH KEY matnr = l_str_vbrk1-matnr bwkey = l_str_vbrk1-werks BINARY SEARCH.
              IF sy-subrc = 0.
                READ TABLE lt_t030 INTO ls_t030 WITH KEY bklas = ls_mbew-bklas BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-customitem7 = ls_t030-konts.  " G/L Account
                  READ TABLE lt_skat INTO ls_skat WITH KEY saknr = ls_t030-konts.
*              " GL A/c Description
*********************** Upto here
                  IF sy-subrc = 0.
*                    l_str_final-customitem16 = ls_skat-txt50.
                  ENDIF.
                ENDIF.
              ENDIF.
              READ TABLE it_mara INTO is_mara WITH KEY matnr =  l_str_vbrk1-matnr BINARY SEARCH.
              IF sy-subrc = 0.
                READ TABLE lt_t023t INTO ls_desc WITH KEY matkl = is_mara-matkl BINARY SEARCH.
                IF sy-subrc = 0.
                  l_str_final-customitem1 = ls_desc-wgbez.
                ENDIF.
              ENDIF.
              READ TABLE lt_vbfa INTO DATA(ls_vbfa) WITH KEY vbelv = l_str_vbrk1-vgbel vbtyp_n = 'i'.
              IF sy-subrc = 0.
                READ TABLE l_tab_mkpf INTO l_str_mkpf WITH KEY mblnr = ls_vbfa-vbeln mjahr = ls_vbfa-mjahr.
                IF sy-subrc = 0.
*                  l_str_final-customitem17 = l_str_mkpf-mblnr.
*                  l_str_final-customitem18 = l_str_mkpf-budat.
                ENDIF.
              ELSE.
                READ TABLE lt_vbfa INTO ls_vbfa WITH KEY vbelv = l_str_vbrk1-vgbel vbtyp_n = 'R'.
                IF sy-subrc = 0.
                  READ TABLE l_tab_mkpf INTO l_str_mkpf WITH KEY mblnr = ls_vbfa-vbeln mjahr = ls_vbfa-mjahr.
                  IF sy-subrc = 0.
*                    l_str_final-customitem17 = l_str_mkpf-mblnr.
*                    l_str_final-customitem18 = l_str_mkpf-budat.
                  ENDIF.
                ENDIF.
              ENDIF.
              READ TABLE lt_lips INTO DATA(ls_lips) WITH KEY vbeln = l_str_vbrk1-vgbel.
              IF sy-subrc = 0.
*                l_str_final-customitem19 = ls_lips-vgbel.
                READ TABLE lt_ekko INTO DATA(ls_ekko) WITH KEY ebeln = ls_lips-vgbel.
                IF sy-subrc = 0.
*                  l_str_final-customitem20 = ls_ekko-bedat.
                ENDIF.
              ENDIF.
              APPEND l_str_final TO gt_final_inward.
            ENDIF.
          ENDIF.
        ENDIF.
        CLEAR: l_str_vbrk1.


        CLEAR: l_str_final,
               l_str_vbrk1 ,
               l_str_bseg ,
               l_str_bset ,
               l_str_kna1 ,
               l_str_t001w,
               l_str_adrc ,
               l_str_adr6 ,
               l_str_marc1 ,
               l_str_j_1bbranch1.
        CLEAR: l_str_konv.
*        CLEAR: wa_state.
      ENDLOOP.

    ENDIF.
*  if gt_final_inward is not initial.
    SELECT * FROM t007s INTO TABLE @DATA(lt_t007s)
      WHERE spras = 'E'.                                "#EC CI_GENBUFF
    SELECT * FROM cepct INTO TABLE @DATA(lt_cepct)
      WHERE spras = 'E'.                                "#EC CI_GENBUFF
    SELECT * FROM cskt INTO TABLE @DATA(lt_cskt)
      WHERE spras = 'E'.                                "#EC CI_GENBUFF
*  endif.
*    IF gt_final_inward[] IS NOT INITIAL.
*      SELECT custom1,custom4,custom9,push_date,push_time,push
*        INTO TABLE @DATA(lt_zcy_doc_push)
*        FROM zcy_doc_push
*        FOR ALL ENTRIES IN @gt_final_inward
*        WHERE custom1 = @gt_final_inward-custom1
*          AND custom4 = @gt_final_inward-custom4
*          AND custom9 = @gt_final_inward-custom9
*        ORDER BY PRIMARY KEY.
*    ENDIF.
*
*    LOOP AT gt_final_inward ASSIGNING FIELD-SYMBOL(<fs_desc>).
*      READ TABLE lt_zcy_doc_push INTO DATA(ls_zcy_doc_push)
*      WITH KEY custom1 = <fs_desc>-custom1
*               custom4 = <fs_desc>-custom4
*               custom9 = <fs_desc>-custom9 BINARY SEARCH.
*      IF sy-subrc = 0.
*        <fs_desc>-push_date = ls_zcy_doc_push-push_date.
*        <fs_desc>-push_time = ls_zcy_doc_push-push_time.
*        <fs_desc>-push = ls_zcy_doc_push-push.
*      ENDIF.
*      READ TABLE lt_t007s INTO DATA(ls_t007s) WITH KEY mwskz = <fs_desc>-customitem2.
*      IF sy-subrc = 0.
*        <fs_desc>-mwskz = ls_t007s-text1.
*        <fs_desc>-customitem6 = ls_t007s-text1.
*      ENDIF.
*      READ TABLE lt_cepct INTO DATA(ls_cepct) WITH KEY prctr = <fs_desc>-customitem3.
*      IF sy-subrc = 0.
*        <fs_desc>-prctr = ls_cepct-ktext..
*        <fs_desc>-customitem13 = ls_cepct-ktext..
*      ENDIF.
*      READ TABLE lt_cskt INTO DATA(ls_cskt) WITH KEY kostl = <fs_desc>-customitem5.
*      IF sy-subrc = 0.
*        <fs_desc>-cost = ls_cskt-ktext.
*        <fs_desc>-customitem14 = ls_cskt-ktext.
*      ENDIF.
*      READ TABLE lt_skat INTO ls_skat WITH KEY saknr = <fs_desc>-customitem8.
*      IF sy-subrc = 0.
*        <fs_desc>-gldesc = ls_skat-txt20.
*        <fs_desc>-customitem15 = ls_skat-txt20.
*      ENDIF.
*      IF <fs_desc>-transactiontype  = 'B2B'.
*        IF <fs_desc>-rate = '' OR <fs_desc>-rate IS INITIAL.
*          <fs_desc>-rate = '0'.
*        ENDIF.
*      ENDIF.
*      CONDENSE <fs_desc>-custom19.
*      CONDENSE <fs_desc>-customitem12.
*      CONDENSE <fs_desc>-customitem18.
*      CONDENSE <fs_desc>-customitem20.
*      CONDENSE <fs_desc>-customitem37.
**    IF <FS_DESC>-CUSTOM19 IS INITIAL OR <FS_DESC>-CUSTOM19 = '00000000'.
**      CLEAR: <FS_DESC>-CUSTOM19.
**    ELSE.
**      CONCATENATE <FS_DESC>-CUSTOM19+6(2) '-' <FS_DESC>-CUSTOM19+4(2) '-' <FS_DESC>-CUSTOM19+0(4)
**                INTO <FS_DESC>-CUSTOM19.
**    ENDIF.
*      IF <fs_desc>-customitem12 IS INITIAL OR <fs_desc>-customitem12 = '00000000'.
*        CLEAR: <fs_desc>-customitem12.
*      ELSE.
*        CONCATENATE <fs_desc>-customitem12+6(2) '-' <fs_desc>-customitem12+4(2) '-' <fs_desc>-customitem12+0(4)
*                  INTO <fs_desc>-customitem12.
*      ENDIF.
*      IF <fs_desc>-customitem18 IS INITIAL OR <fs_desc>-customitem18 = '00000000'.
*        CLEAR: <fs_desc>-customitem18.
*      ELSE.
*        CONCATENATE <fs_desc>-customitem18+6(2) '-' <fs_desc>-customitem18+4(2) '-' <fs_desc>-customitem18+0(4)
*                  INTO <fs_desc>-customitem18.
*      ENDIF.
*      IF <fs_desc>-customitem20 IS INITIAL OR <fs_desc>-customitem20 = '00000000'.
*        CLEAR: <fs_desc>-customitem20.
*      ELSE.
*        CONCATENATE <fs_desc>-customitem20+6(2) '-' <fs_desc>-customitem20+4(2) '-' <fs_desc>-customitem20+0(4)
*                  INTO <fs_desc>-customitem20.
*      ENDIF.
*      IF <fs_desc>-customitem37 IS INITIAL OR <fs_desc>-customitem37 = '00000000'.
*        CLEAR: <fs_desc>-customitem37.
*      ELSE.
*        CONCATENATE <fs_desc>-customitem37+6(2) '-' <fs_desc>-customitem37+4(2) '-' <fs_desc>-customitem37+0(4)
*                  INTO <fs_desc>-customitem37.
*      ENDIF.
*      IF <fs_desc>-rate = '' OR <fs_desc>-rate = '0.00' OR <fs_desc>-rate = '0'.
*        <fs_desc>-tax  = 0.
*      ELSE.
*        <fs_desc>-tax = ( ( ( <fs_desc>-cgstamount + <fs_desc>-sgstamount + <fs_desc>-igstamount ) / <fs_desc>-rate ) * 100 ).
*      ENDIF.
*      <fs_desc>-custom14 = <fs_desc>-custom2.
*
*    ENDLOOP.
*    IF p_tax = 'X'.
*      DELETE gt_final_inward WHERE igstamount IS INITIAL AND cgstamount IS INITIAL AND sgstamount IS INITIAL.
*    ENDIF.
*
*    IF p_item IS INITIAL AND p_rad3 = 'X'.
*      LOOP AT gt_final_inward ASSIGNING FIELD-SYMBOL(<ls_sign>).
*        IF <ls_sign>-documenttype = 'INV'.
*          IF <ls_sign>-igstamount < 0 OR <ls_sign>-sgstamount < 0 OR <ls_sign>-cgstamount < 0.
*            <ls_sign>-documenttype = 'NA'.
*          ENDIF.
*        ENDIF.
*      ENDLOOP.
*
*      gt_final_inward_na[] = gt_final_inward[].
*      DELETE gt_final_inward_na WHERE documenttype NE 'NA'.
*      SORT gt_final_inward_na BY custom1 custom4 custom9.
*
*      LOOP AT gt_final_inward ASSIGNING FIELD-SYMBOL(<ls_sign1>) WHERE documenttype = 'INV'.
*        READ TABLE gt_final_inward_na INTO DATA(ls_del) WITH KEY custom1 = <ls_sign1>-custom1
*        custom4 = <ls_sign1>-custom4 custom9 = <ls_sign1>-custom9 BINARY SEARCH.
*        IF sy-subrc = 0.
*          <ls_sign1>-documenttype = 'NA'.
*        ENDIF.
*      ENDLOOP.
*    ENDIF.


  ENDMETHOD.
ENDCLASS.
```
