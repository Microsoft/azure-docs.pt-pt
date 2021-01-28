---
title: Criar um principal de serviço do Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: dc5297a3abb913c7bef7c1669fa319d01a0c0203
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948391"
---
## <a name="create-an-azure-service-principal"></a>Criar um diretor de serviço Azure

Para que a sua aplicação interaja com a sua conta Azure, precisa de um responsável de serviço da Azure para gerir permissões. Siga as instruções na [Criar um chefe de serviço Azure](/powershell/azure/create-azure-service-principal-azureps?viewFallbackFrom=azps-3.3.0).

Quando criar um diretor de serviço, verá que tem um valor secreto, uma identificação e uma identificação de aplicação. Guarde o ID da aplicação e o segredo para um local temporário para etapas posteriores.
