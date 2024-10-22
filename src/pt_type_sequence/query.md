# query

```cpp
execute_schema.c
7085-		case PT_TYPE_VARCHAR:
7086-		  if (p != DB_DEFAULT_PRECISION
7087-		      && (p < 0 || (p == 0 && check_zero_precision) || p > DB_MAX_VARCHAR_PRECISION))
7088-		    {
7089-		      PT_ERRORmf3 (parser, attribute, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_INV_PREC, p, 0,
7090-				   DB_MAX_VARCHAR_PRECISION);
7091-		    }
7092-		  break;
7093-
7094-		case PT_TYPE_VARNCHAR:
7095-		  if (p != DB_DEFAULT_PRECISION
7096-		      && (p < 0 || (p == 0 && check_zero_precision) || p > DB_MAX_VARNCHAR_PRECISION))
7097-		    {
7098-		      PT_ERRORmf3 (parser, attribute, MSGCAT_SET_PARSER_SEMANTIC, MSGCAT_SEMANTIC_INV_PREC, p, 0,
7099-				   DB_MAX_VARNCHAR_PRECISION);
7100-		    }
7101-		  break;
7102-
7103-		case PT_TYPE_SET:
7104-		case PT_TYPE_MULTISET:
7105:		case PT_TYPE_SEQUENCE:
7106-		  {
7107-		    PT_NODE *elem;
7108-		    for (elem = dtyp; elem != NULL; elem = elem->next)
7109-		      {
7110-			if (PT_IS_LOB_TYPE (elem->type_enum))
7111-			  {
7112-			    PT_ERRORmf2 (parser, attribute, MSGCAT_SET_PARSER_SEMANTIC,
7113-					 MSGCAT_SEMANTIC_INVALID_SET_ELEMENT, pt_show_type_enum (attribute->type_enum),
7114-					 pt_show_type_enum (elem->type_enum));
7115-			    break;
7116-			  }
7117-		      }
7118-		  }
7119-		  break;
7120-
7121-		case PT_TYPE_ENUMERATION:
7122-		  (void) pt_check_enum_data_type (parser, dtyp);
7123-		  break;
7124-
7125-		default:
--
13372-    case PT_TYPE_TIME:
13373-      return empty_time;
13374-
13375-    case PT_TYPE_DATETIME:
13376-      return empty_datetime;
13377-    case PT_TYPE_DATETIMELTZ:
13378-      return empty_dt_ltz;
13379-    case PT_TYPE_DATETIMETZ:
13380-      return empty_dt_tz;
13381-
13382-    case PT_TYPE_CHAR:
13383-    case PT_TYPE_VARCHAR:
13384-      return empty_str;
13385-
13386-    case PT_TYPE_VARNCHAR:
13387-    case PT_TYPE_NCHAR:
13388-      return empty_n_str;
13389-
13390-    case PT_TYPE_SET:
13391-    case PT_TYPE_MULTISET:
13392:    case PT_TYPE_SEQUENCE:
13393-      return empty_set;
13394-
13395-    case PT_TYPE_BIT:
13396-    case PT_TYPE_VARBIT:
13397-      return empty_bit;
13398-    case PT_TYPE_LOGICAL:
13399-    case PT_TYPE_NONE:
13400-    case PT_TYPE_MAYBE:
13401-    case PT_TYPE_NA:
13402-    case PT_TYPE_NULL:
13403-    case PT_TYPE_STAR:
13404-    case PT_TYPE_OBJECT:
13405-    case PT_TYPE_MIDXKEY:
13406-    case PT_TYPE_COMPOUND:
13407-    case PT_TYPE_RESULTSET:
13408-    case PT_TYPE_BLOB:
13409-    case PT_TYPE_CLOB:
13410-    case PT_TYPE_ELO:
13411-      return NULL;
13412-    case PT_TYPE_JSON:

```
