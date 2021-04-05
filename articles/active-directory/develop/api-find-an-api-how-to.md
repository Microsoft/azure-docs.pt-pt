---
title: Encontre uma API para uma aplicação desenvolvida sob medida | Rio Azure
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
ROBOTS: NOINDEX
ms.openlocfilehash: 28cfb3d8b09c9661d16ac6e7146c50e7043d913a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581963"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Como encontrar uma API específica necessária para uma aplicação desenvolvida sob medida

O acesso às APIs requer a configuração de âmbitos e funções de acesso. Se pretender expor as apis web da sua aplicação de recursos às aplicações do cliente, configuure os âmbitos de acesso e as funções para a API. Se quiser que uma aplicação do cliente aceda a uma API web, configuure permissões para aceder à API no registo da aplicação.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurar uma aplicação de recurso para expor APIs Web

Quando expõe a sua API web, a API será exibida na lista **Select a API** ao adicionar permissões a um registo de aplicações. Para adicionar âmbitos de acesso, siga os passos descritos em [Configurar uma aplicação para expor APIs web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurar uma aplicação do cliente para aceder a APIs web

Ao adicionar permissões ao registo da sua aplicação, pode **adicionar acesso API** a APIs web expostos. Para aceder às APIs web, siga os passos descritos em [Configurar uma aplicação do cliente para aceder a APIs web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Passos seguintes

- [Compreender o manifesto de candidatura do Diretório Ativo Azure](./reference-app-manifest.md)