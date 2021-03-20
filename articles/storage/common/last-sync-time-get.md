---
title: Verifique a propriedade da Última Hora do Sincronização para obter uma conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como verificar a propriedade Last Sync Time para obter uma conta de armazenamento geo-replicada. A propriedade Last Sync Time indica a última vez em que todos os escritos da região primária foram escritos com sucesso para a região secundária.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 242700c05053aa9d07e3a561a21986c8451a46c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91612449"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Verifique a propriedade da Última Hora do Sincronização para obter uma conta de armazenamento

Ao configurar uma conta de armazenamento, pode especificar que os seus dados são copiados para uma região secundária que fica a centenas de quilómetros da região primária. A geo-replicação oferece durabilidade para os seus dados em caso de uma paragem significativa na região primária, como um desastre natural. Se permitir ainda o acesso de leitura à região secundária, os seus dados permanecem disponíveis para operações de leitura se a região primária ficar indisponível. Pode conceber a sua aplicação para mudar perfeitamente para a leitura da região secundária se a região primária não responder.

O armazenamento geo-redundante (GRS) e o armazenamento redundante geo-zona (GZRS) replicam os seus dados assíncronamente numa região secundária. Para ler o acesso à região secundária, permitir o armazenamento geo-redundante de acesso à leitura (RA-GRS) ou o armazenamento de zonas de acesso à leitura (RA-GZRS). Para obter mais informações sobre as várias opções de despedimento oferecidas pela Azure Storage, consulte [o Azure Storage despedimento.](storage-redundancy.md)

Este artigo descreve como verificar a propriedade **da Última Hora de Sincronização** para a sua conta de armazenamento para que possa avaliar qualquer discrepância entre as regiões primária e secundária.

## <a name="about-the-last-sync-time-property"></a>Sobre a propriedade Last Sync Time

Uma vez que a geo-replicação é assíncrona, é possível que os dados escritos na região primária ainda não tenha sido escritos à região secundária no momento em que ocorre uma interrupção. A propriedade **Last Sync Time** indica a última vez que os dados da região primária foram escritos com sucesso para a região secundária. Todos os escritos feitos para a região primária antes da última hora de sincronização estão disponíveis para serem lidos a partir do local secundário. As gravações feitas para a região primária após a última sincronização de propriedade podem ou não estar disponíveis para leituras ainda.

A propriedade **Last Sync Time** é um valor de data/hora GMT.

## <a name="get-the-last-sync-time-property"></a>Obtenha a propriedade Last Sync Time

Pode utilizar o PowerShell ou o Azure CLI para recuperar o valor da propriedade **Last Sync Time.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter a última sincronização da conta de armazenamento com o PowerShell, instale a versão 1.11.0 ou posterior do módulo [Az.Storage.](https://www.powershellgallery.com/packages/Az.Storage) Em seguida, verifique a propriedade **GeoReplicationStats.LastSyncTime** da conta de armazenamento. Lembre-se de substituir os valores do espaço reservado pelos seus próprios valores:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para obter a última hora de sincronização da conta de armazenamento com o Azure CLI, verifique a propriedade **geoReplicationStats.lastSyncTime** da conta de armazenamento. Utilize o `--expand` parâmetro para devolver valores para as propriedades aninhadas em **geoReplicationStats**. Lembre-se de substituir os valores do espaço reservado pelos seus próprios valores:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Ver também

- [Redundância do Armazenamento do Azure](storage-redundancy.md)
- [Alterar a opção de despedimento para uma conta de armazenamento](redundancy-migration.md)
- [Use geo-redundância para projetar aplicações altamente disponíveis](geo-redundant-design.md)
