---
title: Importar imagens de contentor
description: Importar imagens de contentores para um registo de contentores Azure utilizando APIs Azure, sem necessidade de executar comandos Docker.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: 364c90b857d0d7d479152e2aa56db4d80041f037
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524503"
---
# <a name="import-container-images-to-a-container-registry"></a>Importar imagens de contentores para um registo de contentores

Pode facilmente importar (copiar) imagens de contentores para um registo de contentores Azure, sem utilizar comandos Docker. Por exemplo, importar imagens de um registo de desenvolvimento para um registo de produção, ou copiar imagens de base de um registo público.

O Registo de Contentores Azure lida com uma série de cenários comuns para copiar imagens de um registo existente:

* Importação de um registo público

* Importação de outro registo de contentores Azure, na mesma ou numa subscrição ou inquilino Azure diferente

* Importação de um registo de contentores privados não-Azure

A importação de imagem para um registo de contentores Azure tem os seguintes benefícios ao utilizar comandos Docker CLI:

* Como o ambiente do seu cliente não precisa de uma instalação local do Docker, importe qualquer imagem de contentor, independentemente do tipo de SO suportado.

* Quando importa imagens multi-arquitetura (como imagens oficiais do Docker), imagens para todas as arquiteturas e plataformas especificadas na lista de manifestos são copiadas.

* O acesso ao registo-alvo não tem de usar o ponto final público do registo.

Para importar imagens de contentores, este artigo requer que você execute o Azure CLI em Azure Cloud Shell ou localmente (versão 2.0.55 ou posteriormente recomendado). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

> [!NOTE]
> Se necessitar de distribuir imagens idênticas de contentores em várias regiões do Azure, o Registo de Contentores Azure também suporta [a geo-replicação.](container-registry-geo-replication.md) Ao geo-replicar um registo (nível de serviço Premium necessário), pode servir várias regiões com imagens idênticas e nomes de etiquetas a partir de um único registo.
>

> [!IMPORTANT]
> As alterações à importação de imagem entre dois registos de contentores Azure foram introduzidas a partir de janeiro de 2021:
> * A importação de ou de um registo restrito de contentores Azure em rede requer o registo restrito para permitir o [**acesso por serviços fidedignos**](allow-access-trusted-services.md) para contornar a rede. Por predefinição, a definição está ativada, permitindo a importação. Se a definição não estiver ativada num registo recém-criado com um ponto final privado ou com regras de firewall de registo, a importação falhará. 
> * Num registo de contentores Azure restrito à rede existente que é utilizado como fonte de importação ou alvo, permitir esta funcionalidade de segurança da rede é opcional, mas recomendado.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver um registo de contentores Azure, crie um registo. Para etapas, consulte [Quickstart: Crie um registo de contentores privados utilizando o Azure CLI](container-registry-get-started-azure-cli.md).

Para importar uma imagem para um registo de contentores Azure, a sua identidade deve ter permissões de escrita para o registo-alvo (pelo menos papel de contribuinte, ou um papel personalizado que permita a ação importImage). Consulte [as funções e permissões do Registo do Contentor Azure](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importação de um registo público

### <a name="import-from-docker-hub"></a>Importação de Docker Hub

Por exemplo, use o comando [de importação az acr][az-acr-import] para importar a imagem multi-arquitetura `hello-world:latest` de Docker Hub para um registo chamado *myregistry*. Porque `hello-world` é uma imagem oficial de Docker Hub, esta imagem está no `library` repositório padrão. Inclua o nome do repositório e opcionalmente uma etiqueta no valor do parâmetro de `--source` imagem. (Pode identificar opcionalmente uma imagem pela sua reerva manifesta em vez de por etiqueta, o que garante uma determinada versão de uma imagem.)
 
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

O exemplo a seguir importa uma imagem pública do `tensorflow` repositório em Docker Hub:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importação do Registo de Contentores da Microsoft

Por exemplo, importe a `ltsc2019` imagem do Núcleo do Servidor do Windows do `windows` repositório no Registo de Contentores da Microsoft.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importação de um registo de contentores Azure no mesmo inquilino da AD

Você pode importar uma imagem de um registro de contentores Azure no mesmo inquilino AD usando permissões integradas do Azure Ative Directory.

* A sua identidade deve ter permissões do Azure Ative Directory para ler a partir do registo de origem (função do leitor) e para importar para o registo-alvo (função de contribuinte, ou um [papel personalizado](container-registry-roles.md#custom-roles) que permite a ação de importagensImage).

* O registo pode ser na mesma ou numa subscrição Azure diferente no mesmo inquilino ative directory.

* [O acesso público](container-registry-access-selected-networks.md#disable-public-network-access) ao registo de origem pode ser desativado. Se o acesso público for desativado, especifique o registo de origem por ID de recurso em vez de pelo nome do servidor de login do registo.

* Se o registo de origem e/ou o registo alvo tiver uma regra privada de firewall de ponto final ou registo, certifique-se de que o registo restrito permite que [serviços fidedignos](allow-access-trusted-services.md) acedam à rede.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importação de um registo na mesma subscrição

Por exemplo, importar a `aci-helloworld:latest` imagem de uma *origem de origem para* o meu registo para o meu *registo* na mesma subscrição do Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

O exemplo seguinte importa a `aci-helloworld:latest` imagem para *o miogredistria* a partir de uma *miocidância* de registo de origem em que o acesso ao ponto final público do registo é desativado. Forneça o ID de recurso do registo de origem com o `--registry` parâmetro. Note que o `--source` parâmetro especifica apenas o repositório de origem e a etiqueta, e não o nome do servidor de login do registo.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

O exemplo a seguir importa uma imagem por manifesta digest (haxixe SHA-256, representada como `sha256:...` ) em vez de por etiqueta:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importação de um registo numa subscrição diferente

No exemplo seguinte, *a minha origem está* numa subscrição diferente da *miogiária* no mesmo inquilino ative directory. Forneça o ID de recurso do registo de origem com o `--registry` parâmetro. Note que o `--source` parâmetro especifica apenas o repositório de origem e a etiqueta, e não o nome do servidor de login do registo.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importação de um registo utilizando credenciais principais de serviço

Para importar de um registo que não pode aceder usando permissões integradas de Diretório Ativo, pode utilizar credenciais principais de serviço (se disponíveis) para o registo de origem. Forneça o appID e a palavra-passe de um diretor de serviço de [Diretório](container-registry-auth-service-principal.md) Ativo que tenha acesso ACRPull ao registo de origem. A utilização de um principal de serviço é útil para construir sistemas e outros sistemas sem supervisão que precisam de importar imagens para o seu registo.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importação de um registo de contentores Azure em um inquilino de AD diferente

Para importar de um registo de contentores Azure num inquilino diferente do Azure Ative Directory, especifique o registo de origem pelo nome do servidor de login e forneça credenciais de nome de utilizador e palavra-passe que permitam o acesso ao registo. Por exemplo, use um token e senha [com âmbito de repositório,](container-registry-repository-scoped-permissions.md) ou o appID e a palavra-passe de um diretor de [serviço](container-registry-auth-service-principal.md) de Diretório Ativo que tenha acesso ACRPull ao registo de origem. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importação de um registo de contentores privados não-Azure

Importe uma imagem de um registo privado não-Azure especificando credenciais que permitem o acesso ao registo. Por exemplo, retire uma imagem de um registo privado do Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a importação de imagens de contentores para um registo de contentores Azure a partir de um registo público ou outro registo privado. Para opções adicionais de importação de imagem, consulte a referência do comando [de importação az acr.][az-acr-import] 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
