---
title: Excluir recursos de imagem
description: Detalhes sobre como gerenciar efetivamente o tamanho do registro excluindo dados de imagem de contêiner usando comandos CLI do Azure.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8d20bf2be1d472855c3e67dd79ea1725c152e3d2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455264"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Excluir imagens de contêiner no registro de contêiner do Azure usando o CLI do Azure

Para manter o tamanho do registro de contêiner do Azure, você deve excluir periodicamente dados de imagem obsoletos. Embora algumas imagens de contêiner implantadas na produção possam exigir armazenamento de longo prazo, outras normalmente podem ser excluídas mais rapidamente. Por exemplo, em um cenário automatizado de compilação e teste, o registro pode ser rapidamente preenchido com imagens que podem nunca ser implantadas e pode ser limpo logo após a conclusão da fase de teste e compilação.

Como você pode excluir dados de imagem de várias maneiras diferentes, é importante entender como cada operação de exclusão afeta o uso do armazenamento. Este artigo aborda vários métodos para excluir dados de imagem:

* Excluir um [repositório](#delete-repository): exclui todas as imagens e todas as camadas exclusivas no repositório.
* Excluir por [marca](#delete-by-tag): exclui uma imagem, a marca, todas as camadas exclusivas referenciadas pela imagem e todas as outras marcas associadas à imagem.
* Excluir por [Resumo do manifesto](#delete-by-manifest-digest): exclui uma imagem, todas as camadas exclusivas referenciadas pela imagem e todas as marcas associadas à imagem.

Os scripts de exemplo são fornecidos para ajudar a automatizar operações de exclusão.

Para obter uma introdução a esses conceitos, consulte [sobre registros, repositórios e imagens](container-registry-concepts.md).

## <a name="delete-repository"></a>Excluir repositório

A exclusão de um repositório exclui todas as imagens no repositório, incluindo todas as marcas, camadas exclusivas e manifestos. Ao excluir um repositório, você recupera o espaço de armazenamento usado pelas imagens que fazem referência a camadas exclusivas nesse repositório.

O comando a seguir CLI do Azure exclui o repositório "ACR-HelloWorld" e todas as marcas e manifestos no repositório. Se as camadas referenciadas pelos manifestos excluídos não forem referenciadas por nenhuma outra imagem no registro, seus dados de camada também serão excluídos, recuperando o espaço de armazenamento.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Excluir por marca

Você pode excluir imagens individuais de um repositório especificando o nome do repositório e a marca na operação de exclusão. Ao excluir por marca, você recupera o espaço de armazenamento usado por quaisquer camadas exclusivas na imagem (as camadas não são compartilhadas por nenhuma outra imagem no registro).

Para excluir por marca, use [AZ ACR Repository Delete][az-acr-repository-delete] e especifique o nome da imagem no parâmetro `--image`. Todas as camadas exclusivas da imagem e quaisquer outras marcas associadas à imagem são excluídas.

Por exemplo, excluir a imagem "ACR-HelloWorld: latest" do registro "myregistry":

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> A exclusão *por marca* não deve ser confundida com a exclusão de uma marca (desmarcação). Você pode excluir uma marca com o comando CLI do Azure o [repositório de ACR AZ][az-acr-repository-untag]é desmarcado. Nenhum espaço é liberado ao se desmarcar uma imagem porque seu [manifesto](container-registry-concepts.md#manifest) e os dados da camada permanecem no registro. Somente a referência de marca em si é excluída.

## <a name="delete-by-manifest-digest"></a>Excluir por Resumo do manifesto

Um [Resumo de manifesto](container-registry-concepts.md#manifest-digest) pode ser associado a uma, nenhuma ou várias marcas. Quando você exclui por Resumo, todas as marcas referenciadas pelo manifesto são excluídas, assim como os dados de camada para todas as camadas exclusivas da imagem. Os dados da camada compartilhada não são excluídos.

Para excluir por Resumo, primeiro liste os resumos do manifesto para o repositório que contém as imagens que você deseja excluir. Por exemplo:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Em seguida, especifique o resumo que você deseja excluir no comando [AZ ACR Repository Delete][az-acr-repository-delete] . O formato do comando é:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Por exemplo, para excluir o último manifesto listado na saída anterior (com a marca "v2"):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

A imagem de `acr-helloworld:v2` é excluída do registro, assim como qualquer dado de camada exclusivo para essa imagem. Se um manifesto estiver associado a várias marcas, todas as marcas associadas também serão excluídas.

## <a name="delete-digests-by-timestamp"></a>Excluir resumos por carimbo de data/hora

Para manter o tamanho de um repositório ou registro, talvez seja necessário excluir periodicamente resumos de manifesto com mais de uma determinada data.

O comando CLI do Azure a seguir lista todos os resumos de manifesto em um repositório com mais de um carimbo de data/hora especificado, em ordem crescente. Substitua `<acrName>` e `<repositoryName>` com os valores apropriados para o seu ambiente. O timestamp pode ser uma expressão de data e hora completa ou uma data, como neste exemplo.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Depois de identificar resumos de manifesto obsoletos, você pode executar o script bash a seguir para excluir resumos de manifesto mais antigos que um carimbo de data/hora especificado. Ele requer o CLI do Azure e **xargs**. Por padrão, o script não executa nenhuma exclusão. Altere o valor de `ENABLE_DELETE` para `true` para habilitar a exclusão de imagem.

> [!WARNING]
> Use o exemplo de script a seguir com cuidado--os dados de imagem excluídos são irrecuperáveis. Se você tiver sistemas que extraem imagens por Resumo do manifesto (em oposição ao nome da imagem), você não deve executar esses scripts. Excluir os resumos do manifesto impedirá que esses sistemas extraiam as imagens do registro. Em vez de efetuar pull por manifesto, considere a adoção de um esquema de *marcação exclusivo* , uma [prática](container-registry-image-tag-version.md)recomendada. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Excluir imagens não marcadas

Conforme mencionado na seção [Resumo do manifesto](container-registry-concepts.md#manifest-digest) , enviar por push uma imagem modificada usando uma marca existente **desmarcará** a imagem enviada anteriormente, resultando em uma imagem órfã (ou "pendente"). O manifesto da imagem enviada anteriormente, e seus dados da camada, permanecem no registro. Considere a seguinte sequência de eventos:

1. ACR de imagem por push *-HelloWorld* com marca **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verificar manifestos para o repositório *ACR-HelloWorld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modificar *ACR-HelloWorld* Dockerfile
1. ACR de imagem por push *-HelloWorld* com marca **mais recente**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verificar manifestos para o repositório *ACR-HelloWorld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Como você pode ver na saída da última etapa na sequência, agora há um manifesto órfão cuja propriedade `"tags"` é uma lista vazia. Esse manifesto ainda existe no registro, juntamente com quaisquer dados de camada exclusivos que fazem referência a ele. **Para excluir essas imagens órfãs e seus dados de camada, você deve excluir por Resumo do manifesto**.

## <a name="delete-all-untagged-images"></a>Excluir todas as imagens não marcadas

Você pode listar todas as imagens não marcadas no repositório usando o comando CLI do Azure a seguir. Substitua `<acrName>` e `<repositoryName>` com os valores apropriados para o seu ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Usando esse comando em um script, você pode excluir todas as imagens não marcadas em um repositório.

> [!WARNING]
> Use os scripts de exemplo a seguir com cuidado--os dados de imagem excluídos são irrecuperáveis. Se você tiver sistemas que extraem imagens por Resumo do manifesto (em oposição ao nome da imagem), você não deve executar esses scripts. A exclusão de imagens não marcadas impedirá que esses sistemas extraiam as imagens do registro. Em vez de efetuar pull por manifesto, considere a adoção de um esquema de *marcação exclusivo* , uma [prática](container-registry-image-tag-version.md)recomendada.

**CLI do Azure no bash**

O script bash a seguir exclui todas as imagens não marcadas de um repositório. Ele requer o CLI do Azure e **xargs**. Por padrão, o script não executa nenhuma exclusão. Altere o valor de `ENABLE_DELETE` para `true` para habilitar a exclusão de imagem.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**CLI do Azure no PowerShell**

O script do PowerShell a seguir exclui todas as imagens não marcadas de um repositório. Ele requer o PowerShell e o CLI do Azure. Por padrão, o script não executa nenhuma exclusão. Altere o valor de `$enableDelete` para `$TRUE` para habilitar a exclusão de imagem.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Remover automaticamente etiquetas e manifestos (pré-visualização)

Como uma alternativa ao script CLI do Azure comandos, execute uma tarefa de ACR sob demanda ou agendada para excluir todas as marcas que são mais antigas do que uma determinada duração ou que correspondam a um filtro de nome especificado. Para obter mais informações, consulte [limpar automaticamente imagens de um registro de contêiner do Azure](container-registry-auto-purge.md).

Opcionalmente, defina uma [política de retenção](container-registry-retention-policy.md) para cada registro, para gerenciar os manifestos não marcados. Quando você habilita uma política de retenção, os manifestos de imagem no registro que não têm nenhuma marca associada e os dados de camada subjacentes são excluídos automaticamente após um período definido.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o armazenamento de imagem no registro de contêiner do Azure, veja [armazenamento de imagem de contêiner no registro de contêiner do Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
