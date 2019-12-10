---
title: Limitações de Azure Cloud Shell | Microsoft Docs
description: Visão geral das limitações do Azure Cloud Shell
services: azure
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
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951484"
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitações de Azure Cloud Shell

Azure Cloud Shell tem as seguintes limitações conhecidas:

## <a name="general-limitations"></a>Limitações gerais

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

O computador que fornece sua sessão de Cloud Shell é temporário e é reciclado depois que a sessão está inativa por 20 minutos. Cloud Shell requer que um compartilhamento de arquivos do Azure seja montado. Como resultado, sua assinatura deve ser capaz de configurar recursos de armazenamento para acessar Cloud Shell. Outras considerações incluem:

* Com o armazenamento montado, somente as modificações dentro do diretório `$Home` são mantidas.
* Os compartilhamentos de arquivos do Azure podem ser montados somente de dentro de sua [região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
  * No bash, execute `env` para localizar sua região definida como `ACC_LOCATION`.

### <a name="browser-support"></a>Browser support (Suporte do browser)

O Cloud Shell dá suporte às versões mais recentes do Microsoft Edge, do Microsoft Internet Explorer, do Google Chrome, do Mozilla Firefox e do Apple Safari. Não há suporte para o Safari no modo privado.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Para um determinado usuário, apenas um shell pode estar ativo

Os usuários só podem iniciar um tipo de shell por vez, **bash** ou **PowerShell**. No entanto, você pode ter várias instâncias do bash ou do PowerShell em execução ao mesmo tempo. Alternar entre o bash ou o PowerShell usando o menu faz com que Cloud Shell reiniciar, o que encerra as sessões existentes. Como alternativa, você pode executar o bash dentro do PowerShell digitando `bash`e pode executar o PowerShell dentro do bash digitando `pwsh`.

### <a name="usage-limits"></a>Limites de utilização

O Cloud Shell destina-se a casos de uso interativos. Como resultado, todas as sessões não interativas de longa execução são encerradas sem aviso.

## <a name="bash-limitations"></a>Limitações do bash

### <a name="user-permissions"></a>Permissões de utilizador

As permissões são definidas como usuários regulares sem acesso ao sudo. Qualquer instalação fora de seu diretório `$Home` não é persistente.

### <a name="editing-bashrc-or-profile"></a>Editando. bashrc ou $PROFILE

Tenha cuidado ao editar o arquivo de $PROFILE do. bashrc ou do PowerShell, fazer isso pode causar erros inesperados no Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="azuread-module-name"></a>nome do módulo de `AzureAD`

O nome do módulo de `AzureAD` está `AzureAD.Standard.Preview`no momento, o módulo fornece a mesma funcionalidade.

### <a name="sqlserver-module-functionality"></a>`SqlServer` funcionalidade do módulo

O módulo `SqlServer` incluído no Cloud Shell tem apenas suporte de pré-lançamento para o PowerShell Core. Em particular, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Local do arquivo padrão quando criado na unidade do Azure:

Usando os cmdlets do PowerShell, os usuários não podem criar arquivos na unidade Azure:. Quando os usuários criam novos arquivos usando outras ferramentas, como vim ou nano, os arquivos são salvos no `$HOME` por padrão. 

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos de GUI

Se o usuário executar um comando que criaria uma caixa de diálogo do Windows, um verá uma mensagem de erro como: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Lacuna grande após a exibição da barra de progresso

Se o usuário executar uma ação que exibe uma barra de progresso, como uma Tabulação sendo concluída enquanto estiver na unidade de `Azure:`, é possível que o cursor não esteja definido corretamente e um intervalo apareça onde a barra de progresso foi anteriormente.

## <a name="next-steps"></a>Passos seguintes

[Solução de problemas Cloud Shell](troubleshooting.md) <br>
[Início Rápido para o Bash](quickstart.md) <br>
[Início rápido para o PowerShell](quickstart-powershell.md)
