---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76279353"
---
> [!WARNING]
> Quando ativa os diagnósticos para uma função existente, quaisquer extensões que já tenha definido são desativadas quando a embalagem é implantada. Estas incluem:
>
> * Diagnóstico de agente de monitorização da Microsoft
> * Monitorização de Segurança do Microsoft Azure
> * Microsoft Antimalware                 
> * Agente de Monitorização da Microsoft
> * Agente de perfil de serviço da Microsoft      
> * Extensão do domínio Do Windows Azure        
> * Extensão de diagnóstico do Windows Azure   
> * Extensão remota do ambiente de trabalho do Windows Azure
> * Colecionador de registos Windows Azure
>
> Pode redefinir as suas extensões através do portal Azure ou powerShell depois de implementar a função atualizada.
>
