---
title: Utilizar a encriptação comum dinâmica com PlayReady e/ou Widevine | Microsoft Docs
description: Pode utilizar os Serviços de Multimédia do Azure para entregar transmissões em fluxo MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidas com o Microsoft PlayReady DRM. Também os pode utilizar para entregar DASH encriptado com o Widevine DRM. Este tópico mostra como encriptar de forma dinâmica com PlayReady e Widevine DRM.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: b8aaaaf1099bb100e45bce4f09aeb6434c132b88
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901615"
---
# <a name="use-playready-andor-widevine-dynamic-common-encryption"></a>Utilizar a encriptação comum dinâmica com PlayReady e/ou Widevine

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-playready-widevine.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>

> [!NOTE]
> Para obter a versão mais recente do Java SDK e começar a programar com o Java, veja [Introdução ao SDK de cliente Java para os Serviços de Multimédia](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Para transferir o SDK mais recente do PHP para os Serviços de Multimédia, procure a versão 0.5.7 do pacote Microsoft/WindowsAzure no [repositório do Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7). 

## <a name="overview"></a>Descrição geral

 Pode utilizar os Serviços de Multimédia para entregar transmissões em fluxo MPEG-DASH, Smooth Streaming e HTTP Live Streaming (HLS) protegidas com a [gestão de direitos digitais (DRM) do PlayReady](https://www.microsoft.com/playready/overview/). Também pode entregar transmissões em fluxo DASH encriptadas com licenças DRM do Widevine. Tanto o PlayReady, como o Widevine, são encriptados de acordo com a especificação de encriptação comum (ISO/IEC 23001 7 CENC). Pode utilizar o [SDK .NET dos Serviços de Multimédia](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir da versão 3.5.1) ou a API REST para configurar o AssetDeliveryConfiguration para utilizar o Widevine.

Os Media Services fornecem um serviço para entrega de licenças PlayReady e Widevine DRM. Os Serviços de Multimédia também disponibilizam APIs que lhe permitem configurar os direitos e as restrições que pretende que o runtime de DRM do PlayReady ou do Widevine imponha quando um utilizador reproduz conteúdo protegido. Quando um utilizador pede um conteúdo protegido por DRM, a aplicação de leitor pede uma licença ao serviço de licenças do Serviço de Multimédia. Se a aplicação de leitor tiver autorização, o serviço de licenças dos Serviços de Multimédia emite uma licença para o leitor. Uma licença PlayReady ou Widevine contém a chave de desencriptação que pode ser utilizada pelo leitor de cliente para desencriptar e transmitir em fluxo o conteúdo.

Também pode utilizar os seguintes parceiros dos Serviços de Multimédia para ajudar a fornecer licenças do Widevine: 

* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Para obter mais informações, veja a integração com [Axinom](media-services-axinom-integration.md) e [castLabs](media-services-castlabs-integration.md).

Os Media Services suportam várias formas de autorização de utilizadores que efetuam pedidos de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token. A política de token restrito tem de ser acompanhada por um token emitido por um serviço de tokens seguro (STS). Os Serviços de Multimédia suportam tokens no formato [simple web tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). 

Para obter mais informações, veja [Configure the content key's authorization policy](media-services-protect-with-aes128.md#configure_key_auth_policy) (Configurar a política de autorização da chave de conteúdo).

Para tirar partido da encriptação dinâmica, tem de ter um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Transmissão em Fluxo Uniforme de múltipla transmissão. Terá também de configurar as políticas de entrega para o elemento (descrito mais adiante neste tópico). Em seguida, com base no formato especificado no URL de transmissão em fluxo, o servidor de transmissão em fluxo a pedido irá garantir que a transmissão é entregue no protocolo que selecionou. Como resultado, armazena e paga os ficheiros apenas num formato de armazenamento. Os Serviços de Multimédia criam e entregam a resposta HTTP adequada com base em cada pedido de um cliente.

Este artigo é útil para os programadores que trabalham com aplicações que entregam multimédia protegida com vários DRMs, tais como o PlayReady e o Widevine. O artigo mostra-lhe como configurar o serviço de entrega de licenças do PlayReady com políticas de autorização para que apenas os clientes autorizados possam receber licenças do PlayReady ou do Widevine. Este também mostra como utilizar a encriptação dinâmica com PlayReady ou Widevine DRM sobre DASH.

>[!NOTE]
>Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo predefinido à mesma, no estado "Parado". Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e da encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado "Em execução”. 

## <a name="download-the-sample"></a>Transferir o exemplo
Pode transferir o exemplo descrito neste artigo em [Azure samples on GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) (Exemplos do Azure no GitHub).

## <a name="configure-dynamic-common-encryption-and-drm-license-delivery-services"></a>Configurar a encriptação comum dinâmica e os serviços de entrega de licenças DRM

Siga os passos gerais abaixo quando estiver a proteger os seus elementos com o PlayReady, através do serviço de entrega de licenças dos Serviços de Multimédia e também da encriptação dinâmica:

1. Crie um elemento e carregue ficheiros para o mesmo.

2. Codifique o elemento que contém o ficheiro para o MP4 de velocidade de transmissão adaptável definido.

3. Crie uma chave de conteúdo e associe-a ao elemento codificado. Nos Serviços de Multimédia, a chave de conteúdo contém a chave de encriptação do elemento.

4. Configure a política de autorização da chave de conteúdo. Tem de configurar a política de autorização da chave de conteúdo. O cliente tem de satisfazer a política antes de a chave de conteúdo lhe ser entregue.

    Quando cria a política de autorização da chave de conteúdo, tem de especificar o método de entrega (PlayReady ou Widevine) e as restrições (abertas ou tokens). Também tem de especificar informações específicas do tipo de entrega de chave que define a forma como a chave é entregue ao cliente (modelo de licença do [PlayReady](media-services-playready-license-template-overview.md) ou do [Widevine](media-services-widevine-license-template-overview.md)).

5. Configure a política de entrega para um elemento. A configuração da política de entrega inclui o protocolo de entrega (por exemplo, MPEG-DASH, HLS, Smooth Streaming ou todos). Também inclui o tipo de encriptação dinâmica (por exemplo, encriptação comum) e o URL de aquisição da licença do PlayReady ou do Widevine.

    Pode aplicar uma política diferente para cada protocolo no mesmo elemento. Por exemplo, pode aplicar a encriptação do PlayReady a Smooth/DASH e envelope AES a HLS. Quaisquer protocolos que não sejam definidos numa política de entrega (por exemplo, se adicionar uma única política que especifica apenas HLS como o protocolo) serão bloqueados da transmissão em fluxo. A exceção é se não tiver nenhuma política de entrega de elementos definida. Aí, todos os protocolos serão permitidos.

6. Crie um localizador OnDemand para obter um URL de transmissão em fluxo.

Pode encontrar um exemplo de .NET completo no final do artigo.

A imagem seguinte demonstra o fluxo de trabalho descrito anteriormente. Aqui, o token é utilizado para autenticação.

![Proteger com PlayReady](media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

O resto deste artigo apresenta explicações detalhadas, exemplos de códigos e ligações para tópicos que lhe mostram como realizar as tarefas descritas acima.

## <a name="current-limitations"></a>Limitações atuais
Se adicionar ou atualizar uma política de entrega de elementos, tem de eliminar qualquer localizador associado e criar um novo.

Atualmente, não são suportadas várias chaves de conteúdos se utilizar o Widevine com os Serviços de Multimédia para encriptar. 

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Criar um elemento e carregar ficheiros no elemento.
Para gerir, codificar e transmitir em fluxo os seus vídeos, primeiro tem de carregar o conteúdo para os Serviços de Multimédia. Quando estiver carregado, o seu conteúdo é armazenado em segurança na cloud para processamento adicional e transmissão em fluxo.

Para obter mais informações, veja [Upload files into a Media Services account](media-services-dotnet-upload-files.md) (Carregar ficheiros para uma conta dos Serviços de Multimédia).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codifique o elemento que contém o ficheiro para o MP4 de velocidade de transmissão adaptável definido.
Com a encriptação dinâmica, vai criar um elemento que contenha um conjunto de ficheiros MP4 com velocidade de transmissão múltipla ou ficheiros de origem de Smooth Streaming de transmissão múltipla. Em seguida, com base no formato especificado no manifesto e no pedido de fragmento, o servidor de transmissão em fluxo a pedido garante que recebe a transmissão em fluxo no protocolo que selecionou. Depois, armazena e paga os ficheiros apenas num único formato de armazenamento. Os Serviços de Multimédia criam e entregam a resposta adequada com base nos pedidos de um cliente. Para obter mais informações, veja [Dynamic packaging overview](media-services-dynamic-packaging-overview.md) (Descrição geral do empacotamento dinâmico).

Para obter instruções sobre como codificar, veja [Encode an asset by using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Utilizar o Media Encoder Standard para codificar elementos).

## <a id="create_contentkey"></a>Criar uma chave de conteúdo e associe-a com elemento codificado
Nos Media Services, a chave de conteúdo contém a chave na qual pretende encriptar um elemento.

Para obter mais informações, veja [Create a content key](media-services-dotnet-create-contentkey.md) (Criar chaves de conteúdo).

## <a id="configure_key_auth_policy"></a>Configurar a política de autorização da chave de conteúdo
Os Media Services suportam várias formas de autenticar utilizadores que efetuam pedidos de chave. Tem de configurar a política de autorização da chave de conteúdo. O cliente (leitor) tem de satisfazer a política antes de a chave lhe ser entregue. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização, quer sejam restrições abertas ou de token.

Para obter mais informações, veja [Configure a content key authorization policy](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption) (Configurar uma política de autorização de chave de conteúdo).

## <a id="configure_asset_delivery_policy"></a>Configurar uma política de entrega de elementos
Configure a política de entrega para o seu elemento. Alguns dos aspetos da configuração da política de entrega de elementos incluem:

* O URL de aquisição da licença da DRM.
* O protocolo de entrega de elementos, (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos).
* O tipo de encriptação dinâmica (neste caso, a encriptação comum).

Para obter mais informações, veja [Configure asset delivery policy](media-services-dotnet-configure-asset-delivery-policy.md) (Configurar a política de entrega de elementos).

## <a id="create_locator"></a>Criar um localizador de transmissão em fluxo OnDemand para obter um URL de transmissão em fluxo
Tem de indicar aos seus utilizadores o URL de transmissão de Smooth Streaming, DASH ou HLS.

> [!NOTE]
> Se adicionar ou atualizar a sua política de entrega de elementos, tem de eliminar qualquer localizador existente e criar um novo.
>
>

Para obter instruções sobre como publicar um elemento e compilar um URL de transmissão em fluxo, consulte [Compilar um URL de transmissão em fluxo](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Obter um token de teste
Obtenha um token de teste baseado na restrição de token que foi utilizada para a política de autorização de chave.

```csharp
// Deserializes a string containing an XML representation of a TokenRestrictionTemplate
// back into a TokenRestrictionTemplate class instance.
TokenRestrictionTemplate tokenTemplate =
TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

// Generate a test token based on the data in the given TokenRestrictionTemplate.
//The GenerateTestToken method returns the token without the word "Bearer" in front,
//so you have to add it in front of the token string.
string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Pode utilizar o [Leitor dos Serviços de Multimédia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar a sua transmissão em fluxo.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento dos Serviços de Multimédia com .NET).

2. Adicione os elementos seguintes a **appSettings** definidos no ficheiro app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exemplo

O exemplo seguinte demonstra a funcionalidade que foi introduzida no SDK dos Serviços de Multimédia para a versão 3.5.2. do .NET. (Mais concretamente, inclui a capacidade de definir um modelo de licença do Widevine e pedir uma licença do Widevine a partir dos Serviços de Multimédia.)

Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.

>[!NOTE]
>Existe um limite de um milhão de políticas para diferentes políticas dos Serviços de Multimédia (por exemplo, para a política Localizador ou ContentKeyAuthorizationPolicy). Se utilizar os mesmos dias/permissões de acesso, etc., utilize o mesmo ID de política. Um exemplo são as políticas para os localizadores que se destinam a estar em vigor durante muito tempo (políticas de não carregamento). 

Para obter mais informações, veja [Manage assets and related entities with the Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies) (Gerir elementos e entidades relacionadas com o SDK .NET dos Serviços de Multimédia).

Certifique-se de que atualiza as variáveis para apontar para as pastas onde se encontram os seus ficheiros de entrada.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DynamicEncryptionWithDRM
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an XML representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note that you need to pass the key ID GUID because 
                // TokenClaim.ContentKeyIdentifierClaim was specified during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            // You can use the https://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
            // Note that DASH works on Internet Explorer 11 (via PlayReady), Microsoft Edge (via PlayReady), and Chrome (via Widevine).

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                        inputAsset.Name,
                        encodingPreset));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }


        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
        {

            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with open restrictions
            // and create an authorization policy.         

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Open",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                    Requirements = null
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with no restrictions").
                Result;


            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString,
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static private string ConfigurePlayReadyLicenseTemplate()
        {
            // The following code configures the PlayReady license template by using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
            //It contains a field for a custom data string between the license server and the application
            //(which might be useful for custom app logic) as well as a list of one or more license templates.
            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template you can use to create PlayReady licenses
            // to be returned to end users.
            //It contains the data on the content key in the license and any rights or restrictions to be
            //enforced by the PlayReady DRM runtime when you use the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
            //Configure whether the license is persistent (saved in persistent storage on the client)
            //or nonpersistent (held in memory only while the player uses the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150. If false (the default), the MinimumSecurityLevel property of the license is set to 2,000.
            licenseTemplate.AllowTestDevices = true;

            // You also can configure the PlayRight in the PlayReady license by using the PlayReadyPlayRight class.
            // It grants the user the ability to play back the content subject to the zero or more restrictions
            // configured in the license and on the PlayRight itself (for playback-specific policy).
            // Much of the policy on the PlayRight has to do with output restrictions,
            // which control the types of outputs that the content can be played over and
            // any restrictions that must be put in place when you use a given output.
            // For example, if DigitalVideoOnlyContentRestriction is enabled,
            // the DRM runtime allows the video to be displayed only over digital outputs
            //(analog video outputs aren't allowed to pass the content).

            //IMPORTANT: These types of restrictions can be very powerful but also can affect the consumer experience.
            // If output protections are too restrictive, 
            // content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

            // For example:
            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

            responseTemplate.LicenseTemplates.Add(licenseTemplate);

            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
        }

        private static string ConfigureWidevineLicenseTemplate()
        {
            var template = new WidevineMessage
            {
                allowed_track_types = AllowedTrackTypes.SD_HD,
                content_key_specs = new[]
            {
                    new ContentKeySpecs
                    {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                    }
                },
                policy_overrides = new
                {
                    can_play = true,
                    can_persist = true,
                    can_renew = false
                }
            };

            string configuration = JsonConvert.SerializeObject(template);
            return configuration;
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            // Get the PlayReady license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

            // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
            // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
            // WidevineBaseLicenseAcquisitionUrl (used in the following) also tells dynamic encryption
            // to append /? KID =< keyId > to the end of the URL when you create the manifest.
            // As a result, the Widevine license acquisition URL has the KID appended twice,
            // so you need to remove the KID in the URL when you call GetKeyDeliveryUrl.

            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
            uriBuilder.Query = String.Empty;
            widevineUrl = uriBuilder.Uri;

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

            };

            // In this case, we specify only the DASH streaming protocol in the delivery policy.
            // All other protocols are blocked from streaming.
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


            // Add AssetDelivery Policy to the asset.
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }

        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the 
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day read-only access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
* [Use the CENC with multi-DRM and access control](media-services-cenc-with-multidrm-access-control.md) (Utilizar a Encriptação Comum (CENC) com multi DRM e com controlo de acesso)
* [Configure Widevine packaging with Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services) (Configurar o empacotamento do Widevine com os Serviços de Multimédia)
* [Anunciar os serviços de entrega de licença Widevine da Google nos Serviços de Multimédia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
