---
title: Criptografia de armazenamento do Azure para dados em repouso | Microsoft Docs
description: O armazenamento do Azure protege seus dados criptografando-os automaticamente antes de mantê-los para a nuvem. Todos os dados em um armazenamento do Azure são criptografados e descriptografados de forma transparente usando a criptografia AES de 256 bits e são compatíveis com o FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673053"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Criptografia de armazenamento do Azure para dados em repouso

O armazenamento do Azure criptografa automaticamente seus dados ao mantê-los para a nuvem. A criptografia protege seus dados e para ajudá-lo a atender aos compromissos de segurança e conformidade da organização. Os dados no armazenamento do Azure são criptografados e descriptografados de forma transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure é semelhante à criptografia BitLocker no Windows.

A criptografia de armazenamento do Azure está habilitada para todas as contas de armazenamento novas e existentes e não pode ser desabilitada. Como os dados são protegidos por padrão, você não precisa modificar seu código ou aplicativos para tirar proveito da criptografia de armazenamento do Azure. 

As contas de armazenamento são criptografadas independentemente de seu nível de desempenho (Standard ou Premium) ou modelo de implantação (Azure Resource Manager ou clássico). Todas as opções de redundância de armazenamento do Azure dão suporte à criptografia e todas as cópias de uma conta de armazenamento são criptografadas. Todos os recursos de armazenamento do Azure são criptografados, incluindo BLOBs, discos, arquivos, filas e tabelas. Todos os metadados de objeto também são criptografados.

A criptografia não afeta o desempenho do armazenamento do Azure. Não há nenhum custo adicional para a criptografia de armazenamento do Azure.

Para obter mais informações sobre os módulos de criptografia subjacentes à criptografia de armazenamento do Azure, consulte API [Cryptography: Próxima geração @ no__t-0.

## <a name="key-management"></a>Gestão de chaves

Você pode contar com chaves gerenciadas pela Microsoft para a criptografia da sua conta de armazenamento ou pode gerenciar a criptografia com suas próprias chaves, junto com Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Chaves gerenciadas pela Microsoft

Por padrão, sua conta de armazenamento usa chaves de criptografia gerenciadas pela Microsoft. Você pode ver as configurações de criptografia para sua conta de armazenamento na seção **criptografia** do [portal do Azure](https://portal.azure.com), conforme mostrado na imagem a seguir.

![Exibir conta criptografada com chaves gerenciadas pela Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Chaves gerenciadas pelo cliente

Você pode gerenciar a criptografia de armazenamento do Azure com chaves gerenciadas pelo cliente. Chaves gerenciadas pelo cliente oferecem mais flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados. 

Use Azure Key Vault para gerenciar suas chaves e auditar o uso da chave. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. A conta de armazenamento e o cofre de chaves devem estar na mesma região, mas podem estar em assinaturas diferentes. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../../key-vault/key-vault-overview.md).

Para revogar o acesso às chaves gerenciadas pelo cliente, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso efetivamente bloqueia o acesso a todos os dados na conta de armazenamento, pois a chave de criptografia é inacessível pelo armazenamento do Azure.

Para saber como usar chaves gerenciadas pelo cliente com o armazenamento do Azure, consulte um destes artigos:

- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves geridas pelo cliente para a encriptação do Armazenamento do Microsoft Azure no PowerShell](storage-encryption-keys-powershell.md)
- [Usar chaves gerenciadas pelo cliente com a criptografia de armazenamento do Azure de CLI do Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Quando você transfere uma assinatura de um diretório do Azure AD para outro, as identidades gerenciadas não são atualizadas e as chaves gerenciadas pelo cliente podem não funcionar mais. Para obter mais informações, consulte **transferindo uma assinatura entre diretórios do Azure ad** em [perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

> [!NOTE]  
> Não há suporte para chaves gerenciadas pelo cliente para [Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Criptografia de armazenamento do Azure versus criptografia de disco

Com a criptografia de armazenamento do Azure, todas as contas de armazenamento do Azure e os recursos que elas contêm são criptografados, incluindo os blobs de páginas que fazem backup de discos de máquina virtual do Azure. Além disso, os discos de máquina virtual do Azure podem ser criptografados com [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption usa o [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) padrão do setor no Windows e [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer soluções de criptografia baseadas no sistema operacional integradas ao Azure Key Vault.

## <a name="next-steps"></a>Passos seguintes

- [O que é Azure Key Vault?](../../key-vault/key-vault-overview.md)
