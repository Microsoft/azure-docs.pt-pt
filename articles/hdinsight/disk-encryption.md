---
title: Dupla encriptação para dados em repouso
titleSuffix: Azure HDInsight
description: Este artigo descreve as duas camadas de encriptação disponíveis para os dados em repouso nos clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: a9a90fbb2eedd6db2873d4ac2a5fea94c05c7eed
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844749"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Encriptação dupla Azure HDInsight para dados em repouso

Este artigo discute métodos de encriptação de dados em repouso em clusters Azure HDInsight. A encriptação de dados em repouso refere-se à encriptação em discos geridos (discos de dados, discos de SO e discos temporários) ligados a máquinas virtuais de cluster HDInsight. 

Este documento não aborda os dados armazenados na sua conta de Armazenamento Azure. Os seus clusters podem ter uma ou mais contas de Armazenamento Azure anexadas onde as chaves de encriptação também podem ser geridas pela Microsoft ou geridas pelo cliente, mas o serviço de encriptação é diferente. Para obter mais informações sobre a encriptação do Azure Storage, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](../storage/common/storage-service-encryption.md).

## <a name="introduction"></a>Introdução

Existem três principais funções de disco gerido no Azure: o disco de dados, o disco DE E e o disco temporário. Para obter mais informações sobre diferentes tipos de discos geridos, consulte [discos geridos introdução ao Azure](../virtual-machines/managed-disks-overview.md). 

HDInsight suporta vários tipos de encriptação em duas camadas diferentes:

- Encriptação lateral do servidor (SSE) - A SSE é realizada pelo serviço de armazenamento. No HDInsight, a SSE é usada para encriptar discos de OS e discos de dados. É ativado por defeito. SSE é um serviço de encriptação de camada 1.
- Encriptação no anfitrião utilizando a chave gerida pela plataforma - Semelhante à SSE, este tipo de encriptação é realizado pelo serviço de armazenamento. No entanto, é apenas para discos temporários e não é ativado por padrão. A encriptação no anfitrião é também um serviço de encriptação de camada 1.
- Encriptação em repouso utilizando a chave gerida pelo cliente - Este tipo de encriptação pode ser usado em dados e discos temporários. Não é ativado por defeito e requer que o cliente forneça a sua própria chave através do cofre da chave Azure. A encriptação em repouso é um serviço de encriptação de camada 2.

Estes tipos são resumidos na tabela seguinte.

|Tipo de cluster |Disco OS (Disco gerido) |Disco de dados (Disco gerido) |Disco de dados temporário (SSD local) |
|---|---|---|---|
|Kafka, HBase com Escreve Acelerada|Camada1: [Encriptação SSE](../virtual-machines/managed-disks-overview.md#encryption) por padrão|Camada1: [Encriptação SSE](../virtual-machines/managed-disks-overview.md#encryption) por padrão, Camada2: Encriptação opcional em repouso usando CMK|Camada1: Encriptação opcional no anfitrião usando PMK, Camada2: Encriptação opcional em repouso usando CMK|
|Todos os outros clusters (Spark, Interactive, Hadoop, HBase sem escritas Aceleradas)|Camada1: [Encriptação SSE](../virtual-machines/managed-disks-overview.md#encryption) por padrão|N/D|Camada1: Encriptação opcional no anfitrião usando PMK, Camada2: Encriptação opcional em repouso usando CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Encriptação em repouso usando chaves geridas pelo Cliente

A encriptação chave gerida pelo cliente é um processo de um passo tratado durante a criação do cluster sem custos adicionais. Tudo o que precisa fazer é autorizar uma identidade gerida com o Azure Key Vault e adicionar a chave de encriptação quando criar o seu cluster.

Tanto os discos de dados como os discos temporários em cada nó do cluster são encriptados com uma chave de encriptação de dados simétrica (DEK). O DEK está protegido utilizando a chave de encriptação (KEK) do seu cofre de chaves. Os processos de encriptação e desencriptação são tratados inteiramente por Azure HDInsight.

Para os discos DES ligados ao cluster VMs apenas uma camada de encriptação (PMK) está disponível. Recomenda-se que os clientes evitem copiar dados sensíveis para discos OS se for necessário ter uma encriptação CMK para os seus cenários.

Se a firewall do cofre de chaves estiver ativada no cofre de chaves onde a chave de encriptação do disco está armazenada, os endereços IP do Fornecedor de Recursos Regional HDInsight para a região onde o cluster será implantado devem ser adicionados à configuração da firewall do cofre de chaves. Isto é necessário porque o HDInsight não é um serviço de cofre de chaves Azure de confiança.

Pode utilizar o portal Azure ou o Azure CLI para rodar com segurança as chaves no cofre da chave. Quando uma chave gira, o cluster HDInsight começa a utilizar a nova tecla dentro de minutos. Ativar as principais funcionalidades de proteção [para eliminar o soft delete](../key-vault/general/soft-delete-overview.md) para proteger contra cenários de ransomware e eliminação acidental. Os cofres sem esta proteção não são suportados.

### <a name="get-started-with-customer-managed-keys"></a>Começar com chaves geridas pelo cliente

Para criar um cluster HDInsight com uma chave gerida pelo cliente, vamos percorrer os seguintes passos:

1. Criar identidades geridas para recursos Azure
1. Criar cofre de chaves Azure
1. Criar chave
1. Criar política de acesso
1. Criar cluster HDInsight com chave gerida pelo cliente ativada
1. Rotação da chave de encriptação

Cada passo é explicado numa das seguintes secções em detalhe.

### <a name="create-managed-identities-for-azure-resources"></a>Criar identidades geridas para recursos Azure

Crie uma identidade gerida atribuída pelo utilizador para autenticar o Key Vault.

Consulte [Criar uma identidade gerida atribuída pelo utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para etapas específicas. Para obter mais informações sobre como as identidades geridas funcionam em Azure HDInsight, consulte [identidades geridas em Azure HDInsight](hdinsight-managed-identities.md). Certifique-se de guardar o ID de recursos de identidade gerido para quando o adicionar à política de acesso ao Cofre de Chaves.

### <a name="create-azure-key-vault"></a>Criar cofre de chaves Azure

Criar um cofre de chaves. Consulte [Create Azure Key Vault](../key-vault/general/quick-create-portal.md) para etapas específicas.

HDInsight suporta apenas o Cofre da Chave Azure. Se tiveres o teu próprio cofre, podes importar as tuas chaves para o Cofre da Chave Azure. Lembre-se de que o cofre da chave deve ter **a exclusão soft** ativada. Para mais informações sobre a importação de chaves existentes, visite [Sobre chaves, segredos e certificados.](../key-vault/general/about-keys-secrets-certificates.md)

### <a name="create-key"></a>Criar chave

1. A partir do seu **Settings** novo cofre-chave, navegue para  >  **Definições Teclas**  >  **+ Gerar/Importar**.

    ![Gere uma nova chave no Cofre da Chave Azure](./media/disk-encryption/create-new-key.png "Gere uma nova chave no Cofre da Chave Azure")

1. Forneça um nome e, em seguida, **selecione Criar**. Mantenha o tipo de **chave** predefinido de **RSA**.

    ![gera nome chave](./media/disk-encryption/create-key.png "Gerar nome chave")

1. Quando voltar à página **Chaves,** selecione a chave que criou.

    ![lista de chaves de cofre chave](./media/disk-encryption/key-vault-key-list.png)

1. Selecione a versão para abrir a página **Versão Chave.** Quando utilizar a sua própria chave para encriptação de cluster HDInsight, precisa fornecer o URI chave. Copie o **identificador chave** e guarde-o em algum lugar até estar pronto para criar o seu cluster.

    ![obter identificador chave](./media/disk-encryption/get-key-identifier.png)

### <a name="create-access-policy"></a>Criar política de acesso

1. A partir do seu novo cofre-chave, navegue para **definições**  >  **Políticas de acesso**+ Adicionar Política de  >  **Acesso**.

    ![Criar nova política de acesso ao Cofre da Chave Azure](./media/disk-encryption/key-vault-access-policy.png)

1. A partir da página **de política de acesso adicionar,** forneça as seguintes informações:

    |Propriedade |Descrição|
    |---|---|
    |Permissões-chave|**Selecione Obter**, **Desembrulhar a tecla** e **embrulhar a tecla**.|
    |Permissões Secretas|**Selecione Obter,** **Definir** e **Eliminar**.|
    |Selecione principal|Selecione a identidade gerida atribuída pelo utilizador que criou anteriormente.|

    ![Definir O principal selecionado para a política de acesso a aacaure key vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Selecione **Adicionar**.

1. Selecione **Guardar**.

    ![Salvar a política de acesso ao cofre da chave Azure](./media/disk-encryption/add-key-vault-access-policy-save.png)

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Criar cluster com encriptação de disco de chave gerida pelo cliente

Está agora pronto para criar um novo cluster HDInsight. As chaves geridas pelo cliente só podem ser aplicadas a novos clusters durante a criação do cluster. A encriptação não pode ser removida dos clusters-chave geridos pelo cliente, e as chaves geridas pelo cliente não podem ser adicionadas aos clusters existentes.

#### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Durante a criação do cluster, forneça o **identificador chave** completo, incluindo a versão chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Também precisa atribuir a identidade gerida ao cluster e fornecer o URI chave.

![Criar novo cluster](./media/disk-encryption/create-cluster-portal.png)

#### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

O exemplo a seguir mostra como usar o Azure CLI para criar um novo cluster Apache Spark com encriptação de disco ativada. Para mais informações, consulte [Azure CLI az hdinsight create](/cli/azure/hdinsight#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Utilizar modelos do Azure Resource Manager

O exemplo a seguir mostra como usar um modelo de Gestor de Recursos Azure para criar um novo cluster Apache Spark com encriptação de disco ativada. Para mais informações, veja [quais são os modelos ARM?](../azure-resource-manager/templates/overview.md)

Este exemplo utiliza o PowerShell para chamar o modelo.

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

O conteúdo do modelo de gestão de `azuredeploy.json` recursos:

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

### <a name="rotating-the-encryption-key"></a>Rotação da chave de encriptação

Pode haver cenários em que poderá querer alterar as chaves de encriptação utilizadas pelo cluster HDInsight depois de ter sido criada. Isto pode ser facilmente através do portal. Para esta operação, o cluster deve ter acesso à tecla atual e à nova chave pretendida, caso contrário o funcionamento da chave rotativa falhará.

#### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para rodar a chave, precisa do cofre uri da chave base. Assim que o tiver feito, aceda à secção de propriedades do cluster HDInsight no portal e clique na **Chave de Alteração** no URL da Chave de **Encriptação do Disco**. Introduza o novo url-chave e submeta-se para rodar a chave.

![girar a chave de encriptação do disco](./media/disk-encryption/change-key.png)

#### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

O exemplo a seguir mostra como rodar a chave de encriptação do disco para um cluster HDInsight existente. Para obter mais informações, consulte [a chave de encriptação rotativa Azure CLI az hdinsight.](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key)

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>FAQ para encriptação de chaves gerida pelo cliente

**Como é que o cluster HDInsight acede ao meu cofre de chaves?**

O HDInsight acede ao seu Azure Key Vault usando a identidade gerida que associa ao cluster HDInsight. Esta identidade gerida pode ser criada antes ou durante a criação do cluster. Também precisa de conceder o acesso de identidade gerido ao cofre onde a chave está armazenada.

**Esta funcionalidade está disponível para todos os clusters em HDInsight?**

A encriptação de chaves gerida pelo cliente está disponível para todos os tipos de cluster, exceto o Spark 2.1 e o 2.2.

**Posso usar várias chaves para encriptar diferentes discos ou pastas?**

Não, todos os discos geridos e discos de recursos são encriptados pela mesma chave.

**O que acontece se o cluster perder acesso ao cofre ou à chave?**

Se o cluster perder acesso à chave, os avisos serão mostrados no portal Apache Ambari. Neste estado, a operação **Change Key** falhará. Uma vez restaurado o acesso à chave, os avisos de Ambari desaparecerão e operações como a rotação da chave podem ser executadas com sucesso.

![acesso chave Alerta Ambari](./media/disk-encryption/ambari-alert.png)

**Como posso recuperar o aglomerado se as chaves são apagadas?**

Uma vez que apenas as teclas ativadas "Soft Delete" são suportadas, se as teclas forem recuperadas no cofre da chave, o cluster deverá recuperar o acesso às teclas. Para recuperar uma chave Azure Key Vault, consulte [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [az-keyvault-key-recovery](/cli/azure/keyvault/key#az-keyvault-key-recover).


**Se um cluster for dimensionado, os novos nós suportam as chaves geridas pelo cliente sem problemas?**

Sim. O cluster precisa de acesso à chave no cofre durante a escala. A mesma chave é usada para encriptar tanto discos geridos como discos de recursos no cluster.

**As chaves geridas pelo cliente estão disponíveis na minha localização?**

As chaves geridas pelo cliente HDInsight estão disponíveis em todas as nuvens públicas e nuvens nacionais.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Encriptação no anfitrião usando teclas geridas pela plataforma

### <a name="enable-in-the-azure-portal"></a>Ativar no portal Azure

A encriptação no anfitrião pode ser ativada durante a criação do cluster no portal Azure.

> [!Note]
> Quando a encriptação no anfitrião está ativada, não é possível adicionar aplicações ao seu cluster HDInsight a partir do mercado Azure.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Ativar a encriptação no anfitrião.":::

Esta opção permite [a encriptação no anfitrião](../virtual-machines/disks-enable-host-based-encryption-portal.md) para discos de dados temporários HDInsight VMs utilizando PMK. A encriptação no anfitrião só é [suportada em certos SKUs VM em regiões limitadas](../virtual-machines/disks-enable-host-based-encryption-portal.md) e o HDInsight suporta a [seguinte configuração de nó e SKUs](./hdinsight-supported-node-configuration.md).

Para compreender o tamanho VM certo para o seu cluster HDInsight consulte [Selecionar o tamanho VM certo para o seu cluster Azure HDInsight](hdinsight-selecting-vm-size.md). O VM SKU padrão para o nó Zookeeper quando a encriptação no hospedeiro estiver ativada será DS2V2.

### <a name="enable-using-powershell"></a>Ativar através do PowerShell

O seguinte corte de código mostra como pode criar um novo cluster Azure HDInsight que tem encriptação no anfitrião ativada usando o PowerShell. Utiliza o parâmetro `-EncryptionAtHost $true` para ativar a funcionalidade.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Ativar através do CLI do Azure

O seguinte corte de código mostra como pode criar um novo cluster Azure HDInsight que tem encriptação no anfitrião ativada, utilizando o Azure CLI. Utiliza o parâmetro `--encryption-at-host true` para ativar a funcionalidade.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o Cofre da Chave Azure, consulte [o cofre da chave Azure](../key-vault/general/overview.md).
* [Visão geral da segurança da empresa em Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
