---
title: Lâmina de resumo do serviço StorSimple Virtual Array [ StorSimple Virtual Array] Microsoft Docs
description: Descreve a lâmina de resumo do serviço para o StorSimple Device Manager e explica como usá-la para monitorizar a saúde do seu StorSimple Virtual Array.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "64720732"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Utilize a lâmina de resumo de serviço para O Gestor de Dispositivos StorSimple ligado ao StorSimple Virtual Array
## <a name="overview"></a>Descrição geral
A lâmina de resumo de serviço para o StorSimple Device Manager fornece uma visão sumária das Matrizes Virtuais StorSimple (também conhecidas como Dispositivos virtuais no local StorSimple ou dispositivos virtuais) que estão ligadas ao seu serviço, destacando aqueles que precisam de um sistema atenção do administrador. Este tutorial introduz a lâmina de resumo do serviço, explica o conteúdo e a função, e descreve as tarefas que pode executar a partir desta lâmina.

![Painel de serviço](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Comandos de gestão e essenciais
Na lâmina de resumo StorSimple, vê as opções para gerir o seu serviço StorSimple Device Manager, bem como as matrizes virtuais registadas neste serviço. Vê-se os comandos de gestão na parte superior da lâmina e no lado esquerdo.

Utilize estas opções para realizar várias operações, tais como adicionar ações ou volumes, ou monitorizar os vários trabalhos em execução nas matrizes virtuais.

A área essencial captura algumas das propriedades importantes, tais como o grupo de recursos, localização e subscrição em que o seu StorSimple Device Manager foi criado.

## <a name="storsimple-device-manager-service-summary"></a>Resumo do serviço StorSimple Device Manager
* O **azulejo Alerts** fornece uma imagem instantânea de todos os alertas ativos em todos os dispositivos virtuais, agrupados por gravidade de alerta. Clicar no azulejo abre a lâmina **alertas,** onde pode clicar num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema tiver sido resolvido.
* Os ecrãs de azulejos **da Capacidade** mostram o armazenamento primário que é aprovisionado e permanece em todos os dispositivos virtuais em relação ao armazenamento total disponível em todos os dispositivos virtuais. **O provisionado** refere-se à quantidade de armazenamento que é preparado e atribuído para utilização, **remanescente** refere-se à capacidade restante que pode ser aprovisionada em todos os dispositivos virtuais. A capacidade restante de **tiered** é a capacidade disponível que pode ser aprovisionada, incluindo a nuvem, enquanto o **Restante Local** é a capacidade restante nos discos ligados às matrizes virtuais.
* Na tabela **de utilização,** pode ver as métricas relevantes para os seus dispositivos virtuais. Pode ver o armazenamento primário utilizado em todos os dispositivos virtuais, bem como o armazenamento em nuvem consumido por dispositivos virtuais ao longo dos últimos 7 dias, o período de tempo padrão. Utilize a opção **Editar** no canto superior direito da tabela para escolher uma escala de tempo diferente.
* O azulejo **do Dispositivo** fornece um resumo do número de matrizes virtuais no seu StorSimple Device Manager agrupados pelo estado do dispositivo. Clique neste azulejo para abrir a lâmina da lista de **Dispositivos** e, em seguida, clique num dispositivo individual para perfurar o resumo do dispositivo específico do dispositivo. Também pode executar ações específicas do dispositivo a partir de uma determinada lâmina de resumo do dispositivo. Para obter mais informações sobre a lâmina de resumo do dispositivo, vá à [lâmina de resumo](storsimple-virtual-array-device-summary.md)do dispositivo .

## <a name="view-the-activity-logs"></a>Ver os registos de atividade
Para visualizar as várias operações realizadas dentro do seu Gestor de Dispositivos StorSimple, clique no link de **registos de atividade** no lado esquerdo da sua lâmina de resumo de serviço StorSimple. Isto leva-o à lâmina de **registos de atividade,** onde pode ver um resumo das operações recentes realizadas.

![Registos de Atividade](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Passos seguintes
Aprenda a [usar a UI web local para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

