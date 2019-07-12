---
title: Regras de ação para os alertas do Azure Monitor
description: Compreender o que são regras de ação no Azure Monitor e como configurar e geri-los.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656729"
---
# <a name="action-rules-preview"></a>Regras de ação (pré-visualização)

Regras de ação ajudam a definir ou suprimir ações em qualquer âmbito de acesso do Azure Resource Manager (subscrição do Azure, grupo de recursos ou recurso de destino). Eles têm vários filtros que ajuda a restringir o subconjunto específico de instâncias de alertas que deseja agir sobre.

## <a name="why-and-when-should-you-use-action-rules"></a>Por que e quando deve utilizar regras de ação?

### <a name="suppression-of-alerts"></a>Supressão de alertas

Existem muitos cenários em que é útil suprimir as notificações que geram alertas. Esses cenários podem variar desde supressão durante uma janela de manutenção planeada para supressão durante o horário de expediente. Por exemplo, a equipe responsável por **ContosoVM** quer suprimir notificações de alerta para o fim de semana futura, pois **ContosoVM** está em manutenção planeada. 

Embora a equipe pode desativar a cada regra de alerta que está configurada no **ContosoVM** manualmente (e ativá-la depois de manutenção), não é um processo simples. Regras de ação ajudam a definir a supressão de alerta em escala com a capacidade de forma flexível configurar o período de supressão. No exemplo anterior, a equipe pode definir uma regra de ação numa **ContosoVM** que suprime todas as notificações de alerta para o fim de semana.


### <a name="actions-at-scale"></a>Ações em escala

Embora as regras de alerta ajudam a definir o grupo de ação que aciona quando é gerado o alerta, os clientes têm, muitas vezes, um grupo de ação comum entre seu escopo de operações. Por exemplo, uma equipe responsável por grupo de recursos **ContosoRG** provavelmente irá definir o mesmo grupo de ação para todas as regras de alerta definidas dentro **ContosoRG**. 

Regras de ação ajudam a simplificar esse processo. Ao definir ações em escala, um grupo de ação pode ser acionado para qualquer alerta que é gerado no âmbito de aplicação configurado. No exemplo anterior, a equipe pode definir uma regra de ação numa **ContosoRG** isso irá disparar o mesmo grupo de ação para todos os alertas gerados dentro do mesmo.

> [!NOTE]
> Regras de ação atualmente não se aplicam aos alertas do Azure Service Health.

## <a name="configuring-an-action-rule"></a>Configurar uma regra de ação

Pode acessar o recurso, selecionando **gerir ações** partir a **alertas** página de destino no Azure Monitor. Em seguida, selecione **regras de ação (pré-visualização)** . Pode acessar as regras selecionando **regras de ação (pré-visualização)** a partir do dashboard da página de destino para alertas.

![Regras de ação a partir da página de aterrissagem do Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Selecione **+ nova regra de ação**. 

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-new-rule.png)

Em alternativa, pode criar uma regra de ação durante a configuração de uma regra de alerta.

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-alert-rule.png)

Agora, deve ver a página de fluxo para a criação de regras de ação. Configure os seguintes elementos: 

![Novo fluxo de criação de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Em primeiro lugar, escolha o âmbito (subscrição do Azure, grupo de recursos ou recurso de destino). Pode também vários-Selecione uma combinação de âmbitos numa única subscrição.

![Âmbito da regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Critérios de filtro

Além disso, pode definir filtros para refiná-los para um subconjunto específico dos alertas. 

Os filtros disponíveis são: 

* **Gravidade**: A opção de selecionar um ou mais gravidades de alerta. **Gravidade = Sev1** significa que a regra de ação é aplicável em todos os alertas, defina como Sev1.
* **Monitorizar serviço**: Um filtro com base no serviço de monitorização de origem. Este filtro também é selecionar vários. Por exemplo, **Monitor Service = "Application Insights"** significa que a regra de ação é aplicável a todos os alertas com base no Application Insights.
* **Tipo de recurso**:  Um filtro com base num tipo de recurso específico. Este filtro também é selecionar vários. Por exemplo, **tipo de recurso = "Virtual Machines"** significa que a regra de ação é aplicável a todas as máquinas virtuais.
* **ID de regra de alerta**: Uma opção para filtrar por regras de alerta específicas utilizando o ID de Gestor de recursos da regra de alerta.
* **Monitorizar a condição**:  Um filtro para as instâncias de alerta com ambos **Fired** ou **resolvido** como a condição do monitor.
* **Descrição**: Uma correspondência de regex (expressão regular) que define uma correspondência de cadeia de caracteres com a descrição, definida como parte da regra de alerta. Por exemplo, **descrição contém "prod"** irá corresponder a todos os alertas que contenham a cadeia "prod" em suas descrições.
* **Alerta de contexto (payload)** : Uma correspondência de regex que define uma correspondência de cadeia de caracteres com os campos de contexto do alerta do payload de um alerta. Por exemplo, **contexto (payload) contém o computador-01 de alerta** corresponderá a todos os alertas cujos payloads contêm a cadeia de caracteres "Computador-01."

Estes filtros são aplicados em conjunto com um do outro. Por exemplo, se definir **tipo de recurso "= máquinas virtuais** e **gravidade ' = Sev0**, em seguida, filtrar para todos os **Sev0** alertas em apenas as VMs. 

![Filtros de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuração do grupo de ação ou de supressão

Em seguida, configure a regra de ação para a supressão de alerta ou suporte de grupo de ação. Não é possível escolher os dois. A configuração funciona em todas as instâncias de alerta que correspondem à âmbito definido anteriormente e filtros.

#### <a name="suppression"></a>Supressão

Se selecionou **supressão**, configurar a duração para a supressão de ações e notificações. Selecione uma das seguintes opções:
* **De agora (sempre)** : Suprime todas as notificações indefinidamente.
* **Num horário agendado**: Suprime notificações dentro de um período de tempo vinculado.
* **Com uma recorrência**: Suprime notificações com base numa agenda diária, semanal ou mensal recorrente.

![Supressão de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>grupo de ação

Se selecionou **grupo de ação** no botão de alternar, adicione um grupo de ação existente ou crie um novo. 

> [!NOTE]
> Pode associar apenas um grupo de ação com uma regra de ação.

![Adicionar ou criar nova regra de ação ao selecionar o grupo de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Detalhes da regra de ação

Por fim, configure os seguintes detalhes para a regra de ação:
* Nome
* Grupo de recursos em que é guardado
* Descrição 

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Cenário 1: Supressão de alertas com base na gravidade

A Contoso pretende suprimir notificações para todos os alertas de Sev4 em todas as VMs dentro da subscrição **ContosoSub** cada fim de semana.

**Solução:** Crie uma regra de ação com:
* Âmbito = **ContosoSub**
* Filtros
    * Severity = **Sev4**
    * Tipo de recurso = **máquinas virtuais**
* Supressão com periodicidade, definida como semanal, e **Sábado** e **Domingo** verificado

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Cenário 2: Supressão de alertas com base no contexto do alerta (payload)

A Contoso pretende suprimir notificações para todos os alertas geradas para de registo **computador-01** na **ContosoSub** indefinidamente, pois está a passar por manutenção.

**Solução:** Crie uma regra de ação com:
* Âmbito = **ContosoSub**
* Filtros
    * Monitorizar serviço = **do Log Analytics**
    * Alerta de contexto (payload) contém **computador-01**
* Supressão definido como **daqui (sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Cenário 3: Grupo de ação definido num grupo de recursos

Contoso definiu [um alerta de métrica ao nível da subscrição](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Mas o que ele deseja definir as ações que acionam especificamente para alertas geradas a partir do grupo de recursos **ContosoRG**.

**Solução:** Crie uma regra de ação com:
* Âmbito = **ContosoRG**
* Não existem filtros
* Grupo de ação definida como **ContosoActionGroup**

> [!NOTE]
> *Grupos de ações definidas dentro das regras de ação e regras de alerta funcionam de forma independente, com a sem eliminação de duplicados.* No cenário descrito anteriormente, se um grupo de ação é definida para a regra de alerta, aciona em conjunto com o grupo de ação definido na regra de ação. 

## <a name="managing-your-action-rules"></a>Gerir as regras de ação

Pode ver e gerir as regras de ação a partir da vista de lista:

![Vista de lista de regras de ação](media/alerts-action-rules/action-rules-list-view.png)

A partir daqui, pode ativar, desativar ou eliminar regras de ação em escala, selecionando a caixa de verificação junto a eles. Quando seleciona uma regra de ação, é aberta a página de configuração. A página de ajuda-o a atualizar a definição da regra de ação e ativar ou desativar a ele.

## <a name="best-practices"></a>Melhores práticas

Registar alertas que criar com o [número de resultados](alerts-unified-log.md) opção gerar uma única instância de alerta com o resultado da pesquisa inteira (que pode abranger em vários computadores). Neste cenário, se uma regra de ação utiliza a **contexto de alerta (payload)** filtro, atua na instância do alerta, desde que existe uma correspondência. No cenário 2, ' descrito anteriormente, se os resultados de pesquisa para o alerta de registo gerado contêm ambos **computador-01** e **computador-02**, a notificação toda suprimida. Não existe nenhuma notificação gerada para **computador-02** de todo.

![Regras de ação e alertas de registo (número de resultados)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Para melhor utilizar alertas de registo com regras de ação, crie alertas de registo com o [medida da métrica](alerts-unified-log.md) opção. Instâncias separadas de alertas são geradas por esta opção, com base no seu campo de grupo definidas. Em seguida, no cenário 2, instâncias separadas de alertas são geradas para **computador-01** e **computador-02**. Devido à regra de ação descritas no cenário, apenas a notificação **computador-01** suprimido. A notificação **computador-02** continua a ser disparado normalmente.

![Regras de ação e alertas de registo (número de resultados)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Enquanto estiver a configurar uma regra de ação, eu gostaria de ver todos os possíveis a sobreposição de regras de ação, para que posso evitar notificações duplicadas. É possível fazer isso?

Depois de definir um âmbito à medida que configura uma regra de ação, pode ver uma lista de regras de ação que se sobreponha no mesmo âmbito (se houver). Essa sobreposição pode ser uma das seguintes opções:

* Uma correspondência exata: Por exemplo, a regra de ação que definirá e a regra de ação sobrepostos são na mesma subscrição.
* Um subconjunto: Por exemplo, a regra de ação que definirá está numa subscrição e a regra de ação sobrepostos é num grupo de recursos dentro da subscrição.
* Um superconjunto: Por exemplo, a regra de ação que definirá é num grupo de recursos e a regra de ação sobrepostos está na subscrição que contém o grupo de recursos.
* Uma intersecção: Por exemplo, a regra de ação definirá é no **VM1** e **VM2**, e a regra de ação sobrepostos está no **VM2** e **VM3**.

![A sobreposição de regras de ação](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Enquanto estiver a configurar uma regra de alerta, é possível saber se já existem regras de ação definidos que pode tomar decisões sobre a regra de alerta que estou definindo?

Depois de definir o recurso de destino para a regra de alerta, pode ver a lista de regras de ação que atuam no mesmo âmbito (se houver), selecionando **ações de configurado de modo de exibição** sob a **ações** secção. Esta lista é preenchida com base nos seguintes cenários para o âmbito:

* Uma correspondência exata: Por exemplo, a regra de alerta que estiver a definir e a regra de ação estão na mesma subscrição.
* Um subconjunto: Por exemplo, a regra de alerta que estiver a definir é uma subscrição e a regra de ação é num grupo de recursos dentro da subscrição.
* Um superconjunto: Por exemplo, a regra de alerta que estiver a definir é num grupo de recursos e a regra de ação está na subscrição que contém o grupo de recursos.
* Uma intersecção: Por exemplo, a regra de alerta estiver a definir é no **VM1** e **VM2**, e a regra de ação é no **VM2** e **VM3**.
    
![A sobreposição de regras de ação](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Pode ver os alertas que foram suprimidos por uma regra de ação?

Na [página de lista de alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), pode escolher uma coluna adicional chamada **estado de supressão**. Se a notificação para uma instância de alerta foi suprimida, mostraria esse Estado na lista.

![Instâncias de alerta suprimidas](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se existir uma regra de ação com um grupo de ação e outro para supressão Active Directory no mesmo escopo, o que acontece?

Supressão sempre terão precedência sobre o mesmo âmbito.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso que está sendo monitorado no duas regras de ação separada? Obter notificações de um ou dois? Por exemplo, **VM2** no seguinte cenário:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

Para cada alerta no VM1 e VM3, o grupo de ação AG1 seria acionado uma vez. Para cada alerta no **VM2**, grupo de ação AG1 seria acionada duas vezes, uma vez que as regras de ação não eliminar duplicados no ações. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso monitorizado no duas regras de ação separada e uma chama para a ação ao outro para supressão? Por exemplo, **VM2** no seguinte cenário:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

Para cada alerta na VM1, o grupo de ação AG1 seria acionado uma vez. Ações e notificações para cada alerta no VM2 e VM3 serão suprimidas. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>O que acontece se eu tiver uma regra de alerta e uma regra de ação definidos para o mesmo recurso chamar grupos de ação diferentes? Por exemplo, **VM1** no seguinte cenário:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
Para cada alerta na VM1, o grupo de ação AG1 seria acionado uma vez. Sempre que a regra de alerta "rule1" é acionada, também irá acionar AG2 além disso. Grupos de ações definidas dentro das regras de ação e regras de alerta funcionam de forma independente, com a sem eliminação de duplicados. 

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre os alertas no Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
