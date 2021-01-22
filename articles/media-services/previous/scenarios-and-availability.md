---
title: Microsoft Azure Media Services cenários comuns | Microsoft Docs
description: Este artigo apresenta uma visão geral dos cenários da Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/3/2020
ms.author: inhenkel
ms.openlocfilehash: d195ad6715c47b9b4c14dc2e65ba1d07ebf79ce8
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696266"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure Media Services cenários comuns

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. Confira a versão mais recente, [Media Services v3](../latest/media-services-overview.md). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Os Serviços de Multimédia do Microsoft Azure (AMS) permitem-lhe carregar, armazenar, codificar e empacotar de forma segura conteúdos de vídeo ou áudio, para a entrega de transmissões em fluxo, tanto a pedido, como em direto, para vários clientes (por exemplo, TV, PC e dispositivos móveis).

Este artigo mostra cenários comuns para entregar o seu conteúdo ao vivo ou a pedido.

## <a name="overview"></a>Descrição geral

### <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com).
* Uma conta de Media Services do Azure Para obter mais informações, consulte [Criar Conta](media-services-portal-create-account.md).
* O ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**.

    Quando a sua conta AMS é criada, um ponto final de streaming **predefinido** é adicionado à sua conta no estado **Stop.** Para começar a transmitir o seu conteúdo em fluxo e tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo tem de estar no estado **Em execução**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Objetos utilizados normalmente no desenvolvimento com o modelo OData do AMS

A imagem seguinte mostra alguns objetos mais frequentemente utilizados ao desenvolver contra o modelo OData dos Serviços de Multimédia.

Clique na imagem para visualizá-lo tamanho completo.  

[![Diagrama mostrando alguns dos objetos mais utilizados ao desenvolver-se contra o modelo de dados de objetos Azure Media Services.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

Pode ver o modelo de todo [aqui](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Proteger conteúdos em armazenamento e distribuir multimédia de transmissão em fluxo em segurança (não encriptadas)

![Fluxo de trabalho do VoD](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Carregue um ficheiro multimédia de alta qualidade para um elemento.

    Recomenda-se a aplicação da opção de encriptação de armazenamento ao seu ativo de forma a proteger o seu conteúdo durante o upload e enquanto estiver em repouso no armazenamento é recomendado.

1. Codifique para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável.

    Recomenda-se a aplicação da opção de encriptação de armazenamento ao ativo de saída para proteger o seu conteúdo em repouso.

1. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico).

    Se o seu elemento estiver armazenamento encriptado, **deve** configurar a política de distribuição do elemento.
1. Publique o elemento através da criação de um localizador OnDemand.
1. Transmita os conteúdos publicados.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Proteger os conteúdos em armazenamento, distribuir multimédia de transmissão em fluxo encriptada de forma dinâmica

![Proteger com PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Carregue um ficheiro multimédia de alta qualidade para um elemento. Aplique a opção de encriptação de armazenamento ao elemento.
1. Codifique para um conjunto de ficheiros MP4 de velocidade de transmissão adaptável. Aplique a opção de encriptação de armazenamento ao elemento de saída.
1. Crie uma chave de conteúdo de encriptação para o elemento que pretende que seja encriptado dinamicamente durante a reprodução.
1. Configure a política de autorização da chave de conteúdo.
1. Configure a política de entrega de elementos (utilizada pelo empacotamento dinâmico e a encriptação dinâmica).
1. Publique o elemento através da criação de um localizador OnDemand.
1. Transmita os conteúdos publicados.

## <a name="deliver-progressive-download"></a>Fornecer transferência progressiva

1. Carregue um ficheiro multimédia de alta qualidade para um elemento.
1. Codifique para um único ficheiro MP4.
1. Publique o elemento através da criação de um localizador OnDemand ou SAS. Se utilizar o localizador SAS, o conteúdo é transferido a partir do Blob Storage do Azure. Não é preciso ter pontos finais de streaming no estado iniciado.
1. Transferir progressivamente os conteúdos.

## <a name="delivering-live-streaming-events"></a>Entregar eventos de transmissão em fluxo

1. Ingira conteúdos em direto com vários protocolos de transmissão em fluxo em direto (por exemplo, RTMP ou Smooth Streaming).
1. (opcionalmente) Codifique a transmissão numa transmissão em fluxo de velocidade de transmissão adaptável.
1. Pré-visualize a sua transmissão em fluxo em direto.
1. Entregar o conteúdo através de:
    1. Protocolos de streaming comuns (por exemplo, MPEG DASH, Smooth, HLS) diretamente para os seus clientes,
    1. Para uma Rede de Entrega de Conteúdos (CDN) para posterior distribuição, ou
    1. Grave e guarde o conteúdo ingerido para ser transmitido mais tarde (Vídeo-on-Demand).

Ao realizar a transmissão em fluxo em direto, pode escolher uma das rotas seguintes:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Trabalhar com canais que recebem transmissões em fluxo em direto com velocidade de transmissão múltipla a partir de codificadores no local (pass-through)

O diagrama seguinte mostra as principais partes da plataforma de AMS envolvidas no fluxo de trabalho de **pass-through**.

![Diagrama que mostra as principais partes da plataforma A M S envolvidas no fluxo de trabalho "pass-through".](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Para obter mais informações, consulte [Trabalhar com Canais que recebem transmissões em Fluxo em Direto de Múltipla Velocidade de Transmissão a partir de Codificadores no Local](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Trabalhar com canais ativados para realizar live encoding com os Serviços de Multimédia do Azure

O diagrama seguinte mostra as principais partes da plataforma AMS que estão envolvidas no fluxo de trabalho live streaming onde um Canal está habilitado a fazer codificação ao vivo com os Media Services.

![Fluxo de trabalho em direto](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Para obter mais informações, consulte [Trabalhar com Canais Ativados para Realizar Live Encoding com Media Services do Azure](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Consumo de conteúdos

Os Media Services do Azure fornecem as ferramentas necessárias para criar aplicações avançadas e dinâmicas de leitor de cliente para a maioria das plataformas, incluindo: Dispositivos iOS dispositivos, Dispositivos Android, Windows, Windows Phone, Xbox e Caixas descodificadoras.

## <a name="enabling-azure-cdn"></a>Ativar a CDN do Azure

Os Media Services suportam a integração com a CDN do Azure. Para obter mais informações sobre como ativar a CDN do Azure, consulte [Como Gerir Transmissão em Fluxo de Pontos Finais numa Conta de Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Escalar uma conta dos Media Services

Os clientes do AMS podem dimensionar pontos finais de transmissões, o processamento de multimédia e o armazenamento nas respetivas contas do AMS.

* Os clientes dos Serviços de Multimédia podem escolher um ponto final de transmissão em fluxo **Standard** ou **Premium**. O ponto final de transmissão em fluxo **Standard** é ideal para a maioria das cargas de trabalho de transmissão em fluxo. Inclui as mesmas funcionalidades dos pontos finais **Premium** e dimensiona a largura de banda de saída automaticamente.

    Os pontos finais de transmissão em fluxo **Premium** são adequadas para cargas de trabalho avançadas, ao fornecer uma capacidade de largura de banda dimensionável e dedicada. Por predefinição, os clientes que têm os pontos finais de transmissão em fluxo **Premium** recebem uma unidade de transmissão em fluxo (SU). O ponto final de transmissão em fluxo pode ser dimensionado mediante a adição de mais SUs. Cada SU fornece capacidade de largura de banda adicional à aplicação. Para obter mais informações sobre o dimensionamento de pontos finais de transmissões em fluxo **Premium** veja o tópico [Scaling streaming endpoints](media-services-portal-scale-streaming-endpoints.md) (Dimensionar pontos finais de transmissões em fluxo).

* As contas dos Serviços de Multimédia estão associadas a um Tipo de Unidade Reservada, que determina a velocidade do processamento das suas tarefas de processamento de mulitmédia. Pode escolher entre os seguintes tipos de unidade reservada: **S1,** **S2** ou **S3**. Por exemplo, a mesma tarefa de trabalho de codificação é executada mais depressa se utilizar o tipo de unidade reservada **S2** em comparação com o tipo **S1**.

    Além de especificar o tipo de unidade reservada, pode especificar a disponibilização da sua conta com **Unidades Reservadas** (RUs). O número de RUs aprovisionadas determina o número de tarefas de multimédia que podem ser processadas em simultâneo numa determinada conta.

    > [!NOTE]
    > As RUs destinam-se a paralelizar todos os processamentos de multimédia, incluindo trabalhos de indexação com o Azure Media Indexer. No entanto, ao contrário da codificação, os trabalhos de indexação não são processados mais depressa com unidades reservadas mais rápidas.

    Para obter mais informações, ver, [dimensionamento de meios de comunicação](media-services-portal-scale-media-processing.md).

* Também pode escalar a sua conta dos Media Services adicionando-lhe contas deo Storage. Cada conta do Storage está limitada a 500 TB. Para obter mais informações, veja [Manage storage accounts](./media-services-managing-multiple-storage-accounts.md) (Gerir contas de armazenamento).

## <a name="next-steps"></a>Passos seguintes

[Migrar para os Serviços de Multimédia v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]