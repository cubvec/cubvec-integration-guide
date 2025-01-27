# Parser Directory

```cpp
parse_dbi.c
594-  db_value_clone ((DB_VALUE *) val, &result->info.value.db_value);
595-  result->info.value.db_value_is_initialized = true;
596-  result->info.value.db_value_is_in_workspace = true;
597-
598-  db_type = DB_VALUE_TYPE (val);
599-  result->type_enum = pt_db_to_type_enum (db_type);
600-
601-  switch (db_type)
602-    {
603-    case DB_TYPE_NULL:
604-      result->type_enum = PT_TYPE_NULL;
605-      break;
606-    case DB_TYPE_SET:
607-      result->info.value.data_value.set = pt_set_elements_to_value (parser, val);
608-      pt_add_type_to_set (parser, result->info.value.data_value.set, &result->data_type);
609-      break;
610-    case DB_TYPE_MULTISET:
611-      result->info.value.data_value.set = pt_set_elements_to_value (parser, val);
612-      pt_add_type_to_set (parser, result->info.value.data_value.set, &result->data_type);
613-      break;
614:    case DB_TYPE_SEQUENCE:
615-      result->info.value.data_value.set = pt_set_elements_to_value (parser, val);
616-      pt_add_type_to_set (parser, result->info.value.data_value.set, &result->data_type);
617-      break;
618-
619-    case DB_TYPE_INTEGER:
620-      result->info.value.data_value.i = db_get_int (val);
621-      break;
622-    case DB_TYPE_BIGINT:
623-      result->info.value.data_value.bigint = db_get_bigint (val);
624-      break;
625-    case DB_TYPE_FLOAT:
626-      result->info.value.data_value.f = db_get_float (val);
627-      break;
628-    case DB_TYPE_DOUBLE:
629-      result->info.value.data_value.d = db_get_double (val);
630-      break;
631-    case DB_TYPE_JSON:
632-      result->data_type = parser_new_node (parser, PT_DATA_TYPE);
633-      if (result->data_type == NULL)
634- {

```

```cpp
1585-    case DB_TYPE_TIMESTAMPTZ:
1586-      retval = tp_domain_construct (domain_type, NULL, DB_TIMESTAMPTZ_PRECISION, 0, NULL);
1587-      break;
1588-    case DB_TYPE_DATETIMELTZ:
1589-    case DB_TYPE_DATETIME:
1590-      retval = tp_domain_construct (domain_type, NULL, DB_DATETIME_PRECISION, DB_DATETIME_DECIMAL_SCALE, NULL);
1591-      break;
1592-    case DB_TYPE_DATETIMETZ:
1593-      retval = tp_domain_construct (domain_type, NULL, DB_DATETIMETZ_PRECISION, DB_DATETIME_DECIMAL_SCALE, NULL);
1594-      break;
1595-    case DB_TYPE_BLOB:
1596-    case DB_TYPE_CLOB:
1597-    case DB_TYPE_SUB:
1598-    case DB_TYPE_POINTER:
1599-    case DB_TYPE_ERROR:
1600-    case DB_TYPE_VOBJ:
1601-    case DB_TYPE_OID:
1602-    case DB_TYPE_OBJECT:
1603-    case DB_TYPE_SET:
1604-    case DB_TYPE_MULTISET:
1605:    case DB_TYPE_SEQUENCE:
1606-    case DB_TYPE_MIDXKEY:
1607-    case DB_TYPE_ENUMERATION:
1608-    case DB_TYPE_JSON:
1609-      retval = tp_domain_construct (domain_type, (DB_OBJECT *) 0, 0, 0, (TP_DOMAIN *) 0);
1610-      break;
1611-
1612-    case DB_TYPE_NUMERIC:
1613-      retval = tp_domain_construct (domain_type, NULL, DB_DEFAULT_NUMERIC_PRECISION, DB_DEFAULT_NUMERIC_SCALE, NULL);
1614-      break;
1615-
1616-    case DB_TYPE_CHAR:
1617-    case DB_TYPE_NCHAR:
1618-    case DB_TYPE_BIT:
1619-    case DB_TYPE_VARCHAR:
1620-    case DB_TYPE_VARNCHAR:
1621-    case DB_TYPE_VARBIT:
1622-      /* Note that we assume that some other force is going to come in and repair the precision of the destination of
1623-       * this domain is for the schema manager.  Might be a problem . . . */
1624-      retval = tp_domain_construct (domain_type, NULL, TP_FLOATING_PRECISION_VALUE, 0, NULL);
1625-      break;
```

```cpp
1904-      precision = dt->info.data_type.precision;
1905-      codeset = dt->info.data_type.units;
1906-      collation_id = dt->info.data_type.collation_id;
1907-      assert (collation_id >= 0);
1908-      collation_flag = dt->info.data_type.collation_flag;
1909-      break;
1910-
1911-    case DB_TYPE_BIT:
1912-    case DB_TYPE_VARBIT:
1913-      precision = dt->info.data_type.precision;
1914-      codeset = dt->info.data_type.units;
1915-      break;
1916-
1917-    case DB_TYPE_NUMERIC:
1918-      precision = dt->info.data_type.precision;
1919-      scale = dt->info.data_type.dec_precision;
1920-      break;
1921-
1922-    case DB_TYPE_SET:
1923-    case DB_TYPE_MULTISET:
1924:    case DB_TYPE_SEQUENCE:
1925-    case DB_TYPE_MIDXKEY:
1926-      return pt_node_to_db_domain (parser, dt, class_name);
1927-
1928-    case DB_TYPE_ENUMERATION:
1929-      if (pt_get_enumeration_from_data_type (parser, dt, &enumeration) != NO_ERROR)
1930- {
1931-   return NULL;
1932- }
1933-      codeset = dt->info.data_type.units;
1934-      collation_id = dt->info.data_type.collation_id;
1935-      assert (collation_id >= 0);
1936-      collation_flag = dt->info.data_type.collation_flag;
1937-      break;
1938-
1939-    case DB_TYPE_NULL:
1940-    case DB_TYPE_DB_VALUE:
1941-    case DB_TYPE_VARIABLE:
1942-    case DB_TYPE_TABLE:
1943-    case DB_TYPE_RESULTSET:
1944-      break;



2125-
2126-    case DB_TYPE_VARCHAR:
2127-    case DB_TYPE_CHAR:
2128-    case DB_TYPE_NCHAR:
2129-    case DB_TYPE_VARNCHAR:
2130-    case DB_TYPE_BIT:
2131-    case DB_TYPE_VARBIT:
2132-      precision = dt->info.data_type.precision;
2133-      codeset = dt->info.data_type.units;
2134-      collation_id = dt->info.data_type.collation_id;
2135-      collation_flag = dt->info.data_type.collation_flag;
2136-      break;
2137-
2138-    case DB_TYPE_NUMERIC:
2139-      precision = dt->info.data_type.precision;
2140-      scale = dt->info.data_type.dec_precision;
2141-      break;
2142-
2143-    case DB_TYPE_SET:
2144-    case DB_TYPE_MULTISET:
2145:    case DB_TYPE_SEQUENCE:
2146-      while (dt && (error == NO_ERROR))
2147- {
2148-   domain = pt_data_type_to_db_domain (parser, dt, NULL);
2149-   if (domain)
2150-     {
2151-       error = tp_domain_add (&setdomain, domain);
2152-     }
2153-   dt = dt->next;
2154- }
2155-      if (error == NO_ERROR)
2156- {
2157-   retval = tp_domain_construct (domain_type, (DB_OBJECT *) 0, 0, 0, setdomain);
2158- }
2159-      return retval;
2160-
2161-    case DB_TYPE_ENUMERATION:
2162-      if (pt_get_enumeration_from_data_type (parser, dt, &enumeration) != NO_ERROR)
2163- {
2164-   return NULL;
2165- }

```

```cpp
2242- */
2243-DB_DOMAIN *
2244-pt_node_to_db_domain (PARSER_CONTEXT * parser, PT_NODE * node, const char *class_name)
2245-{
2246-  int error = NO_ERROR, natts = 0;
2247-  DB_DOMAIN *retval = (DB_DOMAIN *) 0;
2248-  DB_DOMAIN *domain = (DB_DOMAIN *) 0;
2249-  DB_DOMAIN *setdomain = (DB_DOMAIN *) 0;
2250-  DB_TYPE domain_type;
2251-  PT_NODE *dt;
2252-
2253-  CAST_POINTER_TO_NODE (node);
2254-
2255-  if (node->data_type)
2256-    {
2257-      domain_type = pt_type_enum_to_db (node->type_enum);
2258-      switch (domain_type)
2259- {
2260- case DB_TYPE_SET:
2261- case DB_TYPE_MULTISET:
2262: case DB_TYPE_SEQUENCE:
2263- case DB_TYPE_MIDXKEY:
2264-   /* Recursively build the setdomain */
2265-   dt = node->data_type;
2266-   while (dt && (error == NO_ERROR))
2267-     {
2268-       domain = pt_data_type_to_db_domain (parser, dt, class_name);
2269-       if (domain)
2270-  {
2271-    if (domain_type == DB_TYPE_MIDXKEY)
2272-      {
2273-        error = tp_domain_attach (&setdomain, domain);
2274-        natts++;
2275-      }
2276-    else
2277-      {
2278-        error = tp_domain_add (&setdomain, domain);
2279-      }
2280-  }
2281-       else
2282-  {



2376-      db_type = DB_TYPE_VARCHAR;
2377-      break;
2378-
2379-    case PT_TYPE_JSON:
2380-      db_type = DB_TYPE_JSON;
2381-      break;
2382-
2383-    case PT_TYPE_OBJECT:
2384-      db_type = DB_TYPE_OBJECT;
2385-      break;
2386-
2387-    case PT_TYPE_SET:
2388-      db_type = DB_TYPE_SET;
2389-      break;
2390-
2391-    case PT_TYPE_MULTISET:
2392-      db_type = DB_TYPE_MULTISET;
2393-      break;
2394-
2395-    case PT_TYPE_SEQUENCE:
2396:      db_type = DB_TYPE_SEQUENCE;
2397-      break;
2398-
2399-    case PT_TYPE_MIDXKEY:
2400-      db_type = DB_TYPE_MIDXKEY;
2401-      break;
2402-
2403-    case PT_TYPE_NUMERIC:
2404-      db_type = DB_TYPE_NUMERIC;
2405-      break;
2406-    case PT_TYPE_NCHAR:
2407-      db_type = DB_TYPE_NCHAR;
2408-      break;
2409-    case PT_TYPE_VARNCHAR:
2410-      db_type = DB_TYPE_VARNCHAR;
2411-      break;
2412-    case PT_TYPE_BIT:
2413-      db_type = DB_TYPE_BIT;
2414-      break;
2415-    case PT_TYPE_VARBIT:
2416-      db_type = DB_TYPE_VARBIT;



2648-      pt_type = PT_TYPE_DATETIMETZ;
2649-      break;
2650-    case DB_TYPE_DATETIMELTZ:
2651-      pt_type = PT_TYPE_DATETIMELTZ;
2652-      break;
2653-
2654-    case DB_TYPE_MONETARY:
2655-      pt_type = PT_TYPE_MONETARY;
2656-      break;
2657-
2658-    case DB_TYPE_OBJECT:
2659-      pt_type = PT_TYPE_OBJECT;
2660-      break;
2661-
2662-    case DB_TYPE_SET:
2663-      pt_type = PT_TYPE_SET;
2664-      break;
2665-    case DB_TYPE_MULTISET:
2666-      pt_type = PT_TYPE_MULTISET;
2667-      break;
2668:    case DB_TYPE_SEQUENCE:
2669-      pt_type = PT_TYPE_SEQUENCE;
2670-      break;
2671-
2672-    case DB_TYPE_CHAR:
2673-      pt_type = PT_TYPE_CHAR;
2674-      break;
2675-    case DB_TYPE_STRING:
2676-      pt_type = PT_TYPE_VARCHAR;
2677-      break;
2678-    case DB_TYPE_NCHAR:
2679-      pt_type = PT_TYPE_NCHAR;
2680-      break;
2681-    case DB_TYPE_VARNCHAR:
2682-      pt_type = PT_TYPE_VARNCHAR;
2683-      break;
2684-    case DB_TYPE_BIT:
2685-      pt_type = PT_TYPE_BIT;
2686-      break;
2687-    case DB_TYPE_VARBIT:
2688-      pt_type = PT_TYPE_VARBIT;



2859-    case DB_TYPE_RESULTSET:
2860-      /*
2861-       * Nothing more to do for these guys; their type is completely
2862-       * described by the type_enum.  Why don't we care about precision
2863-       * and dec_precision for these, if we care about DB_TYPE_INT?
2864-       */
2865-      break;
2866-
2867-    case DB_TYPE_VARIABLE:
2868-    case DB_TYPE_SUB:
2869-    case DB_TYPE_POINTER:
2870-    case DB_TYPE_ERROR:
2871-    case DB_TYPE_OID:
2872-    case DB_TYPE_DB_VALUE:
2873-      PT_INTERNAL_ERROR (parser, "type assignment");
2874-      node = NULL;
2875-      break;
2876-
2877-    case DB_TYPE_SET:
2878-    case DB_TYPE_MULTISET:
2879:    case DB_TYPE_SEQUENCE:
2880-      dt = pt_bind_set_type (parser, node, val, data_type_added);
2881-      break;
2882-
2883-      /*
2884-       * All of the remaining cases need to tack a new DATA_TYPE node
2885-       * onto the incoming node.  Most of the cases allocate it
2886-       * themselves, but not all.
2887-       */
2888-
2889-    case DB_TYPE_MONETARY:
2890-      dt = parser_new_node (parser, PT_DATA_TYPE);
2891-      if (dt)
2892- {
2893-   dt->type_enum = node->type_enum;
2894-   dt->info.data_type.precision = 0;
2895-   dt->info.data_type.dec_precision = 0;
2896-   dt->info.data_type.units = db_value_get_monetary_currency (val);
2897- }
2898-      break;
2899-
```

```cpp
type_checking.c
13915-    PT_ERRORmf2 (parser, o2, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_CANT_COERCE_TO,
13916-          parser_print_tree (parser, o2), pt_show_type_enum (rTyp));
13917-
13918-    return 0;
13919-  }
13920-       else
13921-  {
13922-    pr_clear_value (arg2);
13923-    *arg2 = tmp_val;
13924-  }
13925-     }
13926- }
13927-
13928-      switch (op)
13929- {
13930- case PT_PLUS:
13931-   switch (typ)
13932-     {
13933-     case DB_TYPE_SET:
13934-     case DB_TYPE_MULTISET:
13935:     case DB_TYPE_SEQUENCE:
13936-       if (!pt_union_sets (parser, domain, arg1, arg2, result, o2))
13937-  {
13938-    return 0; /* set union failed */
13939-  }
13940-       break;
13941-
13942-     case DB_TYPE_CHAR:
13943-     case DB_TYPE_NCHAR:
13944-     case DB_TYPE_VARCHAR:
13945-     case DB_TYPE_VARNCHAR:
13946-     case DB_TYPE_BIT:
13947-     case DB_TYPE_VARBIT:
13948-       if (db_string_concatenate (arg1, arg2, result, &truncation) < 0 || truncation != DATA_STATUS_OK)
13949-  {
13950-    PT_ERRORc (parser, o1, er_msg ());
13951-    return 0;
13952-  }
13953-       break;
13954-
13955-     case DB_TYPE_INTEGER:



14978-        {
14979-   PT_ERRORm (parser, o2, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_DATE_UNDERFLOW);
14980-   return 0;
14981-        }
14982-    }
14983-  db_date_decode (&result_date, &month, &day, &year);
14984-  db_make_date (result, month, day, year);
14985-       }
14986-       break;
14987-
14988-     default:
14989-       return 0;
14990-     }
14991-   break;
14992-
14993- case PT_TIMES:
14994-   switch (typ)
14995-     {
14996-     case DB_TYPE_SET:
14997-     case DB_TYPE_MULTISET:
14998:     case DB_TYPE_SEQUENCE:
14999-       if (!pt_product_sets (parser, domain, arg1, arg2, result, o2))
15000-  {
15001-    return 0; /* set union failed */
15002-  }
15003-       break;
15004-
15005-     case DB_TYPE_INTEGER:
15006-       {
15007-  /* NOTE that we need volatile to prevent optimizer from generating division expression as
15008-   * multiplication.
15009-   */
15010-  volatile int i1, i2, itmp;
15011-
15012-  i1 = db_get_int (arg1);
15013-  i2 = db_get_int (arg2);
15014-  itmp = i1 * i2;
15015-  if (OR_CHECK_MULT_OVERFLOW (i1, i2, itmp))
15016-    {
15017-      goto overflow;
15018-    }

```

```cpp
xasl_generation.c
6906- {
6907-   args = pt_to_regu_variable_list (parser, function->info.function.arg_list, UNBOX_AS_TABLE, NULL, NULL);
6908- }
6909-      else
6910- {
6911-   args = pt_to_regu_variable_list (parser, function->info.function.arg_list, UNBOX_AS_VALUE, NULL, NULL);
6912- }
6913-
6914-      switch (function->info.function.function_type)
6915- {
6916- case F_SET:
6917- case F_TABLE_SET:
6918-   result_type = DB_TYPE_SET;
6919-   break;
6920- case F_MULTISET:
6921- case F_TABLE_MULTISET:
6922-   result_type = DB_TYPE_MULTISET;
6923-   break;
6924- case F_SEQUENCE:
6925- case F_TABLE_SEQUENCE:
6926:   result_type = DB_TYPE_SEQUENCE;
6927-   break;
6928- case F_MIDXKEY:
6929-   result_type = DB_TYPE_MIDXKEY;
6930-   break;
6931- case F_VID:
6932-   result_type = DB_TYPE_VOBJ;
6933-   break;
6934- case F_GENERIC:
6935-   result_type = pt_node_to_db_type (function);
6936-   break;
6937- case F_CLASS_OF:
6938-   result_type = DB_TYPE_OID;
6939-   break;
6940- case F_INSERT_SUBSTRING:
6941- case F_ELT:
6942- case F_REGEXP_COUNT:
6943- case F_REGEXP_INSTR:
6944- case F_REGEXP_LIKE:
6945- case F_REGEXP_REPLACE:
6946- case F_REGEXP_SUBSTR:

```

```cpp
10747-
10748-    case PT_NAME:
10749-      if (rhs->info.name.meta_class != PT_PARAMETER)
10750- {
10751-   goto error;
10752- }
10753-      /* FALLTHRU */
10754-
10755-    case PT_VALUE:
10756-      p = (rhs->node_type == PT_NAME) ? pt_find_value_of_label (rhs->info.name.original) : &rhs->info.value.db_value;
10757-
10758-      if (p == NULL)
10759- {
10760-   goto error;
10761- }
10762-
10763-      switch (DB_VALUE_TYPE (p))
10764- {
10765- case DB_TYPE_SET:
10766- case DB_TYPE_MULTISET:
10767: case DB_TYPE_SEQUENCE:
10768-   break;
10769- default:
10770-   goto error;
10771- }
10772-
10773-      db_collectionp = db_get_collection (p);
10774-      n_elem = db_col_size (db_collectionp);
10775-      break;
10776-
10777-    case PT_HOST_VAR:
10778-      p = pt_value_to_db (parser, rhs);
10779-      if (p == NULL)
10780- {
10781-   goto error;
10782- }
10783-
10784-      switch (DB_VALUE_TYPE (p))
10785- {
10786- case DB_TYPE_SET:
10787- case DB_TYPE_MULTISET:
10788: case DB_TYPE_SEQUENCE:
10789-   break;
10790- default:
10791-   goto error;
10792- }
10793-
10794-      db_collectionp = db_get_collection (p);
10795-      n_elem = db_col_size (db_collectionp);
10796-      break;
10797-
10798-    default:
10799-      goto error;
10800-    }
10801-
10802-  if (n_elem <= 0)
10803-    {
10804-      goto error;
10805-    }
10806-
10807-  /* allocate regu variable list and sequence value list */
10808-  regu_array_alloc (&regu_var_list, n_elem);

```

```cpp
parse_tree_cl.c
2593-  int error = NO_ERROR;
2594-  unsigned int save_custom = parser->custom_print;
2595-
2596-  parser_block_allocator alloc (parser);
2597-  string_buffer sb (alloc);
2598-
2599-  db_value_printer printer (sb);
2600-  if (val == NULL)
2601-    {
2602-      return NULL;
2603-    }
2604-
2605-  /* set custom_print here so describe_data() will know to pad bit strings to full bytes */
2606-  parser->custom_print = parser->custom_print | PT_PAD_BYTE;
2607-  switch (DB_VALUE_TYPE (val))
2608-    {
2609-    case DB_TYPE_SET:
2610-    case DB_TYPE_MULTISET:
2611-      sb ("%s", pt_show_type_enum (pt_db_to_type_enum (DB_VALUE_TYPE (val))));
2612-      /* fall thru */
2613:    case DB_TYPE_SEQUENCE:
2614-      sb ("{");
2615-
2616-      size = db_set_size (db_get_set ((DB_VALUE *) val));
2617-      if (size > 0)
2618- {
2619-   error = db_set_get (db_get_set ((DB_VALUE *) val), 0, &element);
2620-   printer.describe_value (&element);
2621-   for (i = 1; i < size; i++)
2622-     {
2623-       error = db_set_get (db_get_set ((DB_VALUE *) val), i, &element);
2624-       sb (", ");
2625-       printer.describe_value (&element);
2626-     }
2627- }
2628-      sb ("}");
2629-      break;
2630-
2631-    case DB_TYPE_OBJECT:
2632-      /* no printable representation!, should not get here */
2633-      sb ("NULL");

```
