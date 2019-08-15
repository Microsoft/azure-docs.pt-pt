---
title: Atualizar os serviços de mídia após a sobreversão de chaves de acesso de armazenamento | Microsoft Docs
description: Este artigo fornece orientação sobre como atualizar os serviços de mídia depois de reverter as chaves de acesso de armazenamento.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 1cebe0fda7da97933fc94082a62c671535fe689b
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015811"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Atualizar Serviços de Multimédia após a implementação de chaves de acesso ao armazenamento 

Ao criar uma nova conta do AMS (serviços de mídia do Azure), você também será solicitado a selecionar uma conta de armazenamento do Azure que é usada para armazenar o conteúdo da mídia. Você pode adicionar mais de uma contas de armazenamento à sua conta de serviços de mídia. Este artigo mostra como girar as chaves de armazenamento. Ele também mostra como adicionar contas de armazenamento a uma conta de mídia. 

Para executar as ações descritas neste artigo, você deve estar usando [Azure Resource Manager APIs](/rest/api/media/operations/azure-media-services-rest-api-reference) e o [PowerShell](https://docs.microsoft.com/powershell/module/az.media).  Para obter mais informações, consulte [como gerenciar recursos do Azure com o PowerShell e o Gerenciador de recursos](../../azure-resource-manager/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Quando uma nova conta de armazenamento é criada, o Azure gera chaves de acesso de armazenamento de 2 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter as conexões de armazenamento mais seguras, é recomendável regenerar e girar periodicamente sua chave de acesso de armazenamento. Duas chaves de acesso (primária e secundária) são fornecidas para permitir que você mantenha conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra chave de acesso. Esse procedimento também é chamado de "chaves de acesso sem interrupção".

Os serviços de mídia dependem de uma chave de armazenamento fornecida a ele. Especificamente, os localizadores que são usados para transmitir ou baixar seus ativos dependem da chave de acesso de armazenamento especificada. Quando uma conta AMS é criada, ela assume uma dependência da chave de acesso de armazenamento primária por padrão, mas como um usuário, você pode atualizar a chave de armazenamento que o AMS tem. Você deve certificar-se de permitir que os serviços de mídia saibam qual chave usar seguindo as etapas descritas neste artigo.  

>[!NOTE]
> Se você tiver várias contas de armazenamento, você executará esse procedimento com cada conta de armazenamento. A ordem na qual você gira as chaves de armazenamento não é corrigida. Você pode girar a chave secundária primeiro e, em seguida, a chave primária ou vice-versa.
>
> Antes de executar as etapas descritas neste artigo em uma conta de produção, certifique-se de testá-las em uma conta de pré-produção.
>

## <a name="steps-to-rotate-storage-keys"></a>Etapas para girar as chaves de armazenamento 
 
 1. Altere a chave primária da conta de armazenamento por meio do cmdlet do PowerShell ou [do portal do Azure](https://portal.azure.com/) .
 2. Chame o cmdlet Sync-AzMediaServiceStorageKeys com os parâmetros apropriados para forçar a conta de mídia a escolher as chaves da conta de armazenamento
 
    O exemplo a seguir mostra como sincronizar chaves para contas de armazenamento.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Aguarde uma hora ou mais. Verifique se os cenários de streaming estão funcionando.
 4. Altere a chave secundária da conta de armazenamento por meio do cmdlet ou portal do Azure do PowerShell.
 5. Chame Sync-AzMediaServiceStorageKeys PowerShell com os parâmetros apropriados para forçar a conta de mídia a escolher novas chaves de conta de armazenamento. 
 6. Aguarde uma hora ou mais. Verifique se os cenários de streaming estão funcionando.
 
### <a name="a-powershell-cmdlet-example"></a>Um exemplo de cmdlet do PowerShell 

O exemplo a seguir demonstra como obter a conta de armazenamento e sincronizá-la com a conta do AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Etapas para adicionar contas de armazenamento à sua conta do AMS

O artigo a seguir mostra como adicionar contas de armazenamento à sua conta do AMS: [Anexe várias contas de armazenamento a uma conta dos serviços de mídia](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Confirmações
Gostaríamos de reconhecer as seguintes pessoas que contribuíram para criar este documento: Cenk Dingiloglu, Milão gada, Seva Titov.
