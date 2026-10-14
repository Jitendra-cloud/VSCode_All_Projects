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
