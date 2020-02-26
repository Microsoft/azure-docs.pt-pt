---
title: Ligue os dados do evento de segurança do Windows ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados do evento de segurança do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 70190eeb3adec239d1e52f51afcd173497d08e6a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588013"
---
# <a name="connect-windows-security-events"></a>Ligar aos eventos de segurança do Windows 



Pode transmitir todos os eventos de segurança a partir dos Servidores Windows ligados ao seu espaço de trabalho Azure Sentinel. Esta ligação permite-lhe visualizar dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança.  Pode selecionar quais eventos transmitir:

- **Todos os eventos** - Todos os eventos de segurança do Windows e AppLocker.
- **Common** - Um conjunto padrão de eventos para fins de auditoria. Neste conjunto está incluído um rasto completo de auditoria do utilizador. Por exemplo, este conjunto contém o utilizador e o utilizador assina mato de eventos (id do evento 4634). Podemos incluir a auditoria de ações como alterações do grupo de segurança, operações de Kerberos do controlador de domínio de chaves e outros eventos que são recomendados por organizações do setor.

Eventos com um volume muito baixa foram incluídos em comum definir como a principal motivação para escolher ao longo de todos os eventos é reduzir o volume e não para filtrar os eventos específicos.
- **Mínimo** - Um pequeno conjunto de eventos que podem indicar potenciais ameaças. Ao permitir esta opção, não poderá ter um rasto de auditoria completo.  Este conjunto abrange apenas eventos que podem indicar uma violação bem sucedida e eventos importantes que têm um volume muito baixo. Por exemplo, este conjunto contém login bem sucedido e falhado do utilizador (evento IDs 4624, 4625), mas não contém informações de sinalização que são importantes para a auditoria, mas não significativas para a deteção e tem um volume relativamente elevado. A maior parte do volume de dados deste conjunto é o sinal em eventos e evento de criação de processos (evento ID 4688).
- **Nenhum** - Sem eventos de segurança ou AppLocker.

> [!NOTE]
> 
> - Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.
> - Se o Azure Security Center e o Azure Sentinel estiverem a funcionar no mesmo espaço de trabalho, o conector de Eventos de Segurança só pode ser ligado a partir do Azure Security Center ou do Azure Sentinel. Para gerir estes eventos a partir do Azure Sentinel, recomendamos que o desligue do Azure Security Center e o ligue apenas ao Azure Sentinel.


A lista seguinte fornece uma desagregação completa dos IDs do evento Security e App Locker para cada conjunto:

| Camada de dados | Indicadores de eventos recolhidos |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Configurar o conector de eventos de segurança do Windows

Para integrar totalmente os seus eventos de segurança do Windows com o Azure Sentinel:

1. No portal Azure Sentinel, selecione **conectores de Dados** e, em seguida, clique no azulejo Eventos de **Segurança.** 
1. Selecione quais os tipos de dados que pretende transmitir.
1. Clique em **Atualizar**.
6. Para utilizar o esquema relevante no Log Analytics para os eventos de segurança do Windows, procure **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar cerca de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

