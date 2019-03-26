---
title: Importar imagens de contentor para o Azure Container Registry
description: Importe imagens de contentor para um Azure container registry através de APIs do Azure, sem a necessidade de executar comandos do Docker.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: b8a2280fe82e0f4be8e2812f5494150927642692
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417748"
---
# <a name="import-container-images-to-a-container-registry"></a>Importar imagens de contentor para um registo de contentor

Pode importar facilmente imagens de contentor (cópia) para o Azure container registry, sem utilizar comandos do Docker. Por exemplo, importar imagens a partir de um registo de desenvolvimento para um registo de produção ou copiar imagens de base de um registo público.

O Azure Container Registry processa um número de cenários comuns para copiar imagens de um registo existente:

* Importar a partir de um registo público

* Importar a partir de outro registo de contentor do Azure, no mesmo ou outra subscrição do Azure

* Importar a partir de uma privada não pertencente ao Azure registo de contentor

Importação de imagem para um registo de contentor do Azure tem as seguintes vantagens com o uso de comandos da CLI do Docker:

* Porque o ambiente de cliente não precisa de uma instalação local do Docker, importe qualquer imagem de contentor, independentemente do tipo de SO suportada.

* Quando importa a arquitetura de várias imagens (como imagens oficiais do Docker), copiadas imagens para todas as arquiteturas e plataformas especificadas na lista de manifesto.

Para importar imagens de contentor, este artigo requer que execute a CLI do Azure no Azure Cloud Shell ou localmente (versão 2.0.55 ou recomendados mais tarde). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

> [!NOTE]
> Se tiver de distribuir imagens de contentor idênticas em várias regiões do Azure, Azure Container Registry também suporta [georreplicação](container-registry-geo-replication.md). Através da geo-replicação de um registo (é necessário um SKU Premium), pode servir várias regiões com nomes idênticos de etiqueta de imagem ou de um único registo.
>

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver um Azure container registry, crie um registo. Para obter os passos, consulte [início rápido: Criar um registo de contentor privado com a CLI do Azure](container-registry-get-started-azure-cli.md).

Para importar uma imagem para um Azure container registry, sua identidade tem de ter permissões de escrita para o registo de destino (pelo menos a função de contribuinte). Ver [permissões e funções do Azure Container Registry](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importar a partir de um registo público

### <a name="import-from-docker-hub"></a>Importação do Hub do Docker

Por exemplo, utilizar o [importação do az acr] [ az-acr-import] comando para importar a arquitetura Multi `hello-world:latest` imagem do Docker Hub para um registo com o nome *myregistry*. Uma vez `hello-world` é uma imagem oficial do Docker Hub, a imagem é predefinida `library` repositório. Incluir o nome do repositório e, opcionalmente, uma marca no valor do `--source` parâmetro da imagem. (Pode, opcionalmente, identificar uma imagem por seu manifesto digest em vez de por marca, que garante uma versão específica de uma imagem.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

Pode verificar que vários manifestos são associados esta imagem ao executar o `az acr repository show-manifests` comando:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

O exemplo seguinte importa uma imagem pública a partir do `tensorflow` repositório no Docker Hub:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importar do registo de contentor da Microsoft

Por exemplo, importar a imagem mais recente do Windows Server Core do `windows` repositório no registo de contentor do Microsoft.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importar a partir de outro registo de contentor do Azure

Pode importar uma imagem de outro registo de contentor do Azure com permissões integradas do Azure Active Directory.

* Sua identidade tem de ter permissões do Azure Active Directory para ler o registo de origem (função de leitor) e escrever no registo de destino (função de contribuinte).

* O registo pode ser no mesmo ou uma subscrição do Azure diferente no mesmo inquilino do Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importar a partir de um registo na mesma subscrição

Por exemplo, importar os `aci-helloworld:latest` imagem a partir de um registo de origem *mysourceregistry* para *myregistry* na mesma subscrição do Azure.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

O exemplo seguinte importa uma imagem ao manifesto digest (hash de SHA-256, representado como `sha256:...`) em vez de por etiqueta:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importar a partir de um registo numa subscrição diferente

No exemplo a seguir *mysourceregistry* está numa subscrição diferente da *myregistry* no mesmo inquilino do Active Directory. Forneça o ID de recurso do registo de origem com o `--registry` parâmetro. Tenha em atenção que o `--source` parâmetro especifica apenas o repositório e imagem de nome de origem, não o nome do servidor do registo início de sessão.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importar a partir de um registo com credenciais do principal de serviço

Para importar a partir de um registo que não é possível aceder através de permissões do Active Directory, pode utilizar credenciais do principal de serviço (se disponível). Fornecer o appID e a palavra-passe de um Active Directory [principal de serviço](container-registry-auth-service-principal.md) que tem acesso de ACRPull para o registo de origem. Utilizar um principal de serviço é útil para sistemas de compilação e outros sistemas autónomas que tem de importar imagens para o seu registo.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importar a partir de uma privada não pertencente ao Azure registo de contentor

Importe uma imagem de um registo privado ao especificar as credenciais que permitem o acesso de pull para o registo. Por exemplo, solicitar uma imagem de um registo privado do Docker: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre a importação de imagens de contentor para um registo de contentor do Azure de um registo público ou outro registo privado. Opções de importação para imagem adicional, consulte a [importação do az acr] [ az-acr-import] referência do comando. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
