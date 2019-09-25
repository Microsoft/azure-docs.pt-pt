---
title: Conectar dados de eventos de segurança do Windows ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados de eventos de segurança do Windows ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 34762d561edfa5b37b1841c55f3fff6a4b8337a3
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240509"
---
# <a name="connect-windows-security-events"></a>Ligar aos eventos de segurança do Windows 



Você pode transmitir todos os eventos de segurança dos servidores Windows conectados ao seu espaço de trabalho do Azure Sentinel. Essa conexão permite que você exiba painéis, crie alertas personalizados e melhore a investigação. Isso lhe dá mais informações sobre a rede da sua organização e aprimora seus recursos de operação de segurança.  Você pode selecionar quais eventos transmitir:

- **Todos os eventos** -todos os eventos de segurança e AppLocker do Windows.
- **Comum** -um conjunto padrão de eventos para fins de auditoria. Uma trilha de auditoria de usuário completa está incluída neste conjunto. Por exemplo, esse conjunto contém os eventos de entrada do usuário e saída do usuário (ID do evento 4634). Podemos incluir a auditoria de ações como alterações do grupo de segurança, operações de Kerberos do controlador de domínio de chaves e outros eventos que são recomendados por organizações do setor.

Eventos com um volume muito baixa foram incluídos em comum definir como a principal motivação para escolher ao longo de todos os eventos é reduzir o volume e não para filtrar os eventos específicos.
- **Mínimo** -um pequeno conjunto de eventos que pode indicar possíveis ameaças. Ao habilitar essa opção, você não poderá ter uma trilha de auditoria completa.  Esse conjunto abrange apenas os eventos que podem indicar uma violação bem-sucedida e eventos importantes que têm um volume muito baixo. Por exemplo, esse conjunto contém logon bem-sucedido e com falha do usuário (IDs de evento 4624, 4625), mas não contém informações de saída que são importantes para auditoria, mas não são significativas para detecção e tem um volume relativamente alto. A maior parte do volume de dados desse conjunto é os eventos de entrada e o evento de criação de processo (ID do evento 4688).
- **Nenhum** -nenhum evento de segurança ou AppLocker.

> [!NOTE]
> 
> - Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.
> - Se a central de segurança do Azure e o Azure Sentinel estiverem em execução no mesmo espaço de trabalho, o conector de eventos de segurança só poderá ser conectado da central de segurança do Azure ou do Azure Sentinel. Para gerenciar esses eventos do Azure Sentinel, é recomendável desconectá-lo da central de segurança do Azure e conectá-lo somente ao Azure Sentinel.


A lista a seguir fornece uma análise completa das IDs de evento de segurança e do armário de aplicativo para cada conjunto:

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

Para integrar totalmente seus eventos de segurança do Windows com o Azure sentinela:

1. No portal do Azure Sentinel, selecione **conectores de dados** e clique no bloco **eventos de segurança do Windows** . 
1. Selecione os tipos de dados que você deseja transmitir.
1. Clique em **Atualizar**.
6. Para usar o esquema relevante no Log Analytics para os eventos de segurança do Windows, procure **SecurityEvent**.

## <a name="validate-connectivity"></a>Validar conectividade

Pode levar cerca de 20 minutos até que os logs comecem a aparecer na Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

