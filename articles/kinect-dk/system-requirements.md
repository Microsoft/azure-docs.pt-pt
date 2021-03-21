---
title: Requisitos do sistema SDK do sensor Azure Kinect
description: Compreenda os requisitos do sistema para o Azure Kinect Sensor SDK no Windows e Linux.
author: qm13
ms.author: quentinm
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: azul, cinect, requisitos do sistema, CPU, GPU, USB, configuração, mínimo, requisitos
ms.openlocfilehash: 558c1b9ca264874fa808aeba5f1e8a809a8f722d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656979"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Requisitos do sistema SDK sensor Azure Kinect

Este documento fornece detalhes sobre os requisitos do sistema necessários para instalar o sensor SDK e implementar com sucesso o seu Azure Kinect DK.

## <a name="supported-operating-systems-and-architectures"></a>Sistemas operativos e arquiteturas suportados

- Windows 10 abril 2018 (Versão 1803, OS Build 17134) ou uma versão posterior
- Linux Ubuntu 18.04 (x64), com um piloto de GPU que usa OpenGLv4.4 ou uma versão posterior

O Sensor SDK está disponível para o Windows API (Win32) para aplicações nativas do Windows C/C++. O SDK não está atualmente disponível para aplicações UWP. O Azure Kinect DK não é suportado para o Windows 10 no modo S.

## <a name="development-environment-requirements"></a>Requisitos ambientais de desenvolvimento

Para contribuir para o desenvolvimento do sensor SDK, visite [o GitHub.](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK)

## <a name="minimum-host-pc-hardware-requirements"></a>Requisitos mínimos de hardware para PC de anfitrião

O requisito de hardware do anfitrião do PC depende da aplicação/algoritmo/taxa de fotogramas/resolução executada no PC anfitrião. A configuração mínima recomendada do Sensor SDK para windows é:

- Sétimo processador Intel &reg; CoreTM i3 (Dual Core 2.4 GHz com GPU HD620 ou mais rápido)
- 4 GB memória
- Porta USB3 dedicada
- Suporte do controlador gráfico para OpenGL 4.4 ou DirectX 11.0

Os CPUs inferiores ou mais antigos também podem funcionar dependendo da sua caixa de utilização.

O desempenho difere também entre os sistemas operativos Windows/Linux e os controladores gráficos em uso.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Requisitos de hardware de pc de rastreio de corpo

O requisito do anfitrião do PC de rastreio do corpo é mais rigoroso do que o requisito geral do anfitrião do PC. A configuração SDK de rastreio mínimo de corpo recomendada para windows é:

- Sétimo processador Intel &reg; CoreTM i5 (Quad Core 2.4 GHz ou mais rápido)
- 4 GB memória
- NVIDIA GEFORCE GTX 1050 ou equivalente
- Porta USB3 dedicada

A configuração mínima recomendada pressupõe K4A_DEPTH_MODE_NFOV_UNBINNED modo de profundidade a 30fps rastreando 5 pessoas. CpUs inferiores ou mais antigos e GPUs NVIDIA também podem funcionar dependendo da sua caixa de uso.

## <a name="usb3"></a>USB3

Existem problemas de compatibilidade conhecidos com controladores USB Host. Pode encontrar mais informações na [página troubleshooting](troubleshooting.md#usb3-host-controller-compatibility)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurar o rastreio do corpo de Azure Kinect](body-sdk-setup.md)
