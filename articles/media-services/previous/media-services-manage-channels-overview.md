---
title: Visão geral do Live Streaming usando a Azure Media Services | Microsoft Docs
description: Este artigo fornece uma visão geral do streaming ao vivo usando o Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 195333f5f06145a1dfa84f0de2c5984190e3d7ca
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014830"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Visão geral do Live Streaming usando serviços de mídia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

## <a name="overview"></a>Descrição Geral

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

Com os Media Services, pode usufruir de [embalagens dinâmicas,](media-services-dynamic-packaging-overview.md)que lhe permitem transmitir os seus streams ao vivo nos formatos MPEG DASH, HLS e Smooth Streaming a partir do feed de contribuição que está a ser enviado para o serviço. Os seus espectadores podem reproduzir o live stream com qualquer hls, DASH ou jogadores compatíveis com o Streaming Suave. Pode utilizar o Azure Media Player na sua web ou aplicações móveis para entregar o seu stream em qualquer um destes protocolos.

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo deixarão de suportar o protocolo de transmissão rtp/MPEG-2. Por favor, migrar de PROTOCOLOs de ingestão RTP/MPEG-2 para RTMP ou MP4 (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Streaming Endpoints, Canais, Programas

Nos Media Services do Azure, os **Canais**, **Programas** e **Pontos Finais de Transmissão em Fluxo** lidam com todas as funcionalidades de transmissão em fluxo em direto, incluindo inserção, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. Um Canal pode receber transmissões em fluxo de entrada em direto das seguintes formas:

* Um codificador em direto no local envia um **RTMP** ou uma **Transmissão em Fluxo Uniforme** com velocidade de transmissão múltipla (MP4 fragmentado) para o Canal configurado para distribuição **pass-through**. A distribuição **pass-through** ocorre quando as transmissões em fluxo inseridas passam pelos **Canais** sem qualquer processamento adicional. Pode utilizar os seguintes codificadores ao vivo que produzem streaming suave multi-bitrate: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores ao vivo produção RTMP: Telestream Wirecast, Haivision, Teradek transcoders.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Um codificador ao vivo no local envia um fluxo de bitrate único para o Canal que está habilitado a realizar codificação ao vivo com os Media Services num dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). Os seguintes codificadores ao vivo com saída RTMP são conhecidos por funcionarem com canais deste tipo: Telestream Wirecast. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

Começando com a versão Media Services 2.10, quando cria um Canal, pode especificar de que forma pretende que o seu canal receba o fluxo de entrada e se deseja ou não que o canal realize a codificação ao vivo do seu fluxo. Tem duas opções:

* **Nenhum** (pass-through) – Especifique este valor, se pretender utilizar um codificadora vivo no local que produza fluxo multi-bitrate (um fluxo de passagem). Neste caso, o fluxo de entrada passou para a saída sem qualquer codificação. Este é o comportamento de um Canal antes da libertação das 2.10.  
* **Standard** – Escolha este valor, se planeia utilizar os Media Services para codificar o seu fluxo de live stream bitrate único para fluxo multi-bitrate. Este método é mais económico para aumentar rapidamente para eventos pouco frequentes. Esteja ciente de que há um impacto de faturação para codificação ao vivo e deve lembrar-se que deixar um canal de codificação ao vivo no estado "Running" irá incorrer em taxas de faturação.  Recomenda-se que pare imediatamente os seus canais de funcionamento após o evento de streaming ao vivo estar completo para evitar custos extra por hora.

## <a name="comparison-of-channel-types"></a>Comparação dos Tipos de Canal

A tabela seguinte fornece um guia para comparar os dois tipos de Canal suportados nos Serviços de Media

| Funcionalidade | Canal de passagem | Canal Standard |
| --- | --- | --- |
| A entrada bitrate única é codificada em vários bitrates na nuvem |No |Yes |
| Resolução máxima, número de camadas |1080p, 8 camadas, 60+fps |720p, 6 camadas, 30 fps |
| Protocolos de entrada |RTMP, Streaming Suave |RTMP, Streaming Suave |
| Preço |Veja [a página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Live Video" |Consulte [a página de preços](https://azure.microsoft.com/pricing/details/media-services/) |
| Tempo máximo de execução |24x7 |8 horas |
| Suporte para inserção de ardósias |No |Yes |
| Suporte para sinalização de anúncios |No |Yes |
| Pass-through CEA 608/708 legendas |Yes |Yes |
| Suporte para GOPs de entrada não uniforme |Yes |Não – a entrada deve ser fixada 2sec GOPs |
| Suporte para entrada de taxa de fotogramas variáveis |Yes |Não – a entrada deve ser fixa.<br/>Pequenas variações são toleradas, por exemplo, durante cenas de movimento elevado. Mas o codificadores não pode cair para 10 fotogramas/seg. |
| Desligação automática dos canais quando o feed de entrada é perdido |No |Depois de 12 horas, se não houver programa em execução |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com Canais que recebem transmissões em fluxo em direto com velocidade de transmissão múltipla a partir de codificadores no local (pass-through)

O diagrama seguinte mostra as principais partes da plataforma de AMS envolvidas no fluxo de trabalho de **pass-through**.

![Diagrama que mostra as principais partes da plataforma A M S para o fluxo de trabalho "pass-through".](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhar com Canais que recebem transmissões em Fluxo em Direto de Múltipla Velocidade de Transmissão a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com Canais ativados para realizar live encoding com Media Services do Azure

O diagrama seguinte mostra as partes principais da plataforma do AMS envolvidas no fluxo de trabalho de Transmissão em Fluxo em Direto onde um Canal é ativado para realizar live encoding comMedia Services.

![Fluxo de trabalho em direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Descrição de um Canal e seus componentes relacionados

### <a name="channel"></a>Canal

Nos Serviços de Comunicação Social, [os Channel](/rest/api/media/operations/channel)s são responsáveis pelo processamento de conteúdos de streaming ao vivo. Um Canal fornece um ponto final de entrada (ingest URL) que fornece a um transcodificador vivo. O canal recebe streams de entrada ao vivo a partir do transcodificador ao vivo e disponibiliza-o para streaming através de um ou mais StreamingEndpoints. Os canais também fornecem um ponto final de pré-visualização (URL de pré-visualização) que utiliza para visualizar e validar o seu fluxo antes de ser processado e entregue.

Pode obter o URL de ingestão e o URL de pré-visualização quando criar o canal. Para obter estes URLs, o canal não tem que estar no estado iniciado. Quando estiver pronto para começar a empurrar dados de um transcodificador vivo para o canal, o canal tem de ser iniciado. Assim que o transcodificador vivo começar a ingerir dados, pode visualizar o seu fluxo.

Cada conta de Serviços de Mídia pode conter vários canais, vários programas e vários streamingEndpoints. Dependendo da largura de banda e das necessidades de segurança, os serviços de StreamingEndpoint podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode puxar de qualquer Canal.

Ao criar um Canal, pode especificar endereços IP autorizados num dos seguintes formatos: endereço IpV4 com 4 números, intervalo de endereços CIDR.

### <a name="program"></a>Programa
Um [Programa](/rest/api/media/operations/program) permite-lhe controlar a publicação e armazenamento de segmentos num live stream. Canais gerem Programas. A relação entre o Canal e o Programa é muito semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.
Pode especificar o número de horas que pretende reter o conteúdo gravado para o programa, definindo a propriedade **ArchiveWindowLength.** Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

O ArchiveWindowLength também dita o tempo máximo que os clientes podem procurar no tempo a partir da atual posição ao vivo. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor deste imóvel também determina quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um Elemento. Para publicar o programa tem de criar um localizador para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

## <a name="billing-implications"></a>Implicações de faturação
Um canal começa a faturar assim que é transição do estado para "Running" através da API.  

A tabela que se segue mostra como o Canal dos Estados mapeia para estados de faturação no portal API e Azure. Note que os estados são ligeiramente diferentes entre a API e o Portal UX. Assim que um canal estiver no estado "Running" através da API, ou no estado "Ready" ou "Streaming" no portal Azure, a faturação estará ativa.

Para impedir que o Canal o faturar ainda mais, tem de parar o Canal através da API ou do portal Azure.
É responsável por parar os seus canais quando terminar o canal. Se não parar o canal resultará na continuação da faturação.

> [!NOTE]
> Ao trabalhar com os canais Standard, a AMS desligará automaticamente qualquer Canal que ainda esteja em estado de "Funcionamento" 12 horas após a perda do feed de entrada, e não há programas em execução. No entanto, ainda será cobrado pelo tempo em que o Canal esteve em estado de "Running".
>
>

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Os estados do canal e como mapeiam para o modo de faturação
O estado atual de um Canal. Valores possíveis incluem:

* **Parado.** Este é o estado inicial do Canal após a sua criação (a menos que o autostart tenha sido selecionado no portal.) Não há faturação neste estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas o streaming não é permitido.
* **A partir de**. O Canal está a começar. Não há faturação neste estado. Não são permitidas atualizações ou streamings durante este estado. Se ocorrer um erro, o Canal regressa ao estado Stop.
* **A correr.** O Canal é capaz de processar transmissões ao vivo. Está agora a cobrar o uso. Tens de parar o canal para evitar mais faturações.
* **Parar.** O Canal está a ser detido. Nenhuma faturação ocorre neste estado transitório. Não são permitidas atualizações ou streamings durante este estado.
* **Apagar.** O Canal está a ser apagado. Nenhuma faturação ocorre neste estado transitório. Não são permitidas atualizações ou streamings durante este estado.

A tabela que se segue mostra como o Channel afirma o mapa para o modo de faturação.

| Estado do canal | Indicadores de UI do Portal | É Billing? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| Em Execução |Pronto (sem programas de execução)<br/>ou<br/>Streaming (pelo menos um programa em execução) |SIM |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |No |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Azure Media Services Fragmentado MP4 Live Ingest Specification](../media-services-fmp4-live-ingest-overview.md)

[Trabalhar com canais que estão habilitados a executar a codificação ao vivo com os Serviços de Media Azure](media-services-manage-live-encoder-enabled-channels.md)

[Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md)

[Quotas e limitações.](media-services-quotas-and-limitations.md)  

[Conceitos de Serviços de Mídia](media-services-concepts.md)
