# Spring,JPA - PageReqeust of 오류 해결

## 오류 상황
* peagable 기능 구현 중 PageReqeust of 오류
* write_id를 인식 하지 못함

> WriteService
```java
public List<WriteResponse> getList(Pageable pageable) {
        //Pageable pageable = PageRequest.of(page, 5, Sort.Direction.DESC, "write_id");

      
         return writeRepository.findAll(pageable).stream()
                .map(write -> new WriteResponse(write)
                 )
                .collect(Collectors.toList());
    }
```

## 오류 원인 
* 변수를 바꿧을때 제대로 돌아가는 것을 보아 write_id에 문제가 있었음
* 변수 명  write_id에서 _ 이후 변수명을 인식 하지 못해서 sort 기준을 잡지 못함

## 오류 해결
> WriteService
```java
public List<WriteResponse> getList(Pageable pageable) {
        //Pageable pageable = PageRequest.of(page, 5, Sort.Direction.DESC, "write_id");

      
         return writeRepository.findAll(pageable).stream()
                .map(write -> new WriteResponse(write)
                 )
                .collect(Collectors.toList());
    }
```

> Write(Entity)
```java
package com.datePage.request.domain;

import lombok.AccessLevel;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Entity
@NoArgsConstructor(access = AccessLevel.PUBLIC)
@Getter
public class Write {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long writeId;

    @Column(name = "write_title")
    private String title;

    @Lob
    private String content;

    @Builder
    public Write(String title, String content) {
        this.title = title;
        this.content = content;
    }

}
```

* 엔티티 변수명을 writeId로 변환
