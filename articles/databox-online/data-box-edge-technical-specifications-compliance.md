---
title: Microsoft Azure Data Box Edge especificações técnicas e conformidade | Microsoft Docs
description: Saiba mais sobre as especificações técnicas e a conformidade para seu Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/26/2019
ms.author: alkohli
ms.openlocfilehash: 9e1a7f7cd2643aae61e60d77ad74f4a08266a977
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863602"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Especificações técnicas de Azure Data Box Edge

Os componentes de hardware de seu Microsoft Azure Data Box Edge dispositivo aderem às especificações técnicas e aos padrões regulatórios descritos neste artigo. As especificações técnicas descrevem as unidades de fonte de alimentação (PSUs), a capacidade de armazenamento, os compartimentos e os padrões ambientais. 

## <a name="compute-memory-specifications"></a>Computação, especificações de memória

O dispositivo Data Box Edge tem as seguintes especificações para computação e memória:

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| CPU    | CPU de 2 X 10 núcleos                     |
| Memória              | 128 GB de RAM                  |


## <a name="fpga-specifications"></a>Especificações do FPGA

Uma FPGA (matriz de Gate programável) de campo é incluída em cada dispositivo de Data Box Edge que habilita cenários de Machine Learning (ML). 

| Especificação           | Valor                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> Os modelos de DNN (rede neural profunda) disponíveis são os mesmos [suportados pelas instâncias do Cloud FPGA](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure).| 


## <a name="power-supply-unit-specifications"></a>Especificações da unidade de fonte de alimentação

O dispositivo Data Box Edge tem duas PSUs (unidades de alimentação de energia) de 100-240 V com ventiladores de alto desempenho. Os dois PSUs fornecem uma configuração de energia redundante. Se uma PSU falhar, o dispositivo continuará funcionando normalmente na outra PSU até que o módulo com falha seja substituído. A tabela a seguir lista as especificações técnicas do PSUs.

| Especificação           | 750 W PSU                  |
|-------------------------|----------------------------|
| Potência de saída máxima    | 750 W                     |
| Frequência               | 50/60 Hz                   |
| Seleção de faixa de tensão | Variação automática: 100-240 V AC |
| Conectável a quente           | Sim                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos Data Box Edge têm 9 X 2,5 "SSDs de NVMe, cada um com uma capacidade de 1,6 TB. Desses SSDs, 1 é um disco do sistema operacional e os outros 8 são discos de dados. A capacidade utilizável total para o dispositivo é de aproximadamente 12,5 TB. A tabela a seguir tem os detalhes da capacidade de armazenamento do dispositivo.

|     Especificação                          |     Valor             |
|--------------------------------------------|-----------------------|
|    Número de unidades de estado sólido (SSDs)     |    8                  |
|    Capacidade SSD única                     |    1.6 TB             |
|    Capacidade total                          |    12,8 TB            |
|    Capacidade utilizável total *                  |    ~ 12,5 TB            |

**algum espaço é reservado para uso interno.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões de compartimento e especificações de peso

As tabelas a seguir listam as várias especificações de compartimento para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões do compartimento

A tabela a seguir lista as dimensões do compartimento em milímetros e polegadas.

|     SPE     |     Milímetros     |     5,08     |
|-------------------|---------------------|----------------|
|    Altura         |    44,45            |    1,75 "          |
|    Largura          |    434,1           |    17, 9 "          |
|    Comprimento          |    740,4           |    29,15 "          |

A tabela a seguir lista as dimensões do pacote de envio em milímetros e polegadas.

|     Pacote     |     Milímetros     |     5,08     |
|-------------------|---------------------|----------------|
|    Altura         |    311,2            |    12,25 "          |
|    Largura          |    642,8          |    25,31 "          |
|    Comprimento          |   1051,1          |    41,38 "          |

### <a name="enclosure-weight"></a>Peso do compartimento

O pacote do dispositivo pesa 66 kg. e requer duas pessoas para tratá-lo. O peso do dispositivo depende da configuração do compartimento.

|     SPE                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total, incluindo o empacotamento       |    61 lbs.          |
|    Peso do dispositivo                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>Especificações de ambiente de compartimento

Esta seção lista as especificações relacionadas ao ambiente de compartimento, como temperatura, umidade e altitude.

### <a name="temperature-and-humidity"></a>Temperatura e umidade

|     SPE         |     Intervalo de temperatura ambiente     |     Umidade relativa ao ambiente     |     Ponto de orvalho máximo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Eficiência        |    10 ° C-35 ° C (50 ° F-86 ° F)         |    10%-80% sem condensação.         |    29 ° C (84 ° F)            |
|    Não operacional    |    -40 ° c a 65 ° c (-40 ° f-149 ° f)     |    5%-95% sem condensação.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Fluxo de ar, altitude, choque, vibração, orientação, segurança e EMC

|     SPE                           |     Especificações operacionais                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Ar                              |    O fluxo de ar do sistema é de frente para trás. O sistema deve ser operado com uma instalação de exaustão traseira de baixa pressão. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Altitude máxima, operacional        |    3048 metros (10.000 pés) com a temperatura operacional máxima desclassificada determinada pelas [especificações de desclassificação de temperatura operacional](#operating-temperature-de-rating-specifications).                                                                                |
|    Altitude máxima, não operacional    |    12.000 metros (39.370 pés)                                                                                                                                                                                         |
|    Choque, operacional                   |    6 G para 11 milissegundos em 6 orientações                                                                                                                                                                         |
|    Choque, não operacional               |    71 G por 2 milissegundos em 6 orientações                                                                                                                                                                           |
|    Vibração, operacional               |    0,26 G<sub>RMS</sub> 5 Hz a 350 Hz aleatório                                                                                                                                                                                     |
|    Vibração, não operacional           |    1,88 G<sub>RMS</sub> 10 hz a 500 Hz por 15 minutos (todos os seis lados testados.)                                                                                                                                                  |
|    Orientação e montagem             |    19 "montagem em rack                                                                                                                                                                                        |
|    Segurança e aprovações                 |    EN 60950-1:2006 + a1:2010 + a2:2013 + a11:2009 + A12:2011/IEC 60950-1:2005 ed2 + a1:2009 + a2:2013 EN 62311:2008                                                                                                                                                                       |
|    DA                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Não há nenhum. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Especificações de desclassificação da temperatura operacional

|     Reavaliação de temperatura operacional     |     Intervalo de temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Até 35 ° c (95 ° f)                       |    A temperatura máxima é reduzida em 1 ° c/300 m (1 ° f/547 pés) acima de 950 m (3.117 pés).    |
|    35 ° c a 40 ° c (95 ° f a 104 ° f)            |    A temperatura máxima é reduzida em 1 ° c/175 m (1 ° f/319 ft) acima de 950 m (3.117 pés).    |
|    40 ° c a 45 ° c (104 ° f a 113 ° f)           |    A temperatura máxima é reduzida em 1 ° c/125 m (1 ° f/228 ft) acima de 950 m (3.117 pés).    |


## <a name="next-steps"></a>Passos seguintes

- [Implantar seu Azure Data Box Edge](data-box-edge-deploy-prep.md)
