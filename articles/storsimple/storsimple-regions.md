---
title: Disponibilidade de região do StorSimple
description: Explica as regiões do Azure nas quais os vários modelos de dispositivo StorSimple estão disponíveis.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: a2f04d6faa7770eef7768437ae0e624b76713f98
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275117"
---
# <a name="available-regions-for-your-storsimple"></a>Regiões disponíveis para o StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Visão geral

Os data centers do Azure operam em várias regiões geográficas em todo o mundo para atender às demandas de desempenho, requisitos e preferências do cliente em relação ao local dos dados. Uma geografia do Azure é uma área definida do mundo que contém pelo menos uma região do Azure. Uma região do Azure é uma área dentro de uma geografia, que contém um ou mais data centers.

A escolha de uma região do Azure é muito importante e a escolha da região é influenciada por fatores como residência de dados e soberania, disponibilidade de serviço, desempenho, custo e redundância. Para obter mais informações sobre como escolher uma região, vá para [qual região do Azure é adequada para mim?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Para a solução StorSimple, a escolha da região é especificamente determinada pelos seguintes fatores:

- Regiões em que o serviço StorSimple Device Manager está disponível.
- Os países/regiões em que o dispositivo físico, nuvem ou virtual StorSimple está disponível.
- As regiões em que as contas de armazenamento que armazenam dados do StorSimple devem estar localizadas para obter o desempenho ideal.

Este tutorial descreve a disponibilidade de região para o serviço de Device Manager do StorSimple, os dispositivos físicos e de nuvem locais. As informações contidas neste artigo são aplicáveis aos dispositivos StorSimple 8000 e 1200 Series.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Disponibilidade de região para o serviço de Device Manager do StorSimple

O serviço StorSimple Device Manager atualmente tem suporte em 12 regiões públicas e duas regiões do Azure governamental.

Você define uma região ou um local ao criar pela primeira vez o serviço StorSimple Device Manager. Em geral, um local mais próximo da região geográfica em que o dispositivo é implantado é escolhido. Mas o dispositivo e o serviço também podem ser implantados em locais diferentes.

Aqui está uma lista de regiões em que o serviço StorSimple Device Manager está disponível para a nuvem pública do Azure e pode ser implantado.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Para a nuvem do Azure governamental, o serviço de Device Manager do StorSimple está disponível no US Gov Iowa e US Gov-Virgínia data centers.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Disponibilidade de região para dados armazenados no StorSimple

Os dados do StorSimple são armazenados fisicamente nas contas de armazenamento do Azure e essas contas estão disponíveis em todas as regiões do Azure. Quando você cria uma conta de armazenamento do Azure, o local principal da conta de armazenamento é escolhido e determina a região onde os dados residem.

Quando você cria um serviço StorSimple Device Manager e associa uma conta de armazenamento a ele, o serviço StorSimple Device Manager e o armazenamento do Azure podem estar em dois locais separados. Se for esse o caso, terá de criar separadamente a conta do Gestor de Dispositivos do StorSimple e a conta de armazenamento do Azure.

Em geral, escolha a região mais próxima para seu serviço para sua conta de armazenamento. No entanto, a região de Microsoft Azure mais próxima pode não ser realmente a região com a menor latência. É a latência que determina o desempenho do serviço de rede e, portanto, o desempenho da solução. Portanto, se você estiver escolhendo uma conta de armazenamento em uma região diferente, é importante saber quais são as latências entre o serviço e a região associada à sua conta de armazenamento.

Se você estiver usando um dispositivo de nuvem StorSimple, recomendamos que o serviço e a conta de armazenamento associada estejam na mesma região. As contas de armazenamento em uma região diferente podem resultar em baixo desempenho.

## <a name="availability-of-storsimple-device"></a>Disponibilidade do dispositivo StorSimple

Dependendo do modelo, os dispositivos StorSimple podem estar disponíveis em geografias diferentes ou em países/regiões.

### <a name="storsimple-physical-device-models-81008600"></a>Dispositivo físico StorSimple (modelos 8100/8600)

Se estiver usando um dispositivo físico StorSimple 8100 ou 8600, o dispositivo estará disponível nos seguintes países/regiões.

| #  | País/Região        | #  | País/Região     | #  | País/Região      | #  | País/Região             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Austrália             | 16 | RAE de Hong Kong      | 31 | Nova Zelândia         | 46 | África do Sul               |
| 2  | Áustria               | 17 | Hungria            | 32 | Nigéria             | 47 | Coreia do Sul                |
| 3  | Bahrain               | 18 | Islândia            | 33 | Noruega              | 48 | Espanha                      |
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

Essa lista é alterada à medida que mais países/regiões são adicionados. Para obter uma lista mais atualizada das geografias, vá para o apêndice de termos de matriz de armazenamento nos [termos do produto](https://www.microsoft.com/en-us/licensing/product-licensing/products).

A Microsoft pode enviar hardware físico e fornecer substituição de peças de reposição de hardware para o StorSimple para as regiões geográficas na lista anterior.

> [!IMPORTANT]
> Não coloque um dispositivo físico StorSimple em uma região em que o StorSimple não tem suporte. A Microsoft não será capaz de enviar peças de substituição para países/regiões em que não há suporte para o StorSimple.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Dispositivo de nuvem StorSimple (modelos 8010/8020)

Se estiver usando um dispositivo de nuvem StorSimple 8010 ou 8020, o dispositivo terá suporte e estará disponível em todas as regiões em que há suporte para a VM subjacente. O 8010 usa uma VM _Standard_A3_ que tem suporte em todas as regiões do Azure.

O 8020 usa o armazenamento Premium e _Standard_DS3_ VM para criar um dispositivo de nuvem. Há suporte para o 8020 em regiões do Azure que dão suporte ao armazenamento Premium e _Standard_DS3_ VMs do Azure. Utilize [esta lista](https://azure.microsoft.com/regions/services/) para ver se ambas as **Máquinas Virtuais > série DS** e o **Armazenamento > Armazenamento em Disco** estão disponíveis na sua região.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple virtual array (modelo 1200)

Se estiver usando uma matriz virtual do StorSimple da série 1200, a imagem do disco virtual terá suporte em todas as regiões do Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [preços de vários modelos do StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Saiba mais sobre como [gerenciar sua conta de armazenamento do StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [usar o serviço de Device Manager do storsimple para administrar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).
