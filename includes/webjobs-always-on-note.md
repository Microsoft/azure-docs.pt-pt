---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009687"
---
> [!NOTE]
> Uma aplicação web pode sair após 20 minutos de inatividade. e apenas os pedidos para a aplicação web real podem redefinir o temporizador. Ver a configuração da aplicação no portal Azure ou fazer pedidos para o site de ferramentas avançadas `https://<app_name>.scm.azurewebsites.net` () não reinicia o temporizador. Se definir a sua aplicação web para executar WebJobs contínuos ou programados (timer-trigger), ative o **Always on** Setting na página de **Configuração** Azure da sua aplicação web para garantir que os WebJobs são executados de forma fiável. Esta funcionalidade está disponível apenas nos [níveis](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)básico, standard e premium.
