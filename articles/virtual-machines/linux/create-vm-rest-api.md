---
title: Criar uma máquina virtual Linux com a API de REST do Azure | Documentos da Microsoft
description: Saiba como criar uma máquina virtual Linux no Azure que utiliza a autenticação de Managed Disks e o SSH com a API REST do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 2b078cd769a9b4e5e66fe132fd4ef73ec4621efc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447851"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Criar uma máquina virtual do Linux que utiliza autenticação SSH com a API REST

Uma máquina virtual (VM) do Linux no Azure consiste em vários recursos, tais como discos e interfaces de rede e define os parâmetros, tais como definições de imagem e a autenticação de localização, o tamanho e o sistema operativo.

Pode criar uma VM do Linux através do portal do Azure, Azure CLI 2.0, vários SDKs do Azure, modelos Azure Resource Manager e muitas ferramentas de terceiros, como Ansible ou Terraform. Todas essas ferramentas, por fim, utilizam a API REST para criar a VM do Linux.

Este artigo mostra-lhe como utilizar a API REST para criar uma VM do Linux com o Ubuntu 18.04-LTS com discos geridos e autenticação SSH.

## <a name="before-you-start"></a>Antes de começar

Antes de criar e submeter o pedido, terá de:

* O `{subscription-id}` para a sua subscrição
  * Se tiver várias subscrições, veja [trabalhar com várias subscrições](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* A `{resourceGroupName}` que criou antes do tempo
* R [interface de rede virtual](../../virtual-network/virtual-network-network-interface.md) no mesmo grupo de recursos
* Um par de chaves SSH (pode [gerar um novo](mac-create-ssh-keys.md) se não tiver um)

## <a name="request-basics"></a>Noções básicas de pedido

Para criar ou atualizar uma máquina virtual, utilize o seguinte procedimento *colocar* operação:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Além da `{subscription-id}` e `{resourceGroupName}` parâmetros, terá de especificar o `{vmName}` (`api-version` é opcional, mas este artigo foi testado com `api-version=2017-12-01`)

Os seguintes cabeçalhos são necessários:

| Cabeçalho do pedido   | Descrição |
|------------------|-----------------|
| *Content-Type:*  | Necessário. Definido como `application/json`. |
| *Autorização:* | Necessário. Definido como válido `Bearer` [token de acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Para obter informações gerais sobre como trabalhar com pedidos de REST API, consulte [componentes de uma REST API de solicitação/resposta](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo do pedido

As seguintes definições comuns são utilizadas para criar um corpo de pedido:

| Name                       | Necessário | Tipo                                                                                | Descrição  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| localização                   | Verdadeiro     | string                                                                              | Localização do recurso. |
| nome                       |          | string                                                                              | Nome da máquina virtual. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Especifica as definições de hardware para a máquina virtual. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Especifica as definições de armazenamento para os discos da máquina virtual. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Especifica as definições de sistema operativo da máquina virtual. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Especifica as interfaces de rede da máquina virtual. |

É um corpo de pedido de exemplo abaixo. Certifique-se de especificar o nome da VM no `{computerName}` e `{name}` parâmetros, o nome da interface de rede que criou em `networkInterfaces`, seu nome de utilizador no `adminUsername` e `path`e o *public*parte do seu par de chaves SSH (localizado, por exemplo, `~/.ssh/id_rsa.pub`) no `keyData`. Outros parâmetros que pode querer modificar incluem `location` e `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Para obter uma lista completa das definições de disponíveis no corpo do pedido, consulte [máquinas virtuais, criar ou atualizar as definições de corpo de pedido](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Enviar o pedido

Pode utilizar o cliente de sua preferência para enviar este pedido HTTP. Também pode utilizar um [no browser ferramenta](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) clicando a **experimente** botão.

### <a name="responses"></a>Respostas

Existem duas respostas com êxito para a operação criar ou atualizar uma máquina virtual:

| Name        | Tipo                                                                              | Descrição |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 criado | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Criado     |

Um condensada *201 criado* mostra a resposta do corpo de pedido de exemplo anterior que cria uma VM uma *vmId* foi atribuído e o *provisioningState* é *Criando*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Para obter mais informações sobre as respostas de REST API, consulte [processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as APIs de REST do Azure ou outras ferramentas de gestão, como a CLI do Azure ou do Azure PowerShell, consulte o seguinte:

- [API de REST do fornecedor de computação do Azure](/rest/api/compute/)
- [Introdução à API REST do Azure](/rest/api/azure/)
- [CLI do Azure](/cli/azure/)
- [Módulo Azure PowerShell](/powershell/azure/overview)
