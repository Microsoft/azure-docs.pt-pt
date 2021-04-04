---
title: incluir ficheiro
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92284828"
---
Se precisar de configuração adicional, utilize uma tarefa programada que seja operada no arranque para escoar as alterações finais no VM depois de ter sido implementada. Além disso, considere o seguinte:

- Se for uma tarefa de execução, a tarefa deve apagar-se depois de concluída com sucesso.
- As configurações não devem depender de unidades que não sejam C ou D, porque só estas duas unidades estão sempre garantidas a existir (a unidade C é o disco do sistema operativo e a unidade D é o disco local temporário).

Para obter mais informações sobre as personalizações do Linux, consulte [extensões e funcionalidades de máquina virtual para Linux.](../../virtual-machines/extensions/features-linux.md)