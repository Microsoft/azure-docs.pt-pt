---
title: Resolução de problemas do Cloud Shell do Azure | Documentos da Microsoft
description: Resolução de problemas do Azure Cloud Shell
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
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: eb7deacc068661ca9a4f473ee2d36b7d4464c81c
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905516"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Resolução de problemas & limitações do Azure Cloud Shell

Resoluções conhecidas para a resolução de problemas no Azure Cloud Shell incluem:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Resolução de problemas

### <a name="early-timeouts-in-firefox"></a>Tempos limite inicial no FireFox

- **Detalhes**: Cloud Shell utiliza um websocket aberto para passar a entrada/saída ao seu navegador. FireFox tem políticas predefinidas que podem fechar o websocket prematuramente esgotando antecipada no Cloud Shell.
- **Resolução**: Abra o FireFox e navegue para "sobre: config" na caixa URL. Procure "network.websocket.timeout.ping.request" e altere o valor de 0 a 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Desativar o Cloud Shell uma rede bloqueada ambiente

- **Detalhes**: Os administradores podem querer desativar o acesso ao Cloud Shell para os seus utilizadores. Utiliza o acesso ao cloud Shell a `ux.console.azure.com` domínio que pode ser negado, parar qualquer acesso à entrypoints do Cloud Shell, incluindo portal.azure.com, shell.azure.com, a extensão de conta do Azure de código do Visual Studio e o docs.microsoft.com.
- **Resolução**: Restringir o acesso ao `ux.console.azure.com` através das definições de rede para o seu ambiente. O ícone do Cloud Shell continuarão a existir em portal.azure.com, mas não irá ligar com êxito para o serviço.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Diálogo de armazenamento - erro: 403 RequestDisallowedByPolicy

- **Detalhes**: Ao criar uma conta de armazenamento através do Cloud Shell, é sem êxito devido a uma política do Azure colocada pelo seu administrador. Mensagem de erro irá incluir: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: Contacte o administrador do Azure para remover ou atualizar a política do Azure negar a criação de armazenamento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Diálogo de armazenamento - erro: 400 DisallowedOperation

- **Detalhes**: Quando utilizar uma subscrição do Azure Active Directory, não é possível criar o armazenamento.
- **Resolução**: Utilize uma subscrição do Azure com capacidade de criação de recursos de armazenamento. Subscrições do Azure AD não são possível criar recursos do Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal de saída - erro: Falha ao ligar o terminal: não é possível estabelecer o websocket. Prima `Enter` restabelecer a ligação.
- **Detalhes**: Cloud Shell requer a capacidade de estabelecer uma conexão websocket à infraestrutura de Cloud Shell.
- **Resolução**: Verifique que configurou as definições de rede para permitir o envio de pedidos de https e pedidos de websocket para domínios em *. console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Definir a ligação do Cloud Shell para suportar a utilização de TLS 1.2
 - **Detalhes**: Para definir a versão do TLS para a sua ligação para o Cloud Shell, tem de definir as configurações específicas do navegador.
 - **Resolução**: Navegue para as definições de segurança do seu browser e selecione a caixa de verificação junto a "Utilização TLS 1.2".

## <a name="bash-troubleshooting"></a>Resolução de problemas de bash

### <a name="cannot-run-the-docker-daemon"></a>Não é possível executar o daemon do docker

- **Detalhes**: Cloud Shell utiliza um contentor para alojar o seu ambiente de shell, assim que executa o daemon não é permitida.
- **Resolução**: Utilizar [docker-machine](https://docs.docker.com/machine/overview/), que é instalada por predefinição, para gerir os contentores do docker a partir de um anfitrião do Docker remoto.

## <a name="powershell-troubleshooting"></a>Resolução de problemas do PowerShell

### <a name="gui-applications-are-not-supported"></a>Aplicativos de GUI não são suportados

- **Detalhes**: Se um usuário inicia um aplicativo de GUI, linha de comandos não devolve. Por exemplo, quando um clonar um repositório privado do GitHub com autenticação de dois fatores ativada, é apresentada uma caixa de diálogo para concluir a autenticação de dois fatores.
- **Resolução**: Feche e reabra o shell.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Resolução de problemas de gestão remota de VMs do Azure
> [!NOTE]
> VMs do Azure tem de ter um público com acesso à endereço IP.

- **Detalhes**: Devido às predefinições de Firewall do Windows para o WinRM, o utilizador poderá ver o erro seguinte: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolução**:  Executar `Enable-AzVMPSRemoting` para permitir que todos os aspetos da comunicação remota do PowerShell no computador de destino.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` Não atualizar o resultado na unidade do Azure

- **Detalhes**: Por predefinição, para otimizar a experiência de utilizador, os resultados de `dir` é colocado em cache na unidade do Azure.
- **Resolução**: Depois de criar, atualizar ou remover um recurso do Azure, executar `dir -force` para atualizar os resultados na unidade do Azure.

## <a name="general-limitations"></a>Limitações gerais

O Azure Cloud Shell tem as seguintes limitações conhecidas:

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

A máquina que fornece a sua sessão do Cloud Shell é temporária e ele é reciclado, depois da sessão está inativa durante 20 minutos. Cloud Shell requer uma partilha de ficheiros do Azure de ser montado. Como resultado, a sua subscrição tem de ser capaz de configurar recursos de armazenamento para aceder ao Cloud Shell. Outras considerações incluem:

- Com o armazenamento montado, apenas modificações do `clouddrive` diretório são mantidas. No Bash, sua `$HOME` diretório também é mantido.
- Partilhas de ficheiros do Azure podem ser montadas apenas a partir do seu [atribuídos região](persisting-shell-storage.md#mount-a-new-clouddrive).
  - No Bash, execute `env` para encontrar a sua região definida como `ACC_LOCATION`.
- Ficheiros do Azure suportam o armazenamento apenas localmente redundante e contas de armazenamento georredundante.

### <a name="browser-support"></a>Suporte do browser

Cloud Shell suporta as versões mais recentes dos seguintes browsers:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Não é suportado o Safari no modo privado.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limites de utilização

Cloud Shell destina-se para casos de uso interativo. Como resultado, qualquer sessões de não-interativa de execução longa são terminadas sem aviso.

### <a name="user-permissions"></a>Permissões de utilizador

As permissões estão definidas como usuários regulares sem acesso de sudo. Qualquer instalação fora de sua `$Home` diretório não é persistente.

## <a name="bash-limitations"></a>Limitações de bash

### <a name="editing-bashrc"></a>Editar .bashrc

Tenha cuidado de edição .bashrc, isso pode causar erros inesperados no Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="preview-version-of-azuread-module"></a>Versão de pré-visualização do módulo do AzureAD

Atualmente, `AzureAD.Standard.Preview`, uma versão de pré-visualização do módulo .NET Standard com base em, está disponível. Este módulo fornece a mesma funcionalidade que `AzureAD`.

### <a name="sqlserver-module-functionality"></a>`SqlServer` funcionalidade do módulo

O `SqlServer` módulo incluído no Cloud Shell tem suporte apenas de pré-lançamento para o PowerShell Core. Em particular, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Localização do ficheiro predefinido quando criado a partir do disco do Azure

Utilizar cmdlets do PowerShell, os utilizadores não é possível criar arquivos sob a unidade do Azure. Quando os utilizadores criam novos ficheiros com outras ferramentas, como vim ou nano, os ficheiros são guardados para a `$HOME` por predefinição.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Conclusão de tabulação pode lançar a exceção de PSReadline

Se PSReadline EditMode o utilizador estiver definido como Emacs, o utilizador tenta exibir todas as possibilidades por meio de conclusão de tabulação e o tamanho da janela é demasiado pequeno para exibir todas as possibilidades, PSReadline lançará uma exceção não processada.

### <a name="large-gap-after-displaying-progress-bar"></a>Grande lacuna depois de exibir a barra de progresso

Se uma ação de comando ou usuário exibe uma barra, tal um separador de progresso conclusão ao mesmo tempo no `Azure:` unidade, em seguida, é possível que o cursor não está corretamente definido e uma lacuna aparece em que a barra de progresso era anteriormente.

### <a name="random-characters-appear-inline"></a>Caracteres aleatórios são apresentadas inline

A sequência de posição do cursor códigos, por exemplo `5;13R`, pode aparecer na entrada do usuário. Os caracteres podem ser removidos manualmente.

## <a name="personal-data-in-cloud-shell"></a>Dados pessoais no Cloud Shell

O Azure Cloud Shell leva os seus dados pessoais sério, os dados capturados e armazenados pelo serviço do Azure Cloud Shell são utilizados para fornecer predefinições para a sua experiência, como o shell que utilizou mais recentemente, tamanho da fonte preferencial, o tipo de letra preferencial e o arquivo partilharem detalhes que volta a nuvem unidade. Caso queira exportar ou eliminar esses dados, utilize as instruções seguintes.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportar
Para **exportar** as definições de utilizador, o Cloud Shell poupa-se para como preferencial shell, o tamanho da fonte e o tipo de letra, execute os seguintes comandos.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Inicie o Azure Cloud Shell")](https://shell.azure.com)
2. Execute os seguintes comandos no Bash ou do PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Eliminar
Para **eliminar** as definições de utilizador, o Cloud Shell poupa-se para como preferencial shell, o tamanho da fonte e o tipo de letra, execute os seguintes comandos. Da próxima vez que inicia o Cloud Shell será solicitado para carregar uma partilha de ficheiros novamente. 

>[!Note]
> Se eliminar as definições de utilizador, a partilha de ficheiros do Azure real não será eliminada. Vá para os ficheiros do Azure para concluir essa ação.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Inicie o Azure Cloud Shell")](https://shell.azure.com)
2. Execute os seguintes comandos no Bash ou do PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
