---
title: Exemplos da CLI do Azure – Serviço Azure SignalR
description: Siga exemplos reais para saber CLI do Azure para o serviço de Signaler do Azure. Você aprenderá a criar um serviço de Signalr com mais serviços do Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158121"
---
# <a name="azure-cli-reference"></a>Referência da CLI do Azure

A tabela seguinte inclui ligações para scripts bash do Serviço Azure SignalR criados com a CLI do Azure.

| | |
|-|-|
|**Criar**||
| [Criar um novo Serviço SignalR e grupo de recursos](scripts/signalr-cli-create-service.md) | Cria um novo recurso do Serviço Azure SignalR num novo grupo de recursos com um nome aleatório.  |
|**Integrar**||
| [Criar um novo Serviço SignalR e Aplicação Web configurada para utilizar o SignalR](scripts/signalr-cli-create-with-app-service.md) | Cria um novo recurso do Serviço Azure SignalR num novo grupo de recursos com um nome aleatório. Também adiciona um novo aplicativo Web e o plano do serviço de aplicativo para hospedar um ASP.NET Core aplicativo Web que usa o serviço Signalr. A aplicação Web está configurada com uma Definição de Aplicação para ligar ao novo recurso do serviço SignalR. |
| [Criar um novo Serviço SignalR e Aplicação Web configurada para utilizar o SignalR e o GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Cria um novo recurso do Serviço Azure SignalR num novo grupo de recursos com um nome aleatório. O também adiciona um novo aplicativo Web do Azure e um plano de hospedagem para hospedar um ASP.NET Core aplicativo Web que usa o serviço Signalr. A aplicação Web está configurada com definições de aplicação para a cadeia de ligação para o novo recurso do serviço SignalR e segredos do cliente para suportar a [autenticação GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), conforme demonstrado no [tutorial de autenticação](signalr-concept-authenticate-oauth.md). A aplicação Web também está configurada para utilizar uma origem de implementação do repositório Git local. |
| | |
