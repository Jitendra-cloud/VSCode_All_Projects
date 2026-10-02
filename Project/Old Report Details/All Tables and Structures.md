# GSTR2A Payment Block – ABAP Objects

This file contains the supplied DDIC definitions with **ABAP syntax highlighting**.  
The `abap` language identifier on each fenced code block enables syntax colouring in Markdown viewers/editors that support ABAP highlighting.

---

## Table 1 – `z2a_t_pay_block`

```abap
@EndUserText.label : 'GSTR2A Payment Block Data.'

@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory       : #TRANSPARENT
@AbapCatalog.deliveryClass       : #A
@AbapCatalog.dataMaintenance     : #RESTRICTED

define table z2a_t_pay_block
{
  key mandt          : mandt not null;
  key custom1        : zcy_det_custom1 not null;
  key custom4        : zcy_det_custom4 not null;
  key custom9        : zcy_det_custom9 not null;

      returnperiod    : zcy_det_returnperiod;
      locationgstin   : zcy_det_locationgstin;
      documentnumber  : zcy_det_documentnumber;
      documentdate    : bldat;
      financialyear   : zcy_det_financialyear;
      currency        : abap.cuky;

      include zcy_str_2a_pay_block_status;
}
```

---

## Table 2 – `tvarvc`

```abap
@EndUserText.label : 'Tabelle der Variantenvariablen (mandantenabhängig)'

@AbapCatalog.enhancement.category : #NOT_CLASSIFIED
@AbapCatalog.tableCategory        : #TRANSPARENT
@AbapCatalog.deliveryClass        : #G
@AbapCatalog.dataMaintenance      : #ALLOWED

define table tvarvc {

  key mandt      : symandt      not null;
  key name       : rvari_vnam   not null;
  key type       : rsscr_kind   not null;
  key numb       : tvarv_numb   not null;

      sign        : tvarv_sign;
      opti        : tvarv_opti;
      low         : rvari_val_255;
      high        : rvari_val_255;
      clie_indep   : sychar01;
}
```

---

## Structure 1 – `zcy_str_2a_pay_block`

```abap
@EndUserText.label : 'GSTR2A Payment Block Program - ALV Structure.'

@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY

define structure zcy_str_2a_pay_block
{
  procstat               : icon_d;
  cbox                   : checkbox;
  reco_stat              : icon_d;

  financialyear          : zcy_det_financialyear;
  returnperiod           : zcy_det_returnperiod;
  locationgstin          : zcy_det_locationgstin;
  documentnumber         : zcy_det_documentnumber;
  documentdate           : bldat;

  custom1                : zcy_det_custom1;
  custom4                : zcy_det_custom4;
  custom9                : zcy_det_custom9;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  documentvalue          : zcy_det_documentvalue;

  pushstatus             : zcy_det_pushstatus;
  pushdate               : zcy_det_pushdate;
  reconciliationsection  : zcy_det_reconciliationsection;
  reason                 : zcy_det_reason;

  filingdate             : zcy_det_filingdate;
  filingreturnperiod     : zcy_det_filingreturnperiod;
  filingstatus           : zcy_det_filingstatus;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  rate                   : zcy_det_rate;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  taxablevalue           : zcy_det_taxablevalue;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  igstamount             : zcy_det_igstamount;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  cgstamount             : zcy_det_cgstamount;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  sgstamount             : zcy_det_sgstamount;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  totaltax               : hwste;

  rec_date               : datum;
  rec_time               : uzeit;

  include zcy_str_2a_pay_block_status;

  @EndUserText.label : 'Docuemnt Processing Status Description'
  status_desc            : abap.char(50);

  @EndUserText.label : 'Vendor Block Type Description'
  block_type_desc        : abap.char(50);

  vendor_name            : name1_gp;
  blart                  : blart;
  qblock                  : char1;
  preconciliationsection : char40;

  cpgstin                : zcy_det_cpgstin;
  cptradename            : zcy_det_cptradename;
  cplegalname            : zcy_det_cplegalname;
  cpdocumentnumber       : zcy_det_cpdocumentnumber;
  cpdocumentdate         : zcy_det_cpdocumentdate;
  cpvalue                : zcy_det_cpvalue;
  cppos                  : zcy_det_cppos;
  cpreversecharge        : zcy_det_cpreversecharge;
  cptransactiontype      : zcy_det_cptransactiontype;
  cptaxpayertype         : zcy_det_cptaxpayertype;
  cptaxablevalue         : zcy_det_cptaxablevalue;
  cprate                  : zcy_det_cprate;

  cpigstamount           : zcy_det_cpigstamount;
  cpcgstamount           : zcy_det_cpcgstamount;
  cpsgstamount           : zcy_det_cpsgstamount;
  cpcessamount           : zcy_det_cpcessamount;

  cpfilingstatus         : zcy_det_cpfilingstatus;
  cpfilingdate           : zcy_det_cpfilingdate;
  cpfilingreturnperiod   : zcy_det_cpfilingreturnperiod;

  isgstr3bfiled          : zcy_det_isgstr3bfiled;
  isgstr3bfildate        : zcy_det_is3bfilingdate;

  cpitcavailability      : zcy_det_cpitcavailability;
  cpreturnperiod         : zcy_det_cpreturnperiod;
  cpisamendment          : zcy_det_cpisamendment;
  cpavailableingstr2b    : zcy_det_cpavailableingstr2b;
  cpgstr2breturnperiod   : zcy_det_cpgstr2breturnperiod;
  cpavailableingstr98a   : zcy_det_cpavailableingstr98a;
  cpitcclaimmonth        : zcy_det_cpitcclaimmonth;

  billfromlegalname      : zcy_det_billfromlegalname;
  billfromtradename      : zcy_det_billfromtradename;

  pos                    : zcy_det_pos;
  reversecharge          : zcy_det_reversecharge;
  transactiontype        : zcy_det_transactiontype;
  taxpayertype           : zcy_det_taxpayertype;

  @Semantics.amount.currencyCode : 'komk.waerk'
  cessamount             : zcy_det_cessamount;

  isamendment            : zcy_det_isamendment;

  custom2                : zcy_det_custom2;
  custom3                : zcy_det_custom3;
  custom5                : zcy_det_custom5;
  custom6                : zcy_det_custom6;
  custom7                : zcy_det_custom7;
  custom8                : zcy_det_custom8;
  custom10               : zcy_det_custom10;

  hsn                    : zcy_det_hsn;
  itceligibility         : zcy_det_itceligibility;

  @Semantics.amount.currencyCode : 'komk.waerk'
  itcigstamount          : zcy_det_itcigstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
  itccgstamount          : zcy_det_itccgstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
  itcsgstamount          : zcy_det_itcsgstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
  itccessamount          : zcy_det_itccessamount;

  customitem3            : zcy_det_customitem3;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  valuediff              : zcy_det_valuediff;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  taxablediff            : zcy_det_taxablediff;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  taxdiff                : zcy_det_taxdiff;

  msme                   : zcy_msme;
}
```

---

## Structure 2 – `zcy_str_2a_pay_block_status`

```abap
@EndUserText.label : 'Status fields for GSTR2A based payment block program'

@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY

define structure zcy_str_2a_pay_block_status {

  @Semantics.amount.currencyCode : 'vbak.waerk'
  fi_doc_value           : dmbtr;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  fi_tax_value           : hwste;

  fi_tax_code             : mwskz;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  fi_tax_rate             : kbetr;

  status                  : zcy_det_2a_pay_block_status;
  gst_part                : j_1ig_partner;
  vendor                  : lifnr;
  mindk                   : mindk;
  vendor_block_type       : zcy_det_block_type;
  payment_due_date        : faedt_fpos;
  clearing_no             : augbl;
  clearing_date           : augdt;
  expected_filing_date    : zcy_det_exp_fil_date;
  due_date_before_filing  : zcy_det_due_b4_filing;
  cleared_before_filing   : zcy_det_clr_b4_fil;

  grc_check_applicable    : zcy_det_grc_appl;
  grc_checked_on          : zcy_det_grc_date;
  grc_checked_at          : zcy_det_grc_time;
  grc_checked_by          : zcy_det_grc_user;
  grc_score               : zcy_det_grc_score;
  gstr3b_score             : zcy_det_grc3_score;
  grc_check_result        : zcy_det_grc_result;
  grc_check_comment       : zcy_det_grc_comment;

  blocked_on              : zcy_det_block_date;
  blocked_at              : zcy_det_block_time;
  blocked_by              : zcy_det_block_user;
  block_comment           : zcy_det_block_comment;

  split_comp_code         : zcy_det_split_comp;
  split_gst_doc_num       : zcy_det_split_gst_doc;
  split_year              : zcy_det_split_year;
  split_on                : zcy_det_split_date;
  split_at                : zcy_det_split_time;
  split_by                : zcy_det_split_user;
  split_comment           : zcy_det_split_comment;

  split_rev_comp_code     : zcy_det_split_rev_comp;
  split_rev_doc_num       : zcy_det_split_rev_doc;
  split_rev_year          : zcy_det_split_rev_year;
  split_rev_on            : zcy_det_split_rev_date;
  split_rev_at            : zcy_det_split_rev_time;
  split_rev_by            : zcy_det_split_rev_user;
  split_rev_comment       : zcy_det_split_rev_comment;

  jv_comp_code            : zcy_det_jv_comp;
  jv_doc_num              : zcy_det_jv_doc;
  jv_year                 : zcy_det_jv_year;
  jv_posted_on            : zcy_det_jv_date;
  jv_posted_at            : zcy_det_jv_time;
  jv_posted_by            : zcy_det_jv_user;
  jv_comment              : zcy_det_jv_comment;

  jv_rev_comp_code        : zcy_det_jv_rev_comp;
  jv_rev_doc_num          : zcy_det_jv_rev_doc;
  jv_rev_year             : zcy_det_jv_rev_year;
  jv_reversed_on          : zcy_det_jv_rev_date;
  jv_reversed_at          : zcy_det_jv_rev_time;
  jv_reversed_by          : zcy_det_jv_rev_user;
  jv_rev_comment          : zcy_det_jv_rev_comment;

  released_on             : zcy_det_release_date;
  released_at             : zcy_det_release_time;
  released_by             : zcy_det_release_user;
  release_comment         : zcy_det_release_comment;

  debit_note_comp         : zcy_det_dn_comp;
  debit_note_num          : zcy_det_dn_num;
  debit_note_year         : zcy_det_dn_year;
  debit_note_posted_on    : zcy_det_dn_date;
  debit_note_posted_at    : zcy_det_dn_time;
  debit_note_posted_by    : zcy_det_dn_user;
  debit_note_comment      : zcy_det_dn_comment;

  debit_note_rev_comp     : zcy_det_dn_rev_comp;
  debit_note_rev_num      : zcy_det_dn_rev_num;
  debit_note_rev_year     : zcy_det_dn_rev_year;
  debit_note_reversed_on  : zcy_det_dn_rev_date;
  debit_note_reversed_at  : zcy_det_dn_rev_time;
  debit_note_reversed_by  : zcy_det_dn_rev_user;
  debit_note_rev_comment  : zcy_det_dn_rev_comment;

  message                 : zcy_det_message;

  last_checked_on         : zcy_det_lc_date;
  last_checked_at         : zcy_det_lc_time;
  last_checked_by         : zcy_det_lc_user;

  processing_options      : zcy_det_proc_opt;

  wronggst                : char15;
  gstin                   : zcy_det_billfromgstin;
  gstin_part              : char20;
  documenttype            : zcy_det_documenttype;
  remark                  : zcyg_remarks;
}
```

---

## Table 3 – `zcy_tab_rrp_e`

```abap
@EndUserText.label : 'Regular Returns Purchase Export.'

@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory        : #TRANSPARENT
@AbapCatalog.deliveryClass        : #A
@AbapCatalog.dataMaintenance      : #ALLOWED

define table zcy_tab_rrp_e {

  key mandt                      : mandt not null;
  key custom1                    : zcy_det_custom1 not null;
  key custom4                    : zcy_det_custom4 not null;
  key custom9                    : zcy_det_custom9 not null;

      returnperiod                   : zcy_det_returnperiod;
      locationgstin                  : zcy_det_locationgstin;
      documentnumber                 : zcy_det_documentnumber;
      serialnumber                   : zcy_det_serialnumber;
      documentdate                   : bldat;
      billfromgstin                  : zcy_det_billfromgstin;
      financialyear                  : zcy_det_financialyear;
      mapperid                       : zcy_det_mapperid;
      supplytype                     : zcy_det_supplytype;
      purpose                        : zcy_det_purpose;
      locationname                   : zcy_det_locationname;
      irn                            : zcy_det_irn;
      liabilitydischargereturnperiod : zcy_det_liabilitydischargeretu;
      itcclaimreturnperiod           : zcy_det_itcclaimreturnperiod;
      documenttype                   : zcy_det_documenttype;
      transactiontype                : zcy_det_transactiontype;
      taxpayertype                   : zcy_det_taxpayertype;
      transactionnature              : zcy_det_transactionnature;
      documentseriescode             : zcy_det_documentseriescode;
      billfromlegalname              : zcy_det_billfromlegalname;
      billfromtradename              : zcy_det_billfromtradename;
      billfromvendorcode             : zcy_det_billfromvendorcode;
      billfromaddress1               : zcy_det_billfromaddress1;
      billfromaddress2               : zcy_det_billfromaddress2;
      billfromcity                   : zcy_det_billfromcity;
      billfromstatecode              : zcy_det_billfromstatecode;
      billfrompincode                : zcy_det_billfrompincode;
      billfromphone                  : zcy_det_billfromphone;
      billfromemail                  : zcy_det_billfromemail;
      dispatchfromgstin              : zcy_det_dispatchfromgstin;
      dispatchfromtradename          : zcy_det_dispatchfromtradename;
      dispatchfromvendorcode         : zcy_det_dispatchfromvendorcode;
      dispatchfromaddress1           : zcy_det_dispatchfromaddress1;
      dispatchfromaddress2           : zcy_det_dispatchfromaddress2;
      dispatchfromcity               : zcy_det_dispatchfromcity;
      dispatchfromstatecode          : zcy_det_dispatchfromstatecode;
      dispatchfrompincode            : zcy_det_dispatchfrompincode;
      billtogstin                    : zcy_det_billtogstin;
      billtolegalname                : zcy_det_billtolegalname;
      billtotradename                : zcy_det_billtotradename;
      billtovendorcode               : zcy_det_billtovendorcode;
      billtoaddress1                 : zcy_det_billtoaddress1;
      billtoaddress2                 : zcy_det_billtoaddress2;
      billtocity                     : zcy_det_billtocity;
      billtostatecode                : zcy_det_billtostatecode;
      billtopincode                  : zcy_det_billtopincode;
      billtophone                    : zcy_det_billtophone;
      billtoemail                    : zcy_det_billtoemail;
      shiptogstin                    : zcy_det_shiptogstin;
      shiptolegalname                : zcy_det_shiptolegalname;
      shiptotradename                : zcy_det_shiptotradename;
      shiptovendorcode               : zcy_det_shiptovendorcode;
      shiptoaddress1                 : zcy_det_shiptoaddress1;
      shiptoaddress2                 : zcy_det_shiptoaddress2;
      shiptocity                     : zcy_det_shiptocity;
      shiptopincode                  : zcy_det_shiptopincode;
      paymenttype                    : zcy_det_paymenttype;
      paymentmode                    : zcy_det_paymentmode;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      paymentamount                  : zcy_det_paymentamount;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      advancepaidamount              : zcy_det_advancepaidamount;

      paymentdate                    : zcy_det_paymentdate;
      paymentremarks                 : zcy_det_paymentremarks;
      paymentterms                   : zcy_det_paymentterms;
      paymentinstruction             : zcy_det_paymentinstruction;
      payeename                      : zcy_det_payeename;
      payeeaccountnumber             : zcy_det_payeeaccountnumber;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      paymentamountdue               : zcy_det_paymentamountdue;

      ifsc                           : zcy_det_ifsc;
      credittransfer                 : zcy_det_credittransfer;
      directdebit                    : zcy_det_directdebit;
      creditdays                     : zcy_det_creditdays;
      creditavaileddate              : zcy_det_creditavaileddate;
      creditreversaldate             : zcy_det_creditreversaldate;
      refdocumentremarks             : zcy_det_refdocumentremarks;
      refdocumentperiodstartdate     : zcy_det_refdocumentperiodstart;
      refdocumentperiodenddate       : zcy_det_refdocumentperiodendda;
      invno                          : zcy_det_invno;
      invdt                          : zcy_det_invdt;
      refcontractdetails             : zcy_det_refcontractdetails;
      additionalsupportingdocumentde : zcy_det_additionalsupportingdo;
      portcode                       : zcy_det_portcode;
      documentcurrencycode           : zcy_det_documentcurrencycode;
      destinationcountry             : zcy_det_destinationcountry;
      pos                            : zcy_det_pos;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      documentvalue                  : zcy_det_documentvalue;

      documentdiscount               : zcy_det_documentdiscount;
      documentothercharges           : zcy_det_documentothercharges;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      documentvalueinforeigncurrency : zcy_det_documentvalueinforeign;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      roundoffamount                 : zcy_det_roundoffamount;

      differentialpercentage         : zcy_det_differentialpercentage;
      reversecharge                  : zcy_det_reversecharge;
      claimrefund                    : zcy_det_claimrefund;
      underigstact                   : zcy_det_underigstact;
      refundeligibility              : zcy_det_refundeligibility;
      pnroruniquenumber              : zcy_det_pnroruniquenumber;
      availprovisionalitc            : zcy_det_availprovisionalitc;
      originalstatecode              : zcy_det_originalstatecode;
      originaldocumentnumber         : zcy_det_originaldocumentnumber;
      originaldocumentdate           : zcy_det_originaldocumentdate;
      originalreturnperiod           : zcy_det_originalreturnperiod;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      originaltaxablevalue           : zcy_det_originaltaxablevalue;

      toemailaddresses               : zcy_det_toemailaddresses;
      tomobilenumbers                : zcy_det_tomobilenumbers;
      jworiginaldocumentnumber       : zcy_det_jworiginaldocumentnumb;
      jworiginaldocumentdate         : zcy_det_jworiginaldocumentdate;
      jwdocumentnumber               : zcy_det_jwdocumentnumber;
      jwdocumentdate                 : zcy_det_jwdocumentdate;
      autodraftsource                : zcy_det_autodraftsource;
      irngenerationdate              : zcy_det_irngenerationdate;
      isamendment                    : zcy_det_isamendment;
      isavailableingstr2b            : zcy_det_isavailableingstr2b;
      itcavailability                : zcy_det_itcavailability;
      itcunavailabilityreason        : zcy_det_itcunavailabilityreaso;
      custom2                        : zcy_det_custom2;
      custom3                        : zcy_det_custom3;
      custom5                        : zcy_det_custom5;
      custom6                        : zcy_det_custom6;
      custom7                        : zcy_det_custom7;
      custom8                        : zcy_det_custom8;
      custom10                       : zcy_det_custom10;
      custom20                       : zcy_det_custom20;
      natureofjwdone                 : zcy_det_natureofjwdone;
      lossunitofmeasure              : zcy_det_lossunitofmeasure;

  @Semantics.quantity.unitOfMeasure : 'vbap.zieme'
      losstotalquantity              : zcy_det_losstotalquantity;

      pusherrors                     : zcy_det_pusherrors;
      pushstatus                     : zcy_det_pushstatus;
      gstaction                      : zcy_det_gstaction;
      reconciliationsection          : zcy_det_reconciliationsection;
      pushdate                       : zcy_det_pushdate;
      cancelleddate                  : zcy_det_cancelleddate;
      ispregstregime                 : zcy_det_ispregstregime;
      amendedtype                    : zcy_det_amendedtype;
      isgstr3bfiled                  : zcy_det_isgstr3bfiled;
      isgstr3bfildate                : zcy_det_is3bfilingdate;
      filingdate                     : zcy_det_filingdate;
      filingreturnperiod             : zcy_det_filingreturnperiod;
      filingstatus                   : zcy_det_filingstatus;
      isservice                      : zcy_det_isservice;
      hsn                            : zcy_det_hsn;
      productcode                    : zcy_det_productcode;
      name                           : zcy_det_name;
      description                    : zcy_det_description;
      barcode                        : zcy_det_barcode;
      uqc                            : zcy_det_uqc;

  @Semantics.quantity.unitOfMeasure : 'vbap.zieme'
      quantity                       : zcy_det_quantity;

      freequantity                   : zcy_det_freequantity;

  @Semantics.amount.currencyCode : 'rv61a.koei1'
      rate                           : zcy_det_rate;

  @Semantics.amount.currencyCode : 'rv61a.koei1'
      cessrate                       : zcy_det_cessrate;

      statecessrate                  : zcy_det_statecessrate;

  @Semantics.amount.currencyCode : 'rv61a.koei1'
      cessnonadvaloremrate           : zcy_det_cessnonadvaloremrate;

      priceperquantity               : zcy_det_priceperquantity;

  @Semantics.amount.currencyCode : 'komk.waerk'
      discountamount                 : zcy_det_discountamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      grossamount                    : zcy_det_grossamount;

      othercharges                   : zcy_det_othercharges;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      taxablevalue                   : zcy_det_taxablevalue;

  @Semantics.amount.currencyCode : 'komk.waerk'
      igstamount                     : zcy_det_igstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      cgstamount                     : zcy_det_cgstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      sgstamount                     : zcy_det_sgstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      cessamount                     : zcy_det_cessamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      statecessamount                : zcy_det_statecessamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      cessnonadvaloremamount         : zcy_det_cessnonadvaloremamount;

      statecessnonadvaloremamount    : zcy_det_statecessnonadvalorema;
      taxtype                        : zcy_det_taxtype;
      itceligibility                 : zcy_det_itceligibility;

  @Semantics.amount.currencyCode : 'komk.waerk'
      itcigstamount                  : zcy_det_itcigstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      itccgstamount                  : zcy_det_itccgstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      itcsgstamount                  : zcy_det_itcsgstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
      itccessamount                  : zcy_det_itccessamount;

      customitem1                    : zcy_det_customitem1;
      customitem2                    : zcy_det_customitem2;
      customitem3                    : zcy_det_customitem3;
      customitem4                    : zcy_det_customitem4;
      customitem5                    : zcy_det_customitem5;
      customitem6                    : zcy_det_customitem6;
      customitem7                    : zcy_det_customitem7;
      customitem8                    : zcy_det_customitem8;
      customitem9                    : zcy_det_customitem9;
      customitem10                   : zcy_det_customitem10;

      cpgstin                        : zcy_det_cpgstin;
      cptradename                    : zcy_det_cptradename;
      cplegalname                    : zcy_det_cplegalname;
      cporiginaldocumentnumber       : zcy_det_cporiginaldocumentnum;
      cporiginaldocumentdate         : zcy_det_cporiginaldocumentdate;
      cpportcode                     : zcy_det_cpportcode;
      cpdocumentnumber               : zcy_det_cpdocumentnumber;
      cpdocumentdate                 : zcy_det_cpdocumentdate;
      cpvalue                        : zcy_det_cpvalue;
      cppos                          : zcy_det_cppos;
      cpreversecharge                : zcy_det_cpreversecharge;
      cpdifferentialpercentage       : zcy_det_cpdifferentialpercent;
      cptransactiontype              : zcy_det_cptransactiontype;
      cptaxpayertype                 : zcy_det_cptaxpayertype;
      cptaxablevalue                 : zcy_det_cptaxablevalue;
      cprate                         : zcy_det_cprate;
      cpigstamount                   : zcy_det_cpigstamount;
      cpcgstamount                   : zcy_det_cpcgstamount;
      cpsgstamount                   : zcy_det_cpsgstamount;
      cpcessamount                   : zcy_det_cpcessamount;
      cpfilingstatus                 : zcy_det_cpfilingstatus;
      cpsuppliercancellationdate     : zcy_det_cpsuppliercancellation;
      cpfilingdate                   : zcy_det_cpfilingdate;
      cpfilingreturnperiod           : zcy_det_cpfilingreturnperiod;
      cpautodraftsource              : zcy_det_cpautodraftsource;
      cpirn                          : zcy_det_cpirn;
      cpirngenerationdate            : zcy_det_cpirngenerationdate;
      cprefprecedingdocumentdetails  : zcy_det_cprefprecedingdocument;
      cpavailableingstr2b            : zcy_det_cpavailableingstr2b;
      cpgstr2breturnperiod           : zcy_det_cpgstr2breturnperiod;
      cpavailableingstr98a           : zcy_det_cpavailableingstr98a;
      cpitcavailability              : zcy_det_cpitcavailability;
      cpitcunavailabilityreason      : zcy_det_cpitcunavailabilityrea;
      cpitcclaimmonth                : zcy_det_cpitcclaimmonth;
      cpreturnperiod                 : zcy_det_cpreturnperiod;
      cpisamendment                  : zcy_det_cpisamendment;
      cporiginalreturnperiod         : zcy_det_cporiginalreturnperiod;
      cpamendedtype                  : zcy_det_cpamendedtype;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      valuediff                      : zcy_det_valuediff;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      taxablediff                    : zcy_det_taxablediff;

  @Semantics.amount.currencyCode : 'vbak.waerk'
      taxdiff                        : zcy_det_taxdiff;

      reason                         : zcy_det_reason;
      rec_date                       : datum;
      rec_time                       : uzeit;
}
```

---

## Object Relationship

```text
z2a_t_pay_block
       |
       | INCLUDE
       v
zcy_str_2a_pay_block_status


zcy_str_2a_pay_block
       |
       | INCLUDE
       v
zcy_str_2a_pay_block_status


tvarvc
       |
       | Variant / selection variables
       v
GSTR2A Payment Block Program


zcy_tab_rrp_e
       |
       | Regular Returns Purchase Export data
       v
GSTR / Purchase / Reconciliation processing
```

> **Note:** I removed the literal `**;**` markers from Table 3 because those are Markdown formatting artifacts in the supplied text, not ABAP syntax. The ABAP definitions themselves were otherwise retained as supplied.
