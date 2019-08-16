---
title: Capacidades de deteção no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a compreender como funcionam as capacidades de deteção do Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 4c5599cc-99a1-430f-895f-601615ef12a0
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/2/2018
ms.author: rkarlin
ms.openlocfilehash: bb54221dedc3bfd5ddc84098d5309d26abcfa305
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515571"
---
# <a name="azure-security-center-detection-capabilities"></a>Capacidades de deteção do Centro de Segurança do Azure
Este documento aborda os recursos de detecção avançada da central de segurança do Azure, que ajuda a identificar as ameaças ativas que visam seus recursos de Microsoft Azure – Windows e Linux – e fornece as informações necessárias para responder rapidamente.

As deteções avançadas estão disponíveis no Escalão Standard do Centro de Segurança do Azure. Está disponível uma avaliação gratuita. Pode atualizar a partir da seleção do Escalão de Preço na [Política de Segurança](tutorial-security-policy.md). Visite a [página do Centro de Segurança](https://azure.microsoft.com/pricing/details/security-center/) para saber mais sobre os preços.


## <a name="responding-to-todays-threats"></a>Responder às ameaças atuais
Nos últimos 20 anos, ocorreram alterações significativas no campo das ameaças. No passado, geralmente, as empresas só tinham de se preocupar com a desfiguração do site por parte de atacantes individuais que estavam interessados, sobretudo, em ver “o que podiam fazer”. Os atacantes de hoje são muito mais sofisticados e organizados. Têm, muitas vezes, objetivos financeiros e estratégicos específicos. Também têm mais recursos disponíveis, uma vez que podem ser financiados por Estados ou pelo crime organizado.

Esta abordagem deu origem a um nível de profissionalismo sem precedentes da parte dos atacantes. Já não estão interessados na desfiguração dos sites. Estão agora interessados no roubo de informações, contas bancárias e dados privados, sendo que tudo isso pode ser utilizado para gerar liquidez no mercado aberto ou para tirar partido de um negócio específico, posição política ou militar. Ainda mais preocupante do que os atacantes com objetivos financeiros, temos os atacantes que entram ilegalmente em redes de forma a prejudicar infraestruturas e pessoas.

Como resposta, as organizações implementam frequentemente diversas soluções de pontos, as quais se concentram na defesa do perímetro ou dos pontos finais da empresa ao procurar a assinatura de ataques conhecidos. Estas soluções têm tendência a gerar um grande volume de alertas de baixa fidelidade, o que requer que um analista de segurança faça uma triagem e investigue. A maioria das organizações não tem o tempo e os conhecimentos necessários para responder a estes alertas e muitos deles não são sequer abordados.  Entretanto, os atacantes fizeram avançar os respetivos métodos para subverter várias defesas baseadas na assinatura e [adaptar-se a ambientes de nuvem](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). São necessárias novas abordagem para identificar ameaças emergentes mais rapidamente e agilizar a deteção e a resposta.

## Como a central de segurança do Azure detecta e responde às ameaças<a name="asc-detects"></a>
Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Estes têm acesso a um conjunto amplo de telemetria obtida através da presença global da Microsoft na nuvem e no local. Esta coleção abrangente e diversificada de conjuntos de dados permite à Microsoft descobrir novos padrões de ataque e tendências dos seus produtos de consumidor e empresariais no local, assim como os seus serviços online. Como resultado, o Centro de Segurança pode atualizar rapidamente os respetivos algoritmos de deteção à medida que os atacantes disponibilizam novas e cada vez mais sofisticadas explorações. Esta abordagem ajuda-o a estar a par do ritmo dinâmico dos ambientes de ameaças.

A deteção de ameaças do Centro de Segurança funciona através da recolha automática de informações de segurança a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas. Analisa esta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça.

![Recolha e apresentação de dados do Centro de Segurança](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Os avanços nas tecnologias de macrodados e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são aproveitados para avaliar eventos em toda a nuvem, o que permite a deteção de ameaças que seriam impossíveis de identificar através da utilização de abordagens manuais e a previsão da evolução de ataques. Estas análises de segurança incluem:

* **Informações de ameaças integradas**: procuram por pessoas mal-intencionadas conhecidas ao tirar partido da análise de informações globais a partir de produtos e serviços da Microsoft, da Unidade de Crimes Digitais (DCU) da Microsoft, do Microsoft Security Response Center (MSRC) e de feeds externos.
* **Análise comportamental**: aplica padrões conhecidos para detetar um comportamento malicioso.
* **Deteção de anomalias**: utiliza uma criação de perfis estatística para criar uma linha de base histórica. Alerta sobre os desvios de linhas de base estabelecidos em conformidade com um potencial vetor de ataque.

### <a name="threat-intelligence"></a>Informações sobre ameaças
A Microsoft tem uma grande quantidade de informações sobre ameaças globais. A telemetria provém de várias origens, tal como o Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, Unidade de Crimes Digitais (DCU) da Microsoft e Microsoft Security Response Center (MSRC). Os investigadores também recebem informações sobre ameaças que são partilhadas entre os grandes fornecedores de serviços em nuvem e subscrevem feeds de informações sobre ameaças de terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas. Alguns exemplos incluem:

* **Comunicação de saída para um endereço IP mal-intencionado**: o tráfego de saída para um botnet ou Darknet conhecido provavelmente indica que o recurso foi comprometido e um invasor está tentando executar comandos no sistema ou nos dados do exfiltrar. O Centro de Segurança do Azure compara o tráfego de rede com a base de dados de ameaças global da Microsoft e alerta-o se detetar comunicações com um endereço IP malicioso.

## <a name="behavioral-analytics"></a>Análise comportamental
A análise comportamental é uma técnica que analisa e compara os dados para uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos. Também são determinados através de uma análise cuidada de comportamentos maliciosos pelos analistas especialistas. O Centro de Segurança do Azure pode utilizar a análise comportamental para identificar recursos comprometidos com base na análise dos registos das máquinas virtuais, dos registos dos dispositivos de rede virtual, dos registos de recursos de infraestrutura, das informações de falhas do sistema e de outras origens.

Além disso, existe uma correlação com outros sinais para procurar provas de uma campanha ampla. Esta correlação ajuda a identificar eventos que são consistentes com indicadores estabelecidos de comprometimento. Alguns exemplos incluem:

* **Execução de processo suspeita**: Os invasores empregam várias técnicas para executar software mal-intencionado sem detecção. Por exemplo, um atacante pode dar a software maligno um nome igual ao dos ficheiros de sistema legítimos e colocar esses ficheiros em localizações alternativas, utilizar um nome que é muito semelhante a um ficheiro benigno ou mascarar a verdadeira extensão do ficheiro. Os modelos do Centro de Segurança processam comportamentos de processos e monitorizam as execuções dos processos para detetar valores atípicos como estes.  
* **Tentativas de malware e exploração ocultas**: Um malware sofisticado é capaz de escapar os produtos Antimalware tradicionais, nunca gravando em disco ou criptografando componentes de software armazenados em disco.  No entanto, esse software maligno pode ser detetado com a análise de memória, uma vez que este software tem de deixar rastreios na memória para funcionar. Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha.  Ao analisar a memória na informação de falha de sistema, o Centro de Segurança do Azure pode detetar técnicas utilizadas para explora vulnerabilidades no software, aceder a dados confidenciais e persistir clandestinamente na máquina comprometida sem afetar o desempenho da sua máquina.
* **Movimento lateral e reconhecimento interno**: Para persistir em uma rede comprometida e localizar/coletar dados valiosos, os invasores geralmente tentam se mover mais tarde do computador comprometido para outras pessoas na mesma rede. O Centro de Segurança monitoriza os processos e as atividades de início de sessão para detetar as tentativas do atacante em expandir a sua presença dentro da rede, tais como a pesquisa de rede da execução de comandos remotos e a enumeração da conta.
* **Scripts do PowerShell mal-intencionados**: O PowerShell está sendo usado por invasores para executar código mal-intencionado em máquinas virtuais de destino para uma variedade de finalidades. O Centro da Segurança inspeciona a atividade do PowerShell quanto a provas de atividade suspeita.
* **Ataques de saída**: Os invasores geralmente destinam-se a recursos de nuvem com o objetivo de usar esses recursos para montar ataques adicionais. As máquinas virtuais comprometidas, por exemplo, podem ser utilizadas para iniciar ataques de força bruta contra outras máquinas virtuais, enviar SPAM ou analisar portas abertas e outros dispositivos na Internet. Ao aplicar o machine learning ao tráfego de rede, o Centro de Segurança pode detetar quando as comunicações da rede de saída excedem o normal. No caso do SPAM, o Centro de Segurança correlaciona também o tráfego de e-mail invulgar com informações do Office 365 para determinar se o e-mail possui conteúdo nefasto ou se resulta de uma campanha legítima.  

### <a name="anomaly-detection"></a>Deteção de anomalias
O Centro de Segurança do Azure utiliza também a deteção de anomalias para identificar ameaças. Contrariamente à análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais “personalizada” e concentra-se nas linhas de base específicas das suas implementações. O machine Learning é utilizado para determinar a atividade normal das suas implementações e, em seguida, são geradas regras para definir condições de valores atípicos que podem representar um evento de segurança. Segue-se um exemplo:

* **Ataques de força bruta de RDP/SSH de entrada**: Suas implantações podem ter máquinas virtuais ocupadas com muitos logons por dia e outras máquinas virtuais que têm muito poucos ou quaisquer logons. O Centro de Segurança do Azure pode determinar a atividade de início de sessão de linha de base para estas máquinas virtuais e utilizar o machine learning para definir o que está fora da atividade de início de sessão normal. Se o número de inícios de sessão ou a hora do dia dos inícios de sessão, a localização a partir da qual são pedidos os inícios de sessão ou outras características relacionadas com o início de sessão forem significativamente diferentes da linha de base, então, pode ser gerado um alerta. Novamente, o machine learning determina o que é significativo.

## <a name="continuous-threat-intelligence-monitoring"></a>Monitorização de informações sobre ameaças contínua
O Centro de Segurança do Azure emprega equipas de pesquisa de segurança e ciência de dados que monitorizam continuamente as alterações no campo das ameaças. Isto inclui as seguintes iniciativas:

* **Monitoramento de inteligência contra ameaças**: A inteligência contra ameaças inclui mecanismos, indicadores, implicações e conselhos acionáveis sobre ameaças emergentes ou existentes. Esta informação é partilhada na comunidade de segurança e a Microsoft monitoriza continuamente os feeds de informações sobre ameaças de origens internas e externas.
* **Compartilhamento de sinal**: As informações das equipes de segurança no amplo portfólio da Microsoft de serviços locais e de nuvem, servidores e dispositivos de ponto de extremidade de cliente são compartilhadas e analisadas.
* **Especialistas em segurança da Microsoft**: Envolvimento em andamento com equipes da Microsoft que trabalham em campos de segurança especializados, como análise forense e detecção de ataques da Web.
* **Ajuste de detecção**: Os algoritmos são executados em conjuntos de dados de clientes reais e pesquisadores de segurança funcionam com clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Estes esforços combinados culminam em novas e melhoradas deteções das quais pode beneficiar de imediato, sem que seja precisa qualquer ação da sua parte.

## <a name="see-also"></a>Consulte também
Neste documento, aprendeu como funcionam as capacidades de deteção do Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas de segurança na central de segurança do Azure](security-center-alerts-overview.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
