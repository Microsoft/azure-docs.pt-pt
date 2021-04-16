---
title: Monitor IoT Edge implementa - Azure IoT Edge
description: Monitorização de alto nível, incluindo edgeHub e edgeAgent, reportaram propriedades e métricas de implementação automática.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 39e7bb5c151d490e79ef111589f52f260c3e6c7a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483168"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorizar implementações do IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

O Azure IoT Edge fornece relatórios que lhe permitem monitorizar informações em tempo real sobre os módulos implantados nos seus dispositivos IoT Edge. O serviço IoT Hub recupera o estado dos dispositivos e coloca-os à disposição do operador. A monitorização também é importante para [implementações feitas em escala](module-deployment-monitoring.md) que incluem implementações automáticas e implementações em camadas.

Ambos os dispositivos e módulos têm dados semelhantes, como a conectividade, pelo que os valores são obtidos de acordo com o ID do dispositivo ou o ID do módulo.

O serviço IoT Hub recolhe dados relatados por gémeos de dispositivos e módulos e fornece contagens dos vários estados que os dispositivos podem ter. O serviço IoT Hub organiza estes dados em quatro grupos de métricas:

| Tipo | Description |
| --- | ---|
| Direcionado | Mostra os dispositivos IoT Edge que correspondem à condição de alvo de implantação. |
| Aplicado | Mostra os dispositivos IoT Edge direcionados que não são alvo de outra implementação de maior prioridade. |
| Reportagem de sucesso | Mostra os dispositivos IoT Edge que relataram que os módulos foram implantados com sucesso. |
| Falha de reporte | Mostra os dispositivos IoT Edge que relataram que um ou mais módulos não foram implementados com sucesso. Para investigar mais aprofundadamente o erro, ligue-se remotamente a esses dispositivos e veja os ficheiros de registo. |

O serviço IoT Hub disponibiliza estes dados para que possa monitorizar no portal Azure e no Azure CLI.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorize uma implantação no portal Azure

Para visualizar os detalhes de uma implantação e monitorizar os dispositivos que o executam, utilize os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge** no menu do painel esquerdo.
1. Selecione o **separador IoT Edge implementações.**
1. Inspecione a lista de desdobramentos. Para cada implantação, pode ver os seguintes detalhes:

    | Coluna | Description |
    | --- | --- |
    | ID | O nome da implantação. |
    | Tipo | O tipo de implantação, **ou implantação** ou **implantação em camadas**. |
    | Condição alvo | A etiqueta usada para definir dispositivos direcionados. |
    | Prioridade | O número prioritário atribuído à implantação. |
    | Métricas do sistema | O número de gémeos do dispositivo no IoT Hub que correspondem à condição de alvo. **Aplicada** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos gémeos do módulo no IoT Hub. |
    | Métricas do Dispositivo | O número de dispositivos IoT Edge que reportam sucesso ou erros do tempo de funcionaamento do cliente IoT Edge. |
    | Métricas Personalizadas | O número de dispositivos IoT Edge que reportam dados para quaisquer métricas que definiu para a implementação. |
    | Tempo da Criação | A data de tempo de quando a implantação foi criada. Esta estampação de tempo é usada para quebrar laços quando duas implementações têm a mesma prioridade. |

1. Selecione a implementação que pretende monitorizar.  
1. Na página **'Detalhes de implementação',** desloque-se para a secção inferior e selecione o **separador 'Estado alvo'.** Selecione **Ver** para listar os dispositivos que correspondem à condição alvo. Pode alterar a condição e também a **Prioridade.** **Selecione Guardar** se escoou alterações.

   ![Ver dispositivos direcionados para uma implementação](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Selecione o **separador Métricas.** Se escolher uma métrica a partir do **drop-down métrico Select,** aparece um botão **'Visualizar',** para visualizar os resultados. Também pode selecionar **Editar Métricas** para ajustar os critérios para quaisquer métricas personalizadas que tenha definido. **Selecione Guardar** se escoou alterações.

   ![Ver métricas para uma implantação](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)

Para escoar alterações na sua implementação, consulte [Modificar uma implementação](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorize uma implantação com O Azure CLI

Utilize o comando [de exibição de arestas az iot](/cli/azure/iot/edge/deployment) para mostrar os detalhes de uma única implantação:

```azurecli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

O comando do show de implantação toma os seguintes parâmetros:

* **...-id-implantação -** O nome da implantação que existe no hub IoT. Parâmetro necessário.
* **--hub-name** - Nome do hub IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]`

Inspecione a colocação na janela de comando. A propriedade **métrica** lista uma contagem para cada métrica que é avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de gémeos do dispositivo no IoT Hub que correspondem à condição de alvo.
* **appliedCount** - Uma métrica do sistema especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos gémeos do módulo no IoT Hub.
* **reportedSuccessfulCount** - Uma métrica do dispositivo que especifica o número de dispositivos IoT Edge na implementação reportando o sucesso do tempo de execução do cliente IoT Edge.
* **reportedFailedCount** - Uma métrica do dispositivo que especifica o número de dispositivos IoT Edge na falha de reporte de implementação do tempo de funcionamento do cliente IoT Edge.

Pode apresentar uma lista de IDs ou objetos do dispositivo para cada uma das métricas com o comando [métrico de exibição de borda az iot:](/cli/azure/iot/edge/deployment)

```azurecli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

O comando show-metric de implantação requer os seguintes parâmetros:

* **...-id-implantação -** O nome da implantação que existe no hub IoT.
* **--id métrica** - O nome da métrica para a qual pretende ver a lista de identificações do dispositivo, por exemplo `reportedFailedCount` .
* **--hub-name** - Nome do hub IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]` .

Para escoar alterações na sua implementação, consulte [Modificar uma implementação](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Passos seguintes

Aprenda a [monitorizar os gémeos módulos](how-to-monitor-module-twins.md), principalmente os módulos de execução IoT Edge Agent e IoT Edge Hub, para a conectividade e saúde das suas implementações IoT Edge.
