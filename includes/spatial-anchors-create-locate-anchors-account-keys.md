---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993114"
---
## <a name="set-up-authentication"></a>Configurar autenticação

Para aceder ao serviço, precisa de fornecer uma chave de conta, um token de acesso ou um token Azure Ative Directory. Pode também ler mais sobre isso na página do [conceito de Autenticação.](../articles/spatial-anchors/concepts/authentication.md)

### <a name="account-keys"></a>Chaves de conta

As Chaves de Conta são uma credencial que permite que a sua aplicação autente com o serviço Azure Spatial Anchors. O objetivo pretendido das Chaves de Conta é ajudá-lo a começar rapidamente. Especialmente durante a fase de desenvolvimento da integração da sua aplicação com âncoras espaciais Azure. Como tal, pode utilizar as Chaves de Conta incorporando-as nas aplicações do seu cliente durante o desenvolvimento. À medida que progride para além do desenvolvimento, é altamente recomendado mover-se para um mecanismo de autenticação que seja de nível de produção, suportado por Access Tokens ou autenticação do utilizador do Azure Ative Directory. Para obter uma Chave de Conta para o desenvolvimento, visite a sua conta Azure Spatial Anchors e navegue no separador "Chaves".