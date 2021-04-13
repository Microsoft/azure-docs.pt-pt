---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305566"
---
Certifique-se de que podem ser utilizados os seguintes passos para aceder ao dispositivo a partir do seu cliente.

Verifique se o seu cliente pode ligar-se ao Gestor de Recursos Azure local. 

1. Ligue para apis de dispositivo local para autenticar:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Forneça o nome de utilizador `EdgeArmUser` e a palavra-passe para ligar através do Azure Resource Manager. Se não se lembrar da palavra-passe, [reinicie a palavra-passe para O Gestor de Recursos Azure](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) e utilize esta palavra-passe para iniciar sação.