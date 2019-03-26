---
title: Iniciar o Rollover da chave de sessão do Azure AD
description: Este artigo discute as práticas recomendadas de rollover de chave assinatura para o Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82e9941a6c468a3b0ed9d1f22a2970cfa6584617
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439350"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Iniciar o rollover da chave no Azure Active Directory
Este artigo aborda o que precisa saber sobre as chaves públicas que são utilizadas no Azure Active Directory (Azure AD) para assinar os tokens de segurança. É importante observar que esses rollover de chaves periodicamente e, em caso de emergências, foi implementado imediatamente. Todas as aplicações que utilizam o Azure AD devem ser capazes de manipular o processo de rollover de chave ou estabeleça um processo periódico rollover manual de por meio de programação. Continue a ler para compreender como funcionam as chaves, como avaliar o impacto de rollover para seu aplicativo e como atualizar a sua aplicação ou estabelecer um processo de rollover manual periódica para lidar com o rollover da chave, se necessário.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Descrição geral das chaves de autenticação no Azure AD
Azure AD utiliza a criptografia de chave pública criada com os padrões do setor para estabelecer confiança entre ele e os aplicativos que a utilizam. Em termos práticos, isso funciona da seguinte forma: Azure AD utiliza uma chave de assinatura que consiste num par de chaves público e privado. Quando um utilizador inicia sessão a uma aplicação que utiliza o Azure AD para autenticação, o AD do Azure cria um token de segurança que contém informações sobre o utilizador. Este token está assinado pelo Azure AD utilizando a respetiva chave privada, antes de ser enviada para a aplicação. Para verificar que o token é válido e originados do Azure AD, a aplicação tem de validar a assinatura do token com a chave pública exposta pelo Azure AD que está contido do inquilino [documento de deteção de ligação OpenID](https://openid.net/specs/openid-connect-discovery-1_0.html) ou SAML / WS-Fed [documento de metadados de Federação](azure-ad-federation-metadata.md).

Por motivos de segurança, rolls chave periodicamente e, em caso de emergência, de assinatura do Azure AD foi implementado imediatamente. Qualquer aplicativo que se integra com o Azure AD deve estar preparado para processar um evento de rollover de chave, não importa a frequência com que possam ocorrer. Se isso não acontecer e a aplicação tenta utilizar uma chave expirada para verificar a assinatura num token, o pedido de início de sessão falhará.

Sempre há mais de uma chave válida no documento de deteção do OpenID Connect e o documento de metadados de Federação. Seu aplicativo deve estar preparado para utilizar qualquer uma das chaves especificadas no documento, pois uma chave poderá ser revertida em breve, outro pode ser sua substituição e assim por diante.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>A avaliar se o seu aplicativo será afetado e o que fazer sobre ela
Como o seu aplicativo manipula o rollover da chave depende de variáveis, como o tipo de aplicação ou o protocolo de identidade e a biblioteca foi utilizado. As secções abaixo avaliar se os tipos mais comuns de aplicativos são afetados pelo rollover da chave e fornecem orientações sobre como atualizar a aplicação para suportar o rollover automático ou atualizar manualmente a chave.

* [Aceder a recursos de aplicativos de cliente nativo](#nativeclient)
* [Aplicações Web / APIs a aceder a recursos](#webclient)
* [Aplicações Web / APIs proteger os recursos e criados através dos serviços de aplicações do Azure](#appservices)
* [Aplicações Web / APIs proteger os recursos através do .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware](#owin)
* [Aplicações Web / APIs proteger recursos com .NET Core OpenID Connect ou JwtBearerAuthentication middleware](#owincore)
* [Aplicações Web / APIs proteger recursos com o módulo de passport-azure-ad de node. js](#passport)
* [Aplicações Web / APIs proteger os recursos e criados com o Visual Studio 2015 ou Visual Studio 2017](#vs2015)
* [Aplicações Web de proteger os recursos e criado com o Visual Studio 2013](#vs2013)
* APIs da Web, proteger os recursos e criado com o Visual Studio 2013
* [Aplicações Web de proteger os recursos e criado com o Visual Studio 2012](#vs2012)
* [Aplicações Web de proteger os recursos e criado com o Visual Studio 2010, o 2008 usando o Windows Identity Foundation](#vs2010)
* [Aplicações Web / APIs proteger os recursos através de qualquer outra biblioteca ou manualmente a implementar qualquer um dos protocolos suportados](#other)

Essas orientações se apresentam **não** aplicável para:

* Aplicações adicionadas a partir da Galeria de aplicações do Azure AD (incluindo personalizado) ter orientações separada com respeito a chaves de assinatura. [Obter mais informações.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* No local aplicações publicadas através do proxy de aplicações não precisam se preocupar sobre chaves de assinatura.

### <a name="nativeclient"></a>Aceder a recursos de aplicativos de cliente nativo
Aplicativos que apenas acedem aos recursos (ou seja Microsoft Graph, Cofre de chaves, API de Outlook e outras APIs da Microsoft) geralmente apenas obter um token e passá-lo ao longo para o proprietário do recurso. Uma vez que eles não estiver a proteger todos os recursos, eles não Inspecione o token e, portanto, não é necessário garantir que está corretamente assinado.

Aplicações de cliente nativo, se o ambiente de trabalho ou móveis, caem nessa categoria e, portanto, não são afetadas pelo rollover.

### <a name="webclient"></a>Aplicações Web / APIs a aceder a recursos
Aplicativos que apenas acedem aos recursos (ou seja Microsoft Graph, Cofre de chaves, API de Outlook e outras APIs da Microsoft) geralmente apenas obter um token e passá-lo ao longo para o proprietário do recurso. Uma vez que eles não estiver a proteger todos os recursos, eles não Inspecione o token e, portanto, não é necessário garantir que está corretamente assinado.

Aplicativos Web e APIs que estão a utilizar o fluxo de só de aplicação web (credenciais de cliente / certificado de cliente), caem nessa categoria e, portanto, não são afetados pelo rollover.

### <a name="appservices"></a>Aplicações Web / APIs proteger os recursos e criados através dos serviços de aplicações do Azure
A autenticação dos serviços aplicacionais do Azure / funcionalidade de autorização (EasyAuth) já tem a lógica necessária para processar automaticamente o rollover da chave.

### <a name="owin"></a>Aplicações Web / APIs proteger os recursos através do .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware
Se a aplicação estiver a utilizar o .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware, ela já contém a lógica necessária para processar automaticamente o rollover da chave.

Pode confirmar que o seu aplicativo está usando qualquer uma dessas, procurando por qualquer um dos seguintes fragmentos no Startup.cs ou Startup.Auth.cs da sua aplicação

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

### <a name="owincore"></a>Aplicações Web / APIs proteger recursos com .NET Core OpenID Connect ou JwtBearerAuthentication middleware
Se a aplicação estiver a utilizar o .NET Core OWIN OpenID Connect ou JwtBearerAuthentication middleware, ela já contém a lógica necessária para processar automaticamente o rollover da chave.

Pode confirmar que o seu aplicativo está usando qualquer uma dessas, procurando por qualquer um dos seguintes fragmentos no Startup.cs ou Startup.Auth.cs da sua aplicação

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

### <a name="passport"></a>Aplicações Web / APIs proteger recursos com o módulo de passport-azure-ad de node. js
Se a aplicação estiver a utilizar o módulo de passport-ad node. js, ela já contém a lógica necessária para processar automaticamente o rollover da chave.

Pode confirmar que o aplicativo passport-ad ao procurar o trecho a seguir no App. js do seu aplicativo

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Aplicações Web / APIs proteger os recursos e criados com o Visual Studio 2015 ou Visual Studio 2017
Se seu aplicativo foi criado com um modelo de aplicação web no Visual Studio 2015 ou Visual Studio 2017 e selecionou **trabalho e contas escolares** partir a **alterar autenticação** menu, que já tem, a lógica necessária para processar automaticamente o rollover da chave. Essa lógica, incorporada no middleware da OWIN OpenID Connect, recupera e armazena em cache as chaves do documento de deteção do OpenID Connect e atualiza periodicamente.

Se adicionar autenticação à sua solução manualmente, seu aplicativo poderá não ter a lógica de rollover de chave necessárias. Terá de escrevê-lo por conta própria ou siga os passos em [aplicações Web / APIs utilizando qualquer outra biblioteca ou implementar manualmente a qualquer um dos protocolos suportados](#other).

### <a name="vs2013"></a>Aplicações Web de proteger os recursos e criado com o Visual Studio 2013
Se seu aplicativo foi criado com um modelo de aplicação web no Visual Studio 2013 e selecionou **contas institucionais** partir a **alterar autenticação** menu, ela já contém a lógica necessária para lidar automaticamente com o rollover da chave. Essa lógica armazena o identificador exclusivo da sua organização e as informações da chave de assinatura em duas tabelas de base de dados associadas ao projeto. Pode encontrar a cadeia de ligação da base de dados no arquivo de Web. config do projeto.

Se adicionar autenticação à sua solução manualmente, seu aplicativo poderá não ter a lógica de rollover de chave necessárias. Terá de escrever pessoalmente ou, siga os passos no [aplicações Web / APIs utilizar quaisquer outras bibliotecas de ou implementar manualmente a qualquer um dos protocolos suportados](#other).

Os seguintes passos irão ajudá-lo a verificar que a lógica está a funcionar corretamente na sua aplicação.

1. No Visual Studio 2013, abra a solução e, em seguida, clique nas **Explorador de servidores** guia na janela direita.
2. Expanda **conexões de dados**, **DefaultConnection**e, em seguida **tabelas**. Localize a **IssuingAuthorityKeys** da tabela, faça duplo clique nele e, em seguida, clique em **Mostrar dados da tabela**.
3. Na **IssuingAuthorityKeys** de tabela, existirá pelo menos uma linha, que corresponde ao valor do thumbprint para a chave. Elimine quaisquer linhas na tabela.
4. Com o botão direito a **inquilinos** de tabela e, em seguida, clique em **Mostrar dados da tabela**.
5. Na **inquilinos** de tabela, existirá pelo menos uma linha, que corresponde a um identificador de inquilino do diretório exclusivo. Elimine quaisquer linhas na tabela. Se não eliminar as linhas em ambos os **inquilinos** tabela e **IssuingAuthorityKeys** tabela, obterá um erro em tempo de execução.
6. Crie e execute a aplicação. Depois de ter de iniciar sessão na sua conta, pode parar a aplicação.
7. Retorno para o **Explorador de servidores** e ver os valores na **IssuingAuthorityKeys** e **inquilinos** tabela. Perceberá que tenham sido repovoados automaticamente, com as informações apropriadas do documento de metadados de Federação.

### <a name="vs2013"></a>APIs da Web, proteger os recursos e criado com o Visual Studio 2013
Se criou uma aplicação API da web no Visual Studio 2013 usando o modelo de Web API e, em seguida, selecionado **contas institucionais** partir do **alterar autenticação** menu, já tem as informações necessárias lógica em seu aplicativo.

Se tiver configurado manualmente a autenticação, siga as instruções abaixo para saber como configurar a sua API Web para atualizar automaticamente as informações de chave.

O fragmento de código seguinte demonstra como obter as chaves mais recentes do documento de metadados de Federação e, em seguida, utilize o [identificador de Token JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar o token. O trecho de código parte do princípio de que utiliza o seu próprio mecanismo de colocação em cache para fazer a persistência da chave para validar tokens futuros do Azure AD, independentemente de serem num banco de dados, o ficheiro de configuração ou noutro local.

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

### <a name="vs2012"></a>Aplicações Web de proteger os recursos e criado com o Visual Studio 2012
Se seu aplicativo foi criado no Visual Studio 2012, provavelmente utilizou a ferramenta de acesso e identidade para configurar a sua aplicação. Também é provável que está a utilizar o [validação de registo de nome de emissor (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). O VINR é responsável pela manutenção informações sobre fornecedores de identidade fidedignos (Azure AD) e as chaves utilizadas para validar tokens emitidos por eles. O VINR também torna mais fácil atualizar automaticamente as informações da chave armazenadas num arquivo Web. config ao transferir o documento de metadados de Federação mais recentes associado com o seu diretório, verificar se a configuração está desatualizada com o documento mais recentes, e a atualizar a aplicação para utilizar a nova chave, se necessário.

Se tiver criado a sua aplicação com qualquer um dos exemplos de código ou documentação passo a passo fornecidas pela Microsoft, a lógica de rollover da chave já está incluída no seu projeto. Observará que já existe o código abaixo no seu projeto. Se seu aplicativo não tem essa lógica, siga os passos abaixo para adicioná-lo e certifique-se de que está a funcionar corretamente.

1. Na **Explorador de soluções**, adicione uma referência para o **System. IdentityModel** assembly para o projeto apropriado.
2. Abra o **Global.asax.cs** de ficheiros e adicione o seguinte usando diretivas:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Adicione o seguinte método para o **Global.asax.cs** ficheiro:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Invocar o **RefreshValidationSettings()** método na **application_start ()** método na **Global.asax.cs** conforme mostrado:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Depois de seguir estes passos, Web. config de seu aplicativo será atualizado com as informações mais recentes do documento de metadados de Federação, incluindo as chaves mais recente. Esta atualização irá ocorrer sempre que o conjunto aplicacional recicla no IIS; Por predefinição o IIS está definida para reciclar aplicativos a cada 29 horas.

Siga os passos abaixo para verificar se a lógica de rollover da chave está funcionando.

1. Após ter verificado que o seu aplicativo está usando o código acima, abra a **Web. config** de ficheiros e navegue para o  **\<issuerNameRegistry >** bloco, especificamente as recolhas o Algumas linhas a seguir:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Na  **\<adicione thumbprint = "" >** definir, altere o valor do thumbprint substituindo qualquer caractere com um diferente. Guardar a **Web. config** ficheiro.
3. Criar a aplicação e, em seguida, executá-lo. Se pode concluir o processo de início de sessão, com êxito o seu aplicativo está a atualizar a chave ao transferir as informações necessárias do documento de metadados de Federação do diretório. Se estiver a ter problemas ao iniciar sessão, certifique-se das alterações na sua aplicação estão corretas, lendo o [adicionar início de sessão na sua utilização de aplicação Web do Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) artigo, ou baixar e inspecionar o código de exemplo seguinte: [Aplicação de Cloud multi-inquilino para o Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Aplicações Web de proteger os recursos e criado com o Visual Studio 2008 ou 2010 e o Windows Identity Foundation (WIF) v1.0 do .NET 3.5
Se criou uma aplicação no WIF v1.0, não existe nenhum mecanismo fornecido para atualizar automaticamente a configuração da sua aplicação para utilizar uma nova chave.

* *A maneira mais fácil* utilizar as ferramentas de FedUtil incluída no SDK do WIF, que pode obter o documento de metadados mais recentes e atualizar a configuração.
* Atualize a sua aplicação para o .NET 4.5, que inclui a versão mais recente do WIF localizado no System namespace. Em seguida, pode utilizar o [validação de registo de nome de emissor (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) para efetuar as atualizações automáticas da configuração da aplicação.
* Efetue um rollover manual de acordo com as instruções no final deste documento de orientação.

Instruções para utilizar o FedUtil para atualizar a configuração:

1. Certifique-se de que tem a v1.0 do WIF SDK instalado no computador de desenvolvimento para o Visual Studio 2008 ou 2010. Pode [baixá-lo a partir daqui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se ainda não instalou-lo.
2. No Visual Studio, abra a solução e, em seguida, clique com o botão direito no projeto aplicável e selecione **atualizar os metadados de Federação**. Se esta opção não estiver disponível, FedUtil e/ou a v1.0 do WIF SDK não foi instalado.
3. A partir da linha de comandos, selecione **atualização** para começar a atualizar os metadados de Federação. Se tiver acesso ao ambiente de servidor em que o aplicativo é hospedado, opcionalmente, pode utilizar do FedUtil [agendador de atualização automática de metadados](https://msdn.microsoft.com/library/ee517272.aspx).
4. Clique em **concluir** para concluir o processo de atualização.

### <a name="other"></a>Aplicações Web / APIs proteger os recursos através de qualquer outra biblioteca ou manualmente a implementar qualquer um dos protocolos suportados
Se estiver a utilizar alguma outra biblioteca ou implementado manualmente qualquer um dos protocolos suportados, terá de rever a biblioteca ou a sua implementação para garantir que a chave é que está a ser obtida a partir do documento de deteção do OpenID Connect ou os metadados de Federação documento. Uma forma de verificar isso é fazer uma pesquisa no seu código ou no código da biblioteca para todas as chamadas fora para o documento de deteção do OpenID ou o documento de metadados de Federação.

Se a chave estão a ser armazenados em algum lugar ou codificado na sua aplicação, pode manualmente obter a chave e atualize-o em conformidade por efetuar um rollover manual de acordo com as instruções no final deste documento de orientação. **É altamente favorável que melhore a sua aplicação para suportar o rollover automático** usando qualquer um do contorno de abordagens neste artigo para evitar interrupções futuros e a sobrecarga se do Azure AD aumenta sua cadência ou se tiver de emergência rollover de fora de banda.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Como testar a sua aplicação para determinar se ele será afetado
Pode validar se a aplicação suporta o rollover de chave automático ao transferir os scripts e seguir as instruções em [deste repositório do GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Como efetuar um rollover manual, se seu aplicativo não suporta o rollover automático
Se seu aplicativo faça **não** suporte rollover automático, terá de estabelecer um processo que periodicamente os monitores do Azure AD da assinatura de chaves e realiza um rollover manual em conformidade. [Este repositório de GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contém scripts e instruções sobre como fazer isso.

