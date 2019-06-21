---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184601"
---
> [!WARNING]
> Quando ativar o diagnóstico para uma função existente, quaisquer extensões que já definiu estão desativadas quando o pacote é implementado. Estas incluem:
>
> * Diagnóstico de agente de monitorização Microsoft
> * Monitorização de segurança do Microsoft Azure
> * Microsoft Antimalware                 
> * Agente de Monitorização da Microsoft
> * Agente do Profiler de serviço da Microsoft      
> * Extensão de domínio do Azure do Windows        
> * Extensão de diagnóstico do Azure do Windows   
> * Windows Azure extensão de área de trabalho remoto
> * Recoletor de registos do Azure do Windows
>
> Depois de implementar a função atualizada, pode redefinir suas extensões através do portal do Azure ou o PowerShell.
>
