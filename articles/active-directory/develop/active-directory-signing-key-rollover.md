---
title: Substituição de chave de assinatura no Azure AD
description: Este artigo discute as práticas recomendadas de substituição de chave de assinatura para Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3c696b3fed36ca8ee7faf6ec78f833191cedf9d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844656"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Substituição de chave de assinatura em Azure Active Directory
Este artigo discute o que você precisa saber sobre as chaves públicas que são usadas no Azure Active Directory (Azure AD) para assinar tokens de segurança. É importante observar que essas chaves passam periodicamente e, em uma emergência, podem ser transferidas imediatamente. Todos os aplicativos que usam o Azure AD devem ser capazes de manipular programaticamente o processo de substituição de chave ou estabelecer um processo de substituição manual periódico. Continue lendo para entender como as chaves funcionam, como avaliar o impacto da substituição para seu aplicativo e como atualizar seu aplicativo ou estabelecer um processo de substituição manual periódico para lidar com a substituição de chave, se necessário.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Visão geral das chaves de assinatura no Azure AD
O Azure AD usa a criptografia de chave pública criada com base nos padrões do setor para estabelecer confiança entre si mesmo e os aplicativos que o utilizam. Em termos práticos, isso funciona da seguinte maneira: o Azure AD usa uma chave de assinatura que consiste em um par de chaves pública e privada. Quando um usuário entra em um aplicativo que usa o Azure AD para autenticação, o Azure AD cria um token de segurança que contém informações sobre o usuário. Esse token é assinado pelo AD do Azure usando sua chave privada antes de ser enviado de volta para o aplicativo. Para verificar se o token é válido e originado do Azure AD, o aplicativo deve validar a assinatura do token usando a chave pública exposta pelo Azure AD que está contida no [documento de descoberta do OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) do locatário ou no [documento de metadados de Federação](azure-ad-federation-metadata.md)do SAML/WS-alimentado.

Para fins de segurança, a chave de assinatura do AD do Azure é revertida periodicamente e, no caso de uma emergência, pode ser transferida imediatamente. Qualquer aplicativo que se integre ao Azure AD deve estar preparado para lidar com um evento de substituição de chave, independentemente da frequência que possa ocorrer. Caso contrário, e seu aplicativo tentar usar uma chave expirada para verificar a assinatura em um token, a solicitação de entrada falhará.

Há sempre mais de uma chave válida disponível no documento de descoberta do OpenID Connect e no documento de metadados de Federação. Seu aplicativo deve estar preparado para usar qualquer uma das chaves especificadas no documento, já que uma chave pode ser revertida em breve, outra pode ser sua substituição e assim por diante.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Como avaliar se seu aplicativo será afetado e o que fazer a respeito
A maneira como seu aplicativo lida com a substituição de chave depende de variáveis como o tipo de aplicativo ou qual protocolo de identidade e a biblioteca foram usados. As seções a seguir avaliam se os tipos de aplicativos mais comuns são afetados pela substituição de chave e fornecem orientação sobre como atualizar o aplicativo para dar suporte à substituição automática ou atualizar manualmente a chave.

* [Aplicativos cliente nativos que acessam recursos](#nativeclient)
* [Aplicativos/APIs Web que acessam recursos](#webclient)
* [Aplicativos/APIs Web que protegem recursos e são criados usando Azure App Services](#appservices)
* [Aplicativos/APIs Web que protegem recursos usando .NET OWIN OpenID Connect, WS-enalimentado ou middleware WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Aplicativos/APIs Web que protegem recursos usando o .NET Core OpenID Connect ou o middleware JwtBearerAuthentication](#owincore)
* [Aplicativos/APIs Web que protegem recursos usando o módulo Passport-Azure-AD do node. js](#passport)
* [Aplicativos/APIs Web que protegem recursos e criados com o Visual Studio 2015 ou posterior](#vs2015)
* [Aplicativos Web protegendo recursos e criados com Visual Studio 2013](#vs2013)
* APIs da Web protegendo recursos e criados com Visual Studio 2013
* [Aplicativos Web protegendo recursos e criados com o Visual Studio 2012](#vs2012)
* [Aplicativos Web protegendo recursos e criados com o Visual Studio 2010, 2008 o usando o Windows Identity Foundation](#vs2010)
* [Aplicativos/APIs Web que protegem recursos usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos com suporte](#other)

Esta orientação **não** se aplica a:

* Os aplicativos adicionados da Galeria de aplicativos do Azure AD (incluindo os personalizados) têm orientação separada em relação às chaves de assinatura. [Mais informações.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Os aplicativos locais publicados por meio do proxy de aplicativo não precisam se preocupar com as chaves de assinatura.

### <a name="nativeclient"></a>Aplicativos cliente nativos que acessam recursos
Aplicativos que estão acessando apenas recursos (ou seja, Microsoft Graph, keyvault, API do Outlook e outras APIs da Microsoft) geralmente obtêm apenas um token e os passam para o proprietário do recurso. Considerando que eles não estão protegendo nenhum recurso, eles não inspecionam o token e, portanto, não precisam garantir que estejam corretamente assinados.

Os aplicativos cliente nativos, sejam eles móveis, se enquadram nessa categoria e, portanto, não são afetados pela substituição.

### <a name="webclient"></a>Aplicativos/APIs Web que acessam recursos
Aplicativos que estão acessando apenas recursos (ou seja, Microsoft Graph, keyvault, API do Outlook e outras APIs da Microsoft) geralmente obtêm apenas um token e os passam para o proprietário do recurso. Considerando que eles não estão protegendo nenhum recurso, eles não inspecionam o token e, portanto, não precisam garantir que estejam corretamente assinados.

Os aplicativos Web e as APIs Web que estão usando o fluxo somente de aplicativo (credenciais de cliente/certificado de cliente), se enquadram nessa categoria e, portanto, não são afetados pela substituição.

### <a name="appservices"></a>Aplicativos/APIs Web que protegem recursos e são criados usando Azure App Services
A funcionalidade EasyAuth (autenticação/autorização) dos serviços de Azure App já tem a lógica necessária para tratar a substituição de chave automaticamente.

### <a name="owin"></a>Aplicativos/APIs Web que protegem recursos usando .NET OWIN OpenID Connect, WS-enalimentado ou middleware WindowsAzureActiveDirectoryBearerAuthentication
Se seu aplicativo estiver usando o .NET OWIN OpenID Connect, WS-Redirecionado ou middleware WindowsAzureActiveDirectoryBearerAuthentication, ele já terá a lógica necessária para manipular a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo está usando qualquer um deles procurando qualquer um dos trechos de código a seguir no Startup.cs ou Startup.Auth.cs do seu aplicativo

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Aplicativos/APIs Web que protegem recursos usando o .NET Core OpenID Connect ou o middleware JwtBearerAuthentication
Se seu aplicativo estiver usando o .NET Core OWIN OpenID Connect ou o middleware JwtBearerAuthentication, ele já terá a lógica necessária para manipular a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo está usando qualquer um deles procurando qualquer um dos trechos de código a seguir no Startup.cs ou Startup.Auth.cs do seu aplicativo

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="passport"></a>Aplicativos/APIs Web que protegem recursos usando o módulo Passport-Azure-AD do node. js
Se seu aplicativo estiver usando o módulo Passport-AD do node. js, ele já terá a lógica necessária para tratar a substituição de chave automaticamente.

Você pode confirmar que seu aplicativo Passport-ad pesquisando o trecho a seguir no app. js de seu aplicativo

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Aplicativos/APIs Web que protegem recursos e criados com o Visual Studio 2015 ou posterior
Se seu aplicativo foi criado usando um modelo de aplicativo Web no Visual Studio 2015 ou posterior e você selecionou **contas corporativas ou de estudante** no menu **alterar autenticação** , ele já tem a lógica necessária para tratar a substituição de chave automaticamente. Essa lógica, inserida no middleware OWIN OpenID Connect, recupera e armazena em cache as chaves do documento de descoberta do OpenID Connect e as atualiza periodicamente.

Se você adicionou a autenticação à sua solução manualmente, seu aplicativo poderá não ter a lógica de substituição de chave necessária. Você precisará escrevê-lo por conta própria ou seguir as etapas em [aplicativos/APIs Web usando qualquer outra biblioteca ou implementando manualmente qualquer um dos protocolos com suporte](#other).

### <a name="vs2013"></a>Aplicativos Web protegendo recursos e criados com Visual Studio 2013
Se seu aplicativo foi criado usando um modelo de aplicativo Web no Visual Studio 2013 e você selecionou **contas organizacionais** no menu **alterar autenticação** , ele já terá a lógica necessária para tratar a substituição de chave automaticamente. Essa lógica armazena o identificador exclusivo da sua organização e as informações de chave de assinatura em duas tabelas de banco de dados associadas ao projeto. Você pode encontrar a cadeia de conexão para o banco de dados no arquivo Web. config do projeto.

Se você adicionou a autenticação à sua solução manualmente, seu aplicativo poderá não ter a lógica de substituição de chave necessária. Terá de escrever pessoalmente ou, siga os passos no [aplicações Web / APIs utilizar quaisquer outras bibliotecas de ou implementar manualmente a qualquer um dos protocolos suportados](#other).

As etapas a seguir ajudarão você a verificar se a lógica está funcionando corretamente em seu aplicativo.

1. No Visual Studio 2013, abra a solução e, em seguida, clique na guia **Gerenciador de servidores** na janela à direita.
2. Expanda **conexões de dados**, **DefaultConnection**e, em seguida, **tabelas**. Localize a tabela **IssuingAuthorityKeys** , clique com o botão direito do mouse nela e clique em **Mostrar dados da tabela**.
3. Na tabela **IssuingAuthorityKeys** , haverá pelo menos uma linha, que corresponde ao valor de impressão digital da chave. Exclua todas as linhas na tabela.
4. Clique com o botão direito do mouse na tabela **locatários** e clique em **Mostrar dados da tabela**.
5. Na tabela **locatários** , haverá pelo menos uma linha, que corresponde a um identificador de locatário de diretório exclusivo. Exclua todas as linhas na tabela. Se você não excluir as linhas na tabela de **locatários** e na tabela **IssuingAuthorityKeys** , receberá um erro em tempo de execução.
6. Compile e execute a aplicação. Depois de fazer logon em sua conta, você pode parar o aplicativo.
7. Retorne ao **Gerenciador de servidores** e examine os valores na tabela **IssuingAuthorityKeys** e **locatários** . Você observará que eles foram repopulados automaticamente com as informações apropriadas do documento de metadados de Federação.

### <a name="vs2013"></a>APIs da Web protegendo recursos e criados com Visual Studio 2013
Se você criou um aplicativo de API Web em Visual Studio 2013 usando o modelo de API Web e, em seguida, selecionou **contas organizacionais** no menu **alterar autenticação** , você já tem a lógica necessária em seu aplicativo.

Se você configurou manualmente a autenticação, siga as instruções abaixo para saber como configurar sua API Web para atualizar automaticamente suas informações de chave.

O trecho de código a seguir demonstra como obter as chaves mais recentes do documento de metadados de Federação e, em seguida, usar o [manipulador de tokens JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar o token. O trecho de código pressupõe que você usará seu próprio mecanismo de cache para manter a chave para validar tokens futuros do Azure AD, seja em um banco de dados, arquivo de configuração ou em outro lugar.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Aplicativos Web protegendo recursos e criados com o Visual Studio 2012
Se seu aplicativo foi criado no Visual Studio 2012, você provavelmente usou a ferramenta de identidade e acesso para configurar seu aplicativo. Também é provável que você esteja usando o [registro de nome do emissor de validação (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). O VINR é responsável por manter informações sobre provedores de identidade confiáveis (Azure AD) e as chaves usadas para validar tokens emitidos por eles. O VINR também facilita a atualização automática das informações de chave armazenadas em um arquivo Web. config baixando o documento de metadados de Federação mais recente associado ao seu diretório, verificando se a configuração está desatualizada com o documento mais recente e Atualizando o aplicativo para usar a nova chave, conforme necessário.

Se você criou seu aplicativo usando qualquer um dos exemplos de código ou documentação explicativa fornecida pela Microsoft, a lógica de substituição de chave já está incluída no seu projeto. Você observará que o código a seguir já existe em seu projeto. Se seu aplicativo ainda não tiver essa lógica, siga as etapas abaixo para adicioná-la e verificar se ela está funcionando corretamente.

1. Em **Gerenciador de soluções**, adicione uma referência ao assembly **System. IdentityModel** para o projeto apropriado.
2. Abra o arquivo **global.asax.cs** e adicione as seguintes diretivas using:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Adicione o seguinte método ao arquivo **global.asax.cs** :
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Invoque o método **RefreshValidationSettings ()** no método **Application_Start ()** em **global.asax.cs** , conforme mostrado:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Depois de seguir essas etapas, o Web. config do seu aplicativo será atualizado com as informações mais recentes do documento de metadados de Federação, incluindo as chaves mais recentes. Essa atualização ocorrerá sempre que o pool de aplicativos for reciclado no IIS; Por padrão, o IIS é definido para reciclar aplicativos a cada 29 horas.

Siga as etapas abaixo para verificar se a lógica de substituição de chave está funcionando.

1. Depois de verificar se seu aplicativo está usando o código acima, abra o arquivo **Web. config** e navegue até o **\<IssuerNameRegistry >** bloco, procurando especificamente as seguintes linhas:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Na configuração **\<adicionar Thumbprint = "" >** , altere o valor da impressão digital substituindo qualquer caractere com um diferente. Guarde o ficheiro **Web.config**.
3. Compile o aplicativo e, em seguida, execute-o. Se você puder concluir o processo de entrada, seu aplicativo estará atualizando a chave com êxito baixando as informações necessárias do documento de metadados de Federação do seu diretório. Se você estiver tendo problemas para entrar, verifique se as alterações em seu aplicativo estão corretas lendo o artigo [adicionando logon ao seu aplicativo Web usando o Azure ad](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) ou baixando e inspecionando o exemplo de código a seguir: [aplicativo de nuvem multilocatário para Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Aplicativos Web protegendo recursos e criados com o Visual Studio 2008 ou 2010 e o Windows Identity Foundation (WIF) v 1.0 para .NET 3,5
Se você criou um aplicativo no WIF v 1.0, não há um mecanismo fornecido para atualizar automaticamente a configuração do aplicativo para usar uma nova chave.

* *Maneira mais fácil* Use as ferramentas FedUtil incluídas no SDK do WIF, que pode recuperar o documento de metadados mais recente e atualizar sua configuração.
* Atualize seu aplicativo para o .NET 4,5, que inclui a versão mais recente do WIF localizada no namespace System. Em seguida, você pode usar o [VINR (registro de nome do emissor de validação)](https://msdn.microsoft.com/library/dn205067.aspx) para executar atualizações automáticas da configuração do aplicativo.
* Execute uma substituição manual de acordo com as instruções no final deste documento de diretrizes.

Instruções para usar o FedUtil para atualizar sua configuração:

1. Verifique se você tem o SDK do WIF v 1.0 instalado em seu computador de desenvolvimento para o Visual Studio 2008 ou 2010. Você pode [baixá-lo aqui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se ainda não o tiver instalado.
2. No Visual Studio, abra a solução e clique com o botão direito do mouse no projeto aplicável e selecione **atualizar metadados de Federação**. Se essa opção não estiver disponível, o FedUtil e/ou o SDK do WIF v 1.0 não foi instalado.
3. No prompt, selecione **Atualizar** para começar a atualizar seus metadados de Federação. Se você tiver acesso ao ambiente de servidor onde o aplicativo está hospedado, você pode opcionalmente usar o [Agendador de atualização de metadados automático](https://msdn.microsoft.com/library/ee517272.aspx)do FedUtil.
4. Clique em **concluir** para concluir o processo de atualização.

### <a name="other"></a>Aplicativos/APIs Web que protegem recursos usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos com suporte
Se você estiver usando alguma outra biblioteca ou tiver implementado manualmente qualquer um dos protocolos com suporte, você precisará examinar a biblioteca ou sua implementação para garantir que a chave esteja sendo recuperada do documento de descoberta do OpenID Connect ou dos metadados de Federação Document. Uma maneira de verificar isso é fazer uma pesquisa em seu código ou no código da biblioteca para qualquer chamada para o documento de descoberta de OpenID ou o documento de metadados de Federação.

Se a chave estiver sendo armazenada em algum lugar ou codificada em seu aplicativo, você poderá recuperar manualmente a chave e atualizá-la de forma adequada executando uma substituição manual de acordo com as instruções no final deste documento de diretrizes. **É altamente recomendável que você Aprimore seu aplicativo para dar suporte à substituição automática** usando qualquer uma das abordagens deste artigo para evitar futuras interrupções e sobrecarga se o Azure ad aumentar sua cadência de substituição ou tiver uma substituição fora de banda de emergência.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Como testar seu aplicativo para determinar se ele será afetado
Você pode validar se seu aplicativo dá suporte à substituição automática de chave baixando os scripts e seguindo as instruções neste [repositório github.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Como executar uma substituição manual se seu aplicativo não oferecer suporte à substituição automática
Se seu aplicativo não **oferecer suporte** à substituição automática, você precisará estabelecer um processo que monitora periodicamente as chaves de assinatura do Azure AD e executa uma substituição manual de acordo. [Este repositório GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contém scripts e instruções sobre como fazer isso.

