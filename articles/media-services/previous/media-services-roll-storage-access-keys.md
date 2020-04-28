---
title: Atualizar os Serviços de Media após as chaves de acesso ao armazenamento de rolos Microsoft Docs
description: Estes artigos dão-lhe orientações sobre como atualizar os Serviços de Media após as chaves de acesso ao armazenamento de rolos.
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
ms.openlocfilehash: 2a0d1c5af572c88dc11bed950b46706f0a2f081f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981964"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Atualizar Serviços de Multimédia após a implementação de chaves de acesso ao armazenamento 

Quando cria uma nova conta azure media services (AMS), é-lhe também pedido que selecione uma conta Azure Storage que seja usada para armazenar os seus conteúdos de mídia. Pode adicionar mais do que uma conta de armazenamento à sua conta de Serviços de Media. Este artigo mostra como rodar as chaves de armazenamento. Também mostra como adicionar contas de armazenamento a uma conta de mídia. 

Para realizar as ações descritas neste artigo, deve utilizar APIs e [Powershell](https://docs.microsoft.com/powershell/module/az.media)do Gestor de [Recursos Azure](/rest/api/media/operations/azure-media-services-rest-api-reference) .  Para mais informações, consulte como gerir os recursos do Azure com o [PowerShell e o Gestor de Recursos.](../../azure-resource-manager/management/manage-resource-groups-powershell.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Quando uma nova conta de armazenamento é criada, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter as suas ligações de armazenamento mais seguras, recomenda-se regenerar periodicamente e rodar a sua chave de acesso ao armazenamento. São fornecidas duas teclas de acesso (primárias e secundárias) de forma a permitir manter as ligações à conta de armazenamento utilizando uma chave de acesso enquanto regenera a outra chave de acesso. Este procedimento chama-se também "chaves de acesso rolantes".

A Media Services depende de uma chave de armazenamento fornecida. Especificamente, os localizadores que são usados para transmitir ou descarregar os seus ativos dependem da chave de acesso ao armazenamento especificada. Quando uma conta AMS é criada, assume uma dependência da chave de acesso ao armazenamento primário por padrão, mas como utilizador pode atualizar a chave de armazenamento que a AMS tem. Deve certificar-se de que informa os Serviços de Comunicação Social sobre qual a chave a utilizar seguindo os passos descritos neste artigo.  

>[!NOTE]
> Se tiver várias contas de armazenamento, realizará este procedimento com cada conta de armazenamento. A ordem em que gira as chaves de armazenamento não é fixada. Pode rodar a tecla secundária primeiro e depois a chave primária ou vice-versa.
>
> Antes de executar as etapas descritas neste artigo numa conta de produção, certifique-se de testá-las numa conta de pré-produção.
>

## <a name="steps-to-rotate-storage-keys"></a>Passos para rodar chaves de armazenamento 
 
 1. Altere a conta de armazenamento Chave primária através do powershell cmdlet ou [portal Azure.](https://portal.azure.com/)
 2. Ligue para Sync-AzMediaServiceStorageKeys cmdlet com params apropriados para forçar a conta dos media a recolher as chaves da conta de armazenamento
 
    O exemplo que se segue mostra como sincronizar as chaves das contas de armazenamento.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Espere uma hora mais ou menos. Verifique se os cenários de streaming estão a funcionar.
 4. Altere a chave secundária da conta de armazenamento através do powershell cmdlet ou do portal Azure.
 5. Ligue para o Sync-AzMediaServiceStorageKeys powershell com params apropriados para forçar a conta dos media a recolher novas chaves de conta de armazenamento. 
 6. Espere uma hora mais ou menos. Verifique se os cenários de streaming estão a funcionar.
 
### <a name="a-powershell-cmdlet-example"></a>Um exemplo de powershell cmdlet 

O exemplo que se segue demonstra como obter a conta de armazenamento e sincronizá-la com a conta AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Passos para adicionar contas de armazenamento à sua conta AMS

O seguinte artigo mostra como adicionar contas de armazenamento à sua conta AMS: [Anexar várias contas](meda-services-managing-multiple-storage-accounts.md)de armazenamento a uma conta de Serviços de Media .

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de reconhecer as seguintes pessoas que contribuíram para a criação deste documento: Cenk Dingiloglu, Milan Gada, Seva Titov.
