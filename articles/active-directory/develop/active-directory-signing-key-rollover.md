---
title: Assinatura de Chave Rollover na plataforma de identidade da Microsoft
description: Este artigo discute as melhores práticas de rollover chave de assinatura para o Azure Ative Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: bd2bd67774eb55051e55e4433984c0fd1fda5240
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755575"
---
# <a name="signing-key-rollover-in-the-microsoft-identity-platform"></a>Assinatura de capotamento de chaves na plataforma de identidade da Microsoft
Este artigo discute o que precisa de saber sobre as chaves públicas que são usadas pela plataforma de identidade da Microsoft para assinar fichas de segurança. É importante notar que estas teclas rerolam periodicamente e, em caso de emergência, podem ser reviradas imediatamente. Todas as aplicações que utilizam a plataforma de identidade da Microsoft devem ser capazes de lidar programáticamente com o processo de capotamento da chave. Continuar a ler Para entender como funcionam as teclas, como avaliar o impacto da capotamento na sua aplicação e como atualizar a sua aplicação ou estabelecer um processo de capotamento manual periódico para lidar com a capotamento da chave, se necessário.

## <a name="overview-of-signing-keys-in-the-microsoft-identity-platform"></a>Visão geral das chaves de assinatura na plataforma de identidade da Microsoft
A plataforma de identidade da Microsoft utiliza criptografia de chaves públicas baseada nos padrões da indústria para estabelecer confiança entre si e as aplicações que a utilizam. Em termos práticos, isto funciona da seguinte forma: A plataforma de identidade da Microsoft utiliza uma chave de assinatura que consiste num par de chaves público e privado. Quando um utilizador assina uma aplicação que utiliza a plataforma de identidade da Microsoft para autenticação, a plataforma de identidade da Microsoft cria um símbolo de segurança que contém informações sobre o utilizador. Este token é assinado pela plataforma de identidade da Microsoft utilizando a sua chave privada antes de ser devolvido à aplicação. Para verificar se o token é válido e originou-se da plataforma de identidade da Microsoft, a aplicação deve validar a assinatura do token utilizando as chaves públicas expostas pela plataforma de identidade microsoft que está contida no documento de [descoberta OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) do inquilino ou [documento de metadados](../azuread-dev/azure-ad-federation-metadata.md)da federação SAML/WS-Fed.

Por razões de segurança, a chave de assinatura da plataforma de identidade da Microsoft é periódica e, em caso de emergência, pode ser revirada imediatamente. Não existe tempo definido ou garantido entre estes rolos-chave - qualquer aplicação que se integre com a plataforma de identidade da Microsoft deve estar preparada para lidar com um evento de capotamento chave, independentemente da frequência com que possa ocorrer. Se isso não acontecer, e a sua aplicação tentar usar uma chave caducada para verificar a assinatura num token, o pedido de inscrição falhará.  Verificar a cada 24 horas para obter atualizações é uma boa prática, com acelerações (uma vez a cada cinco minutos no máximo) atualizações imediatas do documento chave se um sinal for encontrado com um identificador de chave desconhecido. 

Há sempre mais do que uma chave válida disponível no documento de descoberta OpenID Connect e no documento de metadados da federação. A sua aplicação deve estar preparada para utilizar todas e todas as chaves especificadas no documento, uma vez que uma chave pode ser enrolada em breve, outra pode ser a sua substituição, e assim por diante.  O número de chaves presentes pode mudar ao longo do tempo com base na arquitetura interna da plataforma de identidade da Microsoft, uma vez que apoiamos novas plataformas, novas nuvens ou novos protocolos de autenticação. Nem a ordem das chaves na resposta JSON nem a ordem em que foram expostas devem ser consideradas mesquinhas para a sua aplicação. 

As aplicações que suportam apenas uma única chave de assinatura, ou aquelas que requerem atualizações manuais às teclas de assinatura, são inerentemente menos seguras e fiáveis.  Devem ser atualizados para utilizar [bibliotecas-padrão](reference-v2-libraries.md) para garantir que estão sempre a utilizar chaves de assinatura atualizadas, entre outras boas práticas. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Como avaliar se a sua aplicação será afetada e o que fazer em relação a isso
A forma como a sua aplicação lida com a capotamento da chave depende de variáveis como o tipo de aplicação ou que protocolo de identidade e biblioteca foi usado. As secções abaixo avaliam se os tipos de aplicações mais comuns são impactados pela capotagem da chave e fornecem orientações sobre como atualizar a aplicação para suportar a capotagem automática ou atualizar manualmente a chave.

* [Aplicações de clientes nativos que acedem a recursos](#nativeclient)
* [Aplicações web / APIs que acedem a recursos](#webclient)
* [Aplicações web / APIs protegendo recursos e construídos usando serviços de aplicações Azure](#appservices)
* [Aplicações web / APIs protegendo recursos usando .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware](#owin)
* [Aplicações web / APIs protegendo recursos usando .NET Core OpenID Connect ou JwtBearerAuthentication middleware](#owincore)
* [Aplicações web / APIs protegendo recursos usando Node.js módulo de passaporte-azure-ad](#passport)
* [Aplicações web / APIs protegendo recursos e criadas com o Visual Studio 2015 ou mais tarde](#vs2015)
* [Aplicações web que protegem recursos e criadas com o Visual Studio 2013](#vs2013)
* Web APIs protegendo recursos e criado com Visual Studio 2013
* [Aplicações web que protegem recursos e criadas com o Visual Studio 2012](#vs2012)
* [Aplicações web que protegem recursos e criadas com o Visual Studio 2010, 2008 o usando a Windows Identity Foundation](#vs2010)
* [Aplicações web / APIs protegendo recursos usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos suportados](#other)

Esta orientação **não** é aplicável para:

* As aplicações adicionadas da Azure AD Application Gallery (incluindo o Custom) têm orientações separadas no que diz respeito à assinatura de chaves. [Mais informações.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* As aplicações no local publicadas através do proxy de aplicações não têm de se preocupar em assinar chaves.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Aplicações de clientes nativos que acedem a recursos
Aplicações que só acedem a recursos (i.e) Microsoft Graph, KeyVault, Outlook API e outras APIs da Microsoft) geralmente só obtêm um token e o transmitem ao proprietário do recurso. Dado que não estão a proteger quaisquer recursos, não inspecionam o símbolo e, por conseguinte, não necessitam de assegurar a sua correta assinatura.

As aplicações de clientes nativos, seja desktop ou mobile, enquadram-se nesta categoria e, portanto, não são afetadas pelo capotamento.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Aplicações web / APIs que acedem a recursos
Aplicações que só acedem a recursos (i.e) Microsoft Graph, KeyVault, Outlook API e outras APIs da Microsoft) geralmente só obtêm um token e o transmitem ao proprietário do recurso. Dado que não estão a proteger quaisquer recursos, não inspecionam o símbolo e, por conseguinte, não necessitam de assegurar a sua correta assinatura.

As aplicações web e as APIs web que estão a utilizar o fluxo apenas de aplicações (credenciais de cliente/certificado de cliente) para solicitar que os tokens se enquadram nesta categoria e, portanto, não são impactados pelo capotamento.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Aplicações web / APIs protegendo recursos e construídos usando serviços de aplicações Azure
A funcionalidade autenticação /Autorização (EasyAuth) da Azure App Services já tem a lógica necessária para lidar automaticamente com a capotagem da chave.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Aplicações web / APIs protegendo recursos usando .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware
Se a sua aplicação estiver a utilizar o middleware .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication, já tem a lógica necessária para lidar automaticamente com a capotagem da chave.

Pode confirmar que a sua aplicação está a utilizar qualquer uma delas, procurando qualquer um dos seguintes cortes nos ficheiros Startup.cs ou Startup.Auth.cs da sua aplicação.

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Aplicações web / APIs protegendo recursos usando .NET Core OpenID Connect ou JwtBearerAuthentication middleware
Se a sua aplicação estiver a utilizar o middleware .NET Core OWIN OpenID Ou JwtBearerAuthentication, já tem a lógica necessária para lidar automaticamente com a capotamento da chave.

Pode confirmar que a sua aplicação está a utilizar qualquer uma delas, procurando qualquer um dos seguintes excertos na Startup.cs ou Startup.Auth.cs da sua aplicação

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

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Aplicações web / APIs protegendo recursos usando Node.js módulo de passaporte-azure-ad
Se a sua aplicação estiver a utilizar o módulo de anúncio de passaporte Node.js, já tem a lógica necessária para lidar automaticamente com a capotamento da chave.

Pode confirmar que o anúncio do seu passaporte de candidatura procurando o seguinte corte no app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Aplicações web / APIs protegendo recursos e criadas com o Visual Studio 2015 ou mais tarde
Se a sua aplicação foi construída utilizando um modelo de aplicação web no Visual Studio 2015 ou mais tarde e selecionou **Contas de Trabalho ou Escola** a partir do menu Change **Authentication,** já tem a lógica necessária para lidar com a capotamento da chave automaticamente. Esta lógica, incorporada no middleware OWIN OpenID Connect, recupera e caches as teclas do documento de descoberta OpenID Connect e refresca-as periodicamente.

Se adicionar a autenticação manual à sua solução, a sua aplicação pode não ter a lógica de capotamento da chave necessária. Você mesmo terá de escrevê-lo, ou seguir os passos em [aplicações Web /APIs usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos suportados](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Aplicações web que protegem recursos e criadas com o Visual Studio 2013
Se a sua aplicação foi construída usando um modelo de aplicação web no Visual Studio 2013 e selecionou **Contas Organizacionais** do menu **Change Authentication,** já tem a lógica necessária para lidar com a capotamento da chave automaticamente. Esta lógica armazena o identificador único da sua organização e a informação chave de assinatura em duas tabelas de base de dados associadas ao projeto. Pode encontrar a cadeia de ligação para a base de dados no ficheiro Web.config do projeto.

Se adicionar a autenticação manual à sua solução, a sua aplicação pode não ter a lógica de capotamento da chave necessária. Você mesmo terá de escrevê-lo, ou seguir os passos em [aplicações Web /APIs usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos suportados.](#other)

Os seguintes passos irão ajudá-lo a verificar se a lógica está a funcionar corretamente na sua aplicação.

1. No Visual Studio 2013, abra a solução e, em seguida, clique no **separador Explorador** do Servidor na janela direita.
2. Expandir **ligações de dados,** **Padrão de Ligação** e, em seguida, **Tabelas**. Localizar a tabela **IssuingAuthorityKeys,** clicar à direita e, em seguida, clicar em **Mostrar Dados da Tabela**.
3. Na tabela **IssuingAuthorityKeys,** haverá pelo menos uma linha, que corresponde ao valor da impressão digital da chave. Elimine todas as linhas da mesa.
4. Clique com o botão direito na tabela **dos Inquilinos** e, em seguida, clique em **Mostrar Dados de Tabela**.
5. Na tabela **dos Inquilinos,** haverá pelo menos uma fila, o que corresponde a um identificador de inquilinos de diretório único. Elimine todas as linhas da mesa. Se não eliminar as linhas na tabela **Dos Inquilinos** e na tabela **IssuingAuthorityKeys,** terá um erro no tempo de execução.
6. Compile e execute a aplicação. Depois de ter iniciado sessão na sua conta, pode parar a aplicação.
7. Volte ao Explorador do **Servidor** e veja os valores na tabela **IssuingAuthorityKeys** and **Tenants.** Vai notar que foram automaticamente repovoados com as informações apropriadas do documento de metadados da federação.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Web APIs protegendo recursos e criado com Visual Studio 2013
Se criou uma aplicação web API no Visual Studio 2013 utilizando o modelo da API Web e, em seguida, selecionou **Contas Organizacionais** a partir do menu **Change Authentication,** já tem a lógica necessária na sua aplicação.

Se configurar manualmente a autenticação, siga as instruções abaixo para aprender a configurar a sua API web para atualizar automaticamente as suas informações-chave.

O seguinte corte de código demonstra como obter as chaves mais recentes do documento de metadados da federação e, em seguida, usar o [Manipulador de Token JWT](/previous-versions/dotnet/framework/security/json-web-token-handler) para validar o token. O código de corte assume que irá utilizar o seu próprio mecanismo de caching para persistir na chave para validar futuras fichas da plataforma de identidade da Microsoft, seja numa base de dados, ficheiro de configuração ou em qualquer outro lugar.

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
Se a sua aplicação foi construída no Visual Studio 2012, provavelmente usou a Ferramenta de Identidade e Acesso para configurar a sua aplicação. Também é provável que esteja a utilizar o [Registo de Nomes emitentes validadores (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry). O VINR é responsável pela manutenção de informações sobre fornecedores de identidade fidedignos (plataforma de identidade microsoft) e as chaves utilizadas para validar fichas emitidas por eles. O VINR também facilita a atualização automática das informações-chave armazenadas num ficheiro Web.config, descarregando o mais recente documento de metadados da federação associado ao seu diretório, verificando se a configuração está desatualizada com o documento mais recente e atualizando a aplicação para utilizar a nova chave conforme necessário.

Se criou a sua aplicação utilizando qualquer uma das amostras de código ou documentação de passagem fornecida pela Microsoft, a lógica de capotamento da chave já está incluída no seu projeto. Você vai notar que o código abaixo já existe no seu projeto. Se a sua aplicação ainda não tiver esta lógica, siga os passos abaixo para adicioná-la e verificar se está a funcionar corretamente.

1. No **Solution Explorer,** adicione uma referência ao **conjunto System.IdentityModel** para o projeto apropriado.
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
4. Invocar o método **RefreshValidationSettings()** no método **Application_Start()** em **Global.asax.cs** como mostrado:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Depois de seguir estes passos, o Web.config da sua aplicação será atualizado com as informações mais recentes do documento de metadados da federação, incluindo as chaves mais recentes. Esta atualização ocorrerá sempre que a sua reserva de aplicação recicla no IIS; por defeito, o IIS é definido para reciclar aplicações a cada 29 horas.

Siga os passos abaixo para verificar se a lógica de capotamento da chave está a funcionar.

1. Depois de verificar que a sua aplicação está a utilizar o código acima, abra o ficheiro **Web.config** e navegue para o **\<issuerNameRegistry>** bloco, procurando especificamente as seguintes linhas:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Na **\<add thumbprint="">** definição, altere o valor da impressão digital, substituindo qualquer personagem por outro. Guarde o ficheiro **Web.config**.
3. Construa a aplicação e, em seguida, executá-la. Se conseguir completar o processo de inscrição, a sua aplicação está a atualizar com sucesso a chave, descarregando as informações necessárias do documento de metadados da federação do seu diretório. Se tiver problemas de sessão, certifique-se de que as alterações na sua aplicação estão corretas lendo o [Sign-On de adicionar à sua aplicação web utilizando](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) o artigo da plataforma de identidade da Microsoft, ou descarregando e inspecionando a seguinte amostra de código: [Multi-Tenant Cloud Application for Azure Ative Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Aplicações web que protegem recursos e criadas com o Visual Studio 2008 ou 2010 e a Windows Identity Foundation (WIF) v1.0 para .NET 3.5
Se construiu uma aplicação em WIF v1.0, não existe nenhum mecanismo fornecido para atualizar automaticamente a configuração da sua aplicação para usar uma nova chave.

* *A maneira mais fácil* Utilize a ferramenta FedUtil incluída no WIF SDK, que pode recuperar o documento mais recente de metadados e atualizar a sua configuração.
* Atualize a sua aplicação para .NET 4.5, que inclui a versão mais recente do WIF localizada no espaço de nomes do Sistema. Em seguida, pode utilizar o [Registo de Nome do Emitente Validador (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) para realizar atualizações automáticas da configuração da aplicação.
* Efetuar uma capotamento manual de acordo com as instruções no final deste documento de orientação.

Instruções para utilizar o FedUtil para atualizar a sua configuração:

1. Verifique se tem o WIF v1.0 SDK instalado na sua máquina de desenvolvimento para o Visual Studio 2008 ou 2010. Pode [descarregá-lo a partir daqui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se ainda não o tiver instalado.
2. No Visual Studio, abra a solução e, em seguida, clique com a direita no projeto aplicável e selecione **Metadados da Federação de Atualização**. Se esta opção não estiver disponível, a FedUtil e/ou a WIF v1.0 SDK não foi instalada.
3. A partir do pedido, selecione **Update** para começar a atualizar os metadados da federação. Se tiver acesso ao ambiente do servidor onde a aplicação está hospedada, pode utilizar opcionalmente o programador automático de [atualização de metadados](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10))da FedUtil .
4. Clique **em Terminar** para concluir o processo de atualização.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Aplicações web / APIs protegendo recursos usando quaisquer outras bibliotecas ou implementando manualmente qualquer um dos protocolos suportados
Se estiver a utilizar alguma outra biblioteca ou implementado manualmente qualquer um dos protocolos suportados, terá de rever a biblioteca ou a sua implementação para garantir que a chave está a ser recuperada a partir do documento de descoberta OpenID Connect ou do documento de metadados da federação. Uma forma de verificar isto é fazer uma pesquisa no seu código ou no código da biblioteca para quaisquer chamadas para o documento de descoberta OpenID ou para o documento de metadados da federação.

Se a chave estiver a ser armazenada em algum lugar ou codificada na sua aplicação, pode recuperar manualmente a chave e atualizá-la em conformidade, realizando uma capotamento manual de acordo com as instruções no final deste documento de orientação. **É fortemente encorajado que melhore a sua aplicação para suportar a capotagem automática** usando qualquer um dos contornos de abordagens deste artigo para evitar futuras perturbações e sobrecargas se a plataforma de identidade da Microsoft aumentar a sua cadência de capotamento ou tiver um capotamento fora de banda de emergência.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Como testar a sua aplicação para determinar se será afetada
Pode validar se a sua aplicação suporta o capotamento automático da chave, descarregando os scripts e seguindo as instruções [neste repositório GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Como realizar um capotamento manual se a sua aplicação não suporta capotamento automático
Se a sua aplicação **não** suportar o capotamento automático, terá de estabelecer um processo que monitorize periodicamente as chaves de assinatura da plataforma de identidade da Microsoft e realize uma capotagem manual em conformidade. [Este repositório GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contém scripts e instruções sobre como fazê-lo.