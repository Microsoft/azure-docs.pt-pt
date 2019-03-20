---
title: As especificações técnicas do Edge de caixa de dados do Microsoft Azure e de conformidade | Documentos da Microsoft
description: Saiba mais sobre as especificações técnicas e a conformidade para sua caixa de dados do Azure Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/12/2019
ms.author: alkohli
ms.openlocfilehash: 8ef35709e90c0a58cc0ff8df1afb6e864adc0a23
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "57994709"
---
# <a name="azure-data-box-edge-technical-specifications-preview"></a>Especificações técnicas do Azure do Edge de caixa de dados (pré-visualização)

Os componentes de hardware do seu dispositivo do Edge de caixa de dados do Microsoft Azure cumprem as especificações técnicas e os padrões normativos descritos neste artigo. As especificações técnicas descrevem as unidades de fonte de alimentação de energia (PSUs), a capacidade de armazenamento, inclusões e padrões ambientais. 

> [!IMPORTANT]
> O Data Box Edge está em pré-visualização. Antes de pedir e implementar esta solução, reveja os [do Azure termos de serviço para pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="power-supply-unit-specifications"></a>Especificações de unidade de fonte de alimentação de energia

O dispositivo de limite de caixa de dados tem dois 100 a 240 V fornecimento unidades de energia (PSUs) com os fãs de alto desempenho. Os dois PSUs fornecem uma configuração de energia redundante. Se um PSU falhar, o dispositivo continua a funcionar normalmente na outro PSU até que o módulo com falha é substituído. A tabela seguinte lista as especificações técnicas dos PSUs.

| Especificação           | 750 W PSU                  |
|-------------------------|----------------------------|
| Potência de saída máximo    |  750 W                     |
| Frequência               | 50/60 Hz                   |
| Seleção do intervalo de tensão | Automaticamente, desde: 100 A 240 V AC |
| Armazenamento frequente conectável           | Sim                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Especificações de armazenamento

Os dispositivos de limite de caixa de dados tem 10 X 2.5" NVMe SSD, cada um com uma capacidade de 1,6 TB. Destes SSDs, 2 são discos do sistema operativo e a outros 8 são discos de dados. A capacidade total utilizável para o dispositivo é, aproximadamente, 12,5 TB. A tabela seguinte tem os detalhes para a capacidade de armazenamento do dispositivo.

|     Especificação                          |     Value             |
|--------------------------------------------|-----------------------|
|    Número de unidades de estado sólido (SSDs)     |    8                  |
|    Capacidade SSD única                     |    1.6 TB             |
|    Capacidade total                          |    12.8 TB            |
|    Capacidade total utilizável *                  |    ~ 12,5 TB            |

**Algum espaço é reservado para utilização interna.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Dimensões de bastidor e especificações de peso

As tabelas seguintes listam as várias especificações de inclusão para dimensões e peso.

### <a name="enclosure-dimensions"></a>Dimensões de bastidor

A tabela seguinte lista as dimensões de inclusão em milímetros e polegadas.

|     Bastidor     |     Milímetros     |     Polegadas     |
|-------------------|---------------------|----------------|
|    Altura         |    304.8            |    12          |
|    Largura          |    660.4            |    26          |
|    Profundidade          |    1041.4           |    41          |

### <a name="enclosure-weight"></a>Peso de bastidor

O pacote de dispositivo examina 66 lbs. e requer duas pessoas manipulá-lo. O peso do dispositivo depende da configuração do bastidor.

|     Bastidor                                 |     Peso          |
|-----------------------------------------------|---------------------|
|    Peso total, incluindo o empacotamento       |    66 lbs.          |
|    Peso do dispositivo                       |    48.3 lbs.        |

## <a name="enclosure-environment-specifications"></a>Especificações de ambiente de bastidor

Esta secção lista as especificações relacionadas ao ambiente do bastidor como temperatura, humidade e altitude.

### <a name="temperature-and-humidity"></a>Temperatura e humidade

|     Bastidor         |     Intervalo de temperatura ambiente     |     Ambiente de caminho relativo humidade     |     Ponto de orvalho máximo     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operacional        |    10°C - 35°C (50°F - 86°F)         |    10% a 80% - não-condensar.         |    29°C (84°F)            |
|    Não-operacional    |    -40,Year),temperature ° C 65 ° c (-40,Year),temperature ° F - 149 ° F)     |    5% a 95% - não-condensar.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Ventilação, altitude, choque, vibração, orientação, segurança e EMC

|     Bastidor                           |     Especificações operacionais                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Ventilação                              |    Ventilação do sistema é, de frente para trás. Sistema tem de ser utilizado com uma instalação low-pressure esgotar para trás. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Máximo altitude, operacional        |    Medidores 3048 (pés 10 000) com máximo operacional temperatura anular classificada determinado pela [operacional temperatura anular classificação especificações](#operating-temperature-de-rating-specifications).                                                                                |
|    Máximo altitude, inoperacional    |    Medidores de 12 000 (39,370 metros)                                                                                                                                                                                         |
|    Choque, operacional                   |    6 G para 11 milissegundos em 6 orientações                                                                                                                                                                         |
|    Choque, inoperacional               |    G 71 por 2 milissegundos em 6 orientações                                                                                                                                                                           |
|    Vibração, operacional               |    0.26 G<sub>RMS</sub> Hz 5 para Hz 350 aleatório                                                                                                                                                                                     |
|    Vibração, inoperacional           |    1.88 G<sub>RMS</sub> Hz 10 a 500 Hz para 15 minutos (todos os seis lados testados.)                                                                                                                                                  |
|    Orientação e montagem             |    montar em Bastidor 19"                                                                                                                                                                                        |
|    Segurança e aprovações                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (classe D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    Não. de Regulamento (UE) Commission 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Temperatura operacional anular as especificações de classificação

|     Operar a temperatura anular classificação     |     Intervalo de temperatura ambiente                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Até 35 ° C (95° F)                       |    Temperatura máxima é reduzida por 1° C/300 m (1° F/547 quadrado) acima 950 m (3,117 quadrado).    |
|    35° C, 40° c (95° F para 104° F)            |    Temperatura máxima é reduzida por 1° C/175 m (1° F/319 quadrado) acima 950 m (3,117 quadrado).    |
|    40° C, 45° c (104° F para 113° F)           |    Temperatura máxima é reduzida por 1° C/125 m (1° F/228 quadrado) acima 950 m (3,117 quadrado).    |


## <a name="next-steps"></a>Passos Seguintes

- [Implementar o seu limite de caixa de dados do Azure](data-box-edge-deploy-prep.md)
