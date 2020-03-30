---
title: Importar imagens de contentor
description: Importe imagens de contentores para um registo de contentores Azure utilizando APIs Azure, sem necessidade de executar comandos Docker.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: caf7a47ac8f7ff0e72d2e049a7013542d274a225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051929"
---
# <a name="import-container-images-to-a-container-registry"></a>Importar imagens de contentores para um registo de contentores

Pode importar facilmente (copiar) imagens de contentores para um registo de contentores Azure, sem utilizar comandos Docker. Por exemplo, importar imagens de um registo de desenvolvimento para um registo de produção, ou copiar imagens base de um registo público.

O Registo de Contentores Azure trata de uma série de cenários comuns para copiar imagens de um registo existente:

* Importação de um registo público

* Importação de outro registo de contentores Azure, na mesma ou numa assinatura Azure diferente

* Importação de um registo de contentores privados não-Azure

A importação de imagem para um registo de contentores Azure tem os seguintes benefícios sobre a utilização de comandos Docker CLI:

* Como o ambiente do seu cliente não precisa de uma instalação local do Docker, importe qualquer imagem de contentor, independentemente do tipo de SISTEMA suportado.

* Quando se importam imagens multi-arquitetura (como imagens oficiais do Docker), as imagens para todas as arquiteturas e plataformas especificadas na lista de manifestos são copiadas.

Para importar imagens de contentores, este artigo requer que execute o Azure CLI em Azure Cloud Shell ou localmente (versão 2.0.55 ou posteriormente recomendado). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

> [!NOTE]
> Se precisar de distribuir imagens de contentores idênticas em várias regiões do Azure, o Registo de Contentores Azure também suporta a [geo-replicação.](container-registry-geo-replication.md) Ao replicar um registo (nível de serviço Premium necessário), pode servir várias regiões com imagem idêntica e nomes de etiquetas de um único registo.
>

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver um registo de contentores Azure, crie um registo. Para os passos, consulte [Quickstart: Crie um registo de contentores privado sintetizador com o Azure CLI](container-registry-get-started-azure-cli.md).

Para importar uma imagem para um registo de contentores Azure, a sua identidade deve ter permissões de escrita para o registo-alvo (pelo menos papel de Contribuinte). Consulte [as funções e permissões](container-registry-roles.md)do Registo de Contentores Azure . 

## <a name="import-from-a-public-registry"></a>Importação de um registo público

### <a name="import-from-docker-hub"></a>Importação de Docker Hub

Por exemplo, utilize o comando de [importação az acr][az-acr-import] para importar a imagem multi-arquitetura `hello-world:latest` de Docker Hub para um registo chamado *myregistry*. Porque `hello-world` é uma imagem oficial do Docker Hub, esta imagem está no repositório padrão. `library` Inclua o nome do repositório e opcionalmente uma etiqueta no valor do `--source` parâmetro de imagem. (Pode identificar opcionalmente uma imagem através da sua digestão manifesto em vez de por etiqueta, o que garante uma versão específica de uma imagem.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Pode verificar se vários manifestos estão associados a esta imagem executando o `az acr repository show-manifests` comando:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

O exemplo seguinte importa uma `tensorflow` imagem pública do repositório em Docker Hub:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importação do Registo de Contentores da Microsoft

Por exemplo, importar a mais recente `windows` imagem do Windows Server Core do repositório no Registo de Contentores da Microsoft.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:latest \
--image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importação de outro registo de contentores Azure

Pode importar uma imagem de outro registo de contentores Azure utilizando permissões integradas do Diretório Ativo Azure.

* A sua identidade deve ter permissões de Diretório Ativo Azure para ler a partir do registo de origem (papel de leitor) e escrever para o registo-alvo (função Contributiva).

* O registo pode ser na mesma ou numa assinatura Azure diferente no mesmo inquilino do Diretório Ativo.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importação de um registo na mesma subscrição

Por exemplo, `aci-helloworld:latest` importar a imagem de um registo de *origem* para *o meu registo* na mesma assinatura Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

O exemplo seguinte importa uma imagem por manifesto digestão (SHA-256 hash, representada como `sha256:...`) em vez de por etiqueta:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importação de um registo numa subscrição diferente

No exemplo seguinte, o *mysourceregistry* está numa subscrição diferente do *meu registo* no mesmo inquilino do Diretório Ativo. Forneça o iD de recurso `--registry` do registo de origem com o parâmetro. Note `--source` que o parâmetro especifica apenas o repositório de origem e a etiqueta, e não o nome do servidor de login de registo.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importação de um registo utilizando credenciais principais de serviço

Para importar de um registo que não pode aceder usando permissões de Diretório Ativo, pode utilizar credenciais principais de serviço (se disponível). Forneça o appID e a palavra-passe de um [diretor](container-registry-auth-service-principal.md) ativo que tenha acesso a ACRPull ao registo de origem. A utilização de um diretor de serviço é útil para criar sistemas e outros sistemas não atendidos que precisam de importar imagens para o seu registo.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importação de um registo de contentores privados não-Azure

Importar uma imagem de um registo privado especificando credenciais que permitam puxar o acesso ao registo. Por exemplo, retire uma imagem de um registo privado do Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a importar imagens de contentores para um registo de contentores Azure a partir de um registo público ou de outro registo privado. Para opções adicionais de importação de imagem, consulte a referência do comando de [importação az acr.][az-acr-import] 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
