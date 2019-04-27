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
ms.openlocfilehash: 83770e8c5f415670855b5cf2502d02c4d6919440
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741143"
---
# <a name="point-in-time-snapshot"></a>Instantâneo para um ponto anterior no tempo

Configuração de aplicações do Azure mantém registos dos tempos de precisos quando um novo par de chave-valor é criado e, em seguida, modificado. Estes registos formam uma linha do tempo total de alterações de chave-valor. Um arquivo de configuração de aplicação pode reconstruir o histórico de qualquer valor de chave e seu valor nos últimos de repetição em determinado momento, até o momento presente. Com esta funcionalidade, pode "tempo de viagem" para trás e obter um valor de chave antigo. Por exemplo, pode obter definições de configuração de ontem, apenas antes da implantação mais recente, para recuperar uma configuração anterior e revertê-lo o aplicativo.

## <a name="key-value-retrieval"></a>Recuperação de chave-valor

Para obter últimos valores de chave, especifique um período de tempo em que os valores de chave são instantâneo no cabeçalho de HTTP de uma chamada de REST API. Por exemplo:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Atualmente, a configuração de aplicações mantém sete dias do histórico de alterações.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web ASP.NET Core](./quickstart-aspnet-core-app.md)  
