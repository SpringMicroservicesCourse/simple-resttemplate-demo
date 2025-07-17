# Customer Service Demo ⚡

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Maven](https://img.shields.io/badge/Maven-3.8+-blue.svg)](https://maven.apache.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 專案介紹

這是一個 Spring Boot 客戶端服務示範專案，主要展示如何使用 `RestTemplate` 來訪問外部 Web 資源。在第七章的課程中，我們將學習如何透過 HTTP 客戶端來呼叫提供 JSON 回應的 REST API 介面。

### 核心功能
- **HTTP 客戶端呼叫**：使用 Spring 的 `RestTemplate` 進行 RESTful API 呼叫
- **JSON 資料處理**：自動處理 JSON 序列化與反序列化，將回應轉換為 Java 物件
- **URI 建構**：使用 `UriComponentsBuilder` 來動態建構請求 URL
- **多種 HTTP 方法支援**：支援 GET、POST、PUT、DELETE 等 HTTP 方法

> 💡 **為什麼選擇此專案？**
> - 展示 Spring Boot 中 HTTP 客戶端的基本使用方式
> - 學習如何訪問外部 Web 資源和 REST API
> - 了解 RestTemplate 與 UriComponentsBuilder 的結合使用
> - 為後續學習 WebClient 等進階客戶端技術奠定基礎

### 🎯 專案特色

- **非 Web 應用程式**：設定為 `WebApplicationType.NONE`，不會啟動內建 Web 容器，專注於客戶端功能
- **RestTemplate 實例化**：手動建立 RestTemplate 實例並放入 Spring 容器中
- **UriComponentsBuilder 使用**：展示如何動態建構請求 URL 和參數
- **多種回應處理**：展示 `getForEntity`、`postForObject`、`getForObject` 等不同方法的使用

## 技術棧

### 核心框架
- **Spring Boot 3.4.5** - 現代化 Java 應用程式框架
- **Spring Web** - 提供 RestTemplate 等 HTTP 客戶端功能
- **Spring Boot Web Starter** - 提供 Web 支援和自動配置
- **Lombok** - 減少樣板程式碼，提升開發效率

### 開發工具與輔助
- **Maven** - 專案建置與依賴管理
- **Java 21** - 最新 LTS 版本的 Java 平台
- **SLF4J + Logback** - 日誌記錄框架

## 專案結構

```
simple-resttemplate-demo/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── tw/fengqing/spring/springbucks/customer/
│   │   │       ├── CustomerServiceApplication.java    # 主應用程式類別
│   │   │       ├── RestTemplateConfig.java           # RestTemplate 配置類別
│   │   │       └── model/
│   │   │           └── Coffee.java                   # 咖啡實體類別
│   │   └── resources/
│   │       └── application.properties                # 應用程式設定檔
│   └── test/
│       └── java/
├── pom.xml                                          # Maven 專案配置
└── README.md                                        # 專案說明文件
```

## 快速開始

### 前置需求
- **Java 21** 或更新版本
- **Maven 3.8+** 或更新版本
- **IDE**：建議使用 IntelliJ IDEA 或 Eclipse

### 安裝與執行

1. **克隆此倉庫：**
```bash
git clone https://github.com/SpringMicroservicesCourse/simple-resttemplate-demo.git
```

2. **進入專案目錄：**
```bash
cd simple-resttemplate-demo
```

3. **編譯專案：**
```bash
mvn clean compile
```

4. **執行應用程式：**
```bash
mvn spring-boot:run
```

### 執行結果
成功啟動後，您會看到類似以下的日誌輸出：
```
2025-07-17T11:57:07.218+08:00  INFO --- [main] CustomerServiceApplication : No active profile set, falling back to 1 default profile: "default"
2025-07-17T11:57:07.560+08:00  INFO --- [main] CustomerServiceApplication : Started CustomerServiceApplication in 0.492 seconds
```

**注意**：此專案需要配合第六章的 Springbucks Waiter Service 一起使用。請確保 Waiter Service 已在 8080 端口啟動，才能看到完整的 API 呼叫結果。

## 進階說明

### 環境變數
```properties
# 應用程式設定
spring.application.name=customer-service
logging.level.tw.fengqing.spring.springbucks.customer=INFO

# API 端點設定（可選）
api.coffee.base-url=http://localhost:8080/coffee
```

### 設定檔說明
```properties
# application.properties 主要設定
# 目前為空，可根據需求添加自訂設定
```

### 重要程式碼說明

#### RestTemplate 配置
```java
@Configuration
public class RestTemplateConfig {
    @Bean
    public RestTemplate restTemplate(RestTemplateBuilder builder) {
        return builder.build();
    }
}
```
**說明**：手動建立 RestTemplate 實例並放入 Spring 容器中。雖然 Spring Boot 沒有提供 RestTemplate 的自動配置，但提供了 RestTemplateBuilder 來協助建構。

#### 主應用程式類別
```java
@SpringBootApplication
@Slf4j
public class CustomerServiceApplication implements ApplicationRunner {
    private final RestTemplate restTemplate;

    // 使用建構子注入來避免循環依賴
    public CustomerServiceApplication(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }
}
```
**說明**：採用建構子注入方式，確保依賴關係清晰且避免循環依賴問題。設定為 `WebApplicationType.NONE` 避免啟動 Web 容器。

## API 呼叫範例

### GET 請求範例（使用 getForEntity）
```java
URI uri = UriComponentsBuilder
        .fromUriString("http://localhost:8080/coffee/{id}")
        .build(1);
ResponseEntity<Coffee> response = restTemplate.getForEntity(uri, Coffee.class);
```
**說明**：使用 `getForEntity` 可以同時獲取 HTTP 狀態碼、回應標頭和回應內容。

### POST 請求範例（使用 postForObject）
```java
Coffee request = Coffee.builder()
        .name("Americano")
        .price(BigDecimal.valueOf(125.00))
        .build();
Coffee response = restTemplate.postForObject(coffeeUri, request, Coffee.class);
```
**說明**：使用 `postForObject` 直接獲取回應內容轉換為指定物件。

### GET 請求範例（使用 getForObject）
```java
String response = restTemplate.getForObject(coffeeUri, String.class);
```
**說明**：使用 `getForObject` 直接獲取回應內容為字串格式，適合獲取原始 JSON 回應。

## 參考資源

- [Spring Boot 官方文件](https://spring.io/projects/spring-boot)
- [Spring Web 官方文件](https://docs.spring.io/spring-framework/reference/web/web.html)
- [RestTemplate 使用指南](https://docs.spring.io/spring-framework/reference/integration/rest/client.html)
- [UriComponentsBuilder 官方文件](https://docs.spring.io/spring-framework/reference/web/web.html#web-uri-building)
- [Spring Boot 微服務架構實戰課程]()

## 注意事項與最佳實踐

### ⚠️ 重要提醒

| 項目 | 說明 | 建議做法 |
|------|------|----------|
| 循環依賴 | RestTemplate 注入方式 | 使用建構子注入而非欄位注入 |
| API 端點 | 外部服務連線 | 確保第六章的 Waiter Service 在 8080 端口運行 |
| 錯誤處理 | 網路連線異常 | 實作適當的例外處理機制 |
| 配置管理 | RestTemplate 設定 | 手動建立 RestTemplate 實例並放入容器 |
| 金錢處理 | BigDecimal 使用 | 生產環境建議使用專門的 Money 物件 |

### 🔒 最佳實踐指南

- **依賴注入**：優先使用建構子注入，避免 `@Autowired` 欄位注入
- **RestTemplate 實例化**：手動建立 RestTemplate 實例，Spring Boot 沒有提供自動配置
- **URI 建構**：使用 UriComponentsBuilder 來動態建構請求 URL 和參數
- **回應處理**：根據需求選擇 `getForEntity`、`postForObject`、`getForObject` 等方法
- **錯誤處理**：實作完整的例外處理，包含網路連線失敗的處理
- **日誌記錄**：使用適當的日誌級別記錄重要操作和錯誤訊息
- **程式碼註解**：在重要的程式碼區塊添加清楚註解，方便團隊理解與維護

### 🚨 常見問題

**Q: 為什麼使用 BigDecimal 來代表金錢？**
A: 這只是示範用途，在生產環境中建議使用專門的 Money 物件來處理金融相關計算。

## 授權說明

本專案採用 MIT 授權條款，詳見 LICENSE 檔案。

## 關於我們

我們主要專注在敏捷專案管理、物聯網（IoT）應用開發和領域驅動設計（DDD）。喜歡把先進技術和實務經驗結合，打造好用又靈活的軟體解決方案。

## 聯繫我們

- **FB 粉絲頁**：[風清雲談 | Facebook](https://www.facebook.com/profile.php?id=61576838896062)
- **LinkedIn**：[linkedin.com/in/chu-kuo-lung](https://www.linkedin.com/in/chu-kuo-lung)
- **YouTube 頻道**：[雲談風清 - YouTube](https://www.youtube.com/channel/UCXDqLTdCMiCJ1j8xGRfwEig)
- **風清雲談 部落格**：[風清雲談](https://blog.fengqing.tw/)
- **電子郵件**：[fengqing.tw@gmail.com](mailto:fengqing.tw@gmail.com)

---

**📅 最後更新：2025-07-17**  
**👨‍💻 維護者：風清雲談團隊** 