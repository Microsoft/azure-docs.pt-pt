---
title: Regras de ação para os alertas do Azure Monitor
description: Compreender o que são regras de ação e como configurar e geri-los.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: f8d7b00de24c566cab204c66371dac9b569c42c9
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65619993"
---
# <a name="action-rules-preview"></a>Regras de ação (pré-visualização)

Regras de ação permitem-lhe definir ações (ou supressão das ações) em qualquer âmbito de Gestor de recursos (subscrição, grupo de recursos, ou recursos). Eles têm uma variedade de filtros que permitem-lhe restringir ao subconjunto específico de instâncias de alertas que pretende atuar em. 

## <a name="why-and-when-should-you-use-action-rules"></a>Por que e quando deve utilizar regras de ação?

### <a name="suppression-of-alerts"></a>Supressão de alertas

Muitas vezes, existem muitos cenários em que seria útil suprimir as notificações geradas através de alertas, que podem ir desde supressão durante uma janela de manutenção planeada para supressão durante o horário não comercial. Por exemplo, a equipe responsável por 'ContosoVM' quer suprimir notificações de alerta para o fim de semana futura, uma vez que 'ContosoVM' está numa manutenção planeada. Embora eles podem desativar cada alerta regra configuradas manualmente no 'ContosoVM' (e voltar a ativá-lo postar a manutenção), não é uma experiência simple. Regras de ação permitem-lhe definir a supressão de alerta em escala com a capacidade de forma flexível configurar o período de supressão. Voltando ao exemplo anterior, a equipe agora pode definir uma regra de ação no 'ContosoVM', que irá suprimir a todas as notificações de alerta para o fim de semana.


### <a name="actions-at-scale"></a>Ações em escala

Embora as regras de alerta permitem-lhe definir o grupo de ação que aciona quando é gerado o alerta, os clientes tendem, muitas vezes ter um grupo de ação comum entre seu escopo de operações. Por exemplo, uma equipe responsável por grupo de recursos "ContosoRG" provavelmente definirá o mesmo grupo de ação para todas as regras de alerta definido dentro de 'ContosoRG'. Regras de ação permitem-lhe simplificar esse processo, permitindo-lhe definir ações em escala, para que um grupo de ação pode ser acionado para qualquer alerta gerado no âmbito de aplicação configurado. Voltando ao exemplo anterior, a equipe agora pode definir uma regra de ação no 'ContosoRG', que irão acionar o mesmo grupo de ação para todos os alertas gerados dentro do mesmo.


## <a name="configuring-an-action-rule"></a>Configurar uma regra de ação

Pode acessar o recurso, selecionando **gerir ações** dos alertas página de destino no Azure Monitor. Em seguida, selecione **regras de ação (pré-visualização)**. Pode acessá-los, selecionando **regras de ação (pré-visualização)** a partir do dashboard da página de destino para alertas.

![Regras de ação a partir da página de aterrissagem do Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Selecione **+ nova regra de ação**. 

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-new-rule.png)

Em alternativa, também pode optar por criar uma regra de ação ao configurar uma regra de alerta.

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-alert-rule.png)

Agora, deverá ver o fluxo de criação de regra de ação abrir. Configure os seguintes elementos: 

![Novo fluxo de criação de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Em primeiro lugar, escolha o âmbito, ou seja, o recurso de destino, grupo de recursos ou subscrição. Tem também a capacidade para selecionar uma combinação de qualquer um dos acima (dentro de uma única subscrição). 

![Âmbito da regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Critérios de filtro

Além disso, pode definir filtros para restrinja ainda mais para baixo para um subconjunto específico dos alertas no âmbito definido. 

Os filtros disponíveis são: 

* **Gravidade**: Selecione um ou mais gravidades de alerta. Gravidade = Sev1 significa que a regra de ação é aplicável para todos os alertas com gravidade como Sev1.
* **Monitorizar serviço**: Filtrar com base no serviço de monitorização de origem. Isso também é seleção múltipla. Por exemplo, o Monitor de serviço = "Application Insights" significa que a regra de ação é aplicável para todos os "Application Insights" com base em alertas.
* **Tipo de recurso**: Filtrar com base num tipo de recurso específico. Isso também é seleção múltipla. Por exemplo, tipo de recurso = "Virtual Machines" significa que a regra de ação aplicável a todas as máquinas virtuais.
* **ID de regra de alerta**: Permite-lhe filtrar por regras de alerta específicas com o ID de Gestor de recursos de regra de alerta.
* **Monitorizar a condição**: Filtro para as instâncias de alerta com "Fired" ou "Resolvido" como a condição do monitor.
* **Descrição**: RegEx correspondentes na descrição definida como parte da regra de alerta.
* **Contexto do alerta (payload)**: Correspondência de RegEx dentro de [contexto do alerta](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) campos de uma instância de alerta.

Estes filtros são aplicados em conjunto entre si. Por exemplo, se eu defini o "Tipo de recurso" = 'Máquinas virtuais' e 'Gravidade' = "Sev0", em seguida, posso ter filtrado para todos os alertas de 'Sev0' nas minhas VMs apenas. 

![Filtros de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuração do grupo de ação ou de supressão

Em seguida, configure a regra de ação para a supressão de alerta ou suporte de grupo de ação. Não é possível escolher os dois. A configuração funciona em todas as instâncias de alerta de correspondência do âmbito definido anteriormente e filtros.

#### <a name="suppression"></a>Supressão

Se selecionou **supressão**, configurar a duração para a supressão de ações e notificações. Escolha um dos seguintes:
* **De agora (sempre)**: Suprime todas as notificações indefinidamente.
* **Num horário agendado**: Suprimir notificações dentro de um período de tempo vinculado.
* **Com uma recorrência**: Suprimir numa agenda de periodicidade, que pode ser diária, semanal ou mensal.

![Supressão de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupo de ações

Se selecionou **grupo de ação** no botão de alternar, adicione um grupo de ação existente ou crie um novo. 

> [!NOTE]
> Pode associar apenas um grupo de ação com uma regra de ação.

![Grupo de ação de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Detalhes da regra de ação

Por fim, configure os seguintes detalhes para a regra de ação
* Name
* Grupo de recursos na qual vai ser guardado
* Descrição 

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Cenário 1: Supressão de alertas com base na gravidade

Contoso quer suprimir notificações para todos os alertas de Sev4 em todas as VMs dentro de suas assinaturas 'ContosoSub' cada fim de semana.

**Solução:** Criar uma regra de ação com
* Scope = 'ContosoSub'
* Filtros
    * Gravidade = 'Sev4'
    * Tipo de recurso = "Máquinas virtuais"
* Supressão com periodicidade definida como semanal e "Sábado" e "Domingo" verificados

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Cenário 2: Supressão de alertas com base no contexto do alerta (payload)

A Contoso pretende suprimir notificações para todos os alertas geradas para o computador-01 de registo em 'ContosoSub' indefinidamente como ele está a passar por manutenção.

**Solução:** Criar uma regra de ação com
* Scope = 'ContosoSub'
* Filtros
    * Monitorizar serviço = "Log Analytics"
    * Contexto do alerta (payload) contém o computador-01
* Supressão definido como "daqui (sempre)"

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Cenário 3: Grupo de ação definido num grupo de recursos

Contoso definiu [um alerta de métrica ao nível da subscrição](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), mas pretende definir as ações que acionam alertas em separado para o grupo de recursos 'ContosoRG'.

**Solução:** Criar uma regra de ação com
* Scope = 'ContosoRG'
* Não existem filtros
* Grupo de ação definida como 'ContosoActionGroup'

## <a name="managing-your-action-rules"></a>Gerir as regras de ação

Pode ver e gerir as regras de ação a partir da vista de lista, conforme mostrado abaixo.

![Vista de lista de regras de ação](media/alerts-action-rules/action-rules-list-view.png)

A partir daqui, pode regras de ação de ativar/desativar/eliminar em escala, selecionando a caixa de verificação junto a eles. Clicar em qualquer regra de ação abre a página de configuração, permitindo que Atualize sua definição e ativar/desativá-lo.

## <a name="best-practices"></a>Melhores práticas

Registar alertas criados com o [número de resultados](alerts-unified-log.md) opção gerar **uma única instância de alerta** usando o resultado de pesquisa inteiro (que poderia ser em vários computadores por exemplo). Neste cenário, se uma regra de ação utiliza o filtro de "Contexto de alerta (payload)", ele se aplica a instância de alerta, desde que existe uma correspondência. Cenário 2, tal como descrito anteriormente, se os resultados da pesquisa para o alerta de registo gerado contêm "Computador-01" e 'Computador-02', a notificação toda suprimida (ou seja, não há nenhuma notificação gerada para o computador-02 de todo).

![Regras de ação e alertas de registo (número de resultados)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

A alertas de registo de tirar partido das melhores com regras de ação, Aconselhamo-lo para criar alertas de registo com o ['medida da métrica'](alerts-unified-log.md) opção. Utilizar esta opção, instâncias separadas de alertas são geradas com base no campo grupo definido. Em seguida, no cenário 2, instâncias separadas de alerta geradas para "Computador-01" e 'Computador-02'. Com a regra de ação descrita no cenário, apenas a notificação para o computador-01 poderia ser suprimida enquanto a notificação para o computador-02 continuaria a ser disparado normalmente.

![Regras de ação e alertas de registo (número de resultados)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

* P. Ao configurar uma regra de ação, eu gostaria de ver todos os possíveis a sobreposição de ação de regras, de modo que posso evitar notificações duplicadas. É possível fazer isso?

    R. Depois de definir um âmbito ao configurar uma regra de ação, pode ver uma lista de regras de ação que se sobreponha no mesmo âmbito (se houver). Essa sobreposição pode ser uma das seguintes opções:
    * Uma correspondência exata: Por exemplo, a regra de ação que está a definir e a regra de ação sobrepostos são na mesma subscrição.
    * Um subconjunto: Por exemplo, a regra de ação que está a definir é uma subscrição e a regra de ação sobrepostos é num grupo de recursos dentro da subscrição.
    * Um superconjunto: Por exemplo, a regra de ação que está a definir é num grupo de recursos e a regra de ação sobrepostos está na subscrição que contém o grupo de recursos.
    * Uma interseção: Por exemplo, a regra de ação que está a definir é "VM1" e "VM2" e a regra de ação sobrepostos é em "VM2" e 'VM3'.

    ![A sobreposição de regras de ação](media/alerts-action-rules/action-rules-overlapping.png)

* P. Enquanto configura uma regra do alerta, é possível saber se já existem regras de ação definidos que pode tomar decisões sobre a regra de alerta que estou definindo?

    R. Depois de definir o recurso de destino para a regra de alerta, pode ver a lista de regras de ação que funcionam no mesmo âmbito (se houver), clicando no "Modo de exibição configuradas ações" na seção "Ações". Esta lista é preenchida com base nos seguintes cenários para o âmbito:
    * Uma correspondência exata: Por exemplo, a regra de alerta que está a definir e a regra de ação estão na mesma subscrição.
    * Um subconjunto: Por exemplo, a regra de alerta que está a definir é uma subscrição e a regra de ação é num grupo de recursos dentro da subscrição.
    * Um superconjunto: Por exemplo, a regra de alerta que está a definir é num grupo de recursos e a regra de ação está na subscrição que contém o grupo de recursos.
    * Uma interseção: Por exemplo, a regra de alerta que está a definir é "VM1" e "VM2" e a regra de ação é em "VM2" e 'VM3'.
    
    ![A sobreposição de regras de ação](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* P. Pode ver os alertas que foram suprimidos por uma regra de ação?

    R. Na [página de lista de alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), existe uma coluna adicional que pode ser escolhida chamado 'Status de supressão'. Se a notificação para uma instância de alerta foi suprimida, mostraria esse Estado na lista.

    ![Instâncias de alerta suprimidas](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* P. Se existir uma regra de ação com um grupo de ação e outro para supressão Active Directory no mesmo escopo, o que acontece?

    R. **Supressão sempre terão precedência sobre o mesmo escopo**.

* P. O que acontece se eu tiver um recurso monitorizado no duas regras de ação separada? Obter notificações de um ou dois? Por exemplo "VM2" neste cenário:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    R. Para cada alerta no "VM1" e "VM3", grupo de ação 'AG1' seria acionado uma vez. Para cada alerta no "VM2", grupo de ação 'AG1' seria acionado duas vezes (**regras de ação não remover a duplicação de ações**). 

* P. O que acontece se eu tiver um recurso monitorizado no duas regras de ação separada e uma chama para a ação ao outro para supressão? Por exemplo, VM2 neste cenário:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    R. Para cada alerta na "VM1", o grupo de ação 'AG1' seria acionado uma vez. Ações e notificações para cada alerta no "VM2" e 'VM3' serão suprimidas. 

* P. O que acontece se eu tiver uma regra de alerta e uma regra de ação definidos para o mesmo recurso chamar grupos de ação diferentes? Por exemplo, VM1 neste cenário:

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    R. Para cada alerta na "VM1", o grupo de ação 'AG1' seria acionado uma vez. Sempre que a regra de alerta 'rule1' é acionada, também irá acionar 'AG2' além disso. **Grupos de ação definida dentro das regras de ação e regras de alerta funcionam de forma independente, com a eliminação de duplicação**. 

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre os alertas no Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
