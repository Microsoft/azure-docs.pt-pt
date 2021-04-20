---
title: Criar um conjunto com a encriptação de disco ativada
description: Aprenda a usar a configuração de encriptação do disco para encriptar nós com uma chave gerida pela plataforma.
author: pkshultz
ms.topic: how-to
ms.date: 04/16/2021
ms.author: peshultz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01d2ea03768a09c1ad4e019b9e8ed43a26443637
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728523"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Criar um conjunto com a encriptação de disco ativada

Quando criar um pool Azure Batch utilizando [a Configuração de Máquina Virtual,](nodes-and-pools.md#virtual-machine-configuration)pode encriptar os nós de computação na piscina com uma chave gerida pela plataforma especificando a configuração de encriptação do disco.

Este artigo explica como criar um pool de Lote com encriptação de disco ativada.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Porquê usar uma piscina com configuração de encriptação de disco?

Com um pool de Lote, você pode aceder e armazenar dados no SO e discos temporários do nó computacional. Encriptar o disco do lado do servidor com uma chave gerida pela plataforma irá salvaguardar estes dados com baixa sobrecarga e conveniência.

O Batch aplicará uma destas tecnologias de encriptação de discos em nós computativos, com base na configuração da piscina e na capacidade de suporte regional.

- [Encriptação gerida do disco em repouso com teclas geridas pela plataforma](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Encriptação no hospedeiro usando uma chave gerida pela plataforma](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

Não poderá especificar qual o método de encriptação que será aplicado nos nós da sua piscina. Em vez disso, fornece os discos-alvo que pretende encriptar nos seus nós, e o Batch pode escolher o método de encriptação apropriado, garantindo que os discos especificados são encriptados no nó de computação.

> [!IMPORTANT]
> Se estiver a criar a sua piscina com uma [imagem personalizada,](batch-sig-images.md)só pode ativar a encriptação do disco se utilizar VMs do Windows.

## <a name="azure-portal"></a>Portal do Azure

Ao criar um pool de Lote no portal Azure, selecione **TemporaryDisk** ou **OsAndTemporaryDisk** sob **a Configuração de Encriptação do Disco**.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Screenshot da opção de configuração de encriptação do disco no portal Azure.":::

Após a criação da piscina, pode ver os alvos de configuração da encriptação do disco na secção **Propriedades** da piscina.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Screenshot mostrando os alvos de configuração de encriptação do disco no portal Azure.":::

## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram como encriptar o SISTEMA e os discos temporários num pool de lote utilizando o Batch .NET SDK, o Batch REST API e o Azure CLI.

### <a name="batch-net-sdk"></a>Batch .NET SDK (SDK .NET para o Batch)

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Lote REST API

URL DE REPOUSO API:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Corpo do pedido:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a encriptação do lado do servidor do Azure Disk Storage](../virtual-machines/disk-encryption.md).
- Para obter uma visão geral aprofundada do Lote, consulte o fluxo de trabalho e os recursos do [serviço Batch.](batch-service-workflow-features.md)
