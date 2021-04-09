---
title: Ponto de entrada de substituição em caso de contentor
description: Desloque uma linha de comando para anular o ponto de entrada numa imagem de contentor quando colocar uma instância de contentor Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 23221de3dc91c37c2e6fb96489539d3954efcd87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169634"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Definir a linha de comando numa instância de contentor para anular a operação da linha de comando predefinido

Quando criar uma instância de recipiente, especifique opcionalmente um comando para anular a instrução da linha de comando predefinido cozida na imagem do recipiente. Este comportamento é semelhante ao `--entrypoint` argumento da linha de comando para `docker run` .

Tal como a definição de [variáveis ambientais](container-instances-environment-variables.md) para casos de contentores, especificar uma linha de comando inicial é útil para trabalhos de lote onde é necessário preparar cada recipiente dinamicamente com uma configuração específica de tarefas.

## <a name="command-line-guidelines"></a>Diretrizes da linha de comando

* Por predefinição, a linha de comando especifica um *único processo que começa sem uma concha* no recipiente. Por exemplo, a linha de comando pode executar um script Python ou ficheiro executável. O processo pode especificar parâmetros ou argumentos adicionais.

* Para executar vários comandos, inicie a sua linha de comando definindo um ambiente de concha que é suportado no sistema operativo do contentor. Exemplos:

  |Sistema operativo  |Concha padrão  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpino     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Siga as convenções da concha para combinar vários comandos para correr em sequência.

* Dependendo da configuração do recipiente, poderá ser necessário definir um caminho completo para a linha de comando executável ou argumentos.

* Desacorde uma política de [reinício](container-instances-restart-policy.md) adequada para a instância do contentor, dependendo se a linha de comando especifica uma tarefa de longo prazo ou uma tarefa de execução. Por exemplo, uma política de reinício `Never` de ou é recomendada para uma tarefa de `OnFailure` execução. 

* Se precisar de informações sobre o ponto de entrada predefinido numa imagem de recipiente, utilize o comando de inspeção de [imagem do estivador.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Sintaxe da linha de comando

A sintaxe da linha de comando varia consoante a API ou a ferramenta Azure utilizada para criar as instâncias. Se especificar um ambiente de concha, também observe as convenções de sintaxe de comando da concha.

* [recipiente az criar][az-container-create] comando: Passe uma corda com o `--command-line` parâmetro. Exemplo: `--command-line "python myscript.py arg1 arg2"` ).

* [Grupo New-AzureRmContainer][new-azurermcontainergroup] Cmdlet Azure PowerShell: Passe uma corda com o `-Command` parâmetro. Exemplo: `-Command "echo hello"`.

* Portal Azure: No **Comando sobrepõe-se** à propriedade da configuração do contentor, forneça uma lista de cordas separadas por vírgulas, sem cotações. Exemplo: `python, myscript.py, arg1, arg2` ). 

* Modelo de gestor de recursos ou ficheiro YAML, ou um dos SDKs Azure: Especifique a propriedade da linha de comando como uma matriz de cordas. Exemplo: a matriz JSON `["python", "myscript.py", "arg1", "arg2"]` num modelo de Gestor de Recursos. 

  Se estiver familiarizado com a sintaxe [do Dockerfile,](https://docs.docker.com/engine/reference/builder/) este formato é semelhante à forma *executiva* da instrução CMD.

### <a name="examples"></a>Exemplos

|    |  CLI do Azure   | Portal | Modelo | 
| ---- | ---- | --- | --- |
| **Comando único** | `--command-line "python myscript.py arg1 arg2"` | **Sobreposição de comando:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| **Vários comandos** | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Sobreposição de comando:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exemplo de Azure CLI

Como exemplo, modifique o comportamento da imagem do recipiente [microsoft/aci-wordcount,][aci-wordcount] que analisa o texto em Shakespeare's *Hamlet* para encontrar as palavras mais frequentes. Em vez de analisar *Hamlet,* pode definir uma linha de comando que aponta para uma fonte de texto diferente.

Para ver a saída do recipiente [microsoft/aci-wordcount][aci-wordcount] quando analisar o texto predefinido, execute-o com o [seguinte comando de criação de recipiente az.][az-container-create] Não é especificada nenhuma linha de comando de arranque, pelo que o comando do contentor predefinido funciona. Para fins de ilustração, este exemplo define [variáveis ambientais](container-instances-environment-variables.md) para encontrar as 3 palavras mais importantes que têm pelo menos cinco letras de comprimento:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Uma vez que o estado do recipiente apareça como *Terminado* (use [az container show][az-container-show] para verificar o estado), apresente o tronco com [registos de contentores az][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Resultado:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Agora, crie um segundo exemplo para analisar texto diferente especificando uma linha de comando diferente. O script Python executado pelo recipiente, *wordcount.py,* aceita um URL como argumento, e processa o conteúdo dessa página em vez do padrão.

Por exemplo, para determinar as 3 palavras mais importantes que têm pelo menos cinco letras de comprimento em *Romeu e Julieta:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Mais uma vez, uma vez *terminado* o contentor, veja a saída mostrando os troncos do contentor:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Resultado:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas, como o processamento de um grande conjunto de dados com vários contentores, podem beneficiar de linhas de comando personalizadas em tempo de execução. Para obter mais informações sobre a execução de recipientes baseados em tarefas, consulte [executar tarefas contentorizadas com políticas de reinício](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
