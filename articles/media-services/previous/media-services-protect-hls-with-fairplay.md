---
title: Proteger o conteúdo do HLS com o Microsoft PlayReady ou o Apple FairPlay – Azure | Microsoft Docs
description: Este tópico fornece uma visão geral e mostra como usar os serviços de mídia do Azure para criptografar dinamicamente seu conteúdo de HTTP Live Streaming (HLS) com o Apple FairPlay. Ele também mostra como usar o serviço de entrega de licença dos serviços de mídia para fornecer licenças do FairPlay para os clientes.
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
ms.openlocfilehash: 873bc4ab5e435b91ff4400a39c92db0d0bb9baa8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74968770"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Proteja seu conteúdo do HLS com o Apple FairPlay ou o Microsoft PlayReady

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).   > Não há novos recursos ou funcionalidades sendo adicionados aos serviços de mídia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)
>

Os serviços de mídia do Azure permitem criptografar dinamicamente seu conteúdo de HTTP Live Streaming (HLS) usando os seguintes formatos:  

* **Chave de limpeza de envelope AES-128**

    A parte inteira é criptografada usando o modo de **CBC AES-128** . A descriptografia do fluxo tem suporte do iOS e do OS X Player nativamente. Para obter mais informações, consulte [usando o serviço de distribuição de chaves e criptografia dinâmica AES-128](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Os exemplos de áudio e vídeo individuais são criptografados usando o modo de **CBC AES-128** . **FairPlay Streaming** (FPS) está integrado nos sistemas operativos dos dispositivos, com suporte nativo em dispositivos iOS e Apple TV. Safari nos X permite FPS com o suporte de interface Encrypted Media Extensions (EME).
* **Microsoft PlayReady**

A imagem a seguir mostra o fluxo de trabalho de **criptografia dinâmica HLS + Fairplay ou PlayReady** .

![Diagrama de fluxo de trabalho de criptografia dinâmica](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Este artigo demonstra como usar os serviços de mídia para criptografar dinamicamente o conteúdo do HLS com o Apple FairPlay. Ele também mostra como usar o serviço de entrega de licença dos serviços de mídia para fornecer licenças do FairPlay para os clientes.

> [!NOTE]
> Se você também quiser criptografar o conteúdo do HLS com o PlayReady, precisará criar uma chave de conteúdo comum e associá-la ao seu ativo. Você também precisa configurar a política de autorização da chave de conteúdo, conforme descrito em [usando a criptografia comum dinâmica do PlayReady](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Requisitos e considerações

Os itens a seguir são necessários ao usar os serviços de mídia para fornecer HLS criptografados com FairPlay e para entregar licenças FairPlay:

  * Uma conta do Azure. Para obter mais detalhes, veja [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Uma conta dos Media Services. Para criar um, consulte [criar uma conta dos serviços de mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).
  * Inscreva-se com [programa de desenvolvimento da Apple](https://developer.apple.com/).
  * Apple exige o proprietário do conteúdo obter o [pacote de implementação](https://developer.apple.com/contact/fps/). Estado que já implementada módulo de segurança da chave (KSM) com os serviços de multimédia, e que está a solicitar o pacote FPS final. Existem instruções no pacote final FPS para gerar a certificação e obter a chave de segredo de aplicação (ASK). Utilize ASK para configurar o FairPlay.
  * SDK do .NET dos serviços de mídia do Azure versão **3.6.0** ou posterior.

Os itens a seguir devem ser definidos no lado de distribuição de chaves dos serviços de mídia:

  * **Certificado da aplicação (CA)** : Este é um ficheiro. pfx que contém a chave privada. Pode criar este ficheiro e criptografá-la com uma palavra-passe.

       Ao configurar uma política de distribuição de chaves, você deve fornecer essa senha e o arquivo. pfx no formato base64.

      Os passos seguintes descrevem como gerar um ficheiro de certificado. pfx para o FairPlay:

    1. Instalar o OpenSSL de https://slproweb.com/products/Win32OpenSSL.html.

        Aceda à pasta onde se o certificado de FairPlay e outros ficheiros fornecidos pela Apple.
    2. Execute o comando seguinte a partir da linha de comandos. Desta forma, o ficheiro. cer para um ficheiro. pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509-informar der-no FairPlay.cer-out FairPlay out.pem
    3. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro. pem para um ficheiro. pfx com a chave privada. A palavra-passe para o ficheiro. pfx, em seguida, é pedida ao OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **Palavra-passe do certificado de aplicação**: A palavra-passe para criar o ficheiro. pfx.
  * **ID da senha do certificado do aplicativo**: você deve carregar a senha, semelhante a como elas carregam outras chaves de serviços de mídia. Use o valor de enumeração **ContentKeyType. FairPlayPfxPassword** para obter a ID dos serviços de mídia. Isso é o que eles precisam usar dentro da opção de política de distribuição de chaves.
  * **IV**: esse é um valor aleatório de 16 bytes. Ele deve corresponder ao IV na política de entrega de ativos. Você gera o IV e o coloca em ambos os locais: a política de entrega de ativos e a opção de política de distribuição de chaves.
  * **PEDIR**: esta chave é recebida quando gera a certificação com o portal de programador da Apple. Cada equipe de desenvolvimento recebe um ASK exclusivo. Guardar uma cópia do ASK e armazene-o num local seguro. Você precisa configurar o ASK como Fairplayask nos para os serviços de mídia mais tarde.
  * **Ask ID**: essa ID é obtida quando você carrega o Ask nos serviços de mídia. Você deve carregar ASK usando o valor de enumeração **ContentKeyType. fairplayask nos** . Como resultado, a ID dos serviços de mídia é retornada e isso é o que deve ser usado ao definir a opção de política de distribuição de chave.

Os seguintes procedimentos tem de ser definidos pelo lado do cliente FPS:

  * **Certificado da aplicação (CA)** : Este é um ficheiro de.cer/.der que contém a chave pública, o que o sistema operativo utiliza para criptografar alguns payload. Serviços de multimédia precisa saber sobre isso, porque é necessário pelo jogador. O serviço de entrega de chave descriptografa-lo com a chave privada correspondente.

Para reproduzir um fluxo encriptado de FairPlay, obtenha respostas fazem real primeiro e, em seguida, gerar um certificado real. Esse processo cria todas as três partes:

  * ficheiro. der
  * ficheiro. pfx
  * palavra-passe para o. pfx

Os seguintes clientes dão suporte a HLS com criptografia **AES-128 CBC** : Safari no os X, Apple TV, Ios.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Configurar a criptografia dinâmica FairPlay e os serviços de entrega de licença
Veja a seguir as etapas gerais para proteger seus ativos com o FairPlay usando o serviço de distribuição de licenças dos serviços de mídia e também usando a criptografia dinâmica.

1. Crie um elemento e carregue ficheiros para o mesmo.
2. Codifique o elemento que contém o ficheiro para o MP4 de velocidade de transmissão adaptável definido.
3. Crie uma chave de conteúdo e associe-a ao elemento codificado.  
4. Configure a política de autorização da chave de conteúdo. Especifique o seguinte:

   * O método de entrega (nesse caso, FairPlay).
   * Configuração de opções de política FairPlay. Para obter detalhes sobre como configurar o FairPlay, consulte o método **ConfigureFairPlayPolicyOptions ()** no exemplo a seguir.

     > [!NOTE]
     > Normalmente, iria querer configurar opções de política do FairPlay apenas uma vez, uma vez que apenas terá um conjunto de uma certificação e um ASK.
     >
     >
   * Restrições (abertas ou token).
   * Informações específicas para o tipo de entrega de chave que define como a chave é entregue ao cliente.
5. Configure a política de entrega de ativos. A configuração da política de entrega inclui:

   * O protocolo de entrega (HLS).
   * O tipo de criptografia dinâmica (criptografia de CBC comum).
   * A URL de aquisição de licença.

     > [!NOTE]
     > Se você quiser entregar um fluxo criptografado com FairPlay e outro sistema de Rights Management digital (DRM), precisará configurar políticas de entrega separadas:
     >
     > * Um IAssetDeliveryPolicy para configurar o streaming adaptável dinâmico sobre HTTP (DASH) com Criptografia Comum (CENC) (PlayReady + Widevine) e Smooth com PlayReady
     > * Outro IAssetDeliveryPolicy para configurar o FairPlay para HLS
     >
     >
6. Crie um localizador OnDemand para obter um URL de transmissão em fluxo.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Usar a distribuição de chave FairPlay por aplicativos de Player
Você pode desenvolver aplicativos Player usando o SDK do iOS. Para ser capaz de reproduzir conteúdo de FairPlay, deve implementar o protocolo de troca de licença. Este protocolo não é especificado pela Apple. Cabe a cada aplicação como enviar pedidos de entrega de chave. O serviço de entrega de chave do FairPlay de serviços de multimédia espera o SPC por vir como uma mensagem de mensagem codificada www-form-url, o seguinte formato:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Player de Mídia do Azure dá suporte à reprodução de FairPlay. Consulte a [documentação do player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) para obter mais informações.
>
>

## <a name="streaming-urls"></a>URLs de streaming
Se o ativo foi criptografado com mais de um DRM, você deve usar uma marca de criptografia na URL de streaming: (Format = M3U8-AAPL ', Encryption = ' xxx ').

As seguintes considerações são aplicáveis:

* Somente zero ou um tipo de criptografia pode ser especificado.
* O tipo de criptografia não precisa ser especificado na URL se apenas uma criptografia foi aplicada ao ativo.
* O tipo de criptografia não diferencia maiúsculas de minúsculas.
* Os seguintes tipos de criptografia podem ser especificados:  
  * **Cenc**: criptografia comum (PlayReady ou Widevine)
  * **CBCs-AAPL**: Fairplay
  * **CBC**: criptografia de envelope AES

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 
2. Adicione os elementos seguintes a **appSettings** definidos no ficheiro app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exemplo

O exemplo a seguir demonstra a capacidade de usar os serviços de mídia para entregar seu conteúdo criptografado com o FairPlay. Essa funcionalidade foi introduzida no SDK dos serviços de mídia do Azure para .NET versão 3.6.0. 

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

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
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

## <a name="additional-notes"></a>Notas adicionais

* O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="next-steps-media-services-learning-paths"></a>Passos seguintes: percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
