---
title: Atualizar os Serviços de Mídia v3 após as chaves de acesso ao armazenamento | Microsoft Docs
description: Este artigo dá-lhe orientações sobre como atualizar os Serviços de Mídia v3 após a rolamento das teclas de acesso ao armazenamento.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 76ba1b848b033c5a26e81be18bef23a2b4715a7e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572480"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Atualizar serviços de mídia v3 após as chaves de acesso ao armazenamento rolante

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

É-lhe pedido que selecione uma conta de Armazenamento Azure quando criar uma nova conta Azure Media Services (AMS).  Pode adicionar mais de uma conta de armazenamento à sua conta de Media Services. Este artigo mostra como rodar as chaves de armazenamento. Também mostra como adicionar contas de armazenamento a uma conta de media.

Para completar as ações descritas neste artigo, deverá utilizar APIs e [PowerShell](/powershell/module/az.media) [do Gestor de Recursos Azure](/rest/api/media/operations/azure-media-services-rest-api-reference) .  Para mais informações, consulte [Como gerir os recursos da Azure com o PowerShell e o Resource Manager.](../../azure-resource-manager/management/manage-resource-groups-powershell.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Geração de chaves de acesso ao armazenamento

Quando uma nova conta de armazenamento é criada, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter as suas ligações de armazenamento mais seguras, regenerar periodicamente e rodar a chave de acesso ao armazenamento. São fornecidas duas teclas de acesso (primária e secundária) que lhe permitem manter as ligações à conta de armazenamento utilizando uma chave de acesso enquanto regenera a outra chave de acesso. Este procedimento também é chamado de "chaves de acesso rolante".

Os Serviços de Comunicação Depende de uma chave de armazenamento que lhe seja fornecida. Especificamente, os localizadores que são usados para transmitir ou descarregar os seus ativos dependem da chave de acesso ao armazenamento especificado. Quando uma conta AMS é criada, é necessária uma dependência da chave de acesso de armazenamento primário por padrão. No entanto, como utilizador, pode atualizar a chave de armazenamento que a AMS tem. Deve informar os Serviços de Comunicação Social sobre qual a chave a utilizar seguindo estes passos:

>[!NOTE]
> Se tiver várias contas de armazenamento, realizará este procedimento com cada conta de armazenamento. A ordem em que gira as teclas de armazenamento não está fixa. Pode rodar primeiro a tecla secundária e depois a chave primária ou vice-versa.
>
> Antes de executar os passos numa conta de produção, certifique-se de testá-los numa conta de pré-produção.
>

## <a name="steps-to-rotate-storage-keys"></a>Passos para rodar chaves de armazenamento
 
 1. Alterar a conta de armazenamento Chave principal através do cmdlet PowerShell ou do portal [Azure.](https://portal.azure.com/)
 2. Ligue para o `Sync-AzMediaServiceStorageKeys` cmdlet com parâmetros apropriados para forçar a conta de mídia a recolher as chaves da conta de armazenamento
 
    O exemplo a seguir mostra como sincronizar as chaves das contas de armazenamento.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Espere uma hora mais ou menos. Verifique se os cenários de streaming estão a funcionar.
 4. Altere a chave secundária da conta de armazenamento através do cmdlet PowerShell ou do portal Azure.
 5. Ligue para `Sync-AzMediaServiceStorageKeys` o PowerShell com os parâmetros apropriados para forçar a conta de mídia a recolher novas chaves de conta de armazenamento.
 6. Espere uma hora mais ou menos. Verifique se os cenários de streaming estão a funcionar.
 
### <a name="a-powershell-cmdlet-example"></a>Um exemplo de cmdlet PowerShell

O exemplo a seguir demonstra como obter a conta de armazenamento e sincronizá-la com a conta AMS.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Etapas para adicionar contas de armazenamento à sua conta AMS

O seguinte artigo mostra como adicionar contas de armazenamento à sua conta AMS: [Anexar várias contas de armazenamento a uma conta de Serviços de Mídia](media-services-managing-multiple-storage-accounts.md).
