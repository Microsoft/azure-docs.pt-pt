---
title: Encontre uma API para uma aplicação desenvolvida sob medida Rio Azure
description: Como configurar as permissões que precisa para aceder a uma determinada API na sua aplicação AD AD desenvolvida sob medida
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 8c3d79211ce594ec4c175aa6f6b5f8df3a7c4ba8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88115548"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Como encontrar uma API específica necessária para uma aplicação desenvolvida sob medida

O acesso às APIs requer a configuração de âmbitos e funções de acesso. Se pretender expor as apis web da sua aplicação de recursos às aplicações do cliente, tem de configurar âmbitos e funções de acesso para a API. Se quiser que uma aplicação do cliente aceda a uma API web, precisa de configurar permissões para aceder à API no registo da aplicação.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurar uma aplicação de recurso para expor APIs Web

Quando expõe a sua API web, a API será exibida na lista **Select a API** ao adicionar permissões a um registo de aplicações. Para adicionar âmbitos de acesso, siga os passos descritos em [Configurar uma aplicação para expor APIs web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurar uma aplicação do cliente para aceder a APIs web

Ao adicionar permissões ao registo da sua aplicação, pode **adicionar acesso API** a APIs web expostos. Para aceder às APIs web, siga os passos descritos em [Configurar uma aplicação do cliente para aceder a APIs web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Passos seguintes

- [Compreender o manifesto de candidatura do Diretório Ativo Azure](./reference-app-manifest.md)