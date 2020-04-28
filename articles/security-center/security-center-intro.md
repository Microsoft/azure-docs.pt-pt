---
title: O que é o Centro de Segurança do Azure? | Microsoft Docs
description: Esta página descreve os principais benefícios do Security Center - descobrir o seu estado de segurança, e melhorá-lo com cobertura de cloud e recursos on-prem.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2019
ms.author: memildin
ms.openlocfilehash: a02abd931dddd410882dd67a73a5b5fab6dc8cf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194895"
---
# <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?

O Azure Security Center é um sistema unificado de gestão de segurança de infraestruturas que fortalece a postura de segurança dos seus centros de dados, e fornece proteção avançada contra ameaças em todas as suas cargas de trabalho híbridas na nuvem - quer estejam ou não em Azure - bem como nas instalações.

Manter os seus recursos seguros é um esforço conjunto entre o seu fornecedor de nuvem, o Azure, e você, o cliente. Tem de se certificar de que as suas cargas de trabalho estão seguras à medida que se desloca para a nuvem, e ao mesmo tempo, quando se muda para o IaaS (infraestrutura como serviço) há mais responsabilidade do cliente do que havia no PaaS (plataforma como serviço) e no SaaS (software como serviço). O Azure Security Center fornece-lhe as ferramentas necessárias para endurecer a sua rede, proteger os seus serviços e certificar-se de que está em cima da sua postura de segurança.

O Centro de Segurança do Azure aborda os três desafios de segurança mais urgentes:

-   **Trabalhos** de trabalho em rápida mudança – É simultaneamente uma força e um desafio da nuvem. Por um lado, os utilizadores finais podem realizar mais ações. Por outro lado, como garante que os serviços em constante mudança que as pessoas utilizam e criam estão de acordo com os seus padrões de segurança e seguem as melhores práticas de segurança?

-   **Ataques cada vez mais sofisticados** – onde quer que execute as suas cargas de trabalho, os ataques ficam cada vez mais sofisticados. Tem de proteger as suas cargas de trabalho de cloud pública, que são na verdade uma carga de trabalho com acesso à Internet que pode deixá-lo ainda mais vulnerável se não seguir as melhores práticas de segurança.

-   **As competências de segurança são escassas** – o número de alertas de segurança e sistemas de alerta ultrapassa o número de administradores com a experiência e conhecimentos necessários para garantir que os seus ambientes estão protegidos. Manter-se atualizado em relação aos ataques mais recentes é um desafio constante, o que torna impossível permanecer no mesmo lugar enquanto o mundo da segurança está em constante mudança.

Para o ajudar a proteger-se contra estes desafios, o Centro de Segurança fornece as ferramentas para:

-   **Fortalecer**a postura de segurança : O Security Center avalia o seu ambiente e permite-lhe compreender o estado dos seus recursos e se estão seguros.

-   **Proteja contra ameaças**: O Centro de Segurança avalia as suas cargas de trabalho e levanta recomendações de prevenção de ameaças e alertas de segurança.

-   **Fique seguro mais rápido:** No Centro de Segurança, tudo é feito em velocidade de nuvem. Por ser integrado de forma nativa, a implantação do Security Center é fácil, proporcionando-lhe o fornecimento automático e proteção com os serviços Azure.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Arquitetura

Como o Security Center faz parte do Azure, os serviços PaaS em Azure - incluindo tecido de serviço, bases de dados SQL e contas de armazenamento - são monitorizados e protegidos pelo Security Center sem necessitar de qualquer implantação.

Além disso, o Security Center protege servidores não-Azure e máquinas virtuais na nuvem ou nas instalações, tanto para servidores Windows como Linux, instalando o agente Log Analytics nos mesmos. As máquinas virtuais Azure são auto-aprovisionadas no Centro de Segurança.

Os eventos recolhidos dos agentes e do Azure estão correlacionados no motor de análise de segurança para lhe fornecer recomendações personalizadas (tarefas de endurecimento), que deve seguir para garantir que as suas cargas de trabalho estão seguras e alertas de segurança. Deve investigar esses alertas o mais rapidamente possível para garantir que ataques maliciosos não estão a ocorrer nas suas cargas de trabalho.

Quando permite o Centro de Segurança, a política de segurança incorporada no Centro de Segurança reflete-se na Política Azure como uma iniciativa construída na categoria Centro de Segurança. A iniciativa incorporada é automaticamente atribuída a todas as subscrições registadas do Centro de Segurança (níveis gratuitos ou standard). A iniciativa incorporada contém apenas políticas de auditoria. Para obter mais informações sobre as políticas do Centro de Segurança na Política Azure, consulte Trabalhar com políticas de [segurança.](tutorial-security-policy.md)

## <a name="strengthen-security-posture"></a>Reforçar a postura de segurança

O Azure Security Center permite-lhe reforçar a sua postura de segurança. Isto significa que o ajuda a identificar e executar as tarefas de endurecimento recomendadas como boas práticas de segurança e implementá-las através das suas máquinas, serviços de dados e aplicações. Isto inclui gerir e aplicar as suas políticas de segurança, e garantir que as suas máquinas virtuais Azure, servidores não-Azure e serviços Azure PaaS estão em conformidade. O Security Center fornece-lhe as ferramentas necessárias para ter uma visão de pássaro sobre as suas cargas de trabalho, com visibilidade focada na sua propriedade de segurança de rede. 

### <a name="manage-organization-security-policy-and-compliance"></a>Gerir a política e conformidade da organização

É uma garantia básica para saber e certificar-se de que as suas cargas de trabalho estão seguras, e começa com políticas de segurança personalizadas em vigor. Como todas as políticas no Centro de Segurança são construídas em cima dos controlos da Política Azure, você está recebendo toda a gama e flexibilidade de uma **solução política de classe mundial**. No Security Center, pode definir as suas políticas para funcionar em grupos de gestão, através de subscrições e até mesmo para um inquilino inteiro.

![Dashboard Centro de Segurança](media/security-center-intro/sc-dashboard.png)

O Security Center ajuda-o a **identificar as assinaturas shadow IT**. Ao olhar para as subscrições não **abrangidas** pelo seu painel de instrumentos, pode saber imediatamente quando existem subscrições recém-criadas e certificar-se de que estão abrangidas pelas suas políticas e protegidas pelo Azure Security Center.

![Painel de política do Centro de Segurança](media/security-center-intro/sc-policy-dashboard.png)

As capacidades avançadas de monitorização no Centro de Segurança também permitem **rastrear e gerir o cumprimento e a governação ao longo do tempo.** O **cumprimento geral** fornece-lhe uma medida de quanto as suas subscrições estão em conformidade com as políticas associadas à sua carga de trabalho. 

![Política do Centro de Segurança ao longo do tempo](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Avaliações contínuas

O Security Center descobre continuamente novos recursos que estão a ser implantados nas suas cargas de trabalho e avalia se estão configurados de acordo com as melhores práticas de segurança, se não, estão sinalizados e obtém uma lista prioritária de recomendações para o que precisa de corrigir para proteger as suas máquinas.

Uma das ferramentas mais poderosas que o Security Center fornece para monitorizar continuamente o estado de segurança da sua rede é o **mapa da Rede**. O mapa permite-lhe ver a topologia das suas cargas de trabalho, para que possa ver se cada nó está devidamente configurado. Pode ver como os seus nós estão conectados, o que o ajuda a bloquear ligações indesejadas que podem potencialmente facilitar a vida de um intruso ao longo da sua rede.

![Mapa da rede do Centro de Segurança](media/security-center-intro/sc-net-map.png)

O Centro de Segurança facilita a mitigação dos alertas de segurança um passo mais fácil, adicionando uma **Pontuação Segura**. As Pontuações Seguras estão agora associadas a cada recomendação que recebe para ajudá-lo a entender a importância de cada recomendação para a sua postura de segurança geral. Isto é crucial para lhe permitir **priorizar o seu trabalho**de segurança.

![Pontuação segura do centro de segurança](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Otimize e melhore a segurança configurando controlos recomendados

O coração do valor do Azure Security Center reside nas suas recomendações. As recomendações são adaptadas às preocupações de segurança específicas encontradas nas suas cargas de trabalho, e o Security Center faz o trabalho de segurança para si, não só encontrando as suas vulnerabilidades, mas fornecendo-lhe instruções específicas para como se livrar delas.

![Recomendações do Centro de Segurança](media/security-center-intro/sc-recommendations.png)

Desta forma, o Security Center permite não só definir políticas de segurança, mas aplicar padrões de configuração seguros em todos os seus recursos.

As recomendações ajudam-no a reduzir a superfície de ataque em cada um dos seus recursos. Isto inclui máquinas virtuais Azure, servidores não-Azure e serviços Azure PaaS, como contas SQL e Armazenamento e muito mais - onde cada tipo de recurso é avaliado de forma diferente e tem os seus próprios padrões.

![Exemplo de recomendação do Centro de Segurança](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Proteger contra ameaças

A proteção contra ameaças do Security Center permite detetar e prevenir ameaças na Infraestruturas como camada de Serviço (IaaS), servidores não-Azure, bem como para plataformas como serviço (PaaS) em Azure.

A proteção contra ameaças do Security Center inclui a análise da cadeia de morte de fusão, que correlaciona automaticamente alertas no seu ambiente com base na análise da cadeia de morte cibernética, para ajudá-lo a entender melhor a história completa de uma campanha de ataque, onde começou e que tipo de impacto teve nos seus recursos.



![Recomendação de ataque do Centro de Segurança](media/security-center-intro/sc-attack-recommendation.png)

### <a name="integration-with-microsoft-defender-advanced-threat-protection"></a>Integração com a Proteção avançada de ameaças do Microsoft Defender

O Security Center inclui integração automática e nativa com a Microsoft Defender Advanced Threat Protection. Isto significa que, sem qualquer configuração, as suas máquinas Windows e Linux estão totalmente integradas com as recomendações e avaliações do Security Center.

Além disso, o Security Center permite automatizar as políticas de controlo de aplicações em ambientes de servidores. Os controlos de aplicação adaptativa no Security Center permitem a listagem de aplicações de ponta a ponta nos seus servidores Windows. Não precisas de criar as regras e verificar violações, é tudo feito automaticamente por ti.

### <a name="protect-paas"></a>Proteger paaS

O Centro de Segurança ajuda-o a detetar ameaças nos serviços do Azure PaaS. Pode detetar ameaças dirigidas a serviços Azure, incluindo o Azure App Service, Azure SQL, Conta de Armazenamento Azure e mais serviços de dados. Também pode aproveitar a integração nativa com o Utilizador e Entidade Comportamental da Microsoft Cloud App Security (UEBA) para realizar a deteção de anomalias nos seus registos de atividade do Azure.

### <a name="block-brute-force-attacks"></a>Bloquear ataques de força bruta

O Centro de Segurança ajuda-o a limitar a exposição a ataques de força bruta. Ao reduzir o acesso às portas de máquinas virtuais, utilizando o acesso VM just-in-time, pode endurecer a sua rede evitando um acesso desnecessário. Pode definir políticas de acesso segura em portas selecionadas, apenas para utilizadores autorizados, intervalos de endereços IP de origem ou endereços IP, e por um período limitado de tempo.

![Força bruta do Centro de Segurança](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Proteger os serviços de dados

O Security Center inclui capacidades que o ajudam a realizar a classificação automática dos seus dados no Azure SQL. Também pode obter avaliações de potenciais vulnerabilidades em serviços Azure SQL e Armazenamento, e recomendações sobre como atenuá-las.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>Proteja as cargas de trabalho em nuvem ioT e híbrida

O Azure Security Center for IoT (Internet of Things) simplifica a proteção da carga de trabalho híbrida, proporcionando visibilidade e controlo unificados, prevenção de ameaças adaptativas e proteção inteligente de ameaças e resposta através de cargas de trabalho que funcionam no limite, no local, em Azure, e em outras nuvens. Para mais informações, consulte [o Azure Security Center para ioT](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Fique seguro mais rápido

A integração native Azure (incluindo registos Azure Policy e Azure Monitor) combinada com uma integração perfeita com outras soluções de segurança da Microsoft, como a Microsoft Cloud App Security e o Windows Defender Advanced Threat Protection ajudam a garantir que a sua solução de segurança é abrangente, bem como simples de embarcar e lançar.

Além disso, pode estender a solução completa para além do Azure a cargas de trabalho que correm noutras nuvens e em centros de dados no local.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Descubra automaticamente e a bordo os recursos do Azure

O Centro de Segurança proporciona uma integração nativa e perfeita com recursos Azure e Azure. Isto significa que você pode reunir uma história de segurança completa envolvendo políticas azure policy e políticas do Centro de Segurança incorporado em todos os seus recursos Azure, e certifique-se de que tudo é automaticamente aplicado aos recursos recém-descobertos à medida que os cria em Azure.

Extensa recolha de registos - os registos do Windows e do Linux são todos alavancados no motor de análise de segurança e utilizados para criar recomendações e alertas.

## <a name="next-steps"></a>Passos seguintes

- Para começar a utilizar o Centro de Segurança, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).

- O nível de preços gratuitos do Security Center está ativado em todas as suas subscrições atuais do Azure uma vez que você visita o painel de instrumentos do Azure Security Center no Portal Azure pela primeira vez, ou se ativado programáticamente via API. Para tirar partido das capacidades avançadas de gestão de segurança e deteção de ameaças, tem de fazer upgrade para o nível de preços padrão. O nível padrão pode ser experimentado gratuitamente por 30 dias. Veja a [página de preços do Centro de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/), para obter mais informações.

- Se está pronto para ativar o padrão do Security Center agora, o [Quickstart: A bordo da subscrição do Azure para o Security Center Standard](security-center-get-started.md) acompanha-o através dos degraus.