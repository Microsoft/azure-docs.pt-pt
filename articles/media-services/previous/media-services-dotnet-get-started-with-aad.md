---
title: Autenticação de utilização do Azure AD para aceder à API de serviços de multimédia do Azure com .NET | Documentos da Microsoft
description: Este tópico mostra como utilizar a autenticação do Azure Active Directory (Azure AD) para aceder à API de serviços de multimédia do Azure (AMS) com o .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 15b986d4e7567be48c582e4a39b727ab110de2be
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181508"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Utilizar autenticação do Azure AD para aceder à API de serviços de multimédia do Azure com .NET

A partir do windowsazure.mediaservices 4.0.0.4, o Media Services do Azure suporta a autenticação com base no Azure Active Directory (Azure AD). Este tópico mostra-lhe como utilizar a autenticação do Azure AD para aceder à API de serviços de multimédia do Azure com o Microsoft .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Media Services. Para obter mais informações, consulte [criar uma conta de Media Services do Azure com o portal do Azure](media-services-portal-create-account.md).
- A versão mais recente [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) pacote.
- Familiaridade com o tópico [aceder ao suporte de dados de API dos serviços Azure com a descrição geral da autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Quando estiver usando autenticação do Azure AD com os serviços de multimédia do Azure, pode autenticar-se de uma de duas formas:

- **Autenticação de utilizador** efetua a autenticação de uma pessoa que está a utilizar a aplicação para interagir com os recursos de serviços de multimédia do Azure. O aplicativo interativo do primeiro deve pedir ao utilizador as credenciais. Um exemplo é uma aplicação de consola de gestão que é utilizada por utilizadores autorizados para monitorizar tarefas de codificação ou transmissão em direto. 
- **Autenticação do principal de serviço** autentica um serviço. Aplicativos geralmente usam este método de autenticação são as aplicações que executem serviços de daemon, serviços de camada intermediária ou tarefas agendadas, como aplicações web, aplicações de funções, aplicações lógicas, APIs ou microsserviços.

>[!IMPORTANT]
>Atualmente, o serviço de multimédia do Azure suporta um modelo de autenticação do serviço de controle de acesso do Azure. No entanto, a autorização de controlo de acesso será preterido em 22 de Junho de 2018. Recomendamos que migre para um modelo de autenticação do Azure Active Directory logo que possível.

## <a name="get-an-azure-ad-access-token"></a>Obtenha o token de um acesso do Azure AD

Para ligar à API de serviços de multimédia do Azure com a autenticação do Azure AD, a aplicação cliente precisa de pedir um token de acesso do Azure AD. Quando utiliza o SDK de cliente .NET de serviços de multimédia, muitos detalhes sobre como adquirir um token de acesso do Azure AD são encapsulados e simplificados por si a [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) e [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs)classes. 

Por exemplo, não precisa de fornecer a autoridade do Azure AD, o URI do recurso de serviços de multimédia ou a nativo detalhes da aplicação do Azure AD. Estes são os valores conhecidos que já estão configurados pela classe de fornecedor do token de acesso do Azure AD. 

Se não estiver a utilizar o SDK de .NET de serviço de multimédia do Azure, recomendamos que utilize o [do Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md). Para obter os valores para os parâmetros que tem de utilizar com a biblioteca de autenticação do Azure AD, veja [utilizar o portal do Azure para aceder às definições de autenticação do Azure AD](media-services-portal-get-started-with-aad.md).

Também tem a opção de substituir a implementação padrão do **AzureAdTokenProvider** com sua própria implementação.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Instalar e configurar o SDK de .NET de serviços de multimédia do Azure

>[!NOTE] 
>Para utilizar autenticação do Azure AD com o SDK .NET dos Media Services, tem de ter a versão mais recente [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) pacote. Além disso, adicione uma referência para o **ActiveDirectory** assembly. Se estiver a utilizar uma aplicação existente, inclua o **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** assembly. 

1. Crie um novo aplicativo de console do c# no Visual Studio.
2. Utilize o [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) pacote NuGet para instalar **SDK de .NET de serviços de multimédia do Azure**. 

    Para adicionar referências com o NuGet, siga os passos seguintes: no **Explorador de soluções**, clique com o botão direito no nome do projeto e, em seguida, selecione **gerir pacotes NuGet**. Em seguida, procure **windowsazure.mediaservices** e selecione **instalar**.
    
    -ou-

    Execute o seguinte comando numa **Package Manager Console** no Visual Studio.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Adicione **usando** para seu código-fonte.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Utilizar a autenticação de utilizador

Para ligar à API de serviço de multimédia do Azure com a opção de autenticação de utilizador, a aplicação cliente precisa de pedir um token do Azure AD utilizando os seguintes parâmetros:  

- Endpoint de inquilino do Azure AD. As informações de inquilino podem ser obtidas a partir do portal do Azure. Paire o rato sobre o utilizador com sessão iniciada no canto superior direito.
- URI do recurso de serviços de multimédia.
- ID de cliente de aplicação de serviços de multimédia (nativo). 
- URI de redirecionamento da aplicação de serviços de multimédia (nativo). 

Os valores para estes parâmetros podem ser encontrados na **AzureEnvironments.AzureCloudEnvironment**. O **AzureEnvironments.AzureCloudEnvironment** constante é um auxiliar no SDK do .NET para obter o ambiente certo definições de variáveis para um centro de dados do Azure público. 

Contém definições de ambiente predefinido para aceder aos serviços de multimédia nos centros de dados pública apenas. Para as regiões de cloud soberanas ou do Governo, pode usar **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment**, ou **AzureGermanCloudEnvironment** respectivamente.

O exemplo de código seguinte cria um token:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Para começar a programar com serviços de multimédia, tem de criar uma **CloudMediaContext** instância que representa o contexto de servidor. O **CloudMediaContext** inclui referências a coleções importantes, incluindo tarefas, recursos, ficheiros, as políticas de acesso e localizadores. 

Também tem de passar os **URI para os serviços de REST do suporte de dados do recurso** para o **CloudMediaContext** construtor. Para obter o URI do recurso de serviços de REST de suporte de dados, iniciar sessão no portal do Azure, selecione a sua conta de Media Services do Azure, selecione **acesso à API**e, em seguida, selecione **ligar a serviços de multimédia do Azure com a autenticação de utilizador**. 

O exemplo de código seguinte cria um **CloudMediaContext** instância:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

O exemplo seguinte mostra como criar o token do Azure AD e o contexto:

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Se receber uma exceção que diz "o servidor remoto devolveu um erro: Não autorizada (401),"Veja as [controlo de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control) secção de aceder à API de serviços de multimédia do Azure com a descrição geral da autenticação do Azure AD.

## <a name="use-service-principal-authentication"></a>Utilizar a autenticação do principal de serviço
    
Para ligar à API de serviços de multimédia do Azure, com a opção de principal do serviço, a sua aplicação de camada intermediária, tem de (web API ou aplicação web) solicita um token do Azure AD com os seguintes parâmetros:  

- Endpoint de inquilino do Azure AD. As informações de inquilino podem ser obtidas a partir do portal do Azure. Paire o rato sobre o utilizador com sessão iniciada no canto superior direito.
- URI do recurso de serviços de multimédia.
- Valores de aplicações do Azure AD: os **ID de cliente** e **segredo do cliente**.

Os valores para o **ID de cliente** e **segredo do cliente** parâmetros podem ser encontrados no portal do Azure. Para obter mais informações, consulte [introdução à autenticação do Azure AD no portal do Azure](media-services-portal-get-started-with-aad.md).

O exemplo de código seguinte cria um token com o **AzureAdTokenCredentials** construtor que usa **AzureAdClientSymmetricKey** como um parâmetro: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Também pode especificar a **AzureAdTokenCredentials** construtor que usa **AzureAdClientCertificate** como um parâmetro. 

Para obter instruções sobre como criar e configurar um certificado num formato que pode ser utilizado pelo Azure AD, consulte [autenticar no Azure AD em aplicações daemon com certificados - passos de configuração manual](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Para começar a programar com serviços de multimédia, tem de criar uma **CloudMediaContext** instância que representa o contexto de servidor. Também tem de passar os **URI para os serviços de REST do suporte de dados do recurso** para o **CloudMediaContext** construtor. Pode obter o **URI para os serviços de REST do suporte de dados do recurso** valor a partir do portal do Azure também.

O exemplo de código seguinte cria um **CloudMediaContext** instância:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
O exemplo seguinte mostra como criar o token do Azure AD e o contexto:

    namespace AzureADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Passos Seguintes

Introdução ao [carregar ficheiros para a conta](media-services-dotnet-upload-files.md).
