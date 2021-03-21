---
title: incluir ficheiro
description: incluir ficheiro
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004359"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece com a minha aplicação durante a implementação?

Todos os métodos de implementação oficialmente suportados fazem alterações nos ficheiros na `/home/site/wwwroot` pasta da sua aplicação. Estes ficheiros são usados para executar a sua aplicação. Portanto, a implementação pode falhar por causa de ficheiros bloqueados. A aplicação também pode comportar-se de forma imprevisível durante a implementação, porque nem todos os ficheiros atualizados ao mesmo tempo. Isto é indesejável para uma aplicação virada para o cliente. Há algumas maneiras diferentes de evitar estas questões:

- [Executar a sua aplicação diretamente a partir do pacote ZIP](../articles/app-service/deploy-run-package.md) sem desembalá-la.
- Pare a sua aplicação ou ative o modo offline para a sua aplicação durante a implementação. Para obter mais informações, consulte [Lidar com ficheiros bloqueados durante a implementação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Desloque-se para uma ranhura de [preparação](../articles/app-service/deploy-staging-slots.md) com [troca automática](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) ativada. 
