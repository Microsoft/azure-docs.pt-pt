---
title: Utilize StorSimple 8000 series resumo do dispositivo | Documentos da Microsoft
description: Descreve o painel de resumo do serviço StorSimple e explica como usá-lo para monitorizar o estado de funcionamento da solução StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633148"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Utilize o painel de resumo de serviço para o dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

Painel de resumo do serviço StorSimple Device Manager fornece um resumo de todos os dispositivos que estão ligados ao serviço StorSimple Device Manager, realce os dispositivos que necessitam de atenção de um administrador de sistema. Este tutorial apresenta o painel de resumo do serviço, explica a função e o conteúdo de dashboard e descreve as tarefas que pode efetuar a partir desta página.

![Resumo do serviço](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Comandos de gestão

No painel Resumo do serviço do StorSimple, pode ver as opções para gerir o seu serviço StorSimple Device Manager e os dispositivos da série StorSimple 8000 registados para este serviço. Consulte os comandos de gestão na parte superior do painel e no lado esquerdo.

![Barra de comandos](./media/storsimple-8000-service-dashboard/service-summary2.png)

Utilize estas opções para executar várias operações, como adicionar partilhas ou volumes ou monitor as várias tarefas em execução em dispositivos do StorSimple.


## <a name="essentials"></a>Essentials

A área do essentials captura algumas das propriedades importantes, como o grupo de recursos, a localização e subscrição na qual o seu StorSimple Device Manager foi criado.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Resumo do serviço StorSimple Device Manager

* O **alertas** mosaico fornece um instantâneo de todos os alertas ativos em todos os dispositivos, agrupados por gravidade do alerta.

    ![Mosaico alertas](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Clicar no mosaico abre o **alertas** painel, onde pode clicar num alerta individual para ver detalhes adicionais sobre o alerta, incluindo as ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.

    ![Clique no mosaico alertas](./media/storsimple-8000-service-dashboard/service-summary8.png)

* O **capacidade** apresenta mosaico mostra o armazenamento primário que está aprovisionado e restantes em todos os dispositivos em relação a memória total disponível em todos os dispositivos. **Aprovisionado** refere-se para a quantidade de armazenamento que é preparado e alocado para uso, **restante** refere-se para a capacidade restante, que pode ser aprovisionada em todos os dispositivos.

    ![Mosaico de capacidade](./media/storsimple-8000-service-dashboard/service-summary6.png)

    O **camadas restantes** capacidade é a capacidade disponível que pode ser aprovisionada incluindo cloud, enquanto o **restantes Local** é a capacidade restante nos discos ligados para o StorSimple 8000 dispositivos de série.


* Na **utilização** gráfico, pode ver as métricas relevantes para os seus dispositivos. Pode ver o armazenamento primário utilizado em todos os dispositivos e o armazenamento na cloud consumidos pelos dispositivos nos últimos sete dias, a predefinição do período de tempo. 

    ![Mosaico utilização](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Para escolher uma escala de tempo diferente, utilize o **editar** opção no canto superior direito do gráfico.

     ![Clique no mosaico utilização](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportar dados do gráfico](./media/storsimple-8000-service-dashboard/service-summary11.png)

* O **dispositivos** mosaico fornece um resumo do número de dispositivos da série StorSimple 8000 na seu StorSimple Device Manager agrupadas por Estado de dispositivo. 

    ![Mosaico de dispositivos](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Clique neste mosaico para abrir o **dispositivos** painel de lista e, em seguida, clique num dispositivo individual para explorar o resumo de dispositivo específico para o dispositivo. Também pode executar ações específicas do dispositivo a partir de um painel de resumo de determinado dispositivo. Para obter mais informações sobre o painel de resumo do dispositivo, aceda a [painel de resumo do dispositivo](storsimple-8000-device-dashboard.md).

    ![Clique em mosaico de dispositivos](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Ver os registos de atividade

Para ver as diversas operações executadas dentro do StorSimple Device Manager, clique a **registos de atividades** ligação no lado esquerdo do painel de resumo de serviço do StorSimple. Isto leva-o para o **registos de atividades** painel, onde pode ver um resumo das operações recentes levadas a cabo.

![Registos de Atividade](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

