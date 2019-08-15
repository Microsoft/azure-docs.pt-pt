---
title: Segurança para seu dispositivo StorSimple | Microsoft Docs
description: Descreve as convenções de segurança, as diretrizes e as considerações e explica como instalar e operar seu dispositivo StorSimple com segurança.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 4622a8575d7b6a38226ee3a980c05f143c128356
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963543"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Instalar e operar seu dispositivo StorSimple com segurança

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

![Ícone de aviso](./media/storsimple-safety/IC740885.png) ler ícone de aviso de segurança ler segurança e informações de integridade![](./media/storsimple-safety/IC740879.png)


Leia todas as informações de segurança e integridade neste artigo que se aplicam ao seu dispositivo de Microsoft Azure StorSimple. Mantenha todos os guias impressos fornecidos com seu dispositivo StorSimple para referência futura. A falha em seguir as instruções e a configuração, o uso e o cuidado corretos para esse produto pode aumentar o risco de sérios ferimentos ou morte ou danos ao dispositivo ou aos dispositivos. Também está disponível uma [versão para download deste guia](https://www.microsoft.com/download/details.aspx?id=44233) .

## <a name="safety-icon-conventions"></a>Convenções de ícone de segurança
Aqui estão os ícones que você encontrará ao examinar as precauções de segurança a serem observadas ao configurar e executar o dispositivo Microsoft Azure StorSimple.

| Ícone | Descrição |
|:--- |:--- |
| ![Ícone](./media/storsimple-safety/IC740879.png) de perigo **perigo!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos sério. Esta palavra de sinal é limitada às situações mais extremas. |
| ![Aviso de](./media/storsimple-safety/IC740879.png) ícone de aviso **!** |Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos grave. |
| ![Cuidado com](./media/storsimple-safety/IC740879.png) o ícone de aviso **!** |Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos secundário ou moderado. |
| ![Aviso do](./media/storsimple-safety/IC740881.png) ícone de aviso **:** |Indica informações consideradas importantes, mas não relacionadas a riscos. |
| ![Ícone](./media/storsimple-safety/IC740882.png) de choque elétrico **risco de choque elétrico** |Alta tensão |
| ![Ícone](./media/storsimple-safety/IC740883.png) de peso pesado **peso pesado** | |
| ![Nenhum ícone](./media/storsimple-safety/IC740879.png) de partes sem serviço de usuário **sem nenhuma parte do usuário** |Não acesse, a menos que seja devidamente treinado. |
| ![Ler o ícone de aviso de segurança](./media/storsimple-safety/IC740885.png)**Leia todas as instruções primeiro** | |
| ![Ícone](./media/storsimple-safety/IC740886.png) de risco Tip de **gorjeta** | |

## <a name="handling-precautions"></a>Precauções de manipulação
![Ícone](./media/storsimple-safety/IC740879.png) ](./media/storsimple-safety/IC740883.png) de aviso ícone de peso pesado aviso! ![ 

Para reduzir o risco de ferimentos:

* Um compartimento totalmente configurado pode pesar até 32 kg (70 lbs); Não tente fazê-lo por conta própria.
* Antes de mover o compartimento, sempre verifique se duas pessoas estão disponíveis para lidar com o peso. Lembre-se de que uma pessoa que está tentando levantar esse peso pode sustentar ferimentos.
* Não levante o compartimento pelas alças nos módulos de energia e resfriamento (PCMs) localizados na parte posterior da unidade. Elas não foram projetadas para assumir o peso.

## <a name="connection-precautions"></a>Precauções de conexão
![Ícone](./media/storsimple-safety/IC740879.png) ](./media/storsimple-safety/IC740882.png) de aviso ícone de choque elétrico aviso! ![

Para reduzir a probabilidade de ferimentos, choque elétrico ou morte:

* Quando alimentado por várias fontes AC, desconecte todos os recursos de alimentação para o isolamento completo.
* Desconecte permanentemente a unidade antes de movê-la ou se você acreditar que ela se tornou danificada de alguma forma.
* Forneça uma conexão de terra elétrica segura para os cabos de fonte de alimentação. Verifique se o aterramento do compartimento atende aos requisitos nacionais e locais antes de aplicar energia.
* Verifique se a conexão de energia está sempre desconectada antes da remoção de um PCM do compartimento.
* Considerando que o plug-in do cabo da fonte de energia é o dispositivo de desconexão principal, verifique se as saídas do soquete estão localizadas perto do equipamento e estão facilmente acessíveis.

![Ícone](./media/storsimple-safety/IC740879.png) ](./media/storsimple-safety/IC740882.png) de aviso ícone de choque elétrico aviso! ![

Para reduzir a probabilidade de superaquecimento ou incêndios das conexões elétricas:

* Forneça uma fonte de energia adequada com proteção contra sobrecarga elétrica para atender aos requisitos detalhados na especificação técnica.
* Não use cabos de energia cabos ("Y" leads).
* Para estar em conformidade com os requisitos de segurança, emissão e térmico aplicáveis, nenhuma capa deve ser removida e todos os compartimentos devem ser preenchidos com módulos de plug-in ou em espaços em branco da unidade.
* Verifique se o equipamento é usado de uma maneira especificada pelo fabricante. Se este equipamento for usado de uma maneira não especificada pelo fabricante, a proteção fornecida pelo equipamento poderá ser prejudicada.

![Aviso do](./media/storsimple-safety/IC740881.png) ícone de aviso **:**

Para a operação adequada do seu equipamento e para evitar danos ao produto:

* As portas RJ45 na parte posterior do dispositivo são apenas para uma conexão Ethernet. Eles não devem estar conectados a uma rede de telecomunicações.
* Certifique-se de instalar o dispositivo em um rack que possa acomodar um design de resfriamento de frente para trás.
* Todos os módulos de plug-in e placas em branco fazem parte do compartimento do sistema. Eles só devem ser removidos quando uma substituição puder ser adicionada imediatamente. O sistema não deve ser executado sem todos os módulos ou espaços em branco em vigor.

## <a name="rack-system-precautions"></a>Precauções do sistema de rack
Os requisitos de segurança a seguir devem ser considerados quando você monta o dispositivo em um gabinete de rack.

![Ícone](./media/storsimple-safety/IC740879.png) de![aviso ícone](./media/storsimple-safety/IC740886.png) de risco de dica de advertência **!**

Para reduzir a probabilidade de ferimentos de uma dica:

* O design do rack deve dar suporte ao peso total dos compartimentos instalados e deve incorporar os recursos de estabilização adequados para impedir que o rack se dedique ou seja enviado durante a instalação ou o uso normal.
* Ao carregar um rack, preencha o rack de baixo para cima e esvazie-o de cima para baixo.
* Não deslize mais de um compartimento para fora do rack de cada vez para evitar o perigo do rack.

![Ícone](./media/storsimple-safety/IC740879.png) ](./media/storsimple-safety/IC740882.png) de aviso ícone de choque elétrico aviso! ![

Para reduzir a probabilidade de ferimentos, choque elétrico ou morte:

* O rack deve ter um sistema de distribuição elétrica seguro. Ele deve fornecer proteção de sobrecarga para o compartimento e não deve ser sobrecarregado pelo número total de compartimentos instalados. A classificação de consumo de energia elétrica mostrada no placa deve ser observada.
* O sistema de distribuição elétrica deve fornecer um aterramento confiável para cada compartimento no rack.
* O design do sistema de distribuição elétrica deve levar em consideração o total de vazamentos de aterramento de todas as fontes de alimentação em todos os compartimentos. Observe que cada fonte de alimentação em cada compartimento tem uma corrente de vazamento de aterramento do máximo de 1,0 mA a 60 Hz, 264 volts. O rack pode exigir rotulagem com "corrente de alta perda. A conexão terrestre (terra) é essencial antes de conectar um fornecimento. "
* O rack, quando configurado com os compartimentos, deve atender aos requisitos de segurança de UL 60950-1 e IEC 60950-1/EN 60950-1.

![Aviso do](./media/storsimple-safety/IC740881.png) ícone de aviso **:**

Para o resfriamento adequado do seu sistema de rack:

* Verifique se o design do rack leva em consideração a temperatura ambiente máxima de operação do compartimento de 35 graus Celsius (95 graus Fahrenheit).
* O sistema é operado com baixa pressão, instalação de exaustão traseira (pressão traseira criada por portas de rack e obstáculos para não exceder 5 Pascal [0,5 mm de indicador de água]).

## <a name="power-cooling-module-pcm-precautions"></a>Precauções do módulo de refrigeração de energia (PCM)
O dispositivo foi projetado para operar com dois PCMs. Cada um dos PCMs tem uma fonte de alimentação e um ventilador de dois eixos. Durante uma condição crítica, o sistema permite uma falha de uma fonte de alimentação enquanto continua as operações normais. Duas PCMs (e, portanto, fontes de alimentação) devem ser sempre instaladas. Um único PCM não fornece energia redundante. Portanto, a falha de até mesmo um PCM pode resultar em tempo de inatividade ou possível perda de dados.

![Ícone](./media/storsimple-safety/IC740879.png) ](./media/storsimple-safety/IC740882.png) de aviso ícone de choque elétrico aviso! ![

Para reduzir a probabilidade de ferimentos, choque elétrico ou morte:

* Não remova as capas do PCM. Há um perigo de choque elétrico dentro. Para retornar o PCM e obter uma substituição, [entre em contato com suporte da Microsoft](storsimple-contact-microsoft-support.md).

![Aviso do](./media/storsimple-safety/IC740881.png) ícone de aviso **:**

Para a operação adequada do seu equipamento e para evitar danos ao produto:

* Você deve substituir o PCM com falha dentro de 24 horas. Depois que um PCM é removido para substituição, a substituição deve ser concluída em 10 minutos após a remoção.
* Não remova um PCM, a menos que uma substituição possa ser instalada imediatamente. O compartimento não deve ser operado sem todos os módulos em vigor.

## <a name="electrostatic-discharge-esd-precautions"></a>Precauções de descarga eletrostática (ESD)
![Aviso do](./media/storsimple-safety/IC740881.png) ícone de aviso **:**

Observe as seguintes precauções relacionadas a ESD.

* Verifique se você instalou e verificou uma pulseira antiestática ou um pulso tornozelo adequado.
* Observe todas as precauções de ESD convencionais ao manipular módulos e componentes.
* Evite contatar com componentes de backplane e conectores de módulo.
* Danos ESD não são cobertos pela garantia.

## <a name="battery-disposal-precautions"></a>Precauções de descarte de bateria
A fonte de alimentação usa uma bateria especial para proteger o conteúdo da memória durante interrupções de energia temporárias e de curto prazo. A bateria está encaixada no PCM. Tenha em mente as seguintes informações sobre a bateria.

![Aviso de](./media/storsimple-safety/IC740879.png) ícone de aviso **!**

Para reduzir o risco de curto, incêndio, explosão, lesão ou morte:

* Descarte as baterias usadas de acordo com as normas nacionais/regionais.
* Não desmonte, Crush ou calor acima de 60 graus Celsius (140 graus Fahrenheit) ou incinere. Substitua a bateria do PCM somente por uma bateria fornecida. O uso de outra bateria pode representar um risco de incêndio ou explosão.
* Use as tampas de extremidade de proteção nas baterias se elas forem removidas da fonte de alimentação.

![Aviso do](./media/storsimple-safety/IC740881.png) ícone de aviso **:**

Ao enviar ou transportar de outra forma as baterias por ar, siga o documento de diretrizes de bateria de lítio IATA disponível em[https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Depois de revisar esses avisos de segurança, as próximas etapas serão desempacotadas, rack e cabeamento do seu dispositivo.

## <a name="next-steps"></a>Passos Seguintes
* Para um dispositivo 8100, acesse [instalar o dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).
* Para um dispositivo 8600, acesse [instalar o dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

