---
title: Incorporar o Azure Cloud Shell | Documentos da Microsoft
description: Aprenda a incorporar o Azure Cloud Shell.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995976"
---
# <a name="embed-azure-cloud-shell"></a>Incorporar o Azure Cloud Shell

Incorporar o Cloud Shell permite que os programadores e criadores de conteúdo abrir o Cloud Shell diretamente a um URL dedicado [shell.azure.com](https://shell.azure.com). Isso traz imediatamente todo o potencial da autenticação do Cloud Shell, ferramentas, e atualizados do Azure CLI/Azure PowerShell ferramentas aos seus utilizadores.

Botão de tamanho regular

[![Lançamento regular](https://shell.azure.com/images/launchcloudshell.png "iniciar o Azure Cloud Shell")](https://shell.azure.com)

Botão de tamanho grande

[![Grande lançamento](https://shell.azure.com/images/launchcloudshell@2x.png "iniciar o Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Procedimentos

Integre o botão de lançamento do Cloud Shell em ficheiros de markdown ao copiar o seguinte:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

O HTML para incorporar um pop-up, o Cloud Shell é abaixo:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Personalizar a experiência

Defina uma experiência de shell específica ao aumentar o seu URL.

|Experiência   |do IdP   |
|---|---|
|Mais recentemente utilizado shell   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Passos Seguintes
[Guia de introdução do Cloud Shell de bash](quickstart.md)<br>
[PowerShell no início rápido do Cloud Shell](quickstart-powershell.md)
