---
title: Alertas métricos Azure de resolução de problemas
description: Problemas comuns com alertas métricos do Azure Monitor e possíveis soluções.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 11/25/2020
ms.subservice: alerts
ms.openlocfilehash: fc54d2ba3ca4e7a150a1602c671b99f58197bc44
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657299"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Resolver problemas relacionados com os alertas de métricas do Azure Monitor 

Este artigo discute problemas comuns nos [alertas métricos](alerts-metric-overview.md) do Azure Monitor e como resolvê-los.

Os alertas do Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas antes que os utilizadores do seu sistema os notem. Para obter mais informações sobre o alerta, consulte [a visão geral dos alertas no Microsoft Azure](alerts-overview.md).

## <a name="metric-alert-should-have-fired-but-didnt"></a>Alerta métrico devia ter disparado, mas não disparou 

Se acredita que um alerta métrico deveria ter disparado, mas não disparou e não foi encontrado no portal Azure, experimente os seguintes passos:

1. **Configuração** - Reveja a configuração da regra de alerta métrico para se certificar de que está corretamente configurada:
    - Verifique se o **tipo de agregação,** **granularidade agregação (período)** e valor ou **sensibilidade** **do limiar** estão configurados como esperado
    - Para uma regra de alerta que utilize limiares dinâmicos, verifique se as definições avançadas estão configuradas, pois **o número de violações** pode filtrar alertas e **ignorar dados antes** de poder afetar a forma como os limiares são calculados

       > [!NOTE] 
       > Os limiares dinâmicos requerem pelo menos 3 dias e 30 amostras métricas antes de se tornarem ativas.

2. **Disparado mas sem notificação** - Reveja a [lista de alertas disparados](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) para ver se consegue localizar o alerta de disparo. Se você pode ver o alerta na lista, mas tem um problema com algumas das suas ações ou notificações, consulte mais informações [aqui](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected).

3. **Já ativo** - Verifique se já existe um alerta de incêndio nas séries de tempo métricas para as qual se espera receber um alerta. Os alertas de métrica são monitorizados, o que significa que quando um alerta é acionado numa série temporal de métrica específica, os alertas adicionais nessa série temporal não serão acionados até que o problema já não se verifique. Esta escolha de design reduz o ruído. O alerta é resolvido automaticamente quando a condição de alerta não é cumprida para três avaliações consecutivas.

4. **Dimensões utilizadas** - Se selecionou alguns [valores de dimensão para uma métrica,](./alerts-metric-overview.md#using-dimensions)a regra de alerta monitoriza cada série de tempo métrica individual (tal como definida pela combinação de valores de dimensão) para uma violação do limiar. Para também monitorizar as séries de tempo métricas agregadas (sem quaisquer dimensões selecionadas), configuure uma regra de alerta adicional na métrica sem selecionar dimensões.

5. **Agregação e granularidade do tempo** - Se estiver a visualizar a métrica utilizando [gráficos de métricas,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)certifique-se de que:
    * A **agregação** selecionada no gráfico métrico é o mesmo que o **tipo de agregação** na sua regra de alerta
    * A **granularidade do tempo** selecionada é a mesma que a **granularidade de agregação (período)** na sua regra de alerta (e não definida como 'Automática')

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Alerta métrico disparado quando não deveria ter

Se acredita que o seu alerta métrico não devia ter disparado, mas disparou, os seguintes passos podem ajudar a resolver o problema.

1. Reveja a [lista de alertas disparados](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) para localizar o alerta disparado e clique para ver os seus detalhes. Reveja as informações fornecidas no âmbito do porquê deste  **alerta disparar?**

    > [!NOTE] 
    > Se estiver a utilizar um tipo de condição Dynamic Thresholds e achar que os limiares utilizados não estavam corretos, por favor forneça feedback utilizando o ícone de franzir a testa. Este feedback terá impacto na pesquisa algorítmica de aprendizagem automática e ajudará a melhorar as deteções futuras.

2. Se selecionou vários valores de dimensão para uma métrica, o alerta será acionado quando **qualquer** uma das séries de tempo métricas (tal como definidas pela combinação de valores de dimensão) violar o limiar. Para obter mais informações sobre como utilizar dimensões em alertas de métricas, veja [aqui](./alerts-metric-overview.md#using-dimensions).

3. Reveja a configuração da regra de alerta para se certificar de que está corretamente configurada:
    - Verifique se o **tipo de agregação,** **granularidade agregação (período)** e valor ou **sensibilidade** **do limiar** estão configurados como esperado
    - Para uma regra de alerta que utilize limiares dinâmicos, verifique se as definições avançadas estão configuradas, pois **o número de violações** pode filtrar alertas e **ignorar dados antes** de poder afetar a forma como os limiares são calculados

   > [!NOTE]
   > Os limiares dinâmicos requerem pelo menos 3 dias e 30 amostras métricas antes de se tornarem ativas.

4. Se estiver a visualizar a métrica utilizando o [gráfico métrico,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)certifique-se de que:
    - A **agregação** selecionada no gráfico métrico é o mesmo que o **tipo de agregação** na sua regra de alerta
    - A **granularidade do tempo** selecionada é a mesma que a **granularidade de agregação (período)** na sua regra de alerta (e não definida como 'Automática')

5. Se o alerta disparado enquanto já existem alertas disparados que monitorizam os mesmos critérios (que não estão resolvidos), verifique se a regra de alerta foi configurada com a propriedade *autoMitigar* definida como **falsa** (esta propriedade só pode ser configurada via REST/PowerShell/CLI, por isso verifique o script utilizado para implementar a regra de alerta). Neste caso, a regra de alerta não abre alertas e não requer que um alerta de incêndio seja resolvido antes de voltar a disparar.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Não consigo encontrar a métrica para alertar - máquinas virtuais métricas de hóspedes

Para alertar as métricas do sistema operativo dos hóspedes de máquinas virtuais (por exemplo: memória, espaço em disco), certifique-se de que instalou o agente necessário para recolher estes dados para as Métricas do Monitor Azure:
- [Para VMs do Windows](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [Para VMs do Linux](./collect-custom-metrics-linux-telegraf.md)

Para obter mais informações sobre a recolha de dados do sistema operativo dos hóspedes de uma máquina virtual, consulte [aqui.](../insights/monitor-vm-azure.md#guest-operating-system)

> [!NOTE] 
> Se configurar as métricas dos hóspedes para serem enviadas para um espaço de trabalho log Analytics, as métricas aparecem sob o recurso do espaço de trabalho Log Analytics e começarão a mostrar dados **apenas** depois de criar uma regra de alerta que os monitoriza. Para o fazer, siga os passos para [configurar um alerta de métricas para os registos](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

> [!NOTE] 
> Monitorizar uma métrica de hóspedes para várias máquinas virtuais com uma única regra de alerta não é suportado por alertas métricos atualmente. Pode conseguir isso com uma [regra de alerta de registo.](./alerts-unified-log.md) Para tal, certifique-se de que as métricas dos hóspedes são recolhidas num espaço de trabalho do Log Analytics e crie uma regra de alerta de registo no espaço de trabalho.

## <a name="cant-find-the-metric-to-alert-on"></a>Não consigo encontrar a métrica para alertar

Se quiser receber um alerta sobre uma métrica específica, mas não o vir ao criar uma regra de alerta, verifique o seguinte:
- Se não conseguir ver quaisquer métricas para o recurso, [verifique se o tipo de recurso é suportado para alertas de métricas](./alerts-metric-near-real-time.md).
- Se conseguir ver algumas métricas para o recurso, mas não conseguir encontrar uma métrica específica, [verifique se essa métrica está disponível](./metrics-supported.md) e, em caso afirmativo, consulte a descrição da métrica para verificar se está disponível apenas em versões ou edições específicas do recurso.
- Se a métrica não estiver disponível para o recurso, poderá estar disponível nos registos de recursos e ser monitorizada através de alertas de registos. Veja aqui para obter mais informações sobre como [recolher e analisar registos de recursos de um recurso do Azure](../learn/tutorial-resource-logs.md).

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Não consigo encontrar a dimensão métrica para alertar

Se pretende alertar para [valores de dimensão específicas de uma métrica,](./alerts-metric-overview.md#using-dimensions)mas não conseguir encontrar estes valores, note o seguinte:

1. Pode demorar alguns minutos até os valores de dimensão aparecerem na lista de **Valores de dimensão**
1. Os valores de dimensão apresentados baseiam-se nos dados de métricas recolhidos nos últimos três dias
1. Se o valor da dimensão ainda não for emitido, clique no sinal '+' para adicionar um valor personalizado
1. Se quiser alertar sobre todos os valores possíveis de uma dimensão (incluindo valores futuros), consulte a caixa de verificação 'Selecione *'

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>Regras de alerta métricas ainda definidas num recurso eliminado 

Ao eliminar um recurso do Azure, as regras de alerta da métrica associadas não são eliminadas automaticamente. Para eliminar as regras de alerta associadas a um recurso que foi eliminado:

1. Abra o grupo de recursos em que o recurso eliminado foi definido
1. Na lista que apresenta os recursos, selecione a caixa de verificação **Mostrar tipos ocultos**
1. Filtre a lista por Tipo == **microsoft.insights/metricalerts**
1. Selecione as regras de alerta relevantes e **selecione Eliminar**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Faça alertas métricos ocorrerem cada vez que a minha condição é satisfeita

Os alertas métricos são declarados por defeito, e por isso os alertas adicionais não são disparados se já houver um alerta de disparo numa determinada série de tempo. Se desejar fazer uma regra de alerta métrico específico apátrida, e ser alertado sobre todas as avaliações em que a condição de alerta é cumprida, crie a regra de alerta programáticamente (por exemplo, através [do Gestor de Recursos,](./alerts-metric-create-templates.md) [PowerShell,](/powershell/module/az.monitor/?view=azps-3.6.1) [REST,](/rest/api/monitor/metricalerts/createorupdate) [CLI),](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)e dedeta a propriedade *autoMitidificada* para 'Falso'.

> [!NOTE] 
> Fazer uma regra de alerta métrico apátrida impede que os alertas disparados fiquem resolvidos, por isso mesmo depois de a condição já não ser cumprida, os alertas de incêndio permanecerão em estado de incêndio até ao período de retenção de 30 dias.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Defina uma regra de alerta sobre uma métrica personalizada que ainda não é emitida

Ao criar uma regra de alerta métrico, o nome métrico é validado contra a [API de Definições Métricas](/rest/api/monitor/metricdefinitions/list) para se certificar de que existe. Em alguns casos, gostaria de criar uma regra de alerta sobre uma métrica personalizada mesmo antes de ser emitida. Por exemplo, ao criar (usando um modelo de Gestor de Recursos) um recurso Application Insights que emitirá uma métrica personalizada, juntamente com uma regra de alerta que monitoriza essa métrica.

Para evitar que a implementação falhe ao tentar validar as definições da métrica personalizada, pode utilizar o parâmetro *skipMetricValidation* na secção de critérios da regra de alerta, o que fará com que a validação métrica seja ignorada. Veja o exemplo abaixo para saber como usar este parâmetro num modelo de Gestor de Recursos. Para obter mais informações, consulte as [amostras completas do modelo do Gestor de Recursos para criar regras de alerta métricos](./alerts-metric-create-templates.md).

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Exportar o modelo do Gestor de Recursos Azure de uma regra de alerta métrico através do portal Azure

Exportar o modelo de Gestor de Recursos de uma regra de alerta métrico ajuda-o a compreender a sintaxe e propriedades JSON, e pode ser usado para automatizar futuras implementações.
1. Navegue na secção **Grupos de Recursos** no portal e selecione o grupo de recursos que contém a regra.
2. Na secção 'Visão Geral', verifique a caixa de verificação **dos tipos escondidos do Show.**
3. No filtro **Tipo,** selecione *microsoft.insights/metricalerts*.
4. Selecione a regra de alerta relevante para ver os seus detalhes.
5. Em **Definições**, selecione **Modelo de exportação**.

## <a name="metric-alert-rules-quota-too-small"></a>Quota de regras de alerta métrico muito pequena

O número permitido de regras de alerta métrico por subscrição está sujeito a [limites de quota.](../service-limits.md)

Se atingiu o limite de quota, os seguintes passos podem ajudar a resolver o problema:
1. Tente eliminar ou desativar regras de alerta métricas que já não são usadas.

2. Mude para utilizar regras de alerta de métricas que monitorizem vários recursos. Com esta capacidade, uma regra de alerta único pode monitorizar vários recursos usando apenas uma regra de alerta contada contra a quota. Para obter mais informações sobre esta capacidade e os tipos de recursos suportados, veja [aqui](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

3. Se necessitar de um aumento do limite de quota, abra um pedido de apoio e forneça as seguintes informações:

    - Id ou id de assinatura para os quais o limite de quota deve ser aumentado
    - Tipo de recurso para o aumento da quota: **Alertas métricos** ou **alertas métricos (Clássico)**
    - Limite de quota solicitado

## <a name="check-total-number-of-metric-alert-rules"></a>Verifique o número total de regras de alerta métrico

Para verificar a utilização atual das regras de alerta métrico, siga os passos abaixo.

### <a name="from-the-azure-portal"></a>No portal do Azure

1. Abra o ecrã **Alertas** e clique em **Gerir regras de alerta**
2. Filtrar para a subscrição relevante, utilizando o controlo de entrega de **assinatura**
3. Certifique-se de não filtrar para um grupo de recursos específico, tipo de recurso ou recurso
4. No controlo de dropdown **do tipo Sinal,** selecione **Métricas**
5. Verifique se o controlo de retirada de **estado** está definido para **Ativado**
6. O número total de regras de alerta métrico é apresentado acima da lista de regras de alerta

### <a name="from-api"></a>Na API

- PowerShell – [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- API REST – [Listar por subscrição](/rest/api/monitor/metricalerts/listbysubscription)
- CLI do Azure – [Lista de alertas de métricas do Azure Monitor](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Gerir regras de alerta usando modelos de Gestor de Recursos, API DE REST, PowerShell ou Azure CLI

Se estiver a detetar problemas a criar, atualizar, recuperar ou eliminar alertas métricos utilizando modelos de Gestor de Recursos, API DE REST, PowerShell ou a interface de linha de comando Azure (CLI), os seguintes passos podem ajudar a resolver o problema.

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

- Rever a lista de [erros comuns de implementação do Azure](../../azure-resource-manager/templates/common-deployment-errors.md) e resolver os problemas em conformidade
- Consulte os [alertas métricos Exemplos do modelo do Gestor de Recursos Azure](./alerts-metric-create-templates.md) para garantir que está a passar corretamente todos os parâmetros

### <a name="rest-api"></a>API REST

Reveja o [guia da API](/rest/api/monitor/metricalerts/) REST para verificar se está a passar os parâmetros corretamente

### <a name="powershell"></a>PowerShell

Certifique-se de que está a utilizar os cmdlets PowerShell certos para alertas métricos:

- Os cmdlets do PowerShell para alertas de métricas estão disponíveis no [módulo Az.Monitor](/powershell/module/az.monitor/?view=azps-3.6.1)
- Certifique-se de que utiliza os cmdlets que terminam com 'V2' para novos alertas métricos (não clássicos) (por exemplo, [Add-AzMetricAlertRuleV2)](/powershell/module/az.monitor/add-azmetricalertrulev2?view=azps-3.6.1)

### <a name="azure-cli"></a>CLI do Azure

Certifique-se de que está a utilizar os comandos CLI certos para alertas métricos:

- Os comandos da CLI para alertas de métricas começam por `az monitor metrics alert`. Reveja a [Referência da CLI do Azure](/cli/azure/monitor/metrics/alert?view=azure-cli-latest) para saber mais sobre a sintaxe.
- Pode ver o [exemplo que mostra como utilizar a CLI de alertas de métricas](./alerts-metric.md#with-azure-cli)
- Para alertar numa métrica personalizada, coloque um prefixo no nome da métrica com o espaço de nomes da métrica relevante: NAMESPACE.METRIC

### <a name="general"></a>Geral

- Se estiver a receber um `Metric not found` erro:

   - Para uma métrica da plataforma: Certifique-se de que está a usar o nome **métrico** a partir da [página de métricas suportadas pelo Monitor Azure](./metrics-supported.md), e não o **Nome de Visualização Métrica**

   - Para uma métrica personalizada: Certifique-se de que a métrica já está a ser emitida (não é possível criar uma regra de alerta sobre uma métrica personalizada que ainda não existe), e que está a fornecer o espaço de nome da métrica personalizada (consulte [aqui](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)um exemplo de modelo do Gestor de Recursos)

- Se estiver a criar [alertas métricos nos registos,](./alerts-metric-logs.md)certifique-se de que as dependências adequadas estão incluídas. Veja o [modelo de exemplo](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Se estiver a criar uma regra de alerta que contenha vários critérios, observe os seguintes constrangimentos:

   - Só pode selecionar um valor por dimensão dentro de cada critério
   - Não pode utilizar “\*” como valor de dimensão
   - Quando as métricas configuradas em critérios diferentes suportam a mesma dimensão, então um valor de dimensão configurado deve ser explicitamente definido da mesma forma para todas essas métricas (ver um exemplo de modelo do Gestor de Recursos [aqui)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Sem permissões para criar regras de alerta métrico

Para criar uma regra de alerta métrico, terá de ter as seguintes permissões:

- Leia a permissão sobre o recurso alvo da regra de alerta
- Escreva a permissão no grupo de recursos em que a regra de alerta é criada (se estiver a criar a regra de alerta a partir do portal Azure, a regra de alerta é criada por padrão no mesmo grupo de recursos em que reside o recurso-alvo)
- Leia a permissão em qualquer grupo de ação associado à regra de alerta (se aplicável)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Restrições de nomeação para regras de alerta métrico

Considere as seguintes restrições para nomes de regras de alerta métrico:

- Os nomes das regras de alerta métrico não podem ser alterados (renomeado) uma vez criados
- Os nomes das regras de alerta métrico devem ser únicos dentro de um grupo de recursos
- Os nomes das regras de alerta métrico não podem conter os seguintes caracteres: * # & + : < > ? @ % { } \ / 
- Os nomes das regras de alerta métrico não podem terminar com um espaço ou um período

> [!NOTE] 
> Se o nome da regra de alerta contiver caracteres que não são alfabéticos ou numéricos (por exemplo: espaços, marcas de pontuação ou símbolos), estes caracteres podem estar codificados por URL quando recuperados por determinados clientes.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Restrições ao utilizar dimensões numa regra de alerta métrico com múltiplas condições

Os alertas métricos alertam para métricas multidimensionais, bem como suporte que defina múltiplas condições (até 5 condições por regra de alerta).

Considere os seguintes constrangimentos ao utilizar dimensões numa regra de alerta que contenha múltiplas condições:
- Só é possível selecionar um valor por dimensão dentro de cada condição.
- Não é possível utilizar a opção para "Selecionar todos os valores atuais e futuros" (Selecione). \*
- Quando as métricas configuradas em diferentes condições suportam a mesma dimensão, então um valor de dimensão configurado deve ser explicitamente definido da mesma forma para todas essas métricas (nas condições relevantes).
Por exemplo:
    - Considere uma regra de alerta métrico que é definida numa conta de armazenamento e monitoriza duas condições:
        * Total de **Transações** > 5
        * **Sucesso MédioE2EAancy** > 250 ms
    - Eu gostaria de atualizar a primeira condição, e apenas monitorizar transações onde a dimensão **ApiName** é igual *a "GetBlob"*
    - Como tanto as **métricas de Transações** como **de SucessoE2ELatency** suportam uma dimensão **ApiName,** vou precisar de atualizar ambas as condições, e ter ambas especificar a dimensão **ApiName** com um valor *"GetBlob".*

## <a name="setting-the-alert-rules-period-and-frequency"></a>Definição do período e frequência da regra de alerta

Recomendamos a escolha de uma *granularidade agregada (Período)* maior do que a *Frequência de avaliação,* para reduzir a probabilidade de faltar à primeira avaliação de séries temporais adicionadas nos seguintes casos:
-   Regra de alerta métrico que monitoriza múltiplas dimensões – Quando uma nova combinação de valor de dimensão é adicionada
-   Regra de alerta métrico que monitoriza múltiplos recursos – Quando um novo recurso é adicionado ao âmbito
-   Regra de alerta métrico que monitoriza uma métrica que não é emitida continuamente (métrica escassa) – Quando a métrica é emitida após um período superior a 24 horas em que não foi emitida

## <a name="next-steps"></a>Passos seguintes

- Para obter informações gerais sobre alertas e notificações, consulte [problemas de resolução de problemas nos alertas do Monitor Azure](alerts-troubleshoot.md).
