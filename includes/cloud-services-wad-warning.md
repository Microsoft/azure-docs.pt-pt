---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "76279353"
---
> [!WARNING]
> Quando ativa os diagnósticos para uma função existente, quaisquer extensões que já tenha definido são desativadas quando o pacote é implantado. Incluem-se:
>
> * Diagnóstico de Agente de Monitorização da Microsoft
> * Microsoft Azure Security Monitoring
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Agente do perfil de serviço da Microsoft      
> * Extensão do domínio do Windows Azure        
> * Extensão de diagnóstico do Windows Azure   
> * Extensão de ambiente de trabalho remoto do Windows Azure
> * Colecionador de madeira de Azure windows
>
> Pode redefinir as suas extensões através do portal Azure ou PowerShell depois de implementar a função atualizada.
>
