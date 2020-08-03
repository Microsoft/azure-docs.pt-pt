---
author: baanders
description: incluir ficheiro para as permissões de acesso passo na configuração Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 18aa039977beeff3f6b16249822fbd98370ca404
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408360"
---
Uma vez configurado um exemplo de Azure Digital Twins, é comum interagir com esse caso através de uma aplicação de cliente. Para criar uma aplicação de cliente em funcionamento, terá de se certificar de que a aplicação do cliente será capaz de autenticar contra a Azure Digital Twins. Isto é feito através da criação de um registo de **aplicações** [Azure Ative (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) para a aplicação do seu cliente.

Este registo de aplicações é onde configura permissões de acesso às [APIs das Gémeas Digitais Azure.](../articles/digital-twins/how-to-use-apis-sdks.md) Posteriormente, a aplicação do cliente irá autenticar-se contra o registo da aplicação e, consequentemente, será-lhe concedidas as permissões de acesso configuradas às APIs.

>[!TIP]
> Como subscrição Proprietário, poderá preferir configurar um novo registo de aplicações para cada nova instância Azure Digital Twins, *ou* fazê-lo apenas uma vez, estabelecendo um registo de aplicações única que será partilhado entre todas as instâncias Azure Digital Twins na subscrição.

### <a name="create-the-registration"></a>Criar o registo