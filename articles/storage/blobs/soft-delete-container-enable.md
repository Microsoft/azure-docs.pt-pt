---
title: Ative e gere a eliminação suave para recipientes (pré-visualização)
titleSuffix: Azure Storage
description: Ativar a eliminação suave do recipiente (pré-visualização) para recuperar mais facilmente os seus dados quando estes são erroneamente modificados ou eliminados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2097c1743e07b5563bc75d3d1cce48aa11b98e5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216348"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Ative e gere a eliminação suave para recipientes (pré-visualização)

O recipiente de exclusão suave (pré-visualização) protege os seus dados de serem modificados ou eliminados acidentalmente ou erroneamente. Quando a eliminação suave do contentor estiver ativada para uma conta de armazenamento, um recipiente e o seu conteúdo podem ser recuperados após a sua eliminação, num período de retenção que especifique.

Se houver a possibilidade de os seus dados poderem ser acidentalmente modificados ou eliminados por uma aplicação ou outro utilizador de uma conta de armazenamento, a Microsoft recomenda que se interlige com a eliminação suave do contentor. Este artigo mostra como permitir a eliminação suave para recipientes. Para obter mais detalhes sobre a eliminação suave do recipiente, incluindo como se registar para a pré-visualização, consulte [Soft delete para recipientes (pré-visualização)](soft-delete-container-overview.md).

Para a proteção de dados de ponta a ponta, a Microsoft recomenda que também ative a eliminação suave para bolhas e versões Blob. Para aprender a permitir também a eliminação suave para bolhas, consulte [Ativar e gerir a eliminação suave para bolhas](soft-delete-blob-enable.md). Para aprender a permitir a versão blob, consulte [a versão Blob](versioning-overview.md).

> [!IMPORTANT]
>
> A eliminação suave do recipiente encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="enable-container-soft-delete"></a>Permitir a eliminação suave do recipiente

Pode ativar ou desativar o contentor para a conta de armazenamento a qualquer momento, utilizando o portal Azure ou o modelo do Gestor de Recursos Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para permitir a eliminação suave do recipiente para a sua conta de armazenamento utilizando o portal Azure, siga estes passos:

1. No [portal do Azure](https://portal.azure.com/), navegue para a sua conta de armazenamento.
1. Localizar as definições **de Proteção de Dados** no **serviço Blob**.
1. Deite o **recipiente de apagar suavemente** a propriedade para *ativado*.
1. De acordo com **as políticas de retenção**, especifique quanto tempo os recipientes de sossagem são retidos pelo Azure Storage.
1. Guarde as alterações.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Screenshot mostrando como permitir a eliminação suave do recipiente no portal Azure":::

# <a name="template"></a>[Modelo](#tab/template)

Para permitir a eliminação suave do recipiente com um modelo de Gestor de Recursos Azure, crie um modelo que define a propriedade **containerDeleteRetentionPolicy.** Os passos seguintes descrevem como criar um modelo no portal Azure.

1. No portal Azure, escolha **Criar um recurso.**
1. Em **Search the Marketplace**, **digitar a implementação do modelo** e, em seguida, premir **ENTER**.
1. Escolha a **implementação do modelo,** escolha **Criar** e, em seguida, escolha **Construir o seu próprio modelo no editor**.
1. No editor de modelo, cole no seguinte JSON. Substitua o `<account-name>` espaço reservado pelo nome da sua conta de armazenamento.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. Especifique o período de retenção. O valor predefinido é 7.
1. Guarde o modelo.
1. Especifique o grupo de recursos da conta e, em seguida, escolha o botão **Review + create** para implantar o modelo e permitir a eliminação suave do recipiente.

## <a name="view-soft-deleted-containers"></a>Ver recipientes comvidade

Quando a eliminação suave estiver ativada, pode ver recipientes apagados suavemente no portal Azure. Os recipientes apagados são visíveis durante o período de retenção especificado. Após o termo do período de retenção, um recipiente apagado é permanentemente eliminado e já não é visível.

Para ver recipientes comvidade no portal Azure, siga estes passos:

1. Navegue na sua conta de armazenamento no portal Azure e veja a lista dos seus contentores.
1. Alternar o interruptor de recipientes eliminados para incluir recipientes eliminados na lista.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Screenshot mostrando como ver recipientes suaves eliminados no portal Azure":::

## <a name="restore-a-soft-deleted-container"></a>Restaurar um recipiente de apagamento suave

Pode restaurar um recipiente desausação e o seu conteúdo dentro do período de retenção. Para restaurar um recipiente de apagamento suave no portal Azure, siga estes passos:

1. Navegue na sua conta de armazenamento no portal Azure e veja a lista dos seus contentores.
1. Exiba o menu de contexto para o recipiente que deseja restaurar e escolha **Undelete** no menu.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Screenshot mostrando como restaurar um recipiente de apagamento suave no portal Azure":::

## <a name="next-steps"></a>Passos seguintes

- [Excluir suave para recipientes (pré-visualização)](soft-delete-container-overview.md)
- [Eliminação recuperável para blobs](soft-delete-blob-overview.md)
- [Versão blob](versioning-overview.md)
