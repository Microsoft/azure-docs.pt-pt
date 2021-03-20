---
title: Exemplos da CLI do Azure – Serviço Azure SignalR
description: Siga amostras reais para aprender Azure CLI para o Serviço Azure SignalR. Você vai aprender como criar o Serviço SignalR com mais serviços Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 99ebe9c078ef4775e8f0df0545fb0af4b3d1057b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86515245"
---
# <a name="azure-cli-reference"></a>Referência da CLI do Azure

A tabela seguinte inclui ligações para scripts bash do Serviço Azure SignalR criados com a CLI do Azure.

| Script | Descrições |
|-|-|
|**Criar**||
| [Criar um novo Serviço SignalR e grupo de recursos](scripts/signalr-cli-create-service.md) | Cria um novo recurso do Serviço Azure SignalR num novo grupo de recursos com um nome aleatório.  |
|**Integrar**||
| [Criar um novo Serviço SignalR e Aplicação Web configurada para utilizar o SignalR](scripts/signalr-cli-create-with-app-service.md) | Cria um novo recurso do Serviço Azure SignalR num novo grupo de recursos com um nome aleatório. Também adiciona um novo plano de Web App e App Service para hospedar uma ASP.NET Core Web App que utiliza o Serviço SignalR. A aplicação Web está configurada com uma Definição de Aplicação para ligar ao novo recurso do serviço SignalR. |
| [Criar um novo Serviço SignalR e Aplicação Web configurada para utilizar o SignalR e o GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Cria um novo recurso do Serviço Azure SignalR num novo grupo de recursos com um nome aleatório. Também adiciona uma nova App Azure Web e um plano de hospedagem para hospedar uma ASP.NET Core Web App que utiliza o Serviço SignalR. A aplicação Web está configurada com definições de aplicação para a cadeia de ligação para o novo recurso do serviço SignalR e segredos do cliente para suportar a [autenticação GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), conforme demonstrado no [tutorial de autenticação](signalr-concept-authenticate-oauth.md). A aplicação Web também está configurada para utilizar uma origem de implementação do repositório Git local. |
| | |
