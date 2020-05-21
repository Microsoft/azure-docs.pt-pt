---
title: Implementações de Monitor IoT Edge - Borda Azure IoT
description: Monitorização de alto nível, incluindo propriedades de EdgeHub e edgeAgent reportadas e métricas de implementação automáticas.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 97bc3c8571793ec8c8b67fe0e7c5cb3b6a56fde4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726048"
---
# <a name="monitor-iot-edge-deployments"></a>Monitorizar implementações do IoT Edge

O Azure IoT Edge fornece relatórios que permitem monitorizar informações em tempo real sobre os módulos implantados nos seus dispositivos IoT Edge. O serviço IoT Hub recupera o estado dos dispositivos e disponibiliza-os ao operador. A monitorização também é importante para [as implementações efetuadas à escala](module-deployment-monitoring.md) que incluem implantações automáticas e implantações em camadas.

Tanto os dispositivos como os módulos têm dados semelhantes, como a conectividade, pelo que os valores são obtidos de acordo com o ID do dispositivo ou o ID do módulo.

O serviço IoT Hub recolhe dados relatados por gémeos dispositivo e módulo e fornece contagens dos vários estados que os dispositivos podem ter. O serviço IoT Hub organiza estes dados em quatro grupos de métricas:

| Tipo | Descrição |
| --- | ---|
| Direcionado | Mostra os dispositivos IoT Edge que correspondem à condição de alvo de implantação. |
| Aplicado | Mostra os dispositivos IoT Edge direcionados que não são alvo de outra implementação de maior prioridade. |
| Reportagem Sucesso | Mostra os dispositivos IoT Edge que reportaram que os módulos foram implantados com sucesso. |
| Falha de reporte | Mostra os dispositivos IoT Edge que reportaram que um ou mais módulos não foram implantados com sucesso. Para investigar mais aprofundadamente o erro, ligue-se remotamente a esses dispositivos e veja os ficheiros de registo. |

O serviço IoT Hub disponibiliza estes dados para que possa monitorizar no portal Azure e no Azure CLI.

## <a name="monitor-a-deployment-in-the-azure-portal"></a>Monitorize uma implantação no portal Azure

Para ver os detalhes de uma implementação e monitorizar os dispositivos que a executam, utilize os seguintes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.
1. Selecione **IoT Edge** a partir do menu do painel esquerdo.
1. Selecione o separador de **implementações IoT Edge.**
1. Inspecione a lista de destacamento.Para cada implementação, pode ver os seguintes detalhes:

    | Coluna | Descrição |
    | --- | --- |
    | ID | O nome do destacamento. |
    | Tipo | O tipo de implantação, seja **implantação** ou **implantação em camadas**. |
    | Condição-alvo | A etiqueta usada para definir dispositivos direcionados. |
    | Prioridade | O número prioritário atribuído à implantação. |
    | Métricas do sistema | O número de gémeos dispositivos no IoT Hub que correspondem à condição de alvo. **Aplicado** especifica o número de dispositivos que tiveram o conteúdo de implantação aplicado aos seus gémeos módulos no IoT Hub. |
    | Métricas do dispositivo | O número de dispositivos IoT Edge que reportam sucesso ou erros do tempo de execução do cliente IoT Edge. |
    | Métricas Personalizadas | O número de dispositivos IoT Edge que reportam dados para quaisquer métricas que definiu para a implementação. |
    | Tempo de Criação | A marca de tempo a partir de quando a implantação foi criada. Este carimbo de tempo é usado para quebrar laços quando duas implementações têm a mesma prioridade. |

1. Selecione a implementação que pretende monitorizar.  
1. Na página detalhes de **implementação,** desloque-se até à secção inferior e selecione o separador **Condição de Destino.** Selecione **Ver** listar os dispositivos que correspondem à condição de destino. Pode alterar a condição e também a **Prioridade.** Selecione **Guardar** se fizer alterações.

   ![Ver dispositivos direcionados para uma implementação](./media/how-to-monitor-iot-edge-deployments/target-devices.png)

1. Selecione o separador **Métricas.** Se escolher uma métrica a partir do **selectmetric** drop-down, aparece um botão **'Ver'** para que possa apresentar os resultados. Também pode selecionar **Métricas de Edição** para ajustar os critérios para quaisquer métricas personalizadas que tenha definido. Selecione **Guardar** se fizer alterações.

   ![Ver métricas para uma implantação](./media/how-to-monitor-iot-edge-deployments/deployment-metrics-tab.png)


Para efazer alterações na sua implementação, consulte [Modificar uma implementação](how-to-deploy-at-scale.md#modify-a-deployment).

## <a name="monitor-a-deployment-with-azure-cli"></a>Monitorize uma implantação com o Azure CLI

Utilize o comando de [implementação az IoT Edge](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-show) para mostrar os detalhes de uma única implantação:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

O comando do show de implantação toma os seguintes parâmetros:

* **--implantação-id** - O nome da implantação que existe no centro ioT. Parâmetro necessário.
* **--hub-name** - Nome do centro IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando`az account set -s [subscription name]`

Inspecione a colocação na janela de comando.A **metrics** propriedade métrica lista uma contagem para cada métrica que é avaliada por cada hub:

* **targetedCount** - Uma métrica do sistema que especifica o número de gémeos dispositivo no IoT Hub que correspondem à condição de alvo.
* **aplicadaCount** - Uma métrica do sistema especifica o número de dispositivos que tiveram o conteúdo de implementação aplicado aos seus gémeos módulos no IoT Hub.
* **reportedSuccessfulCount** - Uma métrica do dispositivo que especifica o número de dispositivos IoT Edge no sucesso de relatóriode implementação do tempo de execução do cliente IoT Edge.
* **reportedFailedCount** - Uma métrica do dispositivo que especifica o número de dispositivos IoT Edge na falha de reporte de implementação do tempo de execução do cliente IoT Edge.

Pode apresentar uma lista de iDs ou objetos de dispositivo para cada uma das métricas com o comando [de implementação az IoT Edge:](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/edge/deployment?view=azure-cli-latest#ext-azure-iot-az-iot-edge-deployment-show-metric)

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

O comando de implementação mostra-métrica tem os seguintes parâmetros:

* **--implantação-id** - O nome da implantação que existe no centro ioT.
* **--metric-id** - O nome da métrica para a qual pretende ver a lista de iDs do dispositivo, por `reportedFailedCount` exemplo.
* **--hub-name** - Nome do centro IoT em que a implantação existe. O centro deve estar na subscrição atual. Mude para a subscrição desejada com o comando `az account set -s [subscription name]` .

Para efazer alterações na sua implementação, consulte [Modificar uma implementação](how-to-deploy-cli-at-scale.md#modify-a-deployment).

## <a name="next-steps"></a>Próximos passos

Aprenda a [comunicar com o EdgeAgent utilizando métodos diretos incorporados](how-to-edgeagent-direct-method.md).
