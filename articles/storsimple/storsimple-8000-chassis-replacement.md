---
title: Substitua o chassis no dispositivo da série StorSimple 8000 Microsoft Docs
description: Descreve como remover e substituir o chassis para o seu invólucro primário StorSimple ou para o recinto EBOD.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: de0f6299f35f9d76fdade976bf70456426e5ec51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513442"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Substitua o chassis no seu dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como remover e substituir um chassis num dispositivo da série StorSimple 8000. O modelo StorSimple 8100 é um único dispositivo de caixa (um chassis), enquanto o 8600 é um dispositivo de recinto duplo (dois chassis). Para um modelo 8600, existem potencialmente dois chassis que podem falhar no dispositivo: o chassis para o recinto primário ou o chassis para o recinto EBOD.

Em qualquer dos casos, o chassis de substituição que é enviado pela Microsoft está vazio. Sem Módulos de Potência e Arrefecimento (PCMs), módulos controladores, unidades de disco de estado sólido (SSDs), discos rígidos (HDDs) ou módulos EBOD.

> [!IMPORTANT]
> Antes de remover e substituir o chassis, reveja as informações de segurança na [substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Retire o chassis
Execute os seguintes passos para remover o chassis do seu dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Para remover um chassis
1. Certifique-se de que o dispositivo StorSimple está desligado e desligado de todas as fontes de alimentação.
2. Retire todos os cabos de rede e SAS, se aplicável.
3. Retire a unidade da prateleira.
4. Retire cada uma das unidades e observe as ranhuras a partir das quais são removidas. Para obter mais informações, consulte [Remover a unidade do disco](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. No compartimento EBOD (se este for o chassis que falhou), retire os módulos do controlador EBOD. Para obter mais informações, consulte [Remover um controlador EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    No compartimento primário (se este for o chassis que falhou), retire os controladores e observe as ranhuras a partir das quais são removidas. Para obter mais informações, consulte [Remover um controlador](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instale o chassis
Execute os seguintes passos para instalar o chassis no seu dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Para instalar um chassis
1. Monte o chassis na prateleira. Para obter mais informações, consulte [o rack-mount o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [montar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Depois de montado o chassis na cremalheira, instale os módulos do controlador nas mesmas posições em que foram previamente instalados.
3. Instale as unidades nas mesmas posições e ranhuras em que foram previamente instaladas.
   
   > [!NOTE]
   > Recomendamos que instale primeiro os SSDs nas ranhuras e, em seguida, instale os HDDs.
  
4. Com o dispositivo montado na cremalheira e os componentes instalados, ligue o seu dispositivo às fontes de alimentação adequadas e ligue o dispositivo. Para mais informações, consulte [o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [cabo do seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

