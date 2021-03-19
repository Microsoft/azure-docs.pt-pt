---
title: Atualizar controlador de entrada com Helm
description: Este artigo fornece informações sobre como atualizar uma Entrada de Aplicação Ingress usando Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84806768"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Como atualizar o Controlador de Entrada do Gateway de Aplicação com o Helm 

O Controlador de Entrada de Gateway de Aplicação Azure para Kubernetes (AGIC) pode ser atualizado usando um repositório Helm alojado no Azure Storage.

Antes de iniciarmos o procedimento de upgrade, certifique-se de que adicionou o repositório necessário:

- Veja os seus repositórios Helm atualmente adicionados com:

    ```bash
    helm repo list
    ```

- Adicione o repo AGIC com:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Atualizar

1. Refresque o repositório AGIC Helm para obter o mais recente lançamento:

    ```bash
    helm repo update
    ```

1. Ver versões disponíveis do `application-gateway-kubernetes-ingress` gráfico:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Resposta da amostra:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    A versão mais recente disponível da lista acima é: `0.7.0-rc1`

1. Ver os gráficos Helm atualmente instalados:

    ```bash
    helm list
    ```

    Resposta da amostra:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    A instalação do gráfico Helm a partir da resposta da amostra acima é nomeada `odd-billygoat` . Usaremos este nome para o resto dos comandos. O seu nome de implantação real provavelmente será diferente.

1. Atualize a implementação helm para uma nova versão:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Reversão

Se a implantação do Leme falhar, pode recorrer a uma versão anterior.

1. Obtenha o último número de lançamento saudável conhecido:

    ```bash
    helm history odd-billygoat
    ```

    Resultado do exemplo:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    A partir da saída da amostra do `helm history` comando parece que a última implementação bem sucedida da nossa foi `odd-billygoat` revisão `1`

1. Reversão para a última revisão bem sucedida:

    ```bash
    helm rollback odd-billygoat 1
    ```
