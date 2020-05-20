---
title: Especificações técnicas e conformidade do Microsoft Azure Stack Edge Microsoft Docs
description: Conheça as especificações técnicas e a conformidade com o seu Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 5a4ac2342ca36d83d1a579851c090c4713814c8d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652050"
---
# <a name="azure-stack-edge-technical-specifications"></a>Especificações técnicas azure Stack Edge

Os componentes de hardware do seu dispositivo Microsoft Azure Stack Edge aderem às especificações técnicas e normas regulamentares descritas neste artigo. As especificações técnicas descrevem as unidades de alimentação (OP), capacidade de armazenamento, recintos e normas ambientais.

## <a name="compute-memory-specifications"></a>Compute, especificações de memória

O dispositivo Azure Stack Edge tem as seguintes especificações para computação e memória:

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 CPU central                     |
| Memória              | 128 GB de RAM                  |

## <a name="fpga-specifications"></a>Especificações da FPGA

Um field programmable gate Array (FPGA) está incluído em todos os dispositivos Azure Stack Edge que permite cenários de Machine Learning (ML).

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Os modelos disponíveis da Deep Neural Network (DNN) são os mesmos que [suportados por instâncias fPGA em nuvem.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)|

## <a name="power-supply-unit-specifications"></a>Especificações da unidade de alimentação de energia

O dispositivo Azure Stack Edge tem duas unidades de alimentação 100-240 V (PSUs) com ventiladores de alto desempenho. As duas PSUs fornecem uma configuração de potência redundante. Se uma PSU falhar, o dispositivo continua a funcionar normalmente na outra PSU até que o módulo falhado seja substituído. A tabela que se segue enumera as especificações técnicas das OP.

| Especificação           | 750 W PSU                  |
|-------------------------|----------------------------|
| Potência máxima de saída    | 750 W                     |
| Frequência               | 50/60 Hz                   |
| Seleção de tensão | Auto ranging: 100-240 V AC |
| Pluggable quente           | Sim                        |

### <a name="azure-stack-edge-power-cord-specifications-by-region"></a>Especificações do cabo de alimentação Azure Stack Edge por região

O seu dispositivo Azure Stack Edge necessita de um cabo de alimentação que varia consoante a sua região Azure.
Para obter especificações técnicas de todos os cabos de alimentação suportados, consulte as especificações do cabo de [alimentação Azure Stack Edge por região](azure-stack-edge-technical-specifications-power-cords-regional.md).

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge device.-->

## <a name="network-interface-specifications"></a>Especificações de interface de rede

O seu dispositivo Azure Stack Edge tem 6 interfaces de rede, PORT1-PORT6.

| Especificação           | Descrição                 |
|-------------------------|----------------------------|
|  Interfaces de rede    | 2 interfaces de 1 GbE – 1 de gestão, não configurável pelo utilizador, utilizada para a configuração inicial. A outra interface é configurável pelo utilizador, pode ser usada para transferência de dados, e é DHCP por padrão. <br>2 interfaces de 25 GbE – também podem funcionar como interfaces de 10 GbE. Estas interfaces de dados podem ser configuradas pelo utilizador como DHCP (predefinição) ou estáticas. <br> 2 interfaces de 25 GbE - estas interfaces de dados podem ser configuradas pelo utilizador como DHCP (predefinição) ou estáticas.                  |

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos Azure Stack Edge têm SSDs NVMe de 9 X 2.5", cada um com uma capacidade de 1.6 TB. Destes SSDs, 1 é um disco de sistema operativo, e os outros 8 são discos de dados. A capacidade total utilizável para o dispositivo é de aproximadamente 12,5 TB. A tabela a seguir tem os detalhes para a capacidade de armazenamento do dispositivo.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de unidades de estado sólido (SSDs)     |    8                  |
|    Capacidade única de SSD                     |    1.6 TB             |
|    Capacidade total                          |    12,8 TB            |
|    Total da capacidade utilizável*                  |    ~ 12,5 TB            |

**Algum espaço é reservado para uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do recinto e especificações de peso

As tabelas seguintes listam as várias especificações do recinto para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do recinto

A tabela seguinte enumera as dimensões do recinto em milímetros e polegadas.

|     Recinto     |     Milímetros     |     Centímetros     |
|-------------------|---------------------|----------------|
|    Altura         |    44.45            |    1.75"          |
|    Largura          |    434.1           |    17.09"          |
|    Comprimento          |    740.4           |    29.15"          |

A tabela seguinte enumera as dimensões do pacote de envio em milímetros e polegadas.

|     Pacote     |     Milímetros     |     Centímetros     |
|-------------------|---------------------|----------------|
|    Altura         |    311.2            |    12.25"          |
|    Largura          |    642.8          |    25.31"          |
|    Comprimento          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Peso do recinto

O pacote do dispositivo pesa 61 libras. e requer duas pessoas para lidar com isto. O peso do dispositivo depende da configuração do recinto.

|     Recinto                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total, incluindo a embalagem       |    61 libras.          |
|    Peso do dispositivo                       |    35 libras.          |

## <a name="enclosure-environment-specifications"></a>Especificações ambientais de recinto

Esta secção enumera as especificações relacionadas com o ambiente do recinto, tais como temperatura, humidade e altitude.

### <a name="temperature-and-humidity"></a>Temperatura e humidade

|     Recinto         |     Intervalo de temperatura ambiente     |     Humidade relativa ambiente     |     Ponto máximo de orvalho     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operacional        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% não condensando.         |    29°C (84°F)            |
|    Não operacional    |    -40°C a 65°C (-40°F - 149°F)     |    5% - 95% não condensando.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

|     Recinto                           |     Especificações operacionais                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Fluxo de ar                              |    O fluxo de ar do sistema é frontal para trás. O sistema deve ser acionado com uma instalação de escape traseiro de baixa pressão. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitude máxima, operacional        |    3048 metros (10.000 pés) com temperatura máxima de funcionamento desclassificada determinada pelas [especificações de declassificação da temperatura de funcionamento](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitude máxima, não operacional    |    12.000 metros (39.370 pés)                                                                                                                                                                                         |
|    Choque, operacional                   |    6 G por 11 milissegundos em 6 orientações                                                                                                                                                                         |
|    Choque, não operacional               |    71 G por 2 milissegundos em 6 orientações                                                                                                                                                                           |
|    Vibração, operacional               |    0.26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatório                                                                                                                                                                                     |
|    Vibração, não operacional           |    1.88 G<sub>RMS</sub> 10 Hz a 500 Hz durante 15 minutos (todos os seis lados testados.)                                                                                                                                                  |
|    Orientação e montagem             |    Montagem de cremalheira de 19"                                                                                                                                                                                        |
|    Segurança e aprovações                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Regulamento da Comissão (UE) Nº. 617/2013                                                                                                                                                                                        |
|    Rio Rohs           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>Especificações de declassificação da temperatura de funcionamento

|     Declassificação da temperatura de funcionamento     |     Intervalo de temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Até 35°C (95°F)                       |    A temperatura máxima é reduzida em 1°C/300 m (1°F/547 ft) acima de 950 m (3,117 pés).    |
|    35°C a 40°C (95°F a 104°F)            |    A temperatura máxima é reduzida em 1°C/175 m (1°F/319 ft) acima de 950 m (3,117 pés).    |
|    40°C a 45°C (104°F a 113°F)           |    A temperatura máxima é reduzida em 1°C/125 m (1°F/228 ft) acima de 950 m (3,117 pés).    |

## <a name="next-steps"></a>Passos seguintes

- [Implante a sua borda de pilha azure](azure-stack-edge-deploy-prep.md)
