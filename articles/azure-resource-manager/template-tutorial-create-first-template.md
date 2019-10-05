---
title: Tutorial – criar e implantar seu primeiro modelo de Azure Resource Manager
description: Descreve as etapas para criar seu primeiro modelo de Azure Resource Manager. Você aprende sobre a sintaxe do arquivo de modelo e como implantar uma conta de armazenamento.
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7996f564aaa78313304bf3bc11f549d24fce82a4
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963857"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Tutorial: Criar e implementar o seu primeiro modelo do Azure Resource Manager

Este tutorial apresenta os modelos do Resource Manager. Ele mostra como criar um modelo inicial e implantá-lo no Azure. Você aprenderá sobre a estrutura do modelo e as ferramentas necessárias para trabalhar com modelos. Leva cerca de **12 minutos** para concluir este tutorial, mas o tempo real varia de acordo com o número de ferramentas que você precisa instalar.

Este tutorial é o primeiro de uma série. À medida que avança pela série, você modifica o modelo inicial passo a passo até explorar todas as partes principais de um modelo do Resource Manager. Esses elementos são os blocos de construção para modelos muito mais complexos. Esperamos que o final da série você tenha certeza de criar seus próprios modelos e esteja pronto para automatizar suas implantações com modelos.

Se você quiser saber mais sobre os benefícios de usar modelos e por que deve automatizar a implantação com modelos, consulte [modelos de Azure Resource Manager](template-deployment-overview.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="get-tools"></a>Obter ferramentas

Vamos começar verificando se você tem as ferramentas necessárias para criar e implantar modelos.

### <a name="editor"></a>Editor

Os modelos são arquivos JSON. Para criar modelos, você precisa de um bom editor de JSON. É recomendável Visual Studio Code com a extensão de ferramentas do Resource Manager. Se você precisar instalar essas ferramentas, consulte [usar Visual Studio Code para criar modelos de Azure Resource Manager](./resource-manager-tools-vs-code.md).

### <a name="command-line-deployment"></a>Implantação de linha de comando

Você também precisa de Azure PowerShell ou CLI do Azure para implantar o modelo. Para obter as instruções de instalação, consulte:

- [Instalar o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar o CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instalar o CLI do Azure no Linux](/cli/azure/install-azure-cli-linux)

Depois de instalar o Azure PowerShell ou CLI do Azure, certifique-se de entrar pela primeira vez. Para obter ajuda, consulte [entrar-PowerShell](/powershell/azure/install-az-ps#sign-in) ou [entrar-CLI do Azure](/cli/azure/get-started-with-azure-cli#sign-in).

Ok, você está pronto para começar a aprender sobre modelos.

## <a name="create-your-first-template"></a>Criar seu primeiro modelo

1. Abra Visual Studio Code com a extensão de ferramentas do Resource Manager instalada.
1. No menu **arquivo** , selecione **novo arquivo** para criar um novo arquivo.
1. No menu **arquivo** , selecione **salvar como**.
1. Nomeie o arquivo **azuredeploy** e selecione a extensão de arquivo **JSON** . O nome completo do arquivo **azuredeploy. JSON**.
1. Salve o arquivo em sua estação de trabalho. Selecione um caminho que seja fácil de lembrar, pois você fornecerá esse caminho mais tarde ao implantar o modelo.
1. Copie e cole o JSON a seguir no arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Esta é a aparência de seu ambiente de VS Code:

    ![Modelo do Resource Manager modelo do Visual Studio Code-primeiro](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Este modelo não implanta nenhum recurso. Estamos começando com um modelo em branco para que você possa se familiarizar com as etapas para implantar um modelo, minimizando a chance de algo ficar errado.

    O arquivo JSON tem estes elementos:

    - **$Schema**: Especifica o local do arquivo de esquema JSON. O arquivo de esquema descreve as propriedades que estão disponíveis em um modelo. Por exemplo, o esquema define **recursos** como uma das propriedades válidas para um modelo. Não se preocupe que a data do esquema é 2015-01-01. Essa versão de esquema está atualizada e inclui todos os recursos mais recentes. A data do esquema não foi alterada porque não houve alterações significativas desde sua introdução.
    - **contentVersion**: Especifica a versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para este elemento. Use esse valor para documentar alterações significativas em seu modelo. Ao implantar recursos usando o modelo, esse valor pode ser usado para garantir que o modelo correto esteja sendo usado.
    - **recursos**: Contém os recursos que você deseja implantar ou atualizar. Atualmente, ele está vazio, mas você adicionará recursos mais tarde.

1. Guarde o ficheiro.

Parabéns, você criou seu primeiro modelo.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para começar a trabalhar com Azure PowerShell/CLI do Azure, entre com suas credenciais do Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Criar grupo de recursos

Ao implantar um modelo, você especifica um grupo de recursos que conterá os recursos. Antes de executar o comando de implantação, crie o grupo de recursos com CLI do Azure ou Azure PowerShell. Selecione as guias na seção de código a seguir para escolher entre Azure PowerShell e CLI do Azure.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Implementar o modelo

Para implantar o modelo, use CLI do Azure ou Azure PowerShell. Use o grupo de recursos que você criou. Dê um nome para a implantação para que você possa identificá-la facilmente no histórico de implantação. Para sua conveniência, crie também uma variável que armazena o caminho para o arquivo de modelo. Essa variável facilita a execução dos comandos de implantação porque você não precisa digitar o caminho sempre que implantar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

O comando de implantação retorna resultados. Procure `ProvisioningState` para ver se a implantação foi bem-sucedida.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![Estado de provisionamento de implantação do PowerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

![Estado de provisionamento de CLI do Azure implantação](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Verificar a implementação

Você pode verificar a implantação explorando o grupo de recursos do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione **grupos de recursos**.

1. Selecione o grupo de recursos implantar no último procedimento. O nome padrão é **MyResource**. Você não deverá ver nenhum recurso implantado dentro do grupo de recursos.

1. Observe que, no canto superior direito da visão geral, o status da implantação é exibido. A seleção **1 foi bem-sucedida**.

   ![Exibir status da implantação](./media/template-tutorial-create-first-template/deployment-status.png)

1. Você verá um histórico de implantação para o grupo de recursos. Selecione **blanktemplate**.

   ![Selecionar implantação](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Você verá um resumo da implantação. Nesse caso, não há muito a ver porque nenhum recurso foi implantado. Posteriormente nesta série, talvez você ache útil revisar o resumo no histórico de implantação. Observe à esquerda que você pode exibir entradas, saídas e o modelo usado durante a implantação.

   ![Exibir Resumo da implantação](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira excluir o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Você criou um modelo simples para implantar no Azure. No próximo tutorial, você adicionará uma conta de armazenamento ao modelo e a implantará em seu grupo de recursos.

> [!div class="nextstepaction"]
> [Adicionar recurso](template-tutorial-add-resource.md)