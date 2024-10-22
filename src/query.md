# `src/query` directory

```cpp
cursor.c
195-    {
196-      return ER_FAILED;
197-    }
198-
199-  set = db_get_set (value_p);
200-  size = db_set_size (set);
201-
202-  if (cursor_has_set_vobjs (set) == false)
203-    {
204-      return set_convert_oids_to_objects (set);
205-    }
206-
207-  switch (type)
208-    {
209-    case DB_TYPE_SET:
210-      new_set = db_set_create_basic (NULL, NULL);
211-      break;
212-    case DB_TYPE_MULTISET:
213-      new_set = db_set_create_multi (NULL, NULL);
214-      break;
215:    case DB_TYPE_SEQUENCE:
216-      new_set = db_seq_create (NULL, NULL, size);
217-      break;
218-    default:
219-      return ER_FAILED;
220-    }
221-
222-  /* fixup element vobjs into vmops and add them to new */
223-  for (i = 0; i < size; i++)
224-    {
225-      if (db_set_get (set, i, &element) != NO_ERROR)
226- {
227-   db_set_free (new_set);
228-   return ER_FAILED;
229- }
230-
231-      if (cursor_fixup_vobjs (&element) != NO_ERROR)
232- {
233-   db_set_free (new_set);
234-   return ER_FAILED;
235- }
236-
237:      if (type == DB_TYPE_SEQUENCE)
238- {
239-   rc = db_seq_put (new_set, i, &element);
240- }
241-      else
242- {
243-   rc = db_set_add (new_set, &element);
244- }
245-
246-      if (rc != NO_ERROR)
247- {
248-   db_set_free (new_set);
249-   return ER_FAILED;
250- }
251-    }
252-
253-  pr_clear_value (value_p);
254-
255-  switch (type)
256-    {
257-    case DB_TYPE_SET:
258-      db_make_set (value_p, new_set);
259-      break;
260-    case DB_TYPE_MULTISET:
261-      db_make_multiset (value_p, new_set);
262-      break;
263:    case DB_TYPE_SEQUENCE:
264-      db_make_sequence (value_p, new_set);
265-      break;
266-    default:
267-      db_set_free (new_set);
268-      return ER_FAILED;
269-    }
270-
271-  return NO_ERROR;
272-}
273-
274-/*
275- * cursor_fixup_vobjs () -
276- *   return: NO_ERROR on all ok, ER status( or ER_FAILED) otherwise
277- *   value(in/out): a db_value
278- * Note: if value is an OID then turn it into an OBJECT type value
279- *       if value is a VOBJ then turn it into a vmop
280- *       if value is a set/seq then do same fixups on its elements
281- */
282-static int
283-cursor_fixup_vobjs (DB_VALUE * value_p)
--
292-      db_make_object (value_p, obj);
293-      break;
294-
295-    case DB_TYPE_VOBJ:
296-      if (DB_IS_NULL (value_p))
297- {
298-   db_value_clear (value_p);
299-   db_value_domain_init (value_p, DB_TYPE_OBJECT, DB_DEFAULT_PRECISION, DB_DEFAULT_SCALE);
300-   rc = NO_ERROR;
301- }
302-      else
303- {
304-   rc = vid_vobj_to_object (value_p, &obj);
305-   pr_clear_value (value_p);
306-   db_make_object (value_p, obj);
307- }
308-      break;
309-
310-    case DB_TYPE_SET:
311-    case DB_TYPE_MULTISET:
312:    case DB_TYPE_SEQUENCE:
313-      /* fixup any set/seq of vobjs into a set/seq of vmops */
314-      rc = cursor_fixup_set_vobjs (value_p);
315-      value_p->need_clear = true;
316-      break;
317-
318-    default:
319-      rc = NO_ERROR;
320-      break;
321-    }
322-
323-  return rc;
324-}
325-
326-/*
327- * cursor_copy_vobj_to_dbvalue - The given tuple set value which is in disk
328- *   representation form is copied to the db_value structure
329- *   return: NO_ERROR on all ok, ER status( or ER_FAILED) otherwise
330- *   buf(in)            : Pointer to set disk representation
331- *   db_value(out)      : Set to the set value
332- */
```

```cpp
query_opfunc.c
2080-  int i, card, card1;
2081-#if !defined(NDEBUG)
2082-  DB_TYPE type1, type2;
2083-#endif
2084-
2085-#if !defined(NDEBUG)
2086-  type1 = DB_VALUE_DOMAIN_TYPE (seq_val_p);
2087-  type2 = DB_VALUE_DOMAIN_TYPE (dbval_p);
2088-
2089-  assert (TP_IS_SET_TYPE (type1));
2090-  assert (TP_IS_SET_TYPE (type2));
2091-#endif
2092-
2093-  if (domain_p == NULL)
2094-    {
2095-      return ER_FAILED;
2096-    }
2097-
2098-  db_make_null (&dbval_tmp);
2099-
2100:  if (TP_DOMAIN_TYPE (domain_p) == DB_TYPE_SEQUENCE)
2101-    {
2102-      if (tp_value_coerce (seq_val_p, result_p, domain_p) != DOMAIN_COMPATIBLE)
2103- {
2104-   return ER_FAILED;
2105- }
2106-
2107-      seq_tmp = db_get_set (dbval_p);
2108-      card = db_seq_size (seq_tmp);
2109-      seq_tmp1 = db_get_set (result_p);
2110-      card1 = db_seq_size (seq_tmp1);
2111-
2112-      for (i = 0; i < card; i++)
2113- {
2114-   if (db_seq_get (seq_tmp, i, &dbval_tmp) != NO_ERROR)
2115-     {
2116-       return ER_FAILED;
2117-     }
2118-
2119-   if (db_seq_put (seq_tmp1, card1 + i, &dbval_tmp) != NO_ERROR)
2120-     {
--
2539-
2540-    case DB_TYPE_NUMERIC:
2541-      error = qdata_add_numeric_to_dbval (dbval1_p, dbval2_p, result_p);
2542-      break;
2543-
2544-    case DB_TYPE_MONETARY:
2545-      error = qdata_add_monetary_to_dbval (dbval1_p, dbval2_p, result_p);
2546-      break;
2547-
2548-    case DB_TYPE_CHAR:
2549-    case DB_TYPE_VARCHAR:
2550-    case DB_TYPE_NCHAR:
2551-    case DB_TYPE_VARNCHAR:
2552-    case DB_TYPE_BIT:
2553-    case DB_TYPE_VARBIT:
2554-      error = qdata_add_chars_to_dbval (dbval1_p, dbval2_p, result_p);
2555-      break;
2556-
2557-    case DB_TYPE_SET:
2558-    case DB_TYPE_MULTISET:
2559:    case DB_TYPE_SEQUENCE:
2560-      if (!TP_IS_SET_TYPE (type2))
2561- {
2562-   er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, ER_QPROC_INVALID_DATATYPE, 0);
2563-   return ER_QPROC_INVALID_DATATYPE;
2564- }
2565-      if (domain_p == NULL)
2566- {
2567-   if (type1 == type2)
2568-     {
2569-       /* partial resolve : set only basic domain; full domain will be resolved in 'fetch', based on the
2570-        * result's value */
2571-       domain_p = tp_domain_resolve_default (type1);
2572-     }
2573-   else
2574-     {
2575-       domain_p = tp_domain_resolve_default (DB_TYPE_MULTISET);
2576-     }
2577- }
2578-      error = qdata_add_sequence_to_dbval (dbval1_p, dbval2_p, result_p, domain_p);
2579-      break;
--
4618-      break;
4619-
4620-    case DB_TYPE_FLOAT:
4621-      error = qdata_subtract_float_to_dbval (dbval1_p, dbval2_p, result_p);
4622-      break;
4623-
4624-    case DB_TYPE_DOUBLE:
4625-      error = qdata_subtract_double_to_dbval (dbval1_p, dbval2_p, result_p);
4626-      break;
4627-
4628-    case DB_TYPE_NUMERIC:
4629-      error = qdata_subtract_numeric_to_dbval (dbval1_p, dbval2_p, result_p);
4630-      break;
4631-
4632-    case DB_TYPE_MONETARY:
4633-      error = qdata_subtract_monetary_to_dbval (dbval1_p, dbval2_p, result_p);
4634-      break;
4635-
4636-    case DB_TYPE_SET:
4637-    case DB_TYPE_MULTISET:
4638:    case DB_TYPE_SEQUENCE:
4639-      if (!TP_IS_SET_TYPE (type2))
4640- {
4641-   er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, ER_QPROC_INVALID_DATATYPE, 0);
4642-   return ER_QPROC_INVALID_DATATYPE;
4643- }
4644-      if (domain_p == NULL)
4645- {
4646-   if (type1 == type2 && type1 == DB_TYPE_SET)
4647-     {
4648-       /* partial resolve : set only basic domain; full domain will be resolved in 'fetch', based on the
4649-        * result's value */
4650-       domain_p = tp_domain_resolve_default (type1);
4651-     }
4652-   else
4653-     {
4654-       domain_p = tp_domain_resolve_default (DB_TYPE_MULTISET);
4655-     }
4656- }
4657-      error = qdata_subtract_sequence_to_dbval (dbval1_p, dbval2_p, result_p, domain_p);
4658-      break;
--
5237-      break;
5238-
5239-    case DB_TYPE_FLOAT:
5240-      error = qdata_multiply_float_to_dbval (dbval1_p, dbval2_p, result_p);
5241-      break;
5242-
5243-    case DB_TYPE_DOUBLE:
5244-      error = qdata_multiply_double_to_dbval (dbval1_p, dbval2_p, result_p);
5245-      break;
5246-
5247-    case DB_TYPE_NUMERIC:
5248-      error = qdata_multiply_numeric_to_dbval (dbval1_p, dbval2_p, result_p);
5249-      break;
5250-
5251-    case DB_TYPE_MONETARY:
5252-      error = qdata_multiply_monetary_to_dbval (dbval1_p, dbval2_p, result_p);
5253-      break;
5254-
5255-    case DB_TYPE_SET:
5256-    case DB_TYPE_MULTISET:
5257:    case DB_TYPE_SEQUENCE:
5258-      if (!TP_IS_SET_TYPE (type2))
5259- {
5260-   er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, ER_QPROC_INVALID_DATATYPE, 0);
5261-   return ER_QPROC_INVALID_DATATYPE;
5262- }
5263-      if (domain_p == NULL)
5264- {
5265-   if (type1 == type2 && type1 == DB_TYPE_SET)
5266-     {
5267-       /* partial resolve : set only basic domain; full domain will be resolved in 'fetch', based on the
5268-        * result's value */
5269-       domain_p = tp_domain_resolve_default (type1);
5270-     }
5271-   else
5272-     {
5273-       domain_p = tp_domain_resolve_default (DB_TYPE_MULTISET);
5274-     }
5275- }
5276-      error = qdata_multiply_sequence_to_dbval (dbval1_p, dbval2_p, result_p, domain_p);
5277-      break;
--
5859-      break;
5860-
5861-    case DB_TYPE_FLOAT:
5862-      error = qdata_divide_float_to_dbval (dbval1_p, dbval2_p, result_p);
5863-      break;
5864-
5865-    case DB_TYPE_DOUBLE:
5866-      error = qdata_divide_double_to_dbval (dbval1_p, dbval2_p, result_p);
5867-      break;
5868-
5869-    case DB_TYPE_NUMERIC:
5870-      error = qdata_divide_numeric_to_dbval (dbval1_p, dbval2_p, result_p);
5871-      break;
5872-
5873-    case DB_TYPE_MONETARY:
5874-      error = qdata_divide_monetary_to_dbval (dbval1_p, dbval2_p, result_p);
5875-      break;
5876-
5877-    case DB_TYPE_SET:
5878-    case DB_TYPE_MULTISET:
5879:    case DB_TYPE_SEQUENCE:
5880-    case DB_TYPE_TIME:
5881-    case DB_TYPE_TIMESTAMP:
5882-    case DB_TYPE_TIMESTAMPLTZ:
5883-    case DB_TYPE_TIMESTAMPTZ:
5884-    case DB_TYPE_DATETIME:
5885-    case DB_TYPE_DATETIMELTZ:
5886-    case DB_TYPE_DATETIMETZ:
5887-    case DB_TYPE_DATE:
5888-    case DB_TYPE_STRING:
5889-    default:
5890-      if (prm_get_bool_value (PRM_ID_RETURN_NULL_ON_FUNCTION_ERRORS) == false)
5891- {
5892-   er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, ER_QPROC_INVALID_DATATYPE, 0);
5893-   return ER_QPROC_INVALID_DATATYPE;
5894- }
5895-    }
5896-
5897-  if (error != NO_ERROR)
5898-    {
5899-      return error;
--
6150-
6151-    case DB_TYPE_MONETARY:
6152-      error = qdata_add_monetary_to_dbval (dbval1_p, dbval2_p, result_p);
6153-      break;
6154-
6155-    case DB_TYPE_NULL:
6156-    case DB_TYPE_CHAR:
6157-    case DB_TYPE_VARCHAR:
6158-    case DB_TYPE_NCHAR:
6159-    case DB_TYPE_VARNCHAR:
6160-    case DB_TYPE_BIT:
6161-    case DB_TYPE_VARBIT:
6162-      if (dbval1_p != NULL && dbval2_p != NULL)
6163- {
6164-   error = qdata_add_chars_to_dbval (dbval1_p, dbval2_p, result_p);
6165- }
6166-      break;
6167-
6168-    case DB_TYPE_SET:
6169-    case DB_TYPE_MULTISET:
6170:    case DB_TYPE_SEQUENCE:
6171-      if (!TP_IS_SET_TYPE (type2))
6172- {
6173-   er_set (ER_ERROR_SEVERITY, ARG_FILE_LINE, ER_QPROC_INVALID_DATATYPE, 0);
6174-   return ER_QPROC_INVALID_DATATYPE;
6175- }
6176-      error = qdata_add_sequence_to_dbval (dbval1_p, dbval2_p, result_p, domain_p);
6177-      break;
6178-
6179-    case DB_TYPE_TIME:
6180-      error = qdata_add_time_to_dbval (dbval1_p, dbval2_p, result_p);
6181-      break;
6182-
6183-    case DB_TYPE_TIMESTAMP:
6184-    case DB_TYPE_TIMESTAMPLTZ:
6185-      error = qdata_add_utime_to_dbval (dbval1_p, dbval2_p, result_p, domain_p);
6186-      if (error == NO_ERROR && type1 == DB_TYPE_TIMESTAMPLTZ)
6187- {
6188-   db_make_timestampltz (result_p, *db_get_timestamp (result_p));
6189- }
6190-      break;
--
6570-  DB_COLLECTION *collection_p = NULL;
6571-  SETOBJ *setobj_p = NULL;
6572-  int n, size;
6573-  REGU_VARIABLE_LIST regu_var_p = NULL, operand = NULL;
6574-  int error_code = NO_ERROR;
6575-  TP_DOMAIN *domain_p = NULL;
6576-
6577-  result_p = regu_func_p->value.funcp->value;
6578-  operand = regu_func_p->value.funcp->operand;
6579-  domain_p = regu_func_p->domain;
6580-  db_make_null (&dbval);
6581-
6582-  if (stype == DB_TYPE_SET)
6583-    {
6584-      collection_p = db_set_create_basic (NULL, NULL);
6585-    }
6586-  else if (stype == DB_TYPE_MULTISET)
6587-    {
6588-      collection_p = db_set_create_multi (NULL, NULL);
6589-    }
6590:  else if (stype == DB_TYPE_SEQUENCE || stype == DB_TYPE_VOBJ)
6591-    {
6592-      size = 0;
6593-      for (regu_var_p = operand; regu_var_p; regu_var_p = regu_var_p->next)
6594- {
6595-   size++;
6596- }
6597-
6598-      collection_p = db_seq_create (NULL, NULL, size);
6599-    }
6600-  else
6601-    {
6602-      return ER_FAILED;
6603-    }
6604-
6605-  error_code = set_get_setobj (collection_p, &setobj_p, 1);
6606-  if (error_code != NO_ERROR || !setobj_p)
6607-    {
6608-      goto error;
6609-    }
6610-
--
6770-qdata_evaluate_function (THREAD_ENTRY * thread_p, regu_variable_node * function_p, val_descr * val_desc_p,
6771-    OID * obj_oid_p, QFILE_TUPLE tuple)
6772-{
6773-  FUNCTION_TYPE *funcp;
6774-
6775-  /* should sync with fetch_peek_dbval () */
6776-
6777-  funcp = function_p->value.funcp;
6778-  /* clear any value from a previous iteration */
6779-  pr_clear_value (funcp->value);
6780-
6781-  switch (funcp->ftype)
6782-    {
6783-    case F_SET:
6784-      return qdata_convert_dbvals_to_set (thread_p, DB_TYPE_SET, function_p, val_desc_p, obj_oid_p, tuple);
6785-
6786-    case F_MULTISET:
6787-      return qdata_convert_dbvals_to_set (thread_p, DB_TYPE_MULTISET, function_p, val_desc_p, obj_oid_p, tuple);
6788-
6789-    case F_SEQUENCE:
6790:      return qdata_convert_dbvals_to_set (thread_p, DB_TYPE_SEQUENCE, function_p, val_desc_p, obj_oid_p, tuple);
6791-
6792-    case F_VID:
6793-      return qdata_convert_dbvals_to_set (thread_p, DB_TYPE_VOBJ, function_p, val_desc_p, obj_oid_p, tuple);
6794-
6795-    case F_TABLE_SET:
6796-      return qdata_convert_table_to_set (thread_p, DB_TYPE_SET, function_p, val_desc_p);
6797-
6798-    case F_TABLE_MULTISET:
6799-      return qdata_convert_table_to_set (thread_p, DB_TYPE_MULTISET, function_p, val_desc_p);
6800-
6801-    case F_TABLE_SEQUENCE:
6802:      return qdata_convert_table_to_set (thread_p, DB_TYPE_SEQUENCE, function_p, val_desc_p);
6803-
6804-    case F_GENERIC:
6805-      return qdata_evaluate_generic_function (thread_p, funcp, val_desc_p, obj_oid_p, tuple);
6806-
6807-    case F_CLASS_OF:
6808-      return qdata_get_class_of_function (thread_p, funcp, val_desc_p, obj_oid_p, tuple);
6809-
6810-    case F_INSERT_SUBSTRING:
6811-      return qdata_insert_substring_function (thread_p, funcp, val_desc_p, obj_oid_p, tuple);
6812-
6813-    case F_ELT:
6814-      return qdata_elt (thread_p, funcp, val_desc_p, obj_oid_p, tuple);
6815-
6816-    case F_BENCHMARK:
6817-      return qdata_benchmark (thread_p, funcp, val_desc_p, obj_oid_p, tuple);
6818-
6819-    case F_JSON_ARRAY:
6820-      return qdata_convert_operands_to_value_and_call (thread_p, funcp, val_desc_p, obj_oid_p, tuple,
6821-             db_evaluate_json_array);
6822-
--
6958-  /* execute linked query */
6959-  EXECUTE_REGU_VARIABLE_XASL (thread_p, &(operand->value), val_desc_p);
6960-
6961-  if (CHECK_REGU_VARIABLE_XASL_STATUS (&(operand->value)) != XASL_SUCCESS)
6962-    {
6963-      return ER_FAILED;
6964-    }
6965-
6966-  domain_p = function_p->domain;
6967-  list_id_p = operand->value.value.srlist_id->list_id;
6968-  db_make_null (&dbval);
6969-
6970-  if (stype == DB_TYPE_SET)
6971-    {
6972-      collection_p = db_set_create_basic (NULL, NULL);
6973-    }
6974-  else if (stype == DB_TYPE_MULTISET)
6975-    {
6976-      collection_p = db_set_create_multi (NULL, NULL);
6977-    }
6978:  else if (stype == DB_TYPE_SEQUENCE || stype == DB_TYPE_VOBJ)
6979-    {
6980-      collection_p = db_seq_create (NULL, NULL, (list_id_p->tuple_cnt * list_id_p->type_list.type_cnt));
6981-    }
6982-  else
6983-    {
6984-      return ER_FAILED;
6985-    }
6986-
6987-  error = set_get_setobj (collection_p, &setobj_p, 1);
6988-  if (error != NO_ERROR || !setobj_p)
6989-    {
6990-      set_free (collection_p);
6991-      return ER_FAILED;
6992-    }
6993-
6994-  /*
6995-   * Don't need to worry about the vobj case here; this function can't
6996-   * be called in a context where it's expected to produce a vobj.  See
6997-   * xd_dbvals_to_set for the contrasting case.
6998-   */

```

```cpp
query_dump.c
1185-  switch (type)
1186-    {
1187-    case DB_TYPE_NULL:
1188-      return "NULL";
1189-    case DB_TYPE_INTEGER:
1190-      return "INTEGER";
1191-    case DB_TYPE_BIGINT:
1192-      return "BIGINT";
1193-    case DB_TYPE_FLOAT:
1194-      return "FLOAT";
1195-    case DB_TYPE_DOUBLE:
1196-      return "DOUBLE";
1197-    case DB_TYPE_VARCHAR:
1198-      return "VARCHAR";
1199-    case DB_TYPE_OBJECT:
1200-      return "OBJECT";
1201-    case DB_TYPE_SET:
1202-      return "SET";
1203-    case DB_TYPE_MULTISET:
1204-      return "MULTISET";
1205:    case DB_TYPE_SEQUENCE:
1206-      return "SEQUENCE";
1207-    case DB_TYPE_BLOB:
1208-      return "BLOB";
1209-    case DB_TYPE_CLOB:
1210-      return "CLOB";
1211-    case DB_TYPE_TIME:
1212-      return "TIME";
1213-    case DB_TYPE_TIMESTAMP:
1214-      return "TIMESTAMP";
1215-    case DB_TYPE_TIMESTAMPTZ:
1216-      return "TIMESTAMPTZ";
1217-    case DB_TYPE_TIMESTAMPLTZ:
1218-      return "TIMESTAMPLTZ";
1219-    case DB_TYPE_DATETIME:
1220-      return "DATETIME";
1221-    case DB_TYPE_DATETIMETZ:
1222-      return "DATETIMETZ";
1223-    case DB_TYPE_DATETIMELTZ:
1224-      return "DATETIMELTZ";
1225-    case DB_TYPE_DATE:

```

```cpp
query_executor.c
24984-    case DB_TYPE_MONETARY:
24985-      return "MONETARY";
24986-
24987-
24988-    case DB_TYPE_VARCHAR:
24989-      return "VARCHAR";
24990-
24991-    case DB_TYPE_CHAR:
24992-      return "CHAR";
24993-
24994-    case DB_TYPE_OID:
24995-    case DB_TYPE_OBJECT:
24996-      return "OBJECT";
24997-
24998-    case DB_TYPE_SET:
24999-      return "SET";
25000-
25001-    case DB_TYPE_MULTISET:
25002-      return "MULTISET";
25003-
25004:    case DB_TYPE_SEQUENCE:
25005-      return "SEQUENCE";
25006-
25007-    case DB_TYPE_NCHAR:
25008-      return "NCHAR";
25009-
25010-    case DB_TYPE_VARNCHAR:
25011-      return "NCHAR VARYING";
25012-
25013-    case DB_TYPE_BIT:
25014-      return "BIT";
25015-
25016-    case DB_TYPE_VARBIT:
25017-      return "BIT VARYING";
25018-
25019-    case DB_TYPE_BLOB:
25020-      return "BLOB";
25021-
25022-    case DB_TYPE_CLOB:
25023-      return "CLOB";
25024-
--
25067-    case DB_TYPE_VARBIT:
25068-      precision = domain->precision;
25069-      break;
25070-
25071-    case DB_TYPE_SET:
25072-      setdomain = domain->setdomain;
25073-      if (setdomain == NULL)
25074- {
25075-   return NO_ERROR;
25076- }
25077-      set_of_string = "SET OF ";
25078-      break;
25079-    case DB_TYPE_MULTISET:
25080-      setdomain = domain->setdomain;
25081-      if (setdomain == NULL)
25082- {
25083-   return NO_ERROR;
25084- }
25085-      set_of_string = "MULTISET OF ";
25086-      break;
25087:    case DB_TYPE_SEQUENCE:
25088-      setdomain = domain->setdomain;
25089-      if (setdomain == NULL)
25090- {
25091-   return NO_ERROR;
25092- }
25093-      set_of_string = "SEQUENCE OF ";
25094-      break;
25095-
25096-    case DB_TYPE_ENUMERATION:
25097-      enum_elements = domain->enumeration.elements;
25098-      enum_elements_count = domain->enumeration.count;
25099-      break;
25100-    case DB_TYPE_JSON:
25101-      validator = domain->json_validator;
25102-      break;
25103-    default:
25104-      break;
25105-    }
25106-
25107-  name = qexec_schema_get_type_name_from_id (id);

```
