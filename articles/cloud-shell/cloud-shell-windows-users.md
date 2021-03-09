---
title: Azure Cloud Shell para utilizadores do Windows | Microsoft Docs
description: Guia para os utilizadores que não estão familiarizados com os sistemas Linux
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 8cc1934cc97ecf821de8644dda45d867b3ca3e85
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508951"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell no Azure Cloud Shell para utilizadores do Windows

Em maio de 2018, foram [anunciadas](https://azure.microsoft.com/blog/pscloudshellrefresh/) mudanças à PowerShell em Azure Cloud Shell.
A experiência PowerShell em Azure Cloud Shell agora executa [o PowerShell Core 6](https://github.com/powershell/powershell) num ambiente Linux.
Com esta mudança, pode haver algumas diferenças na experiência PowerShell na Cloud Shell em comparação com o que é esperado numa experiência Windows PowerShell.

## <a name="file-system-case-sensitivity"></a>Sensibilidade ao caso do sistema de ficheiros

O sistema de ficheiros é insensível a caso no Windows, enquanto no Linux, o sistema de ficheiros é sensível a casos.
Anteriormente `file.txt` e `FILE.txt` eram considerados o mesmo ficheiro, mas agora são considerados ficheiros diferentes.
O invólucro adequado deve ser utilizado durante `tab-completing` o sistema de ficheiros.
Experiências específicas PowerShell, tais como `tab-completing` nomes de cmdlet, parâmetros e valores, não são sensíveis a casos.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell aliases vs Utilitários Linux

Alguns pseudónimos powerShell existentes têm os mesmos nomes que comandos Linux incorporados, tais `cat` `ls` como, `sort` , , , `sleep` etc. No PowerShell Core 6, os pseudónimos que colidem com comandos Linux incorporados foram removidos.
Abaixo estão os pseudónimos comuns que foram removidos, bem como os seus comandos equivalentes:  

|Pseudónimo removido   |Comando Equivalente   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>$HOME persistente

Os utilizadores anteriores só podiam persistir scripts e outros ficheiros na sua Cloud Drive.
Agora, o diretório $HOME do utilizador também é persistido em todas as sessões.

## <a name="powershell-profile"></a>Perfil PowerShell

Por predefinição, o perfil PowerShell de um utilizador não é criado.
Para criar o seu perfil, crie um `PowerShell` diretório em `$HOME/.config` .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

Em `$HOME/.config/PowerShell` , pode criar os seus ficheiros de perfil - `profile.ps1` e/ou `Microsoft.PowerShell_profile.ps1` .

## <a name="whats-new-in-powershell-core-6"></a>Novidades no PowerShell Core 6

Para obter mais informações sobre as novidades no PowerShell Core 6, consulte os [docs PowerShell](/powershell/scripting/whats-new/what-s-new-in-powershell-70) e o post de blog [Getting Started with PowerShell Core.](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)