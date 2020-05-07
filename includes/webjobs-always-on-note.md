---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68424154"
---
> [!NOTE]
> Uma aplicação web pode sair após 20 minutos de inatividade. Apenas solicita à aplicação web real redefinir o temporizador. Visualizar a configuração da aplicação no portal Azure ou`https://<app_name>.scm.azurewebsites.net`fazer pedidos para o site de ferramentas avançadas ( ) não redefinir o temporizador. Se a sua aplicação funcionar continuamente ou programada (gatilho do temporizador), ative **sempre on** para garantir que os WebJobs funcionam de forma fiável. Esta funcionalidade está disponível apenas nos níveis de [preços](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Básico, Standard e Premium.
