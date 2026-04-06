# stackexchange  test technique



# StackExchange API – Cas de test fonctionnels 

## Objectif

Test technique 

## Test technique parti 1 quelques idées de tests à effectuer sur cette API

---

## TC01 – Appel nominal sans paramètres

**Objectif :** Vérifier la réponse par défaut  
**Préconditions :** API accessible  meme préconditions pour tous les cas de tests ( TC02 - TC15)


**Données :** GET /questions  
**Étapes :**  
1. Envoyer la requête GET  
**Résultats attendus :**  
- Code 200  
- JSON valide  
- `items` présent et non vide  

---

## TC02 – pagesize valide
**Objectif :** Tester une valeur valide  
**Données :** pagesize=10  
**Étapes :** GET /questions?pagesize=10  
**Résultats attendus :**  
- Code 200  
- ≤ 10 résultats  

---

## TC03 – pagesize négatif
**Objectif :** Tester valeur invalide  
**Données :** pagesize=-1  
**Étapes :** GET /questions?pagesize=-1  
**Résultats attendus :**  
- Code 400 ou message d’erreur  

---

## TC04 – pagesize limite
**Objectif :** Tester valeur maximale  
**Données :** pagesize=100  
**Étapes :** GET /questions?pagesize=100  
**Résultats attendus :**  
- Code 200  
- ≤ 100 résultats  

---

## TC05 – pagesize dépassement
**Objectif :** Tester dépassement limite  
**Données :** pagesize=101  
**Étapes :** GET /questions?pagesize=101  
**Résultats attendus :**  
- Erreur 400 ou valeur limitée à 100  

---

## TC06 – Filtrage par tag
**Objectif :** Vérifier filtre par tag  
**Données :** tagged=python  
**Étapes :** GET /questions?tagged=python  
**Résultats attendus :**  
- Code 200  
- Tous les résultats contiennent "python"  

---

## TC07 – Tag vide
**Objectif :** Tester cas limite  
**Données :** tagged=  
**Étapes :** GET /questions?tagged=  
**Résultats attendus :**  
- Erreur 400 ou comportement par défaut  

---

## TC08 – Pagination
**Objectif :** Vérifier continuité des pages  
**Données :** page=1, page=2  
**Étapes :**  
1. GET page=1  
2. GET page=2  
**Résultats attendus :**  
- Pas de doublons entre les pages  
- `has_more` cohérent  

---

## TC09 – Tri et ordre
**Objectif :** Vérifier tri  
**Données :** sort=activity, order=desc  
**Étapes :** GET avec paramètres  
**Résultats attendus :**  
- Liste triée du plus récent au plus ancien  

---

## TC10 – Sort invalide
**Objectif :** Tester valeur invalide  
**Données :** sort=invalid  
**Étapes :** GET avec sort invalide  
**Résultats attendus :**  
- Code 400, message d’erreur clair  

---

## TC11 – Mauvaise méthode HTTP
**Objectif :** Vérifier REST  
**Étapes :** Envoyer POST /questions  
**Résultats attendus :**  
- Code 405 Method Not Allowed  

---

## TC12 – Validation structure JSON
**Objectif :** Vérifier JSON  
**Étapes :** GET /questions  
**Résultats attendus :**  
- JSON valide  
- Champs présents : `items`, `has_more`  
- Chaque item contient `title`, `creation_date`  

---

## TC13 – Injection caractères spéciaux
**Objectif :** Tester robustesse  
**Données :** tagged=<script>alert(1)</script>  
**Étapes :** GET avec données  
**Résultats attendus :**  
- Pas de crash  
- Données échappées correctement  

---

## TC14 – UTF-8
**Objectif :** Tester encodage  
**Données :** tagged=économie  
**Étapes :** GET avec UTF-8  
**Résultats attendus :**  
- Réponse correcte, pas d’erreur d’encodage  

---

## TC15 – Rate limiting
**Objectif :** Vérifier quota API  
**Étapes :** Envoyer 50+ requêtes rapides  
**Résultats attendus :**  
- Limitation ou blocage (429)  



## Test technique partie 2 ( solution d'automatisation 

## Créez un projet Maven avec cette arborescence : ( en dessous vous pouvez aussi trouvez tous les détais qui correspond à cette arborscence )




src/

├── main/

│   └── java/

│       └── pages/

│           └── StackOverflowPage.java  // Page Object

└── test/

    ├── java/
    
    │   ├── steps/
    
    │   │   ├── ApiSteps.java
    
    │   │   └── WebSteps.java
    
    │   ├── hooks/
    
    │   │   └── Hooks.java
    
    │   └── runner/
    
    │       └── TestRunner.java
    
    └── resources/
    
        └── features/
        
            └── StackOverflowVerification.feature
pom.xml



## Ajoutez ces dépendances dans pom.xml : s'assurer des derniers versions qui sont stables pour les dépendences 

<dependencies>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-java</artifactId>
        <version>7.15.0</version>
    </dependency>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-junit</artifactId>
        <version>7.15.0</version>
    </dependency>
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.5.0</version>
    </dependency>
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>4.26.0</version>
    </dependency>
    <dependency>
        <groupId>io.github.bonigarcia</groupId>
        <artifactId>webdrivermanager</artifactId>
        <version>5.9.2</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.2</version>
    </dependency>
</dependencies>

##   Fichier Feature (Gherkin)   src/test/resources/features/StackOverflowVerification.feature :

Feature: Vérification synchronisation questions StackOverflow

  En tant qu'utilisateur, je veux vérifier que la première question API = première question web
  

  Scenario: Première question API correspond à la première sur la page Newest
  
    Given je récupère la première question de l'API StackExchange
    
    When je navigue sur la page StackOverflow questions Newest
    
    And je récupère le titre de la première question affichée
    
    Then les deux titres sont identiques

##  Page Object Model  src/test/java/pages/StackOverflowPage.java :

package pages;

import org.openqa.selenium.WebDriver;

import org.openqa.selenium.WebElement;

import org.openqa.selenium.support.FindBy;

import org.openqa.selenium.support.PageFactory;


public class StackOverflowPage {
    private WebDriver driver;

    @FindBy(css = "div.question-summary:first-of-type a.question-hyperlink")
    private WebElement firstQuestionTitle;

    public StackOverflowPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public String getFirstQuestionTitle() {
        return firstQuestionTitle.getText().trim();
    }
}

## Steps API (RestAssured)  src/test/java/steps/ApiSteps.java :

package steps;

import io.cucumber.java.en.Given;

import io.restassured.RestAssured;

import io.restassured.response.Response;

import io.restassured.path.json.JsonPath;


public class ApiSteps {
    private static String apiFirstTitle;

    @Given("je récupère la première question de l'API StackExchange")
    public void getFirstQuestionFromApi() {
        Response response = RestAssured
            .get("https://api.stackexchange.com/2.3/questions?order=desc&sort=creation&site=stackoverflow");
        JsonPath json = response.jsonPath();
        apiFirstTitle = json.getString("items[0].title");
    }

    public String getApiFirstTitle() {
        return apiFirstTitle;
    }
}

## Steps Web (Selenium) src/test/java/steps/WebSteps.java :

package steps;

import io.cucumber.java.en.When;

import io.cucumber.java.en.And;

import org.openqa.selenium.WebDriver;

import org.openqa.selenium.chrome.ChromeDriver;

import pages.StackOverflowPage;

import io.github.bonigarcia.wdm.WebDriverManager;


public class WebSteps {
    private WebDriver driver;
    private StackOverflowPage stackOverflowPage;
    private String webFirstTitle;

    @When("je navigue sur la page StackOverflow questions Newest")
    public void navigateToNewestQuestions() {
        WebDriverManager.chromedriver().setup();
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://stackoverflow.com/questions?tab=Newest");
        stackOverflowPage = new StackOverflowPage(driver);
    }

    @And("je récupère le titre de la première question affichée")
    public void getFirstQuestionFromWeb() {
        webFirstTitle = stackOverflowPage.getFirstQuestionTitle();
    }

    public String getWebFirstTitle() {
        return webFirstTitle;
    }
}


## Hooks (Gestion Driver)  src/test/java/hooks/Hooks.java :

package hooks;

import io.cucumber.java.After;

import org.openqa.selenium.WebDriver;


public class Hooks {
    private static WebDriver driver;

    public static void setDriver(WebDriver webDriver) {
        driver = webDriver;
    }

    @After
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}



## Step Definitions Principaux  src/test/java/steps/VerificationSteps.java :

package steps;

import io.cucumber.java.en.Then;


import io.cucumber.java.Before;

import steps.ApiSteps;

import steps.WebSteps;

import hooks.Hooks;


public class VerificationSteps {
    private ApiSteps apiSteps;
    private WebSteps webSteps;

    @Before
    public void setup() {
        apiSteps = new ApiSteps();
        webSteps = new WebSteps();
        Hooks.setDriver(webSteps.driver);  // Lien avec hooks
    }

    @Then("les deux titres sont identiques")
    public void verifyTitlesMatch() {
        apiSteps.getFirstQuestionFromApi();  // Exécuté avant via Given
        String apiTitle = apiSteps.getApiFirstTitle();
        String webTitle = webSteps.getWebFirstTitle();
        assert apiTitle.equals(webTitle) : "Titres ne correspondent pas: API='" + apiTitle + "', Web='" + webTitle + "'";
    }
}

## Test Runner  src/test/java/runner/TestRunner.java :

package runner;

import io.cucumber.junit.Cucumber;

import io.cucumber.junit.CucumberOptions;

import org.junit.runner.RunWith;

@RunWith(Cucumber.class)


@CucumberOptions(

    features = "src/test/resources/features",
    
    glue = {"steps", "hooks"},
    
    plugin = {"pretty", "html:target/cucumber-reports"}
    
)
public class TestRunner {

}


