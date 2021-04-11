---
title: Regras de ação para alertas do Monitor Azure
description: Compreender quais são as regras de ação no Azure Monitor e como configurá-las e geri-las.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: 12e7cf8e72c5423b4a2edd6ea2a0f4537e328b08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105036786"
---
# <a name="action-rules-preview"></a>Regras de ação (pré-visualização)

As regras de ação permitem adicionar ou suprimir os grupos de ação nos seus alertas disparados. Uma única regra pode abranger diferentes âmbitos de recursos-alvo, por exemplo - qualquer alerta sobre um recurso específico (como uma máquina virtual específica) ou qualquer alerta disparado sobre qualquer recurso numa subscrição. Pode opcionalmente adicionar vários filtros para controlar quais os alertas que estão cobertos por uma regra e definir um horário para o mesmo, por exemplo, para que esteja em vigor apenas fora do horário comercial ou durante uma janela de manutenção planeada.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Porquê e quando deves usar regras de ação?

### <a name="suppression-of-alerts"></a>Supressão de alertas

Há muitos cenários em que é útil suprimir as notificações que os alertas geram. Estes cenários vão desde a supressão durante uma janela de manutenção planeada até à supressão durante o horário não comercial. Por exemplo, a equipa responsável pela  **ContosoVM** quer suprimir notificações de alerta para o próximo fim de semana, porque a **ContosoVM** está a ser submetida a uma manutenção planeada.

Embora a equipa possa desativar cada regra de alerta configurada manualmente no **ContosoVM** (e ative-a novamente após a manutenção), não é um processo simples. As regras de ação ajudam-no a definir a supressão de alerta em escala com a capacidade de configurar de forma flexível o período de supressão. No exemplo anterior, a equipa pode definir uma regra de ação sobre **o ContosoVM** que suprime todas as notificações de alerta para o fim de semana.

### <a name="actions-at-scale"></a>Ações em escala

Embora as regras de alerta o ajudem a definir o grupo de ação que desencadeia quando o alerta é gerado, os clientes têm frequentemente um grupo de ação comum em todo o seu âmbito de operações. Por exemplo, uma equipa responsável pelo grupo de recursos **ContosoRG** provavelmente definirá o mesmo grupo de ação para todas as regras de alerta definidas dentro **do ContosoRG**.

As regras de ação ajudam-no a simplificar este processo. Ao definir ações em escala, um grupo de ação pode ser desencadeado para qualquer alerta gerado no âmbito configurado. No exemplo anterior, a equipa pode definir uma regra de ação sobre **o ContosoRG** que irá desencadear o mesmo grupo de ação para todos os alertas gerados dentro do mesmo.

> [!NOTE]
> As regras de ação não se aplicam aos alertas de Saúde do Serviço Azure.

## <a name="configuring-an-action-rule"></a>Configurar uma regra de ação

### <a name="portal"></a>[Portal](#tab/portal)

Pode aceder à funcionalidade selecionando **ações** de Gestão a partir da página de aterragem **de Alertas** no Monitor Azure. Em seguida, selecione **regras de ação (pré-visualização)**. Pode aceder às regras selecionando **regras de ação (pré-visualização)** a partir do painel de instrumentos da página de aterragem para alertas.

![Regras de ação da página de aterragem do Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Selecione **+ Nova Regra de Ação**.

![A screenshot mostra a página 'Gerir acções' com o botão New Action Rule realçado.](media/alerts-action-rules/action-rules-new-rule.png)

Em alternativa, pode criar uma regra de ação enquanto configura uma regra de alerta.

![A screenshot mostra a página de regra criar com o botão de regra de ação Criar realçado.](media/alerts-action-rules/action-rules-alert-rule.png)

Deve agora ver a página de fluxo para criar regras de ação. Configure os seguintes elementos:

![Novo fluxo de criação de regras de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Âmbito

Primeiro escolha o âmbito (subscrição Azure, grupo de recursos ou recurso alvo). Também pode selecionar uma combinação de âmbitos numa única subscrição.

![Âmbito de regras de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria&quot;></a>Critérios de filtragem

Pode definir opcionalmente filtros para que a regra se aplique a um subconjunto específico dos alertas, ou a eventos específicos em cada alerta (por exemplo, apenas &quot;Disparado&quot; ou apenas &quot;Resolvido").

Os filtros disponíveis são:

* **Gravidade**  
Esta regra aplica-se apenas a alertas com as severidades selecionadas.  
Por exemplo, **gravidade = Sev1** significa que a regra se aplicará apenas a alertas com severidade Sev1.
* **Serviço de monitorização**  
Esta regra aplica-se apenas aos alertas provenientes dos serviços de monitorização selecionados.  
Por exemplo, **o serviço de monitorização = "Azure Backup"** significa que a regra se aplicará apenas a alertas de backup (provenientes de Azure Backup).
* **Tipo de recurso**  
Esta regra aplica-se apenas aos alertas sobre os tipos de recursos selecionados.  
Por exemplo, **o tipo de recurso = "Máquinas Virtuais"** significa que a regra se aplicará apenas a alertas em máquinas virtuais.
* **ID da regra de alerta**  
Esta regra aplica-se apenas aos alertas provenientes de uma regra de alerta específica. O valor deve ser o ID do Gestor de Recursos da regra de alerta.  
Por exemplo, **o ID da regra de alerta = "/subscrições/SubId1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/API-Latency"** significa que esta regra se aplicará apenas aos alertas provenientes da regra de alerta métrico "API-Latência".  
_NOTA - pode obter o ID de regra de alerta adequado, listando as suas regras de alerta a partir do CLI, ou abrindo uma regra de alerta específica no portal, clicando em "Propriedades", e copiando o valor "ID de recursos"._
* **Condição do monitor**  
Esta regra aplica-se apenas a eventos de alerta com a condição de monitor especificado - **disparado** ou **resolvido**.
* **Descrição**  
Esta regra aplica-se apenas a alertas que contenham uma cadeia específica no campo de descrição do alerta. O campo contém a descrição da regra de alerta.  
Por exemplo, **a descrição contém 'prod'** significa que a regra apenas corresponderá aos alertas que contêm a "ad" da corda na sua descrição.
* **Contexto de alerta (carga útil)**  
Esta regra aplica-se apenas a alertas que contenham qualquer um ou mais valores específicos nos campos de contexto de alerta.  
Por exemplo, **o contexto de alerta (carga útil) contém 'Computador-01'** significa que a regra só se aplicará a alertas cuja carga útil contenha a cadeia "Computador-01".

Se definir vários filtros numa regra, todos eles se aplicam. Por exemplo, se definir o **tipo de recurso' = Máquinas Virtuais** e **gravidade' = Sev0**, então a regra aplicar-se-á apenas aos alertas Sev0 em máquinas virtuais.

![Filtros de regras de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Supressão ou configuração do grupo de ação

Em seguida, configurar a regra de ação para a supressão de alerta ou suporte do grupo de ação. Não pode escolher os dois. A configuração atua em todas as instâncias de alerta que correspondam ao âmbito e filtros previamente definidos.

#### <a name="suppression"></a>Supressão

Se selecionar **a supressão,** configuure a duração para a supressão de ações e notificações. Selecione uma das seguintes opções:
* **A partir de agora (Sempre)**: Suprime todas as notificações indefinidamente.
* **Em tempo programado**: Suprime as notificações numa duração limitada.
* **Com uma recorrência**: Suprime as notificações num horário diário, semanal ou mensal recorrente.

![Supressão da regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupo de ações

Se selecionar o **Grupo de Ação** no toggle, adicione um grupo de ação existente ou crie um novo.

> [!NOTE]
> Só pode associar um grupo de ação a uma regra de ação.

![Adicione ou crie uma nova regra de ação selecionando o Grupo de Ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Detalhes da regra de ação

Por último, configurar os seguintes detalhes para a regra de ação:
* Name
* Grupo de recursos no qual é salvo
* Description

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode criar regras de ação com o CLI Azure utilizando o comando [de criação de regra de ação do monitor az.](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create)  A `az monitor action-rule` referência é apenas uma das muitas [referências Azure CLI para o Azure Monitor](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Preparar o ambiente

1. [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

   Se preferir, também pode usar a Azure Cloud Shell para completar os passos deste artigo.  Azure Cloud Shell é um ambiente de conchas interativas que utiliza através do seu navegador.  Inicie a Cloud Shell utilizando um destes métodos:

   - Open Cloud Shell indo para [https://shell.azure.com](https://shell.azure.com)

   - Selecione o botão **Cloud Shell** na barra de menu no canto superior direito no [portal Azure](https://portal.azure.com)

1. Inicie sessão.

   Se estiver a utilizar uma instalação local do CLI, inicie sessão com o comando [de login az.](/cli/azure/reference-index#az-login)  Siga os passos apresentados no seu terminal para concluir o processo de autenticação.

    ```azurecli
    az login
    ```

1. Instale a `alertsmanagement` extensão

   O `az monitor action-rule` comando é uma extensão experimental do núcleo Azure CLI. Saiba mais sobre referências de extensão de [extensão de utilização com Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name alertsmanagement
   ```

   Espera-se o seguinte aviso.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Criar regras de ação com o Azure CLI

Consulte o conteúdo de referência do CLI Azure para [a az monitor de regras de ação criar](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) para aprender sobre os parâmetros necessários e opcionais.

Criar uma regra de ação para suprimir notificações num grupo de recursos.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Crie uma regra de ação para suprimir notificações para todos os alertas Sev4 em todos os VMs dentro da subscrição todos os fins de semana.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Cenário 1: Supressão de alertas com base na gravidade

A Contoso quer suprimir as notificações de todos os alertas Sev4 sobre todos os VMs da subscrição **ContosoSub** todos os fins de semana.

**Solução:** Criar uma regra de ação com:
* Âmbito = **ContosoSub**
* Filtros
    * Severidade = **Sev4**
    * Tipo de recurso = **Máquinas Virtuais**
* Supressão com recorrência definida para semanalmente, e **sábado** e **domingo** verificado

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Cenário 2: Supressão de alertas com base no contexto de alerta (carga útil)

A Contoso quer suprimir as notificações de todos os alertas de registo gerados para **o Computador-01** em **ContosoSub** indefinidamente, uma vez que está a passar pela manutenção.

**Solução:** Criar uma regra de ação com:
* Âmbito = **ContosoSub**
* Filtros
    * Serviço de Monitorização = **Log Analytics**
    * Contexto de alerta (carga útil) contém **Computador-01**
* Supressão definida para **A partir de agora (Sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Cenário 3: Grupo de ação definido num grupo de recursos

Contoso definiu [um alerta métrico a nível de subscrição.](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) Mas quer definir as ações que desencadeiam especificamente para alertas gerados pelo grupo de recursos **ContosoRG.**

**Solução:** Criar uma regra de ação com:
* Âmbito = **ContosoRG**
* Sem filtros
* Grupo de ação definido para **o ContosoActionGroup**

> [!NOTE]
> *Os grupos de ação definidos dentro das regras de ação e as regras de alerta funcionam de forma independente, sem deduplicação.* No cenário descrito anteriormente, se um grupo de ação for definido para a regra de alerta, ele desencadeia em conjunto com o grupo de ação definido na regra de ação.

## <a name="managing-your-action-rules"></a>Gerir as suas regras de ação

### <a name="portal"></a>[Portal](#tab/portal)

Pode ver e gerir as suas regras de ação a partir da vista da lista:

![Vista da lista de regras de ação](media/alerts-action-rules/action-rules-list-view.png)

A partir daqui, pode ativar, desativar ou eliminar as regras de ação em escala selecionando a caixa de verificação ao lado. Quando seleciona uma regra de ação, a página de configuração abre-se. A página ajuda-o a atualizar a definição da regra de ação e a capacitá-la ou desativá-la.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode ver e gerir as suas regras de ação utilizando o comando [de regra de ação](/cli/azure/ext/alertsmanagement/monitor) do Azure CLI.

Antes de gerir as regras de ação com o CLI Azure, prepare o seu ambiente utilizando as instruções fornecidas na [configuração de uma regra de ação](#configuring-an-action-rule).

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Melhores práticas

Os alertas de registo que cria com o número de opções [de resultados](./alerts-unified-log.md) geram uma única instância de alerta utilizando todo o resultado da pesquisa (que pode abranger vários computadores). Neste cenário, se uma regra de ação utilizar o filtro **Contexto de Alerta (carga útil),** atua na hora de alerta desde que haja uma correspondência. No Cenário 2, descrito anteriormente, se os resultados de pesquisa do alerta de registo gerado contiverem **tanto o Computador-01** como o **Computador-02,** toda a notificação é suprimida. Não há nenhuma notificação gerada para **o Computador-02.**

![O diagrama mostra as regras de ação e os alertas de registo com uma única instância de alerta realçada.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Para utilizar melhor os alertas de registo com regras de ação, crie alertas de registo com a opção [de medição métrica.](./alerts-unified-log.md) Casos de alerta separados são gerados por esta opção, com base no seu campo de grupo definido. Em seguida, no cenário 2, são geradas instâncias de alerta separadas para **Computador-01** e **Computador-02**. Devido à regra de ação descrita no cenário, apenas a notificação para **Computador-01** é suprimida. A notificação para **Computador-02** continua a disparar normalmente.

![Regras de ação e alertas de registo (número de resultados)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Enquanto estou a configurar uma regra de ação, gostaria de ver todas as regras de ação sobrepostas possíveis, para evitar notificações duplicadas. É possível fazê-lo?

Depois de definir um âmbito à medida que configura uma regra de ação, pode ver uma lista de regras de ação que se sobrepõem no mesmo âmbito (se houver). Esta sobreposição pode ser uma das seguintes opções:

* Uma correspondência exata: Por exemplo, a regra de ação que está a definir e a regra de ação sobreposta está na mesma subscrição.
* Um subconjunto: Por exemplo, a regra de ação que está a definir está numa subscrição, e a regra de ação sobreposta está num grupo de recursos dentro da subscrição.
* Um superconjunto: Por exemplo, a regra de ação que está a definir está num grupo de recursos, e a regra de ação sobreposta está na subscrição que contém o grupo de recursos.
* Uma intersecção: Por exemplo, a regra de ação que está a definir está em **VM1** e **VM2,** e a regra de ação sobreposta está em **VM2** e **VM3**.

![O Screenshot mostra a página New Action Rule com as regras de ação sobrepostas apresentadas nas regras de Ação definidas na mesma janela de âmbito.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Enquanto estou a configurar uma regra de alerta, é possível saber se já existem regras de ação definidas que possam agir na regra de alerta que estou a definir?

Depois de definir o recurso-alvo para a sua regra de alerta, pode ver a lista de regras de ação que atuam no mesmo âmbito (se houver) selecionando **Ver ações configuradas** na secção **Ações.** Esta lista é povoada com base nos seguintes cenários para o âmbito:

* Uma correspondência exata: Por exemplo, a regra de alerta que está a definir e a regra de ação está na mesma subscrição.
* Um subconjunto: Por exemplo, a regra de alerta que está a definir está numa subscrição, e a regra de ação está num grupo de recursos dentro da subscrição.
* Um superconjunto: Por exemplo, a regra de alerta que está a definir está num grupo de recursos, e a regra de ação está na subscrição que contém o grupo de recursos.
* Um cruzamento: Por exemplo, a regra de alerta que está a definir está em **VM1** e **VM2,** e a regra de ação está em **VM2** e **VM3**.

![Regras de ação sobrepostas](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Posso ver os alertas que foram suprimidos por uma regra de ação?

Na página da [lista de alertas,](./alerts-managing-alert-instances.md)pode escolher uma coluna adicional chamada **Estado de Supressão**. Se a notificação de um caso de alerta fosse suprimida, mostraria esse estatuto na lista.

![Instâncias de alerta suprimidas](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se houver uma regra de ação com um grupo de ação e outra com supressão ativa no mesmo âmbito, o que acontece?

A supressão tem sempre precedência no mesmo âmbito.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso que esteja coberto por duas regras de ação? Recebo uma ou duas notificações? Por exemplo, **VM2** no seguinte cenário:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Para cada alerta em VM1 e VM3, o grupo de ação AG1 seria acionado uma vez. Para cada alerta em **VM2,** o grupo de ação AG1 seria acionado duas vezes, porque as regras de ação não desduplicam as ações.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso monitorizado em duas regras de ação separadas e um apelar à ação enquanto outro para a supressão? Por exemplo, **VM2** no seguinte cenário:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Para cada alerta em VM1, o grupo de ação AG1 seria acionado uma vez. As ações e notificações para cada alerta em VM2 e VM3 serão suprimidas.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>O que acontece se eu tiver uma regra de alerta e uma regra de ação definida para o mesmo recurso chamando diferentes grupos de ação? Por exemplo, **VM1** no seguinte cenário:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Para cada alerta em VM1, o grupo de ação AG1 seria acionado uma vez. Sempre que a regra de alerta "regra 1" for acionada, também ativará AG2 adicionalmente. Os grupos de ação definidos dentro das regras de ação e as regras de alerta funcionam de forma independente, sem deduplicação.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre alertas em Azure](./alerts-overview.md)
