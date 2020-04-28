---
title: Incorporada Casca de Nuvem Azure [ Microsoft Docs
description: Aprenda a incorporar Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60614315"
---
# <a name="embed-azure-cloud-shell"></a>Concha de nuvem azure embed

Incorporar a Cloud Shell permite que desenvolvedores e escritores de conteúdos abram diretamente a Cloud Shell a partir de um URL dedicado, [shell.azure.com](https://shell.azure.com). Isto traz imediatamente aos seus utilizadores toda a potência das ferramentas de autenticação, ferramentas e powerShell Azure CLI/Azure da Cloud Shell.

Botão de tamanho regular

[![Lançamento regular](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

Botão grande tamanho

[![Grande lançamento](https://shell.azure.com/images/launchcloudshell@2x.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Procedimentos

Integre o botão de lançamento da Cloud Shell em ficheiros de marcação copiando o seguinte:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

O HTML para incorporar uma Nuvem Shell pop-up está abaixo:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Personalizar experiência

Delineie uma experiência específica de concha aumentando o seu URL.

|Experiência   |do IdP   |
|---|---|
|Concha mais recentemente usada   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Passos seguintes
[Bash in Cloud Shell quickstart](quickstart.md)<br>
[PowerShell em Cloud Shell quickstart](quickstart-powershell.md)
