<!--
    DO NOT MANUALLY EDIT THIS FILE
    THIS FILE IS AUTOMATICALLY GENERATED WITH resilient-circuits codegen
-->

# Example: AWS IAM: Remove User From All Groups

## Function - AWS IAM: Remove User From Groups

### API Name
`fn_aws_iam_remove_user_from_groups`

### Output Name
`None`

### Message Destination
`fn_aws_iam`

### Pre-Processing Script
```python
inputs.aws_iam_user_name = row.UserName
inputs.aws_iam_group_names = row.Groups
```

### Post-Processing Script
```python
##  AWS IAM - fn_aws_iam_detach_user_policies script ##
# Example result:
"""
OK
Result: {
          'version': '1.0', 'success': True, 'reason': None,
          'content': [{'PolicyArn': 'arn:aws:iam::aws:policy/AWSDenyAll', 'Status': 'OK'}
                      {'PolicyArn': 'arn:aws:iam::aws:policy/AWSDenyAll_2', 'Status': 'OK'}],
          'raw': '[{'PolicyArn': "arn:aws:iam::aws:policy/AWSDenyAll", 'Status": 'OK'},
                  {'PolicyArn': 'arn:aws:iam::aws:policy/AWSDenyAll_2', 'Status': 'OK'}]',
          'inputs': {'aws_iam_arns': 'arn:aws:iam::aws:policy/AWSDenyAll', 'aws_iam_user_name': 'iam_test_User_1'},
          'metrics': {'version': '1.0', 'package': 'fn-aws-iam', 'package_version': '1.0.0', 'host': 'myhost.ibm.com',
                      'execution_time_ms': 790, 'timestamp': '2019-11-29 12:18:30'
                     }
}
"""
#  Globals
# List of fields in datatable for fn_aws_iam_detach_user_policies  script
DATA_TBL_FIELDS = ["Policies"]
FN_NAME = "fn_aws_iam_remove_user_from_groups"
WF_NAME = "Remove User From All Groups"
# Processing
CONTENT = results.content
INPUTS = results.inputs
QUERY_EXECUTION_DATE = results["metrics"]["timestamp"]
note_text = ''

def main():
    note_text = ''
    added = 0
    no_such_entity = 0
    added_groups = []
    no_such_entity_groups = []
    if CONTENT:
        for pol_stat in CONTENT:
            if pol_stat["Status"] == "OK":
                added += 1
                added_groups.append(pol_stat["GroupName"])
            else:
                no_such_entity += 1
                no_such_entity_groups.append(pol_stat["GroupName"])
        if added_groups:
            note_text = "AWS IAM Integration: Workflow <b>{0}</b>: The user <b>{1}</b> was removed from the " \
                        "following groups <b>{2}</b> for Resilient function <b>{3}</b>."\
                .format(WF_NAME, INPUTS["aws_iam_user_name"], ", ".join(str(i) for i in added_groups), FN_NAME)
        if no_such_entity:
            note_text = "AWS IAM Integration: : Workflow <b>{0}</b>: There were <b>{1}</b> Groups <b>{2}</b> " \
                        "which did not exist for user <b>{3}</b> for Resilient function <b>{4}</b>."\
                .format(WF_NAME, len(no_such_entity_groups), ", ".join(str(i) for i in no_such_entity_groups), INPUTS["aws_iam_user_name"], FN_NAME)
    else:
        note_text += "AWS IAM Integration: Workflow <b>{0}</b>: There was no result returned for Resilient function <b>{0}</b>."\
            .format(WF_NAME, FN_NAME)

    incident.addNote(helper.createRichText(note_text))
if __name__ == "__main__":
    main()

```

---

## Function - AWS IAM: List User Groups

### API Name
`fn_aws_iam_list_user_groups`

### Output Name
`None`

### Message Destination
`fn_aws_iam`

### Pre-Processing Script
```python
inputs.aws_iam_user_name = row.UserName
```

### Post-Processing Script
```python
##  AWS IAM - fn_aws_iam_list_user_groups script ##
# Example result:
"""
Result: {
         'version': '1.0', 'success': True, 'reason': None,
         'content': [{'Path': '/', 'GroupName': 'system-admins', 'GroupId': 'AGPAJUCG3BHM64OGVGCBG',
                      'Arn': 'arn:aws:iam::834299573936:group/system-admins', 'CreateDate': '2017-05-29 20:37:53'}],
                      'raw': '[{"Path": "/", "GroupName": "system-admins", "GroupId": "AGPAJUCG3BHM64OGVGCBG",
                      "Arn": "arn:aws:iam::834299573936:group/system-admins", "CreateDate": "2017-05-29 20:37:53"
                      }
                    ]',
         'inputs': {'aws_iam_user_name': 'iam_test_User'},
         'metrics': {'version': '1.0', 'package': 'fn-aws-iam', 'package_version': '1.0.0', 'host': 'myhost.ibm.com',
                     'execution_time_ms': 1070, 'timestamp': '2019-11-18 10:19:19'
                     }
}
"""
#  Globals
# List of fields in datatable fn_aws_iam_list_user_groups script
DATA_TBL_FIELDS = ["Groups"]
FN_NAME = "fn_aws_iam_list_user_groups"
WF_NAME = "Remove User From All Groups"
# Processing
CONTENT = results.content
INPUTS = results.inputs
QUERY_EXECUTION_DATE = results["metrics"]["timestamp"]
note_text = ''

def check_add_quotes(tag_name):
    # Using regex
    # If spaces in tag name add quotes
    if re.search(r"\s", tag_name):
        return "'"+tag_name+"'"
    else:
        return tag_name

def main():
    note_text = ''
    if CONTENT:
        groups = []
        for grp in CONTENT:
            if grp["GroupName"] is not None:
                groups.append(grp["GroupName"])
        row.Groups = ",".join(groups)
    else:
        row.Groups = ""

if __name__ == "__main__":
    main()
```

---
