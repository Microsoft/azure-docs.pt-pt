---
title: Como parar de monitorizar o seu cluster Azure e Red Hat OpenShift v4 | Microsoft Docs
description: Este artigo descreve como pode parar de monitorizar o seu Azure Red Hat OpenShift e red Hat OpenShift version 4 cluster com insights de Contentores.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 09ca05a25ce9bb02b8a3d515acf060e2e9e7e8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731804"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Como parar de monitorizar o seu cluster Azure e Red Hat OpenShift v4

Depois de ativar a monitorização do seu Azure Red Hat OpenShift e do red hat openShift versão 4.x, pode parar de monitorizar o cluster com insights do Contentor se decidir que já não o quer monitorizar. Este artigo mostra como fazê-lo.  

## <a name="how-to-stop-monitoring-using-helm"></a>Como parar a monitorização usando o Helm

1. Para identificar primeiro o lançamento do gráfico de timoneiro de insights do recipiente instalado no seu cluster, executar o seguinte comando de leme.

    ```
    helm list
    ```

    A saída assemelha-se-á ao seguinte:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* representa a versão do gráfico de leme para insights de contentores.

2. Para eliminar a versão da tabela, executar o seguinte comando de leme.

    `helm delete <releaseName>`

    Exemplo:

    `helm delete azmon-containers-release-1`

    Isto removerá a libertação do cluster. Pode verificar com a execução do `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A alteração de configuração pode demorar alguns minutos a ser concluída. Como helm rastreia os seus lançamentos mesmo depois de os ter apagado, pode auditar a história de um cluster, e até mesmo desapafe um lançamento com `helm rollback` .

## <a name="next-steps"></a>Passos seguintes

Se o espaço de trabalho Log Analytics foi criado apenas para suportar a monitorização do cluster e já não é necessário, tem de o eliminar manualmente. Se não estiver familiarizado com a forma de eliminar um espaço de trabalho, consulte [Eliminar um espaço de trabalho Azure Log Analytics](../logs/delete-workspace.md).