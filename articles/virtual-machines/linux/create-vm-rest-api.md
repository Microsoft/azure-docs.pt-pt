---
title: Criar uma máquina virtual Linux com a API REST do Azure | Microsoft Docs
description: Saiba como criar uma máquina virtual do Linux no Azure que usa Managed Disks e autenticação SSH com a API REST do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: 9851305bdaa2f214e0d00eda3235068cac2ea980
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083483"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Criar uma máquina virtual Linux que usa a autenticação SSH com a API REST

Uma VM (máquina virtual) do Linux no Azure consiste em vários recursos, como discos e interfaces de rede, e define parâmetros como localização, tamanho, imagem do sistema operacional e configurações de autenticação.

Você pode criar uma VM do Linux por meio do portal do Azure, CLI do Azure 2,0, muitos SDKs do Azure, modelos de Azure Resource Manager e muitas ferramentas de terceiros, como Ansible ou Terraform. Todas essas ferramentas, por fim, usam a API REST para criar a VM do Linux.

Este artigo mostra como usar a API REST para criar uma VM Linux que executa o Ubuntu 18, 4-LTS com discos gerenciados e a autenticação SSH.

## <a name="before-you-start"></a>Antes de começar

Antes de criar e enviar a solicitação, será necessário:

* O `{subscription-id}` para sua assinatura
  * Se você tiver várias assinaturas, consulte [trabalhando com várias assinaturas](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)
* Um `{resourceGroupName}` que você criou antecipadamente
* Uma [interface de rede virtual](../../virtual-network/virtual-network-network-interface.md) no mesmo grupo de recursos
* Um par de chaves SSH (você pode [gerar um novo](mac-create-ssh-keys.md) se não tiver um)

## <a name="request-basics"></a>Noções básicas de solicitação

Para criar ou atualizar uma máquina virtual, use a seguinte operação *Put* :

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

`{subscription-id}` Além dos `api-version=2017-12-01`parâmetros e `{resourceGroupName}` , você precisará especificar o `{vmName}` (`api-version` é opcional, mas este artigo foi testado com)

Os seguintes cabeçalhos são necessários:

| Cabeçalho da solicitação   | Descrição |
|------------------|-----------------|
| *Tipo de conteúdo:*  | Necessário. Defina como `application/json`. |
| *Authorization:* | Necessário. Defina como um token `Bearer` de [acesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)válido. |

Para obter informações gerais sobre como trabalhar com solicitações da API REST, consulte [componentes de uma solicitação/resposta da API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Criar o corpo da solicitação

As seguintes definições comuns são usadas para criar um corpo de solicitação:

| Name                       | Requerido | Tipo                                                                                | Descrição  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | Cadeia de caracteres                                                                              | Localização do recurso. |
| name                       |          | Cadeia de caracteres                                                                              | Nome para a máquina virtual. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Especifica as configurações de hardware para a máquina virtual. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Especifica as configurações de armazenamento para os discos de máquina virtual. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Especifica as configurações do sistema operacional para a máquina virtual. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Especifica as interfaces de rede da máquina virtual. |

Um corpo de solicitação de exemplo está abaixo. Certifique-se de especificar o nome da VM `{computerName}` nos `{name}` parâmetros e, o nome da `networkInterfaces`interface de rede que você criou, seu nome de `adminUsername` usuário `path`no e e a parte *pública* do seu ssh par de chaves (localizado em, por exemplo `~/.ssh/id_rsa.pub`,) `keyData`em. Outros parâmetros que você pode querer modificar incluem `location` e `vmSize`.  

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

Para obter uma lista completa das definições disponíveis no corpo da solicitação, consulte [máquinas virtuais criar ou atualizar definições do corpo da solicitação](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Enviando a solicitação

Você pode usar o cliente de sua preferência para enviar essa solicitação HTTP. Você também pode usar uma [ferramenta no navegador](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) clicando no botão **experimentar** .

### <a name="responses"></a>Responses

Há duas respostas bem-sucedidas para a operação criar ou atualizar uma máquina virtual:

| Name        | Tipo                                                                              | Descrição |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 criado | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Criado     |

Uma resposta condensada *201 criada* do corpo da solicitação de exemplo anterior que cria uma VM mostra que um *vmId* foi atribuído e o *provisioningState* está *criando*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Para obter mais informações sobre as respostas da API REST, consulte [processar a mensagem de resposta](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as APIs REST do Azure ou outras ferramentas de gerenciamento, como CLI do Azure ou Azure PowerShell, consulte o seguinte:

- [API REST do provedor de computação do Azure](/rest/api/compute/)
- [Introdução à API REST do Azure](/rest/api/azure/)
- [CLI do Azure](/cli/azure/)
- [Módulo Azure PowerShell](/powershell/azure/overview)
