---
title: Atualizar e dimensionar uma instância de gerenciamento de API do Azure | Microsoft Docs
description: Este tópico descreve como atualizar e dimensionar uma instância de gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018244"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Atualizar e dimensionar uma instância de gerenciamento de API do Azure  

Os clientes podem dimensionar uma instância do APIM (gerenciamento de API do Azure) adicionando e removendo unidades. Uma **unidade** é composta de recursos dedicados do Azure e tem uma determinada capacidade de carga expressa como um número de chamadas à API por mês. Esse número não representa um limite de chamada, mas sim um valor máximo de taxa de transferência para permitir o planejamento de capacidade aproximada. A taxa de transferência e a latência reais variam em larga escala, dependendo de fatores como número e taxa de conexões simultâneas, o tipo e o número de políticas configuradas, tamanhos de solicitação e resposta e latência de back-end.

A capacidade e o preço de cada unidade dependem da **camada** na qual a unidade existe. Você pode escolher entre quatro camadas: **Developer**, **básico**, **Standard**, **Premium**. Se precisar aumentar a capacidade de um serviço dentro de uma camada, você deverá adicionar uma unidade. Se a camada selecionada no momento em sua instância do APIM não permitir a adição de mais unidades, você precisará atualizar para uma camada de nível superior.

O preço de cada unidade e os recursos disponíveis (por exemplo, implantação em várias regiões) depende da camada que você escolheu para sua instância do APIM. O artigo [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) explica o preço por unidade e os recursos que você obtém em cada camada. 

>[!NOTE]
>O artigo [detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mostra números aproximados de capacidade de unidade em cada camada. Para obter números mais precisos, você precisa examinar um cenário realista para suas APIs. Consulte o artigo [capacidade de uma instância de gerenciamento de API do Azure](api-management-capacity.md) .

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste artigo, você deve:

+ Ter uma assinatura ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Ter uma instância de APIM. Para obter mais informações, consulte [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).

+ Entenda o conceito de [capacidade de uma instância de gerenciamento de API do Azure](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Atualizar e dimensionar  

Você pode escolher entre quatro camadas: **Developer**, **básico**, **Standard** e **Premium**. A camada de **desenvolvedor** deve ser usada para avaliar o serviço; Ele não deve ser usado para produção. A camada de **desenvolvedor** não tem SLA e você não pode dimensionar essa camada (Adicionar/remover unidades). 

**Basic**, **Standard** e **Premium** são as camadas de produção que têm SLA e podem ser dimensionadas. A camada **básica** é a camada mais barata que tem SLA e pode ser dimensionada para até 2 unidades, a camada **Standard** pode ser dimensionada para até quatro unidades. Você pode adicionar qualquer número de unidades à camada **Premium** .

A camada **Premium** permite distribuir uma única instância de gerenciamento de API do Azure em qualquer número de regiões do Azure desejadas. Quando você cria inicialmente um serviço de gerenciamento de API do Azure, a instância contém apenas uma unidade e reside em uma única região do Azure. A região inicial é designada como a região **primária** . Regiões adicionais podem ser facilmente adicionadas. Ao adicionar uma região, você especifica o número de unidades que deseja alocar. Por exemplo, você pode ter uma unidade na região **primária** e cinco unidades em alguma outra região. Você pode personalizar o número de unidades para o tráfego que você tem em cada região. Para obter mais informações, consulte [como implantar uma instância do serviço de gerenciamento de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md).

Você pode atualizar e fazer downgrade de e para qualquer camada. Observe que a atualização ou o downgrade pode remover alguns recursos, por exemplo, VNETs ou implantação de várias regiões, ao fazer downgrade para Standard ou básico da camada Premium.

> [!NOTE]
> O processo de atualização ou de dimensionamento pode demorar de 15 a 45 minutos a aplicar. Você será notificado quando terminar.

> [!NOTE]
> O serviço de gerenciamento de API na camada de **consumo** é dimensionado automaticamente com base no tráfego.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Usar o portal do Azure para atualizar e dimensionar

![Dimensionar APIM em portal do Azure](./media/upgrade-and-scale/portal-scale.png)

1. Navegue até sua instância do APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **escala e preços** no menu.
3. Escolha a camada desejada.
4. Especifique o número de **unidades** que você deseja adicionar. Você pode usar o controle deslizante ou digitar o número de unidades.  
    Se você escolher a camada **Premium** , precisará primeiro selecionar uma região.
5. Prima **Guardar**.

## <a name="downtime-during-scaling-up-and-down"></a>Tempo de inatividade durante a expansão e a redução
Se você estiver dimensionando de ou para a camada de desenvolvedor, haverá tempo de inatividade. Caso contrário, não há nenhum tempo de inatividade. 


## <a name="next-steps"></a>Passos seguintes

- [Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)
- [Como dimensionar automaticamente uma instância do serviço de gerenciamento de API do Azure](api-management-howto-autoscale.md)
