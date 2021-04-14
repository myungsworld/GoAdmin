# GoAdmin

## Usage
- **SetPostHook** Fn
  - go get github.com/GoAdminGroup/go-admin/plugins/admin/modules/form

```go
formList.SetTable("table").
  SetPostHook(func(values form2.Values) error {
  id , _ := strconv.Atoi(values.Get("form에서 Addfield 한 필드 중 하나"))
  이하 테이블이 만들어지고 난 뒤의 디비 핸들링이나 할 작업
```   
