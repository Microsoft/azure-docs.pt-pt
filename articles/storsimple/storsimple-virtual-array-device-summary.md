---
title: O painel de resumo de dispositivo do StorSimple Virtual Array | Documentos da Microsoft
description: Descreve o painel de resumo do dispositivo do StorSimple Device Manager e explica como usá-lo para monitorizar o estado de funcionamento da sua matriz Virtual do StorSimple.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61408511"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Utilize o painel de resumo do dispositivo do StorSimple Device Manager ligado ao StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

O painel de dispositivos do StorSimple Device Manager fornece um resumo de uma matriz Virtual StorSimple que está registado com um determinado StorSimple Device Manager, realce os problemas de dispositivos que necessitam de atenção de um administrador de sistema. Este tutorial apresenta o painel de resumo do dispositivo, explica o conteúdo e a função e descreve as tarefas que pode efetuar a partir deste painel.

Painel de resumo do dispositivo apresenta as seguintes informações:

![Dashboard do dispositivo](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Gestão

No painel do dispositivo StorSimple, pode ver as opções para gerir o dispositivo StorSimple. Consulte os comandos de gestão na parte superior do painel e no lado esquerdo. Utilize estas opções para adicionar partilhas ou volumes, ou atualizar ou efetuar a ativação pós-falha sua matriz virtual.

A área do essentials captura algumas das propriedades importantes, como o estado, modelo, versão do software, bem como uma ligação para o **IU da Web** da matriz. Se estiver numa rede interna, pode iniciar diretamente os [da IU web local](storsimple-ova-web-ui-admin.md) para administrar a sua matriz virtual.

![Essentials do dispositivo](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>Resumo do dispositivo StorSimple

* O **alertas** mosaico fornece um instantâneo de todos os alertas ativos para a sua matriz virtual, agrupado por gravidade do alerta. Clique no mosaico para abrir o **alertas** painel e clique num alerta individual para ver detalhes adicionais sobre o alerta, incluindo as ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.

* O **capacidade** mosaico apresenta o armazenamento primário, que é aprovisionado e restantes entre o dispositivo virtual relativo a memória total disponível para o mesmo. **Aprovisionado** refere-se para a quantidade de armazenamento que é preparado e alocado para uso, **restante** refere-se para a capacidade restante, que pode ser aprovisionada neste dispositivo. O **camadas restantes** capacidade é a capacidade disponível que pode ser aprovisionada incluindo cloud, enquanto o **restantes Local** é a capacidade restante nos discos ligados a esta matriz virtual.

* Na **utilização** gráfico, pode ver o armazenamento primário utilizado em sua matriz virtual, bem como o armazenamento na cloud consumidas durante os últimos sete dias, a predefinição do período de tempo. Utilize o **editar** opção no canto superior direito do gráfico para escolher uma escala de tempo diferentes.

* O **partilhas** ou **Volumes** mosaico fornece um resumo do número de partilhas ou volumes no seu dispositivo, agrupadas por Estado. Clique no mosaico para abrir o **partilhas** ou **Volumes** painel de lista e, em seguida, clique num compartilhamento individual ou um volume para ver ou modificar as respetivas propriedades. Para obter mais informações, consulte como [gerir partilhas](storsimple-virtual-array-manage-shares.md) ou [gerir volumes](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Passos Seguintes
Aprenda a:
- [Gerir partilhas numa matriz Virtual StorSimple](storsimple-virtual-array-manage-shares.md)
    
- [Gerir volumes numa matriz Virtual StorSimple](storsimple-virtual-array-manage-volumes.md)

