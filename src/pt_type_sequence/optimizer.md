# optimizer

```cpp
query_graph.c
1164-      attr->info.name.resolved = entity->info.spec.flat_entity_list->info.name.original;
1165-      attr->info.name.original = "";
1166-      attr->info.name.spec_id = entity->info.spec.id;
1167-      attr->info.name.meta_class = PT_OID_ATTR;
1168-
1169-      /* create oid segment for the entity */
1170-      seg = qo_insert_segment (node, NULL, attr, env, NULL);
1171-      QO_SEG_SET_VALUED (seg) = false; /* oid segments aren't set valued */
1172-      QO_SEG_CLASS_ATTR (seg) = false; /* oid segments aren't class attrs */
1173-      QO_SEG_SHARED_ATTR (seg) = false; /* oid segments aren't shared attrs */
1174-    }
1175-
1176-  /*
1177-   * Create a segment for each symbol in the entities symbol table.
1178-   */
1179-  for (name = attr_list; name != NULL; name = name->next)
1180-    {
1181-      seg = qo_insert_segment (node, NULL, name, env, NULL);
1182-
1183-      if ((name->type_enum == PT_TYPE_SET) || (name->type_enum == PT_TYPE_MULTISET)
1184:   || (name->type_enum == PT_TYPE_SEQUENCE))
1185- {
1186-   QO_SEG_SET_VALUED (seg) = true;
1187- }
1188-      else
1189- {
1190-   QO_SEG_SET_VALUED (seg) = false;
1191- }
1192-
1193-      if (name->info.name.meta_class == PT_META_ATTR)
1194- {
1195-   QO_SEG_CLASS_ATTR (seg) = true;
1196- }
1197-      else
1198- {
1199-   QO_SEG_CLASS_ATTR (seg) = false;
1200- }
1201-
1202-      /* this needs to check a flag Bill is going to add--CHECK!!!!! */
1203-      QO_SEG_SHARED_ATTR (seg) = false;
1204-    }
```

```cpp
query_rewrite.c
875- {
876-   arg = parser_copy_tree (parser, node->info.expr.arg2->info.function.arg_list);
877-   targ = node->info.expr.arg1;
878- }
879-      else if (PT_IS_OID_NAME (node->info.expr.arg2) && PT_IS_FUNCTION (node->info.expr.arg1)
880-        && PT_IS_CONST_INPUT_HOSTVAR (node->info.expr.arg1->info.function.arg_list))
881- {
882-   arg = parser_copy_tree (parser, node->info.expr.arg1->info.function.arg_list);
883-   targ = node->info.expr.arg2;
884- }
885-      break;
886-    default:
887-      break;
888-    }
889-
890-  /* create mset constructor subtree */
891-  if (arg && (set = parser_new_node (parser, PT_FUNCTION)) != NULL)
892-    {
893-      set->info.function.function_type = F_SEQUENCE;
894-      set->info.function.arg_list = arg;
895:      set->type_enum = PT_TYPE_SEQUENCE;
896-
897-      set->data_type = parser_copy_tree_list (parser, arg->data_type);
898-    }
899-
900-  return set;
901-}
902-
903-/*
904- * qo_make_new_derived_tblspec () -
905- *   return:
906- *   parser(in): parser context
907- *   node(in): a PT_SPEC node
908- *   pred(in): node's OID_ATTR predicate
909- *   seqno(in/out): sequence number for generating unique derived table names
910- *
911- * Note:
912- *   It requires that node is the PT_SPEC node (c x) and
913- *   pred is the OID_ATTR predicate (x {=|IN} expr) from
914- *        select ... from c x, ... where ... and x {=|IN} expr
915- *   and modifies parser heap, node

```
