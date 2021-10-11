## ES 数据

#### 查询

```json
{
  "from": 0,
  "size": 1000,
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "company_id": {
              "value": 1,
              "boost": 1.0
            }
          }
        },
        {
          "terms": {
            "refno.keyword": [
              "TEST05260010"
            ],
            "boost": 1.0
          }
        }
      ],
      "adjust_pure_negative": true,
      "boost": 1.0
    }
  },
  "sort": [
    {
      "refno.keyword": {
        "order": "asc"
      }
    },
    {
      "manifest_id": {
        "order": "asc"
      }
    }
  ]
}
```

#### 结果

```json
{
  "_index": "tms_manifest",
  "_type": "_doc",
  "_id": "1743955",
  "_source": {
    "manifest_id": "1743955",
    "company_id": "1",
    "with_stat_id": "1199",
    "sd_stat_id": "1",
    "deli_stat_id": "919",
    "cust_id": "731",
    "hub_in_id": "422",
    "hub_out_id": "579",
    "dest_id": "339",
    "order_id": "3061967",
    "oawb_id": "2443",
    "with_station": "虎门分拨",
    "sd_station": "总部",
    "deli_station": "泰国嘉里",
    "cust_no": "TEST11",
    "cust_name": "测试11",
    "hub_in_code": "TESTKECTH002",
    "hub_out_code": "TESTKECZH",
    "dest_code": "TH",
    "dest_name": "泰国",
    "sd_date": 1621997327000,
    "jobno": "TEST05260010",
    "referenceno": "TEST05260010",
    "refno": "TEST05260010",
    "bagging_no": "ZMTESTWC21052600009M",
    "pack_type": "WPX",
    "pcs": "1",
    "qty": "1",
    "order_weig": "2.0",
    "actual": "2.0",
    "vol": "0.0",
    "flip_rule_id": "238",
    "weig": "2.0",
    "charge_volume": "0.0",
    "ispcs": "0",
    "ship_actual": "2.0",
    "ship_vol": "0.0",
    "ship_weig": "2.0",
    "pay_weig": "2.0",
    "descr_name": "QWEA",
    "cc_payment": "PP",
    "cc_charge": "0.0",
    "cctax_payment": "CC",
    "cod_charge": "0.0",
    "cod_charge_cur": "",
    "cocustom_type": "2",
    "goods_type": "01",
    "dec_value": "100.0",
    "dec_value_cur": "CNY",
    "sd_company": "TESTGS",
    "sd_name": "XIAODAI",
    "sd_tel": "1515075441",
    "sd_addr": "GUANGDONGSHENZHEN",
    "sd_country": "CHINA",
    "sd_state": "GD",
    "sd_city": "SZ",
    "re_company": "AA",
    "re_name": "AA",
    "re_tel": "110",
    "re_addr": "AA",
    "re_state": "HN",
    "re_city": "NEW YORK",
    "re_zip": "24000",
    "isfaraway": "0",
    "manifest_mode": "4",
    "manifest_status": "3",
    "isdel": "0",
    "create_user_name": "哲盟用户",
    "create_datetime": 1621997327000,
    "modify_user_name": "zmtest-trp",
    "modify_datetime": 1628505427000,
    "submit_datetime": 1621997327000,
    "inventory_state": "1",
    "sorting_datetime": 1621997327000,
    "inventory_type": "1",
    "inventory_mode": "1",
    "inventory_user_name": "哲盟用户",
    "sort_code": "ZM-TEST(WC)",
    "bagging_state": "1",
    "intimidate_state": "3",
    "shipment_datetime": 1621997919000,
    "oawb_no": "GZ21052703-B1",
    "isbeclose_befor": "0",
    "isbeclose_after": "0",
    "isrr": "0",
    "isrt": "0",
    "batch_no": "350773441138196480",
    "isprint_label": "0",
    "isprint_invoice": "0",
    "isunlock": "0",
    "ismodify": "0",
    "is_handover": "0",
    "shipmentbatch_no": "GGHM210526T0009",
    "tmls_manifest_id": 1743955,
    "stock_stat_id": 1199,
    "count_detent": 0,
    "is_check_lock": 0,
    "bagging_finish_datetime": "2021-05-26T10:53:45+08:00",
    "is_customs_clearance": 0,
    "pick_up": 0,
    "order_create_datetime": "2021-05-26T10:48:25+08:00",
    "tmls_last_scan_status_code": "DA",
    "tmls_last_scan_status_name": "出货",
    "tmls_last_scan_stat_id": 1199,
    "tmls_last_scan_station": "虎门分拨",
    "tmls_last_scan_datetime": 1621997919000,
    "tmls_last_scan_remark": "快件已由【虎门分拨】发出，出货批次号【GGHM210526T0009】",
    "tob_id": 2443,
    "declared_datetime": 1622044800000,
    "flight_take_off_datetime": 1622131200000,
    "flight_arrival_datetime": 1622217600000,
    "cleared_datetime": 1622304000000,
    "mawb_no1": "test11",
    "mawb_no2": "test22",
    "tols_last_scan_status_code": "HL",
    "tols_last_scan_status_name": "交货到末公里",
    "tols_last_scan_station": "地点5",
    "tols_last_scan_datetime": 1622390400000,
    "tols_last_scan_remark": "交货到末公里",
    "tols_handover_Lastmile_datetime": 1622390400000,
    "last_scan_status_code": "HL",
    "last_scan_status_name": "交货到末公里",
    "last_scan_station": "地点5",
    "last_scan_datetime": 1622390400000,
    "last_scan_remark": "交货到末公里",
    "handover_Lastmile_datetime": 1622390400000,
    "bmc_manifest_id": 1743955,
    "income_rule_id": 21,
    "area_01": 0.0,
    "area_02": 0.0,
    "area_03": 0.0,
    "area_04": 0.0,
    "area_05": 0.0,
    "area_06": 0.0,
    "area_07": 0.0,
    "area_08": 0.0,
    "area_09": 0.0,
    "area_10": 0.0,
    "area_31": 0.0,
    "area_32": 0.0,
    "area_33": 0.0,
    "area_34": 0.0,
    "area_35": 0.0,
    "area_36": 0.0,
    "area_37": 0.0,
    "area_38": 0.0,
    "area_39": 0.0,
    "area_40": 0.0,
    "area_61": 0.0,
    "area_62": 0.0,
    "area_63": 0.0,
    "area_64": 0.0,
    "area_65": 0.0,
    "area_70": 0.0,
    "area_71": 0.0,
    "area_72": 0.0,
    "area_73": 0.0,
    "area_74": 0.0,
    "area_75": 0.0,
    "area_80": 0.0,
    "area_81": 0.0,
    "area_82": 0.0,
    "area_83": 0.0,
    "area_84": 0.0,
    "area_85": 0.0,
    "area_86": 0.0,
    "area_87": 0.0,
    "receipt_charge": 0.0,
    "payment_charge": 0.0,
    "receipt_all": 0,
    "payment_all": 0,
    "receipt_confirm": 0,
    "payment_confirm": 0,
    "reimbursement": 0.0,
    "id": "1743955"
  },
  "sort": [
    "TEST05260010",
    1743955
  ]
}
```

