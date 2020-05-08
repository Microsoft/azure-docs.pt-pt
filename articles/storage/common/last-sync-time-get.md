---
title: Verifique a propriedade Do Último Tempo sincronizado para obter uma conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como verificar a propriedade Do Último Tempo sincronizado para obter uma conta de armazenamento geo-replicada. A propriedade Last Sync Time indica a última vez em que todas as escritas da região primária foram escritas com sucesso para a região secundária.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: afcadd55e87579b25f03176fa3227024863b90fb
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858518"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Verifique a propriedade Do Último Tempo sincronizado para obter uma conta de armazenamento

Ao configurar uma conta de armazenamento, pode especificar que os seus dados são copiados para uma região secundária que fica a centenas de milhas da região primária. A geo-replicação oferece durabilidade para os seus dados em caso de paragem significativa na região primária, como um desastre natural. Se permitir ainda ler o acesso à região secundária, os seus dados permanecem disponíveis para operações de leitura se a região primária ficar indisponível. Pode projetar a sua aplicação para mudar perfeitamente para a leitura da região secundária se a região primária não responder.

O armazenamento geo-redundante (GRS) e o armazenamento geo-zona-redundante (GZRS) replicam os seus dados assincronicamente para uma região secundária. Para ler o acesso à região secundária, permita o armazenamento geo-redundante de acesso à leitura (RA-GRS) ou o armazenamento geo-zona-redundante de acesso à leitura (RA-GZRS). Para obter mais informações sobre as várias opções de despedimento oferecidas pelo Azure Storage, consulte o [despedimento do Armazenamento Azure.](storage-redundancy.md)

Este artigo descreve como verificar a propriedade **do Último Tempo de Sincronização** para a sua conta de armazenamento para que possa avaliar qualquer discrepância entre as regiões primária e secundária.

## <a name="about-the-last-sync-time-property"></a>Sobre a propriedade Do Último Tempo sincronizado

Como a geo-replicação é assíncrona, é possível que os dados escritos para a região primária ainda não foram escritos à região secundária no momento em que ocorre uma interrupção. A propriedade **Last Sync Time** indica a última vez que os dados da região primária foram escritos com sucesso para a região secundária. Todas as escritas feitas para a região primária antes do último tempo de sincronização estão disponíveis para serem lidas a partir do local secundário. As escritas feitas para a região primária após a última propriedade sincronizada podem ou não estar disponíveis para leituras ainda.

A propriedade **Last Sync Time** é um valor GMT data/hora.

## <a name="get-the-last-sync-time-property"></a>Obtenha a propriedade Do Último Tempo sincronizado

Pode utilizar o PowerShell ou o Azure CLI para recuperar o valor da propriedade **Do Último Tempo de Sincronização.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter o último tempo de sincronização para a conta de armazenamento com powerShell, instale uma versão do módulo Az.Storage que suporta obter estatísticas de georeplicação. Por exemplo:

```powershell
Install-Module Az.Storage –Repository PSGallery -RequiredVersion ??? –AllowPrerelease –AllowClobber –Force
```

Em seguida, verifique a propriedade **GeoReplicationStats.LastSyncTime** da conta de armazenamento. Lembre-se de substituir os valores do espaço reservado por valores próprios:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para obter o último tempo de sincronização para a conta de armazenamento com o Azure CLI, verifique a propriedade **geoReplicationStats.lastSyncTime** da conta de armazenamento. Utilize `--expand` o parâmetro para devolver valores para as propriedades aninhadas em **geoReplicationStats**. Lembre-se de substituir os valores do espaço reservado por valores próprios:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Consulte também

- [Redundância de armazenamento azure](storage-redundancy.md)
- [Alterar a opção de despedimento para uma conta de armazenamento](redundancy-migration.md)
- [Use geo-redundância para conceber aplicações altamente disponíveis](geo-redundant-design.md)