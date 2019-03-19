---
title: Segurança do dispositivo StorSimple | Documentos da Microsoft
description: Descreve as convenções de segurança, diretrizes e as considerações e explica como instalar e operar o dispositivo StorSimple em segurança.
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
ms.openlocfilehash: 9d0c8c01ae0d87c944922dece74e13f00879b02f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010260"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Instalar e operar o dispositivo StorSimple em segurança
![Ícone de aviso](./media/storsimple-safety/IC740879.png)
![ícone de aviso de segurança de ler](./media/storsimple-safety/IC740885.png) **leitura informações de estado de funcionamento e segurança**

Ler todas as informações de segurança e o estado de funcionamento neste artigo que se aplica ao seu dispositivo do Microsoft Azure StorSimple. Manter todos os guias de impressos, fornecidos com o dispositivo StorSimple para referência futura. Falha ao siga as instruções e corretamente configurado, utilizar e se importa com para este produto pode aumentar o risco de tendão grave ou de morte ou danificar o dispositivo ou dispositivos. R [versão disponível para download deste guia](https://www.microsoft.com/download/details.aspx?id=44233) também está disponível.

## <a name="safety-icon-conventions"></a>Convenções de ícone de segurança
Aqui estão os ícones que encontrará ao rever as precauções de segurança a ser observado quando configurar e executar o seu dispositivo do Microsoft Azure StorSimple.

| Ícone | Descrição |
|:--- |:--- |
| ![Ícone de perigo](./media/storsimple-safety/IC740879.png) **perigo!** |Indica uma situação perigosa que, se não evitado, resultará em morte ou grave tendão. Esta palavra de sinal é ter um limite das situações mais graves. |
| ![Ícone de aviso](./media/storsimple-safety/IC740879.png) **aviso!** |Indica uma situação perigosa que, se não evitado, poderá resultar em morte ou grave tendão. |
| ![Ícone de aviso](./media/storsimple-safety/IC740879.png) **cuidado!** |Indica uma situação perigosa que, se não evitado, poderá resultar em lesão moderada ou pouco frequentes. |
| ![Observe o ícone](./media/storsimple-safety/IC740881.png) **aviso:** |Indica informações consideradas importantes, mas não relacionados com o perigo. |
| ![Ícone de um choque elétrico](./media/storsimple-safety/IC740882.png) **choque Elétrico perigo** |Tensão elevada |
| ![Ícone de peso pesada](./media/storsimple-safety/IC740883.png) **peso pesado** | |
| ![Nenhum utilizador serviceable na partes ícone](./media/storsimple-safety/IC740879.png) **sem partes serviceable do utilizador** |Não acedam a não ser devidamente treinados. |
| ![Ícone de aviso de segurança de ler](./media/storsimple-safety/IC740885.png)**ler todas as instruções em primeiro lugar** | |
| ![Ícone de perigo de sugestão](./media/storsimple-safety/IC740886.png) **sugestão perigo** | |

## <a name="handling-precautions"></a>Precauções de tratamento
![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![ícone de peso pesada](./media/storsimple-safety/IC740883.png) **aviso!** 

Para reduzir o risco de tendão:

* Um bastidor totalmente configurado pode avaliar até 32 kg (70 lbs); Não tente retirá-lo sozinho.
* Antes de passar o bastidor, certifique-se sempre de que duas pessoas estão disponíveis para lidar com o peso. Lembre-se de que uma pessoa tentar acabar com esta peso suportar injuries.
* Não migrar o bastidor pelos identificadores na energia e refrigeração módulos (PCMs), localizados em traseiro da unidade. Estas entidades não foram concebidas para tirar o peso.

## <a name="connection-precautions"></a>Precauções de ligação
![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![ícone choque Elétrico](./media/storsimple-safety/IC740882.png) **aviso!**

Para reduzir a probabilidade de uma contusão, choque elétrico ou morte:

* Quando com tecnologia de várias origens de AC, desligue todos os de fonte de alimentação de energia para completo isolamento.
* Desligue permanentemente a unidade antes, movê-lo ou se achar que tem de se tornar danificado de qualquer forma.
* Fornece uma ligação de terra elétrico seguro para os cabos de fonte de alimentação de energia. Certifique-se de que o aterrados do bastidor cumpre os requisitos de nacionais e locais antes de aplicar power.
* Certifique-se de que a ligação de energia é sempre desconectada antes da remoção de um PCM da inclusão.
* Dado que o plug no cabo de alimentação de energia é o principal desligar o dispositivo, certifique-se de que a comunicação de socket localização próxima aos equipamentos e são facilmente acessível.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![ícone choque Elétrico](./media/storsimple-safety/IC740882.png) **aviso!**

Para reduzir a probabilidade de superaquecimento ou são disparados a partir as ligações elétrica:

* Forneça uma fonte de energia adequado com proteção de sobrecarga elétrico para cumprir os requisitos detalhados na especificação técnica.
* Não utilize bifurcated power cabos ("Y" oportunidades potenciais).
* Para cumprir requisitos térmicos, emissões e segurança aplicável, não abrange deve ser removidos e compartimentos de todos os devem ser preenchidos com módulos de plug-ins ou unidade espaços em branco.
* Certifique-se de que o equipamento é usado de forma especificada pelo fabricante. Se este equipamento é usado de forma não especificada pelo fabricante, a proteção fornecida pelos equipamentos pode ser afetada.

![Observe o ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Para a operação adequada do seu equipamento e para evitar danos de produto:

* As portas RJ45 parte traseira desse dispositivo destinam-se apenas uma ligação de Ethernet. Eles não devem estar conectados a uma rede de telecomunicações.
* Certifique-se de que instalar o dispositivo num bastidor que pode acomodar um design de resfriamento de frente para trás.
* Módulos de todos os plug-ins e em branco pratos fazem parte da cobertura do sistema. Eles só devem ser removidos quando uma substituição pode ser adicionada imediatamente. O sistema não podem ser executado sem todos os módulos ou espaços em branco no local.

## <a name="rack-system-precautions"></a>Precauções de sistema de bastidor
Os seguintes requisitos de segurança tem de ser considerados ao montar o dispositivo num bastidor Cab.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![ícone de perigo de sugestão](./media/storsimple-safety/IC740886.png) **aviso!**

Para reduzir a probabilidade de uma contusão de uma dica sobre:

* O design de rack deve suportar o peso total das caixas de instalado e deve incorporar funcionalidades de estabilização adequadas impedir que o bastidor tipping ou que está sendo transmitido pelo durante a instalação ou de uso normal.
* Ao carregar um bastidor, preencha o bastidor na parte inferior e vazio na parte superior para baixo.
* Não é slide mais de um bastidor fora do rack cada vez para evitar o perigo de bastidor toppling ao longo.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![ícone choque Elétrico](./media/storsimple-safety/IC740882.png) **aviso!**

Para reduzir a probabilidade de uma contusão, choque elétrico ou morte:

* Bastidor deve ter um sistema de distribuição elétrico seguro. Ele tem de fornecer proteção de excesso atual para o bastidor e não deve ser sobrecarregado pelo número total de inclusões instalado. A classificação de consumo de energia elétrica mostrada no nameplate deve ser observada.
* O sistema de distribuição elétrico tem de fornecer uma raiz confiável para cada bastidor no rack.
* O design do sistema de distribuição elétrico deve levar em consideração a fuga de zero total atual de todas as fontes de alimentação em todas as inclusões. Observe que cada fonte de alimentação em cada bastidor tem um atual de fuga de raiz de 1,0 máximo de mA em 60 Hz, 264 volts. Bastidor pode exigir a etiquetagem com "atual de FUGA elevada. Ligação de zero (Terra) é essencial antes de ligar uma fonte de alimentação."
* Bastidor, quando configurado com as inclusões, tem de cumprir os requisitos de segurança de UL 60950-1 e IEC 60950-1/EN 60950-1.

![Observe o ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Para o resfriamento apropriado do seu sistema de rack:

* Certifique-se de que o design de rack leva em consideração o bastidor máximo operacional temperatura ambiente de 35 graus Celsius (95 graus Fahrenheit).
* O sistema é operado com low-pressure, esgotar por trás da instalação (pressão criado por rack portas e os obstáculos não deve exceder 5 Pascal [medidor de água de 0,5 mm]).

## <a name="power-cooling-module-pcm-precautions"></a>Precauções de refrigeração módulo PCM () de energia
O dispositivo foi concebido para operar com dois PCMs. Cada um os PCMs tem uma fonte de alimentação e um fã de eixo de dual. Durante uma condição crítica, o sistema permite uma falha de uma fonte de alimentação e continuar as operações normais. Dois PCMs (e, por conseguinte, alimentação) tem de ser sempre instalada. Um único PCM não fornece energia redundante. Por conseguinte, a falha de até mesmo um PCM pode resultar em tempo de inatividade ou possível perda de dados.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) ![ícone choque Elétrico](./media/storsimple-safety/IC740882.png) **aviso!**

Para reduzir a probabilidade de uma contusão, choque elétrico ou morte:

* Não remova bastidores do PCM. Existe um perigo de choque elétrico dentro. Para devolver o PCM e obter uma substituição [contacte o Microsoft Support](storsimple-contact-microsoft-support.md).

![Observe o ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Para a operação adequada do seu equipamento e para evitar danos de produto:

* Tem de substituir o PCM com falha dentro de 24 horas. Depois de remover um PCM para substituição, a substituição deve ser concluída em 10 minutos após a remoção.
* Não remova um PCM, a menos que uma substituição pode ser instalada imediatamente. A inclusão não tem de ser utilizada sem todos os módulos no local.

## <a name="electrostatic-discharge-esd-precautions"></a>Precauções mecânico altas (ESD)
![Observe o ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Observe as seguintes precauções ESD relacionados.

* Certifique-se de que tem instalado e verificado uma strap pulso ou pulseiras antistatic adequado.
* Quando o processamento de módulos e componentes, observe todas as precauções ESD convencionais.
* Evite contacto com os componentes de backplane e conectores de módulo.
* Danos ESD não é abrangido por garantia.

## <a name="battery-disposal-precautions"></a>Precauções de disposição de bateria
A fonte de alimentação usa uma bateria especial para proteger o conteúdo da memória durante falhas de energia temporária de curto prazo. A bateria é sentada no PCM. Tenha as seguintes informações em mente sobre a bateria.

![Ícone de aviso](./media/storsimple-safety/IC740879.png) **aviso!**

Para reduzir o risco de caras, fire, explosão, lesão ou morte:

* Descarte baterias utilizadas em conformidade com regulamentos national/regionais.
* Não desassemblar, supere, ou de calor acima 60 graus Celsius (140 graus Fahrenheit) ou incinerate. Substitua a bateria PCM com apenas uma bateria fornecida. Utilização da bateria outro pode representam um risco de fogo ou explosão.
* Utilize limites de proteção final as baterias se estes são removidos de alimentação.

![Observe o ícone](./media/storsimple-safety/IC740881.png) **aviso:**

Quando envio ou caso contrário, transportar as baterias pelo ar, siga o documento de orientação de bateria do Lithium IATA disponível em [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Depois de rever estes avisos de segurança, os passos seguintes são para descompactar, montar e ligar o seu dispositivo.

## <a name="next-steps"></a>Passos Seguintes
* Para um dispositivo 8100, aceda a [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).
* Para um dispositivo 8600, aceda a [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

