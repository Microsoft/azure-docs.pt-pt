---
title: Monitorização de padrões de churn em máquinas virtuais
description: Saiba como monitorizar padrões de churn em máquinas virtuais protegidas usando a recuperação do site Azure
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359871"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>Monitorização de padrões de churn em máquinas virtuais

Este artigo fornece uma visão geral de várias ferramentas que podem ser usadas para monitorizar padrões de churn em uma máquina virtual. Ao utilizar ferramentas adequadas, é fácil descobrir exatamente qual a aplicação que está a causar alta agitação e, em seguida, podem ser tomadas outras ações para essa aplicação.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>Para máquinas virtuais Azure (Windows ou Linux)

Se a sua máquina estiver hospedada em Azure e estiver a utilizar um disco gerido ou não gerido para armazenamento, então pode rastrear facilmente o desempenho, rastreando as métricas do disco. Isto permite-lhe monitorizar de perto e fazer a seleção de disco certa para se adequar ao seu padrão de utilização da aplicação. Também pode usá-lo para criar alertas, diagnóstico e construir automatização. [Saiba mais](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

Depois de ter protegido as suas máquinas com a Recuperação do Local Azure, pode monitorizar as suas máquinas utilizando registos de monitores Azure e Log Analytics. [Saiba mais](./monitor-log-analytics.md).

Existem também algumas ferramentas específicas do sistema operativo que pode utilizar.

## <a name="for-windows-machines"></a>Para máquinas Windows

No caso de ter uma máquina, seja no local ou não, que esteja a executar o sistema operativo Windows, então existem mais algumas ferramentas disponíveis.

Além de verificar a utilização do disco no Task Manager, pode sempre consultar o **Monitor de Recursos** e o Monitor de **Desempenho**. Estas ferramentas já estão presentes nas máquinas do Windows.

### <a name="resource-monitor"></a>Monitor de Recursos

**O Monitor de Recursos** exibe informações sobre a utilização de recursos de hardware e software em tempo real. Para executar o Monitor de Recursos numa máquina Windows, siga os passos abaixo –

1. Pressione Win + R e _escreva resmon_.
1. Uma vez que o resmon, isto é, Monitor de Recursos, a janela abre o interruptor para o separador Disco. Dá uma visão seguinte -

    ![Separador do disco do monitor de recursos](./media/monitoring-high-churn/resmon-disk-tab.png)

1. Este separador deve ser monitorizado continuamente durante algum tempo para obter uma imagem clara. No exemplo acima, vemos que _wmiprv.exe_ está a agitar-se alto.

Uma vez identificadas as aplicações que causam alta agitação na sua máquina, pode tomar as medidas necessárias para lidar com o churn relacionado com essas aplicações.

### <a name="performance-monitor"></a>Monitorização de Desempenho

**O Monitor de Desempenho** monitoriza várias atividades num computador como CPU ou utilização de memória. Para executar o Monitor de Desempenho numa máquina Windows, siga os passos abaixo –

1. Pressione Win + R e escreva _perfmon_.
1. Uma vez que o perfmon, isto é, Monitor de Desempenho, janela abre, dá a seguinte vista -

    ![Passo 1 do monitor de desempenho](./media/monitoring-high-churn/perfmon-step1.png)

1. Expanda a pasta **ferramentas de monitorização** à direita e clique no Monitor de Desempenho. Isto abrirá a vista abaixo, dando-lhe uma informação em tempo real sobre o desempenho atual -

    ![Monitor de Desempenho Step2](./media/monitoring-high-churn/perfmon-step1.png)

1. Este gráfico está atualmente a monitorizar apenas um monitor, ou seja, o 'tempo de processador%' – como indica a tabela logo abaixo do gráfico. Pode adicionar mais itens para monitorização clicando no **'+'** na parte superior da ferramenta.
1. Abaixo está uma visão de como o Monitor de Desempenho se parece uma vez que adicionamos mais contadores -

    ![Monitor de Desempenho Step3](./media/monitoring-high-churn/perfmon-step3.png)

Saiba mais sobre o Monitor de Desempenho [aqui.](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data)

## <a name="for-linux-machines"></a>Para máquinas Linux

No caso de ter uma máquina, seja no local ou não, que esteja a executar o sistema operativo Linux, então existem mais algumas ferramentas disponíveis para monitorizar os padrões de churn.

### <a name="iotop"></a>Iotop

Uma das ferramentas mais usadas é _o iotop._ É uma utilidade para exibir a atividade do disco em tempo real. Pode listar os processos que estão a executar I/O, juntamente com a largura de banda do disco que estão a usar.

Abra o pedido de comando e executar o comando `iotop` .

### <a name="iostat"></a>IoStat

O IoStat é uma ferramenta simples que irá recolher e mostrar estatísticas de entradas e dispositivos de armazenamento de saída. Esta ferramenta é frequentemente usada para rastrear problemas de desempenho do dispositivo de armazenamento, incluindo dispositivos, discos locais, discos remotos.

Abra o pedido de comando e executar o comando `iostat` .

## <a name="next-steps"></a>Passos seguintes

Saiba como monitorizar com [o Azure Monitor](monitor-log-analytics.md).