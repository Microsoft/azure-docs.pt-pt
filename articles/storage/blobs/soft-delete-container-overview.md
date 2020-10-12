---
title: Excluir suave para recipientes (pré-visualização)
titleSuffix: Azure Storage
description: A eliminação suave para recipientes (pré-visualização) protege os seus dados de modo a que possa recuperar mais facilmente os seus dados quando estes são erroneamente modificados ou eliminados por uma aplicação ou por outro utilizador de conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 3c6aa408ab3e89c367fca0701773b35fb9b41bb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460803"
---
# <a name="soft-delete-for-containers-preview"></a>Excluir suave para recipientes (pré-visualização)

A eliminação suave para recipientes (pré-visualização) protege os seus dados de serem modificados ou eliminados acidentalmente ou erroneamente. Quando a eliminação suave do contentor está ativada para uma conta de armazenamento, qualquer recipiente apagado e o seu conteúdo são retidos no Azure Storage durante o período que especificar. Durante o período de retenção, pode restaurar os recipientes previamente eliminados e quaisquer bolhas dentro deles.

Para uma proteção final para os seus dados blob, a Microsoft recomenda que se coloquem as seguintes funcionalidades de proteção de dados:

- Eliminar suavemente o recipiente, para proteger contra a eliminação acidental ou a substituição de um recipiente. Para aprender a permitir a eliminação suave do recipiente, consulte [Ativar e gerir a eliminação suave para recipientes](soft-delete-container-enable.md).
- Blob soft delete, para proteger contra eliminação acidental ou substituição de uma bolha individual. Para aprender a permitir a eliminação suave da bolha, consulte [Soft Delete para bolhas](soft-delete-blob-overview.md).
- Versão blob, para manter automaticamente as versões anteriores de uma bolha. Quando a versão blob estiver ativada, pode restaurar uma versão anterior de uma bolha para recuperar os seus dados se for erroneamente modificada ou eliminada. Para aprender a permitir a versão blob, consulte [Ativar e gerir a versão blob](versioning-enable.md).

> [!WARNING]
> A eliminação de uma conta de armazenamento não pode ser desfeita. A eliminação suave não protege contra a supressão de uma conta de armazenamento. Para evitar a supressão acidental de uma conta de armazenamento, configuure um bloqueio **Não-TDelete** no recurso da conta de armazenamento. Para obter mais informações sobre o bloqueio dos recursos do Azure, consulte [os recursos do Lock para evitar alterações inesperadas](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>Como o recipiente suave apagar funciona

Quando ativar a eliminação suave do recipiente, pode especificar um período de retenção para recipientes eliminados que se encontra entre 1 e 365 dias. O período de retenção por defeito é de 7 dias. Durante o período de retenção, pode recuperar um recipiente apagado ligando para a operação **do Recipiente Undelete.**

Quando restaurar um recipiente, pode restaurá-lo ao seu nome original se esse nome não tiver sido reutilizado. Se o nome original do recipiente tiver sido utilizado, poderá restaurar o recipiente com um novo nome.

Após o período de retenção ter expirado, o recipiente é permanentemente eliminado do Azure Storage e não pode ser recuperado. O relógio começa no período de retenção no ponto em que o recipiente é apagado. Pode alterar o período de retenção a qualquer momento, mas tenha em mente que um período de retenção atualizado se aplica apenas aos recipientes recém-eliminados. Os recipientes previamente eliminados serão permanentemente eliminados com base no período de retenção que estava em vigor no momento em que o contentor foi eliminado.

A desativação do recipiente de eliminação suave não resulta na supressão permanente dos recipientes que foram previamente eliminados. Os recipientes apagados serão permanentemente eliminados no termo do período de retenção que estava em vigor no momento da sua perda do contentor.

## <a name="about-the-preview"></a>Sobre a pré-visualização

O contentor soft delete está disponível em pré-visualização em todas as regiões públicas de Azure.

> [!IMPORTANT]
> A pré-visualização de eliminação suave do recipiente destina-se apenas à não utilização da produção. Os contratos de serviços de produção (SLAs) não estão atualmente disponíveis.

Versão 2019-12-12 e superior do Azure Storage REST API suporta a eliminação suave do contentor.

### <a name="storage-account-support"></a>Suporte à conta de armazenamento

O contentor soft delete está disponível para os seguintes tipos de contas de armazenamento:

- Contas de armazenamento v2 para fins gerais
- Contas de armazenamento de blob bloqueada
- Contas do Blob Storage

Se a sua conta de armazenamento for uma conta V1 para fins gerais, utilize o portal Azure para atualizar para uma conta V2 para fins gerais. Para obter mais informações sobre contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](../common/storage-account-overview.md).

As contas de armazenamento com um espaço hierárquico habilitado para uso com Azure Data Lake Storage Gen2 também são suportadas.

### <a name="register-for-the-preview"></a>Registre-se para a pré-visualização

Para se inscrever na pré-visualização do recipiente de exclusão suave, utilize o PowerShell ou o Azure CLI para apresentar um pedido de registo da funcionalidade com a sua subscrição. Após a aprovação do seu pedido, pode permitir a eliminação suave do contentor com quaisquer novas ou existentes contas de armazenamento de blob ou bloco premium.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para se registar no PowerShell, ligue para o comando [Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para se registar no Azure CLI, ligue para o comando [de registo de recurso az.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Verifique o estado do seu registo

Para verificar o estado do seu registo, utilize o PowerShell ou o Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para verificar o estado do seu registo com o PowerShell, ligue para o comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para verificar o estado da sua inscrição com o Azure CLI, ligue para o comando [de recurso az.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Preços e faturação

Não existe qualquer carga adicional para permitir a eliminação suave do recipiente. Os dados em recipientes eliminados suaves são faturados à mesma taxa que os dados ativos.

## <a name="next-steps"></a>Passos seguintes

- [Configure recipiente de exclusão suave](soft-delete-container-enable.md)
- [Eliminação recuperável para blobs](soft-delete-blob-overview.md)
- [Versão blob](versioning-overview.md)
