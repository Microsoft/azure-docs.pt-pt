---
title: Visão geral do serviço Kudu
description: Conheça o motor que alimenta a implementação contínua no Serviço de Aplicações e as suas funcionalidades.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609633"
---
# <a name="kudu-service-overview"></a>Visão geral do serviço Kudu

Kudu é o motor por trás de uma série de funcionalidades no [Azure App Service](overview.md) relacionadas com a implementação baseada em controlo de fontes, e outros métodos de implementação como dropbox e sincronização OneDrive. 

## <a name="access-kudu-for-your-app"></a>Aceda a Kudu para a sua aplicação
Sempre que cria uma aplicação, o App Service cria uma app de acompanhantes que é protegida pelo HTTPS. Esta aplicação Kudu está acessível em:

- App não em nível isolado: `https://<app-name>.scm.azurewebsites.net`
- App em nível isolado (Ambiente de Serviço de Aplicações): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Para mais informações, consulte ['Accessing the kudu service'.](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)

## <a name="kudu-features"></a>Características de Kudu

A Kudu dá-lhe informações úteis sobre a sua aplicação de Serviço de Aplicações, tais como:

- Definições da aplicação
- Cadeias de ligação
- Variáveis de ambiente
- Variáveis de servidor
- Cabeçalhos HTTP

Também fornece outras características, tais como:

- Executar comandos na [consola Kudu.](https://github.com/projectkudu/kudu/wiki/Kudu-console)
- Descarregue depósitos de diagnóstico IIS ou registos do Docker.
- Gerir processos IIS e extensões de site.
- Adicione webhooks de implementação para os aps do Windows.
- Permitir a colocação zip uI com `/ZipDeploy` .
- Gera [scripts de implementação personalizados](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
- Permite o acesso com [REST API.](https://github.com/projectkudu/kudu/wiki/REST-API)

## <a name="more-resources"></a>Mais Recursos

Kudu é um [projeto de código aberto,](https://github.com/projectkudu/kudu)e tem a sua documentação na [Kudu Wiki](https://github.com/projectkudu/kudu/wiki).

