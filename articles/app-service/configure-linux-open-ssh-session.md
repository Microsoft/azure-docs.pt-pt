---
title: Acesso SSH para contentores Linux
description: Pode abrir uma sessão de SSH num contentor Linux no Azure App Service. Os recipientes Linux personalizados são suportados com algumas modificações à sua imagem personalizada.
keywords: serviço de aplicativos azure, web app, linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7178b8079bbb2411e0b38c3ef59a9981fb2d55be
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005244"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Abra uma sessão de SSH para um recipiente Linux no Azure App Service

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) é comumente utilizado para executar comandos administrativos remotamente a partir de um terminal de linha de comando. O Serviço de Aplicações no Linux fornece suporte SSH no recipiente da aplicação. 

![Serviço de aplicações Linux SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

Também pode ligar-se ao recipiente diretamente a partir da sua máquina de desenvolvimento local utilizando SSH e SFTP.

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Use suporte SSH com imagens personalizadas do Docker

Consulte [o SSH configurado num recipiente personalizado](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Sessão SSH aberta a partir de concha remota

> [!NOTE]
> Esta funcionalidade encontra-se atualmente em Pré-Visualização.
>

Utilizando o túnel TCP, pode criar uma ligação de rede entre a sua máquina de desenvolvimento e a Web App para contentores através de uma ligação WebSocket autenticada. Permite-lhe abrir uma sessão SSH com o seu contentor a funcionar no Serviço de Aplicações a partir do cliente à sua escolha.

Para começar, é necessário instalar o [Azure CLI](/cli/azure/install-azure-cli). Para ver como funciona sem instalar o Azure CLI, abra [a Azure Cloud Shell](../cloud-shell/overview.md). 

Abra uma ligação remota à sua aplicação utilizando o comando [de criação de ligação remota az webapp.](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) Especifique _\<subscription-id>_ , e _ para a sua _\<group-name>_ \_ \<app-name> aplicação.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` no final do comando é apenas por conveniência se estiver a usar a Cloud Shell. Executa o processo em segundo plano para que possa executar o próximo comando na mesma concha.

A saída do comando dá-lhe a informação necessária para abrir uma sessão de SSH.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Abra uma sessão de SSH com o seu recipiente com o cliente à sua escolha, utilizando a porta local. O exemplo a seguir utiliza o comando [ssh predefinido:](https://ss64.com/bash/ssh.html)

```bash
ssh root@127.0.0.1 -p <port>
```

Quando for solicitado, `yes` escreva para continuar a ligar. Em seguida, é solicitado para a senha. Use, `Docker!` que lhe foi mostrado anteriormente.

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

Uma vez autenticado, deverá ver o ecrã de boas-vindas da sessão.

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

Está agora ligado ao seu conector.  

Tente comandar o comando [superior.](https://ss64.com/bash/top.html) Deverá poder ver o processo da sua aplicação na lista de processos. No exemplo de saída abaixo, é o que `PID 263` tem.

<pre>
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
</pre>

## <a name="next-steps"></a>Passos seguintes

Pode publicar perguntas e preocupações no [fórum Azure.](/answers/topics/azure-webapps.html)

Para obter mais informações sobre a Web App para contentores, consulte:

* [Introduzindo depuração remota de aplicações de Node.js no Azure App Service a partir do Código VS](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Quickstart: Executar um recipiente personalizado no Serviço de Aplicações](quickstart-custom-container.md?pivots=container-linux)
* [Utilizar o Ruby no Serviço de Aplicações do Azure no Linux](quickstart-ruby.md)
* [FAQ sobre a Aplicação Web do Serviço de Aplicações do Azure para Contentores](faq-app-service-linux.md)
