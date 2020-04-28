---
title: Concha de nuvem azure para utilizadores do Windows Microsoft Docs
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
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67204146"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell no Azure Cloud Shell para utilizadores do Windows

Em maio de 2018, foram [anunciadas](https://azure.microsoft.com/blog/pscloudshellrefresh/) alterações à PowerShell em Azure Cloud Shell.
A experiência PowerShell em Azure Cloud Shell agora executa [o PowerShell Core 6](https://github.com/powershell/powershell) num ambiente Linux.
Com esta mudança, pode haver algumas diferenças na experiência PowerShell em Cloud Shell em comparação com o esperado numa experiência do Windows PowerShell.

## <a name="file-system-case-sensitivity"></a>Sensibilidade ao caso do sistema de ficheiros

O sistema de ficheiros é insensível a casos no Windows, enquanto no Linux, o sistema de ficheiros é sensível a casos.
Anteriormente `file.txt` `FILE.txt` e eram considerados o mesmo ficheiro, mas agora são considerados ficheiros diferentes.
O invólucro adequado deve `tab-completing` ser utilizado durante o sistema de ficheiros.
Experiências específicas da `tab-completing` PowerShell, tais como nomes, parâmetros e valores da Cmdlet, não são sensíveis aos casos.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell pseudónimos vs utilitários Linux

Alguns pseudónimos da PowerShell existentes têm os mesmos nomes `cat``ls`que `sort` `sleep`os comandos linux incorporados, tais como, , , etc. No PowerShell Core 6, foram removidos pseudónimos que colidem com comandos Linux incorporados.
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

## <a name="persisting-home"></a>Persistindo $HOME

Os utilizadores anteriores só podiam persistir scripts e outros ficheiros na sua Cloud Drive.
Agora, o diretório $HOME do utilizador também é persistiu em sessões.

## <a name="powershell-profile"></a>Perfil PowerShell

Por padrão, o perfil PowerShell de um utilizador não é criado.
Para criar o seu `PowerShell` perfil, `$HOME/.config`crie um diretório em baixo .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

Em `$HOME/.config/PowerShell`baixo, pode criar `profile.ps1` os seus `Microsoft.PowerShell_profile.ps1`ficheiros de perfil - e/ou .

## <a name="whats-new-in-powershell-core-6"></a>Quais as novidades no PowerShell Core 6

Para mais informações sobre o que é novidade no PowerShell Core 6, faça referência aos [docs powerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) e ao Getting Started com a publicação de blog [PowerShell Core.](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)
