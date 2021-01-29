---
title: 'Quickstart: Criar uma conta de Automação - Modelo Azure'
titleSuffix: Azure Automation
description: Este quickstart mostra como criar uma conta Demôm automação utilizando o modelo Azure Resource Manager.
services: automation
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d0582876a08b433a10106b97179ab603d4ec5762
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050673"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Quickstart: Criar uma conta de Automação utilizando o modelo ARM

A Azure Automation oferece um serviço de automação e configuração baseado na nuvem que suporta uma gestão consistente em todos os seus ambientes Azure e não-Azure. Este quickstart mostra-lhe como implementar um modelo de Gestor de Recursos Azure (modelo ARM) que cria uma conta de Automação. A utilização de um modelo ARM dá menos passos em comparação com outros métodos de implantação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

Este modelo de amostra executa o seguinte:

* Automatiza a criação de um espaço de trabalho Azure Monitor Log Analytics.
* Automatiza a criação de uma conta Azure Automation.
* Liga a conta Automation à conta Despassição ao espaço de trabalho Do Log Analytics.
* Adiciona livros de amostra de automatização à conta.

>[!NOTE]
>Criação da automatização Executada Como a conta não é suportada quando se está a utilizar um modelo ARM. Para criar uma conta Run As manualmente a partir do portal ou com o PowerShell, consulte [a conta Criar Como](create-run-as-account.md).

Depois de completar estes passos, precisa de [configurar as definições](automation-manage-send-joblogs-log-analytics.md) de diagnóstico para a sua conta Demôm automação para enviar o estado de trabalho do runbook e os fluxos de trabalho para o espaço de trabalho do Log Analytics ligado.

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>Versões da API

A tabela que se segue lista a versão API para os recursos utilizados neste exemplo.

| Recurso | Tipo de recurso | Versão API |
|:---|:---|:---|
| [Área de trabalho](/azure/templates/microsoft.operationalinsights/workspaces) | áreas de trabalho | Antevisão 2020-03-01 |
| [Conta de automação](/azure/templates/microsoft.automation/automationaccounts) | automatização | Antevisão 2020-01-13 |
| [Serviços ligados ao espaço de trabalho](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | áreas de trabalho | Antevisão 2020-03-01 |

### <a name="before-you-use-the-template"></a>Antes de usar o modelo

O modelo de parâmetros JSON está configurado para especificar:

* O nome do espaço de trabalho.
* A região para criar o espaço de trabalho em.
* O nome da conta Automation.
* A região para criar a conta Automation in.

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* *sku* incumprimentos ao nível de preços por GB lançado no modelo de preços de abril de 2018.
* *dataRetention* predefinição para 30 dias.

>[!WARNING]
>Se pretender criar ou configurar um espaço de trabalho Log Analytics numa subscrição que tenha optado pelo modelo de preços de abril de 2018, o único nível de preços válido do Log Analytics é *o PerGB2018.*
>

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão no seu ambiente. Pode armazenar o modelo numa conta de armazenamento Azure para acesso partilhado na sua organização. Para obter mais informações sobre o trabalho com os modelos, consulte [recursos de implantação com modelos ARM e o Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

Se é novo na Azure Automation e Azure Monitor, é importante que compreenda os seguintes detalhes de configuração. Podem ajudá-lo a evitar erros quando tenta criar, configurar e utilizar um espaço de trabalho Log Analytics ligado à sua nova conta Automation.

* Reveja [detalhes adicionais](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace) para entender completamente as opções de configuração do espaço de trabalho, tais como o modo de controlo de acesso, o nível de preços, a retenção e o nível de reserva de capacidade.

* Reveja [os mapeamentos do espaço de trabalho](how-to/region-mappings.md) para especificar as regiões suportadas em linha ou num ficheiro de parâmetros. Apenas algumas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma conta Automation na sua subscrição.

* Se é novo nos registos do Azure Monitor e ainda não implementou um espaço de trabalho, deverá rever a [orientação](../azure-monitor/platform/design-logs-deployment.md)do design do espaço de trabalho . Irá ajudá-lo a aprender sobre o controlo de acessos e a compreender as estratégias de implementação do design que recomendamos para a sua organização.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma conta Azure Automation, um espaço de trabalho Log Analytics e liga a conta Automation ao espaço de trabalho.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Insira os valores.

3. A colocação pode levar alguns minutos para terminar. Quando concluída, a saída é semelhante à seguinte:

    ![Exemplo resultado quando a implementação está completa](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal Azure, abra a conta Automation que acaba de criar. 

3. A partir do painel esquerdo, selecione **Runbooks**. Na página **Runbooks,** listadas estão três runbooks tutoriais criados com a conta Automation.

    ![Livros de execução tutoriais criados com conta Automation](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. A partir do painel de esquerda, selecione **Linked workspace**. Na página do **espaço de trabalho Linked,** mostra o espaço de trabalho Log Analytics especificado anteriormente ligado à sua conta Automation.

    ![Conta de automação ligada ao espaço de trabalho Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar deles, desvincular a conta Desvinculação do espaço de trabalho Do Log Analytics e, em seguida, eliminar a conta de Automação e espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma conta Automation, um espaço de trabalho Log Analytics e ligou-os.

Para saber mais, continue os tutoriais da Azure Automation.

> [!div class="nextstepaction"]
> [Tutoriais de Automação Azure](learn/automation-tutorial-runbook-graphical.md)