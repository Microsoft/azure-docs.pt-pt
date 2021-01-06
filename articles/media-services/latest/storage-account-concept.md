---
title: Contas de armazenamento do Azure
titleSuffix: Azure Media Services
description: Saiba como criar uma conta de armazenamento Azure para usar com a Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: inhenkel
ms.openlocfilehash: 36593efd4f2583f28dffbb4f5b491fbeec7ba21d
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916325"
---
# <a name="azure-storage-accounts"></a>Contas de Armazenamento do Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure, é necessário criar uma conta de Media Services. Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia.

A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. É fortemente recomendado usar contas de armazenamento no mesmo local que a conta dos Serviços de Comunicação Para evitar custos adicionais de latência e fuga de dados.

Deve ter uma conta de armazenamento **primário** e pode ter qualquer número de contas de armazenamento **secundária** associadas à sua conta de Serviços de Comunicação Social. Os Serviços de Multimédia suportam contas para **Fins gerais v2** (GPv2) ou **Fins gerais v1** (GPv1). As contas blob só não são permitidas como **Primárias.**

Recomendamos que utilize O GPv2, para que possa tirar partido das funcionalidades e desempenhos mais recentes. Para saber mais sobre as contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](../../storage/common/storage-account-overview.md).

> [!NOTE]
> Apenas o nível de acesso a quente é suportado para uso com a Azure Media Services, embora os outros níveis de acesso possam ser usados para reduzir os custos de armazenamento em conteúdos que não estão a ser utilizados ativamente.

Existem diferentes SKUs que pode escolher para a sua conta de armazenamento. Para mais informações, consulte [as contas de armazenamento.](/cli/azure/storage/account?view=azure-cli-latest) Se quiser experimentar contas de armazenamento, use `--sku Standard_LRS` . No entanto, ao escolher um SKU para produção, deve `--sku Standard_RAGRS` considerar, que fornece replicação geográfica para a continuidade do negócio.

## <a name="assets-in-a-storage-account"></a>Ativos numa conta de armazenamento

Nos Serviços de Comunicação social v3, as APIs de Armazenamento são usadas para enviar ficheiros para ativos. Para mais informações, consulte [ativos nos Serviços Azure Media v3](assets-concept.md).

> [!Note]
> Não tente alterar o conteúdo dos contentores blob que foram gerados pela SDK dos Serviços de Comunicação Social sem utilizar ASP Dos Serviços de Comunicação Social.

## <a name="storage-side-encryption"></a>Encriptação lateral do armazenamento

Para proteger os seus ativos em repouso, os ativos devem ser encriptados pela encriptação do lado do armazenamento. A tabela a seguir mostra como funciona a encriptação do lado do armazenamento nos Serviços de Media v3:

|Opção de encriptação|Descrição|Serviços de Multimédia v3|
|---|---|---|
|Encriptação de armazenamento de serviços de mídia| Encriptação AES-256, chave gerida pelos Media Services. |Não apoiado. <sup>(1)</sup>|
|[Encriptação do serviço de armazenamento para dados em repouso](../../storage/common/storage-service-encryption.md)|Encriptação do lado do servidor oferecida pelo Azure Storage, chave gerida pelo Azure ou pelo cliente.|Suportado.|
|[Encriptação do lado do cliente do armazenamento](../../storage/common/storage-client-side-encryption.md)|Encriptação do lado do cliente oferecida pelo armazenamento Azure, chave gerida pelo cliente em Key Vault.|Não suportado.|

<sup>1</sup> Nos Serviços de Media v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para retrocompatibilidade quando os seus ativos foram criados com os Media Services v2, o que significa que a V3 funciona com ativos encriptados de armazenamento existentes, mas não permite a criação de novos.

## <a name="double-encryption"></a>Encriptação dupla
Os Serviços de Comunicação Social suportam a dupla encriptação.  Para saber mais sobre a dupla encriptação, consulte [a encriptação dupla Azure.](https://docs.microsoft.com/azure/security/fundamentals/double-encryption)

## <a name="storage-account-errors"></a>Erros da conta de armazenamento

O estado “Desligado” numa conta de Serviços de Multimédia indica que a conta já não tem acesso a uma ou mais das contas de armazenamento anexadas devido a uma alteração nas chaves de acesso de armazenamento. Os Serviços de Multimédia exigem chaves de acesso de armazenamento atualizadas para a execução de muitas tarefas na conta.

Seguem-se os cenários principais que poderão resultar numa conta de Serviços de Multimédia sem acesso às contas de armazenamento anexadas.

|Problema|Solução|
|---|---|
|A conta de Serviços de Multimédia ou a(s) conta(s) de armazenamento anexada(s) foram migradas para subscrições separadas. |Migrar a conta de armazenamento ou a conta dos Serviços de Comunicação Social para que estejam todos na mesma subscrição. |
|A conta de Serviços de Multimédia está a utilizar uma conta de armazenamento anexada numa subscrição diferente, uma vez que esta era uma conta de Serviços de Multimédia antiga na qual isto era suportado. Todas as primeiras contas dos Media Services foram convertidas para contas modernas do Azure Resources Manager e terão um estado desligado. |Migrar a conta de armazenamento ou a conta dos Serviços de Comunicação Para que estejam todos na mesma subscrição.|

## <a name="azure-storage-firewall"></a>Firewall de armazenamento Azure

A Azure Media Services não suporta contas de armazenamento com a firewall de armazenamento Azure ou [private endpoints](../../storage/common/storage-network-security.md) ativados.

## <a name="next-steps"></a>Passos seguintes

Para saber como anexar uma conta de armazenamento na sua conta de Serviços de Mídia, consulte [Criar uma conta](./create-account-howto.md).
