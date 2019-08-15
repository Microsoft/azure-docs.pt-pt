---
title: Visão geral da transmissão ao vivo usando os serviços de mídia do Azure | Microsoft Docs
description: Este tópico fornece uma visão geral da transmissão ao vivo usando os serviços de mídia do Azure.
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
ms.openlocfilehash: 5ab4a6b96df964497e20b2b93c59febb0e24393c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035890"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Visão geral da transmissão ao vivo usando os serviços de mídia

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Descrição geral

Ao fornecer eventos de transmissão ao vivo com os serviços de mídia do Azure, os seguintes componentes estão normalmente envolvidos:

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

Com os serviços de mídia, você pode aproveitar o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), que permite que você transmita suas transmissões ao vivo nos formatos MPEG Dash, HLS e Smooth streaming do feed de contribuição que está sendo enviado para o serviço. Seus visualizadores podem reproduzir a transmissão ao vivo com qualquer player compatível com HLS, DASH ou Smooth Streaming. Você pode usar Player de Mídia do Azure em seus aplicativos Web ou móveis para entregar seu fluxo em qualquer um desses protocolos.

> [!NOTE]
> A partir de 12 de maio de 2018, os canais ao vivo não serão mais compatíveis com o protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Migre do RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Pontos de extremidade de streaming, canais, programas

Nos Media Services do Azure, os **Canais**, **Programas** e **Pontos Finais de Transmissão em Fluxo** lidam com todas as funcionalidades de transmissão em fluxo em direto, incluindo inserção, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. Um Canal pode receber transmissões em fluxo de entrada em direto das seguintes formas:

* Um codificador em direto no local envia um **RTMP** ou uma **Transmissão em Fluxo Uniforme** com velocidade de transmissão múltipla (MP4 fragmentado) para o Canal configurado para distribuição **pass-through**. A distribuição **pass-through** ocorre quando as transmissões em fluxo inseridas passam pelos **Canais** sem qualquer processamento adicional. Você pode usar os codificadores dinâmicos a seguir que geram Smooth Streaming de múltiplas taxas de bits: MediaExcel, Ateme, imagine Communications, envivio, Cisco e Elemental. Os seguintes codificadores dinâmicos produzem RTMP: O Adobe Flash Media Live encoder (FMLE), o Telestream Wirecast, o HaiVision, o Teradek e o tricastr transcodificadores.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Um codificador ao vivo local envia um fluxo de taxa de bits única para o canal que está habilitado para executar a codificação ativa com os serviços de mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). Os seguintes codificadores dinâmicos com saída RTMP são conhecidos por trabalhar com canais desse tipo: Telestream Wirecast, FMLE. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

A partir da versão 2,10 dos serviços de mídia, ao criar um canal, você pode especificar de que maneira deseja que o canal receba o fluxo de entrada e se deseja ou não que o canal execute a codificação ativa de seu fluxo. Tem duas opções:

* **Nenhum** (passagem) – Especifique esse valor se você planeja usar um codificador ao vivo local que produzirá fluxo de múltiplas taxas de bits (um fluxo de passagem). Nesse caso, o fluxo de entrada passou para a saída sem nenhuma codificação. Esse é o comportamento de um canal antes da versão 2,10.  
* **Padrão** – escolha esse valor se você planeja usar os serviços de mídia para codificar sua transmissão ao vivo de taxa de bits única para fluxo de múltiplas taxas de bits. Esse método é mais econômico para escalar verticalmente rapidamente para eventos infrequentes. Lembre-se de que há um impacto de cobrança para a codificação ativa e você deve se lembrar de que deixar um canal de codificação ativa no estado "em execução" incorrerá em encargos de cobrança.  É recomendável parar imediatamente os canais em execução após a conclusão do evento de transmissão ao vivo para evitar cobranças por hora extra.

## <a name="comparison-of-channel-types"></a>Comparação de tipos de canal

A tabela a seguir fornece um guia para comparar os dois tipos de canal com suporte nos serviços de mídia

| Funcionalidade | Canal de passagem | Canal Standard |
| --- | --- | --- |
| A entrada de taxa de bits única é codificada em várias taxas de bits na nuvem |Não |Sim |
| Resolução máxima, número de camadas |1080p, 8 camadas, 60 + fps |720p, 6 camadas, 30 fps |
| Protocolos de entrada |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Preço |Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia "vídeo ao vivo" |Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) |
| Tempo de execução máximo |Permanente (24x7) |8 horas |
| Suporte para inserção de slates |Não |Sim |
| Suporte para sinalização de anúncios |Não |Sim |
| Legendas CEA 608/708 de passagem |Sim |Sim |
| Suporte para GOPS segundos de entrada não uniforme |Sim |Não – a entrada deve ser corrigida 2sec GOPS segundos |
| Suporte para entrada de taxa de quadros variável |Sim |Não – a entrada deve ser uma taxa de quadros fixa.<br/>Pequenas variações são toleradas, por exemplo, durante cenas de movimento alto. Mas o codificador não pode cair para 10 quadros/s. |
| Desligamento automático de canais quando o feed de entrada é perdido |Não |Após 12 horas, se não houver nenhum programa em execução |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com Canais que recebem transmissões em fluxo em direto com velocidade de transmissão múltipla a partir de codificadores no local (pass-through)

O diagrama seguinte mostra as principais partes da plataforma de AMS envolvidas no fluxo de trabalho de **pass-through**.

![Fluxo de trabalho em direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhar com Canais que recebem transmissões em Fluxo em Direto de Múltipla Velocidade de Transmissão a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com Canais ativados para realizar live encoding com Media Services do Azure

O diagrama seguinte mostra as partes principais da plataforma do AMS envolvidas no fluxo de trabalho de Transmissão em Fluxo em Direto onde um Canal é ativado para realizar live encoding comMedia Services.

![Fluxo de trabalho em direto](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Descrição de um canal e seus componentes relacionados

### <a name="channel"></a>Canal

Nos serviços de mídia, o [canal](https://docs.microsoft.com/rest/api/media/operations/channel)s é responsável por processar conteúdo de transmissão ao vivo. Um canal fornece um ponto de extremidade de entrada (URL de ingestão) que você então fornece a um transcodificador ao vivo. O canal recebe fluxos de entrada ao vivo do transcodificador ao vivo e o torna disponível para streaming por meio de um ou mais StreamingEndpoints. Os canais também fornecem um ponto de extremidade de visualização (URL de visualização) que você usa para visualizar e validar seu fluxo antes do processamento e da entrega.

Você pode obter a URL de ingestão e a URL de visualização ao criar o canal. Para obter essas URLs, o canal não precisa estar no estado iniciado. Quando você estiver pronto para começar a enviar dados de um transcodificador ao vivo para o canal, o canal deverá ser iniciado. Depois que o transcodificador ao vivo inicia a ingestão de dados, você pode visualizar seu fluxo.

Cada conta dos serviços de mídia pode conter vários canais, vários programas e vários StreamingEndpoints. Dependendo da largura de banda e das necessidades de segurança, os serviços StreamingEndpoints podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode efetuar pull de qualquer canal.

Ao criar um canal, você pode especificar endereços IP permitidos em um dos seguintes formatos: Endereço IpV4 com 4 números, intervalo de endereços CIDR.

### <a name="program"></a>Programa
Um [programa](https://docs.microsoft.com/rest/api/media/operations/program) permite que você controle a publicação e o armazenamento de segmentos em uma transmissão ao vivo. Canais gerem Programas. A relação entre o Canal e o Programa é muito semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.
Você pode especificar o número de horas que deseja manter o conteúdo registrado para o programa, definindo a propriedade **ArchiveWindowLength** . Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

O ArchiveWindowLength também determina a quantidade máxima de tempo que os clientes podem buscar de volta no tempo a partir da posição atual ao vivo. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor dessa propriedade também determina por quanto tempo os manifestos do cliente podem crescer.

Cada programa está associado um Elemento. Para publicar o programa, você deve criar um localizador para o ativo associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

## <a name="billing-implications"></a>Implicações de cobrança
Um canal começa a cobrança assim que é transição de estado para "em execução" por meio da API.  

A tabela a seguir mostra como os Estados de canal são mapeados para os Estados de cobrança na API e portal do Azure. Observe que os Estados são ligeiramente diferentes entre a API e o UX do Portal. Assim que um canal estiver no estado "em execução" por meio da API, ou no estado "pronto" ou "streaming" na portal do Azure, a cobrança estará ativa.

Para impedir que o canal faça uma cobrança adicional, você precisa interromper o canal por meio da API ou na portal do Azure.
Você é responsável por parar seus canais quando terminar com o canal. A falha ao parar o canal resultará na cobrança contínua.

> [!NOTE]
> Ao trabalhar com canais padrão, o AMS desligamento automaticamente qualquer canal que ainda esteja no estado "em execução" 12 horas após o feed de entrada ser perdido e não há programas em execução. No entanto, você ainda será cobrado pela hora em que o canal estava no estado "em execução".
>
>

### <a id="states"></a>Estados de canal e como eles são mapeados para o modo de cobrança
O estado atual de um Canal. Os valores possíveis incluem:

* **Parado**. Esse é o estado inicial do canal após sua criação (a menos que AutoStart tenha sido selecionado no Portal). Nenhuma cobrança ocorre nesse estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **Iniciando**. O Canal está a ser iniciado. Nenhuma cobrança ocorre nesse estado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Caso ocorra um erro, o Canal volta para o estado Parado.
* **Em execução**. O Canal é capaz de processar transmissões em fluxo. Agora é o uso de cobrança. Você deve parar o canal para evitar cobrança adicional.
* **Parando**. O Canal está a ser parado. Nenhuma cobrança ocorre nesse estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **Excluindo**. O Canal está a ser apagado. Nenhuma cobrança ocorre nesse estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um Canal mapeiam para o modo de faturação.

| Estado do canal | Indicadores IU do portal | É cobrança? |
| --- | --- | --- |
| A iniciar |A iniciar |Não (estado transitório) |
| A executar |Pronto (nenhum programa em execução)<br/>ou<br/>A Transmitir em fluxo (pelo menos um programa em execução) |SIM |
| A parar |A parar |Não (estado transitório) |
| Parada |Parada |Não |

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Tópicos relacionados
[Especificação de ingestão dinâmica de MP4 fragmentado dos serviços de mídia do Azure](../media-services-fmp4-live-ingest-overview.md)

[Trabalhando com canais habilitados para executar a codificação ativa com os serviços de mídia do Azure](media-services-manage-live-encoder-enabled-channels.md)

[Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local (Working with Channels that Receive Multi-bitrate Live Stream from On-premises Encoders)](media-services-live-streaming-with-onprem-encoders.md)

[Cotas e limitações](media-services-quotas-and-limitations.md).  

[Conceitos dos serviços de mídia](media-services-concepts.md)
