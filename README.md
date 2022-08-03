# Spring,JPA - PageReqeust of 오류 해결

## 오류 상황
* peagable 기능 구현 중 PageReqeust of 오류
* write_id를 인식 하지 못함
* 변수를 바꿧을때 제대로 돌아가는 것을 보아 write_id에 문제가 있었음

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
Qeury DSL 버전 업데이트 되면서 기존에 setting을 인식하지 못함

## 오류 해결
> build.gradle(고친 후)
```java
buildscript {
	ext {
		queryDslVersion = "5.0.0"
	}
}
plugins {
	id 'org.springframework.boot' version '2.6.3'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
	id 'java'
}
group = 'jpabook'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}




repositories {
	mavenCentral()
}
dependencies {

	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-devtools'
	implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.8.0'
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	implementation 'com.fasterxml.jackson.datatype:jackson-datatype-hibernate5'
	//querydsl 추가
	implementation "com.querydsl:querydsl-jpa:${queryDslVersion}"
	annotationProcessor "com.querydsl:querydsl-apt:${queryDslVersion}"

	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'com.h2database:h2'
	annotationProcessor 'org.projectlombok:lombok'

	//테스트에서 lombok 사용
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'

	testImplementation 'org.springframework.boot:spring-boot-starter-test'


}

def querydslDir = "$buildDir/generated/querydsl"
querydsl {
	jpa = true
	querydslSourcesDir = querydslDir
}
sourceSets {
	main.java.srcDir querydslDir
}
configurations {
	querydsl.extendsFrom compileClasspath
}
compileQuerydsl {
	options.annotationProcessorPath = configurations.querydsl
}
//querydsl 추가 끝

tasks.named('test') {
	useJUnitPlatform()
}
```
