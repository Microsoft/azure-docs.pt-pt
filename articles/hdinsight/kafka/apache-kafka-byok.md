---
title: Traga sua própria chave para Apache Kafka no Azure HDInsight
description: Este artigo descreve como usar sua própria chave de Azure Key Vault para criptografar dados armazenados em Apache Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cba8a4fd64b948d7a3e443426ca1f779af68a3fe
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049006"
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

Para autenticar o Key Vault, crie uma identidade gerida atribuída pelo utilizador utilizando o [portal Azure,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) [Azure PowerShell,](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)ou [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Para obter mais informações sobre como as identidades geridas funcionam no Azure HDInsight, consulte [identidades geridas no Azure HDInsight](../hdinsight-managed-identities.md). Embora o Azure Active Directory seja necessário para identidades gerenciadas e BYOK para Kafka, a Enterprise Security Package (ESP) não é um requisito. Certifique-se de salvar a ID de recurso de identidade gerenciada para quando adicioná-la à política de acesso de Key Vault.

![Criar identidade gerenciada atribuída pelo usuário no portal do Azure](./media/apache-kafka-byok/azure-portal-create-managed-identity.png)

## <a name="set-up-the-key-vault-and-keys"></a>Configurar o Key Vault e as chaves

O HDInsight só dá suporte a Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para Azure Key Vault. Lembre-se de que as chaves devem ter "exclusão reversível". O recurso de "exclusão reversível" está disponível por meio das interfacesC#REST, .net/, PowerShell e CLI do Azure.

1. Para criar um novo cofre chave, siga o [Cofre chave Azure.](../../key-vault/quick-create-cli.md) Para mais informações sobre a importação de chaves existentes, visite [chaves, segredos e certificados.](../../key-vault/about-keys-secrets-and-certificates.md)

1. Ative "soft-delete" no cofre da chave utilizando o comando CLI de atualização de [teclado az.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Criar chaves.

    a. Para criar uma nova tecla, selecione **Generate/Import** a partir do menu **Chaves** em **Definições**.

    ![Gerar uma nova chave no Cofre chave Azure](./media/apache-kafka-byok/kafka-create-new-key.png "Gerar uma nova chave no Azure Key Vault")

    b. Definir **Opções** para **Gerar** e dar um nome à chave.

    ![Apache kafka gera nome chave](./media/apache-kafka-byok/apache-kafka-create-key.png "Gerar nome da chave")

    c. Selecione a chave que você criou na lista de chaves.

    ![Lista de chaves do Apache Kafka Key Vault](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

    d. Ao usar sua própria chave para a criptografia de cluster Kafka, você precisa fornecer o URI de chave. Copie o **identificador chave** e guarde-o em algum lugar até estar pronto para criar o seu cluster.

    ![Identificador de chave Get do Apache Kafka](./media/apache-kafka-byok/kafka-get-key-identifier.png)

1. Adicione identidade gerenciada à política de acesso do cofre de chaves.

    a. Crie uma nova política de acesso de Azure Key Vault.

    ![Criar nova política de acesso de Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

    b. Em **'Select Principal',** escolha a identidade gerida atribuída pelo utilizador que criou.

    ![Defina selecionar entidade de segurança para política de acesso de Azure Key Vault](./media/apache-kafka-byok/azure-portal-add-access-policy.png)

    c. Detete **permissões de chave** para **obter,** **desembrulhar a chave**e embrulhar a **tecla**.

    ![Definir permissões chave para a política de acesso ao cofre de chaves Azure1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Definir permissões de chave para Azure Key Vault acesso Policy1")

    d. Definir **permissões secretas** para **obter,** **definir**e **eliminar**.

    ![Definir permissões-chave para a política de acesso ao cofre de chaves Azure2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Definir permissões de chave para policy2 acesso de Azure Key Vault")

    e. Selecione **Guardar**.

    ![Salvar política de acesso de Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>Criar cluster HDInsight

Agora você está pronto para criar um novo cluster HDInsight. A partir do separador **Basics,** **selecione Kafka** para **o tipo de cluster**.

![Portal Azure selecione kafka tipo](./media/apache-kafka-byok/azure-portal-cluster-basics-type-kafka.png)

BYOK só pode ser aplicado a novos clusters durante a criação do cluster. A criptografia não pode ser removida dos clusters BYOK e BYOK não pode ser adicionado a clusters existentes.

![Kafka a criptografia de disco no portal do Azure](./media/apache-kafka-byok/azure-portal-cluster-security-networking-kafka-byok.png)

Durante a criação do cluster, forneça a URL de chave completa, incluindo a versão da chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Você também precisa atribuir a identidade gerenciada ao cluster e fornecer o URI da chave. Para obter detalhes completos sobre a criação de [clusters, consulte Create Apache Hadoop clusters usando o portal Azure](./apache-kafka-get-started.md)

## <a name="rotating-the-encryption-key"></a>Girando a chave de criptografia

Pode haver cenários em que você talvez queira alterar as chaves de criptografia usadas pelo cluster Kafka depois que ele tiver sido criado. Isso pode ser facilmente por meio do Portal. Para essa operação, o cluster deve ter acesso à chave atual e à nova chave pretendida, caso contrário, a operação de rotação de teclas falhará.

Para rodar a tecla, deve ter a url completa da nova tecla (Ver Passo 3 da [Configuração do Cofre de Chaves e Teclas).](#set-up-the-key-vault-and-keys) Assim que o tiver, vá à secção de propriedades do cluster Kafka no portal e clique na **Chave de Alteração** sob url de chave de **encriptação**de disco . Insira na nova URL de chave e envie para girar a chave.

![Kafka girar a chave de criptografia de disco](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Perguntas frequentes sobre BYOK para Apache Kafka

**Como é que o aglomerado de Kafka acede ao meu cofre chave?**

Associe uma identidade gerenciada ao cluster HDInsight Kafka durante a criação do cluster. Essa identidade gerenciada pode ser criada antes ou durante a criação do cluster. Você também precisa conceder o acesso de identidade gerenciada ao cofre de chaves em que a chave está armazenada.

**Esta funcionalidade está disponível para todos os clusters Kafka no HDInsight?**

A criptografia BYOK só é possível para clusters Kafka 1,1 e superiores.

**Posso ter chaves diferentes para diferentes tópicos/divisórias?**

Não, todos os discos gerenciados no cluster são criptografados pela mesma chave.

**O que acontece se o cluster perder o acesso ao cofre ou à chave?**

Se o cluster perder o acesso à chave, os avisos serão mostrados no portal do Apache Ambari. Neste estado, a operação **Change Key** falhará. Depois que o acesso à chave for restaurado, os avisos Ambaris desaparecerão e as operações como a rotação de chaves poderão ser executadas com êxito.

![Alerta de Ambari de acesso à chave Apache Kafka](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Como posso recuperar o agrupamento se as chaves são apagadas?**

Como apenas as chaves habilitadas para "exclusão reversível" têm suporte, se as chaves forem recuperadas no cofre de chaves, o cluster deverá recuperar o acesso às chaves. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Posso ter aplicações de produtor/consumidor a trabalhar com um cluster BYOK e um cluster não-BYOK simultaneamente?**

Sim. O uso de BYOK é transparente para aplicativos de produtor/consumidor. A criptografia ocorre na camada do sistema operacional. Não é necessário fazer alterações em aplicativos Kafka de produtor/consumidor existentes.

**Os discos OS/Recursos também estão encriptados?**

Não. Discos do sistema operacional e discos de recursos não são criptografados.

**Se um cluster for dimensionado, os novos corretores apoiarão o BYOK sem problemas?**

Sim. O cluster precisa de acesso à chave no cofre de chaves durante a expansão. A mesma chave é usada para criptografar todos os discos gerenciados no cluster.

**ByOK está disponível na minha localização?**

Kafka BYOK está disponível em todas as nuvens públicas.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](../../key-vault/key-vault-overview.md)
* Para começar com o Cofre de Chaves Azure, veja [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
