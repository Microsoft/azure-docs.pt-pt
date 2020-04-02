---
title: Regras de ação para alertas do Monitor Azure
description: Compreender quais são as regras de ação no Monitor Azure e como configurá-las e geri-las.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 0498325984641641d6dfc9ee6b89f66800b5c7d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546488"
---
# <a name="action-rules-preview"></a>Regras de ação (pré-visualização)

As regras de ação ajudam-no a definir ou suprimir ações em qualquer âmbito do Gestor de Recursos Azure (subscrição do Azure, grupo de recursos ou recurso-alvo). Eles têm vários filtros que o ajudam a reduzir o subconjunto específico de casos de alerta que você quer agir.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]

## <a name="why-and-when-should-you-use-action-rules"></a>Porquê e quando deve usar as regras de ação?

### <a name="suppression-of-alerts"></a>Supressão de alertas

Há muitos cenários em que é útil suprimir as notificações que os alertas geram. Estes cenários vão desde a supressão durante uma janela de manutenção planeada até à supressão durante o horário não-comercial. Por exemplo, a equipa responsável pela **ContosoVM** quer suprimir notificações de alerta para o próximo fim de semana, porque a **ContosoVM** está a sofrer uma manutenção planeada. 

Embora a equipa possa desativar cada regra de alerta configurada manualmente em **ContosoVM** (e acioná-la novamente após a manutenção), não é um processo simples. As regras de ação ajudam-no a definir a supressão de alerta supressão em escala com a capacidade de configurar de forma flexível o período de supressão. No exemplo anterior, a equipa pode definir uma regra de ação sobre **ContosoVM** que suprime todas as notificações de alerta para o fim de semana.


### <a name="actions-at-scale"></a>Ações à escala

Embora as regras de alerta o ajudem a definir o grupo de ação que dispara quando o alerta é gerado, os clientes têm frequentemente um grupo de ação comum em todo o seu âmbito de operações. Por exemplo, uma equipa responsável pelo grupo de recursos **ContosoRG** provavelmente definirá o mesmo grupo de ação para todas as regras de alerta definidas dentro de **ContosoRG**. 

As regras de ação ajudam a simplificar este processo. Ao definir ações em escala, um grupo de ação pode ser desencadeado para qualquer alerta que seja gerado no âmbito configurado. No exemplo anterior, a equipa pode definir uma regra de ação sobre **ContosoRG** que irá desencadear o mesmo grupo de ação para todos os alertas gerados dentro dela.

> [!NOTE]
> As regras de ação atualmente não se aplicam aos alertas de Saúde do Serviço Azure.

## <a name="configuring-an-action-rule"></a>Configurar uma regra de ação

Pode aceder à funcionalidade selecionando as ações de **Gestão** a partir da página de aterragem de **Alertas** no Monitor Azure. Em seguida, selecione regras de **ação (pré-visualização)**. Pode aceder às regras selecionando regras de **ação (pré-visualização)** a partir do painel de instrumentos da página de aterragem para alertas.

![Regras de ação da página de aterragem do Monitor Azure](media/alerts-action-rules/action-rules-landing-page.png)

Selecione **+ Nova Regra de Ação**. 

![Adicione nova regra de ação](media/alerts-action-rules/action-rules-new-rule.png)

Em alternativa, pode criar uma regra de ação enquanto está a configurar uma regra de alerta.

![Adicione nova regra de ação](media/alerts-action-rules/action-rules-alert-rule.png)

Agora deve ver a página de fluxo para criar regras de ação. Configure os seguintes elementos: 

![Fluxo de criação de novas regras de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Âmbito

Primeiro escolha o âmbito (subscrição Azure, grupo de recursos ou recurso-alvo). Também pode selecionar uma combinação de âmbitos de várias formas dentro de uma única subscrição.

![Âmbito da regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Critérios de filtragem

Pode ainda definir filtros para reduzi-los a um subconjunto específico dos alertas. 

Os filtros disponíveis são: 

* **Severidade**: A opção de selecionar uma ou mais severidades de alerta. **Gravidade = Sev1** significa que a regra de ação é aplicável para todos os alertas definidos para sev1.
* **Serviço de monitorização**: Um filtro baseado no serviço de monitorização de origem. Este filtro também é várias seleções. Por exemplo, **Monitor Service = "Application Insights"** significa que a regra de ação é aplicável para todos os alertas baseados em Insights de Aplicação.
* **Tipo de recurso**: Um filtro baseado num tipo de recurso específico. Este filtro também é várias seleções. Por exemplo, **Tipo de Recurso = "Máquinas Virtuais"** significa que a regra de ação é aplicável a todas as máquinas virtuais.
* **Id**da regra de alerta : Uma opção para filtrar regras específicas de alerta utilizando o ID do Gestor de Recursos da regra de alerta.
* **Condição do monitor**: Um filtro para casos de alerta com **disparados** ou **resolvidos** como condição do monitor.
* **Descrição**: Uma correspondência regex (expressão regular) que define uma correspondência de cordas com a descrição, definida como parte da regra de alerta. Por exemplo, a **descrição contém 'prod'** corresponde a todos os alertas que contenham a corda "prod" nas suas descrições.
* **Contexto de alerta (carga útil)**: Uma correspondência regex que define uma correspondência de cordas com os campos de contexto de alerta da carga útil de um alerta. Por exemplo, o contexto de **alerta (carga útil) contém 'Computer-01'** corresponderá a todos os alertas cujas cargas contêm a corda "Computador-01".

Estes filtros são aplicados em conjunto uns com os outros. Por exemplo, se definir o **tipo de Recurso = Máquinas Virtuais** e **Gravidade' = Sev0,** então filtrado para todos os alertas **Sev0** apenas nos seus VMs. 

![Filtros de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuração de grupo de supressão ou ação

Em seguida, configure a regra de ação para a supressão de alerta ou suporte do grupo de ação. Não pode escolher os dois. A configuração atua em todas as instâncias de alerta que correspondem ao âmbito e filtros previamente definidos.

#### <a name="suppression"></a>Supressão

Se selecionar **a supressão,** configure a duração para a supressão de ações e notificações. Selecione uma das seguintes opções:
* **A partir de agora (Sempre)**: Suprime todas as notificações indefinidamente.
* **Numa hora programada**: Suprime as notificações num prazo limitado.
* **Com uma recorrência**: Suprime notificações num horário diário, semanal ou mensal recorrente.

![Supressão de regras de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupo de ações

Se selecionar o **grupo Action** no alternância, adicione um grupo de ação existente ou crie um novo. 

> [!NOTE]
> Só se pode associar um grupo de ação a uma regra de ação.

![Adicione ou crie nova regra de ação selecionando grupo de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Detalhes da regra da ação

Por último, configure os seguintes detalhes para a regra de ação:
* Nome
* Grupo de recursos em que é salvo
* Descrição 

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Cenário 1: Supressão de alertas com base na gravidade

Contoso quer suprimir notificações para todos os alertas sev4 em todos os VMs dentro da subscrição **ContosoSub** todos os fins de semana.

**Solução:** Criar uma regra de ação com:
* Âmbito = **ContosoSub**
* Filtros
    * Gravidade = **Sev4**
    * Tipo de recurso = **Máquinas Virtuais**
* Supressão com recorrência definida para semana, e **sábado** e **domingo** verificado

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Cenário 2: Supressão de alertas com base no contexto de alerta (carga útil)

Contoso quer suprimir notificações para todos os alertas de registo gerados para **Computer-01** em **ContosoSub** indefinidamente, uma vez que está a passar pela manutenção.

**Solução:** Criar uma regra de ação com:
* Âmbito = **ContosoSub**
* Filtros
    * Monitor Service = **Log Analytics**
    * Contexto de alerta (carga útil) contém **Computador-01**
* Supressão definida para **a partir de agora (Sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Cenário 3: Grupo de ação definido num grupo de recursos

Contoso definiu [um alerta métrico a nível de subscrição.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor) Mas quer definir as ações que desencadeiam especificamente para alertas gerados pelo grupo de recursos **ContosoRG.**

**Solução:** Criar uma regra de ação com:
* Âmbito = **ContosoRG**
* Sem filtros
* Grupo de ação definido para **ContosoActionGroup**

> [!NOTE]
> *Os grupos de ação definidos dentro das regras de ação e das regras de alerta funcionam de forma independente, sem qualquer duplicação.* No cenário descrito anteriormente, se um grupo de ação for definido para a regra de alerta, dispara em conjunto com o grupo de ação definido na regra de ação. 

## <a name="managing-your-action-rules"></a>Gerir as suas regras de ação

Pode ver e gerir as suas regras de ação a partir da vista da lista:

![Visão de lista de regras de ação](media/alerts-action-rules/action-rules-list-view.png)

A partir daqui, pode ativar, desativar ou eliminar regras de ação em escala, selecionando a caixa de verificação ao lado delas. Quando seleciona uma regra de ação, a sua página de configuração abre-se. A página ajuda-o a atualizar a definição da regra de ação e a ativar ou desativar.

## <a name="best-practices"></a>Melhores práticas

Os alertas de registo que cria com o [número de resultados](alerts-unified-log.md) geram uma única instância de alerta utilizando todo o resultado de pesquisa (que pode abranger vários computadores). Neste cenário, se uma regra de ação usar o filtro **Alert Context (carga útil),** atua na instância de alerta desde que haja uma correspondência. No Cenário 2, descrito anteriormente, se os resultados da pesquisa para o alerta de registo gerado contiverem **computador-01** e **Computador-02,** toda a notificação é suprimida. Não há nenhuma notificação gerada para **o Computador-02.**

![Regras de ação e alertas de registo (número de resultados)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Para melhor utilizar alertas de registo com regras de ação, crie alertas de registo com a opção de [medição métrica.](alerts-unified-log.md) Casos de alerta separados são gerados por esta opção, com base no seu campo de grupo definido. Em seguida, no Cenário 2, são geradas instâncias de alerta separadas para **Computador-01** e **Computador-02**. Devido à regra de ação descrita no cenário, apenas a notificação para **Computador-01** é suprimida. A notificação para **o Computador-02** continua a disparar normalmente.

![Regras de ação e alertas de registo (número de resultados)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Enquanto estou a configurar uma regra de ação, gostaria de ver todas as possíveis regras de ação sobrepostas, para evitar notificações duplicadas. É possível fazer isto?

Depois de definir um âmbito à medida que configura uma regra de ação, pode ver uma lista de regras de ação que se sobrepõem no mesmo âmbito (se houver). Esta sobreposição pode ser uma das seguintes opções:

* Uma correspondência exata: Por exemplo, a regra de ação que está a definir e a regra de ação sobreposta estão na mesma subscrição.
* Um subconjunto: Por exemplo, a regra de ação que está a definir está numa subscrição, e a regra de ação sobreposta está num grupo de recursos dentro da subscrição.
* Um superconjunto: Por exemplo, a regra de ação que está a definir está num grupo de recursos, e a regra de ação sobreposta está na subscrição que contém o grupo de recursos.
* Uma intersecção: Por exemplo, a regra de ação que está a definir é em **VM1** e **VM2**, e a regra de ação sobreposta é em **VM2** e **VM3**.

![Regras de ação sobrepostas](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Enquanto estou a configurar uma regra de alerta, é possível saber se já existem regras de ação definidas que possam agir na regra de alerta que estou a definir?

Depois de definir o recurso-alvo para a sua regra de alerta, pode ver a lista de regras de ação que atuam no mesmo âmbito (se houver) selecionando **as ações configuradas do View** na secção **Ações.** Esta lista é povoada com base nos seguintes cenários para o âmbito:

* Uma correspondência exata: Por exemplo, a regra de alerta que está a definir e a regra de ação estão na mesma subscrição.
* Um subconjunto: Por exemplo, a regra de alerta que está a definir está numa subscrição, e a regra de ação está num grupo de recursos dentro da subscrição.
* Um superconjunto: Por exemplo, a regra de alerta que está a definir está num grupo de recursos, e a regra de ação está na subscrição que contém o grupo de recursos.
* Um cruzamento: Por exemplo, a regra de alerta que está a definir é em **VM1** e **VM2**, e a regra de ação é em **VM2** e **VM3**.
    
![Regras de ação sobrepostas](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Posso ver os alertas que foram suprimidos por uma regra de ação?

Na página da lista de [alertas,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)pode escolher uma coluna adicional chamada **Estado de Supressão**. Se a notificação para uma instância de alerta fosse suprimida, mostraria esse estado na lista.

![Casos de alerta suprimidos](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se há uma regra de ação com um grupo de ação e outra com supressão ativa no mesmo âmbito, o que acontece?

A supressão tem sempre precedência sobre o mesmo âmbito.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso que é monitorizado em duas regras de ação separadas? Recebo uma ou duas notificações? Por exemplo, **VM2** no seguinte cenário:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

Para cada alerta em VM1 e VM3, o grupo de ação AG1 seria acionado uma vez. Para cada alerta sobre **vM2**, o grupo de ação AG1 seria desencadeado duas vezes, porque as regras de ação não desduplicam as ações. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso controlado em duas regras de ação separadas e um apela à ação e outro à supressão? Por exemplo, **VM2** no seguinte cenário:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

Para cada alerta em VM1, o grupo de ação AG1 seria acionado uma vez. As ações e notificações para cada alerta sobre VM2 e VM3 serão suprimidas. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>O que acontece se eu tiver uma regra de alerta e uma regra de ação definida para o mesmo recurso chamando diferentes grupos de ação? Por exemplo, **VM1** no seguinte cenário:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
Para cada alerta em VM1, o grupo de ação AG1 seria acionado uma vez. Sempre que a regra de alerta "regra1" for acionada, também irá acionar a AG2 adicionalmente. Os grupos de ação definidos dentro das regras de ação e das regras de alerta funcionam de forma independente, sem qualquer duplicação. 

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre alertas em Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
