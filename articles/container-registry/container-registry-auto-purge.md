---
title: Purgar etiquetas e manifestos
description: Utilize um comando de purga para eliminar várias tags e manifestos de um registo de contentores Azure baseado na idade e num filtro de etiquetas, e agendar opcionalmente operações de purga.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087329"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Expurgar automaticamente imagens de um registo de contentores Do Idee

Quando se utiliza um registo de contentores Azure como parte de um fluxo de trabalho de desenvolvimento, o registo pode rapidamente encher-se de imagens ou outros artefactos que não são necessários após um curto período de tempo. É possível que deva eliminar todas as etiquetas mais antigas do que uma certa duração ou corresponder a um filtro de nome especificado. Para eliminar rapidamente vários artefactos, este artigo introduz o `acr purge` comando que pode executar como uma Tarefa ACR a pedido ou [programada.](container-registry-tasks-scheduled.md) 

O `acr purge` comando é atualmente distribuído numa`mcr.microsoft.com/acr/acr-cli:0.1`imagem de contentor público , construída a partir de código fonte no repo [acr-cli](https://github.com/Azure/acr-cli) no GitHub.

Pode utilizar a Casca de Nuvem Azure ou uma instalação local do Azure CLI para executar os exemplos de tarefas ACR neste artigo. Se quiser usá-lo localmente, a versão 2.0.69 ou posterior é necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install]. 

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

> [!WARNING]
> Utilize `acr purge` o comando com dados de imagem apagados por precaução. Se tiver sistemas que puxem imagens por uma digestão manifesta (em oposição ao nome da imagem), não deve expurgar imagens não marcadas. A apagar imagens não marcadas impedirá que esses sistemas retirem as imagens do seu registo. Em vez de puxar por manifesto, considere a adoção de um esquema *de marcação único,* uma [melhor prática recomendada.](container-registry-image-tag-version.md)

Se pretender eliminar etiquetas de imagem únicas ou manifestos utilizando comandos Azure CLI, consulte Apagar imagens de contentores no Registo de [Contentores Azure](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Use o comando de purga

O `acr purge` comando do recipiente elimina as imagens por etiqueta num repositório que corresponda a um filtro de nome e que sejam mais antigas do que uma duração especificada. Por predefinição, apenas são eliminadas as referências de etiquetas, não os [manifestos subjacentes](container-registry-concepts.md#manifest) e os dados da camada. O comando tem a opção de apagar também manifestos. 

> [!NOTE]
> `acr purge`não apaga uma etiqueta de imagem `write-enabled` ou um repositório em que o atributo está definido para `false`. Para obter informações, consulte Bloqueie uma imagem de contentor num registo de [contentores Azure](container-registry-image-lock.md).

`acr purge`foi concebido para funcionar como um comando de contentores numa [Tarefa ACR,](container-registry-tasks-overview.md)de modo a autenticar automaticamente com o registo onde a tarefa funciona e executa as suas ações. Os exemplos de tarefa `acr purge` neste artigo utilizam o [pseudónimo](container-registry-tasks-reference-yaml.md#aliases) de comando no lugar de um comando de imagem de contentor totalmente qualificado.

No mínimo, especifique `acr purge`o seguinte quando executar:

* `--filter`- Um repositório e uma *expressão regular* para filtrar etiquetas no repositório. Exemplos: `--filter "hello-world:.*"` corresponde a todas `hello-world` as etiquetas `--filter "hello-world:^1.*"` do repositório e corresponde às etiquetas a começar por `1`. Passe `--filter` vários parâmetros para expurgar vários repositórios.
* `--ago`- Uma cadeia de [duração](https://golang.org/pkg/time/) do estilo Go para indicar uma duração para além da qual as imagens são apagadas. A duração consiste numa sequência de um ou mais números decimais, cada um com um sufixo unitário. As unidades de tempo válidas incluem "d" durante dias, "h" durante horas e "m" durante minutos. Por exemplo, `--ago 2d3h6m` seleciona todas as imagens filtradas pela última vez modificadas há mais de 2 dias, 3 horas e 6 minutos, e `--ago 1.5h` seleciona imagens modificadas há mais de 1,5 horas.

`acr purge`suporta vários parâmetros opcionais. Os dois seguintes são utilizados em exemplos neste artigo:

* `--untagged`- São suprimidos os manifestos que não têm etiquetas associadas *(manifestos não marcados).*
* `--dry-run`- Especifica que não são eliminados dados, mas a saída é a mesma que se o comando for executado sem esta bandeira. Este parâmetro é útil para testar um comando de purga para se certificar de que não apaga inadvertidamente os dados que pretende preservar.

Para parâmetros adicionais, corra. `acr purge --help` 

`acr purge`suporta outras funcionalidades dos comandos aCR Tasks, incluindo variáveis de execução e [registos](container-registry-tasks-reference-yaml.md#run-variables) de execução de [tarefas](container-registry-tasks-logs.md) que são transmitidos e também guardados para posterior recuperação.

### <a name="run-in-an-on-demand-task"></a>Executar uma tarefa a pedido

O exemplo seguinte usa o comando de `acr purge` [corrida az acr][az-acr-run] para executar o comando a pedido. Este exemplo elimina todas as etiquetas `hello-world` de imagem e manifestações no repositório do *meu registo* que foram modificados há mais de 1 dia. O comando do recipiente é passado usando uma variável ambiental. A tarefa funciona sem um contexto de origem.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Executar em uma tarefa agendada

O exemplo seguinte utiliza a [tarefa az acr criar][az-acr-task-create] comando para criar uma [tarefa ACR diária agendada](container-registry-tasks-scheduled.md). A tarefa purga etiquetas modificadas há mais `hello-world` de 7 dias no repositório. O comando do recipiente é passado usando uma variável ambiental. A tarefa funciona sem um contexto de origem.

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

Executar o comando de [show de tarefas az acr][az-acr-task-show] para ver se o gatilho do temporizador está configurado.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Expurgar um grande número de tags e manifestos

A purga de um grande número de etiquetas e manifestos pode demorar vários minutos ou mais. Para expurgar milhares de tags e manifestos, o comando pode ter de ser executado mais tempo do que o tempo de tempo de 600 segundos para uma tarefa a pedido, ou 3600 segundos para uma tarefa programada. Se o tempo limite for ultrapassado, apenas um subconjunto de etiquetas e manifestos é eliminado. Para garantir que uma purga em larga `--timeout` escala esteja completa, passe o parâmetro para aumentar o valor. 

Por exemplo, a seguinte tarefa a pedido define um tempo de tempo de 3600 segundos (1 hora):

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Exemplo: Purga programada de múltiplos repositórios num registo

Este exemplo passa `acr purge` pelo uso para limpar periodicamente vários repositórios num registo. Por exemplo, você pode ter um pipeline `samples/devimage1` de `samples/devimage2` desenvolvimento que empurra as imagens para os repositórios e repositórios. Importa periodicamente imagens de desenvolvimento num repositório de produção para as suas implementações, para que já não precise das imagens de desenvolvimento. Semanalmente, expurga-se `samples/devimage1` `samples/devimage2` os e repositórios, em preparação para o trabalho da próxima semana.

### <a name="preview-the-purge"></a>Pré-visualizar a purga

Antes de eliminar os dados, recomendamos executar uma `--dry-run` tarefa de purga a pedido utilizando o parâmetro. Esta opção permite-lhe ver as etiquetas e manifesta que o comando irá purgar, sem remover quaisquer dados. 

No exemplo seguinte, o filtro em cada repositório seleciona todas as etiquetas. O `--ago 0d` parâmetro combina com imagens de todas as idades nos repositórios que combinam com os filtros. Modifique os critérios de seleção conforme necessário para o seu cenário. O `--untagged` parâmetro indica apagar manifestos para além das etiquetas. O comando do contentor é passado para o comando [de corrida az acr][az-acr-run] usando uma variável ambiental.

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

Reveja a saída de comando para ver as etiquetas e manifestos que correspondem aos parâmetros de seleção. Como o comando `--dry-run`é executado com , nenhum dado é eliminado.

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

Depois de verificar a corrida a seco, crie uma tarefa programada para automatizar a purga. O exemplo seguinte marca uma tarefa semanal no domingo às 1:00 UTC para executar o comando de purga anterior:

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

Executar o comando de [show de tarefas az acr][az-acr-task-show] para ver se o gatilho do temporizador está configurado.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outras opções para [eliminar dados](container-registry-delete.md) de imagem no Registo de Contentores Do Azure.

Para obter mais informações sobre o armazenamento de imagem, consulte o armazenamento de [imagem do contentor no Registo de Contentores Azure](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

