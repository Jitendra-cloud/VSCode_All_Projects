# Covert RAP Project

## 2A Payment Block Interface

### CDS View Entity

**Object:** `ZI_2A_PAY_BLOCK`  
**Description:** 2A Payment Block  
**Type:** Root View Entity  
**Source:** `zcy_t_2a_pay_blk`

```abap
@EndUserText.label: '2A Payment Block'
@AccessControl.authorizationCheck: #NOT_REQUIRED
define root view entity ZI_2A_PAY_BLOCK
  as select from zcy_t_2a_pay_blk
{
    key custom1,
    key custom4,
    key custom9,

        returnperiod,
        locationgstin,
        documentnumber,
        documentdate,
        financialyear,

        fi_doc_value,
        fi_tax_value,
        fi_tax_code,
        fi_tax_rate,

        status,
        gst_part,
        vendor,
        mindk,
        vendor_block_type,
        payment_due_date,
        clearing_no,
        clearing_date,
        expected_filing_date,
        due_date_before_filing,
        cleared_before_filing,

        grc_check_applicable,
        grc_checked_on,
        grc_checked_at,
        grc_checked_by,
        grc_score,
        gstr3b_score,
        grc_check_result,
        grc_check_comment,

        blocked_on,
        blocked_at,
        blocked_by,
        block_comment,

        split_comp_code,
        split_gst_doc_num,
        split_year,
        split_on,
        split_at,
        split_by,
        split_comment,

        split_rev_comp_code,
        split_rev_doc_num,
        split_rev_year,
        split_rev_on,
        split_rev_at,
        split_rev_by,
        split_rev_comment,

        jv_comp_code,
        jv_doc_num,
        jv_year,
        jv_posted_on,
        jv_posted_at,
        jv_posted_by,
        jv_comment,

        jv_rev_comp_code,
        jv_rev_doc_num,
        jv_rev_year,
        jv_reversed_on,
        jv_reversed_at,
        jv_reversed_by,
        jv_rev_comment,

        released_on,
        released_at,
        released_by,
        release_comment,

        debit_note_comp,
        debit_note_num,
        debit_note_year,
        debit_note_posted_on,
        debit_note_posted_at,
        debit_note_posted_by,
        debit_note_comment,

        debit_note_rev_comp,
        debit_note_rev_num,
        debit_note_rev_year,
        debit_note_reversed_on,
        debit_note_reversed_at,
        debit_note_rev_by,
        debit_note_rev_comment,

        message,

        last_checked_on,
        last_checked_at,
        last_checked_by,

        processing_options,

        wronggst,
        gstin,
        gstin_part,
        documenttype,
        remark
}
```

## Project Objects

| # | Object | Type | Description |
|---|---|---|---|
| 1 | `ZI_2A_PAY_BLOCK` | Root View Entity | 2A Payment Block |

## Future Objects

Additional objects related to the **Covert RAP Project** will be added to this document as they are provided.
