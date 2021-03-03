---
title: VM insights saúde dos hóspedes (pré-visualização)
description: Visão geral da funcionalidade de saúde em insights de VM, incluindo como você pode ver a saúde das suas máquinas virtuais e receber alertas quando uma máquina virtual fica insalubre.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 3db6c2f4da28bba2d12aacc90b2fa8e420aa6fbf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707460"
---
# <a name="vm-insights-guest-health-preview"></a>VM insights saúde dos hóspedes (pré-visualização)
A saúde dos hóspedes permite-lhe visualizar a saúde das máquinas virtuais com base num conjunto de medições de desempenho que são amostradas a intervalos regulares do sistema operativo do hóspede. Pode verificar rapidamente a saúde de todas as máquinas virtuais num grupo de subscrição ou de recursos, aprofundar a saúde detalhada de uma determinada máquina virtual ou ser notificado proativamente quando uma máquina virtual fica insalubre. 

## <a name="enable-virtual-machine-health"></a>Permitir a saúde da máquina virtual
Consulte [o Enable VM insights guest health (preview)](vminsights-health-enable.md) para obter detalhes sobre como ativar a funcionalidade de saúde do hóspede e as máquinas virtuais de bordo.

## <a name="pricing"></a>Preços
Não há custos diretos para a funcionalidade de saúde do hóspede, mas há um custo para a ingestão e armazenamento de dados do estado de saúde no espaço de trabalho Log Analytics. Todos os dados são armazenados na tabela *HealthStateChangeEvent.* Consulte [Gerir a utilização e os custos com os Registos do Monitor Azure](../logs/manage-cost-storage.md) para obter detalhes sobre os modelos e custos de preços.

## <a name="view-virtual-machine-health"></a>Ver saúde da máquina virtual
A coluna **Guest VM Health** na página **Get Started** dá-lhe uma visão rápida da saúde de cada máquina virtual numa determinada subscrição ou grupo de recursos. A saúde atual de cada máquina virtual é exibida enquanto os ícones de cada grupo mostram o número de máquinas virtuais atualmente em cada estado desse grupo.

[![Começar página com saúde VM convidado](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Monitores
Clique no estado de saúde de uma máquina virtual para ver o estado detalhado de cada um dos seus monitores. Cada monitor mede a saúde de um determinado componente. A saúde geral da máquina virtual é determinada pela saúde dos seus monitores individuais. 

![Exemplo de monitores](media/vminsights-health-overview/monitors.png)

A tabela que se segue lista os monitores agregados e unitários atualmente disponíveis para cada máquina virtual. 

| Monitor | Tipo | Descrição |
|:---|:---|:---|
| Utilização da CPU | Unidade | Utilização percentual do processador. |
| Sistemas de ficheiros | Agregação | Saúde agregada de todos os sistemas de ficheiros em Linux VM. |
| Sistema de ficheiros  | Agregação | Saúde de cada sistema de ficheiros individual no Linux VM. O nome do monitor é o nome do sistema de ficheiros. |
| Espaço livre | Unidade | Percentagem de espaço livre no sistema de ficheiros. |
| Discos lógicos | Agregação | Saúde agregada de todos os discos lógicos no Windows VM. |
| Disco lógico  | Agregação | Saúde de cada disco lógico individual no Windows VM. O nome do monitor é o nome do disco. |
| Memória | Agregação | Saúde agregada da memória no VM. |
| Memória disponível | Unidade | Megabytes disponíveis no VM. |

## <a name="health-states"></a>Estados de saúde
Cada monitor analisa os valores do agente a cada minuto e compara os valores amostrados com os critérios para cada estado de saúde. Se os critérios para um determinado Estado forem verdadeiros, então o monitor é definido para esse estado. Se nenhum dos critérios for verdadeiro, então o monitor está definido para um estado saudável. Os dados são enviados do agente para o Azure Monitor a cada três minutos ou imediatamente se houver uma alteração de estado.

Cada monitor tem uma janela de olhar e analisa quaisquer amostras recolhidas dentro desse tempo. Por exemplo, um monitor pode ter uma janela de olhar de 240 segundos procurando o valor máximo entre pelo menos 2 valores amostrados, mas não mais do que os últimos 3. Embora os valores sejam amostrados a uma taxa regular, o número amostrado numa determinada janela pode variar ligeiramente se houver alguma perturbação na operação do agente.

Os monitores têm os potenciais estados de saúde na tabela seguinte e estarão em um e único em cada momento. Quando um monitor é inicializado, começa em um estado saudável.

| Estado de Funcionamento | Descrição |
|:---|:---|
| Bom estado de funcionamento  | O monitor não excede atualmente o limiar de advertência ou crítico. |
| Aviso  | O monitor excedeu o limiar de aviso (se definido). |
| Crítico | O monitor excedeu o limiar crítico (se definido). |
| Desconhecido  | Não foram recolhidos dados suficientes para determinar o estado de saúde. |
| Desativado | O monitor está atualmente desativado. |
| Nenhum     | O monitor acaba de ser iniciado e ainda não foi avaliado ou o objeto monitorizado já não existe. |



Existem dois tipos de monitores, como mostrado na tabela seguinte.

| Monitor | Descrição |
|:---|:---|
| Monitor de unidade | Mede algum aspeto de um recurso ou aplicação. Pode estar a verificar um contador de desempenho para determinar o desempenho do recurso ou a sua disponibilidade. |
| Monitor de Agregação | Agrupa vários monitores para proporcionar um único estado de funcionamento agregado. Um monitor agregado pode conter um ou mais monitores de unidade e outros monitores agregados. |


  
### <a name="health-rollup-policy"></a>Política de rollup de saúde
O estado de saúde de uma máquina virtual é determinado pelo rollup de saúde de cada uma das suas unidades e monitores agregados. Cada monitor agregado usa uma pior política de saúde do Estado onde o estado do monitor agregado corresponde ao estado do monitor infantil com o pior estado de saúde.  

No exemplo seguinte, o monitor **de espaço Livre** encontra-se num estado crítico que se coloca até aos agregados, por exemplo, e depois aos **sistemas de ficheiros.** Apesar **da utilização do CPU** em estado de alerta, a máquina virtual está definida como crítica, uma vez que este é o pior estado por baixo. Se o espaço livre voltasse a um estado saudável, então a máquina virtual mudaria para um estado de alerta, uma vez que a utilização do CPU se tornaria o monitor com o pior estado.

![Exemplo de rollup de saúde](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>Detalhes do monitor
Selecione um monitor para ver o seu detalhe que inclui os seguintes separadores.

**A visão geral** fornece uma descrição do monitor, a última vez que foi avaliado, e valores amostrados para determinar o estado de saúde atual. O número de amostras pode variar em função da definição do monitor e da volatilidade dos valores.

[![Visão geral dos detalhes do monitor](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**O histórico** lista o histórico de alterações de estado para o monitor. Pode expandir qualquer uma das alterações do Estado para visualizar valores avaliados para determinar o estado de saúde. Clique na **configuração de Visualização aplicada** para visualizar a configuração do monitor no momento em que o estado de saúde foi alterado.



[![Monitorize o histórico de detalhes](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Configuração
Ver e modificar a configuração do monitor para o VM selecionado. Consulte [a monitorização do Configure em informações VM para obter informações sobre a saúde dos hóspedes (pré-visualização)](vminsights-health-enable.md) para obter mais detalhes.

[![Configuração de detalhes do monitor](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Passos seguintes

- [Capacitar a saúde dos hóspedes em insights VM e agentes a bordo.](vminsights-health-enable.md)
- [Configure os monitores utilizando o portal Azure.](vminsights-health-configure.md)
- [Configure os monitores utilizando as regras de recolha de dados.](vminsights-health-configure-dcr.md)