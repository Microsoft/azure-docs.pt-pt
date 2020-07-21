---
title: Alertas métricos Azure de resolução de problemas
description: Problemas comuns com alertas métricos do Azure Monitor e possíveis soluções.
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 07/15/2020
ms.subservice: alerts
ms.openlocfilehash: 0d569facb6c2b58222980cfa1488de3b1f5fb60f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515772"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Problemas de resolução de problemas nos alertas métricos do Monitor Azure 

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

3. **Já ativo** - Verifique se já existe um alerta de incêndio nas séries de tempo métricas para as qual se espera receber um alerta. Os alertas métricos são imponentes, o que significa que uma vez que um alerta é disparado em uma série de tempo métrica específica, os alertas adicionais sobre essa série de tempo não são disparados até que o problema não seja mais observado. Esta escolha de design reduz o ruído. O alerta é resolvido automaticamente quando a condição de alerta não é cumprida para três avaliações consecutivas.

4. **Dimensões utilizadas** - Se selecionou alguns [valores de dimensão para uma métrica,](./alerts-metric-overview.md#using-dimensions)a regra de alerta monitoriza cada série de tempo métrica individual (tal como definida pela combinação de valores de dimensão) para uma rutura de limiar. Para também monitorizar as séries de tempo métricas agregadas (sem quaisquer dimensões selecionadas), configuure uma regra de alerta adicional na métrica sem selecionar dimensões.

5. **Agregação e granularidade do tempo** - Se estiver a visualizar a métrica utilizando [gráficos de métricas,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)certifique-se de que:
    * A **agregação** selecionada no gráfico métrico é o mesmo que o **tipo de agregação** na sua regra de alerta
    * A **granularidade do tempo** selecionada é a mesma que a **granularidade de agregação (período)** na sua regra de alerta (e não definida como 'Automática')

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Alerta métrico disparado quando não deveria ter

Se acredita que o seu alerta métrico não devia ter disparado, mas disparou, os seguintes passos podem ajudar a resolver o problema.

1. Reveja a [lista de alertas disparados](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) para localizar o alerta disparado e clique para ver os seus detalhes. Reveja as informações fornecidas no âmbito do **Metric Value**porquê deste **Threshold value** **alerta disparar?**

    > [!NOTE] 
    > Se estiver a utilizar o tipo de condição Limiares Dinâmicos e considerar que os limiares utilizados não estavam corretos, envie-nos comentários através do ícone de comentários negativos. Este feedback terá impacto na pesquisa algorítmica de aprendizagem automática e ajudará a melhorar as deteções futuras.

2. Se tiver selecionado valores de múltipla dimensão para uma métrica, o alerta será acionado quando **qualquer** uma das séries de tempo métricas (tal como definidas pela combinação de valores de dimensão) violar o limiar. Para obter mais informações sobre como utilizar dimensões em alertas de métricas, veja [aqui](./alerts-metric-overview.md#using-dimensions).

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

Para alertar as métricas do sistema operativo dos hóspedes de máquinas virtuais (por exemplo, memória, espaço em disco), certifique-se de que instalou o agente necessário para recolher estes dados para as Métricas do Monitor Azure:
- [Para VMs do Windows](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [Para VMs do Linux](./collect-custom-metrics-linux-telegraf.md)

Para obter mais informações sobre a recolha de dados do sistema operativo dos hóspedes de uma máquina virtual, consulte [aqui.](../insights/monitor-vm-azure.md#guest-operating-system)
    
> [!NOTE] 
> Se configurar as métricas dos hóspedes para serem enviadas para um espaço de trabalho log Analytics, as métricas aparecem sob o recurso do espaço de trabalho Log Analytics e começarão a mostrar dados **apenas** depois de criar uma regra de alerta que os monitoriza. Para o fazer, siga os passos para [configurar um alerta de métricas para os registos](./alerts-metric-logs.md#configuring-metric-alert-for-logs).

## <a name="cant-find-the-metric-to-alert-on"></a>Não consigo encontrar a métrica para alertar

Se procura alertar uma métrica específica, mas não consegue ver métricas para o recurso, [verifique se o tipo de recurso é suportado para alertas métricos](./alerts-metric-near-real-time.md).
Se consegue ver algumas métricas para o recurso mas não consegue encontrar uma métrica específica, [verifique se essa métrica está disponível](./metrics-supported.md)– e em caso afirmativo, consulte a descrição métrica para ver se só está disponível em versões ou edições específicas do recurso.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Não consigo encontrar a dimensão métrica para alertar

Se procura alertar para [valores de dimensão específica de uma métrica,](./alerts-metric-overview.md#using-dimensions)mas não consegue encontrar estes valores, note o seguinte:

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


## <a name="metric-alert-rules-quota-too-small"></a>Quota de regras de alerta métrico muito pequena

O número permitido de regras de alerta métrico por subscrição está sujeito a [limites de quota.](../service-limits.md)

Se tiver atingido o limite de quota, os passos seguintes podem ajudar a resolver o problema:
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
6. O número total de regras de alerta métrico são apresentados acima da lista de regras

### <a name="from-api"></a>Na API

- PowerShell – [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- API REST – [Listar por subscrição](/rest/api/monitor/metricalerts/listbysubscription)
- CLI do Azure – [Lista de alertas de métricas do Azure Monitor](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Gerir regras de alerta usando modelos de Gestor de Recursos, API DE REST, PowerShell ou Azure CLI

Se estiver a detear problemas na criação, atualização, recuperação ou eliminação de alertas métricos utilizando modelos de Gestor de Recursos, API DE REST, PowerShell ou a interface de linha de comando Azure (CLI), os seguintes passos podem ajudar a resolver o problema.

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

- Rever a lista de [erros comuns de implementação do Azure](../../azure-resource-manager/templates/common-deployment-errors.md) e resolver os problemas em conformidade
- Consulte os [alertas métricos Exemplos do modelo do Gestor de Recursos Azure](./alerts-metric-create-templates.md) para garantir que está a passar corretamente todos os parâmetros

### <a name="rest-api"></a>API REST

Reveja o [Guia da API REST](/rest/api/monitor/metricalerts/) para garantir que está a transmitir todos os parâmetros corretamente

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

- Se estiver a receber um erro `Metric not found`:

   - Para uma métrica da plataforma: Certifique-se de que está a usar o nome **métrico** a partir da [página de métricas suportadas pelo Monitor Azure](./metrics-supported.md), e não o **Nome de Visualização Métrica**

   - Para uma métrica personalizada: Certifique-se de que a métrica já está a ser emitida (não pode criar uma regra de alerta sobre uma métrica personalizada que ainda não existe), e que está a fornecer o espaço de nome da métrica personalizada (ver um exemplo de modelo ARM [aqui)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)

- Se estiver a criar [alertas de métrica em registos](./alerts-metric-logs.md), confirme que as dependências apropriadas estão incluídas. Veja o [modelo de exemplo](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Se estiver a criar uma regra de alerta que contenha vários critérios, observe os seguintes constrangimentos:

   - Só pode selecionar um valor por dimensão dentro de cada critério
   - Não pode utilizar “\*” como valor de dimensão
   - Quando as métricas configuradas em critérios diferentes suportam a mesma dimensão, então um valor de dimensão configurado deve ser explicitamente definido da mesma forma para todas essas métricas (ver um exemplo de modelo do Gestor de Recursos [aqui)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Sem permissões para criar regras de alerta métrico

Para criar uma regra de alerta métrico, terá de ter as seguintes permissões:

- Leia a permissão sobre o recurso alvo da regra de alerta
- Escreva a permissão no grupo de recursos em que a regra de alerta é criada (se estiver a criar a regra de alerta a partir do portal Azure, a regra de alerta é criada no mesmo grupo de recursos em que reside o recurso-alvo)
- Leia a permissão em qualquer grupo de ação associado à regra de alerta (se aplicável)


## <a name="naming-restrictions-for-metric-alert-rules"></a>Restrições de nomeação para regras de alerta métrico

Por favor, note as seguintes restrições para nomes de regras de alerta métrico:

- Os nomes das regras de alerta métrico não podem ser alterados (renomeado) uma vez criados
- Os nomes das regras de alerta métrico devem ser únicos dentro de um grupo de recursos
- Os nomes das regras de alerta métrico não podem conter os seguintes caracteres: * # & + : < > ? @ % { } \ / 
- Os nomes das regras de alerta métrico não podem terminar com o seguinte carácter: .


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>Restrições ao utilizar dimensões numa regra de alerta métrico com múltiplas condições

Os alertas métricos alertam para métricas multidimensionais, bem como suporte que defina múltiplas condições (até 5 condições por regra de alerta).

Por favor, note os seguintes constrangimentos ao utilizar dimensões numa regra de alerta que contém múltiplas condições:
1. Só é possível selecionar um valor por dimensão dentro de cada condição.
2. Não é possível utilizar a opção para "Selecionar todos os valores atuais e futuros" (Selecione). \*
3. Quando as métricas configuradas em diferentes condições suportam a mesma dimensão, então um valor de dimensão configurado deve ser explicitamente definido da mesma forma para todas essas métricas (nas condições relevantes).
Por exemplo:
    - Considere uma regra de alerta métrico que é definida numa conta de armazenamento e monitoriza duas condições:
        * Total de **Transações** > 5
        * **Sucesso MédioE2EAancy** > 250 ms
    - Eu gostaria de atualizar a primeira condição, e apenas monitorizar transações onde a dimensão **ApiName** é igual *a "GetBlob"*
    - Como tanto as **métricas de Transações** como **de SucessoE2ELatency** suportam uma dimensão **ApiName,** vou precisar de atualizar ambas as condições, e ter ambas especificar a dimensão **ApiName** com um valor *"GetBlob".*


## <a name="next-steps"></a>Próximos passos

- Para obter informações gerais sobre alertas e notificações, consulte [problemas de resolução de problemas nos alertas do Monitor Azure](alerts-troubleshoot.md).
