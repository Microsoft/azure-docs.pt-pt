---
title: Encriptação de armazenamento do Azure para dados Inativos | Documentos da Microsoft
description: O armazenamento do Azure protege os dados ao encriptar automaticamente antes persisti-los para a cloud. Todos os dados no armazenamento do Azure são encriptados e desencriptados de forma transparente com a encriptação de AES de 256 bits e está em conformidade com a FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6eb7de7810ce23aed4031cca9f038da7149a6f9c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153095"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Encriptação de armazenamento do Azure para dados Inativos

O armazenamento do Azure encripta automaticamente os dados quando persisti-los para a cloud. A criptografia protege seus dados e para ajudá-lo a respeitar os compromissos de segurança e conformidade. Dados no armazenamento do Azure são encriptados e desencriptados de forma transparente através de 256 bits [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), um do bloco mais forte disponíveis de cifras e está em conformidade com a FIPS 140-2. Encriptação de armazenamento do Azure é semelhante à criptografia de disco BitLocker no Windows.

Encriptação de armazenamento do Azure está ativada para todas as contas de armazenamento de novas e existentes e não pode ser desativada. Porque os seus dados estão protegidos por predefinição, não precisa de modificar as suas aplicações para tirar partido da encriptação de armazenamento do Azure ou de código. 

Contas de armazenamento são encriptadas independentemente do seu modelo de implementação (Azure Resource Manager ou clássico) ou o escalão de desempenho (standard ou premium). Todas as opções de redundância de armazenamento do Azure suportam encriptação, e todas as cópias de uma conta de armazenamento são encriptadas. Todos os recursos de armazenamento do Azure são encriptados, incluindo blobs, discos, ficheiros, filas e tabelas. Todos os metadados do objeto também são encriptados.

Encriptação não afeta o desempenho de armazenamento do Azure. Não existe nenhum custo adicional para a encriptação de armazenamento do Azure.

Para obter mais informações sobre os módulos criptográficos subjacente a encriptação de armazenamento do Azure, consulte [API de criptografia: Próxima geração](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Gestão de chaves

Também pode utilizar chaves geridas pela Microsoft para a encriptação da conta de armazenamento ou pode gerir a encriptação com suas próprias chaves, em conjunto com o Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Chaves geridas pela Microsoft

Por predefinição, a sua conta de armazenamento utiliza as chaves de encriptação gerida pela Microsoft. Pode ver as definições de encriptação para a sua conta de armazenamento na **encriptação** secção a [portal do Azure](https://portal.azure.com), conforme mostrado na imagem seguinte.

![Ver a conta criptografada com chaves geridas pela Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Chaves geridas pelo cliente

Pode gerir a encriptação de armazenamento do Azure com chaves geridas pelo cliente. Chaves geridas pelo cliente dão-lhe mais flexibilidade para criar, girar, desative e revogar os controlos de acesso. Também é possível auditar as chaves de encriptação utilizadas para proteger os seus dados. 

Utilize o Azure Key Vault para gerir as chaves e auditar a utilização de chave. Pode criar suas próprias chaves e armazená-las num cofre de chaves, ou pode utilizar as APIs de Cofre de chaves do Azure para gerar chaves. A conta de armazenamento e o Cofre de chaves têm de estar na mesma região, mas podem estar em diferentes subscrições. Para obter mais informações sobre o Azure Key Vault, consulte [o que é o Azure Key Vault?](../../key-vault/key-vault-overview.md).

Para revogar o acesso às chaves geridas pelo cliente, consulte [PowerShell do Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [CLI do Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso com eficiência bloqueia o acesso a todos os dados na conta de armazenamento, como a chave de encriptação não está acessível pelo armazenamento do Azure.

Para saber como utilizar chaves geridas pelo cliente com o armazenamento do Azure, veja um dos seguintes artigos:

- [Configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure no portal do Azure](storage-encryption-keys-portal.md)
- [Configurar chaves geridas pelo cliente para a encriptação de armazenamento do Azure a partir do PowerShell](storage-encryption-keys-powershell.md)
- [Utilizar chaves geridas pelo cliente com encriptação de armazenamento do Azure da CLI do Azure](storage-encryption-keys-cli.md)

> [!NOTE]  
> Chaves geridas pelo cliente não são suportadas para [discos geridos do Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Encriptação de armazenamento do Azure em comparação com a encriptação de disco

Com a encriptação de armazenamento do Azure, todas as contas de armazenamento do Azure e os recursos que contêm são encriptados, incluindo os blobs de página que fazer uma cópia de discos de máquina virtual do Azure. Além disso, os discos da máquina virtual do Azure podem ser encriptados com [do Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). O Azure Disk Encryption utiliza o padrão da indústria [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para fornecer soluções de encriptação baseada no sistema operativo que são integradas no Azure Key Vault.

## <a name="next-steps"></a>Passos Seguintes

- [O que é o Azure Key Vault?](../../key-vault/key-vault-overview.md)
