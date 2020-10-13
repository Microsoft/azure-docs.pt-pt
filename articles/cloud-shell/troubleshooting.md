---
title: Azure Cloud Shell resolução de problemas Microsoft Docs
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
ms.openlocfilehash: eea64520dd5440467c911b6de42d8c8c31fc1bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543457"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Resolução de problemas & limitações da concha da nuvem de Azure

As resoluções conhecidas para resolver problemas em Azure Cloud Shell incluem:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Resolução geral de problemas

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Erro de execução de cmdlets AzureAD em PowerShell

- **Detalhes**: Quando executar cmdlets AzureAD como `Get-AzureADUser` em Cloud Shell, poderá ver um erro: `You must call the Connect-AzureAD cmdlet before calling any other cmdlets` . 
- **Resolução**: Executar o `Connect-AzureAD` cmdlet. Anteriormente, a Cloud Shell executou este cmdlet automaticamente durante a arranque da PowerShell. Para acelerar a hora de início, o cmdlet já não funciona automaticamente. Pode optar por restaurar o comportamento anterior adicionando `Connect-AzureAD` ao ficheiro $PROFILE no PowerShell.

### <a name="early-timeouts-in-firefox"></a>Intervalos iniciais no FireFox

- **Detalhes**: Cloud Shell utiliza um websocket aberto para passar entrada/saída para o seu navegador. O FireFox tem políticas pré-afinadas que podem fechar o websocket prematuramente causando intervalos antecipados em Cloud Shell.
- **Resolução**: Abra o FireFox e navegue para "about:config" na caixa URL. Procure por "network.websocket.timeout.ping.request" e altere o valor de 0 para 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Desativar a Cloud Shell num ambiente de rede bloqueado

- **Detalhes**: Os administradores podem desejar desativar o acesso à Cloud Shell para os seus utilizadores. A Cloud Shell utiliza o acesso ao domínio, o `ux.console.azure.com` que pode ser negado, impedindo qualquer acesso aos pontos de entrada da Cloud Shell, incluindo portal.azure.com, shell.azure.com, extensão de Conta Azure code do estúdio visual e docs.microsoft.com. Na nuvem do Governo dos EUA, o ponto de entrada é `ux.console.azure.us` ; não há shell.azure.us correspondente.
- **Resolução**: Restringir o acesso `ux.console.azure.com` ou através de `ux.console.azure.us` configurações de rede ao seu ambiente. O ícone Cloud Shell ainda existirá no portal Azure, mas não será conectado com sucesso ao serviço.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Diálogo de armazenamento - Erro: 403 RequestDisallowedByPolicy

- **Detalhes**: Ao criar uma conta de armazenamento através da Cloud Shell, não é bem sucedida devido a uma atribuição da Política Azure colocada pelo seu administrador. A mensagem de erro incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: Contacte o seu administrador Azure para remover ou atualizar a atribuição da Política Azure negando a criação de armazenamento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Diálogo de armazenamento - Erro: 400 Operações Não Permitidas

- **Detalhes**: Ao utilizar uma subscrição do Azure Ative Directory, não é possível criar armazenamento.
- **Resolução**: Utilize uma assinatura Azure capaz de criar recursos de armazenamento. As subscrições AD do Azure não são capazes de criar recursos Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Saída do terminal - Erro: Falha na ligação do terminal: o websocket não pode ser estabelecido. Pressione `Enter` para voltar a ligar.
- **Detalhes**: Cloud Shell requer a capacidade de estabelecer uma ligação websocket à infraestrutura Cloud Shell.
- **Resolução**: Verifique se configura as definições da sua rede para permitir o envio de pedidos de https e pedidos de websocket para domínios em *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Desave a sua ligação Cloud Shell para suportar utilizando o TLS 1.2
 - **Detalhes**: Para definir a versão do TLS para a sua ligação à Cloud Shell, tem de definir as definições específicas do navegador.
 - **Resolução**: Navegue nas definições de segurança do seu navegador e selecione a caixa de verificação ao lado de "Use TLS 1.2".

## <a name="bash-troubleshooting"></a>Resolução de problemas de bash

### <a name="cannot-run-the-docker-daemon"></a>Não pode dirigir o daemon estivador

- **Detalhes**: A Cloud Shell utiliza um recipiente para hospedar o seu ambiente de concha, como resultado de executar o daemon é proibido.
- **Resolução**: Utilize [a máquina de estivar,](https://docs.docker.com/machine/overview/)que é instalada por defeito, para gerir os recipientes de estivadores a partir de um anfitrião estivador remoto.

## <a name="powershell-troubleshooting"></a>Resolução de problemas powerShell

### <a name="gui-applications-are-not-supported"></a>As aplicações gui não são suportadas

- **Detalhes**: Se um utilizador lançar uma aplicação GUI, a solicitação não volta. Por exemplo, quando se clona um repo GitHub privado que tem dois fatores de autenticação ativado, é apresentada uma caixa de diálogo para completar a autenticação de dois fatores.
- **Resolução:** Feche e reabra a concha.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Resolução de problemas da gestão remota dos VMs Azure
> [!NOTE]
> Os VMs Azure devem ter um endereço IP virado para o público.

- **Detalhes**: Devido às definições padrão de Firewall do Windows para WinRM, o utilizador pode ver o seguinte erro: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolução**: Corra `Enable-AzVMPSRemoting` para permitir que todos os aspetos da powerShell se remoam na máquina alvo.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` não atualizar o resultado em unidade Azure

- **Detalhes**: Por padrão, para otimizar para a experiência do utilizador, os resultados `dir` são em cache na unidade Azure.
- **Resolução**: Depois de criar, atualizar ou remover um recurso Azure, corra `dir -force` para atualizar os resultados na unidade Azure.

## <a name="general-limitations"></a>Limitações gerais

A Azure Cloud Shell tem as seguintes limitações conhecidas:

### <a name="quota-limitations"></a>Limitações de quotas

A Azure Cloud Shell tem um limite de 20 utilizadores simultâneos por inquilino por região. Se tentar abrir sessões mais simultâneas do que o limite, verá um erro de "Utilizador inquilino sobre quota". Se tiver uma necessidade legítima de ter mais sessões abertas do que esta (por exemplo, para sessões de treino), contacte o suporte antes da sua utilização antecipada para solicitar um aumento de quota.

A Cloud Shell é fornecida como um serviço gratuito e foi concebida para ser usada para configurar o seu ambiente Azure, não como uma plataforma de computação para fins gerais. Uma utilização automatizada excessiva pode ser considerada uma violação dos Termos de Serviço Azure e pode levar ao bloqueio do acesso da Cloud Shell.

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

A máquina que fornece a sua sessão Cloud Shell é temporária e é reciclada após a sessão estar inativa durante 20 minutos. A Cloud Shell requer que seja montada uma partilha de ficheiros Azure. Como resultado, a sua subscrição deve ser capaz de configurar recursos de armazenamento para aceder à Cloud Shell. Outras considerações incluem:

- Com o armazenamento montado, apenas persistem modificações dentro do `clouddrive` diretório. Em Bash, o seu `$HOME` diretório também é persistido.
- As ações de ficheiros Azure só podem ser montadas dentro da [região afetada.](persisting-shell-storage.md#mount-a-new-clouddrive)
  - Em Bash, corra `env` para encontrar a sua região definida como `ACC_LOCATION` .
- A Azure Files suporta apenas contas de armazenamento redundantes locais e geo-redundantes.

### <a name="browser-support"></a>Browser support (Suporte do browser)

A Cloud Shell suporta as versões mais recentes dos seguintes navegadores:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - O Safari em modo privado não é suportado.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limites de utilização

A Cloud Shell destina-se a casos de utilização interativa. Como resultado, quaisquer sessões não interativas de longa duração terminam sem aviso prévio.

### <a name="user-permissions"></a>Permissões de utilizador

As permissões são definidas como utilizadores regulares sem acesso de sudo. Qualquer instalação fora do seu `$Home` diretório não é persistiu.

## <a name="bash-limitations"></a>Limitações de bash

### <a name="editing-bashrc"></a>Edição .bashrc

Tenha cuidado ao editar .bashrc, ao fazê-lo pode causar erros inesperados na Cloud Shell.

## <a name="powershell-limitations"></a>Limitações PowerShell

### <a name="preview-version-of-azuread-module"></a>Versão de pré-visualização do módulo AzureAD

Atualmente, `AzureAD.Standard.Preview` está disponível uma versão de pré-visualização do módulo .NET Standard. Este módulo fornece a mesma funcionalidade que `AzureAD` .

## <a name="personal-data-in-cloud-shell"></a>Dados pessoais em Cloud Shell

A Azure Cloud Shell leva os seus dados pessoais a sério, os dados capturados e armazenados pelo serviço Azure Cloud Shell são utilizados para fornecer incumprimentos para a sua experiência, como a sua concha mais recentemente usada, tamanho de fonte preferido, tipo de letra preferido, e detalhes de partilha de ficheiros que a unidade de nuvem traseira. Se desejar exportar ou eliminar estes dados, utilize as seguintes instruções.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportar
Para **exportar** as definições do utilizador, a Cloud Shell guarda para si, tal como a concha preferida, o tamanho do tipo de fonte e o tipo de letra executam os seguintes comandos.

1. [![Imagem mostrando um botão marcado Launch Azure Cloud Shell.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

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
Para **eliminar** as definições do utilizador, a Cloud Shell guarda para si, tal como a concha preferida, o tamanho do tipo de letra e o tipo de letra executam os seguintes comandos. Da próxima vez que iniciar a Cloud Shell, será solicitado a embarcar novamente numa partilha de ficheiros. 

>[!Note]
> Se eliminar as definições do utilizador, a partilha de Ficheiros Azure real não será eliminada. Vá aos seus Ficheiros Azure para completar essa ação.

1. [![Imagem mostrando um botão marcado Launch Azure Cloud Shell.](https://shell.azure.com/images/launchcloudshell.png)](https://shell.azure.com)

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
AZure Cloud Shell no Governo de Azure só é acessível através do portal Azure.

>[!Note]
> Atualmente, não é suportada a ligação a GCC-High ou Nuvens do DoD do Governo para o Exchange Online.
