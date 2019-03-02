---
title: Gerir pontos finais de transmissão em fluxo com o portal do Azure | Documentos da Microsoft
description: Este tópico mostra como gerir pontos finais de transmissão em fluxo com o portal do Azure.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: b17706b09ce64735cd9d68ace579fd7f5b366dc3
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244473"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gerir pontos finais de transmissão em fluxo com o portal do Azure 

Este artigo mostra como utilizar o portal do Azure para gerir pontos finais de transmissão em fluxo. 

>[!NOTE]
>Lembre-se de que reveja os [descrição geral](media-services-streaming-endpoints-overview.md) artigo. 

Para obter informações sobre como dimensionar o ponto final de transmissão em fluxo, consulte [isso](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a name="start-managing-streaming-endpoints"></a>Começar a gerir pontos finais de transmissão em fluxo 

Para começar a gerir pontos finais de transmissão em fluxo para a sua conta, faça o seguinte.

1. No [portal do Azure](https://portal.azure.com/) selecione a sua conta de Serviços de Multimédia do Azure.
2. Na **configurações** painel, selecione **pontos finais de transmissão em fluxo**.
   
    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> É-lhe cobrada apenas quando o ponto final de transmissão em fluxo está no estado de execução.

## <a name="adddelete-a-streaming-endpoint"></a>Adicionar/eliminar um ponto de final de transmissão em fluxo

>[!NOTE]
>Não é possível eliminar o ponto final de transmissão em fluxo predefinido.

Para adicionar/eliminar ponto final de transmissão em fluxo com o portal do Azure, faça o seguinte:

1. Para adicionar um ponto de final de transmissão em fluxo, clique a **+ ponto final** na parte superior da página. 

    Pode desejar vários pontos finais de transmissão em fluxo se planeia ter CDNs diferentes ou de uma CDN e acesso direto.

2. Para eliminar um ponto de final de transmissão em fluxo, prima **eliminar** botão.      
3. Clique nas **iniciar** botão para iniciar o ponto final de transmissão em fluxo.
   
    ![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Configurar o ponto final de transmissão em fluxo
Ponto final de transmissão em fluxo permite-lhe configurar as seguintes propriedades:

* Controlo de acesso
* Controlo de cache
* Em várias políticas de acesso do site

Para obter informações detalhadas sobre estas propriedades, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Quando a CDN estiver ativada, não é possível acessar o acesso IP. Acesso IP só é aplicável se não precisa da CDN.

Pode configurar o ponto final de transmissão em fluxo, fazendo o seguinte:

1. Selecione o ponto final de transmissão em fluxo que pretende configurar.
2. Clique em **definições**.

Segue-se uma breve descrição dos campos.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Política de cache máxima: utilizado para configurar a duração da cache para ativos atendidos por meio deste ponto final de transmissão em fluxo. Se nenhum valor for definido, é utilizada a predefinição. Os valores predefinidos também podem ser definidos diretamente no armazenamento do Azure. Se a CDN do Azure está ativada para o ponto final de transmissão em fluxo, não deve definir o valor da política de cache para menos de 600 segundos.  
2. Endereços IP permitidos: utilizado para especificar endereços IP que teria permissão para ligar ao ponto de final de transmissão em fluxo publicado. Se não existem endereços IP especificados, qualquer endereço IP seria capaz de se ligar. Endereços IP podem ser especificados como um endereço IP único (por exemplo, "10.0.0.1"), um intervalo IP com um endereço IP e uma máscara de sub-rede CIDR (por exemplo, "10.0.0.1/22") ou um intervalo IP com o endereço IP e uma máscara de sub-rede de ponto decimal (por exemplo, "10.0.0.1 ( 255.255.255.0) ").
3. Configuração para a autenticação de cabeçalho de assinatura do Akamai: utilizado para especificar a forma como o pedido de autenticação do cabeçalho de assinatura dos servidores Akamai está configurado. Expiração é em UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Dimensionar o seu ponto final de transmissão em fluxo de Premium

Para obter mais informações, veja [este](media-services-portal-scale-streaming-endpoints.md) artigo.

## <a id="enable_cdn"></a>Ativar a integração da CDN do Azure

Quando cria uma nova conta, integração de CDN do Azure de ponto final de transmissão em fluxo predefinido está ativada por predefinição.

Se pretender mais tarde desativar/ativar a CDN, o ponto final de transmissão em fluxo tem de estar no **parado** estado. Pode demorar até duas horas para que a integração da CDN do Azure obter ativada e as alterações de ser o Active Directory em todos os POP de CDN. No entanto, pode iniciar o seu ponto final de transmissão em fluxo e fluxo sem interrupções a partir do ponto final de transmissão em fluxo e assim que a integração foi concluída, a transmissão é entregue da CDN. Durante o período de aprovisionamento, o ponto final de transmissão em fluxo vai estar num **a partir de** estado e poderá observar degradação do desempenho.

Integração da CDN está ativada em todos os centros de dados do Azure, exceto a China e Governo Federal regiões.

Assim que estiver ativada, o **controlo de acesso**, * * nome de anfitrião personalizado, e **autenticação da assinatura do Akamai** configuração obtém desativada.
 
> [!IMPORTANT]
> Integração de serviços de multimédia do Azure com a CDN do Azure é implementada em **CDN do Azure da Verizon** para padrão de transmissão em fluxo em pontos de extremidade. Pontos finais de transmissão em fluxo Premium pode ser configurada com todos os **CDN do Azure, fornecedores e escalões de preços**. Para obter mais informações sobre as funcionalidades de CDN do Azure, consulte a [descrição geral da CDN](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Considerações adicionais

* Quando a CDN está ativada para um ponto de final de transmissão em fluxo, os clientes não podem solicitar conteúdo diretamente a partir da origem. Se tiver a capacidade de testar o seu conteúdo com ou sem CDN, pode criar outro ponto final de transmissão que não seja CDN ativada.
* Anfitrião do ponto final de transmissão em fluxo permanece igual depois de ativar a CDN. Não precisa de fazer alterações ao seu fluxo de trabalho de serviços de multimédia depois de CDN está ativada. Por exemplo, se o anfitrião do ponto final de transmissão em fluxo é strasbourg.streaming.mediaservices.windows.net, depois de ativar a CDN, é utilizado o nome de anfitrião mesmo exato.
* Para pontos finais de transmissão em fluxo novo, pode ativar a CDN simplesmente criando um novo ponto de final; para pontos finais de transmissão em fluxo existentes, terá de interromper primeiro o ponto final e, em seguida, ativar/desativar a CDN.
* Ponto final de transmissão em fluxo Standard só pode ser configurada usando **fornecedor de CDN Standard da Verizon** através do portal clássico do Azure. No entanto, pode ativar a outros fornecedores de CDN do Azure através de REST APIs.

## <a name="configure-cdn-profile"></a>Configurar o perfil da CDN

Pode configurar o perfil da CDN, selecionando o **gerir CDN** botão na parte superior.

![Ponto final de transmissão em fluxo](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Passos Seguintes
Rever os percursos de aprendizagem dos Serviços de Multimédia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

