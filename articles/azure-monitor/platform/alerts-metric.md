---
title: Criar, ver e gerir a métrica alertas utilizando o Azure Monitor
description: Saiba como utilizar o portal do Azure ou a CLI para criar, ver e gerir regras de alerta de métrica.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 117b65265c853194e93a97fe5e2b2dcc6e9f5bc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60712994"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Criar, ver e gerir alertas de métricas através do Azure Monitor

Alertas de métricas no Azure Monitor oferecem uma forma de ser notificado quando uma das suas métricas ultrapassam um determinado limiar. Alertas de métricas de trabalho numa variedade de métricas multidimensionais plataforma, métricas personalizadas, padrão do Application Insights e métricas personalizadas. Neste artigo, descreveremos como criar, ver e gerir regras de alerta de métrica, através do portal do Azure e CLI do Azure. Também pode criar regras de alerta de métrica com modelos do Azure Resource Manager que está descrito [um artigo separado](alerts-metric-create-templates.md).

Pode saber mais sobre métricas como funcionam os alertas do [descrição geral dos alertas de métrica](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Criar com o portal do Azure

O procedimento seguinte descreve como criar uma regra de alerta de métrica no portal do Azure:

1. Na [portal do Azure](https://portal.azure.com), clique em **Monitor**. Painel Monitor consolida todas as suas monitorização definições e dados numa vista.

2. Clique em **alertas** , em seguida, clique em **+ nova regra de alerta**.

    > [!TIP]
    > A maioria dos painéis de recursos também tem **alertas** no respetivo menu de recursos sob **monitorização**, poderia criar alertas a partir do mesmo.

3. Clique em **selecionar destino**, no painel de contexto que carrega, selecione um recurso de destino que quer receber o alerta. Uso **subscrição** e **tipo de recurso** listas pendentes para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Se o recurso selecionado tem métricas, pode criar alertas, **sinais disponíveis** na parte inferior direita irá incluir as métricas. Pode ver a lista completa dos tipos de recurso suportados para alertas de métricas desta [artigo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Assim que tiver selecionado um recurso de destino, clique em **adicionar condição**.

6. Irá ver uma lista de suportadas para o recurso de sinais, selecione a métrica que pretende criar um alerta.

7. Opcionalmente, refinar a métrica ao ajustar **período** e **agregação**. Se a métrica tem dimensões, verá **dimensões** tabela apresentada. Selecione um ou mais valores por dimensão. Alerta de métrica será executado avaliar a condição para todas as combinações de valores selecionados. [Saiba mais sobre como funciona o alertas em métricas multidimensionais](alerts-metric-overview.md). Também pode **selecionar \***  para qualquer uma das dimensões. **Selecione \***  serão dinamicamente escala que a seleção para todos os valores atuais e futuras para uma dimensão.

8. Verá um gráfico para a métrica para as últimas 6 horas. Definir os parâmetros de alerta; **Tipo de condição**, **frequência**, **operador** e **limiar** ou **sensibilidade**, esta irá Determine a lógica que irão avaliar a regra de alerta de métrica. [Saiba mais sobre opções de tipo e a sensibilidade de condição de limiares dinâmicos](alerts-dynamic-thresholds.md).

9. Se estiver a utilizar um limiar estático, o gráfico de métricas pode ajudar a determinar o que pode ser um limiar razoável. Se estiver a utilizar um limiares dinâmicos, o gráfico de métricas irá apresentar os limiares calculados com base nos dados recentes.

10. Clique em **Concluído**

11. Opcionalmente, adicione critérios outro se pretender monitorizar uma regra de alerta complexa. Atualmente os usuários podem ter regras de alerta com os critérios de limiares dinâmicos como um único critério.

12. Preencha **detalhes do alerta** como **nome da regra de alerta**, **Descrição** e **gravidade**

13. Adicione um grupo de ação para o alerta ao selecionar um grupo de ação existente ou criar um novo grupo de ação.

14. Clique em **feito** para guardar a regra de alerta de métrica.

> [!NOTE]
> Alerta de métrica regras criadas através do portal são criadas no mesmo grupo de recursos como o recurso de destino.

## <a name="view-and-manage-with-azure-portal"></a>Ver e gerir com o portal do Azure

Pode ver e gerir regras de alerta de métrica utilizando o painel de gerir regras em alertas. O procedimento a seguir mostra como ver as regras de alerta de métrica e editar uma delas.

1. No portal do Azure, navegue para **Monitor**

2. Clique em **alertas** e **gerir regras**

3. Na **gerir regras** painel, pode ver todas as regras de alerta em várias subscrições. Pode filtrar ainda mais as regras de usando **grupo de recursos**, **tipo de recurso** e **recurso**. Se quiser ver apenas os alertas de métricas, selecione **sinalizar tipo** como métricas.

    > [!TIP]
    > Na **gerir regras** painel, pode selecionar várias regras de alerta e ativar/desativá-los. Isso pode ser útil quando determinados recursos de destino tem de ser colocada em manutenção

4. Clique no nome da regra de alerta métrica que pretende editar

5. A regra de editar, clique nas **critérios de alerta** que pretende editar. Pode alterar a métrica, a condição de limiar e outros campos conforme necessário

    > [!NOTE]
    > Não é possível editar a **recurso de destino** e **nome da regra de alerta** depois de criar o alerta de métrica.

6. Clique em **feito** para guardar as suas edições.

## <a name="with-azure-cli"></a>Com a CLI do Azure

As secções anteriores descreveram como criar, ver e gerir regras de alerta de métrica com portal do Azure. Esta secção descreve como fazer o mesmo com várias plataformas [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). CLI do Azure mais rápida maneira de começar a utilizar é através de [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Neste artigo, iremos irá utilizar o Cloud shell.

1. Aceda ao portal do Azure, clique em **Cloud shell**.

2. Na linha de comandos, pode utilizar os comandos com ``--help`` opção para saber mais sobre o comando e como usá-lo. Por exemplo, o comando seguinte mostra a lista de comandos disponíveis para criar, visualizar e gerir alertas de métricas

    ```azurecli
    az monitor metrics alert --help
    ```

3. Pode criar uma regra alerta de métrica simple que monitoriza se a média de percentagem de CPU, numa VM é superior a 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Pode ver todos os alertas de métricas num grupo de recursos com o seguinte comando

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Pode ver os detalhes de uma regra de alerta métrica específica com o nome ou o ID de recurso da regra.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Pode desativar uma regra de alerta de métrica utilizando o seguinte comando.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Pode eliminar uma regra de alerta de métrica utilizando o seguinte comando.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Passos Seguintes

- [Criar alertas de métricas utilizando modelos do Azure Resource Manager](../../azure-monitor/platform/alerts-enable-template.md).
- [Compreender a forma como o trabalho de alertas de métrica](alerts-metric-overview.md).
- [Compreender como métrica alertas com limiares dinâmicos condição trabalho](alerts-dynamic-thresholds.md).
- [Compreender o esquema de hook de web para alertas de métricas](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

