---
title: Implementar serviços de Gestão Azure API para várias regiões do Azure
titleSuffix: Azure API Management
description: Saiba como implementar uma instância de serviço de Gestão API Azure para várias regiões azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 5c71f37741de06b8633e7eafaae2f29823214f74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75442671"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure

A Azure API Management apoia a implantação em várias regiões, o que permite aos editores da API distribuir um único serviço de gestão da API Azure em várias regiões apoiadas do Azure. A funcionalidade multi-região ajuda a reduzir a latência de pedidos percebida pelos consumidores de API distribuídos geograficamente e melhora a disponibilidade de serviços se uma região ficar offline.

Um novo serviço de Gestão De API Azure contém inicialmente apenas uma [unidade][unit] numa única região de Azure, a região Primária. Regiões adicionais podem ser adicionadas às regiões primária seleções ou secundárias. Um componente de gateway de gestão da API é implantado em todas as regiões primárias e secundárias selecionadas. Os pedidos de API são automaticamente direcionados para a região mais próxima. Se uma região ficar offline, os pedidos da API serão automaticamente encaminhados para a região falhada para o gateway mais próximo.

> [!NOTE]
> Apenas a componente de gateway da API Management é implantada para todas as regiões. A componente de gestão de serviços e o portal de desenvolvimento estão alojados apenas na região Primária. Por isso, no caso da paralisação da região primária, o acesso ao portal de desenvolvimento e a capacidade de alterar a configuração (por exemplo, adicionar APIs, aplicar políticas) serão prejudicados até que a região Primária volte a funcionar. Enquanto a região primária estiver offline, as regiões secundárias disponíveis continuarão a servir o tráfego da API utilizando a configuração mais recente à sua disposição.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-an-api-management-service-instance-to-a-new-region"></a><a name="add-region"> </a>Implementar uma instância de serviço de Gestão API para uma nova região

> [!NOTE]
> Se ainda não criou uma instância de serviço de Gestão API, consulte Criar uma instância de serviço de [Gestão API][create an api management service instance].

No portal Azure, navegue para a Escala e página **de preços** para a sua instância de serviço de Gestão API.

![Separador de escala][api-management-scale-service]

Para se implantar numa nova região, clique em + Adicione a **região** a partir da barra de ferramentas.

![Adicionar região][api-management-add-region]

Selecione a localização da lista de drop-down e detetete o número de unidades com o slider.

![Especificar unidades][api-management-select-location-units]

Clique em **Adicionar** para colocar a sua seleção na tabela Locais.

Repita este processo até ter todos os locais configurados e clique em **Guardar** a partir da barra de ferramentas para iniciar o processo de implementação.

## <a name="delete-an-api-management-service-instance-from-a-location"></a><a name="remove-region"> </a>Eliminar uma instância de serviço de Gestão API a partir de um local

No portal Azure, navegue para a Escala e página **de preços** para a sua instância de serviço de Gestão API.

![Separador de escala][api-management-scale-service]

Para a localização que deseja remover, abra o menu de contexto usando o **...** botão na extremidade direita da mesa. Selecione a opção **Eliminar.**

Confirme a eliminação e clique **em Guardar** para aplicar as alterações.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Rota API chama serviços regionais de backend

A Azure API Management dispõe apenas de um URL de serviço de backend. Apesar de existirem casos de Gestão API Azure em várias regiões, a porta de entrada da API continuará a encaminhar pedidos para o mesmo serviço de backend, que é implantado em apenas uma região. Neste caso, o ganho de desempenho virá apenas de respostas em cache dentro da Azure API Management, numa região específica do pedido, mas contactar o backend em todo o mundo pode ainda causar alta latência.

Para alavancar totalmente a distribuição geográfica do seu sistema, deve ter serviços de backend implantados nas mesmas regiões que as instâncias de Gestão API Azure. Depois, usando `@(context.Deployment.Region)` políticas e propriedades, pode encaminhar o tráfego para instâncias locais do seu backend.

1. Navegue para a sua instância de Gestão API Azure e clique em **APIs** a partir do menu esquerdo.
2. Selecione a sua API desejada.
3. Clique no editor do **Código** a partir da queda da seta no **processamento de entrada**.

    ![Editor de código API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Utilize `set-backend` o combinado `choose` com políticas condicionais para `<inbound> </inbound>` construir uma política de encaminhamento adequada na secção do ficheiro.

    Por exemplo, o ficheiro XML abaixo funcionaria para as regiões dos EUA Ocidentais e da Ásia Oriental:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> Também pode fazer frente aos seus serviços de backend com o Gestor de [Tráfego Azure,](https://azure.microsoft.com/services/traffic-manager/)direcionar as chamadas da API para o Gestor de Tráfego e deixá-lo resolver o encaminhamento automaticamente.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Utilize o encaminhamento personalizado para gateways regionais de Gestão API

A API Management encaminha os pedidos para um _portal_ regional com base na [latência mais baixa.](../traffic-manager/traffic-manager-routing-methods.md#performance) Embora não seja possível anular este cenário na API Management, pode utilizar o seu próprio Gestor de Tráfego com regras de encaminhamento personalizadas.

1. Crie o seu próprio Gestor de [Tráfego Azure.](https://azure.microsoft.com/services/traffic-manager/)
1. Se estiver a utilizar um domínio personalizado, [utilize-o com o Gestor](../traffic-manager/traffic-manager-point-internet-domain.md) de Tráfego em vez do serviço de Gestão API.
1. [Configure os pontos finais regionais da API Management em Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Os pontos finais regionais seguem o padrão URL de, `https://<service-name>-<region>-01.regional.azure-api.net`por exemplo. `https://contoso-westus2-01.regional.azure-api.net`
1. [Configure os pontos finais do estado regional da API Management no Gestor de Tráfego.](../traffic-manager/traffic-manager-monitoring.md) Os pontos finais do `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`estado regional `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`seguem o padrão URL de, por exemplo.
1. Especifique o método de [encaminhamento](../traffic-manager/traffic-manager-routing-methods.md) do Gestor de Tráfego.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
