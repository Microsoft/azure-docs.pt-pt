---
title: Executar comandos em contentores em execução no Azure Container Instances
description: Saiba como executar um comando num contentor que está atualmente em execução no Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: 577e2386c352798bc21a2c78b22726128ac7cf0a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60579751"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Executar um comando numa instância de contentor do Azure em execução

O Azure Container Instances suporta a execução de um comando num contentor em execução. Executar um comando num contêiner que já tiver começado é especialmente útil durante o desenvolvimento de aplicativos e a resolução de problemas. O uso mais comum desse recurso é iniciar um shell interativo para que pode depurar problemas num contentor em execução.

## <a name="run-a-command-with-azure-cli"></a>Executar um comando com a CLI do Azure

Executar um comando num contentor em execução com [exec de contentor az] [ az-container-exec] no [da CLI do Azure][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Por exemplo, para iniciar um shell de Bash no contentor de Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Na saída do exemplo abaixo, o shell de Bash é iniciado num contentor do Linux em execução, fornecendo um terminal em que `ls` é executado:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Neste exemplo, o Prompt de comando é iniciado num contentor Nanoserver em execução:

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

Se sua [grupo de contentores](container-instances-container-groups.md) tem vários contentores, como um contêiner de aplicativo e um sidecar de registo, especifique o nome do contentor no qual pretende executar o comando com `--container-name`.

Por exemplo, no grupo de contentores *mynginx* são dois contentores, *nginx-app* e *logger*. Para iniciar um shell no *nginx aplicação* contentor:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Restrições

O Azure Container Instances suporta atualmente a iniciar um processo único com [exec de contentor az][az-container-exec], e não poder passar os argumentos de comando. Por exemplo, não é possível encadear comandos como na `sh -c "echo FOO && echo BAR"`, ou executar `echo FOO`.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outras ferramentas de resolução de problemas e os problemas comuns de implantação no [resolver problemas de implantação e de contentor no Azure Container Instances](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure