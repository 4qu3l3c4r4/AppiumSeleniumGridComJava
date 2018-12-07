# Rodando testes em paralelo com Appium, Selenium Grid e Java: Parte 1

Estive estudando um pouco sobre como rodar testes em paralelo e a minha maior curiosidade era como poderia executar testes em paralelo para diferentes tipos de plataforma. Para isso tive que entender e estudar o funcionamento de algumas ferramentas antes. Nesta série quero compartilhar minha experiência com vocês de como eu fiz isso, e fazer um tutorial um pouco mais mastigado pra vocês, para que no tenham tantos problemas quanto eu e veja que testar em paralelo não é um bicho de sete cabeças!

## Ferramentas necessárias para configuração inicial dos testes.

* **Configuração Android Home** - https://gist.github.com/ericaroy/d08a846daeebe145d2ef
* **Configuração Java Home** - https://www.mkyong.com/java/how-to-set-java_home-environment-variable-on-mac-os-x/
* **Eclipse ou IDE de preferencia** - https://www.eclipse.org/downloads/
* **Java 8** - https://www.java.com/pt_BR/download/
* **Appium** - http://appium.io/
* **Node** - https://nodejs.org/en/download/
* **Android Emulator** - https://developer.android.com/studio/run/managing-avds.html?hl=pt-br
* **Maven** - http://brewformulas.org/Maven
* **Xcode** - https://developer.apple.com/xcode/

## Let's Go!

### Configuração Inicial

Vamos primeiramente criar o nosso projeto de testes, abra sua IDE, e crie um projeto maven (no meu caso vou usar o eclipse):

```File > New > Maven > Maven Project (Eclipse)```

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Projeto-Maven-Eclipse.png "Novo Projeto Maven")


Escolha **maven-archetype-quickstart 1.1**, para gerar um projeto mais simples possível.

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Arquitetura-Projeto.png "Tipo Arquitetura")

Perfeito, agora vamos no nosso arquivo **pom.xml** e vamos adicionar todas as dependências e plugins que vamos usar no nosso projeto.

* **Java-Client Appium**

```Xml
<dependency>
  <groupId>io.appium</groupId>
  <artifactId>java-client</artifactId>
  <version>4.1.2</version>
</dependency>
```

* **Surefire Maven Plugin**

```Xml
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-surefire-plugin</artifactId>
      <version>2.19.1</version>
    </plugin>
  </plugins>
</build>
 ```

Lembrando que no nosso projeto já vem setado o Junit, vamos somente alterar a versão dele para 4.7.

* **Junit 4**

```Xml
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.7</version>
  <scope>test</scope>
  </dependency>
</dependencies>
 ```

Depois da configuração do **pom.xml**, vamos criar um diretório que vamos colocar os nossos apps.

```File > New > Folder > [folder-name]```

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Nova-Pasta.png "Tela Criação de Pastas")

Vou colocar aqui também os arquivos dos app para download:

* [APK-ANDROID](https://github.com/3qu3l3c4r4/AppiumSeleniumGridComJava/raw/master/apps/app-android-calculator.apk)
* [APP-IOS](https://drive.google.com/open?id=1A8Kw9AFb0SQTjb-dlOY16W1Y0FwBP-70)

Faço o download deles e jogue na pasta que vc acabou de criar!

## Criação das Classes

Ufa, depois de todas essas configurações podemos começar a criar nossas classes, a idéia seria utilizar o padrão Page Object, nos nossos testes. Vamos também testar aplicações semelhantes onde a aplicação IOS tem as mesmas funcionalidades que a aplicação android! Dentro da nossa estrutura **src/test/java/meu-pacote-de-tests** já existe uma classe de teste que foi criado junto com nossa estrutura. (pode excluir ela!)

 Vamos criar um outro pacote que ficará todas as nossas screens.

 ```File > New > Package > [package-name]```

![alt text](https://github.com/4qu3l3c4r4/AndroidAndIosTesteParaleloComAppium/blob/master/imagens/Package-Screen.png "Tela Criação de Pastas")

E dentro deste pacote vamos criar a classe screen que vai contemplar todos elementos da tela da nossa aplicação.

```Java
public class CalculatorScreen {

private AppiumDriver<MobileElement> driver;

public CalculatorScreen(AppiumDriver<MobileElement> driver)  {
	PageFactory.initElements( new AppiumFieldDecorator(driver), this);
	this.driver = driver;
  }

}
``` 


Esse é o início da nossa classe e a parte mais importante rsrsrs, o PageFactory responsável por buscar todos os meus elementos em tela, para isso precisamos de uma instância do nosso driver e do contexto da page. Criamos o PageFactory no meu construtor porque sempre criar uma instância da minha CalculatorScreen ele vai buscar todos os seu elementos com o driver específico que recebermos no construtor. Agora vamos pegar os elementos da tela, não vou mostrar como fazer isso aqui e nem como configurar os emuladores mais posso ajudar com os links, existem diversas ferramentas para isso!


### Cofigurando Emuladores

* [Genymotion Emulador Android](https://www.genymotion.com/)
* [Android Studio AVD](https://developer.android.com/studio/run/managing-avds.html?hl=pt-br)
* [Para IOS eu indico realmente instalar o Xcode!!](https://developer.apple.com/xcode/)

### Inspetores de tela

* [MacacaJs](https://medium.com/@deyvirsonmendona/inspecionando-elementos-no-app-ios-com-macacajs-cad962719ce2)
* [UIAutomatorviewer](https://www.guru99.com/uiautomatorviewer-tutorial.html)
* [Arc](https://github.com/appium/ruby_console)


E depois de pegar os elementos podemos atualizar nossa page, e agora temos esse resultado :


```Java
public class CalculatorScreen {

private AppiumDriver<MobileElement> driver;

public CalculatorScreen(AppiumDriver<MobileElement> driver)  {
	PageFactory.initElements( new AppiumFieldDecorator(driver), this);
	this.driver = driver;
}

  @AndroidFindBy(id = "android_button_sum")
	@iOSFindBy(accessibility = "apple-sum-button")
	public RemoteWebElement buttonSum;

  @AndroidFindBy(id = "android_result_text")
	@iOSFindBy(accessibility = "apple_result_text")
	private RemoteWebElement resultText;

  @AndroidFindBy(id = "android_field_first_number")
	@iOSFindBy(accessibility = "apple_first_input")
	private RemoteWebElement inputFirstNumber;

	@AndroidFindBy(id = "android_field_second_number")
	@iOSFindBy(accessibility = "apple_second_input")
	private RemoteWebElement inputSecondNumber;
}
```


Reparem nas annotations, dentro delas especificamos qual será o nosso locator, temos os locators separados tanto do android como o do ios em apenas uma variável! Visivelmente não temos aquele findElement(By())... Consegue visualizar a abstração da nossa page? Agora vamos finalizar nossa classe criando os métodos com as ações da page.

```Java
public CalculatorScreen fillFirstNumber(String number) {
	inputFirstNumber.click();
	inputFirstNumber.clear();
	driver.getKeyboard().sendKeys(number);
	return this;
}

public CalculatorScreen fillSecondNumber(String number) {
	inputSecondNumber.click();
	inputSecondNumber.clear();
	driver.getKeyboard().sendKeys(number);
	return this;
}

public CalculatorScreen closeKeyboard() {
	driver.getKeyboard().pressKey(Keys.RETURN);
	return this;
}

public String operationResult() {
	return resultText.getText().toString().trim();
}

public void quitDriver() {
	driver.quit();
} 	
```  

Beleza! Vamos criar as duas classes de teste para android e ios, dentro do pacote **src/test/java/meu-pacote-de-tests**


 * **Classe de Teste Android**

```Java
  public class TestAndroidCalculator {

	private static CalculatorScreen calculatorScreen;
	private static AppiumDriver<MobileElement> appiumDriver;

	@BeforeClass
	public static void setup() throws MalformedURLException {
	  DesiredCapabilities capabilities = new DesiredCapabilities();
	  capabilities.setCapability("app",new File("apps/app-android-calculator.apk"));
	  capabilities.setCapability("platformName","Android" );
	  capabilities.setCapability("deviceName","Android Emulator API 22");
	  capabilities.setCapability("unicodeKeyboard", true);
	  capabilities.setCapability("disableAndroidWatchers" , true);
	  appiumDriver = new AndroidDriver<MobileElement>(new URL("http://localhost:4723/wd/hub") , capabilities);
	  calculatorScreen = new CalculatorScreen(appiumDriver);
	}

	@Test
	public void shouldSum() {
	  calculatorScreen.fillFirstNumber("10").fillSecondNumber("10").buttonSum.click();
	  assertTrue(calculatorScreen.operationResult().equals("20"));
	}

	@AfterClass
	  public static void teardown() {
	  calculatorScreen.quitDriver();
	}
}
```

* **Classe de Teste IOS**

```Java
public class TestIosCalculator {

	private static CalculatorScreen calculatorScreen;
	private static AppiumDriver<MobileElement> appiumDriver;

	@BeforeClass
	public static void setup() throws MalformedURLException {
	  DesiredCapabilities capabilities = new DesiredCapabilities();
	  capabilities.setCapability("app",new File("apps/SimpleCalculator.app"));
	  capabilities.setCapability("plataform", "MAC" );
	  capabilities.setCapability("plataformName", "ios" );
	  capabilities.setCapability("deviceName", "iPhone SE");
	  capabilities.setCapability("automationName" , "XCUITest");
	  appiumDriver = new IOSDriver<MobileElement>(new URL("http://localhost:4723/wd/hub") , capabilities);
	  calculatorScreen = new CalculatorScreen(appiumDriver);
	}

	@Test
	public void should_sum() {
	  calculatorScreen.fillFirstNumber("10").fillSecondNumber("10").closeKeyboard().buttonSum.click();
	assertTrue(calculatorScreen.operationResult().equals("20"));
	}

	@AfterClass
	public static void teardown() {
	  calculatorScreen.quitDriver();
	}
}
```


Os casos de teste são bem simples, estão apenas verificando se a aplicação está realizando corretamente o fluxo da soma. Especifiquei também o capabilities de cada dispositivo, e vou deixar um link aqui explicando o
que faz cada capability.

Show, agora já podemos rodar os testes. Inicie seu emulador android (não precisa iniciar o simulador ios) e inicie o appium como de costume com o comando :


```
$ appium
```


Prontinho agora só rodar os testes, para isso vamos aproveitar e usar o maven! Dentro do diretório **AndroidAndIosTesteParaleloComAppium/** execute o comando :

```
$ mvn clean test
```

Tcharannn!!

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Success-Tests.png "Test Success")


Foi executado um teste de cada vez, próximo post vou focar no selenium grid e como vamos fazer nossa estrutura atual rodar em paralelo no selenium grid.


Obrigado <3


### Refrências

* **Repositorio deste Projeto :** https://github.com/3qu3l3c4r4/AppiumSeleniumGridComJava
* **Appium capabilities :** https://appium.io/docs/en/writing-running-appium/caps/
* **Meu Git Hub :** https://github.com/3qu3l3c4r4/


# Rodando testes em paralelo com Appium, Selenium Grid e Java: Parte 2

Fala pessoal beleza ? Neste post vou dar continuidade com a configuração do selenium grid para executar os nossos testes em paralelo, vou falar um pouco de como funciona a estrutura e tentar frisar partes importantes da configuração, para que fique o mais simples possível. Bom se você não viu a primeira parte deste post eu super recomendo que veja, porque vou utilizar a estrutura que já tinha criado anteriormente. Sem mais delongas vamos começar!

## O que é o selenium grid ?

Bom de forma bem resumida o selenium grid é um hub em que nos permite centralizar , paralelizar e distribuir nossos testes em nós que nele são registrados.
Como funciona ?

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Workflow-Selenium-Grid.jpeg "Workflow Selenium Grid")


No desenho acima conseguimos visualizar os scripts de testes, o hub do selenium grid, os nós com servidores appium e os devices que vão rodar os nossos testes, vou explicar o papel de cada um nessa imagem.
Scripts de teste : São os nossos testes propriamente ditos, aqui ficam os comandos que devem ser executados pelos nossos testes, nessa imagem seria o client side da estrutura.
Terminal com servidores appium : Representam cada um dos nossos servidores que vamos subir com configurações diferentes para cada plataforma e local de execução. Eles ficam entre o device ou simulador e selenium hub.
Hub selenium grid : É um outro servidor que serve como um hub, para que seja possível registrar outros nós de servidores appium, nele conseguimos acessar um console com todos os nós nele conectado. O selenium grid fica como uma ponte entre os scprits de teste e os nós de appium.
Legal, agora que fizemos esse overview vamos colocar a mão na massa!
Mãos à obra!
________________________________________
Realizando ajustes na estrutura do projeto atual
Antes de começar tudo vamos fazer alguns ajustes na nossa estrutura atual do projeto, como demorei um pouco para continuar este post, algumas coisas precisam e precisaram ser alteradas, então vamos lá!

## Alterar JRE System Library para JavaSE-1.8 (Somente se necessário)

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Alterando-JRE-System-Library.gif "Alterando JRE System Library")

Vamos definir que esta vai ser a versão do java vamos usar para compilar o nosso projeto, vamos setar isso no arquivo pom.xml:

```Xml
<properties>
	  <maven.compiler.source>1.8</maven.compiler.source>
	  <maven.compiler.target>1.8</maven.compiler.target>
	</properties>
```

## Definindo compilador

Bom depois desses ajustes vamos adicionar uma configuração no surefire, para fazermos com que nossos testes executem em paralelo com duas threadsabertas e execute em paralelo pelas classes de teste.


```Xml
	<configuration>
	  <source>1.8</source>
	  <target>1.8</target>
	  <parallel>classes</parallel>
	  <threadCount>2</threadCount>
	</configuration>
```

Configuração surefire para execução de testes em paralelo

Vamos atualizar também a nossa lib java cliente


```Xml
	<dependency>
	 <groupId>io.appium</groupId>
	 <artifactId>java-client</artifactId>
	 <version>6.1.0</version>
	</dependency>
```

**Atualização do Java Client**

Quando fui executar os testes me deparei com algumas incompatibilidades em padrões que antes funcionavam e fiz algumas atualizações no código!

**alterando o capabilities e chamadas de métodos do appium driver**

```Java
	public CalculatorScreen fillFirstNumber(String number) {
	  inputFirstNumber.clear();
	  inputFirstNumber.sendKeys(number);
	  return this;
	}
		
	public CalculatorScreen fillSecondNumber(String number) {
	  inputSecondNumber.clear();
	  inputSecondNumber.sendKeys(number);
	  return this;
	}
		
	public CalculatorScreen closeKeyboard() {
	  driver.hideKeyboard();
	  return this;
	}
```
**Alteração do métodos de preenchimento e de esconder o teclado

```Java
	@BeforeClass
	public static void setup() throws MalformedURLException {
	  DesiredCapabilities capabilities = new DesiredCapabilities();
	  capabilities.setCapability("app",new File("apps/SimpleCalculator.app"));
	  capabilities.setCapability("deviceName", "iPhone SE");
	  capabilities.setCapability("automationName" , "XCUITest");
	  appiumDriver = new IOSDriver<MobileElement>(new URL("http://localhost:4444/wd/hub") , capabilities);
	  calculatorScreen = new CalculatorScreen(appiumDriver);
	}
```

**Alteração dos capabilities removendo plataform name e plataform**

É importante salientar que é necessário atualizar a url em que nosso cliente vai se conectar, tanto do teste de Android quanto o de Ios, conforme visualizamos mais acima, agora vamos nos conectar utilizando somente o hub!
________________________________________

### Configurando Selenium Grid

Finalmente após estes ajustes podemos agora fazer a configuração do selenium grid!
Para iniciar vamos fazer o download do jar do selenium standalone que será o nosso hub. Após o download vamos colar o arquivo na raiz do nosso projeto.
Agora vamos criar um nó para cada sistema operacional diferente, esse nós são arquivos json, neles serão escritos as configurações que o nó deve possuir.
Lembrando que coloquei estas pequenas descrições no arquivo para resumir alguns atributos de configuração do node, não é possível comentar arquivos json!

```Java
	{
	

	    "capabilities":
	        [
	            {
	                "browserName": "Android", // Nome de browser que será alocado no grid
	                "maxInstances": 1, // Quantidade total de instâncias dentro do nó
	                "platform":"ANDROID", // Plataforma em que o teste será executado o teste
	                "deviceName":"emulator-5554", // udid do device android
	                "newCommandTimeout":"30", 
	                "deviceReadyTimeout":5
	            }
	        ],
	    "configuration":
	    {
	        "cleanUpCycle":2000,
	        "timeout":10800,
	        "url":"http://127.0.0.1:4456/wd/hub", // url deste nó dentro do hub
	        "host": "127.0.0.1", // host deste nó
	        "port": 4456, // porta em que nó será executado
	        "proxy": "org.openqa.grid.selenium.proxy.DefaultRemoteProxy",
	        "maxSession": 1, // quantidade total de sessões simultâneas 
	        "register": true,
	        "registerCycle": 5000,
	        "hubPort": 4444, // porta do hub em que este nó vai se conectar
	        "hubHost": "127.0.0.1" // host do hub em que este nó vai se conectar
	    }
	    
	}
```

## Android Node Capabilities

```Java
	{
	    "capabilities": [
	        {
	            "browserName": "safari",
	            "technologyPreview": false,
	            "version": "11.0",
	            "platform": "MAC",
	            "platformName": "ios",
	            "maxInstances": 1,
	            "seleniumProtocol": "WebDriver"
	        }
	    ],
	    "configuration": {
	        "cleanUpCycle": 2000,
	        "timeout": 10800,
	        "url": "http://127.0.0.1:4455/wd/hub",
	        "host": "127.0.0.1",
	        "port": 4455,
	        "proxy": "org.openqa.grid.selenium.proxy.DefaultRemoteProxy",
	        "maxSession": 1,
	        "register": true,
	        "registerCycle": 5000,
	        "hubPort": 4444,
	        "hubHost": "127.0.0.1"
	    }
	}
```

## IOS Node Capabilities
Após criar os arquivos salve-os e coloque os dois juntos em uma pasta chamada /node-configurations/ na raiz do seu projeto.
Não vou entrar muito no detalhe de todos os atributos pois o post ficaria gigante rsrs. Mas você pode dar uma olhada nesta documentação.
________________________________________
Inicializando HUB e registrando os nós.

**iniciar hub ```$ java -jar selenium-server-standalone-3.14.0.jar -role hub (lembre-se de inserir a sua versão do jar)```

** verifique se o hub foi iniciado corretamente acessando em seu navegador o endereço * **http://localhost:4444/**

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Selenium-Standalone-Page.png "Selenium Standalone Page")

Agora vamos conectar nossos nós ao hub :
```appium --nodeconfig ./ios-node.json -p 4455 -cp 4455```

```appium --nodeconfig ./android-node.json -p 4456 -cp 4456```

**Após realizado os comandos acima precisamos confirmar se tudo está conectado de forma correta, para isso vamos acessar o console da nossa página do selenium standalone * **http://localhost:4444/grid/console**

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Console-Selenium-Grid.png "Console Selenium Grid")

Tudo certo ? Espero que sim!
Executando os testes!
Inicie seu emulador android e vamos rodar os testes com o maven e ver a magia acontecer!
Dentro do diretório raiz em que se encontra o arquivo pom.xml vamos executar o seguinte comando :
```$ mvn clean test```

**Mágico não ?

![alt text](https://github.com/4qu3l3c4r4/AppiumSeleniumGridComJava/blob/master/imagens/Testes-executados-com-sucesso-e-em-paralelo.png "Testes executados com sucesso e em paralelo ❤")

### Conclusão

Mesmo que nesse projeto de exemplo, não tenha aproveitado toda capacidade do selenium-grid podemos ver algumas vantagens em seu uso para execução de testes distribuídos! Podemos ter nós por diversos sistemas operacionais centralizados em um único hub, podemos também executar os mesmos testes distribuídos e em paralelo conforme a nossa necessidade, o que torna nossos testes muito mais escaláveis!

### Referências

* **Selenium Grid**: https://www.seleniumhq.org/projects/grid/
* **Java Client**: https://github.com/appium/java-client
* **Appium Documentation**: http://appium.io/docs/en/about-appium/intro/
