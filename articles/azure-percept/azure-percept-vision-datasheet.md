---
title: Folha de dados da Azure Percept Vision
description: Consulte a ficha de dados da Azure Percept Vision para obter especificações detalhadas do dispositivo
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 54dae3341910bf863de7e2a2cef832ae670ca09a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097572"
---
# <a name="azure-percept-vision-datasheet"></a>Folha de dados da Azure Percept Vision

As especificações listadas abaixo são para o dispositivo Azure Percept Vision, incluído no [Azure Percept DK](./azure-percept-dk-datasheet.md).

|Especificação do produto           |Valor     |
|--------------------------------|---------------------|
|Indústrias-Alvo               |Fabrico <br> Edifícios Inteligentes <br> Automático <br> Retail |
|Cenários de Heróis                  |Análise de compradores <br> Disponibilidade na prateleira <br> Redução de redução <br> Monitorização no local de trabalho|
|Dimensões                      |42mm x 42mm x 40mm (Azure Percept Vision SoM conjunto com habitação) <br> 42mm x 42mm x 6mm (chip Vision SoM)|
|Plano de Controlo de Gestão        |Atualização do dispositivo Azure (ADU)          |
|Software e Serviços Suportados |[Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Atualização do dispositivo Azure |
|Aceleração da IA                 |Intel Movidius Myriad X (MA2085) Unidade de Processamento de Visão (VPU) com Intel Camera ISP integrada, 0.7 TOPS |
|Sensores e Indicadores Visuais   |Sensor de câmara Sony IMX219 com lente de 6P<br>Resolução: 8MP a 30FPS, Distância: 50cm - infinito<br>FoV: 120 graus diagonal, Cor: Gama dinâmica larga, fecho de rolamento de foco fixo|
|Suporte à câmara                  |RGB (atualmente) e IR (no futuro) <br> 2 câmaras podem ser executadas simultaneamente |
|Crypto-Controller de segurança      |ST-Micro STM32L462CE      |
|Componente de versão / ID       |64kb EEPROM |
|Memória                          |LPDDR4 2GB     |
|Energia                           |3.5 W     |
|Portas                           |1x USB 3.0 Tipo C <br> 2x MIPI 4 Lane (até 1,5 Gbps por faixa de rodagem)     |
|Interfaces de controlo              |2x I2C <br> 2x SPI <br> 6x PWM (GPIOs: relógio 2x, sincronização de quadro 2x, 2x não reutilizado) <br> 2x GPIO de reserva |
|Certificação                   |FCC <br> IC <br> Rio Rohs <br> ALCANCE <br> RIO UL   |
|Temperatura operacional           |0 a 27 graus C (Azure Percept Vision SoM conjunto com habitação) <br> -10 a 70 graus C (chip Vision SoM) |
|Temperatura do toque               |<= 48 graus C |
|Humidade relativa               |8% a 90%    |
