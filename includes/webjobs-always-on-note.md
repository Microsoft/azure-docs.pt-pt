---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "68424154"
---
> [!NOTE]
> Uma aplicação web pode sair após 20 minutos de inatividade. Apenas os pedidos para a aplicação web real redefinir o temporizador. Visualizar a configuração da aplicação no portal Azure ou fazer pedidos para o site de ferramentas avançadas `https://<app_name>.scm.azurewebsites.net` () não reinicie o temporizador. Se a sua aplicação funcionar continuamente ou programada (Timer Trigger) WebJobs, ative **Always On** para garantir que os WebJobs funcionam de forma fiável. Esta funcionalidade está disponível apenas nos [níveis](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)básico, standard e premium.
