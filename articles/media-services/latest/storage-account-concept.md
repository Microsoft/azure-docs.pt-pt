---
title: Carregamento e o armazenamento com os serviços de multimédia do Azure na cloud | Documentos da Microsoft
description: Este carregamento do artigo na cloud e conceitos de armazenamento.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/11/2019
ms.author: juliako
ms.openlocfilehash: 9cbb995eb3310a2263185d6fd6dba20efce37f38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550156"
---
# <a name="cloud-upload-and-storage"></a>Carregamento e armazenamento na cloud

Para começar a gerir, encriptar, codificar, analisar e transmissão em fluxo conteúdo de multimédia do Azure, terá de criar uma conta de Media Services. Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. 

A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. É vivamente recomendado para utilizar contas de armazenamento na mesma localização que a conta de serviços de multimédia para evitar custos de saída de latência e dados adicionais

Tem de ter uma conta de armazenamento **Principal** e pode ter qualquer número de contas de armazenamento **Secundárias** associadas à conta dos Serviços de Multimédia. Os Serviços de Multimédia suportam contas para **Fins gerais v2** (GPv2) ou **Fins gerais v1** (GPv1). 

>[!NOTE]
> As contas apenas de blobs não são permitidas como **Principais**. 

Recomendamos que utilize GPv2, para que possa tirar partido de optar entre frequente e esporádico camadas de armazenamento. Para saber mais sobre as contas de armazenamento, veja [descrição geral da conta de armazenamento do Azure](../../storage/common/storage-account-overview.md). 

Existem diferentes SKUs, pode escolher para a sua conta de armazenamento. Para obter mais informações, consulte [contas de armazenamento](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest). Se quiser experimentar as contas de armazenamento, utilize `--sku Standard_LRS`. No entanto, ao escolher um SKU para produção deve considerar, `--sku Standard_RAGRS`, que fornece replicação geográfica para continuidade do negócio. 

## <a name="assets-in-a-storage-account"></a>Recursos numa conta de armazenamento

Em serviços de multimédia v3, as APIs de armazenamento são utilizadas para carregar ficheiros para ativos. Para obter mais informações, consulte [conceito de ativos](assets-concept.md).

> [!Note]
> Não deve tentar alterar os conteúdos de contentores de BLOBs que foram gerados pelo SDK de serviços de multimédia sem utilizar as APIs dos serviços de multimédia.
 
## <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

Para proteger os seus ativos inativos, os recursos devem ser encriptados pela encriptação do lado do armazenamento. A tabela seguinte mostra como a encriptação do lado do armazenamento funciona em serviços de multimédia v3:

|Opção de encriptação|Descrição|Serviços de Multimédia v3|
|---|---|---|
|Encriptação de armazenamento dos serviços de multimédia| Encriptação AES-256, chave gerida pelos serviços de multimédia|Não suportado<sup>(1)</sup>|
|[Encriptação do serviço de armazenamento para dados Inativos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chave de encriptação do lado do servidor oferecidas pelo armazenamento do Azure, gerida pelo Azure ou pelo cliente|Suportadas|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Oferecidas pelo armazenamento do Azure, chave gerida pelo cliente no Cofre de chaves de encriptação do lado do cliente|Não suportado|

<sup>1</sup> em serviços de multimédia v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para em versões anteriores compatibilidade quando os recursos foram criados com os serviços de multimédia v2. O que significa v3 funciona com o armazenamento existente encriptado ativos, mas não permitirá que a criação de novos itens.

## <a name="storage-account-errors"></a>Erros de conta de armazenamento

O estado “Desligado” numa conta de Serviços de Multimédia indica que a conta já não tem acesso a uma ou mais das contas de armazenamento anexadas devido a uma alteração nas chaves de acesso de armazenamento. Os Serviços de Multimédia exigem chaves de acesso de armazenamento atualizadas para a execução de muitas tarefas na conta.

Seguem-se os cenários principais que poderão resultar numa conta de Serviços de Multimédia sem acesso às contas de armazenamento anexadas. 

|Problema|Solução|
|---|---|
|A conta de Serviços de Multimédia ou a(s) conta(s) de armazenamento anexada(s) foram migradas para subscrições separadas. |Migre a(s) conta(s) de armazenamento ou a conta de Serviços de Multimédia para que estejam todas na mesma subscrição. |
|A conta de Serviços de Multimédia está a utilizar uma conta de armazenamento anexada numa subscrição diferente, uma vez que esta era uma conta de Serviços de Multimédia antiga na qual isto era suportado. Todas as contas de Serviços de Multimédia antigas foram convertidas para contas modernas baseadas no Azure Resource Manager (ARM) e vão apresentar um estado Desligado. |Migre a conta de armazenamento ou a conta de Serviços de Multimédia para que estejam todas na mesma subscrição.|

## <a name="next-steps"></a>Passos Seguintes

Para saber como anexar uma conta de armazenamento à sua conta de Media Services, veja [criar uma conta](create-account-cli-quickstart.md).
