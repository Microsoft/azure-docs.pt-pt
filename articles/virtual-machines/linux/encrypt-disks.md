---
title: Encriptar discos numa VM do Linux no Azure | Documentos da Microsoft
description: Como encriptar discos virtuais numa VM do Linux para uma maior segurança com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731622"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Como encriptar uma máquina virtual do Linux no Azure

Para segurança aprimorada de máquina virtual (VM) e de conformidade, discos virtuais e a própria VM podem ser encriptados. As VMs são encriptadas com chaves criptográficas que são protegidas num Azure Key Vault. Pode controlar estas chaves criptográficas e pode auditar o seu uso. Este artigo detalha como encriptar discos virtuais numa VM do Linux com a CLI do Azure. 

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.30 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Descrição geral da encriptação de disco
Discos virtuais em VMs do Linux são encriptados através de rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Não existe nenhum custo associado para encriptar discos virtuais no Azure. As chaves criptográficas são armazenadas no Azure Key Vault com a proteção de software, ou pode importar ou gerar as suas chaves em módulos de segurança de Hardware (HSMs) com certificação FIPS 140-2 de padrões no nível 2. Manter o controlo destas chaves criptográficas e pode auditar o seu uso. Estas chaves criptográficas são utilizados para encriptar e desencriptar os discos virtuais anexados a sua VM. 

O processo para encriptar uma VM é o seguinte:

1. Crie uma chave criptográfica num Azure Key Vault.
1. Configure a chave criptográfica para ser utilizada para encriptar discos.
1. Ative a encriptação de disco para os discos virtuais.
1. As chaves criptográficas necessárias são solicitadas no Azure Key Vault.
1. Os discos virtuais são encriptados com a chave criptográfica fornecida.


## <a name="requirements-and-limitations"></a>Requisitos e limitações
Cenários suportados e os requisitos para encriptação de disco:

* O seguinte servidor Linux SKUs - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e Red Hat Enterprise Linux.
* Todos os recursos (por exemplo, o Key Vault, a conta de armazenamento e a VM) tem de ser na mesma região do Azure e subscrição.
* Um padrão, D, DS, G, GS, etc., as VMs da série.
* A atualizar as chaves criptográficas numa VM do Linux já encriptados.

Encriptação de disco não é atualmente suportada nos seguintes cenários:

* VMs de escalão básico.
* VMs criadas com o modelo de implementação clássica.
* A desativação da encriptação de disco de SO em VMs do Linux.
* Utilização de imagens do Linux personalizadas.

Para obter mais informações sobre cenários suportados e limitações, consulte [Azure Disk Encryption para IaaS VMs](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Criar um Azure Key Vault e chaves
Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluem *myResourceGroup*, *myKey*, e *myVM*.

A primeira etapa é criar um Azure Key Vault para armazenar as chaves criptográficas. O Azure Key Vault pode armazenar chaves, segredos ou palavras-passe que permitem-lhe implementá-los em segurança nas suas aplicações e serviços. Para a encriptação de disco virtual, utilize o Key Vault para armazenar uma chave criptográfica utilizada para encriptar ou desencriptar os discos virtuais.

Ativar o fornecedor do Azure Key Vault com a sua subscrição do Azure com [Registre-se fornecedor de az](/cli/azure/provider#az-provider-register) e crie um grupo de recursos com [criar grupo az](/cli/azure/group#az-group-create). O exemplo seguinte cria um nome de grupo de recursos *myResourceGroup* no `eastus` localização:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

O Cofre de chaves do Azure que contém as chaves criptográficas e os recursos de computação associado como o armazenamento e a própria VM têm de residir na mesma região. Criar um Azure Key Vault com [az keyvault criar](/cli/azure/keyvault#az-keyvault-create) e ativar o Key Vault para utilização com a encriptação de disco. Especifique um nome exclusivo do Key Vault para *keyvault_name* da seguinte forma:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Pode armazenar as chaves criptográficas com o software ou de proteção de modelo de segurança de Hardware (HSM). Utilizar um HSM requer um cofre de chaves de premium. Existe um custo adicional para a criação de uma premium Key Vault, em vez de padrão Key Vault que armazena as chaves protegidas por software. Para criar um cofre de chaves de premium, no passo anterior adicione `--sku Premium` ao comando. O exemplo seguinte utiliza as chaves protegidas por software, uma vez que criou um Key Vault padrão.

Para ambos os modelos de proteção, a plataforma do Azure tem de ter acesso para solicitar as chaves criptográficas quando a VM arranca para desencriptar os discos virtuais. Criar uma chave criptográfica no seu Cofre de chaves com [criar chave de Cofre de chaves de az](/cli/azure/keyvault/key#az-keyvault-key-create). O exemplo seguinte cria uma chave denominada *myKey*:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Crie uma VM com [az vm criar](/cli/azure/vm#az-vm-create) e anexar um disco de dados de 5 Gb. Apenas determinadas imagens do marketplace suportam a encriptação de disco. O exemplo seguinte cria uma VM com o nome *myVM* usando um *Ubuntu 16.04 LTS* imagem:

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH para a VM com o *publicIpAddress* apresentados no resultado do comando anterior. Criar uma partição e o sistema de ficheiros, em seguida, Monte o disco de dados. Para obter mais informações, consulte [ligar a uma VM do Linux para montar o disco novo](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Feche a sessão SSH.


## <a name="encrypt-the-virtual-machine"></a>Encriptar a máquina virtual


Encriptar VM com [ativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-enable):

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Demora algum tempo para conclusão do processo de encriptação de disco. Monitorizar o estado do processo com [show do az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

Quando terminar, o resultado será semelhante ao seguinte exemplo:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Adicionar discos de dados adicionais
Depois de ter a criptografar seus discos de dados, pode adicionar mais discos virtuais para a VM e criptografá-los. 

Assim que o disco de dados foi adicionado à VM, execute novamente o comando para encriptar os discos virtuais da seguinte forma:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações sobre como gerir o Azure Key Vault, incluindo a eliminar as chaves criptográficas e os cofres, consulte [gerir Key Vault com a CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Para obter mais informações sobre a encriptação de disco, como preparar uma VM encriptada personalizada para carregar para o Azure, consulte [do Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
