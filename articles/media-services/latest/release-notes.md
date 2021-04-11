---
title: Notas de lançamento da Azure Media Services v3
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe as últimas atualizações sobre o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 9fe49f44e157708c3083e2c73757988877fbfcb5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563468"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de lançamento da Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

* Os últimos lançamentos
* Problemas conhecidos
* Correções de erros
* Funcionalidade preterida

## <a name="march-2021"></a>março de 2021

### <a name="new-language-support-added-to-the-audioanalyzer-preset"></a>Novo suporte linguístico adicionado à predefinição AudioAnalyzer

Os idiomas adicionais para transcrição de vídeo e legendagem estão agora disponíveis na predefinição AudioAnalyzer (ambos os modos Básico e Standard).

* Inglês (Austrália), 'en-AU'
* Francês (Canadá), 'fr-CA'
* Padrão moderno árabe (Bahrain), 'ar-BH'
* Árabe (Egito), 'ar-EG'
* Árabe (Iraque), 'ar-QI'
* Árabe (Israel), 'ar-IL'
* Árabe (Jordânia), 'ar-JO'
* Árabe (Kuwait), 'ar-KW'
* Árabe (Líbano), 'ar-LB'
* Árabe (Omã), 'ar-OM'
* Árabe (Qatar), 'ar-QA'
* Árabe (Arábia Saudita), 'ar-SA'
* Dinamarquês, 'da-DK'
* Norueguês, 'nb-NO'
* Sueco, 'sv-SE'
* Finlandês, 'fi-FI'
* Tailandês, 'th-TH'
* Turco, 'tr-TR'

Veja os idiomas mais recentes disponíveis no [artigo de conceito de Arquivos de Vídeo e Áudio.](analyzing-video-audio-files-concept.md)

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="hevc-encoding-support-in-standard-encoder"></a>Suporte de codificação HEVC no Standard Encoder

O Standard Encoder suporta agora suporte de codificação HEVC (H.265) de 8 bits. O conteúdo HEVC pode ser entregue e embalado através do Pacote Dinâmico utilizando o formato 'hev1'.  

Uma nova codificação personalizada .NET com amostra HEVC está disponível no [repositório de mídia-serviços-v3-dotnet Git Hub](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC).
Além da codificação personalizada, as seguintes novas predefinições de codificação HEVC incorporadas estão agora disponíveis:

- H265ContentAwareEncoding
- H265AdaptiveStreaming
- H265SingleBitrate720p
- H265SingleBitrate1080p
- H265SingleBitrate4K

Os clientes que utilizem anteriormente o HEVC no Codificadora Premium no V2 API devem migrar para utilizar o novo suporte de codificação HEVC no Codificação Standard.

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Azure Media Services v2 API e SDKs anúncio de depreciação

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Atualize os seus Serviços de Media Azure REST API e SDKs para v3 até 29 de fevereiro de 2024

Como a versão 3 do Azure Media Services REST API e os Clientes SDKs para .NET e Java oferecem mais capacidades do que a versão 2, estamos a retirar a versão 2 do Azure Media Services REST API e os SDKs clientes para .NET e Java.

Encorajamo-lo a fazer a troca mais cedo para obter os benefícios mais ricos da versão 3 da Azure Media Services REST API e dos Clientes SDKs para .NET e Java.
A versão 3 fornece:
 
- Suporte ao evento ao vivo 24x7
- ARM REST APIs, SDKs cliente para .NET core, Node.js, Python, Java, Go e Ruby.
- Chaves geridas pelo cliente, integração de armazenamento confiável, suporte de ligação privada, e [muito mais](https://docs.microsoft.com/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Ação Necessária

Para minimizar a perturbação das suas cargas de trabalho, reveja o guia de [migração](./migrate-v-2-v-3-migration-introduction.md) para transitar o seu código da versão 2 API e SDKs para a versão 3 API e SDK antes de 29 de fevereiro de 2024.
**Depois de 29 de fevereiro de 2024,** a Azure Media Services deixará de aceitar o tráfego na versão 2 REST API, na versão API de gestão de conta ARM 2015-10-01, ou na versão 2 .NET client SDKs. Isto inclui qualquer cliente de código aberto de 3ª parte SDKS que pode ligar para a versão 2 API.  

Veja o anúncio oficial do [Azure Updates](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

### <a name="standard-encoder-support-for-v2-api-features"></a>Suporte Padrão encoder para funcionalidades V2 API

Além do novo suporte adicional para codificação HEVC (H.265), as seguintes funcionalidades estão agora disponíveis na versão 2020-05-01 da API codificante.

- A costura de ficheiro de entrada múltipla é agora suportada utilizando o novo suporte **JobInputClip.**
    - Um exemplo está disponível para .NET mostrando como [coser dois ativos juntos](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets).
- A seleção de faixas de áudio permite que os clientes selecionem e mapeiem as faixas de áudio que chegam e os encaminhem para a saída para codificação
    - Consulte o [REST API OpenAPI para obter mais detalhes](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L385) sobre o **AudioTrackDescriptor** e a seleção de faixas
- A seleção de rastreios para codificação – permite que os clientes escolham faixas a partir de um ficheiro de origem ABR ou de um arquivo ao vivo que tenha várias faixas de bitrate. Extremamente útil para gerar MP4s a partir dos arquivos de arquivo de eventos ao vivo.
    - Ver [VideoTrackDescriptor](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L1562)
- Capacidades de redação (desfocagem) adicionadas ao FaceDetector
    - Consulte os modos [Redact](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L634) e [Combined](https://github.com/Azure/azure-rest-api-specs/blob/8d15dc681b081cca983e4d67fbf6441841d94ce4/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json#L649) da Pré-sintonia faceDetector

### <a name="new-client-sdk-releases-for-2020-05-01-version-of-the-azure-media-services-api"></a>Novo cliente SDK lança para a versão 2020-05-01 da Azure Media Services API

As novas versões SDK do cliente para todos os idiomas disponíveis estão agora disponíveis com as funcionalidades acima.
Por favor, atualize os SDKs mais recentes do cliente nas suas bases de código usando o seu gestor de pacotes.

- [.NET Pacote SDK 3.0.4](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/)
- [Node.js Versão typescript 8.1.0](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Python azure-mgmt-media 3.1.0](https://pypi.org/project/azure-mgmt-media/)
- [Java SDK 1.0.0-beta.2](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-mediaservices/1.0.0-beta.2/jar)

### <a name="new-security-features-available-in-the-2020-05-01-version-of-the-azure-media-services-api"></a>Novas funcionalidades de Segurança disponíveis na versão 2020-05-01 da Azure Media Services API

- **[Chaves Geridas pelo Cliente](concept-use-customer-managed-keys-byok.md)**: Chaves de conteúdo e outros dados armazenados em contas criadas com a versão "2020-05-01" API são encriptados com uma chave de conta. Os clientes podem fornecer uma chave para encriptar a chave da conta.

- **[Armazenamento Fidedigno](concept-trusted-storage.md)**: Os Serviços de Mídia podem ser configurados para aceder ao Azure Storage utilizando uma Identidade Gerida associada à conta dos Serviços de Comunicação Social. Quando as contas de armazenamento são acedidas usando uma Identidade Gerida, os clientes podem configurar ACLs de rede mais restritivos na conta de armazenamento sem bloquear cenários de Media Services.

- **[Identidades geridas](concept-managed-identities.md)**: Os clientes podem permitir que uma identidade gerida atribuída a um sistema para uma conta de Serviços de Mídia forneça acesso a Cofres-Chave (para Chaves Geridas pelo Cliente) e contas de armazenamento (para armazenamento fidedigno).

### <a name="updated-typescript-nodejs-samples-using-isomorphic-sdk-for-javascript"></a>Amostras de Node.js de typescript atualizadas usando SDK isomórfico para JavaScript

As amostras Node.js foram atualizadas para utilizar o mais recente SDK isomórfico. As amostras mostram agora o uso da Typescript. Além disso, foi adicionada uma nova amostra de streaming ao vivo para Node.js/Typescript.

Veja as últimas amostras nos **[media-services-v3-node-tutorials](https://github.com/Azure-Samples/media-services-v3-node-tutorials)** Git Hub repo.

### <a name="new-live-stand-by-mode-to-support-faster-startup-from-warm-state"></a>Novo modo Live Stand-by para apoiar startup mais rápida do estado quente

Live Events agora suporta um modo de faturação mais baixo para "stand-by". Isto permite que os clientes pré-aloquem Eventos Ao Vivo a um custo mais baixo para a criação de "piscinas quentes". Os clientes podem então usar os eventos de stand-by ao vivo para a transição para o estado de Corrida mais rapidamente do que começar do frio na criação.  Isto reduz o tempo para iniciar o canal de forma significativa e permite a atribuição rápida de máquinas que executam em modo de preço mais baixo.
Veja [aqui](https://azure.microsoft.com/pricing/details/media-services)os últimos detalhes dos preços.
Para obter mais informações sobre o estado de StandBy e os outros estados de Eventos Ao Vivo consulte o artigo - [Estados de evento ao vivo e faturação.](./live-event-states-billing.md)

## <a name="december-2020"></a>Dezembro de 2020

### <a name="regional-availability"></a>Disponibilidade regional

A Azure Media Services está agora disponível na região leste da Noruega no portal Azure.  Não há repousoV2 nesta região.

## <a name="october-2020"></a>Outubro de 2020

### <a name="basic-audio-analysis"></a>Análise áudio básica

A predefinição de Análise áudio inclui agora um nível de preços de modo básico. O novo modo Basic Audio Analyzer oferece uma opção de baixo custo para extrair transcrição de voz e legendas e legendas de saída de formato. Este modo executa a transcrição de discurso-a-texto e a geração de um ficheiro de legenda/legenda VTT. A saída deste modo inclui um ficheiro Insights JSON, incluindo apenas as palavras-chave, transcrição e informações de tempo. A deteção automática de idiomas e a diarização dos altifalantes não estão incluídas neste modo. Consulte a lista de [línguas apoiadas.](analyzing-video-audio-files-concept.md#built-in-presets)

Os clientes que usam Indexer v1 e Indexer v2 devem migrar para a predefinição de Análise Áudio Básica.

Para obter mais informações sobre o modo Analisador de Áudio Básico, consulte [os ficheiros De Vídeo e Áudio](analyzing-video-audio-files-concept.md)de Análise .  Para aprender a utilizar o modo Analisador de Áudio Básico com a API REST, consulte [Como Criar uma Transformação Áudio Básica](how-to-create-basic-audio-transform.md).

### <a name="live-events"></a>Eventos em Direto

As atualizações para a maioria das propriedades são agora permitidas quando os eventos ao vivo são interrompidos. Além disso, os utilizadores estão autorizados a especificar um prefixo para o nome de anfitrião estático para os URLs de entrada e pré-visualização do evento ao vivo. A VanityUrl é agora chamada `useStaticHostName` para melhor refletir a intenção da propriedade.

Os eventos ao vivo têm agora um estado de espera.  Ver [Eventos Ao Vivo e Saídas Ao Vivo nos Serviços de Media.](./live-events-outputs-concept.md)

Um evento ao vivo suporta receber vários rácios de aspeto de entrada. O modo alongamento permite que os clientes especifiquem o comportamento de alongamento para a saída.

A codificação ao vivo adiciona agora a capacidade de saída de fragmentos de intervalo de chave fixa entre 0,5 e 20 segundos.

### <a name="accounts"></a>Contas

> [!WARNING]
> Se criar uma conta de Media Services com a versão API 2020-05-01 não funcionará com a RESTv2 

## <a name="august-2020"></a>Agosto de 2020

### <a name="dynamic-encryption"></a>Encriptação Dinâmica

O suporte para a encriptação de ficheiros interoperáveis (PIFF 1.1) do legado PlayReady Protected Está agora disponível no Pacote Dinâmico. Isto fornece suporte para séries de TV inteligentes legacy da Samsung e LG que implementaram os primeiros rascunhos da norma de encriptação comum (CENC) publicada pela Microsoft.  O formato PIFF 1.1 é também conhecido como o formato de encriptação que foi previamente suportado pela biblioteca do cliente Silverlight. Hoje em dia, o único cenário de uso para este formato de encriptação é direcionar o legado mercado smart TV onde permanece um número não trivial de Smart TVs em algumas regiões que apenas suportam o Smooth Streaming com encriptação PIFF 1.1.

Para utilizar o novo suporte de encriptação PIFF 1.1, altere o valor de encriptação para 'piff' no caminho URL do Localizador de Streaming. Para mais detalhes, consulte a [visão geral da Proteção de Conteúdos.](content-protection-overview.md)
Por exemplo: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> O suporte PIFF 1.1 é fornecido como uma solução compatível para trás para Smart TV (Samsung, LG) que implementou a versão inicial "Silverlight" da Common Encryption. Recomenda-se apenas utilizar o formato PIFF sempre que necessário para suporte aos legacy Samsung ou LG Smart TVs enviados entre 2009 e 2015 que suportavam a versão PIFF 1.1 da encriptação PlayReady. 

## <a name="july-2020"></a>Julho de 2020

### <a name="live-transcriptions"></a>Transcrições ao vivo

As transcrições ao vivo suportam agora 19 línguas e 8 regiões.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Proteger o seu conteúdo com serviços de media e Azure AD

Publicámos um tutorial chamado [proteção de conteúdo de ponta a ponta usando a Azure AD](./azure-ad-content-protection.md).

### <a name="high-availability"></a>Elevada disponibilidade

Publicámos uma [visão geral](./media-services-high-availability-encoding.md) e uma amostra de Alta Disponibilidade com Serviços de Comunicação Social e [Vídeo](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)a Pedido (VOD).

## <a name="june-2020"></a>Junho de 2020

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Vídeo ao vivo analítico no lançamento da pré-estreia do IoT Edge

A pré-estreia de Live Video Analytics no IoT Edge tornou-se pública. Para obter mais informações, consulte [as notas de lançamento.](../live-video-analytics-edge/release-notes.md)

Live Video Analytics on IoT Edge é uma expansão para a família Media Service. Permite-lhe analisar vídeo ao vivo com modelos de IA à sua escolha nos seus próprios dispositivos de borda, e capturar e gravar opcionalmente esse vídeo. Agora pode construir aplicativos com análise de vídeo em tempo real no limite sem se preocupar com a complexidade de construir e operar um pipeline de vídeo ao vivo.

## <a name="may-2020"></a>Maio de 2020

A Azure Media Services está agora geralmente disponível nas seguintes regiões: "Alemanha Norte", "Alemanha Central Ocidental", "Suíça Norte" e "Suíça Oeste". Os clientes podem implantar Serviços de Mídia nestas regiões utilizando o portal Azure.

## <a name="april-2020"></a>Abril de 2020

### <a name="improvements-in-documentation"></a>Melhorias na documentação

Os docs do Azure Media Player foram migrados para a documentação do [Azure](../azure-media-player/azure-media-player-overview.md).

## <a name="january-2020"></a>Janeiro de 2020

### <a name="improvements-in-media-processors"></a>Melhorias nos processadores de mídia

- Um melhor suporte para fontes entrelaçadas em Análise de Vídeo – este conteúdo é agora desligado corretamente antes de ser enviado para motores de inferência.
- Ao gerar miniaturas com o modo "Best", o codificador procura agora mais de 30 segundos para selecionar uma moldura que não seja monocromática.

### <a name="azure-government-cloud-updates"></a>Atualizações em nuvem do Governo de Azure

Media Services GA'ed nas seguintes regiões do Governo Azure: *USGov Arizona* e *USGov Texas*.

## <a name="december-2019"></a>Dezembro de 2019

Apoio CDN adicionado aos cabeçalhos *Prefetch Origin-Assist* para streaming ao vivo e vídeo a pedido; disponível para clientes que tenham contrato direto com a Akamai CDN. Origin-Assist CDN-Prefetch funcionalidade envolve as seguintes trocas de cabeçalho HTTP entre a origem da Akamai CDN e da Azure Media Services:

|Cabeçalho HTTP|Valores|Remetente|Recetor|Objetivo|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1 (padrão) ou 0 |CDN|Origem|Para indicar que o CDN é pré-desprotch ativado|
|CDN-Origin-Assist-Prefetch-Path| Exemplo: <br/>Fragmentos (vídeo=14000000000000000000000000000000000000000000000000000000000000000000000000000|Origem|CDN|Para fornecer caminho de prefetch para CDN|
|CDN-Origin-Assist-Prefetch-Request|1 (pedido de precade) ou 0 (pedido regular)|CDN|Origem|Para indicar o pedido da CDN é uma prefâmia|

Para ver parte da troca de cabeçalho em ação, pode experimentar os seguintes passos:

1. Utilize o Carteiro ou o curl para emitir um pedido à origem dos Media Services para um segmento de áudio ou vídeo ou fragmento. Certifique-se de que adiciona o cabeçalho CDN-Origin-Assist-Prefetch-Enabled: 1 no pedido.
2. Na resposta, deverá ver o cabeçalho CDN-Origin-Assist-Prefetch-Path com um percurso relativo como seu valor.

## <a name="november-2019"></a>Novembro de 2019

### <a name="live-transcription-preview"></a>Pré-visualização da transcrição ao vivo

A transcrição ao vivo está agora em pré-visualização pública e disponível para uso na região oeste dos EUA 2.

A transcrição ao vivo é projetada para trabalhar em conjunto com eventos ao vivo como uma capacidade de complemento.  É suportado em eventos de passagem e Standard ou Premium codificando ao vivo.  Quando esta funcionalidade está ativada, o serviço utiliza a funcionalidade [Discurso-Texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio de entrada em texto. Este texto é então disponibilizado para entrega juntamente com vídeo e áudio nos protocolos MPEG-DASH e HLS. A faturação baseia-se num novo medidor adicional que é um custo adicional para o evento ao vivo quando está no estado "Running".  Para mais detalhes sobre transcrição e faturação ao vivo, consulte [a transcrição ao vivo](live-transcription.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como uma funcionalidade de pré-visualização na região oeste dos EUA 2. Só neste momento apoia a transcrição de palavras faladas em inglês (en-us).

### <a name="content-protection"></a>Proteção de conteúdo

A funcionalidade *De Prevenção de Repetição token* lançada em regiões limitadas em setembro está agora disponível em todas as regiões.
Os clientes dos Serviços de Comunicação Social podem agora estabelecer um limite no número de vezes que o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [a Prevenção de Repetição de Token.](content-protection-overview.md#token-replay-prevention)

### <a name="new-recommended-live-encoder-partners"></a>Novos parceiros de codificação ao vivo recomendados

Suporte adicional para os seguintes novos codificadores recomendados para streaming ao vivo RTMP:

- [Cambria Ao Vivo 4.3](https://www.capellasystems.net/products/cambria-live/)
- [Câmaras de ação GoPro Hero7/8 e Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Melhoramentos de codificação de ficheiros

- Uma nova predefinição de Codificação de Conteúdos Conscientes já se encontra disponível. Produz um conjunto de MP4s alinhados com GOP utilizando codificação consciente do conteúdo. Dado qualquer conteúdo de entrada, o serviço realiza uma primeira análise leve do conteúdo de entrada. Utiliza esses resultados para determinar o número ideal de camadas, a taxa de bits adequada e as definições de resolução para entrega através do streaming adaptativo. Esta predefinição é particularmente eficaz para vídeos de baixa complexidade e complexidade média, onde os ficheiros de saída estão a taxas de bits mais baixas, mas a uma qualidade que ainda oferece uma boa experiência aos espectadores. A saída conterá ficheiros MP4 com vídeo e áudio intercalados. Para mais informações, consulte as [especificações abertas da API.](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)
- Melhor desempenho e multi-rosca para o ressizer em Standard Encoder. Em condições específicas, o cliente deve ver um aumento de desempenho entre 5-40% de codificação de VOD. O conteúdo de baixa complexidade codificado em várias taxas de bit verá os maiores aumentos de desempenho. 
- A codificação padrão mantém agora uma cadência gop regular para o conteúdo da taxa de fotogramas variável (VFR) durante a codificação do VOD ao utilizar a definição de GOP baseada no tempo.  Isto significa que o cliente que submete conteúdo misto de taxa de fotogramas que varia entre 15-30 fps, por exemplo, deve agora ver distâncias gop regulares calculadas na saída para ficheiros de streaming de bitrate adaptativo MP4. Isto melhorará a capacidade de alternar perfeitamente entre faixas ao entregar sobre HLS ou DASH. 
-  Sincronização AV melhorada para o teor de origem da taxa de fotogramas variável (VFR)

### <a name="video-indexer-video-analytics"></a>Indexante de Vídeo, Análise de Vídeo

- Os quadros-chave extraídos usando a predefinição videoAnalyzer estão agora na resolução original do vídeo em vez de serem redimensionados. A extração de quadros de alta resolução dá-lhe imagens de qualidade originais e permite-lhe utilizar os modelos de inteligência artificial baseados na imagem fornecidos pelos serviços Microsoft Computer Vision e Custom Vision para obter ainda mais informações a partir do seu vídeo.

## <a name="september-2019"></a>Setembro de 2019

###  <a name="media-services-v3"></a>Serviços de Multimédia v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codificação linear ao vivo de eventos ao vivo

A Media Services v3 está anunciando a pré-estreia de 24 hrs x 365 dias de codificação linear ao vivo de eventos ao vivo.

###  <a name="media-services-v2"></a>Serviços de Multimédia v2  

#### <a name="deprecation-of-media-processors"></a>Depreciação dos processadores de meios de comunicação

Estamos a anunciar a depreciação do *Azure Media Indexer* e *do Azure Media Indexer 2 Preview*. Para as datas de aposentadoria, consulte o artigo [componentes do legado.](../previous/legacy-components.md) [O Azure Media Services Video Indexer](../video-indexer/index.yml) substitui estes processadores de mídia legado.

Para obter mais informações, consulte [Migrar do Azure Media Indexer e do Azure Media Indexer 2 para OZure Media Services Video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

###  <a name="media-services-v3"></a>Serviços de Multimédia v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Par regional da África do Sul está aberto para serviços de media 

Os Serviços de Comunicação Social estão agora disponíveis nas regiões ocidentais do Norte e da África do Sul.

Para mais informações, consulte [Nuvens e regiões em que existem serviços de comunicação v3.](azure-clouds-regions.md)

###  <a name="media-services-v2"></a>Serviços de Multimédia v2  

#### <a name="deprecation-of-media-processors"></a>Depreciação dos processadores de meios de comunicação

Estamos a anunciar a depreciação dos processadores de media *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME), que estão a ser reformados. Para as datas de aposentadoria, consulte este artigo [de componentes legados.](../previous/legacy-components.md)

Para mais detalhes, consulte [a Migração WAME para Media Encoder Standard](../previous/migrate-windows-azure-media-encoder.md) e [Migrar AME para Media Encoder Standard](../previous/migrate-azure-media-encoder.md).
 
## <a name="july-2019"></a>Julho de 2019

### <a name="content-protection"></a>Proteção de conteúdo

Ao transmitir conteúdo protegido com restrição simbólica, os utilizadores finais precisam de obter um token que é enviado como parte do pedido de entrega chave. A funcionalidade *De Prevenção de Repetição token* permite que os clientes dos Media Services estabeleçam um limite para quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [a Prevenção de Repetição de Token.](content-protection-overview.md#token-replay-prevention)

A partir de julho, a funcionalidade de pré-visualização só estava disponível na Central dos EUA e na Central Ocidental dos EUA.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-subclipping"></a>Vídeo subclipping

Agora pode aparar ou subclipar um vídeo ao codificar o vídeo usando um [Trabalho](/rest/api/media/jobs). 

Esta funcionalidade funciona com qualquer [Transform](/rest/api/media/transforms) que seja construído utilizando as predefinições Predefinidas [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições [StandardEncoderPreset.](/rest/api/media/transforms/createorupdate#standardencoderpreset) 

Ver exemplos:

* [Subclip um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Subclip um vídeo com REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maio de 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Suporte do Monitor Azure para registos e métricas de diagnóstico de Serviços de Mídia

Agora pode utilizar o Azure Monitor para visualizar os dados de telemetria emitidos pelos Media Services.

* Utilize os registos de diagnóstico do Azure Monitor para monitorizar os pedidos enviados pelo ponto final de entrega da chave dos serviços de mídia. 
* Métricas de monitorização emitidas por Media Services [Streaming Endpoints](streaming-endpoint-concept.md).   

Para mais informações, consulte [as métricas dos Serviços de Mídia do Monitor e os registos de diagnóstico](monitoring/monitor-media-services-data-reference.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Suporte de faixas de áudio multi-áudio em Embalagem Dinâmica 

Ao transmitir Ativos que tenham múltiplas faixas de áudio com vários codecs e idiomas, [a Dynamic Packaging](dynamic-packaging-overview.md) suporta agora faixas de áudio multi-áudio para a saída HLS (versão 4 ou superior).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Par regional da Coreia está aberto para serviços de media 

Os Serviços de Comunicação Social estão agora disponíveis nas regiões sul da Coreia do Sul e da Coreia. 

Para mais informações, consulte [Nuvens e regiões em que existem serviços de comunicação v3.](azure-clouds-regions.md)

### <a name="performance-improvements"></a>Melhorias de desempenho

Atualizações adicionadas que incluem melhorias no desempenho dos Serviços de Mídia.

* O tamanho máximo do ficheiro suportado para o processamento foi atualizado. Quotas [e limites.](limits-quotas-constraints.md)
* [Melhorias de velocidades de codificação](concept-media-reserved-units.md).

## <a name="april-2019"></a>Abril de 2019

### <a name="new-presets"></a>Novas predefinições

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) foi adicionado às predefinições do analisador incorporado.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) foi adicionado às predefinições codificadoras incorporadas. Para obter mais informações, consulte [codificação consciente do conteúdo.](content-aware-encoding.md) 

## <a name="march-2019"></a>Março de 2019

A Dynamic Packaging suporta agora o Dolby Atmos. Para obter mais informações, consulte [os codecs audio suportados por embalagens dinâmicas.](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

Agora pode especificar uma lista de filtros de ativos ou de conta, que se aplicaria ao seu Localizador de Streaming. Para obter mais informações, consulte [filtros Associados com Localizador de Streaming.](filters-concept.md#associating-filters-with-streaming-locator)

## <a name="february-2019"></a>Fevereiro de 2019

Os Serviços de Comunicação Social v3 são agora apoiados nas nuvens nacionais do Azure. Nem todas as funcionalidades estão disponíveis em todas as nuvens ainda. Para mais detalhes, consulte [Nuvens e regiões em que existem V3 da Azure Media Services](azure-clouds-regions.md).

O evento [Microsoft.Media.JobOutputProgress](monitoring/media-services-event-schemas.md#monitoring-job-output-progress) foi adicionado aos esquemas da Grelha de Eventos Azure para serviços de media.

## <a name="january-2019"></a>Janeiro de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard e MPI ficheiros 

Ao codificar com a Media Encoder Standard para produzir ficheiros MP4, é gerado um novo ficheiro .mpi e adicionado à saída Ativo. Este ficheiro MPI destina-se a melhorar o desempenho para cenários [dinâmicos de embalagem](dynamic-packaging-overview.md) e streaming.

Não deve modificar ou remover o ficheiro MPI, nem assumir qualquer dependência no seu serviço sobre a existência (ou não) de tal ficheiro.

## <a name="december-2018"></a>Dezembro de 2018

As atualizações da versão GA da API V3 incluem:
       
* As propriedades **PresentationTimeRange** já não são 'necessárias' para **filtros de ativos** e **filtros de conta.** 
* As opções de consulta $top e $skip para **Jobs** and Transforms foram **removidas** e $orderby foi adicionada. Como parte da adição da nova funcionalidade de encomendas, descobriu-se que as opções $top e $skip tinham sido acidentalmente expostas anteriormente, mesmo que não fossem implementadas.
* A extensibilidade da enumeração foi re-activada. Esta funcionalidade foi ativada nas versões de pré-visualização do SDK e foi acidentalmente desativada na versão GA.
* Duas políticas de streaming predefinidas foram renomeadas. **O SecureStreaming** é agora **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** está agora **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembro de 2018

O módulo CLI 2.0 já está disponível para [Azure Media Services v3 GA](/cli/azure/ams) – v 2.0.50.

### <a name="new-commands"></a>Novos comandos

- [conta az ams](/cli/azure/ams/account)
- [az ams filtro de conta](/cli/azure/ams/account-filter)
- [az ams ativo](/cli/azure/ams/asset)
- [az ams filtro de ativos](/cli/azure/ams/asset-filter)
- [az ams conteúdo chave-chave-política](/cli/azure/ams/content-key-policy)
- [trabalho az ams](/cli/azure/ams/job)
- [az ams live-event](/cli/azure/ams/live-event)
- [az ams live-output](/cli/azure/ams/live-output)
- [az ams streaming-endpoint](/cli/azure/ams/streaming-endpoint)
- [az ams streaming-localizador](/cli/azure/ams/streaming-locator)
- [az ams account mru](/cli/azure/ams/account/mru) - permite-lhe gerir Unidades Reservadas de Mídia. Para mais informações, consulte [as Unidades Reservadas para Os Meios de Escala.](media-reserved-units-cli-how-to.md)

### <a name="new-features-and-breaking-changes"></a>Novas funcionalidades e mudanças de rutura

#### <a name="asset-commands"></a>Comandos de ativos

- ```--storage-account``` e ```--container``` argumentos adicionados.
- Valores predefinidos para o tempo de validade (Agora+23h) e permissões (Ler) no ```az ams asset get-sas-url``` comando adicionados.

#### <a name="job-commands"></a>Comandos de emprego

- ```--correlation-data``` e ```--label``` argumentos adicionados
- ```--output-asset-names``` renomeado para ```--output-assets``` . Agora aceita uma lista de ativos separados pelo espaço no formato 'assetName=label'. Um ativo sem etiqueta pode ser enviado assim: 'assetName='.

#### <a name="streaming-locator-commands"></a>Comandos de localização de streaming

- ```az ams streaming locator``` comando de base substituído por ```az ams streaming-locator``` .
- ```--streaming-locator-id``` e ```--alternative-media-id support``` argumentos adicionados.
- ```--content-keys argument``` argumento atualizado.
- ```--content-policy-name``` renomeado para ```--content-key-policy-name``` .

#### <a name="streaming-policy-commands"></a>Comandos de Política de Streaming

- ```az ams streaming policy``` comando de base substituído por ```az ams streaming-policy``` .
- Suporte de parâmetros de encriptação ```az ams streaming-policy create``` adicionados.

#### <a name="transform-commands"></a>Transformar comandos

- ```--preset-names``` argumento substituído por ```--preset``` . Agora só pode definir 1 saída/predefinição de cada vez (para adicionar mais tem de ```az ams transform output add``` correr). Além disso, pode definir StandardEncoderPreset personalizado passando o caminho para o seu JSON personalizado.
- ```az ams transform output remove``` pode ser realizado passando o índice de saída para remover.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumentos adicionados ```az ams transform create``` e ```az ams transform output add``` comandos.

## <a name="october-2018---ga"></a>Outubro 2018 - GA

Esta secção descreve atualizações de outubro da Azure Media Services (AMS).

### <a name="rest-v3-ga-release"></a>Rest v3 GA lançamento

A [versão REST v3 GA](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs para filtros manifestos de nível de conta/ativo e suporte a DRM.

#### <a name="azure-resource-management"></a>Gestão de Recursos Azure 

O suporte à Gestão de Recursos Azure permite a API de gestão e operações unificadas (agora tudo num só local).

A partir desta versão, pode utilizar modelos de Gestor de Recursos para criar Eventos Ao Vivo.

#### <a name="improvement-of-asset-operations"></a>Melhoria das operações de ativos 

Foram introduzidas as seguintes melhorias:

- Ingerir em URLs HTTP ou Azure Blob Storage SAS URLs.
- Especifique os seus próprios nomes de contentores para Ativos. 
- Suporte de saída mais fácil para criar fluxos de trabalho personalizados com Funções Azure.

#### <a name="new-transform-object"></a>Novo objeto de transformação

O novo objeto **Transform** simplifica o modelo de codificação. O novo objeto facilita a criação e partilha de modelos e predefinições do Gestor de Recursos de Codificação. 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Autenticação do Azure Ative Directory e Azure RBAC

Azure AD Authentication and Azure role-based access control (Azure RBAC) enable secure Transforms, LiveEvents, Content Key Policies, or Assets by Role or Users in Azure AD.

#### <a name="client-sdks"></a>SDKs do Cliente  

Línguas suportadas em Media Services v3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Atualizações de codificação ao vivo

São introduzidas as seguintes atualizações de codificação ao vivo:

- Novo modo de baixa latência para viver (10 segundos de ponta a ponta).
- Melhor suporte de RTMP (maior estabilidade e mais suporte ao codificação de fontes).
- RTMPS ingerindo seguro.

    Quando crias um Evento Ao Vivo, recebes agora 4 URLs ingeridos. Os 4 URLs ingering são quase idênticos, têm o mesmo token de streaming (AppId), apenas a parte do número da porta é diferente. Dois dos URLs são primários e de reserva para RTMPS. 
- Suporte de transcoding 24 horas. 
- Melhor suporte de sinalização de anúncios em RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Suporte melhorado da grelha de eventos

Pode ver as seguintes melhorias no suporte da Grelha de Eventos:

- Integração da Grelha de Eventos Azure para um desenvolvimento mais fácil com apps lógicas e funções Azure. 
- Inscreva-se para eventos na Codificação, Canais Ao Vivo e muito mais.

### <a name="cmaf-support"></a>Suporte cmaf

CMAF e suporte de encriptação 'cbcs' para os jogadores Apple HLS (iOS 11+) e MPEG-DASH que suportam a CMAF.

### <a name="video-indexer"></a>Video Indexer

O lançamento do Video Indexer GA foi anunciado em agosto. Para obter novas informações sobre funcionalidades atualmente suportadas, consulte [O Que é Indexer de Vídeo](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json). 

### <a name="plans-for-changes"></a>Planos para alterações

#### <a name="azure-cli-20"></a>CLI 2.0 do Azure
 
O módulo Azure CLI 2.0 que inclui operações em todas as funcionalidades (incluindo Live, Políticas chave de conteúdo, filtros de conta/ativos, políticas de streaming) está para breve. 

### <a name="known-issues"></a>Problemas conhecidos

Apenas os clientes que utilizaram a API de pré-visualização para Ativos ou Filtros de Conta são impactados pelo seguinte problema.

Se criou Ativos ou Filtros de Conta entre 09/28 e 10/12 com Serviços de Mídia v3 CLI ou APIs, precisa remover todos os Filtros de Ativos e Contas e recobri-los devido a um conflito de versão. 

## <a name="may-2018---preview"></a>Maio 2018 - Pré-visualização

### <a name="net-sdk"></a>SDK .NET

As seguintes funcionalidades estão presentes no .NET SDK:

* **Transforma** e **Empregos** para codificar ou analisar conteúdos de mídia. Por exemplo, consulte [os ficheiros stream](stream-files-tutorial-with-api.md) e [analise](analyze-videos-tutorial-with-api.md).
* **Localizadores de streaming** para publicação e streaming de conteúdos para dispositivos de utilizador final
* **Políticas de streaming** e **políticas de chave de conteúdo** para configurar a entrega de chaves e proteção de conteúdos (DRM) ao fornecer conteúdo.
* **Live Events** e **Live Outputs** para configurar a ingestão e arquivamento de conteúdos de streaming ao vivo.
* **Ativos** para armazenar e publicar conteúdos de mídia no Azure Storage. 
* **Streaming Endpoints** para configurar e escalar embalagem dinâmica, encriptação e streaming para conteúdos de mídia ao vivo e on-demand.

### <a name="known-issues"></a>Problemas conhecidos

* Ao submeter um trabalho, pode especificar para ingerir o seu vídeo de origem utilizando URLs HTTPS, URLs SAS ou caminhos para ficheiros localizados no armazenamento Azure Blob. Atualmente, a Media Services v3 não suporta a codificação de transferências em fatias sobre URLs HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="see-also"></a>Ver também

[Orientação de migração para passar dos Serviços de Comunicação v2 para v3](migrate-v-2-v-3-migration-introduction.md).

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral](media-services-overview.md)
- [Media Services v2 notas de lançamento](../previous/media-services-release-notes.md)