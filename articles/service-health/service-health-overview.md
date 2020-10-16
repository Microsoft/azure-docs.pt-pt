---
title: Visão geral do Serviço de Saúde / Microsoft Docs
description: Saiba como o Service Health lhe fornece um dashboard personalizável que acompanha a saúde dos seus serviços Azure nas regiões onde os utiliza.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 8246b0ab93b95c13858e4ff96d0f24b255d05e55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967797"
---
# <a name="service-health-overview"></a>Visão geral da saúde do serviço

O Service Health fornece-lhe um dashboard personalizável que acompanha a saúde dos seus serviços Azure nas regiões onde os utiliza. Neste painel de instrumentos, você pode rastrear eventos ativos como problemas de serviço em curso, próxima manutenção planeada ou avisos de saúde relevantes. Quando os eventos ficam inativos, são colocados no seu historial de saúde até 90 dias. Finalmente, pode utilizar o painel de instrumentos de saúde do Serviço para criar e gerir alertas de saúde de serviço que o notificam proativamente quando os problemas de serviço o afetam.

## <a name="service-health-events"></a>Eventos de Saúde de Serviço

O Serviço de Saúde acompanha quatro tipos de eventos de saúde que podem afetar os seus recursos:

1. **Problemas de serviço** - Problemas nos serviços Azure que o afetam neste momento. 
2. **Manutenção planeada** - Próxima manutenção que pode afetar a disponibilidade dos seus serviços no futuro.  
3. **Avisos de saúde** - Alterações nos serviços Azure que requerem a sua atenção. Exemplos incluem a depreciação das funcionalidades Azure ou os requisitos de atualização (por exemplo, atualização para um quadro php suportado).
4. **Avisos de segurança** - Notificações ou violações relacionadas com segurança que podem afetar a disponibilidade dos seus serviços Azure.

> [!NOTE]
> Para visualizar os eventos de Saúde do Serviço, os utilizadores devem [ter a função Reader](../role-based-access-control/role-assignments-portal.md) numa subscrição.

## <a name="get-started-with-service-health"></a>Começar com a Saúde do Serviço

Para lançar o seu painel de saúde de serviço, selecione o azulejo de Saúde do Serviço no seu painel de instrumentos do portal. Se já removeu previamente o azulejo ou está a utilizar o painel de instrumentos personalizado, procure o serviço de Saúde de Serviço em "Mais serviços" (em baixo à esquerda no painel de instrumentos).

![Começar com a Saúde do Serviço](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Veja as questões atuais que afetam os seus serviços

A visão **de problemas de Serviço** mostra quaisquer problemas em curso nos serviços Azure que estão a afetar os seus recursos. Compreende-se quando a questão começou e quais os serviços e regiões que são impactados. Também pode ler a mais recente atualização para entender o que o Azure está a fazer para resolver o problema. 

[![Gerir a questão do serviço](./media/service-health-overview/azure-service-health-overview-2.png)](./media/service-health-overview/azure-service-health-overview-2.png#lightbox)

Escolha o separador **de impacto Potencial** para ver a lista específica de recursos que possui que podem ser impactados pelo problema. Você pode baixar uma lista de CSV destes recursos para compartilhar com a sua equipe.

[![Gerir a questão do serviço - Impacto](./media/service-health-overview/azure-service-health-overview-4.png)](./media/service-health-overview/azure-service-health-overview-4.png#lightbox)

## <a name="see-emerging-issues-which-may-impact-your-services"></a>Veja questões emergentes que podem ter impacto nos seus serviços

Existem situações em que problemas de serviço generalizados podem ser publicados na [página do Estado de Azure](https://status.azure.com) antes que as comunicações direcionadas possam ser enviadas para clientes com impacto. Para garantir que o Serviço Azure Health fornece uma visão abrangente de questões que podem afetá-lo, os problemas ativos da página do Estado do Azure são surgidos na Saúde do Serviço como *questões emergentes*. Quando um evento estiver ativo na página do Estado de Azure, um banner de questões emergentes estará presente na Saúde do Serviço. Clique no banner para ver todos os detalhes da questão.

![Questão de serviço emergente](./media/service-health-overview/azure-service-health-emerging-issue.png)

## <a name="get-links-and-downloadable-explanations"></a>Obtenha links e explicações transferíveis 

Pode obter um link para que o problema seja utilizado no seu sistema de gestão de problemas. Você pode baixar ficheiros PDF e, por vezes, CSV para compartilhar com pessoas que não têm acesso ao portal Azure.   

[![Gerir problemas de serviço - Gestão de problemas](./media/service-health-overview/azure-service-health-overview-3.png)](./media/service-health-overview/azure-service-health-overview-3.png#lightbox)

## <a name="get-support-from-microsoft"></a>Obtenha apoio da Microsoft

Contacte o suporte se o seu recurso for deixado em mau estado mesmo depois de o problema ser resolvido.  Utilize os links de suporte no direito da página.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Pin um mapa de saúde personalizado para o seu painel de instrumentos

Serviço de Filtragem Saúde para mostrar as suas subscrições, regiões e tipos de recursos críticos do seu negócio. Guarde o filtro e afixe um mapa global de estado de funcionamento personalizado ao dashboard do portal. 

[![Filtrar mapa de saúde personalizado](./media/service-health-overview/azure-service-health-overview-6a.png)](./media/service-health-overview/azure-service-health-overview-6a.png#lightbox)

![Pin um mapa de saúde personalizado](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurar alertas de saúde do serviço

O Service Health integra-se com o Azure Monitor para alertá-lo através de e-mails, mensagens de texto e notificações webhook quando os seus recursos críticos do negócio são impactados. Configurar um alerta de registo de atividade para o evento de saúde do serviço apropriado. Encaminhe esse alerta para as pessoas apropriadas da sua organização usando Grupos de Ação. Para mais informações, consulte [Alertas de Configuração para Saúde de Serviço](./alerts-activity-log-service-notifications-portal.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Passos seguintes

Adasse alertas para que você é notificado de problemas de saúde. Para mais informações, consulte [as melhores práticas para a criação de Alertas de Saúde do Serviço Azure.](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s) 
