---
title: Upgrade controlador de ingresso com Helm
description: Este artigo fornece informações sobre como atualizar um Gateway Ingress de aplicação usando helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73795891"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Como atualizar o Controlador de Entrada do Gateway de Aplicação com o Helm 

O Controlador de Entrada de Gateway de Aplicações Azure para Kubernetes (AGIC) pode ser atualizado utilizando um repositório Helm hospedado no Armazenamento Azure.

Antes de iniciarmos o procedimento de atualização, certifique-se de que adicionou o repositório necessário:

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

1. Refresque o repositório AGIC Helm para obter o último lançamento:

    ```bash
    helm repo update
    ```

1. Ver ver versões disponíveis do `application-gateway-kubernetes-ingress` gráfico:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Resposta da amostra:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    A versão mais recente disponível da lista acima é:`0.7.0-rc1`

1. Ver as tabelas Helm atualmente instaladas:

    ```bash
    helm list
    ```

    Resposta da amostra:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    A instalação do gráfico Helm `odd-billygoat`a partir da resposta da amostra acima é denominada . Usaremos este nome para o resto dos comandos. O seu nome de implantação real provavelmente será diferente.

1. Atualize a implementação do Helm para uma nova versão:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Recuo

Se a implantação do Leme falhar, pode voltar para uma versão anterior.

1. Obtenha o último número de libertação saudável conhecido:

    ```bash
    helm history odd-billygoat
    ```

    Resultado do exemplo:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Da saída da `helm history` amostra do comando parece que `odd-billygoat` a última implementação bem sucedida da nossa foi a revisão`1`

1. Reversão para a última revisão bem sucedida:

    ```bash
    helm rollback odd-billygoat 1
    ```
