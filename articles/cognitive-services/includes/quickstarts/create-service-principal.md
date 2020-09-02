---
title: Criar um principal de serviço do Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 5cff103d17138608f4932e36444847437f6da236
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321654"
---
## <a name="create-an-azure-service-principal"></a>Criar um diretor de serviço Azure

Para que a sua aplicação interaja com a sua conta Azure, precisa de um responsável de serviço da Azure para gerir permissões. Siga as instruções na [Criar um chefe de serviço Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

Quando criar um diretor de serviço, verá que tem um valor secreto, uma identificação e uma identificação de aplicação. Guarde o ID da aplicação e o segredo para um local temporário para etapas posteriores.