---
title: Acesso SSH para contentores Linux
description: Pode abrir uma sessão sSH a um contentor Linux no Azure App Service. Os recipientes Linux personalizados são suportados com algumas modificações na sua imagem personalizada.
keywords: serviço de aplicativos azure, web app, linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 3173fe010106963b9079bf151c92957735253e84
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898766"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Suporte sSH para serviço de aplicações Azure em Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) é comumente usado para executar comandos administrativos remotamente a partir de um terminal de linha de comando. O Serviço de Aplicações no Linux fornece suporte SSH para o recipiente de aplicações. 

![Serviço de aplicativos Linux SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh.png)

Também pode ligar-se ao recipiente diretamente da sua máquina de desenvolvimento local utilizando SSH e SFTP.

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Use suporte SSH com imagens personalizadas do Docker

Consulte [o Configure SSH num recipiente personalizado](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Abra a sessão SSH a partir de uma concha remota

> [!NOTE]
> Esta funcionalidade encontra-se atualmente em Pré-visualização.
>

Utilizando o túnel TCP, pode criar uma ligação de rede entre a sua máquina de desenvolvimento e a Web App para contentores através de uma ligação WebSocket autenticada. Permite-lhe abrir uma sessão de SSH com o seu contentor a funcionar no Serviço de Aplicações a partir do cliente à sua escolha.

Para começar, é necessário instalar [o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Para ver como funciona sem instalar o Azure CLI, abra a [Azure Cloud Shell.](../../cloud-shell/overview.md) 

Abra uma ligação remota à sua aplicação utilizando o comando de [criação de ligação remota az webapp.](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) Especifique _\<subscrição-id>_ _\<nome de grupo>_ e \_\<nome de aplicação>_ para a sua aplicação.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` no final do comando é apenas por conveniência se estiver a usar cloud Shell. Executa o processo em segundo plano para que possa executar o próximo comando na mesma concha.

A saída de comando dá-lhe a informação necessária para abrir uma sessão de SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Abra uma sessão de SSH com o seu recipiente com o cliente à sua escolha, utilizando o porto local. O exemplo seguinte utiliza o comando [ssh](https://ss64.com/bash/ssh.html) predefinido:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Quando for solicitado, escreva `yes` para continuar a ligar. Em seguida, é-lhe solicitada a senha. Use `Docker!`, que foi mostrado anteriormente.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Uma vez autenticado, deve ver o ecrã de boas-vindas da sessão.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Está agora ligado ao seu conector.  

Tente mover o comando [superior.](https://ss64.com/bash/top.html) Deverá poder ver o processo da sua aplicação na lista de processos. Na saída de exemplo abaixo, é a que tem `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Passos seguintes

Pode publicar perguntas e preocupações no [fórum Azure.](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)

Para mais informações sobre a Web App para contentores, consulte:

* [Introdução de depuração remota de aplicações nonóio no Serviço de Aplicações Azure a partir do Código VS](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Como utilizar uma imagem personalizada do Docker para as Aplicações Web para Contentores](quickstart-docker-go.md)
* [Utilizar o .NET Core no Serviço de Aplicações do Azure no Linux](quickstart-dotnetcore.md)
* [Utilizar o Ruby no Serviço de Aplicações do Azure no Linux](quickstart-ruby.md)
* [FAQ sobre a Aplicação Web do Serviço de Aplicações do Azure para Contentores](app-service-linux-faq.md)
