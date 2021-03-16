---
title: O que é Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Visão geral do que pode executar módulos Linux IoT Edge em dispositivos Windows 10
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 330eaf5c12372347917e9f3a4aeafb6a2088c592
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492579"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>O que é o Azure IoT Edge para Linux no Windows (Pré-visualização)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

O Azure IoT Edge para Linux no Windows permite-lhe executar cargas de trabalho linux contentorizadas juntamente com aplicações do Windows em implementações do Windows IoT. As empresas que contam com o Windows IoT para alimentar os seus dispositivos de borda podem agora aproveitar as soluções de análise nativas da nuvem que estão a ser construídas no Linux.

IoT Edge para Linux no Windows funciona executando uma máquina virtual Linux num dispositivo Windows. A máquina virtual Linux vem pré-instalada com o tempo de funcionação IoT Edge. Quaisquer módulos IoT Edge implantados no dispositivo funcionam dentro da máquina virtual. Entretanto, as aplicações do Windows em execução no dispositivo anfitrião do Windows podem comunicar com os módulos em execução na máquina virtual Linux.

[Começa](how-to-install-iot-edge-on-windows.md) com a pré-estreia de hoje.

>[!NOTE]
>Por favor, considere fazer a nossa [pesquisa de Produto](https://aka.ms/AzEFLOW-Registration) para nos ajudar a melhorar o Azure IoT Edge para o Linux no Windows com base no seu fundo e objetivos IoT Edge. Também pode utilizar este inquérito para se inscrever no futuro Azure IoT Edge para Linux em anúncios windows.

## <a name="components"></a>Componentes

O IoT Edge para o Linux no Windows utiliza os seguintes componentes para permitir que as cargas de trabalho do Linux e do Windows corram lado a lado e comuniquem perfeitamente:

* **Uma máquina virtual Linux que executa o Azure IoT Edge**: Uma máquina virtual Linux, baseada no primeiro sistema operativo [Mariner](https://github.com/microsoft/CBL-Mariner) da Microsoft, é construída com o tempo de execução IoT Edge e validada como um ambiente suportado por nível 1 para cargas de trabalho IoT Edge.

* **Windows Admin Center**: Uma extensão IoT Edge para o Windows Admin Center facilita a instalação, configuração e diagnósticos de IoT Edge na máquina virtual Linux. O Windows Admin Center pode implementar ioT Edge para Linux no Windows no dispositivo local, ou pode ligar-se aos dispositivos-alvo e geri-los remotamente.

* **Microsoft Update**: A integração com o Microsoft Update mantém os componentes de tempo de execução do Windows, o Mariner Linux VM e o IoT Edge atualizados.

![O Windows e o Linux VM funcionam em paralelo, enquanto o Windows Admin Center controla ambos os componentes](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

A comunicação bidis entre o processo Windows e a máquina virtual Linux significa que os processos do Windows podem fornecer interfaces de utilizador ou proxies de hardware para cargas de trabalho executadas nos contentores Linux.

## <a name="samples"></a>Amostras

IoT Edge for Linux on Windows enfatiza a interoperabilidade entre os componentes Linux e Windows.

Para amostras que demonstrem comunicação entre aplicações Windows e módulos IoT Edge, consulte [amostras IoT do Windows 10](https://github.com/microsoft/Windows-IoT-Samples).

## <a name="public-preview"></a>Pré-visualização pública

IoT Edge for Linux on Windows está atualmente em [pré-visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Os processos de instalação e gestão podem ser diferentes dos recursos geralmente disponíveis.

## <a name="support"></a>Suporte

Utilize os canais de suporte e feedback IoT Edge para obter assistência com ioT Edge para Linux no Windows.

**Reportando bugs** – Bugs podem ser reportados na página de [problemas](https://github.com/azure/iotedge/issues) do projeto de código aberto IoT Edge. Os bugs relacionados com o Azure IoT Edge para Linux no Windows podem ser reportados na [página de problemas iotedge-eflow](https://github.com/azure/iotedge-eflow/issues).

**Equipa de Apoio ao Cliente da Microsoft** – Os utilizadores que tenham um plano de [suporte](https://azure.microsoft.com/support/plans/) podem envolver a equipa de Suporte ao Cliente da Microsoft criando um bilhete de suporte diretamente do [portal Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Pedidos de funcionalidades** – As faixas de características do produto Azure IoT Edge apresentam pedidos através da página user [Voice](https://feedback.azure.com/forums/907045-azure-iot-edge)do produto .

## <a name="next-steps"></a>Passos seguintes

Watch [IoT Edge for Linux on Windows 10 IoT Enterprise](https://aka.ms/EFLOWPPC9) para mais informações e uma amostra em ação.

Siga os passos de [instalação e provisão Azure IoT Edge para Linux num dispositivo Windows](how-to-install-iot-edge-on-windows.md) para configurar um dispositivo com IoT Edge para Linux no Windows.
