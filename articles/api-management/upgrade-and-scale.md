---
title: Atualizar e dimensionar uma instância de gestão de API do Azure | Documentos da Microsoft
description: Este tópico descreve como atualizar e dimensionar uma instância de gestão de API do Azure.
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
ms.openlocfilehash: 99848cf4ba1e6e65a8c41c682916ca391128eb21
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056528"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Atualizar e dimensionar uma instância de gestão de API do Azure  

Os clientes podem aumentar uma instância de gestão de API do Azure (APIM) ao adicionar e remover unidades. R **unidade** é composto por recursos do Azure dedicados e tem uma determinada carga-orientação de capacidade, expressada como um número de API de chamadas por mês. Este número não representa um limite de chamada, mas em vez disso, um valor de débito máximo para permitir o planeamento de capacidade aproximada. O débito Efetivo e latência amplamente variam dependendo de fatores como o número e a velocidade das ligações simultâneas, o tipo e número de políticas configuradas, os tamanhos de solicitação e resposta e latência de back-end.

Capacidade e o preço de cada unidade depende da **escalão** no qual existe a unidade. Pode escolher entre quatro escalões: **desenvolvedor**, **básica**, **padrão**, **Premium**. Se precisar de aumentar a capacidade para um serviço dentro de uma camada, deve adicionar uma unidade. Se a camada atualmente selecionado na sua instância APIM não permite a adição de mais unidades, terá de atualizar para um escalão de nível superior.

O preço de cada unidade e os recursos disponíveis (por exemplo, a implementação de várias regiões) depende do escalão que escolheu para a instância APIM. O [os detalhes dos preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) article, explica o preço por unidade e funcionalidades que pode obter em cada escalão. 

>[!NOTE]
>O [os detalhes dos preços](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) artigo mostra o número aproximado de capacidade de unidade em cada escalão. Para obter números mais precisos, precisa examinar um cenário realista para as suas APIs. Consulte a [capacidade de uma instância de gestão de API do Azure](api-management-capacity.md) artigo.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, tem de:

+ Ter uma subscrição do Azure Active Directory.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Ter uma instância APIM. Para obter mais informações, consulte [criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).

+ Compreender o conceito de [capacidade de uma instância de gestão de API do Azure] (capacity.md de gestão de api).

## <a name="upgrade-and-scale"></a>Atualizar e dimensionar  

Pode escolher entre quatro escalões: **desenvolvedor**, **básica**, **padrão** e **Premium**. O **desenvolvedor** camada deve ser utilizada para avaliar o serviço; não deve ser utilizada para produção. O **desenvolvedor** escalão não tem SLA e não pode expandir este escalão (unidades de adicionar/remover). 

**Básica**, **padrão** e **Premium** são os escalões de produção com o SLA e podem ser dimensionados. O **básica** camada é a camada mais barato que tem o SLA e pode ser dimensionado até 2 unidades, **padrão** camada pode ser dimensionada para até quatro unidades. Pode adicionar qualquer número de unidades para o **Premium** escalão.

O **Premium** escalão permite-lhe distribuir uma única instância de gestão de API do Azure em qualquer número de regiões do Azure pretendidos. Quando cria um serviço de gestão de API do Azure inicialmente, a instância contém apenas uma unidade e reside numa única região do Azure. A região inicial é designada como o **primário** região. Regiões adicionais podem ser facilmente adicionados. Quando adicionar uma região, especifica o número de unidades que pretende atribuir. Por exemplo, pode ter uma unidade **primário** região e cinco unidades de alguma outra região. Pode personalizar o número de unidades para o tráfego que tiver em cada região. Para obter mais informações, consulte [como implementar uma instância de serviço de gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md).

Pode atualizar e mudar para a versão de e para qualquer camada. Tenha em atenção que a atualização ou fazer downgrade pode remover algumas funcionalidades - por exemplo, VNETs ou implementação em várias regiões, quando a mudança para Standard ou Basic do escalão Premium.

>[!NOTE]
>O processo de atualização ou de dimensionamento pode demorar de 15 a 45 minutos a aplicar. Receber notificação quando terminar.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Utilizar o portal do Azure para atualizar e dimensionar

![Dimensionamento APIM no portal do Azure](./media/upgrade-and-scale/portal-scale.png)

1. Navegue para a instância APIM no [portal do Azure](https://portal.azure.com/).
2. Selecione **dimensionamento e preços** no menu.
3. Escolha o escalão desejado.
4. Especifique o número de **unidades** que pretende adicionar. Pode utilizar o controlo de deslize ou escreva o número de unidades.  
    Se escolher a **Premium** escalão, primeiro tem de selecionar uma região.
5. Prima **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

*[Como implementar uma instância de serviço de gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)
*[como Dimensionar automaticamente uma instância de serviço de gestão de API do Azure](api-management-howto-autoscale.md)