---
title: StorSimple 8000 componentes de hardware da série e | Microsoft Docs
description: Saiba como monitorizar os componentes de hardware do seu dispositivo StorSimple através do serviço StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960364"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>Utilize o serviço StorSimple Device Manager para monitorizar os componentes e o estado do hardware

## <a name="overview"></a>Descrição geral
Este artigo descreve os vários componentes físicos e lógicos no seu dispositivo da série StorSimple 8000. Também explica como monitorizar o estado do componente do dispositivo utilizando a lâmina de **saúde status e hardware** no serviço StorSimple Device Manager.

A lâmina **de saúde status e hardware** mostra o estado de hardware de todos os componentes do dispositivo StorSimple.

Na lista de componentes para 8100, existem três secções que descrevem:

* **Componentes Partilhados** – Estes não fazem parte dos controladores, tais como discos, componentes do módulo de alimentação e arrefecimento (PCM) e temperatura de PCM, tensão de linha e sensores de corrente de linha.
* **Controlador 0 Componentes** – Os componentes que residem no Controlador 0, tais como controlador, expansor SAS e conector, sensores de temperatura do controlador e as várias interfaces de rede.
* **Controlador 1 Componentes** – Os componentes que constituem o Controlador 1, semelhantes aos detalhados para o Controlador 0.

Um dispositivo 8600 tem componentes adicionais que correspondem ao invólucro do Bunch Extended of Disks (EBOD). Na lista de componentes, há cinco secções. Destas, existem três secções que contêm os componentes no recinto primário e são idênticas às descritas para 8100. Existem duas secções adicionais para o recinto da EBOD que descrevem:

* **Componentes do Controlador EBOD 0** – Os componentes que residem no compartimento EBOD 0, tais como o controlador EBOD, o expansor e o conector SAS e os sensores de temperatura do controlador.
* **Controlador EBOD 1 Componentes** – Os componentes que constituem o recinto EBOD 1, semelhantes aos detalhados para o recinto EBOD 0.
* **Componentes partilhados do recinto EBOD** – Os componentes presentes no recinto EBOD e PCM que não fazem parte do controlador EBOD.

> [!NOTE]
> **O estado do hardware não está disponível para um Aparelho StorSimple Cloud (8010/8020).**


## <a name="monitor-the-hardware-status"></a>Monitorize o estado do hardware
Execute os seguintes passos para visualizar o estado de hardware de um componente do dispositivo:

1. Navegue para **dispositivos,** selecione um dispositivo StorSimple específico. Vá ao **Monitor > saúde do hardware**.

    ![Screenshot mostrando a lâmina de Definições do serviço Gestor de Dispositivos. No âmbito do Monitor, a saúde do hardware é selecionada.](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Localize a secção **de componentes de Hardware** e escolha entre os componentes disponíveis. Basta clicar na etiqueta de componentes para expandir a lista e ver o estado dos vários componentes do dispositivo. Consulte a [lista de componentes detalhados para o recinto primário](#component-list-for-primary-enclosure-of-storsimple-device) e a lista de componentes [detalhados para o recinto EBOD](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![Screenshot mostrando um componente selecionado na secção de componentes de hardware. Uma lâmina adjacente mostra o nome e o estado das peças desse componente.](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Utilize o seguinte esquema de codificação de cores para interpretar o estado do componente:
   
   * **Verificação verde** – Denota um componente saudável com estatuto **OK.**
   * **Amarelo** – Denota um componente degradado em Estado **de aviso.**
   * **Exclamação vermelha** – Denota um componente falhado que tem um estado **de Falha.**
   * **Branco com texto preto** – Denota um componente que não está presente.
   
   A imagem que se segue mostra um dispositivo que tem componentes no estado **OK,** **Warning** e **Fail.**
       
   ![Screenshot mostrando três componentes na secção de componentes de hardware: um em estado de boa saúde, um em estado de falha e outro em estado de aviso.](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Expandindo a lista de **componentes partilhados, podemos** ver que o NVRAM e o cluster estão degradados.

   ![Screenshot mostrando o item de Componentes Partilhados selecionado. Na lâmina adjacente, o N V R A M e o cluster estão em estado de alerta.](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Expandindo a lista de **componentes do Controlador 1, podemos** ver que o nó de cluster falhou.  

   ![Screenshot mostrando o item de controlador 1 componentes selecionados. Na lâmina adjacente, o nó do cluster está em estado de falha.](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Se encontrar um componente que não esteja em estado **saudável,** contacte o Microsoft Support. Se os alertas estiverem ativados no seu dispositivo, receberá um alerta de e-mail. Se precisar de substituir um componente de hardware falhado, consulte a [substituição do componente de hardware StorSimple](./storsimple-8000-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Lista de componentes para o recinto primário do dispositivo StorSimple
A tabela que se segue descreve os componentes físicos e lógicos contidos no recinto primário (presentes em 8100 e 8600) do seu dispositivo StorSimple no local.

| Componente | Módulo | Tipo | Localização | Unidade substituível de campo (FRU)? | Description |
| --- | --- | --- | --- | --- | --- |
| Unidade na ranhura [0-11] |Unidades de disco |Físico |Partilhado |Yes |Uma linha é apresentada para cada uma das unidades SSD ou HDD no recinto primário. |
| Sensor de temperatura ambiente |Recinto |Físico |Partilhado |No |Mede a temperatura dentro do chassis. |
| Sensor de temperatura do meio do plano |Recinto |Físico |Partilhado |No |Mede a temperatura do meio do avião. |
| Alarme audível |Recinto |Físico |Partilhado |No |Indica se o subsistema de alarme sonoro dentro do chassis está funcional. |
| Recinto |Recinto |Físico |Partilhado |Yes |Indica a presença de um chassis. |
| Definições de recinto |Recinto |Físico |Partilhado |No |Refere-se ao painel frontal do chassis. |
| Sensores de tensão de linha |PCM |Físico |Partilhado |No |Vários sensores de tensão de linha têm o seu estado apresentado, o que indica se a tensão medida está dentro da tolerância. |
| Sensores de corrente de linha |PCM |Físico |Partilhado |No |Numerosos sensores de corrente de linha têm o seu estado apresentado, o que indica se a corrente medida está dentro da tolerância. |
| Sensores de temperatura no PCM |PCM |Físico |Partilhado |No |Numerosos sensores de temperatura, como os sensores de entrada e hotspot, têm o seu estado apresentado, indicando se a temperatura medida está dentro da tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Partilhado |Yes |É apresentada uma linha para cada uma das fontes de alimentação dos dois PCMs localizados na parte de trás do dispositivo. |
| Arrefecimento [0-1] |PCM |Físico |Partilhado |Yes |É apresentada uma linha para cada um dos quatro ventiladores de arrefecimento que residem nos dois PCMs. |
| Bateria [0-1] |PCM |Físico |Partilhado |Yes |É apresentada uma linha para cada um dos módulos de bateria de reserva que estão sentados no PCM. |
| Metis |N/D |Lógico |Partilhado |N/D |Exibe o estado das pilhas: se precisam de ser carregadas e estão a aproximar-se do fim da vida. |
| Cluster |N/D |Lógico |Partilhado |N/D |Exibe o estado do cluster que é criado entre os dois módulos controladores integrados. |
| Nó de cluster |N/D |Lógico |Partilhado |N/D |Indica o estado do controlador como parte do aglomerado. |
| Quórum de cluster |N/D |Lógico | |N/D |Indica a presença da maioria dos membros do disco no conjunto de armazenamento HDD. |
| Espaço de dados HDD |N/D |Lógico |Partilhado |N/D |O espaço de armazenamento que é utilizado para dados no conjunto de armazenamento de disco rígido (HDD). |
| Espaço de gestão HDD |N/D |Lógico |Partilhado |N/D |O espaço reservado na piscina de armazenamento HDD para tarefas de gestão. |
| Espaço de quórum HDD |N/D |Lógico |Partilhado |N/D |O espaço reservado na piscina de armazenamento HDD para o quórum do cluster. |
| Espaço de substituição HDD |N/D |Lógico |Partilhado |N/D |O espaço reservado na piscina de armazenamento HDD para substituição do controlador. |
| Espaço de dados SSD |N/D |Lógico |Partilhado |N/D |O espaço de armazenamento utilizado para os dados no conjunto de armazenamento de unidade de estado sólido (SSD). |
| Espaço SSD NVRAM |N/D |Lógico |Partilhado |N/D |O espaço de armazenamento na piscina de armazenamento SSD que é dedicado à lógica NVRAM. |
| Piscina de armazenamento HDD |N/D |Lógico |Partilhado |N/D |Exibe o estado da piscina de armazenamento lógica que é criada a partir de HDDs do dispositivo. |
| Piscina de armazenamento SSD |N/D |Lógico |Partilhado |N/D |Exibe o estado da piscina de armazenamento lógica que é criada a partir de SSDs do dispositivo. |
| Controlador [0-1] [estado] |I/O |Físico |Controlador |Yes |Exibe o estado do controlador e se está em modo ativo ou em modo de espera dentro do chassis. |
| Sensores de temperatura no controlador |I/O |Físico |Controlador |No |Numerosos sensores de temperatura, tais como módulo de E/S, temperatura CPU, sensores DIMM e PCIE têm o seu estado apresentado, o que indica se a temperatura encontrada está ou não dentro da tolerância. |
| Expansor SAS |I/O |Físico |Controlador |No |Indica o estado do expansor SCSI (SAS) em série, que é utilizado para ligar o armazenamento integrado ao controlador. |
| Conector SAS [0-1] |I/O |Físico |Controlador |No |Indica o estado de cada conector SAS, que é utilizado para ligar o armazenamento integrado ao expansor SAS. |
| Interligação do plano médio SBB |I/O |Físico |Controlador |No |Indica o estado do conector do plano médio, que é utilizado para ligar cada controlador ao plano médio. |
| Núcleo do processador |I/O |Físico |Controlador |No |Indica o estado dos núcleos do processador dentro de cada controlador. |
| Poder eletrónico do recinto |I/O |Físico |Controlador |No |Indica o estado do sistema de alimentação utilizado pelo recinto. |
| Diagnósticos eletrónicos do recinto |I/O |Físico |Controlador |No |Indica o estado dos subsistemas de diagnóstico fornecidos pelo controlador. |
| Controlador BMC |I/O |Físico |Controlador |No |Indica o estado do controlador de gestão do rodapé (BMC), que é um processador de serviço especializado que monitoriza o dispositivo de hardware através de sensores e comunica com o administrador do sistema através de uma ligação independente. |
| Ethernet |I/O |Físico |Controlador |No |Indica o estado de cada uma das interfaces de rede, ou seja, as portas de gestão e dados fornecidas no controlador. |
| NVRAM |I/O |Físico |Controlador |No |Indica o estado da NVRAM, uma memória de acesso aleatório não volátil apoiada pela bateria que serve para reter informações críticas de aplicação em caso de falha de energia. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Lista de componentes para o recinto EBOD do dispositivo StorSimple
O quadro que se segue descreve os componentes físicos e lógicos contidos no recinto EBOD (apenas presente no modelo 8600) do seu dispositivo StorSimple no local.

| Componente | Módulo | Tipo | Localização | O FRU? | Description |
| --- | --- | --- | --- | --- | --- |
| Unidade na ranhura [0-11] |Unidades de disco |Físico |Partilhado |Yes |Uma linha é apresentada para cada uma das unidades HDD na parte da frente do recinto EBOD. |
| Sensor de temperatura ambiente |Recinto |Físico |Partilhado |No |Mede a temperatura dentro do chassis. |
| Sensor de temperatura do meio do plano |Recinto |Físico |Partilhado |No |Mede a temperatura do meio do avião. |
| Alarme audível |Recinto |Físico |Partilhado |No |Indica se o subsistema de alarme sonoro dentro do chassis está funcional. |
| Recinto |Recinto |Físico |Partilhado |Yes |Indica a presença de um chassis. |
| Definições de recinto |Recinto |Físico |Partilhado |No |Consulte o OPS ou o painel frontal do chassis. |
| Sensores de tensão de linha |PCM |Físico |Partilhado |No |Vários sensores de tensão de linha têm o seu estado apresentado, o que indica se a tensão medida está dentro da tolerância. |
| Sensores de corrente de linha |PCM |Físico |Partilhado |No |Numerosos sensores de corrente de linha têm o seu estado apresentado, o que indica se a corrente medida está dentro da tolerância. |
| Sensores de temperatura no PCM |PCM |Físico |Partilhado |No |Numerosos sensores de temperatura, como os sensores de entrada e hotspot, têm o seu estado apresentado, o que indica se a temperatura medida está dentro da tolerância. |
| Fonte de alimentação [0-1] |PCM |Físico |Partilhado |Yes |É apresentada uma linha para cada uma das fontes de alimentação dos dois PCMs localizados na parte de trás do dispositivo. |
| Arrefecimento [0-1] |PCM |Físico |Partilhado |Yes |É apresentada uma linha para cada um dos quatro ventiladores de arrefecimento que residem nos dois PCMs. |
| Armazenamento local [HDD] |N/D |Lógico |Partilhado |N/D |Exibe o estado da piscina de armazenamento lógica que é criada a partir de HDDs do dispositivo. |
| Controlador [0-1] [estado] |I/O |Físico |Controlador |Yes |Exibe o estado dos controladores no módulo EBOD. |
| Sensores de temperatura em EBOD |I/O |Físico |Controlador |No |Numerosos sensores de temperatura de cada controlador têm o seu estado apresentado, o que indica se a temperatura encontrada está dentro da tolerância. |
| Expansor SAS |I/O |Físico |Controlador |No |Indica o estado do expansor SAS, que é utilizado para ligar o armazenamento integrado ao controlador. |
| Conector SAS [0-2] |I/O |Físico |Controlador |No |Indica o estado de cada conector SAS, que é utilizado para ligar o armazenamento integrado ao expansor SAS. |
| Interligação do plano médio SBB |I/O |Físico |Controlador |No |Indica o estado do conector do plano médio, que é utilizado para ligar cada controlador ao plano médio. |
| Poder eletrónico do recinto |I/O |Físico |Controlador |No |Indica o estado do sistema de alimentação utilizado pelo recinto. |
| Diagnósticos eletrónicos do recinto |I/O |Físico |Controlador |No |Indica o estado dos subsistemas de diagnóstico fornecidos pelo controlador. |
| Ligação ao controlador do dispositivo |I/O |Físico |Controlador |No |Indica o estado da ligação entre o módulo EBOD I/O e o controlador do dispositivo. |

## <a name="next-steps"></a>Passos seguintes
* Para utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo, vá [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
* Se necessitar de resolver um componente do dispositivo com um estado degradado ou falhado, consulte os [indicadores de monitorização StorSimple](storsimple-monitoring-indicators.md).
* Para substituir um componente de hardware falhado, consulte a [substituição do componente de hardware StorSimple](./storsimple-8000-hardware-component-replacement.md).
* Se continuar a experimentar problemas com o dispositivo, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).