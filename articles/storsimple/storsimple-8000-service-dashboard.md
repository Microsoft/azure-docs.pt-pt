---
title: Utilize a lâmina sumária de serviço para dispositivo storSimple 8000
description: Descreve a lâmina de resumo do serviço StorSimple e explica como usá-la para monitorizar a saúde da sua solução StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 7b4b697b3d27b57212fc59396e1f8111e297d6cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514940"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Utilize a lâmina de resumo de serviço para o dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição Geral

A lâmina de resumo do serviço StorSimple Device Manager fornece uma visão sumária de todos os dispositivos que estão ligados ao serviço StorSimple Device Manager, realçando os dispositivos que precisam da atenção de um administrador do sistema. Este tutorial introduz a lâmina de resumo do serviço, explica o conteúdo e função do painel de instrumentos e descreve as tarefas que pode executar a partir desta página.

![Resumo do serviço](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Comandos de gestão

Na lâmina de resumo do serviço StorSimple, vê as opções para gerir o seu serviço StorSimple Device Manager e os dispositivos da série StorSimple 8000 registados neste serviço. Vê-se os comandos de gestão na parte superior da lâmina e no lado esquerdo.

![Barra de comandos](./media/storsimple-8000-service-dashboard/service-summary2.png)

Utilize estas opções para executar várias operações, tais como adicionar ações ou volumes, ou monitorizar os vários trabalhos em execução nos dispositivos StorSimple.


## <a name="essentials"></a>Essentials

A área essencial captura algumas das propriedades importantes, tais como, o grupo de recursos, localização e subscrição em que o seu StorSimple Device Manager foi criado.

![Essentials](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Resumo do serviço StorSimple Device Manager

* O **azulejo Alerts** fornece uma imagem instantânea de todos os alertas ativos em todos os dispositivos, agrupados por gravidade de alerta.

    ![Azulejos de alerta](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Clicar no azulejo abre a lâmina **Alertas,** onde pode clicar num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema tiver sido resolvido.

    ![Clique em alertas de azulejos](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Os ecrãs de azulejos **de capacidade** mostram o armazenamento primário que é a provisionado e permanece em todos os dispositivos em relação ao armazenamento total disponível em todos os dispositivos. **O Provisionado** refere-se à quantidade de armazenamento preparada e alocada para utilização, **o Restante** refere-se à capacidade remanescente que pode ser alocada em todos os dispositivos.

    ![Azulejo de capacidade](./media/storsimple-8000-service-dashboard/service-summary6.png)

    A capacidade **restante tiered** é a capacidade disponível que pode ser a provisionada incluindo a nuvem, enquanto o **Local Restante** é a capacidade restante nos discos ligados aos dispositivos da série StorSimple 8000.


* Na tabela **de utilização,** pode ver as métricas relevantes para os seus dispositivos. Pode ver o armazenamento primário utilizado em todos os dispositivos e o armazenamento na nuvem consumido pelos dispositivos ao longo dos últimos 7 dias, o período de tempo padrão. 

    ![Azulejo de utilização](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Para escolher uma escala de tempo diferente, utilize a opção **Edit no** canto superior direito da tabela.

     ![Clique em azulejo de utilização](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Dados do mapa de exportação](./media/storsimple-8000-service-dashboard/service-summary11.png)

* O **azulejo devices** fornece um resumo do número de dispositivos da série StorSimple 8000 no seu StorSimple Device Manager agrupados pelo estado do dispositivo. 

    ![Azulejos de dispositivos](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Clique neste azulejo para abrir a lâmina da lista **de Dispositivos** e, em seguida, clique num dispositivo individual para perfurar o resumo do dispositivo específico do dispositivo. Também pode executar ações específicas do dispositivo a partir de uma determinada lâmina de resumo do dispositivo. Para obter mais informações sobre a lâmina de resumo do dispositivo, aceda à [lâmina de resumo do dispositivo](storsimple-8000-device-dashboard.md).

    ![Clique em azulejos de dispositivos](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Ver os registos de atividade

Para visualizar as várias operações realizadas dentro do seu Gestor de Dispositivos StorSimple, clique no link **de registos** de atividade no lado esquerdo da sua lâmina de resumo de serviço StorSimple. Isto leva-o à lâmina de **registos de atividade,** onde pode ver um resumo das operações recentes realizadas.

![Registos de Atividade](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

