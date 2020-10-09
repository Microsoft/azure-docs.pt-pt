---
title: Limitações da Azure Cloud Shell Microsoft Docs
description: Visão geral das limitações da Azure Cloud Shell
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74951484"
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitações da Concha da Nuvem Azure

A Azure Cloud Shell tem as seguintes limitações conhecidas:

## <a name="general-limitations"></a>Limitações gerais

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

A máquina que fornece a sua sessão Cloud Shell é temporária e é reciclada após a sessão estar inativa durante 20 minutos. A Cloud Shell requer que seja montada uma partilha de ficheiros Azure. Como resultado, a sua subscrição deve ser capaz de configurar recursos de armazenamento para aceder à Cloud Shell. Outras considerações incluem:

* Com o armazenamento montado, apenas persistem modificações dentro do `$Home` diretório.
* As ações de ficheiros Azure só podem ser montadas dentro da [região afetada.](persisting-shell-storage.md#mount-a-new-clouddrive)
  * Em Bash, corra `env` para encontrar a sua região definida como `ACC_LOCATION` .

### <a name="browser-support"></a>Browser support (Suporte do browser)

A Cloud Shell suporta as versões mais recentes do Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. O Safari em modo privado não é suportado.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Para um determinado utilizador, apenas uma concha pode estar ativa

Os utilizadores só podem lançar um tipo de concha de cada vez, ou **Bash** ou **PowerShell**. No entanto, pode ter várias instâncias de Bash ou PowerShell a funcionar em simultâneo. A troca entre Bash ou PowerShell utilizando o menu faz com que a Cloud Shell reinicie, o que termina as sessões existentes. Em alternativa, podes fazer uma bash dentro do PowerShell `bash` digitando, e podes executar o PowerShell dentro da bash dactilografia `pwsh` .

### <a name="usage-limits"></a>Limites de utilização

A Cloud Shell destina-se a casos de utilização interativa. Como resultado, quaisquer sessões não interativas de longa duração terminam sem aviso prévio.

## <a name="bash-limitations"></a>Limitações de bash

### <a name="user-permissions"></a>Permissões de utilizador

As permissões são definidas como utilizadores regulares sem acesso de sudo. Qualquer instalação fora do seu `$Home` diretório não é persistiu.

### <a name="editing-bashrc-or-profile"></a>Edição .bashrc ou $PROFILE

Tenha cuidado ao editar o ficheiro de $PROFILE da PowerShell ou da PowerShell, podendo causar erros inesperados na Cloud Shell.

## <a name="powershell-limitations"></a>Limitações PowerShell

### <a name="azuread-module-name"></a>`AzureAD` nome do módulo

O `AzureAD` nome do módulo encontra-se atualmente, o módulo fornece a mesma `AzureAD.Standard.Preview` funcionalidade.

### <a name="sqlserver-module-functionality"></a>`SqlServer` funcionalidade do módulo

O `SqlServer` módulo incluído na Cloud Shell tem apenas suporte pré-lançado para PowerShell Core. Em particular, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Localização do ficheiro padrão quando criado a partir da unidade Azure:

Utilizando cmdlets PowerShell, os utilizadores não podem criar ficheiros sob o Azure: unidade. Quando os utilizadores criam novos ficheiros utilizando outras ferramentas, como vim ou nano, os ficheiros são guardados `$HOME` por padrão. 

### <a name="gui-applications-are-not-supported"></a>As aplicações gui não são suportadas

Se o utilizador executa um comando que criaria uma caixa de diálogo do Windows, vê-se uma mensagem de erro como: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)` .

### <a name="large-gap-after-displaying-progress-bar"></a>Grande lacuna após exibir barra de progresso

Se o utilizador efetuar uma ação que apresente uma barra de progresso, como uma aba completando durante a `Azure:` unidade, então é possível que o cursor não esteja corretamente definido e apareça uma lacuna onde a barra de progresso estava anteriormente.

## <a name="next-steps"></a>Passos seguintes

[Resolução de problemas Cloud Shell](troubleshooting.md) <br>
[Início Rápido para o Bash](quickstart.md) <br>
[Início rápido para o PowerShell](quickstart-powershell.md)
