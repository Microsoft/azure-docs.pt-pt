---
title: Partilhe fora do seu org (modelo ARM) - Azure Data Share quickstart
description: Saiba como partilhar dados com clientes e parceiros usando a Azure Data Share e um modelo de Gestor de Recursos Azure (modelo ARM) neste arranque rápido.
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 5abe92120c8b822ac86ced90658869a0858d4ff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487692"
---
# <a name="quickstart-share-data-using-azure-data-share-and-arm-template"></a>Quickstart: Partilhar dados usando o modelo Azure Data Share e ARM

Saiba como configurar uma nova partilha de dados Azure a partir de uma conta de armazenamento Azure utilizando um modelo de Gestor de Recursos Azure (modelo ARM). E, comece a partilhar os seus dados com clientes e parceiros fora da sua organização Azure. Para obter uma lista das lojas de dados suportadas, consulte [as lojas de dados suportadas no Azure Data Share](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/).

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

Os seguintes recursos são definidos no modelo:

* [Microsoft.Storage/storageAcontas:](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Storage/storageAcontas/blobServices/contentores](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.DataShare/contas](/azure/templates/microsoft.datashare/accounts)
* [Microsoft.DataShare/contas/ações](/azure/templates/microsoft.datashare/accounts/shares)
* [Microsoft.Storage/storageAconselhos/fornecedores/funAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/contas/ações/dataSets](/azure/templates/microsoft.datashare/accounts/shares/datasets)
* [Microsoft.DataShare/contas/ações/convites](/azure/templates/microsoft.datashare/accounts/shares/invitations)
* [Microsoft.DataShare/contas/ações/sincronizaçõesS](/azure/templates/microsoft.datashare/accounts/shares/synchronizationsettings)

O modelo executa as seguintes tarefas:

* Crie uma conta de armazenamento e um recipiente utilizado como fonte de dados de partilha de dados.
* Criar uma conta de partilha de dados e uma partilha de Dados.
* Crie uma atribuição de papel para conceder o papel de Leitor de Dados blob de armazenamento ao recurso de partilha de dados de origem. Ver [Funções e requisitos para Azure Data Share](./concepts-roles-permissions.md).
* Adicione um conjunto de dados à partilha Data.
* Adicione os destinatários à partilha data.
* Ativar um horário instantâneo para a partilha de Dados.

Este modelo é criado para fins de aprendizagem. Na prática, normalmente tem alguns dados numa conta de armazenamento existente. Você precisaria de criar a atribuição de funções antes de executar um modelo ou um script para criar o conjunto de dados. Por vezes, pode receber a seguinte mensagem de erro quando implementar o modelo:

```plaintext
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

É porque a implementação está a tentar criar o conjunto de dados antes da atribuição de funções do Azure ser finalizada. Apesar da mensagem de erro, a implementação pode ser bem sucedida. Você ainda seria capaz de andar através [de recursos implantados da Revisão.](#review-deployed-resources)

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a seguinte imagem para iniciar seduca e abrir o modelo.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Selecione ou introduza os seguintes valores:

    * **Subscrição**: selecione uma subscrição Azure usada para criar a partilha de dados e os outros recursos.
    * **Grupo de recursos**: selecione **Criar novos** para criar um novo grupo de recursos ou selecionar um grupo de recursos existente.
    * **Localização**: selecione uma localização para o grupo de recursos.
    * **Nome do projeto:** insira um nome de projeto.  O nome do projeto é usado para gerar nomes de recursos.  Consulte as definições variáveis no modelo anterior.
    * **localização:** selecione um local para os recursos.  Pode utilizar o mesmo local para o grupo de recursos.
    * **Email do Convite**: insira o endereço de e-mail de login do destinatário da partilha de dados.  E-mail pseudónimo não funciona.

    Utilize o valor predefinido para o resto das definições.
1. selecionar **Concordo com os termos e condições acima indicados,** e com a **aquisição** selecionada .

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra a conta de partilha de dados que criou.
1. A partir do menu esquerdo, selecione **Enviar Partilhas**.  Verá a conta de armazenamento listada.
1. Selecione a conta de armazenamento.  Em **Detalhes,** verá a definição de sincronização tal como configurada no modelo.

    ![Definições de sincronização de conta de armazenamento de partilha de dados Azure](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Selecione **Convites** a partir do topo. Verá o endereço de e-mail especificado quando implementar o modelo. O **Estatuto** deve estar **pendente.**

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, que elimina os recursos do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma partilha de dados Azure e a convidar os destinatários. Para saber mais sobre como um consumidor de dados pode aceitar e receber uma partilha de dados, continue a aceitar e receber o tutorial [de dados.](subscribe-to-data-share.md)
