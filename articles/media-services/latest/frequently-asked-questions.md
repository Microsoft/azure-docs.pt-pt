---
title: Azure Media Services v3 frequentemente fez perguntas Microsoft Docs
description: Este artigo dá respostas a perguntas frequentes sobre a Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309195"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Serviços de Media v3 frequentemente questionados

Este artigo dá respostas a perguntas frequentes sobre a Azure Media Services v3.

## <a name="general"></a>Geral

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Que funções o Azure pode desempenhar ações sobre os recursos da Azure Media Services? 

Consulte [o controlo de acesso baseado em funções (RBAC) para as contas dos Serviços de Media](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Como faço streaming para dispositivos apple iOS?

Certifique-se de que tem **(formato=m3u8-aapl)** no final do seu percurso (após a parte **/manifesto** do URL) para dizer ao servidor de origem streaming para devolver conteúdo http Live Streaming (HLS) para consumo em dispositivos nativos apple iOS. Para mais detalhes, consulte [a Entrega de conteúdos](dynamic-packaging-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Como configurar unidades reservadas aos media?

Para os trabalhos de Análise de Áudio e Análise de Vídeo que são desencadeados por Media Services v3 ou Video Indexer, recomendamos que disponibilize a sua conta com 10 Unidades Reservadas de Meios de Comunicação Social (MRUs). Se precisar de mais de 10 MrUs S3, abra um bilhete de apoio utilizando o [portal Azure](https://portal.azure.com/).

Para mais detalhes, consulte [o processamento de meios de escala](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Qual é o método recomendado para processar vídeos?

Utilize [transformes](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada Transform descreve uma receita, ou um fluxo de trabalho de tarefas para processar os seus ficheiros de vídeo ou áudio. A [Job](https://docs.microsoft.com/rest/api/media/jobs) é o pedido real aos Serviços de Media para aplicar a Transform a um conteúdo de vídeo ou áudio de entrada. Após a criação da Transform, pode submeter Jobs utilizando APIs de Serviços de Media ou qualquer um dos SDKs publicados. Para obter mais informações, veja [Transforms and Jobs](transforms-jobs-concept.md) (Transformações e Trabalhos).

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Carreguei, codificado e publiquei um vídeo. Porque é que o vídeo não toca quando tento transmiti-lo?

Uma das razões mais comuns é que não tens o ponto final de streaming do qual estás a tentar jogar no estado de Running.

### <a name="how-does-pagination-work"></a>Como funciona a paginação?

Quando estiver a usar paginação, deve sempre utilizar o próximo link para enumerar a coleção e não depender de um determinado tamanho da página. Para mais detalhes e exemplos, consulte [Filtragem, encomenda, paging](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Quais as funcionalidades que ainda não estão disponíveis no Azure Media Services v3?

Para mais detalhes, consulte [As lacunas de funcionalidade em relação às APIs v2](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Qual é o processo de movimentação de uma conta de Media Services entre subscrições?  

Para mais detalhes, consulte [Mover uma conta de Media Services entre subscrições](media-services-account-concept.md).

## <a name="live-streaming"></a>Transmissão em direto 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Como paro a transmissão ao vivo depois da emissão estar feita?

Pode abordá-lo do lado do cliente ou do lado do servidor.

#### <a name="client-side"></a>Lado do cliente

A sua aplicação web deve solicitar ao utilizador se pretende terminar a transmissão à medida que está a fechar o navegador. Este é um evento de navegador que a sua aplicação web pode lidar.

#### <a name="server-side"></a>Lado do servidor

Você pode monitorizar eventos ao vivo subscrevendo eventos da Azure Event Grid. Para mais informações, consulte o [eventGrid schema](media-services-event-schemas.md#live-event-types).

Pode:

* [Subscreva](reacting-to-media-services-events.md) os eventos [Microsoft.Media.LiveEventEncoderDeee](media-services-event-schemas.md#liveeventencoderdisconnected) os eventos e monitorize que não entram reconexões durante algum tempo para parar e eliminar o seu evento ao vivo.
* [Subscreva](reacting-to-media-services-events.md) os eventos de [batimentocardíaco](media-services-event-schemas.md#liveeventingestheartbeat) ao nível da pista. Se todas as faixas tiverem uma bitrate de entrada caindo para 0 ou a última vez que o carimbo já não está aumentando, você pode desligar com segurança o evento ao vivo. Os eventos de batimentos cardíacos chegam a cada 20 segundos para cada pista, por isso pode ser um pouco verboso.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Como insiro quebras/vídeos e ardósias de imagens durante um live stream?

A codificação ao vivo do Media Services v3 ainda não suporta a inserção de imagens de vídeo ou imagem durante o live stream. 

Pode utilizar um [codificador ao vivo no local](recommended-on-premises-live-encoders.md) para mudar o vídeo de origem. Muitas aplicações fornecem capacidade para mudar fontes, incluindo Telestream Wirecast, Switcher Studio (no iOS) e OBS Studio (app gratuita).

## <a name="content-protection"></a>Proteção de conteúdo

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Devo usar encriptação de chave aes-128 ou um sistema DEDRM?

Os clientes muitas vezes perguntam-se se devem usar encriptação AES ou um sistema DEDRM. A principal diferença entre os dois sistemas é que, com a encriptação AES, a chave de conteúdo é transmitida ao cliente através de TLS para que a chave seja encriptada em trânsito, mas sem qualquer encriptação adicional ("in clear"). Como resultado, a chave que é usada para desencriptar o conteúdo é acessível ao leitor cliente e pode ser vista num traço de rede no cliente em texto simples. A encriptação clara da chave AES-128 é adequada para casos de utilização em que o espectador é uma parte de confiança (por exemplo, encriptar vídeos corporativos distribuídos dentro de uma empresa para ser visto pelos colaboradores).

Sistemas DEDRComo como PlayReady, Widevine e FairPlay fornecem um nível adicional de encriptação na chave que é usada para desencriptar o conteúdo, em comparação com uma chave clara AES-128. A chave de conteúdo é encriptada para uma chave protegida pelo tempo de execução da DRM, além de qualquer encriptação ao nível de transporte fornecida pelo TLS. Além disso, a desencriptação é tratada num ambiente seguro ao nível do sistema operativo, onde é mais difícil para um utilizador malicioso atacar. Recomendamos a DRM para casos de utilização em que o espectador pode não ser uma parte de confiança e você precisa do mais alto nível de segurança.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Como posso mostrar um vídeo apenas a utilizadores que tenham uma permissão específica, sem usar o Azure AD?

Não é necessário utilizar nenhum fornecedor de fichas específico, como o Azure Ative Directory (Azure AD). Pode criar o seu próprio fornecedor [JWT](https://jwt.io/) (o chamado Serviço De Token Seguro, ou STS) utilizando encriptação de chave assimétrica. No seu STS personalizado, pode adicionar reclamações com base na sua lógica de negócio.

Certifique-se de que o emitente, o público e as alegações `ContentKeyPolicyRestriction` correspondem `ContentKeyPolicy`exatamente entre o que está em JWT e o valor usado em .

Para mais informações, consulte [Proteja o seu conteúdo utilizando encriptação dinâmica dos Media Services](content-protection-overview.md).

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Como e onde consegui um símbolo jWT antes de usá-lo para pedir uma licença ou chave?

Para produção, é necessário ter o Secure Token Service (isto é, um serviço web), que emite um símbolo JWT sobre um pedido HTTPS. Para o teste, pode utilizar o `GetTokenAsync` código mostrado no método definido em [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

O jogador faz um pedido, após a autenticação de um utilizador, à STS para tal token e atribui-o como o valor do símbolo. Pode utilizar a API do [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/).

Para um exemplo de execução de STS com uma chave simétrica ou uma chave assimétrica, consulte a [ferramenta JWT](https://aka.ms/jwt). Para um exemplo de um jogador baseado no Azure Media Player usando uma ficha JWT, consulte a ferramenta de teste de [mídia Azure](https://aka.ms/amtest). (Expanda a ligação **player_settings** para ver a entrada simbólica.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Como autorizo pedidos para transmitir vídeos com encriptação AES?

A abordagem correta é utilizar o Serviço De Token Seguro. No STS, dependendo do perfil do utilizador, adicione diferentes reclamações (como "Utilizador Premium", "Utilizador Básico", "Utilizador de EnsaioGratuito"). Com diferentes reclamações num JWT, o utilizador pode ver conteúdos diferentes. Para diferentes conteúdos `ContentKeyPolicyRestriction` ou ativos, terá o valor correspondente. `RequiredClaims`

Utilize apIs de Serviços de Mídia Azure para configurar a entrega de licença/chave e encriptar os seus ativos (como mostrado [nesta amostra).](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)

Para obter mais informações, consulte:

- [Visão geral da proteção de conteúdos](content-protection-overview.md)
- [Conceção de um sistema de proteção de conteúdos multi-DRM com controlo de acesso](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Devo usar HTTP ou HTTPS?
A aplicação ASP.NET do leitor de MVC deve suportar o seguinte:

* Autenticação do utilizador através do Azure AD, que se encontra em HTTPS.
* Troca de JWT entre o cliente e a Azure AD, que está em HTTPS.
* Aquisição de licença DRM pelo cliente, que deve estar em HTTPS se a entrega de licença for fornecida pela Media Services. A suíte de produto PlayReady não manda HTTPS para entrega de licença. Se o seu servidor de licença PlayReady estiver fora dos Serviços de Media, pode utilizar http ou HTTPS.

A aplicação ASP.NET jogador utiliza HTTPS como uma melhor prática, pelo que o Media Player está numa página em HTTPS. No entanto, http é preferido para streaming, por isso você precisa considerar estas questões com conteúdo misto:

* O navegador não permite conteúdo misto. Mas plug-ins como Silverlight e o OSMF plug-in para Smooth e DASH permitem. O conteúdo misto é uma preocupação de segurança devido à ameaça da capacidade de injetar JavaScript malicioso, o que pode colocar os dados do cliente em risco. Os navegadores bloqueiam esta capacidade por padrão. A única forma de trabalhar em torno dele é do lado do servidor (origem), permitindo que todos os domínios (independentemente de HTTPS ou HTTP). Provavelmente também não é uma boa ideia.
* Evite conteúdo misto. Tanto a aplicação do jogador como o Media Player devem utilizar HTTP ou HTTPS. Quando estiver a jogar conteúdo misto, a tecnologia SilverlightSS requer a desobstrução de um aviso de conteúdo misto. A tecnologia FlashSS lida com conteúdo misto sem um aviso de conteúdo misto.
* Se o seu ponto final de streaming foi criado antes de agosto de 2014, não apoiará HTTPS. Neste caso, crie e use um novo ponto final de streaming para HTTPS.

### <a name="what-about-live-streaming"></a>E o streaming ao vivo?

Você pode usar exatamente o mesmo design e implementação para ajudar a proteger o streaming ao vivo em Media Services, tratando o ativo associado a um programa como um ativo VOD. Para fornecer uma proteção multi-DRM do conteúdo vivo, aplique a mesma configuração/processamento ao ativo como se fosse um ativo VOD antes de associar o ativo à saída ao vivo.

### <a name="what-about-license-servers-outside-media-services"></a>E os servidores de licença fora dos Serviços de Media?

Muitas vezes, os clientes têm investido numa exploração de servidores de licenças, quer no seu próprio datacenter, quer numa hospedada por prestadores de serviços drm. Com a proteção de conteúdos dos Media Services, pode operar em modo híbrido. Os conteúdos podem ser hospedados e protegidos dinamicamente nos Serviços de Media, enquanto as licenças de DRM são entregues por servidores fora dos Serviços de Media. Neste caso, considere as seguintes alterações:

* O STS precisa de emitir fichas aceitáveis e podem ser verificadas pela exploração do servidor de licenças. Por exemplo, os servidores de licença Widevine fornecidos pela Axinom requerem um JWT específico que contém uma mensagem de direito. Precisa de uma STS para emitir tal JWT. 
* Já não precisa de configurar o serviço de entrega de licenças nos Serviços de Media. Você precisa fornecer os URLs de aquisição de licença (para PlayReady, `ContentKeyPolicy`Widevine e FairPlay) quando configurar .

> [!NOTE]
> A Widevine é um serviço prestado pela Google e sujeito aos termos de serviço e política de privacidade da Google.

## <a name="media-services-v2-vs-v3"></a>Serviços de Media v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Posso usar o portal Azure para gerir os recursos v3?

Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

* Gerir [eventos ao vivo](live-events-outputs-concept.md) em Serviços de Media v3. 
* Ver (não gerir) [ativos](assets-concept.md)v3 . 
* [Obtenha informações sobre o acesso a APIs.](access-api-portal.md) 

Para todas as outras tarefas de gestão (por exemplo, [Transforms and Jobs](transforms-jobs-concept.md) e [proteção de conteúdos),](content-protection-overview.md)utilize a [API REST,](https://docs.microsoft.com/rest/api/media/)o [Azure CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Existe um conceito AssetFile em v3?

O `AssetFile` conceito foi removido da API dos Serviços de Media para separar os Serviços de Media da dependência do SDK de Armazenamento. Agora o Azure Storage, não os Media Services, guarda a informação que pertence ao SDK de Armazenamento. 

Para mais informações, consulte [Migrate to Media Services v3](media-services-v2-vs-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Para onde foi a encriptação de armazenamento do lado do cliente?

Recomendamos agora que utilize encriptação de armazenamento do lado do servidor (que está por defeito). Para mais informações, consulte a Encriptação do Serviço de [Armazenamento Azure para obter dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="offline-streaming"></a>Streaming offline

### <a name="fairplay-streaming-for-ios"></a>FairPlay Streaming para iOS

As seguintes perguntas frequentemente colocadas fornecem assistência com problemas de resolução offline fairPlay streaming para iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Porque é que só o áudio reprodução mas não o vídeo durante o modo offline?

Este comportamento parece ser por design da aplicação de amostra. Quando existe uma faixa de áudio alternativa (o que é o caso do HLS) durante o modo offline, tanto o iOS 10 como o iOS 11 predefinidos para a faixa de áudio alternativa. Para compensar este comportamento do modo offline FPS, retire a faixa de áudio alternativa do fluxo. Para isso nos Serviços de Media, adicione o filtro de manifesto dinâmico **apenas=falso**. Por outras palavras, um URL HLS termina com **.ism/manifest (formato=m3u8-apl,audio-only=falso)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Porque é que ainda reprodução áudio sem vídeo durante o modo offline depois de eu adicionar áudio-only=falso?

Dependendo do design da chave cache para a rede de entrega de conteúdos, o conteúdo pode ser cache. Purgue a cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>O modo offline FPS é suportado no iOS 11 para além do iOS 10?

Sim. O modo offline FPS é suportado para iOS 10 e iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Por que não encontro o documento "Offline Playback with FairPlay Streaming e HTTP Live Streaming" no FPS Server SDK?

Desde a versão 4 do FPS Server SDK, este documento foi fundido no "FairPlay Streaming Programming Guide".

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Qual é a estrutura de ficheiros descarregado/offline nos dispositivos iOS?

A estrutura de ficheiros descarregada num dispositivo iOS parece ser a seguinte imagem. As `_keys` lojas de pastas descarregaram licenças fps, com um ficheiro de loja para cada anfitrião do serviço de licença. A `.movpkg` pasta armazena conteúdo sonoro e vídeo. 

A primeira pasta com um nome que termina com um traço seguido de um número contém conteúdo de vídeo. O valor numérico é a largura de banda máxima das representações em vídeo. A segunda pasta com um nome que termina com um traço seguido de 0 contém conteúdo áudio. A terceira `Data` pasta nomeada contém a lista de reprodução principal do conteúdo do FPS. Finalmente, boot.xml fornece uma descrição completa do conteúdo da `.movpkg` pasta. 

![Estrutura de ficheirooffline para a aplicação de amostra siOS FairPlay](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Aqui está um ficheiro boot.xml de amostra:

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Como posso entregar licenças persistentes (offline habilitadas) para alguns clientes/utilizadores e licenças não persistentes (offline desativadas) para outros? Tenho de duplicar o conteúdo e usar chaves de conteúdo separadas?

Como o Media Services v3 `StreamingLocator` permite que um ativo tenha várias instâncias, pode ter:

* Um `ContentKeyPolicy` caso `license_type = "persistent"` `ContentKeyPolicyRestriction` com, `"persistent"`com reivindicação sobre, e o seu `StreamingLocator`.
* Outro `ContentKeyPolicy` caso `license_type="nonpersistent"` `ContentKeyPolicyRestriction` com, `"nonpersistent`com reivindicação sobre ", e seu `StreamingLocator`.
* Dois `StreamingLocator` casos que `ContentKey` têm valores diferentes.

Dependendo da lógica empresarial do STS personalizado, diferentes reivindicações são emitidas no símbolo JWT. Com o símbolo, apenas a licença correspondente pode ser obtida e apenas o URL correspondente pode ser jogado.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Qual é o mapeamento entre os níveis de segurança drm da Widevine e dos Media Services?

A "Visão Geral da Arquitetura DRM widevine" da Google define três níveis de segurança. No entanto, a documentação da [Azure Media Services sobre o modelo](widevine-license-template-overview.md) de licença Widevine descreve cinco níveis de segurança (requisitos de robustez do cliente para reprodução). Esta secção explica como os níveis de segurança mapeiam.

Ambos os conjuntos de níveis de segurança são definidos pelo Google Widevine. A diferença está no nível de utilização: arquitetura ou API. Os cinco níveis de segurança são usados na API widevine. O `content_key_specs` objeto, `security_level`que contém, é desserializado e passado para o serviço de entrega global Widevine pelo serviço de licença Azure Media Services Widevine. A tabela seguinte mostra o mapeamento entre os dois conjuntos de níveis de segurança.

| **Níveis de segurança definidos na arquitetura Widevine** |**Níveis de segurança utilizados na API widevine**|
|---|---| 
| **Nível de Segurança 1:** Todo o processamento de conteúdos, criptografia e controlo são realizados no Ambiente de Execução Fidedigna (TEE). Em alguns modelos de implementação, o processamento de segurança pode ser realizado em diferentes chips.|**security_level=5**: O crypto, a descodificação e todo o manuseamento dos meios de comunicação (comprimidos e descomprimidos) devem ser manuseados dentro de um TEE apoiado por hardware.<br/><br/>**security_level=4**: A criptoee e a descodificação de conteúdos devem ser realizadas dentro de um TEE apoiado por hardware.|
**Nível de Segurança 2:** A criptografia (mas não o processamento de vídeo) é realizada dentro do TEE. Os amortecedores desencriptados são devolvidos ao domínio da aplicação e processados através de hardware ou software de vídeo separado. No nível 2, no entanto, a informação criptográfica ainda é processada apenas dentro do TEE.| **security_level=3**: O material-chave e as operações de cripto devem ser realizados dentro de um TEE apoiado por hardware. |
| **Nível de segurança 3:** Não há TEE no dispositivo. Podem ser tomadas medidas adequadas para proteger as informações criptográficas e os conteúdos desencriptados no sistema operativo do hospedeiro. Uma implementação de Nível 3 também pode incluir um motor criptográfico de hardware, mas isso aumenta apenas o desempenho, não a segurança. | **security_level=2**: São necessários criptografias de software e um descodificador obfuscado.<br/><br/>**security_level=1**: É necessário um cripto de caixa branca baseado em software.|

#### <a name="why-does-content-download-take-so-long"></a>Porque é que o download de conteúdos demora tanto tempo?

Existem duas formas de melhorar a velocidade de descarregamento:

* Ative uma rede de entrega de conteúdos de modo a que os utilizadores sejam mais propensos a atingir isso em vez do ponto final de origem/streaming para o download de conteúdos. Se um utilizador atingir um ponto final de streaming, cada segmento HLS ou fragmento dash é embalado e encriptado dinamicamente. Mesmo que esta latência esteja em escala de milissegundos para cada segmento ou fragmento, quando você tem um vídeo de uma hora, a latência acumulada pode ser grande e causar um download mais longo.
* Dê aos utilizadores a opção de descarregar seletivamente camadas de qualidade de vídeo e faixas de áudio em vez de todos os conteúdos. Para o modo offline, não faz sentido descarregar todas as camadas de qualidade. Há duas formas de o conseguir:

  * Controlada pelo cliente: A aplicação do jogador seleciona automaticamente, ou o utilizador seleciona, a camada de qualidade de vídeo e as faixas de áudio para descarregar.
  * Controlado pelo serviço: Pode utilizar a funcionalidade Dynamic Manifest nos Serviços De Mídia Azure para criar um filtro (global), que limita a lista de reprodução de HLS ou OMPD DO DASH a uma única camada de qualidade de vídeo e faixas de áudio selecionadas. Em seguida, o URL de descarregamento apresentado aos utilizadores incluirá este filtro.

## <a name="next-steps"></a>Passos seguintes

[Visão geral do Media Services v3](media-services-overview.md)
