# Storage Directory

```cpp
statistics_sr.c
882- case DB_TYPE_STRING:
883-   fprintf (fpp, "DB_TYPE_STRING \n");
884-   break;
885-
886- case DB_TYPE_OBJECT:
887-   fprintf (fpp, "DB_TYPE_OBJECT \n");
888-   break;
889-
890- case DB_TYPE_SET:
891-   fprintf (fpp, "DB_TYPE_SET \n");
892-   break;
893-
894- case DB_TYPE_JSON:
895-   fprintf (fpp, "DB_TYPE_JSON \n");
896-   break;
897-
898- case DB_TYPE_MULTISET:
899-   fprintf (fpp, "DB_TYPE_MULTISET \n");
900-   break;
901-
902: case DB_TYPE_SEQUENCE:
903:   fprintf (fpp, "DB_TYPE_SEQUENCE \n");
904-   break;
905-
906- case DB_TYPE_TIME:
907-   fprintf (fpp, "DB_TYPE_TIME \n");
908-   break;
909-
910- case DB_TYPE_TIMESTAMP:
911-   fprintf (fpp, "DB_TYPE_TIMESTAMP \n");
912-   break;
913-
914- case DB_TYPE_TIMESTAMPLTZ:
915-   fprintf (fpp, "DB_TYPE_TIMESTAMPLTZ \n");
916-   break;
917-
918- case DB_TYPE_TIMESTAMPTZ:
919-   fprintf (fpp, "DB_TYPE_TIMESTAMPTZ \n");
920-   break;
921-
922- case DB_TYPE_DATETIME:
923-   fprintf (fpp, "DB_TYPE_DATETIME \n");

```

```cpp
compactdb_sr.c
119-     return_value = 1;
120-     break;
121-   }
122-
123- if (is_class (ref_oid, &ref_class_oid))
124-   {
125-     break;
126-   }
127-
128-#if defined(CUBRID_DEBUG)
129- printf (msgcat_message (MSGCAT_CATALOG_UTILS, MSGCAT_UTIL_SET_COMPACTDB, COMPACTDB_MSG_REFOID), ref_oid->volid,
130-  ref_oid->pageid, ref_oid->slotid, ref_class_oid.volid, ref_class_oid.pageid, ref_class_oid.slotid);
131-#endif
132-
133- break;
134-      }
135-
136-    case DB_TYPE_POINTER:
137-    case DB_TYPE_SET:
138-    case DB_TYPE_MULTISET:
139:    case DB_TYPE_SEQUENCE:
140-      {
141- return_value = process_set (thread_p, db_get_set (value));
142- break;
143-      }
144-
145-    default:
146-      break;
147-    }
148-
149-  return return_value;
150-}
151-
152-/*
153- * process_set - process one set
154- *    return: whether the object should be updated.
155- *    set(in): the set to process
156- */
157-static int
158-process_set (THREAD_ENTRY * thread_p, DB_SET * set)
159-{

```

```cpp
system_catalog.c
4781-      fprintf (stdout, "DB_TYPE_BIGINT \n");
4782-      break;
4783-    case DB_TYPE_FLOAT:
4784-      fprintf (stdout, "DB_TYPE_FLOAT \n");
4785-      break;
4786-    case DB_TYPE_DOUBLE:
4787-      fprintf (stdout, "DB_TYPE_DOUBLE \n");
4788-      break;
4789-    case DB_TYPE_STRING:
4790-      fprintf (stdout, "DB_TYPE_STRING \n");
4791-      break;
4792-    case DB_TYPE_OBJECT:
4793-      fprintf (stdout, "DB_TYPE_OBJECT \n");
4794-      break;
4795-    case DB_TYPE_SET:
4796-      fprintf (stdout, "DB_TYPE_SET \n");
4797-      break;
4798-    case DB_TYPE_MULTISET:
4799-      fprintf (stdout, "DB_TYPE_MULTISET \n");
4800-      break;
4801:    case DB_TYPE_SEQUENCE:
4802:      fprintf (stdout, "DB_TYPE_SEQUENCE \n");
4803-      break;
4804-    case DB_TYPE_TIME:
4805-      fprintf (stdout, "DB_TYPE_TIME \n");
4806-      break;
4807-    case DB_TYPE_MONETARY:
4808-      fprintf (stdout, "DB_TYPE_MONETARY \n");
4809-      break;
4810-    case DB_TYPE_DATE:
4811-      fprintf (stdout, "DB_TYPE_DATE \n");
4812-      break;
4813-    case DB_TYPE_BLOB:
4814-      fprintf (stdout, "DB_TYPE_BLOB \n");
4815-      break;
4816-    case DB_TYPE_CLOB:
4817-      fprintf (stdout, "DB_TYPE_CLOB \n");
4818-      break;
4819-    case DB_TYPE_VARIABLE:
4820-      fprintf (stdout, "DB_TYPE_VARIABLE \n");
4821-      break;
4822-    case DB_TYPE_SUB:

```

```cpp
catalog_class.c
1375-   || db_get_enum_short (attr_val_p) > or_val->value.data.set->set->size)
1376- {
1377-   error = ER_FAILED;
1378-   goto error;
1379- }
1380-      error = set_get_element (db_get_set (&or_val->value), db_get_enum_short (attr_val_p) - 1, &val);
1381-      if (error != NO_ERROR)
1382- {
1383-   goto error;
1384- }
1385-
1386-      val.need_clear = false;
1387-      db_make_enumeration (attr_val_p, db_get_enum_short (attr_val_p), db_get_string (&val), db_get_string_size (&val),
1388-      db_get_enum_codeset (attr_val_p), db_get_enum_collation (attr_val_p));
1389-      attr_val_p->need_clear = true;
1390-    }
1391-  /* triggers - advance only */
1392-  or_advance (buf_p, vars[ORC_ATT_TRIGGER_INDEX].length);
1393-
1394-  /* properties */
1395:  or_get_value (buf_p, &val, tp_domain_resolve_default (DB_TYPE_SEQUENCE), vars[ORC_ATT_PROPERTIES_INDEX].length, true);
1396-  att_props = db_get_set (&val);
1397-  attr_val_p = &attrs[8].value;
1398-  db_make_null (&default_expr);
1399-  if (att_props != NULL)
1400-    {
1401-      size_t default_value_len = 0;
1402-      const char *default_str_val = NULL;
1403-
1404-      if (classobj_get_prop (att_props, "default_expr", &default_expr) > 0)
1405- {
1406-   size_t len;
1407-
1408:   if (DB_VALUE_TYPE (&default_expr) == DB_TYPE_SEQUENCE)
1409-     {
1410-       assert (set_size (db_get_set (&default_expr)) == 3);
1411-       def_expr_seq = db_get_set (&default_expr);
1412-
1413-       error = set_get_element_nocopy (def_expr_seq, 0, &db_value_default_expr_op);
1414-       if (error != NO_ERROR)
1415-  {
1416-    goto error;
1417-  }
1418-       assert (DB_VALUE_TYPE (&db_value_default_expr_op) == DB_TYPE_INTEGER
1419-        && db_get_int (&db_value_default_expr_op) == (int) T_TO_CHAR);
1420-       with_to_char = true;
1421-
1422-       error = set_get_element_nocopy (def_expr_seq, 1, &db_value_default_expr_type);
1423-       if (error != NO_ERROR)
1424-  {
1425-    goto error;
1426-  }
1427-       default_expr_type = (DB_DEFAULT_EXPR_TYPE) db_get_int (&db_value_default_expr_type);
1428-



1739-       goto error;
1740-     }
1741- }
1742-
1743-      if (DB_IS_NULL (attr_val_p))
1744- {
1745-   /* if self reference for example, "class x (a x)" set an invalid data type, and fill its value later */
1746-   error = db_value_domain_init (attr_val_p, DB_TYPE_VARIABLE, DB_DEFAULT_PRECISION, DB_DEFAULT_SCALE);
1747-   if (error != NO_ERROR)
1748-     {
1749-       goto error;
1750-     }
1751- }
1752-    }
1753-
1754-  /* enumeration */
1755-  if (vars[ORC_DOMAIN_ENUMERATION_INDEX].length)
1756-    {
1757-      /* enumerations are stored as a collection of strings */
1758-      TP_DOMAIN *string_dom = tp_domain_resolve_default (DB_TYPE_STRING);
1759:      PR_TYPE *seq_type = pr_type_from_id (DB_TYPE_SEQUENCE);
1760-
1761:      TP_DOMAIN *domain = tp_domain_construct (DB_TYPE_SEQUENCE, NULL, 0, 0, string_dom);
1762-      if (domain == NULL)
1763- {
1764-   error = ER_FAILED;
1765-   goto error;
1766- }
1767-      domain = tp_domain_cache (domain);
1768-
1769-      seq_type->data_readval (buf_p, &attrs[7].value, domain, -1, true, NULL, 0);
1770-    }
1771-
1772-  /* set_domain */
1773-  error =
1774-    catcls_get_subset (thread_p, buf_p, vars[ORC_DOMAIN_SETDOMAIN_INDEX].length, &attrs[8],
1775-         catcls_get_or_value_from_domain);
1776-
1777-  if (vars[ORC_DOMAIN_SCHEMA_JSON_OFFSET].length > 0)
1778-    {
1779-      char *schema_str;
1780-      error = or_get_json_schema (buf_p, schema_str);
1781-      if (error != NO_ERROR)



2286-      attrs = values[j].sub.value;
2287-
2288-      /* index_name */
2289-      attr_val_p = &attrs[1].value;
2290-      error = set_get_element (seq_p, i, attr_val_p);
2291-      if (error != NO_ERROR)
2292- {
2293-   goto error;
2294- }
2295-      db_string_truncate (attr_val_p, DB_MAX_IDENTIFIER_LENGTH);
2296-
2297-      /* (is_unique) */
2298-      db_make_int (&attrs[2].value, is_unique);
2299-
2300-      error = set_get_element (seq_p, i + 1, &keys);
2301-      if (error != NO_ERROR)
2302- {
2303-   goto error;
2304- }
2305-
2306:      if (DB_VALUE_TYPE (&keys) == DB_TYPE_SEQUENCE)
2307- {
2308-   key_seq_p = db_get_set (&keys);
2309- }
2310-      else
2311- {
2312-   assert (0);
2313-   error = ER_SM_INVALID_PROPERTY;
2314-   er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, error, 0);
2315-   goto error;
2316- }
2317-
2318-      /* the sequence of keys also includes the B+tree ID and the filter predicate expression in the first two
2319-       * positions (0 and 1) */
2320-      key_size = set_size (key_seq_p);
2321-      att_cnt = (key_size - 3) / 2;
2322-
2323-      /* Get status. */
2324-      error = set_get_element (key_seq_p, key_size - 2, &attrs[11].value);
2325-
2326-      /* comment */
2327-      error = set_get_element (key_seq_p, key_size - 1, &attrs[10].value);
2328-      if (error != NO_ERROR)
2329- {
2330-   goto error;
2331- }
2332-      db_string_truncate (&attrs[10].value, DB_MAX_COMMENT_LENGTH);
2333-
2334-      if (!is_primary_key && !is_foreign_key)
2335- {
2336-   /* prefix_length or filter index */
2337-   error = set_get_element (key_seq_p, key_size - 3, &svalue);
2338-   if (error != NO_ERROR)
2339-     {
2340-       goto error;
2341-     }
2342-
2343:   if (DB_VALUE_TYPE (&svalue) != DB_TYPE_SEQUENCE)
2344-     {
2345-       error = ER_SM_INVALID_PROPERTY;
2346-       er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, error, 0);
2347-       goto error;
2348-     }
2349-
2350-   seq = db_get_set (&svalue);
2351-   error = set_get_element (seq, 0, &val);
2352-   if (error != NO_ERROR)
2353-     {
2354-       goto error;
2355-     }
2356-
2357-   if (DB_VALUE_TYPE (&val) == DB_TYPE_INTEGER)
2358-     {
2359-       /* have prefix length */
2360-       prefix_seq = seq;
2361-     }
2362:   else if (DB_VALUE_TYPE (&val) == DB_TYPE_SEQUENCE)
2363-     {
2364-       DB_SET *child_seq = db_get_set (&val);
2365-       int seq_size = set_size (seq);
2366-       int flag, l = 0;
2367-       DB_VALUE temp;
2368-       int col_id, att_index_start;
2369-       char *buffer, *ptr;
2370-       TP_DOMAIN *fi_domain = NULL;
2371-
2372-       /* have filter or function index */
2373-       while (true)
2374-  {
2375-    flag = 0;
2376-    error = set_get_element (child_seq, 0, &avalue);
2377-    if (error != NO_ERROR)
2378-      {
2379-        goto error;
2380-      }
2381-
2382-    if (DB_IS_NULL (&avalue) || DB_VALUE_TYPE (&avalue) != DB_TYPE_STRING)



2390-      {
2391-        flag = 0x01;
2392-      }
2393-    else if (!intl_identifier_casecmp (db_get_string (&avalue), SM_FUNCTION_INDEX_ID))
2394-      {
2395-        flag = 0x02;
2396-      }
2397-    else if (!intl_identifier_casecmp (db_get_string (&avalue), SM_PREFIX_INDEX_ID))
2398-      {
2399-        flag = 0x03;
2400-      }
2401-
2402-    pr_clear_value (&avalue);
2403-
2404-    error = set_get_element (child_seq, 1, &avalue);
2405-    if (error != NO_ERROR)
2406-      {
2407-        goto error;
2408-      }
2409-
2410:    if (DB_VALUE_TYPE (&avalue) != DB_TYPE_SEQUENCE)
2411-      {
2412-        error = ER_SM_INVALID_PROPERTY;
2413-        er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, error, 0);
2414-        goto error;
2415-      }
2416-
2417-    switch (flag)
2418-      {
2419-      case 0x01:
2420-        pred_seq = db_get_set (&avalue);
2421-        attr_val_p = &attrs[8].value;
2422-        error = set_get_element (pred_seq, 0, attr_val_p);
2423-        if (error != NO_ERROR)
2424-   {
2425-     goto error;
2426-   }
2427-        break;
2428-
2429-      case 0x02:
2430-        has_function_index = 1;



2540-        break;
2541-
2542-      default:
2543-        break;
2544-      }
2545-
2546-    pr_clear_value (&avalue);
2547-
2548-    l++;
2549-    if (l >= seq_size)
2550-      {
2551-        break;
2552-      }
2553-
2554-    pr_clear_value (&val);
2555-    if (set_get_element (seq, l, &val) != NO_ERROR)
2556-      {
2557-        error = ER_FAILED;
2558-        goto error;
2559-      }
2560:    if (DB_VALUE_TYPE (&val) != DB_TYPE_SEQUENCE)
2561-      {
2562-        error = ER_FAILED;
2563-        goto error;
2564-      }
2565-
2566-    child_seq = db_get_set (&val);
2567-  }
2568-     }
2569-   else
2570-     {
2571-       error = ER_SM_INVALID_PROPERTY;
2572-       er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, error, 0);
2573-       goto error;
2574-     }
2575-   pr_clear_value (&val);
2576- }
2577-
2578-      /* key_count */
2579-      if (has_function_index == 0)
2580- {



2814-  if (expected_size == 0)
2815-    {
2816-      value_p->sub.count = 0;
2817-      return NO_ERROR;
2818-    }
2819-
2820-  db_value_put_null (&prop_val);
2821-  for (i = 0; i < SM_PROPERTY_NUM_INDEX_FAMILY; i++)
2822-    {
2823-      db_value_put_null (&vals[i]);
2824-    }
2825-
2826-  tp_Sequence.data_readval (buf_p, &prop_val, NULL, expected_size, true, NULL, 0);
2827-  prop_seq_p = db_get_set (&prop_val);
2828-
2829-  for (i = 0; i < SM_PROPERTY_NUM_INDEX_FAMILY; i++)
2830-    {
2831-      if (prop_seq_p != NULL && (classobj_get_prop (prop_seq_p, property_vars[i].name, &vals[i]) > 0))
2832- {
2833-
2834:   if (DB_VALUE_TYPE (&vals[i]) == DB_TYPE_SEQUENCE)
2835-     {
2836-       property_vars[i].seq = db_get_set (&vals[i]);
2837-     }
2838-
2839-   if (property_vars[i].seq != NULL)
2840-     {
2841-       property_vars[i].size = set_size (property_vars[i].seq) / 2;
2842-       n_size += property_vars[i].size;
2843-     }
2844- }
2845-    }
2846-
2847-  if (n_size > 0)
2848-    {
2849-      subset_p = catcls_allocate_or_value (n_size);
2850-      if (subset_p == NULL)
2851- {
2852-   error = ER_OUT_OF_VIRTUAL_MEMORY;
2853-   goto error;
2854- }

```

```cpp
heap_file.c
10201-      else
10202- {
10203-   /*
10204-    * A variable attribute
10205-    */
10206-   if (!OR_VAR_IS_NULL (recdes->data, value->read_attrepr->location))
10207-     {
10208-       /*
10209-        * The variable attribute is bound.
10210-        * Find its location through the variable offset attribute table.
10211-        */
10212-       disk_data = ((char *) recdes->data + OR_VAR_OFFSET (recdes->data, value->read_attrepr->location));
10213-
10214-       disk_bound = true;
10215-       switch (TP_DOMAIN_TYPE (attrepr->domain))
10216-  {
10217-  case DB_TYPE_BLOB:
10218-  case DB_TYPE_CLOB:
10219-  case DB_TYPE_SET: /* it may be just a little bit fast */
10220-  case DB_TYPE_MULTISET:
10221:  case DB_TYPE_SEQUENCE:
10222-    OR_VAR_LENGTH (disk_length, recdes->data, value->read_attrepr->location,
10223-     attr_info->read_classrepr->n_variable);
10224-    break;
10225-  default:
10226-    disk_length = -1; /* remains can read without disk_length */
10227-  }
10228-     }
10229- }
10230-    }
10231-
10232-  /*
10233-   * From now on, I should only use attrepr.. it will point to either
10234-   * a current value or a default one
10235-   */
10236-
10237-  /*
10238-   * Clear/decache any old value
10239-   */
10240-  if (value->state != HEAP_UNINIT_ATTRVALUE)
10241-    {
```
