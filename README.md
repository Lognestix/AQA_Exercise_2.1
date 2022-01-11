## `Статус сборки` [![Build status](https://ci.appveyor.com/api/projects/status/ruhu4udfwgq6v68w?svg=true)](https://ci.appveyor.com/project/Lognestix/aqa-exercise-2-1)
## В build.gradle добавленна поддержка JUnit-Jupiter, Selenium и WebDriverManager.
```gradle
plugins {
    id 'java'
}

group 'ru.netology'
version '1.0-SNAPSHOT'

sourceCompatibility = 11

//Кодировка файлов (если используется русский язык в файлах)
compileJava.options.encoding = "UTF-8"
compileTestJava.options.encoding = "UTF-8"

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter:5.8.2'
    testImplementation 'org.seleniumhq.selenium:selenium-java:4.1.1'
    testImplementation 'io.github.bonigarcia:webdrivermanager:5.0.3'
}

test {
    useJUnitPlatform()
}
```
## Авто-тесты находящиеся в этом репозитории.
```Java
import io.github.bonigarcia.wdm.WebDriverManager;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class CardOrderTest {
    private WebDriver driver;

    @BeforeAll
    static void setUpAll() {
        WebDriverManager.chromedriver().setup();
    }

    @BeforeEach
    void setUp() {
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--disable-dev-shm-usage");
        options.addArguments("--no-sandbox");
        options.addArguments("--headless");
        driver = new ChromeDriver(options);
    }

    @AfterEach
    void tearDown() {
        driver.quit();
        driver = null;
    }

    @Test   //Задача №1
    void shouldSuccessfulCardOrder() {
        driver.get("http://localhost:9999");
        driver.findElement(By.cssSelector("[data-test-id=name] input")).sendKeys("Томас Андерсон");
        driver.findElement(By.cssSelector("[data-test-id=phone] input")).sendKeys("+00000000000");
        driver.findElement(By.cssSelector("[data-test-id=agreement]")).click();
        driver.findElement(By.className("button")).click();

        String text = driver.findElement(By.cssSelector("[data-test-id=order-success]")).getText();
        assertEquals("Ваша заявка успешно отправлена! Наш менеджер свяжется с вами в ближайшее время.", text.trim());
    }

    @Test   //Задача №2
    void shouldNameFieldValidation() {
        driver.get("http://localhost:9999");
        driver.findElement(By.cssSelector("[data-test-id=name] input")).sendKeys("Thomas Anderson");
        driver.findElement(By.cssSelector("[data-test-id=phone] input")).sendKeys("+00000000000");
        driver.findElement(By.cssSelector("[data-test-id=agreement]")).click();
        driver.findElement(By.className("button")).click();

        String text = driver.findElement(By.cssSelector(".input_invalid")).findElement(By.className("input__sub")).getText();
        assertEquals("Имя и Фамилия указаные неверно. Допустимы только русские буквы, пробелы и дефисы.", text.trim());
    }

    @Test   //Задача №2
    void shouldPhoneFieldValidation() {
        driver.get("http://localhost:9999");
        driver.findElement(By.cssSelector("[data-test-id=name] input")).sendKeys("Томас Андерсон");
        driver.findElement(By.cssSelector("[data-test-id=phone] input")).sendKeys("+0000000000");
        driver.findElement(By.cssSelector("[data-test-id=agreement]")).click();
        driver.findElement(By.className("button")).click();

        String text = driver.findElement(By.cssSelector(".input_invalid")).findElement(By.className("input__sub")).getText();
        assertEquals("Телефон указан неверно. Должно быть 11 цифр, например, +79012345678.", text.trim());
    }

    @Test   //Задача №2
    void shouldInactiveCheckBox() {
        driver.get("http://localhost:9999");
        driver.findElement(By.cssSelector("[data-test-id=name] input")).sendKeys("Томас Андерсон");
        driver.findElement(By.cssSelector("[data-test-id=phone] input")).sendKeys("+00000000000");
        driver.findElement(By.className("button")).click();

        String text = driver.findElement(By.cssSelector(".input_invalid")).findElement(By.className("checkbox__text")).getText();
        assertEquals("Я соглашаюсь с условиями обработки и использования моих персональных данных" +
                " и разрешаю сделать запрос в бюро кредитных историй", text.trim());
    }
}
```