---
title: Limitações da Casca de Nuvem Azure [ Microsoft Docs
description: Visão geral das limitações da Casca de Nuvem Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74951484"
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitações da Casca de Nuvem Azure

A Azure Cloud Shell tem as seguintes limitações conhecidas:

## <a name="general-limitations"></a>Limitações gerais

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

A máquina que fornece a sua sessão Cloud Shell é temporária e é reciclada após a sua sessão estar inativa durante 20 minutos. Cloud Shell requer que uma parte de ficheiro Azure seja montada. Como resultado, a sua subscrição deve ser capaz de configurar recursos de armazenamento para aceder à Cloud Shell. Outras considerações incluem:

* Com armazenamento montado, apenas `$Home` persistem modificações dentro do diretório.
* As ações de ficheiros Azure só podem ser montadas a partir da [sua região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Em Bash, `env` corra para encontrar `ACC_LOCATION`a sua região definida como .

### <a name="browser-support"></a>Browser support (Suporte do browser)

A Cloud Shell suporta as versões mais recentes do Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. O Safari em modo privado não é suportado.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Para um determinado utilizador, apenas uma concha pode estar ativa

Os utilizadores só podem lançar um tipo de concha de cada vez, seja **a Bash** ou a **PowerShell.** No entanto, pode ter vários casos de Bash ou PowerShell a funcionar de uma só vez. Trocar entre Bash ou PowerShell utilizando o menu faz com que a Cloud Shell reinicie, o que encerra as sessões existentes. Em alternativa, pode correr bash dentro `bash`da PowerShell digitando , `pwsh`e pode executar PowerShell dentro da festa digitando .

### <a name="usage-limits"></a>Limites de utilização

Cloud Shell destina-se a casos de uso interativo. Como resultado, quaisquer sessões não interativas de longa duração terminam sem aviso prévio.

## <a name="bash-limitations"></a>Limitações de bash

### <a name="user-permissions"></a>Permissões de utilizador

As permissões são definidas como utilizadores regulares sem acesso sudo. Qualquer instalação `$Home` fora do seu diretório não é persistiu.

### <a name="editing-bashrc-or-profile"></a>Edição .bashrc ou $PROFILE

Tenha cuidado ao editar .bashrc ou ficheiro $PROFILE da PowerShell, fazê-lo pode causar erros inesperados na Cloud Shell.

## <a name="powershell-limitations"></a>Limitações powerShell

### <a name="azuread-module-name"></a>`AzureAD`nome do módulo

O `AzureAD` nome do `AzureAD.Standard.Preview`módulo é atualmente , o módulo fornece a mesma funcionalidade.

### <a name="sqlserver-module-functionality"></a>`SqlServer`funcionalidade do módulo

O `SqlServer` módulo incluído na Cloud Shell tem apenas suporte de pré-lançamento para PowerShell Core. Em particular, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Localização do ficheiro predefinido quando criada a partir da unidade Azure:

Utilizando cmdlets PowerShell, os utilizadores não podem criar ficheiros sob o Azure: unidade. Quando os utilizadores criam novos ficheiros utilizando outras ferramentas, `$HOME` como o vim ou o nano, os ficheiros são guardados por defeito. 

### <a name="gui-applications-are-not-supported"></a>As aplicações gui não são suportadas

Se o utilizador tiver um comando que crie uma caixa de diálogo `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`Windows, vê-se uma mensagem de erro como: .

### <a name="large-gap-after-displaying-progress-bar"></a>Grande Lacuna após exibir barra de progresso

Se o utilizador executar uma ação que apresenta uma barra de `Azure:` progresso, como um separador que está a completar durante a unidade, então é possível que o cursor não esteja corretamente definido e apareça uma lacuna onde a barra de progresso estava anteriormente.

## <a name="next-steps"></a>Passos seguintes

[Resolução de problemas Cloud Shell](troubleshooting.md) <br>
[Início Rápido para o Bash](quickstart.md) <br>
[Início rápido para o PowerShell](quickstart-powershell.md)
