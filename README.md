# GoAdmin


## SetPostHook Fn
  - go get github.com/GoAdminGroup/go-admin/plugins/admin/modules/form
  - formList.AddField() 로 만든 모든 필드데이터를 가지고 있고 테이블이 생성되고 난후 바로 핸들링 작업 가능 
  - 내가 사용한 경우는 외래키로 사용되는 테이블 id 를 테이블이 생성되면서 동시에 관련 테이블 데이터 생성 (디비 핸들링)

**Usage**
```go
formList.SetTable("table").
  SetPostHook(func(values form2.Values) error {
    ...
    ex) values.Get("id")
} 
```   
