---
title: Verificar estado de funcionamento do registo no Azure Container Registry
description: Saiba como executar um comando de diagnóstico rápido para identificar os problemas comuns quando utilizar um Azure container registry, incluindo a configuração do Docker locais e de conectividade para o registo
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555101"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Verifique o estado de funcionamento de um Azure container registry

Quando utilizar um Azure container registry, ocasionalmente, poderá ter problemas. Por exemplo, poderá não conseguir extrair uma imagem de contentor devido a um problema com o Docker no seu ambiente local. Em alternativa, um problema de rede poderá impedir a ligação ao registo. 

Como uma primeira etapa de diagnóstico, execute o [az acr verificação de estado de funcionamento][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Run az acr check-health

Os exemplos seguintes apresentam diferentes formas para executar o `az acr check-health` comando.

> [!NOTE]
> Se executar o comando no Azure Cloud Shell, o ambiente local não é verificado. No entanto, pode verificar o acesso a um registo de destino.

### <a name="check-the-environment-only"></a>Verificar apenas o ambiente

Para verificar o Docker local daemon, a versão da CLI e a configuração de cliente do Helm, executam o comando sem parâmetros adicionais:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Verificar o ambiente e um registo de destino

Para verificar o acesso a um registo, bem como realizar verificações de ambiente local, passe o nome de um registo de destino. Por exemplo:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Relatório de erros

O comando regista informações para a saída padrão. Se for detetado um problema, fornece um código de erro e a descrição. Para obter mais informações sobre os códigos e possíveis soluções, consulte a [referência de erro](container-registry-health-error-reference.md).

Por predefinição, o comando deixa de sempre que ele encontra um erro. Também pode executar o comando para que ele fornece a saída para todas as verificações de estado de funcionamento, mesmo se forem encontrados erros. Adicionar o `--ignore-errors` parâmetro, conforme mostrado nos exemplos a seguir:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Resultado do exemplo:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Passos Seguintes

Para obter detalhes sobre códigos de erro retornados pela [az acr verificação de estado de funcionamento][az-acr-check-health] comando, consulte a [referência de erro de verificação de estado de funcionamento](container-registry-health-error-reference.md).

Consulte a [FAQ](container-registry-faq.md) para perguntas mais frequentes e outros problemas conhecidos sobre o Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
