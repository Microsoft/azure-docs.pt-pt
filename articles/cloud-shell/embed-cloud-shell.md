---
title: Incorporar Azure Cloud Shell | Microsoft Docs
description: Aprenda a incorporar a Azure Cloud Shell.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "60614315"
---
# <a name="embed-azure-cloud-shell"></a>Concha de nuvem de Azure incorporada

A Incorporação da Cloud Shell permite que os desenvolvedores e escritores de conteúdos abram diretamente a Cloud Shell a partir de um URL dedicado, [shell.azure.com](https://shell.azure.com). Isto traz imediatamente toda a potência da autenticação, ferramentas e ferramentas Azure CLI/Azure PowerShell atualizadas para os seus utilizadores.

Botão de tamanho regular

[![Lançamento regular](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

Botão grande de tamanho grande

[![Grande lançamento](https://shell.azure.com/images/launchcloudshell@2x.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Procedimentos

Integre o botão de lançamento da Cloud Shell em ficheiros de marcação, copiando o seguinte:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

O HTML para incorporar uma Cloud Shell pop-up está abaixo:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Personalizar experiência

Desaça uma experiência específica de concha aumentando o seu URL.

|Experiência   |URL   |
|---|---|
|Concha mais recentemente usada   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Passos seguintes
[Bash em Cloud Shell quickstart](quickstart.md)<br>
[PowerShell em Cloud Shell quickstart](quickstart-powershell.md)
