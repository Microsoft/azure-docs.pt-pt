---
title: Azure Media Services v3 frequentemente fez perguntas. Microsoft Docs
description: Este artigo dá respostas a perguntas frequentes sobre a Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: d34b5aaaa12a3d296f92e0d7be34ae76931d8506
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268490"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Serviços de Mídia v3 frequentemente perguntas

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo dá respostas a perguntas frequentes sobre a Azure Media Services v3.

## <a name="general"></a>Geral

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Quais são as limitações do portal Azure para os Serviços de Comunicação Social v3?

Você pode usar o [portal Azure](https://portal.azure.com/) para gerir eventos v3 ao vivo, ver ativos e empregos v3, obter informações sobre o acesso a APIs, encriptar conteúdo. <br/>Para todas as outras tarefas de gestão (por exemplo, gerir transformações e empregos ou analisar o conteúdo v3), utilize o [REST API,](https://aka.ms/ams-v3-rest-ref) [OCli,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs suportados.](media-services-apis-overview.md#sdks)

Se o seu vídeo foi previamente enviado para a conta dos Media Services utilizando os Serviços de Comunicação v3 API ou se o conteúdo foi gerado com base numa saída ao vivo, não verá os botões **Encode**, **Analyze**ou **Encrypt** no portal Azure. Utilize os Serviços de Mídia v3 APIs para executar estas tarefas.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Que funções da Azure podem executar ações sobre os recursos da Azure Media Services? 

Consulte [o controlo de acesso baseado em funções (RBAC) para contas de Serviços de Mídia](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Como faço o streaming para dispositivos Apple iOS?

Certifique-se de que tem **(formato=m3u8-aapl)** no final do seu caminho (após a **/parte manifesto** do URL) para dizer ao servidor de origem de streaming para devolver o conteúdo HTTP Live Streaming (HLS) para consumo em dispositivos nativos Apple iOS. Para mais informações, consulte [o conteúdo de Entrega](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Como posso configurar unidades reservadas para os meios de comunicação?

Para os trabalhos de Análise de Áudio e Análise de Vídeo que são desencadeados pelos Media Services v3 ou Por Video Indexer, recomendamos que abrimos a sua conta com 10 Unidades Reservadas para Os Meios de Comunicação Social (MRUs). Se precisar de mais de 10 MRUs S3, abra um bilhete de apoio utilizando o [portal Azure](https://portal.azure.com/).

Para mais informações, consulte [o processamento de mídia scale](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Utilizar [Transformações](/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada Transform descreve uma receita, ou um fluxo de trabalho de tarefas para processar os seus ficheiros de vídeo ou áudio. A [Job](/rest/api/media/jobs) é o pedido real aos Serviços de Comunicação social para aplicar a Transformação a um conteúdo de vídeo ou áudio de entrada. Após a criação da Transform, pode submeter Jobs utilizando APIs dos Serviços de Comunicação Social ou qualquer um dos SDKs publicados. Para obter mais informações, veja [Transforms and Jobs](transforms-jobs-concept.md) (Transformações e Trabalhos).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Fiz o upload, o codificado e publiquei um vídeo. Porque é que o vídeo não é reproduze quando tento transmiti-lo?

Uma das razões mais comuns é que não tens o ponto final de streaming a partir do qual estás a tentar reproduzir-te no estado de Corrida.

### <a name="how-does-pagination-work"></a>Como funciona a paginação?

Quando estiver a usar a paginação, deve sempre utilizar o próximo link para enumerar a coleção e não depender de um determinado tamanho da página. Para mais detalhes e exemplos, consulte [filtragem, pedido, paging](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais as funcionalidades que ainda não estão disponíveis no Azure Media Services v3?

Para mais informações, consulte [as lacunas de funcionalidades relativas às APIs v2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual é o processo de movimentação de uma conta de Serviços de Comunicação entre subscrições?  

Para mais informações, consulte [a conta De movimentação de serviços de mídia entre subscrições](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmissão em direto 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Como paro o transmissão em direto depois da transmissão ser feita?

Pode abordá-lo do lado do cliente ou do servidor.

#### <a name="client-side"></a>Lado do cliente

A sua aplicação web deve solicitar ao utilizador se quiser terminar a transmissão à medida que fecha o navegador. Este é um evento de navegador que a sua aplicação web pode lidar.

#### <a name="server-side"></a>Lado do servidor

Você pode monitorizar eventos ao vivo subscrevendo eventos da Azure Event Grid. Para mais informações, consulte o esquema do [evento EventGrid.](media-services-event-schemas.md#live-event-types)

Pode:

* [Subscreva](reacting-to-media-services-events.md) os eventos ligados ao nível do stream [microsoft.media.LiveEventEncoderDis](media-services-event-schemas.md#liveeventencoderdisconnected) e monitorize que não há reconexões durante algum tempo para parar e apagar o seu evento ao vivo.
* [Subscreva](reacting-to-media-services-events.md) os [eventos](media-services-event-schemas.md#liveeventingestheartbeat) cardíacos ao nível da pista. Se todas as faixas tiverem uma bitrate de entrada caindo para 0 ou a última marca de tempo não estiver mais aumentando, você pode desligar com segurança o evento ao vivo. Os eventos de batimentos cardíacos chegam a cada 20 segundos para cada pista, por isso pode ser um pouco verboso.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Como inseri quebras/vídeos e ardósias de imagens durante uma transmissão em direto?

A codificação ao vivo dos Media Services v3 ainda não suporta a inserção de imagens ou vídeo durante o live stream. 

Pode utilizar um [codificador ao vivo no local](recommended-on-premises-live-encoders.md) para mudar o vídeo de origem. Muitas aplicações fornecem a capacidade de mudar de fontes, incluindo Telestream Wirecast, Switcher Studio (no iOS) e OBS Studio (app gratuita).

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo utilizar a encriptação de chave clara AES-128 ou um sistema DRM?

Os clientes muitas vezes perguntam-se se devem usar encriptação AES ou um sistema DRM. A principal diferença entre os dois sistemas é que, com a encriptação AES, a chave de conteúdo é transmitida ao cliente através do TLS para que a chave seja encriptada em trânsito, mas sem qualquer encriptação adicional ("a claro"). Como resultado, a chave que é usada para desencriptar o conteúdo é acessível ao leitor cliente e pode ser visualizada num traço de rede no cliente em texto simples. A encriptação clara da chave AES-128 é adequada para o uso de casos em que o espectador é uma parte de confiança (por exemplo, encriptar vídeos corporativos distribuídos dentro de uma empresa para serem visualizados por funcionários).

Sistemas DRM como PlayReady, Widevine e FairPlay fornecem um nível adicional de encriptação na chave que é usada para desencriptar o conteúdo, em comparação com uma chave clara AES-128. A chave de conteúdo é encriptada para uma chave protegida pelo tempo de execução drm, além de qualquer encriptação de nível de transporte fornecida pelo TLS. Além disso, a desencriptação é tratada num ambiente seguro a nível do sistema operativo, onde é mais difícil para um utilizador malicioso atacar. Recomendamos drm para uso em casos em que o espectador pode não ser uma parte de confiança e você precisa do mais alto nível de segurança.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como posso mostrar um vídeo apenas a utilizadores que tenham uma permissão específica, sem usar a Azure AD?

Não tem de utilizar nenhum fornecedor específico de fichas, como o Azure Ative Directory (Azure AD). Pode criar o seu próprio fornecedor [JWT](https://jwt.io/) (o chamado Secure Token Service, ou STS) utilizando encriptação de chave assimétrica. No seu STS personalizado, pode adicionar reclamações com base na sua lógica de negócio.

Certifique-se de que o emitente, o público e as reivindicações correspondem exatamente entre o que está no JWT e o `ContentKeyPolicyRestriction` valor usado em `ContentKeyPolicy` .

Para obter mais informações, consulte [Protect your content utilizando encriptação dinâmica dos Media Services](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Como e onde consegui um token JWT antes de usá-lo para pedir uma licença ou chave?

Para a produção, você precisa ter Secure Token Service (isto é, um serviço web), que emite um token JWT após um pedido HTTPS. Para o teste, pode utilizar o código indicado no `GetTokenAsync` método definido em [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

O leitor faz um pedido, depois de um utilizador ser autenticado, para STS para tal token e atribui-o como o valor do token. Pode utilizar a API do [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

Para um exemplo de funcionamento de STS com uma chave simétrica ou uma chave assimétrica, consulte a [ferramenta JWT](https://aka.ms/jwt). Para um exemplo de um jogador baseado no Azure Media Player usando tal token JWT, consulte a [ferramenta de teste de mídia Azure](https://aka.ms/amtest). (Expandir a ligação **player_settings** para ver a entrada simbólica.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como autorizo pedidos para transmitir vídeos com encriptação AES?

A abordagem correta é utilizar o Serviço Secure Token. Em STS, dependendo do perfil do utilizador, adicione diferentes reivindicações (tais como "Utilizador Premium", "Utilizador Básico", "Utilizador de Teste Gratuito"). Com diferentes reivindicações num JWT, o utilizador pode ver diferentes conteúdos. Para diferentes conteúdos ou ativos, `ContentKeyPolicyRestriction` terá o `RequiredClaims` valor correspondente.

Utilize APIs de Serviços de Media Azure para configurar a licença/entrega de chaves e encriptar os seus ativos (como mostrado [nesta amostra).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Para obter mais informações, consulte:

- [Visão geral da proteção de conteúdos](content-protection-overview.md)
- [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Devo utilizar HTTP ou HTTPS?
A aplicação do leitor de MVC ASP.NET deve suportar o seguinte:

* Autenticação do utilizador através do Azure AD, que se encontra em HTTPS.
* Troca de JWT entre o cliente e a Azure AD, que está em HTTPS.
* Aquisição de licença drm pelo cliente, que deve estar sob HTTPS se a entrega da licença for fornecida pela Media Services. A suíte de produto PlayReady não manda HTTPS para entrega de licença. Se o seu servidor de licença PlayReady estiver fora dos Serviços de Mídia, pode utilizar HTTP ou HTTPS.

A aplicação ASP.NET do jogador utiliza HTTPS como uma boa prática, pelo que o Media Player está numa página em HTTPS. No entanto, HTTP é preferido para streaming, por isso deve considerar estas questões com conteúdo misto:

* O navegador não permite conteúdo misto. Mas plug-ins como Silverlight e o plug-in OSMF para Smooth e DASH permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça da capacidade de injetar JavaScript malicioso, o que pode colocar os dados dos clientes em risco. Os navegadores bloqueiam esta capacidade por padrão. A única forma de contornar isto é no lado do servidor (origem) permitindo que todos os domínios (independentemente de HTTPS ou HTTP). Isto também provavelmente não é uma boa ideia.
* Evite o conteúdo misto. Tanto a aplicação do jogador como o Media Player devem utilizar HTTP ou HTTPS. Quando estás a jogar conteúdo misto, a tecnologia SilverlightSS requer um aviso de conteúdo misto. A tecnologia FlashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o seu ponto final de streaming foi criado antes de agosto de 2014, não apoiará HTTPS. Neste caso, crie e utilize um novo ponto final de streaming para HTTPS.

### <a name="what-about-live-streaming"></a>E o streaming ao vivo?

Você pode usar exatamente o mesmo design e implementação para ajudar a proteger o streaming ao vivo em Media Services, tratando o ativo associado a um programa como um ativo VOD. Para fornecer uma proteção multi-DRM do conteúdo ao vivo, aplique a mesma configuração/processamento ao ativo como se fosse um ativo VOD antes de associar o ativo à saída ao vivo.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licenças fora dos Serviços de Media?

Muitas vezes, os clientes têm investido numa fazenda de servidores de licenças, quer no seu próprio datacenter, quer num hospedado por prestadores de serviços DRM. Com a proteção de conteúdos dos Media Services, pode operar em modo híbrido. Os conteúdos podem ser hospedados e dinamicamente protegidos nos Serviços de Mídia, enquanto as licenças de DRM são entregues por servidores fora dos Serviços de Media. Neste caso, considere as seguintes alterações:

* O STS precisa de emitir fichas que sejam aceitáveis e que possam ser verificadas pela fazenda do servidor de licenças. Por exemplo, os servidores de licença widevine fornecidos pela Axinom requerem um JWT específico que contém uma mensagem de direito. Precisa de uma STS para emitir tal JWT. 
* Já não precisa de configurar o serviço de entrega de licenças nos Serviços de Comunicação Social. Tem de fornecer urLs de aquisição de licença (para PlayReady, Widevine e FairPlay) quando configurar `ContentKeyPolicy` .

> [!NOTE]
> Widevine é um serviço prestado pela Google e sujeito aos termos de serviço e política de privacidade da Google.

## <a name="media-services-v2-vs-v3"></a>Serviços de Mídia v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Posso usar o portal Azure para gerir os recursos v3?

Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

* Gerir [Eventos Ao Vivo](live-events-outputs-concept.md) nos Serviços de Media v3. 
* Ver (não gerir) [ativos](assets-concept.md)v3 . 
* [Obtenha informações sobre o acesso às APIs.](./access-api-howto.md) 

Para todas as outras tarefas de gestão (por exemplo, [Transformações e Empregos](transforms-jobs-concept.md) e [proteção de conteúdos),](content-protection-overview.md)utilize a [API REST](/rest/api/media/), o [Azure CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs suportados.](media-services-apis-overview.md#sdks)

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito AssetFile em v3?

O `AssetFile` conceito foi removido da API dos Serviços de Mídia para separar os Serviços de Mídia da dependência da SDK de armazenamento. Agora, a Azure Storage, não a Media Services, mantém a informação que pertence ao SDK de armazenamento. 

Para mais informações, consulte [Migrate to Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Para onde foi a encriptação do armazenamento do lado do cliente?

Recomendamos agora que utilize encriptação de armazenamento do lado do servidor (que está ligado por padrão). Para obter mais informações, consulte [a Encriptação do Serviço de Armazenamento Azure para obter dados em repouso](../../storage/common/storage-service-encryption.md).

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming para iOS

As seguintes perguntas frequentes fornecem assistência com resolução de problemas offline FairPlay streaming para iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Porque é que só o áudio reproduz, mas não o vídeo durante o modo offline?

Este comportamento parece ser por design da aplicação da amostra. Quando existe uma faixa de áudio alternativa (o que é o caso do HLS) durante o modo offline, tanto o iOS 10 como o iOS 11 estão por defeito na faixa de áudio alternativa. Para compensar este comportamento para o modo offline FPS, remova a faixa de áudio alternativa do fluxo. Para isso nos Serviços de Media, adicione o filtro dinâmico manifesto **apenas =falso**. Por outras palavras, um URL HLS termina com **.ism/manifest (formato=m3u8-aapl,audio-only=falso)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Porque é que ainda reproduz áudio apenas sem vídeo durante o modo offline depois de eu adicionar áudio-only=falso?

Dependendo do design da chave de cache para a rede de entrega de conteúdos, o conteúdo pode ser em cache. Purgue a cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>O modo offline FPS é suportado no iOS 11 para além do iOS 10?

Sim. O modo offline FPS é suportado para iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Por que não posso encontrar o documento "Offline Playback with FairPlay Streaming e HTTP Live Streaming" no FPS Server SDK?

Desde a versão 4 do FPS Server SDK, este documento foi fundido no "Guia de Programação de Streaming fairPlay".

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>O que é a estrutura de ficheiros descarregado/offline em dispositivos iOS?

A estrutura de ficheiros descarregada num dispositivo iOS parece a seguinte imagem. As `_keys` lojas de pasta descarregaram licenças de FPS, com um ficheiro de loja para cada anfitrião do serviço de licença. A `.movpkg` pasta armazena conteúdo sonoro e de vídeo. 

A primeira pasta com um nome que termina com um traço seguido de um número contém conteúdo de vídeo. O valor numérico é o pico da largura de banda das representações em vídeo. A segunda pasta com um nome que termina com um traço seguido de 0 contém conteúdo sonoro. A terceira pasta nomeada `Data` contém a lista principal do conteúdo FPS. Finalmente, boot.xml fornece uma descrição completa do conteúdo da `.movpkg` pasta. 

![Estrutura de ficheiros offline para a aplicação de amostras fairPlay iOS](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Aqui está uma amostra boot.xml ficheiro:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Streaming widevine para Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Como posso entregar licenças persistentes (ativadas offline) para alguns clientes/utilizadores e licenças não persistentes (offline desativadas) para outros? Tenho de duplicar o conteúdo e utilizar chaves de conteúdo separadas?

Como o Media Services v3 permite que um ativo tenha `StreamingLocator` múltiplas instâncias, pode ter:

* Um `ContentKeyPolicy` caso `license_type = "persistent"` com, com `ContentKeyPolicyRestriction` reivindicação sobre , e a sua `"persistent"` `StreamingLocator` .
* Outro `ContentKeyPolicy` caso `license_type="nonpersistent"` com, com `ContentKeyPolicyRestriction` reivindicação sobre ", e a sua `"nonpersistent` `StreamingLocator` .
* Dois `StreamingLocator` casos que têm `ContentKey` valores diferentes.

Dependendo da lógica comercial da STS personalizada, são emitidas diferentes reclamações no token JWT. Com o token, apenas a licença correspondente pode ser obtida e apenas o URL correspondente pode ser reproduzido.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual é o mapeamento entre os níveis de segurança da Widevine e dos Media Services DRM?

A "Visão Geral da Arquitetura DRM Widevine" da Google define três níveis de segurança. No entanto, a documentação do [Azure Media Services sobre o modelo de licença Widevine](widevine-license-template-overview.md) descreve cinco níveis de segurança (requisitos de robustez do cliente para reprodução). Esta secção explica como os níveis de segurança mapeiam.

Ambos os conjuntos de níveis de segurança são definidos pelo Google Widevine. A diferença está no nível de utilização: arquitetura ou API. Os cinco níveis de segurança são usados na API widevine. O `content_key_specs` objeto, que `security_level` contém, é deseralizado e passado para o serviço de entrega global Widevine pelo serviço de licenças Azure Media Services Widevine. A tabela a seguir mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura widevine** |**Níveis de segurança utilizados na API de Widevine**|
|---|---| 
| **Nível de Segurança 1**: Todos os conteúdos processados, criptografia e controlo são realizados dentro do Ambiente de Execução Fidedigna (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em diferentes chips.|**security_level=5**: A cripto, descodante e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados dentro de um TEE apoiado por hardware.<br/><br/>**security_level=4**: A cripto e a descodição dos conteúdos devem ser efetuadas dentro de um TEE apoiado por hardware.|
**Nível de Segurança 2**: A criptografia (mas não o processamento de vídeo) é realizada dentro do TEE. Os amortecedores desencriptados são devolvidos ao domínio da aplicação e processados através de hardware ou software de vídeo separados. No nível 2, no entanto, a informação criptográfica ainda é processada apenas dentro do TEE.| **security_level=3**: O material chave e as operações de cripto devem ser efetuadas dentro de um TEE apoiado por hardware. |
| **Nível de Segurança 3**: Não há TEE no dispositivo. Podem ser tomadas medidas adequadas para proteger as informações criptográficas e o conteúdo desencriptado no sistema operativo hospedeiro. Uma implementação de Nível 3 também pode incluir um motor criptográfico de hardware, mas que melhora apenas o desempenho, não a segurança. | **security_level=2**: São necessários criptos de software e um descodificador obfuscado.<br/><br/>**security_level=1**: É necessário cripto de caixa branca baseado em software.|

#### <a name="why-does-content-download-take-so-long"></a>Porque é que o download de conteúdos demora tanto tempo?

Existem duas formas de melhorar a velocidade de descarregamento:

* Ativar uma rede de entrega de conteúdos para que os utilizadores sejam mais propensos a atingir isso em vez do ponto final de origem/streaming para download de conteúdos. Se um utilizador atingir um ponto final de streaming, cada segmento HLS ou fragmento de DASH é dinamicamente embalado e encriptado. Mesmo que esta latência esteja em escala milissegundo para cada segmento ou fragmento, quando você tem um vídeo de uma hora, a latência acumulada pode ser grande e causar um download mais longo.
* Dê aos utilizadores a opção de descarregar seletivamente camadas de qualidade de vídeo e faixas de áudio em vez de todos os conteúdos. Para o modo offline, não faz sentido descarregar todas as camadas de qualidade. Há duas maneiras de o conseguir:

  * Controlado pelo cliente: A aplicação do jogador seleciona automaticamente, ou o utilizador seleciona, a camada de qualidade de vídeo e as faixas de áudio para descarregar.
  * Controlado por serviço: Pode utilizar a função Dynamic Manifest nos Serviços Azure Media para criar um filtro (global), que limita a lista de reprodução HLS ou o MPD do DASH a uma única camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, o URL de descarregamento apresentado aos utilizadores incluirá este filtro.

## <a name="next-steps"></a>Passos seguintes

[Visão geral dos Serviços de Comunicação Social v3](media-services-overview.md)
