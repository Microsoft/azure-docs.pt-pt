---
title: Disponibilidade de região do StorSimple | Documentos da Microsoft
description: Explica as regiões do Azure em que estão disponíveis vários modelos de dispositivos do StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: ff2ea89c257a4941c801f3056450e005a9b37039
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996055"
---
# <a name="available-regions-for-your-storsimple"></a>Regiões disponíveis para o seu StorSimple

## <a name="overview"></a>Descrição geral

Os datacenters do Azure funcionar em várias localizações geográficas em todo o mundo para atender às demandas do cliente de desempenho, requisitos e preferências relativamente à localização dos dados. Uma geografia do Azure é uma área definida de todo o mundo que contém pelo menos uma região do Azure. Uma região do Azure é uma área dentro de uma localização geográfica, que contém um ou mais datacenters.

Escolher uma região do Azure é muito importante e a opção de região é influenciada por fatores como a residência dos dados e soberania de dados, disponibilidade do serviço, desempenho, custo e redundância. Para obter mais informações sobre como escolher uma região, aceda a [região do Azure que é melhor para mim?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Para a solução StorSimple, a escolha da região especificamente é determinada pelos seguintes fatores:

- Regiões onde o serviço StorSimple Device Manager está disponível.
- Os países onde o StorSimple físico, a cloud ou o dispositivo virtual está disponível.
- As regiões em que as contas de armazenamento que armazenam dados do StorSimple devem estar localizadas para um desempenho ideal.

Este tutorial descreve a disponibilidade de região para o serviço StorSimple Device Manager, o físico no local e os dispositivos na cloud. As informações contidas neste artigo são aplicáveis a série StorSimple 8000 e dispositivos da série 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Disponibilidade das regiões do serviço StorSimple Device Manager

Serviço StorSimple Device Manager é atualmente suportado no 12 regiões públicas e 2 regiões do Azure Government.

Define uma região ou a localização quando cria pela primeira vez o serviço StorSimple Device Manager. Em geral, é escolhida uma localização mais próxima da região geográfica onde o dispositivo está implementado. Mas o dispositivo e o serviço também podem ser implementados em localizações diferentes.

Aqui está uma lista de regiões onde o serviço StorSimple Device Manager está disponível para a cloud pública do Azure e pode ser implementado.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Para a cloud do Azure Government, o serviço StorSimple Device Manager está disponível nos datacenters Iowa gov (US) e gov (US) Virginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Disponibilidade de região para dados armazenados no StorSimple

Dados do StorSimple fisicamente são armazenados nas contas de armazenamento do Azure e estas contas estão disponíveis em todas as regiões do Azure. Quando cria uma conta de armazenamento do Azure, da localização primária da conta de armazenamento é escolhida e que determina a região onde residem os dados.

Quando criar um serviço StorSimple Device Manager pela primeira vez e associar uma conta de armazenamento com o mesmo, o serviço StorSimple Device Manager e o armazenamento do Azure podem ser em duas localizações diferentes. Se for esse o caso, terá de criar separadamente a conta do Gestor de Dispositivos do StorSimple e a conta de armazenamento do Azure.

Em geral, escolha a região mais próxima ao seu serviço para a sua conta de armazenamento. No entanto, a região mais próxima do Microsoft Azure poderão não estar a região com a menor latência. É a latência que determina o desempenho do serviço de rede e, por conseguinte, o desempenho da solução. Portanto, se escolher uma conta de armazenamento numa região diferente, é importante saber quais são as latências entre o serviço e a região associada à sua conta de armazenamento.

Se estiver a utilizar uma StorSimple Cloud Appliance, em seguida, recomendamos que o serviço e a conta de armazenamento associado estão na mesma região. Contas de armazenamento numa região diferente poderão resultar num fraco desempenho.

## <a name="availability-of-storsimple-device"></a>Disponibilidade do dispositivo StorSimple

Consoante o modelo, os dispositivos do StorSimple podem estar disponíveis em diferentes geografias ou países.

### <a name="storsimple-physical-device-models-81008600"></a>Dispositivo físico StorSimple (modelos 8100/8600)

Se utilizar um StorSimple 8100 ou 8600 dispositivo físico, o dispositivo está disponível nos seguintes países.

| #  | País        | #  | País     | #  | País      | #  | País              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Austrália      | 16 | RAE de Hong Kong| 31 | Nova Zelândia  | 46 | África do Sul         |
| 2  | Áustria        | 17 | Hungria     | 32 | Nigéria      | 47 | Coreia do Sul          |
| 3  | Barém        | 18 | Islândia     | 33 | Noruega       | 48 | Espanha                |
| 4  | Bélgica        | 19 | Índia       | 34 | Peru         | 49 | Sri Lanka            |
| 5  | Brasil         | 20 | Indonésia   | 35 | Filipinas  | 50 | Suécia               |
| 6  | Canadá         | 21 | Irlanda     | 36 | Polónia       | 51 | Suíça          |
| 7  | Chile          | 22 | Israel      | 37 | Portugal     | 52 | Taiwan               |
| 8  | Colômbia       | 23 | Itália       | 38 | Porto Rico  | 53 | Tailândia             |
| 9  | República Checa | 24 | Japão       | 39 | Catar        | 54 | Turquia               |
| 10 | Dinamarca        | 25 | Quénia       | 40 | Roménia      | 55 | Ucrânia              |
| 11 | Egito          | 26 | Kuwait      | 41 | Rússia       | 56 | Emirados Árabes Unidos |
| 12 | Finlândia        | 27 | R.A.E. de Macau   | 42 | Arábia Saudita | 57 | Reino Unido       |
| 13 | França         | 28 | Malásia    | 43 | Singapura    | 58 | Estados Unidos        |
| 14 | Alemanha        | 29 | México      | 44 | Eslováquia     | 59 | Vietname              |
| 15 | Grécia         | 30 | Países Baixos | 45 | Eslovénia     | 60 | Croácia              |

Essa lista muda à medida que são adicionados mais países. Para obter uma lista mais atualizada das áreas geográficas indicadas, vá para o apêndice de termos de matriz de armazenamento na [termos de produto](https://www.microsoft.com/en-us/licensing/product-licensing/products).

A Microsoft pode enviar o hardware físico e fornecer a substituição de peças sobressalentes de hardware para o StorSimple para áreas geográficas indicadas na lista anterior.

> [!IMPORTANT]
> Não coloque um dispositivo físico StorSimple numa região onde o StorSimple não é suportado. Microsoft não será capaz de enviar qualquer peças de substituição para os países onde o StorSimple não é suportado.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (8010/8020 de modelos)

Se utilizar uma StorSimple Cloud Appliance 8010 ou 8020, em seguida, o dispositivo é suportada e está disponível em todas as regiões onde a VM subjacente é suportada. O 8010 utiliza um _Standard_A3_ VM que é suportado em todas as regiões do Azure.

O 8020 utiliza o armazenamento premium e _Standard_DS3_ VM para criar uma aplicação da cloud. O 8020 é suportada nas regiões do Azure que suportam o Premium Storage e _Standard_DS3_ VMs do Azure. Utilize [esta lista](https://azure.microsoft.com/regions/services/) para ver se ambas as **Máquinas Virtuais > série DS** e o **Armazenamento > Armazenamento em Disco** estão disponíveis na sua região.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual Array (modelo 1200)

Se utilizar uma série de 1200 StorSimple Virtual Array, em seguida, a imagem de disco virtual é suportada em todas as regiões do Azure.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o [preços de vários modelos de StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Saiba mais sobre [gerir a sua conta de armazenamento StorSimple](storsimple-8000-manage-storage-accounts.md).
* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
