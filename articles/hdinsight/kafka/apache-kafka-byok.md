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
ms.openlocfilehash: 61a4be19000265910493963db9f29df143a7e21c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360355"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Traga a sua própria chave para o Apache Kafka no HDInsight do Azure (pré-visualização)

O Azure HDInsight inclui suporte de Bring Your Own Key (BYOK) para o Apache Kafka. Esta capacidade permite-lhe ter e gerir as chaves utilizadas para encriptar dados inativos. 

Todos os discos geridos no HDInsight são protegidos com encriptação de serviço de armazenamento do Azure (SSE). Por predefinição, os dados desses discos são encriptados utilizando chaves geridas pela Microsoft. Se ativar o BYOK, fornecer a chave de encriptação para o HDInsight utilizar e geri-lo com o Azure Key Vault. 

A criptografia de BYOK é um processo de um passo processado durante a criação do cluster sem custos adicionais. Tudo o que precisa fazer é registrar o HDInsight como uma identidade gerida com o Azure Key Vault e adicione a chave de encriptação quando cria o cluster.

Todas as mensagens para o cluster de Kafka (incluindo réplicas mantidas pelo Kafka) são encriptadas com uma chave de encriptação do simétrica dados (DEK). O DEK é protegido com a chave de encriptação de chaves (KEK) do seu Cofre de chaves. Os processos de criptografia e descriptografia são manipulados completamente pelo Azure HDInsight. 

Pode utilizar o portal do Azure ou a CLI do Azure para girar em segurança as chaves no Cofre de chaves. Quando uma chave roda, o cluster HDInsight Kafka é iniciado com a nova chave numa questão de minutos. Ative as funcionalidades de proteção de chaves "Fazer remover" e "Eliminar de forma recuperável" proteger contra ransomware cenários e eliminação acidental. Não são suportadas chaves sem esses recursos de proteção.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>Começar com o BYOK

1. Crie identidades geridas para recursos do Azure.

   Para autenticar para o Key Vault, criar uma identidade gerida atribuído ao utilizador a utilizar o [portal do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), ou [ CLI do Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Para obter mais informações sobre como geridos de identidades de trabalho no Azure HDInsight, consulte [geridos identidades no Azure HDInsight](../hdinsight-managed-identities.md). Enquanto o Azure Active directory é necessário para identidades geridas e a BYOK ao Kafka, o pacote de segurança da empresa (ESP) não é um requisito. Certifique-se de que guarde o ID de recurso de identidade gerida para quando adicioná-lo para a política de acesso do Cofre de chaves.

   ![Criar utilizador atribuído a identidade gerida no portal do Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. Importar um cofre de chaves existente ou crie um novo.

   HDInsight suporta apenas o Azure Key Vault. Se tiver o seu próprio Cofre de chaves, pode importar as suas chaves no Azure Key Vault. Lembre-se de que tem de ter as chaves "Eliminar de forma recuperável" e "Fazer não remover" ativada. Os recursos de "Eliminação de forma recuperável" e "Fazer remover" estão disponíveis através do REST, .NET / interfaces de c#, o PowerShell e CLI do Azure.

   Para criar um novo cofre de chaves, siga os [do Azure Key Vault](../../key-vault/key-vault-overview.md) início rápido. Para obter mais informações sobre como importar chaves existentes, visite [sobre chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md).

   Para criar uma nova chave, selecione **gerar/importar** partir do **chaves** menu em **definições**.

   ![Gerar uma nova chave no Cofre de chaves do Azure](./media/apache-kafka-byok/kafka-create-new-key.png)

   Definir **opções** ao **gerar** e dê a chave de um nome.

   ![Gerar uma nova chave no Cofre de chaves do Azure](./media/apache-kafka-byok/kafka-create-a-key.png)

   Selecione a chave que criou na lista de chaves.

   ![Lista de chaves do Azure Key Vault](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

   Quando usa sua própria chave para encriptação de cluster do Kafka, tem de fornecer o URI da chave. Copiar o **identificador de chave** e guarde-o em algum lugar até estar pronto para criar o cluster.

   ![Identificador de chave de cópia](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
3. Adicione identidade gerida para a política de acesso do Cofre de chaves.

   Crie uma nova política de acesso do Azure Key Vault.

   ![Criar nova política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   Sob **selecionar Principal**, escolha a identidade gerida atribuído ao utilizador que criou.

   ![Definir selecionar Principal de política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

   Definir **permissões da chave** ao **obter**, **anular a moldagem de chave**, e **moldar chave**.

   ![Definir permissões de chave de política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   Definir **permissões do segredo** ao **obter**, **definir**, e **eliminar**.

   ![Definir permissões de chave de política de acesso do Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

4. Criar cluster do HDInsight

   Agora, está pronto para criar um novo cluster do HDInsight. Só pode ser aplicado BYOK para novos clusters durante a criação do cluster. Não é possível remover a encriptação de clusters BYOK e o BYOK não é possível adicionar clusters existentes.

   ![Encriptação de disco do Kafka no portal do Azure](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Durante a criação do cluster, fornecer toda a URL, incluindo a versão da chave da chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Também terá de atribuir a identidade gerida para o cluster e forneça o URI da chave.

## <a name="faq-for-byok-to-apache-kafka"></a>FAQ do BYOK ao Apache Kafka

**Como é que o cluster de Kafka acessar meu Cofre de chaves?**

   Associe uma identidade gerida com o cluster do HDInsight Kafka durante a criação do cluster. Esta identidade gerida pode ser criada antes ou durante a criação do cluster. Também tem de conceder o acesso de identidade gerida para o Cofre de chaves, onde está armazenada a chave.

**Este recurso está disponível para todos os clusters do Kafka no HDInsight?**

   Encriptação de BYOK só é possível para o Kafka 1.1 e posteriores clusters.

**Posso ter chaves diferentes para diferentes tópicos/partições?**

   Não, todos os discos geridos no cluster são criptografados pela mesma chave.

**Como recuperar o cluster se as chaves são eliminadas?**

   Uma vez que são suportadas apenas chaves de "Eliminação de forma recuperável" ativada, se as chaves são restauradas no Cofre de chaves, o cluster deve recuperar o acesso às chaves. Para restaurar uma chave de Cofre de chaves do Azure, consulte [restauro AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey).

**Pode ter aplicações de produtor/consumidor trabalhar com um cluster BYOK e um cluster não BYOK simultaneamente?**

   Sim. A utilização de BYOK é transparente para as aplicações de produtor/consumidor. Encriptação ocorre na camada do sistema operacional. Sem alterações precisam ser feitas para existente de produtor/consumidor aplicativos do Kafka.

**Discos de recursos/discos de SO também são encriptados?**

   Não. Discos de SO e discos de recursos não estão encriptados.

**Se um cluster for aumentado verticalmente, novos mediadores suportará BYOK perfeitamente?**

   Sim. O cluster precisa de aceder à chave no Cofre de chaves durante o dimensionamento cópia de segurança. A mesma chave é utilizada para encriptar todos os discos geridos no cluster.

**Está BYOK disponível na minha localização?**

   Kafka BYOK está disponível em todas as clouds públicas.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre o Azure Key Vault, consulte [o que é o Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* Para começar a utilizar com o Azure Key Vault, consulte o artigo [introdução ao Azure Key Vault](../../key-vault/key-vault-overview.md).
