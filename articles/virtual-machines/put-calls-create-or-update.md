---
title: Chamadas PUT para operações de criação ou atualização
description: PUT apela à criação ou atualização de operações sobre recursos computacional
author: mimckitt
ms.author: mimckitt
ms.reviewer: cynthn
ms.topic: conceptual
ms.service: virtual-machines
ms.date: 08/4/2020
ms.custom: avverma
ms.openlocfilehash: d6ee4179dce905d637e933743ade7452a2484077
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978565"
---
# <a name="put-calls-for-creation-or-updates-on-compute-resources"></a>PUT apela à criação ou atualizações sobre recursos computacional

`Microsoft.Compute`recursos não suportam a definição convencional de *semântica HTTP PUT.* Em vez disso, estes recursos utilizam semântica PATCH tanto para os verbos PUT como para PATCH.

**Criar** operações aplica valores predefinidos quando apropriado. No entanto, **as atualizações** de recursos efetuadas através de PUT ou PATCH, não aplicam quaisquer propriedades predefinidas. As operações de **atualização** aplicam-se a semântica patch rigorosa.

Por exemplo, a propriedade do disco `caching` de uma máquina virtual predefine se o recurso for um disco `ReadWrite` DE.

```json
    "storageProfile": {
      "osDisk": {
        "name": "myVMosdisk",
        "image": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/{existing-generalized-os-image-blob-name}.vhd"
        },
        "osType": "Windows",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "vhd": {
          "uri": "http://{existing-storage-account-name}.blob.core.windows.net/{existing-container-name}/myDisk.vhd"
        }
      }
    },
```

No entanto, para operações **de atualização** quando um imóvel é deixado de fora ou um valor *nulo* é passado, permanecerá inalterado e não há valores de incumprimento.

Isto é importante no envio de operações de atualização para um recurso com a intenção de remover uma associação. Se esse recurso for um `Microsoft.Compute` recurso, o imóvel correspondente que pretende remover tem de ser explicitamente chamado e um valor atribuído. Para isso, os utilizadores podem passar uma corda vazia como **" "**. Isto instruirá a plataforma para remover essa associação.

> [!IMPORTANT]
> Não há suporte para "remendar" um elemento de matriz. Em vez disso, o cliente tem de fazer um pedido DEM ou PATCH com todo o conteúdo da matriz atualizada. Por exemplo, para separar um disco de dados de um VM, faça um pedido GET para obter o modelo VM atual, remova o disco a ser desvinculado `properties.storageProfile.dataDisks` de , e faça um pedido PUT com a entidade VM atualizada.

## <a name="examples"></a>Exemplos

### <a name="correct-payload-to-remove-a-proximity-placement-groups-association"></a>Carga útil correta para remover uma associação de Grupos de Colocação de Proximidade

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": "" } }
`

### <a name="incorrect-payloads-to-remove-a-proximity-placement-groups-association"></a>Cargas incorretas para remover uma associação de Grupos de Colocação de Proximidade

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20, "proximityPlacementGroup": null } }
`

`
{ "location": "westus", "properties": { "platformFaultDomainCount": 2, "platformUpdateDomainCount": 20 } }
`

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre Criar ou Atualizar chamadas para [máquinas virtuais](/rest/api/compute/virtualmachines/createorupdate) e [conjuntos de escala de máquinas virtuais](/rest/api/compute/virtualmachinescalesets/createorupdate)