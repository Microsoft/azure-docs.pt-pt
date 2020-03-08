---
title: Visão geral da Saúde do Serviço  Microsoft Docs
description: Informações personalizadas sobre como as suas aplicações Azure são afetadas por problemas e manutenção de serviços atuais e futuros do Azure.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a1c6a10ab48125c030c9a35864f829438404ad97
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898447"
---
# <a name="service-health-overview"></a>Visão geral da Saúde do Serviço

O Serviço De Saúde fornece-lhe um dashboard personalizável que acompanha a saúde dos seus serviços Azure nas regiões onde os utiliza. Neste dashboard, você pode rastrear eventos ativos como questões de serviço em curso, manutenção planeada próxima ou avisos de saúde relevantes. Quando os eventos ficam inativos, são colocados no seu histórico de saúde por até 90 dias. Finalmente, pode utilizar o dashboard Service Health para criar e gerir alertas de saúde de serviço que o notificam proativamente quando os problemas de serviço estão a afetá-lo.

## <a name="service-health-events"></a>Eventos de Saúde de Serviço

Serviço Saúde rastreia quatro tipos de eventos de saúde que podem afetar os seus recursos:

1. **Problemas** de serviço - Problemas nos serviços Azure que o afetam neste momento. 
2. **Manutenção planeada** - Manutenção futura que poderá afetar a disponibilidade dos seus serviços no futuro.  
3. **Avisos de saúde** - Alterações nos serviços Azure que requerem a sua atenção. Exemplos incluem quando as funcionalidades do Azure são depreciadas ou se exceder uma quota de utilização.
4. **Avisos de segurança (pré-visualização)** - Notificações relacionadas com a segurança que podem afetar a disponibilidade dos seus serviços Azure.

> [!NOTE]
> Para ver os eventos de Saúde de Serviço, os utilizadores devem receber [o papel de Leitor](../role-based-access-control/role-assignments-portal.md) numa subscrição.

## <a name="get-started-with-service-health"></a>Começar com a Saúde do Serviço

Para lançar o seu dashboard Service Health, selecione o azulejo de Saúde de Serviço no seu portal dashboard. Se já removeu previamente o azulejo ou estiver a utilizar o dashboard personalizado, procure o serviço de saúde de serviço em "Mais serviços" (inferior à esquerda no seu painel).

![Começar com a Saúde do Serviço](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Veja as questões atuais que impactam os seus serviços

A visão **de questões** de Serviço mostra quaisquer problemas em curso nos serviços Azure que estão a afetar os seus recursos. Compreende-se quando a questão começou e que serviços e regiões são impactados. Pode também ler a mais recente atualização para perceber o que o Azure está a fazer para resolver o problema. 

![Gerir a emissão de serviço](./media/service-health-overview/azure-service-health-overview-2.png)

Escolha o separador **de impacto Potencial** para ver a lista específica de recursos que possui que podem ser impactados pelo problema. Você pode baixar uma lista de CSV destes recursos para partilhar com a sua equipa.

![Gerir a emissão de serviço - Impacto](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Obtenha links e explicações transferíveis 

Pode obter um link para o problema usar no seu sistema de gestão de problemas. Você pode baixar ficheiros PDF e, por vezes, CSV para partilhar com pessoas que não têm acesso ao portal Azure.   

![Gerir a questão do serviço - Gestão de problemas](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Obtenha suporte da Microsoft

Suporte de contato se o seu recurso for deixado em mau estado mesmo após a resolução do problema.  Utilize os links de suporte à direita da página.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Coloque um mapa de saúde personalizado no seu painel de instrumentos

Serviço de Filtro Saúde para mostrar as suas assinaturas, regiões e tipos de recursos críticos do negócio. Guarde o filtro e coloque um mapa do mundo da saúde personalizado no seu portal dashboard. 

![Filtrar mapa de saúde personalizado](./media/service-health-overview/azure-service-health-overview-6a.png)

![Pin um mapa de saúde personalizado](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurar alertas de saúde de serviço

O Service Health integra-se com o Azure Monitor para o alertar através de e-mails, mensagens de texto e notificações de webhook quando os seus recursos críticos de negócio são impactados. Instale um alerta de registo de atividade para o evento de saúde de serviço apropriado. Encaminhe esse alerta para as pessoas apropriadas da sua organização usando Grupos de Ação. Para mais informações, consulte [Configure Alerts para a Saúde do Serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Passos seguintes

Instale alertas para que seja notificado de problemas de saúde. Para mais informações, consulte [as melhores práticas para a criação de Alertas de Saúde do Serviço Azure](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
