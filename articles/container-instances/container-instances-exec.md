---
title: Executar comandos em instância de contentor de corrida
description: Saiba como executar um comando num contentor que está atualmente em funcionamento em Instâncias de Contentores Azure
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: de48e6ac246e2b0751561b4c60bb63d88b599bdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247205"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Execute um comando num contentor Azure em execução

O Azure Container Instances suporta a execução de um comando num contentor em execução. Executar um comando num contentor que já iniciou é especialmente útil durante o desenvolvimento e resolução de problemas de aplicações. É comum esta funcionalidade ser utilizada para iniciar um shell interativo para que seja possível depurar problemas num contentor em execução.

## <a name="run-a-command-with-azure-cli"></a>Executar um comando com Azure CLI

Executar um comando num recipiente de corrida com um executivo de [contentora az][az-container-exec] no [ClI Azure:][azure-cli]

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Por exemplo, lançar uma concha bash num recipiente nginx:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Na saída de exemplo abaixo, a concha Bash é lançada num recipiente `ls` Linux em execução, fornecendo um terminal no qual é executado:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Neste exemplo, o Comando Prompt é lançado num recipiente Nanoserver em execução:

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

Se o seu grupo de [contentores](container-instances-container-groups.md) tiver vários contentores, como um recipiente de aplicação `--container-name`e um sidecar de madeira, especifique o nome do recipiente para executar o comando com .

Por exemplo, no grupo de contentores *mynginx* estão dois contentores, *nginx-app* e *madeireiros*. Para lançar uma concha no recipiente *de aplicações nginx:*

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Restrições

Atualmente, a Azure Container Instances suporta o lançamento de um único processo com o executivo de [contentores az][az-container-exec], e não pode passar argumentos de comando. Por exemplo, não é possível correntes de comandos como dentro `sh -c "echo FOO && echo BAR"`ou executar `echo FOO`.

## <a name="next-steps"></a>Passos seguintes

Conheça outras ferramentas de resolução de problemas e problemas comuns de implantação em problemas de [contentores e implementação em casos de contentores de Azure](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure