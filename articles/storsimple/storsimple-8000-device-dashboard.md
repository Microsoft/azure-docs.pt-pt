---
title: Utilize StorSimple 8000 series resumo do dispositivo | Documentos da Microsoft
description: Descreve o dispositivo de serviço do Gestor de dispositivos do StorSimple, resumo e como usá-lo para ver as métricas de armazenamento e iniciadores ligados e descobrir o número de série e o IQN.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578322"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Utilizar o dispositivo resumo no serviço StorSimple Device Manager

## <a name="overview"></a>Descrição geral
Painel de resumo do dispositivo StorSimple fornece uma descrição geral de informações de um dispositivo StorSimple específico, em comparação com o painel de resumo do serviço, que fornece informações sobre todos os dispositivos incluídos em sua solução do Microsoft Azure StorSimple.

Painel de resumo do dispositivo fornece um resumo de um dispositivo da série StorSimple 8000 que está registado com um determinado StorSimple Device Manager, realce os problemas de dispositivos que necessitam de atenção de um administrador de sistema. Este tutorial apresenta o painel de resumo do dispositivo, explica o conteúdo e a função e descreve as tarefas que pode efetuar a partir deste painel.

Painel de resumo do dispositivo apresenta as seguintes informações:

![Painel de resumo do dispositivo](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Barra de comandos de gestão

No painel do dispositivo StorSimple, pode ver as opções para gerir o dispositivo StorSimple. Consulte os comandos de gestão na parte superior do painel e no lado esquerdo. Utilize estas opções para adicionar partilhas ou volumes, ou atualizar ou efetuar a ativação pós-falha do seu dispositivo.

![Barra de comandos de gestão](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

A área do essentials captura algumas das propriedades importantes, como, o estado, modelo, IQN de destino e a versão do software. 

![Essentials do dispositivo](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Monitorização

* O **alertas** mosaico fornece um instantâneo de todos os alertas ativos para o seu dispositivo, agrupado por gravidade do alerta.

    ![Mosaico alerta](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Clique no mosaico para abrir o **alertas** painel e clique num alerta individual para ver detalhes adicionais sobre o alerta, incluindo as ações recomendadas. Também pode limpar o alerta se o problema foi resolvido.

    ![Clique no mosaico alerta](./media/storsimple-8000-device-dashboard/device-summary10.png)

* O **estado de funcionamento** mosaico fornece informações sobre o estado de funcionamento do componente de hardware para um dispositivo, incluindo o estado do dispositivo. O estado do dispositivo pode estar offline, online, desativado ou pronto para configurar.

    ![Mosaico de estado e estado de funcionamento](./media/storsimple-8000-device-dashboard/device-summary5.png)

* O **Volumes** mosaico fornece um resumo do número de volumes no seu dispositivo, agrupadas por Estado.

    ![Mosaico de volumes](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Clique no mosaico para abrir o **Volumes** painel de lista e, em seguida, clique num volume individual para ver ou modificar as respetivas propriedades.
    
    ![Clique no mosaico volumes](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Para obter mais informações, consulte como [gerir volumes](storsimple-8000-manage-volumes-u2.md).

* Na **utilização** gráfico, pode ver o armazenamento primário utilizado entre o seu dispositivo e o armazenamento na cloud consumidas durante os últimos sete dias, a predefinição do período de tempo.

     ![Mosaico utilização](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Para escolher uma escala de tempo diferente, utilize o **editar** opção no canto superior direito do gráfico.

     ![Editar Gráfico de utilização](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Neste gráfico, pode ver as métricas para o armazenamento primário total (a quantidade de dados escritos por anfitriões para o seu dispositivo) e o armazenamento de nuvem total consumido pelo seu dispositivo durante um período de tempo.
  
     Neste contexto, *armazenamento primário* refere-se para a quantidade total de dados escritos pelo host e podem ser divididas por tipo de volume: *primário em camadas armazenamento* inclui ambos armazenadas localmente dados e dados em camadas a cloud. *Primário localmente afixado armazenamento* inclui apenas dados armazenados localmente. *Armazenamento na nuvem*, por outro lado, é uma medida da quantidade total de dados armazenados na cloud. Este armazenamento inclui dados em camadas e cópias de segurança. Os dados armazenados na cloud é a eliminação de duplicados e comprimidos, ao passo que o armazenamento primário indica a quantidade de armazenamento utilizado antes dos dados são com eliminação de duplicados e comprimidos. (Pode comparar estes dois números para ter uma idéia da taxa de compressão.) Para ambas as primárias e de armazenamento na cloud, os montantes mostrados baseiam-se a frequência de controlo que configurar. Por exemplo, se optar por uma freqüência de uma semana, em seguida, o gráfico mostra os dados para cada dia da semana anterior.

     Para ver a quantidade de armazenamento na cloud consumido ao longo do tempo, selecione o **utilizado de armazenamento na CLOUD** opção. Para ver o armazenamento total que foi escrito pelo anfitrião, selecione o **primário em CAMADAS armazenamento utilizado** e **primário LOCALMENTE AFIXADO armazenamento utilizado** opções. 
     Para obter mais informações, consulte [utilizar o serviço StorSimple Device Manager para monitorizar o dispositivo StorSimple](storsimple-monitor-device.md).


* O **capacidade** mosaico apresenta o armazenamento primário, que é aprovisionado e restantes entre o dispositivo em relação a memória total disponível para o mesmo. **Aprovisionado** refere-se para a quantidade de armazenamento que é preparado e alocado para uso, **restante** refere-se para a capacidade restante, que pode ser aprovisionada neste dispositivo. 

    ![Mosaico utilização](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Clique neste mosaico para ver a forma como a capacidade é aprovisionada entre volumes afixados localmente e em camadas. O **camadas restantes** capacidade é a capacidade disponível que pode ser aprovisionada incluindo cloud, enquanto o **restantes Local** é a capacidade restante nos discos ligados a este dispositivo.

    ![Clique em gráfico de utilização](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [o painel de resumo de serviço do StorSimple](storsimple-8000-service-dashboard.md).
* Saiba mais sobre [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

