---
title: Componentes de hardware de série 8000 do StorSimple e o Estado | Documentos da Microsoft
description: Saiba como monitorizar os componentes de hardware do dispositivo StorSimple através do serviço StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: a987239669e7437a179f5f24034f4dbe45535663
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632867"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Utilizar o serviço StorSimple Device Manager para monitorizar componentes de hardware e o Estado

## <a name="overview"></a>Descrição geral
Este artigo descreve os vários componentes físicos e lógicos no seu dispositivo da série StorSimple 8000 no local. Também explica como monitorizar o estado do componente de dispositivo com o **estado de funcionamento do Estado e o hardware** painel no serviço StorSimple Device Manager.

O **estado de funcionamento do Estado e o hardware** painel mostra o estado de hardware de todos os componentes do dispositivo StorSimple.

Na lista de componentes para 8100, existem três secções que descrevem:

* **Componentes partilhados** – eles não fazem parte de controladores, como unidades de disco, bastidor, componentes de energia e refrigeração módulo PCM () e temperatura PCM, tensão de linha e sensores atuais de linha.
* **Componentes de controlador 0** – os componentes que residem no controlador 0, como o controlador, Expansor de SAS e conector, sensores de temperatura de controlador e as várias interfaces de rede.
* **Componentes de controlador 1** – os componentes que constituem o controlador 1, semelhantes às detalhadas para o controlador 0.

Um dispositivo 8600 tem componentes adicionais que correspondem ao bastidor estendido monte de discos (EBOD). Na lista de componentes, existem cinco seções. Um deles, existem três secções que contêm os componentes no bastidor principal e são idênticos dos descrito para 8100. Existem duas secções adicionais para a inclusão EBOD que descrevem:

* **Componentes de controlador de EBOD 0** – os componentes que residam no bastidor EBOD 0, como o controlador EBOD, sensores de temperatura de controlador e conector e Expansor SAS.
* **1 de controlador de Ebod** – os componentes que constituem o bastidor EBOD 1, semelhante a esses detalhadas para inclusão EBOD 0.
* **Componentes partilhados de EBOD do bastidor** – os componentes de apresentam o bastidor EBOD e PCM que não fazem parte do controlador de EBOD.

> [!NOTE]
> **O estado de hardware não está disponível para uma StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorizar o estado de hardware
Execute os seguintes passos para ver o estado de hardware de um componente de dispositivo:

1. Navegue para **dispositivos**, selecione um dispositivo específico do StorSimple. Aceda a **Monitor > Estado de funcionamento do Hardware**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Localize a **componentes de Hardware** secção e escolha de entre os componentes disponíveis. Basta clicar a etiqueta de componente para expandir a lista e ver o estado dos vários componentes do dispositivo. Consulte a [lista de componentes detalhadas para o bastidor primário](#component-list-for-primary-enclosure-of-storsimple-device) e o [lista de componentes detalhadas para a inclusão EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Utilize o seguinte esquema de codificação de cores para interpretar o estado do componente:
   
   * **Verificação de verde** – indica um componente de bom estado de funcionamento com **OK** estado.
   * **Amarelo** – indica um componente degradado **aviso** estado.
   * **Exclamação vermelha** – Denotes um componente que falhou com um **falha** estado.
   * **Branco com texto preto** – denota um componente que não está presente.
   
   Captura de ecrã seguinte mostra um dispositivo que tem componentes no **OK**, **aviso**, e **falha** estado.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Expandir a **lista de componentes de partilhado**, podemos ver que a NVRAM e o cluster estão degradados.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Expandir a **componentes de controlador 1** lista, podemos ver que o nó de cluster falhou.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Se tiver um componente que não se encontra numa **bom estado de funcionamento** de estado, contacte o Support da Microsoft. Se os alertas estão ativados no seu dispositivo, receberá um alerta por e-mail. Se precisar de substituir um componente de falha de hardware, consulte [substituição de componente de hardware do StorSimple](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista de componentes para inclusão principal do dispositivo StorSimple
A tabela seguinte descreve os componentes físicos e lógicos contidos no bastidor principal (presente no 8100 e 8600) do seu dispositivo do StorSimple no local.

| Componente | Módulo | Type | Location | Unidade substituível em campo (FRU)? | Descrição |
| --- | --- | --- | --- | --- | --- |
| Unidade na ranhura [0-11] |Unidades de disco |Físico |Partilhado |Sim |Uma linha é apresentada para cada um do SSD ou as unidades HDD no bastidor principal. |
| Sensor de temperatura ambiente |Bastidor |Físico |Partilhado |Não |Mede a temperatura dentro do chassi. |
| Sensor de temperatura do plano médio |Bastidor |Físico |Partilhado |Não |Mede a temperatura do avião médio. |
| Alarme sonoro |Bastidor |Físico |Partilhado |Não |Indica se o subsistema de alarme sonoro dentro do chassi é funcional. |
| Bastidor |Bastidor |Físico |Partilhado |Sim |Indica a presença de um chassis. |
| Definições do bastidor |Bastidor |Físico |Partilhado |Não |Refere-se para o painel frontal da chassi. |
| Sensores de tensão de linha |PCM |Físico |Partilhado |Não |O estado apresentado, que indica se a tensão medida é respeitam a tolerância de ter vários sensores de tensão de linha. |
| Sensores atuais da linha |PCM |Físico |Partilhado |Não |O estado apresentado, que indica se a medida atual é respeitam a tolerância de ter vários sensores atual de linha. |
| Sensores de temperatura no PCM |PCM |Físico |Partilhado |Não |Vários sensores de temperatura como à entrada e os sensores de ponto de acesso tem o estado apresentado, que indica se a temperatura de medida é respeitam a tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Partilhado |Sim |Uma linha é apresentada para cada um das fontes de alimentação nos dois PCMs localizados na parte traseira o dispositivo. |
| Refrigeração [0-1] |PCM |Físico |Partilhado |Sim |Uma linha é apresentada para cada um as quatro ventoinhas de arrefecimento que residem nos dois PCMs. |
| Bateria [0-1] |PCM |Físico |Partilhado |Sim |Uma linha é apresentada para cada um dos módulos de bateria de segurança que são sentados no PCM. |
| Metis |N/A |Lógica |Partilhado |N/A |Apresenta o estado das baterias: tem de cobrança e são a atingir o final da vida. |
| Cluster |N/A |Lógica |Partilhado |N/A |Apresenta o estado do cluster que é criado entre os dois módulos de controlador integrada. |
| Nó de cluster |N/A |Lógica |Partilhado |N/A |Indica o estado do controlador como parte do cluster. |
| Quórum do cluster |N/A |Lógica | |N/A |Indica a presença da associação de disco à maioria no agrupamento de armazenamento HDD. |
| Espaço de dados HDD |N/A |Lógica |Partilhado |N/A |O espaço de armazenamento que é utilizado para dados no agrupamento de armazenamento de unidade de disco rígido (HDD). |
| Espaço de armazenamento HDD |N/A |Lógica |Partilhado |N/A |O espaço reservado no agrupamento de armazenamento HDD para tarefas de gestão. |
| Espaço de quórum HDD |N/A |Lógica |Partilhado |N/A |O espaço reservado no agrupamento de armazenamento HDD para o quórum do cluster. |
| Espaço de substituição HDD |N/A |Lógica |Partilhado |N/A |O espaço reservado no agrupamento de armazenamento HDD para substituição de controlador. |
| Espaço de dados SSD |N/A |Lógica |Partilhado |N/A |O espaço de armazenamento utilizado para os dados no agrupamento de armazenamento de estado sólido (SSD) de unidade. |
| Espaço SSD NVRAM |N/A |Lógica |Partilhado |N/A |O espaço de armazenamento no agrupamento de armazenamento SSD dedicado para a lógica NVRAM. |
| Agrupamento de armazenamento HDD |N/A |Lógica |Partilhado |N/A |Apresenta o estado do agrupamento de armazenamento lógico, que é criado a partir do dispositivo HDDs. |
| Agrupamento de armazenamento SSD |N/A |Lógica |Partilhado |N/A |Apresenta o estado do agrupamento de armazenamento lógico, que é criado a partir de dispositivos SSDs. |
| Controlador [0-1], [Estado] |I/O |Físico |Controlador |Sim |Apresenta o estado do controlador, e se está no modo de Active Directory ou em modo de espera dentro do chassi. |
| Sensores de temperatura no controlador |I/O |Físico |Controlador |Não |Vários sensores de temperatura, como o módulo de e/s, temperatura de CPU, sensores DIMM e PCIe tem o estado apresentado, que indica se é ou não a temperatura encontrou respeitam a tolerância. |
| Expansor de SAS |I/O |Físico |Controlador |Não |Indica o estado da série anexado SCSI (SAS) Expansor, que é utilizada para ligar o armazenamento integrado para o controlador. |
| Conector de SAS [0-1] |I/O |Físico |Controlador |Não |Indica o estado de cada conector SAS, o que é utilizada para ligar o armazenamento integrada para o Expansor de SAS. |
| Interconexão de plano médio SBB |I/O |Físico |Controlador |Não |Indica o estado do conector do plano de médio e o que é utilizado para ligar cada controlador para o plano de médio. |
| Núcleo de processador |I/O |Físico |Controlador |Não |Indica o estado dos núcleos de processador dentro de cada controlador. |
| Bastidor eletrónico-energia |I/O |Físico |Controlador |Não |Indica o estado do sistema de energia usado pelo bastidor. |
| Bastidor eletrónico-diagnóstico |I/O |Físico |Controlador |Não |Indica o estado dos subsistemas de diagnóstico fornecida pelo controlador. |
| Controlador de gestão da placa base (BMC) |I/O |Físico |Controlador |Não |Indica o estado do controlador de gestão da placa base (BMC), que é um processador de serviços especializado que monitora o dispositivo de hardware através de sensores e comunica com o administrador de sistema através de uma ligação independente. |
| Ethernet |I/O |Físico |Controlador |Não |Indica o estado de cada uma das interfaces de rede, ou seja, o gerenciamento e as portas de dados fornecidas no controlador. |
| NVRAM |I/O |Físico |Controlador |Não |Indica o estado da NVRAM, uma memória de acesso de aleatório não volátil uma cópia de segurança a bateria que serve para manter informações críticas para a aplicação em caso de falha de energia. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista de componentes para inclusão EBOD do dispositivo StorSimple
A tabela seguinte descreve os componentes físicos e lógicos contidos no bastidor EBOD (apenas presente no modelo 8600) do seu dispositivo do StorSimple no local.

| Componente | Módulo | Type | Location | FRU? | Descrição |
| --- | --- | --- | --- | --- | --- |
| Unidade na ranhura [0-11] |Unidades de disco |Físico |Partilhado |Sim |Uma linha é apresentada para as unidades HDD no início a inclusão EBOD. |
| Sensor de temperatura ambiente |Bastidor |Físico |Partilhado |Não |Mede a temperatura dentro do chassi. |
| Sensor de temperatura do plano médio |Bastidor |Físico |Partilhado |Não |Mede a temperatura do avião médio. |
| Alarme sonoro |Bastidor |Físico |Partilhado |Não |Indica se o subsistema de alarme sonoro dentro do chassi é funcional. |
| Bastidor |Bastidor |Físico |Partilhado |Sim |Indica a presença de um chassis. |
| Definições do bastidor |Bastidor |Físico |Partilhado |Não |Refere-se para os OPS ou o painel frontal da chassi. |
| Sensores de tensão de linha |PCM |Físico |Partilhado |Não |O estado apresentado, que indica se a tensão medida é respeitam a tolerância de ter vários sensores de tensão de linha. |
| Sensores atuais da linha |PCM |Físico |Partilhado |Não |O estado apresentado, que indica se a medida atual é respeitam a tolerância de ter vários sensores atual de linha. |
| Sensores de temperatura no PCM |PCM |Físico |Partilhado |Não |Vários sensores de temperatura como à entrada e os sensores de ponto de acesso tem o estado apresentado, que indica se a temperatura de medida é respeitam a tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Partilhado |Sim |Uma linha é apresentada para cada um das fontes de alimentação nos dois PCMs localizados na parte traseira o dispositivo. |
| Refrigeração [0-1] |PCM |Físico |Partilhado |Sim |Uma linha é apresentada para cada um as quatro ventoinhas de arrefecimento que residem nos dois PCMs. |
| Armazenamento local [HDD] |N/A |Lógica |Partilhado |N/A |Apresenta o estado do agrupamento de armazenamento lógico, que é criado a partir do dispositivo HDDs. |
| Controlador [0-1], [Estado] |I/O |Físico |Controlador |Sim |Apresenta o estado dos controladores no módulo EBOD. |
| Sensores de temperatura de EBOD |I/O |Físico |Controlador |Não |Vários sensores de temperatura de cada controlador de tem o estado apresentado, que indica se a temperatura encontrou é respeitam a tolerância. |
| Expansor de SAS |I/O |Físico |Controlador |Não |Indica o estado do Expansor SAS, que é utilizada para ligar o armazenamento integrado para o controlador. |
| Conector de SAS [0-2] |I/O |Físico |Controlador |Não |Indica o estado de cada conector SAS, o que é utilizada para ligar o armazenamento integrada para o Expansor de SAS. |
| Interconexão de plano médio SBB |I/O |Físico |Controlador |Não |Indica o estado do conector do plano de médio e o que é utilizado para ligar cada controlador para o plano de médio. |
| Bastidor eletrónico-energia |I/O |Físico |Controlador |Não |Indica o estado do sistema de energia usado pelo bastidor. |
| Bastidor eletrónico-diagnóstico |I/O |Físico |Controlador |Não |Indica o estado dos subsistemas de diagnóstico fornecida pelo controlador. |
| Ligação ao controlador de dispositivo |I/O |Físico |Controlador |Não |Indica o estado da ligação entre o módulo de e/s de EBOD e o controlador de dispositivo. |

## <a name="next-steps"></a>Passos Seguintes
* Para utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo, aceda à [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
* Se precisar de resolver um componente de dispositivo que tem um Estado degradado ou falhado, consulte [StorSimple indicadores de monitorização](storsimple-monitoring-indicators.md).
* Para substituir um componente de falha de hardware, consulte [substituição de componente de hardware do StorSimple](storsimple-hardware-component-replacement.md).
* Se continuar a ter problemas de dispositivos [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).

