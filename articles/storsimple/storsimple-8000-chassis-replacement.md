---
title: Substituir o chassis no dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Descreve como remover e substituir o chassis para o seu StorSimple de bastidor principal ou o bastidor EBOD.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61312450"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Substituir o chassis no dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como remover e substituir um chassis num dispositivo da série StorSimple 8000. O modelo de StorSimple 8100 é um dispositivo de bastidor único (chassis), ao passo que o 8600 é um dispositivo de bastidor dupla (dois chassis). Para um modelo 8600, são potencialmente por um período de dois chassis, que pode falhar no dispositivo: chassis para o bastidor principal ou os chassis para a inclusão EBOD.

Em ambos os casos, o chassi de substituição que é fornecido pela Microsoft está vazio. Sem energia e refrigeração módulos do (PCMs), módulos de controlador, unidades de disco de estado sólido (SSDs), unidades de disco rígido (HDDs) ou módulos EBOD serão incluídos.

> [!IMPORTANT]
> Antes de remover e substituir os chassis, reveja as informações de segurança na [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Remover os chassis
Execute os seguintes passos para remover chassis no dispositivo StorSimple.

#### <a name="to-remove-a-chassis"></a>Para remover um chassis
1. Certifique-se de que o dispositivo StorSimple é encerrado e desligado de todas as origens de energia.
2. Remova toda a rede e cabos SAS, se aplicável.
3. Remova a unidade de rack.
4. Remova as unidades e tenha em atenção as ranhuras a partir da qual são removidas. Para obter mais informações, consulte [remover a unidade de disco](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. Na caixa EBOD (se este é os chassis que falha), remover os módulos do controlador EBOD. Para obter mais informações, consulte [remover um controlador EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    Na caixa primária (se este é os chassis que falha), remova os controladores e tenha em atenção as ranhuras a partir da qual são removidas. Para obter mais informações, consulte [remover um controlador de](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Instalar os chassis
Execute os seguintes passos para instalar o chassi no dispositivo StorSimple.

#### <a name="to-install-a-chassis"></a>Para instalar um chassis
1. Monte o chassi no rack. Para obter mais informações, consulte [dispositivo montar em Bastidor seu StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [dispositivo montar em Bastidor seu StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Depois do chassis esteja montada em rack, instale os módulos do controlador nas posições mesmo que anteriormente estivessem instalados no.
3. Instale as unidades no posições e ranhuras que anteriormente estivessem instalados no mesmo.
   
   > [!NOTE]
   > Recomendamos que instale os SSDs nas ranhuras primeiro e, em seguida, instale os HDDs.
  
4. Com o dispositivo montado em bastidor e os componentes instalados, ligue o seu dispositivo para as origens de dados apropriadas e ativar o dispositivo. Para obter detalhes, consulte [ligar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [instalar os cabos do dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

