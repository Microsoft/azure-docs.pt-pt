---
title: Verifique a saúde do registo
description: Aprenda a executar um comando de diagnóstico rápido para identificar problemas comuns ao usar um registo de contentores Azure, incluindo a configuração local do Docker e conectividade com o registo
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456402"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Verifique a saúde de um registo de contentores Azure

Ao utilizar um registo de contentores Azure, pode ocasionalmente encontrar problemas. Por exemplo, você pode não ser capaz de puxar uma imagem de recipiente por causa de um problema com Docker no seu ambiente local. Ou, um problema de rede pode impedi-lo de se ligar ao registo. 

Como primeiro passo de diagnóstico, execute o comando de saúde de [az acr][az-acr-check-health] para obter informações sobre a saúde do ambiente e opcionalmente o acesso a um registo-alvo. Este comando está disponível na versão 2.0.67 do Azure CLI ou posterior. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

## <a name="run-az-acr-check-health"></a>Executar az acr check-health

Os exemplos que se seguem mostram diferentes formas de executar o `az acr check-health` comando.

> [!NOTE]
> Se executar o comando em Azure Cloud Shell, o ambiente local não é verificado. No entanto, pode verificar o acesso a um registo-alvo.

### <a name="check-the-environment-only"></a>Verifique apenas o ambiente

Para verificar o daemon local Docker, versão CLI e configuração do cliente Helm, executar o comando sem parâmetros adicionais:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Verifique o ambiente e um registo-alvo

Para verificar o acesso a um registo, bem como efetuar verificações ambientais locais, passe o nome de um registo-alvo. Por exemplo:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Relatório de erros

O comando regista informações para a saída padrão. Se for detetado um problema, fornece um código de erro e descrição. Para obter mais informações sobre os códigos e possíveis soluções, consulte a referência do [erro](container-registry-health-error-reference.md).

Por padrão, o comando para sempre que encontra um erro. Também pode executar o comando de modo a que forneça a saída para todos os controlos de saúde, mesmo que sejam encontrados erros. Adicione `--ignore-errors` o parâmetro, como mostram os seguintes exemplos:

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



## <a name="next-steps"></a>Passos seguintes

Para obter detalhes sobre códigos de erro devolvidos pelo comando de saúde de [verificação az acr,][az-acr-check-health] consulte a referência de erro de verificação de [saúde](container-registry-health-error-reference.md).

Consulte as [FAQ](container-registry-faq.md) para perguntas frequentes e outras questões conhecidas sobre o Registo de Contentores Azure.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
