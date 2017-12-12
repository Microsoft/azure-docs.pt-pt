# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chamar o Microsoft Graph API a partir de uma aplicação de ambiente de trabalho do Windows

Este guia demonstra como uma aplicação .NET de ambiente de trabalho do Windows (XAML) nativo pode obter um token de acesso e chamar outras APIs que necessitam de tokens de acesso a partir do ponto final do Azure Active Directory v2 ou Microsoft Graph API.

No final deste guia, a aplicação será possível chamar uma API protegida com contas pessoais (incluindo outlook.com, live.com e outros), bem como o trabalho contas escolares ou profissionais de qualquer da empresa ou organização que tenha o Azure Active Directory.  

> Este guia requer o Visual Studio 2015 Update 3 ou Visual Studio 2017.  Não o tiver? [Transferir o Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>Como funciona este guia

![Como funciona este guia](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

A aplicação de exemplo criada por este guia permite que uma aplicação de ambiente de trabalho do Windows consultar o Microsoft Graph API ou uma API Web que aceita tokens de ponto final do Azure Active Directory v2. Neste cenário, um token é adicionado a pedidos de HTTP através do cabeçalho de autorização. Aquisição do token e a renovação é processado pela biblioteca de autenticação do Microsoft (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Processamento de aquisição de token para aceder ao protegidos APIs da Web

Depois do utilizador é autenticado, a aplicação de exemplo recebe um token que pode ser utilizado para consultar o Microsoft Graph API ou uma API Web protegida pelo Microsoft Azure Active Directory v2.

APIs, tais como o Microsoft Graph requerem um token de acesso para permitir o acesso a recursos específicos – por exemplo, um perfil de utilizador, calendário do utilizador de acesso de leitura ou enviar mensagens de correio eletrónico. A aplicação pode pedir um token de acesso utilizando MSAL para aceder a estes recursos, especificando os âmbitos de API. Este token de acesso é então adicionada ao cabeçalho de autorização de HTTP para cada chamada efetuada em relação ao recurso protegido. 

MSAL gere a colocação em cache e atualizar os tokens de acesso para si, pelo que não tem da aplicação.


### <a name="nuget-packages"></a>Pacotes NuGet

Este guia utiliza os seguintes pacotes de NuGet:

|Biblioteca|Descrição|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteca de autenticação da Microsoft (MSAL)|

