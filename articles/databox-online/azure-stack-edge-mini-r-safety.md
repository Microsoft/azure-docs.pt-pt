---
title: Guia de segurança Azure Stack Edge Mini R | Microsoft Docs
description: Descreve convenções de segurança, diretrizes, considerações e explica como instalar e operar com segurança o seu dispositivo Azure Stack Edge Mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: alkohli
ms.openlocfilehash: aa363b1eeddff6c3b10d8e36371becb8b690697c
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981030"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Instruções de segurança Azure Stack Edge Mini R

![Ícone de aviso 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Leia o ícone do aviso de segurança ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **LEIA INFORMAÇÕES DE SEGURANÇA E SAÚDE**

Leia todas as informações de segurança deste artigo antes de utilizar o seu dispositivo Azure Stack Edge Mini R, uma composição de uma bateria, uma fonte de alimentação ligada ac/DC, um adaptador de alimentação do módulo e um módulo de servidor. O não cumprimento das instruções pode resultar em incêndio, choque elétrico, ferimentos ou danos nas suas propriedades. Leia todas as informações de segurança abaixo antes de utilizar o Azure Stack Edge Mini R.

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança

As seguintes palavras de sinal para sinais de alerta de perigo são:

| Ícone | Description |
|:--- |:--- |
| ![Símbolo de perigo](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **PERIGO:** Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos graves. <br> **ADVERTÊNCIA:** Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos graves. <br> **ATENÇÃO:** Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos ligeiros ou moderados.|
|

Os seguintes ícones de perigo devem ser observados ao configurar e executar o seu dispositivo Azure Stack Edge Mini R:

| Ícone | Description |
|:--- |:--- |
| ![Leia todas as instruções primeiro](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Leia todas as instruções primeiro |
| ![Aviso de ícone ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **de aviso:** | Indica informações consideradas importantes, mas não relacionadas com riscos. |
| ![Símbolo de perigo](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Símbolo de perigo |
| ![Ícone de choque elétrico](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Risco de choque elétrico |
| ![Uso interior apenas](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Uso interior apenas |
| ![Sem ícone de peças de serviço do utilizador](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Sem peças de serviço para o utilizador. Não aceda a menos que esteja devidamente treinado. |
|

## <a name="handling-precautions-and-site-selection"></a>Precauções de manuseamento e seleção do local

O dispositivo Azure Stack Edge Mini R tem as seguintes precauções de manuseamento e critérios de seleção do local:

![Ícone de aviso 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Ícone de choque elétrico Sem ícone ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ de peças de serviço do utilizador ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **PRECAUÇÃO:**

* Inspecione o dispositivo *recebido* para verificar se há danos. Se a caixa do dispositivo estiver danificada, contacte o [Microsoft Support](azure-stack-edge-placeholder.md) para obter uma substituição. Não tente operar o aparelho.
* Se suspeitar que o dispositivo está avariado, contacte o [Microsoft Support](azure-stack-edge-placeholder.md) para obter uma substituição. Não tente fazer o serviço.
* O aparelho não contém peças que possam ser ressoáveis pelo utilizador. Tensão perigosa, corrente e níveis de energia estão presentes no interior. Não abra. Devolva o dispositivo à Microsoft para manutenção.

![Ícone de aviso 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATENÇÃO:**

Recomenda-se o funcionamento do sistema:

* Longe de fontes de calor, incluindo luz solar direta e radiadores.
* Em locais não expostos a humidade ou chuva.
* Localizado num espaço que minimiza a vibração e o choque físico.  O sistema foi concebido para choques e vibrações de acordo com o MIL-STD-810G.
* Isolados de campos eletromagnéticos fortes produzidos por dispositivos elétricos.
* Não permita que nenhum objeto líquido ou estranho entre no Sistema. Não coloque bebidas ou outros recipientes líquidos sobre ou perto do sistema.

![Ícone de aviso 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Sem ícone de peças de serviço do utilizador ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **CUIDADO:**

* Este equipamento contém uma bateria de lítio. Não tente fazer o serviço da bateria. As pilhas deste equipamento não são ressutilizáveis pelo utilizador. Risco de explosão se a bateria for substituída por um tipo incorreto.

![Ícone de aviso 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATENÇÃO:**

Carregue apenas a bateria quando faz parte do dispositivo Azure Stack Edge Mini R, não carregue como um dispositivo separado.

![Ícone de aviso 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATENÇÃO:**

* O interruptor ON/OFF da bateria destina-se apenas a descarregar a bateria no módulo do servidor. Se o adaptador de alimentação estiver ligado à bateria, a energia é transmitida para o módulo do servidor, mesmo que o interruptor esteja na posição OFF.

![Ícone de aviso 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATENÇÃO:**

* Não queime nem faça um curto-circuito na bateria. Deve ser reciclado ou eliminado corretamente.

![Ícone de aviso 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATENÇÃO:**

* Em vez de utilizar a fonte de alimentação AC/DC fornecida, este sistema também tem a opção de utilizar um campo fornecido bateria tipo 2590. Neste caso, o utilizador final verificará se satisfaz todas as normas de segurança, transporte, ambientais e quaisquer outras regulamentações nacionais/locais aplicáveis.
* Ao utilizar o sistema com bateria tipo 2590, opere a bateria dentro das condições de utilização especificadas pelo fabricante da bateria.

![Ícone de aviso 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **ATENÇÃO:**

Este dispositivo tem duas portas SFP+, que podem ser utilizadas com transceptores óticos. Para evitar radiações laser perigosas, utilize apenas com os transmissores da classe 1.

## <a name="electrical-precautions"></a>Precauções elétricas

O dispositivo Azure Stack Edge Mini R tem as seguintes precauções elétricas:

![Ícone de aviso 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ ícone de choque elétrico ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **AVISO:**

Quando utilizado com o adaptador de alimentação:

* Fornecer uma ligação de terra elétrica segura ao cabo de alimentação. O cabo de corrente alternada (AC) tem uma ficha de ligação à terra de três fios (uma ficha que tem um pino de ligação à terra). Esta ficha serve apenas a uma tomada de AR à terra. Não derrote o propósito do pino de ligação à terra.
* Dado que a ficha do cabo de alimentação é o dispositivo principal de desconexão, certifique-se de que as tomadas estão localizadas perto do dispositivo e são facilmente acessíveis.
* Desligue o cabo de alimentação (puxando a ficha, não o cabo) e desligando todos os cabos se existirem algumas das seguintes condições:

  * O cabo de alimentação ou a ficha ficam desgastados ou danificados de outra forma.
  * O aparelho está exposto à chuva, excesso de humidade ou outros líquidos.
  * O aparelho foi largado e o invólucro do dispositivo foi danificado.
  * Suspeita que o dispositivo precisa de assistência ou reparação.
* Desligue permanentemente a ficha antes de a mover ou se pensa que ficou danificada de alguma forma.

* Fornecer uma fonte de alimentação adequada com proteção elétrica para sobrecargas para satisfazer as seguintes especificações de energia:

* Voltagem: 100 - 240 Volts AC
* Corrente: 1.7 Amperes
* Frequência: 50 a 60 Hz

![Ícone de aviso 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Ícone de choque elétrico ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Não tente modificar ou utilizar cabos de alimentação CA que não os fornecidos com o equipamento.

![Ícone de aviso 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ Ícone de choque elétrico uso ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ interior apenas ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **AVISO:**

* A alimentação com este símbolo é classificada apenas para uso interno.

## <a name="regulatory-information"></a>Informação regulamentar

O seguinte contém informações regulamentares para o dispositivo Azure Stack Edge Mini R, número de modelo regulamentar: TMA01.

O dispositivo Azure Stack Edge Mini R foi concebido para ser utilizado com equipamentos de tecnologias de informação compatíveis com a NRTL (UL, CSA, ETL, etc.) e IEC/EN 60950-1 ou IEC/EN 62368-1 (marcado com CE) equipamentos de tecnologias de informação.

Noutros países que não os EUA e no Canadá, os cabos de rede (não fornecidos com o equipamento) não devem ser instalados com este equipamento se o seu comprimento for superior a 3 metros.

O equipamento foi concebido para funcionar nos seguintes ambientes:

| Ambiente | Especificações |
|:---  |:--- |
| Especificações de temperatura do sistema | <ul><li>Temperatura de armazenamento: -20 &deg; C-50 &deg; C (-4 &deg; F-122 &deg; F)</li><li>Funcionamento contínuo: 0 &deg; C-40 &deg; C (32 &deg; F-104 &deg; F)</li></ul> |
| Especificações relativas de humidade (RH) | <ul><li>Armazenamento: 5% a 95% de humidade relativa</li><li>Funcionamento: 10% a 90% de humidade relativa</li></ul>|
| Especificações máximas de altitude | <ul><li>Funcionamento: 15.000 pés (4.572 metros)</li><li>Não funcionamento: 40.000 pés (12.192 metros)</li></ul>|

> ![Aviso Ícone - 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **AVISO:** &nbsp; Alterações ou modificações efetuadas no equipamento não expressamente aprovadas pela Microsoft podem anular a autoridade do utilizador para operar o equipamento.

#### <a name="canada-and-usa"></a>CANADÁ e EUA:

> ![Aviso Ícone - 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **AVISO:** &nbsp; Este equipamento foi testado e encontra-se em conformidade com os limites para um dispositivo digital de classe A, nos termos da parte 15 das Regras da FCC. Estes limites destinam-se a proporcionar uma proteção razoável contra interferências nocivas quando o equipamento é operado num ambiente comercial. Este equipamento gera, utiliza e pode irradiar energia de radiofrequência e, se não for instalado e utilizado de acordo com o manual de instruções, pode causar interferências prejudiciais às comunicações de rádio. O funcionamento deste equipamento numa área residencial pode causar interferências prejudiciais, caso em que o utilizador será obrigado a corrigir a interferência às suas próprias custas.

O Adaptador WiFi USB Netgear A6150 fornecido com este equipamento destina-se a ser operado perto do corpo humano para a conformidade com a Taxa de Absorção Específica (SAR) usada pelo corpo. O limite sartos fixado pela FCC é de 1,6 W/kg quando em média mais de 1 g de tecido. Ao transportar o produto ou usá-lo durante o uso do seu corpo, mantenha uma distância de 10 mm do corpo para garantir o cumprimento dos requisitos de exposição ao RF.

O Adaptador WiFi WIFi Netgear A6150 está em conformidade com o ANSI/IEEE C95.1-1999 e foi testado de acordo com os métodos e procedimentos de medição especificados no Suplemento C do Boletim OET 65.

Taxa de absorção específica (SAR) de 1,18 W/kg média superior a 1 g de tecido

O Adaptador WiFi USB Netgear A6150 deve ser utilizado apenas com antenas aprovadas. Este dispositivo e as suas antenas não devem ser co-localizados ou a funcionar em conjunto com qualquer outra antena ou transmissor, exceto de acordo com os procedimentos do produto multitransmissor da FCC. Para os produtos disponíveis no mercado dos EUA, apenas o canal 1~11 pode ser operado. A seleção de outros canais não é possível.

A operação na banda 5150-5250 MHz destina-se apenas ao uso interno para reduzir o potencial de interferência prejudicial aos sistemas de satélites móveis de co-canal.

![Aviso de informação regulamentar - uso interior](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)


Os utilizadores são avisados de que os radares de alta potência são atribuídos como utilizadores primários (utilizadores prioritários) das bandas 5250-5350 MHz e 5650-5850 MHz, e estes radares podem causar interferências e/ou danos nos dispositivos LE-LAN.

Este equipamento gera, utiliza e pode irradiar energia de radiofrequência e, se não for instalado e utilizado de acordo com as instruções, pode causar interferência prejudicial às comunicações de rádio. No entanto, não há garantias de que não ocorram interferências numa determinada instalação.

Se este equipamento causar interferência prejudicial à receção de rádio ou televisão, o que pode ser determinado desligando e desligando o equipamento, o utilizador é encorajado a tentar corrigir a interferência através de uma ou mais das seguintes medidas:

- Reoriente ou reloque a antena recetora.
- Aumente a separação entre o equipamento e o recetor.
- Ligue o equipamento a uma tomada num circuito diferente daquele ao qual o recetor está ligado.
- Consulte o revendedor ou um técnico experiente de rádio/TV para obter ajuda.

Para obter mais informações sobre questões de interferência, aceda ao site da FCC [em fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). Também pode ligar para a FCC em 1-888-CALL FCC para solicitar fichas de interferência e interferência telefónica.

Informações adicionais sobre a segurança da radiofrequência podem ser encontradas no site da FCC [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) e no site da Industry Canada em [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Este produto demonstrou a conformidade da EMC em condições que incluem a utilização de dispositivos periféricos compatíveis e cabos blindados entre componentes do sistema. É importante que utilize dispositivos periféricos compatívels e cabos blindados entre componentes do sistema para reduzir a possibilidade de causar interferências em rádios, televisores e outros dispositivos eletrónicos.

Este dispositivo está em conformidade com a parte 15 das normas RSS isentas de licenças da FCC e da indústria do Canadá. O funcionamento está sujeito às duas condições seguintes: (1) este dispositivo não pode causar interferência prejudicial, e (2) este dispositivo deve aceitar qualquer interferência recebida, incluindo interferências que possam causar o funcionamento nãodesejável do dispositivo.

![Aviso de informação regulamentar 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, EUA

Estados Unidos: (800) 426-9400

Canadá: (800) 933-4750

Netgear A6150 WiFi ADAPTADOR USB FCC ID: PY318300429
 
Netgear A6150 WiFi ADAPTADOR USB IC ID: 4054A-18300429

O Adaptador WiFi USB Netgear A6150 fornecido com este equipamento está em conformidade com a SAR para limites gerais de exposição populacional/não controlada no IC RSS-102 e foi testado de acordo com os métodos e procedimentos de medição especificados no IEEE 1528. Mantenha uma distância mínima de 10 mm para o estado de desgaste corporal.

O Adaptador WiFi WIFi Netgear A6150 está em conformidade com o limite de exposição portátil de RF do Canadá estabelecido para um ambiente descontrolado e é seguro para o funcionamento pretendido, conforme descrito no seu manual. Uma redução adicional da exposição ao RF pode ser conseguida mantendo o produto o mais longe possível do seu corpo ou definindo o dispositivo para uma potência de saída mais baixa se tal função estiver disponível.

Uma tabela com a Taxa de Absorção Específica (SAR) média superior a 1 g para cada produto pode ser vista na secção dos EUA acima.

![Aviso de informação regulamentar 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>UNIÃO EUROPEIA:

Solicite uma cópia da Declaração de Conformidade da UE para este equipamento.

O Adaptador WiFi USB Netgear A6150 fornecido com este equipamento está em conformidade com a Diretiva 2014/53/UE e também pode ser fornecido a pedido.

> ![Ícone de aviso 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) Este é um produto de classe A. Num ambiente doméstico, este produto pode causar interferência sonora, caso em que o utilizador pode ser obrigado a tomar as medidas adequadas.

Eliminação de resíduos de pilhas e equipamentos elétricos e eletrónicos:

![Ícone de aviso 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Este símbolo no produto ou nas pilhas ou na sua embalagem significa que este produto e quaisquer pilhas que contenha não devem ser eliminados com o lixo doméstico. Em vez disso, é da sua responsabilidade entregá-lo a um ponto de recolha aplicável para a reciclagem de baterias e equipamentos elétricos e eletrónicos. Esta recolha e reciclagem separadas ajudarão a conservar os recursos naturais e a prevenir potenciais consequências negativas para a saúde humana e para o ambiente, devido à possível presença de substâncias perigosas nas baterias e nos equipamentos elétricos e eletrónicos, que poderiam ser causadas por uma eliminação inadequada. Para mais informações sobre onde deixar as suas baterias e resíduos elétricos e eletrónicos, contacte o seu escritório local de cidade/município, o seu serviço de eliminação de resíduos domésticos ou a loja onde adquiriu este produto. Contato erecycle@microsoft.com para informações adicionais sobre o WEEE.

Este produto contém pilhas de moedas(ies).

O Adaptador WiFi USB Netgear A6150 fornecido com este equipamento destina-se a ser operado perto do corpo humano e é testado para a conformidade da Taxa de Absorção Específica (SAR) usada pelo corpo (ver valores abaixo). Ao transportar o produto ou usá-lo durante o uso do seu corpo, mantenha uma distância de 10 mm do corpo para garantir o cumprimento dos requisitos de exposição ao RF.

**Taxa de absorção específica (SAR) de** 0,54 W/kg média superior a 10g de tecido

 
Este dispositivo pode funcionar em todos os Estados-Membros da UE. Respeite os regulamentos nacionais e locais em que o dispositivo é utilizado. Este dispositivo limita-se apenas à utilização no interior quando funciona na gama de frequências 5150-5350 MHz nos seguintes países:  

![Países da UE que exigem apenas uso interno](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

Em conformidade com os n.os 8 do artigo 10.8 e 10.8 da RED, o quadro seguinte fornece informações sobre as bandas de frequência utilizadas e a potência máxima de transmissão de RF dos produtos sem fios Netgear para venda na UE:

**Wi-Fi**

| Gama de frequências (MHz) | Canais utilizados | Potência de transmissão max (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400-2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) com TPC <br> 19,9 dBm (97,7 mW) não-TPC |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) com TPC <br> 29,6 dBm (490 mW) não-TPC |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Número de telefone: +353 1 295 3826

Número de fax: +353 1 706 4110

#### <a name="singapore"></a>Singapura:

O Adaptador Usb Netgear A6150 fornecido com este equipamento está em conformidade com as normas IMDA.


## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implantar O Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md)
