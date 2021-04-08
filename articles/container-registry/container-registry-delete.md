---
title: Eliminar recursos de imagem
description: Detalhes sobre como gerir eficazmente o tamanho do registo eliminando os dados de imagem do contentor usando comandos Azure CLI.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "78403338"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Apagar imagens de contentores no Registo do Contentor de Azure utilizando o CLI Azure

Para manter o tamanho do seu registo de contentores Azure, deve eliminar periodicamente dados de imagem em atraso. Embora algumas imagens de contentores implantadas em produção possam necessitar de armazenamento a longo prazo, outras podem normalmente ser eliminadas mais rapidamente. Por exemplo, num cenário automatizado de construção e teste, o seu registo pode preencher rapidamente com imagens que podem nunca ser implantadas, e pode ser purgada pouco depois de completar a construção e o passe de teste.

Como pode eliminar dados de imagem de várias maneiras diferentes, é importante entender como cada operação de eliminação afeta o uso do armazenamento. Este artigo abrange vários métodos para eliminar dados de imagem:

* Eliminar um [repositório](#delete-repository): Elimina todas as imagens e todas as camadas únicas dentro do repositório.
* Excluir por [marca:](#delete-by-tag)Elimina uma imagem, a etiqueta, todas as camadas únicas referenciadas pela imagem e todas as outras tags associadas à imagem.
* Excluir por [leitura manifesta](#delete-by-manifest-digest): Elimina uma imagem, todas as camadas únicas referenciadas pela imagem e todas as tags associadas à imagem.

Os scripts de amostra são fornecidos para ajudar a automatizar as operações de eliminação.

Para uma introdução a estes conceitos, consulte [sobre registos, repositórios e imagens.](container-registry-concepts.md)

## <a name="delete-repository"></a>Eliminar repositório

A eliminação de um repositório elimina todas as imagens do repositório, incluindo todas as tags, camadas únicas e manifestos. Quando se apaga um repositório, recupera-se o espaço de armazenamento utilizado pelas imagens que referenciam camadas únicas nesse repositório.

O seguinte comando Azure CLI elimina o repositório "acr-helloworld" e todas as tags e manifestos dentro do repositório. Se as camadas referenciadas pelos manifestos eliminados não forem referenciadas por quaisquer outras imagens no registo, os seus dados de camada também são eliminados, recuperando o espaço de armazenamento.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Excluir por etiqueta

Pode eliminar imagens individuais de um repositório especificando o nome e a etiqueta do repositório na operação de eliminação. Quando apaga por marca, recupera o espaço de armazenamento utilizado por quaisquer camadas únicas na imagem (camadas não partilhadas por quaisquer outras imagens no registo).

Para eliminar por marca, utilize [o repositório az acr e especifique][az-acr-repository-delete] o nome da imagem no `--image` parâmetro. Todas as camadas únicas à imagem, e quaisquer outras tags associadas à imagem são eliminadas.

Por exemplo, eliminando a imagem "acr-helloworld:mais recente" do registo "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> A *eliminação por etiqueta* não deve ser confundida com a eliminação de uma etiqueta (desacompanhada). Pode eliminar uma etiqueta com o comando Azure CLI [az acr repositório untag][az-acr-repository-untag]. Nenhum espaço é libertado quando desmarca uma imagem porque os seus dados [manifestos](container-registry-concepts.md#manifest) e de camada permanecem no registo. Apenas a referência da etiqueta em si é eliminada.

## <a name="delete-by-manifest-digest"></a>Excluir por leitura manifesta

Uma [leitura manifesta](container-registry-concepts.md#manifest-digest) pode ser associada a uma, nenhuma ou a várias tags. Quando elimina por digestão, todas as tags referenciadas pelo manifesto são eliminadas, assim como os dados da camada para quaisquer camadas únicas à imagem. Os dados da camada partilhada não são eliminados.

Para eliminar por digestão, primeiro enumerar as leituras do manifesto para o repositório que contém as imagens que pretende eliminar. Por exemplo:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
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

Em seguida, especifique a digestão que pretende eliminar no comando de eliminação do [repositório az acr.][az-acr-repository-delete] O formato do comando é:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Por exemplo, eliminar o último manifesto listado na saída anterior (com a etiqueta "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

A `acr-helloworld:v2` imagem é eliminada do registo, assim como qualquer dado de camada único a essa imagem. Se um manifesto estiver associado a várias tags, todas as tags associadas também são eliminadas.

## <a name="delete-digests-by-timestamp"></a>Eliminar digestões por tempotampia

Para manter o tamanho de um repositório ou registo, poderá ser necessário eliminar periodicamente as digestão manifestas com mais idade do que uma determinada data.

O comando Azure CLI que se segue lista todos os manifestos digeridos num repositório mais antigo do que uma hora especificada, por ordem ascendente. Substitua `<acrName>` e `<repositoryName>` por valores apropriados para o seu ambiente. A data pode ser uma expressão de data completa ou uma data, como neste exemplo.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Depois de identificar as digestões de manifestos, pode executar o seguinte script Bash para eliminar as digestão manifestas mais antigas do que uma placa de tempo especificada. Requer o Azure CLI e **xargs**. Por predefinição, o script não executa nenhuma supressão. Altere o `ENABLE_DELETE` valor para permitir a `true` eliminação de imagem.

> [!WARNING]
> Utilize o seguinte script de amostra com precaução-- os dados de imagem eliminados são IRRECONHECÍVEIS. Se tiver sistemas que retiram imagens por manifesta digestão (em oposição ao nome da imagem), não deve executar estes scripts. A eliminação das digestão manifestas impedirá que esses sistemas retirem as imagens do seu registo. Em vez de puxar pelo manifesto, considere a adoção de um esquema *de marcação único,* uma [melhor prática recomendada.](container-registry-image-tag-version.md) 

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

## <a name="delete-untagged-images"></a>Apagar imagens não gravadas

Como mencionado na secção [Deserção](container-registry-concepts.md#manifest-digest) do Manifesto, empurrando uma imagem modificada usando uma etiqueta existente **desmarca** a imagem previamente empurrada, resultando numa imagem órfã (ou "pendurada"). O manifesto da imagem anteriormente empurrado e os seus dados de camadas permanecem no registo. Considere a seguinte sequência de eventos:

1. Push image *acr-helloworld* com etiqueta **mais recente:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos do repositório *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
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

1. Modificar *acr-helloworld* Dockerfile
1. Push image *acr-helloworld* com etiqueta **mais recente:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Verifique os manifestos do repositório *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
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

Como pode ver na saída do último passo da sequência, há agora um manifesto órfão cuja `"tags"` propriedade é uma lista vazia. Este manifesto ainda existe dentro do registo, juntamente com quaisquer dados únicos de camada que ele refere. **Para eliminar tais imagens órfãs e os seus dados de camada, deve apagar por manifesta digestão**.

## <a name="delete-all-untagged-images"></a>Apagar todas as imagens não gravadas

Pode listar todas as imagens não gravadas no seu repositório utilizando o seguinte comando Azure CLI. Substitua `<acrName>` e `<repositoryName>` por valores apropriados para o seu ambiente.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Utilizando este comando num script, pode eliminar todas as imagens não gravadas num repositório.

> [!WARNING]
> Utilize os seguintes scripts de amostra com precaução-- os dados de imagem eliminados são IRRECONHECÍVEIS. Se tiver sistemas que retiram imagens por manifesta digestão (em oposição ao nome da imagem), não deve executar estes scripts. A eliminação de imagens não identificadas impedirá que esses sistemas retirem as imagens do seu registo. Em vez de puxar pelo manifesto, considere a adoção de um esquema *de marcação único,* uma [melhor prática recomendada.](container-registry-image-tag-version.md)

**Azure CLI em Bash**

O seguinte script Bash elimina todas as imagens não gravadas de um repositório. Requer o Azure CLI e **xargs**. Por predefinição, o script não executa nenhuma supressão. Altere o `ENABLE_DELETE` valor para permitir a `true` eliminação de imagem.

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

**Azure CLI em PowerShell**

O seguinte script PowerShell elimina todas as imagens não gravadas de um repositório. Requer PowerShell e o Azure CLI. Por predefinição, o script não executa nenhuma supressão. Altere o `$enableDelete` valor para permitir a `$TRUE` eliminação de imagem.

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

Como alternativa à scripting Azure CLI comandos, executar uma tarefa ACR a pedido ou programada para eliminar todas as tags que sejam mais antigas do que uma determinada duração ou correspondam a um filtro de nome especificado. Para obter mais informações, consulte [as imagens de purga automática de um registo de contentores Azure](container-registry-auto-purge.md).

Definindo opcionalmente uma [política de retenção](container-registry-retention-policy.md) para cada registo, para gerir manifestos não marcados. Quando ativa uma política de retenção, os manifestos de imagem no registo que não têm nenhuma etiqueta associada, e os dados subjacentes da camada, são automaticamente eliminados após um período definido.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o armazenamento de imagens no Registo do Contentor de Azure consulte [o armazenamento de imagem do contentor no Registo do Contentor de Azure](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
