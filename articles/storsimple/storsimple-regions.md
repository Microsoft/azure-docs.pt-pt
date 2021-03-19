---
title: Disponibilidade da região StorSimple
description: Explica as regiões Azure em que estão disponíveis os vários modelos de dispositivos StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: 5efb87ad674474b2e21b86b5cdc48785753a51dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90564156"
---
# <a name="available-regions-for-your-storsimple"></a>Regiões disponíveis para o seu StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição Geral

Os datacenters da Azure operam em múltiplas geografias em todo o mundo para atender às exigências do cliente de desempenho, requisitos e preferências em relação à localização dos dados. Uma geografia azul é uma área definida do mundo que contém pelo menos uma região de Azure. Uma região de Azure é uma área dentro de uma geografia, contendo um ou mais datacenters.

A escolha de uma região de Azure é muito importante e a escolha da região é influenciada por fatores como a residência de dados e soberania, disponibilidade de serviços, desempenho, custo e redundância. Para mais informações sobre como escolher uma região, vá para [qual região de Azure é a certa para mim?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Para a solução StorSimple, a escolha da região é especificamente determinada pelos seguintes fatores:

- Regiões onde está disponível o serviço StorSimple Device Manager.
- Os países/regiões onde o dispositivo físico, nuvem ou virtual StorSimple está disponível.
- As regiões onde as contas de armazenamento que armazenam dados StorSimple devem ser localizadas para um desempenho ótimo.

Este tutorial descreve a disponibilidade da região para o serviço StorSimple Device Manager, os dispositivos físicos e de nuvem no local. As informações contidas neste artigo aplicam-se aos dispositivos da série StorSimple 8000 e 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Disponibilidade da região para o serviço StorSimple Device Manager

O serviço StorSimple Device Manager é atualmente apoiado em 12 regiões públicas e 2 regiões do Governo Azure.

Define uma região ou localização quando cria pela primeira vez o serviço StorSimple Device Manager. Em geral, é escolhida uma localização mais próxima da região geográfica onde o dispositivo é implantado. Mas o dispositivo e o serviço também podem ser implantados em diferentes locais.

Aqui está uma lista de regiões onde o serviço StorSimple Device Manager está disponível para a nuvem pública de Azure e pode ser implantado.

![A screenshot mostra o Gestor de Dispositivos StorSimple com a lista de localização em destaque.](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Para a nuvem do Governo Azure, o serviço StorSimple Device Manager está disponível nos centros de dados do Gov Iowa e do Us Gov Virginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Disponibilidade da região para os dados armazenados em StorSimple

Os dados StorSimple são armazenados fisicamente em contas de armazenamento Azure e estas contas estão disponíveis em todas as regiões do Azure. Quando se cria uma conta de armazenamento Azure, a localização primária da conta de armazenamento é escolhida e que determina a região onde os dados residem.

Quando cria pela primeira vez um serviço StorSimple Device Manager e associa uma conta de armazenamento com ele, o seu serviço StorSimple Device Manager e o armazenamento Azure podem estar em dois locais distintos. Se for esse o caso, terá de criar separadamente a conta do Gestor de Dispositivos do StorSimple e a conta de armazenamento do Azure.

Em geral, escolha a região mais próxima do seu serviço para a sua conta de armazenamento. No entanto, a região mais próxima do Microsoft Azure pode não ser a região com a latência mais baixa. É a latência que dita o desempenho do serviço de rede e, consequentemente, o desempenho da solução. Por isso, se estiver a escolher uma conta de armazenamento numa região diferente, é importante saber quais são as latências entre o seu serviço e a região associada à sua conta de armazenamento.

Se estiver a utilizar um StorSimple Cloud Appliance, recomendamos que o serviço e a conta de armazenamento associada estejam na mesma região. As contas de armazenamento numa região diferente podem resultar num mau desempenho.

## <a name="availability-of-storsimple-device"></a>Disponibilidade de dispositivo StorSimple

Dependendo do modelo, os dispositivos StorSimple podem estar disponíveis em diferentes geografias ou países/regiões.

### <a name="storsimple-physical-device-models-81008600"></a>Dispositivo físico StorSimple (Modelos 8100/8600)

Se utilizar um dispositivo físico StorSimple 8100 ou 8600, o dispositivo está disponível nos seguintes países/regiões.

| #  | País/Região        | #  | País/Região     | #  | País/Região      | #  | País/Região             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Austrália             | 16 | R.A.E. de Hong Kong      | 31 | Nova Zelândia         | 46 | África do Sul               |
| 2  | Áustria               | 17 | Hungria            | 32 | Nigéria             | 47 | Coreia do Sul                |
| 3  | Barém               | 18 | Islândia            | 33 | Noruega              | 48 | Espanha                      |
| 4  | Bélgica               | 19 | Índia              | 34 | Peru                | 49 | Sri Lanca                  |
| 5  | Brasil                | 20 | Indonésia          | 35 | Filipinas         | 50 | Suécia                     |
| 6  | Canadá                | 21 | Irlanda            | 36 | Polónia              | 51 | Suíça                |
| 7  | Chile                 | 22 | Israel             | 37 | Portugal            | 52 | Taiwan                     |
| 8  | Colômbia              | 23 | Itália              | 38 | Porto Rico         | 53 | Tailândia                   |
| 9  | República Checa        | 24 | Japão              | 39 | Catar               | 54 | Turquia                     |
| 10 | Dinamarca               | 25 | Quénia              | 40 | Roménia             | 55 | Ucrânia                    |
| 11 | Egito                 | 26 | Koweit             | 41 | Rússia              | 56 | Emirados Árabes Unidos       |
| 12 | Finlândia               | 27 | RAE de Macau          | 42 | Arábia Saudita        | 57 | Reino Unido             |
| 13 | França                | 28 | Malásia           | 43 | Singapura           | 58 | Estados Unidos da América              |
| 14 | Alemanha               | 29 | México             | 44 | Eslováquia            | 59 | Vietname                    |
| 15 | Grécia                | 30 | Países Baixos        | 45 | Eslovénia            | 60 | Croácia                    |

Esta lista muda à medida que mais países/regiões são adicionados. Para obter uma lista mais atualizada das geografias, aceda ao apêndice de termos de array de armazenamento nos [termos do Produto](https://www.microsoft.com/en-us/licensing/product-licensing/products).

A Microsoft pode enviar hardware físico e fornecer substituição de peças sobressalentes de hardware para storSimple às geografias da lista anterior.

> [!IMPORTANT]
> Não coloque um dispositivo físico StorSimple numa região onde o StorSimple não esteja suportado. A Microsoft não poderá enviar peças de substituição para países/regiões onde o StorSimple não seja suportado.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (Modelos 8010/8020)

Se utilizar um StorSimple Cloud Appliance 8010 ou 8020, o dispositivo é suportado e disponível em todas as regiões onde o VM subjacente é suportado. O 8010 utiliza um _Standard_A3_ VM que é suportado em todas as regiões do Azure.

O 8020 utiliza armazenamento premium e _Standard_DS3_ VM para criar um aparelho em nuvem. O 8020 é apoiado nas regiões de Azure que suportam o Armazenamento Premium e _Standard_DS3_ VMs Azure. Utilize [esta lista](https://azure.microsoft.com/regions/services/) para ver se ambas as **Máquinas Virtuais > série DS** e o **Armazenamento > Armazenamento em Disco** estão disponíveis na sua região.

### <a name="storsimple-virtual-array-model-1200"></a>Matriz Virtual StorSimple (Modelo 1200)

Se utilizar uma série de 1200 séries StorSimple Virtual Array, então a imagem do disco virtual é suportada em todas as regiões do Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [preços de vários modelos StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Saiba mais sobre [a gestão da sua conta de armazenamento StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
