---
title: Ligar a dados de eventos de segurança do Windows para a pré-visualização de sentinela do Azure | Documentos da Microsoft
description: Saiba como ligar a dados de eventos de segurança do Windows para sentinela do Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 36d38aa82b4f0ec8d7d9ef6ebb1145b1fcc334df
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190571"
---
# <a name="connect-windows-security-events"></a>Ligar aos eventos de segurança do Windows 

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir em fluxo todos os eventos de segurança de servidores do Windows ligado à sua área de trabalho de sentinela do Azure. Esta ligação permite-lhe ver os dashboards e criar alertas personalizados e a melhorar a investigação. Isso dá-lhe mais informações sobre a rede da sua organização e melhora as capacidades de operação de segurança.  Pode selecionar os eventos para transmitir em fluxo:

- **Todos os eventos** -segurança de todos os Windows e eventos do AppLocker.
- **Comuns** -um conjunto padrão de eventos para fins de auditoria. Uma trilha de auditoria de utilizador completo está incluída neste conjunto. Por exemplo, este conjunto contém inícios de sessão do utilizador e de fim de sessão do utilizador (evento ID 4634). Podemos incluir a auditoria de ações como alterações do grupo de segurança, operações de Kerberos do controlador de domínio de chaves e outros eventos que são recomendados por organizações do setor.

Eventos com um volume muito baixa foram incluídos em comum definir como a principal motivação para escolher ao longo de todos os eventos é reduzir o volume e não para filtrar os eventos específicos.
- **Mínimo** -um pequeno conjunto de eventos que possam indicar a potenciais ameaças. Ao ativar esta opção, não será possível ter um registo de auditoria completo.  Este conjunto aborda apenas os eventos que podem indicar uma violação com êxito e eventos importantes que têm um volume muito baixo. Por exemplo, este conjunto contém utilizador login bem e mal-sucedidas (evento IDs 4624, 4625), mas ele não contém a fim de sessão que é importante para auditoria, mas não significativo para deteção e tem relativamente elevado volume. A maioria do volume de dados deste conjunto é os eventos de início de sessão e o evento de criação de processo (evento ID 4688).
- **Nenhum** -sem segurança ou eventos do AppLocker.

> [!NOTE]
> 
> - Os dados serão armazenados na localização geográfica da área de trabalho no qual está a executar sentinela do Azure.

A lista seguinte fornece uma repartição completa de segurança e do App Locker do ID de evento para cada conjunto:

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

Para integrar totalmente os eventos de segurança do Windows com o Azure sentinela:

1. No portal do Azure sentinela, selecione **conectores de dados** e, em seguida, clique nas **eventos de segurança do Windows** mosaico. 
1. Selecione os tipos de dados que pretende transmitir em fluxo.
1. Clique em **Atualizar**.
6. Para utilizar o esquema relevante no Log Analytics para os eventos de segurança do Windows, procure **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar cerca de 20 minutos até que os seus registos começam a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar eventos de segurança do Windows para sentinela do Azure. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).

