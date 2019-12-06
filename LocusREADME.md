# LocusAssignment
import static org.testng.Assert.assertTrue;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.testng.annotations.AfterTest;
import org.testng.annotations.BeforeTest;
import org.testng.annotations.Test;
import com.snc.testUtil.TestUtil;

/*
1.  Search - open www.flipkart.com (it would show a pop up, you need to cancel the pop up), perform a search for term 'shoes', 
    you now need to verify that the values on the search page are actually searched for shoes. 
2. Apply Filters - You can choose two filters - eg - price & brand. You need to ensure the filters are selected. 
   Again this is left a bit open-ended for you to come up with the asserts yourself. 
3. Product Detail Page - Open the detail page of the first result, select the size of the item & then click 'buy now'. 
   You should be on login page now. 
   
   PreRequisite: 1. User has to provide the minimum and max value from the given options.
                 2. User has to provide the correct spelling of brand name.
*/

public class LocusAssignment2 extends TestUtil {
	

	public WebDriver driver;
	public WebElement elem;
	boolean flag;
	int size;

	
	@BeforeTest
	public void setUp() {
		
	    initialize();
	  
	    pauseMe(5);
		
	}
	
	@Test
	public void searchItem() throws Exception {
		
		//POP-UP handle	
		try {
			  driver.findElement(By.xpath("//button[@class='_2AkmmA _29YdH8']")).click();
			}catch(Exception e) {
				
			  //System.out.println(e);
				
		     }
		
		 //To verify the search result shown is for given item 
		String searchitem = prop.getProperty("inputsearchitem");
		VerifySearchResult(searchitem);
		
		//To get all the available minimum and Maximum Price 
	    getMinAndMaxPrice();
		
		//Take user input for minimum and Maximum Price 
		 String minimumvalue = prop.getProperty("inputminimumvalue");
		 String maximumvalue = prop.getProperty("inputmaximumvalue");
		 
		//To select Minimum and Maximum price 	 
		 int minprice = Integer.parseInt(minimumvalue);
		 int maxprice = Integer.parseInt(maximumvalue);	 
		 selectMinAndMaxPrice(minimumvalue,maximumvalue);
		 
		 //To apply filter for given brand name and verify it 
		 String Brandvalue = prop.getProperty("inputBrandvalue"); 
		 applyBrandFilter(Brandvalue);
		 pauseMe(5);
	 	
		 //To verify the price filter 
	     VerifyPriceFilter(minprice, maxprice);
	     
         
	     // Code to open the detail page of the first item    
	     elem = driver.findElement(By.xpath("(//img[@class='_3togXc'])[1]"));
	     elem.click();
	     switchToChildWindow();	
	   
	     assertTrue(driver.findElement(By.xpath("//button[text()='BUY NOW']")).isEnabled());
	     assertTrue(driver.findElement(By.xpath("//button[text()='ADD TO CART']")).isEnabled());
	   
	   //To select the size
	   String sizevalue = prop.getProperty("inputsizevalue");
	   selectSize(sizevalue);
	   pauseMe(5);
		 
	   //Verify the user is on login page	 
		assertTrue( driver.findElement(By.xpath("//span[text()='Login or Signup']")).isDisplayed());
			  		   				   
	}
	
    @AfterTest
	public void tearDown() {
    	    driver.close();	
	}
	
	

}

//code for TestUtil class which contains all the method used in LocusAssignment2 test class

package newpack.com.testUtil;
import static org.testng.Assert.assertEquals;
import static org.testng.Assert.assertNotNull;
import java.util.List;
import org.openqa.selenium.By;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.ui.Select;
import com.snc.Base.TestBase;
public class TestUtil extends TestBase{
	
	public WebElement elem;
	boolean flag;
	int size;

 
	public void VerifySearchResult(String searchitem){
		
		elem = driver.findElement(By.xpath("//input[@title='Search for products, brands and more']"));
		assertNotNull(elem);
		elem.click();
		elem.sendKeys(searchitem);
		elem = driver.findElement(By.xpath("//button[@type='submit']"));
		elem.click();
		try {
		      driver.findElement(By.xpath("//button[@Class='_2AkmmA _29YdH8']")).click();
		}catch(Exception e) {
			
		}
		
		pauseMe(5);
		String ActualSearchResult = driver.findElement(By.xpath("//span[@Class='_2yAnYN']")).getText();
		System.out.println(ActualSearchResult);
		String [] result = ActualSearchResult.split(" ");
		System.out.println(result[8]);
		assertEquals(result[8],"\"shoes\"");
		
	}
	
	public void getMinAndMaxPrice(){
		
		//Code to provide list of minimum Price
		 List <WebElement> minvalues =      driver.findElements(By.xpath("//div[@Class='_1qKb_B']//select[@Class='fPjUPw']"));
		 size = minvalues.size();
		 for(int i=0; i<size; i++) { 
			System.out.println("min values avaialble is " + minvalues.get(i).getText());
		 }
		 
		//Code to provide list of maximum Price 
		 List <WebElement> maxvalues = driver.findElements(By.xpath("//div[@Class='_1YoBfV']//select[@Class='fPjUPw']"));
		 size = maxvalues.size();
		 for(int i=0; i<size; i++) { 
			System.out.println("max values avaialble is " + maxvalues.get(i).getText());
		 }
	}
	
	public void selectMinAndMaxPrice(String minimumvalue,String maximumvalue){
		
		 Select minvalue = new Select(driver.findElement(By.xpath("//div[@Class='_1qKb_B']//select[@Class='fPjUPw']")));
		 minvalue.selectByValue(minimumvalue);
		 
		 Select maxvalue = new Select(driver.findElement(By.xpath("//div[@Class='_1YoBfV']//select[@Class='fPjUPw']")));
		 maxvalue.selectByValue(maximumvalue);
		 
	}
	
	public void VerifyPriceFilter(int minprice,int maxprice) {
		
		// Code to verify price filter is applied for the searched result	
		   List<WebElement> elem = driver.findElements(By.xpath("//div[@Class='_1vC4OE']"));
		   
		   for(int i=1; i<2;i++) {
			   
			   String str = elem.get(i).getText();
			   System.out.println(str);
			   String actualPrice = str.replaceAll("[₹,]", "");
			   int price = Integer.parseInt(actualPrice);
			   System.out.println(price);
				
		         if(price>minprice && price<maxprice) {
			   
			            System.out.println(price + " is correct as per the filter applied");
		          }else {
			   
			          System.out.println(price + " is notcorrect as per the filter applied");
		          }	
		         
			}
		   
	}
	
	public void applyBrandFilter(String Brandvalue) {
		
		      //Code to apply the Brand filter 
				
				elem = driver.findElement(By.xpath("//input[@Placeholder='Search Brand']"));
				elem.clear();
				elem.sendKeys(Brandvalue);
				elem = driver.findElement(By.xpath("//div[@Class='_1GEhLw'][text()='"+Brandvalue+"']"));
				flag = elem.isSelected();
				if(flag==false) {
					elem.click();
				}else {
					System.out.println("given brand is already selected");
				}
				
				// Code to verify brand filter is applied for the searched result		
				List<WebElement> actualBrand= driver.findElements(By.xpath("//div[@Class='_2B_pmu']"));
				
				for(int i=1; i<2;i++) {
					   
					   String str = actualBrand.get(i).getText();
					   System.out.println(str);
					   assertEquals(str, Brandvalue);			  		  			   
			    }			
				
	}
	
	public void pauseMe(int seconds) {
	
		try {
			Thread.sleep (1000*seconds);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	public void switchToChildWindow() {
		
		for(String childWindow:driver.getWindowHandles()){
			   
			   driver.switchTo().window(childWindow);
		   }
	}
	
    public void selectSize(String sizevalue) {
    	
          	// Code to select the size
    			 size = Integer.parseInt(sizevalue);
    			 driver.findElement(By.xpath("//a[@Class='_1TJldG _2I_hq9 _2UBURg'][text()='"+sizevalue+"']")).click();
    			 flag = driver.findElement(By.xpath("//button[text()='BUY NOW']")).isEnabled();
    			 if(flag == true) {
    			   driver.findElement(By.xpath("//button[text()='BUY NOW']")).click();
    			 }else {
    				 
    				 System.out.println("Item is sold out, please try another item");			 
    			 } 	
    	
    }	 	
	
}


//Code for TestBase class which is having the code to read propery file and open browser it is extended By TestUtil class 

package newpack.com.Base;
import java.io.FileInputStream;
import java.util.Properties;
import java.util.concurrent.TimeUnit;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;

public class TestBase {
	
	public WebDriver driver;
	public Properties prop;
	
	
	public TestBase() {
		
		prop = new Properties();

		try {
			FileInputStream fis = new FileInputStream("/Users/trinitygeniustbs/git/PagefactoryTest/SeleniumTestNg/src/com/config/FlipkartData.properties");
			prop.load(fis);
			
		} catch( Exception e) {
			
			e.printStackTrace();	
		}
			
		
	}
	
	public void initialize() {
		
		String browsername = prop.getProperty("browser");
	
		if(browsername.equals("chrome")) {
		
		   System.setProperty("webdriver.chrome.driver","/Users/trinitygeniustbs/git/PagefactoryTest/SeleniumTestNg/chromedriver");
		   driver = new ChromeDriver();	
		   
		}else {
			
			System.setProperty("webdriver.gecko.driver","put the path of firefox driver.exe file");
			   driver = new FirefoxDriver();	
		}
		
		driver.manage().window().maximize();
		driver.manage().deleteAllCookies();
		driver.get(prop.getProperty("url"));
		driver.manage().timeouts().pageLoadTimeout(40, TimeUnit.SECONDS);
		driver.manage().timeouts().implicitlyWait(50, TimeUnit.SECONDS);	
	      	
	}

}


// FlipkartData.Properties.file which is used to provide the input data

browser = chrome
url= https://www.flipkart.com/
inputsearchitem = shoes
inputminimumvalue = 1000
inputmaximumvalue = 2000
inputBrandvalue = Puma
inputsizevalue = 8
