---

copyright:
  years: 2017
lastupdated: "2017-11-02"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen:.screen}
{:codeblock: .codeblock}


# Configurando o iOS Swift SDK
{: #getting-started-ios}

Construa os seus apps Swift com o SDK do cliente do {{site.data.keyword.appid_short}}, inicialize o SDK, autentique usuários e faça solicitações
para recursos protegidos e desprotegidos.
{:shortdesc}


## Antes de Começar
{: #before-you-begin}

As seguintes informações são necessárias:
  * Uma instância de {{site.data.keyword.appid_short_notm}}.
  * O seu ID do locatário.
    * Na guia **Credenciais de serviço** de seu painel de serviço, clique em **Visualizar credenciais**. O seu ID do
locatário é exibido no campo **TenantID**. Esse valor é usado para inicializar o seu app.
  * A sua região do {{site.data.keyword.Bluemix_notm}}.
  É possível localizar a sua região procurando na UI. O valor é usado para inicializar o seu app.
    <table> <caption> Tabela 1. Regiões e valores do SDK correspondentes do {{site.data.keyword.Bluemix_notm}} </caption>
    <tr>
      <th> Região do Bluemix </th>
      <th> Valor do SDK </th>
    </tr>
    <tr>
      <td> Sul dos Estados Unidos </td>
      <td> AppID.REGION_US_SOUTH </td>
    </tr>
    <tr>
      <td> Sydney </td>
      <td> AppID.REGION_SYDNEY </td>
    </tr>
    <tr>
      <td> United Kingdom </td>
      <td> AppID.REGION_UK </td>
    </tr>
  </table>

  * Um projeto Xcode (versão 8.1 ou superior).
  * CocoaPods (versão 1.1.0 ou superior).


## Instalando o SDK do cliente
{: #install-appid-sdk}

O SDK do cliente do {{site.data.keyword.appid_short_notm}} é distribuído com o CocoaPods, um gerenciador de dependência para projetos Swift e
Objective-C Cocoa. O CocoaPods faz download de artefatos e os torna disponíveis para o seu projeto.

1. Crie um projeto Xcode ou abra um projeto existente.
2. Abra ou crie o arquivo pod no diretório do projeto.
3. Sob o seu destino de projeto inclua uma dependência para o pod 'BluemixAppID'. Certifique-se de que o comando `use_frameworks!` também
esteja sob o seu destino.

  Por exemplo:

  ```swift
  target '<yourTarget>' do
     use_frameworks!
     pod 'BluemixAppID'
  end
  ```
  {: codeblock}

4. Para fazer download da dependência `BluemixAppID`, execute o comando a seguir.

  ```swift
  pod install --repo-update
  ```
  {: codeblock}

6. Abra o seu projeto Xcode e ative o compartilhamento de keychain. Sob **Configurações de projeto**, clique
em **Recursos** >
**Compartilhamento de Keychain**.
7. Sob **Configurações de projeto** > **Informações** > **Tipos de URL**, inclua um Tipo de URL. Preencha
ambas as caixas de texto de **Identificador** e de **Esquema URL** com este valor:
$(PRODUCT_BUNDLE_IDENTIFIER)


## Inicializando o client SDK
{: #initialize-client-sdk}

1. Inclua a importação a seguir em seu arquivo `AppDelegate.swift`.

  ```swift
  import BluemixAppID
  ```
  {: codeblock}

2. Inicialize o SDK do cliente passando os parâmetros de ID do locatário e região para o método de inicialização. Um local comum, mas não obrigatório, para
colocar o código de inicialização está no método application:didFinishLaunchingWithOptions: do AppDelegate em seu aplicativo Swift.

  ```swift
  AppID.sharedInstance.initialize(tenantId: <tenantId>, bluemixRegion: AppID.Region_UK)
  ```
  {: codeblock}

  * Substitua o tenantId pelo ID do locatário para o seu serviço de ID do App
  * Substitua AppID.REGION_UK pela sua região do {{site.data.keyword.appid_short_notm}}.

3. Inclua o código a seguir em seu arquivo AppDelegate.

  ```swift
  func application(_ application: UIApplication, open url: URL, options :[UIApplicationOpenURLOptionsKey : Any]) -> Bool {
          return AppID.sharedInstance.application(application, open: url, options: options)
      }
  ```
  {: codeblock}

## Autentique os usuários usando o widget de login
{: #authenticate-login}


Após o SDK do cliente do {{site.data.keyword.appid_short_notm}} ser inicializado, será possível autenticar os seus usuários executando o widget de login. A configuração padrão do widget de login usa o Facebook e o Google como opções de autenticação. Se você configurar apenas um provedor de identidade, o widget de login não será iniciado e o usuário será redirecionado para a tela de autenticação do IDP configurado.





1. Inclua a importação a seguir no arquivo no qual você deseja usar o SDK.

  ```swift
  import BluemixAppID
  ```
  {: codeblock}

2. Execute o comando a seguir para ativar o widget.

  ```swift
  class delegate : AuthorizationDelegate {
      public func onAuthorizationSuccess(accessToken: AccessToken, identityToken: IdentityToken, response:Response?) {
          //User authenticated
      }

      public func onAuthorizationCanceled() {
          //Authentication canceled by the user
      }

      public func onAuthorizationFailure(error: AuthorizationError) {
          //Exception occurred
      }
  }

  AppID.sharedInstance.loginWidget?.launch(delegate: delegate())
  ```
  {: codeblock}

## Acessando atributos do usuário
{: #accessing}

Ao obter um token de acesso, é possível obter acesso ao terminal protegido de atributos do usuário. Isso é feito usando os métodos de API a seguir.

  ```swift
  func setAttribute(key: String, value: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  func setAttribute(key: String, value: String, accessTokenString: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)

  func getAttribute(key: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  func getAttribute(key: String, accessTokenString: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)

  func getAttributes(completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  func getAttributes(accessTokenString: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)

  func deleteAttribute(key: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  func deleteAttribute(key: String, accessTokenString: String, completionHandler: @escaping(Error?, [String:Any]?) -> Void)
  ```
  {: codeblock}

Quando um token de acesso não for transmitido explicitamente, o {{site.data.keyword.appid_short_notm}} usará o último token recebido.

Por exemplo, é possível chamar o código a seguir para configurar um novo atributo ou substituir um existente.

  ```swift
  AppID.sharedInstance.userAttributeManager?.setAttribute("key", "value", completionHandler: { (error, result) in
      if error = nil {
          //Attributes recieved as a Dictionary
      } else {
          // An error has occurred
      }
  })
  ```
  {: codeblock}


### Login anônimo
{: #anonymous notoc}

Com o {{site.data.keyword.appid_short_notm}} é possível efetuar login [anonimamente](/docs/services/appid/user-profile.html#anonymous).

  ```swift
  class delegate : AuthorizationDelegate {

      public func onAuthorizationSuccess(accessToken: AccessToken, identityToken: IdentityToken, response:Response?) {
          //User authenticated
      }

      public func onAuthorizationCanceled() {
          //Authentication canceled by the user
      }

      public func onAuthorizationFailure(error: AuthorizationError) {
          //Error occurred
      }
   }

  AppID.sharedInstance.loginAnonymously( authorizationDelegate: delegate())
  ```
  {: codeblock}

### Autenticação progressiva
{: #progressive notoc}

Quando você retém um token de acesso anônimo, o usuário pode se tornar um usuário identificado passando-o para o método `loginWidget.launch`.

  ```swift
  func launch(accessTokenString: String? , delegate: AuthorizationDelegate)
  ```
  {: codeblock}

Após um login anônimo, a autenticação progressiva ocorrerá mesmo se o widget de login for chamado sem passar um token de acesso porque o serviço usou o último
token recebido. Se desejar limpar os tokens armazenados, execute o comando a seguir.

  ```swift
  var appIDAuthorizationManager = AppIDAuthorizationManager(appid: AppID.sharedInstance)
  appIDAuthorizationManager.clearAuthorizationData()
  ```
  {: codeblock}



## Próximas etapas
{: #next-steps}

O {{site.data.keyword.appid_short_notm}} fornecerá uma configuração padrão quando você inicialmente configurar os seus provedores de identidade. É
possível usar a configuração padrão apenas no modo de desenvolvimento. Antes de publicar o seu aplicativo, [atualize a configuração padrão para as suas próprias credenciais](/docs/services/appid/identity-providers.html).
