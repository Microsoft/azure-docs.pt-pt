---
title: Ligue os dados do evento de segurança do Windows ao Azure Sentinel| Microsoft Docs
description: Aprenda a utilizar o conector Eventos de Segurança para transmitir todos os eventos de segurança dos seus sistemas Windows para o seu espaço de trabalho Azure Sentinel.
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: bc75ee64174957ad6486146b4da6f8a66a2120e5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570554"
---
# <a name="connect-windows-security-events"></a>Ligar aos eventos de segurança do Windows 

O conector Security Events permite-lhe transmitir todos os eventos de segurança dos seus sistemas Windows (servidores e estações de trabalho, físicas e virtuais) para o seu espaço de trabalho Azure Sentinel. Isto permite-lhe visualizar eventos de segurança do Windows nos seus dashboards, usá-los na criação de alertas personalizados e confiar neles para melhorar as suas investigações, dando-lhe mais informações sobre a rede da sua organização e expandindo as suas capacidades de operações de segurança. Pode selecionar quais os eventos a transmitir entre os seguintes conjuntos: <a name="event-sets"></a>

- **Todos os eventos** - Todos os eventos de segurança windows e AppLocker.
- **Comum** - Um conjunto padrão de eventos para fins de auditoria. Neste conjunto está incluído um rasto completo de auditoria do utilizador. Por exemplo, contém eventos de inscrição do utilizador e de inscrição do utilizador (IDs de evento 4624, 4634). Existem também ações de auditoria, tais como alterações de grupos de segurança, operações de controlador de domínio chave Kerberos e outros tipos de eventos em conformidade com as melhores práticas aceites.

    O conjunto de eventos **comuns** pode conter alguns tipos de eventos que não são tão comuns.  Isto porque o principal ponto do conjunto **comum** é reduzir o volume de eventos para um nível mais manejável, mantendo ao mesmo tempo a capacidade total de fuga à auditoria.

- **Mínimo** - Um pequeno conjunto de eventos que podem indicar potenciais ameaças. Este conjunto não contém um rasto de auditoria completo. Abrange apenas eventos que possam indicar uma violação bem sucedida, e outros eventos importantes que têm taxas de ocorrência muito baixas. Por exemplo, contém logons de utilizador bem sucedidos e falhados (IDs de evento 4624, 4625), mas não contém informações de sinalização (4634) que, embora importantes para a auditoria, não têm significado para a deteção de violações e têm um volume relativamente elevado. A maior parte do volume de dados deste conjunto é composta por eventos de inscrição e eventos de criação de processos (ID 4688 do evento).

- **Nenhum** - Sem eventos de segurança ou AppLocker. (Esta definição é utilizada para desativar o conector.)

    A lista a seguir fornece uma desagregação completa dos IDs de eventos de Segurança e App Locker para cada conjunto:

    | Conjunto de eventos | IDs de eventos recolhidos |
    | --- | --- |
    | **Mínimo** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Common** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> A recolha de Eventos de Segurança no contexto de um único espaço de trabalho pode ser configurada a partir do Azure Security Center ou Azure Sentinel, mas não ambos. Se estiver a bordo do Azure Sentinel num espaço de trabalho que já está a receber alertas do Azure Defender do Azure Security Center, e está pronto para recolher Eventos de Segurança, tem duas opções:
> - Deixe a coleção de Eventos de Segurança no Centro de Segurança Azure como está. Poderá consultar e analisar estes eventos em Azure Sentinel, bem como no Azure Defender. No entanto, não será capaz de monitorizar o estado de conectividade do conector ou alterar a sua configuração no Azure Sentinel. Se isto é importante para si, considere a segunda opção.
>
> - [Desativar a coleção de Eventos](../security-center/security-center-enable-data-collection.md) de Segurança no Azure Security Center e só depois adicionar o conector eventos de segurança em Azure Sentinel. Tal como na primeira opção, poderá consultar e analisar eventos tanto no Azure Sentinel como no Azure Defender/ASC, mas agora poderá monitorizar o estado de conectividade do conector ou alterar a sua configuração em - e apenas em - Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Configurar o conector de eventos de segurança do Windows

Para recolher os seus eventos de segurança windows em Azure Sentinel:

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**. A partir da lista de conectores, clique em **Eventos de Segurança** e, em seguida, no botão de página do **conector Open** no direito inferior. Em seguida, siga as instruções no ecrã no **separador Instruções,** conforme descrito no resto desta secção.

1. Verifique se tem as permissões apropriadas, tal como descritas na secção **Pré-Requisitos** na página do conector.

1. Descarregue e instale o [agente Log Analytics](../azure-monitor/agents/log-analytics-agent.md) (também conhecido como Agente de Monitorização da Microsoft ou MMA) nas máquinas para as quais pretende transmitir eventos de segurança para o Azure Sentinel.

    Para máquinas virtuais Azure:
    
    1. Clique em **Instalar o agente na Máquina Virtual Azure Windows** e, em seguida, no link que aparece abaixo.
    1. Para cada máquina virtual que pretende ligar, clique no seu nome na lista que aparece à direita e, em seguida, clique em **Connect**.

    Para máquinas windows não-Azure (física, virtual on-prem ou virtual em outra nuvem):

    1. Clique em **Instalar o agente na Máquina do Windows não-Azure** e, em seguida, no link que aparece abaixo.
    1. Clique nos links de descarregamento apropriados que aparecem à direita, em **Windows Computers**.
    1. Utilizando o ficheiro executável descarregado, instale o agente nos sistemas Windows à sua escolha e configuure-o utilizando o **ID e as Chaves do Espaço de Trabalho** que aparecem abaixo dos links de descarregamento acima mencionados.

    > [!NOTE]
    >
    > Para permitir que os sistemas Windows sem a necessária conectividade de internet ainda transmitam eventos para o Azure Sentinel, descarregue e instale o **OMS Gateway** numa máquina separada, utilizando o link no direito inferior, para funcionar como um proxy.  Ainda terá de instalar o agente Log Analytics em cada sistema Windows cujos eventos pretende recolher.
    >
    > Para obter mais informações sobre este cenário, consulte a documentação do [ **gateway Log Analytics**](../azure-monitor/agents/gateway.md).

    Para obter opções de instalação adicionais e mais detalhes, consulte a documentação do [ **agente Log Analytics**](../azure-monitor/agents/agent-windows.md).

1. Selecione qual o conjunto de eventos[(All, Common ou Minimal)](#event-sets)que pretende transmitir.

1. Clique em **Atualizar**.

1. Para utilizar o esquema relevante no Log Analytics para eventos de segurança do Windows, `SecurityEvent` digite na janela de consulta.

## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar cerca de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>Configure o conector de eventos de segurança para deteção de login RDP anómalo

> [!IMPORTANT]
> A deteção de login RDP anómala está atualmente em visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel pode aplicar machine learning (ML) a dados de eventos de segurança para identificar atividade de login anómala do Protocolo remoto de Ambiente de Trabalho (RDP). Os cenários incluem:

- **IP incomum** - o endereço IP raramente ou nunca foi observado nos últimos 30 dias

- **Geolocalização incomum** - o endereço IP, cidade, país e ASN raramente foram observados nos últimos 30 dias

- **Novo utilizador** - um novo utilizador inicia sessão a partir de um endereço IP e geolocalização, ambos ou qualquer um dos quais não era esperado ser visto com base em dados dos 30 dias anteriores.

**Instruções de configuração**

1. Deve estar a recolher dados de login RDP (Evento ID 4624) através do conector de **dados de eventos de Segurança.** Certifique-se de que selecionou um [conjunto de eventos](#event-sets) para além de "Nenhum" para transmitir para o Azure Sentinel.

1. A partir do portal Azure Sentinel, clique em **Analytics** e, em seguida, clique no **separador 'Modelos'.** Escolha a regra **de deteção de início de Sessão RDP (pré-visualização) anómala** e mova o slider **de estado** para **Ativado**.

    > [!NOTE]
    > Como o algoritmo de aprendizagem automática requer 30 dias de dados para construir um perfil de base do comportamento do utilizador, deve permitir que 30 dias de dados de eventos de Segurança sejam recolhidos antes de qualquer incidente ser detetado.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar eventos de segurança do Windows ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa a detetar ameaças com o Azure Sentinel, utilizando regras [incorporadas](tutorial-detect-threats-built-in.md) ou [personalizadas.](tutorial-detect-threats-custom.md)

