---
title: Criar, ver e gerir alertas métricos usando monitor de Azure
description: Saiba como usar o portal Azure ou OCli para criar, visualizar e gerir regras de alerta métricos.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.subservice: alerts
ms.openlocfilehash: 6919040308dcd3a80c3c0b05ec729744099b6d8f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944497"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Criar, ver e gerir alertas de métricas com o Azure Monitor

Os alertas métricos no Azure Monitor fornecem uma forma de ser notificado quando uma das suas métricas cruza um limiar. Os alertas de métricas funcionam numa variedade de métricas de plataforma multidimensionais, métricas personalizadas e métricas padrão e personalizadas do Application Insights. Neste artigo, descreveremos como criar, visualizar e gerir regras de alerta métrico através do portal Azure e do Azure CLI. Também pode criar regras de alerta métricas utilizando modelos do Gestor de Recursos Azure, que são descritos [num artigo separado](alerts-metric-create-templates.md).

Pode aprender mais sobre como os alertas métricos funcionam a partir de [alertas métricos.](alerts-metric-overview.md)

## <a name="create-with-azure-portal"></a>Criar com o portal do Azure

O procedimento que se segue descreve como criar uma regra de alerta métrico no portal Azure:

1. No [portal Azure,](https://portal.azure.com)clique no **Monitor**. A lâmina Monitor consolida todas as suas definições e dados de monitorização numa única vista.

2. Clique **em Alertas** e clique **em + Nova regra de alerta**.

    > [!TIP]
    > A maioria das lâminas de recursos também tem **Alertas** no seu menu de recursos em **Monitorização,** podendo também criar alertas a partir daí.

3. Clique em **Selecionar o alvo,** no painel de contexto que carrega, selecione um recurso-alvo que pretende alertar. Utilize as reduções do tipo de **subscrição** e **do tipo de recurso** para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Se o recurso selecionado tiver métricas, pode criar alertas, os **sinais disponíveis** no canto inferior direito incluirão métricas. Pode ver a lista completa de tipos de recursos suportados para alertas métricos neste [artigo](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Uma vez selecionado um recurso-alvo, clique na **condição de Adicionar**.

6. Verá uma lista de sinais suportados para o recurso, selecione a métrica em que pretende criar um alerta.

7. Verá um gráfico para a métrica nas últimas seis horas. Utilize o período de entrega do **gráfico** para selecionar para ver um histórico mais longo para a métrica.

8. Se a métrica tiver dimensões, verá uma tabela de dimensões apresentada. Selecione um ou mais valores por dimensão.
    - Os valores de dimensão apresentados baseiam-se em dados métricos do último dia.
    - Se o valor de dimensão que procura não for apresentado, clique em "Adicionar valor personalizado" para adicionar um valor de dimensão personalizada.
    - Também pode **selecionar todos os valores atuais e futuros** para qualquer uma das dimensões. Isto irá escalar dinamicamente a seleção para todos os valores atuais e futuros para uma dimensão.

    A regra de alerta métrico avaliará a condição para todas as combinações de valores selecionados. [Saiba mais sobre como funciona o alerta sobre métricas multidimensionais.](alerts-metric-overview.md)

9. Selecione o tipo **Threshold,** **Operador** e Tipo **de Agregação**. Isto determinará a lógica que a regra de alerta métrico irá avaliar.
    - Se estiver a utilizar um limiar **estático,** continue a definir um **valor limiar**. O gráfico métrico pode ajudar a determinar o que pode ser um limiar razoável.
    - Se estiver a utilizar um limiar **Dinâmico,** continue a definir a **sensibilidade Threshold**. O gráfico métrico apresentará os limiares calculados com base em dados recentes. [Saiba mais sobre o tipo de condições e opções de sensibilidade dos Limiares Dinâmicos](alerts-dynamic-thresholds.md).

10. Opcionalmente, refinar a condição ajustando a **granularidade da agregação** e **a frequência de avaliação**. 

11. Clique em **Concluído**.

12. Opcionalmente, adicione outro critério se quiser monitorizar uma regra de alerta complexa. Atualmente, os utilizadores podem ter regras de alerta com critérios de Limiares Dinâmicos como um único critério.

13. Preencha **detalhes de alerta** como nome da regra de **alerta,** **descrição** e **severidade**.

14. Adicione um grupo de ação ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.

15. Clique **em Fazer** para salvar a regra de alerta métrico.

> [!NOTE]
> As regras de alerta métrica criadas através do portal são criadas no mesmo grupo de recursos que o recurso alvo.

## <a name="view-and-manage-with-azure-portal"></a>Ver e gerir com o portal Azure

Pode ver e gerir as regras de alerta métricas utilizando a lâmina 'Gerir regras' em alertas. O procedimento abaixo mostra-lhe como ver as suas regras de alerta métrico e editar uma delas.

1. No portal Azure, navegue para **Monitor**

2. Clique em **Alertas** e **Gerir regras**

3. Na lâmina **de regras 'Gerir',** pode ver todas as suas regras de alerta através de subscrições. Pode filtrar ainda mais as regras utilizando o **grupo de recursos,** **o tipo de recurso** e o **recurso.** Se quiser ver apenas alertas métricos, selecione **o tipo de sinal** como Métricas.

    > [!TIP]
    > Na lâmina **de regras De gestão,** pode selecionar várias regras de alerta e ativar/desativá-las. Isto pode ser útil quando certos recursos-alvo precisam ser colocados sob manutenção

4. Clique no nome da regra de alerta métrico que pretende editar

5. Na Regra de Edição, clique nos **critérios de Alerta** que pretende editar. Pode alterar a métrica, condição limiar e outros campos conforme necessário

    > [!NOTE]
    > Não é possível editar o **recurso Target** e o Nome da Regra **de Alerta** após a criação do alerta métrico.

6. Clique **em Fazer** para guardar as suas edições.


## <a name="with-azure-cli"></a>Com a CLI do Azure

As secções anteriores descreveram como criar, visualizar e gerir regras de alerta métricas usando o portal Azure. Esta secção descreverá como fazer o mesmo utilizando o [Azure CLI](/cli/azure/get-started-with-azure-cli)da plataforma transversal . A forma mais rápida de começar a usar o Azure CLI é através da [Azure Cloud Shell](../../cloud-shell/overview.md). Para este artigo, usaremos a Cloud Shell.

1. Vá ao portal Azure, clique em **Cloud Shell**.

2. No momento, pode utilizar comandos com ``--help`` opção de saber mais sobre o comando e como usá-lo. Por exemplo, o seguinte comando mostra-lhe a lista de comandos disponíveis para criar, visualizar e gerir alertas métricos

    ```azurecli
    az monitor metrics alert --help
    ```

3. Pode criar uma regra de alerta métrica simples que monitoriza se a média de CPU percentual num VM for superior a 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Pode ver todos os alertas métricos num grupo de recursos utilizando o seguinte comando

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Pode ver os detalhes de uma determinada regra de alerta métrico usando o nome ou o ID de recurso da regra.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Pode desativar uma regra de alerta métrica utilizando o seguinte comando.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Pode eliminar uma regra de alerta métrica utilizando o seguinte comando.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="with-powershell"></a>Com o PowerShell

As regras de alerta métrico têm cmdlets powerShell dedicados disponíveis:

- [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2): Criar uma nova regra de alerta métrico ou atualizar uma existente.
- [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2): Obtenha uma ou mais regras de alerta métricas.
- [Remover-AzMetricAlertRuleV2](/powershell/module/az.monitor/remove-azmetricalertrulev2): Eliminar uma regra de alerta métrico.

## <a name="with-rest-api"></a>Com a API REST

- [Criar ou atualizar:](/rest/api/monitor/metricalerts/createorupdate)Criar uma nova regra de alerta métrico ou atualizar uma existente.
- [Obter:](/rest/api/monitor/metricalerts/get)Obtenha uma regra específica de alerta métrico.
- [Lista por Grupo de Recursos](/rest/api/monitor/metricalerts/listbyresourcegroup): Obtenha uma lista de regras de alerta métrico num grupo de recursos específico.
- [Lista por subscrição](/rest/api/monitor/metricalerts/listbysubscription): Obtenha uma lista de regras de alerta métrico numa subscrição específica.
- [Atualização](/rest/api/monitor/metricalerts/update): Atualizar uma regra de alerta métrico.
- [Excluir](/rest/api/monitor/metricalerts/delete): Elimine uma regra de alerta métrico.

## <a name="next-steps"></a>Próximos passos

- [Criar alertas métricos usando modelos de gestor de recursos Azure](./alerts-metric-create-templates.md)
- [Entenda como os alertas métricos funcionam](alerts-metric-overview.md)
- [Entenda como os alertas métricos com limiares dinâmicos condicionam o funcionamento](alerts-dynamic-thresholds.md)
- [Compreenda o esquema do gancho web para alertas métricos](./alerts-metric-near-real-time.md#payload-schema)
- [Problemas de resolução de problemas em alertas métricos](alerts-troubleshoot-metric.md)
