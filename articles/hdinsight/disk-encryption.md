---
title: Criptografia de disco de chave gerenciada pelo cliente para o Azure HDInsight
description: Este artigo descreve como usar sua própria chave de criptografia de Azure Key Vault para criptografar dados armazenados em discos gerenciados em clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: 2c015db828bcbfa8b26f519b3a4707b5ec69b8f3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982500"
---
# <a name="customer-managed-key-disk-encryption"></a>Criptografia de disco de chave gerenciada pelo cliente

O Azure HDInsight dá suporte a chaves gerenciadas pelo cliente, também conhecidas como criptografia de Bring Your Own Key (BYOK) para dados em discos gerenciados e discos de recursos anexados a VMs do cluster HDInsight. Esse recurso permite que você use Azure Key Vault para gerenciar as chaves de criptografia que protegem dados em repouso em seus clusters HDInsight. Seus clusters podem ter uma ou mais contas de armazenamento do Azure anexadas em que as chaves de criptografia também podem ser gerenciadas pela Microsoft ou gerenciadas pelo cliente, mas o serviço de criptografia é diferente.

Este documento não aborda dados armazenados em sua conta de armazenamento do Azure. Para obter mais informações sobre o ASE, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

Todos os discos gerenciados no HDInsight são protegidos com o Azure Criptografia do Serviço de Armazenamento (SSE). Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar chaves gerenciadas pelo cliente para o HDInsight, forneça as chaves de criptografia para o HDInsight usar e gerenciar essas chaves usando Azure Key Vault.

A criptografia de chave gerenciada pelo cliente é um processo de uma etapa manipulado durante a criação do cluster sem custo adicional. Tudo o que você precisa fazer é registrar o HDInsight como uma identidade gerenciada com Azure Key Vault e adicionar a chave de criptografia ao criar o cluster.

O disco de recursos e os discos gerenciados em cada nó do cluster são criptografados com uma DEK (chave de criptografia de dados simétrica). O DEK é protegido usando a chave de criptografia de chave (KEK) do cofre de chaves. Os processos de criptografia e descriptografia são tratados inteiramente pelo Azure HDInsight.

Você pode usar o portal do Azure ou CLI do Azure para girar com segurança as chaves no cofre de chaves. Quando uma tecla gira, o cluster HDInsight começa a usar a nova chave em minutos. Habilite os recursos de proteção de chave de "exclusão reversível" para proteger contra cenários de ransomware e exclusão acidental. Não há suporte para cofres de chaves sem esse recurso de proteção.

## <a name="get-started-with-customer-managed-keys"></a>Introdução às chaves gerenciadas pelo cliente

Para criar um cluster HDInsight habilitado para chave gerenciada pelo cliente, percorreremos as seguintes etapas:

1. Criar identidades gerenciadas para recursos do Azure
2. Configurar Azure Key Vault e chaves
3. Criar cluster HDInsight com a chave gerenciada pelo cliente habilitada
4. Girando a chave de criptografia

## <a name="create-managed-identities-for-azure-resources"></a>Criar identidades gerenciadas para recursos do Azure

Para autenticar no Key Vault, crie uma identidade gerenciada atribuída pelo usuário usando o [portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)ou [CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](hdinsight-managed-identities.md). Certifique-se de salvar a ID de recurso de identidade gerenciada para quando adicioná-la à política de acesso de Key Vault.

## <a name="set-up-the-key-vault-and-keys"></a>Configurar o Key Vault e as chaves

O HDInsight só dá suporte a Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para Azure Key Vault. Lembre-se de que as chaves devem ter "exclusão reversível". O recurso de "exclusão reversível" está disponível por meio das interfacesC#REST, .net/, PowerShell e CLI do Azure.

1. Para criar um novo cofre de chaves, siga o guia de início rápido do [Azure Key Vault](../key-vault/key-vault-overview.md) . Para obter mais informações sobre como importar chaves existentes, visite [sobre chaves, segredos e certificados](../key-vault/about-keys-secrets-and-certificates.md).

1. Habilite a "exclusão reversível" no cofre de chaves usando o comando [AZ keyvault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Criar chaves.

    a. Para criar uma nova chave, selecione **gerar/importar** no menu **chaves** em **configurações**.

    ![Gerar uma nova chave no Azure Key Vault](./media/disk-encryption/create-new-key.png "Gerar uma nova chave no Azure Key Vault")

    b. Defina **as opções** para **gerar** e dar um nome à chave.

    ![gera o nome da chave](./media/disk-encryption/create-key.png "Gerar nome da chave")

    c. Selecione a chave que você criou na lista de chaves.

    ![lista de chaves do Key Vault](./media/disk-encryption/key-vault-key-list.png)

    d. Ao usar sua própria chave para a criptografia do cluster HDInsight, você precisa fornecer o URI da chave. Copie o **identificador de chave** e salve-o em algum lugar até que você esteja pronto para criar o cluster.

    ![obter identificador de chave](./media/disk-encryption/get-key-identifier.png)

1. Adicione identidade gerenciada à política de acesso do cofre de chaves.

    a. Crie uma nova política de acesso de Azure Key Vault.

    ![Criar nova política de acesso de Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy.png)

    b. Em **selecionar entidade de segurança**, escolha a identidade gerenciada atribuída pelo usuário que você criou.

    ![Defina selecionar entidade de segurança para política de acesso de Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-select-principal.png)

    c. Defina **as permissões de chave** para **obter**, **desencapsular chave**e **encapsular chave**.

    ![Definir permissões de chave para Azure Key Vault acesso Policy1](./media/disk-encryption/add-key-vault-access-policy-keys.png "Definir permissões de chave para Azure Key Vault acesso Policy1")

    d. Defina **as permissões secretas** para **obter**, **definir**e **excluir**.

    ![Definir permissões de chave para policy2 acesso de Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Definir permissões de chave para policy2 acesso de Azure Key Vault")

    e. Selecione **Guardar**.

    ![Salvar política de acesso de Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Criar cluster com criptografia de disco de chave gerenciada pelo cliente

Agora você está pronto para criar um novo cluster HDInsight. A chave gerenciada pelo cliente só pode ser aplicada a novos clusters durante a criação do cluster. A criptografia não pode ser removida de clusters de chaves gerenciados pelo cliente, e a chave gerenciada pelo cliente não pode ser adicionada a clusters existentes.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Durante a criação do cluster, forneça a URL de chave completa, incluindo a versão da chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Você também precisa atribuir a identidade gerenciada ao cluster e fornecer o URI da chave.

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

O exemplo a seguir mostra como usar CLI do Azure para criar um novo cluster de Apache Spark com a criptografia de disco habilitada. Consulte a documentação do [CLI do Azure AZ hdinsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) para obter mais informações.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Girando a chave de criptografia

Pode haver cenários em que você talvez queira alterar as chaves de criptografia usadas pelo cluster HDInsight depois que ele tiver sido criado. Isso pode ser facilmente por meio do Portal. Para essa operação, o cluster deve ter acesso à chave atual e à nova chave pretendida, caso contrário, a operação de rotação de teclas falhará.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para girar a chave, você deve ter a URL completa da nova chave (consulte a etapa 3 de [Configurar o Key Vault e as chaves](#set-up-the-key-vault-and-keys)). Depois de fazer isso, vá para a seção de propriedades do cluster HDInsight no portal e clique em **alterar chave** em **URL da chave de criptografia do disco**. Insira na nova URL de chave e envie para girar a chave.

![girar chave de criptografia de disco](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

O exemplo a seguir mostra como girar a chave de criptografia de disco para um cluster HDInsight existente. Consulte [CLI do Azure AZ hdinsight Rotate-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key) para obter mais detalhes.

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Perguntas frequentes para criptografia de chave gerenciada pelo cliente

**Como o cluster HDInsight acessa meu cofre de chaves?**

O HDInsight acessa sua instância de Azure Key Vault usando a identidade gerenciada que você associa ao cluster HDInsight. Essa identidade gerenciada pode ser criada antes ou durante a criação do cluster. Você também precisa conceder o acesso de identidade gerenciada ao cofre de chaves em que a chave está armazenada.

**Esse recurso está disponível para todos os clusters no HDInsight?**

A criptografia de chave gerenciada pelo cliente está disponível para todos os tipos de cluster, exceto Spark 2,1 e 2,2.

**Posso usar várias chaves para criptografar diferentes discos ou pastas?**

Não, todos os discos gerenciados e discos de recursos são criptografados pela mesma chave.

**O que acontecerá se o cluster perder o acesso ao cofre de chaves ou à chave?**

Se o cluster perder o acesso à chave, os avisos serão mostrados no portal do Apache Ambari. Nesse estado, a operação de **alteração da chave** falhará. Depois que o acesso à chave for restaurado, os avisos Ambaris desaparecerão e as operações como a rotação de chaves poderão ser executadas com êxito.

![alerta de Ambari de acesso à chave](./media/disk-encryption/ambari-alert.png)

**Como posso recuperar o cluster se as chaves forem excluídas?**

Como apenas as chaves habilitadas para "exclusão reversível" têm suporte, se as chaves forem recuperadas no cofre de chaves, o cluster deverá recuperar o acesso às chaves. Para recuperar uma chave de Azure Key Vault, consulte [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [AZ-keyvault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Quais tipos de disco são criptografados? OS discos de recursos/discos do sistema operacional também são criptografados?**

Os discos de recursos e os discos gerenciados são criptografados. OS discos do sistema operacional não são criptografados.

**Se um cluster for escalado verticalmente, os novos nós oferecerão suporte a chaves gerenciadas pelo cliente de forma direta?**

Sim. O cluster precisa de acesso à chave no cofre de chaves durante a expansão. A mesma chave é usada para criptografar discos gerenciados e discos de recursos no cluster.

**As chaves gerenciadas pelo cliente estão disponíveis no meu local?**

As chaves gerenciadas pelo cliente do HDInsight estão disponíveis em todas as nuvens públicas e nuvens nacionais.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da segurança corporativa no Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
