---
title: Especificações técnicas e conformidade do Microsoft Azure Stack Edge Pro Microsoft Docs
description: Conheça as especificações técnicas e a conformidade com o Azure Stack Edge Pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 8b36bb34f4c9081d807998cb8287797443625a6b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460304"
---
# <a name="azure-stack-edge-pro-technical-specifications"></a>Especificações técnicas Azure Stack Edge Pro

Os componentes de hardware do seu dispositivo Microsoft Azure Stack Edge Pro aderem às especificações técnicas e padrões regulamentares descritos neste artigo. As especificações técnicas descrevem as unidades de alimentação de energia (PSUs), capacidade de armazenamento, recintos e normas ambientais.

## <a name="compute-memory-specifications"></a>Computação, especificações de memória

O dispositivo Azure Stack Edge Pro tem as seguintes especificações para cálculo e memória:

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| CPU    | 2 x 10 núcleo CPU Intel Xeon Silver 4114 2.2G                    |
| Memória              | RAM de 128 GB (8x 16GB RDIMM)                 |

## <a name="fpga-specifications"></a>Especificações da FPGA

Um Field Programmable Gate Array (FPGA) está incluído em todos os dispositivos Azure Stack Edge Pro que permite cenários de Machine Learning (ML).

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Os modelos disponíveis da Rede Neural Profunda (DNN) são os mesmos que os [suportados por instâncias FPGA em nuvem](../machine-learning/how-to-deploy-fpga-web-service.md#fpga-support-in-azure).|

## <a name="power-supply-unit-specifications"></a>Especificações da unidade de alimentação

O dispositivo Azure Stack Edge Pro tem duas unidades de alimentação de 100-240 V (PSUs) com ventiladores de alto desempenho. As duas PSUs fornecem uma configuração de potência redundante. Se uma PSU falhar, o dispositivo continua a funcionar normalmente na outra PSU até que o módulo falhado seja substituído. A tabela que se segue enumera as especificações técnicas das PSUs.

| Especificação           | 750 W PSU                  |
|-------------------------|----------------------------|
| Potência máxima de saída    | 750 W                     |
| Frequência               | 50/60 Hz                   |
| Seleção de gama de tensão | Variação automática: 100-240 V AC |
| Pluggável quente           | Sim                        |

### <a name="azure-stack-edge-pro-power-cord-specifications-by-region"></a>Especificações do cabo de alimentação Azure Stack Edge Pro por região

O seu dispositivo Azure Stack Edge Pro necessita de um cabo de alimentação que varia consoante a sua região Azure.
Para especificações técnicas de todos os cabos de alimentação suportados, consulte [as especificações do cabo de alimentação Azure Stack Edge Pro por região](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge Pro device.-->

## <a name="network-interface-specifications"></a>Especificações de interface de rede

O seu dispositivo Azure Stack Edge Pro tem 6 interfaces de rede, PORT1-PORT6.

| Especificação           | Descrição                 |
|-------------------------|----------------------------|
|  Interfaces de rede    | 2 interfaces de 1 GbE – 1 de gestão, não configurável pelo utilizador, utilizada para a configuração inicial. A outra interface é configurável pelo utilizador, pode ser usada para transferência de dados, e é DHCP por padrão. <br>2 interfaces de 25 GbE – também podem funcionar como interfaces de 10 GbE. Estas interfaces de dados podem ser configuradas pelo utilizador como DHCP (predefinição) ou estáticas. <br> 2 interfaces de 25 GbE - estas interfaces de dados podem ser configuradas pelo utilizador como DHCP (predefinição) ou estáticas.                  |

Os adaptadores de rede utilizados são: 

| Especificação           | Descrição                 |
|-------------------------|----------------------------|
|Cartão filha de rede (rNDC) |QLogic FastLinQ 41264 Dual Port 25GbE SFP+, Dual Port 1GbE, rNDC|
|Adaptador de rede PCI |QLogic FastLinQ 41262 zwei Ports 25Gbit/s SFP28 Adaptador|

Consulte a Lista de Compatibilidade de Hardware da Intel QLogic para obter um Conversor de Interface Gigabit compatível (GBIC). O Conversor de Interface Gigabit (GBIC) não está incluído na entrega do Azure Stack Edge. 

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos Azure Stack Edge Pro têm SSDS NVMe de 9 X 2,5", cada um com uma capacidade de 1.6 TB. Destes SSDs, 1 é um disco do sistema operativo, e os outros 8 são discos de dados. A capacidade total utilizável para o dispositivo é de aproximadamente 12,5 TB. A tabela seguinte tem os detalhes para a capacidade de armazenamento do dispositivo.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de unidades de estado sólido (SSDs)     |    8                  |
|    Capacidade SSD única                     |    1.6 TB             |
|    Capacidade total                          |    12.8 TB            |
|    Capacidade total utilizável*                  |    ~ 12,5 TB            |

**Algum espaço está reservado para uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do recinto e especificações de peso

As tabelas a seguir enumeram as várias especificações do recinto para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do recinto

A tabela que se segue lista as dimensões do recinto em milímetros e polegadas.

|     Recinto     |     Milímetros     |     Centímetros     |
|-------------------|---------------------|----------------|
|    Height         |    44.45            |    1.75"          |
|    Width          |    434.1           |    17.09"          |
|    Comprimento          |    740.4           |    29.15"          |

A tabela que se segue lista as dimensões do pacote de transporte em milímetros e polegadas.

|     Pacote     |     Milímetros     |     Centímetros     |
|-------------------|---------------------|----------------|
|    Height         |    311.2            |    12.25"          |
|    Width          |    642.8          |    25.31"          |
|    Comprimento          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Peso do recinto

A embalagem do dispositivo pesa 61 libras. e requer duas pessoas para lidar com isto. O peso do dispositivo depende da configuração do recinto.

|     Recinto                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total, incluindo a embalagem       |    61 libras.          |
|    Peso do dispositivo                       |    35 libras.          |

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente do recinto

Esta secção enumera as especificações relacionadas com o ambiente do recinto, tais como temperatura, humidade e altitude.

### <a name="temperature-and-humidity"></a>Temperatura e humidade

|     Recinto         |     Intervalo de temperatura ambiente     |     Humidade relativa ambiente     |     Ponto de orvalho máximo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operacional        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% não-condensação.         |    29°C (84°F)            |
|    Não operacional    |    -40°C a 65°C (-40°F - 149°F)     |    5% - 95% não condensando.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

|     Recinto                           |     Especificações operacionais                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Fluxo de ar                              |    O fluxo de ar do sistema é frontal para a traseira. O sistema deve ser acionado com uma instalação de exaustão traseira de baixa pressão. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitude máxima, operacional        |    3048 metros (10.000 pés) com temperatura máxima de funcionamento desapurado determinada pelas [especificações de desins classificação da temperatura de funcionamento](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitude máxima, não operacional    |    12.000 metros (39.370 pés)                                                                                                                                                                                         |
|    Choque, operacional                   |    6 G para 11 milissegundos em 6 orientações                                                                                                                                                                         |
|    Choque, não operacional               |    71 G por 2 milissegundos em 6 orientações                                                                                                                                                                           |
|    Vibração, operacional               |    0,26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatoriamente                                                                                                                                                                                     |
|    Vibração, não operacional           |    1.88 G<sub>RMS</sub> 10 Hz a 500 Hz durante 15 minutos (todos os seis lados testados.)                                                                                                                                                  |
|    Orientação e montagem             |    Montagem de cremalheira de 19"                                                                                                                                                                                        |
|    Segurança e aprovações                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Regulamento da Comissão (UE) Nº. 617/2013                                                                                                                                                                                        |
|    Rio Rohs           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Especificações de desadição da temperatura de funcionamento

|     Desa classificação da temperatura de funcionamento     |     Intervalo de temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Até 35°C (95°F)                       |    A temperatura máxima é reduzida em 1°C/300 m (1°F/547 ft) acima de 950 m (3.117 pés).    |
|    35°C a 40°C (95°F a 104°F)            |    A temperatura máxima é reduzida em 1°C/175 m (1°F/319 pés) acima de 950 m (3.117 pés).    |
|    40°C a 45°C (104°F a 113°F)           |    A temperatura máxima é reduzida em 1°C/125 m (1°F/228 ft) acima de 950 m (3.117 pés).    |

## <a name="next-steps"></a>Passos seguintes

- [Implemente o seu Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)