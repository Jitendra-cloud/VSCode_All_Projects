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
