---
title: Substitua o chassis no dispositivo da série StorSimple 8000 [ StorSimple 8000 ] Microsoft Docs
description: Descreve como remover e substituir o chassis para o seu recinto primário StorSimple ou o recinto EBOD.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "61312450"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Substitua o chassis no seu dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como remover e substituir um chassis num dispositivo da série StorSimple 8000. O modelo StorSimple 8100 é um único dispositivo de recinto (um chassis), enquanto o 8600 é um dispositivo de caixa duplo (dois chassis). Para um modelo 8600, existem potencialmente dois chassis que podem falhar no dispositivo: o chassis para o recinto primário ou o chassis para o recinto EBOD.

Em qualquer dos casos, o chassis de substituição que é enviado pela Microsoft está vazio. Não serão incluídos módulos de potência e arrefecimento (PCMs), módulos de comando, discos de estado sólido (SSDs), discos rígidos (HDDs) ou módulos EBOD.

> [!IMPORTANT]
> Antes de remover e substituir o chassis, reveja as informações de segurança na substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Retire o chassis
Execute os seguintes passos para remover o chassis do seu dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Para remover um chassis
1. Certifique-se de que o dispositivo StorSimple está desligado e desligado de todas as fontes de alimentação.
2. Retire toda a rede e cabos SAS, se aplicável.
3. Retire o aparelho da prateleira.
4. Retire cada uma das unidades e observe as ranhuras das quais são removidas. Para mais informações, consulte [Remova a unidade do disco](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. No recinto eBOD (se este for o chassis que falhou), retire os módulos do controlador EBOD. Para mais informações, consulte [Remova um controlador EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    No recinto primário (se este for o chassis que falhou), retire os controladores e observe as ranhuras das quais são removidos. Para mais informações, consulte [Remova um controlador](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instale o chassis
Execute os seguintes passos para instalar o chassis no seu dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Para instalar um chassis
1. Monte o chassis na prateleira. Para mais informações, consulte [rack-mount o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [rack-mount your StorSimple 8600 device](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Depois de o chassis ser montado na cremalheira, instale os módulos do controlador nas mesmas posições em que foram previamente instalados.
3. Instale as unidades nas mesmas posições e ranhuras onde foram previamente instaladas.
   
   > [!NOTE]
   > Recomendamos que instale primeiro os SSDs nas ranhuras e, em seguida, instale os HDDs.
  
4. Com o dispositivo montado na cremalheira e os componentes instalados, ligue o seu dispositivo às fontes de alimentação adequadas e ligue o dispositivo. Para mais detalhes, consulte [o cabo do seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou cabo o seu dispositivo [StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

