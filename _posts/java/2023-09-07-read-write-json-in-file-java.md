---
title: Read a json from a file and write it in the file in Java
layout: post
author: andreea
lang: en
tags:
- JAVA
- Read file
- Write file
- JSON
- Jackson
categories:
- JAVA
---
Reading a json object from a file or writing a json object in a file is always a task that you have to do it.
Because I was always in the situation to do this, I was thinking to propose here a really simple example. Let's suppose that we have a file with the following content:
```
[
   {
      "name":"Test",
      "age":20
   },
   {
      "name":"Test Second",
      "age":21
   }
]
```
I have the JSON saved in a file named persons.json and stored in a location on the hard drive.
Now we define a POJO for the JSON above:
```

public class Person {
  private String name;
  private int age;

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public int getAge() {
    return age;
  }

  public void setAge(int age) {
    this.age = age;
  }

  @Override
  public boolean equals(Object o) {
    if ( this == o ) return true;
    if ( o == null || getClass() != o.getClass() ) return false;
    Person person = (Person) o;
    return age == person.age && Objects.equals(name, person.name);
  }

  @Override
  public int hashCode() {
    return Objects.hash(name, age);
  }

  @Override
  public String toString() {
    return "Person{" +
        "name='" + name + '\'' +
        ", age=" + age +
        '}';
  }
}
```
After defining the POJO we can use Jackson to deserialize and write the objects in the file. Below is a
small code which does the reading and the writing in another file.

```
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;

import java.io.File;
import java.io.IOException;
import java.util.List;

public class Main {

    public static void main(String[] args) throws IOException {
        ObjectMapper mapper = new ObjectMapper();
        List<Person> persons = mapper.readValue(new File("/fileslocation/persons.json"), new TypeReference<List<Person>>() {
        });
        System.out.println("I have read the persons: ");
        printPersons(persons);
        File outFile = new File("/fileslocation/persons_copy.json");
        persons.get(0).setAge(23);
        mapper.writeValue(outFile,persons);
        List<Person> personList = mapper.readValue(new File("/fileslocation/persons_copy.json"), new TypeReference<List<Person>>() {
        });
        System.out.println("I have wrote the persons: ");
        printPersons(persons);
    }
    private static  void printPersons(List<Person> persons){
        persons.stream().forEach(p ->System.out.println(p));
    }
}

```
This is a pretty easy way to read a JSON object in a POJO and to write it in another file or you can just use it further in the application.
See you next time!
