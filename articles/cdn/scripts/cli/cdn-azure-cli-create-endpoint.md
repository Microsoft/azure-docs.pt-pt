---
title: Criar um perfil e ponto final da Rede de Entrega de Conteúdos Azure (CDN) utilizando o Azure CLI
description: Scripts de amostra de Azure CLI para criar um perfil Azure CDN, ponto final, grupo de origem, origem e domínio personalizado.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726933"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Criar um perfil E ponto final Azure CDN usando o Azure CLI

Como alternativa ao portal Azure, pode utilizar estas scripts Azure CLI para gerir as seguintes operações cdN:

- Crie um perfil CDN.
- Criar um ponto final da CDN.
- Crie um grupo de origem CDN e faça dele o grupo predefinido.
- Criar uma origem CDN.
- Crie um domínio personalizado e ative HTTPS.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Scripts de exemplo

Se ainda não tiver um grupo de recursos para o seu perfil CDN, crie-o com o `az group create` comando:

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

O seguinte script Azure CLI cria um perfil CDN e ponto final CDN:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

O seguinte script Azure CLI cria um grupo de origem CDN, define o grupo de origem padrão para um ponto final, e cria uma nova origem:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

O seguinte script Azure CLI cria um domínio personalizado CDN e permite HTTPS. Antes de poder associar um domínio personalizado a um ponto final Azure CDN, tem primeiro de criar um registo de nome canónico (CNAME) com o Azure DNS ou o seu fornecedor DENS para apontar para o seu ponto final CDN. Para obter mais informações, consulte [Criar um registo DE DNS CNAME](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar de executar os scripts da amostra, use o seguinte comando para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Comandos Azure CLI usados neste artigo

- [az cdn ponto final criar](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [atualização de ponto final az cdn](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [origem az cdn criar](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az cdn origem-grupo criar](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [perfil az cdn criar](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az cdn domínio personalizado criar](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az cdn domínio personalizado-enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
