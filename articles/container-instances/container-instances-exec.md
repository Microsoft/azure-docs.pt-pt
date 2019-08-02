---
title: Executar comandos em contêineres em execução em instâncias de contêiner do Azure
description: Saiba como executar um comando em um contêiner que está sendo executado atualmente nas instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: a8583cf605891631a2bce6914b24525aebd59ea0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325988"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Executar um comando em uma instância de contêiner do Azure em execução

As instâncias de contêiner do Azure dão suporte à execução de um comando em um contêiner em execução. A execução de um comando em um contêiner que você já iniciou é especialmente útil durante o desenvolvimento de aplicativos e a solução de problemas. O uso mais comum desse recurso é iniciar um shell interativo para que você possa depurar problemas em um contêiner em execução.

## <a name="run-a-command-with-azure-cli"></a>Executar um comando com CLI do Azure

Execute um comando em um contêiner em execução com [AZ container exec][az-container-exec] no [CLI do Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Por exemplo, para iniciar um shell bash em um contêiner Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Na saída de exemplo abaixo, o shell bash é iniciado em um contêiner do Linux em execução, fornecendo um terminal `ls` no qual é executado:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Neste exemplo, o prompt de comando é iniciado em um contêiner do beserver em execução:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Grupos com vários contentores

Se o [grupo](container-instances-container-groups.md) de contêineres tiver vários contêineres, como um contêiner de aplicativo e um sidecar de log, especifique o nome do contêiner no qual executar o `--container-name`comando.

Por exemplo, no grupo de contêineres *mynginx* são dois contêineres, *Nginx-app* e *logger*. Para iniciar um shell no contêiner *Nginx-app* :

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Restrições

Atualmente, as instâncias de contêiner do Azure dão suporte à inicialização de um único processo com [AZ container exec][az-container-exec], e você não pode passar argumentos de comando. Por exemplo, você não pode encadear comandos `sh -c "echo FOO && echo BAR"`como em ou `echo FOO`executar.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outras ferramentas de solução de problemas e problemas comuns de implantação em [solucionar problemas de contêiner e implantação em instâncias de contêiner do Azure](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure