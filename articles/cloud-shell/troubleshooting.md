---
title: Solução de problemas Azure Cloud Shell | Microsoft Docs
description: Solução de problemas Azure Cloud Shell
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
ms.openlocfilehash: 91dc87cd6bda93663fb4b4eae3d498ae56ba4b3e
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169601"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Solução de problemas & limitações do Azure Cloud Shell

As resoluções conhecidas para solucionar problemas no Azure Cloud Shell incluem:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Solução de problemas gerais

### <a name="early-timeouts-in-firefox"></a>Tempos limite iniciais no FireFox

- **Detalhes**: Cloud Shell utiliza um WebSocket aberto para passar entrada/saída para o navegador. O FireFox tem políticas predefinidas que podem fechar o WebSocket prematuramente, causando tempos limite iniciais em Cloud Shell.
- **Resolução**: Abra o Firefox e navegue até "sobre: config" na caixa URL. Pesquise "Network. WebSocket. tempo_limite. ping. Request" e altere o valor de 0 para 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Desabilitando Cloud Shell em um ambiente de rede bloqueado

- **Detalhes**: os administradores podem desejar desabilitar o acesso a Cloud Shell para seus usuários. O Cloud Shell utiliza o acesso ao domínio `ux.console.azure.com`, que pode ser negado, interrompendo qualquer acesso aos ponto de entrada de Cloud Shell, incluindo portal.azure.com, shell.azure.com, Visual Studio Code extensão de conta do Azure e docs.microsoft.com.
- **Resolução**: restrinja o acesso a `ux.console.azure.com` por meio de configurações de rede para seu ambiente. O ícone de Cloud Shell ainda existirá em portal.azure.com, mas não se conectará com êxito ao serviço.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Caixa de diálogo de armazenamento-erro: 403 RequestDisallowedByPolicy

- **Detalhes**: ao criar uma conta de armazenamento por meio do Cloud Shell, ela não é bem-sucedida devido a uma política do Azure colocada pelo administrador. A mensagem de erro incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: contate o administrador do Azure para remover ou atualizar a política do Azure que nega a criação de armazenamento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Caixa de diálogo de armazenamento-erro: 400 DisallowedOperation

- **Detalhes**: ao usar uma assinatura do Azure Active Directory, você não pode criar o armazenamento.
- **Resolução**: Use uma assinatura do Azure capaz de criar recursos de armazenamento. As assinaturas do Azure AD não são capazes de criar recursos do Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Saída de terminal-erro: falha ao conectar terminal: o WebSocket não pode ser estabelecido. Pressione `Enter` para reconectar.
- **Detalhes**: Cloud Shell requer a capacidade de estabelecer uma conexão WebSocket com a infraestrutura Cloud Shell.
- **Resolução**: Verifique se você definiu as configurações de rede para habilitar o envio de solicitações HTTPS e solicitações WebSocket para domínios em *. console.Azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Defina sua conexão de Cloud Shell para dar suporte ao uso do TLS 1,2
 - **Detalhes**: para definir a versão do TLS para sua conexão com Cloud Shell, você deve definir configurações específicas do navegador.
 - **Resolução**: Navegue até as configurações de segurança do seu navegador e marque a caixa de seleção ao lado de "usar TLS 1,2".

## <a name="bash-troubleshooting"></a>Solução de problemas do bash

### <a name="cannot-run-the-docker-daemon"></a>Não é possível executar o daemon do Docker

- **Detalhes**: o Cloud Shell utiliza um contêiner para hospedar seu ambiente de Shell, pois um resultado que executa o daemon não é permitido.
- **Resolução**: utilize a [máquina Docker](https://docs.docker.com/machine/overview/), que é instalada por padrão, para gerenciar contêineres do Docker de um host remoto do Docker.

## <a name="powershell-troubleshooting"></a>Solução de problemas do PowerShell

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos de GUI

- **Detalhes**: se um usuário iniciar um aplicativo de GUI, o prompt não retornará. Por exemplo, quando uma clonagem de um repositório GitHub privado que tem a autenticação de dois fatores habilitada, uma caixa de diálogo é exibida para concluir a autenticação de dois fatores.
- **Resolução**: feche e reabra o Shell.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Solucionando problemas de gerenciamento remoto de VMs do Azure
> [!NOTE]
> As VMs do Azure devem ter um endereço IP voltado para o público.

- **Detalhes**: devido às configurações padrão do firewall do Windows para WinRM, o usuário pode ver o seguinte erro: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolução**: execute `Enable-AzVMPSRemoting` para habilitar todos os aspectos da comunicação remota do PowerShell no computador de destino.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` não atualiza o resultado na unidade do Azure

- **Detalhes**: por padrão, para otimizar a experiência do usuário, os resultados de `dir` são armazenados em cache na unidade do Azure.
- **Resolução**: depois de criar, atualizar ou remover um recurso do Azure, execute `dir -force` para atualizar os resultados na unidade do Azure.

## <a name="general-limitations"></a>Limitações gerais

Azure Cloud Shell tem as seguintes limitações conhecidas:

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

O computador que fornece sua sessão de Cloud Shell é temporário e é reciclado depois que a sessão está inativa por 20 minutos. Cloud Shell requer que um compartilhamento de arquivos do Azure seja montado. Como resultado, sua assinatura deve ser capaz de configurar recursos de armazenamento para acessar Cloud Shell. Outras considerações incluem:

- Com o armazenamento montado, somente as modificações no diretório `clouddrive` são mantidas. No bash, seu diretório `$HOME` também é persistido.
- Os compartilhamentos de arquivos do Azure podem ser montados somente de dentro de sua [região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
  - No bash, execute `env` para localizar sua região definida como `ACC_LOCATION`.
- Os arquivos do Azure dão suporte apenas a contas de armazenamento com redundância local e de armazenamento geograficamente.

### <a name="browser-support"></a>Suporte a navegador

O Cloud Shell dá suporte às versões mais recentes dos seguintes navegadores:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Não há suporte para o Safari no modo privado.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limites de utilização

O Cloud Shell destina-se a casos de uso interativos. Como resultado, todas as sessões não interativas de longa execução são encerradas sem aviso.

### <a name="user-permissions"></a>Permissões de utilizador

As permissões são definidas como usuários regulares sem acesso ao sudo. Qualquer instalação fora do diretório `$Home` não será persistida.

## <a name="bash-limitations"></a>Limitações do bash

### <a name="editing-bashrc"></a>Editando. bashrc

Tome cuidado ao editar. bashrc, isso pode causar erros inesperados no Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="preview-version-of-azuread-module"></a>Versão de visualização do módulo AzureAD

Atualmente, `AzureAD.Standard.Preview`, uma versão de visualização do módulo baseado em .NET Standard, está disponível. Esse módulo fornece a mesma funcionalidade que `AzureAD`.

### <a name="sqlserver-module-functionality"></a>funcionalidade de módulo `SqlServer`

O módulo `SqlServer` incluído no Cloud Shell tem apenas suporte de pré-lançamento para o PowerShell Core. Em particular, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Local do arquivo padrão quando criado na unidade do Azure

Usando cmdlets do PowerShell, os usuários não podem criar arquivos na unidade do Azure. Quando os usuários criam novos arquivos usando outras ferramentas, como vim ou nano, os arquivos são salvos no `$HOME` por padrão.

### <a name="tab-completion-can-throw-psreadline-exception"></a>O preenchimento com Tab pode gerar exceção PSReadline

Se o PSReadline EditMode do usuário for definido como Emacs, o usuário tentará exibir todas as possibilidades por meio de preenchimento com Tab e o tamanho da janela será muito pequeno para exibir todas as possibilidades, PSReadline gerará uma exceção sem tratamento.

### <a name="large-gap-after-displaying-progress-bar"></a>Lacuna grande após a exibição da barra de progresso

Se um comando ou uma ação do usuário exibir uma barra de progresso, como a conclusão da tecla enquanto estiver na unidade `Azure:`, é possível que o cursor não esteja definido corretamente e um intervalo apareça em que a barra de progresso foi anteriormente.

### <a name="random-characters-appear-inline"></a>Os caracteres aleatórios aparecem embutidos

Os códigos de sequência da posição do cursor, por exemplo `5;13R`, podem aparecer na entrada do usuário. Os caracteres podem ser removidos manualmente.

## <a name="personal-data-in-cloud-shell"></a>Dados pessoais no Cloud Shell

Azure Cloud Shell leva seus dados pessoais seriamente, os dados capturados e armazenados pelo serviço de Azure Cloud Shell são usados para fornecer padrões para sua experiência, como o Shell usado mais recentemente, o tamanho de fonte preferencial, o tipo de fonte preferencial e os detalhes de compartilhamento de arquivos Essa unidade de nuvem de volta. Se você quiser exportar ou excluir esses dados, use as instruções a seguir.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportar
Para **Exportar** as configurações de usuário Cloud Shell salvas para você, como o Shell preferencial, o tamanho da fonte e o tipo de fonte, execute os comandos a seguir.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Iniciar Azure Cloud Shell")](https://shell.azure.com)
2. Execute os seguintes comandos no bash ou PowerShell:

Raso

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
Para **excluir** as configurações de usuário Cloud Shell salvas para você, como o Shell preferencial, o tamanho da fonte e o tipo de fonte, execute os comandos a seguir. Na próxima vez que iniciar Cloud Shell você será solicitado a carregar um compartilhamento de arquivo novamente. 

>[!Note]
> Se você excluir suas configurações de usuário, o compartilhamento real de arquivos do Azure não será excluído. Vá para os arquivos do Azure para concluir essa ação.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Iniciar Azure Cloud Shell")](https://shell.azure.com)
2. Execute os seguintes comandos no bash ou PowerShell:

Raso

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Limitações do Azure governamental
Azure Cloud Shell no Azure governamental só é acessível por meio do portal do Azure.
