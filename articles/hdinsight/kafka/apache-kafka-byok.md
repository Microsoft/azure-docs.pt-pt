---
title: Traga sua própria chave para Apache Kafka no Azure HDInsight
description: Este artigo descreve como usar sua própria chave de Azure Key Vault para criptografar dados armazenados em Apache Kafka no Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: f619a0179849e2ca17a0528d97ef13f0788a4838
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811542"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Traga sua própria chave para Apache Kafka no Azure HDInsight

O Azure HDInsight inclui suporte a Bring Your Own Key (BYOK) para Apache Kafka. Essa funcionalidade permite que você tenha e gerencie as chaves usadas para criptografar dados em repouso.

Todos os discos gerenciados no HDInsight são protegidos com o Azure Criptografia do Serviço de Armazenamento (SSE). Por padrão, os dados nesses discos são criptografados usando chaves gerenciadas pela Microsoft. Se você habilitar o BYOK, fornecerá a chave de criptografia para o HDInsight usar e gerenciá-lo usando Azure Key Vault.

A criptografia BYOK é um processo de uma etapa manipulado durante a criação do cluster sem custo adicional. Tudo o que você precisa fazer é registrar o HDInsight como uma identidade gerenciada com Azure Key Vault e adicionar a chave de criptografia ao criar o cluster.

Todas as mensagens para o cluster Kafka (incluindo réplicas mantidas por Kafka) são criptografadas com uma chave de criptografia de dados simétrica (DEK). O DEK é protegido usando a chave de criptografia de chave (KEK) do cofre de chaves. Os processos de criptografia e descriptografia são tratados inteiramente pelo Azure HDInsight. 

Você pode usar o portal do Azure ou CLI do Azure para girar com segurança as chaves no cofre de chaves. Quando uma tecla gira, o cluster HDInsight Kafka começa a usar a nova chave em minutos. Habilite os recursos de proteção de chave de "exclusão reversível" para proteger contra cenários de ransomware e exclusão acidental. Não há suporte para cofres de chaves sem esse recurso de proteção.

## <a name="get-started-with-byok"></a>Introdução ao BYOK
Para criar um cluster Kafka habilitado para BYOK, vamos percorrer as seguintes etapas:
1. Criar identidades gerenciadas para recursos do Azure
2. Configurar Azure Key Vault e chaves
3. Criar cluster HDInsight Kafka com BYOK habilitado
4. Girando a chave de criptografia

## <a name="create-managed-identities-for-azure-resources"></a>Criar identidades gerenciadas para recursos do Azure

   Para autenticar no Key Vault, crie uma identidade gerenciada atribuída pelo usuário usando o [portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)ou [CLI do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](../hdinsight-managed-identities.md). Embora o Azure Active Directory seja necessário para identidades gerenciadas e BYOK para Kafka, a Enterprise Security Package (ESP) não é um requisito. Certifique-se de salvar a ID de recurso de identidade gerenciada para quando adicioná-la à política de acesso de Key Vault.

   ![Criar identidade gerenciada atribuída pelo usuário no portal do Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Configurar o Key Vault e as chaves

   O HDInsight só dá suporte a Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para Azure Key Vault. Lembre-se de que as chaves devem ter "exclusão reversível". O recurso de "exclusão reversível" está disponível por meio das interfacesC#REST, .net/, PowerShell e CLI do Azure.

   1. Para criar um novo cofre de chaves, siga o guia de início rápido do [Azure Key Vault](../../key-vault/key-vault-overview.md) . Para obter mais informações sobre como importar chaves existentes, visite [sobre chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Habilite a "exclusão reversível" no cofre de chaves usando o comando [AZ keyvault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI.
        ```Azure CLI
        az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. Criar chaves

        a. Para criar uma nova chave, selecione **gerar/importar** no menu **chaves** em **configurações**.

        ![Gerar uma nova chave no Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png "Gerar uma nova chave no Azure Key Vault")

        b. Defina **as opções** para **gerar** e dar um nome à chave.

        ![Gerar nome da chave](./media/apache-kafka-byok/kafka-create-a-key.png "Gerar nome da chave")

        c. Selecione a chave que você criou na lista de chaves.

        ![Lista de chaves de Azure Key Vault](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. Ao usar sua própria chave para a criptografia de cluster Kafka, você precisa fornecer o URI de chave. Copie o **identificador de chave** e salve-o em algum lugar até que você esteja pronto para criar o cluster.

        ![Copiar identificador de chave](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Adicione identidade gerenciada à política de acesso do cofre de chaves.

        a. Crie uma nova política de acesso de Azure Key Vault.

        ![Criar nova política de acesso de Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Em **selecionar entidade de segurança**, escolha a identidade gerenciada atribuída pelo usuário que você criou.

        ![Defina selecionar entidade de segurança para política de acesso de Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Defina **as permissões de chave** para **obter**, **desencapsular chave**e **encapsular chave**.

        ![Definir permissões de chave para Azure Key Vault acesso Policy1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Definir permissões de chave para Azure Key Vault acesso Policy1")

        d. Defina **as permissões secretas** para **obter**, **definir**e **excluir**.

        ![Definir permissões de chave para policy2 acesso de Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Definir permissões de chave para policy2 acesso de Azure Key Vault")

        e. Clique em **Guardar**. 

        ![Salvar política de acesso de Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>Criar cluster HDInsight

   Agora você está pronto para criar um novo cluster HDInsight. BYOK só pode ser aplicado a novos clusters durante a criação do cluster. A criptografia não pode ser removida dos clusters BYOK e BYOK não pode ser adicionado a clusters existentes.

   ![Kafka a criptografia de disco no portal do Azure](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Durante a criação do cluster, forneça a URL de chave completa, incluindo a versão da chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Você também precisa atribuir a identidade gerenciada ao cluster e fornecer o URI da chave.

## <a name="rotating-the-encryption-key"></a>Girando a chave de criptografia
   Pode haver cenários em que você talvez queira alterar as chaves de criptografia usadas pelo cluster Kafka depois que ele tiver sido criado. Isso pode ser facilmente por meio do Portal. Para essa operação, o cluster deve ter acesso à chave atual e à nova chave pretendida, caso contrário, a operação de rotação de teclas falhará.

   Para girar a chave, você deve ter a URL completa da nova chave (consulte a etapa 3 de [Configurar o Key Vault e as chaves](#setup-the-key-vault-and-keys)). Depois de fazer isso, vá para a seção Propriedades do cluster Kafka no portal e clique em **alterar chave** em **URL da chave de criptografia do disco**. Insira na nova URL de chave e envie para girar a chave.

   ![Kafka girar a chave de criptografia de disco](./media/apache-kafka-byok/kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Perguntas frequentes sobre BYOK para Apache Kafka

**Como o cluster Kafka acessa meu cofre de chaves?**

   Associe uma identidade gerenciada ao cluster HDInsight Kafka durante a criação do cluster. Essa identidade gerenciada pode ser criada antes ou durante a criação do cluster. Você também precisa conceder o acesso de identidade gerenciada ao cofre de chaves em que a chave está armazenada.

**Esse recurso está disponível para todos os clusters do Kafka no HDInsight?**

   A criptografia BYOK só é possível para clusters Kafka 1,1 e superiores.

**Posso ter chaves diferentes para diferentes tópicos/partições?**

   Não, todos os discos gerenciados no cluster são criptografados pela mesma chave.

**O que acontecerá se o cluster perder o acesso ao cofre de chaves ou à chave?**
Se o cluster perder o acesso à chave, os avisos serão mostrados no portal do Apache Ambari. Nesse estado, a operação de **alteração da chave** falhará. Depois que o acesso à chave for restaurado, os avisos Ambaris desaparecerão e as operações como a rotação de chaves poderão ser executadas com êxito.

   ![Alerta de Ambari de acesso à chave Kafka](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Como posso recuperar o cluster se as chaves forem excluídas?**

   Como apenas as chaves habilitadas para "exclusão reversível" têm suporte, se as chaves forem recuperadas no cofre de chaves, o cluster deverá recuperar o acesso às chaves. Para recuperar uma chave de Azure Key Vault, consulte [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [AZ-keyvault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Posso ter aplicativos produtor/consumidor trabalhando com um cluster BYOK e um cluster não BYOK simultaneamente?**

   Sim. O uso de BYOK é transparente para aplicativos de produtor/consumidor. A criptografia ocorre na camada do sistema operacional. Não é necessário fazer alterações em aplicativos Kafka de produtor/consumidor existentes.

**OS discos de recursos/discos do sistema operacional também são criptografados?**

   Não. Discos do sistema operacional e discos de recursos não são criptografados.

**Se um cluster for escalado verticalmente, os novos agentes terão suporte do BYOK diretamente?**

   Sim. O cluster precisa de acesso à chave no cofre de chaves durante a expansão. A mesma chave é usada para criptografar todos os discos gerenciados no cluster.

**O BYOK está disponível em meu local?**

   Kafka BYOK está disponível em todas as nuvens públicas.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* Para começar a usar o Azure Key Vault, consulte [introdução com Azure Key Vault](../../key-vault/key-vault-overview.md).
