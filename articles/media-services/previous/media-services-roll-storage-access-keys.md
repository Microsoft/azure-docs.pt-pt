---
title: Atualizar serviços de multimédia após chaves de acesso de armazenamento a implementar | Documentos da Microsoft
description: Este artigo dão-lhe orientações sobre como atualizar os serviços de multimédia após chaves de acesso de armazenamento a implementar.
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
ms.date: 02/08/2019
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: cdf644ab37d7f2e15cb35a3234786900a1a64ec1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733508"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Atualizar serviços de multimédia após sem interrupção de chaves de acesso de armazenamento 

Quando cria uma nova conta de serviços de multimédia do Azure (AMS), é também solicitado para selecionar uma conta de armazenamento do Azure que é utilizada para armazenar o conteúdo de mídia. Pode adicionar mais contas de armazenamento à sua conta de Media Services. Este artigo mostra como Rodar chaves de armazenamento. Ela também mostra como adicionar contas de armazenamento para uma conta de suporte de dados. 

Para executar as ações descritas neste artigo, deve estar usando [APIs do Azure Resource Manager](/rest/api/media/operations/azure-media-services-rest-api-reference) e [Powershell](https://docs.microsoft.com/powershell/module/az.media).  Para obter mais informações, consulte [como gerir recursos do Azure com o PowerShell e do Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Descrição geral

Quando é criada uma nova conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são utilizadas para autenticar o acesso à sua conta de armazenamento. Para manter as ligações de armazenamento mais seguro, é recomendado para periodicamente regenerar e girar a sua chave de acesso de armazenamento. Duas chaves de acesso (primárias e secundárias) são fornecidas para que possa manter as ligações para a conta de armazenamento através de uma chave de acesso enquanto volta a gerar a chave de acesso. Este procedimento também é denominado "chaves sem interrupção de acesso".

Serviços de multimédia depende de uma chave de armazenamento fornecida ao mesmo. Especificamente, os localizadores que são utilizados para transmitir ou transferir os seus ativos dependem a chave de acesso de armazenamento especificada. Quando uma conta do AMS é criada, demora uma dependência na chave de acesso de armazenamento primária por predefinição, mas como um utilizador pode atualizar a chave de armazenamento que tenha de AMS. Verifique se a permitir aos serviços de multimédia saber qual chave usar ao seguir os passos descritos neste artigo.  

>[!NOTE]
> Se tiver várias contas de armazenamento, executaria este procedimento com cada conta de armazenamento. A ordem na qual Rodar chaves de armazenamento não é fixo. Pode girar a primeira chave secundária e, em seguida, o principal chave ou vice-versa.
>
> Antes de executar os passos descritos neste artigo numa conta de produção, certifique-se de testá-los numa conta de pré-produção.
>

## <a name="steps-to-rotate-storage-keys"></a>Passos para rodar chaves de armazenamento 
 
 1. Alterar a chave primária de conta de armazenamento através do cmdlet do powershell ou [Azure](https://portal.azure.com/) portal.
 2. Chamar o cmdlet Sync-AzMediaServiceStorageKeys com parâmetros adequados para forçar a conta de multimédia para recolher chaves de conta de armazenamento
 
    O exemplo seguinte mostra como sincronizar chaves para contas de armazenamento.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Aguarde uma hora ou menos. Certifique-se de que os cenários de transmissão em fluxo estiver a trabalhar.
 4. Alterar a chave secundária da conta de armazenamento através do cmdlet do powershell ou o portal do Azure.
 5. Chame sincronização AzMediaServiceStorageKeys powershell com os parâmetros adequados para forçar a conta de multimédia para recolher chaves de conta de armazenamento nova. 
 6. Aguarde uma hora ou menos. Certifique-se de que os cenários de transmissão em fluxo estiver a trabalhar.
 
### <a name="a-powershell-cmdlet-example"></a>Um exemplo de cmdlet do powershell 

O exemplo seguinte demonstra como obter a conta de armazenamento e sincronizá-la com a conta de AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Passos para adicionar contas de armazenamento à sua conta do AMS

O seguinte artigo mostra como adicionar contas de armazenamento à sua conta de AMS: [Anexar várias contas de armazenamento para uma conta de Media Services](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Agradecimentos
Gostaríamos de agradecer a seguintes pessoas que contribuíram para a criação deste documento: Cenk Dingiloglu, Gada Milão, Seva Titov.
