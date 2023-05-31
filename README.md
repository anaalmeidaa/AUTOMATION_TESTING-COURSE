# AUTOMATION_TESTING-COURSE

package selenium;

 import org.junit.jupiter.api.Assertions;

 import static org.hamcrest.CoreMatchers.is;
 import static org.hamcrest.MatcherAssert.assertThat;
 import static org.junit.Assert.assertEquals;
 import static org.junit.Assert.assertThat;
import static org.junit.Assert.fail;

import java.time.Duration;
import java.time.LocalDateTime;
 import java.time.format.DateTimeFormatter;

 import org.junit.jupiter.api.AfterAll;
 import org.junit.jupiter.api.AfterEach;
 import org.junit.jupiter.api.BeforeAll;
 import org.junit.jupiter.api.DisplayName;
 import org.junit.jupiter.api.Test;
 import org.openqa.selenium.Alert;
 import org.openqa.selenium.By;
 import org.openqa.selenium.Dimension;
import org.openqa.selenium.Keys;
import org.openqa.selenium.NoAlertPresentException;
import org.openqa.selenium.WebDriver;
 import org.openqa.selenium.WebElement;
 import org.openqa.selenium.chrome.ChromeDriver;
 import org.openqa.selenium.interactions.Actions;
 import org.openqa.selenium.support.ui.ExpectedConditions;
 import org.openqa.selenium.support.ui.Select;
 import org.openqa.selenium.support.ui.WebDriverWait;
 import io.github.bonigarcia.wdm.WebDriverManager;

  public class Add_New_Account {
  public static int count = 1;
  private static WebDriver driver;
  private String customerID = "12345";
  private String inicialAmount_current = "1000";
  
  
  @BeforeAll
  public static void setUp() {
    // Set environment variable
    System.setProperty("webdriver.http.factory", "jdk-http-client");

    // WebDriverManager will setup the chrome browser 
    WebDriverManager.chromedriver().setup();

    // Initialize our virtual Browser
    driver = new ChromeDriver();
  }
  
  @AfterAll
  public static void afterAll() {
    driver.quit(); 
  }

  @Test
  public void TC001() throws InterruptedException {
    //---------------------------
    // Setup Environment  --------
    //---------------------------

    // Navigate to the login page
    driver.get("https://demo.guru99.com/v4/index.php");
    Thread.sleep(4000);

    // Close the iframe - Privacy Police
    if (count==1) {
      Thread.sleep(4000);
      driver.switchTo().frame("gdpr-consent-notice").findElement(By.id("save")).click();
      count--;
      Thread.sleep(4000);
    }

    // Find the username and password fields and enter the credentials
    WebElement username = driver.findElement(By.name("uid"));
    WebElement password = driver.findElement(By.name("password"));
    username.sendKeys("mngr478326");
    password.sendKeys("razAgAj");

    // Click the login button
    WebElement loginButton = driver.findElement(By.name("btnLogin"));
    loginButton.click();

    // Verify that we are logged in successfully
    Assertions.assertTrue(driver.getCurrentUrl().contains("manager"));

    // Click on the "New Account" link
    WebElement newAccountLink = driver.findElement(By.linkText("New Account"));
    newAccountLink.click();
    
    //Verify that clicking the " New Account" button takes the user to the account creation page.

    // Verify that we are on the account creation page
    Assertions.assertTrue(driver.getCurrentUrl().contains("addAccount.php"));

  }

  @Test
  public void TC002() throws InterruptedException {
    //Verify that all mandatory fields (such as Customer id, Account type and Initial deposit) are present on the account creation page.
    
    
    // Verify that mandatory fields are present
    WebElement customerIdField = driver.findElement(By.name("cusid"));
    WebElement accountTypeField = driver.findElement(By.name("selaccount"));
    WebElement initialDepositField = driver.findElement(By.name("inideposit"));
    Assertions.assertNotNull(customerIdField);
    Assertions.assertNotNull(accountTypeField);
    Assertions.assertNotNull(initialDepositField);
    
    // Verify that we are on the account creation page
    Assertions.assertTrue(driver.getCurrentUrl().contains("addAccount.php"));

  }
  
  @Test
  public void TC003() throws InterruptedException{
    //Verify that an error message is displayed if the user tries to submit the account creation form without filling in all mandatory fields.
    
      // Navigate to the New Account page
      driver.get("https://demo.guru99.com/V4/manager/addAccount.php");
      
      // Click the Submit button without entering any data
      driver.findElement(By.name("button2")).click();
      
      // Verify that an alert with the message "Please fill all fields" is displayed
      Alert alert = driver.switchTo().alert();
      String alertMessage = alert.getText();
      assertEquals("Please fill all fields", alertMessage);
      // Dismiss the alert
      alert.accept();
  }
  
  @Test
  public void TC004() throws InterruptedException {
      //Verify that the Customer id field, is not accepting characters since is not allowed (such numbers). 
      
      // Navigate to the New Account page
      driver.get("https://demo.guru99.com/V4/manager/addAccount.php");

      // Enter Customer ID
      driver.findElement(By.name("cusid")).sendKeys(customerID);

      // Add letters in the Customer id field with abcd
      driver.findElement(By.name("cusid")).sendKeys("abcd");

      // Click in next field button
      driver.findElement(By.name("inideposit")).click();

      // Verify the error message
      try {
          Alert alert = driver.switchTo().alert();
          String alertMessage = alert.getText();
          assertEquals("Characters are not allowed", alertMessage);

          // Dismiss the alert
          alert.accept();
      } catch (NoAlertPresentException e) {
          fail("Expected alert not found.");
      }
  }
  
      
 

  @Test
  public void TC005()throws InterruptedException{
    //Verify that the Initial deposit field, is not accepting characters since is not allowed (such numbers).
    
      // Navigate to the New Account page
     driver.get("https://demo.guru99.com/V4/manager/addAccount.php");

      // Enter Initial deposit
      WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
      WebElement       inideposit = wait.until(ExpectedConditions.visibilityOfElementLocated(By.name("inideposit")));
      
      // Clear any existing text in the field
      inideposit.sendKeys(Keys.CONTROL + "a", Keys.DELETE);
      
      // Enter the invalid input
      inideposit.sendKeys("abcd");
   
      // Click in next field button
      driver.findElement(By.name("cusid")).click();
  
      // Verify the error message
      Alert alert = driver.switchTo().alert();
      String alertMessage = alert.getText();
      assertEquals("Characters are not allowed", alertMessage);
      
      // Dismiss the alert
      alert.accept();

  }

  @Test
  public void TC006() throws InterruptedException {
     //Verify the results on entering a valid information for all fields for Savings Account 
    
    
    // Navigate to the New Account page
      driver.get("https://demo.guru99.com/V4/manager/Managerhomepage.php");

      // Enter Customer ID
      String customerId = "12345";
      WebElement customerIdInput = driver.findElement(By.name("cusid"));
      customerIdInput.sendKeys(customerId);

      // Select Savings Account
      WebElement accountTypeList = driver.findElement(By.name("selaccount"));
      Select select = new Select(accountTypeList);
      select.selectByVisibleText("Savings");

      // Enter Initial Deposit
      String initialAmountCurrent = "1000";
      WebElement initialDepositInput = driver.findElement(By.name("inideposit"));
      initialDepositInput.sendKeys(initialAmountCurrent);

      // Click the Submit button
      driver.findElement(By.name("button2")).click();
      

      // Verify the expected results
      String expectedMessage = "Account Generated Successfully!!!";
      WebElement successMessage = driver.findElement(By.cssSelector("#account > tbody > tr:nth-child(1) > td > p"));
      assertEquals(expectedMessage, successMessage.getText());

      String accountId = driver.findElement(By.cssSelector("#account > tbody > tr:nth-child(4) > td:nth-child(2)")).getText();
      System.out.println(accountId);

      String expectedCustomerId = customerId;
      WebElement actualCustomerIdElement = driver.findElement(By.xpath("//*[@id=\"account\"]/tbody/tr[5]/td[2]"));
      assertEquals(expectedCustomerId, actualCustomerIdElement.getText());

      String expectedCustomerName = "John Smith";
      WebElement actualCustomerNameElement = driver.findElement(By.cssSelector("#account > tbody > tr:nth-child(6) > td:nth-child(2)"));
      assertEquals(expectedCustomerName, actualCustomerNameElement.getText());

      DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
      LocalDateTime now = LocalDateTime.now();
      String expectedDateOfOpening = formatter.format(now);
      WebElement actualDateOfOpeningElement = driver.findElement(By.cssSelector("#account > tbody > tr:nth-child(9) > td:nth-child(2)"));
      assertEquals(expectedDateOfOpening, actualDateOfOpeningElement.getText());

      driver.quit();
  }


  @Test
  public void TC007() throws InterruptedException {
      //Verify the results on entering a valid information for all fields for Current Account
    
       // Navigate to the New Account page
          driver.get("https://demo.guru99.com/V4/manager/Managerhomepage.php");

      //Enter Customer ID
      driver.findElement(By.name("cusid")).sendKeys(customerID);
      
      //Select Savings Account
      //Find the list
      
      WebElement accountTypeList = driver.findElement(By.name("selaccount"));
      //Create Select object with a list we found
      
      
      Select select = new Select(accountTypeList);
      //select the option you want
      
      //select.selectByValue("Current");
      select.selectByVisibleText(driver.accountType_current);
    
      
      //Enter Inicial Deposit
      driver.findElement(By.name("inideposit")).sendKeys(inicialAmount_current);
      
       // Click the Submit button
        driver.findElement(By.name("button2")).click();
       
      //Checking the expected Results
      //Checking Successfully message
      String expectedResult = "Account Generated Successfully!!!";
      String actualResult = driver.findElement(By.cssSelector("#account > tbody > tr:nth-child(1) > td > p")).getText();
      
      assertEquals(expectedResult,actualResult);
      
      //AccountID
      String accountID = driver.findElement(By.cssSelector("#account > tbody > tr:nth-child(4) > td:nth-child(2)")).getText();
      System.out.println(accountID);
      
      //Checking CustomerID
      expectedResult = customerID;
      actualResult = driver.findElement(By.xpath("//*[@id=\"account\"]/tbody/tr[5]/td[2]")).getText();
      assertEquals(expectedResult, actualResult);
      
      //Checking Customer Name
      expectedResult = driver.name;
      actualResult = driver.findElement(By.cssSelector("#account > tbody > tr:nth-child(6) > td:nth-child(2)")).getText();
      
      //Checking the Date of Opening field
      DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd");
      LocalDateTime now = LocalDateTime.now();
      System.out.println(dtf.format(now));
      expectedResult = dtf.format(now);
      actualResult = driver.findElement(By.cssSelector("#account > tbody > tr:nth-child(9) > td:nth-child(2)")).getText();
      assertEquals(expectedResult,actualResult);
      
      
    }
  @Test 
  
  public void TC008() throws InterruptedException {
      //Check results a space before Customer ID field  and Initial deposit and try to submit the form.

      // Navigate to the New Account page
      driver.get("https://demo.guru99.com/V4/manager/addAccount.php");

      // Add letters in the Customer id field with space before ID
      driver.findElement(By.name("cusid")).sendKeys(" ");

      // Enter the invalid input
      driver.findElement(By.name("inideposit")).sendKeys(" ");

      // Enter Initial deposit
      WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
      WebElement initialDepositField = wait.until(ExpectedConditions.visibilityOfElementLocated(By.name("inideposit")));

      // Clear any existing text in the field
      initialDepositField.sendKeys(Keys.CONTROL + "a", Keys.DELETE);

      // Verify that an alert with the message "First character cannot have space" is displayed
      WebDriverWait wait2 = new WebDriverWait(driver, Duration.ofSeconds(10));
      Alert alert = wait2.until(ExpectedConditions.alertIsPresent());
      String alertMessage = alert.getText();
      assertEquals("First character cannot have space", alertMessage);

      // Dismiss the alert
      alert.accept();
  
  }
  }
