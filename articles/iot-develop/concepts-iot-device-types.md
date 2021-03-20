---
title: Visão geral dos tipos de dispositivos Azure IoT
description: Aprenda os diferentes tipos de dispositivos suportados pelo Azure IoT e as ferramentas disponíveis.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654370"
---
# <a name="overview-of-azure-iot-device-types"></a>Visão geral dos tipos de dispositivos Azure IoT
Os dispositivos IoT existem através de uma ampla seleção de plataformas de hardware. Existem pequenos MCUs de 8 bits até aos mais recentes CPUs x86, como encontrados num computador de secretária. Muitas variáveis fator na decisão para qual hardware você escolher para um dispositivo IoT e este artigo delineou algumas das principais diferenças.

## <a name="key-hardware-differentiators"></a>Diferenciadores de hardware chave
Alguns fatores importantes na escolha do hardware são o custo, o consumo de energia, o networking e as entradas e saídas disponíveis.

* **Custo:** Dispositivos mais pequenos mais baratos são normalmente utilizados quando produzem em massa o produto final. No entanto, a compensação é que o desenvolvimento do dispositivo pode ser mais caro dado o dispositivo altamente constrangido. O custo de desenvolvimento pode ser distribuído por todos os dispositivos produzidos, pelo que o custo de desenvolvimento por unidade será baixo.

* **Potência:** A quantidade de energia que um dispositivo consome é importante se o dispositivo utilizar pilhas e não estiver ligado à rede elétrica. Os MCUs são muitas vezes projetados para cenários de menor potência e podem ser uma melhor escolha para prolongar a vida útil da bateria.

* **Acesso à rede:** Existem muitas formas de ligar um dispositivo a um serviço de nuvem. Ethernet, Wi-Fi e celular e algumas das opções disponíveis. O tipo de ligação que escolher dependerá do local onde o dispositivo é implantado e da forma como é utilizado. Por exemplo, o telemóvel pode ser uma opção atraente dada a alta cobertura, no entanto para dispositivos de tráfego elevado pode ser caro. O hardwired ethernet fornece custos de dados mais baratos, mas com a desvantagem de ser menos portátil.

* **Entradas e saídas:** As entradas e saídas disponíveis no dispositivo afetam diretamente as capacidades de funcionamento dos dispositivos. Um microcontrolador normalmente terá muitas funções de E/S incorporadas diretamente no chip e fornece uma ampla escolha de sensores para se conectar diretamente.

## <a name="microcontrollers-vs-microprocessors"></a>Microcontroladores vs Microprocessadores
Os dispositivos IoT podem ser separados em duas grandes categorias, microcontroladores (MCUs) e microprocessadores (MPUs).

**Os MCUs** são menos dispendiosos e mais simples de operar do que as MPUs. Um MCU conterá muitas das funções, tais como memória, interfaces e E/S dentro do próprio chip. Um MPU irá retirar esta funcionalidade de componentes em chips de suporte. Um MCU utilizará frequentemente um SISTEMA (RTOS) em tempo real ou executará o metal nu (Sem OS) e fornecerá resposta em tempo real e reações altamente determinísticas a eventos externos.

**As MPUs** geralmente executarão um SO de propósito geral, como Windows, Linux ou MacOSX, que fornecem uma resposta não determinista em tempo real. Normalmente, não há garantias para quando uma tarefa será concluída. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MCU vs MPU":::

Abaixo está uma tabela que mostra algumas das diferenças de definição entre um MCU e um sistema baseado em MPU:

||Microcontrolador (MCU)|Microprocessador (MPU)|
|-|-|-|
|**CPU**| Less | Mais |
|**RAM**| Less | Mais |
|**Flash**| Less | Mais |
|**SO**| Não ou RTOS | Fins Gerais |
|**Dificuldade de Desenvolvimento**| Mais difícil |  Mais fácil |
|**Consumo de Energia**| Mais baixo | Mais alto |
|**Custo**| Mais baixo | Mais alto |
|**Determinístico**| Yes | Não - com exceções|
|**Tamanho do dispositivo**| Menor | Maior |
