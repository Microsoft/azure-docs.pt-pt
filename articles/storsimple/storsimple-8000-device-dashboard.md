---
title: Utilize o resumo do dispositivo da série StorSimple 8000 [ Resumo do dispositivo da série StorSimple 8000 ] Microsoft Docs
description: Descreve o resumo do dispositivo de serviço StorSimple Device Manager e como usá-lo para visualizar métricas de armazenamento e iniciadores ligados e encontrar o número de série e o IQN.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 1d88af2c0739c30b2562bad7660015b890e8159c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60578322"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Utilize o resumo do dispositivo no serviço StorSimple Device Manager

## <a name="overview"></a>Descrição geral
A lâmina de resumo do dispositivo StorSimple dá-lhe uma visão geral das informações para um dispositivo StorSimple específico, em contraste com a lâmina de resumo do serviço, que lhe dá informações sobre todos os dispositivos incluídos na sua solução Microsoft Azure StorSimple.

A lâmina de resumo do dispositivo fornece uma visão sumária de um dispositivo da série StorSimple 8000 que está registado com um determinado Gestor de Dispositivos StorSimple, destacando os problemas do dispositivo que precisam da atenção de um administrador do sistema. Este tutorial introduz a lâmina de resumo do dispositivo, explica o conteúdo e a função, e descreve as tarefas que pode executar a partir desta lâmina.

A lâmina de resumo do dispositivo apresenta as seguintes informações:

![Lâmina de resumo do dispositivo](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Barra de comando de gestão

Na lâmina do dispositivo StorSimple, vê as opções para gerir o seu dispositivo StorSimple. Vê-se os comandos de gestão na parte superior da lâmina e no lado esquerdo. Utilize estas opções para adicionar ações ou volumes, ou atualizar ou falhar sobre o seu dispositivo.

![Barra de comando de gestão](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

A área essencial captura algumas das propriedades importantes, tais como, o estado, o modelo, o target IQN e a versão do software. 

![Elemento essencial do dispositivo](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorização

* O azulejo **Alerts** fornece uma imagem instantânea de todos os alertas ativos para o seu dispositivo, agrupados por gravidade de alerta.

    ![Telha alerta](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Clique no azulejo para abrir a lâmina **alertas** e, em seguida, clique num alerta individual para ver detalhes adicionais sobre esse alerta, incluindo quaisquer ações recomendadas. Também pode limpar o alerta se o problema tiver sido resolvido.

    ![Clique em azulejo de alerta](./media/storsimple-8000-device-dashboard/device-summary10.png)

* O Estado e o azulejo de **saúde** fornecem informações sobre a saúde do componente de hardware para um dispositivo, incluindo o estado do dispositivo. O estado do dispositivo pode estar offline, on-line, desativado ou pronto para ser configurado.

    ![Estado e azulejode saúde](./media/storsimple-8000-device-dashboard/device-summary5.png)

* O azulejo **Volumes** fornece um resumo do número de volumes no seu dispositivo agrupados pelo estado.

    ![Azulejo sintetário](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Clique no azulejo para abrir a lâmina da lista **de Volumes** e, em seguida, clique num volume individual para visualizar ou modificar as suas propriedades.
    
    ![Clique em azulejo de volumes](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Para mais informações, consulte como [gerir volumes](storsimple-8000-manage-volumes-u2.md).

* Na tabela **de utilização,** pode visualizar o armazenamento primário utilizado em todo o seu dispositivo, e o armazenamento em nuvem consumido nos últimos 7 dias, o período de tempo padrão.

     ![Telha de uso](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Para escolher uma escala de tempo diferente, utilize a opção **Editar** no canto superior direito da tabela.

     ![Editar gráfico de utilização](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Neste gráfico, pode ver métricas para o armazenamento primário total (a quantidade de dados escritos pelos anfitriões do seu dispositivo) e o armazenamento total em nuvem consumido pelo seu dispositivo durante um período de tempo.
  
     Neste contexto, o *armazenamento primário* refere-se à quantidade total de dados escritos pelo anfitrião, e pode ser discriminado por tipo de volume: o armazenamento *primário hierárquico* inclui dados armazenados localmente e dados hierarquizados para a nuvem. *O armazenamento primário localmente fixado* inclui apenas dados armazenados localmente. O armazenamento em *nuvem,* por outro lado, é uma medição da quantidade total de dados armazenados na nuvem. Este armazenamento inclui dados e cópias de segurança hierárquicos. Os dados armazenados na nuvem são desduplicados e comprimidos, enquanto o armazenamento primário indica a quantidade de armazenamento utilizado antes de os dados ser desduplicado e comprimido. (Pode comparar estes dois números para ter uma ideia da taxa de compressão.) Tanto para o armazenamento primário como para a nuvem, as quantidades mostradas baseiam-se na frequência de rastreio que configura. Por exemplo, se escolher uma frequência de uma semana, então o gráfico mostra dados para cada dia na semana anterior.

     Para ver a quantidade de armazenamento em nuvem consumida ao longo do tempo, selecione a opção ARMAZENAMENTO EM **NUVEM USADA.** Para ver o armazenamento total que foi escrito pelo anfitrião, selecione o **ARMAZENAMENTO PRIMÁRIO DE TIERED USADO** e as opções usadas de ARMAZENAMENTO LOCAL **PRIMÁRIO.** 
     Para mais informações, consulte [Utilize o serviço StorSimple Device Manager para monitorizar o seu dispositivo StorSimple](storsimple-monitor-device.md).


* O azulejo da **Capacidade** apresenta o armazenamento primário que é aprovisionado e permanece em todo o dispositivo em relação ao armazenamento total disponível para o mesmo. **O provisionado** refere-se à quantidade de armazenamento que é preparado e atribuído para utilização, **remanescente** refere-se à capacidade restante que pode ser aprovisionada através deste dispositivo. 

    ![Telha de uso](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Clique neste azulejo para ver como a capacidade é aprovisionada em volumes hierárquicos e fixados localmente. A capacidade restante de **tiered** é a capacidade disponível que pode ser aprovisionada, incluindo a nuvem, enquanto o **Restante Local** é a capacidade restante nos discos ligados a este dispositivo.

    ![Clique na tabela de utilização](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a lâmina de resumo do [serviço StorSimple](storsimple-8000-service-dashboard.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

