# base

```cpp
memory_hash.c
641-   {
642-     DB_DATETIMETZ *dt_tz;
643-     dt_tz = db_get_datetimetz (val);
644-     hash = (unsigned int) (dt_tz->datetime.date ^ dt_tz->datetime.time);
645-   }
646-   break;
647- case DB_TYPE_DATE:
648-   {
649-     DB_DATE *date = db_get_date (val);
650-     hash = (unsigned int) (*date);
651-     break;
652-   }
653- case DB_TYPE_MONETARY:
654-   {
655-     DB_MONETARY *mon = db_get_monetary (val);
656-     hash = (unsigned int) mon->amount;
657-     break;
658-   }
659- case DB_TYPE_SET:
660- case DB_TYPE_MULTISET:
661: case DB_TYPE_SEQUENCE:
662-   db_make_null (&t_val);
663-   {
664-     DB_SET *set;
665-     set = db_get_set (val);
666-     if (set_get_element (set, 0, &t_val) == NO_ERROR)
667-       {
668-  hash = mht_valhash (&t_val, ht_size);
669-  (void) pr_clear_value (&t_val);
670-  t_n = set_size (set);
671-  if ((t_n > 0) && set_get_element (set, t_n - 1, &t_val) == NO_ERROR)
672-    {
673-      hash += mht_valhash (&t_val, ht_size);
674-      (void) pr_clear_value (&t_val);
675-    }
676-       }
677-   }
678-   break;
679- case DB_TYPE_OBJECT:
680-   hash = GET_PTR_FOR_HASH (db_get_object (val));
681-   break;
```

```cpp

2416-    /* only the trailing ASCII space is ignored; the hashing for other characters depend on collation */
2417-    if (ptr[i] != 0x20)
2418-      {
2419-        break;
2420-      }
2421-  }
2422-       i++;
2423-     }
2424-   if (!ptr || ptr[0] == 0 || i <= 0)
2425-     {
2426-       hashcode = 0;
2427-     }
2428-   else
2429-     {
2430-       hashcode = MHT2STR_COLL (db_get_string_collation (val), (unsigned char *) ptr, i);
2431-       hashcode %= ht_size;
2432-     }
2433-   break;
2434- case DB_TYPE_SET:
2435- case DB_TYPE_MULTISET:
2436: case DB_TYPE_SEQUENCE:
2437- case DB_TYPE_VOBJ:
2438-   {
2439-     int size = val->data.set->disk_size / 4;
2440-     unsigned int x = 0;
2441-     int i;
2442-
2443-     for (i = 0; i < size; i++)
2444-       {
2445-  x ^= (unsigned int) (val->data.set->disk_set[i * 4]);
2446-       }
2447-
2448-     hashcode = mht_get_shiftmult32 (x, ht_size);
2449-   }
2450-   break;
2451- case DB_TYPE_ENUMERATION:
2452-   hashcode = mht_get_shiftmult32 (val->data.enumeration.short_val, ht_size);
2453-   break;
2454- case DB_TYPE_JSON:
2455-   {
2456-     char *json_body = NULL;

```

```cpp
object_representation_sr.c
1914-  index->type = type;
1915-  index->fk = NULL;
1916-  index->attrs_prefix_length = NULL;
1917-  index->filter_predicate = NULL;
1918-  index->func_index_info = NULL;
1919-  index->index_status = OR_NO_INDEX;
1920-
1921-  /*
1922-   * For each attribute ID in the set,
1923-   *   Extract the attribute ID,
1924-   *   Find the matching attribute and insert the pointer into the array.
1925-   */
1926-
1927-  /* Remember that the attribute IDs start in the second position. */
1928-  e = 1;
1929-
1930-  for (i = 0; i < att_cnt; i++)
1931-    {
1932-      if (set_get_element_nocopy (constraint_seq, e++, &att_val) == NO_ERROR)
1933- {
1934:   if (DB_VALUE_TYPE (&att_val) == DB_TYPE_SEQUENCE)
1935-     {
1936-       break;
1937-     }
1938-
1939-   att_id = db_get_int (&att_val);
1940-   if (IS_DEDUPLICATE_KEY_ATTR_ID (att_id))
1941-     {
1942-       index->atts[index->n_atts] = (OR_ATTRIBUTE *) dk_find_or_deduplicate_key_attribute (att_id);
1943-       (index->n_atts)++;
1944-     }
1945-   else
1946-     {
1947-       for (j = 0, att = rep->attributes; j < rep->n_attributes; j++, att++)
1948-  {
1949-    if (att->id == att_id)
1950-      {
1951-        index->atts[index->n_atts] = att;
1952-        (index->n_atts)++;
1953-        break;
1954-      }
```

```cpp

1962-   index->asc_desc[i] = db_get_int (&att_val);
1963- }
1964-    }
1965-  index->btname = strdup (cons_name);
1966-
1967-  /* Get the index status. */
1968-  set_get_element_nocopy (constraint_seq, seq_size - 2, &stat_val);
1969-  index->index_status = (OR_INDEX_STATUS) (db_get_int (&stat_val));
1970-
1971-  if (type == BTREE_FOREIGN_KEY)
1972-    {
1973-      if (set_get_element_nocopy (constraint_seq, seq_size - 3, &att_val) == NO_ERROR)
1974- {
1975-   or_install_btids_foreign_key (cons_name, db_get_set (&att_val), index);
1976- }
1977-    }
1978-  else if (type == BTREE_PRIMARY_KEY)
1979-    {
1980-      if (set_get_element_nocopy (constraint_seq, seq_size - 3, &att_val) == NO_ERROR)
1981- {
1982:   if (DB_VALUE_TYPE (&att_val) == DB_TYPE_SEQUENCE)
1983-     {
1984-       or_install_btids_foreign_key_ref (db_get_set (&att_val), index);
1985-     }
1986- }
1987-    }
1988-  else
1989-    {
1990-      if (set_get_element_nocopy (constraint_seq, seq_size - 3, &att_val) == NO_ERROR)
1991- {
1992:   if (DB_VALUE_TYPE (&att_val) == DB_TYPE_SEQUENCE)
1993-     {
1994-       DB_SEQ *seq = db_get_set (&att_val);
1995-       DB_VALUE val;
1996-
1997-       if (set_get_element_nocopy (seq, 0, &val) == NO_ERROR)
1998-  {
1999-    if (DB_VALUE_TYPE (&val) == DB_TYPE_INTEGER)
2000-      {
2001-        or_install_btids_prefix_length (db_get_set (&att_val), index, att_cnt);
2002-      }
2003:    else if (DB_VALUE_TYPE (&val) == DB_TYPE_SEQUENCE)
2004-      {
2005-        DB_VALUE avalue;
2006-        DB_SET *child_seq = db_get_set (&val);
2007-        int seq_size = set_size (seq);
2008-        int flag;
2009-
2010-        j = 0;
2011-        while (true)
2012-   {
2013-     flag = 0;
2014-     if (set_get_element_nocopy (child_seq, 0, &avalue) != NO_ERROR)
2015-       {
2016-         goto next_child;
2017-       }
2018-
2019-     if (DB_IS_NULL (&avalue) || DB_VALUE_TYPE (&avalue) != DB_TYPE_STRING)
2020-       {
2021-         goto next_child;
2022-       }
2023-
2024-     if (strcmp (db_get_string (&avalue), SM_FILTER_INDEX_ID) == 0)
2025-       {
2026-         flag = 0x01;
2027-       }
2028-     else if (strcmp (db_get_string (&avalue), SM_FUNCTION_INDEX_ID) == 0)
2029-       {
2030-         flag = 0x02;
2031-       }
2032-     else if (strcmp (db_get_string (&avalue), SM_PREFIX_INDEX_ID) == 0)
2033-       {
2034-         flag = 0x03;
2035-       }
2036-
2037-     if (set_get_element_nocopy (child_seq, 1, &avalue) != NO_ERROR)
2038-       {
2039-         goto next_child;
2040-       }
2041-
2042:     if (DB_VALUE_TYPE (&avalue) != DB_TYPE_SEQUENCE)
2043-       {
2044-         goto next_child;
2045-       }
2046-
2047-     switch (flag)
2048-       {
2049-       case 0x01:
2050-         or_install_btids_filter_pred (db_get_set (&avalue), index);
2051-         break;
2052-
2053-       case 0x02:
2054-         or_install_btids_function_info (db_get_set (&avalue), index);
2055-         break;
2056-
2057-       case 0x03:
2058-         or_install_btids_prefix_length (db_get_set (&avalue), index, att_cnt);
2059-         break;
2060-
2061-       default:
2062-         break;
2063-       }
2064-
2065-   next_child:
2066-     j++;
2067-     if (j >= seq_size)
2068-       {
2069-         break;
2070-       }
2071-
2072-     if (set_get_element_nocopy (seq, j, &val) != NO_ERROR)
2073-       {
2074-         continue;
2075-       }
2076-
2077:     if (DB_VALUE_TYPE (&val) != DB_TYPE_SEQUENCE)
2078-       {
2079-         continue;
2080-       }
2081-
2082-     child_seq = db_get_set (&val);
2083-   }
2084-
2085-        if (index->func_index_info)
2086-   {
2087-     /* function index and prefix length not allowed, yet */
2088-     index->attrs_prefix_length = (int *) malloc (sizeof (int) * att_cnt);
2089-     if (index->attrs_prefix_length == NULL)
2090-       {
2091-         er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, ER_OUT_OF_VIRTUAL_MEMORY, 1,
2092-          sizeof (int) * att_cnt);
2093-         return;
2094-       }
2095-     for (i = 0; i < att_cnt; i++)
2096-       {
2097-         index->attrs_prefix_length[i] = -1;
```

```cpp

2297-    {SM_PROPERTY_UNIQUE, NULL, BTREE_UNIQUE, 0},
2298-    {SM_PROPERTY_REVERSE_UNIQUE, NULL, BTREE_REVERSE_UNIQUE, 0},
2299-    {SM_PROPERTY_INDEX, NULL, BTREE_INDEX, 0},
2300-    {SM_PROPERTY_REVERSE_INDEX, NULL, BTREE_REVERSE_INDEX, 0}
2301-  };
2302-
2303-  DB_VALUE vals[SM_PROPERTY_NUM_INDEX_FAMILY];
2304-  int i;
2305-  int n_btids;
2306-
2307-  /*
2308-   *  The first thing to do is to determine how many unique and index
2309-   *  BTIDs we have.  We need this up front so that we can allocate
2310-   *  the OR_INDEX structure in the class (rep).
2311-   */
2312-  n_btids = 0;
2313-  for (i = 0; i < SM_PROPERTY_NUM_INDEX_FAMILY; i++)
2314-    {
2315-      if (props != NULL && or_cl_get_prop_nocopy (props, property_vars[i].name, &vals[i]))
2316- {
2317:   if (DB_VALUE_TYPE (&vals[i]) == DB_TYPE_SEQUENCE)
2318-     {
2319-       property_vars[i].seq = db_get_set (&vals[i]);
2320-     }
2321-
2322-   if (property_vars[i].seq)
2323-     {
2324-       property_vars[i].length = set_size (property_vars[i].seq);
2325-       n_btids += property_vars[i].length;
2326-     }
2327- }
2328-    }
2329-
2330-  n_btids /= 2;
2331-
2332-  if (n_btids > 0)
2333-    {
2334-      rep->indexes = (OR_INDEX *) malloc (sizeof (OR_INDEX) * n_btids);
2335-      if (rep->indexes == NULL)
2336- {
2337-   er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, ER_OUT_OF_VIRTUAL_MEMORY, 1, sizeof (OR_INDEX) * n_btids);
```

```cpp

2346-    {
2347-      if (property_vars[i].seq)
2348- {
2349-   int j;
2350-   DB_VALUE ids_val, cons_name_val;
2351-   DB_SEQ *ids_seq;
2352-   const char *cons_name = NULL;
2353-   int error = NO_ERROR;
2354-
2355-   for (j = 0; j < property_vars[i].length && error == NO_ERROR; j += 2)
2356-     {
2357-       error = set_get_element_nocopy (property_vars[i].seq, j, &cons_name_val);
2358-       if (error == NO_ERROR)
2359-  {
2360-    cons_name = db_get_string (&cons_name_val);
2361-  }
2362-
2363-       error = set_get_element_nocopy (property_vars[i].seq, j + 1, &ids_val);
2364-       if (error == NO_ERROR && cons_name != NULL)
2365-  {
2366:    if (DB_VALUE_TYPE (&ids_val) == DB_TYPE_SEQUENCE)
2367-      {
2368-        ids_seq = db_get_set (&ids_val);
2369-        or_install_btids_constraint (rep, ids_seq, property_vars[i].type, cons_name);
2370-      }
2371-  }
2372-     }
2373- }
2374-    }
2375-}
2376-
2377-/*
2378- * or_get_current_representation () - build an OR_CLASSREP structure for the
2379- *                                    most recent representation
2380- *   return: disk representation structure
2381- *   record(in): disk record
2382- *   do_indexes(in):
2383- *
2384- * Note: This is similar to the old function orc_diskrep_from_record, but is
2385- *       a little simpler now that we don't need to maintain a separate
2386- *       list for the fixed and variable length attributes.
```

```cpp

2574-
2575-      /* get the default value - constant, this could be using a new DB_VALUE ? */
2576-      if (original_val_len > 0)
2577- {
2578-   if (or_get_default_value (att, original_val_ptr, original_val_len) == 0)
2579-     {
2580-       goto error_cleanup;
2581-     }
2582- }
2583-
2584-      /* get the default expression. */
2585-      classobj_initialize_default_expr (&att->current_default_value.default_expr);
2586-      att->on_update_expr = DB_DEFAULT_NONE;
2587-      if (properties_val_len > 0)
2588- {
2589-   db_make_null (&properties_val);
2590-   db_make_null (&def_expr);
2591-   db_make_null (&def_expr_op);
2592-   db_make_null (&def_expr_format);
2593-
2594:   or_get_value (&buf, &properties_val, tp_domain_resolve_default (DB_TYPE_SEQUENCE), properties_val_len, true);
2595-   att_props = db_get_set (&properties_val);
2596-
2597-   if (att_props != NULL && classobj_get_prop (att_props, "default_expr", &def_expr) > 0)
2598-     {
2599-       /* We have two cases: simple and complex expression. */
2600:       if (DB_VALUE_TYPE (&def_expr) == DB_TYPE_SEQUENCE)
2601-  {
2602-    /*
2603-     * We can't have an attribute with default expression and default value simultaneously. However,
2604-     * in some situations attr->default_value.value contains the value of default expression. This happens
2605-     * when the client executes the query on broker side and use attr->default_value.value to cache
2606-     * the default expression value. Then the broker can modify the schema and send to server the default
2607-     * expression and its cached value. Another option may be to clear default value on broker side,
2608-     * but may lead to inconsistency.
2609-     */
2610-
2611-    /* Currently, we allow only (T_TO_CHAR(int), default_expr(int), default_expr_format(string)) */
2612-    assert (set_size (db_get_set (&def_expr)) == 3);
2613-
2614-    def_expr_set = db_get_set (&def_expr);
2615-
2616-    /* get and cache default expression operator - op of expr */
2617-    if (set_get_element_nocopy (def_expr_set, 0, &def_expr_op) != NO_ERROR)
2618-      {
2619-        assert (false);
2620-        pr_clear_value (&def_expr);
```

```cpp

3539-      error = set_get_element_nocopy (setref, i, &value);
3540-      if (error != NO_ERROR || DB_VALUE_TYPE (&value) != DB_TYPE_STRING)
3541- {
3542-   goto error_exit;
3543- }
3544-
3545-      prop_name = db_get_string (&value);
3546-      if (prop_name == NULL)
3547- {
3548-   goto error_exit;
3549- }
3550-
3551-      if (strcmp (prop_name, SM_PROPERTY_PRIMARY_KEY) != 0 && strcmp (prop_name, SM_PROPERTY_UNIQUE) != 0
3552-   && strcmp (prop_name, SM_PROPERTY_REVERSE_UNIQUE) != 0 && strcmp (prop_name, SM_PROPERTY_INDEX) != 0
3553-   && strcmp (prop_name, SM_PROPERTY_REVERSE_INDEX) != 0 && strcmp (prop_name, SM_PROPERTY_FOREIGN_KEY) != 0)
3554- {
3555-   continue;
3556- }
3557-
3558-      error = set_get_element_nocopy (setref, i + 1, &value);
3559:      if (error != NO_ERROR || DB_VALUE_TYPE (&value) != DB_TYPE_SEQUENCE)
3560- {
3561-   goto error_exit;
3562- }
3563-
3564-      /* this sequence is an alternating pair of constraint name & info sequence, as by: { name, { BTID, [att_name,
3565-       * asc_dsc], {fk_info | pk_info | prefix_length}, filter_predicate, comment}, name, { BTID, [att_name, asc_dsc],
3566-       * {fk_info | pk_info | prefix_length}, filter_predicate, comment}, ... } */
3567-      props = db_get_set (&value);
3568-      len = set_size (props);
3569-      for (j = 0; j < len; j += 2)
3570- {
3571-   /* get the name */
3572-   if (set_get_element_nocopy (props, j, &uvalue) || DB_VALUE_TYPE (&uvalue) != DB_TYPE_STRING)
3573-     {
3574-       goto error_exit;
3575-     }
3576-
3577-   if (strcmp (constraint_name, db_get_string (&uvalue)) != 0)
3578-     {
3579-       continue;
3580-     }
3581-
3582-   found = true;
3583-
3584-   if (set_get_element_nocopy (props, j + 1, &uvalue))
3585-     {
3586-       goto error_exit;
3587-     }
3588:   if (DB_VALUE_TYPE (&uvalue) != DB_TYPE_SEQUENCE)
3589-     {
3590-       goto error_exit;
3591-     }
3592-
3593-   info = db_get_set (&uvalue);
3594-   info_len = set_size (info);
3595-
3596-   if (set_get_element_nocopy (info, info_len - 1, &cvalue) || DB_IS_NULL (&cvalue))
3597-     {
3598-       /* if not exists, set comment to null */
3599-       comment = NULL;
3600-     }
3601-   else if (DB_VALUE_TYPE (&cvalue) == DB_TYPE_STRING)
3602-     {
3603-       /* strdup, caller shall free it */
3604-       const char *cvalue_string = db_get_string (&cvalue);
3605-       comment = strdup (cvalue_string);
3606-     }
3607-   else
3608-     {


```
