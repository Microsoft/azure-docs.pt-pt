---
title: Instantâneo de ponto no tempo de configuração de aplicações do Azure | Documentos da Microsoft
description: Uma descrição geral de como ponto no tempo instantâneo funciona na configuração de aplicações do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884868"
---
# <a name="point-in-time-snapshot"></a>Instantâneo de ponto no tempo

Configuração de aplicações do Azure mantém registos dos tempos de precisos quando um novo par de chave-valor é criado e, em seguida, modificado. Estes registos formam uma linha do tempo total de alterações de chave-valor. Um arquivo de configuração de aplicação pode reconstruir o histórico de qualquer chave-valor e seu valor nos últimos de repetição em determinado momento, até o momento presente. Esta funcionalidade permite-lhe "tempo de viagem" para trás e obter um chave-valor antigo. Por exemplo, pode obter as definições de configuração para amanhã, apenas antes da implantação de mais recente, para que possa recuperar uma configuração anterior em caso de que tem de reverter a aplicação.

## <a name="key-value-retrieval"></a>Recuperação de chave-valor

Para obter últimos valores de chave, terá de especificar um período de tempo em que os valores de chave são instantâneo no cabeçalho de HTTP de uma chamada de REST API. Por exemplo:

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Atualmente, a configuração da aplicação irá reter 7 dias de histórico de alterações.

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma aplicação web ASP.NET](quickstart-aspnet-core-app.md)  
