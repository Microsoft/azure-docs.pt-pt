---
title: Visão geral da Saúde dos Recursos Azure
description: Saiba como a Azure Resource Health o ajuda a diagnosticar e obter apoio para problemas de serviço que afetam os seus recursos Azure.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 25425eadfaf54ba56cde3b377a44cd85c2b68959
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90967876"
---
# <a name="resource-health-overview"></a>Visão geral da saúde dos recursos
 
A Azure Resource Health ajuda-o a diagnosticar e a obter apoio para problemas de serviço que afetam os seus recursos Azure. Relata a saúde atual e passada dos seus recursos.

[Relatórios de estado do Azure](https://status.azure.com) sobre problemas de serviço que afetam um vasto conjunto de clientes Azure. A Resource Health dá-lhe um dashboard personalizado da saúde dos seus recursos. A Resource Health mostra todas as vezes que os seus recursos não estiveram disponíveis devido a problemas de serviço da Azure. Estes dados facilitam-lhe a mente ver se um SLA foi violado.

## <a name="resource-definition-and-health-assessment"></a>Definição de recursos e avaliação da saúde

Um *recurso* é uma instância específica de um serviço Azure, como uma máquina virtual, uma aplicação web ou uma Base de Dados SQL. A Resource Health baseia-se em sinais de diferentes serviços Azure para avaliar se um recurso é saudável. Se um recurso não for saudável, a Resource Health analisa informações adicionais para determinar a origem do problema. Também relata as ações que a Microsoft está a tomar para corrigir o problema e identifica coisas que pode fazer para o resolver.

Para obter mais informações sobre a forma como a saúde é avaliada, consulte a lista de tipos de recursos e verificações de saúde na [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Estado de funcionamento

A saúde de um recurso é apresentada como um dos seguintes estados.

### <a name="available"></a>Disponível

*Disponível* significa que não são detetados eventos que afetem a saúde do recurso. Nos casos em que o recurso foi recuperado de tempo de inatividade não planeado durante as últimas 24 horas, verá uma notificação "recentemente resolvida".

![Estado de *Disponível* para uma máquina virtual que tem uma notificação "recentemente resolvida"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível

*Indisponível* significa que o serviço detetou uma plataforma em curso ou evento não-plataforma que afeta a saúde do recurso.

#### <a name="platform-events"></a>Eventos da plataforma

Os eventos da plataforma são desencadeados por múltiplos componentes da infraestrutura Azure. Incluem ambas as ações programadas (por exemplo, manutenção planeada) e incidentes inesperados (por exemplo, um reboot de hospedeiro não planeado ou hardware de hospedeiro degradado que se prevê falhar após uma janela de tempo especificada).

A Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Também lhe permite contactar o Microsoft Support mesmo que não tenha um acordo de suporte ativo.

![Estado de *Indisponível* para uma máquina virtual por causa de um evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos não-plataforma

Os eventos não-plataforma são desencadeados por ações do utilizador. Exemplos incluem parar uma máquina virtual ou alcançar o número máximo de ligações a Azure Cache para Redis.

![Estado de "Indisponível" para uma máquina virtual por causa de um evento não-plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconhecido

*Desconhecido* significa que a Resource Health não recebe informações sobre o recurso há mais de 10 minutos. Embora este estado não seja uma indicação definitiva do estado do recurso, é um importante ponto de dados para a resolução de problemas.

Se o recurso estiver a funcionar como esperado, o estado do recurso mudará para *Disponível* após alguns minutos.

Se tiver problemas com o recurso, o estado de saúde *desconhecido* pode significar que um evento na plataforma está a afetar o recurso.

![Estado de *Desconhecido* para uma máquina virtual](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado

*Degradado* significa que o seu recurso detetou uma perda de desempenho, embora ainda esteja disponível para uso.

Os diferentes recursos têm os seus próprios critérios para quando relatam que estão degradados.

![Estado de *Degradado* para uma máquina virtual](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Reportar um estado incorreto

Se achar que o estado de saúde atual está incorreto, pode dizer-nos selecionando **o relatório de estado de saúde incorreto**. Nos casos em que um problema de Azure está a afetá-lo, encorajamo-lo a contactar o Suporte da Saúde dos Recursos.

![Formulário para submeter informações sobre um estado incorreto](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informação de história

Pode aceder até 30 dias de história na secção de História da **Saúde** da Saúde.

![Lista de eventos de Saúde de Recursos nas últimas duas semanas](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Introdução

Para abrir a Saúde dos Recursos para um recurso:

1. Inicie sessão no portal do Azure.
2. Navegue até ao recurso.
3. No menu de recursos no painel esquerdo, selecione **Resource health**.

![Abertura da Saúde dos Recursos a partir da vista de recursos](./media/resource-health-overview/from-resource-blade.png)

Também pode aceder à Saúde dos Recursos selecionando **Todos os serviços** e digitando a **saúde** dos recursos na caixa de texto do filtro. No painel **de apoio Help +,** selecione [Resource health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abertura de Recursos Saúde de "Todos os serviços"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Passos seguintes

Confira estas referências para saber mais sobre a Saúde dos Recursos:
-  [Tipos de recursos e verificações de saúde na Azure Resource Health](resource-health-checks-resource-types.md)
-  [Perguntas frequentes sobre Azure Resource Health](resource-health-faq.md)
