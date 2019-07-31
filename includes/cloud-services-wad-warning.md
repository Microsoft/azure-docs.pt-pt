---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67184601"
---
> [!WARNING]
> Quando você habilita o diagnóstico para uma função existente, todas as extensões que você já definiu são desabilitadas quando o pacote é implantado. Estas incluem:
>
> * Diagnóstico de Microsoft Monitoring Agent
> * Monitoramento de segurança do Microsoft Azure
> * Microsoft Antimalware                 
> * Agente de Monitorização da Microsoft
> * Agente do Microsoft Service Profiler      
> * Extensão de domínio do Windows Azure        
> * Extensão de Diagnóstico do Azure do Windows   
> * Extensão de Área de Trabalho Remota do Windows Azure
> * Coletor de logs do Windows Azure
>
> Você pode redefinir suas extensões por meio do portal do Azure ou do PowerShell depois de implantar a função atualizada.
>
