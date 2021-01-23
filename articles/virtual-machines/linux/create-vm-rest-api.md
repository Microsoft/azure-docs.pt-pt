---
title: Criar um Linux VM com a API REST
description: Saiba como criar uma máquina virtual Linux em Azure que utiliza Discos Geridos e autenticação SSH com API AZure REST.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: b3eeaf5f343b6026e02d17e4d3bd90dba4b991c4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737050"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Crie uma máquina virtual Linux que utilize a autenticação SSH com a API REST

Uma máquina virtual Linux (VM) em Azure é constituída por vários recursos, tais como discos e interfaces de rede e define parâmetros como a localização, tamanho e imagem do sistema operativo e configurações de autenticação.

Pode criar um Linux VM através do portal Azure, Azure CLI 2.0, muitos SDKs Azure, modelos de Gestor de Recursos Azure e muitas ferramentas de terceiros, como Ansible ou Terraform. Todas estas ferramentas acabam por utilizar a API REST para criar o Linux VM.

Este artigo mostra-lhe como usar a API REST para criar um Linux VM executando Ubuntu 18.04-LTS com discos geridos e autenticação SSH.

## <a name="before-you-start"></a>Antes de começar

Antes de criar e submeter o pedido, necessitará de:

* O `{subscription-id}` para a sua subscrição
  * Se tiver várias subscrições, consulte [Trabalhar com várias subscrições](/cli/azure/manage-azure-subscriptions-azure-cli)
* A `{resourceGroupName}` que criaste antes do tempo
* Uma [interface de rede virtual](../../virtual-network/virtual-network-network-interface.md) no mesmo grupo de recursos
* Um par de chaves SSH (pode [gerar um novo](mac-create-ssh-keys.md) se não tiver um)

## <a name="request-basics"></a>Solicitar o básico

Para criar ou atualizar uma máquina virtual, utilize a seguinte operação *PUT:*

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Além dos `{subscription-id}` `{resourceGroupName}` parâmetros e parâmetros, você precisa especificar o `{vmName}` ( é `api-version` opcional, mas este artigo foi testado `api-version=2017-12-01` com)

Os seguintes cabeçalhos são obrigatórios:

| Cabeçalho do pedido   | Descrição |
|------------------|-----------------|
| *Content-Type:*  | Necessário. Definido como `application/json`. |
| *Authorization:* | Necessário. Definido como um [token de acesso `Bearer` ](/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |

Para obter informações gerais sobre o trabalho com pedidos de API REST, consulte [componentes de um pedido/resposta da API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo de pedido

As seguintes definições comuns são utilizadas para construir um organismo de pedido:

| Name                       | Necessário | Tipo                                                                                | Descrição  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| localização                   | Verdadeiro     | string                                                                              | Localização de recursos. |
| name                       |          | string                                                                              | Nome para a máquina virtual. |
| propriedades.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Especifica as definições de hardware para a máquina virtual. |
| propriedades.storageProfile  |          | [ArmazenamentoProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Especifica as definições de armazenamento dos discos de máquinas virtuais. |
| propriedades.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Especifica as definições do sistema operativo para a máquina virtual. |
| propriedades.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Especifica as interfaces de rede da máquina virtual. |

Um corpo de pedido de exemplo está abaixo. Certifique-se de que especifica o nome VM nos `{computerName}` parâmetros e `{name}` parâmetros, o nome da interface de rede que `networkInterfaces` criou, o seu nome de utilizador `adminUsername` e , e a parte `path` *pública* do seu keypair SSH (localizado, por exemplo, `~/.ssh/id_rsa.pub` em . `keyData` Outros parâmetros que pode querer modificar incluem `location` e `vmSize` .  

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

Para obter uma lista completa das definições disponíveis no organismo de pedido, consulte [máquinas virtuais para criar ou atualizar definições do corpo do pedido](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Envio do pedido

Poderá utilizar o cliente da sua preferência para o envio deste pedido HTTP. Também pode utilizar uma [ferramenta no navegador](/rest/api/compute/virtualmachines/createorupdate) clicando no botão **Tentar.**

### <a name="responses"></a>Respostas

Existem duas respostas bem sucedidas para a operação para criar ou atualizar uma máquina virtual:

| Nome        | Tipo                                                                              | Descrição |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Criado | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Criado     |

Uma resposta condensada *201 Criada* a partir do organismo de pedido de exemplo anterior que cria um VM mostra que um *vmId* foi atribuído e o *Estado de provisionamento* está *a criar:*

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Para obter mais informações sobre as respostas da API rest, consulte [processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre as APIs Azure REST ou outras ferramentas de gestão, tais como Azure CLI ou Azure PowerShell, consulte o seguinte:

- [Azure Compute provider REST API](/rest/api/compute/)
- [Introdução à API REST do Azure](/rest/api/azure/)
- [CLI do Azure](/cli/azure/)
- [Módulo do Azure PowerShell](/powershell/azure/)
