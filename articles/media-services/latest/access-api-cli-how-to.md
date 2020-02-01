---
title: Aceda à API - Azure Media Services API - Azure CLI Microsoft Docs
description: Siga os passos deste artigo de como aceder à API dos Serviços de Comunicação Social.
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
ms.openlocfilehash: 06ca26ee9a8af7f49aba6a48a1831d30d5886851
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896346"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Access Azure Media Services API com o Azure CLI
 
Para utilizar a autenticação principal do serviço Azure AD para ligar à API azure Media Services, a sua aplicação necessita de solicitar um token Azure AD que tenha os seguintes parâmetros:

* Ponto final do inquilino da AD Azure
* Recurso uri dos Serviços de Media
* Recursos URI para Serviços de Mídia REST
* Valores de aplicação Azure AD: o ID do cliente e o segredo do cliente

> [!IMPORTANT]
> Para uma explicação detalhada, reveja o acesso aos Serviços de [Media v3 APIs](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Este artigo mostra-lhe como usar o Azure CLI para criar uma aplicação e diretor de serviço saqueado do Azure AD e obter os valores necessários para aceder aos recursos da Azure Media Services.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

Lembre-se dos valores que utilizou para o nome do grupo de recursos e nome da conta Media Services.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Ver também

- [Unidades Reservadas de Meios de Escala - CLI](media-reserved-units-cli-how-to.md)
- [Criar uma conta de Media Services - CLI](create-account-cli-how-to.md) 
- [Repor credenciais de conta - CLI](cli-reset-account-credentials.md)
- [Criar ativos - CLI](cli-create-asset.md)
- [Faça upload de um ficheiro - CLI](cli-upload-file-asset.md)
- [Criar transformações - CLI](cli-create-transform.md)
- [Codificar com uma transformação personalizada - CLI](custom-preset-cli-howto.md)
- [Criar empregos - CLI](cli-create-jobs.md)
- [Criar EventGrid - CLI](job-state-events-cli-how-to.md)
- [Publicar um ativo - CLI](cli-publish-asset.md)
- [Filtro - CLI](filters-dynamic-manifest-cli-howto.md)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passos seguintes

O Ponto Final de Streaming a partir do qual pretende transmitir conteúdo tem de estar no estado de Execução. O seguinte comando CLI inicia o seu ponto final de streaming padrão:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

