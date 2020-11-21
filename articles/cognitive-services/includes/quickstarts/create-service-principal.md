---
title: Criar um principal de serviço do Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95096012"
---
## <a name="create-an-azure-service-principal"></a>Criar um diretor de serviço Azure

Para que a sua aplicação interaja com a sua conta Azure, precisa de um responsável de serviço da Azure para gerir permissões. Siga as instruções na [Criar um chefe de serviço Azure](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Quando criar um diretor de serviço, verá que tem um valor secreto, uma identificação e uma identificação de aplicação. Guarde o ID da aplicação e o segredo para um local temporário para etapas posteriores.