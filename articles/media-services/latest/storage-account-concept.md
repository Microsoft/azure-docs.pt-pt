---
title: Contas de armazenamento do Azure
titleSuffix: Azure Media Services
description: Saiba como criar uma conta de armazenamento Azure para usar com a Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499833"
---
# <a name="azure-storage-accounts"></a>Contas de Armazenamento do Azure

Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure, precisa de criar uma conta de Media Services. Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia.

A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. É fortemente recomendado usar contas de armazenamento no mesmo local que a conta de Serviços de Media para evitar custos adicionais de latência e de esgress de dados.

Você deve ter uma conta de armazenamento **primário** e você pode ter qualquer número de contas de armazenamento **secundário** associadas à sua conta De Serviços de Media. Os Serviços de Multimédia suportam contas para **Fins gerais v2** (GPv2) ou **Fins gerais v1** (GPv1). Só as contas blob não são permitidas como **Primárias.**

Recomendamos que utilize o GPv2, para que possa tirar partido das funcionalidades mais recentes e desempenho. Para saber mais sobre as contas de armazenamento, consulte a [visão geral da conta de Armazenamento Azure.](../../storage/common/storage-account-overview.md)

> [!NOTE]
> Apenas o nível de acesso quente é suportado para uso com a Azure Media Services, embora os outros níveis de acesso possam ser usados para reduzir os custos de armazenamento em conteúdos que não estão sendo usados ativamente.

Existem Diferentes SKUs que pode escolher para a sua conta de armazenamento. Para mais informações, consulte [as contas de armazenamento.](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest) Se quiser experimentar contas de armazenamento, utilize. `--sku Standard_LRS` No entanto, ao escolher um SKU para produção, deve considerar `--sku Standard_RAGRS`, que fornece replicação geográfica para a continuidade do negócio.

## <a name="assets-in-a-storage-account"></a>Ativos numa conta de armazenamento

Nos Serviços de Media v3, as APIs de Armazenamento são usadas para fazer upload de ficheiros em ativos. Para mais informações, consulte [Ativos em Azure Media Services v3](assets-concept.md).

> [!Note]
> Não tente alterar o conteúdo dos contentores blob que foram gerados pelo Media Services SDK sem utilizar APIs de Serviços de Media.

## <a name="storage-side-encryption"></a>Encriptação do lado do armazenamento

Para proteger os seus bens em repouso, os ativos devem ser encriptados pela encriptação do lado do armazenamento. A tabela seguinte mostra como a encriptação do lado do armazenamento funciona em Media Services v3:

|Opção de encriptação|Descrição|Serviços de Multimédia v3|
|---|---|---|
|Encriptação de armazenamento de Serviços de Mídia| Encriptação AES-256, chave gerida pela Media Services. |Não apoiado. <sup>(1)</sup>|
|[Encriptação do serviço de armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Encriptação do lado do servidor oferecida pelo Azure Storage, chave gerida pelo Azure ou pelo cliente.|Suportado.|
|[Encriptação do lado do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Encriptação do lado do cliente oferecida pelo armazenamento Azure, chave gerida pelo cliente em Key Vault.|Não suportado.|

<sup>1</sup> Nos Serviços de Media v3, a encriptação de armazenamento (encriptação AES-256) só é suportada para retrocompatibilidade quando os seus ativos foram criados com o Media Services v2, o que significa que a v3 trabalha com os ativos encriptados de armazenamento existentes, mas não permite a criação de novos.

## <a name="storage-account-errors"></a>Erros da conta de armazenamento

O estado “Desligado” numa conta de Serviços de Multimédia indica que a conta já não tem acesso a uma ou mais das contas de armazenamento anexadas devido a uma alteração nas chaves de acesso de armazenamento. Os Serviços de Multimédia exigem chaves de acesso de armazenamento atualizadas para a execução de muitas tarefas na conta.

Seguem-se os cenários principais que poderão resultar numa conta de Serviços de Multimédia sem acesso às contas de armazenamento anexadas.

|Problema|Solução|
|---|---|
|A conta de Serviços de Multimédia ou a(s) conta(s) de armazenamento anexada(s) foram migradas para subscrições separadas. |Migrar a conta ou serviços de armazenamento ou serviços de mídia para que estejam todos na mesma subscrição. |
|A conta de Serviços de Multimédia está a utilizar uma conta de armazenamento anexada numa subscrição diferente, uma vez que esta era uma conta de Serviços de Multimédia antiga na qual isto era suportado. Todas as primeiras contas dos Serviços de Media foram convertidas para contas modernas baseadas em Azure Resources Manager e terão um estado desligado. |Migrar a conta de armazenamento ou a conta dos Serviços de Media para que estejam todos na mesma subscrição.|

## <a name="azure-storage-firewall"></a>Firewall de armazenamento azure

A Azure Media Services não suporta contas de armazenamento com a firewall de armazenamento Azure ou [pontos finais privados](https://docs.microsoft.com/azure/storage/common/storage-network-security) ativados.

## <a name="next-steps"></a>Passos seguintes

Para aprender a anexar uma conta de armazenamento à sua conta de Serviços de Media, consulte [Criar uma conta](create-account-cli-quickstart.md).
