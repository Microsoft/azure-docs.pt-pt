---
title: Traga a sua própria chave para o Apache Kafka no HDInsight do Azure (pré-visualização)
description: Este artigo descreve como utilizar a sua própria chave do Azure Key Vault para encriptar dados armazenados no Apache Kafka no HDInsight do Azure.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b5f7c472c8ebd60d8e7f928534834c9672fe3b14
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471312"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Traga a sua própria chave para o Apache Kafka no HDInsight do Azure (pré-visualização)

O Azure HDInsight inclui suporte de Bring Your Own Key (BYOK) para o Apache Kafka. Esta capacidade permite-lhe ter e gerir as chaves utilizadas para encriptar dados inativos. 

Todos os discos geridos no HDInsight são protegidos com encriptação de serviço de armazenamento do Azure (SSE). Por predefinição, os dados desses discos são encriptados utilizando chaves geridas pela Microsoft. Se ativar o BYOK, fornecer a chave de encriptação para o HDInsight utilizar e geri-lo com o Azure Key Vault. 

A criptografia de BYOK é um processo de um passo processado durante a criação do cluster sem custos adicionais. Tudo o que precisa fazer é registrar o HDInsight como uma identidade gerida com o Azure Key Vault e adicione a chave de encriptação quando cria o cluster.

Todas as mensagens para o cluster de Kafka (incluindo réplicas mantidas pelo Kafka) são encriptadas com uma chave de encriptação do simétrica dados (DEK). O DEK é protegido com a chave de encriptação de chaves (KEK) do seu Cofre de chaves. Os processos de criptografia e descriptografia são manipulados completamente pelo Azure HDInsight. 

Pode utilizar o portal do Azure ou a CLI do Azure para girar em segurança as chaves no Cofre de chaves. Quando uma chave roda, o cluster HDInsight Kafka é iniciado com a nova chave numa questão de minutos. Ative as funcionalidades de proteção de chaves "Eliminar de forma recuperável" proteger contra ransomware cenários e eliminação acidental. Cofres de chaves sem esta funcionalidade de proteção não são suportados.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>Começar com o BYOK
Para criar um BYOK ativado o cluster do Kafka, iremos percorrer os passos seguintes:
1. Criar identidades geridas para recursos do Azure
2. Configurar o Azure Key Vault e chaves
3. Criar cluster do HDInsight Kafka com o BYOK ativada

## <a name="create-managed-identities-for-azure-resources"></a>Criar identidades geridas para recursos do Azure

   Para autenticar para o Key Vault, criar uma identidade gerida atribuído ao utilizador a utilizar o [portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), ou [ CLI do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Para obter mais informações sobre como geridos de identidades de trabalho no Azure HDInsight, consulte [geridos identidades no Azure HDInsight](../hdinsight-managed-identities.md). Enquanto o Azure Active directory é necessário para identidades geridas e a BYOK ao Kafka, o pacote de segurança da empresa (ESP) não é um requisito. Certifique-se de que guarde o ID de recurso de identidade gerida para quando adicioná-lo para a política de acesso do Cofre de chaves.

   ![Criar utilizador atribuído a identidade gerida no portal do Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>O Cofre de chaves e as chaves de configuração

   HDInsight suporta apenas o Azure Key Vault. Se tiver o seu próprio Cofre de chaves, pode importar as suas chaves no Azure Key Vault. Lembre-se de que as chaves têm de ter "Eliminar de forma recuperável". A funcionalidade "Eliminação de forma recuperável" está disponível através de REST, .NET /C#, interfaces de PowerShell e CLI do Azure.

   1. Para criar um novo cofre de chaves, siga os [do Azure Key Vault](../../key-vault/key-vault-overview.md) início rápido. Para obter mais informações sobre como importar chaves existentes, visite [sobre chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Ative o "eliminação de forma recuperável" no Cofre de chaves utilizando o [atualização do Cofre de chaves de az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) comando da cli.
        ' ' Atualização do Cofre de chaves do azure CLI az - nome <Key Vault Name> – enable-eliminação de forma recuperável
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.
        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save** 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
