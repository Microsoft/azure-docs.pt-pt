---
title: Proteja os conteúdos HLS com o Microsoft PlayReady ou Apple FairPlay - Azure / Microsoft Docs
description: Este tópico dá uma visão geral e mostra como usar o Azure Media Services para encriptar dinamicamente o seu conteúdo HTTP Live Streaming (HLS) com o Apple FairPlay. Também mostra como usar o serviço de entrega de licenças de Media Services para entregar licenças FairPlay aos clientes.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f9b3cdd0b2080a26a9b1948263a7638dc66f2b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259749"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Proteja o seu conteúdo HLS com Apple FairPlay ou Microsoft PlayReady

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).   > Não estão a ser adicionadas novas funcionalidades ou funcionalidades aos Serviços de Comunicação Social v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)
>

O Azure Media Services permite-lhe encriptar dinamicamente o seu conteúdo HTTP Live Streaming (HLS) utilizando os seguintes formatos:  

* **Chave clara do envelope AES-128**

    Todo o pedaço é encriptado utilizando o modo **CBC AES-128.** A desencriptação do fluxo é suportada pelo leitor de iOS e OS X de forma nativa. Para obter mais informações, consulte [o serviço de encriptação dinâmica AES-128 e o serviço de entrega de chaves](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    As amostras individuais de vídeo e áudio são encriptadas utilizando o modo **CBC AES-128.** **O FairPlay Streaming** (FPS) está integrado nos sistemas operativos do dispositivo, com suporte nativo no iOS e Apple TV. O Safari no OS X permite fps utilizando o suporte de interface de extensões de mídia encriptadas (EME).
* **Microsoft PlayReady**

A imagem a seguir mostra o fluxo de trabalho **de encriptação dinâmica HLS + FairPlay ou PlayReady.**

![Diagrama de fluxo de trabalho de encriptação dinâmica](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Este artigo demonstra como usar os Media Services para encriptar dinamicamente o seu conteúdo HLS com o Apple FairPlay. Também mostra como usar o serviço de entrega de licenças de Media Services para entregar licenças FairPlay aos clientes.

> [!NOTE]
> Se também pretende encriptar o seu conteúdo HLS com o PlayReady, tem de criar uma chave de conteúdo comum e associá-la ao seu ativo. Também é necessário configurar a política de autorização da chave de conteúdo, conforme descrito na utilização da [encriptação comum dinâmica Do PlayReady](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Requisitos e considerações

São necessários os seguintes quando se utilizam os Serviços de Comunicação social para entregar o HLS encriptado com FairPlay e para entregar licenças FairPlay:

  * Uma conta do Azure. Para mais detalhes, consulte [o julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)
  * Uma conta dos Media Services. Para criar um, consulte [criar uma conta Azure Media Services utilizando o portal Azure](media-services-portal-create-account.md).
  * Inscreva-se no [Apple Development Program](https://developer.apple.com/).
  * A Apple exige que o proprietário do conteúdo obtenha o [pacote de implementação.](https://developer.apple.com/contact/fps/) Diga que já implementou o Módulo de Segurança Chave (KSM) com os Media Services e que está a solicitar o pacote final de FPS. Existem instruções no pacote final de FPS para gerar certificação e obter a Chave Secreta de Aplicação (ASK). Usa o ASK para configurar o FairPlay.
  * Azure Media Services .NET SDK versão **3.6.0** ou posterior.

As seguintes coisas devem ser definidas no lado da entrega da chave dos serviços de comunicação social:

  * **App Cert (AC)**: Este é um ficheiro .pfx que contém a chave privada. Cria este ficheiro e encripta-o com uma palavra-passe.

       Ao configurar uma política de entrega de chaves, deve fornecer essa palavra-passe e o ficheiro .pfx no formato Base64.

      Os seguintes passos descrevem como gerar um ficheiro de certificado .pfx para FairPlay:

    1. Instale o OpenSSL a partir de https://slproweb.com/products/Win32OpenSSL.html .

        Vá à pasta onde estão o certificado FairPlay e outros ficheiros entregues pela Apple.
    2. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro .cer num ficheiro .pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -informe der -in FairPlay.cer -out FairPlay-out.pem
    3. Execute o comando seguinte a partir da linha de comandos. Isto converte o ficheiro .pem para um ficheiro .pfx com a chave privada. A palavra-passe para o ficheiro .pfx é então solicitada pela OpenSSL.

        Pkcs12 "C:\OpenSSL-Win32\bin\openssl.exe" -exportação -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **Aplicação Cert password**: A palavra-passe para criar o ficheiro .pfx.
  * **ID de senha de aplicação Cert**: Tem de carregar a palavra-passe, semelhante à forma como carregam outras teclas de Serviços de Comunicação Social. Utilize o valor enum **do ContentKeyType.FairPlayPfxPassword** para obter o ID dos Serviços de Comunicação. Isto é o que eles precisam de usar dentro da opção de política de entrega chave.
  * **iv**: Trata-se de um valor aleatório de 16 bytes. Deve coincidir com o iv na política de entrega de ativos. Gera-se o iv e coloca-se em ambos os lugares: a política de entrega de ativos e a opção de política de entrega chave.
  * **ASK:** Esta chave é recebida quando gera a certificação utilizando o portal Apple Developer. Cada equipa de desenvolvimento recebe um ASK único. Guarde uma cópia do ASK e guarde-a num local seguro. Mais tarde, é necessário configurar o ASK como FairPlayAsk para os Media Services.
  * **ID ASK**: Este ID é obtido quando envia ASK para os Serviços de Mídia. Tem de fazer o upload ask utilizando o valor enum **contentKeyType.FairPlayAsk.** Como resultado, o ID dos Serviços de Comunicação é devolvido, e é isso que deve ser usado ao definir a opção de política de entrega chave.

As seguintes coisas devem ser definidas pelo lado do cliente FPS:

  * **App Cert (AC)**: Trata-se de um ficheiro .cer/.der que contém a chave pública, que o sistema operativo utiliza para encriptar alguma carga útil. Os Serviços de Comunicação Social precisam de saber porque é exigido pelo jogador. O serviço de entrega de chaves desencripta-o utilizando a chave privada correspondente.

Para reproduzir um stream encriptado FairPlay, obtenha um ASK real primeiro e, em seguida, gere um certificado real. Este processo cria as três partes:

  * .der arquivo
  * Arquivo .pfx
  * palavra-passe para o .pfx

Os seguintes clientes suportam HLS com encriptação **CBC AES-128:** Safari em OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Configure Serviços de encriptação dinâmica FairPlay e entrega de licenças
Seguem-se os passos gerais para proteger os seus ativos com o FairPlay, utilizando o serviço de entrega de licenças media Services, e também utilizando encriptação dinâmica.

1. Crie um elemento e carregue ficheiros para o mesmo.
2. Codifique o elemento que contém o ficheiro para o MP4 de velocidade de transmissão adaptável definido.
3. Crie uma chave de conteúdo e associe-a ao elemento codificado.  
4. Configure a política de autorização da chave de conteúdo. Especifique o seguinte:

   * O método de entrega (neste caso, FairPlay).
   * Configuração de opções de política do FairPlay. Para obter mais informações sobre como configurar o FairPlay, consulte o método **ConfigureFairPlayPolicyOptions na** amostra abaixo.

     > [!NOTE]
     > Normalmente, gostaria de configurar as opções de política do FairPlay apenas uma vez, porque só terá um conjunto de certificação e um ASK.
     >
     >
   * Restrições (abertas ou simbólicas).
   * Informação específica do tipo de entrega chave que define como a chave é entregue ao cliente.
5. Configure a política de entrega de ativos. A configuração da política de entrega inclui:

   * O protocolo de entrega (HLS).
   * O tipo de encriptação dinâmica (encriptação cbc comum).
   * A URL de aquisição de licença.

     > [!NOTE]
     > Se quiser entregar um stream que esteja encriptado com o FairPlay e outro sistema de Gestão de Direitos Digitais (DRM), tem de configurar políticas de entrega separadas:
     >
     > * One IAssetDeliveryPolicy para configurar o Streaming Adaptativo Dinâmico em HTTP (DASH) com Encriptação Comum (CENC) (PlayReady + Widevine) e Smooth com PlayReady
     > * Outra IAssetDeliveryPolicy para configurar FairPlay para o HLS
     >
     >
6. Crie um localizador OnDemand para obter um URL de transmissão em fluxo.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Use a entrega da chave FairPlay por apps do jogador
Pode desenvolver aplicações para jogadores utilizando o iOS SDK. Para poder jogar o conteúdo do FairPlay, tem de implementar o protocolo de troca de licenças. Este protocolo não é especificado pela Apple. Cabe a cada app enviar pedidos de entrega chave. O serviço de entrega chave de media Serviços FairPlay espera que o SPC venha como uma mensagem postal codificada www-form-url, na seguinte forma:

`spc=<Base64 encoded SPC>`

> [!NOTE]
> Azure Media Player suporta a reprodução do FairPlay. Consulte [a documentação do Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) para mais informações.
>
>

## <a name="streaming-urls"></a>Streaming URLs
Se o seu ativo foi encriptado com mais de um DRM, deve utilizar uma etiqueta de encriptação no URL de streaming: (formato='m3u8-aapl', encriptação='xxx').

As seguintes considerações são aplicáveis:

* Apenas um tipo de encriptação zero ou um pode ser especificado.
* O tipo de encriptação não tem de ser especificado no URL se apenas uma encriptação foi aplicada ao ativo.
* O tipo de encriptação é insensível.
* Podem ser especificados os seguintes tipos de encriptação:  
  * **cenc**: Encriptação comum (PlayReady ou Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: Encriptação do envelope AES

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

1. Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 
2. Adicione os elementos seguintes a **appSettings** definidos no ficheiro app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exemplo

A amostra que se segue demonstra a capacidade de utilizar os Media Services para entregar o seu conteúdo encriptado com FairPlay. Esta funcionalidade foi introduzida no Azure Media Services SDK para a versão 3.NET 3.6.0. 

Substitua o código no seu ficheiro Program.cs com o código mostrado nesta secção.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Deve utilizar o mesmo ID de política se estiver a utilizar sempre os mesmas permissões de dias/acesso, por exemplo, políticas para localizadores que pretendam permanecem no local durante muito tempo (políticas de não carregamento). Para mais informações, consulte [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

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

* Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="next-steps-media-services-learning-paths"></a>Passos seguintes: percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
