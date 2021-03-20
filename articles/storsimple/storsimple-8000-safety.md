---
title: Instale com segurança & operar o dispositivo da série StorSimple 8000
description: Descreve convenções de segurança, diretrizes e considerações e explica como instalar e operar com segurança o seu dispositivo StorSimple.
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
ms.openlocfilehash: 3adbd292d42fd6fd2a0e149e103c17968a6f084b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94950759"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Instale e opere com segurança o seu dispositivo StorSimple
![Ícone de aviso ](./media/storsimple-safety/IC740879.png)
 ![ Leia o ícone do aviso de segurança ](./media/storsimple-safety/IC740885.png) **LEIA INFORMAÇÕES DE SEGURANÇA E SAÚDE**

Leia todas as informações de segurança e saúde deste artigo que se aplica ao seu dispositivo Microsoft Azure StorSimple. Guarde todas as guias impressas enviadas com o seu dispositivo StorSimple para referência futura. O não cumprimento das instruções e a configuração, utilização e cuidados adequados deste produto podem aumentar o risco de ferimentos graves ou morte, ou danos no dispositivo ou dispositivos. Está também [disponível uma versão transferível deste guia.](https://www.microsoft.com/download/details.aspx?id=44233)

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança
Aqui estão os ícones que irá encontrar quando revê as precauções de segurança a observar ao configurar e executar o seu dispositivo Microsoft Azure StorSimple.

| Ícone | Description |
|:--- |:--- |
| ![Perigo de ícone ](./media/storsimple-safety/IC740879.png) **de perigo!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos graves. Esta palavra de sinal deve limitar-se às situações mais extremas. |
| ![Aviso ícone ](./media/storsimple-safety/IC740879.png) **AVISO!** |Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos graves. |
| ![Aviso Ícone ](./media/storsimple-safety/IC740879.png) **CUIDADO!** |Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos ligeiros ou moderados. |
| ![Aviso de ícone ](./media/storsimple-safety/IC740881.png) **de aviso:** |Indica informações consideradas importantes, mas não relacionadas com riscos. |
| ![Risco de choque elétrico do ícone do choque ](./media/storsimple-safety/IC740882.png) **elétrico** |Alta tensão |
| ![Peso pesado ícone peso ](./media/storsimple-safety/IC740883.png) **pesado** | |
| ![Nenhum ícone de peças de serviço do utilizador ](./media/storsimple-safety/IC740879.png) **sem peças resmáveis do utilizador** |Não aceda a menos que esteja devidamente treinado. |
| ![Leia o ícone do aviso de segurança](./media/storsimple-safety/IC740885.png)**Leia todas as instruções primeiro** | |
| ![Risco de ponta de ](./media/storsimple-safety/IC740886.png) **aviso de risco de ponta** | |

## <a name="handling-precautions"></a>Precauções de manuseamento
![Aviso Ícone ](./media/storsimple-safety/IC740879.png) ![ de peso pesado ](./media/storsimple-safety/IC740883.png) **AVISO!** 

Para reduzir o risco de ferimentos:

* Um recinto totalmente configurado pode pesar até 32 kg ( 70 lbs); não tente levantá-lo sozinho.
* Antes de mover o recinto, certifique-se sempre de que duas pessoas estão disponíveis para suportar o peso. Esteja ciente de que uma pessoa que tenta levantar este peso pode sofrer ferimentos.
* Não levante o invólucro pelas pegas dos Módulos de Alimentação e Arrefecimento (PCMs) situados na parte de trás da unidade. Estes não são projetados para assumir o peso.

## <a name="connection-precautions"></a>Precauções de ligação
![Aviso Ícone ](./media/storsimple-safety/IC740879.png) ![ ícone ícone de choque ](./media/storsimple-safety/IC740882.png) **elétrico AVISO!**

Para reduzir a probabilidade de ferimentos, choques elétricos ou morte:

* Quando alimentado por várias fontes de CA, desligue toda a alimentação para um isolamento total.
* Desligue permanentemente a ficha antes de a mover ou se pensa que ficou danificada de alguma forma.
* Fornecer uma ligação de terra elétrica segura aos cabos de alimentação. Verifique se a ligação à terra do recinto satisfaz os requisitos nacionais e locais antes de aplicar a energia.
* Certifique-se de que a ligação elétrica está sempre desligada antes da remoção de um PCM do recinto.
* Dado que a ficha do cabo de alimentação é o dispositivo principal de desconexão, certifique-se de que as tomadas estão localizadas perto do equipamento e são facilmente acessíveis.

![Aviso Ícone ](./media/storsimple-safety/IC740879.png) ![ ícone ícone de choque ](./media/storsimple-safety/IC740882.png) **elétrico AVISO!**

Para reduzir a probabilidade de sobreaquecimento ou incêndio das ligações elétricas:

* Fornecer uma fonte de alimentação adequada com proteção elétrica para sobrecargas para satisfazer os requisitos detalhados na especificação técnica.
* Não utilize cabos de alimentação bifurcados (cabos "Y").
* Para cumprir os requisitos de segurança, emissões e térmicos aplicáveis, não devem ser removidas tampas e todas as baías devem ser povoadas com módulos plug-in ou em branco de condução.
* Certifique-se de que o equipamento é utilizado de forma especificada pelo fabricante. Se este equipamento for utilizado de forma não especificada pelo fabricante, a proteção fornecida pelo equipamento pode ser prejudicada.

![Aviso de ícone ](./media/storsimple-safety/IC740881.png) **de aviso:**

Para o bom funcionamento do seu equipamento e para evitar danos no produto:

* As portas RJ45 na parte de trás do dispositivo destinam-se apenas a uma ligação Ethernet. Estes não devem estar ligados a uma rede de telecomunicações.
* Certifique-se de que instala o dispositivo num rack que pode acomodar um design de arrefecimento frontal a traseiro.
* Todos os módulos plug-in e placas em branco fazem parte do recinto do sistema. Estes só devem ser removidos quando uma substituição pode ser imediatamente adicionada. O sistema não deve ser executado sem todos os módulos ou espaços em branco no lugar.

## <a name="rack-system-precautions"></a>Precauções do sistema de cremalheira
Os seguintes requisitos de segurança devem ser considerados quando montar o aparelho num armário de prateleiras.

![Aviso Ícone ](./media/storsimple-safety/IC740879.png) ![ de perigo de ponta de ](./media/storsimple-safety/IC740886.png) **aviso AVISO!**

Para reduzir a probabilidade de ferimentos de uma inclinação:

* O desenho da cremalheira deve suportar o peso total das caixas instaladas e deve incorporar características de estabilização adequadas para evitar que a cremalheira tombe ou seja empurrada durante a instalação ou utilização normal.
* Ao carregar uma cremalheira, encha a cremalheira de baixo para cima e esvazie de cima para baixo.
* Não deslize mais do que uma caixa para fora da cremalheira de cada vez para evitar o perigo de a cremalheira cair.

![Aviso Ícone ](./media/storsimple-safety/IC740879.png) ![ ícone ícone de choque ](./media/storsimple-safety/IC740882.png) **elétrico AVISO!**

Para reduzir a probabilidade de ferimentos, choques elétricos ou morte:

* O rack deve ter um sistema de distribuição elétrica seguro. Deve proporcionar uma proteção excessiva ao recinto e não deve ser sobrecarregado pelo número total de caixas instaladas. A classificação de consumo de energia elétrica mostrada na placa de identificação deve ser observada.
* O sistema de distribuição elétrica deve fornecer um terreno fiável para cada caixa na cremalheira.
* A conceção do sistema de distribuição elétrica deve ter em conta a corrente total de fugas de terra de todas as fontes de alimentação em todos os compartimentos. Note que cada fonte de alimentação em cada caixa tem uma corrente de fuga de terra de 1,0 mA no máximo a 60 Hz, 264 volts. A cremalheira pode necessitar de rotulagem com "CORRENTE DE FUGA ELEVADA. A ligação terrestre (terra) é essencial antes de ligar uma fonte."
* A cremalheira, quando configurada com os recintos, deve satisfazer os requisitos de segurança da UL 60950-1 e do IEC 60950-1/EN 60950-1.

![Aviso de ícone ](./media/storsimple-safety/IC740881.png) **de aviso:**

Para o arrefecimento adequado do seu sistema de cremalheira:

* Certifique-se de que o desenho da cremalheira tem em conta a temperatura ambiente máxima de 35 graus Celsius (95 graus Fahrenheit). Mantenha o local onde o sistema de cremalheira está fresco e verifique se existe um fluxo de ar adequado da ventilação cacídrico no centro de dados.
* O sistema é operado com uma instalação de baixo pressão, de escape traseiro (pressão traseira criada por portas de cremalheira e obstáculos que não excedam 5 Pascal [manómetro de água de 0,5 mm]).

## <a name="power-cooling-module-pcm-precautions"></a>Precauções do Módulo de Arrefecimento de Potência (PCM)

O dispositivo foi concebido para funcionar com dois PCMs. Cada um dos PCMs tem uma fonte de alimentação e um ventilador de eixo duplo. Durante um estado crítico, o sistema permite uma falha de uma fonte de alimentação enquanto continua as operações normais. Devem ser sempre instalados dois PCMs (e, portanto, fontes de alimentação). Um único PCM não fornece energia redundante. Portanto, a falha de um PCM pode resultar em tempo de inatividade ou possível perda de dados.

![Aviso Ícone ](./media/storsimple-safety/IC740879.png) ![ ícone ícone de choque ](./media/storsimple-safety/IC740882.png) **elétrico AVISO!**

Para reduzir a probabilidade de ferimentos, choques elétricos ou morte:

* Não retire as tampas do PCM. Existe o perigo de choque elétrico no interior. Para retornar o PCM e obter uma substituição, [contacte o Microsoft Support](./storsimple-8000-contact-microsoft-support.md).

![Aviso de ícone ](./media/storsimple-safety/IC740881.png) **de aviso:**

Para o bom funcionamento do seu equipamento e para evitar danos no produto:

* Deve substituir o PCM falhado dentro de 24 horas. Depois de um PCM ser removido para substituição, a substituição deve ser concluída no prazo de 10 minutos após a remoção.
* Não remova um PCM a menos que uma substituição possa ser instalada imediatamente. O recinto não deve ser acionado sem todos os módulos no lugar.

## <a name="electrostatic-discharge-esd-precautions"></a>Precauções de descarga eletrostática (ESD)

![Aviso de ícone ](./media/storsimple-safety/IC740881.png) **de aviso:**

Respeite as seguintes precauções relacionadas com o ESD.

* Certifique-se de que instalou e verificou uma pulseira ou pulseira antiestática adequada.
* Observe todas as precauções convencionais do ESD ao manusear módulos e componentes.
* Evite o contacto com os componentes do backplane e os conectores do módulo.
* Os danos em ESD não estão cobertos pela garantia.

## <a name="battery-disposal-precautions"></a>Precauções de eliminação da bateria

A alimentação utiliza uma bateria especial para proteger o conteúdo da memória durante falhas temporárias de energia a curto prazo. A bateria está sentada no PCM. Tenha em mente as seguintes informações sobre a bateria.

![Aviso ícone ](./media/storsimple-safety/IC740879.png) **AVISO!**

Para reduzir o risco de calções, incêndio, explosão, ferimentos ou morte:

* Elimine as pilhas usadas de acordo com as normas nacionais/regionais.
* Não desmonte, esmague ou aqueça acima de 60 graus Celsius (140 graus Fahrenheit) ou incine. Substitua a bateria PCM apenas por uma bateria fornecida. A utilização de outra bateria pode representar um risco de incêndio ou explosão.
* Utilize tampas de proteção nas pilhas se estas forem retiradas da alimentação elétrica.

![Aviso de ícone ](./media/storsimple-safety/IC740881.png) **de aviso:**

Ao transportar ou transportar as baterias por ar, siga o documento de orientação da bateria de lítio IATA disponível em [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Depois de ter revisto estes avisos de segurança, os próximos passos são desempacotar, grelhar e cabo do seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

* Para um dispositivo 8100, vá [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).
* Para um dispositivo 8600, vá [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).