---
title: Criar e gerir aplicativos lógicos com o Azure CLI
description: Utilize o CLI Azure para criar uma aplicação lógica, em seguida, gerencie a sua aplicação lógica usando operações como lista, show (get), atualização e exclusão.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperfq2
ms.date: 11/23/2020
ms.openlocfilehash: f5b8497772a14e9613977c9cdc22025e8e58b92c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95509464"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Quickstart: Criar e gerir aplicações lógicas utilizando o Azure CLI

Este quickstart mostra-lhe como criar e gerir aplicações lógicas utilizando a [extensão Azure CLI Logic Apps](/cli/azure/ext/logic/logic) `az logic` (). A partir da linha de comando, pode criar uma aplicação lógica utilizando o ficheiro JSON para uma definição lógica de fluxo de trabalho de aplicações. Em seguida, pode gerir a sua aplicação lógica executando operações `list` como, `show` por `get` `update` exemplo, e `delete` a partir da linha de comando.

> [!WARNING]
> A extensão Azure CLI Logic Apps é atualmente *experimental* e *não coberta pelo apoio ao cliente.* Utilize com cuidado esta extensão CLI, especialmente se optar por utilizar a extensão em ambientes de produção.

Se é novo em Aplicações Lógicas, também pode aprender a criar as suas primeiras aplicações lógicas [através do portal Azure](quickstart-create-first-logic-app-workflow.md), [no Visual Studio](quickstart-create-logic-apps-with-visual-studio.md), e no Visual [Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [CLI Azure](/cli/azure/install-azure-cli) instalado no seu computador local.
* A [extensão Logic Apps Azure CLI](/cli/azure/azure-cli-extensions-list) instalada no seu computador. Para instalar esta extensão, utilize este comando: `az extension add --name logic`
* Um [grupo de recursos Azure](#example---create-resource-group) para criar a sua aplicação lógica.

### <a name="prerequisite-check"></a>Verificação pré-requisito

Valide o seu ambiente antes de começar:

* Inscreva-se no portal Azure e verifique se a sua subscrição está ativa executando `az login` .
* Verifique a sua versão do Azure CLI numa janela de terminal ou comando em funcionamento `az --version` . Para a versão mais recente, consulte as [últimas notas de lançamento](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Se não tiver a versão mais recente, atualize a sua instalação seguindo o [guia de instalação do seu sistema operativo ou plataforma.](/cli/azure/install-azure-cli)

### <a name="example---create-resource-group"></a>Exemplo - criar grupo de recursos

Se ainda não tiver um grupo de recursos para a sua aplicação lógica, crie o grupo com o comando `az group create` . Por exemplo, o seguinte comando cria um grupo de recursos nomeado `testResourceGroup` no local `westus` .

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

A saída mostra `provisioningState` como quando o seu grupo de recursos é criado com `Succeeded` sucesso:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Definição do fluxo de trabalho

Antes de [criar uma nova aplicação lógica](#create-logic-apps-from-cli) ou atualizar uma [aplicação lógica existente](#update-logic-apps-from-cli) utilizando o Azure CLI, precisa de uma definição de fluxo de trabalho para a sua aplicação lógica. No portal Azure, pode ver a definição subjacente do fluxo de trabalho da sua aplicação lógica no formato JSON, mudando de vista de **Designer** para **vista de Código.**

Quando executa os comandos para criar ou atualizar a sua aplicação lógica, a sua definição de fluxo de trabalho é carregada como um parâmetro necessário `--definition` (). Tem de criar a definição de fluxo de trabalho como um ficheiro JSON que segue o [esquema de linguagem de definição de fluxo de trabalho.](./logic-apps-workflow-definition-language.md)

## <a name="create-logic-apps-from-cli"></a>Criar aplicativos lógicos do CLI

Pode criar um fluxo de trabalho de aplicações lógicas a partir do CLI Azure utilizando o comando [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) com um ficheiro JSON para a definição.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

O seu comando deve incluir os [seguintes parâmetros necessários:](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters)

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Definição do fluxo de trabalho | `--definition` | Um ficheiro JSON com a [definição](#workflow-definition)de fluxo de trabalho da sua aplicação lógica. |
| Localização | `--location -l` | A região de Azure em que se encontra a sua aplicação lógica. |
| Name | `--name -n` | O nome da sua aplicação lógica. O nome só pode conter letras, números, hífens `-` (), sublinhados `_` (), parênteses `()` ( ) e períodos ( `.` ). O nome também deve ser único em todas as regiões. |
| Nome do grupo de recursos | `--resource-group -g` | O [grupo de recursos Azure](../azure-resource-manager/management/overview.md) no qual pretende criar a sua aplicação lógica. [Crie um grupo de recursos](#example---create-resource-group) antes de começar se ainda não tiver um para a sua aplicação lógica. |

Também pode incluir [parâmetros opcionais](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) adicionais para configurar os controlos de acesso da sua aplicação lógica, pontos finais, conta de integração, ambiente de serviço de integração, estado e etiquetas de recursos.

### <a name="example---create-logic-app"></a>Exemplo - criar app lógica

Neste exemplo, é criado um fluxo de trabalho nomeado `testLogicApp` no grupo de recursos no `testResourceGroup` `westus` local. O ficheiro JSON `testDefinition.json` contém a definição de fluxo de trabalho.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

Quando o seu fluxo de trabalho é criado com sucesso, o CLI mostra o código JSON da sua nova definição de fluxo de trabalho. Se a sua criação de fluxo de trabalho falhar, consulte a [lista de possíveis erros](#errors).

## <a name="update-logic-apps-from-cli"></a>Atualizar aplicativos lógicos do CLI

Também pode atualizar o fluxo de trabalho de uma aplicação lógica a partir do CLI Azure utilizando o comando [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) .

O seu comando deve incluir os [mesmos parâmetros exigidos](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) que quando [cria uma aplicação lógica.](#create-logic-apps-from-cli) Também pode adicionar os [mesmos parâmetros opcionais](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) que ao criar uma aplicação lógica.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Exemplo - app lógica de atualização

Neste exemplo, o fluxo de trabalho da [amostra criado na secção anterior](#example---create-logic-app) é atualizado para utilizar um ficheiro de definição JSON diferente, e adicionar `newTestDefinition.json` duas etiquetas de recursos, `testTag1` e com `testTag2` valores de descrição.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

Quando o seu fluxo de trabalho é atualizado com sucesso, o CLI mostra a definição atualizada do fluxo de trabalho da sua aplicação lógica. Se a atualização falhar, consulte a [lista de possíveis erros](#errors).

## <a name="delete-logic-apps-from-cli"></a>Eliminar aplicativos lógicos do CLI

Pode eliminar o fluxo de trabalho de uma aplicação lógica do CLI Azure utilizando o comando [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete) .

O seu comando deve incluir os [seguintes parâmetros necessários:](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters)

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Nome | `--name -n` | O nome da sua aplicação lógica. |
| Nome do grupo de recursos | `-resource-group -g` | O grupo de recursos no qual a sua aplicação lógica está localizada. |

Também pode incluir um [parâmetro opcional](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters) para saltar as solicitações de confirmação, `--yes -y` .

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

Em seguida, o CLI pede-lhe que confirme a eliminação da sua aplicação lógica. Pode saltar o pedido de confirmação utilizando o parâmetro opcional `--yes -y` com o seu comando.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Pode confirmar a eliminação de uma aplicação lógica [listando as suas aplicações lógicas no CLI](#list-logic-apps-in-cli), ou visualizando as suas aplicações lógicas no portal Azure.

### <a name="example---delete-logic-app"></a>Exemplo - eliminar aplicativo lógico

Neste exemplo, o fluxo de trabalho da [amostra criado numa secção anterior](#example---create-logic-app) é eliminado.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Depois de responder ao pedido de `y` confirmação, a aplicação lógica é eliminada.

## <a name="show-logic-apps-in-cli"></a>Mostrar aplicativos lógicos no CLI

Pode obter um fluxo de trabalho de aplicações lógicas específico usando o comando [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show) .

```azurecli

az logic workflow show --name
                       --resource-group

```

O seu comando deve incluir os [seguintes parâmetros necessários](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters)

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Nome | `--name -n` | O nome da sua aplicação lógica. |
| Nome do grupo de recursos | `--resource-group -g` | O nome do grupo de recursos em que a sua aplicação lógica está localizada. |

### <a name="example---get-logic-app"></a>Exemplo - obtenha app lógica

Neste exemplo, a aplicação lógica `testLogicApp` do grupo de recursos é devolvida com `testResourceGroup` registos completos para depuração.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Listar aplicativos lógicos no CLI

Pode listar as suas aplicações lógicas por subscrição utilizando o comando [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list) . Este comando devolve o código JSON para os fluxos de trabalho das suas aplicações lógicas.

Pode filtrar os seus resultados pelos [seguintes parâmetros opcionais:](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters)

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Nome do grupo de recursos | `--resource-group -g` | O nome do grupo de recursos através do qual pretende filtrar os seus resultados. |
| Número de itens | `--top` | O número de itens incluídos nos seus resultados. |
| Filtro | `--filter` | O tipo de filtro que está a usar na sua lista. Pode filtrar por estado `State` (, gatilho `Trigger` ) e o identificador do recurso referenciado ( `ReferencedResourceId` ). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Exemplo - listar aplicativos lógicos

Neste exemplo, todos os fluxos de trabalho ativados no grupo de recursos `testResourceGroup` são devolvidos num formato de tabela ASCII.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Erros

O seguinte erro indica que a extensão CLI das Aplicações Lógicas Azure não está instalada. Siga os passos nos pré-requisitos para [instalar a extensão De Aplicações Lógicas](#prerequisites) no seu computador.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

O seguinte erro pode indicar que o caminho do ficheiro para o upload da definição de fluxo de trabalho está incorreto.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Parâmetros globais

Pode utilizar os seguintes parâmetros globais do Azure CLI com os seus `az logic` comandos:

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Formato de saída | `--output -o` | Altere o formato de [saída](/cli/azure/format-output-azure-cli) a partir do JSON predefinido. |
| Apenas mostrar erros | `--only-show-errors` | Suprimir avisos e apenas mostrar erros. |
| Verboso | `--verbose` | Mostre troncos verbosos. |
| Depurar | `--debug` | Mostra todos os registos de depurg. |
| Mensagem de ajuda | `--help -h` | Mostre o diálogo de ajuda. |
| Consulta | `--query` | Deslote uma cadeia de consulta JMESPath para a saída JSON. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure CLI, consulte a documentação do [Azure CLI](/cli/azure/).

Pode encontrar amostras de scripts CLI de aplicações lógicas adicionais no [navegador de amostras de código da Microsoft.](/samples/browse/?products=azure-logic-apps)

Em seguida, pode criar uma lógica de aplicação de exemplo através do CLI Azure usando um script de amostra e definição de fluxo de trabalho.

> [!div class="nextstepaction"]
> [Crie aplicativo lógico usando o script da amostra.](sample-logic-apps-cli-script.md)
