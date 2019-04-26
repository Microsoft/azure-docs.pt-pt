---
title: O painel de resumo de serviço do StorSimple Virtual Array | Documentos da Microsoft
description: Descreve o painel de resumo do serviço do StorSimple Device Manager e explica como usá-lo para monitorizar o estado de funcionamento da sua matriz Virtual do StorSimple.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 9c05bddaeb3c34400db1ec75c624ef00a85d9444
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337930"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Utilize o painel de resumo do serviço do StorSimple Device Manager ligado ao StorSimple Virtual Array
## <a name="overview"></a>Descrição geral
O painel de resumo do serviço do StorSimple Device Manager fornece um resumo do StorSimple Virtual matrizes (também conhecido como StorSimple no local dispositivos virtuais ou dispositivos virtuais) que estão ligadas ao seu serviço, realce os que precisa de um sistema atenção do administrador. Este tutorial apresenta o painel de resumo do serviço, explica o conteúdo e a função e descreve as tarefas que pode efetuar a partir deste painel.

![Dashboard de serviço](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Comandos de gestão e o essentials
No painel de resumo do StorSimple, pode ver as opções para gerir o seu serviço StorSimple Device Manager, bem como as matrizes virtuais registadas para este serviço. Consulte os comandos de gestão na parte superior do painel e no lado esquerdo.

Utilize estas opções para executar várias operações, como adicionar partilhas ou volumes ou monitor as várias tarefas em execução nas matrizes virtuais.

A área do essentials captura algumas das propriedades importantes, como o grupo de recursos, a localização e subscrição na qual o seu StorSimple Device Manager foi criado.

## <a name="storsimple-device-manager-service-summary"></a>Resumo do serviço StorSimple Device Manager
* O **alertas** mosaico fornece um instantâneo de todos os alertas ativos em todos os dispositivos virtuais, agrupados por gravidade do alerta. Clicar no mosaico abre o **alertas** painel, onde pode clicar num alerta individual para ver detalhes adicionais sobre o alerta, incluindo as ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.
* O **capacidade** apresenta mosaico mostra o armazenamento primário que está aprovisionado e restantes em todos os dispositivos virtuais em relação a memória total disponível em todos os dispositivos virtuais. **Aprovisionado** refere-se para a quantidade de armazenamento que é preparado e alocado para uso, **restante** refere-se para a capacidade restante, que pode ser aprovisionada em todos os dispositivos virtuais. O **camadas restantes** capacidade é a capacidade disponível que pode ser aprovisionada incluindo cloud, enquanto o **restantes Local** é a capacidade restante nos discos ligados para as matrizes virtuais.
* Na **utilização** gráfico, pode ver as métricas relevantes para os seus dispositivos virtuais. Pode ver o armazenamento primário utilizado em todos os dispositivos virtuais, bem como o armazenamento na cloud consumidos por dispositivos virtuais nos últimos sete dias, a predefinição do período de tempo. Utilize o **editar** opção no canto superior direito do gráfico para escolher uma escala de tempo diferentes.
* O **dispositivos** mosaico fornece um resumo do número de matrizes virtuais no seu StorSimple Device Manager agrupadas por Estado de dispositivo. Clique neste mosaico para abrir o **dispositivos** painel de lista e, em seguida, clique num dispositivo individual para explorar o resumo de dispositivo específico para o dispositivo. Também pode executar ações específicas de dispositivo a partir de um painel de resumo de determinado dispositivo. Para obter mais informações sobre o painel de resumo do dispositivo, aceda a [painel de resumo do dispositivo](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Ver os registos de atividade
Para ver as diversas operações executadas dentro do StorSimple Device Manager, clique a **registos de atividades** ligação no lado esquerdo do painel de resumo de serviço do StorSimple. Isto leva-o para o **registos de atividades** painel, onde pode ver um resumo das operações recentes levadas a cabo.

![Registos de Atividade](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Passos Seguintes
Saiba como [utilizar a IU web local para administrar a sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

