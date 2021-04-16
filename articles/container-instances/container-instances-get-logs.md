---
title: Obtenha registos de casos de contentores & eventos
description: Saiba como recuperar registos e eventos de contentores em instâncias de contentores do Azure para ajudar a resolver problemas com os contentores
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: b2b7ffb2cb4a7b1171afa42c2ef5a64b2bd928f8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379286"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Obter registos de contentor e eventos no Azure Container Instances

Quando tiver um recipiente mal comportado em Instâncias de Contentores Azure, comece por ver os seus registos com [troncos de contentores az][az-container-logs], e transmita o seu erro padrão com [o encaixe do recipiente Az][az-container-attach]. Também pode visualizar registos e eventos para casos de contentores no portal Azure, ou enviar dados de registo e evento para grupos de contentores para [registos do Azure Monitor](container-instances-log-analytics.md).

## <a name="view-logs"></a>Ver registos

Para visualizar registos do seu código de aplicação dentro de um recipiente, pode utilizar o comando [de registos de contentores az.][az-container-logs]

Segue-se a saída de registo do recipiente baseado em tarefas de exemplo em Definir a linha de comando num caso de [contentor,](container-instances-start-command.md#azure-cli-example)depois de ter fornecido um URL inválido utilizando uma sobreposição de linha de comando:

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

O comando [de anexação do recipiente az][az-container-attach] fornece informações de diagnóstico durante o arranque do recipiente. Uma vez iniciado o contentor, transmite STDOUT e STDERR para a consola local.

Por exemplo, aqui está a saída do recipiente baseado em tarefas em Definir a linha de [comando num caso de contentor,](container-instances-start-command.md#azure-cli-example)depois de ter fornecido um URL válido de um grande ficheiro de texto para processar:

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

Se o seu recipiente não conseguir ser implantado com sucesso, reveja as informações de diagnóstico fornecidas pelo fornecedor de recursos Azure Container Instances. Para ver os eventos para o seu recipiente, executar o comando [de demonstração de contentores az:][az-container-show]

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
Saiba como [resolver problemas de resolução de recipientes comuns e problemas de implantação](container-instances-troubleshooting.md) para instâncias de contentores Azure.

Saiba como enviar dados de registo e evento para grupos de contentores para [registos do Monitor Azure](container-instances-log-analytics.md).

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
