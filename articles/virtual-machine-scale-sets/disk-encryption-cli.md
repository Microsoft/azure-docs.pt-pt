---
title: Criptografar discos para conjuntos de dimensionamento do Azure com CLI do Azure
description: Saiba como usar Azure PowerShell para criptografar instâncias de VM e discos anexados em um conjunto de dimensionamento de máquinas virtuais do Windows
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279081"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Criptografar o sistema operacional e OS discos de dados anexados em um conjunto de dimensionamento de máquinas virtuais com o CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de início rápido mostra como usar o CLI do Azure para criar e criptografar um conjunto de dimensionamento de máquinas virtuais. Para obter mais informações sobre como aplicar a criptografia de disco do Azure a um conjunto de dimensionamento de máquinas virtuais, consulte [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando o CLI do Azure versão 2.0.31 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento

Para poder criar um conjunto de dimensionamento, crie primeiro um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss). O exemplo seguinte cria um conjunto de dimensionamento com o nome *myScaleSet*, que está definido para atualizar automaticamente à medida que as alterações são aplicadas, e gera chaves SSH, caso não existam em *~/.ssh/id_rsa*. Um disco de dados de 32 GB é anexado a cada instância de VM e a [extensão de script personalizado](../virtual-machines/linux/extensions-customscript.md) do Azure é usada para preparar os discos de dados com o [conjunto de extensões AZ vmss](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um cofre de chaves do Azure habilitado para criptografia de disco

Azure Key Vault pode armazenar chaves, segredos ou senhas que permitem implementá-los com segurança em seus aplicativos e serviços. As chaves de criptografia são armazenadas em Azure Key Vault usando a proteção de software ou você pode importar ou gerar suas chaves em HSMs (módulos de segurança de hardware) certificados para os padrões FIPS 140-2 nível 2. Essas chaves de criptografia são usadas para criptografar e descriptografar discos virtuais anexados à sua VM. Você mantém o controle dessas chaves de criptografia e pode auditar seu uso.

Defina seu próprio *keyvault_name*exclusivo. Em seguida, crie um keyvault com [AZ keyvault criar](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) na mesma assinatura e região que o conjunto de dimensionamento e defina a política de acesso *habilitado para criptografia de disco* .

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Usar um Key Vault existente

Esta etapa só será necessária se você tiver um Key Vault existente que deseja usar com a criptografia de disco. Ignore esta etapa se você criou um Key Vault na seção anterior.

Defina seu próprio *keyvault_name*exclusivo. Em seguida, atualize o keyvault com [AZ keyvault Update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) e defina a política de acesso *habilitado para criptografia de disco* .

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Habilitar criptografia

Para criptografar instâncias de VM em um conjunto de dimensionamento, primeiro obtenha algumas informações sobre a Key Vault ID de recurso com [AZ keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Essas variáveis são usadas para iniciar o processo de criptografia com [AZ vmss Encryption Enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Pode levar um minuto ou dois para que o processo de criptografia seja iniciado.

Como o conjunto de dimensionamento é a política de atualização no conjunto de dimensionamento criado em uma etapa anterior definida como *automática*, as instâncias de VM iniciam automaticamente o processo de criptografia. Em conjuntos de dimensionamento em que a política de atualização é manual, inicie a política de criptografia nas instâncias de VM com [AZ vmss Update-instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Habilitar criptografia usando KEK para encapsular a chave

Você também pode usar uma chave de criptografia de chave para aumentar a segurança ao criptografar o conjunto de dimensionamento de máquinas virtuais.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:</br>
/subscriptions/[Subscription-ID-GUID]/resourceGroups/[nome-do-grupo-de-recursos]/providers/Microsoft.KeyVault/vaults/[keyvault-nome]</br></br>
> A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em:</br>
https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID]

## <a name="check-encryption-progress"></a>Verificar o progresso da criptografia

Para verificar o status da criptografia de disco, use [AZ vmss Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Quando as instâncias de VM são criptografadas, o código de status relata *criptografiastate/criptografado*, conforme mostrado na seguinte saída de exemplo:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Desativar a encriptação

Se você não deseja mais usar discos de instâncias de VM criptografadas, você pode desabilitar a criptografia com [AZ vmss Encryption Disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) da seguinte maneira:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Passos seguintes

- Neste artigo, você usou a CLI do Azure para criptografar um conjunto de dimensionamento de máquinas virtuais. Você também pode usar modelos de [Azure PowerShell](disk-encryption-powershell.md) ou [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Se desejar ter Azure Disk Encryption aplicado depois que outra extensão for provisionada, você poderá usar o [sequenciamento de extensão](virtual-machine-scale-sets-extension-sequencing.md). 
- Um exemplo de arquivo em lotes de ponta a ponta para criptografia de disco de dados do conjunto de dimensionamento do Linux pode ser encontrado [aqui](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Este exemplo cria um grupo de recursos, o conjunto de dimensionamento do Linux, monta um disco de dados de 5 GB e encripta o conjunto de dimensionamento de máquina virtual.
