---
title: Criar uma conta dos Media Services do Azure
description: Este tutorial acompanha-o através dos passos da criação de uma conta Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93f5e4d659b94bd79345a5e687de14ab6a5e8ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89267977"
---
# <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para começar a encriptar, codificar, analisar, gerir e transmitir conteúdos de mídia em Azure, é necessário criar uma conta de Media Services. A conta dos Serviços de Comunicação Social tem de ser associada a uma ou mais contas de armazenamento. Este artigo descreve passos para a criação de uma nova conta Azure Media Services.

> [!NOTE]
> A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Recomendamos vivamente que utilize as contas de armazenamento na mesma localização que a conta dos Serviços de Multimédia para evitar custos de saída de dados e latência adicionais.

 Pode utilizar o portal Azure ou o CLI para criar uma conta de Serviços de Mídia. Escolha o separador para o método que pretende utilizar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
O portal Azure fornece uma forma de criar rapidamente uma conta Azure Media Services. Pode utilizar a sua conta para aceder aos Media Services que permitem armazenar, encriptar, codificar, gerir e transmitir conteúdo no Azure.

Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

* gerir os Serviços de Comunicação Social v3 [Live Events,](live-events-outputs-concept.md) 
* ver (não gerir) v3 [Ativos,](assets-concept.md) 
* [obter informações sobre o acesso a APIs](./access-api-howto.md). 

Para todas as outras tarefas de gestão (por exemplo, [Transformações e Empregos](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs suportados.](media-services-apis-overview.md#sdks)
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Utilize o portal Azure para criar uma conta de Serviços de Comunicação Social

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Clique **+Criar um recurso**  >  **Media**  >  **Media Services**.
1. Na secção **Criar uma conta de Serviços de Mídia** introduza os valores necessários.

    | Nome | Descrição |
    | ---|---|
    |**Nome da Conta**|Insira o nome da nova conta dos Serviços de Comunicação Social. Um nome de conta dos Serviços de Multimédia é composto por letras minúsculas ou números sem espaços, com 3 a 24 carateres de comprimento.|
    |**Subscrição**|Se tiver mais de uma subscrição, selecione uma da lista de subscrições Azure a que tem acesso.|
    |**Grupo de Recursos**|Selecione o recurso novo ou existente. Um grupo de recursos é uma coleção de recursos que partilham o ciclo de vida, as permissões e as políticas. Sabia mais [aqui](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Localização**|Selecione a região geográfica que será usada para armazenar os registos de mídia e metadados para a sua conta de Media Services. Esta região será utilizada para processar e transmitir em fluxo a sua multimédia. Apenas as regiões dos Media Services disponíveis são apresentadas na caixa de lista pendente. |
    |**Conta de Armazenamento**|Selecione uma conta de armazenamento para fornecer armazenamento blob do conteúdo dos meios de comunicação a partir da sua conta De Serviços de Media. Pode selecionar uma conta do Storage existente na mesma região geográfica da conta dos Media Services ou pode criar uma nova conta do Storage. É criada uma nova conta do Storage na mesma região. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.<br/><br/>Deve ter uma conta de armazenamento **primário** e pode ter qualquer número de contas de armazenamento **secundária** associadas à sua conta de Serviços de Comunicação Social. Pode utilizar o portal Azure para adicionar contas de armazenamento secundário. Para mais informações, consulte [as contas do Azure Storage com as contas da Azure Media Services](storage-account-concept.md).<br/><br/>A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Recomendamos vivamente que utilize as contas de armazenamento na mesma localização que a conta dos Serviços de Multimédia para evitar custos de saída de dados e latência adicionais.|

1. Selecione **Afixar no dashboard** para ver o progresso da implementação da conta.
1. Clique em **Criar** na parte inferior do formulário.

    Quando a sua conta De Serviços de Media é criada, um ponto final de streaming **padrão** é adicionado à sua conta no estado **Stop.** Para começar a transmitir o seu conteúdo e tirar partido da [embalagem dinâmica](dynamic-packaging-overview.md) e da [encriptação dinâmica,](content-protection-overview.md)o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **de Funcionamento.** 

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>Definir a subscrição do Azure

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>Consulte também

* [CLI do Azure](/cli/azure/ams?view=azure-cli-latest)
* [Anexar um armazenamento secundário a uma conta de Serviços de Mídia](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>Passos seguintes

[Transmissão de um ficheiro](stream-files-dotnet-quickstart.md)
