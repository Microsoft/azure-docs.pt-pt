---
title: Guia de segurança Azure Stack Edge Pro R / Microsoft Docs
description: Descreve convenções de segurança, diretrizes, considerações e explica como instalar e operar com segurança o seu dispositivo Azure Stack Edge Pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: ac3f2cc1c68ea552b2858d932217a28055fee0fd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467578"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Instruções de segurança Azure Stack Edge Pro R

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ Leia o ícone do aviso de segurança ](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
 **LEIA INFORMAÇÕES DE SEGURANÇA E SAÚDE**

Leia todas as informações de segurança deste artigo antes de utilizar o seu dispositivo Azure Stack Edge Pro R. O não cumprimento das instruções pode resultar em incêndio, choque elétrico, ferimentos ou danos nas suas propriedades. Leia todas as informações de segurança abaixo antes de utilizar o Azure Stack Edge Pro R.

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança

As seguintes palavras de sinal para sinais de alerta de perigo são:

| Ícone | Descrição |
|:--- |:--- |
| ![Símbolo de perigo](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **PERIGO:** Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos graves. <br> **ADVERTÊNCIA:** Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos graves. <br> **ATENÇÃO:** Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos ligeiros ou moderados.|
|

Os seguintes ícones de perigo devem ser observados ao configurar e executar o seu dispositivo Azure Stack Edge Pro R Edge:

| Ícone | Descrição |
|:--- |:--- |
| ![Leia todas as instruções primeiro](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | Leia todas as instruções primeiro |
| ![Símbolo de perigo](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | Símbolo de perigo |
| ![Ícone de perigo de ponta](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | Risco de ponta|
| ![Ícone de peso pesado](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | Risco de peso pesado|
| ![Ícone de choque elétrico](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | Risco de choque elétrico |
| ![Sem ícone de peças de serviço do utilizador](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | Sem peças de serviço para o utilizador. Não aceda a menos que esteja devidamente treinado. |
| ![Ícone de várias fontes de energia](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | Múltiplas fontes de energia. Desligue todos os cabos de alimentação para remover toda a energia do equipamento. |
| ![Ícone de pontos de beliscão](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | Pontos de beliscar estão presentes. |
| ![Ícone de componentes quentes ou superfícies](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | Indica componentes quentes ou superfícies. |
|

## <a name="handling-precautions-and-site-selection"></a>Precauções de manuseamento e seleção do local

![Aviso ícone ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **AVISO:**

* Equipamento adequado (por exemplo, tomada de paletes) e equipamento de proteção individual (EPI), por exemplo, as luvas devem ser utilizadas ao mover e manusear o dispositivo como enviado.

![Aviso ícone ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ de perigo de ponta de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **AVISO:**

* Coloque o equipamento sobre uma superfície plana, dura e estável para evitar um potencial perigo de ponta ou de esmagamento.
* Não empilhar mais de 2 dispositivos uns nos outros.
* Certifique-se de que o sistema está seguro antes do empilhamento.
* Não realojar nem mover dispositivos empilhados.
* Não empilhe dispositivos que estejam energizados com cabos externos.
* Certifique-se de que os cabos de alimentação não são esmagados ou danificados durante as operações de empilhamento.
* Os dispositivos não devem ser utilizados como mesas ou espaços de trabalho.

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ Ícone de choque elétrico Sem ícone ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ de peças de serviço do utilizador ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **PRECAUÇÃO:**

* Inspecione o dispositivo *recebido* para verificar se há danos. Se a caixa do dispositivo estiver danificada, contacte o [Microsoft Support](azure-stack-edge-contact-microsoft-support.md) para obter uma substituição. Não tente operar o aparelho.
* Se suspeitar que o dispositivo está avariado, contacte o [Microsoft Support](azure-stack-edge-contact-microsoft-support.md) para obter uma substituição. Não tente fazer o serviço.
* O aparelho não contém peças que possam ser ressoáveis pelo utilizador. Tensão perigosa, corrente e níveis de energia estão presentes no interior. Não abra. Devolva o dispositivo à Microsoft para manutenção.

![Ícone ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ de aviso ícone pesado de peso ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **AVISO:**

* A remoção do módulo de alimentação da UPS expõe as peças energizadas dentro da UPS. Não introduza objetos estranhos no interior do compartimento do módulo de alimentação.
* Não tente levantar sozinho um dispositivo Azure Stack Edge Pro R Edge. Um recinto pode pesar entre 52 kg e 93 kg (115 lbs e 205 lbs); Utilize assistência mecânica ou outra assistência adequada ao deslocar e levantar equipamento. Em conformidade com os requisitos locais de saúde e segurança no trabalho quando se desloca e levanta o equipamento.
* Não tente levantar o equipamento sem uma ajuda mecânica adequada. Tenha em atenção que tentar levantar este peso pode causar ferimentos graves.

![Aviso ícone ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **AVISO:**  

* O sistema foi concebido para funcionar num ambiente controlado. Escolha um site que seja:
  * Bem ventilado e longe de fontes de calor, incluindo luz solar direta e radiadores.
  * Não exposto à humidade ou à chuva.
  * Localizado num espaço que minimiza a vibração e o choque físico.  O sistema foi concebido para choques e vibrações de acordo com o MIL-STD-810G.
  * Isolados de campos eletromagnéticos fortes produzidos por dispositivos elétricos.
  * Provido de tomadas devidamente aterradas.
  * Dotamento de espaço adequado para aceder aos cabos de alimentação, porque servem como a principal desconexão de energia do produto.
* Instale o equipamento numa área de trabalho que permite uma circulação de ar adequada em torno do equipamento; Certifique-se de que as tampas dianteiras e traseiras estão completamente removidas enquanto o aparelho está em funcionamento.
* Instale o equipamento em zona controlada a temperatura, sem contaminantes condutores e permita uma circulação adequada do ar em torno do equipamento.
* Mantenha o equipamento afastado de fontes de ambientes líquidos e excessivamente húmidos.
* Não permita que nenhum objeto líquido ou estranho entre no sistema. Não coloque bebidas ou outros recipientes líquidos sobre ou perto do sistema.

## <a name="heater-precautions"></a>Precauções do aquecedor

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ Componentes quentes ou ícone de superfícies ícone 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:** 

* O funcionamento automático do aquecedor enquanto o sistema é ligado pode criar um risco de toque devido às altas temperaturas de superfície na tampa do conjunto do aquecedor. Não toque nesta superfície enquanto o sistema estiver ligado. Deixe arrefecer um período de arrefecimento de 10 minutos após o sistema ter sido desligado.

![Pontos de aperto do ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ Ícone 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:** 

* Quando o sistema é ligado, a atuação automática da porta do plenum traseiro pode criar um risco de ponto de aperto. Mantenha as mãos afastadas desta área quando o sistema estiver ligado.

## <a name="electrical-precautions"></a>Precauções elétricas

![Ícone de ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ aviso ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Fornecer uma ligação de terra elétrica segura ao cabo de alimentação. O cabo de corrente alternada (AC) tem uma ficha de ligação à terra de três fios (uma ficha que tem um pino de ligação à terra). Esta ficha serve apenas a uma tomada de AR à terra. Não derrote o propósito do pino de ligação à terra.
* Dado que a ficha do cabo de alimentação é o dispositivo principal de desconexão, certifique-se de que as tomadas estão localizadas perto do dispositivo e são facilmente acessíveis.
* Desligue o cabo de alimentação (puxando a ficha, não o cabo) e desligando todos os cabos se existirem algumas das seguintes condições:

  * O cabo de alimentação ou a ficha ficam desgastados ou danificados de outra forma.
  * Derrama algo no invólucro do dispositivo.
  * O aparelho está exposto à chuva ou ao excesso de humidade.
  * O dispositivo foi largado e o invólucro do dispositivo está danificado.
  * Suspeita que o dispositivo precisa de assistência ou reparação.
* Desligue permanentemente a ficha antes de a mover ou se pensa que ficou danificada de alguma forma.
* Fornecer uma fonte de alimentação adequada com proteção elétrica para sobrecargas para satisfazer as seguintes especificações de energia:

  * Voltagem: 100 a 240 Volts AC
  * Corrente: 20 A, máximo por cabo de alimentação. Os cabos de alimentação são fornecidos.
  * Frequência: 50 a 60 Hz

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ várias fontes de energia ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **AVISO:**

* Para sistemas sem alimentação elétrica ininterrupta (UPS), desligue todos os cabos de alimentação CA para remover completamente a energia CA do equipamento.
* Para sistemas com UPS, desligue todos os cabos de alimentação CA e utilize o interruptor de alimentação UPS para desenergizar o Sistema. A UPS contém tensões CA e DC perigosas.

![Ícone de ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ aviso ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Para os sistemas equipados com tensão UPS, AC e/ou DC envolverão sempre um risco potencial de tensão CA na saída UPS gerada a partir de baterias ou utilidade. Para evitar danos no equipamento ou ferimentos pessoais, assuma sempre que pode haver tensão na saída UPS, mesmo quando desligado da fonte de alimentação primária.
* Para sistemas equipados com UPS, todas as ligações externas energizadas da UPS são femininas. Não remova a caixa nem introduza nada nestes ou em qualquer conector na UPS.

![Ícone de ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ aviso ícone de choque elétrico ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **AVISO:**

* Não tente modificar ou utilizar cabos de alimentação CA que não os fornecidos com o equipamento.

![Ícone de aviso ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ Sem ícone de peças de serviço do utilizador ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **PRECAUÇÃO:**

* Este equipamento contém pilhas de moedas de lítio e/ou baterias de fosfato de ferro de lítio. Não tente fazer a manutenção do equipamento. As pilhas deste equipamento não são ressutilizáveis pelo utilizador. Risco de explosão se a bateria for substituída por um tipo incorreto.
* A remoção do módulo de bateria da UPS expõe as peças energizadas dentro da UPS. Não introduza objetos estranhos no interior do compartimento do módulo da bateria.

## <a name="regulatory-information"></a>Informação regulamentar

Esta secção contém informações regulamentares para o dispositivo Azure Stack Edge Pro R, número do modelo regulamentar: Azure Stack Edge Pro R .

O dispositivo Azure Stack Edge Pro R Edge foi concebido para ser utilizado com equipamentos de tecnologia de informação compatíveis com a NRTL (UL, CSA, ETL, etc.) e IEC/EN 60950-1 ou IEC/EN 62368-1 (marcado com CE) equipamentos de tecnologias de informação.

O aparelho foi concebido para funcionar nos seguintes ambientes:

| Ambiente | Especificações |
|:--- |:--- |
|Especificações de temperatura | <ul><li>Temperatura de armazenamento: -33 &deg; C-63 &deg; C (-28 &deg; F-145 &deg; F) </li><li>Funcionamento contínuo: 5 &deg; C-43 &deg; C (41 &deg; F-110 &deg; F)</li><li>Gradiente de temperatura máxima (funcionamento e armazenamento): 20 &deg; C/h (68 &deg; F/h)</li></ul> |
|Especificações relativas de humidade | <ul><li>Armazenamento: 5% a 95% RH com 33 &deg; C (91 &deg; F) ponto máximo de orvalho. A atmosfera deve estar sempre a não ser condensada.</li><li>Funcionamento: 5% a 85% humidade relativa com 29 &deg; C (84,2 &deg; F) ponto máximo de orvalho</li></ul> |
| Especificações máximas de altitude | <ul><li>Funcionamento (Sem UPS): 15.000 pés (4.572 metros)</li><li>Funcionamento (com UPS): 6.561 pés (2.000 metros)</li><li>Armazenamento: 40.000 pés (12.192 metros)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![Aviso de ícone ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **aviso:** &nbsp; Alterações ou modificações efetuadas no equipamento não expressamente aprovadas pela Microsoft podem anular a autoridade do utilizador para operar o equipamento.

CANADÁ e EUA:

AVISO: Este equipamento foi testado e encontra-se em conformidade com os limites para um dispositivo digital de classe A, nos termos da parte 15 das Regras da FCC. Estes limites destinam-se a proporcionar uma proteção razoável contra interferências nocivas quando o equipamento é operado num ambiente comercial. Este equipamento gera, utiliza e pode irradiar energia de radiofrequência e, se não for instalado e utilizado de acordo com o manual de instruções, pode causar interferências prejudiciais às comunicações de rádio. O funcionamento deste equipamento numa área residencial pode causar interferências prejudiciais, caso em que o utilizador será obrigado a corrigir a interferência às suas próprias custas.

Este dispositivo está em conformidade com a parte 15 das normas RSS isentas de licenças da FCC e da indústria do Canadá. O funcionamento está sujeito às duas condições seguintes: (1) este dispositivo não pode causar interferência prejudicial, e (2) este dispositivo deve aceitar qualquer interferência recebida, incluindo interferências que possam causar o funcionamento nãodesejável do dispositivo.

![Aviso de informação regulamentar 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, EUA.
Estados Unidos: (800) 426-9400 Canadá: (800) 933-4750

UNIÃO EUROPEIA: Solicitar uma cópia da Declaração de Conformidade da UE. 

![Ícone de aviso](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)

Este é um produto de classe A. Num ambiente doméstico, este produto pode causar interferência sonora, caso em que o utilizador pode ser obrigado a tomar as medidas adequadas.

Eliminação de resíduos de pilhas e equipamentos elétricos e eletrónicos:

![Ícone de aviso 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Este símbolo no produto ou nas pilhas ou na sua embalagem significa que este produto e quaisquer pilhas que contenha não devem ser eliminados com o lixo doméstico. Em vez disso, é da sua responsabilidade entregá-lo a um ponto de recolha aplicável para a reciclagem de baterias e equipamentos elétricos e eletrónicos. Esta recolha e reciclagem separadas ajudarão a conservar os recursos naturais e a prevenir potenciais consequências negativas para a saúde humana e para o ambiente, devido à possível presença de substâncias perigosas nas baterias e nos equipamentos elétricos e eletrónicos, que poderiam ser causadas por uma eliminação inadequada. Para mais informações sobre onde deixar as suas baterias e resíduos elétricos e eletrónicos, contacte o seu escritório local de cidade/município, o seu serviço de eliminação de resíduos domésticos ou a loja onde adquiriu este produto. Contato erecycle@microsoft.com para informações adicionais sobre o WEEE.

Este produto contém pilhas de moedas(ies).

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL Número de telefone: +353 1 295 3826 Número de fax: +353 1 706 4110



## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implementar Azure Stack Edge Pro R Edge](azure-stack-edge-pro-r-deploy-prep.md)
