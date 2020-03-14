---
title: Componentes e estatuto de hardware da série StorSimple 8000 Microsoft Docs
description: Aprenda a monitorizar os componentes de hardware do seu dispositivo StorSimple através do serviço StorSimple Device Manager.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254745"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Utilize o serviço StorSimple Device Manager para monitorizar componentes de hardware e estado

## <a name="overview"></a>Descrição geral
Este artigo descreve os vários componentes físicos e lógicos no seu dispositivo da série StorSimple 8000. Também explica como monitorizar o estado do componente do dispositivo utilizando a lâmina de **saúde de Estado e hardware** no serviço StorSimple Device Manager.

A lâmina de **saúde de Estado e hardware** mostra o estado do hardware de todos os componentes do dispositivo StorSimple.

Na lista de componentes para 8100, existem três secções que descrevem:

* **Componentes partilhados** – Estes não fazem parte dos controladores, tais como unidades de disco, componentes de comando, módulo de potência e arrefecimento (PCM) e temperatura PCM, tensão de linha e sensores de corrente de linha.
* **Controlador 0 Componentes** – Os componentes que residem no Controlador 0, tais como controlador, expansor sas e conector, sensores de temperatura do controlador e as várias interfaces de rede.
* **Controlador 1 Componentes** – Os componentes que constituem o Controlador 1, semelhantes aos detalhados para o Controlador 0.

Um dispositivo 8600 tem componentes adicionais que correspondem ao recinto extended bunch of Disks (EBOD). Na lista de componentes, há cinco secções. Destas, existem três secções que contêm os componentes no recinto primário e são idênticas às descritas para 8100. Existem duas secções adicionais para o recinto ebod que descrevem:

* **Controlador EBOD 0 Componentes** – Os componentes que residem no recinto eBOD 0, tais como o controlador EBOD, expansor e conector SAS, e sensores de temperatura do controlador.
* **Controlador EBOD 1 Componentes** – Os componentes que constituem o recinto EBOD 1, semelhante aos detalhados para o recinto EBOD 0.
* **EBOD recinto Componentes partilhados** – Os componentes presentes no recinto EBOD e PCM que não fazem parte do controlador EBOD.

> [!NOTE]
> **O estado do hardware não está disponível para um StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorize o estado do hardware
Execute os seguintes passos para visualizar o estado do hardware de um componente do dispositivo:

1. Navegue para **Dispositivos,** selecione um dispositivo StorSimple específico. Vá ao **Monitor > Saúde de Hardware.**

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Localize a secção de componentes de **Hardware** e escolha entre os componentes disponíveis. Basta clicar na etiqueta do componente para expandir a lista e ver o estado dos vários componentes do dispositivo. Consulte a lista de [componentes detalhados para o recinto primário](#component-list-for-primary-enclosure-of-storsimple-device) e a lista de componentes [detalhados para o recinto EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Utilize o seguinte esquema de codificação de cores para interpretar o estado do componente:
   
   * **Verificação verde** – Denota um componente saudável com estatuto **OK.**
   * **Amarelo** – Denota um componente degradado em estado de **alerta.**
   * **Exclamação vermelha** – Denota um componente falhado que tem um estado de **falha.**
   * **Branco com texto preto** – Denota um componente que não está presente.
   
   A imagem que se segue mostra um dispositivo que tem componentes no estado **OK**, **Aviso**e **Falha.**
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Expandindo a lista de **componentes Partilhados,** podemos ver que o NVRAM e o cluster estão degradados.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Expandindo a lista de componentes do **Controlador 1,** podemos ver que o nó de cluster falhou.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Se encontrar um componente que não esteja em estado **saudável,** contacte o Microsoft Support. Se os alertas estiverem ativados no seu dispositivo, receberá um alerta de e-mail. Se precisar de substituir um componente de hardware falhado, consulte a substituição do componente de [hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista de componentes para o recinto primário do dispositivo StorSimple
A tabela seguinte descreve os componentes físicos e lógicos contidos no recinto primário (presente tanto em 8100 como 8600) do seu dispositivo StorSimple no local.

| Componente | Módulo | Tipo | Localização | Unidade substituível de campo (FRU)? | Descrição |
| --- | --- | --- | --- | --- | --- |
| Unidade na ranhura [0-11] |Unidades de Disco |Físico |Partilhada |Sim |Uma linha é apresentada para cada uma das unidades SSD ou HDD no recinto primário. |
| Sensor de temperatura ambiente |Recinto |Físico |Partilhada |Não |Mede a temperatura dentro do chassis. |
| Sensor de temperatura a meio do avião |Recinto |Físico |Partilhada |Não |Mede a temperatura do meio do avião. |
| Alarme sonoro |Recinto |Físico |Partilhada |Não |Indica se o subsistema de alarme sonoro dentro do chassis está funcional. |
| Recinto |Recinto |Físico |Partilhada |Sim |Indica a presença de um chassis. |
| Definições de recinto |Recinto |Físico |Partilhada |Não |Refere-se ao painel frontal do chassis. |
| Sensores de tensão de linha |PCM |Físico |Partilhada |Não |Numerosos sensores de tensão de linha têm o seu estado apresentado, o que indica se a tensão medida está dentro da tolerância. |
| Sensores de corrente de linha |PCM |Físico |Partilhada |Não |Numerosos sensores de corrente de linha têm o seu estado apresentado, o que indica se a corrente medida está dentro da tolerância. |
| Sensores de temperatura em PCM |PCM |Físico |Partilhada |Não |Numerosos sensores de temperatura, como os sensores de intinhista e hotspot, têm o seu estado apresentado, indicando se a temperatura medida está dentro da tolerância. |
| Alimentação [0-1] |PCM |Físico |Partilhada |Sim |É apresentada uma linha para cada uma das fontes de alimentação dos dois PCMs localizados na parte de trás do dispositivo. |
| Arrefecimento [0-1] |PCM |Físico |Partilhada |Sim |Uma linha é apresentada para cada um dos quatro ventiladores de arrefecimento que residem nos dois PCMs. |
| Bateria [0-1] |PCM |Físico |Partilhada |Sim |É apresentada uma linha para cada um dos módulos de bateria de reserva que estão sentados no PCM. |
| Metis |N/D |Lógica |Partilhada |N/D |Exibe o estado das pilhas: se precisam de ser carregadas e estão a aproximar-se do fim de vida. |
| Cluster |N/D |Lógica |Partilhada |N/D |Exibe o estado do cluster que é criado entre os dois módulos de comando integrados. |
| Nó de cluster |N/D |Lógica |Partilhada |N/D |Indica o estado do controlador como parte do cluster. |
| Quórum cluster |N/D |Lógica | |N/D |Indica a presença da maioria da filiação em disco no depósito de HDD. |
| Espaço de dados HDD |N/D |Lógica |Partilhada |N/D |O espaço de armazenamento que é utilizado para dados no depósito de disco rígido (HDD). |
| Espaço de gestão HDD |N/D |Lógica |Partilhada |N/D |O espaço reservado no depósito hdd para tarefas de gestão. |
| Espaço quórum HDD |N/D |Lógica |Partilhada |N/D |O espaço reservado na piscina de armazenamento HDD para o cluster quórum. |
| Espaço de substituição hdd |N/D |Lógica |Partilhada |N/D |O espaço reservado no depósito hdd para substituição do controlador. |
| Espaço de dados SSD |N/D |Lógica |Partilhada |N/D |O espaço de armazenamento utilizado para dados no depósito de unidade de estado sólido (SSD). |
| Espaço SSD NVRAM |N/D |Lógica |Partilhada |N/D |O espaço de armazenamento no armazém SSD que é dedicado à lógica NVRAM. |
| Piscina de armazenamento HDD |N/D |Lógica |Partilhada |N/D |Exibe o estado do armazenamento lógico que é criado a partir de HDDs do dispositivo. |
| Piscina de armazenamento SSD |N/D |Lógica |Partilhada |N/D |Exibe o estado do armazenamento lógico que é criado a partir de SSDs do dispositivo. |
| Controlador [0-1] [estado] |I/O |Físico |Controlador |Sim |Exibe o estado do controlador e se está ativo ou em modo de espera dentro do chassis. |
| Sensores de temperatura no controlador |I/O |Físico |Controlador |Não |Numerosos sensores de temperatura, tais como módulo si/O, temperatura CPU, dimm e sensores DEPC têm o seu estado apresentado, o que indica se a temperatura encontrada está ou não dentro da tolerância. |
| Expansor SAS |I/O |Físico |Controlador |Não |Indica o estado do expansor SCSI (SAS) em série, que é utilizado para ligar o armazenamento integrado ao controlador. |
| Conector SAS [0-1] |I/O |Físico |Controlador |Não |Indica o estado de cada conector SAS, que é utilizado para ligar o armazenamento integrado ao expansor SAS. |
| Interligação de meio-plano SBB |I/O |Físico |Controlador |Não |Indica o estado do conector de meio do avião, que é usado para ligar cada controlador ao meio do avião. |
| Núcleo de processador |I/O |Físico |Controlador |Não |Indica o estado dos núcleos do processador dentro de cada controlador. |
| Poder eletrónico de recinto |I/O |Físico |Controlador |Não |Indica o estado do sistema de energia utilizado pelo recinto. |
| Diagnósticos eletrónicos de recinto |I/O |Físico |Controlador |Não |Indica o estado dos subsistemas de diagnóstico fornecidos pelo controlador. |
| Controlador de Gestão de Quadros de Base (BMC) |I/O |Físico |Controlador |Não |Indica o estado do controlador de gestão de quadros base (BMC), que é um processador de serviço especializado que monitoriza o dispositivo de hardware através de sensores e comunica com o administrador do sistema através de uma ligação independente. |
| Ethernet |I/O |Físico |Controlador |Não |Indica o estado de cada uma das interfaces de rede, ou seja, as portas de gestão e dados fornecidas no controlador. |
| NVRAM |I/O |Físico |Controlador |Não |Indica o estado da NVRAM, uma memória de acesso aleatório não volátil apoiada pela bateria que serve para reter informações críticas de aplicação em caso de falha de energia. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista de componentes para o recinto EBOD do dispositivo StorSimple
A tabela seguinte descreve os componentes físicos e lógicos contidos no recinto EBOD (apenas presente sem modelo no 8600) do seu dispositivo StorSimple no local.

| Componente | Módulo | Tipo | Localização | Fru? | Descrição |
| --- | --- | --- | --- | --- | --- |
| Unidade na ranhura [0-11] |Unidades de Disco |Físico |Partilhada |Sim |Uma linha é apresentada para cada uma das unidades HDD na frente do recinto EBOD. |
| Sensor de temperatura ambiente |Recinto |Físico |Partilhada |Não |Mede a temperatura dentro do chassis. |
| Sensor de temperatura a meio do avião |Recinto |Físico |Partilhada |Não |Mede a temperatura do meio do avião. |
| Alarme sonoro |Recinto |Físico |Partilhada |Não |Indica se o subsistema de alarme sonoro dentro do chassis está funcional. |
| Recinto |Recinto |Físico |Partilhada |Sim |Indica a presença de um chassis. |
| Definições de recinto |Recinto |Físico |Partilhada |Não |Refere-se ao OPS ou ao painel frontal do chassis. |
| Sensores de tensão de linha |PCM |Físico |Partilhada |Não |Numerosos sensores de tensão de linha têm o seu estado apresentado, o que indica se a tensão medida está dentro da tolerância. |
| Sensores de corrente de linha |PCM |Físico |Partilhada |Não |Numerosos sensores de corrente de linha têm o seu estado apresentado, o que indica se a corrente medida está dentro da tolerância. |
| Sensores de temperatura em PCM |PCM |Físico |Partilhada |Não |Numerosos sensores de temperatura, como os sensores de entrada e hotspot, têm o seu estado apresentado, o que indica se a temperatura medida está dentro da tolerância. |
| Alimentação [0-1] |PCM |Físico |Partilhada |Sim |É apresentada uma linha para cada uma das fontes de alimentação dos dois PCMs localizados na parte de trás do dispositivo. |
| Arrefecimento [0-1] |PCM |Físico |Partilhada |Sim |Uma linha é apresentada para cada um dos quatro ventiladores de arrefecimento que residem nos dois PCMs. |
| Armazenamento local [HDD] |N/D |Lógica |Partilhada |N/D |Exibe o estado do armazenamento lógico que é criado a partir de HDDs do dispositivo. |
| Controlador [0-1] [estado] |I/O |Físico |Controlador |Sim |Apresenta o estado dos controladores no módulo EBOD. |
| Sensores de temperatura em EBOD |I/O |Físico |Controlador |Não |Numerosos sensores de temperatura de cada controlador têm o seu estado apresentado, o que indica se a temperatura encontrada está dentro da tolerância. |
| Expansor SAS |I/O |Físico |Controlador |Não |Indica o estado do expansor SAS, que é usado para ligar o armazenamento integrado ao controlador. |
| Conector SAS [0-2] |I/O |Físico |Controlador |Não |Indica o estado de cada conector SAS, que é utilizado para ligar o armazenamento integrado ao expansor SAS. |
| Interligação de meio-plano SBB |I/O |Físico |Controlador |Não |Indica o estado do conector de meio do avião, que é usado para ligar cada controlador ao meio do avião. |
| Poder eletrónico de recinto |I/O |Físico |Controlador |Não |Indica o estado do sistema de energia utilizado pelo recinto. |
| Diagnósticos eletrónicos de recinto |I/O |Físico |Controlador |Não |Indica o estado dos subsistemas de diagnóstico fornecidos pelo controlador. |
| Ligação ao controlador de dispositivos |I/O |Físico |Controlador |Não |Indica o estado da ligação entre o módulo EBOD I/O e o controlador do dispositivo. |

## <a name="next-steps"></a>Passos seguintes
* Para utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo, vá [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
* Se precisar de resolver um componente do dispositivo que tenha um estado degradado ou falhado, consulte os indicadores de [monitorização StorSimple](storsimple-monitoring-indicators.md).
* Para substituir um componente de hardware falhado, consulte a substituição do componente de [hardware StorSimple](storsimple-hardware-component-replacement.md).
* Se continuar a experimentar problemas com dispositivos, [contacte](storsimple-8000-contact-microsoft-support.md)o Microsoft Support .

