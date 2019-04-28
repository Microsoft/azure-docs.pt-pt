---
title: Descrição geral do Estado de funcionamento de recursos do Azure | Documentos da Microsoft
description: Descrição geral do Estado de funcionamento de recursos do Azure
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 11/16/2018
ms.openlocfilehash: d2a77e831290aa1ee0fcb6d4addf8f6e90786d52
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119858"
---
# <a name="azure-resource-health-overview"></a>Descrição geral do Estado de funcionamento de recursos do Azure
 
Estado de funcionamento de recursos do Azure ajuda-o a diagnosticar e obter suporte quando um problema de serviço do Azure afeta os seus recursos. Informa-o sobre o estado de funcionamento atual e anterior dos seus recursos. E fornece suporte técnico para o ajudar a atenuar os problemas.

Enquanto [estado do Azure](https://status.azure.com) informa-sobre os problemas de serviço que afetam um vasto leque de clientes do Azure, o estado de funcionamento do recurso dá-lhe um dashboard personalizado do Estado de funcionamento dos seus recursos. O Resource Health mostra-lhe sempre que os recursos estiveram indisponíveis anteriormente devido a problemas do serviço Azure. Em seguida, é simples de compreender se um SLA foi violado. 

## <a name="resource-definition-and-health-assessment"></a>Avaliação de estado de funcionamento e a definição do recurso
Um recurso é uma instância específica de um serviço do Azure: por exemplo, uma máquina virtual, uma aplicação web ou uma base de dados SQL.

Estado de funcionamento do recurso baseia-se em sinais emitidas pelos diferentes serviços do Azure para avaliar se um recurso está em bom estado ou não. Se um recurso está danificado, o Resource Health analisa as informações adicionais para determinar a origem do problema. Ele também identifica ações que a Microsoft está a demorar para corrigir o problema ou as ações que pode tomar para resolver a causa do problema. 

Para obter mais informações sobre como o estado de funcionamento é analisado, analise a lista completa de tipos de recursos e verificações do Estado de funcionamento no [do Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Estado de funcionamento
O estado de funcionamento de um recurso é apresentado como um dos seguintes Estados.

### <a name="available"></a>Disponível
Estado **disponível** significa que o serviço ainda não detetou quaisquer eventos que afetam o estado de funcionamento do recurso. Em casos em que o recurso recuperou de um período de indisponibilidade não planeado durante as últimas 24 horas, consulte a **resolvidos recentemente** notificação.

![Estado de "Disponível" para uma máquina virtual com uma notificação de "Resolvido recentemente"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível
Estado **indisponível** significa que o serviço detetou uma plataforma em curso ou um evento de plataforma de não que afeta o estado de funcionamento do recurso.

#### <a name="platform-events"></a>Eventos de plataforma
Eventos de plataforma são acionados por vários componentes da infraestrutura do Azure. Elas incluem ações agendadas (por exemplo, a manutenção planeada) e incidentes inesperados (por exemplo, um reinício do anfitrião não planeada).

Estado de funcionamento do recurso fornece detalhes adicionais sobre o evento e o processo de recuperação. Ele também permite-lhe contactar o suporte, mesmo se não tiver um contrato de suporte de Microsoft Active Directory.

![Estado de "Não disponível" para uma máquina virtual devido a um evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos de plataforma não
Eventos de plataforma não são acionados por ações dos utilizadores. Exemplos sejam parar uma máquina virtual ou atingir o número máximo de ligações a uma Cache do Azure para Redis.

![Estado de "Não disponível" para uma máquina virtual devido a um evento de plataforma de não](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconhecidos
O estado de funcionamento das **desconhecido** indica que o estado de funcionamento do recurso não recebe informações sobre este recurso para mais de 10 minutos. Embora este estado não é uma indicação definitiva do Estado do recurso, é um ponto de dados importantes no processo de resolução de problemas.

Se o recurso está em execução conforme esperado, o estado do recurso será alterado para **disponível** após alguns minutos.

Se estiver a ter problemas com o recurso, o **desconhecido** estado de funcionamento pode sugerir que um evento na plataforma está a afetar o recurso.

![Estado de "Desconhecido" para uma máquina virtual](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado
O estado de funcionamento das **Degraded** indica que o seu recurso detetou uma perda de desempenho, embora ele ainda está disponível para utilização.
Recursos diferentes têm seus próprios critérios para quando especificar que um recurso está degradado.

![Estado de "Degraded" para uma máquina virtual](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Comunicar um Estado incorreto
Se acreditar que o atual estado de funcionamento incorreto, pode fale conosco selecionando **comunicou o estado de funcionamento incorreto**. Em casos em que um problema do Azure está a afetar, recomendamos que contacte o suporte do Estado de funcionamento do recurso. 

![Caixa para o envio de informações sobre um Estado incorreto](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Informações do histórico
Pode acessar até 14 dias do histórico de estado de funcionamento no **histórico de estado de funcionamento** secção do Estado de funcionamento do recurso. 

![Lista de eventos de estado de funcionamento do recurso durante as últimas duas semanas](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introdução
Para abrir o estado de funcionamento do recurso para um recurso:
1.  Inicie sessão no Portal do Azure.
2.  Navegue até ao recurso.
3.  No menu de recursos no painel esquerdo, selecione **estado de funcionamento do recurso**.

![Estado de funcionamento de recursos de abertura da vista do recurso](./media/resource-health-overview/from-resource-blade.png)

Também pode acessar o estado de funcionamento do recurso, selecionando **todos os serviços** e escrever **estado de funcionamento do recurso** na caixa de texto de filtro. Na **ajuda + suporte** painel, selecione [estado de funcionamento do recurso](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Estado de funcionamento de recursos de abertura de "Todos os serviços"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Passos Seguintes

Veja estes recursos para saber mais sobre o estado de funcionamento do recurso:
-  [Tipos de recursos e o estado de funcionamento verificações no Azure Resource Health](resource-health-checks-resource-types.md)
-  [Perguntas mais frequentes sobre o Azure Resource Health](resource-health-faq.md)




