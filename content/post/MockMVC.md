---
title: SpringBoot MockMVC 学习
date: 2018-10-03
tags:
- MockMVC
categories:
- 单元测试
---

学习使用 `MockMVC` 对代码进行测试，代码片段记录。

<!--more-->

## 测试

记录测试`get`, `post`等请求方式。

### Get

```java
@SpringBootTest
@RunWith(SpringRunner.class)
@AutoConfigureMockMvc
public class MockMVCControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testGet() throws Exception {
        Integer id = 1;
        MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/rest/get/"+id))
                .andReturn();
        MockHttpServletResponse response = result.getResponse();
        assertEquals(200, response.getStatus());
        System.out.println(response.getContentAsString());
    }
}
```

### Post

```java
@Test
public void testPost() throws Exception {
    Person person = new Person();
    person.setName("李四");
    person.setAge(30);
    MvcResult result = mockMvc.perform(MockMvcRequestBuilders.post("/rest/post")
            .contentType(MediaType.APPLICATION_JSON_UTF8)
            .content(gson.toJson(person))
            .accept(MediaType.APPLICATION_JSON))
            .andReturn();
    MockHttpServletResponse response = result.getResponse();
    assertEquals(200, response.getStatus());
    System.out.println(response.getContentAsString());
}
```

### Request

```java
@Test
public void testRequest() throws Exception {
    String name = "jack";
    MvcResult result = mockMvc.perform(MockMvcRequestBuilders.request(HttpMethod.POST, "/rest/request")
            .contentType(MediaType.APPLICATION_FORM_URLENCODED)
            .param("name", name)
            .accept(MediaType.APPLICATION_JSON))
            .andReturn();
    MockHttpServletResponse response = result.getResponse();
    assertEquals(200, response.getStatus());
    System.out.println(response.getContentAsString());
}
```



