---
title: Instantâneo do ponto de configuração do Azure App no momento | Microsoft Docs
description: Uma visão geral de como o instantâneo pontual funciona na configuração do Azure App
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
ms.openlocfilehash: fdbe4846700c690261dbc734063f4420478666a8
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185204"
---
# <a name="point-in-time-snapshot"></a>Instantâneo para um ponto anterior no tempo

Azure App configuração mantém registros dos tempos precisos quando um novo par chave-valor é criado e, em seguida, modificado. Esses registros formam uma linha do tempo completa em alterações de chave-valor. Um repositório de configuração de aplicativo pode reconstruir o histórico de qualquer valor de chave e reproduzir seu valor anterior em qualquer momento determinado, até o presente. Com esse recurso, você pode retroceder "tempo-de-viagem" e recuperar um valor de chave antigo. Por exemplo, você pode obter as definições de configuração de ontem, logo antes da implantação mais recente, para recuperar uma configuração anterior e reverter o aplicativo.

## <a name="key-value-retrieval"></a>Recuperação de chave-valor

Para recuperar valores de chave anteriores, especifique uma hora na qual os valores de chave são instantâneos no cabeçalho HTTP de uma chamada à API REST. Por exemplo:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Atualmente, a configuração de aplicativo mantém sete dias de histórico de alterações.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
