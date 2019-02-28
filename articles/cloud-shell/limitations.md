---
title: Limitações do Azure do Cloud Shell | Documentos da Microsoft
description: Descrição geral das limitações do Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 0cbd2c1f9a5a36d4e11eb86a3d531340b0e0ff03
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983732"
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitações do Azure Cloud Shell

O Azure Cloud Shell tem as seguintes limitações conhecidas:

## <a name="general-limitations"></a>Limitações gerais

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

A máquina que fornece a sua sessão do Cloud Shell é temporária e ele é reciclado, depois da sessão está inativa durante 20 minutos. Cloud Shell requer uma partilha de ficheiros do Azure de ser montado. Como resultado, a sua subscrição tem de ser capaz de configurar recursos de armazenamento para aceder ao Cloud Shell. Outras considerações incluem:

* Com o armazenamento montado, apenas modificações do `$Home` diretório são mantidas.
* Partilhas de ficheiros do Azure podem ser montadas apenas a partir do seu [atribuídos região](persisting-shell-storage.md#mount-a-new-clouddrive).
  * No Bash, execute `env` para encontrar a sua região definida como `ACC_LOCATION`.

### <a name="browser-support"></a>Suporte do browser

Cloud Shell suporta as versões mais recentes do Microsoft Edge, o Microsoft Internet Explorer, o Google Chrome, o Mozilla Firefox e o Apple Safari. Não é suportado o Safari no modo privado.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Para um determinado usuário, apenas um shell pode estar ativa

Os utilizadores podem apenas iniciar um tipo de shell por vez, optar por **Bash** ou **PowerShell**. No entanto, pode ter várias instâncias de Bash ou do PowerShell em execução em simultâneo. A troca entre o Bash ou o PowerShell, utilizando o menu faz com que o Cloud Shell reiniciar, que termina sessões existentes. Em alternativa, pode executar o bash no interior do PowerShell, escrevendo `bash`, e pode executar o PowerShell no interior do bash, escrevendo `pwsh`.

### <a name="usage-limits"></a>Limites de utilização

Cloud Shell destina-se para casos de uso interativo. Como resultado, qualquer sessões de não-interativa de execução longa são terminadas sem aviso.

## <a name="bash-limitations"></a>Limitações de bash

### <a name="user-permissions"></a>Permissões de utilizador

As permissões estão definidas como usuários regulares sem acesso de sudo. Qualquer instalação fora de sua `$Home` diretório não é persistente.

### <a name="editing-bashrc-or-profile"></a>Editar .bashrc ou $PROFILE

Tenha cuidado de edição .bashrc ou arquivo de $PROFILE do PowerShell, se o fizer, pode causar erros inesperados no Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="azuread-module-name"></a>`AzureAD` nome do módulo

O `AzureAD` nome do módulo está atualmente `AzureAD.Standard.Preview`, o módulo fornece a mesma funcionalidade.

### <a name="sqlserver-module-functionality"></a>`SqlServer` funcionalidade do módulo

O `SqlServer` módulo incluído no Cloud Shell tem suporte apenas de pré-lançamento para o PowerShell Core. Em particular, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Localização do ficheiro predefinido quando criado a partir do disco do Azure:

Utilizar cmdlets do PowerShell, os utilizadores não é podem criar ficheiros do Azure: unidade. Quando os utilizadores criam novos ficheiros com outras ferramentas, como vim ou nano, os ficheiros são guardados para a `$HOME` por predefinição. 

### <a name="gui-applications-are-not-supported"></a>Aplicativos de GUI não são suportados

Se o usuário executa um comando que criaria uma caixa de diálogo do Windows, um vê uma mensagem de erro, tais como: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Grande lacuna depois de exibir a barra de progresso

Se o utilizador efetua uma ação que exibe uma barra, tal um separador de progresso conclusão ao mesmo tempo no `Azure:` unidade, em seguida, é possível que o cursor não está corretamente definido e uma lacuna aparece em que a barra de progresso era anteriormente.

## <a name="next-steps"></a>Passos Seguintes

[Resolução de problemas do Cloud Shell](troubleshooting.md) <br>
[Início Rápido para o Bash](quickstart.md) <br>
[Início rápido para o PowerShell](quickstart-powershell.md)
