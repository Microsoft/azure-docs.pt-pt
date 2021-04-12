---
title: StorSimple Virtual Array | Microsoft Docs
description: Descreve a lâmina de resumo do dispositivo para O Gestor de Dispositivos StorSimple e explica como usá-la para monitorizar a saúde do seu StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005882"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Utilize a lâmina de resumo do dispositivo para o Gestor de Dispositivos StorSimple ligado ao StorSimple Virtual Array

## <a name="overview"></a>Descrição Geral

A lâmina do gestor de dispositivos StorSimple fornece uma visão sumária de um StorSimple Virtual Array que está registado com um determinado Gestor de Dispositivos StorSimple, destacando os problemas do dispositivo que precisam da atenção de um administrador de sistema. Este tutorial introduz a lâmina de resumo do dispositivo, explica o conteúdo e a função e descreve as tarefas que pode executar a partir desta lâmina.

A lâmina de resumo do dispositivo apresenta as seguintes informações:

![Dashboard do dispositivo](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Gestão

Na lâmina do dispositivo StorSimple, vê as opções de gestão do seu dispositivo StorSimple. Vê-se os comandos de gestão na parte superior da lâmina e no lado esquerdo. Utilize estas opções para adicionar ações ou volumes, ou atualizar ou falhar sobre o seu array virtual.

A área essencial captura algumas das propriedades importantes, tais como, o estado, o modelo, a versão do software, bem como uma ligação à **UI Web** da matriz. Se estiver numa rede interna, pode lançar diretamente o [UI web local](storsimple-ova-web-ui-admin.md) para administrar a sua matriz virtual.

![Elementos essenciais do dispositivo](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Resumo do dispositivo StorSimple

* O **azulejo Alerts** fornece uma imagem instantânea de todos os alertas ativos para a sua matriz virtual, agrupadas por gravidade de alerta. Clique no azulejo para abrir a lâmina **alertas** e, em seguida, clique num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema tiver sido resolvido.

* O azulejo **da Capacidade** exibe o armazenamento primário que é a provisionado e permanece em todo o dispositivo virtual em relação ao armazenamento total disponível para o mesmo. **O Provisionado** refere-se à quantidade de armazenamento preparada e alocada para utilização, **o Restante** refere-se à capacidade remanescente que pode ser alocado em todo este dispositivo. A capacidade **restante tiered** é a capacidade disponível que pode ser a provisionada incluindo a nuvem, enquanto o **Local Restante** é a capacidade restante nos discos ligados a esta matriz virtual.

* Na tabela **De Utilização,** pode ver o armazenamento primário utilizado em toda a sua matriz virtual, bem como o armazenamento em nuvem consumido ao longo dos últimos 7 dias, o período de tempo padrão. Utilize a opção **Editar** no canto superior direito da tabela para escolher uma escala de tempo diferente.

* O azulejo **de Ações** ou **Volumes** fornece um resumo do número de ações ou volumes no seu dispositivo agrupados por estado. Clique no azulejo para abrir a lâmina da lista **de Partilhas**  ou **Volumes** e, em seguida, clique numa partilha ou volume individual para visualizar ou modificar as suas propriedades. Para obter mais informações, consulte como [gerir ações](storsimple-virtual-array-manage-shares.md) ou [gerir volumes.](storsimple-virtual-array-manage-volumes.md)

## <a name="next-steps"></a>Passos seguintes
Aprenda a:
- [Gerir ações numa Matriz Virtual StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Gerir volumes numa Matriz Virtual StorSimple](storsimple-virtual-array-manage-volumes.md)

