---
title: Suporte de SSH para serviço de aplicativo no Linux – Azure | Microsoft Docs
description: Saiba como usar o SSH com o serviço de Azure App no Linux.
keywords: serviço de aplicativo do Azure, aplicativo Web, Linux, OSS
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fef8a17de4539a1427c269cdc512063d07df195c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066866"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Suporte de SSH para serviço de Azure App no Linux

O [Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) normalmente é usado para executar comandos administrativos remotamente de um terminal de linha de comando. O serviço de aplicativo no Linux fornece suporte a SSH no contêiner do aplicativo com cada uma das imagens internas do Docker usadas para a pilha de tempo de execução de novos aplicativos Web. 

![Pilhas de tempo de execução](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Para imagens personalizadas do Docker, configurando o servidor SSH em sua imagem personalizada.

Você também pode se conectar ao contêiner diretamente do computador de desenvolvimento local usando SSH e SFTP.

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Usar o suporte a SSH com imagens personalizadas do Docker

Consulte [Configurar o ssh em um contêiner personalizado](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Abrir sessão SSH no Shell remoto

> [!NOTE]
> Este recurso está atualmente em visualização.
>

Usando o túnel TCP, você pode criar uma conexão de rede entre seu computador de desenvolvimento e Aplicativo Web para Contêineres por uma conexão de WebSocket autenticada. Ele permite que você abra uma sessão SSH com seu contêiner em execução no serviço de aplicativo do cliente de sua escolha.

Para começar, você precisa instalar o [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Para ver como ele funciona sem instalar o CLI do Azure, abra [Azure cloud Shell](../../cloud-shell/overview.md). 

Abra uma conexão remota ao seu aplicativo usando o comando [AZ webapp Remote-Connection Create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) . \<Especifique \_  _\<a ID da assinatura >_ ,  _\<o nome do grupo >_ e o nome do aplicativo > _ para seu aplicativo.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&`no final do comando, você só será conveniente se estiver usando Cloud Shell. Ele executa o processo em segundo plano para que você possa executar o próximo comando no mesmo Shell.

A saída do comando fornece as informações necessárias para abrir uma sessão SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Abra uma sessão SSH com o seu contêiner com o cliente de sua escolha, usando a porta local. O exemplo a seguir usa o comando [SSH](https://ss64.com/bash/ssh.html) padrão:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Quando solicitado, digite `yes` para continuar a se conectar. Em seguida, você será solicitado a fornecer a senha. Use `Docker!`, que foi mostrado anteriormente.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Depois que você estiver autenticado, você deverá ver a tela de boas-vindas da sessão.

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

Agora você está conectado ao seu conector.  

Tente executar o comando [superior](https://ss64.com/bash/top.html) . Você deve ser capaz de ver o processo do aplicativo na lista de processos. Na saída de exemplo abaixo, trata-se de um `PID 263`com.

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

Você pode postar perguntas e preocupações no [Fórum do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Para obter mais informações sobre Aplicativo Web para Contêineres, consulte:

* [Introdução à depuração remota de aplicativos node. js no serviço Azure App de VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Como utilizar uma imagem personalizada do Docker para as Aplicações Web para Contentores](quickstart-docker-go.md)
* [Utilizar o .NET Core no Serviço de Aplicações do Azure no Linux](quickstart-dotnetcore.md)
* [Utilizar o Ruby no Serviço de Aplicações do Azure no Linux](quickstart-ruby.md)
* [FAQ sobre a Aplicação Web do Serviço de Aplicações do Azure para Contentores](app-service-linux-faq.md)
