---
title: Upgrade e escala uma instância de Gestão API Azure [ Microsoft Docs
description: Este tópico descreve como atualizar e escalar uma instância de Gestão API Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018244"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Upgrade e escala uma instância de Gestão API Azure  

Os clientes podem escalar uma instância de Gestão De API Azure (APIM) adicionando e removendo unidades. Uma **unidade** é composta por recursos Azure dedicados e tem uma certa capacidade de suporte de carga expressa como uma série de chamadas API por mês. Este número não representa um limite de chamada, mas sim um valor máximo de entrada para permitir um planeamento de capacidade brusco. A produção e a latência reais variam em geral dependendo de fatores como o número e a taxa de ligações simultâneas, o tipo e o número de políticas configuradas, tamanhos de pedido e resposta, e latência de backend.

A capacidade e o preço de cada unidade dependem do **nível** em que a unidade existe. Pode escolher entre quatro níveis: **Developer,** **Basic**, **Standard,** **Premium**. Se precisar de aumentar a capacidade de um serviço dentro de um nível, deve adicionar uma unidade. Se o nível atualmente selecionado na sua instância APIM não permitir adicionar mais unidades, precisa de atualizar para um nível mais elevado.

O preço de cada unidade e as funcionalidades disponíveis (por exemplo, implantação em várias regiões) depende do nível que escolheu para a sua instância APIM. O artigo de detalhes de [preços,](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) explica o preço por unidade e as funcionalidades que obtém em cada nível. 

>[!NOTE]
>O artigo [de pormenores](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) sobre preços mostra números aproximados de capacidade unitária em cada nível. Para obter números mais precisos, precisa olhar para um cenário realista para as suas APIs. Consulte a Capacidade de um artigo de gestão da [API Azure.](api-management-capacity.md)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, deve:

+ Tenha uma subscrição azure ativa.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Tenha uma instância da APIM. Para mais informações, consulte Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)

+ Compreender o conceito de Capacidade de uma instância de [Gestão API Azure.](api-management-capacity.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Atualizar e dimensionar  

Pode escolher entre quatro níveis: **Developer,** **Basic,** **Standard** e **Premium.** O nível **de Desenvolvimento** deve ser utilizado para avaliar o serviço; não deve ser utilizado para a produção. O nível **de Desenvolvimento** não tem SLA e não pode escalar este nível (adicionar/remover unidades). 

**Básico**, **Standard** e **Premium** são níveis de produção que têm SLA e podem ser dimensionados. O nível **Básico** é o nível mais barato que tem SLA e pode ser dimensionado até 2 unidades, o nível **Standard** pode ser dimensionado até quatro unidades. Pode adicionar qualquer número de unidades ao nível **Premium.**

O nível **Premium** permite-lhe distribuir uma única instância de Gestão API Azure em várias regiões azure desejadas. Quando inicialmente cria um serviço de Gestão API Azure, a instância contém apenas uma unidade e reside numa única região do Azure. A região inicial é designada como a região **primária.** Regiões adicionais podem ser facilmente adicionadas. Ao adicionar uma região, especifice o número de unidades que pretende alocar. Por exemplo, pode ter uma unidade na região **primária** e cinco unidades em alguma outra região. Pode adaptar o número de unidades ao tráfego que tem em cada região. Para mais informações, consulte como implementar uma instância de serviço de [Gestão API Azure para várias regiões do Azure.](api-management-howto-deploy-multi-region.md)

Pode atualizar e desvalorizar de e para qualquer nível. Note que a atualização ou a degradação podem remover algumas funcionalidades - por exemplo, VNETs ou implantação multi-região, ao degradar-se para Standard ou Basic do nível Premium.

> [!NOTE]
> O processo de atualização ou escala pode demorar de 15 a 45 minutos a aplicar. É notificado quando estiver feito.

> [!NOTE]
> Serviço de Gestão API nas escalas de nível de **consumo** automaticamente com base no tráfego.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Use o portal Azure para atualizar e escalar

![Escala APIM no portal Azure](./media/upgrade-and-scale/portal-scale.png)

1. Navegue para a sua instância APIM no [portal Azure.](https://portal.azure.com/)
2. Selecione **Escala e preços** no menu.
3. Escolha o nível desejado.
4. Especifique o número de **unidades** que pretende adicionar. Pode utilizar o slider ou escrever o número de unidades.  
    Se escolher o nível **Premium,** primeiro terá de selecionar uma região.
5. Prima **Guardar**.

## <a name="downtime-during-scaling-up-and-down"></a>Tempo de inatividade durante a escala para cima e para baixo
Se estiver a escalonar de ou para o nível de Desenvolvimento, haverá tempo de inatividade. Caso contrário, não há tempo de descanso. 


## <a name="next-steps"></a>Passos seguintes

- [Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)
- [Como escalar automaticamente uma instância de serviço de Gestão API Azure](api-management-howto-autoscale.md)
