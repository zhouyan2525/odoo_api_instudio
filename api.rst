胤元数据导入方案：

//13.0
配置：
final String url = <insert server URL>,
              db = <insert database name>,
        username = "admin",
        password = <insert password for your admin user (default: admin)>;


登陆：
final XmlRpcClientConfigImpl common_config = new XmlRpcClientConfigImpl();
common_config.setServerURL(
    new URL(String.format("%s/xmlrpc/2/common", url)));
client.execute(common_config, "version", emptyList());

int uid = (int)client.execute(
    common_config, "authenticate", asList(
        db, username, password, emptyMap()));

final XmlRpcClient models = new XmlRpcClient() {{
    setConfig(new XmlRpcClientConfigImpl() {{
        setServerURL(new URL(String.format("%s/xmlrpc/2/object", url)));
    }});
}};

//13.0
远程调用：
models.execute("execute_kw", asList(
    db, uid, password,
    "res.partner", "check_access_rights",
    asList("read"),
    new HashMap() {{ put("raise_exception", false); }}
));

//13.0
生成产品：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "product.template", "create",
    asList(new HashMap() {{
        put("name", 产品名称，必填，数据库varchar类型);
        put("type", "product"(注释：产品类型，非必填，可选项有["product", "consu", "service"], 数据库varchar类型));
        put("categ_id", 1(注释：产品类别，非必填，外键，来源表"product.category"，数据库integer类型));
        put("uom_id", 1(注释：产品单位，非必填，外键，来源表"product.uom"，数据库integer类型));
        put("uom_po_id", 1(注释：产品采购单位，非必填，外键，来源表"product.uom"，数据库integer类型));
        put("sale_line_warn", "no-message"(注释：销售明细警告，非必填，可选项有["no-message", "warning", "block"]，数据库varchar类型));
        put("responsible_id", 1(注释：负责人，非必填，外键，来源表"res.partner"，数据库integer类型));
        put("tracking", "none"(注释：跟踪类别，非必填，可选项有["serial", "lot", "none"]，数据库varchar类型));
        put("purchase_line_warn", "no-message"(注释：采购明细警告，非必填，可选项有["no-message", "warning", "block"]，数据库varchar类型));
        put("list_price", 产品售价，非必填，数据库numeric类型);
        put("sale_ok", True(注释：产品可销售，非必填，数据库boolean类型));
        put("purchase_ok", True(注释：产品可采购，非必填，数据库boolean类型));
        put("company_id", 1(注释：产品所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("active", True(注释：产品激活状态，非必填，数据库boolean类型));
        put("invoice_policy", "order"(注释：销售开票控制策略，非必填，可选项有["order", "delivery"]，数据库varchar类型));
        put("purchase_method", "purchase"(注释：采购开票控制策略，非必填，可选项有["purchase", "receive"]，数据库varchar类型));
        }})
));

//13.0
生成客户和供应商：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "res.partner", "create",
    asList(new HashMap() {{
        put("name", 联系人名称，必填，数据库varchar类型);
        put("invoice_warn", "no-message"(注释：开票警告，非必填，可选项有["no-message", "warning", "block"]，数据库varchar类型));
        put("picking_warn", "no-message"(注释：调拨警告，非必填，可选项有["no-message", "warning", "block"]，数据库varchar类型));
        put("purchase_warn", "no-message"(注释：采购警告，非必填，可选项有["no-message", "warning", "block"]，数据库varchar类型));
        put("sale_warn", "no-message"(注释：销售警告，非必填，可选项有["no-message", "warning", "block"]，数据库varchar类型));
        put("customer_rank", 1(注释：该联系人是客户时填入一个大于0的整形数，非必填，数据库integer类型));
        put("supplier_rank", 1(注释：该联系人是供应商时填入一个大于0的整形数，非必填，数据库integer类型));
        put("lang", "en_US"(注释：语言，非必填，数据库varchar类型));
        put("tz", "Asia/Shanghai"(注释：时区，非必填，数据库varchar类型));
        put("type", "contact"(注释：联系人类型，非必填，可选项有["contact", "invoice", "delivery"，"other", "private"]，数据库varchar类型));
        put("is_company", False(注释：该联系人是公司，非必填，数据库boolean类型));
        put("company_id", 1(注释：联系人所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("active", True(注释：联系人激活状态，非必填，数据库boolean类型));
        }})
));

//13.0
生成采购订单：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "purchase.order", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，非必填，数据库varchar类型));
        put("company_id", 1(注释：所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("currency_id", 8(注释：货币，非必填，外键，来源表"res.currency"，数据库integer类型));
        put("date_order", "2018-12-17 08:30:48"(注释：订单时间，非必填，数据库timestamp without time zone类型);
        put("partner_id", (注释：供应商，取生成供应商后返回的id，必填，外键，来源表"res.partner"，数据库integer类型));
        put("picking_type_id", 1(注释：调拨类型，非必填，外键，来源表"res.partner"，数据库integer类型));
        put("state", "draft"(注释：订单状态，非必填，可选项有["draft", "sent", "to approve"，"purchase", "done"，"cancel"]，数据库varchar类型));
        }})
));

//13.0
生成采购订单明细：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "purchase.order.line", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，必填，数据库varchar类型));
        put("date_planned", "2018-12-17 08:30:48"(注释：订单时间，必填，数据库timestamp without time zone类型);
        put("order_id", 1(注释：采购订单，取生成采购订单后返回的id，必填，外键，来源表"purchase.order"，数据库integer类型));
        put("product_id", 产品，必填，外键，来源表"product.product"，数据库integer类型);
        put("price_unit", 产品单价，必填，数据库numeric类型);
        put("product_qty", 产品数量，必填，数据库numeric类型));
        put("product_uom", 1(注释：产品单位，必填，外键，来源表"product.uom"，数据库integer类型));
        }})
));

//13.0
生成销售订单：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "sale.order", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，非必填，数据库varchar类型));
        put("company_id", 1(注释：所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("date_order", "2018-12-17 08:30:48"(注释：订单时间，非必填，数据库timestamp without time zone类型);
        put("partner_id", (注释：客户，取生成客户后返回的id，必填，外键，来源表"res.partner"，数据库integer类型));
        put("partner_invoice_id", (注释：客户，取生成客户后返回的id，非必填，外键，来源表"res.partner"，数据库integer类型));
        put("partner_shipping_id", (注释：客户，取生成客户后返回的id，非必填，外键，来源表"res.partner"，数据库integer类型));
        put("picking_policy", "direct"(注释：调拨策略，非必填，可选项有["direct", "one"]，数据库varchar类型));
        put("pricelist_id", (注释：价格表，取预先设置的价格表id，非必填，外键，来源表"product.pricelist"，数据库integer类型));
        put("warehouse_id", 1(注释：仓库，非必填，外键，来源表"stock.warehouse"，数据库integer类型));
        put("user_id", 1(注释：销售员，非必填，外键，来源表"res.users"，数据库integer类型));
        put("team_id", 1(注释：销售渠道，非必填，外键，来源表"crm.team"，数据库integer类型));
        put("analytic_account_id", 1(注释：分析账户，非必填，外键，来源表"account.analytic.account"，数据库integer类型));
        put("state", "draft"(注释：订单状态，非必填，可选项有["draft", "sent", "sale", "done", "cancel"]，数据库varchar类型));
        }})
));

//13.0
生成销售订单明细：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "sale.order.line", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，非必填，数据库varchar类型));
        put("customer_lead", 0(客户要求交货天数，非必填，数据库numeric类型));
        put("order_id", 1(注释：销售订单，取生成销售订单后返回的id，必填，外键，来源表"sale.order"，数据库integer类型));
        put("product_id", 产品，取生成产品后返回的id，必填，外键，来源表"product.product"，数据库integer类型);
        put("price_unit", 产品单价，非必填，数据库numeric类型);
        put("product_uom_qty", 产品数量，非必填，数据库numeric类型));
        put("product_uom", 1(注释：产品单位，非必填，外键，来源表"product.uom"，数据库integer类型));
        put("discount", 0(注释：产品折扣，非必填，数据库numeric类型));
        }})
));

生成发票：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "account.invoice", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，非必填，数据库varchar类型));
        put("account_id", 5(科目，必填，外键，来源表"account.account"，数据库integer类型，客户发票为5，供应商发票为41));
        put("partner_id", (注释：客户或供应商，取生成对应人员后返回的id，必填，外键，来源表"res.partner"，数据库integer类型));
        put("company_id", 1(注释：所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("currency_id", 8(注释：货币，非必填，外键，来源表"res.currency"，数据库integer类型));
        put("journal_id", 1(注释：日记账，必填，外键，来源表"account.journal"，数据库integer类型, 客户发票为1，供应商发票为2));
        put("type", "out_invoice"(注释：发票类型，非必填，可选项有["out_invoice", "in_invoice", "out_refund", "in_refund"]，数据库varchar类型));
        put("state", "draft"(注释：发票状态，非必填，可选项有["draft", "open", "paid", "cancel"]，数据库varchar类型));
        put("sent", False(注释：发票已发送，非必填，数据库boolean类型));
        put("user_id", 1(注释：销售员，非必填，外键，来源表"res.users"，数据库integer类型));
        put("team_id", 1(注释：销售团队，非必填，外键，来源表"crm.team"，数据库integer类型));
	put("date_invoice", "2018-12-17"(注释：时间，必填，数据库date类型);
        }})
));

生成发票明细：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "account.invoice.line", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：说明，必填，数据库text类型));
        put("invoice_id", (科目，必填，外键，来源表"account.invoice"，数据库integer类型));
        put("account_id", 62(科目，必填，外键，来源表"account.account"，数据库integer类型，客户发票为62，供应商发票为67));
        put("price_unit", 产品单价，必填，数据库numeric类型);
        put("quantity", 产品数量，非必填，数据库numeric类型));
        put("product_id", 产品，取生成产品后返回的id，非必填，外键，来源表"product.product"，数据库integer类型);
        put("uom_id", 1(注释：产品单位，非必填，外键，来源表"product.uom"，数据库integer类型));
        put("discount", 0(注释：产品折扣，非必填，数据库numeric类型));
        put("sale_line_ids", [(6, 0, [7])](注释：销售订单明细，非必填，6和0两个值不可更改，之后的数组内可以传入多个销售明细id);
        }})
));

确认发票：
models.execute_kw(db, uid, password, 'account.invoice', 'action_invoice_open', [[id]](注释：发票，必填，外键，来源表"account.invoice"，数据库integer类型))

生成银行对账单
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "account.bank.statement", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，必填，数据库varchar类型));
        put("date", "2018-12-17"(注释：时间，必填，数据库date类型);
        put("journal_id", 1(注释：日记账，必填，外键，来源表"account.journal"，数据库integer类型));
        put("state", "open"(注释：发票状态，必填，数据库varchar类型));
        put("balance_start", 期初余额，必填，数据库numeric类型);
        put("balance_end_real", 期末余额，必填，数据库numeric类型);
        put("company_id", 1(注释：所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        }})
));

生成银行对账单明细
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "account.bank.statement.line", "create",
    asList(new HashMap() {{
        put("statement_id", "5"(银行对账单，必填，外键，来源表"account.bank.statement"，数据库integer类型)));
        put("name", "REMIT IN/12181031BKR02544"(注释：备忘，必填，数据库varchar类型));
        put("date", "2018-12-17"(注释：时间，必填，数据库date类型);
        put("amount", 金额，必填，数据库numeric类型);
        }})
));

生成付款信息：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "account.payment", "create",
    asList(new HashMap() {{
        put("invoice_ids", [(4, 7709, 0)](注释：发票，非必填（如果需要将付款与发票绑定则必填），外键，来源表"account.invoice"，4和0两个值不可更改，中间的7709为发票id));
        put("payment_method_id", 1(付款方式，必填，外键，来源表"account.payment.metho"，数据库integer类型));
        put("journal_id", 11(注释：日记账，必填，外键，来源表"account.journal"，数据库integer类型));
        put("amount", 1(金额，必填，数据库numeric类型));
        put("payment_type", 'inbound'(注释：付款类型，必填，可选项有["inbound", "outbound"]，数据库varchar类型));
        put("partner_type", 'customer'(注释：合作伙伴类型，必填，可选项有["customer", "supplier"]，数据库varchar类型));
        put("currency_id", 8(注释：货币，非必填，外键，来源表"res.currency"，数据库integer类型));
        put("payment_date", "2018-12-17"(注释：时间，非必填，数据库date类型);
        put("name", "SUPP.OUT/2019/0006"(注释：名称，非必填，数据库varchar类型));
        put("partner_id", 1(注释：客户或供应商，取生成对应人员后返回的id，非必填，外键，来源表"res.partner"，数据库integer类型));
        put("state", "draft"(注释：发票状态，非必填，可选项有["draft", "posted", "sent", "reconciled", "cancelled"]，数据库varchar类型));
	put("company_id", 1(注释：所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("payment_difference_handling", 'open'(注释：付款差异处理，非必填，可选项有["open", "reconcile"]，数据库varchar类型));
        put("writeoff_account_id", 8(注释：差异科目，如payment_difference_handling为reconcile必填，外键，来源表"account.account"，数据库integer类型));
        put("writeoff_label", "Write-Off"(注释：日记账项目标签，如payment_difference_handling为reconcile必填，数据库varchar类型));
        }})
));

将付款信息与发票绑定：
models.execute_kw(db, uid, password, 'account.payment', 'action_validate_invoice_payment', [[id]](注释：付款信息，必填，外键，来源表"account.payment"，数据库integer类型))

不绑定发票直接确认：
models.execute_kw(db, uid, password, 'account.payment', 'post', [[id]](注释：付款信息，必填，外键，来源表"account.payment"，数据库integer类型))

生成费用：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "hr.expense", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，必填，数据库varchar类型));
	put("employee_id", 1(注释：员工，必填，外键，来源表"hr.employee"，数据库integer类型));
        put("product_id", 用于记账的产品，必填，外键，来源表"product.product"，数据库integer类型);
        put("unit_amount", 产品单价，必填，数据库numeric类型);
        put("product_uom_id", 1(注释：产品单位，必填，外键，来源表"product.uom"，数据库integer类型));
        put("quantity", 产品数量，非必填，数据库numeric类型));
        put("date", "2018-12-17"(注释：时间，非必填，数据库date类型);
        put("company_id", 1(注释：所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("currency_id", 8(注释：货币，非必填，外键，来源表"res.currency"，数据库integer类型));
        put("account_id", 67(科目，非必填，外键，来源表"account.account"，数据库integer类型));
        put("payment_mode", "own_account"(注释：由...支付，非必填，可选项有["own_account", "company_account"]，数据库varchar类型));
        }})
));

生成费用报表：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "hr.expense.sheet", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，必填，数据库varchar类型));
	put("employee_id", 1(注释：员工，必填，外键，来源表"hr.employee"，数据库integer类型));
        put("bank_journal_id", 14(银行日记账，非必填（如果对应hr.expense的payment_mode为company_account则必填），外键，来源表"account.journal"，数据库integer类型);
        put("company_id", 1(注释：所属公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("currency_id", 8(注释：货币，非必填，外键，来源表"res.currency"，数据库integer类型));
        put("expense_line_ids", [(4, 7, 0)](注释：费用明细，必填，外键，来源表"hr.expense"，4和0两个值不可更改，中间的7为费用明细id));
        put("journal_id", 2(费用日记账，必填，外键，来源表"account.journal"，数据库integer类型));
        }})
));

确认费用报表：
models.execute_kw(db, uid, password, 'hr.expense.sheet', 'action_post_sheet', [[id]](注释：费用报表，必填，外键，来源表"hr.expense.sheet"，数据库integer类型))

生成费用对应付款：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "account.payment", "create",
    asList(new HashMap() {{
        put("payment_method_id", 1(付款方式，必填，外键，来源表"account.payment.metho"，数据库integer类型));
        put("journal_id", 11(注释：日记账，必填，外键，来源表"account.journal"，数据库integer类型));
        put("amount", 1(金额，必填，数据库numeric类型));
        put("payment_type", 'outbound'(注释：付款类型，必填，数据库varchar类型));
        put("partner_type", 'supplier'(注释：合作伙伴类型，必填，数据库varchar类型));
        put("partner_id", 1(注释：供应商，取生成对应人员后返回的id，必填，外键，来源表"res.partner"，数据库integer类型));
        put("currency_id", 8(注释：货币，非必填，外键，来源表"res.currency"，数据库integer类型));
        put("payment_date", "2018-12-17"(注释：时间，非必填，数据库date类型);
        put("name", "SUPP.OUT/2019/0006"(注释：名称，非必填，数据库varchar类型));
        }})
));

将付款与费用报表绑定：
models.execute_kw(db, uid, password, 'hr.expense.sheet', 'action_reconcile_payment', [[[sheet_id, payment_id]]](注释：费用报表id和付款id，必填，外键，来源表"hr.expense.sheet"和"account.payment"，数据库integer类型))

修改采购调拨信息流程：
1.发送请求确认采购订单，后台会自动生成调拨单。
2.发送请求返回某天的stock.move信息
3.根据返回的stock.move信息，确认需要修改的内容，将其发送回后台
4.查询调拨信息，确认调拨状态
4.确认所有调拨（包括已修改和不需要修改的调拨），完成整个流程

//13.0
确认采购订单并生成调拨：
asList((Object[])models.execute("execute_kw", asList(
    db, uid, password,
    "purchase.order", "button_confirm",
    asList(ids(注释：采购订单，必填，外键，来源表"purchase.order"，数据库integer类型，可以输入多个，需转为列表后传入))
));

//13.0
根据单据日期查询仓库stock.move信息：
asList((Object[])models.execute("execute_kw", asList(
    db, uid, password,
    "stock.move", "search_read",
    asList(asList(
        asList("create_date", "<", "2019-01-17 00:00:00"),
        asList("create_date", ">=", "2019-01-16 00:00:00")))
));
返回参数中{'id': 3(stock.move的id),
           'product_id': [2002(产品id), 'New'],
           'product_uom_qty': 2.0(计划调拨产品数量),
           'product_uom': [1(产品单位id), 'Unit(s)'],
           'state': 'done'(状态),
           'quantity_done': 2.0(已完成调拨的数量),
           'purchase_line_id': 5(采购明细行id),
           'picking_id': 12(调拨的id),
           }

修改stock.move信息：
models.execute("execute_kw", asList(
    db, uid, password,
    "stock.move", "multi_write",
    asList(
        asList(ids(注释：stock.move的id，必填，数据库integer类型，可以输入多个，需转为列表后传入)),
        new HashMap() {{
        put("product_uom_qty", "5"(产品计划调拨数量，非必填，数据库numeric类型));
        put("quantity_done", "5"(产品完成调拨数量，非必填，数据库numeric类型));
        }}
    )
));

查询调拨信息：
final Map record = (Map)((Object[])models.execute(
    "execute_kw", asList(
        db, uid, password,
        "stock.picking", "read",
        asList(ids(注释：stock.picking，必填，数据库integer类型, 取查询stock.move时的picking_id，可以输入多个，需转为列表后传入))
    )
))[0];

修改调拨信息：
models.execute("execute_kw", asList(
    db, uid, password,
    "stock.picking", "write",
    asList(
        asList(ids(注释：stock.picking的id，必填，数据库integer类型，可以输入多个，需转为列表后传入)),
        new HashMap() {{
		put("force_date_done", "2018-12-17 08:30:48"(注释：强制生效时间，非必填，确认调拨前传入，数据库timestamp without time zone类型);
        }}
    )
));

确认完成调拨，只有状态"state"在['waiting', 'confirmed', 'assigned']中的调拨可以确认完成。
models.execute("execute_kw", asList(
    db, uid, password,
    "stock.picking", "multi_validate",
    asList(ids(注释：stock.picking，必填，数据库integer类型，取查询stock.move时的picking_id，可以输入多个，需转为列表后传入))
));

修改销售调拨流程
1.发送请求确认销售订单，后台会自动生成调拨单。
2.发送请求返回某天的stock.move信息
3.根据返回的stock.move信息，确认需要修改的内容，将其发送回后台
4.查询调拨信息，确认调拨状态
4.确认所有调拨（包括已修改和不需要修改的调拨），完成整个流程
确认销售订单并生成调拨：

asList((Object[])models.execute("execute_kw", asList(
    db, uid, password,
    "sale.order", "action_confirm",
    asList(ids(注释：销售订单，必填，外键，来源表"sale.order"，数据库integer类型，可以输入多个，需转为列表后传入))
));

根据单据日期查询仓库stock.move信息：
asList((Object[])models.execute("execute_kw", asList(
    db, uid, password,
    "stock.move", "search_read",
    asList(asList(
        asList("create_date", "<", "2019-01-17 00:00:00"),
        asList("create_date", ">=", "2019-01-16 00:00:00")))
));
返回参数中{'id': 3(stock.move的id),
           'product_id': [2002(产品id), 'New'],
           'product_uom_qty': 2.0(计划调拨产品数量),
           'product_uom': [1(产品单位id), 'Unit(s)'],
           'state': 'done'(状态),
           'quantity_done': 2.0(已完成调拨的数量),
           'sale_line_id': 5(销售明细行id),
           'picking_id': 12(调拨的id),
           }

修改stock.move信息：
models.execute("execute_kw", asList(
    db, uid, password,
    "stock.move", "multi_write",
    asList(
        asList(ids(注释：stock.move的id，必填，数据库integer类型，可以输入多个，需转为列表后传入)),
        new HashMap() {{
        put("product_uom_qty", "5"(产品计划调拨数量，非必填，数据库numeric类型));
        put("quantity_done", "5"(产品完成调拨数量，非必填，数据库numeric类型));
        }}
    )
));

查询调拨信息：
final Map record = (Map)((Object[])models.execute(
    "execute_kw", asList(
        db, uid, password,
        "stock.picking", "read",
        asList(ids(注释：stock.picking，必填，数据库integer类型, 取查询stock.move时的picking_id，可以输入多个，需转为列表后传入))
    )
))[0];

修改调拨信息：
models.execute("execute_kw", asList(
    db, uid, password,
    "stock.picking", "write",
    asList(
        asList(ids(注释：stock.picking的id，必填，数据库integer类型，可以输入多个，需转为列表后传入)),
        new HashMap() {{
		put("force_date_done", "2018-12-17 08:30:48"(注释：强制生效时间，非必填，确认调拨前传入，数据库timestamp without time zone类型);
        }}
    )
));

确认完成调拨，只有状态"state"在['waiting', 'confirmed', 'assigned']中的调拨可以确认完成。
models.execute("execute_kw", asList(
    db, uid, password,
    "stock.picking", "multi_validate",
    asList(ids(注释：stock.picking，必填，数据库integer类型，取查询stock.move时的picking_id，可以输入多个，需转为列表后传入))
));

生成线索：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "crm.lead", "create",
    asList(new HashMap() {{
        put("name", 'subject'(注释：主题，必填，数据库varchar类型));
        put("contact_name", "Mike"(注释：联系人名称，非必填，数据库varchar类型));
	put("phone", "13910002000"(注释：电话，非必填，数据库varchar类型));
        put("email_from", "email@example.com"(注释：非邮箱，非必填，数据库varchar类型);
        put("partner_name", 'example co.ltd'(注释：公司名称，非必填，数据库varchar类型);
        put("description", 'Your question'(注释：问题，非必填，数据库numeric类型));
        put("user_id", 1(注释：销售员，非必填，外键，来源表"res.users"，数据库integer类型));
        put("team_id", 1(注释：销售渠道，非必填，外键，来源表"crm.team"，数据库integer类型));
        }})
));

生成销售渠道：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "crm.team", "create",
    asList(new HashMap() {{
        put("name", 'sales team'(注释：渠道名称，必填，数据库varchar类型));
        put("team_type", "sales"(注释：团队类型，必填，可选项有["sales", "website"]，数据库varchar类型));
        put("user_id", 1(注释：团队负责人，非必填，外键，来源表"res.users"，数据库integer类型));
        put("member_ids", [(6, 0, ids)](注释：团队成员，非必填，外键，来源表"res.users"，6和0两个值不可更改，ids可以接受多个值，需要打包成数组后传入));
        put("platform_id", 1(注释：销售平台，非必填，外键，来源表"sale.platform"，数据库integer类型));
        }})
));

生成销售平台：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "sale.platform", "create",
    asList(new HashMap() {{
        put("name", 'sales team'(注释：平台名称，必填，数据库varchar类型));
        put("code", "0001"(注释：平台编码，非必填，数据库varchar类型));
        }})
));

//13.0
生成库存期初：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "stock.inventory", "create",
    asList(new HashMap() {{
        put("name", "New"(注释：名称，必填，数据库varchar类型));
	put("company_id", 1(注释：所属公司，必填，外键，来源表"res.company"，数据库integer类型));
	put("date", "2018-12-17 08:30:48"(注释：盘点时间，必填，数据库timestamp without time zone类型);
        put("location_ids", [ 6, 0, [ 5 ] ](盘点位置，必填，外键，来源表"stock.location"，数据库integer类型);
	put("state", "confirm"(注释：盘点状态，必填，可选项有["draft", "cancel", "confirm", "done"]，数据库varchar类型));
	}})
));

//13.0
生成库存期初明细：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "stock.inventory.line", "create",
    asList(new HashMap() {{
        put("inventory_id", (库存期初，必填，外键，来源表"stock.inventory"，数据库integer类型));
        put("location_id", 5(盘点位置，必填，外键，来源表"stock.location"，数据库integer类型，需要和库存期初一致);
	put("product_id", 产品，取生成产品后返回的id，必填，外键，来源表"product.product"，数据库integer类型，只能选取type是product的产品);
	put("product_uom_id", 1(注释：产品单位，非必填，外键，来源表"product.uom"，数据库integer类型));
	put("product_qty", 产品数量，必填，数据库numeric类型));
        }})
));

确认库存期初：
models.execute_kw(db, uid, password, 'stock.inventory', 'action_remote_validate', [[id]](注释：库存期初，必填，外键，来源表"stock.inventory"，数据库integer类型))

//13.0
修改产品成本：
models.execute("execute_kw", asList(
    db, uid, password,
    "product.template", "write",
    asList(
        asList(ids(注释：product.template的id，必填，数据库integer类型，可以输入多个，需转为列表后传入)),
        new HashMap() {{
        put("standard_price", "200"(产品成本，非必填，数据库numeric类型));
        put("categ_id", 1(产品类别，外键，来源表"product.category"，数据库integer类型，));
        }}
    )
));

生成到岸成本：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "stock.landed.cost", "create",
    asList(new HashMap() {{
	put("account_journal_id", 1(注释：日记账，必填，外键，来源表"account.journal"，数据库integer类型));
	put("date", "2018-12-17"(注释：盘点时间，必填，数据库date类型);
        put("picking_ids", [(6, 0, [ids])](注释：调拨单，必填，外键，来源表"stock.picking"，6和0两个值不可更改，ids可以接受多个值，需要打包成数组后传入));
        put("name", "New"(注释：名称，非必填，数据库varchar类型));
	}})
));

生成到岸成本明细：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "stock.landed.cost.lines", "create",
    asList(new HashMap() {{
        put("cost_id", (到岸成本，必填，外键，来源表"stock.landed.cost"，数据库integer类型));
	put("product_id", 1(产品，取生成产品后返回的id，必填，外键，来源表"product.product"，数据库integer类型，只能选取landed_cost_ok是True的产品);
        put("price_unit", 100(产品单价，必填，数据库numeric类型);
	put("split_method", "equal"(注释：盘点状态，必填，可选项有["equal", "by_quantity", "by_current_cost_price", "by_weight", "by_volume"]，数据库varchar类型));
        put("account_id", 67(科目，必填，外键，来源表"account.account"，数据库integer类型));
	put("name", "New"(注释：名称，非必填，数据库varchar类型));
        }})
));

计算到岸成本分摊：
models.execute_kw(db, uid, password, 'stock.landed.cost', 'compute_landed_cost', [[id]](到岸成本，必填，外键，来源表"stock.landed.cost"，数据库integer类型))

确认到岸成本：
models.execute_kw(db, uid, password, 'stock.landed.cost', 'button_validate', [[id]](到岸成本，必填，外键，来源表"stock.landed.cost"，数据库integer类型))

修改stock.picking信息：
models.execute("execute_kw", asList(
    db, uid, password,
    "stock.picking", "write",
    asList(
        asList(ids(注释：stock.picking的id，必填，数据库integer类型，可以输入多个，需转为列表后传入)),
        new HashMap() {{
		put("date_done", "2018-12-17 08:30:48"(注释：生效时间，必填，数据库timestamp without time zone类型);
        }}
    )
));

从调拨生成发票流程
1.生成调拨单
2.确认调拨单
3.修改调拨时间
4.根据调拨时间生成发票

从调拨生成发票：
models.execute_kw(db, uid, password, 'stock.picking', 'action_post_create_invoice', [[id]](调拨，必填，外键，来源表"stock.picking"，数据库integer类型))
查询采购订单对应的发票：
models.execute_kw(db, uid, password, 'purchase.order', 'read', [[id], ['invoice_ids']](id是采购订单，必填，外键，来源表"purchase.order"，数据库integer类型))
查询销售订单对应的发票：
models.execute_kw(db, uid, password, 'sale.order', 'read', [[id], ['invoice_ids']](id是销售订单，必填，外键，来源表"sale.order"，数据库integer类型))


从采购订单生成发票：
models.execute_kw(db, uid, password, 'purchase.order', 'action_post_create_invoice', [[id]，{'date_invoice': "2018-12-17"}](采购单，必填，外键，来源表"purchase.order"，数据库integer类型))

从调拨退货：（原始调拨需要完成）
models.execute_kw(db, uid, password, 'stock.picking', 'action_post_create_return_picking', [[id]](调拨，必填，外键，来源表"stock.picking"，数据库integer类型))

生成分析账户：
final Integer id = (Integer)models.execute("execute_kw", asList(
    db, uid, password,
    "account.analytic.account", "create",
    asList(new HashMap() {{
        put("name", 'account'(注释：名称，必填，数据库varchar类型));
        put("code", "fifo"(注释：参考，非必填，数据库varchar类型));
        put("partner_id", 1(注释：客户，非必填，外键，来源表"res.partner"，数据库integer类型));
        put("group_id", 1(注释：组，非必填，外键，来源表"account.analytic.group"，数据库integer类型));
        put("company_id", 1(注释：公司，非必填，外键，来源表"res.company"，数据库integer类型));
        put("currency_id", 1(注释：币种，非必填，外键，来源表"res.currency"，数据库integer类型));
        }})
));

查询产品库存
models.execute_kw(db, uid, password, 'product.product', 'get_product_quantity', [[id]，{'location_ids': [location_id]}](id是调拨，必填，外键，来源表"stock.picking"，数据库integer类型，location_id是库位，非必填，外键，来源表'stock.location', 数据库integer类型，可以传入多))
