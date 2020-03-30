---
title: Monitor Azure para recipientes monitoriza configuração de monitores de saúde Microsoft Docs
description: Este artigo fornece conteúdo que descreve a configuração detalhada dos monitores de saúde no Monitor Azure para contentores.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055709"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Monitor Azure para guia de configuração do monitor de saúde de contentores

Os monitores são o elemento principal para medir a saúde e detetar erros no Monitor Azure para os contentores. Este artigo ajuda-o a compreender os conceitos de como a saúde é medida e os elementos que compõem o modelo de saúde para monitorizar e reportar sobre a saúde do seu cluster Kubernetes com a funcionalidade [Saúde (pré-visualização).](container-insights-health.md)

>[!NOTE]
>A funcionalidade de Saúde está em pré-visualização pública neste momento.
>

## <a name="monitors"></a>Monitores

Um monitor mede a saúde de algum aspeto de um objeto gerido. Os monitores têm dois ou três estados de saúde. Um monitor estará num e apenas um dos seus estados potenciais a qualquer momento. Quando um monitor carregado pelo agente contentorizado, é inicializado para um estado saudável. O Estado só muda se forem detetadas as condições especificadas para outro estado.

A saúde geral de um determinado objeto é determinada pela saúde de cada um dos seus monitores. Esta hierarquia é ilustrada no painel da Hierarquia da Saúde no Monitor Azure para contentores. A política de como a saúde é enrolada faz parte da configuração dos monitores agregados.

## <a name="types-of-monitors"></a>Tipos de monitores

|Monitorizar | Descrição | 
|--------|-------------|
| Monitor de unidade |Um monitor unitário mede algum aspeto de um recurso ou aplicação. Isto pode estar a verificar um contador de desempenho para determinar o desempenho do recurso, ou a sua disponibilidade. |
|Monitor agregado | O agregado monitoriza vários monitores para fornecer um único estado de saúde agregado. Os monitores de unidade são normalmente configurados sob um monitor agregado particular. Por exemplo, um monitor agregado do Nó atualiza o estado da utilização do CPU do Nó, utilização da memória e estado do nó.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Política de rollup de monitorização agregada

Cada monitor agregado define uma política de rollup de saúde, que é a lógica que é usada para determinar a saúde do monitor agregado com base na saúde dos monitores sob a sua parte. As possíveis políticas de rollup de saúde para um monitor agregado são as seguintes:

#### <a name="worst-state-policy"></a>Pior política estatal

O estado do monitor agregado corresponde ao estado do monitor infantil com o pior estado de saúde. Esta é a política mais comum utilizada pelos monitores agregados.

![Exemplo de monitor agregado rollup pior estado](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Política percentual

O objeto de origem corresponde ao pior estado de um único membro de uma determinada percentagem de objectos-alvo no melhor estado. Esta política é utilizada quando uma determinada percentagem de objectos-alvo deve ser saudável para que o objecto-alvo seja considerado saudável. A política percentual classifica os monitores em ordem descendente de gravidade do Estado, e o estado do monitor agregado é calculado como o pior estado de N% (N é ditado pelo parâmetro de configuração *StateThresholdPercentage).*

Por exemplo, suponha que existam cinco casos de contentores de uma imagem de contentor, e os seus estados individuais são **Críticos**, **Aviso,** **Saudável,** **Saudável,** **Saudável**.  O estado do monitor de utilização do CPU do contentor será **Crítico,** uma vez que o pior estado de 90% dos contentores é **crítico** quando classificado por ordem descendente de gravidade.

## <a name="understand-the-monitoring-configuration"></a>Compreender a configuração de monitorização

O Monitor Azure para contentores inclui uma série de cenários de monitorização chave que são configurados da seguinte forma.

### <a name="unit-monitors"></a>Monitores unitários

|**Monitorizar o nome** | Tipo de monitor | **Descrição** | **Parâmetro** | **Valor** |
|-----------------|--------------|-----------------|---------------|-----------|
|Utilização da memória do nó |Monitor de unidade |Este monitor avalia a utilização da memória de um nó a cada minuto, utilizando os dados reportados pelo cadvisor. |ConsecutivasAmostras ForstateTransition<br> FailifGreaterThanPercentage<br> WarnifGreaterThanPercentage | 3<br> 90<br> 80  ||
|Utilização do CPU do nó |Monitor de Unidade |Este monitor verifica a utilização do CPU do nó a cada minuto, utilizando os dados reportados pelo cadvisor. | ConsecutivasAmostras ForstateTransition<br> FailifGreaterThanPercentage<br> WarnifGreaterThanPercentage | 3<br> 90<br> 80  ||
|Estado do nó |Monitor de unidade |Este monitor verifica as condições do nó reportados pela Kubernetes.<br> Atualmente, as seguintes condições do nó são verificadas: Pressão do disco, pressão de memória, pressão PID, out of Disk, Rede indisponível, Estado de pronto para o nó.<br> Fora das condições acima referidas, se *fora do disco* ou *da rede Indisponível* é **verdade,** o monitor altera-se em estado **crítico.**<br> Se quaisquer outras condições forem **verdadeiras**, com além de um estado **de Pronto,** o monitor muda para um estado de **aviso.** | NodeConditionTypeForFailedState | outofdisk,networkin ||
|Utilização da memória do recipiente |Monitor de unidade |Este monitor reporta o estado de saúde combinado da utilização da memória (RSS) das instâncias do recipiente.<br> Executa uma simples comparação que compara cada amostra a um único limiar, e especificada pelo parâmetro de configuração **ConsecutiveSamplesForStateTransition**.<br> O seu estado é calculado como o pior estado de 90% dos casos de contentores (StateThresholdPercentage), classificados por ordem descendente de gravidade do estado de saúde dos contentores (isto é, Crítico, Aviso, Saudável).<br> Se nenhum registo for recebido de um caso de contentor, então o estado de saúde da instância do contentor é reportado como **Desconhecido**, e tem maior precedência na ordem de triagem sobre o estado **crítico.**<br> O estado de cada instância de contentores é calculado utilizando os limiares especificados na configuração. Se o uso for superior ao limiar crítico (90%), então a instância está num estado **crítico,** se for inferior ao limiar crítico (90%) mas maior do que o limiar de alerta (80%), então a instância está em estado de **alerta.** Caso contrário, está em estado **saudável.** |ConsecutivasAmostras ForstateTransition<br> FailifLessThanPercentage<br> StateThresholdPercentage<br> WarnifGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Utilização do CPU do contentor |Monitor de unidade |Este monitor reporta o estado de saúde combinado da utilização da CPU das instâncias do contentor.<br> Executa uma simples comparação que compara cada amostra a um único limiar, e especificada pelo parâmetro de configuração **ConsecutiveSamplesForStateTransition**.<br> O seu estado é calculado como o pior estado de 90% dos casos de contentores (StateThresholdPercentage), classificados por ordem descendente de gravidade do estado de saúde dos contentores (isto é, Crítico, Aviso, Saudável).<br> Se nenhum registo for recebido de um caso de contentor, então o estado de saúde da instância do contentor é reportado como **Desconhecido**, e tem maior precedência na ordem de triagem sobre o estado **crítico.**<br> O estado de cada instância de contentores é calculado utilizando os limiares especificados na configuração. Se o uso for superior ao limiar crítico (90%), então a instância está num estado **crítico,** se for inferior ao limiar crítico (90%) mas maior do que o limiar de alerta (80%), então a instância está em estado de **alerta.** Caso contrário, está em estado **saudável.** |ConsecutivasAmostras ForstateTransition<br> FailifLessThanPercentage<br> StateThresholdPercentage<br> WarnifGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Cápsulas de carga de trabalho do sistema prontas |Monitor de unidade |Este monitor reporta o estado com base na percentagem de cápsulas em estado pronto numa determinada carga de trabalho. O seu estado está definido para **crítico** se menos de 100% das cápsulas estiverem em estado **saudável** |ConsecutivasAmostras ForstateTransition<br> FailifLessThanPercentage |2<br> 100 ||
|Estado da API de Kube |Monitor de unidade |Este monitor reporta o estado do serviço Kube API. O monitor encontra-se em estado crítico no caso de kube API endpoint não estar disponível. Para este monitor em particular, o estado é determinado fazendo uma consulta ao ponto final dos "nós" para o servidor kube-api. Qualquer coisa que não seja um código de resposta OK muda o monitor para um estado **crítico.** | Sem propriedades de configuração |||

### <a name="aggregate-monitors"></a>Monitores agregados

|**Monitorizar o nome** | **Descrição** | **Algoritmo** |
|-----------------|-----------------|---------------|
|Nó |Este monitor é um agregado de todos os monitores do nó. Combina com o estado do monitor infantil com o pior estado de saúde:<br> Utilização do CPU do nó<br> Utilização da memória do nó<br> Estado do nó | O pior de|
|Piscina de nó |Este monitor reporta o estado de saúde combinado de todos os nós na piscina de *agentes*da piscina do nó . Trata-se de um monitor de três Estados, cujo estado se baseia no pior estado de 80% dos nós na piscina do nó, classificados em ordem descendente de gravidade dos estados do nó (isto é, Crítico, Aviso, Saudável).|Percentagem |
|Nós (pai da piscina do nó) |Este é um monitor agregado de todas as piscinas de nós. O seu estado baseia-se no pior estado dos seus monitores infantis (isto é, as piscinas de nós presentes no cluster). |O pior de |
|Cluster (pai dos nós/<br> Infraestrutura kubernetes) |Este é o monitor dos pais que combina com o estado do monitor infantil com o pior estado de saúde, que é a infraestrutura de kubernetes e os nós. |O pior de |
|Infraestrutura de Kubernetes |Este monitor reporta o estado de saúde combinado dos componentes de infraestrutura geridos do cluster. o seu estatuto é calculado como o "pior de" que o seu monitor infantil afirma, ou seja, as cargas de trabalho do sistema kube e o estado do Servidor API. |O pior de|
|Carga de trabalho do sistema |Este monitor relata o estado de saúde de uma carga de trabalho do sistema kube. Este monitor corresponde ao estado do monitor infantil com o pior estado de saúde, ou seja, as **Cápsulas em estado de preparação** (monitor e os recipientes na carga de trabalho). |O pior de |
|Contentor |Este monitor relata o estado de saúde geral de um recipiente numa determinada carga de trabalho. Este monitor corresponde ao estado do monitor infantil com o pior estado de saúde, ou seja, os monitores de **utilização** da CPU e **da utilização da memória.** |O pior de |

## <a name="next-steps"></a>Passos seguintes

Veja a [saúde](container-insights-health.md) do cluster monitor para aprender sobre ver o estado de saúde do seu cluster Kubernetes.
