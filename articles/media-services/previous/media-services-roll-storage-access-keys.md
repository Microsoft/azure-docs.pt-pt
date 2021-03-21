---
title: Atualizar serviços de mídia após rolar chaves de acesso ao armazenamento | Microsoft Docs
description: Estes artigos dão-lhe orientação sobre como atualizar os Serviços de Mídia após a rolamento das teclas de acesso ao armazenamento.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 12732171f774e6ce010f722cde4a27bb298275b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007941"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Atualizar Serviços de Multimédia após a implementação de chaves de acesso ao armazenamento

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Quando cria uma nova conta Azure Media Services (AMS), é-lhe também pedido que selecione uma conta de Armazenamento Azure que seja utilizada para armazenar o seu conteúdo de mídia. Pode adicionar mais de uma conta de armazenamento à sua conta de Media Services. Este artigo mostra como rodar as chaves de armazenamento. Também mostra como adicionar contas de armazenamento a uma conta de media. 

Para executar as ações descritas neste artigo, deverá utilizar APIs e [Powershell](/powershell/module/az.media) [do Gestor de Recursos Azure.](/rest/api/media/operations/azure-media-services-rest-api-reference)  Para mais informações, consulte [Como gerir os recursos da Azure com o PowerShell e o Resource Manager.](../../azure-resource-manager/management/manage-resource-groups-powershell.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Quando uma nova conta de armazenamento é criada, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticar o acesso à sua conta de armazenamento. Para manter as suas ligações de armazenamento mais seguras, é aconselhável regenerar periodicamente e rodar a chave de acesso ao armazenamento. São fornecidas duas teclas de acesso (primária e secundária) de forma a permitir manter as ligações à conta de armazenamento utilizando uma chave de acesso enquanto regenera a outra chave de acesso. Este procedimento também é chamado de "chaves de acesso rolante".

Os Serviços de Comunicação Depende de uma chave de armazenamento que lhe seja fornecida. Especificamente, os localizadores que são usados para transmitir ou descarregar os seus ativos dependem da chave de acesso ao armazenamento especificado. Quando uma conta AMS é criada, é necessária uma dependência da chave de acesso ao armazenamento primário por padrão, mas como utilizador pode atualizar a chave de armazenamento que a AMS tem. Deve certificar-se de que os Serviços de Comunicação Social sabem qual a chave a utilizar seguindo os passos descritos neste artigo.  

>[!NOTE]
> Se tiver várias contas de armazenamento, realizará este procedimento com cada conta de armazenamento. A ordem em que gira as teclas de armazenamento não está fixa. Pode rodar primeiro a tecla secundária e depois a chave primária ou vice-versa.
>
> Antes de executar as etapas descritas neste artigo numa conta de produção, certifique-se de testá-las numa conta de pré-produção.
>

## <a name="steps-to-rotate-storage-keys"></a>Passos para rodar chaves de armazenamento 
 
 1. Alterar a conta de armazenamento Chave principal através do cmdlet de powershell ou do portal [Azure.](https://portal.azure.com/)
 2. Ligue Sync-AzMediaServiceStorageKeys cmdlet com params apropriados para forçar conta de mídia a recolher chaves de conta de armazenamento
 
    O exemplo a seguir mostra como sincronizar as chaves das contas de armazenamento.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Espere uma hora mais ou menos. Verifique se os cenários de streaming estão a funcionar.
 4. Alterar a chave secundária da conta de armazenamento através do cmdlet powershell ou do portal Azure.
 5. Ligue Sync-AzMediaServiceStorageKeys powershell com params apropriados para forçar a conta dos meios de comunicação a recolher novas chaves de conta de armazenamento. 
 6. Espere uma hora mais ou menos. Verifique se os cenários de streaming estão a funcionar.
 
### <a name="a-powershell-cmdlet-example"></a>Um exemplo de cmdlet de concha de poder 

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

O seguinte artigo mostra como adicionar contas de armazenamento à sua conta AMS: [Anexar várias contas de armazenamento a uma conta de Serviços de Mídia](./media-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de reconhecer as seguintes pessoas que contribuíram para a criação deste documento: Cenk Dingiloglu, Milan Gada, Seva Titov.
