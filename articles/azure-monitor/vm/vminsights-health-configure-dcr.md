---
title: Configure a monitorização no Azure Monitor para a saúde dos hóspedes em VMs utilizando regras de recolha de dados (pré-visualização)
description: Descreve como modificar a monitorização predefinida no Azure Monitor para a saúde dos hóspedes em VMs em escala usando modelos de Gestor de Recursos.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 2001fece40267ca2e3256e699d2dc253ceb10f0c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620621"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-using-data-collection-rules-preview"></a>Configure a monitorização no Azure Monitor para a saúde dos hóspedes em VMs utilizando regras de recolha de dados (pré-visualização)
[O Azure Monitor para a saúde dos hóspedes em VMs](vminsights-health-overview.md) permite-lhe visualizar a saúde de uma máquina virtual, tal como definida por um conjunto de medições de desempenho que são amostradas a intervalos regulares. Este artigo descreve como pode modificar a monitorização predefinitiva em várias máquinas virtuais usando regras de recolha de dados.


## <a name="monitors"></a>Monitores
O estado de saúde de uma máquina virtual é determinado pelo [rollup de saúde](vminsights-health-overview.md#health-rollup-policy) de cada um dos seus monitores. Existem dois tipos de monitores no Azure Monitor para a saúde dos hóspedes VMs, como mostrado na tabela seguinte.

| Monitor | Description |
|:---|:---|
| Monitor de unidade | Mede algum aspeto de um recurso ou aplicação. Pode estar a verificar um contador de desempenho para determinar o desempenho do recurso ou a sua disponibilidade. |
| Monitor de Agregação | Agrupa vários monitores para proporcionar um único estado de funcionamento agregado. Um monitor agregado pode conter um ou mais monitores de unidade e outros monitores agregados. |

O conjunto de monitores utilizados pelo Azure Monitor para a saúde dos hóspedes em VMs e a sua configuração não podem ser alterados diretamente. Pode criar [sobreposições](#overrides) que modificam o comportamento da configuração padrão. As substituições são definidas nas regras de recolha de dados. Pode criar várias regras de recolha de dados cada uma contendo múltiplas sobreposições para alcançar a configuração de monitorização necessária.

## <a name="monitor-properties"></a>Monitorizar propriedades
A tabela seguinte descreve as propriedades que podem ser configuradas em cada monitor.

| Propriedade | Monitores | Description |
|:---|:---|:---|
| Ativado | Agregação<br>Unidade | Se for verdade, o monitor estatal é calculado e contribui para a saúde da máquina virtual. Pode desencadear um alerta de alerta. |
| Alertas | Agregação<br>Unidade | Se for verdade, um alerta é acionado para o monitor quando se desloca para um estado pouco saudável. Se for falso, o estado do monitor continuará a contribuir para a saúde da máquina virtual, o que poderá desencadear um alerta. |
| Aviso | Unidade | Critérios para o estado de alerta. Se nenhum, então o monitor nunca entrará num estado de aviso. |
| Crítico | Unidade | Critérios para o estado crítico. Se nenhum, então o monitor nunca entrará num estado crítico. |
| Frequência de avaliação | Unidade | A frequência do estado de saúde é avaliada. |
| Olhar para o interior | Unidade | Tamanho da janela de olhar em segundos. Consulte [o elemento de configuração do monitor](#monitorconfiguration-element) para obter uma descrição detalhada. |
| Tipo de Avaliação | Unidade | Define qual o valor a utilizar a partir do conjunto de amostras. Consulte [o elemento de configuração do monitor](#monitorconfiguration-element) para obter uma descrição detalhada. |
| Amostra de min | Unidade | Número mínimo de valores a utilizar para calcular o valor. |
| Amostra máxima | Unidade | Número máximo de valores a utilizar para calcular o valor. |


## <a name="default-configuration"></a>Configuração predefinida
A tabela seguinte lista a configuração predefinitiva para cada monitor. Esta configuração padrão não pode ser alterada diretamente, mas pode definir [sobreposições](#overrides) que irão modificar a configuração do monitor para determinadas máquinas virtuais.


| Monitor | Ativado | Alertas | Aviso | Crítico | Frequência de avaliação | Olhar para o interior | Tipo de avaliação | Amostra de min | Amostras max |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Utilização da CPU  | Verdadeiro | Falso | Nenhum | \> 90%    | 60 s | 240 seg | Mín | 2 | 3 |
| Memória disponível | Verdadeiro | Falso | Nenhum | \< 100 MB | 60 s | 240 seg | Máx | 2 | 3 |
| Sistema de ficheiros      | Verdadeiro | Falso | Nenhum | \< 100 MB | 60 s | 120 seg | Máx | 1 | 1 |


## <a name="overrides"></a>Substituições
Uma *sobreposição* altera mais um minério de propriedades de um monitor. Por exemplo, uma sobreposição poderia desativar um monitor que é ativado por padrão, definir critérios de aviso para o monitor ou modificar o limiar crítico do monitor. 

As substituições são definidas numa [Regra de Recolha de Dados (DCR)](../agents/data-collection-rule-overview.md). Pode criar vários DCRs com diferentes conjuntos de sobreposições e aplicá-los em várias máquinas virtuais. Aplica-se um DCR a uma máquina virtual criando uma associação como descrito na [recolha de dados Configure para o agente Azure Monitor (pré-visualização)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations).


## <a name="multiple-overrides"></a>Múltiplas sobreposições

Um único monitor pode ter múltiplas sobreposições. Se as sobreposições definem diferentes propriedades, então a configuração resultante é uma combinação de todas as sobreposições.

Por exemplo, o `memory|available` monitor não especifica um limiar de aviso ou permite o alerta por predefinição. Considere as seguintes sobreposições aplicadas a este monitor:

- Sobreposição 1 define `alertConfiguration.isEnabled` o valor da propriedade como `true`
- Sobreposição 2 define `monitorConfiguration.warningCondition` com uma condição limiar de `< 250` .

A configuração resultante seria um monitor que entra em estado de alerta quando menos de 250Mb de memória está disponível e cria alerta severidade 2 e também entra em estado crítico de saúde quando menos de 100Mb de memória disponível está disponível e cria alerta Severidade 1 (ou altera o alerta existente de 2 para 1 se já existisse).

Se duas sobreposições definirem a mesma propriedade no mesmo monitor, um valor terá precedência. As sobreposições serão aplicadas com base no seu [âmbito,](#scopes-element)do mais geral ao mais específico. Isto significa que as sobreposições mais específicas terão a maior possibilidade de serem aplicadas. A ordem específica é a seguinte:

1. Global 
2. Subscrição
3. Grupo de recursos
4. Máquina virtual. 

Se várias sobreposições ao mesmo nível de âmbito definem a mesma propriedade no mesmo monitor, então são aplicadas na ordem em que aparecem no DCR. Se as sobreposições estiverem em DIFERENTES DCRs, então são aplicadas por ordem alfabética dos IDs de recursos DCR.


## <a name="data-collection-rule-configuration"></a>Configuração da regra de recolha de dados
Os elementos JSON na regra de recolha de dados que definem sobreposições são descritos nas seguintes secções. Um exemplo completo é fornecido na [regra de recolha de dados da amostra.](#sample-data-collection-rule)

## <a name="extensions-structure"></a>estrutura de extensões
A saúde dos hóspedes é implementada como uma extensão ao agente Azure Monitor, pelo que as sobreposições são definidas no `extensions` elemento da regra de recolha de dados. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Elemento | Obrigatório | Descrição |
|:---|:---|:---|
| `name` | Sim | Cadeia definida pelo utilizador para a extensão. |
| `streams` | Yes | Lista de streams para os que os dados de saúde dos hóspedes serão enviados. Isto deve incluir **microsoft-HealthStateChange**.  |
| `extensionName` | Yes | O nome da extensão. Esta deve ser **a HealthExtension**. |
| `extensionSettings` | Yes | Matriz de `healthRuleOverride` elementos a aplicar à configuração padrão. |


## <a name="extensionsettings-element"></a>extensãoSelementos elemento
Contém definições para a extensão.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Elemento | Obrigatório | Descrição |
|:---|:---|:---|
| `schemaVersion` | Sim | Cadeia definida pela Microsoft para representar o esquema esperado do elemento. Atualmente deve ser definido para 1.0 |
| `contentVersion` | No | Cadeia definida pelo utilizador para rastrear diferentes versões da configuração de saúde, se necessário. |
| `healthRuleOverrides` | Yes | Matriz de `healthRuleOverride` elementos a aplicar à configuração padrão. |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides elemento
Contém um ou mais `healthRuleOverride` elementos que cada um define uma sobreposição.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Elemento | Obrigatório | Descrição |
|:---|:---|:---|
| `scopes` | Sim | Lista de um ou mais âmbitos que especificam as máquinas virtuais às quais esta sobreposição é aplicável. Mesmo que o DCR esteja associado a uma máquina virtual, a máquina virtual deve estar dentro de uma margem para que o sobreposição seja aplicado. |
| `monitors` | Yes | Lista de uma ou mais cordas que definem quais os monitores que receberão esta sobreposição.  |
| `monitorConfiguration` | No | Configuração para o monitor, incluindo estados de saúde e como são calculados. |
| `alertConfiguration` | No | Configuração de alerta para o monitor. |
| `isEnabled` | No | Controla se o monitor está ativado ou não. O monitor desativado muda para estado especial de saúde *para deficientes* e está incapacitado, a menos que seja reativado. Se omitido, o monitor herdará o seu estatuto do monitor dos pais na hierarquia. |


## <a name="scopes-element"></a>elemento de âmbitos
Cada sobreposição tem um ou mais âmbitos a definir a que máquinas virtuais a sobreposição deve ser aplicada. O âmbito pode ser uma subscrição, um grupo de recursos ou uma única máquina virtual. Mesmo que a sobreposição esteja associada a uma determinada máquina virtual, só é aplicada a essa máquina virtual se a máquina virtual estiver dentro de um dos âmbitos da sobreposição. Isto permite-lhe associar um número menor de DCRs a um conjunto de VMs, mas fornecer controlo granular sobre a atribuição de cada sobreposição dentro do próprio DCR. Pode querer criar um pequeno conjunto de DCRs e associação-os a um conjunto de máquinas virtuais que utilizam a política, ao mesmo tempo que especifica os sobreposições do monitor de saúde para diferentes subconjuntos dessas máquinas virtuais utilizando o elemento de âmbito.

A tabela a seguir mostra exemplos de diferentes âmbitos.

| Âmbito | Exemplo |
|:---|:---|
| Máquina virtual única | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Todas as máquinas virtuais num grupo de recursos | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Todas as máquinas virtuais numa subscrição | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Todas as máquinas virtuais com a regra de recolha de dados está associada | `*` |


## <a name="monitors-element"></a>elemento monitores
Lista de uma ou mais cordas que definem quais os monitores na hierarquia da saúde receberão esta sobreposição. Cada elemento pode ser um nome de monitor ou nome de tipo que corresponda a um ou mais monitores que receberão esta sobreposição. 

```json
"monitors": [
    "<monitor name>"
 ],
```



A tabela que se segue lista os nomes atuais do monitor disponível.

| Nome do tipo | Nome | Descrição |
|:---|:---|:---|
| raiz | raiz | Monitor de nível superior que representa a saúde da máquina virtual. | |
| cpu-utilização | cpu-utilização | Monitor de utilização do CPU. | |
| discos lógicos | discos lógicos | Monitor agregado para o estado de saúde de todos os discos monitorizados na máquina virtual do Windows. | |
| discos lógicos\|* | discos lógicos \| C:<br>discos lógicos \| D: | Monitor agregado de rastreio de um determinado disco na máquina virtual do Windows. | 
| \| * \| espaço livre de discos lógicos | discos lógicos \| C: \| espaço livre<br>discos lógicos \| D: \| espaço livre | Monitor de espaço livre de disco na máquina virtual do Windows. |
| sistemas de ficheiros | sistemas de ficheiros | Monitor agregado para a saúde de todos os sistemas de ficheiros na máquina virtual Linux. |
| sistemas de ficheiros\|* | sistemas de ficheiros\|/<br>sistemas de ficheiros \| /mnt | Monitor agregado de rastreio de saúde de um sistema de ficheiros da máquina virtual Linux. | sistemas de ficheiros|/var/log |
| sistemas de arquivos \| * \| espaço livre | sistemas de arquivos \| / \| espaço livre<br>sistemas de ficheiros \| /mnt \| free-space | Monitor de espaço livre de disco no sistema de ficheiros de máquina virtual Linux. | 
| Memória | Memória | Monitor agregado para a saúde da memória da máquina virtual. | |
| memória \| disponível| memória \| disponível | Monitorize a memória disponível na máquina virtual. | |


## <a name="alertconfiguration-element"></a>elemento deconfiguração de alerta
Especifica se deve ser criado um alerta a partir do monitor.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Elemento | Obrigatório | Descrição | 
|:---|:---|:---|
| `isEnabled` | Não | Se for definido como verdadeiro, o monitor gerará alerta ao mudar para um estado crítico ou de alerta e resolverá o alerta quando regressar a um estado saudável. Se for falso ou omitido, não é gerado nenhum alerta.  |


## <a name="monitorconfiguration-element"></a>monitor Elemento deconfiguação
Aplica-se apenas aos monitores unitários. Define a configuração para o monitor, incluindo estados de saúde e como são calculados.

Os parâmetros definem o algoritmo para calcular o valor métrico para comparar com limiares. Em vez de agir numa amostra de dados da métrica subjacente, o monitor avalia várias amostras métricas recebidas dentro da janela do tempo e do tempo de `lookbackSec` avaliação. Todas as amostras recebidas dentro desse prazo são consideradas e se a contagem de amostras for `maxSamples` superior, as amostras mais antigas acima `maxSamples` são ignoradas. 

No caso de haver menos amostras em intervalo de retrocesso do `minSamples` que, o monitor irá mudar para o estado de saúde *desconhecido,* indicando que não há dados suficientes para tomar uma decisão informada sobre a saúde das métricas subjacentes. Se houver um maior número de `minSamples` amostras, então está disponível uma função de agregação especificada por parâmetro de avaliaçãoOposer-nos executar o conjunto para calcular um único valor.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Elemento | Obrigatório | Descrição | 
|:---|:---|:---|
| `evaluationFrequencySecs` | Não | Define a frequência para a avaliação do estado de saúde. Cada monitor é avaliado no momento em que o agente inicia e num intervalo regular definido por este parâmetro posteriormente. |
| `lookbackSecs`   | No | Tamanho da janela de olhar em segundos. |
| `evaluationType` | No | `min` – tirar o valor mínimo de todo o conjunto de amostras<br>`max` - tirar o valor máximo de todo o conjunto de amostras<br>`avg` – recolher a média dos valores definidos de amostras<br>`all` – comparar cada valor no conjunto com os limiares. O monitor comuta o estado se e somente se todas as amostras do conjunto satisfizerem a condição limiar. |
| `minSamples`     | No | Número mínimo de valores a utilizar para calcular o valor. |
| `maxSamples`     | No | Número máximo de valores a utilizar para calcular o valor. |
| `warningCondition`  | No | Lógica de limiar e comparação para a condição de aviso. |
| `criticalCondition` | No | Lógica de limiar e comparação para a condição crítica. |


## <a name="warningcondition-element"></a>elemento de pré-aviso
Define a lógica de limiar e comparação para a condição de aviso. Se este elemento não estiver incluído, o monitor nunca mudará para o estado de saúde de aviso.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Propriedade | Obrigatório | Descrição | 
|:---|:---|:---|
| `isEnabled` | Não | Especifica se a condição está ativada. Se for definido como **falso,** a condição é desativada, mesmo que as propriedades do limiar e do operador possam ser definidas. |
| `threshold` | No | Define o limiar para comparar o valor avaliado. |
| `operator`  | No | Define o operador de comparação a utilizar na expressão limiar. Valores possíveis: >, <, >=, <=, ==. |


## <a name="criticalcondition-element"></a>elemento críticocondição
Define a lógica de limiar e comparação para a condição crítica. Se este elemento não estiver incluído, o monitor nunca mudará para o estado crítico de saúde.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Propriedade | Obrigatório | Descrição | 
|:---|:---|:---|
| `isEnabled` | Não | Especifica se a condição está ativada. Se for definido como **falso,** a condição é desativada, mesmo que as propriedades do limiar e do operador possam ser definidas. |
| `threshold` | No | Define o limiar para comparar o valor avaliado. |
| `operator`  | No | Define o operador de comparação a utilizar na expressão limiar. Valores possíveis: >, <, >=, <=, ==. |

## <a name="sample-data-collection-rule"></a>Regra de recolha de dados de amostra
Para obter uma regra de recolha de dados de amostra que permita a monitorização do hóspede, consulte [Ativar uma máquina virtual utilizando o modelo de Gestor de Recursos](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre [as regras de recolha de dados.](../agents/data-collection-rule-overview.md)