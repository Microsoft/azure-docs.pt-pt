---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 9bd213b63b69a25fb2530cd8f6659abf5357616a
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694189"
---
## <a name="set-up-authentication"></a>Configurar autenticação

Para aceder ao serviço, é necessário fornecer uma chave de conta, ficha de acesso ou um token auth Azure Ative Directory. Pode também ler mais sobre isso na página do [conceito de Autenticação.](/azure/spatial-anchors/concepts/authentication)

### <a name="account-keys"></a>Chaves de conta

As Chaves de Conta são uma credencial que permite que a sua aplicação se autentique com o serviço Deâncoras Espaciais Azure. O objetivo das Chaves de Conta é ajudá-lo a começar rapidamente. Especialmente durante a fase de desenvolvimento da integração da sua aplicação com âncoras espaciais Azure. Como tal, pode utilizar as Chaves da Conta incorporando-as nas aplicações do seu cliente durante o desenvolvimento. À medida que progride para além do desenvolvimento, é altamente recomendado passar para um mecanismo de autenticação que é ao nível da produção, suportado por Access Tokens, ou autenticação de utilizador do Diretório Ativo Azure. Para obter uma Chave de Conta para desenvolvimento, visite a sua conta De âncoras espaciais Azure e navegue para o separador "Keys".
