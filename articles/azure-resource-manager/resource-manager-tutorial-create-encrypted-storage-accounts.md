---
title: Utilizar a referência de modelo do Azure Resource Manager | Documentos da Microsoft
description: Utilize a referência de modelo do Azure Resource Manager para criar um modelo para a implementação de uma conta de armazenamento encriptado.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 68199083369018cab1c9f83dd6781653a8cae37c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849300"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>Tutorial: Utilizar a referência de modelo do Azure Resource Manager

Saiba como encontrar as informações de esquema do modelo e utilizar as informações para criar modelos do Azure Resource Manager.

Neste tutorial, vai utilizar um modelo de base dos modelos de Início Rápido do Azure. Com a documentação de referência do modelo, personalize o modelo para criar uma conta de Armazenamento encriptada.

![Conta de armazenamento encriptada de implementar a referência de modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Abrir um modelo de Início Rápido
> * Compreender o modelo
> * Encontre a referência do modelo
> * Editar o modelo
> * Implementar o modelo

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* [Visual Studio Code](https://code.visualstudio.com/) com [extensão Ferramentas do Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Abrir um modelo de Início Rápido

Os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/) são um repositório de modelos do Resource Manager. Em vez de criar um modelo do zero, pode encontrar um modelo de exemplo e personalizá-lo. O modelo utilizado neste início rápido chama-se [Criar uma conta de armazenamento padrão](https://azure.microsoft.com/resources/templates/101-storage-account-create/). O modelo define um recurso de conta de Armazenamento do Azure.

1. No Visual Studio Code, selecione **Ficheiro**>**Abrir Ficheiro**.
2. em **Nome de ficheiro**, cole o seguinte URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selecione **Abrir** para abrir o ficheiro.
4. Selecione **Ficheiro**>**Guardar Como** para guardar o ficheiro como **azuredeploy.json** no computador local.

## <a name="understand-the-schema"></a>Compreender o esquema

1. No VS Code, feche o modelo até ao nível da raiz. Tem a estrutura mais simples com os seguintes elementos:

    ![Estrutura mais simples do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: especifique a localização do ficheiro de esquema JSON que descreve a versão da linguagem de modelos.
    * **contentVersion**: especifique qualquer valor para este elemento, para documentar alterações significativas ao seu modelo.
    * **parâmetros**: especifique os valores que são fornecidos quando a implementação é executada, para personalizar a implementação de recursos.
    * **variáveis**: especifique os valores que são utilizados como fragmentos JSON no modelo, para simplificar as expressões de linguagem de modelos.
    * **recursos**: especifique os tipos de recurso que são implementados ou atualizados num grupo de recursos.
    * **saídas**: especifique os valores que são devolvidos após a implementação.

2. Expanda os **recursos**. Existe um recurso de `Microsoft.Storage/storageAccounts` definido. O modelo cria uma conta de Armazenamento não encriptada.

    ![Definição da conta de armazenamento do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>Encontre a referência do modelo

1. Navegue até [referência de modelo do Azure](https://docs.microsoft.com/azure/templates/).
2. Na **filtrar por título** , introduza **contas de armazenamento**.
3. Selecione **referência de referência/modelo/armazenamento/&lt;versão > / contas de armazenamento** conforme mostrado na captura de ecrã seguinte:

    ![Conta de armazenamento de referência do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    Se não sabe qual é a versão para escolher, utilize a versão mais recente.

4. Encontre as informações de definição relacionadas com a encriptação.  

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Na mesma página Web, a descrição seguinte confirma que o objeto `encryption` é utilizado para criar uma conta de armazenamento encriptada.

    ![Encriptação da conta de armazenamento de referência do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    E existem duas formas de gerir a chave de encriptação. Pode utilizar chaves de encriptação geridas pela Microsoft com a Encriptação do Serviço de Armazenamento, ou pode utilizar as suas próprias chaves de encriptação. Para simplificar este tutorial, utilize a opção `Microsoft.Storage`, para que não tenha de criar um Azure Key Vault.

    ![Objeto de encriptação da conta de armazenamento de referência do modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    O objeto de encriptação deve ter o seguinte aspeto:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>Editar o modelo

A partir do Visual Studio Code, modifique o modelo para que o elemento de recursos se pareça com:

![Recursos de conta de armazenamento encriptada de modelo do Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Implementar o modelo

Veja a secção [Implementar o modelo](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) no guia de introdução do Visual Studio Code para o procedimento de implementação.

A captura de ecrã seguinte mostra o comando da CLI para listar a conta de armazenamento recentemente criada, o que indica que a encriptação foi ativada para o armazenamento de blobs.

![Conta de armazenamento encriptada do Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.  Verá um total de seis recursos no grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar a referência de modelo para personalizar um modelo existente. Para saber mais sobre como criar várias instâncias de contas de armazenamento, veja:

> [!div class="nextstepaction"]
> [Criar múltiplas instâncias](./resource-manager-tutorial-create-multiple-instances.md)
