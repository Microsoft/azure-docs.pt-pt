---
title: Purgue etiquetas e manifestos
description: Utilize um comando de purga para eliminar várias tags e manifestos de um registo de contentores Azure baseado na idade e num filtro de etiquetas, e agendar opcionalmente operações de purga.
ms.topic: article
ms.date: 01/27/2021
ms.openlocfilehash: ab1a925092784effd07431d75e4ec1535c53ed33
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927270"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Purgar automaticamente imagens de um registo de contentores Azure

Quando utiliza um registo de contentores Azure como parte de um fluxo de trabalho de desenvolvimento, o registo pode rapidamente encher-se de imagens ou outros artefactos que não são necessários após um curto período de tempo. É possível que seja melhor apagar todas as etiquetas mais antigas do que uma determinada duração ou corresponder a um filtro de nome especificado. Para eliminar rapidamente vários artefactos, este artigo introduz o `acr purge` comando que pode executar como uma tarefa ACR a pedido ou [programada.](container-registry-tasks-scheduled.md) 

O `acr purge` comando é atualmente distribuído numa imagem de contentor público , `mcr.microsoft.com/acr/acr-cli:0.4` construído a partir do código fonte no repo [acr-cli](https://github.com/Azure/acr-cli) em GitHub.

Pode utilizar o Azure Cloud Shell ou uma instalação local do CLI Azure para executar os exemplos de tarefas ACR neste artigo. Se quiser usá-lo localmente, é necessário utilizar a versão 2.0.76 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install]. 

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

> [!WARNING]
> Utilize o `acr purge` comando com precaução-- os dados de imagem eliminados são IRRECONHECÍVEIS. Se tiver sistemas que retiram imagens por manifesta digestão (em oposição ao nome da imagem), não deve expurgar imagens não gravadas. A eliminação de imagens não identificadas impedirá que esses sistemas retirem as imagens do seu registo. Em vez de puxar pelo manifesto, considere a adoção de um esquema *de marcação único,* uma [melhor prática recomendada.](container-registry-image-tag-version.md)

Se pretender eliminar as etiquetas de imagem ou manifestos únicos utilizando os comandos Azure CLI, consulte [apagar as imagens dos contentores no Registo do Contentor de Azure](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Use o comando de purga

O `acr purge` comando do recipiente elimina as imagens por marca num repositório que corresponde a um filtro de nome e que são mais antigas do que uma duração especificada. Por padrão, apenas as referências de etiqueta são eliminadas, não os [manifestos subjacentes](container-registry-concepts.md#manifest) e os dados da camada. O comando tem a opção de apagar manifestos. 

> [!NOTE]
> `acr purge` não apaga uma etiqueta de imagem ou repositório onde o `write-enabled` atributo está definido para `false` . Para obter informações, consulte [a imagem do recipiente num registo de contentores Azure](container-registry-image-lock.md).

`acr purge` é projetado para funcionar como um comando de contentor numa [Tarefa ACR](container-registry-tasks-overview.md), de modo que autentica automaticamente com o registo onde a tarefa executa e executa ações lá. Os exemplos de tarefa deste artigo utilizam o `acr purge` pseudónimo de comando no lugar de [um](container-registry-tasks-reference-yaml.md#aliases) comando de imagem de contentor totalmente qualificado.

No mínimo, especifique o seguinte quando `acr purge` correr:

* `--filter` - Um repositório e uma *expressão regular* para filtrar etiquetas no repositório. Exemplos: `--filter "hello-world:.*"` corresponde a todas as etiquetas do `hello-world` repositório e `--filter "hello-world:^1.*"` corresponde às tags que começam com `1` . Passe `--filter` vários parâmetros para expurgar vários repositórios.
* `--ago` - Uma cadeia de [duração](https://golang.org/pkg/time/) estilo Go para indicar uma duração para além da qual as imagens são eliminadas. A duração consiste numa sequência de um ou mais números decimais, cada um com um sufixo de unidade. As unidades de tempo válidas incluem "d" durante dias, "h" durante horas e "m" durante minutos. Por exemplo, `--ago 2d3h6m` seleciona todas as imagens filtradas modificadas há mais de 2 dias, 3 horas e 6 minutos atrás, e `--ago 1.5h` seleciona as imagens modificadas pela última vez há mais de 1,5 horas.

`acr purge` suporta vários parâmetros opcionais. Os dois seguintes são usados em exemplos neste artigo:

* `--untagged`- Especifica que os manifestos que não têm tags associadas *(manifestos não com marcação) são eliminados.*
* `--dry-run` - Especifica que nenhum dado é eliminado, mas a saída é a mesma que se o comando for executado sem esta bandeira. Este parâmetro é útil para testar um comando de purga para se certificar de que não apaga inadvertidamente os dados que pretende preservar.
* `--keep` - Especifica que o último x número de tags a eliminar é mantido.
* `--concurrency` - Especifica que as tarefas de purga x são processadas simultaneamente. Um valor predefinido será utilizado se este parâmetro não for fornecido.

Para parâmetros adicionais, corra `acr purge --help` . 

`acr purge` suporta outras funcionalidades dos comandos ACR Tasks, incluindo variáveis de [execução](container-registry-tasks-reference-yaml.md#run-variables) e [registos de execução](container-registry-tasks-logs.md) de tarefas que são transmitidos e também guardados para posterior recuperação.

### <a name="run-in-an-on-demand-task"></a>Executar numa tarefa a pedido

O exemplo a seguir utiliza o comando [de execução az acr][az-acr-run] para executar o comando a `acr purge` pedido. Este exemplo elimina todas as etiquetas de imagem e manifestos no `hello-world` repositório no *miogressitório* que foram modificados há mais de um dia. O comando do contentor é passado usando uma variável ambiental. A tarefa é executado sem um contexto de origem.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Executar numa tarefa programada

O exemplo a seguir utiliza a [tarefa az acr criar][az-acr-task-create] comando para criar uma [tarefa diária de ACR programada](container-registry-tasks-scheduled.md). A tarefa purga as etiquetas modificadas há mais de 7 dias no `hello-world` repositório. O comando do contentor é passado usando uma variável ambiental. A tarefa é executado sem um contexto de origem.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Executar o comando [de programa de tarefas az acr][az-acr-task-show] para ver se o gatilho do temporizador está configurado.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Purgue um grande número de tags e manifestos

Purgar um grande número de tags e manifestos pode demorar vários minutos ou mais. Para expurgar milhares de tags e manifestos, o comando pode precisar de correr mais tempo do que o tempo de tempo limite de 600 segundos para uma tarefa a pedido, ou 3600 segundos para uma tarefa programada. Se o tempo de tempo limite for ultrapassado, apenas um subconjunto de tags e manifestos são eliminados. Para garantir que uma purga em larga escala esteja completa, passe o `--timeout` parâmetro para aumentar o valor. 

Por exemplo, a seguinte tarefa a pedido define um tempo de tempo limite de 3600 segundos (1 hora):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Exemplo: Purga programada de vários repositórios num registo

Este exemplo passa pela utilização `acr purge` para limpar periodicamente vários repositórios num registo. Por exemplo, você pode ter um oleoduto de desenvolvimento que empurra imagens para os `samples/devimage1` `samples/devimage2` repositórios e repositórios. Você importa periodicamente imagens de desenvolvimento em um repositório de produção para seus lançamentos, para que você não precise mais das imagens de desenvolvimento. Semanalmente, expurga-se os `samples/devimage1` `samples/devimage2` repositórios e os repositórios, em preparação para o trabalho da próxima semana.

### <a name="preview-the-purge"></a>Pré-visualizar a purga

Antes de eliminar os dados, recomendamos executar uma tarefa de purga a pedido utilizando o `--dry-run` parâmetro. Esta opção permite-lhe ver as etiquetas e manifesta que o comando irá purgar, sem remover quaisquer dados. 

No exemplo seguinte, o filtro em cada repositório seleciona todas as etiquetas. O `--ago 0d` parâmetro corresponde a imagens de todas as idades nos repositórios que combinam com os filtros. Modifique os critérios de seleção conforme necessário para o seu cenário. O `--untagged` parâmetro indica apagar manifestos para além das etiquetas. O comando do contentor é passado para o comando [de execução az acr][az-acr-run] usando uma variável ambiental.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Reveja a saída do comando para ver as etiquetas e manifestos que correspondem aos parâmetros de seleção. Como o comando é executado `--dry-run` com, nenhum dado é eliminado.

Resultado do exemplo:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Agende a purga

Depois de verificar a secagem, crie uma tarefa programada para automatizar a purga. O exemplo a seguir marca uma tarefa semanal no domingo às 1:00 UTC para executar o comando anterior de purga:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Executar o comando [de programa de tarefas az acr][az-acr-task-show] para ver se o gatilho do temporizador está configurado.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre outras opções para [eliminar dados](container-registry-delete.md) de imagem no Registo do Contentor Azure.

Para obter mais informações sobre o armazenamento de [imagens, consulte o armazenamento de imagem do contentor no Registo do Contentor Azure.](container-registry-storage.md)

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

