---
title: Segurança para seu Azure Data Box | Microsoft Docs
description: Descreve as convenções de segurança, as diretrizes e as considerações e explica como instalar e operar seu Azure Data Box com segurança.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/13/2019
ms.author: alkohli
ms.openlocfilehash: db6e2eadbf19d78a203a4d4ba6111ad88430b192
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68985950"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Instalar e operar seu Azure Data Box com segurança
Ícone de ![Warning ](./media/data-box-safety/warning_icon.png)
 ícone de aviso de segurança ![Read ](./media/data-box-safety/read_safety_and_health_information_icon.png) **segurança de leitura e informações de integridade**

Leia todas as informações de segurança neste artigo antes de usar Azure Data Box. A falha em seguir as instruções pode resultar em incêndio, choque elétrico ou outros ferimentos ou danos às suas propriedades.

## <a name="safety-icon-conventions"></a>Convenções de ícone de segurança
Aqui estão os ícones que você encontrará ao revisar as precauções de segurança a serem observadas ao configurar e executar o Data Box.

| Cone | Descrição |
|:--- |:--- |
| Ícone de ![Danger ](./media/data-box-safety/warning_icon.png) **perigo!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos sério. Esta palavra de sinal é limitada às situações mais extremas. |
| Ícone de ![Warning ](./media/data-box-safety/warning_icon.png) **aviso!** |Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos grave. |
| ![Warning ícone ](./media/data-box-safety/warning_icon.png) **cuidado!** |Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos secundário ou moderado. |
| Ícone de ![Notice ](./media/data-box-safety/notice_icon.png) **AVISO:** |Indica informações consideradas importantes, mas não relacionadas a riscos. |
| Ícone de choque de ![Electrical ](./media/data-box-safety/electrical_shock_hazard_icon.png) **risco de choque elétrico** |Alta voltagem. |
| Ícone de peso ![Heavy ](./media/data-box-safety/heavy_weight_hazard_icon.png) **peso pesado** | |
| ![No ícone de partes com serviço de usuário ](./media/data-box-safety/no_user_serviceable_parts_icon.png) **nenhuma parte do usuário com serviço** |Não acesse, a menos que seja devidamente treinado. |
| Ícone de aviso de segurança ![Read ](./media/data-box-safety/read_safety_and_health_information_icon.png) **ler todas as instruções primeiro** | |
| Ícone de risco de ![Tip ](./media/data-box-safety/tip_hazard_icon.png) **risco de gorjeta** | |

## <a name="handling-precautions"></a>Precauções de manipulação

Ícone de ![Warning ](./media/data-box-safety/warning_icon.png) ![Electrical ícone de choque ](./media/data-box-safety/electrical_shock_hazard_icon.png)![No ícone de partes de serviço de usuário ](./media/data-box-safety/no_user_serviceable_parts_icon.png) **cuidado** 

* Inspecione o dispositivo *como recebido* para danos. Se o compartimento do dispositivo estiver danificado, [entre em contato com suporte da Microsoft](data-box-disk-contact-microsoft-support.md) para obter uma substituição. Não tente operar o dispositivo. 
* O dispositivo está equipado com parafusos à prova de adulteração. Se você suspeitar de que o dispositivo está com problemas de funcionamento, [entre em contato com suporte da Microsoft](data-box-disk-contact-microsoft-support.md) para obter uma substituição. Não tente atender ao dispositivo. 
* O dispositivo não contém nenhuma parte que seja atendida pelo usuário. A tensão perigosa, os níveis atuais e de energia estão presentes no. Não abra. Retorne o dispositivo para a Microsoft para manutenção.

Ícone de ![Warning ](./media/data-box-safety/warning_icon.png) ícone de peso ![Heavy ](./media/data-box-safety/heavy_weight_hazard_icon.png) **aviso!** 

* Um compartimento totalmente configurado pode pesar até 22,7 kg (50 lbs); Não tente fazê-lo por conta própria.
* Antes de mover o compartimento, sempre verifique se duas pessoas estão disponíveis para lidar com o peso. Lembre-se de que uma pessoa que está tentando levantar esse peso pode sustentar ferimentos.


Ícone de ![Warning ](./media/data-box-safety/warning_icon.png) ![Tip ícone de risco ](./media/data-box-safety/tip_hazard_icon.png) **aviso!**
* Coloque o dispositivo em uma superfície plana, sólida e estável para evitar um possível risco de gorjeta.
* Equipamento montado em rack não deve ser usado como prateleiras ou espaços de trabalho. Não coloque o Data Box sobre equipamentos montados em rack. Adicionar qualquer tipo de carga a uma unidade montada em rack estendido pode criar um possível risco de gorjeta que poderia levar a danos, morte ou dano ao produto.

Ícone de ![Warning ](./media/data-box-safety/warning_icon.png) **aviso!**

* Configure o dispositivo em uma área de trabalho que permita a circulação de ar adequada em todo o dispositivo.
* Instale o dispositivo em uma área de interno controlada por temperatura sem contaminants de conduta e permita a circulação de ar adequada em todo o dispositivo.
* Mantenha o dispositivo longe de fontes de ambientes líquidos e Humid excessivamente.


## <a name="electrical-precautions"></a>Precauções elétricas

Ícone de ![Warning ](./media/data-box-safety/warning_icon.png) ![Electrical ícone de choque ](./media/data-box-safety/electrical_shock_hazard_icon.png) **aviso!**

* Forneça uma conexão de terra elétrica segura para o cabo da fonte de alimentação. O cabo AC tem um plugue de aterramento de três fios (um plug que tem um pino de aterramento). Esse plugue se ajusta apenas a uma tomada de AC em aterramento. Não derrotar a finalidade do pino de aterramento.
* Considerando que o plug-in do cabo da fonte de energia é o dispositivo de desconexão principal, verifique se as saídas do soquete estão localizadas perto do dispositivo e estão facilmente acessíveis.
* Desconecte o cabo de alimentação (puxando o plug-in, não o cabo) e desconecte todos os cabos se alguma das seguintes condições existir:

    - O cabo de alimentação ou o plugue se torna desgastado ou danificado de outra forma.
    - Você despeja algo na embalagem do dispositivo.
    - O dispositivo é exposto à umidade Rain ou excedente.
    - O dispositivo foi descartado e a capitalização do dispositivo está danificada.
    - Você suspeita que o dispositivo precisa de reparo ou serviço.
* Desconecte permanentemente a unidade antes de movê-la ou se você acreditar que ela se tornou danificada de alguma forma.
* Forneça uma fonte de energia adequada com proteção contra sobrecarga elétrica para atender às seguintes especificações de energia:

    - Voltagem: 100 V AC a 240 V AC
    - Atual: 6 A, máximo
    - Frequência: 50 Hz a 60 Hz

Ícone de ![Warning ](./media/data-box-safety/warning_icon.png) **cuidado:**

* Este dispositivo contém baterias de células de moeda. Não tente atender ao dispositivo. As baterias neste dispositivo não são atendidas pelo usuário. 
* **Somente para a equipe de serviço**: risco de explosão se a bateria for substituída por um tipo incorreto. Descarte as baterias usadas de acordo com as instruções.

Ícone de ![Notice ](./media/data-box-safety/notice_icon.png) **AVISO:**

Para a operação adequada do seu dispositivo e para evitar danos ao produto:

* Verifique se as portas frontal e traseira estão totalmente abertas enquanto o dispositivo está em execução.

## <a name="regulatory-information"></a>Informações regulatórias

Esta seção contém informações regulatórias para Azure Data Box, o número do modelo normativo DB010.

Este dispositivo é:

- Avaliado como equipamento de tecnologia da informação (ITE), projetado para operar em um ambiente de sala de dados típico. A adequação deste produto para outros ambientes pode exigir mais avaliação.
- Projetado para uso com NRTL listados (UL, CSA, ETL, etc.) e IEC/EN 60950-1 ou IEC/EN 62368-1 em conformidade com o equipamento de tecnologia da informação (CE marcado).
- Projetado para operar no ambiente a seguir. 
    - Temperatura operacional: 50 ° a 95 ° F (10 ° a 35 ° C)
    - Temperatura de armazenamento:-4 ° a 122 ° F (-20 ° a 50 ° C)
    - Umidade relativa: 15% a 85% (sem condensação) 
    - Altitude operacional: testada até 6500 pés (0 metros a 2000 metros)

Para as classificações de fornecimento elétrico, consulte o rótulo de classificação do dispositivo fornecido com a unidade. 

Ícone de ![Notice ](./media/data-box-safety/notice_icon.png) **AVISO:** 

Alterações ou modificações feitas no dispositivo não expressamente aprovadas pela Microsoft podem anular a autoridade do usuário para operar o dispositivo.

**Canadá e EUA:**

Ícone de ![Notice ](./media/data-box-safety/notice_icon.png) **AVISO:** 

Este equipamento foi testado e foi considerado compatível com os limites de um dispositivo digital de classe a, de acordo com a parte 15 das regras de FCC. Esses limites foram projetados para fornecer proteção razoável contra interferência prejudicial quando o equipamento é operado em um ambiente comercial. Este equipamento gera, usa e pode radiar a energia da frequência de rádio e, se não estiver instalado e usado de acordo com o manual de instruções, poderá causar interferência prejudicial às comunicações de rádio. A operação deste equipamento em uma área residencial provavelmente causará interferência prejudicial, caso em que o usuário será solicitado a corrigir a interferência por sua própria despesa.

Este dispositivo está em conformidade com a parte 15 das regras de FCC e padrões RSS isentos de licenças do setor (s). A operação está sujeita às duas condições a seguir: (1) este dispositivo pode não causar interferência prejudicial e (2) este dispositivo deve aceitar qualquer interferência recebida, incluindo a interferência que pode causar a operação indesejada do dispositivo.

![Canadá](./media/data-box-safety/canada.png)

PODE ICES-3 (A)/NMB-3 (A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, EUA.

Estados Unidos: (800) 426-9400

Canadá: (800) 933-4750

**UNIÃO EUROPEIA:**

[Solicite uma cópia da declaração de conformidade da UE](mailto:CSI_Compliance@microsoft.com).

Ícone de ![Warning ](./media/data-box-safety/warning_icon.png) **AVISO:** 

Este é um produto de classe A. Em um ambiente doméstico, esse produto pode causar interferência de rádio, caso o usuário possa ser solicitado a tomar medidas adequadas.

**Eliminação de baterias de resíduos e equipamentos eletrônicos e elétricos:**

![Ícone de disposição da bateria](./media/data-box-safety/battery_disposal_icon.png)

Esse símbolo no produto ou em suas baterias ou em seu empacotamento significa que este produto e todas as baterias que ele contém não devem ser descartados com seu desperdício doméstico. Em vez disso, é sua responsabilidade entregar isso a um ponto de coleta aplicável para a reciclagem de baterias e equipamentos eletrônicos e elétricos. Essa coleta e reciclagem separadas ajudarão a conservar recursos naturais e a evitar possíveis conseqüências negativas para a saúde humana e o ambiente devido à possível presença de substâncias perigosas em baterias e elétricas e eletrônicas equipamento, que pode ser causado por descarte inadequado. Para obter mais informações sobre onde soltar suas baterias e resíduos elétricos e eletrônicos, entre em contato com seu escritório Municipal/município local, seu serviço de descarte de resíduos domésticos ou a loja em que você comprou este produto. Contate a *erecycle \@microsoft. com* para obter informações adicionais sobre a WEEE.

Este produto contém bateria de célula de moeda (s).

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Número de telefone: + 353 1 295 3826

Número de fax: + 353 1 706 4110 

**Japão**

![Japão](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

Depois de revisar esses avisos de segurança, você pode configurar e conectar seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

* [Cabo e conecte seu Data Box](data-box-deploy-set-up.md)


