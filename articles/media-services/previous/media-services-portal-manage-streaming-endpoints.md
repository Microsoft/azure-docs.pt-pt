---
title: Gerir pontos finais de streaming com o portal Azure [ Microsoft Docs
description: Este artigo demonstra como gerir os pontos finais de streaming com o portal Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900888"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Manage streaming endpoints with the Azure portal (Gerir os pontos finais de transmissão em fluxo com o portal do Azure) 

Este artigo mostra como usar o portal Azure para gerir os pontos finais de streaming. 

>[!NOTE]
>Certifique-se de rever o artigo sobre [visão geral.](media-services-streaming-endpoints-overview.md) 

Para obter informações sobre como escalar o ponto final de streaming, consulte [este](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a name="start-managing-streaming-endpoints"></a>Comece a gerir os pontos finais de streaming 

Para começar a gerir os pontos finais de streaming para a sua conta, faça o seguinte.

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na lâmina **Definições,** selecione **pontos finais de streaming**.
   
    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Só é cobrado quando o seu Streaming Endpoint está em estado de funcionamento.

## <a name="adddelete-a-streaming-endpoint"></a>Adicionar/eliminar um ponto final de streaming

>[!NOTE]
>O ponto final de transmissão predefinido não pode ser eliminado.

Para adicionar/eliminar o ponto final de streaming utilizando o portal Azure, faça o seguinte:

1. Para adicionar um ponto final de streaming, clique no **ponto final +** no topo da página. 

    Pode querer vários Pontos Finais de Streaming se pretender ter CDNs diferentes ou um CDN e acesso direto.

2. Para eliminar um ponto final de streaming, prima **Apagar** o botão.      
3. Clique no botão **Iniciar** para iniciar o ponto final de streaming.
   
    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Configurar o ponto final de streaming
O Streaming Endpoint permite-lhe configurar as seguintes propriedades:

* Controlo de acesso
* Controlo de cache
* Políticas de acesso ao local transversal

Para obter informações detalhadas sobre estas propriedades, consulte [o StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Quando o CDN está ativado, não é possível aceder ao acesso IP. O acesso IP só é aplicável quando não tem CDN.

Pode configurar o ponto final de streaming fazendo o seguinte:

1. Selecione o ponto final de streaming que pretende configurar.
2. Clique em **Definições**.

Segue-se uma breve descrição dos campos.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Política máxima de cache: usada para configurar o tempo de vida da cache para os ativos servidos através deste ponto final de streaming. Se não for definido qualquer valor, o predefinido é utilizado. Os valores predefinidos também podem ser definidos diretamente no armazenamento do Azure. Se o Azure CDN estiver ativado para o ponto final de streaming, não deve definir o valor da política de cache para menos de 600 segundos.  
2. Endereços IP permitidos: utilizados para especificar endereços IP que seriam autorizados a ligar-se ao ponto final de streaming publicado. Se não especificarem os endereços IP, qualquer endereço IP poderá ligar-se. Os endereços IP podem ser especificados como um único endereço IP (por exemplo, '10.0.0.1'), uma gama IP utilizando um endereço IP e uma máscara de sub-rede CIDR (por exemplo, '10.0.0.1/22'), ou uma gama IP utilizando endereço IP e uma máscara de cimalol pontilhada (por exemplo, '10.0.0.1 (255.255.25).').
3. Configuração para autenticação do cabeçalho de assinatura Akamai: usado para especificar como o pedido de autenticação do cabeçalho de assinatura dos servidores da Akamai está configurado. A expiração é na UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Dimensione o seu ponto final de streaming Premium

Para mais informações, consulte [este](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Permitir a integração do CDN azure

Quando cria uma nova conta, a integração padrão do Streaming Endpoint Endpoint Azure CDN é ativada por padrão.

Se mais tarde pretender desativar/ativar o CDN, o seu ponto final de streaming deve estar no estado **de paragem.** Pode levar até duas horas para que a integração do CDN Azure seja ativada e que as alterações sejam ativas em todos os POPs da CDN. No entanto, pode iniciar o seu ponto final de streaming e transmitir sem interrupções do ponto final de streaming e uma vez concluída a integração, o fluxo é entregue a partir do CDN. Durante o período de provisionamento, o seu ponto final de streaming estará em estado **de partida** e poderá observar um desempenho degradado.

A integração da CDN é ativada em todos os centros de dados azure, exceto as regiões da China e do Governo Federal.

Uma vez ativado, o Controlo de **Acesso,****Nome de anfitrião personalizado e configuração de **autenticação Da Assinatura Akamai** ficam desativados.
 
> [!IMPORTANT]
> A integração da Azure Media Services com o Azure CDN é implementada no **Azure CDN da Verizon** para pontos finais de streaming padrão. Os pontos finais de streaming premium podem ser configurados utilizando todos os níveis e fornecedores de **preços Do CDN do Azure.** Para obter mais informações sobre as funcionalidades do CDN azure, consulte a visão geral do [CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Considerações adicionais

* Quando a CDN está ativada para um ponto final de streaming, os clientes não podem solicitar conteúdo diretamente da origem. Se precisar da capacidade de testar o seu conteúdo com ou sem CDN, pode criar outro ponto final de streaming que não esteja ativado pela CDN.
* O seu nome de anfitrião de ponto final de streaming permanece o mesmo depois de ativar a CDN. Não é necessário fazer alterações no fluxo de trabalho dos seus serviços de media depois de a CDN estar ativada. Por exemplo, se o seu nome de anfitrião de ponto final de streaming for strasbourg.streaming.mediaservices.windows.net, depois de ativar a CDN, é utilizado exatamente o mesmo nome de anfitrião.
* Para novos pontos finais de streaming, pode ativar o CDN simplesmente criando um novo ponto final; para os pontos finais de streaming existentes, é necessário primeiro parar o ponto final e, em seguida, ativar/desativar o CDN.
* O ponto final de streaming padrão só pode ser configurado utilizando o **fornecedor Verizon Standard CDN** utilizando o portal clássico Azure. No entanto, pode permitir que outros fornecedores de CDN Azure utilizem APIs REST.

## <a name="configure-cdn-profile"></a>Configure o perfil CDN

Pode configurar o perfil CDN selecionando o botão **Manage CDN** a partir da parte superior.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Passos seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

