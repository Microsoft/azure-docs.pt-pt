---
title: Navegação e história da versão IoT Edge - Azure IoT Edge
description: Descubra as novidades no IoT Edge com informações sobre novas funcionalidades e capacidades nas últimas versões.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cdb84f817f63e6401d17f18319e161f4c3477293
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024658"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Versões Azure IoT Edge e notas de lançamento

Azure IoT Edge é um produto construído a partir do projeto IoT Edge de código aberto hospedado no GitHub. Todos os novos lançamentos são disponibilizados no [projeto Azure IoT Edge.](https://github.com/Azure/azure-iotedge) As contribuições e os relatórios de bugs podem ser feitos no [projeto IoT Edge de código aberto.](https://github.com/Azure/iotedge)

## <a name="documented-versions"></a>Versões documentadas

A documentação IoT Edge neste site está disponível para duas versões diferentes do produto, para que possa escolher o conteúdo que se aplica ao seu ambiente IoT Edge. Atualmente, as duas versões suportadas são:

* **O IoT Edge 1.0.10** abrange todas as funcionalidades e capacidades através da mais recente versão geralmente disponível: [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10).
* **IoT Edge 1.2 (pré-visualização)** contém conteúdo adicional para funcionalidades e capacidades que estão na última versão de [pré-visualização: 1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * Enquanto o IoT Edge 1.2 estiver em pré-visualização, é necessário instalar as versões dos candidatos de lançamento. Para obter mais informações, consulte [offline ou instalação de versão específica](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation).

## <a name="version-history"></a>Histórico de versões

Esta tabela fornece o histórico de versão recente para lançamentos de pacotes IoT Edge e destaca as atualizações de documentação feitas para cada versão.

| Notas de lançamento e ativos | Tipo | Date | Destaques |
| ------------------------ | ---- | ---- | ---------- |
| [1.2-rc1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Pré-visualizar | Novembro de 2020 | [Dispositivos IoT Edge por trás de gateways](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[Corretor MQTT IoT Edge](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Estável | Outubro de 2020 | [Método direto uploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Carregar métricas de tempo de execução](how-to-access-built-in-metrics.md)<br>[Prioridade de rota e tempo a viver](module-composition.md#priority-and-time-to-live)<br>[Pedido de arranque de módulos](module-composition.md#configure-modules)<br>[Fornecimento manual X.509](how-to-manual-provision-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Estável | Março de 2020 | [X.509 auto-provisionamento com DPS](how-to-auto-provision-x509-certs.md)<br>[Método direto RestartModule](how-to-edgeagent-direct-method.md#restart-module)<br>[comando de pacote de apoio](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Próximos passos

* [Ver todos os lançamentos do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)
* [Faça ou reveja os pedidos de recurso no fórum de feedback](https://feedback.azure.com/forums/907045-azure-iot-edge)