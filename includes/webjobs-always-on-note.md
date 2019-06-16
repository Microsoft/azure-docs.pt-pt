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
ms.openlocfilehash: 14f2e663e3db81684a73c4ea093ed0403cbb09ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136190"
---
> [!NOTE]
> Uma aplicação web pode tempo limite após 20 minutos de inatividade. Apenas os pedidos para a aplicação web real redefiniria o temporizador. Visualizar a configuração da aplicação no portal do Azure ou para fazer pedidos para o site de ferramentas avançadas (`https://<app_name>.scm.azurewebsites.net`) não Redefina o temporizador. Se a aplicação for executada contínua ou trabalhos Web agendados, ative **Always On** para garantir que os trabalhos Web executam de forma fiável. Esta funcionalidade só está disponível no básico, Standard e Premium [escalões de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
