---
title: Os utilizadores do Azure do Cloud Shell para Windows | Documentos da Microsoft
description: Guia para utilizadores que não estão familiarizados com sistemas Linux
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
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204146"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell dos utilizadores do Azure Cloud Shell para Windows

Em Maio de 2018, as alterações foram [anunciado](https://azure.microsoft.com/blog/pscloudshellrefresh/) para o PowerShell no Azure Cloud Shell.
A experiência do PowerShell no Azure Cloud Shell agora execuções [PowerShell Core 6](https://github.com/powershell/powershell) num ambiente do Linux.
Com esta alteração, poderá experimentar algumas diferenças na experiência do PowerShell no Cloud Shell em comparação com o que é esperado no Windows PowerShell.

## <a name="file-system-case-sensitivity"></a>Sistema de ficheiros maiúsculas e minúsculas

O sistema de ficheiros diferencia maiúsculas de minúsculas no Windows, enquanto que no Linux, o sistema de ficheiros diferencia maiúsculas de minúsculas.
Anteriormente `file.txt` e `FILE.txt` foram consideradas para ser o mesmo ficheiro, mas agora eles são considerados como arquivos diferentes.
Maiúsculas/minúsculas adequadas têm de ser utilizadas enquanto `tab-completing` no sistema de arquivos.
Experiências específicas do PowerShell, tal como `tab-completing` nomes de cmdlet, parâmetros e valores, não diferenciam maiúsculas de minúsculas.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell aliases vs utilitários do Linux

Alguns aliases existentes de PowerShell tem os mesmos nomes que comandos internos do Linux, tal como `cat`,`ls`, `sort`, `sleep`, etc. No PowerShell Core 6, aliases que entrar em conflito com os comandos de Linux internos foram removidos.
Seguem-se os aliases comuns que foram removidos, bem como seus comandos equivalentes:  

|Alias removido   |Comando equivalente   |
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

## <a name="persisting-home"></a>$HOME persistentes

Os utilizadores anteriores só poderiam manter scripts e outros arquivos na respetiva unidade na Cloud.
Agora, o diretório do usuário $HOME também é mantido entre sessões.

## <a name="powershell-profile"></a>Perfil do PowerShell

Por predefinição, não é criado um perfil de usuário do PowerShell.
Para criar seu perfil, crie uma `PowerShell` diretório em `$HOME/.config`.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

Sob `$HOME/.config/PowerShell`, pode criar os ficheiros de perfil - `profile.ps1` e/ou `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>O que há de novo no PowerShell Core 6

Para obter mais informações sobre o que há de novo no PowerShell Core 6, fazer referência a [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) e o [introdução ao PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) postagem de blog.
