# Pay Block CDS Views and Helper CDS Views

> Formatted from the uploaded source file. CDS source statements and business logic are preserved; only Markdown headings, code fences, and spacing between sections are added.

## Table of Contents

- [ZPB_I_2A_PAY_BLOCK — Interface for Pay block](#zpb-i-2a-pay-block)
- [ZPB_I_2A_PAY_BLOCK_LY1 — Interface for Pay block layer 1](#zpb-i-2a-pay-block-ly1)
- [ZPB_I_2A_PAY_BLOCK_LY2 — Pay Block LY2](#zpb-i-2a-pay-block-ly2)
- [ZPB_I_2A_PAY_BLOCK_LY3 — Pay Block LY3](#zpb-i-2a-pay-block-ly3)
- [ZPB_I_2A_PAY_BLOCK_LY4 — Pay Block LY4](#zpb-i-2a-pay-block-ly4)
- [ZPB_R_lt_bsik — lt_bsik](#zpb-r-lt-bsik)
- [ZPB_R_MT_ACC_DATA — MT_ACC_DATA](#zpb-r-mt-acc-data)
- [ZPB_R_MT_ACC_DATA_LY1 — MT_ACC_DATA_LY1](#zpb-r-mt-acc-data-ly1)
- [ZPB_R_MT_ACC_DOC — MT_ACC_DOC](#zpb-r-mt-acc-doc)
- [ZPB_R_MT_ACC_DOC_F — MT_ACC_DOC_F](#zpb-r-mt-acc-doc-f)
- [ZPB_R_MT_TAX_DATA — MT_TAX_DATA](#zpb-r-mt-tax-data)
- [ZPB_R_MT_TAX_TOTAL — Aggregated FI Tax Total](#zpb-r-mt-tax-total)
- [ZPB_R_MT_VENDOR_DATA — MT VENDOR DATA](#zpb-r-mt-vendor-data)
- [ZPB_R_MT_VENDOR_DATA_M — MT VENDOR DATA M](#zpb-r-mt-vendor-data-m)
- [zpb_r_vendor_block_type — Vendor Block Type](#zpb-r-vendor-block-type)

---

## ZPB_I_2A_PAY_BLOCK

**Label:** Interface for Pay block

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Interface for Pay block'
@Metadata.ignorePropagatedAnnotations: false
define root view entity ZPB_I_2A_PAY_BLOCK


  as select from    zcy_tab_rrp_e    as a
    left outer join z2a_t_pay_block  as b on  a.returnperiod   = b.returnperiod
                                          and a.locationgstin  = b.locationgstin
                                          and a.documentnumber = b.documentnumber
                                          and a.documentdate   = b.documentdate
                                          and a.custom1        = b.custom1
                                          and a.custom4        = b.custom4
                                          and a.custom9        = b.custom9
    left outer join zcy_t_2a_recstat as c on a.reconciliationsection = c.reco_status
                                          and(
                                            c.category               = 'X'
                                          )
    left outer join zcyg_panno_exmp  as p on p.panno = substring(
      b.gstin, 3, 10
    )


{
  key a.custom1                                       as Custom1,
  key a.custom4                                       as Custom4,
  key a.custom9                                       as Custom9,
      a.returnperiod                                  as Returnperiod,
      a.locationgstin                                 as Locationgstin,
      a.documentnumber                                as Documentnumber,
      a.serialnumber                                  as Serialnumber,
      a.documentdate                                  as Documentdate,
      a.billfromgstin                                 as Billfromgstin,
      a.financialyear                                 as Financialyear,
      a.mapperid                                      as Mapperid,
      a.supplytype                                    as Supplytype,
      a.purpose                                       as Purpose,
      a.locationname                                  as Locationname,
      a.irn                                           as Irn,
      a.liabilitydischargereturnperiod                as Liabilitydischargereturnperiod,
      a.itcclaimreturnperiod                          as Itcclaimreturnperiod,
      a.documenttype                                  as Documenttype,
      a.transactiontype                               as Transactiontype,
      a.taxpayertype                                  as Taxpayertype,
      a.transactionnature                             as Transactionnature,
      a.documentseriescode                            as Documentseriescode,
      a.billfromlegalname                             as Billfromlegalname,
      a.billfromtradename                             as Billfromtradename,
      a.billfromvendorcode                            as Billfromvendorcode,
      a.billfromaddress1                              as Billfromaddress1,
      a.billfromaddress2                              as Billfromaddress2,
      a.billfromcity                                  as Billfromcity,
      a.billfromstatecode                             as Billfromstatecode,
      a.billfrompincode                               as Billfrompincode,
      a.billfromphone                                 as Billfromphone,
      a.billfromemail                                 as Billfromemail,
      a.dispatchfromgstin                             as Dispatchfromgstin,
      a.dispatchfromtradename                         as Dispatchfromtradename,
      a.dispatchfromvendorcode                        as Dispatchfromvendorcode,
      a.dispatchfromaddress1                          as Dispatchfromaddress1,
      a.dispatchfromaddress2                          as Dispatchfromaddress2,
      a.dispatchfromcity                              as Dispatchfromcity,
      a.dispatchfromstatecode                         as Dispatchfromstatecode,
      a.dispatchfrompincode                           as Dispatchfrompincode,
      a.billtogstin                                   as Billtogstin,
      a.billtolegalname                               as Billtolegalname,
      a.billtotradename                               as Billtotradename,
      a.billtovendorcode                              as Billtovendorcode,
      a.billtoaddress1                                as Billtoaddress1,
      a.billtoaddress2                                as Billtoaddress2,
      a.billtocity                                    as Billtocity,
      a.billtostatecode                               as Billtostatecode,
      a.billtopincode                                 as Billtopincode,
      a.billtophone                                   as Billtophone,
      a.billtoemail                                   as Billtoemail,
      a.shiptogstin                                   as Shiptogstin,
      a.shiptolegalname                               as Shiptolegalname,
      a.shiptotradename                               as Shiptotradename,
      a.shiptovendorcode                              as Shiptovendorcode,
      a.shiptoaddress1                                as Shiptoaddress1,
      a.shiptoaddress2                                as Shiptoaddress2,
      a.shiptocity                                    as Shiptocity,
      a.shiptostatecode                               as Shiptostatecode,
      a.shiptopincode                                 as Shiptopincode,
      a.paymenttype                                   as Paymenttype,
      a.paymentmode                                   as Paymentmode,
      @Semantics.amount.currencyCode: 'Currency'
      a.paymentamount                                 as Paymentamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.advancepaidamount                             as Advancepaidamount,
      a.paymentdate                                   as Paymentdate,
      a.paymentremarks                                as Paymentremarks,
      a.paymentterms                                  as Paymentterms,
      a.paymentinstruction                            as Paymentinstruction,
      a.payeename                                     as Payeename,
      a.payeeaccountnumber                            as Payeeaccountnumber,
      @Semantics.amount.currencyCode: 'Currency'
      a.paymentamountdue                              as Paymentamountdue,
      a.ifsc                                          as Ifsc,
      a.credittransfer                                as Credittransfer,
      a.directdebit                                   as Directdebit,
      a.creditdays                                    as Creditdays,
      a.creditavaileddate                             as Creditavaileddate,
      a.creditreversaldate                            as Creditreversaldate,
      a.refdocumentremarks                            as Refdocumentremarks,
      a.refdocumentperiodstartdate                    as Refdocumentperiodstartdate,
      a.refdocumentperiodenddate                      as Refdocumentperiodenddate,
      a.invno                                         as Invno,
      a.invdt                                         as Invdt,
      a.refcontractdetails                            as Refcontractdetails,
      a.additionalsupportingdocumentde                as Additionalsupportingdocumentde,
      a.portcode                                      as Portcode,
      a.documentcurrencycode                          as Documentcurrencycode,
      a.destinationcountry                            as Destinationcountry,
      a.pos                                           as Pos,
      @Semantics.amount.currencyCode: 'Currency'
      a.documentvalue                                 as Documentvalue,
      a.documentdiscount                              as Documentdiscount,
      a.documentothercharges                          as Documentothercharges,
      @Semantics.amount.currencyCode: 'Currency'
      a.documentvalueinforeigncurrency                as Documentvalueinforeigncurrency,
      @Semantics.amount.currencyCode: 'Currency'
      a.roundoffamount                                as Roundoffamount,
      a.differentialpercentage                        as Differentialpercentage,
      a.reversecharge                                 as Reversecharge,
      a.claimrefund                                   as Claimrefund,
      a.underigstact                                  as Underigstact,
      a.refundeligibility                             as Refundeligibility,
      a.pnroruniquenumber                             as Pnroruniquenumber,
      a.availprovisionalitc                           as Availprovisionalitc,
      a.originalstatecode                             as Originalstatecode,
      a.originaldocumentnumber                        as Originaldocumentnumber,
      a.originaldocumentdate                          as Originaldocumentdate,
      a.originalreturnperiod                          as Originalreturnperiod,
      @Semantics.amount.currencyCode: 'Currency'
      a.originaltaxablevalue                          as Originaltaxablevalue,
      a.toemailaddresses                              as Toemailaddresses,
      a.tomobilenumbers                               as Tomobilenumbers,
      a.jworiginaldocumentnumber                      as Jworiginaldocumentnumber,
      a.jworiginaldocumentdate                        as Jworiginaldocumentdate,
      a.jwdocumentnumber                              as Jwdocumentnumber,
      a.jwdocumentdate                                as Jwdocumentdate,
      a.autodraftsource                               as Autodraftsource,
      a.irngenerationdate                             as Irngenerationdate,
      a.isamendment                                   as Isamendment,
      a.isavailableingstr2b                           as Isavailableingstr2b,
      a.itcavailability                               as Itcavailability,
      a.itcunavailabilityreason                       as Itcunavailabilityreason,
      a.custom2                                       as Custom2,
      a.custom3                                       as Custom3,
      a.custom5                                       as Custom5,
      a.custom6                                       as Custom6,
      a.custom7                                       as Custom7,
      a.custom8                                       as Custom8,
      a.custom10                                      as Custom10,
      a.custom20                                      as Custom20,
      a.natureofjwdone                                as Natureofjwdone,
      a.lossunitofmeasure                             as Lossunitofmeasure,
      @Semantics.quantity.unitOfMeasure: 'uom'
      a.losstotalquantity                             as Losstotalquantity,
      a.pusherrors                                    as Pusherrors,
      a.pushstatus                                    as Pushstatus,
      a.gstaction                                     as Gstaction,
      //      a.reconciliationsection                    as Reconciliationsection,
      a.reconciliationsection                         as Preconciliationsection,
      case when a.filingdate is null then 'MISMATCHED'
           when a.taxdiff < 0
           and a.reconciliationsection = 'MISMATCHED'
           then 'MATCHED'
           else a.reconciliationsection end           as Reconciliationsection,
      //
      //       case when Preconciliationsection = 'CANCELLED' then 'CANCEL'
      //           else a.reconciliationsection1 end as Reconciliationsection2,

      a.pushdate                                      as Pushdate,
      a.cancelleddate                                 as Cancelleddate,
      a.ispregstregime                                as Ispregstregime,
      a.amendedtype                                   as Amendedtype,
      a.isgstr3bfiled                                 as Isgstr3bfiled,
      a.isgstr3bfildate                               as Isgstr3bfildate,
      a.filingdate                                    as Filingdate,
      a.filingreturnperiod                            as Filingreturnperiod,
      a.filingstatus                                  as Filingstatus,
      a.isservice                                     as Isservice,
      a.hsn                                           as Hsn,
      a.productcode                                   as Productcode,
      a.name                                          as Name,
      a.description                                   as Description,
      a.barcode                                       as Barcode,
      a.uqc                                           as Uqc,
      @Semantics.quantity.unitOfMeasure: 'uom'
      a.quantity                                      as Quantity,
      a.freequantity                                  as Freequantity,
      @Semantics.amount.currencyCode: 'Currency'
      a.rate                                          as Rate,
      @Semantics.amount.currencyCode: 'Currency'
      a.cessrate                                      as Cessrate,
      a.statecessrate                                 as Statecessrate,
      @Semantics.amount.currencyCode: 'Currency'
      a.cessnonadvaloremrate                          as Cessnonadvaloremrate,
      a.priceperquantity                              as Priceperquantity,
      @Semantics.amount.currencyCode: 'Currency'
      a.discountamount                                as Discountamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.grossamount                                   as Grossamount,
      a.othercharges                                  as Othercharges,
      @Semantics.amount.currencyCode: 'Currency'
      a.taxablevalue                                  as Taxablevalue,
      @Semantics.amount.currencyCode: 'Currency'
      a.igstamount                                    as Igstamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.cgstamount                                    as Cgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.sgstamount                                    as Sgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.cessamount                                    as Cessamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.statecessamount                               as Statecessamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.cessnonadvaloremamount                        as Cessnonadvaloremamount,
      a.statecessnonadvaloremamount                   as Statecessnonadvaloremamount,
      a.taxtype                                       as Taxtype,
      a.itceligibility                                as Itceligibility,
      @Semantics.amount.currencyCode: 'Currency'
      a.itcigstamount                                 as Itcigstamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.itccgstamount                                 as Itccgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.itcsgstamount                                 as Itcsgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      a.itccessamount                                 as Itccessamount,
      a.customitem1                                   as Customitem1,
      a.customitem2                                   as Customitem2,
      a.customitem3                                   as Customitem3,
      a.customitem4                                   as Customitem4,
      a.customitem5                                   as Customitem5,
      a.customitem6                                   as Customitem6,
      a.customitem7                                   as Customitem7,
      a.customitem8                                   as Customitem8,
      a.customitem9                                   as Customitem9,
      a.customitem10                                  as Customitem10,
      a.cpgstin                                       as Cpgstin,
      a.cptradename                                   as Cptradename,
      a.cplegalname                                   as Cplegalname,
      a.cporiginaldocumentnumber                      as Cporiginaldocumentnumber,
      a.cporiginaldocumentdate                        as Cporiginaldocumentdate,
      a.cpportcode                                    as Cpportcode,
      a.cpdocumentnumber                              as Cpdocumentnumber,
      a.cpdocumentdate                                as Cpdocumentdate,
      a.cpvalue                                       as Cpvalue,
      a.cppos                                         as Cppos,
      a.cpreversecharge                               as Cpreversecharge,
      a.cpdifferentialpercentage                      as Cpdifferentialpercentage,
      a.cptransactiontype                             as Cptransactiontype,
      a.cptaxpayertype                                as Cptaxpayertype,
      a.cptaxablevalue                                as Cptaxablevalue,
      a.cprate                                        as Cprate,
      a.cpigstamount                                  as Cpigstamount,
      a.cpcgstamount                                  as Cpcgstamount,
      a.cpsgstamount                                  as Cpsgstamount,
      a.cpcessamount                                  as Cpcessamount,
      a.cpfilingstatus                                as Cpfilingstatus,
      a.cpsuppliercancellationdate                    as Cpsuppliercancellationdate,
      a.cpfilingdate                                  as Cpfilingdate,
      a.cpfilingreturnperiod                          as Cpfilingreturnperiod,
      a.cpautodraftsource                             as Cpautodraftsource,
      a.cpirn                                         as Cpirn,
      a.cpirngenerationdate                           as Cpirngenerationdate,
      a.cprefprecedingdocumentdetails                 as Cprefprecedingdocumentdetails,
      a.cpavailableingstr2b                           as Cpavailableingstr2b,
      a.cpgstr2breturnperiod                          as Cpgstr2breturnperiod,
      a.cpavailableingstr98a                          as Cpavailableingstr98a,
      a.cpitcavailability                             as Cpitcavailability,
      a.cpitcunavailabilityreason                     as Cpitcunavailabilityreason,
      a.cpitcclaimmonth                               as Cpitcclaimmonth,
      a.cpreturnperiod                                as Cpreturnperiod,
      a.cpisamendment                                 as Cpisamendment,
      a.cporiginalreturnperiod                        as Cporiginalreturnperiod,
      a.cpamendedtype                                 as Cpamendedtype,
      @Semantics.amount.currencyCode: 'Currency'
      a.valuediff                                     as Valuediff,
      @Semantics.amount.currencyCode: 'Currency'
      a.taxablediff                                   as Taxablediff,
      @Semantics.amount.currencyCode: 'Currency'
      a.taxdiff                                       as Taxdiff,
      a.reason                                        as Reason,
      a.rec_date                                      as RecDate,
      a.rec_time                                      as RecTime,
      b.currency                                      as Currency,
      @Semantics.amount.currencyCode: 'Currency'
      b.fi_doc_value                                  as FiDocValue,
      @Semantics.amount.currencyCode: 'Currency'
      b.fi_tax_value                                  as FiTaxValue,
      b.fi_tax_code                                   as FiTaxCode,
      @Semantics.amount.currencyCode: 'Currency'
      b.fi_tax_rate                                   as FiTaxRate,
      b.status                                        as Status,
      b.gst_part                                      as GstPart,
      b.vendor                                        as Vendor,
      b.mindk                                         as Mindk,
      b.vendor_block_type                             as VendorBlockType,
      b.payment_due_date                              as PaymentDueDate,
      b.clearing_no                                   as ClearingNo,
      b.clearing_date                                 as ClearingDate,
      b.expected_filing_date                          as ExpectedFilingDate,
      b.due_date_before_filing                        as DueDateBeforeFiling,
      b.cleared_before_filing                         as ClearedBeforeFiling,
      b.grc_check_applicable                          as GrcCheckApplicable,
      b.grc_checked_on                                as GrcCheckedOn,
      b.grc_checked_at                                as GrcCheckedAt,
      b.grc_checked_by                                as GrcCheckedBy,
      b.grc_score                                     as GrcScore,
      b.gstr3b_score                                  as Gstr3bScore,
      b.grc_check_result                              as GrcCheckResult,
      b.grc_check_comment                             as GrcCheckComment,
      b.blocked_on                                    as BlockedOn,
      b.blocked_at                                    as BlockedAt,
      b.blocked_by                                    as BlockedBy,
      b.block_comment                                 as BlockComment,
      b.split_comp_code                               as SplitCompCode,
      b.split_gst_doc_num                             as SplitGstDocNum,
      b.split_year                                    as SplitYear,
      b.split_on                                      as SplitOn,
      b.split_at                                      as SplitAt,
      b.split_by                                      as SplitBy,
      b.split_comment                                 as SplitComment,
      b.split_rev_comp_code                           as SplitRevCompCode,
      b.split_rev_doc_num                             as SplitRevDocNum,
      b.split_rev_year                                as SplitRevYear,
      b.split_rev_on                                  as SplitRevOn,
      b.split_rev_at                                  as SplitRevAt,
      b.split_rev_by                                  as SplitRevBy,
      b.split_rev_comment                             as SplitRevComment,
      b.jv_comp_code                                  as JvCompCode,
      b.jv_doc_num                                    as JvDocNum,
      b.jv_year                                       as JvYear,
      b.jv_posted_on                                  as JvPostedOn,
      b.jv_posted_at                                  as JvPostedAt,
      b.jv_posted_by                                  as JvPostedBy,
      b.jv_comment                                    as JvComment,
      b.jv_rev_comp_code                              as JvRevCompCode,
      b.jv_rev_doc_num                                as JvRevDocNum,
      b.jv_rev_year                                   as JvRevYear,
      b.jv_reversed_on                                as JvReversedOn,
      b.jv_reversed_at                                as JvReversedAt,
      b.jv_reversed_by                                as JvReversedBy,
      b.jv_rev_comment                                as JvRevComment,
      b.released_on                                   as ReleasedOn,
      b.released_at                                   as ReleasedAt,
      b.released_by                                   as ReleasedBy,
      b.release_comment                               as ReleaseComment,
      b.debit_note_comp                               as DebitNoteComp,
      b.debit_note_num                                as DebitNoteNum,
      b.debit_note_year                               as DebitNoteYear,
      b.debit_note_posted_on                          as DebitNotePostedOn,
      b.debit_note_posted_at                          as DebitNotePostedAt,
      b.debit_note_posted_by                          as DebitNotePostedBy,
      b.debit_note_comment                            as DebitNoteComment,
      b.debit_note_rev_comp                           as DebitNoteRevComp,
      b.debit_note_rev_num                            as DebitNoteRevNum,
      b.debit_note_rev_year                           as DebitNoteRevYear,
      b.debit_note_reversed_on                        as DebitNoteReversedOn,
      b.debit_note_reversed_at                        as DebitNoteReversedAt,
      b.debit_note_reversed_by                        as DebitNoteReversedBy,
      b.debit_note_rev_comment                        as DebitNoteRevComment,
      b.message                                       as Message,
      b.last_checked_on                               as LastCheckedOn,
      b.last_checked_at                               as LastCheckedAt,
      b.last_checked_by                               as LastCheckedBy,
      b.processing_options                            as ProcessingOptions,
      b.wronggst                                      as Wronggst,
      b.gstin                                         as Gstin,
      b.gstin_part                                    as GstinPart,
      b.remark                                        as Remark,

      cast( '' as meins )                             as uom,
      @Semantics.amount.currencyCode: 'Currency'
      a.igstamount + a.cgstamount + a.sgstamount      as TOTALTAX,
      cast( '' as abap.char( 4 ))                     as Procstat,
      cast( '' as abap.char( 1 ) )                    as Cbox,
      cast( '' as abap.char( 4 )  )                   as Recostat,
      cast( '' as abap.char( 50 ) )                   as Statusdesc,
      cast( '' as abap.char( 50 ) )                   as BlockTypeDesc,
      cast( '' as abap.char( 35 ) )                   as Vendorname,
      cast( '' as blart )                             as Blart,
      cast( '' as abap.char( 1 ) )                    as Qblock,
      cast( '' as abap.char( 20 ))                    as Msme,

      p.panno                                         as Panno,

      // MT_ACC_DATA ------------------------------------------------

      substring( a.custom9, 1, 4 )                    as MtAccDoc1_BUKRS,
      lpad( substring( a.custom1, 1, 10 ), 10, '0' )  as MtAccDoc1_BELNR,
      substring( a.custom4, 1, 4 )                    as MtAccDoc1_GJAHR,

      case when b.split_gst_doc_num is not null and b.split_gst_doc_num <> '' then b.split_comp_code
           else '' end                                as MtAccDoc2_BUKRS,
      case when b.split_gst_doc_num is not null and b.split_gst_doc_num <> '' then b.split_gst_doc_num
            else '' end                               as MtAccDoc2_BELNR,
      case when b.split_gst_doc_num is not null and b.split_gst_doc_num <> '' then b.split_year
            else cast( '0000' as abap.numc( 4 ) ) end as MtAccDoc2_GJAHR,

      case when b.jv_doc_num is not null and b.jv_doc_num <> '' then b.jv_comp_code
           else '' end                                as MtAccDoc3_BUKRS,
      case when b.jv_doc_num is not null and b.jv_doc_num <> '' then b.jv_doc_num
           else '' end                                as MtAccDoc3_BELNR,
      case when b.jv_doc_num is not null and b.jv_doc_num <> '' then b.jv_year
           else cast( '0000' as abap.numc( 4 ) ) end  as MtAccDoc3_GJAHR,

      case when b.jv_rev_doc_num is not null and b.jv_rev_doc_num <> '' then b.jv_rev_comp_code
           else '' end                                as MtAccDoc4_BUKRS,
      case when b.jv_rev_doc_num is not null and b.jv_rev_doc_num <> '' then b.jv_rev_doc_num
           else '' end                                as MtAccDoc4_BELNR,
      case when b.jv_rev_doc_num is not null and b.jv_rev_doc_num <> '' then b.jv_rev_year
           else cast( '0000' as abap.numc( 4 ) ) end  as MtAccDoc4_GJAHR,

      case when b.debit_note_num is not null and b.debit_note_num <> '' then b.debit_note_comp
           else '' end                                as MtAccDoc5_BUKRS,
      case when b.debit_note_num is not null and b.debit_note_num <> '' then b.debit_note_num
           else '' end                                as MtAccDoc5_BELNR,
      case when b.debit_note_num is not null and b.debit_note_num <> '' then b.debit_note_year
           else cast( '0000' as abap.numc( 4 ) ) end  as MtAccDoc5_GJAHR,

      case when b.debit_note_rev_num is not null and b.debit_note_rev_num <> '' then b.debit_note_rev_comp
           else '' end                                as MtAccDoc6_BUKRS,
      case when b.debit_note_rev_num is not null and b.debit_note_rev_num <> '' then b.debit_note_rev_num
          else '' end                                 as MtAccDoc6_BELNR,
      case when b.debit_note_rev_num is not null and b.debit_note_rev_num <> '' then b.debit_note_rev_year
          else cast( '0000' as abap.numc( 4 ) ) end   as MtAccDoc6_GJAHR


      // MT_ACC_DATA ------------------------------------------------

}
where
  c.reco_status is null







@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Interface for Pay block layer 1'
//@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
```

---

## ZPB_I_2A_PAY_BLOCK_LY1

**Label:** Interface for Pay block layer 1

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Interface for Pay block layer 1'
//@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
define root view entity ZPB_I_2A_PAY_BLOCK_LY1
  as select from    ZPB_I_2A_PAY_BLOCK as a
    left outer join zcy_t_2a_recsta1   as r on  a.Preconciliationsection = r.reco_status
                                            and a.Reason                 = r.reason

    left outer join dd07t              as t on  t.domname    = 'ZCY_DOM_2A_PAY_BLOCK_STATUS'
                                            and t.domvalue_l = a.VendorBlockType
                                            and t.ddlanguage = $session.system_language

  association [0..1] to ZPB_R_MT_ACC_DATA_LY1   as _MT_ACC_DATA          on  $projection.Custom9 = _MT_ACC_DATA.Bukrs
                                                                         and $projection.Custom1 = _MT_ACC_DATA.Belnr
                                                                         and $projection.Custom4 = _MT_ACC_DATA.Gjahr

  association [1]    to ZPB_R_MT_VENDOR_DATA    as _MT_VENDOR_DATA       on  $projection.Custom9 = _MT_VENDOR_DATA.Bukrs
                                                                         and $projection.Custom1 = _MT_VENDOR_DATA.Belnr
                                                                         and $projection.Custom4 = _MT_VENDOR_DATA.Gjahr

  association [1]    to ZPB_R_MT_VENDOR_DATA_M  as _MT_VENDOR_DATA_M     on  $projection.Custom9 = _MT_VENDOR_DATA_M.Bukrs
                                                                         and $projection.Custom1 = _MT_VENDOR_DATA_M.Belnr
                                                                         and $projection.Custom4 = _MT_VENDOR_DATA_M.Gjahr

  association [1]    to zpb_r_vendor_block_type as _MT_VENDOR_BLOCK_TYPE on  $projection.Custom9 = _MT_VENDOR_BLOCK_TYPE.Bukrs
                                                                         and $projection.Custom1 = _MT_VENDOR_BLOCK_TYPE.Belnr
                                                                         and $projection.Custom4 = _MT_VENDOR_BLOCK_TYPE.Gjahr

  association [1]    to ZPB_R_MT_ACC_DOC_F      as _MT_ACC_DOC           on  $projection.Custom9 = _MT_ACC_DOC.BUKRS
                                                                         and $projection.Custom1 = _MT_ACC_DOC.BELNR
                                                                         and $projection.Custom4 = _MT_ACC_DOC.GJAHR

  //     association [0..*]    to ZPB_R_MT_TAX_DATA      as _MT_TAX_DATA           on  $projection.Custom9 = _MT_TAX_DATA.Bukrs
  //                                                                         and $projection.Custom1 = _MT_TAX_DATA.Belnr
  //                                                                         and $projection.Custom4 = _MT_TAX_DATA.Gjahr

  association [1]    to ZPB_R_lt_bsik           as _lt_bsik              on  $projection.Custom9 = _lt_bsik.bukrs
                                                                         and $projection.Custom1 = _lt_bsik.belnr
                                                                         and $projection.Custom4 = _lt_bsik.gjahr

  association [0..1] to ZPB_R_MT_TAX_TOTAL      as _MT_TAX_TOTAL         on  $projection.Custom9 = _MT_TAX_TOTAL.Bukrs
                                                                         and $projection.Custom1 = _MT_TAX_TOTAL.Belnr
                                                                         and $projection.Custom4 = _MT_TAX_TOTAL.Gjahr

{
      //  key a.Custom1,
  key a.MtAccDoc1_BUKRS                         as Custom9,
  key a.MtAccDoc1_BELNR                         as Custom1,
  key a.MtAccDoc1_GJAHR                         as Custom4,
      a.MtAccDoc2_BUKRS,
      a.MtAccDoc2_BELNR,
      a.MtAccDoc2_GJAHR,
      a.MtAccDoc3_BUKRS,
      a.MtAccDoc3_BELNR,
      a.MtAccDoc3_GJAHR,
      a.MtAccDoc4_BUKRS,
      a.MtAccDoc4_BELNR,
      a.MtAccDoc4_GJAHR,
      a.MtAccDoc5_BUKRS,
      a.MtAccDoc5_BELNR,
      a.MtAccDoc5_GJAHR,
      a.MtAccDoc6_BUKRS,
      a.MtAccDoc6_BELNR,
      a.MtAccDoc6_GJAHR,
      a.Returnperiod,
      a.Locationgstin,
      a.Documentnumber,
      a.Serialnumber,
      a.Documentdate,
      a.Billfromgstin,
      a.Financialyear,
      a.Mapperid,
      a.Supplytype,
      a.Purpose,
      a.Locationname,
      a.Irn,
      a.Liabilitydischargereturnperiod,
      a.Itcclaimreturnperiod,
      a.Documenttype,
      a.Transactiontype,
      a.Taxpayertype,
      a.Transactionnature,
      a.Documentseriescode,
      a.Billfromlegalname,
      a.Billfromtradename,
      a.Billfromvendorcode,
      a.Billfromaddress1,
      a.Billfromaddress2,
      a.Billfromcity,
      a.Billfromstatecode,
      a.Billfrompincode,
      a.Billfromphone,
      a.Billfromemail,
      a.Dispatchfromgstin,
      a.Dispatchfromtradename,
      a.Dispatchfromvendorcode,
      a.Dispatchfromaddress1,
      a.Dispatchfromaddress2,
      a.Dispatchfromcity,
      a.Dispatchfromstatecode,
      a.Dispatchfrompincode,
      a.Billtogstin,
      a.Billtolegalname,
      a.Billtotradename,
      a.Billtovendorcode,
      a.Billtoaddress1,
      a.Billtoaddress2,
      a.Billtocity,
      a.Billtostatecode,
      a.Billtopincode,
      a.Billtophone,
      a.Billtoemail,
      a.Shiptogstin,
      a.Shiptolegalname,
      a.Shiptotradename,
      a.Shiptovendorcode,
      a.Shiptoaddress1,
      a.Shiptoaddress2,
      a.Shiptocity,
      a.Shiptostatecode,
      a.Shiptopincode,
      a.Paymenttype,
      a.Paymentmode,
      a.Paymentamount,
      a.Advancepaidamount,
      a.Paymentdate,
      a.Paymentremarks,
      a.Paymentterms,
      a.Paymentinstruction,
      a.Payeename,
      a.Payeeaccountnumber,
      a.Paymentamountdue,
      a.Ifsc,
      a.Credittransfer,
      a.Directdebit,
      a.Creditdays,
      a.Creditavaileddate,
      a.Creditreversaldate,
      a.Refdocumentremarks,
      a.Refdocumentperiodstartdate,
      a.Refdocumentperiodenddate,
      a.Invno,
      a.Invdt,
      a.Refcontractdetails,
      a.Additionalsupportingdocumentde,
      a.Portcode,
      a.Documentcurrencycode,
      a.Destinationcountry,
      a.Pos,
      a.Documentvalue,
      a.Documentdiscount,
      a.Documentothercharges,
      a.Documentvalueinforeigncurrency,
      a.Roundoffamount,
      a.Differentialpercentage,
      a.Reversecharge,
      a.Claimrefund,
      a.Underigstact,
      a.Refundeligibility,
      a.Pnroruniquenumber,
      a.Availprovisionalitc,
      a.Originalstatecode,
      a.Originaldocumentnumber,
      a.Originaldocumentdate,
      a.Originalreturnperiod,
      a.Originaltaxablevalue,
      a.Toemailaddresses,
      a.Tomobilenumbers,
      a.Jworiginaldocumentnumber,
      a.Jworiginaldocumentdate,
      a.Jwdocumentnumber,
      a.Jwdocumentdate,
      a.Autodraftsource,
      a.Irngenerationdate,
      a.Isamendment,
      a.Isavailableingstr2b,
      a.Itcavailability,
      a.Itcunavailabilityreason,
      a.Custom2,
      a.Custom3,
      a.Custom5,
      a.Custom6,
      a.Custom7,
      a.Custom8,
      a.Custom10,
      a.Custom20,
      a.Natureofjwdone,
      a.Lossunitofmeasure,
      a.Losstotalquantity,
      a.Pusherrors,
      a.Pushstatus,
      a.Gstaction,
      a.Preconciliationsection,
      //  a.Reconciliationsection,
      case
           when r.category = 'M' then 'MATCHED'
           when r.category = 'B' then 'MISMATCHED'
           when a.Panno is not null then 'NOTRELEVANT'
           when a.Preconciliationsection = 'CANCELLED' then 'CANCEL'
               else a.Reconciliationsection end as Reconciliationsection,
      a.Pushdate,
      a.Cancelleddate,
      a.Ispregstregime,
      a.Amendedtype,
      a.Isgstr3bfiled,
      a.Isgstr3bfildate,
      a.Filingdate,
      a.Filingreturnperiod,
      a.Filingstatus,
      a.Isservice,
      a.Hsn,
      a.Productcode,
      a.Name,
      a.Description,
      a.Barcode,
      a.Uqc,
      a.Quantity,
      a.Freequantity,
      a.Rate,
      a.Cessrate,
      a.Statecessrate,
      a.Cessnonadvaloremrate,
      a.Priceperquantity,
      a.Discountamount,
      a.Grossamount,
      a.Othercharges,
      a.Taxablevalue,
      a.Igstamount,
      a.Cgstamount,
      a.Sgstamount,
      a.Cessamount,
      a.Statecessamount,
      a.Cessnonadvaloremamount,
      a.Statecessnonadvaloremamount,
      a.Taxtype,
      a.Itceligibility,
      a.Itcigstamount,
      a.Itccgstamount,
      a.Itcsgstamount,
      a.Itccessamount,
      a.Customitem1,
      a.Customitem2,
      a.Customitem3,
      a.Customitem4,
      a.Customitem5,
      a.Customitem6,
      a.Customitem7,
      a.Customitem8,
      a.Customitem9,
      a.Customitem10,
      a.Cpgstin,
      a.Cptradename,
      a.Cplegalname,
      a.Cporiginaldocumentnumber,
      a.Cporiginaldocumentdate,
      a.Cpportcode,
      a.Cpdocumentnumber,
      a.Cpdocumentdate,
      a.Cpvalue,
      a.Cppos,
      a.Cpreversecharge,
      a.Cpdifferentialpercentage,
      a.Cptransactiontype,
      a.Cptaxpayertype,
      a.Cptaxablevalue,
      a.Cprate,
      a.Cpigstamount,
      a.Cpcgstamount,
      a.Cpsgstamount,
      a.Cpcessamount,
      a.Cpfilingstatus,
      a.Cpsuppliercancellationdate,
      a.Cpfilingdate,
      a.Cpfilingreturnperiod,
      a.Cpautodraftsource,
      a.Cpirn,
      a.Cpirngenerationdate,
      a.Cprefprecedingdocumentdetails,
      a.Cpavailableingstr2b,
      a.Cpgstr2breturnperiod,
      a.Cpavailableingstr98a,
      a.Cpitcavailability,
      a.Cpitcunavailabilityreason,
      a.Cpitcclaimmonth,
      a.Cpreturnperiod,
      a.Cpisamendment,
      a.Cporiginalreturnperiod,
      a.Cpamendedtype,
      a.Valuediff,
      a.Taxablediff,
      a.Taxdiff,
      a.Reason,
      a.RecDate,
      a.RecTime,
      a.FiDocValue,
      //      @Semantics.amount.currencyCode: 'Currency'
      //      case when a.FiDocValue = 0 then _MT_ACC_DATA.dmbtr else a.FiDocValue end as FiDocValue,
      a.Currency,
      //      a.FiTaxValue,
      @Semantics.amount.currencyCode: 'Currency'
      case
         when a.TOTALTAX is null or a.TOTALTAX = 0
         then case
         when _MT_TAX_TOTAL.FiTaxTotal is null
         then cast( 0 as abap.curr( 15, 2 ) )
         else _MT_TAX_TOTAL.FiTaxTotal
         end
         else a.TOTALTAX

         end                                    as FiTaxValue,

      a.FiTaxCode,
      a.FiTaxRate,
      a.Status,
      a.GstPart,
      a.Vendor,
      a.Mindk,
      a.VendorBlockType,
      a.PaymentDueDate,
      a.ClearingNo,
      a.ClearingDate,
      a.ExpectedFilingDate,
      a.DueDateBeforeFiling,
      a.ClearedBeforeFiling,
      a.GrcCheckApplicable,
      a.GrcCheckedOn,
      a.GrcCheckedAt,
      a.GrcCheckedBy,
      a.GrcScore,
      a.Gstr3bScore,
      a.GrcCheckResult,
      a.GrcCheckComment,
      a.BlockedOn,
      a.BlockedAt,
      a.BlockedBy,
      a.BlockComment,
      a.SplitCompCode,
      a.SplitGstDocNum,
      a.SplitYear,
      a.SplitOn,
      a.SplitAt,
      a.SplitBy,
      a.SplitComment,
      a.SplitRevCompCode,
      a.SplitRevDocNum,
      a.SplitRevYear,
      a.SplitRevOn,
      a.SplitRevAt,
      a.SplitRevBy,
      a.SplitRevComment,
      a.JvCompCode,
      a.JvDocNum,
      a.JvYear,
      a.JvPostedOn,
      a.JvPostedAt,
      a.JvPostedBy,
      a.JvComment,
      a.JvRevCompCode,
      a.JvRevDocNum,
      a.JvRevYear,
      a.JvReversedOn,
      a.JvReversedAt,
      a.JvReversedBy,
      a.JvRevComment,
      a.ReleasedOn,
      a.ReleasedAt,
      a.ReleasedBy,
      a.ReleaseComment,
      a.DebitNoteComp,
      a.DebitNoteNum,
      a.DebitNoteYear,
      a.DebitNotePostedOn,
      a.DebitNotePostedAt,
      a.DebitNotePostedBy,
      a.DebitNoteComment,
      a.DebitNoteRevComp,
      a.DebitNoteRevNum,
      a.DebitNoteRevYear,
      a.DebitNoteReversedOn,
      a.DebitNoteReversedAt,
      a.DebitNoteReversedBy,
      a.DebitNoteRevComment,
      a.Message,
      a.LastCheckedOn,
      a.LastCheckedAt,
      a.LastCheckedBy,
      a.ProcessingOptions,
      a.Wronggst,
      a.Gstin,
      a.GstinPart,
      a.Remark,
      a.uom,
      a.TOTALTAX,
      a.Procstat,
      a.Cbox,
      a.Recostat,
      a.Statusdesc,
      a.BlockTypeDesc,
      a.Vendorname,
      a.Blart,
      a.Qblock,
      a.Msme,
      a.Panno,

      'X'                                       as p_pbf,
      ' '                                       as p_jv,
      'X'                                       as p_dbf,
      'X'                                       as p_daf,

      t.ddtext,
      _MT_TAX_TOTAL.FiTaxTotal                  as FITAXTOTAL,

      _MT_ACC_DATA,
      _MT_VENDOR_DATA,
      _MT_VENDOR_DATA_M,
      _MT_VENDOR_BLOCK_TYPE,
      _MT_ACC_DOC,
      _lt_bsik,
      _MT_TAX_TOTAL
      //      ,
      //      _MT_TAX_DATA

}



@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Pay Block LY2'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_I_2A_PAY_BLOCK_LY2

**Label:** Pay Block LY2

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Pay Block LY2'
@Metadata.ignorePropagatedAnnotations: true
define root view entity ZPB_I_2A_PAY_BLOCK_LY2
  as select distinct from ZPB_I_2A_PAY_BLOCK_LY1
{
  key Custom9,
  key Custom1,
  key Custom4,
      MtAccDoc2_BUKRS,
      MtAccDoc2_BELNR,
      MtAccDoc2_GJAHR,
      MtAccDoc3_BUKRS,
      MtAccDoc3_BELNR,
      MtAccDoc3_GJAHR,
      MtAccDoc4_BUKRS,
      MtAccDoc4_BELNR,
      MtAccDoc4_GJAHR,
      MtAccDoc5_BUKRS,
      MtAccDoc5_BELNR,
      MtAccDoc5_GJAHR,
      MtAccDoc6_BUKRS,
      MtAccDoc6_BELNR,
      MtAccDoc6_GJAHR,
      Returnperiod,
      Locationgstin,
      Documentnumber,
      Serialnumber,
      Documentdate,
      Billfromgstin,
      Financialyear,
      Mapperid,
      Supplytype,
      Purpose,
      Locationname,
      Irn,
      Liabilitydischargereturnperiod,
      Itcclaimreturnperiod,
      Documenttype,
      Transactiontype,
      Taxpayertype,
      Transactionnature,
      Documentseriescode,
      Billfromlegalname,
      Billfromtradename,
      Billfromvendorcode,
      Billfromaddress1,
      Billfromaddress2,
      Billfromcity,
      Billfromstatecode,
      Billfrompincode,
      Billfromphone,
      Billfromemail,
      Dispatchfromgstin,
      Dispatchfromtradename,
      Dispatchfromvendorcode,
      Dispatchfromaddress1,
      Dispatchfromaddress2,
      Dispatchfromcity,
      Dispatchfromstatecode,
      Dispatchfrompincode,
      Billtogstin,
      Billtolegalname,
      Billtotradename,
      Billtovendorcode,
      Billtoaddress1,
      Billtoaddress2,
      Billtocity,
      Billtostatecode,
      Billtopincode,
      Billtophone,
      Billtoemail,
      Shiptogstin,
      Shiptolegalname,
      Shiptotradename,
      Shiptovendorcode,
      Shiptoaddress1,
      Shiptoaddress2,
      Shiptocity,
      Shiptostatecode,
      Shiptopincode,
      Paymenttype,
      Paymentmode,
      @Semantics.amount.currencyCode: 'Currency'
      Paymentamount,
      @Semantics.amount.currencyCode: 'Currency'
      Advancepaidamount,
      Paymentdate,
      Paymentremarks,
      Paymentterms,
      Paymentinstruction,
      Payeename,
      Payeeaccountnumber,
      @Semantics.amount.currencyCode: 'Currency'
      Paymentamountdue,
      Ifsc,
      Credittransfer,
      Directdebit,
      Creditdays,
      Creditavaileddate,
      Creditreversaldate,
      Refdocumentremarks,
      Refdocumentperiodstartdate,
      Refdocumentperiodenddate,
      Invno,
      Invdt,
      Refcontractdetails,
      Additionalsupportingdocumentde,
      Portcode,
      Documentcurrencycode,
      Destinationcountry,
      Pos,
      @Semantics.amount.currencyCode: 'Currency'
      Documentvalue,
      Documentdiscount,
      Documentothercharges,
      @Semantics.amount.currencyCode: 'Currency'
      Documentvalueinforeigncurrency,
      @Semantics.amount.currencyCode: 'Currency'
      Roundoffamount,
      Differentialpercentage,
      Reversecharge,
      Claimrefund,
      Underigstact,
      Refundeligibility,
      Pnroruniquenumber,
      Availprovisionalitc,
      Originalstatecode,
      Originaldocumentnumber,
      Originaldocumentdate,
      Originalreturnperiod,
      @Semantics.amount.currencyCode: 'Currency'
      Originaltaxablevalue,
      Toemailaddresses,
      Tomobilenumbers,
      Jworiginaldocumentnumber,
      Jworiginaldocumentdate,
      Jwdocumentnumber,
      Jwdocumentdate,
      Autodraftsource,
      Irngenerationdate,
      Isamendment,
      Isavailableingstr2b,
      Itcavailability,
      Itcunavailabilityreason,
      Custom2,
      Custom3,
      Custom5,
      Custom6,
      Custom7,
      Custom8,
      Custom10,
      Custom20,
      Natureofjwdone,
      Lossunitofmeasure,
      @Semantics.quantity.unitOfMeasure: 'uom'
      Losstotalquantity,
      Pusherrors,
      Pushstatus,
      Gstaction,
      Preconciliationsection,
      Reconciliationsection,
      Pushdate,
      Cancelleddate,
      Ispregstregime,
      Amendedtype,
      Isgstr3bfiled,
      Isgstr3bfildate,
      Filingdate,
      cast(
        concat(
          concat(
           substring( Filingdate,7,4 ),
           substring( Filingdate,4,2)
          ),
          substring( Filingdate,1,2)
        ) as abap.dats
      )                                                                                                                             as LV_ACT_FILING_DATE,
      Filingreturnperiod,
      Filingstatus,
      Isservice,
      Hsn,
      Productcode,
      Name,
      Description,
      Barcode,
      Uqc,
      @Semantics.quantity.unitOfMeasure: 'uom'
      Quantity,
      Freequantity,
      @Semantics.amount.currencyCode: 'Currency'
      Rate,
      @Semantics.amount.currencyCode: 'Currency'
      Cessrate,
      Statecessrate,
      @Semantics.amount.currencyCode: 'Currency'
      Cessnonadvaloremrate,
      Priceperquantity,
      @Semantics.amount.currencyCode: 'Currency'
      Discountamount,
      @Semantics.amount.currencyCode: 'Currency'
      Grossamount,
      Othercharges,
      @Semantics.amount.currencyCode: 'Currency'
      Taxablevalue,
      @Semantics.amount.currencyCode: 'Currency'
      Igstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Cgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Sgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Cessamount,
      @Semantics.amount.currencyCode: 'Currency'
      Statecessamount,
      @Semantics.amount.currencyCode: 'Currency'
      Cessnonadvaloremamount,
      Statecessnonadvaloremamount,
      Taxtype,
      Itceligibility,
      @Semantics.amount.currencyCode: 'Currency'
      Itcigstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Itccgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Itcsgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Itccessamount,
      Customitem1,
      Customitem2,
      Customitem3,
      Customitem4,
      Customitem5,
      Customitem6,
      Customitem7,
      Customitem8,
      Customitem9,
      Customitem10,
      Cpgstin,
      Cptradename,
      Cplegalname,
      Cporiginaldocumentnumber,
      Cporiginaldocumentdate,
      Cpportcode,
      Cpdocumentnumber,
      Cpdocumentdate,
      Cpvalue,
      Cppos,
      Cpreversecharge,
      Cpdifferentialpercentage,
      Cptransactiontype,
      Cptaxpayertype,
      Cptaxablevalue,
      Cprate,
      Cpigstamount,
      Cpcgstamount,
      Cpsgstamount,
      Cpcessamount,
      Cpfilingstatus,
      Cpsuppliercancellationdate,
      Cpfilingdate,
      Cpfilingreturnperiod,
      Cpautodraftsource,
      Cpirn,
      Cpirngenerationdate,
      Cprefprecedingdocumentdetails,
      Cpavailableingstr2b,
      Cpgstr2breturnperiod,
      Cpavailableingstr98a,
      Cpitcavailability,
      Cpitcunavailabilityreason,
      Cpitcclaimmonth,
      Cpreturnperiod,
      Cpisamendment,
      Cporiginalreturnperiod,
      Cpamendedtype,
      @Semantics.amount.currencyCode: 'Currency'
      Valuediff,
      @Semantics.amount.currencyCode: 'Currency'
      Taxablediff,
      @Semantics.amount.currencyCode: 'Currency'
      Taxdiff,
      Reason,
      RecDate,
      RecTime,
      //                @Semantics.amount.currencyCode: 'Currency'
      //                FiDocValue,
      @Semantics.amount.currencyCode: 'Currency'
      case when FiDocValue is null then _MT_ACC_DATA.dmbtr else FiDocValue end                                                      as FiDocValue,
      Currency,
      @Semantics.amount.currencyCode: 'Currency'
      FiTaxValue,
      FiTaxCode,
      @Semantics.amount.currencyCode: 'Currency'
      FiTaxRate,
      Status,
      //      GstPart,
      //      Vendor,
      case when Vendor is null then _MT_ACC_DATA.lifnr  end                                                                         as Vendor,
      case when Vendor is null then _MT_ACC_DATA.gst_part  end                                                                      as GstPart,
      //      Mindk,
      case when _MT_VENDOR_DATA_M.MINDK is not null then _MT_VENDOR_DATA_M.MINDK else Mindk  end                                    as Mindk,
      //      VendorBlockType,
      case when _MT_VENDOR_BLOCK_TYPE.vendor_block_type is null then 'S' else _MT_VENDOR_BLOCK_TYPE.vendor_block_type  end          as VendorBlockType,
      PaymentDueDate,
      //      ClearingNo,
      case when ClearingNo is null then _MT_ACC_DATA.augbl  end                                                                     as ClearingNo,
      //      ClearingDate,
      case when ClearingDate is null then _MT_ACC_DATA.augdt  end                                                                   as ClearingDate,
      ExpectedFilingDate,
      DueDateBeforeFiling,
      ClearedBeforeFiling,

      //      GrcCheckApplicable,
      case when GrcCheckApplicable is null then
      case when _MT_VENDOR_BLOCK_TYPE.grc_check_applicable = 'N' then _MT_VENDOR_BLOCK_TYPE.grc_check_applicable  else 'Y'  end end as GrcCheckApplicable,
      GrcCheckedOn,
      GrcCheckedAt,
      GrcCheckedBy,
      GrcScore,
      Gstr3bScore,
      GrcCheckResult,
      GrcCheckComment,
      BlockedOn,
      BlockedAt,
      BlockedBy,
      BlockComment,
      SplitCompCode,
      SplitGstDocNum,
      SplitYear,
      SplitOn,
      SplitAt,
      SplitBy,
      SplitComment,
      SplitRevCompCode,
      SplitRevDocNum,
      SplitRevYear,
      SplitRevOn,
      SplitRevAt,
      SplitRevBy,
      SplitRevComment,
      JvCompCode,
      JvDocNum,
      JvYear,
      JvPostedOn,
      JvPostedAt,
      JvPostedBy,
      JvComment,
      JvRevCompCode,
      JvRevDocNum,
      JvRevYear,
      JvReversedOn,
      JvReversedAt,
      JvReversedBy,
      JvRevComment,
      ReleasedOn,
      ReleasedAt,
      ReleasedBy,
      ReleaseComment,
      DebitNoteComp,
      DebitNoteNum,
      DebitNoteYear,
      DebitNotePostedOn,
      DebitNotePostedAt,
      DebitNotePostedBy,
      DebitNoteComment,
      DebitNoteRevComp,
      DebitNoteRevNum,
      DebitNoteRevYear,
      DebitNoteReversedOn,
      DebitNoteReversedAt,
      DebitNoteReversedBy,
      DebitNoteRevComment,
      Message,
      LastCheckedOn,
      LastCheckedAt,
      LastCheckedBy,

      Wronggst,
      //      Gstin,
      //      _MT_VENDOR_DATA.STCD3 ,
      case when _MT_VENDOR_DATA.STCD3 is not null then _MT_VENDOR_DATA.STCD3 else Gstin  end                                        as Gstin,
      //      GstinPart,
      case when _MT_VENDOR_DATA.STCD3 is not null then _MT_VENDOR_DATA.STCD3 else GstinPart  end                                    as GstinPart,
      Remark,
      uom,
      @Semantics.amount.currencyCode: 'Currency'
      //      TOTALTAX,
      //      @Semantics.amount.currencyCode: 'Currency'
      case when TOTALTAX is not null then FiTaxValue  end                                                                           as TOTALTAX,
      Procstat,
      Cbox,
      Recostat,
      Statusdesc,
      BlockTypeDesc,
      //      case when BlockTypeDesc is null then 'Unprocessed' else ddtext                                                        end     as BlockTypeDesc,
      //      Vendorname,
      case when _MT_VENDOR_DATA.NAME is not null then _MT_VENDOR_DATA.NAME else Vendorname  end                                     as Vendorname,

      //            Blart,
      case when Blart is initial then _MT_ACC_DATA.blart else Blart end                                                             as Blart,
      Qblock,
      //      Msme,
      case when _MT_VENDOR_DATA.brtxt is not null then _MT_VENDOR_DATA.brtxt else Msme  end                                         as Msme,
      Panno,
      _MT_ACC_DATA.koart,
      @Semantics.amount.currencyCode: 'Currency'
      _MT_ACC_DATA.dmbtr,

      p_pbf,
      p_jv,
      p_dbf,
      p_daf,
      p_jv                                                                                                                          as p_block,
      p_jv                                                                                                                          as p_split,
      p_jv                                                                                                                          as p_deb_n,

      p_dbf                                                                                                                         as p_dbf_cl,
      p_dbf                                                                                                                         as p_dbf_op,


      p_daf                                                                                                                         as p_daf_cl,
      p_daf                                                                                                                         as p_daf_op,


      ProcessingOptions,



      /* Associations */
      _MT_ACC_DATA,
      _MT_ACC_DOC,
      _MT_VENDOR_BLOCK_TYPE,
      _MT_VENDOR_DATA,
      _MT_VENDOR_DATA_M,
      _lt_bsik
      //      ,
      //      _MT_TAX_DATA
}

no reply
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Pay Block LY3'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_I_2A_PAY_BLOCK_LY3

**Label:** Pay Block LY3

```abap
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Pay Block LY3'
@Metadata.ignorePropagatedAnnotations: true
define root view entity ZPB_I_2A_PAY_BLOCK_LY3
  as select from ZPB_I_2A_PAY_BLOCK_LY2

{
  key Custom9,
  key Custom1,
  key Custom4,
      MtAccDoc2_BUKRS,
      MtAccDoc2_BELNR,
      MtAccDoc2_GJAHR,
      MtAccDoc3_BUKRS,
      MtAccDoc3_BELNR,
      MtAccDoc3_GJAHR,
      MtAccDoc4_BUKRS,
      MtAccDoc4_BELNR,
      MtAccDoc4_GJAHR,
      MtAccDoc5_BUKRS,
      MtAccDoc5_BELNR,
      MtAccDoc5_GJAHR,
      MtAccDoc6_BUKRS,
      MtAccDoc6_BELNR,
      MtAccDoc6_GJAHR,
      Returnperiod,
      Locationgstin,
      Documentnumber,
      Serialnumber,
      Documentdate,
      Billfromgstin,
      Financialyear,
      Mapperid,
      Supplytype,
      Purpose,
      Locationname,
      Irn,
      Liabilitydischargereturnperiod,
      Itcclaimreturnperiod,
      Documenttype,
      Transactiontype,
      Taxpayertype,
      Transactionnature,
      Documentseriescode,
      Billfromlegalname,
      Billfromtradename,
      Billfromvendorcode,
      Billfromaddress1,
      Billfromaddress2,
      Billfromcity,
      Billfromstatecode,
      Billfrompincode,
      Billfromphone,
      Billfromemail,
      Dispatchfromgstin,
      Dispatchfromtradename,
      Dispatchfromvendorcode,
      Dispatchfromaddress1,
      Dispatchfromaddress2,
      Dispatchfromcity,
      Dispatchfromstatecode,
      Dispatchfrompincode,
      Billtogstin,
      Billtolegalname,
      Billtotradename,
      Billtovendorcode,
      Billtoaddress1,
      Billtoaddress2,
      Billtocity,
      Billtostatecode,
      Billtopincode,
      Billtophone,
      Billtoemail,
      Shiptogstin,
      Shiptolegalname,
      Shiptotradename,
      Shiptovendorcode,
      Shiptoaddress1,
      Shiptoaddress2,
      Shiptocity,
      Shiptostatecode,
      Shiptopincode,
      Paymenttype,
      Paymentmode,
      @Semantics.amount.currencyCode: 'Currency'
      Paymentamount,
      @Semantics.amount.currencyCode: 'Currency'
      Advancepaidamount,
      Paymentdate,
      Paymentremarks,
      Paymentterms,
      Paymentinstruction,
      Payeename,
      Payeeaccountnumber,
      @Semantics.amount.currencyCode: 'Currency'
      Paymentamountdue,
      Ifsc,
      Credittransfer,
      Directdebit,
      Creditdays,
      Creditavaileddate,
      Creditreversaldate,
      Refdocumentremarks,
      Refdocumentperiodstartdate,
      Refdocumentperiodenddate,
      Invno,
      Invdt,
      Refcontractdetails,
      Additionalsupportingdocumentde,
      Portcode,
      Documentcurrencycode,
      Destinationcountry,
      Pos,
      @Semantics.amount.currencyCode: 'Currency'
      Documentvalue,
      Documentdiscount,
      Documentothercharges,
      @Semantics.amount.currencyCode: 'Currency'
      Documentvalueinforeigncurrency,
      @Semantics.amount.currencyCode: 'Currency'
      Roundoffamount,
      Differentialpercentage,
      Reversecharge,
      Claimrefund,
      Underigstact,
      Refundeligibility,
      Pnroruniquenumber,
      Availprovisionalitc,
      Originalstatecode,
      Originaldocumentnumber,
      Originaldocumentdate,
      Originalreturnperiod,
      @Semantics.amount.currencyCode: 'Currency'
      Originaltaxablevalue,
      Toemailaddresses,
      Tomobilenumbers,
      Jworiginaldocumentnumber,
      Jworiginaldocumentdate,
      Jwdocumentnumber,
      Jwdocumentdate,
      Autodraftsource,
      Irngenerationdate,
      Isamendment,
      Isavailableingstr2b,
      Itcavailability,
      Itcunavailabilityreason,
      Custom2,
      Custom3,
      Custom5,
      Custom6,
      Custom7,
      Custom8,
      Custom10,
      Custom20,
      Natureofjwdone,
      Lossunitofmeasure,
      @Semantics.quantity.unitOfMeasure: 'uom'
      Losstotalquantity,
      Pusherrors,
      Pushstatus,
      Gstaction,
      Preconciliationsection,
      Reconciliationsection,
      Pushdate,
      Cancelleddate,
      Ispregstregime,
      Amendedtype,
      Isgstr3bfiled,
      Isgstr3bfildate,
      Filingdate,
      LV_ACT_FILING_DATE,
      Filingreturnperiod,
      Filingstatus,
      Isservice,
      Hsn,
      Productcode,
      Name,
      Description,
      Barcode,
      Uqc,
      @Semantics.quantity.unitOfMeasure: 'uom'
      Quantity,
      Freequantity,
      @Semantics.amount.currencyCode: 'Currency'
      Rate,
      @Semantics.amount.currencyCode: 'Currency'
      Cessrate,
      Statecessrate,
      @Semantics.amount.currencyCode: 'Currency'
      Cessnonadvaloremrate,
      Priceperquantity,
      @Semantics.amount.currencyCode: 'Currency'
      Discountamount,
      @Semantics.amount.currencyCode: 'Currency'
      Grossamount,
      Othercharges,
      @Semantics.amount.currencyCode: 'Currency'
      Taxablevalue,
      @Semantics.amount.currencyCode: 'Currency'
      Igstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Cgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Sgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Cessamount,
      @Semantics.amount.currencyCode: 'Currency'
      Statecessamount,
      @Semantics.amount.currencyCode: 'Currency'
      Cessnonadvaloremamount,
      Statecessnonadvaloremamount,
      Taxtype,
      Itceligibility,
      @Semantics.amount.currencyCode: 'Currency'
      Itcigstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Itccgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Itcsgstamount,
      @Semantics.amount.currencyCode: 'Currency'
      Itccessamount,
      Customitem1,
      Customitem2,
      Customitem3,
      Customitem4,
      Customitem5,
      Customitem6,
      Customitem7,
      Customitem8,
      Customitem9,
      Customitem10,
      Cpgstin,
      Cptradename,
      Cplegalname,
      Cporiginaldocumentnumber,
      Cporiginaldocumentdate,
      Cpportcode,
      Cpdocumentnumber,
      Cpdocumentdate,
      Cpvalue,
      Cppos,
      Cpreversecharge,
      Cpdifferentialpercentage,
      Cptransactiontype,
      Cptaxpayertype,
      Cptaxablevalue,
      Cprate,
      Cpigstamount,
      Cpcgstamount,
      Cpsgstamount,
      Cpcessamount,
      Cpfilingstatus,
      Cpsuppliercancellationdate,
      Cpfilingdate,
      Cpfilingreturnperiod,
      Cpautodraftsource,
      Cpirn,
      Cpirngenerationdate,
      Cprefprecedingdocumentdetails,
      Cpavailableingstr2b,
      Cpgstr2breturnperiod,
      Cpavailableingstr98a,
      Cpitcavailability,
      Cpitcunavailabilityreason,
      Cpitcclaimmonth,
      Cpreturnperiod,
      Cpisamendment,
      Cporiginalreturnperiod,
      Cpamendedtype,
      @Semantics.amount.currencyCode: 'Currency'
      Valuediff,
      @Semantics.amount.currencyCode: 'Currency'
      Taxablediff,
      @Semantics.amount.currencyCode: 'Currency'
      Taxdiff,
      Reason,
      RecDate,
      RecTime,
      @Semantics.amount.currencyCode: 'Currency'
      FiDocValue,
      Currency,
      @Semantics.amount.currencyCode: 'Currency'
      FiTaxValue,
      FiTaxCode,
      @Semantics.amount.currencyCode: 'Currency'
      FiTaxRate,
      Status,
      Vendor,
      GstPart,
      Mindk,
      VendorBlockType,
      PaymentDueDate,
      //    ClearingNo,
      coalesce( _lt_bsik.augbl, '' ) as ClearingNo,
      ClearingDate,
      ExpectedFilingDate,
      DueDateBeforeFiling,
      ClearedBeforeFiling,
      GrcCheckApplicable,
      GrcCheckedOn,
      GrcCheckedAt,
      GrcCheckedBy,
      GrcScore,
      Gstr3bScore,
      GrcCheckResult,
      GrcCheckComment,
      BlockedOn,
      BlockedAt,
      BlockedBy,
      BlockComment,
      SplitCompCode,
      SplitGstDocNum,
      SplitYear,
      SplitOn,
      SplitAt,
      SplitBy,
      SplitComment,
      SplitRevCompCode,
      SplitRevDocNum,
      SplitRevYear,
      SplitRevOn,
      SplitRevAt,
      SplitRevBy,
      SplitRevComment,
      JvCompCode,
      JvDocNum,
      JvYear,
      JvPostedOn,
      JvPostedAt,
      JvPostedBy,
      JvComment,
      JvRevCompCode,
      JvRevDocNum,
      JvRevYear,
      JvReversedOn,
      JvReversedAt,
      JvReversedBy,
      JvRevComment,
      ReleasedOn,
      ReleasedAt,
      ReleasedBy,
      ReleaseComment,
      DebitNoteComp,
      DebitNoteNum,
      DebitNoteYear,
      DebitNotePostedOn,
      DebitNotePostedAt,
      DebitNotePostedBy,
      DebitNoteComment,
      DebitNoteRevComp,
      DebitNoteRevNum,
      DebitNoteRevYear,
      DebitNoteReversedOn,
      DebitNoteReversedAt,
      DebitNoteReversedBy,
      DebitNoteRevComment,
      Message,
      LastCheckedOn,
      LastCheckedAt,
      LastCheckedBy,
      ProcessingOptions,
      Wronggst,
      Gstin,
      GstinPart,
      Remark,
      uom,
      @Semantics.amount.currencyCode: 'Currency'
      TOTALTAX,
//      Procstat,
      case Status
      when '' then 'UNPROCESSED'
      when 'B' then 'BLOCKED'
      when 'S' then 'SPLIT'
      when 'R' then 'RELEASED'
      when 'M' then 'MATCHED'
      when 'X' then 'EXEMPTED'
      when 'W' then 'WAITING'
      when 'D' then 'DEBIT_NOTE_POSTED'
      when 'I' then 'IRELEASED'
      else '' end as Procstat,
      
      case when Status = 'R' then ' '
      when Status = 'M' then ' '
      else 'X' end as Cbox,
      Recostat,
      Statusdesc,
      BlockTypeDesc,
      Vendorname,
      Blart,
      Qblock,
      Msme,
      Panno,
      koart,
      @Semantics.amount.currencyCode: 'Currency'
      dmbtr,
      p_daf,
      p_dbf,
      p_jv,
      p_pbf,
      p_block,
      p_deb_n,
      p_split,
      p_dbf_op,
      p_dbf_cl,
      
      p_daf_cl,
      p_daf_op,
      
      concat( concat( 'PBF = ''', p_pbf ), ''', ' ) as ProcessingOptionsP1,
      
      
      concat( concat( concat( concat( concat( concat( concat( concat( '[ JV = ''', p_jv ), ''', B = ''' ), p_block ), ''', S = ''' ), p_split ), ''', D = ''' ), p_deb_n ), ''' ], ' ) as ProcessingOptionsP2,
      
      concat( concat( concat( concat( concat( '[ DBF = ''', p_dbf ), ''', CL = ''' ), p_dbf_cl ), ''', OP = ''' ), concat( p_dbf_op, ''' ], ' ) ) as ProcessingOptionsP3,
      
      concat( concat( concat( concat( concat( '[ DAF = ''', p_daf ), ''', CL = ''' ), p_daf_cl ), ''', OP = ''' ), concat( p_daf_op, ''' ]' ) ) as ProcessingOptionsP4,
      
      
      
      /* Associations */
      _MT_ACC_DATA,
      _MT_ACC_DOC,
      _MT_VENDOR_BLOCK_TYPE,
      _MT_VENDOR_DATA,
      _MT_VENDOR_DATA_M
}



@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Pay Block LY4'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_I_2A_PAY_BLOCK_LY4

**Label:** Pay Block LY4

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Pay Block LY4'
@Metadata.ignorePropagatedAnnotations: true
define root view entity ZPB_I_2A_PAY_BLOCK_LY4 as select from ZPB_I_2A_PAY_BLOCK_LY3
{
    key Custom9,
    key Custom1,
    key Custom4,
    MtAccDoc2_BUKRS,
    MtAccDoc2_BELNR,
    MtAccDoc2_GJAHR,
    MtAccDoc3_BUKRS,
    MtAccDoc3_BELNR,
    MtAccDoc3_GJAHR,
    MtAccDoc4_BUKRS,
    MtAccDoc4_BELNR,
    MtAccDoc4_GJAHR,
    MtAccDoc5_BUKRS,
    MtAccDoc5_BELNR,
    MtAccDoc5_GJAHR,
    MtAccDoc6_BUKRS,
    MtAccDoc6_BELNR,
    MtAccDoc6_GJAHR,
    Returnperiod,
    Locationgstin,
    Documentnumber,
    Serialnumber,
    Documentdate,
    Billfromgstin,
    Financialyear,
    Mapperid,
    Supplytype,
    Purpose,
    Locationname,
    Irn,
    Liabilitydischargereturnperiod,
    Itcclaimreturnperiod,
    Documenttype,
    Transactiontype,
    Taxpayertype,
    Transactionnature,
    Documentseriescode,
    Billfromlegalname,
    Billfromtradename,
    Billfromvendorcode,
    Billfromaddress1,
    Billfromaddress2,
    Billfromcity,
    Billfromstatecode,
    Billfrompincode,
    Billfromphone,
    Billfromemail,
    Dispatchfromgstin,
    Dispatchfromtradename,
    Dispatchfromvendorcode,
    Dispatchfromaddress1,
    Dispatchfromaddress2,
    Dispatchfromcity,
    Dispatchfromstatecode,
    Dispatchfrompincode,
    Billtogstin,
    Billtolegalname,
    Billtotradename,
    Billtovendorcode,
    Billtoaddress1,
    Billtoaddress2,
    Billtocity,
    Billtostatecode,
    Billtopincode,
    Billtophone,
    Billtoemail,
    Shiptogstin,
    Shiptolegalname,
    Shiptotradename,
    Shiptovendorcode,
    Shiptoaddress1,
    Shiptoaddress2,
    Shiptocity,
    Shiptostatecode,
    Shiptopincode,
    Paymenttype,
    Paymentmode,
    @Semantics.amount.currencyCode: 'Currency'
    Paymentamount,
    @Semantics.amount.currencyCode: 'Currency'
    Advancepaidamount,
    Paymentdate,
    Paymentremarks,
    Paymentterms,
    Paymentinstruction,
    Payeename,
    Payeeaccountnumber,
    @Semantics.amount.currencyCode: 'Currency'
    Paymentamountdue,
    Ifsc,
    Credittransfer,
    Directdebit,
    Creditdays,
    Creditavaileddate,
    Creditreversaldate,
    Refdocumentremarks,
    Refdocumentperiodstartdate,
    Refdocumentperiodenddate,
    Invno,
    Invdt,
    Refcontractdetails,
    Additionalsupportingdocumentde,
    Portcode,
    Documentcurrencycode,
    Destinationcountry,
    Pos,
    @Semantics.amount.currencyCode: 'Currency'
    Documentvalue,
    Documentdiscount,
    Documentothercharges,
    @Semantics.amount.currencyCode: 'Currency'
    Documentvalueinforeigncurrency,
    @Semantics.amount.currencyCode: 'Currency'
    Roundoffamount,
    Differentialpercentage,
    Reversecharge,
    Claimrefund,
    Underigstact,
    Refundeligibility,
    Pnroruniquenumber,
    Availprovisionalitc,
    Originalstatecode,
    Originaldocumentnumber,
    Originaldocumentdate,
    Originalreturnperiod,
    @Semantics.amount.currencyCode: 'Currency'
    Originaltaxablevalue,
    Toemailaddresses,
    Tomobilenumbers,
    Jworiginaldocumentnumber,
    Jworiginaldocumentdate,
    Jwdocumentnumber,
    Jwdocumentdate,
    Autodraftsource,
    Irngenerationdate,
    Isamendment,
    Isavailableingstr2b,
    Itcavailability,
    Itcunavailabilityreason,
    Custom2,
    Custom3,
    Custom5,
    Custom6,
    Custom7,
    Custom8,
    Custom10,
    Custom20,
    Natureofjwdone,
    Lossunitofmeasure,
    @Semantics.quantity.unitOfMeasure: 'uom'
    Losstotalquantity,
    Pusherrors,
    Pushstatus,
    Gstaction,
    Preconciliationsection,
    Reconciliationsection,
    Pushdate,
    Cancelleddate,
    Ispregstregime,
    Amendedtype,
    Isgstr3bfiled,
    Isgstr3bfildate,
    Filingdate,
    LV_ACT_FILING_DATE,
    Filingreturnperiod,
    Filingstatus,
    Isservice,
    Hsn,
    Productcode,
    Name,
    Description,
    Barcode,
    Uqc,
    @Semantics.quantity.unitOfMeasure: 'uom'
    Quantity,
    Freequantity,
    @Semantics.amount.currencyCode: 'Currency'
    Rate,
    @Semantics.amount.currencyCode: 'Currency'
    Cessrate,
    Statecessrate,
    @Semantics.amount.currencyCode: 'Currency'
    Cessnonadvaloremrate,
    Priceperquantity,
    @Semantics.amount.currencyCode: 'Currency'
    Discountamount,
    @Semantics.amount.currencyCode: 'Currency'
    Grossamount,
    Othercharges,
    @Semantics.amount.currencyCode: 'Currency'
    Taxablevalue,
    @Semantics.amount.currencyCode: 'Currency'
    Igstamount,
    @Semantics.amount.currencyCode: 'Currency'
    Cgstamount,
    @Semantics.amount.currencyCode: 'Currency'
    Sgstamount,
    @Semantics.amount.currencyCode: 'Currency'
    Cessamount,
    @Semantics.amount.currencyCode: 'Currency'
    Statecessamount,
    @Semantics.amount.currencyCode: 'Currency'
    Cessnonadvaloremamount,
    Statecessnonadvaloremamount,
    Taxtype,
    Itceligibility,
    @Semantics.amount.currencyCode: 'Currency'
    Itcigstamount,
    @Semantics.amount.currencyCode: 'Currency'
    Itccgstamount,
    @Semantics.amount.currencyCode: 'Currency'
    Itcsgstamount,
    @Semantics.amount.currencyCode: 'Currency'
    Itccessamount,
    Customitem1,
    Customitem2,
    Customitem3,
    Customitem4,
    Customitem5,
    Customitem6,
    Customitem7,
    Customitem8,
    Customitem9,
    Customitem10,
    Cpgstin,
    Cptradename,
    Cplegalname,
    Cporiginaldocumentnumber,
    Cporiginaldocumentdate,
    Cpportcode,
    Cpdocumentnumber,
    Cpdocumentdate,
    Cpvalue,
    Cppos,
    Cpreversecharge,
    Cpdifferentialpercentage,
    Cptransactiontype,
    Cptaxpayertype,
    Cptaxablevalue,
    Cprate,
    Cpigstamount,
    Cpcgstamount,
    Cpsgstamount,
    Cpcessamount,
    Cpfilingstatus,
    Cpsuppliercancellationdate,
    Cpfilingdate,
    Cpfilingreturnperiod,
    Cpautodraftsource,
    Cpirn,
    Cpirngenerationdate,
    Cprefprecedingdocumentdetails,
    Cpavailableingstr2b,
    Cpgstr2breturnperiod,
    Cpavailableingstr98a,
    Cpitcavailability,
    Cpitcunavailabilityreason,
    Cpitcclaimmonth,
    Cpreturnperiod,
    Cpisamendment,
    Cporiginalreturnperiod,
    Cpamendedtype,
    @Semantics.amount.currencyCode: 'Currency'
    Valuediff,
    @Semantics.amount.currencyCode: 'Currency'
    Taxablediff,
    @Semantics.amount.currencyCode: 'Currency'
    Taxdiff,
    Reason,
    RecDate,
    RecTime,
    @Semantics.amount.currencyCode: 'Currency'
    FiDocValue,
    Currency,
    @Semantics.amount.currencyCode: 'Currency'
    FiTaxValue,
    FiTaxCode,
    @Semantics.amount.currencyCode: 'Currency'
    FiTaxRate,
    Status,
    Vendor,
    GstPart,
    Mindk,
    VendorBlockType,
    PaymentDueDate,
    ClearingNo,
    ClearingDate,
    ExpectedFilingDate,
    DueDateBeforeFiling,
    ClearedBeforeFiling,
    GrcCheckApplicable,
    GrcCheckedOn,
    GrcCheckedAt,
    GrcCheckedBy,
    GrcScore,
    Gstr3bScore,
    GrcCheckResult,
    GrcCheckComment,
    BlockedOn,
    BlockedAt,
    BlockedBy,
    BlockComment,
    SplitCompCode,
    SplitGstDocNum,
    SplitYear,
    SplitOn,
    SplitAt,
    SplitBy,
    SplitComment,
    SplitRevCompCode,
    SplitRevDocNum,
    SplitRevYear,
    SplitRevOn,
    SplitRevAt,
    SplitRevBy,
    SplitRevComment,
    JvCompCode,
    JvDocNum,
    JvYear,
    JvPostedOn,
    JvPostedAt,
    JvPostedBy,
    JvComment,
    JvRevCompCode,
    JvRevDocNum,
    JvRevYear,
    JvReversedOn,
    JvReversedAt,
    JvReversedBy,
    JvRevComment,
    ReleasedOn,
    ReleasedAt,
    ReleasedBy,
    ReleaseComment,
    DebitNoteComp,
    DebitNoteNum,
    DebitNoteYear,
    DebitNotePostedOn,
    DebitNotePostedAt,
    DebitNotePostedBy,
    DebitNoteComment,
    DebitNoteRevComp,
    DebitNoteRevNum,
    DebitNoteRevYear,
    DebitNoteReversedOn,
    DebitNoteReversedAt,
    DebitNoteReversedBy,
    DebitNoteRevComment,
    Message,
    LastCheckedOn,
    LastCheckedAt,
    LastCheckedBy,
//    ProcessingOptions,
    Wronggst,
    Gstin,
    GstinPart,
    Remark,
    uom,
    @Semantics.amount.currencyCode: 'Currency'
    TOTALTAX,
    Procstat,
    Cbox,
    Recostat,
    Statusdesc,
    BlockTypeDesc,
    Vendorname,
    Blart,
    Qblock,
    Msme,
    Panno,
    koart,
    @Semantics.amount.currencyCode: 'Currency'
    dmbtr,
    p_block,
    p_deb_n,
    p_split,
    p_dbf_op,
    p_dbf_cl,
    p_daf_cl,
    p_daf_op,
    p_daf,
    p_dbf,
    p_jv,
    p_pbf,
    ProcessingOptionsP1,
    ProcessingOptionsP2,
    ProcessingOptionsP3,
    ProcessingOptionsP4,
    
    concat( concat( concat( ProcessingOptionsP1, ProcessingOptionsP2 ), ProcessingOptionsP3 ), ProcessingOptionsP4 ) as ProcessingOptions,
    
    /* Associations */
    _MT_ACC_DATA,
    _MT_ACC_DOC,
    _MT_VENDOR_BLOCK_TYPE,
    _MT_VENDOR_DATA,
    _MT_VENDOR_DATA_M
}







@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'lt_bsik'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_R_lt_bsik

**Label:** lt_bsik

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'lt_bsik'
@Metadata.ignorePropagatedAnnotations: true
define view entity ZPB_R_lt_bsik as select from bsak_view as b
inner join ZPB_R_MT_ACC_DATA_LY1 as a on b.bukrs = a.Bukrs
                                      and b.belnr = a.Belnr
                                      and b.gjahr = a.Gjahr
                                      
inner join t001 as t on b.bukrs = t.bukrs                                     
{
    key b.bukrs,
    key b.belnr,
    key b.gjahr,
    key b.buzei,
    b.zlspr,
    b.zterm,
    b.augbl,
    @Semantics.amount.currencyCode: 'waers'
    b.dmbtr,
    
    t.waers
}
where b.zlspr <> 'Y'

------------------------------------------------------------------------------------------------

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_ACC_DATA'
//@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_R_MT_ACC_DATA

**Label:** MT_ACC_DATA

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_ACC_DATA'
//@Metadata.ignorePropagatedAnnotations: true
define view entity ZPB_R_MT_ACC_DATA
  as select from bseg as a
  inner join bkpf as b on a.bukrs = b.bukrs
                       and a.belnr = b.belnr
                       and a.gjahr = b.gjahr
                       
  inner join ZPB_R_MT_ACC_DOC_F as c on a.bukrs = c.BUKRS
                                   and a.belnr = c.BELNR
                                   and a.gjahr = c.GJAHR                    
{
  key a.bukrs as Bukrs,
  key a.belnr as Belnr,
  key a.gjahr as Gjahr,
  
  b.bldat ,
  b.budat,
  b.cpudt,
  b.xblnr,
  b.bktxt,
  b.awtyp,
  b.awkey,
  b.awsys,
  b.blart,
  b.glvor,
  a.buzei,
  a.buzid,
  a.augdt,
  @Semantics.amount.currencyCode: 'hwaer'
  a.dmbtr,
  a.zfbdt,
  a.zbd1t,
  a.zbd2t,
  a.zbd3t,
  a.koart,
  a.shkzg,
  a.lifnr,
  a.rebzg,
  a.zuonr,
  a.sgtxt,
  a.mwskz,
  a.zlspr,
  a.umskz,
  a.prctr,
  a.gsber,
  a.kostl,
  a.bupla,
  a.secco,
  a.hsn_sac,
  a.gst_part,
  a.augbl,
  a.zterm,
  
  // only for UOM Reference
  b.hwaer
  
  
}

------------------------------------------------------------------------------------------------

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_ACC_DATA_LY1'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_R_MT_ACC_DATA_LY1

**Label:** MT_ACC_DATA_LY1

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_ACC_DATA_LY1'
@Metadata.ignorePropagatedAnnotations: true
define view entity ZPB_R_MT_ACC_DATA_LY1
  as select distinct from ZPB_R_MT_ACC_DATA
{
  key Bukrs,
  key Belnr,
  key Gjahr,
      bldat,
      budat,
      cpudt,
      xblnr,
      bktxt,
      awtyp,
      awkey,
      awsys,
      blart,
      glvor,
      buzei,
      buzid,
      augdt,
      @Semantics.amount.currencyCode: 'hwaer'
      dmbtr,
      zfbdt,
      zbd1t,
      zbd2t,
      zbd3t,
      koart,
      shkzg,
      lifnr,
      rebzg,
      zuonr,
      sgtxt,
      mwskz,
      zlspr,
      umskz,
      prctr,
      gsber,
      kostl,
      bupla,
      secco,
      hsn_sac,
      gst_part,
      augbl,
      zterm,
      hwaer
}
where koart = 'K'

------------------------------------------------------------------------------------------------------

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_ACC_DOC'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_R_MT_ACC_DOC

**Label:** MT_ACC_DOC

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_ACC_DOC'
@Metadata.ignorePropagatedAnnotations: true
define view entity ZPB_R_MT_ACC_DOC
  as select from ZPB_I_2A_PAY_BLOCK_LY1
{
  Custom9 as BUKRS,
  Custom1 as BELNR,
  Custom4 as GJAHR

}

union all

select from ZPB_I_2A_PAY_BLOCK_LY1
{
  MtAccDoc2_BUKRS as BUKRS,
  MtAccDoc2_BELNR as BELNR,
  MtAccDoc2_GJAHR as GJAHR
}

union all

select from ZPB_I_2A_PAY_BLOCK_LY1
{
  MtAccDoc3_BUKRS as BUKRS,
  MtAccDoc3_BELNR as BELNR,
  MtAccDoc3_GJAHR as GJAHR
}

union all

select from ZPB_I_2A_PAY_BLOCK_LY1
{
  MtAccDoc4_BUKRS as BUKRS,
  MtAccDoc4_BELNR as BELNR,
  MtAccDoc4_GJAHR as GJAHR
}

union all

select from ZPB_I_2A_PAY_BLOCK_LY1
{
  MtAccDoc5_BUKRS as BUKRS,
  MtAccDoc5_BELNR as BELNR,
  MtAccDoc5_GJAHR as GJAHR
}

union all

select from ZPB_I_2A_PAY_BLOCK_LY1
{
  MtAccDoc6_BUKRS as BUKRS,
  MtAccDoc6_BELNR as BELNR,
  MtAccDoc6_GJAHR as GJAHR
};


---------------------------------------------------------------------------------------------------------

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_ACC_DOC_F'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_R_MT_ACC_DOC_F

**Label:** MT_ACC_DOC_F

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_ACC_DOC_F'
@Metadata.ignorePropagatedAnnotations: true
define view entity ZPB_R_MT_ACC_DOC_F
  as select distinct from ZPB_R_MT_ACC_DOC
{
  key BUKRS,
  key BELNR,
  key GJAHR
}


------------------------------------------------------------------------------------------------------

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_TAX_DATA'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_R_MT_TAX_DATA

**Label:** MT_TAX_DATA

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT_TAX_DATA'
@Metadata.ignorePropagatedAnnotations: true
define view entity ZPB_R_MT_TAX_DATA as select from bset as a
left outer join t001 as t on a.bukrs = t.bukrs
                           
{
    key a.bukrs as Bukrs,
    key a.belnr as Belnr,
    key a.gjahr as Gjahr,
    key a.buzei as Buzei,
    a.mwskz,
    a.txgrp,
    a.taxps,
    a.ktosl,
    a.kschl,
    @Semantics.amount.currencyCode: 'Currency'
    a.kbetr,
    @Semantics.amount.currencyCode: 'Currency'
    a.hwbas,
    @Semantics.amount.currencyCode: 'Currency'
    a.hwste,
    a.shkzg,
    a.hkont,
    t.waers as Currency
    
}

--------------------------------------------------------------------------------------------------
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT VENDOR DATA'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_R_MT_VENDOR_DATA

**Label:** MT VENDOR DATA

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT VENDOR DATA'
@Metadata.ignorePropagatedAnnotations: true
define view entity ZPB_R_MT_VENDOR_DATA
  as select from    ZPB_R_MT_ACC_DATA_LY1 as a
    left outer join lfa1              as v on v.lifnr = a.lifnr
                                           or v.lifnr = a.gst_part

    left outer join t016t             as t on v.brsch = t.brsch
{
  key a.Bukrs,
  key a.Belnr,
  key a.Gjahr,

      v.lifnr as VENDOR,
      v.name1 as NAME,
      v.stcd3 as STCD3,
      v.brsch,
      t.brtxt


}
where
  a.koart = 'K'

------------------------------------------------------------------------------------------------------------------------------------

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT VENDOR DATA M'
@Metadata.ignorePropagatedAnnotations: true
```

---

## ZPB_R_MT_VENDOR_DATA_M

**Label:** MT VENDOR DATA M

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'MT VENDOR DATA M'
@Metadata.ignorePropagatedAnnotations: true
define view entity ZPB_R_MT_VENDOR_DATA_M
  as select from ZPB_R_MT_ACC_DATA as a
    inner join   lfb1              as b on b.bukrs   = a.Bukrs
                                        and(
                                          b.lifnr    = a.lifnr
                                          or b.lifnr = a.gst_part
                                        )
{
  key a.Bukrs,
  key a.Belnr,
  key a.Gjahr,

      b.lifnr as VENDOR,
      b.mindk as MINDK

}
where
  a.koart = 'K'


---------------------------------------------------

@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Vendor Block Type'
@Metadata.ignorePropagatedAnnotations: true
```

---

## zpb_r_vendor_block_type

**Label:** Vendor Block Type

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Vendor Block Type'
@Metadata.ignorePropagatedAnnotations: true
define view entity zpb_r_vendor_block_type
  as select from ZPB_R_MT_ACC_DATA as a
    inner join   zcy_t_2a_blk_typ  as b on b.vendor = a.lifnr
                                        or b.vendor = a.gst_part
{
  key a.Bukrs,
  key a.Belnr,
  key a.Gjahr,
      a.lifnr,
      a.gst_part,
      b.vendor,
      b.vendor_block_type,
      b.grc_check_applicable

}

union all

select from  ZPB_R_MT_ACC_DATA as a
  inner join zcy_t_2a_blk_typ  as b on b.vendor = a.lifnr
                                    or b.vendor = a.gst_part
  inner join lfa1              as c on b.vendor = c.ktokk
{
  key a.Bukrs,
  key a.Belnr,
  key a.Gjahr,
      a.lifnr,
      a.gst_part,
      b.vendor,
      b.vendor_block_type,
      b.grc_check_applicable
      
}
```

---

## ZPB_R_MT_TAX_TOTAL

**Label:** Aggregated FI Tax Total

```abap
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Aggregated FI Tax Total'
define view entity ZPB_R_MT_TAX_TOTAL
  as select from ZPB_R_MT_TAX_DATA
{
  key Bukrs,
  key Belnr,
  key Gjahr,

  @Semantics.amount.currencyCode: 'Currency'
  sum(
    case
      when shkzg = 'S' then hwste
      when shkzg = 'H' then hwste * -1
      else cast( 0 as abap.curr( 15, 2 ) )
    end
  ) as FiTaxTotal,

  Currency
}
group by
  Bukrs,
  Belnr,
  Gjahr,
  Currency
```

