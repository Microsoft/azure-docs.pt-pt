---
title: Descrição geral da transmissão em direto através dos serviços de multimédia do Azure | Documentos da Microsoft
description: Este tópico apresenta uma descrição geral da transmissão em direto através dos serviços de multimédia do Azure.
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
ms.openlocfilehash: a9d0daaacb046df7943202775adc77bc912cce11
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189517"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Descrição geral da transmissão em direto utilizando os Media Services

> [!NOTE]
> A partir de 12 de Maio de 2018, os canais em direto será já não suporte o fluxo de transporte RTP/MPEG-2 protocolo de ingestão. Migre de RTP/MPEG-2 para RTMP ou MP4 fragmentado (Smooth Streaming) protocolos de ingestão.

## <a name="overview"></a>Descrição geral

Os seguintes componentes estão normalmente relacionados ao entregar eventos de transmissão em fluxo em direto com Media Services do Azure:

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

Quando distribui os seus conteúdos aos seus clientes, o seu objetivo é fornecer um vídeo de alta qualidade para vários dispositivos em condições de rede diferentes. Para conseguir isso, utilize codificadores em direto para codificar a sua transmissão em fluxo para um fluxo de vídeo do múltipla (velocidade de transmissão adaptável).  Para realizar a transmissão em fluxo para dispositivos diferentes, utilize o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md) dos Media Services para empacotar novamente e de forma dinâmica a sua transmissão em fluxo para protocolos diferentes. Serviços de multimédia suportam a distribuição das seguinte velocidade de transmissão adaptável tecnologias de transmissão em fluxo: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

Nos Media Services do Azure, os **Canais**, **Programas** e **Pontos Finais de Transmissão em Fluxo** lidam com todas as funcionalidades de transmissão em fluxo em direto, incluindo inserção, formatação, DVR, segurança, escalabilidade e redundância.

Um **Canal** representa um pipeline de processamento de conteúdos de transmissão em fluxo em direto. Um Canal pode receber transmissões em fluxo de entrada em direto das seguintes formas:

* Um codificador em direto no local envia um **RTMP** ou uma **Transmissão em Fluxo Uniforme** com velocidade de transmissão múltipla (MP4 fragmentado) para o Canal configurado para distribuição **pass-through**. A distribuição **pass-through** ocorre quando as transmissões em fluxo inseridas passam pelos **Canais** sem qualquer processamento adicional. Pode utilizar os seguintes codificadores em direto que múltipla transmissão em fluxo uniforme de saída: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco e Elemental. Os seguintes codificadores em direto transmitem RTMP: Transcodificadores Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek e Tricaster.  Um codificador em direto pode também enviar uma transmissão em fluxo de velocidade de transmissão única para um canal, que não está ativado para live encoding, mas tal não é recomendado. Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

  > [!NOTE]
  > A utilização de um método pass-through é a forma mais económica de realizar uma transmissão em fluxo em direto quando estiver a realizar vários eventos durante um longo período de tempo e já investiu em codificadores no local. Consulte os detalhes dos [preços](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Um codificador em direto no local envia um fluxo de velocidade de transmissão única para o canal ativado para realizar live encoding com Media Services dos seguintes formatos: RTMP ou transmissão em fluxo uniforme (MP4 fragmentado). Os seguintes codificadores em direto com a saída RTMP são conhecidos para trabalhar com canais deste tipo: Telestream Wirecast, FMLE. O Canal, em seguida, realiza live encoding da transmissão em fluxo de velocidade de transmissão única de entrada para uma transmissão em fluxo de vídeo com várias velocidades (adaptável). Quando solicitado, os Media Services disponibilizam a transmissão em fluxo para os clientes.

Começando com o lançamento de 2.10 de serviços de multimédia, quando cria um canal, pode especificar de que forma pretende para o canal receber o fluxo de entrada e se é ou não desejar para o canal realizar live encoding da sua transmissão em fluxo. Tem duas opções:

* **Nenhum** (pass-through) – especifique esse valor, se planeja usar um codificador em direto no local, que serão de saída do fluxo de velocidade de transmissão (um fluxo de pass-through). Neste caso, o fluxo de entrada transmitida para a saída sem qualquer tipo de codificação. Este é o comportamento de um canal antes do lançamento 2.10.  
* **Padrão** – escolha esse valor, se planeja usar serviços de multimédia para codificar a transmissão em direto de velocidade de transmissão única para o fluxo de velocidade de transmissão. Esse método é mais econômico para aumentar verticalmente rapidamente para eventos pouco frequentes. Lembre-se de que há um impacto de faturação para live encoding e deve se lembrar de que a sair de um canal de codificação em direto no estado "Em execução" irá incorrer em custos de faturas.  Recomenda-se que parar imediatamente os seus canais em execução depois do evento de transmissão em fluxo em direto foi concluído para evitar custos adicionais por hora.

## <a name="comparison-of-channel-types"></a>Comparação de tipos de canais

A tabela seguinte fornece um guia para a comparação entre os dois tipos de canal suportados nos serviços de multimédia

| Funcionalidade | Canal pass-through | Canais Standard |
| --- | --- | --- |
| Velocidade de transmissão única entrada é codificada em múltiplas velocidades de transmissão na cloud |Não |Sim |
| Resolução máxima, número de camadas |1080p, 8 camadas, mais de 60 fps |720p, 6 camadas, 30 fps |
| Protocolos de entrada |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Preço |Consulte a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique no separador "Vídeo em direto" |Consulte o [página de preços](https://azure.microsoft.com/pricing/details/media-services/) |
| Tempo de execução máximo |24x7 |8 horas |
| Suporte para a inserção de slates |Não |Sim |
| Suporte para a sinalização do ad |Não |Sim |
| Legendas de legenda oculta CEA 608/708 pass-through |Sim |Sim |
| Suporte para não-uniforme GOPs de entrada |Sim |Não, deve ser corrigida seg 2 GOPs entrada |
| Suporte para entrada de taxa de quadros de variável |Sim |Não – entrada deve ser corrigida taxa de quadros.<br/>Secundárias variações são pela tolerar, por exemplo, durante o plano de movimento elevada. Mas codificador não é possível remover a 10 quadros por segundo. |
| Auto-shutoff canais quando a introdução de feed é perdido |Não |Após 12 horas, se não houver nenhum programa em execução |

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

Nos serviços de multimédia [canal](https://docs.microsoft.com/rest/api/media/operations/channel)s são responsáveis por processar o conteúdo de transmissão em fluxo em direto. Um canal fornece um ponto de final de entrada (URL de ingestão) que, em seguida, forneça a um transcodificador ao vivo. O canal recebe transmissões em direto de entrada do transcodificador em direto e disponibiliza-o para transmissão em fluxo através de pontos finais de um ou mais. Canais também fornecem um ponto de extremidade pré-visualização (URL de pré-visualização) que utilizar para pré-visualizar e validar a sua transmissão antes de mais processamentos e entregas.

Pode obter o URL de inserção e o URL de pré-visualização ao criar o canal. Para obter estes URLs, o canal não tem de estar no estado iniciado. Quando estiver pronto para começar a enviar dados a partir de um transcodificador ao vivo dentro do canal, o canal tem de ser iniciado. Assim que for iniciada a transcodificador ao vivo a ingestão de dados, pode visualizar a sua transmissão em fluxo.

Cada conta de Media Services pode conter vários canais, vários programas e pontos finais vários. Consoante as necessidades de largura de banda e de segurança, StreamingEndpoint serviços podem ser dedicados a um ou mais canais. Qualquer StreamingEndpoint pode extrair a partir de qualquer canal.

Ao criar um canal, pode especificar endereços IP permitidos em um dos seguintes formatos: Endereço IpV4 com 4 números, o intervalo de endereços CIDR.

### <a name="program"></a>Programa
R [programa](https://docs.microsoft.com/rest/api/media/operations/program) permite-lhe controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Canais gerem Programas. A relação entre o Canal e o Programa é muito semelhante à multimédia tradicional onde um canal tem uma transmissão em fluxo constante de conteúdo e um programa está confinado a alguns eventos temporizados nesse canal.
Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração do **ArchiveWindowLength** propriedade. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas.

ArchiveWindowLength também determina que a quantidade máxima de clientes de tempo pode recuar a partir da posição atual em direto. Os programas podem ser executados durante o período de tempo especificado, mas o conteúdo que se situe atrás da duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada programa está associado um Elemento. Para publicar o programa tem de criar um localizador para o elemento associado. Ter este localizador irá permitir compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três programas em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

## <a name="billing-implications"></a>Implicações de faturação
Um canal começa assim que é transições de estado para "Em execução" através da API de faturação.  

A tabela seguinte mostra como os Estados de um canal mapeiam para Estados de faturas no portal do Azure e de API. Tenha em atenção que os Estados são ligeiramente diferentes entre a API e o Portal de experiência do usuário. Assim que um canal está no estado "Em execução" através da API ou, no estado "Pronto" ou "Transmissão em fluxo" no portal do Azure, a faturação será Active Directory.

Para parar o canal de faturação ainda mais, terá de parar o canal através da API ou no portal do Azure.
É responsável por seus canais a parar quando tiver terminado com o canal. Falha ao parar o canal irá resultar numa faturação contínua.

> [!NOTE]
> Ao trabalhar com canais Standard, o AMS será automaticamente shutoff qualquer canal que ainda está no estado "Em execução" 12 horas depois do feed de entrada é perdido e não há nenhum programa em execução. No entanto, ainda será cobrado por hora, o canal permaneceu no estado "Em execução".
>
>

### <a id="states"></a>Estados de um canal e como devem ser mapeadas para o modo de faturação
O estado atual de um Canal. Os valores possíveis incluem:

* **Parado**. Este é o estado inicial do canal após a sua criação (a menos que início automático foi selecionado no portal.) Ocorre uma faturação sem neste estado. Neste estado, as propriedades do Canal podem ser atualizadas, mas a transmissão em fluxo não é permitida.
* **A iniciar**. O Canal está a ser iniciado. Ocorre uma faturação sem neste estado. Não são permitidas transmissões em fluxo nem atualizações durante este estado. Caso ocorra um erro, o Canal volta para o estado Parado.
* **Executar**. O Canal é capaz de processar transmissões em fluxo. Ele é agora utilização de faturação. Tem de parar o canal para impedir que mais de faturação.
* **A parar**. O Canal está a ser parado. Ocorre uma faturação sem neste estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.
* **A eliminar**. O Canal está a ser apagado. Ocorre uma faturação sem neste estado transitório. Não são permitidas transmissões em fluxo nem atualizações durante este estado.

A tabela seguinte mostra como os estados de um Canal mapeiam para o modo de faturação.

| Estado do canal | Indicadores IU do portal | É a faturação? |
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
[Especificação de ingestão de MP4 fragmentado em direto de serviços de multimédia do Azure](media-services-fmp4-live-ingest-overview.md)

[Trabalhar com canais ativados para realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md)

[Trabalhar com Canais que Recebem Transmissões em Fluxo em Direto com Velocidade de Transmissão Múltipla a partir de Codificadores no Local (Working with Channels that Receive Multi-bitrate Live Stream from On-premises Encoders)](media-services-live-streaming-with-onprem-encoders.md)

[Quotas e limitações](media-services-quotas-and-limitations.md).  

[Conceitos dos serviços de multimédia](media-services-concepts.md)
