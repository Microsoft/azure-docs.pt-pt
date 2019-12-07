---
title: Acessar a API dos serviços de mídia do Azure-CLI do Azure | Microsoft Docs
description: Siga as etapas deste artigo de instruções para acessar a API dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896162"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Acessar a API dos serviços de mídia do Azure com o CLI do Azure
 
Para usar a autenticação de entidade de serviço do Azure AD para se conectar à API dos serviços de mídia do Azure, seu aplicativo precisa solicitar um token do Azure AD que tenha os seguintes parâmetros:

* Ponto de extremidade de locatário do Azure AD
* URI de recurso dos serviços de mídia
* URI de recurso para serviços de mídia REST
* Valores de aplicativo do Azure AD: a ID do cliente e o segredo do cliente

Para obter uma explicação detalhada, consulte [acessando as APIs do Media Services V3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Este artigo mostra como usar o CLI do Azure para criar um aplicativo do Azure AD e uma entidade de serviço e obter os valores necessários para acessar os recursos dos serviços de mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

Lembre-se de lembrar os valores que você usou para o nome do grupo de recursos e o nome da conta dos serviços de mídia.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Ver também

- [Dimensionar unidades reservadas de mídia-CLI](media-reserved-units-cli-how-to.md)
- [Criar uma conta dos serviços de mídia-CLI](create-account-cli-how-to.md) 
- [Redefinir credenciais da conta-CLI](cli-reset-account-credentials.md)
- [Criar ativos-CLI](cli-create-asset.md)
- [Carregar um arquivo-CLI](cli-upload-file-asset.md)
- [Criar transformações-CLI](cli-create-transform.md)
- [Codificar com uma transformação personalizada-CLI](custom-preset-cli-howto.md)
- [Criar trabalhos-CLI](cli-create-jobs.md)
- [Criar EventGrid-CLI](job-state-events-cli-how-to.md)
- [Publicar um ativo-CLI](cli-publish-asset.md)
- [Filter-CLI](filters-dynamic-manifest-cli-howto.md)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passos seguintes

O ponto de extremidade de streaming do qual você deseja transmitir conteúdo deve estar no estado executando. O comando da CLI a seguir inicia o ponto de extremidade de streaming padrão:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

