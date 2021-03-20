---
title: Implementar serviços de Gestão API da Azure em várias regiões do Azure
titleSuffix: Azure API Management
description: Saiba como implementar um serviço de gestão AZure API para várias regiões do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 427ebfe865002612be2f9aeb9db416f5c2f41e52
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88065459"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure

A Azure API Management suporta a implantação multi-região, o que permite aos editores da API distribuir um único serviço de gestão AZure API em qualquer número de regiões de Azure apoiadas. A funcionalidade multi-região ajuda a reduzir a latência de pedidos percebida pelos consumidores de API distribuídos geograficamente e melhora a disponibilidade de serviços se uma região ficar offline.

Um novo serviço de Gestão AZURE API contém inicialmente apenas uma [unidade][unit] numa única região de Azure, a região primária. Podem ser adicionadas unidades adicionais às regiões primárias ou secundárias. Um componente de gateway de gestão API é implantado em todas as regiões primárias e secundárias selecionadas. Os pedidos de API de entrada são automaticamente direcionados para a região mais próxima. Se uma região ficar offline, os pedidos da API serão automaticamente encaminhados em torno da região falhada para o portão mais próximo.

> [!NOTE]
> Apenas a componente de gateway da API Management é implantada em todas as regiões. A componente de gestão de serviços e o portal de desenvolvedores estão hospedados apenas na região Primária. Assim, em caso de paralisação da região primária, o acesso ao portal do desenvolvedor e a capacidade de alterar a configuração (por exemplo, a adição de APIs, políticas de aplicação) serão prejudicados até que a região primária volte a funcionar. Enquanto a região primária estiver offline, as regiões secundárias disponíveis continuarão a servir o tráfego da API utilizando a configuração mais recente disponível para eles.

>[!IMPORTANT]
> A funcionalidade para permitir o armazenamento de dados de clientes numa única região está atualmente disponível apenas na Região do Sudeste Asiático (Singapura) da Asia Pacific Geo. Para todas as outras regiões, os dados dos clientes são armazenados na Geo.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-api-management-service-to-a-new-region"></a><a name="add-region"> </a>Implementar serviço de Gestão da API para uma nova região

> [!NOTE]
> Se ainda não criou uma instância de serviço de Gestão API, consulte [criar uma instância de serviço de Gestão API.][create an api management service instance]

1. No portal Azure, navegue para o seu serviço de Gestão API e clique na entrada **Localização** no menu.
2. Clique **+ Adicione** na barra superior.
3. Selecione a localização da lista de drop-down e desa estale o número de unidades com o slider.
4. Clique no botão **Adicionar** para confirmar.
5. Repita este processo até configurar todos os locais.
6. Clique em **Guardar** na barra superior para iniciar o processo de implementação.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Excluir uma localização de serviço de gestão API

1. No portal Azure, navegue para o seu serviço de Gestão API e clique na entrada **Localização** no menu.
2. Para a localização que pretende remover, abra o menu de contexto utilizando o **botão ...** na extremidade direita da tabela. Selecione a opção **Eliminar.**
3. Confirme a eliminação e clique **em Guardar** para aplicar as alterações.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Rota API chama para serviços regionais de backend

A Azure API Management dispõe de apenas um URL de serviço de backend. Apesar de existirem instâncias de Gestão da API da Azure em várias regiões, a porta de entrada da API continuará a encaminhar pedidos para o mesmo serviço de backend, que é implantado em apenas uma região. Neste caso, o ganho de desempenho virá apenas de respostas em cache dentro da Azure API Management numa região específica ao pedido, mas contactar o backend em todo o mundo pode ainda causar alta latência.

Para alavancar plenamente a distribuição geográfica do seu sistema, deverá ter serviços de backend implantados nas mesmas regiões que os casos de Gestão API da Azure. Em seguida, usando políticas e `@(context.Deployment.Region)` propriedades, você pode encaminhar o tráfego para instâncias locais do seu backend.

1. Navegue para a sua instância de Gestão AZure API e clique em **APIs** a partir do menu esquerdo.
2. Selecione a API desejada.
3. Clique **em Code editor** a partir da queda da seta no processamento de **Entrada**.

    ![Editor de código da API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Utilize o `set-backend` combinado com políticas condicionais para construir uma `choose` política de encaminhamento adequada na secção `<inbound> </inbound>` do ficheiro.

    Por exemplo, o ficheiro XML abaixo funcionaria para as regiões dos EUA e da Ásia Oriental:

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
> Pode também fazer frente aos seus serviços de backend com o [Azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)direcione as chamadas da API para o Gestor de Tráfego e deixe-o resolver automaticamente o encaminhamento.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Utilize encaminhamento personalizado para gateways regionais da API Management

A API Management encaminha os pedidos para uma _porta de entrada_ regional com base na [latência mais baixa.](../traffic-manager/traffic-manager-routing-methods.md#performance) Embora não seja possível anular esta definição na API Management, pode utilizar o seu próprio Gestor de Tráfego com regras de encaminhamento personalizados.

1. Crie o seu próprio [Gestor de Tráfego Azure.](https://azure.microsoft.com/services/traffic-manager/)
1. Se estiver a utilizar um domínio personalizado, [use-o com o Gestor](../traffic-manager/traffic-manager-point-internet-domain.md) de Tráfego em vez do serviço de Gestão API.
1. [Configurar os pontos finais regionais da API Management em Gestor de Tráfego.](../traffic-manager/traffic-manager-manage-endpoints.md) Os pontos finais regionais seguem o padrão url `https://<service-name>-<region>-01.regional.azure-api.net` de, por `https://contoso-westus2-01.regional.azure-api.net` exemplo.
1. [Configurar os pontos finais do estatuto regional da API Management em Gestor de Tráfego.](../traffic-manager/traffic-manager-monitoring.md) Os pontos finais do estatuto regional seguem o padrão url `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` de, por `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` exemplo.
1. Especificar o método de [encaminhamento](../traffic-manager/traffic-manager-routing-methods.md) do Gestor de Tráfego.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
