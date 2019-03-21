---
title: Proteger HLS conteúdo com o Microsoft PlayReady ou do Apple FairPlay - Azure | Documentos da Microsoft
description: Este tópico fornece uma visão geral e mostra como utilizar os serviços de multimédia do Azure para encriptar dinamicamente o seu conteúdo de HTTP Live Streaming (HLS) com o Apple FairPlay. Ele também mostra como utilizar o serviço de entrega de licença de serviços de multimédia para entregar licenças do FairPlay para clientes.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7763338a5c2d5ccb58fe912d9989ab5bb57d5932
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258501"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Proteger o seu HLS conteúdo com o Apple FairPlay ou o Microsoft PlayReady
Serviços de multimédia do Azure permite-lhe encriptar dinamicamente o seu conteúdo de HTTP Live Streaming (HLS), utilizando os seguintes formatos:  

* **Chave não encriptada do AES-128 envelope**

    A parte inteira é encriptada utilizando o **AES-128 CBC** modo. A descriptografia da transmissão em fluxo é suportada nativamente por iOS e o leitor dos X. Para obter mais informações, consulte [AES-128, utilizando a encriptação dinâmica e o serviço de entrega de chave](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    As amostras de áudio e vídeos individuais são encriptadas utilizando a **AES-128 CBC** modo. **FairPlay Streaming** (FPS) está integrado nos sistemas operativos dos dispositivos, com suporte nativo em dispositivos iOS e Apple TV. Safari nos X permite FPS com o suporte de interface Encrypted Media Extensions (EME).
* **Microsoft PlayReady**

A imagem seguinte mostra os **HLS + FairPlay ou PlayReady encriptação dinâmica** fluxo de trabalho.

![Diagrama de fluxo de trabalho de encriptação dinâmica](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Este artigo demonstra como utilizar os serviços de multimédia para encriptar dinamicamente o conteúdo HLS com o Apple FairPlay. Ele também mostra como utilizar o serviço de entrega de licença de serviços de multimédia para entregar licenças do FairPlay para clientes.

> [!NOTE]
> Se também pretender encriptar o seu conteúdo HLS com PlayReady, terá de criar uma chave de conteúdo comum e associá-lo com o seu elemento. Terá também de configurar a política de autorização da chave de conteúdo, conforme descrito em [encriptação comum dinâmica com PlayReady usando](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Requisitos e considerações

É necessário o seguinte ao utilizar os serviços de multimédia para entregar HLS encriptado com FairPlay e para entregar licenças do FairPlay:

  * Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Uma conta dos Media Services. Para criar um, veja [criar uma conta de Media Services do Azure com o portal do Azure](media-services-portal-create-account.md).
  * Inscreva-se com [programa de desenvolvimento da Apple](https://developer.apple.com/).
  * Apple exige o proprietário do conteúdo obter o [pacote de implementação](https://developer.apple.com/contact/fps/). Estado que já implementada módulo de segurança da chave (KSM) com os serviços de multimédia, e que está a solicitar o pacote FPS final. Existem instruções no pacote final FPS para gerar a certificação e obter a chave de segredo de aplicação (ASK). Utilize ASK para configurar o FairPlay.
  * Versão do SDK .NET dos Media Services do Azure **3.6.0** ou posterior.

Os seguintes procedimentos tem de ser definidos no lado de entrega de chave de serviços de multimédia:

  * **Certificado de aplicação (CA)**: Este é um ficheiro. pfx que contém a chave privada. Pode criar este ficheiro e criptografá-la com uma palavra-passe.

       Ao configurar uma política de entrega de chave, tem de fornecer essa palavra-passe e o ficheiro. pfx no formato Base64.

      Os passos seguintes descrevem como gerar um ficheiro de certificado. pfx para o FairPlay:

    1. Instalar o OpenSSL de https://slproweb.com/products/Win32OpenSSL.html.

        Aceda à pasta onde se o certificado de FairPlay e outros ficheiros fornecidos pela Apple.
    2. Execute o comando seguinte a partir da linha de comandos. Desta forma, o ficheiro. cer para um ficheiro. pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-informar der-no FairPlay.cer-out FairPlay out.pem
    3. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro. pem para um ficheiro. pfx com a chave privada. A palavra-passe para o ficheiro. pfx, em seguida, é pedida ao OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **Palavra-passe do certificado de aplicação**: A palavra-passe para criar o ficheiro. pfx.
  * **ID de palavra-passe do certificado de aplicação**: Tem de carregar a palavra-passe, semelhante a como eles carregam outras chaves de serviços de multimédia. Utilize o **ContentKeyType.FairPlayPfxPassword** valor de enumeração para obter o ID de serviços de suporte de dados Este é o que precisam de utilizar dentro da opção de política de entrega de chave.
  * **iv**: Este é um valor aleatório de 16 bytes. Tem de corresponder o iv na política de entrega de elementos. Gerar o iv e colocá-lo em ambos os locais: a política de entrega de elementos e a opção de política de entrega de chave.
  * **ASK**: Esta chave é recebida quando gera a certificação com o portal de programador da Apple. Cada equipe de desenvolvimento recebe um ASK exclusivo. Guardar uma cópia do ASK e armazene-o num local seguro. Tem de configurar ASK como FairPlayAsk para serviços de multimédia mais tarde.
  * **PEDIR ID**: Este ID é obtido ao carregar ASK para os serviços multimédia. Tem de carregar ASK utilizando o **ContentKeyType.FairPlayAsk** valor de enumeração. Como resultado, o ID de serviços de multimédia é devolvido e, este é o que deve ser utilizado ao definir a opção de política de entrega de chave.

Os seguintes procedimentos tem de ser definidos pelo lado do cliente FPS:

  * **Certificado de aplicação (CA)**: Este é um ficheiro de.cer/.der que contém a chave pública, o que o sistema operativo utiliza para criptografar alguns payload. Serviços de multimédia precisa saber sobre isso, porque é necessário pelo jogador. O serviço de entrega de chave descriptografa-lo com a chave privada correspondente.

Para reproduzir um fluxo encriptado de FairPlay, obtenha respostas fazem real primeiro e, em seguida, gerar um certificado real. Esse processo cria todas as três partes:

  * ficheiro. der
  * ficheiro. pfx
  * palavra-passe para o. pfx

Os seguintes clientes suportam HLS com **AES-128 CBC** encriptação: Safari no iOS da Apple TV, dos X.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Configurar o FairPlay dinâmicos licença e de encriptação de serviços de entrega
Seguem-se os passos gerais para proteger seus ativos com o FairPlay com o serviço de entrega de licença de serviços de multimédia e também ao utilizar a encriptação dinâmica.

1. Crie um elemento e carregue ficheiros para o mesmo.
2. Codifique o elemento que contém o ficheiro para o MP4 de velocidade de transmissão adaptável definido.
3. Crie uma chave de conteúdo e associe-a ao elemento codificado.  
4. Configure a política de autorização da chave de conteúdo. Especifique o seguinte:

   * O método de entrega (no caso, FairPlay).
   * Configuração de opções de política do FairPlay. Para obter detalhes sobre como configurar o FairPlay, consulte a **ConfigureFairPlayPolicyOptions()** método no exemplo abaixo.

     > [!NOTE]
     > Normalmente, iria querer configurar opções de política do FairPlay apenas uma vez, uma vez que apenas terá um conjunto de uma certificação e um ASK.
     >
     >
   * Restrições (abertas ou token).
   * Informações específicas para o tipo de entrega de chave que define a forma como a chave é entregue ao cliente.
5. Configure a política de entrega de elementos. A configuração de política de entrega inclui:

   * O protocolo de entrega (HLS).
   * O tipo de encriptação dinâmica (CBC a encriptação comum).
   * O URL de aquisição de licença.

     > [!NOTE]
     > Se quiser fornecer um fluxo que é encriptado com FairPlay e outro sistema de gestão de direitos digitais (DRM), tem de configurar políticas de entrega separado:
     >
     > * Um IAssetDeliveryPolicy para configurar o Dynamic Adaptive Streaming através de HTTP (DASH) com encriptação comum (CENC) (PlayReady + Widevine) e uniforme com PlayReady
     > * Outro IAssetDeliveryPolicy para configurar o FairPlay para HLS
     >
     >
6. Crie um localizador OnDemand para obter um URL de transmissão em fluxo.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Utilizar a entrega de chave do FairPlay por aplicações de leitor
Pode desenvolver aplicações de leitor, utilizando o SDK do iOS. Para ser capaz de reproduzir conteúdo de FairPlay, deve implementar o protocolo de troca de licença. Este protocolo não é especificado pela Apple. Cabe a cada aplicação como enviar pedidos de entrega de chave. O serviço de entrega de chave do FairPlay de serviços de multimédia espera o SPC por vir como uma mensagem de mensagem codificada www-form-url, o seguinte formato:

    spc=<Base64 encoded SPC>

> [!NOTE]
> O Azure Media Player suporta FairPlay reprodução. Ver [documentação de leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) para obter mais informações.
>
>

## <a name="streaming-urls"></a>URLs de transmissão em fluxo
Se o seu elemento foi criptografado com mais do que um DRM, deve utilizar uma etiqueta de encriptação no URL de transmissão em fluxo: (formato ' Format=m3u8-aapl ', encriptação = "xxx").

As seguintes considerações aplicam-se:

* Apenas zero ou um tipo de encriptação pode ser especificado.
* O tipo de encriptação não tem de ser especificado no URL, se apenas uma encriptação foi aplicada ao elemento.
* O tipo de encriptação diferencia maiúsculas de minúsculas.
* Os seguintes tipos de encriptação podem ser especificados:  
  * **cenc**:  Encriptação comum (PlayReady ou Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: Encriptação AES de envelope

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 
2. Adicione os elementos seguintes a **appSettings** definidos no ficheiro app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exemplo

O exemplo seguinte demonstra a capacidade de utilizar os serviços de multimédia para entregar o conteúdo encriptado com FairPlay. Esta funcionalidade foi introduzida no SDK de serviços de multimédia do Azure para .NET versão 3.6.0. 

Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

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
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
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

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

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

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
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

            // Create a 30-day readonly access policy.
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

## <a name="next-steps-media-services-learning-paths"></a>Passos seguintes: Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
