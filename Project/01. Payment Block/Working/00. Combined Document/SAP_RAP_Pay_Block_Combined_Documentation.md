# SAP RAP Pay Block Application — Combined Technical Documentation

## Master document

This file combines the supplied CDS layers, helper CDS views, projection CDS, and behavior definition into one Markdown document. Missing implementation source is explicitly listed as pending.

## Table of contents

1. [RAP Application Overview](#1-rap-application-overview)
2. [Main CDS Layers](#2-main-cds-layers)
3. [Helper CDS Layers](#3-helper-cds-layers)
4. [Projection CDS](#4-projection-cds)
5. [Behavior Definition](#5-behavior-definition)
6. [Tables and Structures](#6-tables-and-structures)
7. [Pending Implementation Objects](#7-pending-implementation-objects)


---

# 1. RAP Application Overview

# SAP RAP Application — Pay Block

## Purpose and scope

This is the combined technical documentation for the user's SAP RAP Pay Block application, assembled from the supplied CDS and behavior Markdown files.

## Known missing implementation objects

- **Behavior implementation class:** `ZBP_PB_I_2A_PAY_BLOCK_LY4` implementation is not yet supplied.
- **Helper class:** `ZCL_PB_PROCESSOR` is not yet supplied.

These objects are intentionally marked as pending. No implementation logic has been invented. They can be added to this document when the source objects are provided.

## Current supplied artifacts

- Interface/root CDS layers: `ZPB_I_2A_PAY_BLOCK`, `ZPB_I_2A_PAY_BLOCK_LY1`, `ZPB_I_2A_PAY_BLOCK_LY2`, `ZPB_I_2A_PAY_BLOCK_LY3`, `ZPB_I_2A_PAY_BLOCK_LY4`
- Helper/reference CDS views from the supplied helper CDS document
- Projection CDS: `ZPB_C_2A_PAY_BLOCK`
- Behavior definition for `ZPB_I_2A_PAY_BLOCK_LY4`

> **Source handling:** The sections below are included from the supplied files. The source objects have not been functionally reconciled or corrected during this merge.


---

# 2. Main CDS Layers

# SAP CDS Views — Pay Block Layers

This document contains the CDS view definitions from the supplied `All CDS Layers.txt` file, organized by layer for easier reading and navigation.

## Contents

1. [`ZPB_I_2A_PAY_BLOCK`](#zpb-i-2a-pay-block)
2. [`ZPB_I_2A_PAY_BLOCK_LY1`](#zpb-i-2a-pay-block-ly1)
3. [`ZPB_I_2A_PAY_BLOCK_LY2`](#zpb-i-2a-pay-block-ly2)
4. [`ZPB_I_2A_PAY_BLOCK_LY3`](#zpb-i-2a-pay-block-ly3)
5. [`ZPB_I_2A_PAY_BLOCK_LY4`](#zpb-i-2a-pay-block-ly4)

> **Note:** The CDS source has been preserved as provided, with repeated blank lines reduced for readability. No business logic has intentionally been changed.


---

## `ZPB_I_2A_PAY_BLOCK`

```abap
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
      a.p_dbf                                       as P_dbf1,
      a.p_jv                                          as P_jv1,
      a.p_pbf                                        as P_pbf1,
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
```


---

## `ZPB_I_2A_PAY_BLOCK_LY1`

```abap
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

      a.P_dbf1                                  as p_pbf,
      a.P_jv1                                   as p_jv,
      a.P_pbf1                                  as p_dbf,

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
```


---

## `ZPB_I_2A_PAY_BLOCK_LY2`

```abap
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
      case when FiTaxCode is null then _MT_TAX_TOTAL.mwskz 
      else FiTaxCode
      end as FiTaxCode,
      
      @Semantics.amount.currencyCode: 'Currency'
      cast( case when FiTaxRate is null then _MT_TAX_TOTAL.TaxRate
      else cast( FiTaxRate as abap.dec( 15, 2 ) )
      end as abap.dec( 15, 2 ) ) as FiTaxRate ,
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
```


---

## `ZPB_I_2A_PAY_BLOCK_LY3`

```abap
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
```


---

## `ZPB_I_2A_PAY_BLOCK_LY4`

```abap
define root view entity ZPB_I_2A_PAY_BLOCK_LY4
  as select from ZPB_I_2A_PAY_BLOCK_LY3
//  composition [0..1] of ZPB_I_2A_PARAMETER as _PARAMETER
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
```


---

# 3. Helper CDS Layers

# SAP Helper CDS Layers

Formatted Markdown version of the supplied `All Helper CDS Layers.txt` source.

## Contents

- [`ZPB_R_MT_ACC_DATA`](#zpb-r-mt-acc-data)
- [`ZPB_R_MT_ACC_DATA_LY1`](#zpb-r-mt-acc-data-ly1)
- [`ZPB_R_MT_ACC_DOC`](#zpb-r-mt-acc-doc)
- [`ZPB_R_MT_ACC_DOC_F`](#zpb-r-mt-acc-doc-f)
- [`ZPB_R_MT_TAX_DATA`](#zpb-r-mt-tax-data)
- [`ZPB_R_MT_TAX_DATA`](#zpb-r-mt-tax-data)
- [`ZPB_R_MT_VENDOR_DATA`](#zpb-r-mt-vendor-data)
- [`ZPB_R_MT_VENDOR_DATA_M`](#zpb-r-mt-vendor-data-m)
- [`zpb_r_vendor_block_type`](#zpb-r-vendor-block-type)

> **Note:** The CDS definitions are preserved from the supplied source. Repeated blank lines have been reduced for readability; business logic has not intentionally been changed.

---

## `ZPB_R_MT_ACC_DATA`

```abap
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
```

---

## `ZPB_R_MT_ACC_DATA_LY1`

```abap
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
```

---

## `ZPB_R_MT_ACC_DOC`

```abap
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
```

---

## `ZPB_R_MT_ACC_DOC_F`

```abap
define view entity ZPB_R_MT_ACC_DOC_F
  as select distinct from ZPB_R_MT_ACC_DOC
{
  key BUKRS,
  key BELNR,
  key GJAHR
}
```

---

## `ZPB_R_MT_TAX_DATA`

```abap
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
```

---

## `ZPB_R_MT_TAX_DATA`

```abap
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
```

---

## `ZPB_R_MT_VENDOR_DATA`

```abap
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
```

---

## `ZPB_R_MT_VENDOR_DATA_M`

```abap
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
```

---

## `zpb_r_vendor_block_type`

```abap
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

# 4. Projection CDS

# ZPB_C_2A_PAY_BLOCK

Projection CDS view for Pay Block.

```abap
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Projection for Pay Block'
//@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
define root view entity ZPB_C_2A_PAY_BLOCK
  provider contract transactional_query

  as projection on ZPB_I_2A_PAY_BLOCK_LY4

{
  key Custom9,
  key Custom1,
  key Custom4,
      Procstat,
      Cbox,
      Recostat,
      Statusdesc,
      BlockTypeDesc,
      Vendorname,
      Blart,
      Qblock,
      Preconciliationsection,
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
      Paymentamount,
      Advancepaidamount,
      Paymentdate,
      Paymentremarks,
      Paymentterms,
      Paymentinstruction,
      Payeename,
      Payeeaccountnumber,
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
      Documentvalue,
      Documentdiscount,
      Documentothercharges,
      Documentvalueinforeigncurrency,
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
      Losstotalquantity,
      Pusherrors,
      Pushstatus,
      Gstaction,
      Reconciliationsection,
      Pushdate,
      Cancelleddate,
      Ispregstregime,
      Amendedtype,
      Isgstr3bfiled,
      Isgstr3bfildate,
      Filingdate,
      Filingreturnperiod,
      Filingstatus,
      Isservice,
      Hsn,
      Productcode,
      Name,
      Description,
      Barcode,
      Uqc,
      Quantity,
      Freequantity,
      Rate,
      Cessrate,
      Statecessrate,
      Cessnonadvaloremrate,
      Priceperquantity,
      Discountamount,
      Grossamount,
      Othercharges,
      Taxablevalue,
      Igstamount,
      Cgstamount,
      Sgstamount,
      Cessamount,
      Statecessamount,
      Cessnonadvaloremamount,
      Statecessnonadvaloremamount,
      Taxtype,
      Itceligibility,
      Itcigstamount,
      Itccgstamount,
      Itcsgstamount,
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
      Valuediff,
      Taxablediff,
      Taxdiff,
      Reason,
      RecDate,
      RecTime,
      Currency,
      FiDocValue,
      FiTaxValue,
      FiTaxCode,
      FiTaxRate,
      Status,
      GstPart,
      Vendor,
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
      ProcessingOptions,
      Wronggst,
      Gstin,
      GstinPart,
      Remark,
      TOTALTAX,
      uom,
      Msme,
      p_pbf,
      p_daf,
      p_jv,
      _MT_ACC_DATA,
      _MT_ACC_DOC,
      _MT_VENDOR_BLOCK_TYPE,
      _MT_VENDOR_DATA,
      _MT_VENDOR_DATA_M
}
```


---

# 5. Behavior Definition

# Behavior Definition — ZPB_I_2A_PAY_BLOCK_LY4

Behavior definition supplied with the projection source.

```abap
managed implementation in class zbp_pb_i_2a_pay_block_ly4 unique;
strict ( 2 );

define behavior for ZPB_I_2A_PAY_BLOCK_LY4 alias PayBlock
persistent table z2a_t_pay_block
lock master
authorization master ( instance )
//etag master <field_name>
{
  create;
  update;
  delete;

  static action pbf parameter ZPB_A_STATUS result [0..*] $self;
  action process parameter ZPB_S_PROCESS_PARAM;

  // side effects {
  //   action pbf affects field P_pbf, field P_jv;
  // }

  static action unblock result [1] $self;

  mapping for z2a_t_pay_block
  {
    Custom1        = custom1;
    Custom4        = custom4;
    Custom9        = custom9;
    Returnperiod   = returnperiod;
    Locationgstin  = locationgstin;
    Documentnumber = documentnumber;
    Documentdate   = documentdate;
    Financialyear  = financialyear;
    FiDocValue     = fi_doc_value;
    FiTaxValue     = fi_tax_value;
    FiTaxCode      = fi_tax_code;
    FiTaxRate      = fi_tax_rate;
    Status         = status;
    Currency       = currency;
    P_dbf          = p_dbf1;
    P_jv           = p_jv1;
    P_pbf          = p_pbf1;
  }

  // mapping for zcy_tab_rrp_e {
  //   P_dbf1 = p_dbf;
  //   P_jv1  = p_jv;
  //   P_pbf1 = p_pbf;
  // }
}
```


---

# 6. Tables and Structures

This section incorporates the supplied database table and DDIC structure definitions for the GSTR2A Pay Block application. Full formatted definitions and field-group notes are maintained in the standalone companion document `SAP_RAP_Pay_Block_Tables_and_Structures.md` and are reproduced below for a self-contained master document.

# 1. `Z2A_T_PAY_BLOCK` — Payment Block Database Table

**Label:** GSTR2A Payment Block Data.

**Purpose:** Transparent table for GSTR2A payment-block records. It uses `MANDT`, `CUSTOM1`, `CUSTOM4`, and `CUSTOM9` as key fields and includes the shared payment-block status structure.

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

## Key fields

| Field | Type | Notes |
|---|---|---|
| `MANDT` | `MANDT` | Client key |
| `CUSTOM1` | `ZCY_DET_CUSTOM1` | Business key component |
| `CUSTOM4` | `ZCY_DET_CUSTOM4` | Business key component |
| `CUSTOM9` | `ZCY_DET_CUSTOM9` | Business key component |

## Included structure

`ZCY_STR_2A_PAY_BLOCK_STATUS` supplies FI document/tax information, reconciliation status, vendor/payment-block information, GRC check data, split/journal-voucher/debit-note details, release details, processing metadata, and related GST fields.

---

# 2. `ZCY_STR_2A_PAY_BLOCK` — ALV Structure

**Label:** GSTR2A Payment Block Program - ALV Structure.

**Purpose:** ALV/output structure for the GSTR2A payment-block program. It contains display and selection indicators, document and tax values, reconciliation and filing details, counterparty fields, custom fields, ITC/tax details, and the shared status structure.

```abap
@EndUserText.label : 'GSTR2A Payment Block Program - ALV Structure.'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
define structure zcy_str_2a_pay_block
{
  procstat               : icon_d;
  cbox                   : checkbox;
  reco_stat              : icon_d;

  financialyear          : zcy_det_financialyear;
  returnperiod            : zcy_det_returnperiod;
  locationgstin           : zcy_det_locationgstin;
  documentnumber          : zcy_det_documentnumber;
  documentdate            : bldat;
  custom1                 : zcy_det_custom1;
  custom4                 : zcy_det_custom4;
  custom9                 : zcy_det_custom9;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  documentvalue           : zcy_det_documentvalue;
  pushstatus              : zcy_det_pushstatus;
  pushdate                : zcy_det_pushdate;
  reconciliationsection   : zcy_det_reconciliationsection;
  reason                  : zcy_det_reason;
  filingdate              : zcy_det_filingdate;
  filingreturnperiod      : zcy_det_filingreturnperiod;
  filingstatus            : zcy_det_filingstatus;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  rate                    : zcy_det_rate;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  taxablevalue            : zcy_det_taxablevalue;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  igstamount              : zcy_det_igstamount;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  cgstamount              : zcy_det_cgstamount;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  sgstamount              : zcy_det_sgstamount;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  totaltax                : hwste;

  rec_date                : datum;
  rec_time                : uzeit;

  include zcy_str_2a_pay_block_status;

  @EndUserText.label : 'Docuemnt Processing Status Description'
  status_desc             : abap.char(50);

  @EndUserText.label : 'Vendor Block Type Description'
  block_type_desc         : abap.char(50);

  vendor_name             : name1_gp;
  blart                   : blart;
  qblock                  : char1;
  preconciliationsection  : char40;

  cpgstin                 : zcy_det_cpgstin;
  cptradename             : zcy_det_cptradename;
  cplegalname             : zcy_det_cplegalname;
  cpdocumentnumber        : zcy_det_cpdocumentnumber;
  cpdocumentdate          : zcy_det_cpdocumentdate;
  cpvalue                 : zcy_det_cpvalue;
  cppos                   : zcy_det_cppos;
  cpreversecharge         : zcy_det_cpreversecharge;
  cptransactiontype       : zcy_det_cptransactiontype;
  cptaxpayertype          : zcy_det_cptaxpayertype;
  cptaxablevalue          : zcy_det_cptaxablevalue;
  cprate                  : zcy_det_cprate;

  cpigstamount            : zcy_det_cpigstamount;
  cpcgstamount            : zcy_det_cpcgstamount;
  cpsgstamount            : zcy_det_cpsgstamount;
  cpcessamount            : zcy_det_cpcessamount;

  cpfilingstatus          : zcy_det_cpfilingstatus;
  cpfilingdate            : zcy_det_cpfilingdate;
  cpfilingreturnperiod    : zcy_det_cpfilingreturnperiod;

  isgstr3bfiled           : zcy_det_isgstr3bfiled;
  isgstr3bfildate         : zcy_det_is3bfilingdate;

  cpitcavailability       : zcy_det_cpitcavailability;
  cpreturnperiod          : zcy_det_cpreturnperiod;
  cpisamendment           : zcy_det_cpisamendment;
  cpavailableingstr2b     : zcy_det_cpavailableingstr2b;
  cpgstr2breturnperiod    : zcy_det_cpgstr2breturnperiod;
  cpavailableingstr98a    : zcy_det_cpavailableingstr98a;
  cpitcclaimmonth         : zcy_det_cpitcclaimmonth;

  billfromlegalname       : zcy_det_billfromlegalname;
  billfromtradename       : zcy_det_billfromtradename;

  pos                     : zcy_det_pos;
  reversecharge           : zcy_det_reversecharge;
  transactiontype         : zcy_det_transactiontype;
  taxpayertype            : zcy_det_taxpayertype;

  @Semantics.amount.currencyCode : 'komk.waerk'
  cessamount              : zcy_det_cessamount;

  isamendment             : zcy_det_isamendment;

  custom2                 : zcy_det_custom2;
  custom3                 : zcy_det_custom3;
  custom5                 : zcy_det_custom5;
  custom6                 : zcy_det_custom6;
  custom7                 : zcy_det_custom7;
  custom8                 : zcy_det_custom8;
  custom10                : zcy_det_custom10;

  hsn                     : zcy_det_hsn;
  itceligibility          : zcy_det_itceligibility;

  @Semantics.amount.currencyCode : 'komk.waerk'
  itcigstamount           : zcy_det_itcigstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
  itccgstamount           : zcy_det_itccgstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
  itcsgstamount           : zcy_det_itcsgstamount;

  @Semantics.amount.currencyCode : 'komk.waerk'
  itccessamount           : zcy_det_itccessamount;

  customitem3             : zcy_det_customitem3;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  valuediff               : zcy_det_valuediff;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  taxablediff             : zcy_det_taxablediff;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  taxdiff                 : zcy_det_taxdiff;

  msme                    : zcy_msme;
}
```

## Main field groups

- **ALV controls:** `PROCSTAT`, `CBOX`, `RECO_STAT`
- **Document identification:** financial year, return period, GSTIN, document number/date, and custom keys
- **Amounts and taxes:** document value, rate, taxable value, IGST, CGST, SGST, total tax, cess, and ITC amounts
- **Reconciliation and filing:** push status/date, reconciliation section, reason, filing date/period/status, and record timestamps
- **Counterparty data:** counterparty GSTIN/name/document/tax fields and ITC availability information
- **Additional metadata:** vendor, document type, block descriptions, custom fields, MSME, and the included status structure

---

# 3. `ZCY_STR_2A_PAY_BLOCK_STATUS` — Status Structure

**Label:** Status fields for GSTR2A based payment block program.

**Purpose:** Shared status and processing structure included by `Z2A_T_PAY_BLOCK` and `ZCY_STR_2A_PAY_BLOCK`. It stores FI/tax details, reconciliation status, vendor block information, GRC checks, split and reversal data, journal vouchers, debit notes, release information, and processing metadata.

```abap
@EndUserText.label : 'Status fields for GSTR2A based payment block program'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
define structure zcy_str_2a_pay_block_status {

  @Semantics.amount.currencyCode : 'vbak.waerk'
  fi_doc_value           : dmbtr;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  fi_tax_value           : hwste;

  fi_tax_code            : mwskz;

  @Semantics.amount.currencyCode : 'vbak.waerk'
  fi_tax_rate            : kbetr;

  status                 : zcy_det_2a_pay_block_status;
  gst_part               : j_1ig_partner;
  vendor                 : lifnr;
  mindk                  : mindk;
  vendor_block_type      : zcy_det_block_type;
  payment_due_date       : faedt_fpos;
  clearing_no            : augbl;
  clearing_date          : augdt;
  expected_filing_date   : zcy_det_exp_fil_date;
  due_date_before_filing : zcy_det_due_b4_filing;
  cleared_before_filing  : zcy_det_clr_b4_fil;

  grc_check_applicable   : zcy_det_grc_appl;
  grc_checked_on         : zcy_det_grc_date;
  grc_checked_at         : zcy_det_grc_time;
  grc_checked_by         : zcy_det_grc_user;
  grc_score              : zcy_det_grc_score;
  gstr3b_score           : zcy_det_grc3_score;
  grc_check_result       : zcy_det_grc_result;
  grc_check_comment      : zcy_det_grc_comment;

  blocked_on             : zcy_det_block_date;
  blocked_at             : zcy_det_block_time;
  blocked_by             : zcy_det_block_user;
  block_comment          : zcy_det_block_comment;

  split_comp_code        : zcy_det_split_comp;
  split_gst_doc_num      : zcy_det_split_gst_doc;
  split_year             : zcy_det_split_year;
  split_on               : zcy_det_split_date;
  split_at               : zcy_det_split_time;
  split_by               : zcy_det_split_user;
  split_comment          : zcy_det_split_comment;

  split_rev_comp_code    : zcy_det_split_rev_comp;
  split_rev_doc_num      : zcy_det_split_rev_doc;
  split_rev_year         : zcy_det_split_rev_year;
  split_rev_on           : zcy_det_split_rev_date;
  split_rev_at           : zcy_det_split_rev_time;
  split_rev_by           : zcy_det_split_rev_user;
  split_rev_comment      : zcy_det_split_rev_comment;

  jv_comp_code           : zcy_det_jv_comp;
  jv_doc_num             : zcy_det_jv_doc;
  jv_year                : zcy_det_jv_year;
  jv_posted_on           : zcy_det_jv_date;
  jv_posted_at           : zcy_det_jv_time;
  jv_posted_by           : zcy_det_jv_user;
  jv_comment             : zcy_det_jv_comment;

  jv_rev_comp_code       : zcy_det_jv_rev_comp;
  jv_rev_doc_num         : zcy_det_jv_rev_doc;
  jv_rev_year            : zcy_det_jv_rev_year;
  jv_reversed_on         : zcy_det_jv_rev_date;
  jv_reversed_at         : zcy_det_jv_rev_time;
  jv_reversed_by         : zcy_det_jv_rev_user;
  jv_rev_comment         : zcy_det_jv_rev_comment;

  released_on            : zcy_det_release_date;
  released_at            : zcy_det_release_time;
  released_by            : zcy_det_release_user;
  release_comment        : zcy_det_release_comment;

  debit_note_comp        : zcy_det_dn_comp;
  debit_note_num         : zcy_det_dn_num;
  debit_note_year        : zcy_det_dn_year;
  debit_note_posted_on   : zcy_det_dn_date;
  debit_note_posted_at   : zcy_det_dn_time;
  debit_note_posted_by   : zcy_det_dn_user;
  debit_note_comment     : zcy_det_dn_comment;

  debit_note_rev_comp    : zcy_det_dn_rev_comp;
  debit_note_rev_num     : zcy_det_dn_rev_num;
  debit_note_rev_year    : zcy_det_dn_rev_year;
  debit_note_reversed_on : zcy_det_dn_rev_date;
  debit_note_reversed_at : zcy_det_dn_rev_time;
  debit_note_reversed_by : zcy_det_dn_rev_user;
  debit_note_rev_comment : zcy_det_dn_rev_comment;

  message                : zcy_det_message;

  last_checked_on        : zcy_det_lc_date;
  last_checked_at        : zcy_det_lc_time;
  last_checked_by        : zcy_det_lc_user;

  processing_options     : zcy_det_proc_opt;

  wronggst               : char15;
  gstin                  : zcy_det_billfromgstin;
  gstin_part             : char20;
  documenttype           : zcy_det_documenttype;
  remark                 : zcyg_remarks;
}
```

## Status field groups

| Group | Representative fields |
|---|---|
| FI and tax | `FI_DOC_VALUE`, `FI_TAX_VALUE`, `FI_TAX_CODE`, `FI_TAX_RATE` |
| Payment and clearing | `VENDOR`, `VENDOR_BLOCK_TYPE`, `PAYMENT_DUE_DATE`, `CLEARING_NO`, `CLEARING_DATE` |
| Filing timing | `EXPECTED_FILING_DATE`, `DUE_DATE_BEFORE_FILING`, `CLEARED_BEFORE_FILING` |
| GRC checks | `GRC_CHECK_APPLICABLE`, checked-on/at/by fields, scores, result, comment |
| Blocking | `BLOCKED_ON`, `BLOCKED_AT`, `BLOCKED_BY`, `BLOCK_COMMENT` |
| Split and reversal | `SPLIT_*` and `SPLIT_REV_*` fields |
| Journal vouchers | `JV_*` and `JV_REV_*` fields |
| Debit notes | `DEBIT_NOTE_*` and `DEBIT_NOTE_REV_*` fields |
| Release and processing | `RELEASED_*`, `MESSAGE`, `LAST_CHECKED_*`, `PROCESSING_OPTIONS` |
| GST metadata | `WRONGGST`, `GSTIN`, `GSTIN_PART`, `DOCUMENTTYPE`, `REMARK` |

---

# 4. `ZCY_TAB_RRP_E` — Regular Returns Purchase Export Table

**Label:** Regular Returns Purchase Export.

**Purpose:** Transparent application table containing regular-returns purchase/export data. Its key is composed of client and the three custom key fields. The definition includes document, supplier/customer, payment, tax, ITC, counterparty, reconciliation, and record timestamp data.

```abap
@EndUserText.label : 'Regular Returns Purchase Export.'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory        : #TRANSPARENT
@AbapCatalog.deliveryClass       : #A
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

## Field groups

- **Keys and document identity:** client, custom keys, return period, GSTIN, document number/date, serial number, financial year, and IRN
- **Supplier/customer and address details:** bill-from, dispatch-from, bill-to, and ship-to identity/address fields
- **Payments and references:** payment mode/type/amounts, dates, terms, payee details, bank details, credit information, and referenced documents
- **Invoice values and tax:** document/taxable values, rates, discounts, charges, IGST/CGST/SGST/cess, and ITC tax amounts
- **GST return and ITC:** filing status/period, GSTR-3B/GSTR-2B availability, ITC eligibility/claim data, amendment/original-document fields
- **Counterparty comparison:** `CP*` fields for supplier/counterparty document and tax information
- **Reconciliation and audit:** `VALUEDIFF`, `TAXABLEDIF`, `TAXDIFF`, `REASON`, `REC_DATE`, and `REC_TIME`


---

# 7. Pending Implementation Objects

## Behavior implementation class

**Object:** `ZBP_PB_I_2A_PAY_BLOCK_LY4`

**Status:** Pending — implementation source not supplied yet.

## Helper class

**Object:** `ZCL_PB_PROCESSOR`

**Status:** Pending — implementation source not supplied yet.

When you provide either object later, its implementation can be inserted into the relevant section and this combined Markdown document regenerated.
