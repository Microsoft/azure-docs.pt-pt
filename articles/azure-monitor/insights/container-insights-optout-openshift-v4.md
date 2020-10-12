---
title: Como parar de monitorizar o seu Azure e Red Hat OpenShift v4 cluster Microsoft Docs
description: Este artigo descreve como pode parar de monitorizar o seu Azure Red Hat OpenShift e red Hat OpenShift série 4 com Azure Monitor para recipientes.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: bf61457b9c8cff40eb3fee2c93c7184fbaae6db5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091152"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Como parar de monitorizar o seu cluster Azure e Red Hat OpenShift v4

Depois de ativar a monitorização do seu Azure Red Hat OpenShift e do red hat OpenShift versão 4.x, pode parar de monitorizar o cluster com o Azure Monitor para contentores se decidir que já não o quer monitorizar. Este artigo mostra como fazê-lo.  

## <a name="how-to-stop-monitoring-using-helm"></a>Como parar a monitorização usando o Helm

1. Para identificar primeiro o Monitor Azure para a libertação do gráfico de leme de contentores instalado no seu cluster, executar o seguinte comando de leme.

    ```
    helm list
    ```

    A saída assemelha-se-á ao seguinte:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* representa a versão do gráfico de leme para o Monitor Azure para contentores.

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

Se o espaço de trabalho Log Analytics foi criado apenas para suportar a monitorização do cluster e já não é necessário, tem de o eliminar manualmente. Se não estiver familiarizado com a forma de eliminar um espaço de trabalho, consulte [Eliminar um espaço de trabalho Azure Log Analytics](../platform/delete-workspace.md).
