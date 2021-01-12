---
title: Atualizar e escalar um exemplo de Gestão API da Azure ! Microsoft Docs
description: Este tópico descreve como atualizar e escalar uma instância de Gestão API da Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: bd36434bbfe435a53567c46728610627f99f987f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127792"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Atualizar e escalar uma instância de gestão da API Azure  

Os clientes podem escalar uma instância de Gestão API Azure adicionando e removendo unidades. Uma **unidade** é composta por recursos Azure dedicados e tem uma certa capacidade de suporte de carga expressa como uma série de chamadas API por mês. Este número não representa um limite de chamada, mas sim um valor máximo de produção para permitir um planeamento de capacidades bruscos. A produção e a latência reais variam amplamente dependendo de fatores como o número e a taxa de conexões simultâneas, o tipo e o número de políticas configuradas, tamanhos de pedido e resposta, e latência de backend.

A capacidade e o preço de cada unidade dependem do **nível** em que a unidade existe. Pode escolher entre quatro níveis: **Developer**, **Basic,** **Standard,** **Premium**. Se precisar de aumentar a capacidade de um serviço dentro de um nível, deve adicionar uma unidade. Se o nível atualmente selecionado na sua instância de Gestão de API não permitir adicionar mais unidades, precisa de atualizar para um nível mais elevado.

O preço de cada unidade e as funcionalidades disponíveis (por exemplo, implantação multi-região) dependem do nível que escolheu para a sua instância de Gestão de API. O [artigo de detalhes de preços,](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) explica o preço por unidade e as características que obtém em cada nível. 

>[!NOTE]
>O artigo [de detalhes de preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mostra um número aproximado de capacidade unitária em cada nível. Para obter números mais precisos, é preciso olhar para um cenário realista para as suas APIs. Consulte a Capacidade de um artigo [de exemplo de Gestão API da Azure.](api-management-capacity.md)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, deve:

+ Ter uma subscrição ativa do Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Tenha um caso de Gestão da API. Para obter mais informações, consulte [Criar uma instância de Gestão API Azure.](get-started-create-service-instance.md)

+ Compreender o conceito de [Capacidade de uma Instância de Gestão API Azure.](api-management-capacity.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Atualizar e dimensionar  

Pode escolher entre quatro níveis: **Developer,** **Basic,** **Standard** e **Premium.** O nível **de Desenvolvedor** deve ser utilizado para avaliar o serviço; não deve ser utilizado para a produção. O nível **de Desenvolvedor** não tem SLA e não é possível escalar este nível (adicionar/remover unidades). 

**Básico**, **Standard** e **Premium** são níveis de produção que têm SLA e podem ser dimensionados. O nível **Básico** é o nível mais barato com um SLA e pode ser dimensionado até duas unidades, o nível **standard** pode ser dimensionado até quatro unidades. Pode adicionar qualquer número de unidades ao nível **Premium.**

O nível **Premium** permite-lhe distribuir uma única instância de Gestão API Azure em qualquer número de regiões Azure desejadas. Quando inicialmente cria um serviço de Gestão API Azure, o caso contém apenas uma unidade e reside numa única região de Azure. A região inicial é designada como a região **primária.** Regiões adicionais podem ser facilmente adicionadas. Ao adicionar uma região, especifique o número de unidades que pretende alocar. Por exemplo, pode ter uma unidade na região **primária** e cinco unidades em outra região. Pode adaptar o número de unidades ao tráfego que tem em cada região. Para obter mais informações, consulte [Como implementar um serviço de gestão AZure API para várias regiões do Azure.](api-management-howto-deploy-multi-region.md)

Pode atualizar e descer de e para qualquer nível. A atualização ou degradação pode remover algumas funcionalidades - por exemplo, VNETs ou implantação multi-região, ao descer para Standard ou Basic a partir do nível Premium.

> [!NOTE]
> O processo de upgrade ou escala pode demorar de 15 a 45 minutos a aplicar. É notificado quando está feito.

> [!NOTE]
> Serviço de Gestão API nas escalas de nível **de consumo** automaticamente com base no tráfego.

## <a name="scale-your-api-management-service"></a>Dimensione o seu serviço de Gestão API

![Serviço de Gestão de API em escala no portal Azure](./media/upgrade-and-scale/portal-scale.png)

1. Navegue para o seu serviço de Gestão API no [portal Azure.](https://portal.azure.com/)
2. Selecione **Locais** do menu.
3. Clique na linha com a localização que deseja escalar.
4. Especifique o novo número de **unidades** - utilize o slider ou digite o número.
5. Clique em **Aplicar**.

## <a name="change-your-api-management-service-tier"></a>Altere o seu nível de serviço de Gestão API

1. Navegue para o seu serviço de Gestão API no [portal Azure.](https://portal.azure.com/)
2. Clique no **nível de preços** no menu.
3. Selecione o nível de serviço pretendido a partir do dropdown. Utilize o slider para especificar a escala do seu serviço de Gestão API após a alteração.
4. Clique em **Guardar**.

## <a name="downtime-during-scaling-up-and-down"></a>Tempo de paragem durante a escalada para cima e para baixo
Se estiver a escalar de ou para o nível de Desenvolvedor, haverá tempo de inatividade. Caso contrário, não há tempo de descanso. 

## <a name="compute-isolation"></a>Isolamento computacional
Se os seus requisitos de segurança incluirem [isolamento computacional,](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)pode utilizar o nível de preços **isolado.** Este nível garante que os recursos compute de uma instância de serviço de gestão da API consomem todo o hospedeiro físico e fornecem o nível de isolamento necessário para suportar, por exemplo, cargas de trabalho do Departamento de Impacto do Departamento de Defesa dos EUA 5 (IL5). Para ter acesso ao nível isolado, [crie um bilhete de apoio.](../azure-portal/supportability/how-to-create-azure-support-request.md) 



## <a name="next-steps"></a>Passos seguintes

- [Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)
- [Como escalar automaticamente uma instância de serviço da API Azure](api-management-howto-autoscale.md)
- [Planear e gerir custos para a Gestão da API](plan-manage-costs.md)
- [Limites de gestão da API](../azure-resource-manager/management/azure-subscription-service-limits.md#api-management-limits)