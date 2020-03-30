---
title: Criar uma conta dos Media Services do Azure
description: Este tutorial acompanha-o através dos passos da criação de uma conta Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 2f1694825319ed8b8682c044e7e2282ed4c43dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478802"
---
# <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

Para começar a encriptar, codificar, analisar, gerir e transmitir conteúdos de mídia em Azure, precisa de criar uma conta de Media Services. A conta media services precisa de ser associada a uma ou mais contas de armazenamento.

> [!NOTE]
> A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Recomendamos vivamente que utilize as contas de armazenamento na mesma localização que a conta dos Serviços de Multimédia para evitar custos de saída de dados e latência adicionais.

Este artigo descreve passos para a criação de uma nova conta Azure Media Services. Escolha entre os seguintes separadores.

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

O portal Azure fornece uma forma de criar rapidamente uma conta Azure Media Services. Pode utilizar a sua conta para aceder aos Media Services que permitem armazenar, encriptar, codificar, gerir e transmitir conteúdo no Azure.

Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

* gerir os Media Services v3 [Eventos Ao Vivo,](live-events-outputs-concept.md) 
* vista (não gerir) [v3 Ativos,](assets-concept.md) 
* [obtenha informações sobre o acesso a APIs.](access-api-portal.md) 

Para todas as outras tarefas de gestão (por exemplo, [Transforms and Jobs](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados.

Este artigo mostra como criar uma conta de Media Services utilizando o portal Azure.

### <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Clique **em +Criar um recurso** > **Media Media** > **Services**.
1. Na secção **Criar uma conta de Serviços de Media** insira os valores exigidos.
    
    | Nome | Descrição |
    | ---|---|
    |**Nome da Conta**|Insira o nome da nova conta de Serviços de Media. Um nome de conta dos Serviços de Multimédia é composto por letras minúsculas ou números sem espaços, com 3 a 24 carateres de comprimento.|
    |**Assinatura**|Se tiver mais de uma subscrição, selecione uma da lista de subscrições do Azure a que tem acesso.|
    |**Grupo de Recursos**|Selecione o novo recurso ou existente. Um grupo de recursos é uma coleção de recursos que partilham o ciclo de vida, as permissões e as políticas. Sabia mais [aqui](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Localização**|Selecione a região geográfica que será usada para armazenar os registos de mídia e metadados para a sua conta de Media Services. Esta região será utilizada para processar e transmitir em fluxo a sua multimédia. Apenas as regiões dos Media Services disponíveis são apresentadas na caixa de lista pendente. |
    |**Conta de Armazenamento**|Selecione uma conta de armazenamento para fornecer armazenamento blob do conteúdo dos media na sua conta Media Services. Pode selecionar uma conta do Storage existente na mesma região geográfica da conta dos Media Services ou pode criar uma nova conta do Storage. É criada uma nova conta do Storage na mesma região. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.<br/><br/>Você deve ter uma conta de armazenamento **primário** e você pode ter qualquer número de contas de armazenamento **secundário** associadas à sua conta De Serviços de Media. Pode utilizar o portal Azure para adicionar contas de armazenamento secundárias. Para mais informações, consulte as contas de [Armazenamento Azure com contas azure media services](storage-account-concept.md).<br/><br/>A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Recomendamos vivamente que utilize as contas de armazenamento na mesma localização que a conta dos Serviços de Multimédia para evitar custos de saída de dados e latência adicionais.|
    
1. Selecione **Afixar no dashboard** para ver o progresso da implementação da conta.
1. Clique em **Criar** na parte inferior do formulário.

    Quando a sua conta De serviços de media é criada, um ponto final de streaming **predefinido** é adicionado à sua conta no estado **Deter.** Para começar a transmitir o seu conteúdo e tirar partido de [embalagens dinâmicas](dynamic-packaging-overview.md) e [encriptação dinâmica,](content-protection-overview.md)o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **De Execução.** 

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="set-the-azure-subscription"></a>Definir a subscrição do Azure

No comando seguinte, forneça o ID da subscrição do Azure que quer utilizar na conta dos Serviços de Multimédia. Pode ver uma lista de subscrições a que tem acesso ao navegar até [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos ao utilizar o comando seguinte. Um grupo de recursos do Azure é um contentor lógico no qual os recursos, como as contas dos Serviços de Multimédia do Azure e as contas de Armazenamento associadas, são implementados e geridos.

Pode substituir `amsResourceGroup` o seu valor.

```azurecli
az group create --name amsResourceGroup --location westus2
```

### <a name="create-a-storage-account"></a>Criar uma conta do Storage

Quando criar uma conta dos Serviços de Multimédia, terá de fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está ligada à sua conta dos Serviços de Multimédia. Para obter mais informações sobre a forma como as contas de armazenamento são utilizadas nos Serviços de Multimédia, veja [Storage accounts](storage-account-concept.md) (Contas de armazenamento).

Tem de ter uma conta de armazenamento **Principal** e pode ter qualquer número de contas de armazenamento **Secundárias** associadas à conta dos Serviços de Multimédia. Os Serviços de Multimédia suportam contas para **Fins gerais v2** (GPv2) ou **Fins gerais v1** (GPv1). As contas apenas de blobs não são permitidas como **Principais**. Se quiser saber mais sobre as contas de armazenamento, veja [Opções de contas de Armazenamento do Azure](../../storage/common/storage-account-options.md). 

Neste exemplo, criamos uma conta De propósito Geral v2, Standard LRS. Se quiser experimentar contas de armazenamento, utilize. `--sku Standard_LRS` No entanto, ao escolher um SKU para produção deve considerar, `--sku Standard_RAGRS`que fornece replicação geográfica para a continuidade do negócio. Para mais informações, consulte [as contas de armazenamento.](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)
 
O comando seguinte cria uma conta de Armazenamento que será associada à conta dos Serviços de Multimédia. No script abaixo, pode substituir `storageaccountforams` pelo seu valor. `amsResourceGroup`deve corresponder ao valor que deu ao grupo de recursos no passo anterior. O nome da conta de armazenamento deve ter duração inferior a 24.

```azurecli
az storage account create --name storageaccountforams \  
  --kind StorageV2 \
  --sku Standard_LRS \
  -l westus2 \
  -g amsResourceGroup
```

### <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

O seguinte comando da CLI do Azure cria uma nova conta dos Serviços de Multimédia. Pode substituir os seguintes valores: `amsaccount` `storageaccountforams` (deve corresponder ao `amsResourceGroup` valor que deu para a sua conta de armazenamento) e (deve corresponder ao valor que deu ao grupo de recursos).  

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```

### <a name="see-also"></a>Consulte também

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Anexar um armazenamento secundário a uma conta de Serviços de Media](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Passos seguintes

[Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
