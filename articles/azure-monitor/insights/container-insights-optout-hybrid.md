---
title: Como parar de monitorizar o seu cluster híbrido Kubernetes [ Microsoft Docs
description: Este artigo descreve como pode parar de monitorizar o seu cluster híbrido Kubernetes com o Monitor Azure para contentores.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f2f3a8671c1f2baf60d399cc87f2f843dfee4f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196221"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Como parar de monitorizar o seu cluster híbrido

Depois de ativar a monitorização do seu cluster Kubernetes em funcionamento no Azure Stack ou no local, pode parar de monitorizar o cluster com o Monitor Azure para obter contentores se decidir que já não o quer monitorizar. Este artigo mostra como fazer isto.  

## <a name="how-to-stop-monitoring-using-helm"></a>Como parar de monitorizar usando helm

1. Para identificar primeiro o Monitor Azure para a libertação do gráfico de leme dos contentores instalado no seu cluster, execute o seguinte comando de leme.

    ```
    helm list
    ```

    A saída assemelhar-se-á ao seguinte:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Azmon-containers-release-1* representa a versão da tabela de leme para o Monitor Azure para contentores.

2. Para eliminar a versão do gráfico, execute o seguinte comando de leme.

    `helm delete <releaseName>`

    Exemplo:

    `helm delete azmon-containers-release-1`

    Isto removerá a libertação do cluster. Pode verificar executando o `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A mudança de configuração pode demorar alguns minutos a ser completada. Como o Helm rastreia os teus lançamentos mesmo depois de os apagares, podes `helm rollback`auditar o histórico de um cluster e até mesmo apagar uma versão com .

## <a name="next-steps"></a>Passos seguintes

Se o espaço de trabalho log Analytics foi criado apenas para suportar a monitorização do cluster e já não for necessário, tem de o eliminar manualmente. Se não estiver familiarizado com a forma de eliminar um espaço de trabalho, consulte [Delete a Azure Log Analytics workspace](../../log-analytics/log-analytics-manage-del-workspace.md).
