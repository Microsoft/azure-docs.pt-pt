---
title: Verificar saúde do registo
description: Aprenda a executar um comando de diagnóstico rápido para identificar problemas comuns ao usar um registo de contentores Azure, incluindo a configuração local do Docker e conectividade com o registo
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: f27a99818260553cbd7ba26158db0064c145a21f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88245388"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Verifique a saúde de um registo de contentores Azure

Ao utilizar um registo de contentores Azure, poderá ocasionalmente encontrar problemas. Por exemplo, pode não conseguir tirar uma imagem de um contentor devido a um problema com o Docker no seu ambiente local. Ou, um problema de rede pode impedi-lo de se ligar ao registo. 

Como primeiro passo de diagnóstico, executar o comando [de check-health az acr][az-acr-check-health] para obter informações sobre a saúde do ambiente e acesso opcional a um registo alvo. Este comando está disponível na versão Azure CLI 2.0.67 ou posterior. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Para obter orientações adicionais de resolução de problemas de registo, consulte:
* [Login de registo de resolução de problemas](container-registry-troubleshoot-login.md)
* [Problemas de rede de resolução de problemas com registo](container-registry-troubleshoot-access.md)
* [Desempenho do registo de resolução de problemas](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Executar az acr check-health

Os exemplos seguintes mostram diferentes formas de executar o `az acr check-health` comando.

> [!NOTE]
> Se executar o comando em Azure Cloud Shell, o ambiente local não é verificado. No entanto, pode verificar o acesso a um registo de destino.

### <a name="check-the-environment-only"></a>Verifique apenas o ambiente

Para verificar a configuração local do daemon Doemon, versão CLI e Helm, executar o comando sem parâmetros adicionais:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Verifique o ambiente e um registo alvo

Para verificar o acesso a um registo, bem como realizar verificações ambientais locais, passe o nome de um registo-alvo. Por exemplo:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Relatório de erros

O comando regista informações para a saída padrão. Se for detetado um problema, fornece um código de erro e uma descrição. Para obter mais informações sobre os códigos e possíveis soluções, consulte a [referência de erro](container-registry-health-error-reference.md).

Por predefinição, o comando para sempre que encontra um erro. Também pode executar o comando de modo a fornecer saída para todos os controlos de saúde, mesmo que se verifiquem erros. Adicione o `--ignore-errors` parâmetro, como mostram os seguintes exemplos:

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

Para obter mais informações sobre os códigos de erro devolvidos pelo comando [de verificação de saúde az acr,][az-acr-check-health] consulte a referência de erro de [verificação de Saúde](container-registry-health-error-reference.md).

Consulte as [FAQ](container-registry-faq.md) para obter perguntas frequentes e outras questões conhecidas sobre o Registo de Contentores Azure.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
