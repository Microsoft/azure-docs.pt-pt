---
title: Suporte SSH para o serviço de aplicações no Linux – Azure | Documentos da Microsoft
description: Saiba mais sobre a utilização do SSH com o serviço de aplicações do Azure no Linux.
keywords: serviço de aplicações do Azure, aplicação web, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 18d10afc9132c81c2dcfbb1aa17ded81a21336ca
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780033"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Suporte SSH para o serviço de aplicações do Azure no Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) costuma ser utilizada para executar comandos administrativos remotamente a partir de um terminal da linha de comandos. Serviço de aplicações no Linux fornece suporte SSH para o contentor de aplicações, com cada uma das imagens do Docker incorporadas, utilizadas para a pilha de tempo de execução de novas aplicações web. 

![Pilhas de Runtime](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Personalizado para imagens do Docker, ao configurar o servidor SSH na sua imagem personalizada.

Também pode ligar para o contentor diretamente a partir de sua máquina de desenvolvimento local através de SSH e SFTP.

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Utilizar o suporte SSH com imagens personalizadas do Docker

Ver [configurar o SSH num contentor personalizado](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Abrir sessão SSH a partir da shell remoto

> [!NOTE]
> Esta funcionalidade está atualmente em pré-visualização.
>

Através de TCP de túnel, podem criar uma ligação de rede entre o computador de desenvolvimento e a aplicação Web para contentores através de uma ligação autenticada do WebSocket. Permite-lhe abrir uma sessão SSH com o seu contentor em execução no serviço de aplicações do cliente à sua escolha.

Para começar, tem de instalar [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Para ver como ele funciona sem instalar a CLI do Azure, abra [Azure Cloud Shell](../../cloud-shell/overview.md). 

Abrir uma ligação remota à sua aplicação com o [az webapp remoto-ligação criar](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) comando. Especifique  _\<id da subscrição >_,  _\<grupo-name >_ e \__ < nome da aplicação > para a sua aplicação.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` no final do comando é apenas para conveniência se estiver a utilizar o Cloud Shell. Ele é executado o processo em segundo plano para que possam executar o comando seguinte na shell do mesmo.

A saída do comando dá-lhe as informações necessárias abrir uma sessão SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Abra uma sessão SSH com o contentor com o cliente da sua preferência, com a porta local. O exemplo seguinte utiliza a predefinição [ssh](https://ss64.com/bash/ssh.html) comando:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Quando a ser solicitado, digite `yes` para continuar a ligar. Em seguida, é-lhe pedido para a palavra-passe. Utilize `Docker!`, que foi mostrado anteriormente para.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Quando estiver autenticado, deverá ver o ecrã de boas-vindas da sessão.

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

Está agora ligado ao conector.  

Tente executar o [superior](https://ss64.com/bash/top.html) comando. Deve ser capaz de ver o processo da sua aplicação na lista de processos. O resultado de exemplo abaixo, é aquele com `PID 263`.

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

## <a name="next-steps"></a>Passos Seguintes

Pode postar perguntas e questões sobre o [fórum do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Para obter mais informações sobre a aplicação Web para contentores, consulte:

* [Apresentando a depuração remota de aplicações node. js no serviço de aplicações do Azure do VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Como utilizar uma imagem personalizada do Docker para as Aplicações Web para Contentores](quickstart-docker-go.md)
* [Utilizar o .NET Core no Serviço de Aplicações do Azure no Linux](quickstart-dotnetcore.md)
* [Utilizar o Ruby no Serviço de Aplicações do Azure no Linux](quickstart-ruby.md)
* [FAQ sobre a Aplicação Web do Serviço de Aplicações do Azure para Contentores](app-service-linux-faq.md)
