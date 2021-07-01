# GoAdmin

---
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
---
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
---
### JumpInNewTab
  - 버튼으로 다른 웹의 테이블을 반환할때 쓴다.
  - 참조하는 테이블의 id와 foreignKey 를 연결시켜서 원하는 테이블의 리스트를 반환 할수 있다.

**Usage**
```go
info.AddColumButtons("반환될 테이블",
  types.GetColumnButton("클릭" , icon.Check, action.JumpInNewTab("/{{config.Prefix}}/info/{{key}}?반환할 테이블의 컬럼명={{.Id}}", "")),
  )
```  
---
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
---
### SetDeleteFn 
  - 삭제 기능에 추가적인 트랜잭션을 추가하고 싶을때 사용
  - 필자의 경우는 삭제를 할때 테이블에 어떤 권한이 있을 시 삭제를 불가능하게 함

**Usage**
```go

info.SetTable("table").
  SetDeleteFn(func (idArr []string) error {
  
  // idArr = goAdmin에서 여러개를 선택할수 있기 떄문에 선택한 id들을 배열로 가져옴
  
  // Example 
  for _ , id := range idArr {
    // Your code
  }  
  
  return nil 
  
  })
  ```
  ---
  ### SetUpdateFn 
    - 위에 삭제 기능에 추가적인 트랜잭션을 추가한다고 했는데 
    - 정확히 말하자면 기능을 새로 만드는것 ( 이걸 추가하면 그냥 그 버튼의 기능을 다 바꿔버림 )
    - delete 는 getInfo 에서 핸들링 update 는 getForm 에서 핸들링
  
  **Usage**
  ```go
  
  formList.SetUpdateFn(func values form2.Values) error {
    
    fmt.println("이렇게만 하면 그냥 로그만 찍히고 아무일도 안일어남")
    
   return nil
  })
  ```
---
### FieldPostFilterFn
  - 필드 하나만의 속성에 대한 제어를 하려면 이걸 사용

**Usage**
```go

formList.AddField("니모","name", db.Varchar, form.Text).
  FieldPostFilterFn(func(value types.PostFieldModel) interface{} {
    
    // value.Value.First() 가 입력받아서 가져오는 값
    newName := value.Value.First()
    
    // 만들어질 경우의 트랜잭션
    if value.IsCreate() {
      return newName
    } else {
    
      // 그 이외의 (수정될 경우의) 트랜잭션
      id , _ := strconv.Atoi(value.Row["id"]
      ... your code
    }
    
    return newName
}) 

``` 

### Double Join?
  - join 여러개 하는 방법

**Usage**
```go

info.AddField("니얼굴","your_face",db.Varchar).
FieldJoin(types.Join{
  Field: "info가 가지는 table column",
  JoinField : "join할 table column",
  Table : "table name",
}).
FieldJoin(types.Join{
  Table : "table name",
  JoinField :"위 table column",
  Field : "base table column",
  BaseTable : "table name",
})  
