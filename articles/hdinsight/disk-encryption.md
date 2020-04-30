---
title: Encriptação de disco de chave gerida pelo cliente para Azure HDInsight
description: Este artigo descreve como usar a sua própria chave de encriptação do Azure Key Vault para encriptar os dados armazenados em discos geridos em clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 732709dbcb5ebe54025a963379128f1a1e74183e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536306"
---
# <a name="customer-managed-key-disk-encryption"></a>Encriptação de discos chave gerida pelo cliente

O Azure HDInsight suporta encriptação de chaves gerida pelo cliente para dados em discos geridos e discos de recursos ligados a máquinas virtuais de cluster HDInsight. Esta funcionalidade permite-lhe utilizar o Cofre de Chaves Azure para gerir as chaves de encriptação que protegem os dados em repouso nos seus clusters HDInsight.

Todos os discos geridos no HDInsight estão protegidos com encriptação do serviço de armazenamento Azure (SSE). Por predefinição, os dados desses discos são encriptados utilizando chaves geridas pela Microsoft. Se ativar as chaves geridas pelo cliente para o HDInsight, fornece as chaves de encriptação para o HDInsight utilizar e gerir essas teclas utilizando o Cofre de Chaves Azure.

Este documento não aborda os dados armazenados na sua conta de Armazenamento Azure. Para obter mais informações sobre encriptação do Armazenamento Azure, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../storage/common/storage-service-encryption.md). Os seus clusters podem ter uma ou mais contas de Armazenamento Azure anexadas onde as chaves de encriptação também podem ser geridas pela Microsoft ou geridas pelo cliente, mas o serviço de encriptação é diferente.

## <a name="introduction"></a>Introdução

A encriptação de chaves gerida pelo cliente é um processo de uma etapa tratado durante a criação do cluster sem custos adicionais. Tudo o que precisa de fazer é registar o HDInsight como uma identidade gerida com o Azure Key Vault e adicionar a chave de encriptação quando criar o seu cluster.

Tanto o disco de recursos como os discos geridos em cada nó do cluster são encriptados com uma chave de encriptação de dados simétrica (DEK). O DEK está protegido utilizando a chave de encriptação (KEK) do seu cofre de chaves. Os processos de encriptação e dedesencriptação são tratados inteiramente pelo Azure HDInsight.

Se a firewall do cofre chave estiver ativada no cofre da chave onde a chave de encriptação do disco está armazenada, os endereços IP do Fornecedor regional de Recursos HDInsight para a região onde o cluster será implantado devem ser adicionados à configuração da firewall do cofre chave. Isto é necessário porque o HDInsight não é um serviço de cofre chave Azure de confiança.

Pode utilizar o portal Azure ou o Azure CLI para rodar com segurança as chaves no cofre da chave. Quando uma tecla gira, o cluster HDInsight começa a utilizar a nova tecla dentro de minutos. Ative as funcionalidades de proteção da tecla [Soft Delete](../key-vault/general/overview-soft-delete.md) para proteger contra cenários de ransomware e eliminação acidental. Os cofres sem esta característica de proteção não são suportados.

|Tipo de cluster |Disco OS (Disco Gerido) |Disco de dados (disco gerido) |Disco de dados temporário (SSD Local) |
|---|---|---|---|
|Kafka, HBase com escritos acelerados|[Encriptação SSE](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|Encriptação SSE + Encriptação OPCIONAL CMK|Encriptação opcional da CMK|
|Todos os outros clusters (Spark, Interactive, Hadoop, HBase sem escritos acelerados)|Encriptação SSE|N/D|Encriptação opcional da CMK|

## <a name="get-started-with-customer-managed-keys"></a>Começar com chaves geridas pelo cliente

Para criar um cluster HDInsight ativado pelo cliente, vamos passar pelos seguintes passos:

1. Criar identidades geridas para os recursos do Azure
1. Criar cofre de chaves azure
1. Criar chave
1. Criar política de acesso
1. Criar cluster HDInsight com chave gerida pelo cliente ativada
1. Girando a chave de encriptação

## <a name="create-managed-identities-for-azure-resources"></a>Criar identidades geridas para os recursos do Azure

Crie uma identidade gerida atribuída ao utilizador para autenticar o Cofre chave.

Consulte [Criar uma identidade gerida atribuída](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) ao utilizador para passos específicos. Para obter mais informações sobre como as identidades geridas funcionam no Azure HDInsight, consulte [identidades geridas no Azure HDInsight](hdinsight-managed-identities.md). Certifique-se de guardar o ID de recurso de identidade gerido para quando o adicionar à política de acesso ao Cofre chave.

## <a name="create-azure-key-vault"></a>Criar cofre de chaves azure

Criar um cofre de chaves. Consulte [o Cofre chave Create Azure](../key-vault/secrets/quick-create-portal.md) para obter passos específicos.

HDInsight apenas suporta cofre de chave Azure. Se tiver o seu próprio cofre, pode importar as chaves para o Cofre de Chaves Azure. Lembre-se que o cofre da chave deve ter **soft delete** ativado. Para mais informações sobre a importação de chaves existentes, visite [chaves, segredos e certificados.](../key-vault/about-keys-secrets-and-certificates.md)

## <a name="create-key"></a>Criar chave

1. A partir do seu novo cofre de chaves, navegue para **Definições** > **Teclas** > **+ Generate/Import**.

    ![Gerar uma nova chave no Cofre chave Azure](./media/disk-encryption/create-new-key.png "Gerar uma nova chave no Cofre chave Azure")

1. Forneça um nome e, em seguida, selecione **Criar**. Mantenha o tipo de **chave** padrão de **RSA**.

    ![gera nome chave](./media/disk-encryption/create-key.png "Gerar nome-chave")

1. Quando voltar à página **Keys,** selecione a chave que criou.

    ![lista de chaves do cofre](./media/disk-encryption/key-vault-key-list.png)

1. Selecione a versão para abrir a página **versão chave.** Quando utiliza a sua própria chave para encriptação de cluster HDInsight, tem de fornecer a chave URI. Copie o **identificador chave** e guarde-o em algum lugar até estar pronto para criar o seu cluster.

    ![obter identificador chave](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Criar política de acesso

1. A partir do seu novo cofre de chaves, navegue para **definições** > **Políticas** > de acesso **+ Adicionar Política de Acesso**.

    ![Criar nova política de acesso ao Cofre chave Azure](./media/disk-encryption/key-vault-access-policy.png)

1. A partir da página política de **acesso adicionar,** forneça as seguintes informações:

    |Propriedade |Descrição|
    |---|---|
    |Permissões-chave|Selecione **Get,** **Desembrulhar a chave**e **embrulhar a tecla**.|
    |Permissões Secretas|Selecione **'Obter,** **Definir'** e **Apagar**.|
    |Selecione principal|Selecione a identidade gerida atribuída ao utilizador que criou anteriormente.|

    ![Definir Selecione principal para a política de acesso ao cofre de chaves Azure](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Selecione **Adicionar**.

1. Selecione **Guardar**.

    ![Salvar a política de acesso ao cofre de chaves Azure](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Criar cluster com encriptação de disco de chave gerida pelo cliente

Está agora pronto para criar um novo cluster HDInsight. A chave gerida pelo cliente só pode ser aplicada a novos clusters durante a criação do cluster. A encriptação não pode ser removida dos clusters de chaves geridos pelo cliente, e a chave gerida pelo cliente não pode ser adicionada aos clusters existentes.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Durante a criação do cluster, forneça o **identificador Chave**completo, incluindo a versão chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Também precisa atribuir a identidade gerida ao cluster e fornecer a chave URI.

![Criar novo cluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

O exemplo que se segue mostra como usar o Azure CLI para criar um novo cluster Apache Spark com encriptação de disco ativada. Para mais informações, consulte [Azure CLI az hdinsight criar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

### <a name="using-azure-resource-manager-templates"></a>Utilizar modelos do Azure Resource Manager

O exemplo seguinte mostra como usar um modelo de Gestor de Recursos Azure para criar um novo cluster Apache Spark com encriptação de disco ativada. Para mais informações, veja o que são os [modelos ARM?](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

Este exemplo usa powerShell para chamar o modelo.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

O conteúdo do modelo `azuredeploy.json`de gestão de recursos, :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

## <a name="rotating-the-encryption-key"></a>Girando a chave de encriptação

Pode haver cenários em que poderá querer alterar as chaves de encriptação utilizadas pelo cluster HDInsight depois de ter sido criada. Isto pode ser facilmente através do portal. Para esta operação, o cluster deve ter acesso tanto à chave atual como à nova chave pretendida, caso contrário, o funcionamento da chave rotativa falhará.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para rodar a chave, precisa do cofre da chave base URI. Depois de o ter feito, vá à secção de propriedades do cluster HDInsight no portal e clique na **Chave de Alteração** sob url de chave de **encriptação de disco**. Introduza no novo url da chave e submeta-se para rodar a tecla.

![chave de encriptação de disco rotativo](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

O exemplo que se segue mostra como rodar a chave de encriptação do disco para um cluster HDInsight existente. Para mais informações, consulte [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>FAQ para encriptação de chaves gerida pelo cliente

**Como é que o cluster HDInsight acede ao meu cofre chave?**

O HDInsight acede à sua instância de Cofre de Chave Azure utilizando a identidade gerida que associa ao cluster HDInsight. Esta identidade gerida pode ser criada antes ou durante a criação do cluster. Também precisa de conceder acesso de identidade gerido ao cofre da chave onde a chave está armazenada.

**Esta funcionalidade está disponível para todos os clusters no HDInsight?**

A encriptação da chave gerida pelo cliente está disponível para todos os tipos de cluster, exceto o Spark 2.1 e 2.2.

**Posso usar várias teclas para encriptar diferentes discos ou pastas?**

Não, todos os discos geridos e discos de recursos são encriptados pela mesma tecla.

**O que acontece se o cluster perder o acesso ao cofre ou à chave?**

Se o cluster perder o acesso à chave, os avisos serão mostrados no portal Apache Ambari. Neste estado, a operação **Change Key** falhará. Uma vez restaurado o acesso chave, os avisos Ambari desaparecerão e operações como a rotação da chave podem ser realizadas com sucesso.

![acesso chave Alerta Ambari](./media/disk-encryption/ambari-alert.png)

**Como posso recuperar o agrupamento se as chaves são apagadas?**

Uma vez que apenas as teclas ativadas "Soft Delete" são suportadas, se as teclas forem recuperadas no cofre da chave, o cluster deve recuperar o acesso às teclas. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Que tipos de disco estão encriptados? Os discos/discos de recursos os estão também encriptados?**

Os discos de recursos e os discos de dados/geridos são encriptados. Os discos de soe não estão encriptados.

**Se um cluster for dimensionado, os novos nós suportam as chaves geridas pelo cliente sem problemas?**

Sim. O cluster precisa de acesso à chave no cofre durante a escala. A mesma tecla é usada para encriptar discos geridos e discos de recursos no cluster.

**As chaves geridas pelo cliente estão disponíveis na minha localização?**

As chaves geridas pelo cliente HDInsight estão disponíveis em todas as nuvens públicas e nuvens nacionais.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre o Cofre de Chaves Azure, consulte [o what is Azure Key Vault](../key-vault/general/overview.md).
* [Visão geral da segurança da empresa em Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
