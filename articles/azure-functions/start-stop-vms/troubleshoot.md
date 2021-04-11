---
title: Início/Paragem de VMs de resolução de problemas (pré-visualização)
description: Este artigo diz como resolver problemas encontrados com a funcionalidade Iniciar/Parar VMs (pré-visualização) para os seus VMs Azure.
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111800"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Resolver problemas comuns com VMs de início/paragem (pré-visualização)

Este artigo fornece informações sobre resolução de problemas e resolução de problemas que podem ocorrer enquanto tenta instalar e configurar VMs iniciar/parar (pré-visualização). Para obter informações [gerais, consulte a visão geral dos VMs iniciar/parar](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Validação geral e resolução de problemas

Esta secção abrange como resolver problemas gerais com os cenários dos horários e ajudar a identificar a causa principal.

### <a name="azure-dashboard"></a>Dashboard do Azure

Pode começar por rever o painel partilhado do Azure. O painel de instrumentos partilhado Azure implantado como parte de VMs de início/paragem v2 (pré-visualização) é uma forma rápida e fácil de verificar o estado de cada operação que é realizada nos seus VMs. Consulte as **ações recentemente tentadas em VMs** para ver todas as operações recentes executadas nos seus VMs. Existe alguma latência, cerca de cinco minutos, para que os dados apareçam no relatório à medida que retiram dados do recurso Application Insights.

### <a name="logic-apps"></a>Logic Apps

Dependendo das Aplicações Lógicas que habilitado a suportar o seu cenário de início/paragem, pode rever o seu histórico de execução para ajudar a identificar por que razão o cenário de arranque/paragem programado não completou com sucesso para um ou mais VMs alvo. Para aprender a rever isto em detalhe, veja [as Aplicações Lógicas executar a história.](../../logic-apps/monitor-logic-apps.md#review-runs-history)

### <a name="azure-storage"></a>Armazenamento do Azure

Pode rever os detalhes das operações realizadas nos VMs que são escritas para a mesa **de pedidos** na conta de armazenamento Azure utilizada para VMs de início/paragem v2 (pré-visualização). Execute os seguintes passos para ver os registos.

1. Navegue para a conta de armazenamento no portal Azure e na conta selecione **Storage Explorer (pré-visualização) a partir do painel esquerdo.
1. Selecione **TABLES** e, em seguida, selecione **requeststoretable**.
1. Cada registo na tabela representa a ação de início/paragem realizada contra um VM Azure com base no âmbito-alvo definido no cenário da aplicação lógica. Pode filtrar os resultados por qualquer uma das propriedades de gravação (por exemplo, TIMETAMP, ACTION ou TARGETTOPLEVELRESOURCENAME).

### <a name="azure-functions"></a>Funções do Azure

Pode rever os mais recentes detalhes de invocação de qualquer uma das Funções Azure responsáveis pelo início do VM e parar a execução. Primeiro vamos rever o fluxo de execução.

O fluxo de execução para o cenário **programado** e **sequenciado** é controlado pela mesma função. O esquema de carga útil é o que determina qual o cenário que é realizado. Para o cenário **programado,** o fluxo de execução é - **HTTP agendado** > **Fila virtualMachineRequestOrchestrator** > **VirtualMachineRequestExecutor** Queue.

A partir da aplicação lógica, a função HTTP **Agendada** é invocada com esquema de carga útil. Uma vez que a função HTTP **Programada** recebe o pedido, envia a informação para a função de fila **do Orquestrador,** que por sua vez cria várias filas para cada VM realizar a ação.

Execute os seguintes passos para ver os detalhes da invocação.

1. No portal Azure, navegue para as **Funções Azure**.
1. Selecione o aplicativo Função para Iniciar/Parar VMs v2 (pré-visualização) da lista.
1. Selecione **Funções** a partir do painel da esquerda.
1. Na lista, vê-se várias funções associadas a cada cenário. Selecione a função HTTP **Agendada.**
1. Selecione **Monitor** a partir do painel da esquerda.
1. Selecione os últimos vestígios de execução para ver os detalhes da invocação e a secção de mensagem para registos detalhados.
1. Repita os mesmos passos para cada função descrita como parte da revisão do fluxo de execução mais cedo.

Para saber mais sobre a monitorização das funções do Azure, consulte [a telemetria de Funções Azure em Insights de Aplicação](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a monitorização das Funções Azure e aplicações lógicas:

* [Funções Azure Monitor](../../azure-functions/functions-monitoring.md).

* [Como configurar a monitorização para as funções Azure](../../azure-functions/configure-monitoring.md).

* [Monitorize aplicações lógicas.](../../logic-apps/monitor-logic-apps.md)