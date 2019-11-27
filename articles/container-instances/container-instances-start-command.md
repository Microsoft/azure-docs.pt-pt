---
title: Substituir EntryPoint na instância de contêiner
description: Definir uma linha de comando para substituir o ponto de entrada em uma imagem de contêiner ao implantar uma instância de contêiner do Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533411"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Definir a linha de comando em uma instância de contêiner para substituir a operação de linha de comando padrão

Ao criar uma instância de contêiner, especifique opcionalmente um comando para substituir a instrução de linha de comando padrão inclusas na imagem de contêiner. Esse comportamento é semelhante ao argumento de linha de comando `--entrypoint` para `docker run`.

Como definir [variáveis de ambiente](container-instances-environment-variables.md) para instâncias de contêiner, especificar uma linha de comando inicial é útil para trabalhos em lotes onde você precisa preparar cada contêiner dinamicamente com a configuração específica de tarefa.

## <a name="command-line-guidelines"></a>Diretrizes de linha de comando

* Por padrão, a linha de comando especifica um *único processo que inicia sem um shell* no contêiner. Por exemplo, a linha de comando pode executar um script Python ou um arquivo executável. O processo pode especificar parâmetros ou argumentos adicionais.

* Para executar vários comandos, comece sua linha de comando definindo um ambiente de shell com suporte no sistema operacional do contêiner. Exemplos:

  |Sistema operativo  |Shell padrão  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Siga as convenções do Shell para combinar vários comandos a serem executados em sequência.

* Dependendo da configuração do contêiner, talvez seja necessário definir um caminho completo para o executável ou os argumentos da linha de comando.

* Defina uma [política de reinicialização](container-instances-restart-policy.md) apropriada para a instância de contêiner, dependendo se a linha de comando especifica uma tarefa de execução longa ou uma tarefa de execução única. Por exemplo, uma política de reinicialização de `Never` ou `OnFailure` é recomendada para uma tarefa de execução única. 

* Se você precisar de informações sobre o ponto de entrada padrão definido em uma imagem de contêiner, use o comando [Docker Image eninspecione](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

## <a name="command-line-syntax"></a>Sintaxe de linha de comando

A sintaxe da linha de comando varia dependendo da API do Azure ou da ferramenta usada para criar as instâncias. Se você especificar um ambiente de Shell, observe também as convenções de sintaxe de comando do Shell.

* comando [AZ container Create][az-container-create] : passe uma cadeia de caracteres com o parâmetro `--command-line`. Exemplo: `--command-line "python myscript.py arg1 arg2"`).

* [New-AzureRmContainerGroup][new-azurermcontainergroup] Azure PowerShell cmdlet: passe uma cadeia de caracteres com o parâmetro `-Command`. Exemplo: `-Command "echo hello"`.

* Portal do Azure: na propriedade de **substituição de comando** da configuração do contêiner, forneça uma lista separada por vírgulas de cadeias de caracteres, sem aspas. Exemplo: `python, myscript.py, arg1, arg2`). 

* Modelo do Resource Manager ou arquivo YAML ou um dos SDKs do Azure: Especifique a propriedade de linha de comando como uma matriz de cadeias de caracteres. Exemplo: a matriz JSON `["python", "myscript.py", "arg1", "arg2"]` em um modelo do Resource Manager. 

  Se você estiver familiarizado com a sintaxe [Dockerfile](https://docs.docker.com/engine/reference/builder/) , esse formato será semelhante ao formulário *exec* da instrução cmd.

### <a name="examples"></a>Exemplos

|    |  CLI do Azure   | Portal | Modelo | 
| ---- | ---- | --- | --- |
| Comando único | `--command-line "python myscript.py arg1 arg2"` | **Substituição de comando**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Vários comandos | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Substituição de comando**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Exemplo de CLI do Azure

Por exemplo, modifique o comportamento da imagem de contêiner [Microsoft/ACI-WordCount][aci-wordcount] , que analisa o texto em *Hamlet* de Shakespeare para localizar as palavras que ocorrem com mais frequência. Em vez de analisar *Hamlet*, você pode definir uma linha de comando que aponta para uma fonte de texto diferente.

Para ver a saída do contêiner [Microsoft/ACI-WordCount][aci-wordcount] ao analisar o texto padrão, execute-o com o comando [AZ container Create][az-container-create] a seguir. Nenhuma linha de comando inicial é especificada, portanto, o comando de contêiner padrão é executado. Para fins de ilustração, este exemplo define [variáveis de ambiente](container-instances-environment-variables.md) para localizar as três principais palavras que têm pelo menos cinco letras de comprimento:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Quando o estado do contêiner aparecer como *encerrado* (use [AZ container show][az-container-show] para verificar o estado), exiba o log com [AZ container logs][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Saída:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Agora, configure um segundo contêiner de exemplo para analisar o texto diferente especificando uma linha de comando diferente. O script Python executado pelo contêiner, *WordCount.py*, aceita uma URL como um argumento e processa o conteúdo dessa página em vez do padrão.

Por exemplo, para determinar as três principais palavras que têm pelo menos cinco letras de comprimento em *Romeu e Julieta*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Novamente, depois que o contêiner for *encerrado*, exiba a saída mostrando os logs do contêiner:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Saída:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas, como processamento em lote de um grande conjunto de grandes com vários contêineres, podem se beneficiar de linhas de comando personalizadas em tempo de execução. Para obter mais informações sobre a execução de contêineres baseados em tarefas, consulte [executar tarefas em contêineres com políticas de reinicialização](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
