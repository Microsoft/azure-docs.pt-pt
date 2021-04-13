---
title: Especificações técnicas e conformidade do Microsoft Azure Stack Edge Mini R| Microsoft Docs
description: Conheça as especificações técnicas e a conformidade com o seu dispositivo Azure Stack Edge Mini R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: b0f3b81ac12d0ae96dde0397bc01a4570d992ea9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310177"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Especificações técnicas Azure Stack Edge Mini R

Os componentes de hardware do seu dispositivo Microsoft Azure Stack Edge Mini R aderem às especificações técnicas e padrões regulamentares descritos neste artigo. As especificações técnicas descrevem o CPU, a memória, as unidades de alimentação (PSUs), a capacidade de armazenamento, as dimensões do recinto e o peso.


## <a name="compute-memory-specifications"></a>Computação, especificações de memória

O dispositivo Azure Stack Edge Mini R tem as seguintes especificações para cálculo e memória:

| Especificação           | Valor                           |
|-------------------------|---------------------------------|
| Tipo CPU                | Intel Xeon-D 1577               |
| CPU: cru                | 16 núcleos totais, 32 vCPUs totais  |
| CPU: utilizável             | 24 vCPUs                        |
| Tipo de memória             | 16 GB 2400 MT/s SODIMM          |
| Memória: crua             | 48 GB DE RAM (3 x 16 GB)           |
| Memória: utilizável          | 32 GB de RAM                       |


## <a name="compute-acceleration-specifications"></a>Especificações de aceleração computacional

Uma Unidade de Processamento de Visão (VPU) está incluída em todos os dispositivos Azure Stack Edge Mini R que permite kubernetes, aplicações baseadas em rede neural profunda e aplicações baseadas em visão computacional.

| Especificação             | Valor                  |
|---------------------------|------------------------|
| Cartão de aceleração computacional | Intel Movidius Myriad X VPU <br> Para mais informações, consulte [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX) |


## <a name="storage-specifications"></a>Especificações de armazenamento

O dispositivo Azure Stack Edge Mini R tem 1 disco de dados e 1 disco de arranque (que serve como armazenamento do sistema operativo). A tabela seguinte mostra os detalhes da capacidade de armazenamento do dispositivo.

|     Especificação                          |     Valor                                              |
|--------------------------------------------|--------------------------------------------------------|
|    Número de unidades de estado sólido (SSDs)     |    2 discos X 1 TB <br> Um disco de dados e um disco de arranque |
|    Capacidade SSD única                     |    1 TB                                                |
|    Capacidade total (apenas dados)              |    1 TB                                                |
|    Capacidade total utilizável*                  |    ~ 750 GB                                            |

**Algum espaço está reservado para uso interno.*

## <a name="network-specifications"></a>Especificações de rede

O dispositivo Azure Stack Edge Mini R tem as seguintes especificações para a rede:


|Especificação  |Valor  |
|---------|---------|
|Interfaces de rede    |2 x 10 GbE SFP+ <br> Apresentado como PORT 3 e PORT 4 na UI local           |
|Interfaces de rede    |2 x 1 GbE RJ45 <br> Apresentado como PORT 1 e PORT 2 na UI local          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>Especificações da unidade de alimentação

O dispositivo Azure Stack Edge Mini R inclui um adaptador ac externo de 85 W para alimentar e carregar a bateria de bordo.

O quadro a seguir mostra as especificações da unidade de alimentação:

| Especificação           | Valor                      |
|-------------------------|----------------------------|
| Potência máxima de saída    | 85 W                       |
| Frequência               | 50/60 Hz                   |
| Seleção de gama de tensão | Variação automática: 100-240 V AC |



## <a name="included-battery"></a>Bateria incluída

O dispositivo Azure Stack Edge Mini R também inclui uma bateria a bordo que é carregada pela fonte de alimentação.

Uma [bateria adicional do Tipo 2590](https://www.bren-tronics.com/bt-70791ck.html) pode ser utilizada em conjunto com a bateria de bordo para prolongar a utilização do dispositivo entre as cargas. Esta bateria deve estar em conformidade com todas as normas de segurança, transporte e ambiente aplicáveis no país de utilização.


| Especificação            | Valor                      |
|--------------------------|----------------------------|
| Capacidade da bateria a bordo | 73 Wh                     |

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do recinto e especificações de peso

As tabelas a seguir enumeram as várias especificações do recinto para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do recinto

A tabela que se segue lista as dimensões do dispositivo e da USP com a caixa acidentada em milímetros e polegadas.

|     Recinto     |     Milímetros     |     Centímetros     |
|-------------------|---------------------|----------------|
|    Height         |    68               |    2.68        |
|    Width          |    208              |      8.19      |
|    Comprimento          |   259              |    10.20       |


### <a name="enclosure-weight"></a>Peso do recinto

A tabela que se segue lista o peso do dispositivo, incluindo a bateria.

|     Recinto                     |     Peso          |
|-----------------------------------|---------------------|
|    Peso total do dispositivo     |     7 libras           |

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente do recinto


Esta secção enumera as especificações relacionadas com o ambiente do recinto, tais como temperatura, humidade e altitude.


|     Especificações             |     Description                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     Intervalo de temperatura          |     0 - 43° C (operacional)                                              |
|     Vibração                  |     Método MIL-STD-810 514.7*<br> Procedimento I CAT 4, 20                  |
|     Choque                      |     Método MIL-STD-810 516.7*<br> Procedimento IV, Logística                 |
|     Altitude                   |     Operacional: 10.000 pés<br> Não operacional: 40.000 pés          |

**Todas as referências são para MIL-STD-810G Alteração 1 (2014)*


## <a name="next-steps"></a>Passos seguintes

- [Implemente o seu Azure Stack Edge Mini R](azure-stack-edge-placeholder.md)
