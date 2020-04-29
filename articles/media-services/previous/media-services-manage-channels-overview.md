---
title: Visão geral do Live Streaming utilizando os Serviços De Mídia Azure [ Microsoft Docs
description: Este artigo dá uma visão geral do streaming ao vivo usando o Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f875b4a5c4f1322f4a992dc3738ab1ce6431149d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641128"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Visão geral do Live Streaming usando os Serviços de Media

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Descrição geral

Ao entregar eventos de streaming ao vivo com a Azure Media Services, os seguintes componentes estão geralmente envolvidos:

* Uma câmara, que é utilizada para difundir um evento.
* Um codificador vídeo em direto que converte sinais da câmara para transmissões em fluxos que são enviadas para um serviço de transmissão em fluxo em direto.

    Opcionalmente, vários codificadores com sincronização de hora em direto. Para determinados eventos críticos em direto que exigem uma enorme disponibilidade e qualidade da experiência, é recomendado utilizar codificadores ativo-ativo com sincronização de hora para obter uma ativação pós-falha totalmente integrada sem perda de dados.
* Um serviço de transmissão em fluxo em direto que permite realizar o seguinte:

  * inserir conteúdos em direto utilizando vários protocolos de transmissão em fluxo em direto (por exemplo, RTMP ou Transmissão em Fluxo Uniforme)
  * (opcionalmente) codificação da sua transmissão para uma transmissão em fluxo de velocidade de transmissão adaptável
  * pré-visualizar a sua transmissão em fluxo em direto
  * gravar e armazenar os conteúdos inseridos de forma a transmiti-los em fluxo posteriormente (Vídeo a Pedido)
  * distribuir os conteúdos através de protocolos de transmissão em fluxo comuns (por exemplo, MPEG DASH, Uniforme, HLS) diretamente para os seus clientes ou para uma Rede de Entrega de Conteúdos (CDN) para uma maior distribuição.

Os **Media Services do Microsoft Azure** (AMS) fornecem a capacidade de inserir, codificar, pré-visualizar, armazenar e distribuir os seus conteúdos por transmissão em fluxo em direto.

Com os Media Services, pode usufruir de [embalagens dinâmicas,](media-services-dynamic-packaging-overview.md)que lhe permitem transmitir os seus streams ao vivo em formatos MPEG DASH, HLS e Smooth Streaming a partir do feed de contribuição que está a ser enviado para o serviço. Os seus espectadores podem reproduzir o live stream com quaisquer jogadores compatíveis hls, DASH ou Smooth Streaming. Pode utilizar o Azure Media Player nas suas aplicações web ou móveis para entregar o seu stream em qualquer um destes protocolos.

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo deixarão de apoiar o protocolo de transporte sintetizador da RTP/MPEG-2. Por favor, emigrar de PROTOCOLOS de ingerir RTP/MPEG-2 para RTMP ou MP4 fragmentados (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Pontos finais de streaming, canais, programas

Nos Media Services do Azure, os **Canais**, **Programas** e **Pontos Finais de Transmissão em Fluxo** lidam com todas as funcionalidades de transmissão em fluxo em direto, incluindo inserção, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. Um Canal pode receber transmissões em fluxo de entrada em direto das seguintes formas:

* Um codificador em direto no local envia um **RTMP** ou uma **Transmissão em Fluxo Uniforme** com velocidade de transmissão múltipla (MP4 fragmentado) para o Canal configurado para distribuição **pass-through**. A distribuição **pass-through** ocorre quando as transmissões em fluxo inseridas passam pelos **Canais** sem qualquer processamento adicional. Pode utilizar os seguintes codificadores ao vivo que produzem streaming liso multibitado: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores de saída RTMP: Telestream Wirecast, Haivision, Transcoders Teradek.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Um codificador ao vivo no local envia um fluxo de bitrate único para o Canal que está habilitado a realizar codificação ao vivo com os Media Services num dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). Os seguintes codificadores ao vivo com saída RTMP são conhecidos por funcionarem com canais deste tipo: Telestream Wirecast. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

A partir do lançamento do Media Services 2.10, quando criar um Canal, pode especificar de que forma pretende que o seu canal receba o fluxo de entrada e se pretende ou não que o canal realize codificação ao vivo do seu stream. Tem duas opções:

* **Nenhum** (pass-through) – Especifique este valor, se pretender utilizar um codificador ao vivo no local que irá obter fluxo multibitado (um fluxo de passagem). Neste caso, o fluxo de entrada passou para a saída sem qualquer codificação. Este é o comportamento de um Canal antes do lançamento das 2:10.  
* **Standard** – Escolha este valor, se planeia utilizar os Serviços de Media para codificar o seu único fluxo bitrate ao fluxo multibitado. Este método é mais económico para escalonar rapidamente para eventos pouco frequentes. Esteja ciente de que há um impacto de faturação para codificação ao vivo e deve lembrar-se que deixar um canal de codificação ao vivo no estado de "Running" incorrerá em taxas de faturação.  Recomenda-se que pare imediatamente os seus canais de funcionamento após o seu evento de streaming ao vivo estar completo para evitar encargos extra por hora.

## <a name="comparison-of-channel-types"></a>Comparação de tipos de canais

A tabela seguinte fornece um guia para comparar os dois tipos de Canal suportados nos Serviços de Media

| Funcionalidade | Canal pass-through | Canal Padrão |
| --- | --- | --- |
| A entrada única de bitrate é codificada em várias bitrates na nuvem |Não |Sim |
| Resolução máxima, número de camadas |1080p, 8 camadas, 60+fps |720p, 6 camadas, 30 fps |
| Protocolos de entrada |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Preço |Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Live Video" |Ver a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) |
| Tempo máximo de execução |24x7 |8 horas |
| Suporte para inserção de ardósias |Não |Sim |
| Suporte para sinalização de anúncios |Não |Sim |
| Legendas pass-through CEA 608/708 |Sim |Sim |
| Suporte para GOPs de entrada não uniformes |Sim |Não – a entrada deve ser fixada 2sec GOPs |
| Suporte para entrada de taxa de fotogramas variável |Sim |Não – a entrada deve ser fixada.<br/>Pequenas variações são toleradas, por exemplo, durante cenas de movimento elevado. Mas o codificador não pode cair para 10 fotogramas/seg. |
| Desligamento automático dos Canais quando se perde o feed de entrada |Não |Depois de 12 horas, se não houver programa em execução |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com Canais que recebem transmissões em fluxo em direto com velocidade de transmissão múltipla a partir de codificadores no local (pass-through)

O diagrama seguinte mostra as principais partes da plataforma de AMS envolvidas no fluxo de trabalho de **pass-through**.

![Fluxo de trabalho em direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhar com Canais que recebem transmissões em Fluxo em Direto de Múltipla Velocidade de Transmissão a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com Canais ativados para realizar live encoding com Media Services do Azure

O diagrama seguinte mostra as partes principais da plataforma do AMS envolvidas no fluxo de trabalho de Transmissão em Fluxo em Direto onde um Canal é ativado para realizar live encoding comMedia Services.

![Fluxo de trabalho em direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Descrição de um Canal e seus componentes relacionados

### <a name="channel"></a>Canal

Nos Serviços de Media, [o Channel](https://docs.microsoft.com/rest/api/media/operations/channel)s é responsável pelo processamento de conteúdos de streaming ao vivo. Um Canal fornece um ponto final de entrada (URL ingerir) que fornece a um transcodificador vivo. O canal recebe streams de entrada ao vivo do transcodificador ao vivo e disponibiliza-o para streaming através de um ou mais StreamingEndpoints. Os canais também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para pré-visualizar e validar o seu fluxo antes de continuar a processar e a entregar.

Pode obter o URL ingerir e o URL de pré-visualização quando criar o canal. Para obter estes URLs, o canal não tem que estar no estado de partida. Quando estiver pronto para começar a empurrar dados de um transcodificador vivo para o canal, o canal deve ser iniciado. Assim que o transcodificador ao vivo começar a ingerir dados, pode pré-visualizar o seu fluxo.

Cada conta de Media Services pode conter vários Canais, vários Programas e vários StreamingEndpoints. Dependendo da largura de banda e das necessidades de segurança, os serviços de StreamingEndpoint podem ser dedicados a um ou mais canais. Qualquer Ponto De StreamingEnd pode puxar de qualquer Canal.

Ao criar um Canal, pode especificar endereços IP permitidos num dos seguintes formatos: endereço IpV4 com 4 números, intervalo de endereços CIDR.

### <a name="program"></a>Programa
Um [Programa](https://docs.microsoft.com/rest/api/media/operations/program) permite controlar a publicação e armazenamento de segmentos em live stream. Canais gerem Programas. A relação entre o Canal e o Programa é muito semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.
Pode especificar o número de horas que pretende reter o conteúdo gravado para o programa, definindo a propriedade **ArchiveWindowLength.** Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

O ArchiveWindowLength também dita o tempo máximo que os clientes podem procurar no tempo a partir da atual posição ao vivo. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor deste imóvel também determina quanto tempo os manifestos de cliente podem crescer.

Cada programa está associado um Elemento. Para publicar o programa tem de criar um localizador para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

## <a name="billing-implications"></a>Implicações de faturação
Um canal começa a faturar assim que é as transições estatais para "Running" através da API.  

A tabela que se segue mostra como os estados do Canal mapeiam os estados de faturação no portal API e Azure. Note que os estados são ligeiramente diferentes entre a API e o Portal UX. Assim que um canal estiver no estado "Running" através da API, ou no estado "Ready" ou "Streaming" no portal Azure, a faturação estará ativa.

Para impedir que o Canal o faça mais longe, tem de parar o Canal através da API ou no portal Azure.
É responsável por parar os seus canais quando terminar com o canal. A não paragem do canal resultará na continuação da faturação.

> [!NOTE]
> Ao trabalhar com os canais Standard, a AMS irá desligar automaticamente qualquer Canal que ainda esteja em estado de "Execução" 12 horas após a perda do feed de entrada, e não há programas em execução. No entanto, ainda será cobrado pelo tempo em que o Canal estava em estado de "Running".
>
>

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Estados do canal e como mapeiam para o modo de faturação
O estado atual de um Canal. Valores possíveis incluem:

* **Parou.** Este é o estado inicial do Canal após a sua criação (a menos que o autoarranque tenha sido selecionado no portal.) Não há faturação neste estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas o streaming não é permitido.
* **A partir de**. O Canal está a ser iniciado. Não há faturação neste estado. Não são permitidas atualizações ou streaming durante este estado. Se ocorrer um erro, o Canal regressa ao estado de parada.
* **A correr.** O Canal é capaz de processar transmissões ao vivo. Está agora a cobrar o uso. Tem de parar o canal para evitar mais faturações.
* **Parando.** O Canal está a ser detido. Não ocorre nenhuma faturação neste estado transitório. Não são permitidas atualizações ou streaming durante este estado.
* **Apagar.** O Canal está a ser apagado. Não ocorre nenhuma faturação neste estado transitório. Não são permitidas atualizações ou streaming durante este estado.

A tabela que se segue mostra como os estados do Canal mapeiam para o modo de faturação.

| Estado do canal | Indicadores do Portal UI | É billing? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| A executar |Pronto (sem programas de execução)<br/>ou<br/>Streaming (pelo menos um programa de execução) |SIM |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |Não |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Especificação de ingest de mídia azure fragmentada MP4](../media-services-fmp4-live-ingest-overview.md)

[Trabalhar com canais que estão habilitados a realizar encodificação ao vivo com os Serviços De Mídia Azure](media-services-manage-live-encoder-enabled-channels.md)

[Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md)

[Quotas e limitações.](media-services-quotas-and-limitations.md)  

[Conceitos de Serviços de Mídia](media-services-concepts.md)
