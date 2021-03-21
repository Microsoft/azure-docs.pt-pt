---
title: Microsoft Azure Stack Edge Pro com especificações técnicas da GPU e conformidade| Microsoft Docs
description: Conheça as especificações técnicas e a conformidade do seu dispositivo Azure Stack Edge Pro com GPU
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 937f0d1feb0c45c1c158b5e88daf268aeb383509
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034749"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-pro-with-gpu"></a>Especificações técnicas e conformidade para Azure Stack Edge Pro com GPU 

Os componentes de hardware do seu Azure Stack Edge Pro com uma Unidade de Processamento de Gráficos a bordo (GPU) aderem às especificações técnicas e padrões regulamentares descritos neste artigo. As especificações técnicas descrevem hardware, unidades de alimentação (PSUs), capacidade de armazenamento, recintos e normas ambientais.

## <a name="compute-and-memory-specifications"></a>Especificações de cálculo e memória

O dispositivo Azure Stack Edge Pro tem as seguintes especificações para cálculo e memória:

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| CPU                     | 2 X Intel Xeon Silver 4214 (Lago cascata) CPU<br> 24 núcleos físicos (12 por CPU)<br>48 núcleos lógicos (vCPUs) (24 por CPU)       |
| Memória                  | 128 (8x16 GB) GB RAM <br> Dell Compatível 16 GB PC4-23400 DDR4-2933Mhz 2Rx8 1.2v ECC Registado RDIMM       |


## <a name="compute-acceleration-specifications"></a>Especificações de aceleração computacional

Uma Unidade de Processamento de Gráficos (GPU) está incluída em todos os dispositivos Azure Stack Edge Pro que permite a Kubernetes, deep learning e machine learning.

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| GPU   | Um ou dois GPUs nVidia T4 <br> Para mais informações, consulte [a NVIDIA T4.](https://www.nvidia.com/en-us/data-center/tesla-t4/)| 


## <a name="power-supply-unit-specifications"></a>Especificações da unidade de alimentação

O dispositivo Azure Stack Edge Pro tem duas unidades de alimentação de 100-240 V (PSUs) com ventiladores de alto desempenho. As duas PSUs fornecem uma configuração de potência redundante. Se uma PSU falhar, o dispositivo continua a funcionar normalmente na outra PSU até que o módulo falhado seja substituído. A tabela que se segue enumera as especificações técnicas das PSUs.

| Especificação           | 750 W PSU                  |
|-------------------------|----------------------------|
| Potência máxima de saída    | 750 W                     |
| Frequência               | 50/60 Hz                   |
| Seleção de gama de tensão | Variação automática: 100-240 V AC |
| Pluggável quente           | Yes                        |


## <a name="network-interface-specifications"></a>Especificações de interface de rede

O seu dispositivo Azure Stack Edge Pro tem seis interfaces de rede, PORT1-PORT6.

| Especificação           | Description                 |
|-------------------------|----------------------------|
|  Interfaces de rede    | **2 interfaces X 1 GbE** – 1 interface de gestão A Porta 1 é utilizada para a configuração inicial e está estática por predefinição. Após a configuração inicial estar concluída, pode utilizar a interface para obter dados com qualquer endereço IP. No entanto, no reset, a interface volta a ip estática. <br>A outra interface A Porta 2 é configurável pelo utilizador, pode ser usada para transferência de dados, e é DHCP por padrão. <br>**4 X 25 Interfaces GbE** – Estas interfaces de dados, Porta 3 até à Porta 6, podem ser configuradas pelo utilizador como DHCP (padrão) ou estática. Também podem funcionar como interfaces de 10 GbE.  | 

O seu dispositivo Azure Stack Edge Pro tem o seguinte hardware de rede:

* **Adaptador personalizado do Microsoft Qlogic Cavium 25G NDC** - Porta 1 através da porta 4.
* **Adaptador de rede de rede de canal Mellanox dual port 25G ConnectX-4** - Porta 5 e porta 6.

Aqui estão os detalhes do cartão Mellanox:

| Parâmetro           | Descrição                 |
|-------------------------|----------------------------|
| Modelação    | Cartão de interface de rede ConnectX®-4 Lx EN                      |
| Descrição do modelo               | 25 GbE de duas portas SFP28; PCIe3.0 x8; ROHS R6                    |
| Número da peça do dispositivo (R640) | MCX4121A-ACAT  |
| PSID (R640)           | MT_2420110034                         |

Para uma lista completa de cabos, interruptores e transmissores suportados para estes cartões de rede, aceda a:

- [Matriz de interoperabilidade do adaptador Qlogic Cavium 25G NDC](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Mellanox porta dupla 25G ConnectX-4 produtos compatíveis com o adaptador de rede de canais](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos Azure Stack Edge Pro têm cinco SSDs NVMe DC P4610 de 2,5", cada um com uma capacidade de 1.6 TB. O boot drive é um SATA SSD de 240 GB. A capacidade total utilizável para o dispositivo é de aproximadamente 4.19 TB. A tabela que se segue indica a capacidade de armazenamento do dispositivo.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de SSDs NVMe                     |    5                  |
|    Capacidade SSD única NVMe                |    1.6 TB             |
|    Boot SATA unidades de estado sólido (SSD)      |    1                  |
|    Capacidade SSD de arranque                       |    240 GB             |
|    Capacidade total                          |    8.0 TB             |
|    Capacidade total utilizável                   |    ~ 4.19 TB          |
|    Configuração RAID                      |    Espaços de armazenamento Direto com uma combinação de espelhamento e paridade  |
|    Controlador SAS                          |    HBA330 12 Gbps     |

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge Pro device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do recinto e especificações de peso

As tabelas a seguir enumeram as várias especificações do recinto para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do recinto

A tabela que se segue lista as dimensões do recinto do dispositivo 1U em milímetros e polegadas.

|     Recinto     |     Milímetros     |     Centímetros     |
|-------------------|---------------------|----------------|
|    Height         |    44.45            |    1.75"       |
|    Width          |    434.1            |    17.09"      |
|    Comprimento         |    740.4            |    29.15"      |

A tabela que se segue lista as dimensões do pacote de transporte em milímetros e polegadas.

|     Pacote     |     Milímetros     |     Centímetros     |
|-------------------|---------------------|----------------|
|    Height         |    311.2            |    12.25"          |
|    Width          |    642.8            |    25.31"          |
|    Comprimento         |    1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>Peso do recinto

A embalagem do dispositivo pesa 66 libras. e requer duas pessoas para lidar com isto. O peso do dispositivo depende da configuração do recinto.

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
| Proteção contra entradas (IP)                 |    Este tipo de equipamento montado na cremalheira para uso no interior normalmente não é testado para proteção contra entradas (proteção contra sólidos e líquidos para um recinto elétrico). A avaliação de segurança do fabricante mostra o IPXO (sem proteção contra entradas).  |
|    Altitude máxima, operacional        |    3048 metros (10.000 pés) com temperatura máxima de funcionamento desapurado determinada pelas [especificações de desins classificação da temperatura de funcionamento](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitude máxima, não operacional    |    12.000 metros (39.370 pés)                                                                                                                                                                                         |
|    Choque, operacional                   |    6 G para 11 milissegundos em 6 orientações                                                                                                                                                                         |
|    Choque, não operacional               |    71 G por 2 milissegundos em 6 orientações                                                                                                                                                                           |
|    Vibração, operacional               |    0,26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatoriamente                                                                                                                                                                                     |
|    Vibração, não operacional           |    1.88 G<sub>RMS</sub> 10 Hz a 500 Hz durante 15 minutos (todos os seis lados testados.)                                                                                                                                                  |
|    Orientação e montagem             |    Suporte padrão de 19" (1U)                                                                                                                                                                                       |
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

[Implemente o seu Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
