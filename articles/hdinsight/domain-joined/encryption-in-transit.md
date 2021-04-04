---
title: Encriptação Azure HDInsight em trânsito
description: Saiba mais sobre funcionalidades de segurança para fornecer encriptação em trânsito para o seu cluster Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946848"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>Encriptação IPSec em trânsito para Azure HDInsight

Este artigo discute a implementação da encriptação em trânsito para a comunicação entre os nós do cluster Azure HDInsight.

## <a name="background"></a>Fundo

O Azure HDInsight oferece uma variedade de funcionalidades de segurança para garantir os seus dados empresariais. Estas soluções estão agrupadas sob os pilares da segurança do perímetro, autenticação, autorização, auditoria, encriptação e conformidade. A encriptação pode ser aplicada aos dados tanto em repouso como em trânsito.

A encriptação em repouso é coberta pela encriptação do lado do servidor nas contas de armazenamento Azure, bem como a encriptação de discos nos VMs Azure que fazem parte do seu cluster HDInsight.

A encriptação de dados em trânsito em HDInsight é conseguida com a [Segurança da Camada de Transporte (TLS)](../transport-layer-security.md) para aceder aos gateways de cluster e à Segurança do Protocolo de Internet [(IPSec)](https://wikipedia.org/wiki/IPsec) entre nós de cluster. O IPSec pode ser opcionalmente ativado entre todos os nós da cabeça, nós de trabalhadores, nós de borda, nós de zookeeper, bem como nós de gateway e [id broker.](./identity-broker.md)

## <a name="enable-encryption-in-transit"></a>Ativar a encriptação em trânsito

### <a name="azure-portal"></a>Portal do Azure

Para criar um novo cluster com encriptação em trânsito ativada através do portal Azure, faça os seguintes passos:

1. Inicie o processo normal de criação de clusters. Consulte [os clusters baseados em Linux em HDInsight utilizando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) para os passos iniciais de criação de clusters.
1. Preencha os **separadores básicos** e **de armazenamento.** Dirija-se ao **separador Segurança + Rede.**

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Criar cluster - separador de segurança e rede.":::

1. No **separador Segurança + Rede,** selecione a encriptação Ativa na caixa de **verificação de trânsito.**

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Create cluster - ative a encriptação em trânsito.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Criar um cluster com encriptação em trânsito ativado através do CLI Azure

A encriptação em trânsito é ativada usando a `isEncryptionInTransitEnabled` propriedade.

Pode [descarregar um modelo de amostra e um ficheiro de parâmetros.](https://github.com/Azure-Samples/hdinsight-enterprise-security) Antes de utilizar o modelo e o corte de código Azure CLI abaixo, substitua os seguintes espaços reservados pelos seus valores corretos:

| Marcador de posição | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | O ID da sua assinatura Azure |
| `<RESOURCE_GROUP>` | O grupo de recursos onde pretende a criação da nova conta de cluster e armazenamento. |
| `<STORAGEACCOUNTNAME>` | A conta de armazenamento existente que deve ser usada com o cluster. O nome deve ser da forma `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | O nome do seu cluster HDInsight. |
| `<PASSWORD>` | A sua senha escolhida para iniciar sessão no cluster utilizando o SSH e o dashboard Ambari. |
| `<VNET_NAME>` | A rede virtual onde o cluster será implantado. |

O código abaixo faz os seguintes passos iniciais:

1. Entra na sua conta Azure.
1. Define a subscrição ativa onde serão feitas as operações de criação.
1. Cria um novo grupo de recursos para as novas atividades de implantação.
1. Implemente o modelo para criar um novo cluster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da segurança da empresa em Azure HDInsight](hdinsight-security-overview.md)
* [Sincronizar os utilizadores do Azure Ative Directory para um cluster HDInsight](../disk-encryption.md).
