---
title: Utilizar uma linha de comando inicial no Azure Container Instances
description: Substituir o ponto de entrada configurado numa imagem de contentor ao implementar uma instância de contentor do Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: cbe14066cfd7493806176e834373e952daf19339
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610260"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Definir a linha de comandos numa instância de contentor para substituir a operação de linha de comandos padrão

Quando cria uma instância de contentor, opcionalmente, especifica um comando para substituir a instrução de linha de comandos padrão embutida na imagem de contentor. Este comportamento é semelhante para o `--entrypoint` argumento da linha de comandos para `docker run`.

Como a definição [variáveis de ambiente](container-instances-environment-variables.md) para instâncias de contentor, especificar uma linha de comando inicial é útil para tarefas do batch em que precisa preparar cada contentor dinamicamente com a configuração de tarefa específica.

## <a name="command-line-guidelines"></a>Diretrizes de linha de comandos

* Por predefinição, a linha de comando Especifica um *único processo que é iniciado sem um shell* no contentor. Por exemplo, a linha de comandos pode executar um script de Python ou arquivo executável. 

* Para executar vários comandos, comece a sua linha de comandos ao definir um ambiente do shell no sistema de operativo contentor (exemplos: `bin/sh`, `/bin/bash`, `cmd`). Siga as convenções de shell para combinar vários comandos para executar na sequência.

* Dependendo da configuração de contentor, poderá ter de definir um caminho completo para o executável da linha de comando ou os argumentos.

* Definir apropriadas [política de reinício](container-instances-restart-policy.md) para a instância de contentor, dependendo se a linha de comando Especifica uma tarefa de execução longa ou uma tarefa de execução única. Por exemplo, uma política de reinício do `Never` ou `OnFailure` é recomendada para uma tarefa de execução única. 

* Se precisar de informações sobre o ponto de entrada padrão definido numa imagem de contentor, utilize o [inspecionar a imagem do docker](https://docs.docker.com/engine/reference/commandline/image_inspect/) comando.

## <a name="command-line-syntax"></a>Sintaxe de linha de comandos

A sintaxe de linha de comandos varia consoante a API do Azure ou a ferramenta usada para criar as instâncias. Se especificar um ambiente de shell, também observe as convenções de sintaxe de comando do shell.

* [az container create][az-container-create] command: Passar uma cadeia de caracteres com o `--command-line` parâmetro. Exemplo: `--command-line "python myscript.py arg1 arg2"`).

* [Novo-AzureRmContainerGroup] [ new-azurermcontainergroup] cmdlet do PowerShell do Azure: Passar uma cadeia de caracteres com o `-Command` parâmetro. Exemplo: `-Command "echo hello"`.

* Portal do Azure: Na **substituição de comando** propriedade da configuração do contentor, fornecer uma lista separada por vírgulas de cadeias de caracteres, sem aspas. Exemplo: `python, myscript.py, arg1, arg2`). 

* Modelo do Resource Manager ou ficheiro YAML ou um dos SDKs do Azure: Especifique a propriedade de linha de comandos como uma matriz de cadeias de caracteres. Exemplo: a matriz JSON `["python", "myscript.py", "arg1", "arg2"]` num modelo do Resource Manager. 

  Se estiver familiarizado com o [Dockerfile](https://docs.docker.com/engine/reference/builder/) sintaxe, esse formato é semelhante a *exec* formulário da instrução CMD.

### <a name="examples"></a>Exemplos

|    |  CLI do Azure   | Portal | Modelo | 
| ---- | ---- | --- | --- |
| Único comando | `--command-line "python myscript.py arg1 arg2"` | **Substituição de comando**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Múltiplos comandos | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Substituição de comando**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exemplo da CLI do Azure

Por exemplo, modificar o comportamento do [aci/microsoft-wordcount] [ aci-wordcount] imagem de contentor, que analisa o texto de Shakespeare *Mítico* para localizar com mais frequência ocorrendo palavras. Em vez de analisar *Mítico*, é possível definir uma linha de comando que aponta para uma origem de texto diferente.

Para ver o resultado do [aci/microsoft-wordcount] [ aci-wordcount] contentor ao analisar o texto predefinido, executá-la com seguintes [criar contentor de az] [ az-container-create] comando. Nenhuma linha de comando de início é especificada, pelo que o comando de contentor predefinido é executado. Para efeitos de ilustração, este exemplo define [variáveis de ambiente](container-instances-environment-variables.md) para encontrar as palavras de 3 principais que são letras, pelo menos, cinco longas:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Assim que é apresentado como estado do contentor *Terminated* (utilizar [show de contentor az] [ az-container-show] para verificar o estado), apresentar o registo com [registos de contentor az] [ az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Saída:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Configure agora um segundo contentor de exemplo para analisar texto diferente ao especificar uma linha de comandos diferente. O script de Python executado pelo contêiner, *wordcount.py*, aceita um URL como um argumento e processa o conteúdo dessa página em vez da predefinição.

Por exemplo, para determinar a parte superior 3 palavras que são, pelo menos, cinco há muito tempo em letras *Romeu e Julieta*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Novamente, assim que o contentor estiver *Terminated*, ver o resultado, mostrando os registos do contentor:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Saída:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passos Seguintes

Cenários de baseado em tarefas, como um grande conjunto de dados com vários contentores, de processamento em lote podem beneficiar de linhas de comando personalizadas em tempo de execução. Para obter mais informações sobre a execução de contentores com base em tarefas, consulte [executar tarefas em contentores com políticas de reinício](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
