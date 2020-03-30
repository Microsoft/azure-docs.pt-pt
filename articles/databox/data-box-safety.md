---
title: Segurança para a sua Caixa de Dados Azure [ Microsoft Docs
description: Descreve convenções de segurança, diretrizes e considerações, e explica como instalar e operar com segurança a sua Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/13/2019
ms.author: alkohli
ms.openlocfilehash: db6e2eadbf19d78a203a4d4ba6111ad88430b192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68985950"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Instale e opere com segurança a sua Caixa de Dados Azure
![Ícone](./media/data-box-safety/warning_icon.png)
![de aviso](./media/data-box-safety/read_safety_and_health_information_icon.png) Ler Ícone de Aviso de Segurança **LEIA INFORMAÇÕES DE SEGURANÇA E SAÚDE**

Leia todas as informações de segurança deste artigo antes de utilizar a Caixa de Dados Azure. O não cumprimento das instruções pode resultar em incêndio, choque elétrico ou outros ferimentos, ou danos nas suas propriedades.

## <a name="safety-icon-conventions"></a>Convenções de ícones de segurança
Aqui estão os ícones que encontrará quando rever as precauções de segurança a observar ao configurar e executar a sua Caixa de Dados.

| Ícone | Descrição |
|:--- |:--- |
| ![Perigo](./media/data-box-safety/warning_icon.png) **perigo PERIGO!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos graves. Esta palavra de sinal deve limitar-se às situações mais extremas. |
| ![Aviso](./media/data-box-safety/warning_icon.png) **Ícone AVISO!** |Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos graves. |
| ![Advertência](./media/data-box-safety/warning_icon.png) Ícone **ATENÇÃO!** |Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos ligeiros ou moderados. |
| ![Aviso](./media/data-box-safety/notice_icon.png) de ícone **de aviso:** |Indica informações consideradas importantes, mas não relacionadas com riscos. |
| ![](./media/data-box-safety/electrical_shock_hazard_icon.png) **Risco de choque elétrico** do ícone de choque elétrico |Alta tensão. |
| ![](./media/data-box-safety/heavy_weight_hazard_icon.png) **Peso pesado** ícone do peso pesado | |
| ![Sem ícone de](./media/data-box-safety/no_user_serviceable_parts_icon.png) peças reparaíveis ao **utilizador, sem peças reparaveis** ao utilizador |Não aceda a menos que esteja devidamente treinado. |
| ![Leia o](./media/data-box-safety/read_safety_and_health_information_icon.png) ícone do aviso de **segurança, leia todas as instruções primeiro** | |
| ![Perigo de](./media/data-box-safety/tip_hazard_icon.png) ponta hazard **ícone** | |

## <a name="handling-precautions"></a>Precauções de manuseamento

![Ícone](./media/data-box-safety/warning_icon.png) ![de choque](./media/data-box-safety/electrical_shock_hazard_icon.png)![elétrico do ícone](./media/data-box-safety/no_user_serviceable_parts_icon.png) do aviso, sem ícone de peças reparadoras **do utilizador, cuidado** 

* Inspecione o dispositivo *as-received* para obter danos. Se o recinto do dispositivo estiver danificado, [contacte o Microsoft Support](data-box-disk-contact-microsoft-support.md) para obter uma substituição. Não tente operar o aparelho. 
* O aparelho está equipado com parafusos à prova de adulteração. Se suspeitar que o dispositivo está avariado, [contacte](data-box-disk-contact-microsoft-support.md) o Microsoft Support para obter uma substituição. Não tente servir o dispositivo. 
* O dispositivo não contém peças que possam ser reparadas pelo utilizador. Tensão perigosa, corrente e níveis de energia estão presentes no interior. Não abra. Devolva o dispositivo à Microsoft para ser evindo.

![Aviso](./media/data-box-safety/warning_icon.png) ![Ícone de](./media/data-box-safety/heavy_weight_hazard_icon.png) peso pesado **ícone AVISO!** 

* Um recinto totalmente configurado pode pesar até 22,7 kg (50 lbs); não tente levantá-lo sozinho.
* Antes de deslocar o recinto, certifique-se sempre de que duas pessoas estão disponíveis para lidar com o peso. Esteja ciente de que uma pessoa que tenta levantar este peso pode sofrer ferimentos.


![Aviso](./media/data-box-safety/warning_icon.png) ![Ícone de](./media/data-box-safety/tip_hazard_icon.png) perigo de ponta do ícone **aTENÇÃO!**
* Coloque o aparelho sobre uma superfície plana, dura e estável para evitar um potencial perigo de ponta.
* O equipamento montado em rack não deve ser utilizado como prateleiras ou espaços de trabalho. Não coloque a Caixa de Dados em cima de equipamento montado em rack. A adição de qualquer tipo de carga a uma unidade montada na cremalheira estendida pode criar um potencial risco de ponta que pode levar a ferimentos, morte ou danos no produto.

![Aviso](./media/data-box-safety/warning_icon.png) **Ícone AVISO!**

* Instale o dispositivo numa área de trabalho que permita uma circulação de ar adequada em torno do dispositivo.
* Instale o aparelho numa área interior controlada pela temperatura, livre de contaminantes condutores e permita uma circulação de ar adequada à volta do dispositivo.
* Mantenha o dispositivo afastado de fontes de ambientes líquidos e excessivamente húmidos.


## <a name="electrical-precautions"></a>Precauções elétricas

![Aviso](./media/data-box-safety/warning_icon.png) ![Ícone](./media/data-box-safety/electrical_shock_hazard_icon.png) de choque elétrico **Ícone AVISO!**

* Forneça uma ligação elétrica segura à alimentação elétrica. O cabo CA tem uma ficha de ligação à terra de três fios (uma ficha que tem um pino de ligação à terra). Esta ficha serve apenas a uma tomada CA aterrada. Não derrote o propósito do pino de ligação à terra.
* Dado que a ficha do cabo de alimentação é o principal dispositivo de desconexão, certifique-se de que as tomadas estão localizadas perto do dispositivo e são facilmente acessíveis.
* Desligue o cabo de alimentação (puxando a ficha, não o cabo) e desligue todos os cabos se existirem alguma das seguintes condições:

    - O cabo de alimentação ou a ficha ficam desgastados ou danificados de outra forma.
    - Derrame algo no invólucro do dispositivo.
    - O aparelho está exposto a chuva ou excesso de humidade.
    - O dispositivo foi largado e o invólucro do dispositivo está danificado.
    - Suspeita que o dispositivo precisa de serviço ou reparação.
* Desligue permanentemente a unidade antes de a mover ou se achar que ficou danificada de alguma forma.
* Fornecer uma fonte de alimentação adequada com proteção de sobrecarga elétrica para satisfazer as seguintes especificações de energia:

    - Tensão: 100 V AC a 240 V AC
    - Corrente: 6 A, máximo
    - Frequência: 50 Hz a 60 Hz

![Advertência](./media/data-box-safety/warning_icon.png) Ícone **ATENÇÃO:**

* Este dispositivo contém pilhas de pilhas de moedas. Não tente servir o dispositivo. As pilhas neste dispositivo não são utilizáveis pelo utilizador. 
* **Apenas para pessoal de serviço**: Risco de explosão se a bateria for substituída por um tipo incorreto. Elimine as pilhas usadas de acordo com as instruções.

![Aviso](./media/data-box-safety/notice_icon.png) de ícone **de aviso:**

Para o bom funcionamento do seu dispositivo e para evitar danos no produto:

* Certifique-se de que as portas dianteiras e traseiras estão completamente abertas enquanto o dispositivo está em funcionamento.

## <a name="regulatory-information"></a>Informação regulamentar

Esta secção contém informações regulamentares para a Caixa de Dados Azure, modelo regulamentar db010.

Este dispositivo é:

- Avaliado como Equipamento de Tecnologia saem (ITE), projetado para funcionar num ambiente típico de sala de dados. A adequação deste produto para outros ambientes pode exigir uma avaliação mais aprofundada.
- Concebido para utilização com equipamento de tecnologia de informação NRTL Listado (UL, CSA, ETL, etc.) e IEC/EN 60950-1 ou IEC/EN 62368-1 compatível (marcado CE).
- Projetado para funcionar no ambiente seguinte. 
    - Temperatura de funcionamento: 50° a 95° F (10° a 35° C)
    - Temperatura de armazenamento: -4° a 122° F (-20° a 50° C)
    - Humidade relativa: 15% a 85% (não condensando) 
    - Altitude operacional: Testado até 6500 pés (0 metros a 2000 metros)

Para obter classificações de alimentação elétrica, consulte a etiqueta de classificação do dispositivo fornecida com a unidade. 

![Aviso](./media/data-box-safety/notice_icon.png) de ícone **de aviso:** 

Alterações ou modificações feitas no dispositivo não expressamente aprovados pela Microsoft podem anular a autoridade do utilizador para operar o dispositivo.

**CANADÁ e EUA:**

![Aviso](./media/data-box-safety/notice_icon.png) de ícone **de aviso:** 

Este equipamento foi testado e encontrado para cumprir os limites para um dispositivo digital classe A, nos termos da parte 15 das Regras da FCC. Estes limites destinam-se a proporcionar uma proteção razoável contra interferências nocivas quando o equipamento é operado num ambiente comercial. Este equipamento gera, utiliza e pode irradiar energia de radiofrequência e, se não for instalado e utilizado de acordo com o manual de instruções, pode causar interferências prejudiciais às comunicações de rádio. O funcionamento deste equipamento numa área residencial pode causar interferênciaprejudicial, caso em que o utilizador será obrigado a corrigir a interferência às suas próprias custas.

Este dispositivo cumpre a parte 15 das normas RSS isentas de licença s isenta de licença s da FCC E da Indústria do Canadá. O funcionamento está sujeito às duas seguintes condições: (1) este dispositivo não pode causar interferência prejudicial e (2) este dispositivo deve aceitar qualquer interferência recebida, incluindo interferências que possam causar o funcionamento indesejado do dispositivo.

![Canadá](./media/data-box-safety/canada.png)

PODE ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, EUA.

Estados Unidos: (800) 426-9400

Canadá: (800) 933-4750

**UNIÃO EUROPEIA:**

[Solicite uma cópia da Declaração de Conformidade da UE.](mailto:CSI_Compliance@microsoft.com)

![Aviso](./media/data-box-safety/warning_icon.png) **Ícone AVISO:** 

Este é um produto de classe A. Em ambiente doméstico, este produto pode causar interferência sonora, caso em que o utilizador pode ser obrigado a tomar as medidas adequadas.

**Eliminação de pilhas de resíduos e equipamentos elétricos e eletrónicos:**

![Ícone de eliminação de baterias](./media/data-box-safety/battery_disposal_icon.png)

Este símbolo no produto ou nas suas baterias ou na sua embalagem significa que este produto e as pilhas que contém não devem ser eliminados com os resíduos domésticos. Em vez disso, é da sua responsabilidade entregá-lo a um ponto de recolha aplicável para a reciclagem de baterias e equipamentos elétricos e eletrónicos. Esta recolha e reciclagem separadas ajudará a conservar os recursos naturais e prevenir potenciais consequências negativas para a saúde humana e para o ambiente, devido à possível presença de substâncias perigosas em baterias e elétricas e eletrónicas equipamento, que poderia ser causado por eliminação inadequada. Para mais informações sobre onde deixar as suas baterias e resíduos elétricos e eletrónicos, contacte o seu escritório local de localização/município, o serviço de eliminação de resíduos domésticos ou a loja onde adquiriu este produto. Contacte microsoft.com de *ereciclo\@* para obter informações adicionais sobre o WEEE.

Este produto contém baterias de pilhas de moedas(ies).

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Número de telefone: +353 1 295 3826

Número do fax: +353 1 706 4110 

**Japão**

![Japão](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

Depois de ter revisto estes avisos de segurança, pode configurar e cabo o seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

* [Cabo e ligue a sua Caixa de Dados](data-box-deploy-set-up.md)


