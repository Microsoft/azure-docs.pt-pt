---
title: Criar, visualizar e gerir alertas métricos usando o Monitor Azure
description: Aprenda a usar o portal Azure ou o CLI para criar, visualizar e gerir regras de alerta métricos.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 03/13/2020
ms.subservice: alerts
ms.openlocfilehash: cefccd08ea66638f08f00e280fe2704444a7f916
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369391"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Criar, visualizar e gerir alertas métricos usando o Monitor Azure

Os alertas métricos no Monitor Azure fornecem uma forma de ser notificado quando uma das suas métricas ultrapassa um limiar. Os alertas de métricas funcionam numa variedade de métricas de plataforma multidimensionais, métricas personalizadas e métricas padrão e personalizadas do Application Insights. Neste artigo, descreveremos como criar, visualizar e gerir regras de alerta métrica através do portal Azure e do Azure CLI. Também pode criar regras de alerta métricautilizando modelos do Gestor de Recursos Azure, que são descritos [num artigo separado](alerts-metric-create-templates.md).

Pode saber mais sobre como os alertas métricos funcionam a partir de [alertas métricos.](alerts-metric-overview.md)

## <a name="create-with-azure-portal"></a>Criar com portal Azure

O seguinte procedimento descreve como criar uma regra de alerta métrico no portal Azure:

1. No [portal Azure,](https://portal.azure.com)clique no **Monitor**. A lâmina Monitor consolida todas as definições e dados de monitorização numa só vista.

2. Clique em **Alertas** e clique + **Nova regra de alerta**.

    > [!TIP]
    > A maioria das lâminas de recursos também tem **Alertas** no seu menu de recursos em **Monitorização,** você poderia criar alertas a partir daí também.

3. Clique em **Selecionar o alvo**, no painel de contexto que carrega, selecione um recurso-alvo que pretende alertar. Utilize as quedas do tipo **Subscrição** e **Recursos** para encontrar o recurso que pretende monitorizar. Também pode utilizar a barra de pesquisa para encontrar o seu recurso.

4. Se o recurso selecionado tiver métricas em que pode criar alertas, **os sinais disponíveis** no canto inferior direito incluirão métricas. Pode ver a lista completa de tipos de recursos suportados para alertas métricos neste [artigo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Depois de ter selecionado um recurso-alvo, clique na **condição de Adicionar**.

6. Verá uma lista de sinais suportados para o recurso, selecione a métrica em que pretende criar um alerta.

7. Verá um gráfico para a métrica nas últimas seis horas. Utilize o período de queda **do gráfico** para selecionar para ver um histórico mais longo para a métrica.

8. Se a métrica tiver dimensões, verá uma tabela de dimensões apresentada. Selecione um ou mais valores por dimensão.
    - Os valores de dimensão apresentados baseiam-se em dados métricos dos últimos três dias.
    - Se o valor de dimensão que procura não for apresentado, clique em "+" para adicionar um valor personalizado.
    - Também pode **selecionar \*** para qualquer uma das dimensões. **Selecione \*** escalará dinamicamente a seleção para todos os valores atuais e futuros para uma dimensão.

    A regra de alerta métrico avaliará a condição para todas as combinações de valores selecionados. Saiba mais sobre como funciona o alerta sobre as [métricas multidimensionais.](alerts-metric-overview.md)

9. Selecione o tipo **Limiar,** **Operador**e tipo **de agregação**. Isto determinará a lógica que a regra de alerta métrico irá avaliar.
    - Se estiver a utilizar um limiar **estático,** continue a definir um **valor limiar**. O gráfico métrico pode ajudar a determinar o que pode ser um limiar razoável.
    - Se estiver a utilizar um limiar **Dinâmico,** continue a definir a sensibilidade ao **limiar**. O gráfico métrico apresentará os limiares calculados com base em dados recentes. Saiba mais sobre o tipo de [condição dos Limiares Dinâmicos e opções](alerts-dynamic-thresholds.md)de sensibilidade.

10. Opcionalmente, refine a condição ajustando a **granularidade de agregação** e **a frequência de avaliação.** 

11. Clique em **Concluído**.

12. Opcionalmente, adicione outros critérios se quiser monitorizar uma regra de alerta complexa. Atualmente, os utilizadores podem ter regras de alerta com critérios de Limiardinâmico como um critério único.

13. Preencha **os detalhes** do Alerta como o nome da regra do **Alerta,** **Descrição**e **Gravidade.**

14. Adicione um grupo de ação ao alerta, quer selecionando um grupo de ação existente, quer criando um novo grupo de ação.

15. Clique **em Feito** para salvar a regra de alerta métrico.

> [!NOTE]
> As regras de alerta métrico criadas através do portal são criadas no mesmo grupo de recursos que o recurso-alvo.

## <a name="view-and-manage-with-azure-portal"></a>Ver e gerir com o portal Azure

Pode visualizar e gerir regras de alerta métricautilizando a lâmina 'Regras de Gestão' em Alertas. O procedimento abaixo mostra como ver as suas regras de alerta métrico e editar uma delas.

1. No portal Azure, navegue para **Monitor**

2. Clique em **Alertas** e **Gerir regras**

3. Na lâmina de **regras de Gestão,** pode ver todas as suas regras de alerta em subscrições. Pode filtrar ainda mais as regras utilizando o **grupo Derecurso,** **o tipo de Recurso**e o **Recurso**. Se quiser ver apenas alertas métricos, selecione **o tipo de sinal** como Métricas.

    > [!TIP]
    > Na lâmina de **regras 'Gerir',** pode selecionar várias regras de alerta e ative-as/desativá-las. Isto pode ser útil quando certos recursos-alvo precisam de ser colocados sob manutenção

4. Clique no nome da regra de alerta métrico que pretende editar

5. Na Regra de Edição, clique nos **critérios** de Alerta que pretende editar. Pode alterar a métrica, condição limiar e outros campos conforme necessário

    > [!NOTE]
    > Não é possível editar o **recurso Target** e o Nome da Regra de **Alerta** após a criação do alerta métrico.

6. Clique **em Feito** para guardar as suas edições.

## <a name="with-azure-cli"></a>Com a CLI do Azure

As secções anteriores descreveram como criar, visualizar e gerir regras de alerta métrica usando o portal Azure. Esta secção descreverá como fazer o mesmo utilizando o [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)da plataforma transversal . A forma mais rápida de começar a usar o Azure CLI é através da [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Para este artigo, usaremos cloud shell.

1. Vá ao portal Azure, clique na **Cloud Shell.**

2. A pedido, pode utilizar comandos com ``--help`` opção para saber mais sobre o comando e como usá-lo. Por exemplo, o seguinte comando mostra-lhe a lista de comandos disponíveis para criar, visualizar e gerir alertas métricos

    ```azurecli
    az monitor metrics alert --help
    ```

3. Pode criar uma regra de alerta métrico simples que monitoriza se a percentagem média de CPU num VM for superior a 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. Pode ver todos os alertas métricos num grupo de recursos usando o seguinte comando

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Pode ver os detalhes de uma determinada regra de alerta métrico usando o nome ou a identificação de recursos da regra.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. Pode desativar uma regra de alerta métrico utilizando o seguinte comando.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. Pode eliminar uma regra de alerta métrico utilizando o seguinte comando.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>Passos seguintes

- [Crie alertas métricos utilizando modelos](../../azure-monitor/platform/alerts-metric-create-templates.md)de gestor de recursos azure .
- [Entenda como os alertas métricos funcionam.](alerts-metric-overview.md)
- [Compreenda como funcionam os alertas métricos com a condição dos Limiares Dinâmicos.](alerts-dynamic-thresholds.md)
- [Compreenda o esquema do gancho web para alertas métricos](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

