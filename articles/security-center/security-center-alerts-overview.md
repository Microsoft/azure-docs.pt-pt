---
title: Alertas de segurança na central de segurança do Azure | Microsoft Docs
description: Este tópico explica o que são os alertas de segurança e os diferentes tipos disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 3b4b02574c028822d25d841376b127a718243b2e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202574"
---
# <a name="security-alerts-in-azure-security-center"></a>Alertas de segurança no Centro de Segurança do Azure

Na central de segurança do Azure, há uma variedade de alertas para vários tipos de recursos diferentes. A central de segurança gera alertas para recursos implantados no Azure e também para recursos implantados em ambientes de nuvem híbrida e locais.

As deteções avançadas estão disponíveis no Escalão Standard do Centro de Segurança do Azure. Está disponível uma avaliação gratuita. Pode atualizar a partir da seleção do Escalão de Preço na [Política de Segurança](security-center-pricing.md). Visite a [página do Centro de Segurança](https://azure.microsoft.com/pricing/details/security-center/) para saber mais sobre os preços.

## Respondendo às ameaças <a name="respond-threats"></a> atuais

Nos últimos 20 anos, ocorreram alterações significativas no campo das ameaças. No passado, geralmente, as empresas só tinham de se preocupar com a desfiguração do site por parte de atacantes individuais que estavam interessados, sobretudo, em ver “o que podiam fazer”. Os atacantes de hoje são muito mais sofisticados e organizados. Têm, muitas vezes, objetivos financeiros e estratégicos específicos. Também têm mais recursos disponíveis, uma vez que podem ser financiados por Estados ou pelo crime organizado.

Essas realidades em constante mudança levaram a um nível incomparável de profissionalismo nas classificações de invasor. Já não estão interessados na desfiguração dos sites. Agora eles estão interessados em roubar informações, contas financeiras e dados privados – todos eles podem usar para gerar dinheiro no mercado aberto ou para aproveitar uma posição empresarial, política ou militar específica. Ainda mais preocupante do que os atacantes com objetivos financeiros, temos os atacantes que entram ilegalmente em redes de forma a prejudicar infraestruturas e pessoas.

Como resposta, as organizações implementam frequentemente diversas soluções de pontos, as quais se concentram na defesa do perímetro ou dos pontos finais da empresa ao procurar a assinatura de ataques conhecidos. Estas soluções têm tendência a gerar um grande volume de alertas de baixa fidelidade, o que requer que um analista de segurança faça uma triagem e investigue. A maioria das organizações não tem o tempo e os conhecimentos necessários para responder a estes alertas e muitos deles não são sequer abordados.  

Além disso, os invasores evoluíram seus métodos para subverter muitas defesas baseadas em assinatura e [adaptar-se a ambientes de nuvem](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). São necessárias novas abordagem para identificar ameaças emergentes mais rapidamente e agilizar a deteção e a resposta.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?

Alertas são as notificações que a central de segurança gera quando detecta ameaças em seus recursos. A central de segurança prioriza e lista os alertas, juntamente com as informações necessárias para que você investigue rapidamente o problema. A central de segurança também fornece recomendações sobre como você pode corrigir um ataque.

## Como a central de segurança detecta ameaças? <a name="detect-threats"> </a>

Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Devido à presença global da Microsoft na nuvem e no local, eles têm acesso a um conjunto extenso de telemetria. A coleção abrangente e diversificada de conjuntos de valores permite a descoberta de novos padrões de ataque e tendências em seus produtos de consumidores e empresas locais, bem como seu serviços online. Como resultado, o Centro de Segurança pode atualizar rapidamente os respetivos algoritmos de deteção à medida que os atacantes disponibilizam novas e cada vez mais sofisticadas explorações. Esta abordagem ajuda-o a estar a par do ritmo dinâmico dos ambientes de ameaças.

Para detectar ameaças reais e reduzir falsos positivos, a central de segurança coleta, analisa e integra dados de log de seus recursos do Azure e da rede. Ele também funciona com soluções de parceiros conectadas, como firewall e soluções de proteção de ponto de extremidade. A central de segurança analisa essas informações, geralmente correlacionando informações de várias fontes, para identificar ameaças.

![Recolha e apresentação de dados do Centro de Segurança](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Os avanços nas tecnologias de macrodados e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são aproveitados para avaliar eventos em toda a nuvem, o que permite a deteção de ameaças que seriam impossíveis de identificar através da utilização de abordagens manuais e a previsão da evolução de ataques. Estas análises de segurança incluem:

* **Inteligência de ameaças integrada**: Procura atores ruins conhecidos aproveitando a inteligência contra ameaças globais dos produtos e serviços da Microsoft, da DCU (unidade de crimes digitais da Microsoft), do Microsoft Security Response Center (MSRC) e de feeds externos.
* **Análise comportamental**: Aplica padrões conhecidos para descobrir comportamentos mal-intencionados.
* **Detecção**de anomalias: Usa a criação de perfil estatística para criar uma linha de base histórica. Alerta sobre os desvios de linhas de base estabelecidos em conformidade com um potencial vetor de ataque.

Os tópicos a seguir abordam cada uma dessas análises com mais detalhes.

### <a name="integrated-threat-intelligence"></a>Inteligência de ameaças integrada

A Microsoft tem uma grande quantidade de informações sobre ameaças globais. A telemetria provém de várias origens, tal como o Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Unidade de Crimes Digitais (DCU) da Microsoft e Microsoft Security Response Center (MSRC). Os investigadores também recebem informações sobre ameaças que são partilhadas entre os grandes fornecedores de serviços em nuvem e subscrevem feeds de informações sobre ameaças de terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise comportamental é uma técnica que analisa e compara os dados para uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos. Também são determinados através de uma análise cuidada de comportamentos maliciosos pelos analistas especialistas. O Centro de Segurança do Azure pode utilizar a análise comportamental para identificar recursos comprometidos com base na análise dos registos das máquinas virtuais, dos registos dos dispositivos de rede virtual, dos registos de recursos de infraestrutura, das informações de falhas do sistema e de outras origens.

Além disso, existe uma correlação com outros sinais para procurar provas de uma campanha ampla. Esta correlação ajuda a identificar eventos que são consistentes com indicadores estabelecidos de comprometimento. 

### <a name="anomaly-detection"></a>Deteção de anomalias

O Centro de Segurança do Azure utiliza também a deteção de anomalias para identificar ameaças. Contrariamente à análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais “personalizada” e concentra-se nas linhas de base específicas das suas implementações. O machine Learning é utilizado para determinar a atividade normal das suas implementações e, em seguida, são geradas regras para definir condições de valores atípicos que podem representar um evento de segurança.

## <a name="continuous-monitoring-and-assessments"></a>Monitoramento e avaliações contínuos

A central de segurança do Azure beneficia-se de ter equipes de pesquisa de segurança e ciência de dados em toda a Microsoft que monitoram continuamente as alterações no cenário de ameaças. Isto inclui as seguintes iniciativas:

* **Monitoramento de inteligência contra ameaças**: A inteligência contra ameaças inclui mecanismos, indicadores, implicações e conselhos acionáveis sobre ameaças emergentes ou existentes. Esta informação é partilhada na comunidade de segurança e a Microsoft monitoriza continuamente os feeds de informações sobre ameaças de origens internas e externas.
* **Compartilhamento de sinal**: As informações das equipes de segurança no amplo portfólio da Microsoft de serviços locais e de nuvem, servidores e dispositivos de ponto de extremidade de cliente são compartilhadas e analisadas.
* **Especialistas em segurança da Microsoft**: Envolvimento em andamento com equipes da Microsoft que trabalham em campos de segurança especializados, como análise forense e detecção de ataques da Web.
* **Ajuste de detecção**: Os algoritmos são executados em conjuntos de dados de clientes reais e pesquisadores de segurança funcionam com clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Estes esforços combinados culminam em novas e melhoradas deteções das quais pode beneficiar de imediato, sem que seja precisa qualquer ação da sua parte.

## Tipos <a name="security-alert-types"></a> de alertas de segurança

Os tópicos a seguir orientam você pelos diferentes alertas, de acordo com os tipos de recursos:

* [Alertas de VMs e servidores IaaS](security-center-alerts-iaas.md)
* [Alertas de computação nativos](security-center-alerts-compute.md)
* [Alertas de serviços de dados](security-center-alerts-data-services.md)

Os tópicos a seguir explicam como a central de segurança usa a telemetria diferente que ela coleta da integração com a infraestrutura do Azure, para aplicar camadas de proteção adicionais para recursos implantados no Azure:

* [Alertas da camada de serviço](security-center-alerts-service-layer.md)
* [Integração com os produtos de segurança do Azure](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>O que são incidentes de segurança?

Um incidente de segurança é uma coleção de alertas relacionados, em vez de listar cada alerta individualmente. A central de segurança usa a [correlação de alertas inteligentes na nuvem](security-center-alerts-cloud-smart.md) para correlacionar diferentes alertas e sinais de baixa fidelidade em incidentes de segurança.

Usando incidentes, a central de segurança fornece uma única exibição de uma campanha de ataque e todos os alertas relacionados. Essa exibição permite que você entenda rapidamente as ações tomadas pelo invasor e quais recursos foram afetados. Para obter mais informações, consulte [correlação de alertas inteligentes na nuvem](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre os diferentes tipos de alertas disponíveis na central de segurança. Para obter mais informações, consulte:

* [Guia de operações e planeamento do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [FAQ do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-faq)

