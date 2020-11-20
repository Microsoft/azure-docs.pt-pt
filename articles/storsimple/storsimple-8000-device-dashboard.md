---
title: Use o resumo do dispositivo no serviço StorSimple Device Manager
description: Descreve o resumo do dispositivo de serviço StorSimple Device Manager e como usá-lo para visualizar métricas de armazenamento e iniciadores conectados e encontrar o número de série e IQN.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 34bbf4d723e46663efe77560245db74d9dea1fc4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957916"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Utilize o resumo do dispositivo no serviço StorSimple Device Manager

## <a name="overview"></a>Descrição geral
A lâmina de resumo do dispositivo StorSimple dá-lhe uma visão geral das informações para um dispositivo StorSimple específico, em contraste com a lâmina de resumo do serviço, que lhe dá informações sobre todos os dispositivos incluídos na sua solução Microsoft Azure StorSimple.

A lâmina de resumo do dispositivo fornece uma visão sumária de um dispositivo da série StorSimple 8000 que está registado com um determinado Gestor de Dispositivos StorSimple, realçando os problemas do dispositivo que precisam da atenção de um administrador de sistema. Este tutorial introduz a lâmina de resumo do dispositivo, explica o conteúdo e a função e descreve as tarefas que pode executar a partir desta lâmina.

A lâmina de resumo do dispositivo apresenta as seguintes informações:

![Lâmina de resumo do dispositivo](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Barra de comando de gestão

Na lâmina do dispositivo StorSimple, vê as opções de gestão do seu dispositivo StorSimple. Vê-se os comandos de gestão na parte superior da lâmina e no lado esquerdo. Utilize estas opções para adicionar ações ou volumes, ou atualizar ou falhar sobre o seu dispositivo.

![Barra de comando de gestão](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

A área essencial captura algumas das propriedades importantes, tais como, o estado, o modelo, o IQN alvo e a versão do software. 

![Elementos essenciais do dispositivo](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorização

* O **azulejo Alerts** fornece uma imagem instantânea de todos os alertas ativos para o seu dispositivo, agrupados por gravidade de alerta.

    ![Azulejo de alerta](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Clique no azulejo para abrir a lâmina **alertas** e, em seguida, clique num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema tiver sido resolvido.

    ![Clique em azulejo de alerta](./media/storsimple-8000-device-dashboard/device-summary10.png)

* O **Estado e o** azulejo sanitário fornecem informações sobre a saúde do componente de hardware para um dispositivo, incluindo o estado do dispositivo. O estado do dispositivo pode estar offline, on-line, desativado ou pronto para ser configurado.

    ![Estado e azulejos sanitários](./media/storsimple-8000-device-dashboard/device-summary5.png)

* O **azulejo Volumes** fornece um resumo do número de volumes no seu dispositivo agrupados por estado.

    ![Azulejos de volumes](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Clique no azulejo para abrir a lâmina da lista **volumes** e, em seguida, clique num volume individual para visualizar ou modificar as suas propriedades.
    
    ![Clique em azulejos de volumes](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Para mais informações, consulte como [gerir volumes.](storsimple-8000-manage-volumes-u2.md)

* Na tabela **De Utilização,** pode visualizar o armazenamento primário utilizado em todo o seu dispositivo, e o armazenamento na nuvem consumido ao longo dos últimos 7 dias, o período de tempo padrão.

     ![Azulejo de utilização](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Para escolher uma escala de tempo diferente, utilize a opção **Edit no** canto superior direito da tabela.

     ![Editar gráfico de utilização](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Neste gráfico, pode visualizar métricas para o armazenamento primário total (a quantidade de dados escritos pelos anfitriões do seu dispositivo) e o armazenamento total de nuvem consumido pelo seu dispositivo durante um período de tempo.
  
     Neste contexto, o *armazenamento primário* refere-se à quantidade total de dados escritos pelo hospedeiro, e pode ser discriminado por tipo de volume: o armazenamento *primário hierárquico* inclui dados armazenados localmente e dados hierárquicos na nuvem. *O armazenamento primário localmente fixado* inclui apenas dados armazenados localmente. *O armazenamento* em nuvem , por outro lado, é uma medição da quantidade total de dados armazenados na nuvem. Este armazenamento inclui dados hierárquicos e cópias de segurança. Os dados armazenados na nuvem são desduplicados e comprimidos, enquanto o armazenamento primário indica a quantidade de armazenamento utilizada antes de os dados serem desativados e comprimidos. (Pode comparar estes dois números para ter uma ideia da taxa de compressão.) Para o armazenamento primário e em nuvem, as quantidades mostradas baseiam-se na frequência de rastreio que configura. Por exemplo, se escolher uma frequência de uma semana, o gráfico mostra dados para cada dia na semana anterior.

     Para ver a quantidade de armazenamento em nuvem consumida ao longo do tempo, selecione a opção **CLOUD STORAGE USED.** Para ver o armazenamento total que foi escrito pelo anfitrião, selecione as opções **DE ARMAZENAMENTO TIERED PRIMÁRIO UTILIZADAs** e **DE ARMAZENAMENTO PRIMÁRIO FIXADO LOCALMENTE USADO.** 
     Para obter mais informações, consulte [o serviço StorSimple Device Manager para monitorizar o seu dispositivo StorSimple](./storsimple-8000-monitor-device.md).


* O azulejo **da Capacidade** exibe o armazenamento primário que é a provisionado e permanece em todo o dispositivo em relação ao armazenamento total disponível para o mesmo. **O Provisionado** refere-se à quantidade de armazenamento preparada e alocada para utilização, **o Restante** refere-se à capacidade remanescente que pode ser alocado em todo este dispositivo. 

    ![Azulejo de utilização 2](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Clique neste azulejo para ver como a capacidade é alocada em volumes hierárquicos e fixados localmente. A capacidade **restante tiered** é a capacidade disponível que pode ser a provisionada incluindo a nuvem, enquanto o **Local Restante** é a capacidade restante nos discos ligados a este dispositivo.

    ![Clique no gráfico de utilização](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a lâmina de resumo do [serviço StorSimple](storsimple-8000-service-dashboard.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).