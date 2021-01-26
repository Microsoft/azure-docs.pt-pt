---
title: Azure Defender para Gestor de Recursos - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para Gestor de Recursos
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9591dae058a19cea73d88513b7c4ff4ab8f88045
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797755"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Introdução ao Azure Defender para Gestor de Recursos

[Azure Resource Manager](../azure-resource-manager/management/overview.md) é o serviço de implementação e gestão da Azure. Fornece uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na sua conta do Azure. Pode utilizar funcionalidades de gestão, como controlo de acesso, bloqueios e etiquetas, para proteger e organizar os seus recursos após a implementação.

A camada de gestão de nuvens é um serviço crucial ligado a todos os seus recursos em nuvem. Por isso, é também um alvo potencial para os atacantes. Consequentemente, recomendamos que as equipas de operações de segurança monitorizem de perto a camada de gestão de recursos. 

O Azure Defender for Resource Manager monitoriza automaticamente as operações de gestão de recursos na sua organização, quer sejam realizadas através do portal Azure REST, Azure REST APIs, Azure CLI ou outros clientes programáticos Azure. O Azure Defender executa análises avançadas de segurança para detetar ameaças e alertá-lo sobre atividades suspeitas.

## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Pré-visualizar<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preços:|**Azure Defender for Resource Manager** é faturado como mostrado na página de [preços](security-center-pricing.md)|
|Nuvens:|![Yes](./media/icons/yes-icon.png) Nuvens comerciais<br>![No](./media/icons/no-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Quais são os benefícios do Azure Defender para o Gestor de Recursos?

O Azure Defender for Resource Manager protege contra questões como:

- **Operações suspeitas** de gestão de recursos , tais como operações a partir de endereços IP suspeitos, antimalware desativação e scripts suspeitos em extensões de VM
- **Utilização de conjuntos de ferramentas de exploração** como Microburst ou PowerZure
- **Movimento lateral** da camada de gestão Azure para o plano de dados de recursos Azure

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Diagrama de visão geral do Azure Resource Manager":::

Uma lista completa dos alertas fornecidos pelo Azure Defender para o Gestor de Recursos está na página de referência dos [alertas](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Como investigar alertas do Azure Defender para gestor de recursos

Os alertas de segurança do Azure Defender para o Gestor de Recursos baseiam-se em ameaças detetadas através da monitorização das operações do Azure Resource Manager. O Azure Defender utiliza fontes internas de log do Azure Resource Manager, bem como do registo de Atividades Azure, um registo de plataforma no Azure que fornece informações sobre eventos de nível de subscrição.

Saiba mais sobre [o registo de Atividades Azure](../azure-monitor/platform/activity-log.md).

Para investigar alertas de segurança da Azure Defender para o Gestor de Recursos:

1. Abrir registo de atividades Azure.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Como abrir o registo de atividades do Azure":::

1. Filtrar os eventos para:
    - A subscrição mencionada no alerta
    - O prazo da atividade detetada
    - A conta de utilizador relacionada (se relevante)

1. Procure atividades suspeitas.

> [!TIP]
> Para uma melhor e mais rica experiência de investigação, transmita os seus registos de atividade do Azure para o Azure Sentinel, conforme descrito nos [dados do Connect a partir do registo de atividades Azure](../sentinel/connect-azure-activity.md).



## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu sobre Azure Defender para Gestor de Recursos. Para material relacionado, consulte o seguinte artigo: 

- Os alertas de segurança podem ser gerados pelo Security Center ou recebidos pelo Security Center a partir de diferentes produtos de segurança. Para exportar todos estes alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Ativar o Azure Defender](security-center-pricing.md#enable-azure-defender)