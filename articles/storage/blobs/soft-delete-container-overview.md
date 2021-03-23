---
title: Excluir suave para recipientes (pré-visualização)
titleSuffix: Azure Storage
description: A eliminação suave para recipientes (pré-visualização) protege os seus dados de modo a que possa recuperar mais facilmente os seus dados quando estes são erroneamente modificados ou eliminados por uma aplicação ou por outro utilizador de conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 54e703b096ea4e3572a6fc00aa6b7b2b99c4bcad
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800749"
---
# <a name="soft-delete-for-containers-preview"></a>Excluir suave para recipientes (pré-visualização)

A eliminação suave para recipientes (pré-visualização) protege os seus dados de serem acidental ou maliciosamente eliminados. Quando a eliminação recuperável de contentores estiver ativada para uma conta de armazenamento, todos os contentores e conteúdo eliminado serão mantidos no Armazenamento do Microsoft Azure durante o período que especificar. Durante o período de retenção, poderá restaurar contentores eliminados anteriormente. O restauro de um contentor restaura quaisquer blobs nesse contentor eliminado.

Para uma proteção final para os seus dados blob, a Microsoft recomenda que se coloquem as seguintes funcionalidades de proteção de dados:

- Recipiente apagar suavemente, para restaurar um recipiente que foi eliminado. Para aprender a permitir a eliminação suave do recipiente, consulte [Ativar e gerir a eliminação suave para recipientes](soft-delete-container-enable.md).
- Versão blob, para manter automaticamente as versões anteriores de uma bolha. Quando a versão blob estiver ativada, pode restaurar uma versão anterior de uma bolha para recuperar os seus dados se for erroneamente modificada ou eliminada. Para aprender a permitir a versão blob, consulte [Ativar e gerir a versão blob](versioning-enable.md).
- Blob soft delete, para restaurar uma bolha ou versão que foi eliminada. Para aprender a permitir a eliminação suave do blob, consulte [Ativar e gerir a eliminação suave para bolhas](soft-delete-blob-enable.md).

> [!IMPORTANT]
> A eliminação suave do recipiente encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="how-container-soft-delete-works"></a>Como o recipiente suave apagar funciona

Quando ativar a eliminação suave do recipiente, pode especificar um período de retenção para recipientes eliminados que se encontra entre 1 e 365 dias. O período de retenção por defeito é de 7 dias. Durante o período de retenção, pode recuperar um recipiente apagado chamando a operação **do Recipiente Restaurar.**

Quando restaurar um recipiente, as bolhas do recipiente e quaisquer versões blob também são restauradas. No entanto, só pode utilizar a eliminação macia do recipiente para restaurar as bolhas se o próprio recipiente for eliminado. Para restaurar uma bolha apagada quando o seu recipiente-mãe não tiver sido apagado, deve utilizar a versão de exclusão macia ou bolha.

> [!WARNING]
> A eliminação suave do recipiente só pode restaurar recipientes inteiros e as bolhas que continham no momento da eliminação. Não é possível restaurar uma bolha apagada dentro de um recipiente utilizando uma eliminação macia do recipiente.

O seguinte diagrama mostra como um recipiente eliminado pode ser restaurado quando o contentor é eliminado suavemente:

:::image type="content" source="media/soft-delete-container-overview/container-soft-delete-diagram.png" alt-text="Diagrama que mostra como um recipiente apagado pode ser restaurado":::

Quando restaurar um recipiente, pode restaurá-lo ao seu nome original se esse nome não tiver sido reutilizado. Se o nome original do recipiente tiver sido utilizado, poderá restaurar o recipiente com um novo nome.

Após o período de retenção ter expirado, o recipiente é permanentemente eliminado do Azure Storage e não pode ser recuperado. O relógio começa no período de retenção no ponto em que o recipiente é apagado. Pode alterar o período de retenção a qualquer momento, mas tenha em mente que um período de retenção atualizado se aplica apenas aos recipientes recém-eliminados. Os recipientes previamente eliminados serão permanentemente eliminados com base no período de retenção que estava em vigor no momento em que o contentor foi eliminado.

A desativação do recipiente de eliminação suave não resulta na supressão permanente dos recipientes que foram previamente eliminados. Os recipientes apagados serão permanentemente eliminados no termo do período de retenção que estava em vigor no momento da sua perda do contentor.

> [!IMPORTANT]
> A eliminação suave do contentor não protege contra a supressão de uma conta de armazenamento, mas apenas contra a supressão de contentores nessa conta. Para proteger uma conta de armazenamento da supressão, configuure um bloqueio no recurso da conta de armazenamento. Para obter mais informações sobre o bloqueio dos recursos do Azure Resource Manager, consulte [os recursos do Lock para evitar alterações inesperadas.](../../azure-resource-manager/management/lock-resources.md)

## <a name="about-the-preview"></a>Sobre a pré-visualização

O contentor soft delete está disponível em pré-visualização em todas as regiões do Azure.

Versão 2019-12-12 ou superior ao Azure Storage REST API suporta a eliminação suave do contentor.

### <a name="storage-account-support"></a>Suporte à conta de armazenamento

O contentor soft delete está disponível para os seguintes tipos de contas de armazenamento:

- Contas de armazenamento v2 e v1 para fins gerais
- Contas de armazenamento de blob bloqueada
- Contas do Blob Storage

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
