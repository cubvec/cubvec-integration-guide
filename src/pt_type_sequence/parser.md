# parser directory

```cpp
func_type.cpp
104-{
105-  {PT_TYPE_DOUBLE, {PT_GENERIC_TYPE_NUMBER}, {}},
106-};
107-
108-func_all_signatures sig_of_count_star =
109-{
110-  {PT_TYPE_BIGINT, {}, {}},
111-};
112-
113-func_all_signatures sig_of_count =
114-{
115-  {PT_TYPE_BIGINT, {PT_GENERIC_TYPE_ANY}, {}},
116-};
117-
118-func_all_signatures sig_of_sum =
119-{
120-  {0, {PT_GENERIC_TYPE_NUMBER}, {}},
121-  {0, {PT_TYPE_MAYBE}, {}},
122-  {0, {PT_TYPE_SET}, {}},
123-  {0, {PT_TYPE_MULTISET}, {}},
124:  {0, {PT_TYPE_SEQUENCE}, {}},
125-};
126-
127-func_all_signatures sig_ret_double_arg_r_any =
128-{
129-  {PT_TYPE_DOUBLE, {}, {}},
130-  {PT_TYPE_DOUBLE, {}, {PT_GENERIC_TYPE_ANY}},
131-};
132-
133-func_all_signatures sig_of_ntile =
134-{
135-  {PT_TYPE_INTEGER, {PT_GENERIC_TYPE_NUMBER}, {}}, //argument value will be truncated at execution
136-};
137-
138-/*cannot define a clear signature because casting depends on actual value
139-  MEDIAN('123456')     <=> MEDIAN(double) -> double
140-  MEDIAN('2018-03-14') <=> MEDIAN(date)   -> date  */
141-func_all_signatures sig_of_median =
142-{
143-  {PT_TYPE_MAYBE, {PT_GENERIC_TYPE_NUMBER}, {}}, //if ret type is double => tests with median(int) will fail
144-  {0, {PT_GENERIC_TYPE_DATETIME}, {}},
--
298-  {PT_TYPE_JSON, {PT_GENERIC_TYPE_JSON_VAL}, {}}
299-};
300-
301-func_all_signatures sig_of_json_objectagg =
302-{
303-  {PT_TYPE_JSON, {PT_GENERIC_TYPE_STRING, PT_GENERIC_TYPE_JSON_VAL}, {}}
304-};
305-
306-func_all_signatures sig_ret_set_arg_r_any =
307-{
308-  {PT_TYPE_SET, {}, {PT_GENERIC_TYPE_ANY}},
309-};
310-
311-func_all_signatures sig_ret_multiset_arg_r_any =
312-{
313-  {PT_TYPE_MULTISET, {}, {PT_GENERIC_TYPE_ANY}},
314-};
315-
316-func_all_signatures sig_ret_sequence_arg_r_any =
317-{
318:  {PT_TYPE_SEQUENCE, {}, {PT_GENERIC_TYPE_ANY}},
319-};
320-
321-func_all_signatures sig_of_generic =
322-{
323-  {0, {PT_GENERIC_TYPE_ANY}, {}},
324-};
325-
326-func_all_signatures sig_ret_string_arg_jdoc =
327-{
328-  {PT_TYPE_VARCHAR, {PT_GENERIC_TYPE_JSON_DOC}, {}},
329-};
330-
331-func_all_signatures sig_ret_type0_arg_str =
332-{
333-  {0, {PT_GENERIC_TYPE_STRING}, {}},
334-};
335-
336-func_all_signatures sig_of_benchmark =
337-{
338-  {PT_TYPE_DOUBLE, {PT_GENERIC_TYPE_DISCRETE_NUMBER, PT_GENERIC_TYPE_ANY}, {}},
--
1964-   node->type_enum = PT_TYPE_BIGINT;
1965-   break;
1966-
1967- case PT_AGG_BIT_AND:
1968- case PT_AGG_BIT_OR:
1969- case PT_AGG_BIT_XOR:
1970-   node->type_enum = PT_TYPE_BIGINT;
1971-   break;
1972-
1973- case F_TABLE_SET:
1974-   node->type_enum = PT_TYPE_SET;
1975-   pt_add_type_to_set (parser, pt_get_select_list (parser, arg_list), &node->data_type);
1976-   break;
1977-
1978- case F_TABLE_MULTISET:
1979-   node->type_enum = PT_TYPE_MULTISET;
1980-   pt_add_type_to_set (parser, pt_get_select_list (parser, arg_list), &node->data_type);
1981-   break;
1982-
1983- case F_TABLE_SEQUENCE:
1984:   node->type_enum = PT_TYPE_SEQUENCE;
1985-   pt_add_type_to_set (parser, pt_get_select_list (parser, arg_list), &node->data_type);
1986-   break;
1987-
1988- case F_SET:
1989-   node->type_enum = PT_TYPE_SET;
1990-   pt_add_type_to_set (parser, arg_list, &node->data_type);
1991-   break;
1992-
1993- case F_MULTISET:
1994-   node->type_enum = PT_TYPE_MULTISET;
1995-   pt_add_type_to_set (parser, arg_list, &node->data_type);
1996-   break;
1997-
1998- case F_SEQUENCE:
1999:   node->type_enum = PT_TYPE_SEQUENCE;
2000-   pt_add_type_to_set (parser, arg_list, &node->data_type);
2001-   break;
2002-
2003- case PT_SUM:
2004-   node->type_enum = arg_type;
2005-   node->data_type = parser_copy_tree_list (parser, arg_list->data_type);
2006-   if (arg_type == PT_TYPE_NUMERIC && node->data_type)
2007-     {
2008-       node->data_type->info.data_type.precision = DB_MAX_NUMERIC_PRECISION;
2009-     }
2010-   break;
2011-
2012- case PT_AVG:
2013- case PT_STDDEV:
2014- case PT_STDDEV_POP:
2015- case PT_STDDEV_SAMP:
2016- case PT_VARIANCE:
2017- case PT_VAR_POP:
2018- case PT_VAR_SAMP:
2019-   node->type_enum = arg_type;

```

```cpp
parse_tree.h
128-        ( ((t) == PT_TYPE_INTEGER)  || \
129-   ((t) == PT_TYPE_BIGINT)   || \
130-   ((t) == PT_TYPE_FLOAT)    || \
131-   ((t) == PT_TYPE_DOUBLE)   || \
132-   ((t) == PT_TYPE_SMALLINT) || \
133-   ((t) == PT_TYPE_MONETARY) || \
134-   ((t) == PT_TYPE_LOGICAL)  || \
135-   ((t) == PT_TYPE_NUMERIC))
136-
137-#define PT_IS_DISCRETE_NUMBER_TYPE(t) \
138-        ( ((t) == PT_TYPE_INTEGER)  || \
139-          ((t) == PT_TYPE_BIGINT)   || \
140-   ((t) == PT_TYPE_SMALLINT))
141-
142-#define PT_IS_COUNTER_TYPE(t) \
143-  PT_IS_DISCRETE_NUMBER_TYPE(t)
144-
145-#define PT_IS_COLLECTION_TYPE(t) \
146-        ( ((t) == PT_TYPE_SET)       || \
147-   ((t) == PT_TYPE_MULTISET)  || \
148:   ((t) == PT_TYPE_SEQUENCE))
149-
150-#define PT_IS_STRING_TYPE(t) \
151-        ( ((t) == PT_TYPE_CHAR)     || \
152-   ((t) == PT_TYPE_VARCHAR)  || \
153-   ((t) == PT_TYPE_NCHAR)    || \
154-   ((t) == PT_TYPE_VARNCHAR) || \
155-   ((t) == PT_TYPE_BIT)      || \
156-   ((t) == PT_TYPE_VARBIT))
157-
158-#define PT_IS_NATIONAL_CHAR_STRING_TYPE(t) \
159-        ( ((t) == PT_TYPE_NCHAR)      || \
160-   ((t) == PT_TYPE_VARNCHAR))
161-
162-#define PT_IS_SIMPLE_CHAR_STRING_TYPE(t) \
163-        ( ((t) == PT_TYPE_CHAR)      || \
164-   ((t) == PT_TYPE_VARCHAR))
165-
166-#define PT_IS_CHAR_STRING_TYPE(t) \
167-        ( ((t) == PT_TYPE_CHAR)      || \
168-   ((t) == PT_TYPE_VARCHAR)   || \
169-   ((t) == PT_TYPE_NCHAR)     || \
170-   ((t) == PT_TYPE_VARNCHAR))
171-
172-#define PT_IS_BIT_STRING_TYPE(t) \
173-        ( ((t) == PT_TYPE_BIT)      || \
174-   ((t) == PT_TYPE_VARBIT))
175-
176-#define PT_IS_COMPLEX_TYPE(t) \
177-        ( ((t) == PT_TYPE_MONETARY)  || \
178-   ((t) == PT_TYPE_NUMERIC)   || \
179-   ((t) == PT_TYPE_CHAR)      || \
180-   ((t) == PT_TYPE_VARCHAR)   || \
181-   ((t) == PT_TYPE_NCHAR)     || \
182-   ((t) == PT_TYPE_VARNCHAR)  || \
183-   ((t) == PT_TYPE_BIT)       || \
184-   ((t) == PT_TYPE_VARBIT)    || \
185-   ((t) == PT_TYPE_OBJECT)    || \
186-   ((t) == PT_TYPE_SET)       || \
187-   ((t) == PT_TYPE_MULTISET)  || \
188:   ((t) == PT_TYPE_SEQUENCE)  || \
189-   ((t) == PT_TYPE_ENUMERATION))
190-
191-#define PT_IS_DATE_TIME_WITH_TZ_TYPE(t) \
192-        ( (t) == PT_TYPE_TIMESTAMPTZ  || \
193-   (t) == PT_TYPE_TIMESTAMPLTZ || \
194-   (t) == PT_TYPE_DATETIMETZ   || \
195-   (t) == PT_TYPE_DATETIMELTZ)
196-
197-#define PT_IS_DATE_TIME_TYPE(t) \
198-        ( ((t) == PT_TYPE_DATE)         || \
199-   ((t) == PT_TYPE_TIME)         || \
200-   ((t) == PT_TYPE_TIMESTAMP)    || \
201-   ((t) == PT_TYPE_DATETIME)     || \
202-   ((t) == PT_TYPE_DATETIMETZ)   || \
203-   ((t) == PT_TYPE_DATETIMELTZ)  || \
204-   ((t) == PT_TYPE_TIMESTAMPTZ)  || \
205-   ((t) == PT_TYPE_TIMESTAMPLTZ))
206-
207-#define PT_HAS_DATE_PART(t) \
208-        ( ((t) == PT_TYPE_DATE)         || \
--
321-
322-#define PT_IS_NAME_NODE(n) \
323-        ( (n) && ((n)->node_type == PT_NAME) )
324-
325-#define PT_IS_OID_NAME(n) \
326-        ( (n) && \
327-          ((n)->node_type == PT_NAME && \
328-             ((n)->info.name.meta_class == PT_OID_ATTR || \
329-              (n)->info.name.meta_class == PT_VID_ATTR) ) \
330-        )
331-
332-#define PT_IS_VALUE_NODE(n) \
333-        ( (n) && ((n)->node_type == PT_VALUE) )
334-
335-#define PT_IS_INSERT_VALUE_NODE(n) \
336- ( (n) && ((n)->node_type == PT_INSERT_VALUE) )
337-
338-#define PT_IS_SET_TYPE(n) \
339-        ( (n) && ((n)->type_enum == PT_TYPE_SET || \
340-                  (n)->type_enum == PT_TYPE_MULTISET || \
341:                  (n)->type_enum == PT_TYPE_SEQUENCE) \
342-        )
343-
344-#define PT_IS_HOSTVAR(n) \
345-        ( (n) && ((n)->node_type == PT_HOST_VAR) )
346-
347-#define PT_IS_INPUT_HOSTVAR(n) \
348-        ( (n) && ((n)->node_type == PT_HOST_VAR && \
349-                  (n)->info.host_var.var_type == PT_HOST_IN) \
350-        )
351-
352-#define PT_IS_OUTPUT_HOSTVAR(n) \
353-        ( (n) && ((n)->node_type == PT_HOST_VAR && \
354-                  (n)->info.host_var.var_type == PT_HOST_OUT) \
355-        )
356-
357-#define PT_IS_PARAMETER(n) \
358-        ( (n) && ((n)->node_type == PT_NAME && \
359-                  (n)->info.name.meta_class == PT_PARAMETER) )
360-
361-#define PT_IS_INPUT_PARAMETER(n) \
--
1099-  PT_TYPE_NUMERIC,
1100-  PT_TYPE_CHAR,
1101-  PT_TYPE_VARCHAR,
1102-  PT_TYPE_NCHAR,
1103-  PT_TYPE_VARNCHAR,
1104-  PT_TYPE_BIT,
1105-  PT_TYPE_VARBIT,
1106-  PT_TYPE_LOGICAL,
1107-  PT_TYPE_MAYBE,
1108-  PT_TYPE_JSON,
1109-
1110-  /* special values */
1111-  PT_TYPE_NA,   /* in SELECT NA */
1112-  PT_TYPE_NULL,   /* in assignment and defaults */
1113-  PT_TYPE_STAR,   /* select (*), count (*), will be expanded later */
1114-
1115-  /* non primitive types */
1116-  PT_TYPE_OBJECT,
1117-  PT_TYPE_SET,
1118-  PT_TYPE_MULTISET,
1119:  PT_TYPE_SEQUENCE,
1120-  PT_TYPE_MIDXKEY,
1121-  PT_TYPE_COMPOUND,
1122-
1123-  PT_TYPE_EXPR_SET,  /* type of parentheses expr set, avail for parser only */
1124-  PT_TYPE_RESULTSET,
1125-
1126-  PT_TYPE_BIGINT,
1127-
1128-  PT_TYPE_BLOB,
1129-  PT_TYPE_CLOB,
1130-  PT_TYPE_ELO,
1131-
1132-  PT_TYPE_ENUMERATION,
1133-  PT_TYPE_TIMESTAMPLTZ,
1134-  PT_TYPE_TIMESTAMPTZ,
1135-  PT_TYPE_DATETIMETZ,
1136-  PT_TYPE_DATETIMELTZ,
1137-
1138-  PT_TYPE_MAX,
1139-


```

```cpp
view_transform.c
6996-{
6997-  PT_NODE *select_list;
6998-
6999-  if (!parser || !stmt)
7000-    {
7001-      return;
7002-    }
7003-
7004-  switch (stmt->node_type)
7005-    {
7006-    case PT_SELECT:
7007-      select_list = stmt->info.query.q.select.list;
7008-      if (select_list != NULL && !PT_IS_VALUE_QUERY (stmt))
7009- {
7010-   DB_VALUE vid;
7011-
7012-   select_list->node_type = PT_FUNCTION;
7013-   /* make info set up properly */
7014-   memset (&(select_list->info), 0, sizeof (select_list->info));
7015-   select_list->data_type->info.data_type.entity = NULL;
7016:   select_list->data_type->info.data_type.virt_type_enum = PT_TYPE_SEQUENCE;
7017-   select_list->type_enum = PT_TYPE_OBJECT;
7018-
7019-   /* set vclass_name as literal string */
7020-   db_make_string (&vid, db_get_class_name (virt_entity->info.name.db_object));
7021-   select_list->info.function.arg_list = pt_dbval_to_value (parser, &vid);
7022-   select_list->info.function.function_type = F_SEQUENCE;
7023-
7024-   select_list->data_type->info.data_type.virt_object = virt_entity->info.name.db_object;
7025-
7026-   pr_clear_value (&vid);
7027- }
7028-      break;
7029-    case PT_UNION:
7030-    case PT_INTERSECTION:
7031-    case PT_DIFFERENCE:
7032-      mq_set_non_updatable_oid (parser, stmt->info.query.q.union_.arg1, virt_entity);
7033-      mq_set_non_updatable_oid (parser, stmt->info.query.q.union_.arg2, virt_entity);
7034-      break;
7035-    default:
7036-      break;


```

```cpp
semantic_check.c
372-      is_compatible = true;
373-
374-      cinfo->type_enum = common_type;
375-
376-      cinfo->scale = MAX (att1_info->scale, att2_info->scale);
377-      cinfo->prec = MAX ((att1_info->prec - att1_info->scale), (att2_info->prec - att2_info->scale)) + cinfo->scale;
378-
379-      if (cinfo->prec > DB_MAX_NUMERIC_PRECISION)
380- {   /* overflow */
381-   cinfo->scale -= (cinfo->prec - DB_MAX_NUMERIC_PRECISION);
382-   if (cinfo->scale < 0)
383-     {
384-       cinfo->scale = 0;
385-     }
386-   cinfo->prec = DB_MAX_NUMERIC_PRECISION;
387- }
388-      break;
389-
390-    case PT_TYPE_SET:
391-    case PT_TYPE_MULTISET:
392:    case PT_TYPE_SEQUENCE:
393-      /* NEVER try to fix set types */
394-      is_compatible = true;
395-      break;
396-    case PT_TYPE_NONE:
397-      is_compatible = false;
398-      break;
399-
400-    default:
401-      is_compatible = true;
402-      cinfo->type_enum = common_type;
403-      break;
404-    }
405-
406-  return is_compatible;
407-}
408-
409-/* pt_values_query_to_compatible_cast () - cast select list with cinfo
410- *   return:
411- *   parser(in):
412- *   node(in):
--
1025-  switch (arg_type)
1026-    {
1027-    case PT_TYPE_INTEGER:
1028-    case PT_TYPE_BIGINT:
1029-    case PT_TYPE_FLOAT:
1030-    case PT_TYPE_DOUBLE:
1031-    case PT_TYPE_SMALLINT:
1032-    case PT_TYPE_MONETARY:
1033-    case PT_TYPE_NUMERIC:
1034-      switch (cast_type)
1035- {
1036- case PT_TYPE_BIT:
1037- case PT_TYPE_VARBIT:
1038- case PT_TYPE_DATE:
1039-   /* allow numeric to TIME and TIMESTAMP conversions */
1040- case PT_TYPE_DATETIME:
1041- case PT_TYPE_DATETIMETZ:
1042- case PT_TYPE_DATETIMELTZ:
1043- case PT_TYPE_SET:
1044- case PT_TYPE_MULTISET:
1045: case PT_TYPE_SEQUENCE:
1046- case PT_TYPE_BLOB:
1047- case PT_TYPE_CLOB:
1048- case PT_TYPE_OBJECT:
1049-   cast_is_valid = PT_CAST_INVALID;
1050-   break;
1051- default:
1052-   break;
1053- }
1054-      break;
1055-    case PT_TYPE_DATE:
1056-      switch (cast_type)
1057- {
1058- case PT_TYPE_INTEGER:
1059- case PT_TYPE_BIGINT:
1060- case PT_TYPE_FLOAT:
1061- case PT_TYPE_DOUBLE:
1062- case PT_TYPE_SMALLINT:
1063- case PT_TYPE_MONETARY:
1064- case PT_TYPE_NUMERIC:
1065- case PT_TYPE_BIT:
1066- case PT_TYPE_VARBIT:
1067- case PT_TYPE_TIME:
1068- case PT_TYPE_SET:
1069- case PT_TYPE_MULTISET:
1070: case PT_TYPE_SEQUENCE:
1071- case PT_TYPE_BLOB:
1072- case PT_TYPE_CLOB:
1073- case PT_TYPE_OBJECT:
1074-   cast_is_valid = PT_CAST_INVALID;
1075-   break;
1076- default:
1077-   break;
1078- }
1079-      break;
1080-    case PT_TYPE_TIME:
1081-      switch (cast_type)
1082- {
1083- case PT_TYPE_INTEGER:
1084- case PT_TYPE_BIGINT:
1085- case PT_TYPE_FLOAT:
1086- case PT_TYPE_DOUBLE:
1087- case PT_TYPE_SMALLINT:
1088- case PT_TYPE_MONETARY:
1089- case PT_TYPE_NUMERIC:
1090- case PT_TYPE_BIT:
1091- case PT_TYPE_VARBIT:
1092- case PT_TYPE_DATE:
1093- case PT_TYPE_SET:
1094- case PT_TYPE_MULTISET:
1095: case PT_TYPE_SEQUENCE:
1096- case PT_TYPE_BLOB:
1097- case PT_TYPE_CLOB:
1098- case PT_TYPE_OBJECT:
1099-   cast_is_valid = PT_CAST_INVALID;
1100-   break;
1101- case PT_TYPE_TIMESTAMP:
1102- case PT_TYPE_TIMESTAMPLTZ:
1103- case PT_TYPE_TIMESTAMPTZ:
1104- case PT_TYPE_DATETIME:
1105- case PT_TYPE_DATETIMELTZ:
1106- case PT_TYPE_DATETIMETZ:
1107-   cast_is_valid = PT_CAST_UNSUPPORTED;
1108-   break;
1109- default:
1110-   break;
1111- }
1112-      break;
1113-    case PT_TYPE_TIMESTAMP:
1114-    case PT_TYPE_TIMESTAMPTZ:
1115-    case PT_TYPE_TIMESTAMPLTZ:
1116-      switch (cast_type)
1117- {
1118- case PT_TYPE_INTEGER:
1119- case PT_TYPE_BIGINT:
1120- case PT_TYPE_FLOAT:
1121- case PT_TYPE_DOUBLE:
1122- case PT_TYPE_SMALLINT:
1123- case PT_TYPE_MONETARY:
1124- case PT_TYPE_NUMERIC:
1125- case PT_TYPE_BIT:
1126- case PT_TYPE_VARBIT:
1127- case PT_TYPE_SET:
1128- case PT_TYPE_MULTISET:
1129: case PT_TYPE_SEQUENCE:
1130- case PT_TYPE_BLOB:
1131- case PT_TYPE_CLOB:
1132- case PT_TYPE_OBJECT:
1133-   cast_is_valid = PT_CAST_INVALID;
1134-   break;
1135- default:
1136-   break;
1137- }
1138-      break;
1139-    case PT_TYPE_DATETIME:
1140-    case PT_TYPE_DATETIMETZ:
1141-    case PT_TYPE_DATETIMELTZ:
1142-      switch (cast_type)
1143- {
1144- case PT_TYPE_INTEGER:
1145- case PT_TYPE_FLOAT:
1146- case PT_TYPE_DOUBLE:
1147- case PT_TYPE_SMALLINT:
1148- case PT_TYPE_MONETARY:
1149- case PT_TYPE_NUMERIC:
1150- case PT_TYPE_BIT:
1151- case PT_TYPE_VARBIT:
1152- case PT_TYPE_SET:
1153- case PT_TYPE_MULTISET:
1154: case PT_TYPE_SEQUENCE:
1155- case PT_TYPE_BLOB:
1156- case PT_TYPE_CLOB:
1157- case PT_TYPE_OBJECT:
1158-   cast_is_valid = PT_CAST_INVALID;
1159-   break;
1160- default:
1161-   break;
1162- }
1163-      break;
1164-    case PT_TYPE_CHAR:
1165-    case PT_TYPE_VARCHAR:
1166-    case PT_TYPE_NCHAR:
1167-    case PT_TYPE_VARNCHAR:
1168-      if ((PT_IS_NATIONAL_CHAR_STRING_TYPE (arg_type) && PT_IS_SIMPLE_CHAR_STRING_TYPE (cast_type))
1169-   || (PT_IS_SIMPLE_CHAR_STRING_TYPE (arg_type) && PT_IS_NATIONAL_CHAR_STRING_TYPE (cast_type)))
1170- {
1171-   cast_is_valid = PT_CAST_INVALID;
1172-   break;
1173- }
1174-
1175-      switch (cast_type)
1176- {
1177- case PT_TYPE_SET:
1178- case PT_TYPE_MULTISET:
1179: case PT_TYPE_SEQUENCE:
1180-   cast_is_valid = PT_CAST_UNSUPPORTED;
1181-   break;
1182- case PT_TYPE_OBJECT:
1183-   cast_is_valid = PT_CAST_INVALID;
1184-   break;
1185- default:
1186-   break;
1187- }
1188-      break;
1189-    case PT_TYPE_BIT:
1190-    case PT_TYPE_VARBIT:
1191-      switch (cast_type)
1192- {
1193- case PT_TYPE_INTEGER:
1194- case PT_TYPE_BIGINT:
1195- case PT_TYPE_FLOAT:
1196- case PT_TYPE_DOUBLE:
1197- case PT_TYPE_SMALLINT:
1198- case PT_TYPE_MONETARY:
1199- case PT_TYPE_NUMERIC:
1200- case PT_TYPE_DATE:
1201- case PT_TYPE_TIME:
1202- case PT_TYPE_TIMESTAMP:
1203- case PT_TYPE_TIMESTAMPTZ:
1204- case PT_TYPE_TIMESTAMPLTZ:
1205- case PT_TYPE_DATETIME:
1206- case PT_TYPE_DATETIMELTZ:
1207- case PT_TYPE_DATETIMETZ:
1208- case PT_TYPE_SET:
1209- case PT_TYPE_MULTISET:
1210: case PT_TYPE_SEQUENCE:
1211- case PT_TYPE_OBJECT:
1212-   cast_is_valid = PT_CAST_INVALID;
1213-   break;
1214- case PT_TYPE_CLOB:
1215-   cast_is_valid = PT_CAST_UNSUPPORTED;
1216-   break;
1217- default:
1218-   break;
1219- }
1220-      break;
1221-    case PT_TYPE_OBJECT:
1222-      /* some functions like DECODE, CASE perform wrap with CAST, allow it */
1223-      if (!PT_EXPR_INFO_IS_FLAGED (node, PT_EXPR_INFO_CAST_SHOULD_FOLD))
1224- {
1225-   cast_is_valid = PT_CAST_UNSUPPORTED;
1226- }
1227-      break;
1228-    case PT_TYPE_SET:
1229-    case PT_TYPE_MULTISET:
1230:    case PT_TYPE_SEQUENCE:
1231-      switch (cast_type)
1232- {
1233- case PT_TYPE_INTEGER:
1234- case PT_TYPE_BIGINT:
1235- case PT_TYPE_FLOAT:
1236- case PT_TYPE_DOUBLE:
1237- case PT_TYPE_SMALLINT:
1238- case PT_TYPE_MONETARY:
1239- case PT_TYPE_NUMERIC:
1240- case PT_TYPE_BIT:
1241- case PT_TYPE_VARBIT:
1242- case PT_TYPE_DATE:
1243- case PT_TYPE_TIME:
1244- case PT_TYPE_TIMESTAMP:
1245- case PT_TYPE_TIMESTAMPTZ:
1246- case PT_TYPE_TIMESTAMPLTZ:
1247- case PT_TYPE_DATETIME:
1248- case PT_TYPE_DATETIMETZ:
1249- case PT_TYPE_DATETIMELTZ:
1250- case PT_TYPE_BLOB:
--
1490-      i_attr->type_enum = pt_db_to_type_enum (db_attribute_type (attributes));
1491-      switch (i_attr->type_enum)
1492- {
1493- case PT_TYPE_OBJECT:
1494-   cls = db_domain_class (db_attribute_domain (attributes));
1495-   if (cls)
1496-     {
1497-       name = pt_name (parser, db_get_class_name (cls));
1498-       name->info.name.meta_class = PT_CLASS;
1499-       name->info.name.db_object = cls;
1500-       name->info.name.spec_id = (UINTPTR) name;
1501-       i_attr->data_type = typ = parser_new_node (parser, PT_DATA_TYPE);
1502-       if (typ)
1503-  {
1504-    typ->info.data_type.entity = name;
1505-  }
1506-     }
1507-   break;
1508-
1509- case PT_TYPE_SET:
1510: case PT_TYPE_SEQUENCE:
1511- case PT_TYPE_MULTISET:
1512-   types = NULL;
1513-   dom = db_domain_set (db_attribute_domain (attributes));
1514-   while (dom)
1515-     {
1516-       typ = pt_domain_to_data_type (parser, dom);
1517-       if (typ)
1518-  {
1519-    typ->next = types;
1520-  }
1521-       types = typ;
1522-       dom = db_domain_next (dom);
1523-     }
1524-   i_attr->data_type = types;
1525-   break;
1526-
1527- default:
1528-   dom = attributes->domain;
1529-   typ = pt_domain_to_data_type (parser, dom);
1530-   i_attr->data_type = typ;
--
2033-
2034-  if (!d_col || !s_col || !PT_IS_COLLECTION_TYPE (d_col->type_enum))
2035-    {
2036-      return 0;
2037-    }
2038-
2039-  /* NULL is assignable to any class type */
2040-  if (s_col->type_enum == PT_TYPE_NA || s_col->type_enum == PT_TYPE_NULL)
2041-    {
2042-      return 1;
2043-    }
2044-
2045-  /* make sure we are dealing only with collection types */
2046-  if (!PT_IS_COLLECTION_TYPE (s_col->type_enum))
2047-    {
2048-      return 0;
2049-    }
2050-
2051-  /* can't assign a multiset or a sequence to a set, or a multiset to a sequence */
2052-  if (((d_col->type_enum == PT_TYPE_SET)
2053:       && ((s_col->type_enum == PT_TYPE_MULTISET) || (s_col->type_enum == PT_TYPE_SEQUENCE)))
2054:      || ((d_col->type_enum == PT_TYPE_SEQUENCE) && (s_col->type_enum == PT_TYPE_MULTISET)))
2055-    {
2056-      assignable = 0;
2057-    }
2058-  else if (!d_col->data_type)
2059-    {
2060-      /* the wildcard set (set of anything) can be assigned a set of any type. */
2061-      assignable = 1;
2062-    }
2063-  else if (!s_col->data_type)
2064-    {
2065-      /* in this case, we have a wild card set being assigned to a non-wildcard set. */
2066-      assignable = 0;
2067-    }
2068-  else
2069-    {
2070-      /* Check to see that every type in the source collection is in the destination collection.  That is, the source
2071-       * types must be a subset of the destination types.  There is no coercion allowed. */
2072-      PT_NODE *st, *dt;
2073-      int found;
2074-
--
9410-    case PT_TYPE_SMALLINT:
9411-    case PT_TYPE_INTEGER:
9412-    case PT_TYPE_BIGINT:
9413-    case PT_TYPE_NUMERIC:
9414-    case PT_TYPE_FLOAT:
9415-    case PT_TYPE_DOUBLE:
9416-    case PT_TYPE_CHAR:
9417-    case PT_TYPE_VARCHAR:
9418-    case PT_TYPE_DATE:
9419-    case PT_TYPE_TIME:
9420-    case PT_TYPE_DATETIME:
9421-    case PT_TYPE_TIMESTAMP:
9422-    case PT_TYPE_RESULTSET:
9423-      return true;
9424-
9425-    case PT_TYPE_OBJECT:
9426-      return pt_is_defined_class (parser, data_type);
9427-
9428-    case PT_TYPE_SET:
9429-    case PT_TYPE_MULTISET:
9430:    case PT_TYPE_SEQUENCE:
9431-      {
9432- PT_NODE *dt;
9433- for (dt = data_type; dt; dt = dt->next)
9434-   {
9435-     if (!pt_is_defined_class (parser, dt))
9436-       {
9437-  return false;
9438-       }
9439-   }
9440-      }
9441-      return true;
9442-
9443-    default:
9444-      return false;
9445-    }
9446-}
9447-
9448-static const char *
9449-pt_get_type_name (PT_TYPE_ENUM type_enum, PT_NODE * data_type)
9450-{


```

```cpp
type_checking.c
6425- *   return:
6426- *   type_enum(in):
6427- */
6428-static bool
6429-pt_is_symmetric_type (const PT_TYPE_ENUM type_enum)
6430-{
6431-  switch (type_enum)
6432-    {
6433-    case PT_TYPE_INTEGER:
6434-    case PT_TYPE_BIGINT:
6435-    case PT_TYPE_FLOAT:
6436-    case PT_TYPE_DOUBLE:
6437-    case PT_TYPE_NUMERIC:
6438-    case PT_TYPE_SMALLINT:
6439-    case PT_TYPE_MONETARY:
6440-
6441-    case PT_TYPE_LOGICAL:
6442-
6443-    case PT_TYPE_SET:
6444-    case PT_TYPE_MULTISET:
6445:    case PT_TYPE_SEQUENCE:
6446-    case PT_TYPE_OBJECT:
6447-
6448-    case PT_TYPE_VARCHAR:
6449-    case PT_TYPE_CHAR:
6450-    case PT_TYPE_VARNCHAR:
6451-    case PT_TYPE_NCHAR:
6452-    case PT_TYPE_VARBIT:
6453-    case PT_TYPE_BIT:
6454-      return true;
6455-
6456-    default:
6457-      return false;
6458-    }
6459-}
6460-
6461-/*
6462- * pt_is_symmetric_op () -
6463- *   return:
6464- *   op(in):
6465- */
--
7227-   if (col)
7228-     {
7229-       col->type_enum = PT_TYPE_NULL;
7230-       subq->list = parser_append_node (subq->list, col);
7231-     }
7232-   else
7233-     {
7234-       PT_ERRORm (parser, node, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_OUT_OF_MEMORY);
7235-       return NULL;
7236-     }
7237- }
7238-
7239-      /* rewrite FROM list */
7240-      set = parser_new_node (parser, PT_VALUE);
7241-      spec = parser_new_node (parser, PT_SPEC);
7242-      if (set && spec)
7243- {
7244-   parser_free_tree (parser, subq->from);
7245-   subq->from = NULL;
7246-
7247:   set->type_enum = PT_TYPE_SEQUENCE;
7248-
7249-   spec->info.spec.id = (UINTPTR) spec;
7250-   spec->info.spec.derived_table = set;
7251-   spec->info.spec.derived_table_type = PT_IS_SET_EXPR;
7252-
7253-   /* set line number to dummy class, dummy attr */
7254-   spec->info.spec.range_var = pt_name (parser, "av6749");
7255-   spec->info.spec.as_attr_list = pt_name (parser, "av_1");
7256-
7257-   if (spec->info.spec.as_attr_list)
7258-     {
7259-       PT_NAME_INFO_SET_FLAG (spec->info.spec.as_attr_list, PT_NAME_GENERATED_DERIVED_SPEC);
7260-     }
7261-   subq->from = spec;
7262- }
7263-      else
7264- {
7265-   PT_ERRORm (parser, node, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_OUT_OF_MEMORY);
7266-   return NULL;
7267- }
--
11159-     default:
11160-       common_type = PT_TYPE_NONE;
11161-       break;
11162-     }
11163-   break;
11164-
11165- case PT_TYPE_OBJECT:
11166-   switch (arg2_type)
11167-     {
11168-     case PT_TYPE_OBJECT:
11169-       common_type = PT_TYPE_OBJECT;
11170-       break;
11171-     default:
11172-       common_type = PT_TYPE_NONE;
11173-       break;
11174-     }
11175-   break;
11176-
11177- case PT_TYPE_SET:
11178- case PT_TYPE_MULTISET:
11179: case PT_TYPE_SEQUENCE:
11180-   switch (arg2_type)
11181-     {
11182-     case PT_TYPE_SET:
11183-     case PT_TYPE_MULTISET:
11184:     case PT_TYPE_SEQUENCE:
11185-       common_type = PT_TYPE_MULTISET;
11186-       break;
11187-     default:
11188-       common_type = PT_TYPE_NONE;
11189-       break;
11190-     }
11191-   break;
11192-
11193- case PT_TYPE_LOGICAL:
11194-   switch (arg2_type)
11195-     {
11196-     case PT_TYPE_LOGICAL:
11197-       common_type = PT_TYPE_LOGICAL;
11198-       break;
11199-     case PT_TYPE_DOUBLE:
11200-       common_type = PT_TYPE_DOUBLE;
11201-       break;
11202-     case PT_TYPE_NUMERIC:
11203-       common_type = PT_TYPE_NUMERIC;
11204-       break;
--
11290-
11291-  if (pt_is_op_hv_late_bind (op) && (t1 == PT_TYPE_MAYBE || t2 == PT_TYPE_MAYBE))
11292-    {
11293-      result_type = PT_TYPE_MAYBE;
11294-    }
11295-  else
11296-    {
11297-      result_type = pt_common_type (t1, t2);
11298-    }
11299-
11300-  switch (op)
11301-    {
11302-    case PT_DIVIDE:
11303-      if (oracle_compat_number && PT_IS_DISCRETE_NUMBER_TYPE (t1) && PT_IS_DISCRETE_NUMBER_TYPE (t2))
11304- {
11305-   result_type = PT_TYPE_NUMERIC;
11306- }
11307-      break;
11308-    case PT_MINUS:
11309-    case PT_TIMES:
11310:      if (result_type == PT_TYPE_SEQUENCE)
11311- {
11312-   result_type = PT_TYPE_MULTISET;
11313- }
11314-      else if ((PT_IS_STRING_TYPE (t1) && PT_IS_NUMERIC_TYPE (t2))
11315-        || (PT_IS_NUMERIC_TYPE (t1) && PT_IS_STRING_TYPE (t2)))
11316- {
11317-   /* + and - have their own way of handling this situation */
11318-   return PT_TYPE_NONE;
11319- }
11320-      break;
11321-    case PT_SUPERSET:
11322-    case PT_SUPERSETEQ:
11323-    case PT_SUBSET:
11324-    case PT_SUBSETEQ:
11325:      if (result_type == PT_TYPE_SEQUENCE)
11326- {
11327-   result_type = PT_TYPE_NONE;
11328- }
11329-      break;
11330-    case PT_IFNULL:
11331-      if (result_type == PT_TYPE_MAYBE)
11332- {
11333-   result_type = PT_TYPE_VARCHAR;
11334- }
11335-      break;
11336-    case PT_COALESCE:
11337-      if (t1 == PT_TYPE_MAYBE)
11338- {
11339-   if (t2 == PT_TYPE_MAYBE || t2 == PT_TYPE_NULL)
11340-     {
11341-       result_type = PT_TYPE_MAYBE;
11342-     }
11343-   else
11344-     {
11345-       result_type = t2;
--
13861-       rTyp = PT_TYPE_DOUBLE;
13862-     }
13863-   typ = pt_type_enum_to_db (rTyp);
13864-   domain = tp_domain_resolve_default (typ);
13865- }
13866-
13867-      if (typ1 == DB_TYPE_NULL || typ2 == DB_TYPE_NULL)
13868- {
13869-   bool check_empty_string;
13870-
13871-   check_empty_string = ((prm_get_bool_value (PRM_ID_ORACLE_STYLE_EMPTY_STRING)) ? true : false);
13872-   if (!check_empty_string || op != PT_PLUS || !PT_IS_STRING_TYPE (rTyp))
13873-     {
13874-       db_make_null (result); /* NULL arith_op any = NULL */
13875-       break;
13876-     }
13877- }
13878-
13879-      /* screen out cases we don't evaluate */
13880-      if (!PT_IS_NUMERIC_TYPE (rTyp) && !PT_IS_STRING_TYPE (rTyp) && rTyp != PT_TYPE_SET && rTyp != PT_TYPE_MULTISET
13881:   && rTyp != PT_TYPE_SEQUENCE && !PT_IS_DATE_TIME_TYPE (rTyp))
13882- {
13883-   return 0;
13884- }
13885-
13886-      /* don't coerce dates and times */
13887-      if (!TP_IS_DATE_OR_TIME_TYPE (typ)
13888-   && !((typ == DB_TYPE_INTEGER || typ == DB_TYPE_BIGINT) && (TP_IS_DATE_OR_TIME_TYPE (typ1) && typ1 == typ2)))
13889- {
13890-   /* coerce operands to data type of result */
13891-   if (typ1 != typ)
13892-     {
13893-       db_make_null (&tmp_val);
13894-       /* force explicit cast ; scenario : INSERT INTO t VALUE(''1''+?) , USING 10, column is INTEGER */
13895-       if (tp_value_cast (arg1, &tmp_val, domain, false) != DOMAIN_COMPATIBLE)
13896-  {
13897-    PT_ERRORmf2 (parser, o1, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_CANT_COERCE_TO,
13898-          parser_print_tree (parser, o1), pt_show_type_enum (rTyp));
13899-
13900-    return 0;
13901-  }
--
18948-     e.g.) (col1,1) in (..) and col1=1 -> qo_reduce_equality_terms() -> function type (1,1) -> value type (1,1) */
18949-  if (pt_is_set_type (func) && func->info.function.function_type == F_SEQUENCE)
18950-    {
18951-      PT_NODE *func_arg = func->info.function.arg_list;
18952-      bool is_const_multi_col = true;
18953-
18954-      for ( /* none */ ; func_arg; func_arg = func_arg->next)
18955- {
18956-   if (func_arg && func_arg->node_type != PT_VALUE)
18957-     {
18958-       is_const_multi_col = false;
18959-       break;
18960-     }
18961- }
18962-      if (is_const_multi_col)
18963- {
18964-   func->node_type = PT_VALUE;
18965-   func_arg = func->info.function.arg_list;
18966-   memset (&(func->info), 0, sizeof (func->info));
18967-   func->info.value.data_value.set = func_arg;
18968:   func->type_enum = PT_TYPE_SEQUENCE;
18969- }
18970-    }
18971-
18972-  if (func->flag.do_not_fold)
18973-    {
18974-      return func;
18975-    }
18976-
18977-  if (func->info.function.function_type == PT_COUNT)
18978-    {
18979-      parser_node *arg_list = func->info.function.arg_list;
18980-      /* do special constant folding; COUNT(1), COUNT(?), COUNT(:x), ... -> COUNT(*) */
18981-      if (pt_is_const (arg_list) && !PT_IS_NULL_NODE (arg_list))
18982- {
18983-   PT_MISC_TYPE all_or_distinct;
18984-   all_or_distinct = func->info.function.all_or_distinct;
18985-   if (func->info.function.function_type == PT_COUNT && all_or_distinct != PT_DISTINCT)
18986-     {
18987-       func->info.function.function_type = PT_COUNT_STAR;
18988-       parser_free_tree (parser, arg_list);
--
19599-  temp->info.value.db_value_is_in_workspace = 0;
19600-  parser_free_node (parser, temp);
19601-       }
19602-   }
19603-      }
19604-      break;
19605-
19606-    case PT_FUNCTION:
19607-      if (src == dest)
19608- {
19609-   switch (src->info.function.function_type)
19610-     {
19611-     case F_MULTISET:
19612-     case F_SEQUENCE:
19613-       switch (desired_type)
19614-  {
19615-  case PT_TYPE_SET:
19616-    dest->info.function.function_type = F_SET;
19617-    dest->type_enum = PT_TYPE_SET;
19618-    break;
19619:  case PT_TYPE_SEQUENCE:
19620-    dest->info.function.function_type = F_SEQUENCE;
19621:    dest->type_enum = PT_TYPE_SEQUENCE;
19622-    break;
19623-  case PT_TYPE_MULTISET:
19624-    dest->info.function.function_type = F_MULTISET;
19625-    dest->type_enum = PT_TYPE_MULTISET;
19626-    break;
19627-  default:
19628-    break;
19629-  }
19630-       break;
19631-
19632-     case F_TABLE_MULTISET:
19633-     case F_TABLE_SEQUENCE:
19634-       switch (desired_type)
19635-  {
19636-  case PT_TYPE_SET:
19637-    dest->info.function.function_type = F_TABLE_SET;
19638-    dest->type_enum = PT_TYPE_SET;
19639-    break;
19640:  case PT_TYPE_SEQUENCE:
19641-    dest->info.function.function_type = F_TABLE_SEQUENCE;
19642:    dest->type_enum = PT_TYPE_SEQUENCE;
19643-    break;
19644-  case PT_TYPE_MULTISET:
19645-    dest->info.function.function_type = F_TABLE_MULTISET;
19646-    dest->type_enum = PT_TYPE_MULTISET;
19647-    break;
19648-  default:
19649-    break;
19650-  }
19651-       break;
19652-
19653-     default:
19654-       break;
19655-     }
19656- }
19657-      break;
19658-
19659-    default:
19660-      err = ((pt_common_type (desired_type, src->type_enum) == PT_TYPE_NONE) ? ER_IT_INCOMPATIBLE_DATATYPE : NO_ERROR);


```

```cpp
19661-      break;
19662-    }
--
19787-pt_compare_bounds_to_value (PARSER_CONTEXT * parser, PT_NODE * expr, PT_OP_TYPE op, PT_TYPE_ENUM lhs_type,
19788-       DB_VALUE * rhs_val, PT_TYPE_ENUM rhs_type)
19789-{
19790-  bool lhs_less = false;
19791-  bool lhs_greater = false;
19792-  bool always_false = false;
19793-  bool always_false_due_to_null = false;
19794-  bool always_true = false;
19795-  PT_NODE *result = expr;
19796-  double dtmp;
19797-
19798-  /* we can't determine anything if the types are the same */
19799-  if (lhs_type == rhs_type)
19800-    {
19801-      return result;
19802-    }
19803-
19804-  /* check if op is always false due to null */
19805-  if (op != PT_IS_NULL && op != PT_IS_NOT_NULL)
19806-    {
19807:      if (DB_IS_NULL (rhs_val) && rhs_type != PT_TYPE_SET && rhs_type != PT_TYPE_SEQUENCE
19808-   && rhs_type != PT_TYPE_MULTISET)
19809- {
19810-   always_false_due_to_null = true;
19811-   goto end;
19812- }
19813-    }
19814-
19815-  /* we only allow PT_EQ, PT_GT, PT_GE, PT_LT, PT_LE. */
19816-  if (op != PT_EQ && op != PT_GT && op != PT_GE && op != PT_LT && op != PT_LE)
19817-    {
19818-      return result;
19819-    }
19820-
19821-  /* we need to extend the following to compare dates and times, but probably not until we make the ranges of PT_* and
19822-   * DB_* the same */
19823-  switch (lhs_type)
19824-    {
19825-    case PT_TYPE_SMALLINT:
19826-      switch (rhs_type)
19827- {


```

```cpp
parse_tree_cl.c
4121-    case PT_TYPE_MONETARY:
4122-      return "monetary";
4123-    case PT_TYPE_JSON:
4124-      return "json";
4125-    case PT_TYPE_MAYBE:
4126-      return "uncertain";
4127-
4128-    case PT_TYPE_NA:
4129-      return "na";
4130-    case PT_TYPE_NULL:
4131-      return "null";
4132-    case PT_TYPE_STAR:
4133-      return "*";
4134-
4135-    case PT_TYPE_OBJECT:
4136-      return "object";
4137-    case PT_TYPE_SET:
4138-      return "set";
4139-    case PT_TYPE_MULTISET:
4140-      return "multiset";
4141:    case PT_TYPE_SEQUENCE:
4142-      return "sequence";
4143-    case PT_TYPE_RESULTSET:
4144-      return "cursor";
4145-    case PT_TYPE_COMPOUND:
4146-      return "unknown";
4147-
4148-    case PT_TYPE_BLOB:
4149-      return "blob";
4150-    case PT_TYPE_CLOB:
4151-      return "clob";
4152-    case PT_TYPE_ELO:
4153-      return "*elo*";
4154-
4155-    case PT_TYPE_ENUMERATION:
4156-      return "enum";
4157-
4158-    case PT_TYPE_MAX:
4159-    default:
4160-      return "unknown";
4161-    }
--
4579-  }
4580-
4581-       node->info.query.q.select.list = parser_copy_tree_list (parser, spec->info.spec.as_attr_list);
4582-       node->info.query.q.select.from = spec;
4583-     }
4584-   else
4585-     {
4586-       /* remove unnecessary ORDER BY clause */
4587-       if (node->info.query.order_by && !node->info.query.q.select.connect_by)
4588-  {
4589-    parser_free_tree (parser, node->info.query.order_by);
4590-    node->info.query.order_by = NULL;
4591-  }
4592-     }
4593-
4594-   /* create parentheses expr set value */
4595-   val = parser_new_node (parser, PT_VALUE);
4596-   if (val)
4597-     {
4598-       val->info.value.data_value.set = node->info.query.q.select.list;
4599:       val->type_enum = PT_TYPE_SEQUENCE;
4600-     }
4601-   node->info.query.q.select.list = val;
4602- }
4603-      else
4604- {
4605-   if (pt_length_of_select_list (list, EXCLUDE_HIDDEN_COLUMNS) == 1 && pt_is_set_type (list))
4606-     {   /* one column */
4607-       col = list;
4608-       next = list->next; /* save hidden column */
4609-       col->next = NULL;
4610-
4611-       if (list->node_type == PT_VALUE)
4612-  {
4613-    list = col->info.value.data_value.set;
4614-    col->info.value.data_value.set = NULL;
4615-    parser_free_tree (parser, col);
4616-  }
4617-       else if (list->node_type == PT_FUNCTION)
4618-  {
4619-    list = col->info.function.arg_list;
--
8606-   {
8607-     sprintf (buf, "(%d)", precision);
8608-     q = pt_append_nulstring (parser, q, buf);
8609-   }
8610-      }
8611-      break;
8612-    case PT_TYPE_DOUBLE:
8613-      q = pt_append_nulstring (parser, q, pt_show_type_enum (p->type_enum));
8614-      break;
8615-    case PT_TYPE_ENUMERATION:
8616-      q = pt_append_nulstring (parser, q, pt_show_type_enum (p->type_enum));
8617-      q = pt_append_nulstring (parser, q, "(");
8618-      r1 = pt_print_bytes_l (parser, p->info.data_type.enumeration);
8619-      q = pt_append_varchar (parser, q, r1);
8620-      q = pt_append_nulstring (parser, q, ")");
8621-      show_collation = true;
8622-      break;
8623-
8624-    case PT_TYPE_SET:
8625-    case PT_TYPE_MULTISET:
8626:    case PT_TYPE_SEQUENCE:
8627-      q = pt_append_nulstring (parser, q, pt_show_type_enum (p->type_enum));
8628-
8629-      /* not to print data_type node for SET data types with empty domain */
8630-      if (p->data_type && p->data_type->info.data_type.precision != TP_FLOATING_PRECISION_VALUE)
8631- {
8632-   r1 = pt_print_bytes_l (parser, p->data_type);
8633-   q = pt_append_nulstring (parser, q, "(");
8634-   q = pt_append_varchar (parser, q, r1);
8635-   q = pt_append_nulstring (parser, q, ")");
8636- }
8637-      break;
8638-
8639-    case PT_TYPE_TABLE_COLUMN:
8640-      r1 = pt_print_bytes (parser, p->info.data_type.table_column);
8641-      q = pt_append_varchar (parser, q, r1);
8642-      q = pt_append_nulstring (parser, q, "%type");
8643-
8644-      break;
8645-
8646-    default:
--
15980-  return b;
15981-}
15982-#endif
15983-
15984-/* VALUE */
15985-/*
15986- * pt_apply_value () -
15987- *   return:
15988- *   parser(in):
15989- *   p(in):
15990- *   g(in):
15991- *   arg(in):
15992- */
15993-static PT_NODE *
15994-pt_apply_value (PARSER_CONTEXT * parser, PT_NODE * p, void *arg)
15995-{
15996-  switch (p->type_enum)
15997-    {
15998-    case PT_TYPE_SET:
15999-    case PT_TYPE_MULTISET:
16000:    case PT_TYPE_SEQUENCE:
16001-      PT_APPLY_WALK (parser, p->info.value.data_value.set, arg);
16002-    default:
16003-      break;
16004-    }
16005-  return p;
16006-}
16007-
16008-/*
16009- * pt_init_value () -
16010- *   return:
16011- *   p(in):
16012- */
16013-static PT_NODE *
16014-pt_init_value (PT_NODE * p)
16015-{
16016-  p->info.value.host_var_index = -1;
16017-  return p;
16018-}
16019-
16020-/*
--
16210-   prt_coll_id = -1;
16211- }
16212-
16213-      prt_cs = (p->data_type != NULL) ? (INTL_CODESET) (p->data_type->info.data_type.units) : LANG_SYS_CODESET;
16214-
16215-      /* do not print charset introducer for NCHAR and VARNCHAR */
16216-      if ((p->info.value.print_charset == false
16217-    && !(parser->custom_print & (PT_CHARSET_COLLATE_FULL | PT_CHARSET_COLLATE_USER_ONLY)))
16218-   || (p->type_enum != PT_TYPE_CHAR && p->type_enum != PT_TYPE_VARCHAR)
16219-   || (prt_cs == LANG_SYS_CODESET && (parser->custom_print & PT_SUPPRESS_CHARSET_PRINT))
16220-   || (parser->custom_print & PT_CHARSET_COLLATE_USER_ONLY && p->info.value.has_cs_introducer == false))
16221- {
16222-   prt_cs = INTL_CODESET_NONE;
16223- }
16224-    }
16225-
16226-  switch (p->type_enum)
16227-    {
16228-    case PT_TYPE_SET:
16229-    case PT_TYPE_MULTISET:
16230:    case PT_TYPE_SEQUENCE:
16231-      if (p->spec_ident || ((parser->custom_print & PT_PRINT_SUPPRESS_FOR_DBLINK) && p->flag.print_in_value_for_dblink))
16232- {
16233-   /* this is tagged as an "in" clause right hand side Print it as a parenthesized list */
16234-   /* print_in_value_for_dblink is a flag as same meaning for dblink */
16235-   r1 = pt_print_bytes_l (parser, p->info.value.data_value.set);
16236-   q = pt_append_nulstring (parser, q, "(");
16237-   q = pt_append_varchar (parser, q, r1);
16238-   q = pt_append_nulstring (parser, q, ")");
16239- }
16240-      else
16241- {
16242:   if (p->type_enum != PT_TYPE_SEQUENCE && !(parser->custom_print & PT_PRINT_SUPPRESS_FOR_DBLINK))
16243-     {
16244-       q = pt_append_nulstring (parser, q, pt_show_type_enum (p->type_enum));
16245-     }
16246-   q = pt_append_nulstring (parser, q, "{");
16247-
16248-   if (p->info.value.data_value.set)
16249-     {
16250-       r1 = pt_print_bytes_l (parser, p->info.value.data_value.set);
16251-       q = pt_append_varchar (parser, q, r1);
16252-     }
16253-   q = pt_append_nulstring (parser, q, "}");
16254- }
16255-      break;
16256-
16257-    case PT_TYPE_LOGICAL:
16258-    case PT_TYPE_FLOAT:
16259-    case PT_TYPE_DOUBLE:
16260-    case PT_TYPE_NUMERIC:
16261-    case PT_TYPE_INTEGER:
16262-    case PT_TYPE_BIGINT:


```

```cpp
parse_dbi.c
1382- *   parser(in):
1383- *   s(in): domain string
1384- *   node(out):
1385- */
1386-void
1387-pt_string_to_data_type (PARSER_CONTEXT * parser, const char *s, PT_NODE * node)
1388-{
1389-  DB_DOMAIN *dom;
1390-
1391-  dom = pt_string_to_db_domain (s, NULL);
1392-  if (dom == NULL)
1393-    {
1394-      return;
1395-    }
1396-
1397-  node->type_enum = pt_db_to_type_enum (TP_DOMAIN_TYPE (dom));
1398-  switch (node->type_enum)
1399-    {
1400-    case PT_TYPE_OBJECT:
1401-    case PT_TYPE_SET:
1402:    case PT_TYPE_SEQUENCE:
1403-    case PT_TYPE_MULTISET:
1404-    case PT_TYPE_NUMERIC:
1405-    case PT_TYPE_BIT:
1406-    case PT_TYPE_VARBIT:
1407-    case PT_TYPE_CHAR:
1408-    case PT_TYPE_VARCHAR:
1409-    case PT_TYPE_NCHAR:
1410-    case PT_TYPE_VARNCHAR:
1411-      node->data_type = pt_domain_to_data_type (parser, dom);
1412-      break;
1413-    default:
1414-      break;
1415-    }
1416-}
1417-#endif /* ENABLE_UNUSED_FUNCTION */
1418-
1419-/*
1420- * pt_type_enum_to_db_domain_name() - returns string form of t's datatype
1421- *   return:  character string denoting datatype dt
1422- *   t(in): a PT_TYPE_ENUM
--
1488-      name = "monetary";
1489-      break;
1490-
1491-    case PT_TYPE_VARCHAR:
1492-      name = "char varying";
1493-      break;
1494-    case PT_TYPE_CHAR:
1495-      name = "char";
1496-      break;
1497-
1498-    case PT_TYPE_OBJECT:
1499-      name = "object";
1500-      break;
1501-
1502-    case PT_TYPE_SET:
1503-      name = "set";
1504-      break;
1505-    case PT_TYPE_MULTISET:
1506-      name = "multiset";
1507-      break;
1508:    case PT_TYPE_SEQUENCE:
1509-      name = "sequence";
1510-      break;
1511-
1512-    case PT_TYPE_NCHAR:
1513-      name = "nchar";
1514-      break;
1515-    case PT_TYPE_VARNCHAR:
1516-      name = "nchar varying";
1517-      break;
1518-    case PT_TYPE_BIT:
1519-      name = "bit";
1520-      break;
1521-    case PT_TYPE_VARBIT:
1522-      name = "bit varying";
1523-      break;
1524-
1525-    case PT_TYPE_BLOB:
1526-      name = "blob";
1527-      break;
1528-    case PT_TYPE_CLOB:
--
2375-    case PT_TYPE_VARCHAR:
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
2395:    case PT_TYPE_SEQUENCE:
2396-      db_type = DB_TYPE_SEQUENCE;
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
--
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
2668-    case DB_TYPE_SEQUENCE:
2669:      pt_type = PT_TYPE_SEQUENCE;
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
2689-      break;
--
3250-    case PT_TYPE_MULTISET:
3251-      multiset = db_set_create_multi (NULL, NULL);
3252-      if (multiset == NULL)
3253- {
3254-   PT_ERROR (parser, value,
3255-      msgcat_message (MSGCAT_CATALOG_CUBRID, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_OUT_OF_MEMORY));
3256-   return (DB_VALUE *) NULL;
3257- }
3258-
3259-      db_make_multiset (db_value, multiset);
3260-
3261-      if (pt_set_value_to_db (parser, &value->info.value.data_value.set, db_value, &value->data_type) == NULL)
3262- {
3263-   pr_clear_value (db_value);
3264-   return (DB_VALUE *) NULL;
3265- }
3266-
3267-      value->info.value.db_value_is_in_workspace = true;
3268-      break;
3269-
3270:    case PT_TYPE_SEQUENCE:
3271-      seq = db_seq_create (NULL, NULL, 0);
3272-      if (seq == NULL)
3273- {
3274-   PT_ERROR (parser, value,
3275-      msgcat_message (MSGCAT_CATALOG_CUBRID, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_OUT_OF_MEMORY));
3276-   return (DB_VALUE *) NULL;
3277- }
3278-
3279-      db_make_sequence (db_value, seq);
3280-
3281-      if (pt_seq_value_to_db (parser, value->info.value.data_value.set, db_value, &value->data_type) == NULL)
3282- {
3283-   pr_clear_value (db_value);
3284-   return (DB_VALUE *) NULL;
3285- }
3286-
3287-      value->info.value.db_value_is_in_workspace = true;
3288-      break;
3289-
3290-    case PT_TYPE_INTEGER:


```

```cpp
csql_grammar.y
2026-  {{ DBG_TRACE_GRAMMAR(stmt_, | DATA_TYPE___ data_type);
2027-
2028-   PT_NODE *dt, *set_dt;
2029-   PT_TYPE_ENUM typ;
2030-
2031-   typ = (PT_TYPE_ENUM) TO_NUMBER (CONTAINER_AT_0 ($2));
2032-   dt = CONTAINER_AT_1 ($2);
2033-
2034-   if (!dt)
2035-     {
2036-       dt = parser_new_node (this_parser, PT_DATA_TYPE);
2037-       if (dt)
2038-         {
2039-    dt->type_enum = typ;
2040-    dt->data_type = NULL;
2041-         }
2042-     }
2043-   else
2044-     {
2045-       if ((typ == PT_TYPE_SET) ||
2046:    (typ == PT_TYPE_MULTISET) || (typ == PT_TYPE_SEQUENCE))
2047-         {
2048-    set_dt = parser_new_node (this_parser, PT_DATA_TYPE);
2049-    if (set_dt)
2050-      {
2051-        set_dt->type_enum = typ;
2052-        set_dt->data_type = dt;
2053-        dt = set_dt;
2054-      }
2055-         }
2056-     }
2057-
2058-   if (PT_HAS_COLLATION (typ))
2059-     {
2060-       dt->info.data_type.units = LANG_SYS_CODESET;
2061-       dt->info.data_type.collation_id = LANG_SYS_COLLATION;
2062-     }
2063-
2064-   $$ = dt;
2065-   PARSER_SAVE_ERR_CONTEXT ($$, @$.buffer_pos)
2066-
--
14668-
14669-   PT_NODE *node = $1;
14670-   if (node != NULL)
14671-     {
14672-       node->flag.is_alias_enabled_expr = 1;
14673-     }
14674-   $$ = node;
14675-   PARSER_SAVE_ERR_CONTEXT ($$, @$.buffer_pos)
14676-
14677-  DBG_PRINT}}
14678- ;
14679-
14680-alias_enabled_expression_
14681- : normal_expression opt_as_identifier %dprec 2
14682-  {{ DBG_TRACE_GRAMMAR(alias_enabled_expression_, : normal_expression opt_as_identifier);
14683-
14684-   PT_NODE *subq, *id;
14685-   PT_NODE *node = $1;
14686-   if (node->node_type == PT_VALUE && node->type_enum == PT_TYPE_EXPR_SET)
14687-     {
14688:       node->type_enum = PT_TYPE_SEQUENCE; /* for print out */
14689-       PT_ERRORf (this_parser, node,
14690-           "check syntax at %s, illegal parentheses set expression.",
14691-           pt_short_print (this_parser, node));
14692-     }
14693-   else if (PT_IS_QUERY_NODE_TYPE (node->node_type))
14694-     {
14695-       /* mark as single tuple query */
14696-       node->info.query.flag.single_tuple = 1;
14697-
14698-       if ((subq = pt_get_subquery_list (node)) && subq->next)
14699-         {
14700-    /* illegal multi-column subquery */
14701-    PT_ERRORm (this_parser, node, MSGCAT_SET_PARSER_SEMANTIC,
14702-        MSGCAT_SEMANTIC_NOT_SINGLE_COL);
14703-         }
14704-     }
14705-
14706-
14707-   id = $2;
14708-   if (id && id->node_type == PT_NAME)
--
17587-
17588-  DBG_PRINT}}
17589- | CAST
17590-  { push_msg(MSGCAT_SYNTAX_INVALID_CAST); }
17591-   '(' expression_ AS of_cast_data_type ')'
17592-  { pop_msg(); }
17593-  {{ DBG_TRACE_GRAMMAR(reserved_func, | CAST '(' expression_ AS of_cast_data_type ')' );
17594-
17595-   PT_NODE *expr = parser_make_expression (this_parser, PT_CAST, $4, NULL, NULL);
17596-   PT_TYPE_ENUM typ = TO_NUMBER (CONTAINER_AT_0 ($6));
17597-   PT_NODE *dt = CONTAINER_AT_1 ($6);
17598-   PT_NODE *set_dt;
17599-
17600-   if (!dt)
17601-     {
17602-       dt = parser_new_node (this_parser, PT_DATA_TYPE);
17603-       dt->type_enum = TO_NUMBER (CONTAINER_AT_0 ($6));
17604-       dt->data_type = NULL;
17605-     }
17606-   else if (typ == PT_TYPE_SET || typ == PT_TYPE_MULTISET
17607:     || typ == PT_TYPE_SEQUENCE)
17608-     {
17609-       set_dt = parser_new_node (this_parser, PT_DATA_TYPE);
17610-       set_dt->type_enum = typ;
17611-       set_dt->data_type = dt;
17612-       dt = set_dt;
17613-     }
17614-   if (dt->type_enum == PT_TYPE_ENUMERATION)
17615-     {
17616-       (void) pt_check_enum_data_type(this_parser, dt);
17617-     }
17618-
17619-   expr->info.expr.cast_type = dt;
17620-   $$ = expr;
17621-   PARSER_SAVE_ERR_CONTEXT ($$, @$.buffer_pos)
17622-
17623-  DBG_PRINT}}
17624- | CLASS '(' identifier ')'
17625-  {{ DBG_TRACE_GRAMMAR(reserved_func, | CLASS '(' identifier ')' );
17626-
17627-   $3->info.name.meta_class = PT_OID_ATTR;
--
19661-   bool found_paren_set_expr = false;
19662-   int lhs_cnt, rhs_cnt = 0;
19663-   bool found_match = false;
19664-
19665-   opd2 = $3;
19666-   e = parser_make_expression (this_parser, $2, $1, NULL, NULL);
19667-
19668-   if (e && !pt_has_error (this_parser))
19669-     {
19670-
19671-       e->info.expr.arg2 = opd2;
19672-       opd1 = e->info.expr.arg1;
19673-       op = e->info.expr.op;
19674-
19675-       /* convert parentheses set expr value into sequence */
19676-       if (opd1)
19677-         {
19678-    if (opd1->node_type == PT_VALUE &&
19679-        opd1->type_enum == PT_TYPE_EXPR_SET)
19680-      {
19681:        opd1->type_enum = PT_TYPE_SEQUENCE;
19682-        found_paren_set_expr = true;
19683-      }
19684-    else if (PT_IS_QUERY_NODE_TYPE (opd1->node_type))
19685-      {
19686-        if ((subq = pt_get_subquery_list (opd1)) && subq->next == NULL)
19687-          {
19688-     /* single-column subquery */
19689-          }
19690-        else
19691-          {
19692-     if (subq)
19693-       {
19694-         /* If not PT_TYPE_STAR */
19695-         pt_select_list_to_one_col (this_parser, opd1, true);
19696-       }
19697-     found_paren_set_expr = true;
19698-          }
19699-      }
19700-         }
19701-       if (opd2)
19702-         {
19703-    if (opd2->node_type == PT_VALUE &&
19704-        opd2->type_enum == PT_TYPE_EXPR_SET)
19705-      {
19706:        opd2->type_enum = PT_TYPE_SEQUENCE;
19707-        found_paren_set_expr = true;
19708-      }
19709-    else if (PT_IS_QUERY_NODE_TYPE (opd2->node_type))
19710-      {
19711-        if ((subq = pt_get_subquery_list (opd2)) && subq->next == NULL)
19712-          {
19713-     /* single-column subquery */
19714-          }
19715-        else
19716-          {
19717-     if (subq)
19718-       {
19719-         /* If not PT_TYPE_STAR */
19720-         pt_select_list_to_one_col (this_parser, opd2, true);
19721-       }
19722-     found_paren_set_expr = true;
19723-          }
19724-      }
19725-         }
19726-                              if (found_paren_set_expr == true)
--
19863-
19864-   PT_NODE *node = parser_make_expression (this_parser, $2, $1, NULL, NULL);
19865-   PT_NODE *t = CONTAINER_AT_1 ($3);
19866-   bool is_paren = (bool)TO_NUMBER (CONTAINER_AT_0 ($3));
19867-   int lhs_cnt, rhs_cnt = 0;
19868-   PT_NODE *v, *lhs, *rhs, *subq;
19869-   bool found_match = false;
19870-   bool lhs_found_paren_set_expr = false;
19871-   bool rhs_found_paren_set_expr = false;
19872-
19873-   PARSER_SAVE_ERR_CONTEXT (node, @$.buffer_pos)
19874-   if (node)
19875-     {
19876-       lhs = node->info.expr.arg1;
19877-       /* convert lhs parentheses set expr value into
19878-        * sequence value */
19879-       if (lhs)
19880-         {
19881-    if (lhs->node_type == PT_VALUE && lhs->type_enum == PT_TYPE_EXPR_SET)
19882-      {
19883:        lhs->type_enum = PT_TYPE_SEQUENCE;
19884-        lhs_found_paren_set_expr = true;
19885-      }
19886-    else if (PT_IS_QUERY_NODE_TYPE (lhs->node_type))
19887-      {
19888-        if ((subq = pt_get_subquery_list (lhs)) && subq->next == NULL)
19889-          {
19890-     /* single column subquery */
19891-          }
19892-        else
19893-          {
19894-     if (subq)
19895-                                          {
19896-                                            /* If not PT_TYPE_STAR */
19897-                                            pt_select_list_to_one_col (this_parser, lhs, true);
19898-                                          }
19899-                                        lhs_found_paren_set_expr = true;
19900-          }
19901-      }
19902-         }
19903-
--
19929-       if (is_paren == true)
19930-         {
19931-    rhs = rhs->info.value.data_value.set;
19932-         }
19933-       else if (rhs->node_type == PT_VALUE
19934-         && !(PT_IS_COLLECTION_TYPE (rhs->type_enum)
19935-       || rhs->type_enum == PT_TYPE_EXPR_SET))
19936-         {
19937-    PT_ERRORmf2 (this_parser, rhs, MSGCAT_SET_PARSER_SYNTAX,
19938-          MSGCAT_SYNTAX_ERROR_MSG1,
19939-          pt_show_binopcode ($2),
19940-          "SELECT or '('");
19941-         }
19942-
19943-       /* for each rhs elements, convert parentheses
19944-        * set expr value into sequence value */
19945-       for (t = rhs; t; t = t->next)
19946-         {
19947-    if (t->node_type == PT_VALUE && t->type_enum == PT_TYPE_EXPR_SET)
19948-      {
19949:        t->type_enum = PT_TYPE_SEQUENCE;
19950-        rhs_found_paren_set_expr = true;
19951-      }
19952-    else if (PT_IS_QUERY_NODE_TYPE (t->node_type))
19953-      {
19954-        if ((subq = pt_get_subquery_list (t)) && subq->next == NULL)
19955-          {
19956-     /* single column subquery */
19957-          }
19958-        else
19959-          {
19960-                                        if (subq)
19961-                                          {
19962-                                            /* If not PT_TYPE_STAR */
19963-                                            pt_select_list_to_one_col (this_parser, t, true);
19964-                                          }
19965-     rhs_found_paren_set_expr = true;
19966-          }
19967-      }
19968-         }
19969-       if (rhs_found_paren_set_expr && lhs_found_paren_set_expr)
--
21026-
21027-   $$ = $1;
21028-
21029-  DBG_PRINT}}
21030- ;
21031-
21032-data_type_list
21033- : data_type_list ',' data_type
21034-  {{ DBG_TRACE_GRAMMAR(data_type_list, : data_type_list ',' data_type);
21035-
21036-   PT_NODE *dt;
21037-   PT_TYPE_ENUM e;
21038-
21039-   e = TO_NUMBER (CONTAINER_AT_0 ($3));
21040-   dt = CONTAINER_AT_1 ($3);
21041-
21042-   if (dt)
21043-     {
21044-       if (e == PT_TYPE_SET ||
21045-    e == PT_TYPE_MULTISET ||
21046:    e == PT_TYPE_SEQUENCE)
21047-         {
21048-    csql_yyerror("nested data type definition");
21049-         }
21050-     }
21051-
21052-   if (!dt)
21053-     {
21054-       dt = parser_new_node (this_parser, PT_DATA_TYPE);
21055-       if (dt)
21056-         {
21057-    dt->type_enum = e;
21058-    dt->data_type = NULL;
21059-         }
21060-     }
21061-
21062-   $$ = parser_make_link ($1, dt);
21063-   PARSER_SAVE_ERR_CONTEXT ($$, @$.buffer_pos)
21064-
21065-  DBG_PRINT}}
21066- | data_type
21067-  {{ DBG_TRACE_GRAMMAR(data_type_list, | data_type);
21068-
21069-   PT_NODE *dt;
21070-   PT_TYPE_ENUM e;
21071-
21072-   e = TO_NUMBER (CONTAINER_AT_0 ($1));
21073-   dt = CONTAINER_AT_1 ($1);
21074-
21075-   if (dt)
21076-     {
21077-       if (e == PT_TYPE_SET ||
21078-    e == PT_TYPE_MULTISET ||
21079:    e == PT_TYPE_SEQUENCE)
21080-         {
21081-    csql_yyerror("nested data type definition");
21082-         }
21083-     }
21084-
21085-   if (!dt)
21086-     {
21087-       dt = parser_new_node (this_parser, PT_DATA_TYPE);
21088-       if (dt)
21089-         {
21090-    dt->type_enum = e;
21091-    dt->data_type = NULL;
21092-         }
21093-     }
21094-
21095-   $$ = dt;
21096-   PARSER_SAVE_ERR_CONTEXT ($$, @$.buffer_pos)
21097-
21098-  DBG_PRINT}}
21099- ;
--
22352-
22353-  DBG_PRINT}}
22354- ;
22355-
22356-set_type
22357- : SET_OF
22358-  {{ DBG_TRACE_GRAMMAR(set_type, : SET_OF );
22359-
22360-   $$ = PT_TYPE_SET;
22361-
22362-  DBG_PRINT}}
22363- | MULTISET_OF
22364-  {{ DBG_TRACE_GRAMMAR(set_type, | MULTISET_OF );
22365-
22366-   $$ = PT_TYPE_MULTISET;
22367-
22368-  DBG_PRINT}}
22369- | SEQUENCE_OF
22370-  {{ DBG_TRACE_GRAMMAR(set_type, | SEQUENCE_OF );
22371-
22372:   $$ = PT_TYPE_SEQUENCE;
22373-
22374-  DBG_PRINT}}
22375- | of_container opt_of
22376-  {{ DBG_TRACE_GRAMMAR(set_type, | of_container opt_of );
22377-
22378-   $$ = $1;
22379-
22380-  DBG_PRINT}}
22381- ;
22382-
22383-opt_of
22384- : /* empty */
22385- | OF
22386- ;
22387-
22388-signed_literal_
22389- : literal_
22390-  {{ DBG_TRACE_GRAMMAR(signed_literal_,  : literal_ );
22391-
22392-   $$ = $1;
--
22689-  {{ DBG_TRACE_GRAMMAR(constant_set, | opt_of_container '{' '}' );
22690-
22691-   PT_NODE *node = parser_new_node (this_parser, PT_VALUE);
22692-
22693-   if (node)
22694-     {
22695-       node->info.value.data_value.set = 0;
22696-       node->type_enum = $1;
22697-     }
22698-
22699-   $$ = node;
22700-   PARSER_SAVE_ERR_CONTEXT ($$, @$.buffer_pos)
22701-
22702-  DBG_PRINT}}
22703- ;
22704-
22705-opt_of_container
22706- : /* empty */
22707-  {{ DBG_TRACE_GRAMMAR(opt_of_container, : );
22708-
22709:   $$ = PT_TYPE_SEQUENCE;
22710-
22711-  DBG_PRINT}}
22712- | of_container
22713-  {{ DBG_TRACE_GRAMMAR(opt_of_container, | of_container );
22714-
22715-   $$ = $1;
22716-
22717-  DBG_PRINT}}
22718- ;
22719-
22720-of_container
22721- : SET
22722-  {{ DBG_TRACE_GRAMMAR(of_container, : SET );
22723-
22724-   $$ = PT_TYPE_SET;
22725-
22726-  DBG_PRINT}}
22727- | MULTISET
22728-  {{ DBG_TRACE_GRAMMAR(of_container, | MULTISET );
22729-
22730-   $$ = PT_TYPE_MULTISET;
22731-
22732-  DBG_PRINT}}
22733- | SEQUENCE
22734-  {{ DBG_TRACE_GRAMMAR(of_container, | SEQUENCE );
22735-
22736:   $$ = PT_TYPE_SEQUENCE;
22737-
22738-  DBG_PRINT}}
22739- | LIST
22740-  {{ DBG_TRACE_GRAMMAR(of_container, | LIST );
22741-
22742:   $$ = PT_TYPE_SEQUENCE;
22743-
22744-  DBG_PRINT}}
22745- ;
22746-
22747-identifier_list
22748- : identifier_list ',' identifier
22749-  {{ DBG_TRACE_GRAMMAR(identifier_list, : identifier_list ',' identifier );
22750-
22751-   $$ = parser_make_link ($1, $3);
22752-   PARSER_SAVE_ERR_CONTEXT ($$, @$.buffer_pos)
22753-
22754-  DBG_PRINT}}
22755- | identifier
22756-  {{ DBG_TRACE_GRAMMAR(identifier_list, | identifier );
22757-
22758-   $$ = $1;
22759-   PARSER_SAVE_ERR_CONTEXT ($$, @$.buffer_pos)
22760-
22761-  DBG_PRINT}}
22762- ;
--
28067-{
28068-  PT_NODE *val, *tmp;
28069-
28070-  if (exp && exp->next == NULL)
28071-    {
28072-      if (exp->node_type == PT_EXPR)
28073- {
28074-   exp->info.expr.paren_type = 1;
28075- }
28076-      exp->flag.is_paren = 1;
28077-    }
28078-  else
28079-    {
28080-      val = parser_new_node (this_parser, PT_VALUE);
28081-      if (val)
28082- {
28083-   for (tmp = exp; tmp; tmp = tmp->next)
28084-     {
28085-       if (tmp->node_type == PT_VALUE && tmp->type_enum == PT_TYPE_EXPR_SET)
28086-  {
28087:    tmp->type_enum = PT_TYPE_SEQUENCE;
28088-  }
28089-     }
28090-
28091-   val->info.value.data_value.set = exp;
28092-   val->type_enum = PT_TYPE_EXPR_SET;
28093- }
28094-      exp = val;
28095-      parser_groupby_exception = PT_EXPR;
28096-    }
28097-  return exp;
28098-}
28099-
28100-
28101-static PT_NODE *
28102-pt_check_non_logical_expr (PARSER_CONTEXT * parser, PT_NODE * node)
28103-{
28104-   if(node)
28105-     {
28106-        if (node->type_enum != PT_TYPE_LOGICAL)
28107-          {


```

```cpp
name_resolution.c
1574-   */
1575-       }
1576-   }
1577- else
1578-   {
1579-     PT_NODE *arg_list = node->info.value.data_value.set;
1580-     /* roll back error messages for set values. this is a set function reference, which we just realized, since
1581-      * the syntax for constant sets and set functions is the same. Convert the node to a set function. */
1582-     node->node_type = PT_FUNCTION;
1583-     /* make info set up properly */
1584-     memset (&(node->info), 0, sizeof (node->info));
1585-     node->info.function.arg_list = arg_list;
1586-     if (node->type_enum == PT_TYPE_SET)
1587-       {
1588-  node->info.function.function_type = F_SET;
1589-       }
1590-     else if (node->type_enum == PT_TYPE_MULTISET)
1591-       {
1592-  node->info.function.function_type = F_MULTISET;
1593-       }
1594:     else if (node->type_enum == PT_TYPE_SEQUENCE)
1595-       {
1596-  node->info.function.function_type = F_SEQUENCE;
1597-       }
1598-     else
1599-       {
1600-  node->info.function.function_type = (FUNC_CODE) 0;
1601-       }
1602-
1603-     /* now we need to type the innards of the set ... */
1604-     /* first we tag this not typed so the type will be recomputed from scratch. */
1605-     node->type_enum = PT_TYPE_NONE;
1606-     node = pt_semantic_type (parser, node, bind_arg->sc_info);
1607-   }
1608-
1609-      }
1610-      break;
1611-
1612-    case PT_EXPR:
1613-      (void) pt_instnum_compatibility (node);
1614-      pt_mark_function_index_expression (parser, node, bind_arg);
--
2081-    parser_free_tree (parser, node);
2082-    node = NULL;
2083-    goto select_end;
2084-  }
2085-       /* using_index is just a name, mark as index name */
2086-       using_index->info.name.meta_class = PT_INDEX_NAME;
2087-       using_index->etc = (void *) PT_IDX_HINT_FORCE;
2088-
2089-       /* mark spec to scan for index key info */
2090-       node->info.query.q.select.from->info.spec.flag =
2091-  (PT_SPEC_FLAG) (node->info.query.q.select.from->info.spec.flag | PT_SPEC_FLAG_BTREE_NODE_INFO_SCAN);
2092-     }
2093- }
2094-
2095-      /* resolve '*' for rewritten multicolumn subquery during parsing STEP 1: remove sequence from select_list STEP 2:
2096-       * resolve '*', if exists STEP 3: restore sequence */
2097-
2098-      /* STEP 1 */
2099-      seq = NULL;
2100-      if (node->info.query.q.select.list->node_type == PT_VALUE
2101:   && node->info.query.q.select.list->type_enum == PT_TYPE_SEQUENCE
2102-   && pt_length_of_select_list (node->info.query.q.select.list, EXCLUDE_HIDDEN_COLUMNS) == 1)
2103- {
2104-   seq = node->info.query.q.select.list;
2105-   node->info.query.q.select.list = seq->info.value.data_value.set;
2106-   seq->info.value.data_value.set = NULL; /* cut-off link */
2107- }
2108-
2109-      /* STEP 2 */
2110-      if (node->info.query.q.select.list)
2111- {   /* resolve "*" */
2112-   if (node->info.query.q.select.list->node_type == PT_VALUE
2113-       && node->info.query.q.select.list->type_enum == PT_TYPE_STAR)
2114-     {
2115-       PT_NODE *next = node->info.query.q.select.list->next;
2116-
2117-       /* To consider 'select *, xxx ...', release "*" node only. */
2118-       node->info.query.q.select.list->next = NULL;
2119-       parser_free_node (parser, node->info.query.q.select.list);
2120-
2121-       node->info.query.q.select.list = pt_resolve_star (parser, node->info.query.q.select.from, NULL);
--
4155-      if (dt1->info.data_type.entity == NULL && dt2->info.data_type.entity == NULL)
4156- {
4157-   return 1;
4158- }
4159-
4160-      /* Can't mix annonymous and non-annonymous (guards null pointers) */
4161-      if (dt1->info.data_type.entity == NULL || dt2->info.data_type.entity == NULL)
4162- {
4163-   return 0;
4164- }
4165-
4166-      /* class objects must be the same */
4167-      if (dt1->info.data_type.entity->info.name.db_object != dt2->info.data_type.entity->info.name.db_object)
4168- {
4169-   return 0;
4170- }
4171-      break;
4172-
4173-    case PT_TYPE_SET:
4174-    case PT_TYPE_MULTISET:
4175:    case PT_TYPE_SEQUENCE:
4176-      s = dt1;
4177-      t = dt2;
4178-
4179-      /* if sizes are different, sets can't match */
4180-      if (pt_length_of_list (s) != pt_length_of_list (t))
4181- {
4182-   return 0;
4183- }
4184-
4185-      /* element types must match */
4186-      while (s)
4187- {
4188-   v = t;
4189-   while (v)
4190-     {
4191-       if (pt_check_same_datatype (parser, s, v))
4192-  {
4193-    break; /* got match */
4194-  }
4195-       v = v->next;
--
4496-      while (domain)
4497- {
4498-   db = db_domain_class (domain);
4499-   if (db)
4500-     {
4501-       /* prim_type = PT_TYPE_OBJECT, attach db_object, attach name */
4502-       entity =
4503-  pt_add_class_to_entity_list (parser, db, result->info.data_type.entity, result, (UINTPTR) result,
4504-          PT_CLASS);
4505-       if (entity == NULL)
4506-  {
4507-    return NULL;
4508-  }
4509-       result->info.data_type.entity = entity;
4510-     }
4511-   domain = (DB_DOMAIN *) db_domain_next (domain);
4512- }
4513-      break;
4514-
4515-    case PT_TYPE_SET:
4516:    case PT_TYPE_SEQUENCE:
4517-    case PT_TYPE_MULTISET:
4518-    case PT_TYPE_MIDXKEY:
4519-      /* set of what? */
4520-      dom = (DB_DOMAIN *) db_domain_set (domain);
4521-      /* make list of types in set */
4522-      while (dom)
4523- {
4524-   s = pt_domain_to_data_type (parser, dom); /* recursion here */
4525-
4526-   if (s)
4527-     {
4528-       if (result)
4529-  {
4530-    /*
4531-     * We want to make sure that the flat name list
4532-     * hanging off of the first PT_DATA_TYPE node is the
4533-     * union of all flat name lists from all nodes in
4534-     * this list; this makes certain things much easier
4535-     * later on.
4536-     * PRESERVE THE ORDER OF THESE LISTS!
--
4912-
4913-PT_TYPE_ENUM pt_type[CCI_U_TYPE_LAST + 1] = {
4914-  PT_TYPE_NULL,
4915-  PT_TYPE_CHAR,
4916-  PT_TYPE_VARCHAR,
4917-  PT_TYPE_NCHAR,
4918-  PT_TYPE_VARNCHAR,
4919-  PT_TYPE_BIT,
4920-  PT_TYPE_VARBIT,
4921-  PT_TYPE_NUMERIC,
4922-  PT_TYPE_INTEGER,
4923-  PT_TYPE_SMALLINT,
4924-  PT_TYPE_MONETARY,
4925-  PT_TYPE_FLOAT,
4926-  PT_TYPE_DOUBLE,
4927-  PT_TYPE_DATE,
4928-  PT_TYPE_TIME,
4929-  PT_TYPE_TIMESTAMP,
4930-  PT_TYPE_SET,
4931-  PT_TYPE_MULTISET,
4932:  PT_TYPE_SEQUENCE,
4933-  PT_TYPE_OBJECT,
4934-  PT_TYPE_RESULTSET,
4935-  PT_TYPE_BIGINT,
4936-  PT_TYPE_DATETIME,
4937-  PT_TYPE_BLOB,
4938-  PT_TYPE_CLOB,
4939-  PT_TYPE_ENUMERATION,
4940-  PT_TYPE_SMALLINT,
4941-  PT_TYPE_INTEGER, PT_TYPE_BIGINT, PT_TYPE_TIMESTAMPTZ, PT_TYPE_TIMESTAMPLTZ, PT_TYPE_DATETIMETZ, PT_TYPE_DATETIMELTZ,
4942-  /* Disabled type */
4943-  PT_TYPE_NA,   /* CCI_U_TYPE_TIMETZ, internal only */
4944-  /* end of disabled types */
4945-  PT_TYPE_JSON
4946-};
4947-
4948-static T_CCI_U_TYPE
4949-pt_dblink_get_basic_utype (T_CCI_U_EXT_TYPE u_ext_type)
4950-{
4951-  if (CCI_IS_SET_TYPE (u_ext_type))
4952-    {
--
4989-    {
4990-    case PT_TYPE_JSON:
4991-      break;
4992-
4993-    case PT_TYPE_VARCHAR:
4994-    case PT_TYPE_CHAR:
4995-    case PT_TYPE_VARNCHAR:
4996-    case PT_TYPE_NCHAR:
4997-    case PT_TYPE_BIT:
4998-    case PT_TYPE_VARBIT:
4999-    case PT_TYPE_NUMERIC:
5000-    case PT_TYPE_MONETARY:
5001-      break;
5002-
5003-    case PT_TYPE_BLOB:
5004-    case PT_TYPE_CLOB:
5005-    case PT_TYPE_OBJECT:
5006-    case PT_TYPE_ENUMERATION:
5007-    case PT_TYPE_SET:
5008-    case PT_TYPE_MULTISET:
5009:    case PT_TYPE_SEQUENCE:
5010-      PT_ERRORmf (parser, attr_def_node, MSGCAT_SET_PARSER_SEMANTIC,
5011-    MSGCAT_SEMANTIC_DBLINK_NOT_SUPPORTED_TYPE, pt_show_type_enum (attr_def_node->type_enum));
5012-      return false;
5013-
5014-    default:
5015-      need_precision = false;
5016-      break;
5017-    }
5018-
5019-  if (need_precision)
5020-    {
5021-      attr_def_node->data_type = dt = parser_new_node (parser, PT_DATA_TYPE);
5022-      if (dt == NULL)
5023- {
5024-   PT_ERROR (parser, attr_def_node, er_msg ());
5025-   return false;
5026- }
5027-
5028-      dt->type_enum = attr_def_node->type_enum;
5029-
--
5519- *   att(in): a db_attribute
5520- *   attr(in/out): a PT_NAME node corresponding to att
5521- *   db(in):
5522- */
5523-static void
5524-pt_get_attr_data_type (PARSER_CONTEXT * parser, DB_ATTRIBUTE * att, PT_NODE * attr)
5525-{
5526-  DB_DOMAIN *dom;
5527-  if (!attr || !att)
5528-    {
5529-      return;
5530-    }
5531-
5532-  dom = db_attribute_domain (att);
5533-  attr->etc = dom;  /* used for getting additional db-specific domain information in the Versant driver */
5534-  attr->type_enum = pt_db_to_type_enum (TP_DOMAIN_TYPE (dom));
5535-  switch (attr->type_enum)
5536-    {
5537-    case PT_TYPE_OBJECT:
5538-    case PT_TYPE_SET:
5539:    case PT_TYPE_SEQUENCE:
5540-    case PT_TYPE_MULTISET:
5541-    case PT_TYPE_NUMERIC:
5542-    case PT_TYPE_BIT:
5543-    case PT_TYPE_VARBIT:
5544-    case PT_TYPE_CHAR:
5545-    case PT_TYPE_VARCHAR:
5546-    case PT_TYPE_NCHAR:
5547-    case PT_TYPE_VARNCHAR:
5548-    case PT_TYPE_ENUMERATION:
5549-    case PT_TYPE_JSON:
5550-      attr->data_type = pt_domain_to_data_type (parser, dom);
5551-      break;
5552-    default:
5553-      break;
5554-    }
5555-
5556-  attr->info.name.meta_class = PT_NORMAL;
5557-
5558-  /* set its shared attribute flag */
5559-  if (db_attribute_is_shared (att))
--
10089-    {
10090-      node->info.method_call.method_type = PT_IS_INST_MTHD;
10091-    }
10092-
10093-  /* look up the domain of the method's return type */
10094-  if ((dom = db_method_arg_domain (method, 0)) == NULL)
10095-    {
10096-      /* only give error if it is a method expression */
10097-      if (node->info.method_call.call_or_expr != PT_IS_CALL_STMT)
10098- {
10099-   PT_ERRORmf (parser, node, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_METH_NOT_TYPED, method_name);
10100- }
10101-      return false;
10102-    }
10103-
10104-  node->type_enum = pt_db_to_type_enum (TP_DOMAIN_TYPE (dom));
10105-  switch (node->type_enum)
10106-    {
10107-    case PT_TYPE_OBJECT:
10108-    case PT_TYPE_SET:
10109:    case PT_TYPE_SEQUENCE:
10110-    case PT_TYPE_MULTISET:
10111-    case PT_TYPE_NUMERIC:
10112-    case PT_TYPE_BIT:
10113-    case PT_TYPE_VARBIT:
10114-    case PT_TYPE_CHAR:
10115-    case PT_TYPE_VARCHAR:
10116-    case PT_TYPE_NCHAR:
10117-    case PT_TYPE_VARNCHAR:
10118-      node->data_type = pt_domain_to_data_type (parser, dom);
10119-      break;
10120-    default:
10121-      break;
10122-    }
10123-
10124-  /* finally resolve method id */
10125-  node->info.method_call.method_id = (UINTPTR) node;
10126-  return true;
10127-}    /* pt_resolve_method_type */
10128-
10129-


```

```cpp
method_transform.c
622-
623-  /* newly create additional dummy_set_tbl as derived1 for instance method and stored precdure. check for path-expr. */
624-  if (spec->info.spec.meta_class == PT_CLASS && spec->info.spec.path_entities == NULL && !has_hierarchical_expr)
625-    {    /* can't handle path-expr */
626-      DB_VALUE val;
627-      PT_NODE *arg, *set;
628-
629-      /* not derived-table spec and not meta class spec */
630-      db_make_int (&val, true);
631-      arg = pt_dbval_to_value (parser, &val);
632-
633-      set = parser_new_node (parser, PT_FUNCTION);
634-      if (set == NULL)
635- {
636-   PT_INTERNAL_ERROR (parser, "allocate new node");
637-   return NULL;
638- }
639-
640-      set->info.function.function_type = F_SEQUENCE;
641-      set->info.function.arg_list = arg;
642:      set->type_enum = PT_TYPE_SEQUENCE;
643-
644-      dummy_set_tbl = parser_new_node (parser, PT_SPEC);
645-      if (dummy_set_tbl == NULL)
646- {
647-   PT_INTERNAL_ERROR (parser, "allocate new node");
648-   return NULL;
649- }
650-
651-      dummy_set_tbl->info.spec.id = (UINTPTR) dummy_set_tbl; /* set id */
652-      dummy_set_tbl->info.spec.derived_table = set;
653-      dummy_set_tbl->info.spec.derived_table_type = PT_IS_SET_EXPR;
654-      dummy_set_tbl->info.spec.range_var = meth_make_unique_range_var (parser, dummy_set_tbl);
655-      dummy_set_tbl->info.spec.as_attr_list =
656- meth_gen_as_attr_list (parser, dummy_set_tbl->info.spec.range_var, dummy_set_tbl->info.spec.id, arg);
657-    }
658-  else
659-    {
660-      /* check for outside references for correlation level determination */
661-      info1.id = spec->info.spec.id;
662-      info1.found = 0;

```
