---
title: Obtenha registos de instância de contentores & eventos
description: Saiba como recuperar registos e eventos de contentores em Casos de Contentores De Azure para ajudar a resolver problemas com problemas em contentores
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: 0991b9cb1f99606910dbdf2c87b111f67da6da7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250003"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Obter registos de contentor e eventos no Azure Container Instances

Quando tiver um recipiente de comportamento errado em Casos de Contentores Azure, comece por ver os seus troncos com troncos de [contentores az][az-container-logs]e transmita o seu erro padrão e padrão com a fixação do [recipiente Az][az-container-attach]. Também pode ver registos e eventos para casos de contentores no portal Azure, ou enviar dados de registo e evento sinuoso para grupos de contentores para [registos do Monitor Azure](container-instances-log-analytics.md).

## <a name="view-logs"></a>Ver registos

Para visualizar os registos do seu código de aplicação dentro de um recipiente, pode utilizar o comando de registos de [contentores az.][az-container-logs]

Segue-se a saída de registo do recipiente baseado em tarefas de exemplo em definir a linha de comando numa instância de [contentor,](container-instances-start-command.md#azure-cli-example)depois de ter fornecido um URL inválido utilizando uma sobreposição da linha de comando:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

## <a name="attach-output-streams"></a>Anexar fluxos de saída

O comando [az container attach][az-container-attach] fornece informações de diagnóstico durante o arranque do recipiente. Uma vez iniciado o contentor, transmite STDOUT e STDERR para a sua consola local.

Por exemplo, aqui está a saída do recipiente baseado em tarefas em definir a linha de comando numa instância de [contentor,](container-instances-start-command.md#azure-cli-example)depois de ter fornecido um URL válido de um grande ficheiro de texto para processar:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Obtenha eventos de diagnóstico

Se o seu recipiente não for acionado com sucesso, reveja as informações de diagnóstico fornecidas pelo fornecedor de recursos de instâncias de contentores Azure. Para ver os eventos do seu recipiente, execute o comando do [contentor az:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A saída inclui as propriedades centrais do seu recipiente, juntamente com eventos de implantação (mostrados aqui truncados):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```
## <a name="next-steps"></a>Passos seguintes
Aprenda a resolver problemas com problemas com [os problemas](container-instances-troubleshooting.md) com os problemas com os problemas com os casos de contentores e contentores do Azure.

Saiba como enviar dados de registo e evento saqueados para grupos de contentores para [registos do Monitor Azure](container-instances-log-analytics.md).

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show