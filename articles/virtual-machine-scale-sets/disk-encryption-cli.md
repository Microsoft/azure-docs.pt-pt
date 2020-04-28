---
title: Criptografe discos para conjuntos de escala Azure com Azure CLI
description: Aprenda a usar o Azure PowerShell para encriptar instâncias VM e discos anexados num conjunto de escala de máquina virtual do Windows
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 557d5c023acbc7987d58c9e78bfe11e25f314879
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279081"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Criptografe os OS e discos de dados anexados numa escala de máquina virtual com o AZURE CLI

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart mostra-lhe como usar o CLI Azure para criar e encriptar um conjunto de escala de máquina virtual. Para obter mais informações sobre a aplicação da encriptação do Disco Azure num conjunto de escala de máquina virtual, consulte a encriptação do [disco Azure para conjuntos](disk-encryption-overview.md)de escala de máquina virtual .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.0.31 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento

Para poder criar um conjunto de dimensionamento, crie primeiro um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss). O exemplo seguinte cria um conjunto de dimensionamento com o nome *myScaleSet*, que está definido para atualizar automaticamente à medida que as alterações são aplicadas, e gera chaves SSH, caso não existam em *~/.ssh/id_rsa*. Um disco de dados de 32Gb é anexado a cada instância VM, e a [extensão do script personalizado](../virtual-machines/linux/extensions-customscript.md) Azure é usada para preparar os discos de dados com conjunto de [extensão az vmss:](/cli/azure/vmss/extension)

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Criar um cofre de chaves Azure habilitado para encriptação de disco

O Azure Key Vault pode armazenar chaves, segredos ou senhas que lhe permitam implementá-las de forma segura nas suas aplicações e serviços. As chaves criptográficas são armazenadas no Cofre de Chaves Azure utilizando a proteção de software, ou pode importar ou gerar as suas chaves em Módulos de Segurança de Hardware (HSMs) certificados para normas de nível 2 FIPS 140-2. Estas teclas criptográficas são usadas para encriptar e desencriptar discos virtuais ligados ao seu VM. Mantém o controlo destas chaves criptográficas e pode auditar a sua utilização.

Defina o seu próprio *keyvault_name*único. Em seguida, crie um KeyVault com [keyvault az criar](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) na mesma subscrição e região que o conjunto de escala, e definir a política de acesso de *encriptação de discos ativada por disco.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Use um cofre de chaves existente

Este passo só é necessário se tiver um Cofre chave existente que deseja utilizar com encriptação de disco. Ignore este passo se criou um Cofre chave na secção anterior.

Defina o seu próprio *keyvault_name*único. Em seguida, atualizou o seu KeyVault com [a atualização do keyvault az](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) e definiu a política de acesso de *encriptação ativada por discos.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Ativar encriptação

Para encriptar as instâncias vm num conjunto de escala, obtenha primeiro algumas informações sobre o ID de recurso key vault com [o az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Estas variáveis são usadas para iniciar o processo de encriptação com [o az vmss enable:](/cli/azure/vmss/encryption#az-vmss-encryption-enable)

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

Pode levar um minuto ou dois para o processo de encriptação começar.

Como o conjunto de escala é a política de atualização no conjunto de escala criado num passo anterior é definido para *automático,* as instâncias vM iniciam automaticamente o processo de encriptação. Em conjuntos de escala onde a política de atualização é manual, inicie a política de encriptação nas instâncias VM com [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Ativar a encriptação usando KEK para embrulhar a chave

Também pode utilizar uma chave de encriptação para uma maior segurança ao encriptar o conjunto de escala de máquina virtual.

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
>  A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa:</br>
/subscrições/[subscrição-id-guid]/recursosGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[[keyvault-name]</br></br>
> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Verifique o progresso da encriptação

Para verificar o estado da encriptação do disco, utilize o programa de [encriptação az vmss:](/cli/azure/vmss/encryption#az-vmss-encryption-show)

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Quando as instâncias vm são encriptadas, o código de estado *reporta EncriptaçãoEstado/encriptado,* como mostra a seguinte saída de exemplo:

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

Se já não pretender utilizar discos de instâncias VM encriptados, pode desativar a encriptação com [encriptação az vmss desativar](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) da seguinte forma:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Passos seguintes

- Neste artigo, utilizou o Azure CLI para encriptar um conjunto de escala de máquina virtual. Também pode utilizar modelos [Azure PowerShell](disk-encryption-powershell.md) ou [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Se desejar que a Encriptação do Disco Azure seja aplicada após a disponibilização de outra extensão, pode utilizar [a sequenciação](virtual-machine-scale-sets-extension-sequencing.md)de extensões . 
- Um exemplo de ficheiro de lote de ponta a ponta para encriptação de disco de conjunto de linha Linux pode ser encontrado [aqui](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Este exemplo cria um grupo de recursos, conjunto de escala Linux, monta um disco de dados de 5 GB e encripta o conjunto de escala de máquina virtual.
