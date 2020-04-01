---
title: Utilize lâmina de resumo de serviço para dispositivo da série StorSimple 8000
description: Descreve a lâmina de resumo do serviço StorSimple e explica como usá-la para monitorizar a saúde da sua solução StorSimple.
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
ms.openlocfilehash: 14c37227a7e8598ee6f8f3adc62acd69dd274475
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396497"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Utilize a lâmina de resumo de serviço para dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

A lâmina de resumo do serviço StorSimple Device Manager fornece uma visão sumária de todos os dispositivos que estão ligados ao serviço StorSimple Device Manager, destacando os dispositivos que precisam da atenção de um administrador do sistema. Este tutorial introduz a lâmina de resumo do serviço, explica o conteúdo e função do painel de instrumentos, e descreve as tarefas que pode executar a partir desta página.

![Resumo do serviço](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Comandos de gestão

Na lâmina de resumo do serviço StorSimple, vê as opções para gerir o seu serviço StorSimple Device Manager e os dispositivos da série StorSimple 8000 registados neste serviço. Vê-se os comandos de gestão na parte superior da lâmina e no lado esquerdo.

![Barra de comandos](./media/storsimple-8000-service-dashboard/service-summary2.png)

Utilize estas opções para realizar várias operações, tais como adicionar partilhas ou volumes, ou monitorizar os vários trabalhos em execução nos dispositivos StorSimple.


## <a name="essentials"></a>Essentials

A área essencial captura algumas das propriedades importantes, tais como o grupo de recursos, localização e subscrição em que o seu StorSimple Device Manager foi criado.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Resumo do serviço StorSimple Device Manager

* O **azulejo Alerts** fornece uma imagem instantânea de todos os alertas ativos em todos os dispositivos, agrupados por gravidade de alerta.

    ![Alertas de azulejos](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Clicar no azulejo abre a lâmina **alertas,** onde pode clicar num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema tiver sido resolvido.

    ![Clique em azulejo sintetizar alertas](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Os ecrãs de **azulejos** da Capacidade mostram o armazenamento primário que é aprovisionado e permanece em todos os dispositivos em relação ao armazenamento total disponível em todos os dispositivos. **O provisionado** refere-se à quantidade de armazenamento que é preparado e atribuído para utilização, **remanescente** refere-se à capacidade restante que pode ser aprovisionada em todos os dispositivos.

    ![Telha de capacidade](./media/storsimple-8000-service-dashboard/service-summary6.png)

    A capacidade restante de **Tiered** é a capacidade disponível que pode ser aprovisionada, incluindo a nuvem, enquanto o **Restante Local** é a capacidade restante nos discos ligados aos dispositivos da série StorSimple 8000.


* Na tabela **de utilização,** pode ver as métricas relevantes para os seus dispositivos. Pode visualizar o armazenamento primário utilizado em todos os dispositivos, e o armazenamento em nuvem consumido pelos dispositivos ao longo dos últimos 7 dias, o período de tempo padrão. 

    ![Telha de uso](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Para escolher uma escala de tempo diferente, utilize a opção **Editar** no canto superior direito da tabela.

     ![Clique em azulejo de uso](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Dados do gráfico de exportação](./media/storsimple-8000-service-dashboard/service-summary11.png)

* O azulejo **dispositivos** fornece um resumo do número de dispositivos da série StorSimple 8000 no seu StorSimple Device Manager agrupado pelo estado do dispositivo. 

    ![Telha de dispositivos](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Clique neste azulejo para abrir a lâmina da lista de **Dispositivos** e, em seguida, clique num dispositivo individual para perfurar o resumo do dispositivo específico do dispositivo. Também pode executar ações específicas do dispositivo a partir de uma determinada lâmina de resumo do dispositivo. Para obter mais informações sobre a lâmina de resumo do dispositivo, vá à [lâmina de resumo](storsimple-8000-device-dashboard.md)do dispositivo .

    ![Clique em azulejo de dispositivos](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Ver os registos de atividade

Para visualizar as várias operações realizadas dentro do seu Gestor de Dispositivos StorSimple, clique no link de **registos de atividade** no lado esquerdo da sua lâmina de resumo de serviço StorSimple. Isto leva-o à lâmina de **registos de atividade,** onde pode ver um resumo das operações recentes realizadas.

![Registos de Atividade](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

