---
title: Visão geral da integridade do serviço | Microsoft Docs
description: Informações personalizadas sobre como seus aplicativos do Azure são afetados por problemas e manutenção atuais e futuros do serviço do Azure.
author: stephbaron
ms.author: stbaron
services: service-health
ms.service: service-health
ms.topic: article
ms.date: 05/10/2019
ms.openlocfilehash: 3e3c83b7233ad4da263dec31c6012209ebaca0fe
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515844"
---
# <a name="service-health-overview"></a>Visão geral da integridade do serviço

A integridade do serviço fornece um painel personalizável que controla a integridade dos serviços do Azure nas regiões em que você os utiliza. Nesse painel, você pode acompanhar eventos ativos, como problemas de serviço contínuos, manutenção planejada futura ou comunicados de integridade relevantes. Quando os eventos se tornam inativos, eles são colocados em seu histórico de integridade por até 90 dias. Por fim, você pode usar o painel de integridade do serviço para criar e gerenciar alertas de integridade do serviço que notificam proativamente quando problemas de serviço estão afetando você.

## <a name="service-health-events"></a>Eventos de integridade do serviço

A integridade do serviço rastreia três tipos de eventos de integridade que podem afetar seus recursos:

1. **Problemas de serviço** -problemas nos serviços do Azure que afetam você no momento. 
2. **Manutenção planejada** – manutenção futura que pode afetar a disponibilidade de seus serviços no futuro.  
3. **Comunicados de integridade** – alterações nos serviços do Azure que exigem sua atenção. Os exemplos incluem quando os recursos do Azure são preteridos ou se você excede uma cota de uso.

> [!NOTE]
> Para exibir eventos de integridade do serviço, os usuários devem receber [a função leitor](../role-based-access-control/role-assignments-portal.md) em uma assinatura.

## <a name="get-started-with-service-health"></a>Introdução à integridade do serviço

Para iniciar o painel de integridade do serviço, selecione o bloco integridade do serviço no painel do Portal. Se você tiver removido o bloco anteriormente ou estiver usando o painel personalizado, procure serviço de integridade do serviço em "mais serviços" (no canto inferior esquerdo no painel).

![Introdução à integridade do serviço](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Veja os problemas atuais que afetam seus serviços

A exibição **problemas de serviço** mostra os problemas em andamento nos serviços do Azure que estão afetando seus recursos. Você pode entender quando o problema começou e quais serviços e regiões são afetados. Você também pode ler a atualização mais recente para entender o que o Azure está fazendo para resolver o problema. 

![Gerenciar problema de serviço](./media/service-health-overview/azure-service-health-overview-2.png)

Escolha a guia **impacto potencial** para ver a lista específica de recursos que você possui que podem ser afetados pelo problema. Você pode baixar uma lista CSV desses recursos para compartilhar com sua equipe.

![Gerenciar problema de serviço-impacto](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Obter links e explicações para download 

Você pode obter um link para o problema a ser usado no sistema de gerenciamento de problemas. Você pode baixar PDF e, às vezes, arquivos CSV para compartilhar com pessoas que não têm acesso ao portal do Azure.   

![Gerenciar problema de serviço-gerenciamento de problemas](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Obtenha suporte da Microsoft

Contate o suporte se o recurso for deixado em um estado inadequado mesmo depois que o problema for resolvido.  Use os links de suporte à direita da página.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Fixar um mapa de integridade personalizado em seu painel

Filtre a integridade do serviço para mostrar suas assinaturas, regiões e tipos de recursos críticos para os negócios. Salve o filtro e fixe um mapa do World Health personalizado no painel do Portal. 

![Filtrar mapa de integridade personalizado](./media/service-health-overview/azure-service-health-overview-6a.png)

![Fixar um mapa de integridade personalizado](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurar alertas de integridade do serviço

A integridade do serviço integra-se com o Azure Monitor para alertá-lo por emails, mensagens de texto e notificações de webhook quando seus recursos críticos para os negócios são afetados. Configure um alerta do log de atividades para o evento de integridade do serviço apropriado. Encaminhe esse alerta para as pessoas apropriadas em sua organização usando grupos de ação. Para obter mais informações, consulte [configurar alertas para integridade do serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Passos seguintes

Configure alertas para que você seja notificado sobre problemas de integridade. Para obter mais informações, consulte [práticas recomendadas para configurar alertas de integridade do serviço do Azure](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
