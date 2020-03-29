---
title: Lâmina de resumo do dispositivo StorSimple Virtual Array [ StorSimple Virtual Array] Microsoft Docs
description: Descreve a lâmina de resumo do dispositivo para o StorSimple Device Manager e explica como usá-la para monitorizar a saúde do seu StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 9edc0b552f5c2f38e646bc4b44dd8df5c16b0457
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61408511"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Utilize a lâmina de resumo do dispositivo para o StorSimple Device Manager ligado ao StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

A lâmina do dispositivo StorSimple Device Manager fornece uma visão sumária de um StorSimple Virtual Array que está registado com um determinado Gestor de Dispositivos StorSimple, destacando os problemas do dispositivo que precisam da atenção de um administrador do sistema. Este tutorial introduz a lâmina de resumo do dispositivo, explica o conteúdo e a função, e descreve as tarefas que pode executar a partir desta lâmina.

A lâmina de resumo do dispositivo apresenta as seguintes informações:

![Dashboard do dispositivo](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Gestão

Na lâmina do dispositivo StorSimple, vê as opções para gerir o seu dispositivo StorSimple. Vê-se os comandos de gestão na parte superior da lâmina e no lado esquerdo. Utilize estas opções para adicionar ações ou volumes, ou atualizar ou falhar sobre a sua matriz virtual.

A área essencial captura algumas das propriedades importantes, tais como, o estado, modelo, versão de software, bem como uma ligação à **Web UI** da matriz. Se estiver numa rede interna, pode lançar diretamente a [UI web local](storsimple-ova-web-ui-admin.md) para administrar a sua matriz virtual.

![Elemento essencial do dispositivo](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Resumo do dispositivo StorSimple

* O azulejo **Alerts** fornece uma imagem de todos os alertas ativos para a sua matriz virtual, agrupando pela gravidade do alerta. Clique no azulejo para abrir a lâmina **alertas** e, em seguida, clique num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema tiver sido resolvido.

* O azulejo **da Capacidade** apresenta o armazenamento primário que é aprovisionado e permanece em todo o dispositivo virtual em relação ao armazenamento total disponível para o mesmo. **O provisionado** refere-se à quantidade de armazenamento que é preparado e atribuído para utilização, **remanescente** refere-se à capacidade restante que pode ser aprovisionada através deste dispositivo. A capacidade restante de **tiered** é a capacidade disponível que pode ser aprovisionada, incluindo a nuvem, enquanto o **Restante Local** é a capacidade restante nos discos ligados a esta matriz virtual.

* Na tabela **de utilização,** pode ver o armazenamento primário utilizado em toda a sua matriz virtual, bem como o armazenamento em nuvem consumido nos últimos 7 dias, o período de tempo padrão. Utilize a opção **Editar** no canto superior direito da tabela para escolher uma escala de tempo diferente.

* O azulejo **Shares** ou **Volumes** fornece um resumo do número de ações ou volumes no seu dispositivo agrupados pelo estado. Clique no azulejo para abrir a lâmina da lista **de Partilhas** ou **Volumes** e, em seguida, clique numa parte ou volume individual para visualizar ou modificar as suas propriedades. Para mais informações, consulte como [gerir ações](storsimple-virtual-array-manage-shares.md) ou [gerir volumes.](storsimple-virtual-array-manage-volumes.md)

## <a name="next-steps"></a>Passos seguintes
Aprenda a:
- [Gerir ações num Matriz Virtual StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Gerir volumes numa Matriz Virtual StorSimple](storsimple-virtual-array-manage-volumes.md)

