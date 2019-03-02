---
title: Inteligência e mapa de alerta de segurança no Centro de segurança do Azure de ameaças | Documentos da Microsoft
description: Saiba como utilizar o alerta mapa e threat intelligence capacidade de segurança no Centro de segurança do Azure para identificar potenciais ameaças nas suas VMs e computadores.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: f1e238890b777e2816648ec407e2581f636d1c12
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57239832"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Informações sobre ameaças e mapa de alertas de segurança
Este artigo ajuda-o a utilizar o mapa de alertas de segurança do Centro de segurança do Azure e a segurança mapa das informações sobre ameaças baseada em eventos para resolver problemas relacionados à segurança.

## <a name="how-the-security-alerts-map-works"></a>Como os alertas de segurança o mapeamento funciona
Centro de segurança fornece um mapa que o ajuda a identificar ameaças de segurança contra o ambiente. Por exemplo, pode identificar se um determinado computador faz parte de um botnet e, em que a ameaça é proveniente de. Computadores podem se tornar nós de um botnet quando os atacantes instalam ilicitamente malware que secretamente interage com o comando e controlo que gerem o botnet. 

Para criar esse mapa, o Centro de segurança utiliza dados provenientes de várias origens na Microsoft. Centro de segurança utiliza estes dados para mapear a potenciais ameaças contra o seu ambiente. 

Um dos passos de um [processo de resposta a incidentes de segurança](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) é identificar a gravidade dos sistemas comprometidos. Nesta fase, deve realizar as tarefas seguintes:

- Determinar a natureza do ataque.
- Determine o ponto de origem do ataque.
- Determinar a intenção do ataque. O ataque foi direcionado à sua organização, para adquirir informações específicas, ou foi aleatório?
- Identificar os sistemas que foram comprometidos.
- Identificar os ficheiros que foram acedidos e determinar a sensibilidade desses ficheiros.

Pode utilizar o mapa de alertas de segurança no Centro de segurança para ajudar com estas tarefas.

## <a name="access-the-security-alerts-map"></a>O mapa de alertas de segurança de acesso
Para visualizar as ameaças atuais no seu ambiente, abra o mapa de alertas de segurança:

1. Abra o dashboard **Centro de Segurança**.
2. No painel esquerdo, sob **proteção contra ameaças** selecionar **mapa de alertas de segurança**. É aberto o mapa.
3. Para obter mais informações sobre o alerta e receber passos de remediação, clique no ponto de alerta no mapa e siga as instruções. 
 
O mapa de alertas de segurança baseia-se em alertas. Estes alertas são baseados em atividades de rede à qual comunicação foi associada um endereço IP que foi resolvido com êxito, se é ou não o endereço IP é um endereço IP duvidoso conhecido (por exemplo, um cryptominer conhecido) ou um endereço IP que não é reconhecido anteriormente como perigoso. O map fornece alertas em quaisquer subscrições que selecionou anteriormente no Azure. 

Os alertas no mapa são exibidos de acordo com a localização geográfica onde eles são detetados como sendo proveniente e são codificadas por gravidade. 
    ![Informações sobre ameaças](./media/security-center-threat-intel/security-center-alert-map.png)

## <a name="viewing-the-event-based-threat-intelligence-dashboard"></a>Visualizar o dashboard de informações sobre ameaças com base em eventos
Para ver o mapa de inteligência de ameaças com base em eventos de segurança bruto, pode seguir este procedimento. Este mapa mostra apenas os eventos que envolvem um endereço IP que é considerado o risco, por exemplo um endereço IP de um botnet conhecido.

1. Abra o dashboard **Centro de Segurança**.

1. No painel esquerdo, sob **proteção contra ameaças** selecionar **mapa de alertas de segurança**. É aberto o mapa.
2. No canto superior direito, clique em **vá para o mapa de eventos de segurança**.
3. Selecione a área de trabalho para o qual pretende ver o dashboard.
4. Na parte superior do mapa, selecione **ver informações sobre ameaças clássico**. O **informações sobre ameaças** dashboard abre.

   > [!NOTE]
   > Se a coluna mais à direita mostrar **ATUALIZAR PLANO**, esta área de trabalho está a utilizar a subscrição gratuita. Atualize para o plano Standard para utilizar esta funcionalidade. Se a coluna da direita mostra **precisa de ATUALIZAÇÃO**, atualizar [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para utilizar esta funcionalidade. Para obter mais informações sobre o plano de preços, leia o artigo sobre os preços do Centro de Segurança do Azure.
   >
5. Se tiver mais do que uma área de trabalho para investigar, priorize a investigação de acordo com a coluna **IP malicioso**. Mostra o número atual de IPs maliciosos nesta área de trabalho. Selecione a área de trabalho que pretende utilizar e o dashboard **Informações sobre ameaças** é apresentado.

    ![Dados de informações sobre ameaças](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

6. O dashboard está dividido em quatro mosaicos:

    a.  **Tipos de ameaça**. Resume o tipo de ameaças que foram detetadas na área de trabalho selecionada.

    b.  **País de origem**. Agrega a quantidade de tráfego de acordo com a localização de origem.

    c.  **Localização da ameaça**. Ajuda-o a identificar as localizações atuais em todo o mundo que comunicam com o seu ambiente. No mapa apresentado, as setas laranja (recebido) e vermelhas (enviado) identificam as direções de tráfego. Se selecionar uma destas setas, é apresentado o tipo de ameaça e a direção de tráfego.

    d.  **Detalhes da ameaça**. Mostra mais detalhes sobre a ameaça que selecionou no mapa.

Independentemente do mosaico de opção que selecionar, o dashboard que é apresentado baseia-se na consulta de Pesquisa de Registos. A única diferença é o tipo de consulta e o resultado.

### <a name="threat-types"></a>Tipos de ameaças
Selecione o mosaico **Tipos de ameaça** para abrir o dashboard **Pesquisa de Registos**. As opções de filtro aparecem no lado esquerdo e os resultados da consulta aparecem no lado direito.

![Pesquisa de Registos](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

O resultado da consulta mostra as ameaças por nome. Pode utilizar o painel da esquerda para selecionar o atributo que pretende filtrar. Por exemplo, para ver apenas as ameaças que estão atualmente ligadas às máquinas, em **SESSIONSTATE**, selecione **Ligado** > **Aplicar**.

![Estado da Sessão](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Relativamente às VMs do Azure, só são apresentados no dashboard **Informações sobre ameaças** os dados da rede que fluem através do agente. As informações sobre ameaças também utilizam os tipos de dados seguintes:

- Dados CEF (Type=CommonSecurityLog)
- WireData (Type= WireData)
- Registos do IIS (Type=W3CIISLog)
- Firewall do Windows (Type=WindowsFirewall)
- Eventos de DNS (Type=DnsEvents)


## <a name="see-also"></a>Consulte também
Neste artigo, aprendeu a utilizar as informações sobre ameaças no Centro de Segurança para ajudar a identificar atividade suspeita. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de resolução de problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre respostas para as perguntas mais frequentes sobre o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
