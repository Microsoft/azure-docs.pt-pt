---
title: Assinatura de capotamento de chave em Azure AD
description: Este artigo discute a assinatura de chaves de rollover boas práticas para o Azure Ative Directory
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
ms.openlocfilehash: e0a38eb03df3d1da64172842fb6eca3cd762f9cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537241"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Assinatura de capotamento de chave no Diretório Ativo do Azure
Este artigo discute o que precisa de saber sobre as chaves públicas que são usadas no Azure Ative Directory (Azure AD) para assinar fichas de segurança. É importante notar que estas chaves rolam periodicamente e, em caso de emergência, podem ser imediatamente revestidas. Todas as aplicações que utilizem a AD Azure devem ser capazes de lidar programáticamente com o processo de capotamento da chave ou estabelecer um processo de capotamento manual periódico. Continuar a ler Para perceber como funcionam as chaves, como avaliar o impacto do capotamento na sua aplicação e como atualizar a sua aplicação ou estabelecer um processo de capotamento manual periódico para lidar com o capotamento da chave, se necessário.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Visão geral da assinatura de chaves em Azure AD
A Azure AD utiliza criptografia de chave pública baseada nos padrões da indústria para estabelecer confiança entre si e as aplicações que a utilizam. Em termos práticos, isto funciona da seguinte forma: a Azure AD usa uma chave de assinatura que consiste num par de chaves público sacana e privado. Quando um utilizador insere uma aplicação que utiliza a AD Azure para autenticação, a Azure AD cria um sinal de segurança que contém informações sobre o utilizador. Esta ficha é assinada pela Azure AD utilizando a sua chave privada antes de ser devolvida ao pedido. Para verificar se o símbolo é válido e originado a partir de Azure AD, a aplicação deve validar a assinatura do token utilizando a chave pública exposta pela Azure AD que está contida no documento de [descoberta openID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) do inquilino ou no documento de [metadados](../azuread-dev/azure-ad-federation-metadata.md)da federação SAML/WS-Fed .

Para efeitos de segurança, a chave de assinatura da Azure AD rola periodicamente e, em caso de emergência, pode ser imediatamente rerolada. Qualquer aplicação que se integre com a AD Azure deve estar preparada para lidar com um evento de capotamento chave, independentemente da frequência com que possa ocorrer. Se não o fizer, e a sua aplicação tentar utilizar uma chave caducada para verificar a assinatura num símbolo, o pedido de inscrição falhará.

Há sempre mais do que uma chave válida disponível no documento de descoberta OpenID Connect e no documento de metadados da federação. A sua aplicação deve estar preparada para utilizar qualquer uma das chaves especificadas no documento, uma vez que uma chave pode ser enrolada em breve, outra pode ser a sua substituição, e assim por diante.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Como avaliar se a sua aplicação será afetada e o que fazer sobre isso
A forma como a sua aplicação lida com o capotamento da chave depende de variáveis como o tipo de aplicação ou qual o protocolo de identidade e biblioteca utilizados. As secções abaixo avaliam se os tipos de aplicações mais comuns são impactados pelo capotamento da chave e fornecem orientações sobre como atualizar a aplicação para suportar o capotamento automático ou atualizar manualmente a chave.

* [Aplicações de clientes nativos que acedem a recursos](#nativeclient)
* [Aplicações web / APIs de acesso a recursos](#webclient)
* [Aplicações web / APIs protegendo recursos e construídos usando serviços de aplicações Azure](#appservices)
* [Aplicações web / APIs protegendo recursos usando .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware](#owin)
* [Aplicações web / APIs protegendo recursos usando .NET Core OpenID Connect ou JwtBearerAuthentication middleware](#owincore)
* [Aplicações web / APIs protegendo recursos usando módulo de anúncio de passaporte-azure nonó](#passport)
* [Aplicações web / APIs protegendo recursos e criados com Visual Studio 2015 ou posterior](#vs2015)
* [Aplicações web que protegem recursos e criadas com o Visual Studio 2013](#vs2013)
* ApIs web protegendo recursos e criado com Visual Studio 2013
* [Aplicações web que protegem recursos e criadas com o Visual Studio 2012](#vs2012)
* [Aplicações web que protegem recursos e criadas com o Visual Studio 2010, 2008 o usando a Windows Identity Foundation](#vs2010)
* [Aplicações web / APIs protegendo recursos usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos suportados](#other)

Esta orientação **não** é aplicável para:

* As aplicações adicionadas da Galeria de Aplicações AD Azure (incluindo personalizada) têm orientações separadas no que diz respeito à assinatura de chaves. [Mais informações.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* As aplicações no local publicadas através de procuração de aplicações não têm de se preocupar com a assinatura de chaves.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Aplicações de clientes nativos que acedem a recursos
Aplicações que estão apenas a aceder a recursos (isto é, Microsoft Graph, KeyVault, Outlook API e outras APIs da Microsoft) geralmente apenas obtêm um token e passá-lo ao proprietário do recurso. Dado que não estão a proteger quaisquer recursos, não inspecionam o símbolo e, por conseguinte, não precisam de assegurar a sua assinatura correta.

As aplicações de clientes nativos, sejam elas de desktop ou mobile, enquadram-se nesta categoria e, portanto, não são afetadas pela capotamento.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Aplicações web / APIs de acesso a recursos
Aplicações que estão apenas a aceder a recursos (isto é, Microsoft Graph, KeyVault, Outlook API e outras APIs da Microsoft) geralmente apenas obtêm um token e passá-lo ao proprietário do recurso. Dado que não estão a proteger quaisquer recursos, não inspecionam o símbolo e, por conseguinte, não precisam de assegurar a sua assinatura correta.

As aplicações web e as APIs web que estão a utilizar o fluxo apenas de aplicações (credenciais de cliente/certificado de cliente), caem nesta categoria e não são, portanto, impactadas pela capotamento.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Aplicações web / APIs protegendo recursos e construídos usando serviços de aplicações Azure
A funcionalidade de autenticação/autorização dos Serviços de Aplicação Azure (EasyAuth) já tem a lógica necessária para lidar automaticamente com o capotamento da chave.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Aplicações web / APIs protegendo recursos usando .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware
Se a sua aplicação estiver a utilizar o .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication, já tem a lógica necessária para lidar automaticamente com o rollover da chave.

Pode confirmar que a sua aplicação está a utilizar qualquer uma delas, procurando qualquer um dos seguintes excertos no Startup.cs ou Startup.Auth.cs da sua aplicação.

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

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Aplicações web / APIs protegendo recursos usando .NET Core OpenID Connect ou JwtBearerAuthentication middleware
Se a sua aplicação estiver a utilizar o .NET Core OWIN OpenID Connect ou o JwtBearerAuthentication middleware, já tem a lógica necessária para lidar automaticamente com o rollover da chave.

Pode confirmar que a sua aplicação está a utilizar qualquer uma delas, procurando qualquer um dos seguintes excertos no Startup.cs ou Startup.Auth.cs da sua aplicação.

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

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Aplicações web / APIs protegendo recursos usando módulo de anúncio de passaporte-azure nonó
Se a sua aplicação estiver a utilizar o módulo de anúncio de passaporte Node.js, já tem a lógica necessária para lidar automaticamente com o capotamento da chave.

Pode confirmar que o seu anúncio de passaporte de aplicação procurando o seguinte corte na app.js da sua aplicação

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Aplicações web / APIs protegendo recursos e criados com Visual Studio 2015 ou posterior
Se a sua aplicação foi construída utilizando um modelo de aplicação web no Visual Studio 2015 ou mais tarde e selecionou Contas de **Trabalho ou Escola** do menu **'Change Authentication',** já tem a lógica necessária para lidar automaticamente com o capotamento da chave. Esta lógica, incorporada no middleware OWIN OpenID Connect, recupera e caches as chaves do documento de descoberta OpenID Connect e as atualiza periodicamente.

Se adicionou a autenticação à sua solução manualmente, a sua aplicação pode não ter a lógica de capotamento necessária. Você mesmo terá de escrevê-lo, ou seguir os passos em [aplicações Web /APIs usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos suportados](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Aplicações web que protegem recursos e criadas com o Visual Studio 2013
Se a sua aplicação foi construída utilizando um modelo de aplicação web no Visual Studio 2013 e selecionou **Contas Organizacionais** do menu **'Change Authentication',** já tem a lógica necessária para lidar automaticamente com o capotamento da chave. Esta lógica armazena o identificador único da sua organização e a informação chave de assinatura em duas tabelas de bases de dados associadas ao projeto. Pode encontrar a cadeia de ligação para a base de dados no ficheiro Web.config do projeto.

Se adicionou a autenticação à sua solução manualmente, a sua aplicação pode não ter a lógica de capotamento necessária. Você mesmo terá de escrevê-lo, ou seguir os passos em [aplicações Web /APIs usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos suportados.](#other). .

Os seguintes passos irão ajudá-lo a verificar se a lógica está a funcionar corretamente na sua aplicação.

1. No Visual Studio 2013, abra a solução e clique no separador **Server Explorer** na janela certa.
2. Expandir **as ligações**de dados, **a ligação predefinida**e, em seguida, **as tabelas.** Localize a tabela **ItsuingAuthorityKeys,** clique à direita e, em seguida, clique em Dados de **Tabela de Mostrar**.
3. Na tabela **ItsuingAuthorityKeys,** haverá pelo menos uma linha, o que corresponde ao valor de impressão digital da chave. Apague as linhas da mesa.
4. Clique na tabela **dos Inquilinos** e, em seguida, clique em **Mostrar Dados**da Tabela .
5. Na tabela dos **Inquilinos,** haverá pelo menos uma fila, o que corresponde a um identificador de inquilino de diretório único. Apague as linhas da mesa. Se não apagar as linhas na tabela **Tenants** e na tabela **ItsuingAuthorityKeys,** terá um erro no prazo de execução.
6. Compile e execute a aplicação. Depois de ter iniciado sessão na sua conta, pode parar o pedido.
7. Volte ao **Server Explorer** e veja os valores na tabela **ItsuingAuthorityKeys** e **Tenants.** Vai notar que foram automaticamente repovoados com as informações apropriadas do documento de metadados da federação.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>ApIs web protegendo recursos e criado com Visual Studio 2013
Se criou uma aplicação Web API no Visual Studio 2013 utilizando o modelo Web API e, em seguida, selecionou **Contas Organizacionais** do menu **'Change Authentication',** já tem a lógica necessária na sua aplicação.

Se configurar manualmente a autenticação, siga as instruções abaixo para saber como configurar a Sua API web para atualizar automaticamente as suas informações-chave.

O seguinte código de snippet demonstra como obter as chaves mais recentes do documento de metadados da federação, e depois usar o [JWT Token Handler](https://msdn.microsoft.com/library/dn205065.aspx) para validar o símbolo. O código snippet pressupõe que você usará o seu próprio mecanismo de cache para persistir a chave para validar futuras fichas da Azure AD, seja numa base de dados, ficheiro de configuração ou em qualquer outro lugar.

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

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Aplicações web que protegem recursos e criadas com o Visual Studio 2012
Se a sua aplicação foi construída no Visual Studio 2012, provavelmente usou a Ferramenta de Identidade e Acesso para configurar a sua aplicação. Também é provável que esteja a utilizar o Registo de Nomes do [Emitente de Validação (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). O VINR é responsável pela manutenção de informações sobre fornecedores de identidade fidedignos (Azure AD) e as chaves utilizadas para validar fichas emitidas por eles. O VINR também facilita a atualização automática das informações-chave armazenadas num ficheiro Web.config, descarregando o mais recente documento de metadados da federação associado ao seu diretório, verificando se a configuração está desatualizada com o documento mais recente e atualizando a aplicação para utilizar a nova chave conforme necessário.

Se criou a sua aplicação utilizando alguma das amostras de código ou documentação de passagem fornecida pela Microsoft, a lógica de capotamento chave já está incluída no seu projeto. Você vai notar que o código abaixo já existe no seu projeto. Se a sua aplicação ainda não tiver esta lógica, siga os passos abaixo para adicioná-la e verificar se está a funcionar corretamente.

1. No **Solution Explorer,** adicione uma referência ao conjunto **System.IdentityModel** para o projeto apropriado.
2. Abra o ficheiro **Global.asax.cs** e adicione as seguintes diretivas:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Adicione o seguinte método ao ficheiro **Global.asax.cs:**
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Invocar o método **RefreshValidaÇão ()** no método **Application_Start()** em **Global.asax.cs,** como mostrado:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Depois de ter seguido estes passos, o Web.config da sua aplicação será atualizado com as últimas informações do documento de metadados da federação, incluindo as chaves mais recentes. Esta atualização ocorrerá sempre que o seu pool de aplicações recicla no IIS; por padrão, o IIS está definido para reciclar aplicações a cada 29 horas.

Siga os passos abaixo para verificar se a lógica de capotamento da chave está a funcionar.

1. Depois de ter verificado que a sua aplicação está a utilizar o código acima, abra o ficheiro **Web.config** e navegue para o ** \<bloco>emitenteNameRegistry,** especificamente procurando as seguintes linhas:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Na ** \<impressão digital do polegar=">** definição, altere o valor da impressão digital substituindo qualquer personagem por outro. Guarde o ficheiro **Web.config**.
3. Construa a aplicação e, em seguida, executá-la. Se conseguir completar o processo de inscrição, a sua aplicação está a atualizar com sucesso a chave, baixando as informações necessárias a partir do documento de metadados da federação do seu diretório. Se tiver problemas de sessão, certifique-se de que as alterações na sua aplicação estão corretas, lendo o [Sign-On adicionando à sua aplicação web utilizando](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) o artigo da AD Azure, ou descarregando e inspecionando a seguinte amostra de código: [Aplicação multi-inquilino cloud para diretório ativo azure](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Aplicações web que protegem recursos e criadas com o Visual Studio 2008 ou 2010 e a Windows Identity Foundation (WIF) v1.0 para .NET 3.5
Se construiu uma aplicação no WIF v1.0, não existe um mecanismo fornecido para atualizar automaticamente a configuração da sua aplicação para utilizar uma nova chave.

* *Maneira mais fácil* Utilize a ferramenta FedUtil incluída no WIF SDK, que pode recuperar o mais recente documento de metadados e atualizar a sua configuração.
* Atualize a sua aplicação para .NET 4.5, que inclui a versão mais recente do WIF localizada no espaço de nome do Sistema. Em seguida, pode utilizar o Registo de Nomes do [Emitente validador (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) para efetuar atualizações automáticas da configuração da aplicação.
* Efetuar uma capotação manual de acordo com as instruções no final deste documento de orientação.

Instruções para utilizar o FedUtil para atualizar a sua configuração:

1. Verifique se tem o WIF v1.0 SDK instalado na sua máquina de desenvolvimento para o Visual Studio 2008 ou 2010. Pode [descarregá-lo a partir daqui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se ainda não o tiver instalado.
2. No Estúdio Visual, abra a solução e, em seguida, clique no projeto aplicável e selecione **Metadados da Federação de Atualização**. Se esta opção não estiver disponível, a FedUtil e/ou o WIF v1.0 SDK não foram instalados.
3. A partir do pedido, selecione **Update** para começar a atualizar os metadados da federação. Se tiver acesso ao ambiente do servidor onde a aplicação está hospedada, pode utilizar opcionalmente o [programador automático](https://msdn.microsoft.com/library/ee517272.aspx)de atualização de metadados da FedUtil .
4. Clique em **Terminar** para completar o processo de atualização.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Aplicações web / APIs protegendo recursos usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos suportados
Se estiver a utilizar alguma outra biblioteca ou tiver implementado manualmente algum dos protocolos suportados, terá de rever a biblioteca ou a sua implementação para garantir que a chave está a ser recuperada do documento de descoberta openID Connect ou do documento de metadados da federação. Uma maneira de verificar isto é fazer uma pesquisa no seu código ou o código da biblioteca para quaisquer chamadas para o documento de descoberta OpenID ou o documento de metadados da federação.

Se a chave estiver a ser armazenada em algum lugar ou codificada na sua aplicação, pode recuperar manualmente a chave e atualizá-la em conformidade, executando um capotamento manual de acordo com as instruções no final deste documento de orientação. **É fortemente encorajado que você melhore a sua aplicação para apoiar** o capotamento automático usando qualquer uma das abordagens delineadas neste artigo para evitar futuras perturbações e sobrecargase se a Azure AD aumentar a sua cadência de capotamento ou tiver um capotamento de emergência fora da banda.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Como testar a sua aplicação para determinar se será afetada
Pode validar se a sua aplicação suporta o capotamento automático da chave, baixando os scripts e seguindo as instruções [neste repositório GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Como realizar uma capotamento manual se a sua aplicação não suportar capotamento automático
Se a sua aplicação **não** suportar o capotamento automático, terá de estabelecer um processo que monitorize periodicamente as chaves de assinatura da Azure AD e realize uma capotamento manual em conformidade. [Este repositório GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contém scripts e instruções sobre como fazê-lo.
