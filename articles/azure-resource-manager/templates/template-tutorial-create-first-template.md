---
title: Tutorial - Criar & modelo de implementação
description: Crie o seu primeiro modelo de Gestor de Recursos Azure (modelo ARM). No tutorial, você aprende sobre a sintaxe do ficheiro do modelo e como implementar uma conta de armazenamento.
author: mumian
ms.date: 12/17/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 001a20c38a82d053ae22a2614a32f0be9d8aabce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656993"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Tutorial: Crie e implemente o seu primeiro modelo ARM

Este tutorial apresenta-o aos modelos do Gestor de Recursos Azure (modelos ARM). Mostra como criar um modelo de arranque e implantá-lo para Azure. Você vai aprender sobre a estrutura do modelo e as ferramentas que você precisa para trabalhar com modelos. Leva cerca de **12 minutos** para completar este tutorial, mas o tempo real variará com base em quantas ferramentas precisa instalar.

Este tutorial é o primeiro de uma série. À medida que progride através da série, modifica o modelo inicial passo a passo até que tenha explorado todas as partes centrais de um modelo ARM. Estes elementos são os blocos de construção para modelos muito mais complexos. Esperamos que até ao final da série esteja confiante em criar os seus próprios modelos e pronto para automatizar as suas implementações com modelos.

Se quiser saber sobre os benefícios da utilização de modelos e por que deve automatizar a implementação com modelos, consulte a [visão geral do modelo ARM](overview.md). Para conhecer os modelos ARM através de um conjunto guiado de módulos no Microsoft Learn, consulte [implementar e gerir recursos em Azure utilizando modelos ARM](/learn/paths/deploy-manage-resource-manager-templates/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="get-tools"></a>Obter ferramentas

Comecemos por ter a certeza de que tem as ferramentas necessárias para criar e implementar modelos. Instale estas ferramentas na sua máquina local.

### <a name="editor"></a>Editor

Os modelos são ficheiros JSON. Para criar modelos, precisa de um bom editor JSON. Recomendamos o Código do Estúdio Visual com a extensão de Ferramentas do Gestor de Recursos. Se precisar de instalar estas ferramentas, consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).

### <a name="command-line-deployment"></a>Implantação da linha de comando

Também precisa de Azure PowerShell ou Azure CLI para implementar o modelo. Se utilizar o Azure CLI, deve ter a versão mais recente. Para as instruções de instalação, consulte:

- [Instalar o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instale o Azure CLI no Linux](/cli/azure/install-azure-cli-linux)
- [Instalar a CLI do Azure no macOS](/cli/azure/install-azure-cli-macos)

Depois de instalar o Azure PowerShell ou o Azure CLI, certifique-se de que faz sê-lo pela primeira vez. Para obter ajuda, consulte [Iniciar s-se-in - PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Iniciar sação - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

> [!IMPORTANT]
> Se estiver a utilizar o Azure CLI, certifique-se de que tem a versão 2.6 ou posterior. Os comandos mostrados neste tutorial não funcionarão se estiver a utilizar versões anteriores. Para verificar a sua versão instalada, utilize: `az --version` .

Está pronto para começar a aprender sobre modelos.

## <a name="create-your-first-template"></a>Criar o primeiro modelo

1. Código de estúdio visual aberto com a extensão de Ferramentas do Gestor de Recursos instaladas.
1. A partir do menu **Ficheiro,** selecione **Novo Ficheiro** para criar um novo ficheiro.
1. A partir do menu **'Ficheiro',** **selecione Guardar como**.
1. Nomeie o ficheiro _azuredeploy_ e selecione a extensão do ficheiro _json._ O nome completo do ficheiro é _azuredeploy.js._
1. Guarde o ficheiro para a sua estação de trabalho. Selecione um caminho que seja fácil de lembrar porque irá fornecer esse caminho mais tarde ao implementar o modelo.
1. Copiar e colar o seguinte JSON no ficheiro:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Aqui está o seu ambiente visual Studio Code:

    ![Modelo de arm visual studio código primeiro modelo](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Este modelo não implementa recursos. Estamos começando com um modelo em branco para que você possa se familiarizar com os passos para implementar um modelo, minimizando a chance de algo correr mal.

    O ficheiro JSON tem estes elementos:

    - `$schema`: Especifica a localização do ficheiro de esquema JSON. O ficheiro de esquema descreve as propriedades que estão disponíveis dentro de um modelo. Por exemplo, o esquema define `resources` como uma das propriedades válidas para um modelo. Não se preocupe que a data para o esquema é 2019-04-01. Esta versão de esquema está atualizada e inclui todas as funcionalidades mais recentes. A data do esquema não foi alterada porque não houve alterações de rutura desde a sua introdução.
    - `contentVersion`: Especifica a versão do modelo (tal como 1.0.0.0). Pode fornecer qualquer valor para este elemento. Utilize este valor para documentar alterações significativas no seu modelo. Ao utilizar recursos utilizando o modelo, este valor pode ser usado para garantir que o modelo certo está a ser utilizado.
    - `resources`: Contém os recursos que pretende utilizar ou atualizar. Atualmente, está vazio, mas vai adicionar recursos mais tarde.

1. Guarde o ficheiro.

Parabéns, criaste o teu primeiro modelo.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para começar a trabalhar com a Azure PowerShell/Azure CLI, inscreva-se com as suas credenciais Azure.

Selecione os separadores nas seguintes secções de código para escolher entre Azure PowerShell e Azure CLI. Os exemplos do CLI neste artigo são escritos para a concha bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se tiver várias subscrições do Azure, selecione a subscrição que pretende utilizar. Substitua `[SubscriptionID/SubscriptionName]` e os suportes quadrados `[]` por informações de subscrição:

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

Quando implementa um modelo, especifica um grupo de recursos que conterá os recursos. Antes de executar o comando de implementação, crie o grupo de recursos com a CLI do Azure ou o Azure PowerShell.

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

Para implementar o modelo, utilize o Azure CLI ou o Azure PowerShell. Utilize o grupo de recursos que criou. Dê um nome à implementação para que possa identificá-lo facilmente no histórico de implantação. Por conveniência, também crie uma variável que armazena o caminho para o arquivo do modelo. Esta variável torna mais fácil executar os comandos de implementação, porque não tem de reescrever o caminho sempre que implementa. Substitua `{provide-the-path-to-the-template-file}` e as chaves encaracoladas `{}` pelo caminho para o seu ficheiro de modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

O comando de implantação retorna os resultados. Procure `ProvisioningState` para ver se o destacamento foi bem sucedido.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![Estado de fornecimento powerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

![Estado de provisionamento do Azure CLI](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir do menu esquerdo, selecione **Grupos de Recursos.**

1. Selecione o grupo de recursos implementar no último procedimento. O nome predefinido é **myResourceGroup**. Não verá nenhum recurso implantado dentro do grupo de recursos.

1. Note-se no canto superior direito da visão geral, o estado da implantação é visualizado. Selecione **1 Conseguiu**.

   ![Ver estado de implantação](./media/template-tutorial-create-first-template/deployment-status.png)

1. Vê-se um histórico de implantação para o grupo de recursos. Selecione **otemplate em branco**.

   ![Selecione implantação](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Vê-se um resumo da implantação. Neste caso, não há muito para ver porque não foram mobilizados recursos. Mais tarde nesta série poderá ser útil rever o resumo do histórico de implementação. Note à esquerda, pode ver entradas, saídas e o modelo utilizado durante a implementação.

   ![Ver resumo de implementação](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se está a parar agora, é melhor apagar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Criou um modelo simples para implementar para O Azure. No próximo tutorial, irá adicionar uma conta de armazenamento ao modelo e implantá-la-á para o seu grupo de recursos.

> [!div class="nextstepaction"]
> [Adicionar recurso](template-tutorial-add-resource.md)
