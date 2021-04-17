---
author: baanders
description: incluir ficheiro que descreve uma solução de código para a limitação do inquilino cruzado com a Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589381"
---
O exemplo a seguir mostra como definir o valor de ID de um inquilino `InteractiveBrowserTenantId` nas `DefaultAzureCredential` opções:

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="Screenshot do código que mostra o método DefaultAzureCredentialOptions. O valor da InteractiveBrowserTenantId é definido para um valor de identificação do inquilino de amostra.":::

Existem opções semelhantes disponíveis para definir um inquilino para autenticação com Visual Studio e Visual Studio Code. Para obter mais informações sobre as opções disponíveis, consulte a [documentação DefaultAzureCredentialOptions](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true).