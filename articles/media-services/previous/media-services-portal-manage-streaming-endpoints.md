---
title: Gerenciar pontos de extremidade de streaming com o portal do Azure | Microsoft Docs
description: Este artigo demonstra como gerenciar pontos de extremidade de streaming com o portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 57ec22cb02512577f8737718cae9175403fb5603
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900888"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gerenciar pontos de extremidade de streaming com o portal do Azure 

Este artigo mostra como usar o portal do Azure para gerenciar pontos de extremidade de streaming. 

>[!NOTE]
>Certifique-se de examinar o artigo de [visão geral](media-services-streaming-endpoints-overview.md) . 

Para obter informações sobre como dimensionar o ponto de extremidade de streaming, consulte [este](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a name="start-managing-streaming-endpoints"></a>Começar a gerenciar pontos de extremidade de streaming 

Para começar a gerenciar pontos de extremidade de streaming para sua conta, faça o seguinte.

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na folha **configurações** , selecione **pontos de extremidade de streaming**.
   
    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Você será cobrado somente quando o ponto de extremidade de streaming estiver em estado de execução.

## <a name="adddelete-a-streaming-endpoint"></a>Adicionar/excluir um ponto de extremidade de streaming

>[!NOTE]
>O ponto de extremidade de streaming padrão não pode ser excluído.

Para adicionar/excluir o ponto de extremidade de streaming usando o portal do Azure, faça o seguinte:

1. Para adicionar um ponto de extremidade de streaming, clique no **ponto de extremidade +** na parte superior da página. 

    Você pode querer vários pontos de extremidade de streaming se planeja ter diferentes CDNs ou uma CDN e acesso direto.

2. Para excluir um ponto de extremidade de streaming, pressione o botão **excluir** .      
3. Clique no botão **Iniciar** para iniciar o ponto de extremidade de streaming.
   
    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Configurando o ponto de extremidade de streaming
O ponto de extremidade de streaming permite que você configure as seguintes propriedades:

* Controlo de acesso
* Controlo de cache
* Políticas de acesso entre sites

Para obter informações detalhadas sobre essas propriedades, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Quando a CDN está habilitada, você não pode acessar o acesso a IP. O acesso a IP só é aplicável quando você não tem a CDN.

Você pode configurar o ponto de extremidade de streaming fazendo o seguinte:

1. Selecione o ponto de extremidade de streaming que você deseja configurar.
2. Clique em **Definições**.

Segue uma breve descrição dos campos.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Política de cache máxima: usada para configurar o tempo de vida do cache para ativos servidos por meio desse ponto de extremidade de streaming. Se nenhum valor for definido, o padrão será usado. Os valores padrão também podem ser definidos diretamente no armazenamento do Azure. Se a CDN do Azure estiver habilitada para o ponto de extremidade de streaming, você não deverá definir o valor da política de cache para menos de 600 segundos.  
2. Endereços IP permitidos: usados para especificar os endereços IP que teriam permissão para se conectar ao ponto de extremidade de streaming publicado. Se não forem especificados endereços IP, qualquer endereço IP poderá se conectar. Os endereços IP podem ser especificados como um único endereço IP (por exemplo, ' 10.0.0.1 '), um intervalo de IP usando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, ' 10.0.0.1/22 ') ou um intervalo de IP usando o endereço IP e uma máscara de sub-rede decimal com pontos (por exemplo, ' 10.0.0.1 (255.255.255.0) ').
3. Configuração da autenticação de cabeçalho de assinatura Akamai: usada para especificar como a solicitação de autenticação de cabeçalho de assinatura de servidores Akamai está configurada. A expiração está em UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Dimensionar seu ponto de extremidade de streaming Premium

Para obter mais informações, veja [este](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a id="enable_cdn"></a>Habilitar a integração da CDN do Azure

Quando você cria uma nova conta, o ponto de extremidade de streaming padrão integração da CDN do Azure é habilitado por padrão.

Se posteriormente você quiser desabilitar/habilitar a CDN, o ponto de extremidade de streaming deverá estar no estado **parado** . Pode levar até duas horas para que a integração da CDN do Azure seja habilitada e que as alterações estejam ativas em todos os POPs da CDN. No entanto, seu pode iniciar seu ponto de extremidade de streaming e transmitir sem interrupções do ponto de extremidade de streaming e, depois que a integração for concluída, o fluxo será entregue da CDN. Durante o período de provisionamento, o ponto de extremidade de streaming estará no estado **inicial** e você poderá observar o desempenho degradado.

A integração da CDN é habilitada em todos os data centers do Azure, exceto nas regiões da China e do governo federal.

Depois de habilitado, o **controle de acesso**, * * nome de host personalizado e configuração de **autenticação de assinatura Akamai** são desabilitados.
 
> [!IMPORTANT]
> A integração dos serviços de mídia do Azure com a CDN do Azure é implementada na **CDN do Azure da Verizon** para pontos de extremidade de streaming padrão. Os pontos de extremidade de streaming Premium podem ser configurados usando todos os **tipos de preços e provedores da CDN do Azure**. Para obter mais informações sobre os recursos da CDN do Azure, consulte a [visão geral da CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Considerações adicionais

* Quando a CDN está habilitada para um ponto de extremidade de streaming, os clientes não podem solicitar conteúdo diretamente da origem. Se você precisar da capacidade de testar o conteúdo com ou sem CDN, poderá criar outro ponto de extremidade de streaming que não esteja habilitado para CDN.
* O nome do host do ponto de extremidade de streaming permanece o mesmo depois de habilitar a CDN. Você não precisa fazer nenhuma alteração no fluxo de trabalho dos serviços de mídia após a CDN estar habilitada. Por exemplo, se o nome do host do ponto de extremidade de streaming for strasbourg.streaming.mediaservices.windows.net, depois de habilitar a CDN, exatamente o mesmo nome do host será usado.
* Para os novos pontos de extremidade de streaming, você pode habilitar a CDN simplesmente criando um novo ponto de extremidade; para pontos de extremidade de streaming existentes, primeiro você precisa parar o ponto de interrupção e, em seguida, habilitar/desabilitar a CDN.
* O ponto de extremidade de streaming standard só pode ser configurado usando o **provedor CDN padrão da Verizon** usando o portal clássico do Azure. No entanto, você pode habilitar outros provedores de CDN do Azure usando APIs REST.

## <a name="configure-cdn-profile"></a>Configurar o perfil CDN

Você pode configurar o perfil CDN selecionando o botão **gerenciar CDN** na parte superior.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

