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
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124971"
---
# <a name="connect-windows-security-events"></a>Ligar aos eventos de segurança do Windows 

O conector Security Events permite transmitir todos os eventos de segurança dos seus sistemas Windows (servidores e estações de trabalho, físicos e virtuais) para o seu espaço de trabalho Azure Sentinel. Isto permite-lhe visualizar eventos de segurança do Windows nos seus dashboards, usá-los na criação de alertas personalizados e confiar neles para melhorar as suas investigações, dando-lhe mais informações sobre a rede da sua organização e expandindo as suas capacidades de operações de segurança. Pode selecionar quais os eventos a transmitir entre os seguintes conjuntos:<a name="event-sets"></a>

- **Todos os eventos** - Todos os eventos de segurança do Windows e AppLocker.
- **Common** - Um conjunto padrão de eventos para fins de auditoria. Neste conjunto está incluído um rasto completo de auditoria do utilizador. Por exemplo, contém eventos de inscrição do utilizador e de inscrição do utilizador (iDs de evento 4624, 4634). Existem também ações de auditoria, tais como alterações de grupos de segurança, operações principais do controlador de domínio Kerberos e outros tipos de eventos em conformidade com as melhores práticas aceites.

    O conjunto de eventos **comuns** pode conter alguns tipos de eventos que não são tão comuns.  Isto porque o principal ponto do conjunto **comum** é reduzir o volume de eventos para um nível mais manejável, mantendo ainda a capacidade total de rasto de auditoria.

- **Mínimo** - Um pequeno conjunto de eventos que podem indicar potenciais ameaças. Este conjunto não contém um rasto de auditoria completo. Abrange apenas eventos que podem indicar uma violação bem sucedida, e outros eventos importantes que têm taxas de ocorrência muito baixas. Por exemplo, contém logons de utilizador bem sucedidos e falhados (iDs de evento 4624, 4625), mas não contém informações de inscrição (4634) que, embora importantes para a auditoria, não têm significado para a deteção de violações e têm um volume relativamente elevado. A maior parte do volume de dados deste conjunto é composta por eventos de inscrição e eventos de criação de processos (id do evento 4688).

- **Nenhum** - Sem eventos de segurança ou AppLocker. (Esta definição é utilizada para desativar o conector.)

    A lista seguinte fornece uma desagregação completa dos IDs do evento Security e App Locker para cada conjunto:

    | Conjunto de eventos | IDs de eventos recolhidos |
    | --- | --- |
    | **Mínimo** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Common** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> A recolha de Eventos de Segurança no contexto de um único espaço de trabalho pode ser configurada a partir do Azure Security Center ou do Azure Sentinel, mas não ambos. Se estiver a embarcar no Azure Sentinel num espaço de trabalho que já está a funcionar no Azure Security Center, e está pronto para recolher Eventos de Segurança, tem duas opções:
> - Deixe a coleção de Eventos de Segurança no Centro de Segurança Azure como está. Poderá consultar e analisar estes eventos no Azure Sentinel, bem como no Azure Security Center. No entanto, não poderá monitorizar o estado de conectividade do conector ou alterar a sua configuração no Azure Sentinel. Se isto é importante para si, considere a segunda opção.
>
> - [Desativar](../security-center/security-center-enable-data-collection.md) a recolha de Eventos de Segurança no Azure Security Center, e só depois adicione o conector de Eventos de Segurança em Azure Sentinel. Tal como na primeira opção, poderá consultar e analisar eventos tanto no Azure Sentinel como no Azure Security Center, mas agora poderá monitorizar o estado de conectividade do conector ou alterar a sua configuração em - e apenas em - Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Configurar o conector de Eventos de Segurança do Windows

Para recolher os seus eventos de segurança do Windows em Azure Sentinel:

1. A partir do menu de navegação Azure Sentinel, selecione **conectores de dados**. A partir da lista de conectores, clique em **Eventos**de Segurança e, em seguida, no botão de página do **conector Aberto** na parte inferior direita. Em seguida, siga as instruções no ecrã sob o separador **Instruções,** conforme descrito através do resto desta secção.

1. Verifique se tem as permissões adequadas conforme descrito nos **pré-requisitos**.

1. Descarregue e instale o [agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (também conhecido como Microsoft Monitoring Agent ou MMA) nas máquinas para as quais pretende transmitir eventos de segurança para o Azure Sentinel.

    Para máquinas virtuais Azure:
    
    1. Clique no **agente Instalar na Máquina Virtual Do Windows Azul**e, em seguida, no link que aparece abaixo.
    1. Para cada máquina virtual que pretende ligar, clique no seu nome na lista que aparece à direita e, em seguida, clique em **Connect**.

    Para máquinas Windows não-Azure (física, virtual on-prem ou virtual em outra nuvem):

    1. Clique no **agente Instalar na Máquina Windows não Azure**e, em seguida, no link que aparece abaixo.
    1. Clique nos links de descarregamento apropriados que aparecem à direita, em **Computadores Windows**.
    1. Utilizando o ficheiro executável descarregado, instale o agente nos sistemas Windows à sua escolha e configure-o utilizando o **ID e as Teclas do Espaço de Trabalho** que aparecem abaixo dos links de descarregamento acima mencionados.

    > [!NOTE]
    >
    > Para permitir que os sistemas Windows sem a conectividade de internet necessária susponsem ainda eventos para o Azure Sentinel, descarregue e instale o **Gateway OMS** numa máquina separada, utilizando o link no canto inferior direito, para agir como um proxy.  Ainda terá de instalar o agente Log Analytics em cada sistema Windows cujos eventos pretende recolher.
    >
    > Para obter mais informações sobre este cenário, consulte a documentação do [ **gateway Log Analytics** ](../azure-monitor/platform/gateway.md).

    Para opções adicionais de instalação e mais detalhes, consulte a documentação do [ **agente Log Analytics** ](../azure-monitor/platform/agent-windows.md).

1. Selecione qual o conjunto de eventos[(Tudo, Comum ou Mínimo)](#event-sets)que pretende transmitir.

1. Clique em **Atualizar**.

1. Para utilizar o esquema relevante no Log Analytics `SecurityEvent` para eventos de segurança do Windows, escreva na janela de consulta.

## <a name="validate-connectivity"></a>Validar a conectividade

Pode levar cerca de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar a detetar ameaças com o Azure Sentinel, usando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)

