---
title: Encontre uma API para uma aplicação desenvolvida sob medida Azure
description: Como configurar as permissões necessárias para aceder a uma determinada API na sua aplicação AD Azure desenvolvida sob medida
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: bc50ec86866b7fe04c549c7fd463b6de4df3444b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698395"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Como encontrar uma API específica necessária para uma aplicação desenvolvida sob medida

O acesso às APIs requer configuração de âmbitos e funções de acesso. Se pretender expor as APIs da sua aplicação de recursos às aplicações do cliente, precisa de configurar os âmbitos de acesso e as funções para a API. Se pretender que uma aplicação de cliente aceda a uma API web, precisa de configurar permissões para aceder à API no registo da aplicação.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurar uma aplicação de recurso para expor APIs Web

Quando expõe a sua API web, a API será exibida na lista **Select a API** ao adicionar permissões a um registo de aplicações. Para adicionar os âmbitos de acesso, siga os passos descritos no [Configure uma aplicação para expor APIs web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurar uma aplicação de cliente para aceder a APIs web

Quando adicionar permissões ao registo da sua aplicação, pode **adicionar acesso a API** às APIs expostas da Web. Para aceder às APIs web, siga os passos delineados na [Configuração de uma aplicação do cliente para aceder a APIs web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Passos seguintes

- [Compreender o manifesto de candidatura do Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
