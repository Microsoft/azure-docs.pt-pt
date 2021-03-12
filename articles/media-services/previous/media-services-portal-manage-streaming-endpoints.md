---
title: Gerir os pontos finais de streaming com o portal Azure | Microsoft Docs
description: Este artigo demonstra como gerir os pontos finais de streaming com o portal Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: bda244267e81d959abbc4ae0a8532ddce68d5726
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017397"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Manage streaming endpoints with the Azure portal (Gerir os pontos finais de transmissão em fluxo com o portal do Azure)

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Este artigo mostra como usar o portal Azure para gerir os pontos finais de streaming. 

>[!NOTE]
>Certifique-se de rever o artigo [geral.](media-services-streaming-endpoints-overview.md) 

Para obter informações sobre como escalar o ponto final de streaming, consulte [este](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a name="start-managing-streaming-endpoints"></a>Comece a gerir os pontos finais de streaming 

Para começar a gerir os pontos finais de streaming para a sua conta, faça o seguinte.

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na lâmina **Definições,** selecione **pontos finais de streaming**.
   
    ![Screenshot que mostra a página "Serviços de mídia" com "Streaming endpoints" selecionados a partir da lâmina "Definições".](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Só é cobrado quando o seu Streaming Endpoint está em funcionamento.

## <a name="adddelete-a-streaming-endpoint"></a>Adicionar/eliminar um ponto final de streaming

>[!NOTE]
>O ponto final de streaming predefinido não pode ser eliminado.

Para adicionar/eliminar o ponto final de streaming utilizando o portal Azure, faça o seguinte:

1. Para adicionar um ponto final de streaming, clique no **+ Ponto final** no topo da página. 

    Pode querer vários Pontos Finais de Streaming se pretender ter CDNs diferentes ou um CDN e acesso direto.

2. Para eliminar um ponto final de streaming, prima **Eliminar** o botão.      
3. Clique no botão **Iniciar** para iniciar o ponto final de streaming.
   
    ![Screenshot que mostra a ação "Endpoint" selecionada e o painel "Streaming Endpoint Details" exibido.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Configurar o ponto final de streaming
O Streaming Endpoint permite-lhe configurar as seguintes propriedades:

* Controlo de acesso
* Controlo de cache
* Políticas de acesso ao site transversal

Para obter informações detalhadas sobre estas propriedades, consulte [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Quando o CDN está ativado, não é possível aceder ao acesso IP. O acesso IP só é aplicável quando não tiver CDN.

Pode configurar o ponto final de streaming fazendo o seguinte:

1. Selecione o ponto final de streaming que pretende configurar.
2. Clique em **Definições**.

Segue-se uma breve descrição dos campos.

![Screenshot que mostra a ação "Definições" selecionada para o ponto final de streaming.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Política máxima de cache: usada para configurar a vida útil da cache para os ativos servidos através deste ponto final de streaming. Se não for definido qualquer valor, o padrão é utilizado. Os valores predefinidos também podem ser definidos diretamente no armazenamento do Azure. Se o Azure CDN estiver ativado para o ponto final de streaming, não deverá definir o valor da política de cache em menos de 600 segundos.  
2. Endereços IP permitidos: usados para especificar endereços IP que seriam autorizados a ligar-se ao ponto final de streaming publicado. Se nenhum endereço IP especificado, qualquer endereço IP poderá ser conectado. Os endereços IP podem ser especificados como um único endereço IP (por exemplo, '10.0.0.1'), uma gama IP utilizando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, «10.0.0.1/22»), ou uma gama IP utilizando o endereço IP e uma máscara de sub-rede decimal pontilhada (por exemplo, '10.0.0.1(255.255.255.0)»).
3. Configuração para autenticação do cabeçalho de assinatura da Akamai: usada para especificar como o pedido de autenticação do cabeçalho de assinatura dos servidores da Akamai está configurado. A expiração está na UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Dimensione o seu ponto final de streaming Premium

Para mais informações, consulte [este](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Permitir a integração do Azure CDN

Quando cria uma nova conta, a integração padrão do Streaming Endpoint Azure CDN é ativada por padrão.

Se mais tarde pretender desativar/ativar o CDN, o seu ponto final de streaming deve estar no estado **de paragem.** Pode levar até duas horas para que a integração do Azure CDN seja ativada e para que as alterações estejam ativas em todos os POPs do CDN. No entanto, pode iniciar o seu ponto final de streaming e streaming sem interrupções a partir do ponto final de streaming e uma vez que a integração esteja completa, o fluxo é entregue a partir do CDN. Durante o período de provisionamento, o seu ponto final de streaming estará no estado **inicial** e poderá observar um desempenho degradado.

A integração da CDN está ativada em todos os centros de dados do Azure, exceto na China e nas regiões do Governo Federal.

Uma vez ativado, o **Access Control**, o nome de anfitrião **e a configuração **de autenticação Da Assinatura Akamai** são desativados.
 
> [!IMPORTANT]
> A integração do Azure Media Services com a Azure CDN é implementada na **Azure CDN da Verizon** para os pontos finais de streaming padrão. Os pontos finais de streaming premium podem ser configurados utilizando todos os **níveis e fornecedores de preços da Azure CDN**. Para obter mais informações sobre as funcionalidades do Azure CDN, consulte a [visão geral](../../cdn/cdn-overview.md)do CDN .
 
### <a name="additional-considerations"></a>Considerações adicionais

* Quando a CDN está ativada para um ponto final de streaming, os clientes não podem solicitar conteúdo diretamente a partir da origem. Se precisar da capacidade de testar o seu conteúdo com ou sem CDN, pode criar outro ponto final de streaming que não esteja ativado pela CDN.
* O seu nome de anfitrião do ponto de streaming permanece o mesmo depois de ativar o CDN. Não precisa de fazer alterações no fluxo de trabalho dos seus serviços de comunicação após a ativação da CDN. Por exemplo, se o seu nome de anfitrião do ponto de streaming for strasbourg.streaming.mediaservices.windows.net, depois de ativar o CDN, é utilizado exatamente o mesmo nome de hospedeiro.
* Para novos pontos finais de streaming, pode ativar a CDN simplesmente criando um novo ponto final; para os pontos finais de streaming existentes, é necessário primeiro parar o ponto final e, em seguida, ativar/desativar o CDN.
* O ponto final de streaming padrão só pode ser configurado usando **o fornecedor De CDN padrão Verizon** usando o portal clássico Azure. No entanto, pode ativar outros fornecedores de CDN Azure utilizando APIs REST.

## <a name="configure-cdn-profile"></a>Configure o perfil CDN

Pode configurar o perfil CDN selecionando o botão **Manage CDN** a partir da parte superior.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
