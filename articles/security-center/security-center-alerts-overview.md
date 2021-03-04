---
title: Alertas de segurança e incidentes no Centro de Segurança Azure
description: Saiba como o Azure Security Center gera alertas de segurança e os correlaciona em incidentes.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: memildin
ms.openlocfilehash: 5824457a9f2ce411a871b5e76802ecf2e2f106c3
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099510"
---
# <a name="security-alerts-and-incidents-in-azure-security-center"></a>Alertas de segurança e incidentes no Centro de Segurança Azure

O Security Center gera alertas para os recursos implantados no seu Azure, no local e em ambientes de nuvem híbrida.

Os alertas de segurança são desencadeados por deteções avançadas e estão disponíveis apenas com o Azure Defender. Pode atualizar a partir da página de **definições & de preços,** conforme descrito no [Quickstart: Enable Azure Defender](enable-azure-defender.md). Está disponível um julgamento gratuito de 30 dias. Para obter detalhes sobre preços na sua moeda de eleição e de acordo com a sua região, consulte [os preços do Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>O que são alertas de segurança e incidentes de segurança? 

**Os alertas** são as notificações que o Centro de Segurança gera quando deteta ameaças nos seus recursos. O Centro de Segurança dá prioridade e lista os alertas, juntamente com as informações necessárias para que investigue rapidamente o problema. O Centro de Segurança também fornece recomendações sobre como pode remediar um ataque.

**Um incidente** de segurança é uma coleção de alertas relacionados, em vez de listar cada alerta individualmente. O Security Center usa [a correlação de alerta inteligente Cloud](#cloud-smart-alert-correlation-in-azure-security-center-incidents) para correlacionar diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

Usando incidentes, o Security Center fornece-lhe uma única visão de uma campanha de ataque e todos os alertas relacionados. Esta visão permite-lhe entender rapidamente que ações o agressor tomou e que recursos foram afetados.

## <a name="respond-to-todays-threats"></a>Responda às ameaças de hoje

Nos últimos 20 anos, ocorreram alterações significativas no campo das ameaças. No passado, as empresas normalmente só tinham de se preocupar com o desfiguramento do site por parte de agressores individuais que estavam maioritariamente interessados em ver "o que podiam fazer". Os agressores de hoje são muito mais sofisticados e organizados. Têm, muitas vezes, objetivos financeiros e estratégicos específicos. Também têm mais recursos à sua disposição, uma vez que podem ser financiados por estados-nação ou pelo crime organizado.

Estas realidades em mudança levaram a um nível de profissionalismo sem precedentes nas fileiras dos agressores. Já não estão interessados na desfiguração dos sites. Estão agora interessados em roubar informação, contas financeiras e dados privados – todos eles podem usar para gerar dinheiro no mercado aberto ou para alavancar uma determinada posição comercial, política ou militar. Ainda mais preocupante do que os atacantes com objetivos financeiros, temos os atacantes que entram ilegalmente em redes de forma a prejudicar infraestruturas e pessoas.

Como resposta, as organizações implementam frequentemente diversas soluções de pontos, as quais se concentram na defesa do perímetro ou dos pontos finais da empresa ao procurar a assinatura de ataques conhecidos. Estas soluções têm tendência a gerar um grande volume de alertas de baixa fidelidade, o que requer que um analista de segurança faça uma triagem e investigue. A maioria das organizações não tem o tempo e os conhecimentos necessários para responder a estes alertas e muitos deles não são sequer abordados.  

Além disso, os atacantes desenvolveram os seus métodos para subverter muitas defesas baseadas em assinaturas e [adaptar-se a ambientes em nuvem.](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/) São necessárias novas abordagem para identificar ameaças emergentes mais rapidamente e agilizar a deteção e a resposta.


## <a name="continuous-monitoring-and-assessments"></a>Monitorização e avaliação contínuas

O Azure Security Center beneficia de ter equipas de pesquisa de segurança e de ciência de dados em toda a Microsoft que monitorizam continuamente as mudanças no panorama das ameaças. Isto inclui as seguintes iniciativas:

* **Monitorização de informações por ameaça**: A inteligência de ameaças inclui mecanismos, indicadores, implicações e conselhos acccídeos sobre ameaças existentes ou emergentes. Esta informação é partilhada na comunidade de segurança e a Microsoft monitoriza continuamente os feeds de informações sobre ameaças de origens internas e externas.
* **Partilha de sinais**: São partilhadas e analisadas informações das equipas de segurança através do vasto portfólio de serviços de cloud e no local, servidores e dispositivos de ponto final do cliente.
* **Especialistas de segurança da Microsoft**: existe um envolvimento contínuo com equipas da Microsoft que trabalham em campos de segurança especializados, tal como a deteção de ataques da Web e forense.
* **Otimização da deteção**: os algoritmos são executados em conjuntos de dados de clientes reais e os investigadores de segurança trabalham com os clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Estes esforços combinados culminam em novas e melhoradas deteções, das quais podes beneficiar instantaneamente – não há nenhuma ação a tomar.

## <a name="how-does-security-center-detect-threats"></a>Como é que o Centro de Segurança deteta ameaças? <a name="detect-threats"> </a>

Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Devido à nossa presença global na nuvem e no local, temos acesso a um conjunto expansivo de telemetria. A ampla e diversificada recolha de conjuntos de dados permite-nos descobrir novos padrões e tendências de ataque em todos os nossos produtos de consumo e empresa no local, bem como os nossos serviços online. Como resultado, o Centro de Segurança pode atualizar rapidamente os respetivos algoritmos de deteção à medida que os atacantes disponibilizam novas e cada vez mais sofisticadas explorações. Esta abordagem ajuda-o a estar a par do ritmo dinâmico dos ambientes de ameaças.

Para detetar ameaças reais e reduzir falsos positivos, o Security Center recolhe, analisa e integra dados de registo dos seus recursos Azure e da rede. Também trabalha com soluções parceiras conectadas, como soluções de firewall e proteção de pontos finais. O Centro de Segurança analisa esta informação, muitas vezes correlacionando informações de várias fontes, para identificar ameaças.

![Recolha e apresentação de dados do Centro de Segurança](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Os avanços nas tecnologias de macrodados e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são aproveitados para avaliar eventos em toda a nuvem, o que permite a deteção de ameaças que seriam impossíveis de identificar através da utilização de abordagens manuais e a previsão da evolução de ataques. Estas análises de segurança incluem:

- **Inteligência integrada de ameaças**: A Microsoft tem uma quantidade imensa de inteligência global de ameaças. A telemetria flui de várias fontes, tais como Azure, Microsoft 365, Microsoft CRM on-line, Microsoft Dynamics AX, outlook.com, MSN.com, a Microsoft Digital Crimes Unit (DCU) e o Microsoft Security Response Center (MSRC). Os investigadores também recebem informações de inteligência de ameaça que são partilhadas entre os principais fornecedores de serviços de nuvem e feeds de outros terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas.

- **Análise comportamental**: A análise comportamental é uma técnica que analisa e compara dados a uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos. Também são determinados através de uma análise cuidada de comportamentos maliciosos pelos analistas especialistas. O Centro de Segurança do Azure pode utilizar a análise comportamental para identificar recursos comprometidos com base na análise de registos de máquina virtual, registos dos dispositivos de rede virtual, registos de recursos de infraestrutura e outras origens.

- **Deteção de anomalias**: O Centro de Segurança Azure também utiliza deteção de anomalias para identificar ameaças. Em contraste com a análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais "personalizada" e foca-se em linhas de base específicas para as suas implementações. O machine Learning é utilizado para determinar a atividade normal das suas implementações e, em seguida, são geradas regras para definir condições de valores atípicos que podem representar um evento de segurança.

## <a name="how-are-alerts-classified"></a>Como são os alertas confidenciais?

O Centro de Segurança atribui uma gravidade aos alertas, para o ajudar a priorizar a ordem em que atende a cada alerta, para que quando um recurso estiver comprometido, possa chegar imediatamente a ele. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

> [!NOTE]
> A severidade de alerta é exibida de forma diferente no portal e versões da API REST que antecedem o 01-01-2019. Se estiver a utilizar uma versão mais antiga da API, atualize a experiência consistente descrita abaixo.

| Gravidade          | Resposta recomendada                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Alto**          | Há uma grande probabilidade de o seu recurso estar comprometido. Devia investigar imediatamente. O Centro de Segurança tem alta confiança tanto na intenção maliciosa como nas descobertas usadas para emitir o alerta. Por exemplo, um alerta que deteta a execução de uma ferramenta maliciosa conhecida como Mimikatz, uma ferramenta comum usada para roubo de credenciais.                                                                                                               |
| **Medium**        | Isto é provavelmente uma atividade suspeita pode indicar que um recurso está comprometido. A confiança do Centro de Segurança no analítico ou na descoberta é média e a confiança da intenção maliciosa é média a alta. Estes seriam geralmente aprendizagem automática ou deteções baseadas em anomalias. Por exemplo, uma tentativa de inscrição de um local anómalo.                                                                                                                |
| **Baixo**           | Isto pode ser um positivo benigno ou um ataque bloqueado. O Centro de Segurança não está confiante o suficiente para que a intenção seja maliciosa e a atividade possa ser inocente. Por exemplo, o log clear é uma ação que pode acontecer quando um intruso tenta esconder os seus rastos, mas em muitos casos é uma operação de rotina realizada por administradores. O Centro de Segurança não costuma dizer-te quando os ataques foram bloqueados, a não ser que seja um caso interessante que sugiramos que investigas. |
| **Informativo** | Só verá alertas informativos quando perfurar um incidente de segurança, ou se utilizar a API REST com um ID de alerta específico. Um incidente é normalmente composto por uma série de alertas, alguns dos quais podem aparecer por si sós como apenas informativos, mas no contexto dos outros alertas podem ser dignos de um olhar mais atento.                                                                                                                         |  |
|                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

## <a name="export-alerts"></a>Exportar alertas

Tem um leque de opções para visualizar os seus alertas fora do Centro de Segurança, incluindo:

- **O relatório CSV** sobre o painel de alertas fornece uma exportação única para o CSV.
- **A exportação contínua** de definições de preços & permite-lhe configurar fluxos de alertas de segurança e recomendações para log analytics workspaces e Centros de Eventos. [Saiba mais sobre a exportação contínua](continuous-export.md)
- **O conector Azure Sentinel** transmite alertas de segurança do Centro de Segurança Azure para o Azure Sentinel. [Saiba mais sobre a ligação do Centro de Segurança Azure com o Azure Sentinel](../sentinel/connect-azure-security-center.md)

## <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlação de alerta inteligente em nuvem no Centro de Segurança Azure (incidentes)

O Azure Security Center analisa continuamente as cargas de trabalho em nuvem híbrida, utilizando análises avançadas e inteligência de ameaças para alertá-lo sobre atividades maliciosas.

A amplitude da cobertura de ameaças está a aumentar. A necessidade de detetar até mesmo o menor compromisso é importante, e pode ser um desafio para os analistas de segurança triagem dos diferentes alertas e identificar um ataque real. O Centro de Segurança ajuda os analistas a lidar com esta fadiga de alerta. Ajuda a diagnosticar ataques à medida que ocorrem, correlacionando diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

A análise de fusão é a tecnologia e o back end analítico que alimenta os incidentes do Security Center, permitindo-lhe correlacionar diferentes alertas e sinais contextuais em conjunto. A fusão analisa os diferentes sinais relatados numa subscrição através dos recursos. A fusão encontra padrões que revelam progressão de ataque ou sinais com informações contextuais partilhadas, indicando que deve utilizar um procedimento de resposta unificado para eles.

A análise de fusão combina o conhecimento do domínio de segurança com a IA para analisar alertas, descobrindo novos padrões de ataque à medida que ocorrem. 

O Centro de Segurança aproveita a Matriz de Ataque MITRE para associar alertas com a sua perceção de intenção, ajudando a formalizar o conhecimento do domínio de segurança. Além disso, usando a informação recolhida para cada passo de um ataque, o Centro de Segurança pode excluir atividade que parece ser passos de um ataque, mas na verdade não é.

Como os ataques ocorrem frequentemente em diferentes inquilinos, o Security Center pode combinar algoritmos de IA para analisar sequências de ataque que são reportadas em cada subscrição. Esta técnica identifica as sequências de ataque como padrões de alerta predominantes, em vez de serem acidentalmente associadas umas às outras.

Durante uma investigação de um incidente, os analistas muitas vezes precisam de um contexto extra para chegar a um veredicto sobre a natureza da ameaça e como atenuá-la. Por exemplo, mesmo quando uma anomalia de rede é detetada, sem entender o que mais está acontecendo na rede ou no que diz respeito ao recurso direcionado, é difícil entender que ações tomar a seguir. Para ajudar, um incidente de segurança pode incluir artefactos, eventos relacionados e informações. As informações adicionais disponíveis para incidentes de segurança variam, dependendo do tipo de ameaça detetada e da configuração do seu ambiente. 

> [!TIP]
> Para obter uma lista de alertas de incidentes de segurança que podem ser produzidos pela análise de fusão, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Screenshot do incidente de segurança detetado relatório":::

Para gerir os seus incidentes de segurança, consulte [como gerir incidentes de segurança no Centro de Segurança Azure.](security-center-incident.md)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre os diferentes tipos de alertas disponíveis no Centro de Segurança. Para obter mais informações, consulte:

- [Alertas de Segurança em Registo de Atividades Azure](https://go.microsoft.com/fwlink/?linkid=2114113) - Além de estarem disponíveis no portal Azure ou programáticamente, alertas de segurança e incidentes são auditados como eventos no Diário de Atividades do Azure
- [Tabela de referência dos alertas do Azure Defender](alerts-reference.md)
- [Responder a alertas de segurança](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)