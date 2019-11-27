---
title: Importar imagens de contentor
description: Importe imagens de contêiner para um registro de contêiner do Azure usando as APIs do Azure, sem a necessidade de executar comandos do Docker.
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e649447d7b9280dbebef1ae332c1f25910f5a516
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456300"
---
# <a name="import-container-images-to-a-container-registry"></a>Importar imagens de contêiner para um registro de contêiner

Você pode facilmente importar (copiar) imagens de contêiner para um registro de contêiner do Azure, sem usar comandos do Docker. Por exemplo, importe imagens de um registro de desenvolvimento para um registro de produção ou copie imagens base de um registro público.

O registro de contêiner do Azure lida com vários cenários comuns para copiar imagens de um registro existente:

* Importar de um registro público

* Importar de outro registro de contêiner do Azure, na mesma assinatura do Azure ou em outra

* Importar de um registro de contêiner privado não Azure

A importação de imagem em um registro de contêiner do Azure tem os seguintes benefícios em relação ao uso de comandos da CLI do Docker:

* Como seu ambiente de cliente não precisa de uma instalação local do Docker, importe qualquer imagem de contêiner, independentemente do tipo de sistema operacional com suporte.

* Quando você importa imagens de várias arquiteturas (como imagens oficiais do Docker), as imagens de todas as arquiteturas e plataformas especificadas na lista de manifestos são copiadas.

Para importar imagens de contêiner, este artigo requer que você execute o CLI do Azure em Azure Cloud Shell ou localmente (versão 2.0.55 ou posterior recomendada). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli].

> [!NOTE]
> Se você precisar distribuir imagens de contêiner idênticas em várias regiões do Azure, o registro de contêiner do Azure também dará suporte à [replicação geográfica](container-registry-geo-replication.md). Ao replicar geograficamente um registro (SKU Premium necessário), você pode atender a várias regiões com nomes de imagem e marca idênticos de um único registro.
>

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tiver um registro de contêiner do Azure, crie um registro. Para obter as etapas, consulte [início rápido: criar um registro de contêiner privado usando o CLI do Azure](container-registry-get-started-azure-cli.md).

Para importar uma imagem para um registro de contêiner do Azure, sua identidade deve ter permissões de gravação para o registro de destino (pelo menos a função de colaborador). Consulte [funções e permissões do registro de contêiner do Azure](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importar de um registro público

### <a name="import-from-docker-hub"></a>Importar do Hub do Docker

Por exemplo, use o comando [AZ ACR Import][az-acr-import] para importar a imagem `hello-world:latest` de várias arquiteturas do Hub do Docker para um registro chamado *myregistry*. Como `hello-world` é uma imagem oficial do Hub do Docker, essa imagem está no repositório de `library` padrão. Inclua o nome do repositório e, opcionalmente, uma marca no valor do parâmetro `--source` Image. (Opcionalmente, você pode identificar uma imagem por seu resumo de manifesto em vez de por marca, o que garante uma versão específica de uma imagem.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

Você pode verificar se vários manifestos estão associados a essa imagem executando o comando `az acr repository show-manifests`:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

O exemplo a seguir importa uma imagem pública do repositório `tensorflow` no Hub do Docker:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importar do registro de contêiner da Microsoft

Por exemplo, importe a imagem mais recente do Windows Server Core do repositório `windows` no registro de contêiner da Microsoft.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importar de outro registro de contêiner do Azure

Você pode importar uma imagem de outro registro de contêiner do Azure usando permissões de Azure Active Directory integradas.

* Sua identidade deve ter Azure Active Directory permissões para ler o registro de origem (função de leitor) e gravar no registro de destino (função de colaborador).

* O registro pode estar no mesmo ou em uma assinatura diferente do Azure no mesmo locatário Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importar de um registro na mesma assinatura

Por exemplo, importe a imagem de `aci-helloworld:latest` de um registro de origem *mysourceregistry* para *myregistry* na mesma assinatura do Azure.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

O exemplo a seguir importa uma imagem por Resumo de manifesto (hash SHA-256, representado como `sha256:...`) em vez de por marca:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importar de um registro em uma assinatura diferente

No exemplo a seguir, *mysourceregistry* está em uma assinatura diferente de *myregistry* no mesmo locatário de Active Directory. Forneça a ID de recurso do registro de origem com o parâmetro `--registry`. Observe que o parâmetro `--source` especifica apenas o repositório de origem e o nome da imagem, não o nome do servidor de logon do registro.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importar de um registro usando credenciais da entidade de serviço

Para importar de um registro que você não pode acessar usando permissões de Active Directory, você pode usar as credenciais da entidade de serviço (se disponível). Forneça a appID e a senha de uma [entidade de serviço](container-registry-auth-service-principal.md) Active Directory que tenha acesso ACRPull ao registro de origem. Usar uma entidade de serviço é útil para sistemas de compilação e outros sistemas autônomos que precisam importar imagens para o registro.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importar de um registro de contêiner privado não Azure

Importe uma imagem de um registro particular especificando as credenciais que habilitam o acesso de pull ao registro. Por exemplo, extraia uma imagem de um registro do Docker privado: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre a importação de imagens de contêiner para um registro de contêiner do Azure de um registro público ou outro registro privado. Para obter opções de importação de imagem adicionais, consulte a referência do comando [AZ ACR Import][az-acr-import] . 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
