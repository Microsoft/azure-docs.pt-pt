---
title: Azure Monitor para configuração de monitores de integridade de contêineres | Microsoft Docs
description: Este artigo fornece conteúdo que descreve a configuração detalhada dos monitores de integridade em Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: d2d602d767fa6a39b7f72650c426c90be210a6ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405045"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Guia de configuração do monitor de integridade de Azure Monitor para contêineres

Monitores são o elemento principal para medir a integridade e detectar erros em Azure Monitor para contêineres. Este artigo ajuda você a entender os conceitos de como a integridade é medida e os elementos que compõem o modelo de integridade para monitorar e relatar a integridade do cluster kubernetes com o recurso de [integridade (versão prévia)](container-insights-health.md) .

>[!NOTE]
>O recurso de integridade está em visualização pública no momento.
>

## <a name="monitors"></a>Monitores

Um monitor mede o estado de funcionamento de algum aspeto de um objeto gerido. Os monitores têm dois ou três estados de funcionamento. Um monitor estará em um e apenas um dos seus estados potenciais num determinado momento. Quando um monitor é carregado pelo agente em contêiner, ele é inicializado para um estado íntegro. O estado será alterado somente se as condições especificadas para outro Estado forem detectadas.

O estado de funcionamento global de um determinado objeto é determinado a partir do estado de funcionamento de cada um dos seus monitores. Essa hierarquia é ilustrada no painel hierarquia de integridade em Azure Monitor para contêineres. A política de como a integridade é acumulada faz parte da configuração dos monitores agregados.

## <a name="types-of-monitors"></a>Tipos de monitores

|Monitorizar | Descrição | 
|--------|-------------|
| Monitor de unidade |Um monitor de unidade mede algum aspecto de um recurso ou aplicativo. Isso pode estar verificando um contador de desempenho para determinar o desempenho do recurso ou sua disponibilidade. |
|Monitor agregado | Os monitores agregados agrupam vários monitores para fornecer um único estado de integridade agregado de integridade. Os monitores de unidade normalmente são configurados em um monitor de agregação específico. Por exemplo, um monitor agregado de nó acumula o status da utilização de CPU do nó, da utilização de memória e do status do nó.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Política de acumulação de integridade do monitor agregado

Cada monitor agregado define uma política de acumulação de integridade, que é a lógica usada para determinar a integridade do monitor agregado com base na integridade dos monitores sob ele. As políticas de acúmulo de integridade possíveis para um monitor agregado são as seguintes:

#### <a name="worst-state-policy"></a>Política de pior estado

O estado do monitor agregado corresponde ao estado do monitor filho com o pior estado de integridade. Essa é a política mais comum usada por monitores agregados.

![Exemplo de pior estado de Rollup de monitor agregado](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Política de porcentagem

O objeto de origem corresponde ao pior estado de um único membro de um percentual especificado de objetos de destino no melhor estado. Essa política é usada quando uma determinada porcentagem de objetos de destino deve estar íntegra para que o objeto de destino seja considerado íntegro. A política de porcentagem classifica os monitores em ordem decrescente de severidade de estado, e o estado do monitor agregado é calculado como o pior estado de N% (N é ditado pelo parâmetro de configuração *StateThresholdPercentage*).

Por exemplo, suponha que haja cinco instâncias de contêiner de uma imagem de contêiner e seus Estados individuais sejam **críticos**, de **aviso**, **íntegros** **, íntegros e** **íntegros**.  O status do monitor de utilização da CPU do contêiner será **crítico**, pois o pior estado de 90% dos contêineres é **crítico** quando classificado em ordem decrescente de severidade.

## <a name="understand-the-monitoring-configuration"></a>Entender a configuração de monitoramento

Azure Monitor para contêineres inclui vários cenários de monitoramento principais configurados da seguinte maneira.

### <a name="unit-monitors"></a>Monitores de unidade

|**Nome do monitor** | Tipo de monitor | **Descrição** | **Parâmetro** | **Valor** |
|-----------------|--------------|-----------------|---------------|-----------|
|Utilização de memória do nó |Monitor de unidade |Esse monitor avalia a utilização de memória de um nó a cada minuto, usando os dados relatados do cadvisor. |ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Utilização de CPU do nó |Monitor de unidade |Esse monitor verifica a utilização da CPU do nó a cada minuto, usando os dados relatados do cadvisor. | ConsecutiveSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Status do nó |Monitor de unidade |Esse monitor verifica as condições do nó relatadas pelo kubernetes.<br> Atualmente, as seguintes condições de nó estão marcadas: pressão de disco, pressão de memória, pressão de PID, sem disco, rede indisponível, status pronto para o nó.<br> Fora das condições acima, se o *disco* ou a *rede não estiver disponível* **, o**monitor mudará para o estado **crítico** .<br> Se qualquer outra condição for igual a **true**, além de um status **pronto** , o monitor mudará para um estado de **aviso** . | NodeConditionTypeForFailedState | outofdisk,networkunavailable ||
|Utilização de memória do contêiner |Monitor de unidade |Este monitor relata o status de integridade combinado da utilização de memória (RSS) das instâncias do contêiner.<br> Ele executa uma comparação simples que compara cada amostra com um único limite e especificado pelo parâmetro de configuração **ConsecutiveSamplesForStateTransition**.<br> Seu estado é calculado como o pior estado de 90% das instâncias de contêiner (StateThresholdPercentage), classificados em ordem decrescente de severidade do estado de integridade do contêiner (isto é, crítico, aviso, íntegro).<br> Se nenhum registro for recebido de uma instância de contêiner, o estado de integridade da instância de contêiner será relatado como **desconhecido**e terá maior precedência na ordem de classificação sobre o estado **crítico** .<br> Cada Estado de instância de contêiner individual é calculado usando os limites especificados na configuração. Se o uso estiver acima do limite crítico (90%), a instância estará em um estado **crítico** , se for menor que o limite crítico (90%) Mas maior que o limite de aviso (80%), a instância está em um estado de **aviso** . Caso contrário, ele estará em estado **íntegro** . |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Utilização de CPU do contêiner |Monitor de unidade |Este monitor relata o status de integridade combinado da utilização da CPU das instâncias do contêiner.<br> Ele executa uma comparação simples que compara cada amostra com um único limite e especificado pelo parâmetro de configuração **ConsecutiveSamplesForStateTransition**.<br> Seu estado é calculado como o pior estado de 90% das instâncias de contêiner (StateThresholdPercentage), classificados em ordem decrescente de severidade do estado de integridade do contêiner (isto é, crítico, aviso, íntegro).<br> Se nenhum registro for recebido de uma instância de contêiner, o estado de integridade da instância de contêiner será relatado como **desconhecido**e terá maior precedência na ordem de classificação sobre o estado **crítico** .<br> Cada Estado de instância de contêiner individual é calculado usando os limites especificados na configuração. Se o uso estiver acima do limite crítico (90%), a instância estará em um estado **crítico** , se for menor que o limite crítico (90%) Mas maior que o limite de aviso (80%), a instância está em um estado de **aviso** . Caso contrário, ele estará em estado **íntegro** . |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Pods de carga de trabalho do sistema pronta |Monitor de unidade |Este monitor relata o status com base na porcentagem de pods no estado pronto em uma determinada carga de trabalho. Seu estado é definido como **crítico** se menos de 100% dos pods estiverem em um estado **íntegro** |ConsecutiveSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Status da API Kube |Monitor de unidade |Este monitor relata o status do serviço de API Kube. O monitor está em estado crítico caso o ponto de extremidade da API do Kube não esteja disponível. Para esse monitor específico, o estado é determinado pela criação de uma consulta para o ponto de extremidade ' nós ' para o servidor Kube-API. Qualquer coisa que não seja um código de resposta OK altera o monitor para um estado **crítico** . | Nenhuma propriedade de configuração |||

### <a name="aggregate-monitors"></a>Monitores agregados

|**Nome do monitor** | **Descrição** | **Algoritmo** |
|-----------------|-----------------|---------------|
|Nó |Esse monitor é uma agregação de todos os monitores de nó. Ele corresponde ao estado do monitor filho com o pior estado de integridade:<br> Utilização de CPU do nó<br> Utilização de memória do nó<br> Status do nó | Pior dos|
|Pool de nós |Este monitor relata o status de integridade combinado de todos os nós no pool de nós *agentpool*. Esse é um monitor de três Estados, cujo estado é baseado no pior estado de 80% dos nós no pool de nós, classificados em ordem decrescente de severidade de Estados de nó (isto é, crítico, aviso, íntegro).|Percentagem |
|Nós (pai do pool de nós) |Esse é um monitor agregado de todos os pools de nós. Seu estado é baseado no pior estado de seus monitores filho (ou seja, os pools de nós presentes no cluster). |Pior dos |
|Cluster (pai de nós/<br> Infraestrutura kubernetes) |Esse é o monitor pai que corresponde ao estado do monitor filho com o pior estado de integridade, que é a infraestrutura kubernetes e os nós. |Pior dos |
|Infraestrutura de kubernetes |Este monitor relata o status de integridade combinado dos componentes de infraestrutura gerenciados do cluster. seu status é calculado como o "pior de" seus Estados de monitor filho, ou seja, cargas de trabalho do sistema Kube e status do servidor de API. |Pior dos|
|Carga de trabalho do sistema |Esse monitor relata o status de integridade de uma carga de trabalho do Kube. Esse monitor corresponde ao estado do monitor filho com o pior estado de integridade, que é o **pods no estado pronto** (monitor e os contêineres na carga de trabalho). |Pior dos |
|Contentor |Este monitor relata o status de integridade geral de um contêiner em uma determinada carga de trabalho. Esse monitor corresponde ao estado do monitor filho com o pior estado de integridade, que é o **uso da CPU** e os monitores de **utilização de memória** . |Pior dos |

## <a name="next-steps"></a>Passos seguintes

Exiba [monitorar a integridade do cluster](container-insights-health.md) para saber mais sobre como exibir o status de integridade do cluster kubernetes.
