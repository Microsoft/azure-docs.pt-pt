---
title: Alertas de segurança no Centro de Segurança Azure Microsoft Docs
description: Este tópico explica quais são os alertas de segurança e os diferentes tipos disponíveis no Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: a77e75caf836c4f7d142b0b5c23327d73afa6a77
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744630"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertas de segurança no Centro de Segurança do Azure

No Azure Security Center, existem uma variedade de alertas para vários tipos de recursos diferentes. O Centro de Segurança gera alertas para os recursos implantados no Azure, bem como para os recursos implantados em ambientes de nuvem no local e híbridos.

Os alertas de segurança são desencadeados por deteções avançadas e estão disponíveis apenas no Nível Padrão do Centro de Segurança Azure. Está disponível uma avaliação gratuita. Pode atualizar a partir da seleção do Escalão de Preço na [Política de Segurança](security-center-pricing.md). Visite a [página do Centro de Segurança](https://azure.microsoft.com/pricing/details/security-center/) para saber mais sobre os preços.

## <a name="responding-to-todays-threats"></a>Respondendo às ameaças <a name="respond-threats"> </a> de hoje

Nos últimos 20 anos, ocorreram alterações significativas no campo das ameaças. No passado, as empresas só tinham de se preocupar com o desfiguramento do site por parte de agressores individuais que estavam maioritariamente interessados em ver "o que podiam fazer". Os agressores de hoje são muito mais sofisticados e organizados. Têm, muitas vezes, objetivos financeiros e estratégicos específicos. Também têm mais recursos disponíveis, uma vez que podem ser financiados por Estados ou pelo crime organizado.

Estas realidades em mudança levaram a um nível de profissionalismo sem precedentes nas fileiras dos agressores. Já não estão interessados na desfiguração dos sites. Estão agora interessados em roubar informação, contas financeiras e dados privados – tudo o que podem usar para gerar dinheiro no mercado aberto ou para alavancar uma determinada posição empresarial, política ou militar. Ainda mais preocupante do que os atacantes com objetivos financeiros, temos os atacantes que entram ilegalmente em redes de forma a prejudicar infraestruturas e pessoas.

Como resposta, as organizações implementam frequentemente diversas soluções de pontos, as quais se concentram na defesa do perímetro ou dos pontos finais da empresa ao procurar a assinatura de ataques conhecidos. Estas soluções têm tendência a gerar um grande volume de alertas de baixa fidelidade, o que requer que um analista de segurança faça uma triagem e investigue. A maioria das organizações não tem o tempo e os conhecimentos necessários para responder a estes alertas e muitos deles não são sequer abordados.  

Além disso, os atacantes desenvolveram os seus métodos para subverter muitas defesas baseadas em assinaturas e [adaptar-se a ambientes em nuvem.](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/) São necessárias novas abordagem para identificar ameaças emergentes mais rapidamente e agilizar a deteção e a resposta.

## <a name="what-are-security-alerts-and-security-incidents"></a>O que são alertas de segurança e incidentes de segurança? 

**Os alertas** são as notificações que o Centro de Segurança gera quando deteta ameaças nos seus recursos. O Centro de Segurança prioriza e lista os alertas, juntamente com as informações necessárias para que investigue rapidamente o problema. O Centro de Segurança também fornece recomendações sobre como pode remediar um ataque.

**Um incidente de segurança** é uma coleção de alertas relacionados, em vez de enumerar cada alerta individualmente. O Centro de Segurança usa a [Correlação](security-center-alerts-cloud-smart.md) de Alerta Inteligente cloud para correlacionar diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

Utilizando incidentes, o Security Center fornece-lhe uma única visão de uma campanha de ataque e de todos os alertas relacionados. Esta visão permite-lhe compreender rapidamente que ações o agressor tomou e que recursos foram afetados. Para mais informações, consulte cloud [smart alert correlation](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>Como é que o Centro de Segurança deteta ameaças? <a name="detect-threats"> </a>

Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Devido à presença global da Microsoft na nuvem e no local, eles têm acesso a um conjunto expansivo de telemetria. A ampla e diversificada recolha de conjuntos de dados permite a descoberta de novos padrões de ataque e tendências em todo o seu local de consumo e produtos empresariais, bem como os seus serviços online. Como resultado, o Centro de Segurança pode atualizar rapidamente os respetivos algoritmos de deteção à medida que os atacantes disponibilizam novas e cada vez mais sofisticadas explorações. Esta abordagem ajuda-o a estar a par do ritmo dinâmico dos ambientes de ameaças.

Para detetar ameaças reais e reduzir falsos positivos, o Security Center recolhe, analisa e integra dados de registo dos seus recursos Azure e da rede. Também trabalha com soluções de parceiros conectados, como soluções de proteção de firewall e endpoint. O Security Center analisa esta informação, muitas vezes correlacionado com informações de várias fontes, para identificar ameaças.

![Recolha e apresentação de dados do Centro de Segurança](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Os avanços nas tecnologias de macrodados e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são aproveitados para avaliar eventos em toda a nuvem, o que permite a deteção de ameaças que seriam impossíveis de identificar através da utilização de abordagens manuais e a previsão da evolução de ataques. Estas análises de segurança incluem:

* **Inteligência integrada**de ameaças : A Microsoft tem uma enorme quantidade de inteligência global de ameaças. A telemetria flui de várias fontes, tais como Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, a Microsoft Digital Crimes Unit (DCU) e o Microsoft Security Response Center (MSRC). Os investigadores também recebem informações de inteligência de ameaças que são partilhadas entre os principais fornecedores de serviços na nuvem e feeds de outros terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas.

* **Análise comportamental**: A análise comportamental é uma técnica que analisa e compara dados a uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos. Também são determinados através de uma análise cuidada de comportamentos maliciosos pelos analistas especialistas. O Centro de Segurança do Azure pode utilizar a análise comportamental para identificar recursos comprometidos com base na análise de registos de máquina virtual, registos dos dispositivos de rede virtual, registos de recursos de infraestrutura e outras origens.

* **Deteção de anomalias**: O Centro de Segurança Azure também utiliza a deteção de anomalias para identificar ameaças. Em contraste com a análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais "personalizada" e foca-se em linhas de base específicas das suas implementações. O machine Learning é utilizado para determinar a atividade normal das suas implementações e, em seguida, são geradas regras para definir condições de valores atípicos que podem representar um evento de segurança.

## <a name="how-are-alerts-classified"></a>Como são classificados os alertas?

O Centro de Segurança atribui uma severidade aos alertas, para ajudá-lo a priorizar a ordem em que atende a cada alerta, para que quando um recurso está comprometido, possa chegar lá imediatamente. A gravidade baseia-se na confiança do Centro de Segurança na descoberta ou na analítica usada para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta.

> [!NOTE]
> A gravidade do alerta é exibida de forma diferente no portal e versões da API REST que antecedem 01-01-2019. Se estiver a utilizar uma versão mais antiga da API, atualize para a experiência consistente descrita abaixo.

- **Alto:** Há uma grande probabilidade de o seu recurso estar comprometido. Devia sonhá-lo imediatamente. O Centro de Segurança tem alta confiança tanto na intenção maliciosa como nas descobertas usadas para emitir o alerta. Por exemplo, um alerta que deteta a execução de uma ferramenta maliciosa conhecida como Mimikatz, uma ferramenta comum usada para roubo de credenciais.
- **Médio:** Esta é provavelmente uma atividade suspeita pode indicar que um recurso está comprometido.
A confiança do Centro de Segurança na analítica ou na descoberta é média e a confiança da intenção maliciosa é média a alta. Normalmente, trata-se de aprendizagem automática ou deteções baseadas em anomalias. Por exemplo, uma tentativa de inscrição de um local anómalo.
- **Baixo:** Isto pode ser um ataque benigno positivo ou bloqueado.
   * O Centro de Segurança não está confiante o suficiente para que a intenção seja maliciosa e a atividade pode ser inocente. Por exemplo, o registo claro é uma ação que pode acontecer quando um intruso tenta esconder os seus rastos, mas em muitos casos é uma operação de rotina realizada por administradores.
   * O Centro de Segurança não costuma dizer-te quando os ataques foram bloqueados, a não ser que seja um caso interessante que sugerimos que procures. 
- **Informação:** Só verá alertas informais quando se ajusta a um incidente de segurança, ou se utilizar a API REST com um ID de alerta específico. Um incidente é tipicamente composto por uma série de alertas, alguns dos quais podem aparecer por si só como apenas informativos, mas no contexto dos outros alertas podem ser dignos de um olhar mais atento. 

## <a name="continuous-monitoring-and-assessments"></a>Monitorização e avaliações contínuas

O Azure Security Center beneficia de ter equipas de pesquisa de segurança e ciência de dados em toda a Microsoft que monitorizam continuamente as mudanças no panorama das ameaças. Isto inclui as seguintes iniciativas:

* **Monitorização da inteligência de ameaças**: A inteligência das ameaças inclui mecanismos, indicadores, implicações e conselhos atuais sobre ameaças existentes ou emergentes. Esta informação é partilhada na comunidade de segurança e a Microsoft monitoriza continuamente os feeds de informações sobre ameaças de origens internas e externas.
* **Partilha de sinais**: São partilhadas e analisadas as informações das equipas de segurança através do vasto portfólio de serviços, servidores e dispositivos de ponto final da Microsoft.
* **Especialistas de segurança da Microsoft**: existe um envolvimento contínuo com equipas da Microsoft que trabalham em campos de segurança especializados, tal como a deteção de ataques da Web e forense.
* **Otimização da deteção**: os algoritmos são executados em conjuntos de dados de clientes reais e os investigadores de segurança trabalham com os clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Estes esforços combinados culminam em novas e melhoradas deteções, das quais pode beneficiar instantaneamente – não há nenhuma ação a tomar.


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre os diferentes tipos de alertas disponíveis no Centro de Segurança. Para obter mais informações, consulte:

* [Proteção contra ameaças no Centro](threat-protection.md) de Segurança Azure - Para uma breve descrição das fontes dos alertas de segurança exibidos pelo Azure Security Center 
* **Alertas de segurança no Registo de Atividades do Azure** - Além de estarem disponíveis no portal Azure ou programáticamente, os alertas de segurança e incidentes são auditados como eventos no Registo de [Atividades do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view). Para mais informações sobre o esquema do evento, consulte Alertas de [Segurança no registo de Atividade seletiva do Azure](https://go.microsoft.com/fwlink/?linkid=2114113)

