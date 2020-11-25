---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: c9b4e0fdf95c2bb4888a3a11820f1785d8545bd4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993129"
---
### <a name="access-tokens"></a>Tokens de Acesso

Os Tokens de acesso são um método mais robusto para autenticar com âncoras espaciais Azure. Especialmente quando prepara a sua candidatura para uma implantação de produção. O resumo desta abordagem é criar um serviço back-end com o qual a sua aplicação do cliente possa autenticar de forma segura. O seu serviço back-end interfaces com a AAD em tempo de execução e com o Serviço de Token Seguro de Âncoras Espaciais Azure para solicitar um Token de acesso. Este token é então entregue à aplicação do cliente e usado no SDK para autenticar com âncoras espaciais Azure.
