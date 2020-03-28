---
title: Utilizar referência de modelo
description: Utilize a referência do modelo do Gestor de Recursos Azure para criar um modelo.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b742982121a20a2b057eba4211584b0386dde411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373429"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Tutorial: Utilize a referência do modelo ARM

Saiba como encontrar a informação do esquema do modelo e use a informação para criar modelos de Gestor de Recursos Azure (ARM).

Neste tutorial, vai utilizar um modelo de base dos modelos de Início Rápido do Azure. Usando documentação de referência de modelo, personaliza o modelo.

![Conta de armazenamento de ponto de referência do gestor de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Compreender o modelo
> * Encontre a referência do modelo
> * Editar o modelo
> * Implementar o modelo

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [o Use Visual Studio Code para criar modelos ARM](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

[Os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/) é um repositório para modelos ARM. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste início rápido chama-se [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso de conta de Armazenamento do Azure.

1. A partir do Código do Estúdio Visual, selecione **File**>**Open File**.
1. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Selecione **Abrir** para abrir o ficheiro.
1. Selecione **File**>**Save As** para guardar o ficheiro como **azuredeploy.json** para o seu computador local.

## <a name="understand-the-schema"></a>Compreender o esquema

1. No VS Code, feche o modelo até ao nível da raiz. Tem a estrutura mais simples com os seguintes elementos:

    ![Estrutura mais simples do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: especifique a localização do ficheiro de esquema JSON que descreve a versão da linguagem de modelos.
    * **contentVersion**: especifique qualquer valor para este elemento, para documentar alterações significativas ao seu modelo.
    * **parâmetros**: especifique os valores que são fornecidos quando a implementação é executada, para personalizar a implementação de recursos.
    * **variáveis**: especifique os valores que são utilizados como fragmentos JSON no modelo, para simplificar as expressões de linguagem de modelos.
    * **recursos**: especifique os tipos de recurso que são implementados ou atualizados num grupo de recursos.
    * **saídas**: especifique os valores que são devolvidos após a implementação.

1. Expanda os **recursos**. Existe um recurso de `Microsoft.Storage/storageAccounts` definido.

    ![Definição da conta de armazenamento do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

## <a name="find-the-template-reference"></a>Encontre a referência do modelo

1. Navegue na referência do [modelo Azure.](https://docs.microsoft.com/azure/templates/)
1. No Filtro por caixa de **título,** introduza as contas de armazenamento e selecione as **primeiras**Contas de **Armazenamento** em referência **> Armazenamento**.

    ![Conta de armazenamento de referência do modelo do Resource Manager](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Um fornecedor de recursos geralmente tem várias versões API:

    ![Versões de conta de armazenamento de referência do gestor de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Selecione **todos os recursos** em **armazenamento** a partir do painel esquerdo. Esta página lista os tipos de recursos e versões do fornecedor de recursos de armazenamento. Recomenda-se utilizar as versões API mais recentes para os tipos de recursos definidos no seu modelo.

    ![Modelo de gestor de recursos modelo de armazenamento de contas versões](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Selecione a versão mais recente do tipo de recursos **da Conta de armazenamento.**  A versão mais recente é **2019-06-01** quando este artigo é escrito.

1. Esta página lista os detalhes do tipo de recursos da Conta de armazenamento.  Por exemplo, lista os valores permitidos para o objeto **Sku.** Há mais skus do que o que está listado no modelo de quickstart que abriu mais cedo. Você pode personalizar o modelo de arranque rápido para incluir todos os tipos de armazenamento disponíveis.

    ![Conta de armazenamento de referência do gestor de recursos skus](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Editar o modelo

A partir do Código do Estúdio Visual, adicione os tipos de conta de armazenamento adicionais, como mostrado na seguinte imagem:

![Recursos recursos de conta de armazenamento de modelo de gestor de recursos](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Implementar o modelo

Veja a secção [Implementar o modelo](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) no guia de introdução do Visual Studio Code para o procedimento de implementação. Quando implementar o modelo, especifique o parâmetro **de armazenamentoAccountType** com um valor acrescentado recentemente acrescentado, por exemplo, **Premium_ZRS**. A implementação falharia se usasse o modelo original de arranque rápido porque **Premium_ZRS** não era um valor permitido.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar a referência de modelo para personalizar um modelo existente. Para saber mais sobre como criar várias instâncias de contas de armazenamento, veja:

> [!div class="nextstepaction"]
> [Criar múltiplas instâncias](./template-tutorial-create-multiple-instances.md)
