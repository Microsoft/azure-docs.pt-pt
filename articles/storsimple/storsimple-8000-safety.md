---
title: Segurança para o seu dispositivo StorSimple / Microsoft Docs
description: Descreve convenções de segurança, diretrizes e considerações, e explica como instalar e operar com segurança o seu dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 66b881ab13e27ee457af4fa1bafb82ad14e9674d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631685"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Instale e opere com segurança o seu dispositivo StorSimple
![Ícone](./media/storsimple-safety/IC740879.png)
![de aviso](./media/storsimple-safety/IC740885.png) Ler Ícone de Aviso de Segurança **LEIA INFORMAÇÕES DE SEGURANÇA E SAÚDE**

Leia todas as informações de segurança e saúde deste artigo que se aplicam ao seu dispositivo Microsoft Azure StorSimple. Guarde todos os guias impressos enviados com o seu dispositivo StorSimple para referência futura. O não cumprimento das instruções e a instalação, utilização e cuidados adequados deste produto podem aumentar o risco de ferimentos graves ou morte, ou danos no dispositivo ou dispositivos. Também está disponível uma [versão transferível deste guia.](https://www.microsoft.com/download/details.aspx?id=44233)

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança
Aqui estão os ícones que encontrará quando rever as precauções de segurança a observar ao configurar e executar o seu dispositivo Microsoft Azure StorSimple.

| Ícone | Descrição |
|:--- |:--- |
| ![Perigo](./media/storsimple-safety/IC740879.png) **perigo PERIGO!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos graves. Esta palavra de sinal deve limitar-se às situações mais extremas. |
| ![Aviso](./media/storsimple-safety/IC740879.png) **Ícone AVISO!** |Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos graves. |
| ![Advertência](./media/storsimple-safety/IC740879.png) Ícone **ATENÇÃO!** |Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos ligeiros ou moderados. |
| ![Aviso](./media/storsimple-safety/IC740881.png) de ícone **de aviso:** |Indica informações consideradas importantes, mas não relacionadas com riscos. |
| ![](./media/storsimple-safety/IC740882.png) **Risco de choque elétrico** do ícone de choque elétrico |Alta tensão |
| ![](./media/storsimple-safety/IC740883.png) **Peso pesado** ícone do peso pesado | |
| ![Sem ícone de](./media/storsimple-safety/IC740879.png) peças reparaíveis ao **utilizador, sem peças reparaveis** ao utilizador |Não aceda a menos que esteja devidamente treinado. |
| ![Leia o ícone do aviso de](./media/storsimple-safety/IC740885.png)**segurança, leia todas as instruções primeiro** | |
| ![Perigo de](./media/storsimple-safety/IC740886.png) ponta hazard **ícone** | |

## <a name="handling-precautions"></a>Precauções de manuseamento
![Aviso](./media/storsimple-safety/IC740879.png) ![Ícone de](./media/storsimple-safety/IC740883.png) peso pesado **ícone AVISO!** 

Para reduzir o risco de ferimentos:

* Um recinto totalmente configurado pode pesar até 32 kg( 70 lbs); não tente levantá-lo sozinho.
* Antes de deslocar o recinto, certifique-se sempre de que duas pessoas estão disponíveis para lidar com o peso. Esteja ciente de que uma pessoa que tenta levantar este peso pode sofrer ferimentos.
* Não levante o invólucro pelas pegas dos módulos de alimentação e arrefecimento (PCMs) situados na parte traseira da unidade. Estes não foram concebidos para tirar o peso.

## <a name="connection-precautions"></a>Precauções de ligação
![Aviso](./media/storsimple-safety/IC740879.png) ![Ícone](./media/storsimple-safety/IC740882.png) de choque elétrico **Ícone AVISO!**

Para reduzir a probabilidade de ferimentos, choques elétricos ou morte:

* Quando alimentado por várias fontes cacis, desligue toda a alimentação para um isolamento completo.
* Desligue permanentemente a unidade antes de a mover ou se achar que ficou danificada de alguma forma.
* Forneça uma ligação elétrica segura à terra aos cabos de alimentação. Verifique se a ligação à terra do recinto satisfaz os requisitos nacionais e locais antes de aplicar o poder.
* Certifique-se de que a ligação elétrica está sempre desligada antes da remoção de um PCM do recinto.
* Dado que a ficha do cabo de alimentação é o principal dispositivo de desconexão, certifique-se de que as tomadas estão localizadas perto do equipamento e são facilmente acessíveis.

![Aviso](./media/storsimple-safety/IC740879.png) ![Ícone](./media/storsimple-safety/IC740882.png) de choque elétrico **Ícone AVISO!**

Para reduzir a probabilidade de sobreaquecimento ou incêndio das ligações elétricas:

* Forneça uma fonte de energia adequada com proteção de sobrecarga elétrica para satisfazer os requisitos descritos na especificação técnica.
* Não utilize cabos de alimentação bifurcados (cabos "Y").
* Para satisfazer os requisitos de segurança, emissão e térmica aplicáveis, não devem ser retiradas tampas e todas as baías devem ser povoadas com módulos plug-in ou espaços em branco.
* Certifique-se de que o equipamento é utilizado de forma especificada pelo fabricante. Se este equipamento for utilizado de forma não especificada pelo fabricante, a proteção fornecida pelo equipamento pode ser prejudicada.

![Aviso](./media/storsimple-safety/IC740881.png) de ícone **de aviso:**

Para o bom funcionamento do seu equipamento e para evitar danos no produto:

* As portas RJ45 na parte de trás do dispositivo destinam-se apenas a uma ligação Ethernet. Estes não devem estar ligados a uma rede de telecomunicações.
* Certifique-se de que instala o dispositivo numa prateleira que possa acomodar um design de arrefecimento frontal.
* Todos os módulos plug-in e placas em branco fazem parte do recinto do sistema. Estes só devem ser removidos quando uma substituição pode ser imediatamente adicionada. O sistema não deve ser executado sem todos os módulos ou espaços em branco no lugar.

## <a name="rack-system-precautions"></a>Precauções do sistema de rack
Os seguintes requisitos de segurança devem ser considerados quando montar o aparelho num armário de cremalheira.

![Aviso](./media/storsimple-safety/IC740879.png) ![Ícone de](./media/storsimple-safety/IC740886.png) perigo de ponta do ícone **aTENÇÃO!**

Para reduzir a probabilidade de ferimentos de uma ponta:

* A conceção do rack deve suportar o peso total dos recintos instalados e incorporar características de estabilização adequadas para evitar que a cremalheira tombe ou seja empurrada durante a instalação ou utilização normal.
* Ao carregar uma prateleira, encha a cremalheira de baixo para cima e esvazie de cima para baixo.
* Não deslize mais do que um recinto para fora da prateleira de cada vez para evitar o perigo de a cremalheira cair.

![Aviso](./media/storsimple-safety/IC740879.png) ![Ícone](./media/storsimple-safety/IC740882.png) de choque elétrico **Ícone AVISO!**

Para reduzir a probabilidade de ferimentos, choques elétricos ou morte:

* A cremalheira deve ter um sistema de distribuição elétrica seguro. Deve proporcionar proteção excessiva para o recinto e não deve ser sobrecarregado pelo número total de recintos instalados. Deve observar-se a classificação de consumo de energia elétrica mostrada na placa de identificação.
* O sistema de distribuição elétrica deve fornecer um terreno fiável para cada caixa da cremalheira.
* A conceção do sistema de distribuição elétrica deve ter em conta a corrente total de fugas de terra de todas as fontes de alimentação em todos os recintos. Note que cada alimentação em cada recinto tem uma corrente de fuga de 1,0 mA máxima a 60 Hz, 264 volts. A cremalheira pode necessitar de rotulagem com "ALTA CORRENTE DE FUGAS. A ligação terrestre (terra) é essencial antes de ligar uma fonte."
* O cremalheira, quando configurado com os recintos, deve satisfazer os requisitos de segurança dos UL 60950-1 e do IEC 60950-1/EN 60950-1.

![Aviso](./media/storsimple-safety/IC740881.png) de ícone **de aviso:**

Para o arrefecimento adequado do seu sistema de cremalheira:

* Certifique-se de que o design do rack tem em conta a temperatura ambiente máxima de funcionamento do recinto de 35 graus Celsius (95 graus Fahrenheit).
* O sistema é operado com instalação de escape traseiro de baixa pressão (pressão traseira criada por portas de cremalheira e obstáculos para não exceder 5 Pascal [calibre de água de 0,5 mm]).

## <a name="power-cooling-module-pcm-precautions"></a>Precauções do Módulo de Arrefecimento de Energia (PCM)
O dispositivo foi concebido para funcionar com dois PCMs. Cada um dos PCMs tem uma fonte de alimentação e um ventilador de dois eixos. Em estado crítico, o sistema permite uma falha de uma alimentação enquanto continua as operações normais. Devem ser sempre instalados dois PCMs (e, portanto, fontes de alimentação). Um único PCM não fornece energia redundante. Portanto, a falha de um mesmo PCM pode resultar em tempo de inatividade ou possível perda de dados.

![Aviso](./media/storsimple-safety/IC740879.png) ![Ícone](./media/storsimple-safety/IC740882.png) de choque elétrico **Ícone AVISO!**

Para reduzir a probabilidade de ferimentos, choques elétricos ou morte:

* Não retire as tampas do PCM. Há o perigo de choque elétrico lá dentro. Para devolver o PCM e obter uma substituição, [contacte](storsimple-contact-microsoft-support.md)o Microsoft Support .

![Aviso](./media/storsimple-safety/IC740881.png) de ícone **de aviso:**

Para o bom funcionamento do seu equipamento e para evitar danos no produto:

* Deve substituir o PCM falhado dentro de 24 horas. Depois de removido um PCM para substituição, a substituição deve ser concluída no prazo de 10 minutos após a remoção.
* Não remova um PCM a menos que uma substituição possa ser instalada imediatamente. O recinto não deve ser acionado sem todos os módulos no lugar.

## <a name="electrostatic-discharge-esd-precautions"></a>Precauções de descarga eletrostática (ESD)
![Aviso](./media/storsimple-safety/IC740881.png) de ícone **de aviso:**

Observe as seguintes precauções relacionadas com o ESD.

* Certifique-se de que instalou e verificou uma correia antistática adequada no pulso ou no tornozelo.
* Observe todas as precauções convencionais do ESD ao manusear módulos e componentes.
* Evite o contacto com componentes de backplane e conectores de módulos.
* Os danos em ESD não estão cobertos pela garantia.

## <a name="battery-disposal-precautions"></a>Precauções de eliminação de baterias
A alimentação utiliza uma bateria especial para proteger o conteúdo da memória durante falhas temporárias de energia a curto prazo. A bateria está sentada no PCM. Tenha em mente as seguintes informações sobre a bateria.

![Aviso](./media/storsimple-safety/IC740879.png) **Ícone AVISO!**

Para reduzir o risco de calções, incêndio, explosão, ferimentos ou morte:

* Elimine as pilhas usadas de acordo com as normas nacionais/regionais.
* Não desmonte, esmague ou aqueça acima dos 60 graus Celsius (140 graus Fahrenheit) nem incinerar. Substitua a bateria PCM apenas por uma bateria fornecida. A utilização de outra bateria pode representar um risco de incêndio ou explosão.
* Utilize tampas de proteção nas pilhas se estas forem removidas da fonte de alimentação.

![Aviso](./media/storsimple-safety/IC740881.png) de ícone **de aviso:**

Ao enviar ou transportar as baterias por ar, siga o documento de orientação da bateria de lítio IATA disponível em[https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Depois de ter revisto estes avisos de segurança, os próximos passos são desempacotar, grelhar e cabo o seu dispositivo.

## <a name="next-steps"></a>Passos seguintes
* Para um dispositivo 8100, vá instalar [o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).
* Para um dispositivo 8600, vá instalar [o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

