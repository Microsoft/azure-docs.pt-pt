---
title: Executar comandos em execução de caso de contentor
description: Saiba como executar um comando num contentor que está atualmente a funcionar em Instâncias de Contentores Azure
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: de48e6ac246e2b0751561b4c60bb63d88b599bdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "79247205"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Execute um comando em uma instância de contentor Azure em execução

O Azure Container Instances suporta a execução de um comando num contentor em execução. Executar um comando num contentor que já iniciou é especialmente útil durante o desenvolvimento e resolução de problemas de aplicações. É comum esta funcionalidade ser utilizada para iniciar um shell interativo para que seja possível depurar problemas num contentor em execução.

## <a name="run-a-command-with-azure-cli"></a>Executar um comando com Azure CLI

Execute um comando num recipiente de funcionamento com [um executivo de contentores az][az-container-exec] no [Azure CLI:][azure-cli]

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Por exemplo, para lançar uma concha Bash num recipiente Nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Na saída de exemplo abaixo, a concha Bash é lançada num recipiente Linux em funcionamento, fornecendo um terminal no qual `ls` é executado:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Neste exemplo, o Comando Prompt é lançado num recipiente nanoserver em funcionamento:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
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

Se o seu [grupo de contentores](container-instances-container-groups.md) tiver vários contentores, tais como um recipiente de aplicação e um sidecar de madeira, especifique o nome do recipiente para executar o comando com `--container-name` .

Por exemplo, no grupo de contentores *mynginx* encontram-se dois recipientes, *nginx-app* e *madeir.* Para lançar uma concha no recipiente *nginx-app:*

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Restrições

A azure Container Instances suporta atualmente o lançamento de um único processo com [o executivo de contentores az][az-container-exec], e não é possível passar argumentos de comando. Por exemplo, não é possível acorrentar comandos como em `sh -c "echo FOO && echo BAR"` , ou executar `echo FOO` .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outras ferramentas de resolução de problemas e problemas comuns de implantação em [contentores de resolução de problemas e problemas de implantação em instâncias de contentores Azure](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure