# GoAdmin


### SetPostHook 
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
**Advanced Usage**
```go
formList.SetTable("table").
  SetPostHook(func(values form2.Values) error {
    
    
    // Post가 일어나는 모든 작업에 대해서 트랜잭션을 일으킴
    
    
    
    // 테이블이 새로 생성될때만 트랜잭션 일어남
    if values.IsInsertPost() {
      ...
    }
  
  
    // 테이블이 변경 될때만 트랜잭션 일어남
    if values.IsUpdatePost() {
      ...
    }
  return nil
}
```

### SetInsertFn
  - SetPostHook안에 Insert 기능이 있는데 이건 왜 존재하는지 모르겠음 
  - 테이블이 생성될때 트랜잭션이 일어나는건 동일한데 이건 테이블이 완성되기전에 값을 가져오는거라 키값을 가져올수도 없음
  - 결론 쓰레기 (사실 이거때문에 뻘짓 2시간 하다가 IsInsertPost로 해결함)

**Usage**
```go
formList.SetInsertFn(func(values form2.Values) error {
  
  fmt.println("나는 해적왕이 될사람임")
  
  return nil
})
```
### JumpInNewTab
  - 버튼으로 다른 웹의 테이블을 반환할때 쓴다.
  - 참조하는 테이블의 id와 foreignKey 를 연결시켜서 원하는 테이블의 리스트를 반환 할수 있다.

**Usage**
```go
info.AddColumButtons("반환될 테이블",
  types.GetColumnButton("클릭" , icon.Check, action.JumpInNewTab("/{{config.Prefix}}/info/{{key}}?반환할 테이블의 컬럼명={{.Id}}", "")),
  )
```  

### FieldOptions
  - go get github.com/GoAdminGroup/go-admin/template/types
  - Drop box 생성

**Usage**
```go

//Basic

formList.AddField("종류","type",db.Varchar,form.SelectSingle).
  FieldOptions(types.filedOptions{
    {Text: "개" Value : "개"},
    {Text: "고양이" Value : "고양이},
    ...
    }
    
//Intermediate

formList.AddField("종류","type",db.Varchar,form.SelectSingle).
  FieldOptions(
    func() []types.FieldOption {
    options := make([]types.FieldOption, 0)
    for _ , animalType := range constatns.AnimalTypes {
      options : append(options, types.fieldOption{
        Text: animalType,
        Value : animalType,
      })
    }
    return options
  }()).FieldRowWidth(2)  
```
