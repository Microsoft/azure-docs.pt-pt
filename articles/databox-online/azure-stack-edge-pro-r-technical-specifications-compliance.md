---
title: Especificações técnicas e conformidade do Microsoft Azure Stack Edge Pro R| Microsoft Docs
description: Conheça as especificações técnicas e a conformidade com o seu dispositivo Azure Stack Edge Pro R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: alkohli
ms.openlocfilehash: 3b323bf920bd884e821d03bf2def37471775e720
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312710"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Especificações técnicas Azure Stack Edge Pro R

Os componentes de hardware do seu dispositivo Azure Stack Edge Pro R aderem às especificações técnicas descritas neste artigo. As especificações técnicas descrevem as unidades de alimentação de energia (PSUs), capacidade de armazenamento, recintos e normas ambientais.


## <a name="compute-memory-specifications"></a>Computação, especificações de memória

O dispositivo Azure Stack Edge Pro R tem as seguintes especificações para cálculo e memória:

| Especificação  | Valor                                             |
|----------------|---------------------------------------------------|
| Tipo CPU       | Dual Intel Xeon Silver 4114 CPU                   |
| CPU: cru       | 20 núcleos totais, 40 vCPUs totais                    |
| CPU: utilizável    | 32 vCPUs                                          |
| Tipo de memória    | Dell Compatível 16 GB RDIMM, 2666 MT/s, Dual rank |
| Memória: crua    | 256 GB DE RAM (16 x 16 GB)                           |
| Memória: utilizável | 230 GB DE RAM                                        |

## <a name="compute-acceleration-specifications"></a>Especificações de aceleração computacional

Uma Unidade de Processamento de Gráficos (GPU) está incluída em todos os dispositivos que permitem kubernetes, deep learning e cenários de aprendizagem automática.

| Especificação           | Valor                      |
|-------------------------|----------------------------|
| GPU   | Um gpu nVidia T4 <br> Para mais informações, consulte [a NVIDIA T4.](https://www.nvidia.com/en-us/data-center/tesla-t4/) | 

## <a name="power-supply-unit-specifications"></a>Especificações da unidade de alimentação

O dispositivo Azure Stack Edge Pro R tem duas unidades de alimentação de 100-240 V (PSUs) com ventiladores de alto desempenho. As duas PSUs fornecem uma configuração de potência redundante. Se uma PSU falhar, o dispositivo continua a funcionar normalmente na outra PSU até que o módulo falhado seja substituído. A tabela que se segue enumera as especificações técnicas das PSUs.

| Especificação              | 550 W PSU                  |
|----------------------------|----------------------------|
| Potência máxima de saída       | 550 W                      |
| Dissipação de calor (máximo) | 2891 BTU/hr                |
| Frequência                  | 50/60 Hz                   |
| Seleção de gama de tensão    | Variação automática: 115-230 V AC |
| Pluggável quente              | Yes                        |

## <a name="network-specifications"></a>Especificações de rede

O dispositivo Azure Stack Edge Pro R tem quatro interfaces de rede, PORT1 - PORT4.


|Especificação         |Description                       |
|----------------------|----------------------------------|
|Interfaces de rede    |**2 x 1 GbE RJ45** <br> O PORT 1 é utilizado como interface de gestão para a configuração inicial e está estático por defeito. Após a configuração inicial estar concluída, pode utilizar a interface para obter dados com qualquer endereço IP. No entanto, no reset, a interface reverte para IP estático. <br>A outra interface, PORT 2, que é configurável pelo utilizador, pode ser usada para transferência de dados, e é DHCP por padrão. |
|Interfaces de rede    |**2 x 25 GbE SFP28** <br> Estas interfaces de dados nos PORTO 3 e PORT 4 podem ser configuradas como DHCP (padrão) ou estática. |

O seu dispositivo Azure Stack Edge Pro R tem o seguinte hardware de rede:

* **Adaptador de rede de rede de canal Mellanox dual port 25G ConnectX-4** - PORT 3 e PORT 4. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25 GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

Para obter uma lista completa de cabos, interruptores e transceptores suportados para estes cartões de rede, vá a [mellanox dual port 25G ConnectX-4 produtos compatíveis com o adaptador de rede de canais](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos Azure Stack Edge Pro R têm oito discos de dados e dois discos M.2 SATA que servem de discos do sistema operativo. Para mais informações, aceda aos [discos M.2 SATA](https://en.wikipedia.org/wiki/M.2).

#### <a name="storage-for-1-node-device"></a>Armazenamento para dispositivo de 1 nó

A tabela seguinte tem detalhes sobre a capacidade de armazenamento do dispositivo de nó de 1.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de unidades de estado sólido (SSDs)     |    8                  |
|    Capacidade SSD única                     |    8 TB               |
|    Capacidade total                          |    64 TB              |
|    Capacidade total utilizável*                  |    ~ 42 TB            |

**Algum espaço está reservado para uso interno.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões do recinto e especificações de peso

As tabelas a seguir enumeram as várias especificações do recinto para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do recinto 

A tabela que se segue lista as dimensões do dispositivo e da UPS com a caixa acidentada em milímetros e polegadas.

|     Recinto     |     Milímetros     |     Centímetros     |
|-------------------|---------------------|----------------|
|    Height         |    301.2            |    11.86       |
|    Width          |    604.5            |    23.80       |
|    Comprimento         |    740.4            |    35.50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Peso do recinto 

O peso do dispositivo depende da configuração do recinto.

|     Recinto                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total de dispositivo de 1 nó + caixa acidentada com tampas finais     |    ~114 lbs          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Especificações do ambiente do recinto

Esta secção lista as especificações relacionadas com o ambiente do recinto, tais como temperatura, vibração, choque e altitude.


|     Especificação              |     Valor    |
|--------------------------------|-------------------------------------------------------------------|
|     Intervalo de temperatura          |     0 - 43° C (operacional)    |
|     Vibração                  |     Método MIL-STD-810 514.7*<br>Procedimento I CAT 4, 20                  |
|     Choque                      |     Método MIL-STD-810 516.7*<br>Procedimento IV, Logística                 |
|     Altitude                   |     Operacional: 10.000 pés<br>Não operacional: 40.000 pés          |

**Todas as referências são para MIL-STD-810G Alteração 1 (2014)*

## <a name="next-steps"></a>Passos seguintes

- [Implemente o seu Azure Stack Edge](azure-stack-edge-placeholder.md)
