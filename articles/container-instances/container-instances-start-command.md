---
title: Anular o ponto de entrada em instância de contentor
description: Delineie uma linha de comando para sobrepor o ponto de entrada numa imagem de contentor quando implementar uma instância de contentor Estoque Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247127"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Delineie a linha de comando numa instância de contentorpara anular o funcionamento da linha de comando padrão

Quando criar uma instância de contentor, especifique opcionalmente um comando para anular as instruções de linha de comando predefinidas cozidas na imagem do recipiente. Este comportamento é `--entrypoint` semelhante ao argumento `docker run`da linha de comando para .

Tal como definir [variáveis ambientais](container-instances-environment-variables.md) para instâncias de contentores, especificar uma linha de comando inicial é útil para trabalhos de lote onde você precisa preparar cada recipiente dinamicamente com configuração específica da tarefa.

## <a name="command-line-guidelines"></a>Diretrizes da linha de comando

* Por defeito, a linha de comando especifica um *único processo que começa sem uma concha* no recipiente. Por exemplo, a linha de comando pode executar um script Python ou um ficheiro executável. O processo pode especificar parâmetros ou argumentos adicionais.

* Para executar vários comandos, inicie a sua linha de comando definindo um ambiente de concha que é suportado no sistema operativo do contentor. Exemplos:

  |Sistema operativo  |Concha padrão  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpino     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Siga as convenções da concha para combinar vários comandos para correr em sequência.

* Dependendo da configuração do recipiente, poderá ser necessário definir um caminho completo para a linha de comando executável ou argumentos.

* Estabeleça uma política de [reinício](container-instances-restart-policy.md) adequada para a instância do contentor, dependendo se a linha de comando especifica uma tarefa de longo prazo ou uma tarefa de execução. Por exemplo, uma `Never` política `OnFailure` de reinício ou recomenda-se para uma tarefa de execução. 

* Se precisar de informações sobre o ponto de entrada predefinido numa imagem de recipiente, utilize o comando de inspeção de [imagem do estivador.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Sintaxe da linha de comando

A sintaxe da linha de comando varia consoante a API Azure ou a ferramenta utilizada para criar as instâncias. Se especificar um ambiente de concha, observe também as convenções de sintaxe de comando da concha.

* [az recipiente criar][az-container-create] comando: Passe `--command-line` uma corda com o parâmetro. Exemplo: `--command-line "python myscript.py arg1 arg2"`).

* [Grupo New-AzureRmContainer][new-azurermcontainergroup] Cmdlet Azure PowerShell: Passe `-Command` uma corda com o parâmetro. Exemplo: `-Command "echo hello"`.

* Portal Azure: No **Comando sobrepor** a propriedade da configuração do contentor, forneça uma lista separada de cordas, sem citações. Exemplo: `python, myscript.py, arg1, arg2`). 

* Modelo de Gestor de Recursos ou ficheiro YAML, ou um dos SDKs Azure: Especifique a propriedade da linha de comando como uma variedade de cordas. Exemplo: a matriz `["python", "myscript.py", "arg1", "arg2"]` JSON num modelo de Gestor de Recursos. 

  Se estiver familiarizado com a sintaxe [do Dockerfile,](https://docs.docker.com/engine/reference/builder/) este formato é semelhante à forma *executiva* da instrução cmd.

### <a name="examples"></a>Exemplos

|    |  CLI do Azure   | Portal | Modelo | 
| ---- | ---- | --- | --- |
| Comando único | `--command-line "python myscript.py arg1 arg2"` | **Sobreposição de comando:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Múltiplos comandos | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Sobreposição de comando:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exemplo azure CLI

Como exemplo, modifique o comportamento da imagem de recipiente [microsoft/aci-wordcount,][aci-wordcount] que analisa texto em *Shakespeare's Hamlet* para encontrar as palavras mais frequentes. Em vez de analisar *Hamlet,* pode definir uma linha de comando que aponta para uma fonte de texto diferente.

Para ver a saída do recipiente [microsoft/aci-wordcount][aci-wordcount] quando analisar o texto predefinido, execute-o com o [seguinte recipiente az criar][az-container-create] comando. Não é especificada nenhuma linha de comando de arranque, pelo que o comando do recipiente predefinido funciona. Para fins de ilustração, este exemplo define [variáveis ambientais](container-instances-environment-variables.md) para encontrar as 3 palavras top 3 que têm pelo menos cinco letras de comprimento:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Uma vez que o estado do recipiente mostre como *Terminado (use* o [recipiente az mostrar][az-container-show] para verificar o estado), exiba o registo com troncos de recipiente [az][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Saída:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Agora, instale um segundo recipiente de exemplo para analisar textos diferentes, especificando uma linha de comando diferente. O script Python executado pelo recipiente, *wordcount.py,* aceita um URL como argumento, e processa o conteúdo dessa página em vez do padrão.

Por exemplo, para determinar as 3 palavras top 3 que têm pelo menos cinco letras de comprimento em *Romeu e Julieta:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Mais uma vez, uma vez *terminado*o recipiente, veja a saída mostrando os registos do recipiente:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Saída:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas, como o processamento de lotes um grande conjunto de dados com vários contentores, podem beneficiar de linhas de comando personalizadas no tempo de execução. Para obter mais informações sobre a execução de contentores baseados em [tarefas, consulte executar tarefas contentorizadas com políticas](container-instances-restart-policy.md)de reinício .

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
