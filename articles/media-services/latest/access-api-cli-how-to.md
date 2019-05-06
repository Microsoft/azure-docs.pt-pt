---
title: Aceder à API de serviços de multimédia do Azure - CLI do Azure | Documentos da Microsoft
description: Siga os passos nesta explicação de procedimento para acessar a API de serviços de multimédia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: a27f7597ddc934b925d63507a816ac5816b682d6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65151052"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Serviços de multimédia do Azure de acesso à API com a CLI do Azure
 
Para utilizar a autenticação do principal de serviço do Azure AD para ligar à API de serviços de multimédia do Azure, o seu aplicativo precisa de pedir um token do Azure AD que tenha os seguintes parâmetros:

* Ponto final de inquilino do Azure AD
* URI do recurso de serviços de multimédia
* URI para serviços de multimédia REST do recurso
* Valores de aplicações do Azure AD: o ID de cliente e o segredo de cliente

Para obter explicações detalhadas, consulte [aceder a serviços de multimédia v3 APIs](media-services-apis-overview.md#accessing-the-azure-media-services-api).

Este artigo mostra-lhe como utilizar a CLI do Azure para criar uma aplicação do Azure AD e o serviço principal e obter os valores que são necessárias para aceder aos recursos de serviços de multimédia do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta de Media Services](create-account-cli-how-to.md).

Lembre-se de que não se esqueça dos valores que utilizou para o nome do grupo de recursos e o nome de conta de serviços de multimédia.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Consulte também

- [Unidades - CLI de reservadas de multimédia de dimensionamento](media-reserved-units-cli-how-to.md)
- [Criar uma conta de Media Services - CLI](./scripts/cli-create-account.md) 
- [Repor as credenciais da conta - CLI](./scripts/cli-reset-account-credentials.md)
- [Criar recursos - CLI](./scripts/cli-create-asset.md)
- [Carregar um ficheiro - CLI](./scripts/cli-upload-file-asset.md)
- [Criar transformações - CLI](./scripts/cli-create-transform.md)
- [Criar tarefas - CLI](./scripts/cli-create-jobs.md)
- [Criar EventGrid - CLI](./scripts/cli-create-event-grid.md)
- [Publicar um elemento - CLI](./scripts/cli-publish-asset.md)
- [Filtro - CLI](filters-dynamic-manifest-cli-howto.md)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passos Seguintes

Ponto final de transmissão em fluxo partir do qual quer transmitir conteúdo tem de estar no Estado em execução. O seguinte comando da CLI começa seu padrão de ponto final de transmissão em fluxo:


`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`
