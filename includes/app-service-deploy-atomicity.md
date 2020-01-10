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
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752265"
---
## <a name="what-happens-to-my-app-during-deployment"></a>O que acontece com meu aplicativo durante a implantação?

Todos os métodos de implantação oficialmente suportados fazem alterações nos arquivos na pasta `/home/site/wwwroot` do seu aplicativo. Esses arquivos são usados para executar seu aplicativo. Portanto, a implantação pode falhar devido a arquivos bloqueados. O aplicativo também pode se comportar de modo não previsível durante a implantação, porque nem todos os arquivos são atualizados ao mesmo tempo. Isso não é desejável para um aplicativo voltado para o cliente. Há algumas maneiras diferentes de evitar esses problemas:

- Pare seu aplicativo ou habilite o modo offline para seu aplicativo durante a implantação. Para obter mais informações, consulte [lidar com arquivos bloqueados durante a implantação](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Implante em um [slot de preparo](../articles/app-service/deploy-staging-slots.md) com a [troca automática](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) habilitada. 
- Use [Executar do pacote](https://github.com/Azure/app-service-announcements/issues/84) em vez da implantação contínua.
