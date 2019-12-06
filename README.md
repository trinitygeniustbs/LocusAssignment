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
