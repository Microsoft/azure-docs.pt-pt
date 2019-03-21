---
title: Transmitir em fluxo com codificadores no local com o portal do Azure | Microsoft Docs
description: Este tutorial explica os passos para criar um Canal configurado para uma entrega pass-through.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6f4acd95-cc64-4dd9-9e2d-8734707de326
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d86151b436ec3cc5ea3d4b687f5c8692b2ca4efa
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258715"
---
# <a name="how-to-perform-live-streaming-with-on-premises-encoders-using-the-azure-portal"></a>Como realizar uma transmissão em fluxo em direto com codificadores no local através do Portal do Azure
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> 

Este tutorial explica os passos para criar um **Canal** configurado para uma entrega pass-through através do Portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
O seguinte é necessário para concluir o tutorial:

* Uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
* Uma conta dos Media Services. Para criar uma conta dos Media Services, consulte [Como Criar uma Conta de Media Services](media-services-portal-create-account.md).
* Uma câmara Web. Por exemplo, [codificador Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm).

É altamente recomendado rever os seguintes artigos:

* [Azure Media Services RTMP Support and Live Encoders (Suporte RTMP dos Serviços de Multimédia do Azure e Codificadores em Direto)](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Overview of Live Streaming using Azure Media Services (Descrição Geral da Transmissão em Fluxo em Direto com os Serviços de Multimédia do Azure)](media-services-manage-channels-overview.md)
* [Transmissão em fluxo em direto com codificadores no local que criam transmissões com velocidade de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md)

## <a id="scenario"></a>Cenário comum de transmissão em fluxo em direto
Os passos seguintes descrevem as tarefas envolvidas na criação de aplicações de transmissão em fluxo em direto comuns que utilizam canais que estão configurados para entrega pass-through. Este tutorial mostra como criar e gerir um canal pass-through e eventos em direto.

>[!NOTE]
>Certifique-se de que o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo está no estado **Em execução**. 
    
1. Ligue uma câmara de vídeo a um computador. Iniciar e configurar um codificador em direto no local que produza um RTMP com velocidade de transmissão múltipla ou uma transmissão em fluxo MP4 fragmentada. Para obter mais informações, consulte [Suporte RTMP dos Media Services do Azure e Codificadores em Direto](https://go.microsoft.com/fwlink/?LinkId=532824).
   
    Este passo também pode ser realizado depois de criar o Canal.
2. Criar e iniciar um Canal pass-through.
3. Obtenha o URL de inserção do Canal. 
   
    O URL de inserção é utilizado pelo codificador em direto para enviar a transmissão para o Canal.
4. Obtenha o URL de pré-visualização do Canal. 
   
    Utilize este URL para verificar se o canal está a receber corretamente a transmissão em fluxo em direto.
5. Crie um evento/programa em direto. 
   
    Quando utilizar o Portal do Azure, ao criar um evento em direto também cria um elemento. 

6. Inicie o evento/programa quando estiver pronto para começar a transmissão em fluxo e o arquivamento.
7. Opcionalmente, o codificador em direto pode ser indicado para iniciar um anúncio. O anúncio é inserido na transmissão de saída.
8. Pare o evento/programa sempre que pretender interromper a transmissão em fluxo e arquivar o evento.
9. Elimine o evento/programa (e, opcionalmente, elimine o elemento).     

> [!IMPORTANT]
> Reveja a [Transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo com velocidade de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md) para saber mais sobre os conceitos e as considerações relacionadas com a transmissão em fluxo em direto com codificadores no local e canais pass-through.
> 
> 

## <a name="to-view-notifications-and-errors"></a>Para ver as notificações e erros
Se pretende ver as notificações e os erros produzidos pelo Portal do Azure, clique no ícone de Notificação.

![Notificações](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="create-and-start-pass-through-channels-and-events"></a>Criar e iniciar eventos e canais pass-through
Um canal está associado a eventos/programas que permitem controlar a publicação e armazenamento de segmentos numa transmissão em fluxo em direto. Os canais gerem eventos. 

Pode especificar o número de horas que pretenda manter o conteúdo gravado para o programa através da configuração da duração da **Janela de Arquivo**. Este valor pode ser definido a partir de um mínimo de 5 minutos até um máximo de 25 horas. A duração da janela de arquivo dita também o tempo máximo que os clientes podem recuar a partir da posição atual em direto. Os eventos podem ser executados durante o período de tempo especificado, contudo, o conteúdo que não respeitar essa duração da janela é continuamente descartado. O valor desta propriedade também determina durante quanto tempo os manifestos dos clientes podem aumentar.

Cada evento está associado a um elemento. Para publicar o evento, deve criar um localizador OnDemand para o elemento associado. Ter este localizador permite compilar um URL de transmissão em fluxo que pode fornecer aos seus clientes.

Um canal suporta até três eventos em execução em simultâneo para que possa criar vários arquivos da mesma transmissão em fluxo recebida. Isto permite publicar e arquivar diferentes partes de um evento, conforme necessário. Por exemplo, os seus requisitos de negócios devem arquivar 6 horas de um programa, mas difundir apenas os últimos 10 minutos. Para tal, tem de criar dois programas em execução em simultâneo. Um programa está definido para arquivar 6 horas do evento, mas o programa não está publicado. O outro programa está definido para arquivar durante 10 minutos e este está publicado.

Não deve reutilizar os eventos em direto existentes. Em vez disso, crie e inicie um novo evento para cada evento.

Inicie o evento quando estiver pronto para começar a transmissão em fluxo e o arquivamento. Pare o programa sempre que pretender interromper a transmissão e arquivar o evento. 

Para eliminar conteúdo arquivado, pare e elimine o evento e, em seguida, elimine o elemento associado. Não é possível eliminar um elemento se este é utilizado por um evento; o evento deve ser eliminado primeiro. 

Mesmo depois de parar e eliminar o evento, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não elimine o elemento.

Se pretende manter o conteúdo arquivado, mas não o quer manter disponível para transmissão em fluxo, elimine o localizador de transmissão em fluxo.

### <a name="to-use-the-portal-to-create-a-channel"></a>Utilizar o portal para criar um canal
Esta secção mostra como utilizar a opção **Criação Rápida** para criar um canal pass-through.

Para obter mais detalhes sobre canais pass-through, veja [Transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo com velocidade de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md).

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na janela **Definições**, clique em **Transmissão em Fluxo em Direto**. 
   
    ![Introdução](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    A janela **Transmissão em Fluxo em Direto** é apresentada.
3. Clique em **Criação Rápida** para criar um canal pass-through com o protocolo de inserção RTMP.
   
    A janela **CRIAR UM NOVO CANAL** é apresentada.
4. Dê um nome ao novo canal e clique em **Criar**. 
   
    Tal cria um canal pass-through com o protocolo de inserção RTMP.

## <a name="create-events"></a>Criar eventos
1. Selecione um canal para o qual pretende adicionar um evento.
2. Prima o botão **Evento em Direto**.

![Evento](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>Obter URLs de inserção
Assim que o canal seja criado, pode obter os URLs de inserção que fornecerá ao codificador em direto. O codificador utiliza estes URLs para exibir uma transmissão um fluxo direto.

![Criado](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>Ver o evento
Para ver o evento, clique em **Ver** no Portal do Azure ou copie o URL de transmissão em fluxo e utilize um leitor da sua preferência. 

![Criado](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

O evento em direto é convertido automaticamente para conteúdo a pedido quando parado.

## <a name="clean-up"></a>Limpeza
Para obter mais detalhes sobre canais pass-through, veja [Transmissão em fluxo em direto com codificadores no local que criam transmissões em fluxo com velocidade de transmissão múltipla](media-services-live-streaming-with-onprem-encoders.md).

* Um canal pode ser parado apenas quando todos os eventos/programas no canal foram parados.  Assim que o Canal esteja parado, não é cobrado qualquer custo. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
* Um canal pode ser eliminado apenas quando todos os eventos em direto canal tiverem sido eliminados.

## <a name="view-archived-content"></a>Ver conteúdo arquivado
Mesmo depois de parar e eliminar o evento, os utilizadores conseguirão transmitir o seu conteúdo arquivado como um vídeo a pedido, desde que não elimine o elemento. Não é possível eliminar um elemento se este é utilizado por um evento; o evento deve ser eliminado primeiro. 

Para gerir os seus elementos, selecione **Definição** e clique em **Elementos**.

![Elementos](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>Passo seguinte
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

