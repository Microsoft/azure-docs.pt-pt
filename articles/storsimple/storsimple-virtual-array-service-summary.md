---
title: StorSimple Folha de serviço virtual array | Microsoft Docs
description: Descreve a lâmina de resumo de serviço para StorSimple Device Manager e explica como usá-la para monitorizar a saúde do seu StorSimple Virtual Array.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 31220a8f8b012e08d46195b60a26be9cfc261be5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85514524"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Utilize a lâmina de resumo de serviço para o Gestor de Dispositivos StorSimple ligado ao StorSimple Virtual Array
## <a name="overview"></a>Descrição Geral
A lâmina de resumo de serviço para o StorSimple Device Manager fornece uma visão sumária das Matrizes Virtuais StorSimple (também conhecidas como dispositivos virtuais storSimple no local ou dispositivos virtuais) que estão ligadas ao seu serviço, realçando aqueles que precisam da atenção de um administrador de sistema. Este tutorial introduz a lâmina de resumo do serviço, explica o conteúdo e a função e descreve as tarefas que pode executar a partir desta lâmina.

![Painel de serviço](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Comandos de gestão e bens essenciais
Na lâmina de resumo StorSimple, vê as opções de gestão do seu serviço StorSimple Device Manager, bem como as matrizes virtuais registadas neste serviço. Vê-se os comandos de gestão na parte superior da lâmina e no lado esquerdo.

Utilize estas opções para executar várias operações, tais como adicionar ações ou volumes, ou monitorizar os vários trabalhos em execução nas matrizes virtuais.

A área essencial captura algumas das propriedades importantes, tais como, o grupo de recursos, localização e subscrição em que o seu StorSimple Device Manager foi criado.

## <a name="storsimple-device-manager-service-summary"></a>Resumo do serviço StorSimple Device Manager
* O **azulejo Alerts** fornece uma imagem instantânea de todos os alertas ativos em todos os dispositivos virtuais, agrupados por gravidade de alerta. Clicar no azulejo abre a lâmina **Alertas,** onde pode clicar num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema tiver sido resolvido.
* Os ecrãs de azulejos **de capacidade** mostram o armazenamento primário que é a provisionado e permanece em todos os dispositivos virtuais em relação ao armazenamento total disponível em todos os dispositivos virtuais. **O Provisionado** refere-se à quantidade de armazenamento que é preparada e atribuída para utilização, **o Restante** refere-se à capacidade remanescente que pode ser alocada em todos os dispositivos virtuais. A capacidade **restante tiered** é a capacidade disponível que pode ser a provisionada incluindo a nuvem, enquanto o **Local Restante** é a capacidade restante nos discos ligados às matrizes virtuais.
* Na tabela **de utilização,** pode ver as métricas relevantes para os seus dispositivos virtuais. Pode ver o armazenamento primário utilizado em todos os dispositivos virtuais, bem como o armazenamento em nuvem consumido por dispositivos virtuais ao longo dos últimos 7 dias, o período de tempo padrão. Utilize a opção **Editar** no canto superior direito da tabela para escolher uma escala de tempo diferente.
* O **azulejo devices** fornece um resumo do número de matrizes virtuais no seu StorSimple Device Manager agrupados pelo estado do dispositivo. Clique neste azulejo para abrir a lâmina da lista **de Dispositivos** e, em seguida, clique num dispositivo individual para perfurar o resumo do dispositivo específico do dispositivo. Também pode executar ações específicas do dispositivo a partir de uma determinada lâmina de resumo do dispositivo. Para obter mais informações sobre a lâmina de resumo do dispositivo, aceda à [lâmina de resumo do dispositivo](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Ver os registos de atividade
Para visualizar as várias operações realizadas dentro do seu Gestor de Dispositivos StorSimple, clique no link **de registos** de atividade no lado esquerdo da sua lâmina de resumo de serviço StorSimple. Isto leva-o à lâmina de **registos de atividade,** onde pode ver um resumo das operações recentes realizadas.

![Registos de Atividade](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Passos seguintes
Saiba como [utilizar a UI web local para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

