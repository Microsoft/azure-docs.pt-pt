---
title: Utilize o IoT Plug e reprodução em dispositivos constrangidos | Microsoft Docs
description: Saiba como pode implementar IoT Plug e Play em dispositivos constrangidos utilizando o SDK para Embedded C ou Azure RTOS.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cfa71e02688d5bf63d894ae42dabab7da1d4174c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057319"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementar IoT Plug e Reproduzir em dispositivos constrangidos

Se estiver a desenvolver *dispositivos constrangidos,* pode utilizar o IoT Plug e o Play com o [Azure SDK para bibliotecas de clientes C IoT incorporados](https://aka.ms/embeddedcsdk) ou com [o Azure RTOS](/azure/rtos/overview-rtos). Este artigo inclui links e recursos para estes cenários constrangidos.

## <a name="use-the-sdk-for-embedded-c"></a>Utilize o SDK para Embedded C

O SDK for Embedded C oferece uma solução leve para ligar dispositivos constrangidos aos serviços Azure IoT, incluindo a utilização das [convenções IoT Plug and Play](concepts-convention.md). Os seguintes links incluem amostras para dispositivos reais e para fins educacionais e depuração.

### <a name="use-a-real-device"></a>Utilizar um dispositivo real

Para um tutorial completo de ponta a ponta utilizando o SDK para Embedded C, o Serviço de Provisionamento de Dispositivos e IoT Plug e Play num dispositivo real, consulte [a Repurpose PIC-IoT Wx Development Board para ligar ao Azure através do Serviço de Provisionamento de Dispositivos IoT Hub](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Amostras introdutórias

O repositório SDK para incorporado C contém [várias amostras](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) que mostram como usar ioT Plug e Play:

> [!NOTE]
> Estas amostras são mostradas em funcionamento no Windows e Linux para fins educacionais e depurantes. Num cenário de produção, as amostras destinam-se apenas a dispositivos constrangidos.

- [Exemplo do termóstato com SDK para Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Exemplo do Controlador de Temperatura com o SDK para Incorporado C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Utilização de RTOS Azure

O Azure RTOS inclui uma camada leve que adiciona conectividade nativa aos serviços de nuvem Azure IoT. Esta camada fornece um mecanismo simples para ligar dispositivos constrangidos ao Azure IoT enquanto utiliza as funcionalidades avançadas do Azure RTOS. Para saber mais, consulte o [RtOS do Microsoft Azure.](/azure/rtos/overview-rtos)

### <a name="toolchains"></a>Poltronas de ferramentas

As amostras Azure RTOS são fornecidas com diferentes tipos de combinações de IDE e de ferramentas, tais como:

- IAR: IAR's [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake: Construa em cima do sistema de construção [CMake](https://cmake.org/) de código aberto e [na gnu arm embedded toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) da NXP
- STM32Cube: [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html) da STMicroelectronic
- MPLAB: [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide) do Microchip

### <a name="samples"></a>Amostras

Para amostras que lhe mostrem como começar em diferentes dispositivos com Azure RTOS e IoT Plug and Play, consulte a seguinte tabela:

Fabricante | Dispositivo | Amostras |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu sobre as opções de implementação do IoT Plug e Play em dispositivos constrangidos, um passo sugerido é aprender sobre as [convenções IoT Plug and Play](concepts-convention.md).