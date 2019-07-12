---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93332002cd2ac513d125e0f9eb75dff4a2d8760c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836739"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece ao meu aplicativo durante a implantação?

Todos os métodos de implementação suportadas oficialmente fazem alterações aos ficheiros no `/home/site/wwwroot` pasta da sua aplicação. Estes ficheiros são os mesmos aqueles que são executados na produção. Por conseguinte, a implementação pode falhar devido a arquivos bloqueados. A aplicação na produção pode também ter um comportamento imprevisível durante a implementação, porque nem todos os ficheiros atualizados ao mesmo tempo. Existem várias formas de evitar esses problemas:

- Parar a sua aplicação ou ativar o modo offline para a sua aplicação durante a implementação. Para obter mais informações, consulte [lidar com arquivos bloqueados durante a implementação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implementar um [blocos de teste](../articles/app-service/deploy-staging-slots.md) com [troca automática](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) ativada. 
- Uso [execução do pacote](https://github.com/Azure/app-service-announcements/issues/84) em vez da implementação contínua.
