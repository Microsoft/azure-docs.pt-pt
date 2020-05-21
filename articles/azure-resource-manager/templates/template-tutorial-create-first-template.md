---
title: Tutorial - Criar & modelo de implantação
description: Crie o seu primeiro modelo de Gestor de Recursos Azure. No tutorial, você aprende sobre a sintaxe de ficheiro sintaxe do modelo e como implementar uma conta de armazenamento.
author: mumian
ms.date: 05/20/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 2180ca80d87643eb885d814318e516b4b3c53f37
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714802"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Tutorial: Crie e implante o seu primeiro modelo ARM

Este tutorial apresenta-lhe os modelos do Gestor de Recursos Azure (ARM). Mostra como criar um modelo de arranque e implantá-lo para Azure. Você vai aprender sobre a estrutura do modelo e as ferramentas que você precisa para trabalhar com modelos. Leva cerca de **12 minutos** para completar este tutorial, mas o tempo real variará em função de quantas ferramentas precisa de instalar.

Este tutorial é o primeiro de uma série. À medida que progride através da série, modifica-se o modelo de partida passo a passo até explorar todas as partes centrais de um modelo ARM. Estes elementos são os blocos de construção para modelos muito mais complexos. Esperamos que até ao final da série esteja confiante em criar os seus próprios modelos e pronto para automatizar as suas implementações com modelos.

Se quiser saber os benefícios de usar modelos e por que deve automatizar a implementação com modelos, consulte os modelos do Gestor de [Recursos Do Azure](overview.md).

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="get-tools"></a>Obter ferramentas

Vamos começar por ter a certeza de que tem as ferramentas necessárias para criar e implementar modelos.

### <a name="editor"></a>Editor

Os modelos são ficheiros JSON. Para criar modelos, precisa de um bom editor JSON. Recomendamos o Código do Estúdio Visual com a extensão ferramentas de gestor de recursos. Se precisar de instalar estas ferramentas, consulte [o Use Visual Studio Code para criar modelos ARM](use-vs-code-to-create-template.md).

### <a name="command-line-deployment"></a>Implantação da linha de comando

Também precisa do Azure PowerShell ou do Azure CLI para implementar o modelo. Se utilizar o Azure CLI, deve ter a versão mais recente. Para as instruções de instalação, consulte:

- [Instalar o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instale o Azure CLI no Linux](/cli/azure/install-azure-cli-linux)

Depois de instalar o Azure PowerShell ou o Azure CLI, certifique-se de que faz o início. Para obter ajuda, consulte [Iniciar sessão - PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Iniciar sessão - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Está pronto para começar a aprender sobre modelos.

## <a name="create-your-first-template"></a>Criar o primeiro modelo

1. Código de estúdio visual aberto com a extensão ferramentas de gestor de recursos instalada.
1. A partir do menu **'Ficheiro',** selecione **New File** para criar um novo ficheiro.
1. A partir do menu **'Ficheiro',** selecione **Guardar as**.
1. Nomeie o ficheiro **de implantação e** selecione a extensão do ficheiro **JSON.** O nome completo do ficheiro **azuredeploy.json**.
1. Guarde o ficheiro para o seu posto de trabalho. Selecione um caminho que seja fácil de lembrar porque irá fornecer esse caminho mais tarde ao implementar o modelo.
1. Copiar e colar o seguinte JSON no ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Aqui está o que o seu ambiente vs Código parece:

    ![Modelo de modelo de gestor de recursos modelo primeiro modelo](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Este modelo não implementa recursos. Estamos começando com um modelo em branco para que você possa se familiarizar com os passos para implementar um modelo, minimizando a chance de algo correr mal.

    O ficheiro JSON tem estes elementos:

    - **$schema**: Especifica a localização do ficheiro schema JSON. O ficheiro de esquema descreve as propriedades que estão disponíveis dentro de um modelo. Por exemplo, o esquema define **os recursos** como uma das propriedades válidas para um modelo. Não se preocupe que a data para o esquema é 2019-04-01. Esta versão de esquema está atualizada e inclui todas as funcionalidades mais recentes. A data do esquema não foi alterada porque não houve alterações desde a sua introdução.
    - **conteúdoVersão**: Especifica a versão do modelo (como 1.0.0.0). Pode fornecer qualquer valor para este elemento. Utilize este valor para documentar alterações significativas no seu modelo. Ao utilizar recursos utilizando o modelo, este valor pode ser utilizado para se certificar de que o modelo certo está a ser utilizado.
    - **recursos**: Contém os recursos que pretende implementar ou atualizar. Atualmente, está vazio, mas mais tarde vai adicionar recursos.

1. Guarde o ficheiro.

Parabéns, criou o seu primeiro modelo.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para começar a trabalhar com o Azure PowerShell/Azure CLI, inscreva-se com as suas credenciais Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se tiver várias subscrições do Azure, selecione a subscrição que pretende utilizar:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---


## <a name="create-resource-group"></a>Criar grupo de recursos

Ao implementar um modelo, especifice um grupo de recursos que conterá os recursos. Antes de executar o comando de implantação, crie o grupo de recursos com o Azure CLI ou o Azure PowerShell. Selecione os separadores na secção de código seguinte para escolher entre o Azure PowerShell e o Azure CLI. Os exemplos do CLI neste artigo são escritos para a concha bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Implementar o modelo

Para implementar o modelo, utilize o Azure CLI ou o Azure PowerShell. Use o grupo de recursos que criou. Dê um nome à implantação para que possa identificá-lo facilmente no histórico de implantação. Para conveniência, também criar uma variável que armazena o caminho para o ficheiro do modelo. Esta variável facilita-lhe a execução dos comandos de implantação porque não tem de reescrever o caminho sempre que se desdobra.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

O comando de implantação devolve os resultados. Procure `ProvisioningState` ver se o destacamento foi bem sucedido.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![Estado de fornecimento powerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

![Estado de provisionamento de implantação do Azure CLI](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Se a implantação falhar, utilize o interruptor **de depuração** com o comando de implantação para mostrar os registos de depuração.  Também pode utilizar o interruptor **verboso** para mostrar os registos completos de depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implantação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do menu esquerdo, selecione **Grupos de Recursos**.

1. Selecione o conjunto de recursos implantado no último procedimento. O nome predefinido é **myResourceGroup**. Não verá nenhum recurso implantado no grupo de recursos.

1. Nota na parte superior direita da visão geral, o estado da implantação é apresentado. Selecione **1 Bem-sucedido**.

   ![Ver estado de implantação](./media/template-tutorial-create-first-template/deployment-status.png)

1. Vê-se um historial de implantação para o grupo de recursos. Selecione **o modelo em branco**.

   ![Selecione implementação](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Vê um resumo do destacamento. Neste caso, não há muito para ver porque não foram mobilizados recursos. Mais tarde nesta série poderá achar útil rever o resumo da história da implantação. Note à esquerda que pode ver as inputs, saídas e o modelo utilizado durante a implantação.

   ![Ver resumo de implantação](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se está sanando agora, talvez queira apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Próximos passos

Criou um modelo simples para implantar no Azure. No próximo tutorial, você adicionará uma conta de armazenamento ao modelo e irá implementá-la ao seu grupo de recursos.

> [!div class="nextstepaction"]
> [Adicionar recurso](template-tutorial-add-resource.md)
