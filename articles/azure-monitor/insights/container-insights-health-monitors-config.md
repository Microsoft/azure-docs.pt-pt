---
title: Monitor Azure para a configuração dos monitores de saúde dos contentores Microsoft Docs
description: Este artigo fornece conteúdo que descreve a configuração detalhada dos monitores de saúde no Azure Monitor para recipientes.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: 99ea6e96f5a8a486784cb3d633a6e031b60eaad7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80055709"
---
# <a name="azure-monitor-for-containers-health-monitor-configuration-guide"></a>Monitor Azure para guia de configuração do monitor de saúde de contentores

Os monitores são o elemento principal para medir a saúde e detetar erros no Azure Monitor para recipientes. Este artigo ajuda-o a compreender os conceitos de como a saúde é medida e os elementos que compõem o modelo de saúde para monitorizar e reportar sobre a saúde do seu cluster Kubernetes com a funcionalidade [Saúde (pré-visualização).](container-insights-health.md)

>[!NOTE]
>A funcionalidade Saúde está em pré-visualização pública neste momento.
>

## <a name="monitors"></a>Monitores

Um monitor mede a saúde de algum aspeto de um objeto gerido. Os monitores têm dois ou três estados de saúde. Um monitor estará num só estado em qualquer momento. Quando um monitor carregado pelo agente contentorizado, é inicializado para um estado saudável. O estado só muda se forem detetadas as condições especificadas para outro estado.

A saúde geral de um determinado objeto é determinada a partir da saúde de cada um dos seus monitores. Esta hierarquia é ilustrada no painel da Hierarquia da Saúde no Monitor Azure para contentores. A política de como a saúde é enrolada faz parte da configuração dos monitores agregados.

## <a name="types-of-monitors"></a>Tipos de monitores

|Monitor | Description | 
|--------|-------------|
| Monitor de unidade |Um monitor de unidade mede algum aspeto de um recurso ou aplicação. Isto pode estar a verificar um contador de desempenho para determinar o desempenho do recurso, ou a sua disponibilidade. |
|Monitor Agregado | Monitores agregados grupo múltiplos monitores para fornecer um único estado de saúde agregado de saúde. Os monitores unitários são tipicamente configurados sob um determinado monitor agregado. Por exemplo, um monitor agregado nó revira o estado da utilização do CPU do nó, da utilização da memória e do estado do nó.
 |

### <a name="aggregate-monitor-health-rollup-policy"></a>Política agregada de rollups de saúde

Cada monitor agregado define uma política de rollup de saúde, que é a lógica que é usada para determinar a saúde do monitor agregado com base na saúde dos monitores sob o mesmo. As possíveis políticas de rollup de saúde para um monitor agregado são as seguintes:

#### <a name="worst-state-policy"></a>Pior política do Estado

O estado do monitor agregado corresponde ao estado do monitor infantil com o pior estado de saúde. Esta é a política mais comum utilizada pelos monitores agregados.

![Exemplo do pior estado de rollup do monitor agregado](./media/container-insights-health-monitoring-cfg/aggregate-monitor-rollup-worstof.png)

### <a name="percentage-policy"></a>Política percentual

O objeto de origem corresponde ao pior estado de um único membro de uma percentagem especificada de objectos-alvo no melhor estado. Esta política é utilizada quando uma certa percentagem de objectos-alvo deve ser saudável para que o objecto-alvo seja considerado saudável. A política percentual classifica os monitores em ordem descendente de gravidade do Estado, e o estado do monitor agregado é calculado como o pior estado de N% (N é ditado pelo parâmetro de configuração *StateThresholdPercentage).*

Por exemplo, suponha que existam cinco casos de contentores de uma imagem de recipiente, e os seus estados individuais são **Críticos,** **Aviso,** **Saudável,** **Saudável,** **Saudável**.  O estado do monitor de utilização do CPU do contentor será **crítico,** uma vez que o pior estado de 90% dos contentores é **crítico** quando classificado em ordem descendente de gravidade.

## <a name="understand-the-monitoring-configuration"></a>Compreender a configuração de monitorização

O Monitor Azure para contentores inclui uma série de cenários-chave de monitorização que são configurados da seguinte forma.

### <a name="unit-monitors"></a>Monitores de unidade

|**Nome do monitor** | Tipo de monitor | **Descrição** | **Parâmetro** | **Valor** |
|-----------------|--------------|-----------------|---------------|-----------|
|Utilização da memória do nó |Monitor de unidade |Este monitor avalia a utilização da memória de um nó a cada minuto, utilizando os dados reportados pelo CADVISOR. |ConsecutivosSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Utilização do NÓ CPU |Monitor da Unidade |Este monitor verifica a utilização do CPU do nó a cada minuto, utilizando os dados reportados pelo CADVISOR. | ConsecutivosSamplesForStateTransition<br> FailIfGreaterThanPercentage<br> WarnIfGreaterThanPercentage | 3<br> 90<br> 80  ||
|Estado do nó |Monitor de unidade |Este monitor verifica as condições do nó reportadas pela Kubernetes.<br> Atualmente, verificam-se as seguintes condições do nó: Pressão do disco, pressão de memória, pressão PID, saída do disco, rede indisponível, Estado de preparação para o nó.<br> Fora das condições acima referidas, se *fora do disco* ou da rede *indisponível* for **verdade,** o monitor muda para o estado **crítico.**<br> Se quaisquer outras condições forem **iguais a verdadeiras**, para além de um estado **de Pronto,** o monitor muda para um estado **de aviso.** | NóconditionTypeForFailedState | outofdisk,networkun disponível ||
|Utilização da memória do contentor |Monitor de unidade |Este monitor informa o estado de saúde combinado da utilização da memória (RSS) dos casos do recipiente.<br> Executa uma comparação simples que compara cada amostra a um único limiar, e especificada pelo parâmetro de configuração **ConsecutiveSamplesForStateTransition**.<br> O seu estado é calculado como o pior estado de 90% dos casos de contentores (StateThresholdPercentage), classificados em ordem descendente de gravidade do estado de saúde dos contentores (isto é, Crítico, Aviso, Saudável).<br> Se nenhum registo for recebido de uma instância de contentor, então o estado de saúde da instância do contentor é reportado como **Desconhecido**, e tem uma precedência mais elevada na ordem de triagem sobre o estado **crítico.**<br> O estado de cada instância de contentor é calculado utilizando os limiares especificados na configuração. Se a utilização for superior ao limiar crítico (90%), então a instância está num estado **crítico,** se for menos do que o limiar crítico (90%) mas maior do que o limiar de aviso (80%), então o caso está em estado de **Aviso.** Caso contrário, está em estado **saudável.** |ConsecutivosSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Utilização do CPU do contentor |Monitor de unidade |Este monitor informa o estado de saúde combinado da utilização da CPU dos casos do contentor.<br> Executa uma comparação simples que compara cada amostra a um único limiar, e especificada pelo parâmetro de configuração **ConsecutiveSamplesForStateTransition**.<br> O seu estado é calculado como o pior estado de 90% dos casos de contentores (StateThresholdPercentage), classificados em ordem descendente de gravidade do estado de saúde dos contentores (isto é, Crítico, Aviso, Saudável).<br> Se nenhum registo for recebido de uma instância de contentor, então o estado de saúde da instância do contentor é reportado como **Desconhecido**, e tem uma precedência mais elevada na ordem de triagem sobre o estado **crítico.**<br> O estado de cada instância de contentor é calculado utilizando os limiares especificados na configuração. Se a utilização for superior ao limiar crítico (90%), então a instância está num estado **crítico,** se for menos do que o limiar crítico (90%) mas maior do que o limiar de aviso (80%), então o caso está em estado de **Aviso.** Caso contrário, está em estado **saudável.** |ConsecutivosSamplesForStateTransition<br> FailIfLessThanPercentage<br> StateThresholdPercentage<br> WarnIfGreaterThanPercentage| 3<br> 90<br> 90<br> 80 ||
|Cápsulas de carga de trabalho do sistema prontas |Monitor de unidade |Este monitor reporta o estado com base na percentagem de cápsulas em estado pronto numa determinada carga de trabalho. O seu estado está definido para **a Critical** se menos de 100% das cápsulas estiverem num estado **saudável.** |ConsecutivosSamplesForStateTransition<br> FailIfLessThanPercentage |2<br> 100 ||
|Estatuto de API de Kube |Monitor de unidade |Este monitor informa o estado do serviço API da Kube. O Monitor encontra-se em estado crítico caso o ponto final da API da Kube não esteja disponível. Para este monitor em particular, o estado é determinado fazendo uma consulta ao ponto final dos "nós" para o servidor kube-api. Qualquer outra coisa que não um código de resposta OK muda o monitor para um estado **crítico.** | Sem propriedades de configuração |||

### <a name="aggregate-monitors"></a>Monitores agregados

|**Nome do monitor** | **Descrição** | **Algoritmo** |
|-----------------|-----------------|---------------|
|Nó |Este monitor é um agregado de todos os monitores de nó. Combina com o estado do monitor infantil com o pior estado de saúde:<br> Utilização do CPU de nó<br> Utilização da memória do nó<br> Estado do nó | O pior dos|
|Piscina de nó |Este monitor informa o estado de saúde combinado de todos os nós na *piscina*de nós. Este é um monitor de três estados, cujo estado é baseado no pior estado de 80% dos nóns na piscina de nó, classificado em ordem descendente de gravidade dos estados do nó (isto é, Crítico, Aviso, Saudável).|Percentagem |
|Nós (pai da piscina nó) |Este é um monitor agregado de todas as piscinas de nós. O seu estado baseia-se no pior estado dos seus monitores infantis (isto é, nas piscinas de nó presentes no aglomerado). |O pior dos |
|Cluster (pai dos nós/<br> Infraestrutura de Kubernetes) |Este é o monitor dos pais que combina com o estado do monitor infantil com o pior estado de saúde, que é a infraestrutura de kubernetes e os nós. |O pior dos |
|Infraestrutura de Kubernetes |Este monitor informa o estado de saúde combinado dos componentes de infraestrutura geridos do cluster. o seu estado é calculado como o "pior dos" estados do seu monitor infantil, ou seja, cargas de trabalho do sistema kube e o estado do Servidor API. |O pior dos|
|Carga de trabalho do sistema |Este monitor reporta o estado de saúde de uma carga de trabalho do sistema kube. Este monitor corresponde ao estado do monitor infantil com o pior estado de saúde, ou seja, as **Cápsulas em estado de preparação** (monitor e os recipientes na carga de trabalho). |O pior dos |
|Contentor |Este monitor informa o estado sanitário geral de um recipiente numa determinada carga de trabalho. Este monitor corresponde ao estado do monitor infantil com o pior estado de saúde, ou seja, os monitores de **utilização** do CPU e **da utilização da memória.** |O pior dos |

## <a name="next-steps"></a>Passos seguintes

Consulte [a saúde do cluster monitor](container-insights-health.md) para saber sobre a visualização do estado de saúde do seu cluster Kubernetes.
