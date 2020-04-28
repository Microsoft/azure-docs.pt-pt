---
title: Disponibilidade da região StorSimple
description: Explica as regiões azure nas quais estão disponíveis os vários modelos de dispositivos StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: a2f04d6faa7770eef7768437ae0e624b76713f98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76275117"
---
# <a name="available-regions-for-your-storsimple"></a>Regiões disponíveis para o seu StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral

Os datacenters Azure operam em múltiplas geografias em todo o mundo para atender às exigências dos clientes de desempenho, requisitos e preferências em relação à localização de dados. Uma geografia Azure é uma área definida do mundo que contém pelo menos uma região azure. Uma região de Azure é uma área dentro de uma geografia, contendo um ou mais datacenters.

A escolha de uma região azure é muito importante e a escolha da região é influenciada por fatores como a residência de dados e a soberania, disponibilidade de serviços, desempenho, custo e redundância. Para mais informações sobre como escolher uma região, ir para [qual região de Azure é a certa para mim?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Para a solução StorSimple, a escolha da região é especificamente determinada pelos seguintes fatores:

- Regiões onde o serviço StorSimple Device Manager está disponível.
- Os países/regiões onde o dispositivo físico, nuvem ou virtual StorSimple está disponível.
- As regiões onde as contas de armazenamento que armazenam dados StorSimple devem ser localizadas para um desempenho ótimo.

Este tutorial descreve a disponibilidade da região para o serviço StorSimple Device Manager, os dispositivos físicos no local e os dispositivos de nuvem. As informações contidas neste artigo aplicam-se aos dispositivos da série StorSimple 8000 e 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Disponibilidade da região para o serviço StorSimple Device Manager

O serviço StorSimple Device Manager é atualmente apoiado em 12 regiões públicas e 2 regiões do Governo Azure.

Define uma região ou local quando cria o serviço StorSimple Device Manager. Em geral, é escolhida uma localização mais próxima da região geográfica onde o dispositivo é implantado. Mas o dispositivo e o serviço também podem ser implantados em diferentes locais.

Aqui está uma lista de regiões onde o serviço StorSimple Device Manager está disponível para a nuvem pública do Azure e pode ser implantado.

![storsimple-dispositivo-manager-service-regiões](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Para a nuvem do Governo Azure, o serviço StorSimple Device Manager está disponível nos centros de dados norte-americanos Gov Iowa e Us Gov Virginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Disponibilidade da região para dados armazenados no StorSimple

Os dados StorSimple são armazenados fisicamente em contas de armazenamento Azure e estas contas estão disponíveis em todas as regiões do Azure. Quando se cria uma conta de armazenamento Azure, a localização principal da conta de armazenamento é escolhida e que determina a região onde residem os dados.

Quando cria pela primeira vez um serviço StorSimple Device Manager e associa uma conta de armazenamento com ele, o serviço StorSimple Device Manager e o armazenamento Azure podem estar em dois locais distintos. Se for esse o caso, terá de criar separadamente a conta do Gestor de Dispositivos do StorSimple e a conta de armazenamento do Azure.

Em geral, escolha a região mais próxima do seu serviço para a sua conta de armazenamento. No entanto, a região mais próxima do Microsoft Azure pode não ser a região com menor latência. É a latência que dita o desempenho do serviço de rede e, consequentemente, o desempenho da solução. Por isso, se está a escolher uma conta de armazenamento numa região diferente, é importante saber quais são as tardios entre o seu serviço e a região associada à sua conta de armazenamento.

Se estiver a utilizar um StorSimple Cloud Appliance, recomendamos que o serviço e a conta de armazenamento associada estejam na mesma região. As contas de armazenamento numa região diferente podem resultar num mau desempenho.

## <a name="availability-of-storsimple-device"></a>Disponibilidade do dispositivo StorSimple

Dependendo do modelo, os dispositivos StorSimple podem estar disponíveis em diferentes geografias ou países/regiões.

### <a name="storsimple-physical-device-models-81008600"></a>Dispositivo físico StorSimple (Modelos 8100/8600)

Se utilizar um dispositivo físico StorSimple 8100 ou 8600, o dispositivo está disponível nos seguintes países/regiões.

| #  | País/Região        | #  | País/Região     | #  | País/Região      | #  | País/Região             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Austrália             | 16 | RAE de Hong Kong      | 31 | Nova Zelândia         | 46 | África do Sul               |
| 2  | Áustria               | 17 | Hungria            | 32 | Nigéria             | 47 | Coreia do Sul                |
| 3  | Barém               | 18 | Islândia            | 33 | Noruega              | 48 | Espanha                      |
| 4  | Bélgica               | 19 | Índia              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brasil                | 20 | Indonésia          | 35 | Filipinas         | 50 | Suécia                     |
| 6  | Canadá                | 21 | Irlanda            | 36 | Polónia              | 51 | Suíça                |
| 7  | Chile                 | 22 | Israel             | 37 | Portugal            | 52 | Taiwan                     |
| 8  | Colômbia              | 23 | Itália              | 38 | Porto Rico         | 53 | Tailândia                   |
| 9  | República Checa        | 24 | Japão              | 39 | Catar               | 54 | Turquia                     |
| 10 | Dinamarca               | 25 | Quénia              | 40 | Roménia             | 55 | Ucrânia                    |
| 11 | Egito                 | 26 | Kuwait             | 41 | Rússia              | 56 | Emirados Árabes Unidos       |
| 12 | Finlândia               | 27 | RAE de Macau          | 42 | Arábia Saudita        | 57 | Reino Unido             |
| 13 | França                | 28 | Malásia           | 43 | Singapura           | 58 | Estados Unidos              |
| 14 | Alemanha               | 29 | México             | 44 | Eslováquia            | 59 | Vietname                    |
| 15 | Grécia                | 30 | Países Baixos        | 45 | Eslovénia            | 60 | Croácia                    |

Esta lista muda à medida que mais países/regiões são adicionados. Para obter uma lista mais atualizada das geografias, vá ao Apêndice de Termos de Armazenamento nos termos do [Produto](https://www.microsoft.com/en-us/licensing/product-licensing/products).

A Microsoft pode enviar hardware físico e fornecer hardware substituto de peças sobressalentes para storSimple às geografias na lista anterior.

> [!IMPORTANT]
> Não coloque um dispositivo físico StorSimple numa região onde o StorSimple não seja suportado. A Microsoft não poderá enviar peças de substituição para países/regiões onde o StorSimple não seja suportado.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (Modelos 8010/8020)

Se utilizar um StorSimple Cloud Appliance 8010 ou 8020, então o dispositivo é suportado e disponível em todas as regiões onde o VM subjacente é suportado. O 8010 usa um _Standard_A3_ VM que é apoiado em todas as regiões de Azure.

O 8020 utiliza armazenamento premium e _Standard_DS3_ VM para criar um aparelho em nuvem. O 8020 é apoiado nas regiões de Azure que suportam O Armazenamento Premium e _Standard_DS3_ VMs Azure. Utilize [esta lista](https://azure.microsoft.com/regions/services/) para ver se ambas as **Máquinas Virtuais > série DS** e o **Armazenamento > Armazenamento em Disco** estão disponíveis na sua região.

### <a name="storsimple-virtual-array-model-1200"></a>Matriz Virtual StorSimple (Modelo 1200)

Se utilizar um StorSimple Virtual Array de série 1200, então a imagem de disco virtual é suportada em todas as regiões do Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [preços de vários modelos StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Saiba mais sobre a gestão da sua conta de [armazenamento StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
