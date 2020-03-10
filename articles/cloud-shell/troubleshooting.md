---
title: Tiroteio em Nuvem Azure  Microsoft Docs
description: Resolução de problemas Azure Cloud Shell
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
ms.openlocfilehash: 1d244d7b62fcfefeec6f628f473274ae982bf4d8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394310"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Resolução de problemas e limitações da casca de nuvem azure

Resoluções conhecidas para problemas de resolução de problemas em Azure Cloud Shell incluem:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Resolução geral de problemas

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Erro com cmdlets AzureAD na PowerShell

- **Detalhes**: Quando executa cmdlets AzureAD como `Get-AzureADUser` em Cloud Shell, poderá ver um erro: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`. 
- **Resolução**: Executar o `Connect-AzureAD` cmdlet. Anteriormente, a Cloud Shell executou este cmdlet automaticamente durante o arranque da PowerShell. Para acelerar a hora de início, o cmdlet já não funciona automaticamente. Pode optar por restaurar o comportamento anterior adicionando `Connect-AzureAD` ao ficheiro $PROFILE no PowerShell.

### <a name="early-timeouts-in-firefox"></a>Intervalos iniciais no FireFox

- **Detalhes**: Cloud Shell utiliza uma websocket aberta para passar a entrada/saída para o seu navegador. O FireFox tem políticas pré-definidas que podem fechar a websocket prematuramente causando intervalos precoces em Cloud Shell.
- **Resolução**: Abra o FireFox e navegue para "about:config" na caixa URL. Procure "network.websocket.timeout.ping.request" e altere o valor de 0 para 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Desativando a Cloud Shell em um ambiente de rede bloqueado

- **Detalhes**: Os administradores podem desejar desativar o acesso à Cloud Shell para os seus utilizadores. A Cloud Shell utiliza o acesso ao domínio `ux.console.azure.com`, o que pode ser negado, impedindo qualquer acesso aos pontos de entrada da Cloud Shell, incluindo portal.azure.com, shell.azure.com, extensão de Código de Código do Estúdio Visual Azure e docs.microsoft.com. Na nuvem do Governo dos EUA, o ponto de entrada é `ux.console.azure.us`; não há shell.azure.us correspondentes.
- **Resolução**: Restringir o acesso a `ux.console.azure.com` ou `ux.console.azure.us` através de definições de rede para o seu ambiente. O ícone Cloud Shell ainda existirá no portal Azure, mas não irá ligar-se com sucesso ao serviço.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Diálogo de Armazenamento - Erro: 403 RequestDisallowedByPolicy

- **Detalhes**: Ao criar uma conta de armazenamento através da Cloud Shell, não é bem sucedido devido a uma política Azure colocada pelo seu administrador. A mensagem de erro incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: Contacte o seu administrador Azure para remover ou atualizar a política Azure que nega a criação de armazenamento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Diálogo de Armazenamento - Erro: 400 Operação Proibida

- **Detalhes**: Ao utilizar uma subscrição de Diretório Ativo Azure, não é possível criar armazenamento.
- **Resolução**: Utilize uma subscrição Azure capaz de criar recursos de armazenamento. As subscrições da Azure AD não são capazes de criar recursos Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Saída do terminal - Erro: Falha na ligação do terminal: a tomada de teia não pode ser estabelecida. Pressione `Enter` para se reconectar.
- **Detalhes**: Cloud Shell requer a capacidade de estabelecer uma ligação websocket à infraestrutura Cloud Shell.
- **Resolução**: Verifique se configurou as definições da sua rede para permitir o envio de pedidos de https e pedidos de websocket para domínios em *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Detete a sua ligação Cloud Shell para suporte usando TLS 1.2
 - **Detalhes**: Para definir a versão do TLS para a sua ligação à Cloud Shell, tem de definir as definições específicas do navegador.
 - **Resolução**: Navegue para as definições de segurança do seu navegador e selecione a caixa de verificação ao lado de "Use TLS 1.2".

## <a name="bash-troubleshooting"></a>Resolução de problemas de bash

### <a name="cannot-run-the-docker-daemon"></a>Não pode dirigir o daemon estivador

- **Detalhes:** Cloud Shell utiliza um recipiente para alojar o seu ambiente de concha, como resultado de executar o daemon é proibido.
- **Resolução**: Utilize a [máquina de estivadores,](https://docs.docker.com/machine/overview/)instalada por defeito, para gerir os recipientes de estivadores a partir de um hospedeiro remoto Docker.

## <a name="powershell-troubleshooting"></a>Resolução de problemas da PowerShell

### <a name="gui-applications-are-not-supported"></a>As aplicações gui não são suportadas

- **Detalhes**: Se um utilizador lançar uma aplicação GUI, o pedido não devolve. Por exemplo, quando um clone um repo gitHub privado que tem dois fatores de autenticação ativado, é apresentada uma caixa de diálogo para completar a autenticação de dois fatores.
- **Resolução**: Feche e reabra a concha.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Resolução remota de problemas de VMs Azure
> [!NOTE]
> Os VMs Azure devem ter um endereço IP virado para o público.

- **Detalhes**: Devido às definições predefinidas do Windows Firewall para winRM, o utilizador pode ver o seguinte erro: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolução**: Executar `Enable-AzVMPSRemoting` para permitir que todos os aspetos da PowerShell remona máquina-alvo.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` não atualiza o resultado na unidade Azure

- **Detalhes**: Por padrão, para otimizar a experiência do utilizador, os resultados do `dir` estão em cache na unidade Azure.
- **Resolução**: Depois de criar, atualizar ou remover um recurso Azure, faça `dir -force` para atualizar os resultados na unidade Azure.

## <a name="general-limitations"></a>Limitações gerais

A Azure Cloud Shell tem as seguintes limitações conhecidas:

### <a name="quota-limitations"></a>Limitações de quotas

A Azure Cloud Shell tem um limite de 20 utilizadores simultâneos por inquilino por região. Se tentar abrir sessões mais simultâneas do que o limite, verá um erro de "Utilizador Inquilino sobre quota". Se tiver uma necessidade legítima de ter mais sessões abertas do que esta (por exemplo, para sessões de treino), contacte o suporte antes da sua utilização prevista para solicitar um aumento de quota.

A Cloud Shell é fornecida como um serviço gratuito e foi concebida para ser usada para configurar o seu ambiente Azure, e não como uma plataforma de computação para fins gerais. O uso automatizado excessivo pode ser considerado em violação dos Termos de Serviço Do Azure e pode levar ao bloqueio do acesso da Cloud Shell.

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

A máquina que fornece a sua sessão Cloud Shell é temporária e é reciclada após a sua sessão estar inativa durante 20 minutos. Cloud Shell requer que uma parte de ficheiro Azure seja montada. Como resultado, a sua subscrição deve ser capaz de configurar recursos de armazenamento para aceder à Cloud Shell. Outras considerações incluem:

- Com o armazenamento montado, apenas persistem modificações no interior do diretório `clouddrive`. Em Bash, o seu diretório `$HOME` também é persistente.
- As ações de ficheiros Azure só podem ser montadas a partir da [sua região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
  - Em Bash, corra `env` para encontrar a sua região definida como `ACC_LOCATION`.
- A Azure Files suporta apenas contas de armazenamento redundantes localmente e de armazenamento georedundant.

### <a name="browser-support"></a>Suporte ao navegador

Cloud Shell suporta as versões mais recentes dos seguintes navegadores:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - O Safari em modo privado não é suportado.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limites de utilização

Cloud Shell destina-se a casos de uso interativo. Como resultado, quaisquer sessões não interativas de longa duração terminam sem aviso prévio.

### <a name="user-permissions"></a>Permissões de utilizador

As permissões são definidas como utilizadores regulares sem acesso sudo. Qualquer instalação fora do seu diretório `$Home` não é persistiu.

## <a name="bash-limitations"></a>Limitações de bash

### <a name="editing-bashrc"></a>Edição .bashrc

Tenha cuidado ao editar .bashrc, fazê-lo pode causar erros inesperados na Cloud Shell.

## <a name="powershell-limitations"></a>Limitações powerShell

### <a name="preview-version-of-azuread-module"></a>Versão de pré-visualização do módulo AzureAD

Atualmente, `AzureAD.Standard.Preview`, uma versão de pré-visualização do módulo .NET Standard, está disponível. Este módulo fornece a mesma funcionalidade que `AzureAD`.

### <a name="sqlserver-module-functionality"></a>funcionalidade do módulo `SqlServer`

O módulo `SqlServer` incluído na Cloud Shell tem apenas suporte de pré-lançamento para PowerShell Core. Em particular, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Localização de ficheiro padrão quando criada a partir da unidade Azure

Utilizando cmdlets PowerShell, os utilizadores não podem criar ficheiros sob a unidade Azure. Quando os utilizadores criam novos ficheiros utilizando outras ferramentas, como o vim ou o nano, os ficheiros são guardados para o `$HOME` por padrão.

### <a name="tab-completion-can-throw-psreadline-exception"></a>A conclusão do separador pode lançar a exceção psReadline

Se o PsReadline EditMode do utilizador estiver definido para Emacs, o utilizador tenta apresentar todas as possibilidades através da conclusão do separador, e o tamanho da janela é demasiado pequeno para apresentar todas as possibilidades, a PSReadline lançará uma exceção não tratada.

### <a name="large-gap-after-displaying-progress-bar"></a>Grande lacuna após exibir barra de progresso

Se um comando ou ação do utilizador apresentar uma barra de progresso, tal separador que está a ser completado durante a unidade `Azure:`, então é possível que o cursor não esteja corretamente definido e apareça uma lacuna onde a barra de progresso estava anteriormente.

### <a name="random-characters-appear-inline"></a>Caracteres aleatórios aparecem inline

Os códigos de sequência de posição do cursor, por exemplo, `5;13R`, podem aparecer na entrada do utilizador. Os caracteres podem ser removidos manualmente.

## <a name="personal-data-in-cloud-shell"></a>Dados pessoais em Cloud Shell

A Azure Cloud Shell leva os seus dados pessoais a sério, os dados capturados e armazenados pelo serviço Azure Cloud Shell são utilizados para fornecer incumprimentos para a sua experiência, como a sua concha mais recentemente usada, tamanho de fonte preferido, tipo de letra preferido e detalhes de partilha de ficheiros que unidade de nuvem de volta. Caso deseje exportar ou eliminar estes dados, utilize as seguintes instruções.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportar
Para **exportar** as definições de utilizador, a Cloud Shell guarda para si, como concha preferida, tamanho de fonte e tipo de letra executar os seguintes comandos.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Executar os seguintes comandos em Bash ou PowerShell:

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
Para **eliminar** as definições do utilizador, a Cloud Shell guarda para si, como a concha preferida, o tamanho da fonte e o tipo de letra executam os seguintes comandos. Da próxima vez que começar a Cloud Shell, será convidado a embarcar novamente numa partilha de ficheiros. 

>[!Note]
> Se eliminar as definições do utilizador, a partilha real de Ficheiros Azure não será eliminada. Vá aos seus Ficheiros Azure para completar essa ação.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Executar os seguintes comandos em Bash ou PowerShell:

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
## <a name="azure-government-limitations"></a>Limitações do Governo de Azure
A Azure Cloud Shell no Governo de Azure só é acessível através do portal Azure.
