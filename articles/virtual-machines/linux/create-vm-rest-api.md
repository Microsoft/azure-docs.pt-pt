---
title: Crie um VM Linux com a API REST
description: Aprenda a criar uma máquina virtual Linux em Azure que utilize discos geridos e autenticação SSH com API Bluee REST.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 1594c030839cccdd48c4b032c6ad92f746f78e26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78970278"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Crie uma máquina virtual Linux que utilize a autenticação SSH com a API REST

Uma máquina virtual Linux (VM) em Azure é composta por vários recursos, tais como discos e interfaces de rede e define parâmetros como localização, tamanho e imagem do sistema operativo e configurações de autenticação.

Você pode criar um Linux VM através do portal Azure, Azure CLI 2.0, muitos SDKs Azure, modelos de Gestor de Recursos Azure e muitas ferramentas de terceiros como Ansible ou Terraform. Todas estas ferramentas acabam por utilizar a API REST para criar o VM Linux.

Este artigo mostra-lhe como usar a API REST para criar um Linux VM executando Ubuntu 18.04-LTS com discos geridos e autenticação SSH.

## <a name="before-you-start"></a>Antes de começar

Antes de criar e submeter o pedido, necessitará:

* Para `{subscription-id}` a sua subscrição
  * Se tiver várias subscrições, consulte [Trabalhar com várias subscrições](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* A `{resourceGroupName}` que criaste antes do tempo
* Uma [interface de rede virtual](../../virtual-network/virtual-network-network-interface.md) no mesmo grupo de recursos
* Um par de chaves SSH (pode [gerar um novo](mac-create-ssh-keys.md) se não tiver um)

## <a name="request-basics"></a>Solicitar fundamentos

Para criar ou atualizar uma máquina virtual, utilize a seguinte operação *PUT:*

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Além dos `{subscription-id}` `{resourceGroupName}` parâmetros e parâmetros, terá `{vmName}` `api-version` de especificar o ( é `api-version=2017-12-01`opcional, mas este artigo foi testado com)

Os seguintes cabeçalhos são obrigatórios:

| Cabeçalho do pedido   | Descrição |
|------------------|-----------------|
| *Content-Type:*  | Necessário. Definido como `application/json`. |
| *Authorization:* | Necessário. Definido como um  [token de acesso `Bearer`](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |

Para obter informações gerais sobre o trabalho com pedidos da API REST, consulte [Componentes de um pedido/resposta REST API](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo de pedido

As seguintes definições comuns são utilizadas para construir um organismo de pedido:

| Nome                       | Necessário | Tipo                                                                                | Descrição  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| localização                   | Verdadeiro     | string                                                                              | Localização de recursos. |
| nome                       |          | string                                                                              | Nome para a máquina virtual. |
| propriedades.hardwarePerfil |          | [HardwarePerfil](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Especifica as definições de hardware para a máquina virtual. |
| propriedades.storagePerfil  |          | [Perfil de armazenamento](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Especifica as definições de armazenamento dos discos de máquina virtual. |
| propriedades.osPerfil       |          | [Perfil osso](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Especifica as definições do sistema operativo para a máquina virtual. |
| properties.networkProfile  |          | [Perfil de rede](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Especifica as interfaces de rede da máquina virtual. |

Um corpo de pedido de exemplo está abaixo. Certifique-se de especificar o `{computerName}` nome `{name}` VM nos e parâmetros, o `networkInterfaces`nome da interface `adminUsername` `path`de rede que criou, o seu nome de utilizador `~/.ssh/id_rsa.pub`e, `keyData`e a parte *pública* do seu teclado SSH (localizado, por exemplo, ) em . Outros parâmetros que você `location` pode `vmSize`querer modificar incluem e .  

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

Para obter uma lista completa das definições disponíveis no organismo de pedido, consulte as [máquinas virtuais criar ou atualizar as definições do corpo](/rest/api/compute/virtualmachines/createorupdate#definitions)de pedidos .

## <a name="sending-the-request"></a>Envio do pedido

Pode utilizar o cliente da sua preferência para enviar este pedido HTTP. Também pode utilizar uma [ferramenta no navegador](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) clicando no botão **Experimente.**

### <a name="responses"></a>Respostas

Existem duas respostas bem sucedidas para a operação criar ou atualizar uma máquina virtual:

| Nome        | Tipo                                                                              | Descrição |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Criado | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Criado     |

Uma resposta condensada *de 201 Criada* pelo organismo de pedido de exemplo anterior que cria um VM mostra que foi atribuído um *vmId* e o Estado de *provisionamento* está *a criar:*

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Para obter mais informações sobre as respostas da API REST, consulte [Processe a mensagem de resposta](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as APIs do Azure REST ou outras ferramentas de gestão, como o Azure CLI ou o Azure PowerShell, consulte o seguinte:

- [Fornecedor de computação Azure REST API](/rest/api/compute/)
- [Introdução à API REST do Azure](/rest/api/azure/)
- [CLI do Azure](/cli/azure/)
- [Módulo do Azure PowerShell](/powershell/azure/overview)
