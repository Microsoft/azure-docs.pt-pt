---
title: O que é o Centro de Segurança do Azure? | Microsoft Docs
description: Esta página descreve os principais benefícios do Security Center - descobrir o seu estado de segurança e melhorá-lo com cobertura de recursos de nuvem e no local.
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
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: c4861192c7f2bbfb2a51e19b88daee45b501949b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634775"
---
# <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?

O Azure Security Center é um sistema unificado de gestão de segurança de infraestruturas que fortalece a postura de segurança dos seus centros de dados, e fornece uma proteção avançada de ameaças através das suas cargas de trabalho híbridas na nuvem - quer estejam em Azure ou não - bem como nas instalações.

Manter os seus recursos seguros é um esforço conjunto entre o seu fornecedor de nuvem, a Azure, e você, o cliente. Tem de se certificar de que as suas cargas de trabalho estão seguras à medida que se move para a nuvem e, ao mesmo tempo, quando se muda para o IaaS (infraestrutura como serviço) há mais responsabilidade do cliente do que no PaaS (plataforma como serviço) e no SaaS (software como serviço). O Azure Security Center fornece-lhe as ferramentas necessárias para endurecer a sua rede, proteger os seus serviços e certificar-se de que está em cima da sua postura de segurança.

O Centro de Segurança do Azure aborda os três desafios de segurança mais urgentes:

-   **Mudanças rápidas de carga de trabalho** – É simultaneamente uma força e um desafio da nuvem. Por um lado, os utilizadores finais podem realizar mais ações. Por outro lado, como garante que os serviços em constante mudança que as pessoas utilizam e criam estão de acordo com os seus padrões de segurança e seguem as melhores práticas de segurança?

-   **Ataques cada vez mais sofisticados** – onde quer que execute as suas cargas de trabalho, os ataques ficam cada vez mais sofisticados. Tem de proteger as suas cargas de trabalho de cloud pública, que são na verdade uma carga de trabalho com acesso à Internet que pode deixá-lo ainda mais vulnerável se não seguir as melhores práticas de segurança.

-   **As competências de segurança são escassas** – o número de alertas de segurança e sistemas de alerta ultrapassa o número de administradores com a experiência e conhecimentos necessários para garantir que os seus ambientes estão protegidos. Manter-se atualizado em relação aos ataques mais recentes é um desafio constante, o que torna impossível permanecer no mesmo lugar enquanto o mundo da segurança está em constante mudança.

Para o ajudar a proteger-se contra estes desafios, o Centro de Segurança fornece as ferramentas para:

-   **Fortaleça a postura de segurança**: O Centro de Segurança avalia o seu ambiente e permite-lhe compreender o estado dos seus recursos e se estão seguros.

-   **Proteja contra ameaças**: O Centro de Segurança avalia as suas cargas de trabalho e eleva as recomendações de prevenção de ameaças e alertas de segurança.

-   **Proteger-se rapidamente**: no Centro de Segurança, tudo é feito à velocidade da cloud. Como é integrada nativamente, a implementação do Centro de Segurança é fácil, fornecendo aprovisionamento automático e proteção com serviços do Azure.

> [!NOTE]
> Este serviço suporta [o Azure Lighthouse,](../lighthouse/overview.md)que permite que os prestadores de serviços inscrevam-se no seu próprio inquilino para gerir subscrições e grupos de recursos que os clientes delegaram. Para cenários do Azure Security Center, uma subscrição deve ser delegada em vez de grupos de recursos individuais.

## <a name="architecture"></a>Arquitetura

Como o Security Center faz parte da Azure, os serviços paaS em Azure - incluindo tecido de serviço, base de dados SQL, SQL Managed Instance e contas de armazenamento - são monitorizados e protegidos pelo Security Center sem necessitar de qualquer implantação.

Além disso, o Security Center protege servidores e máquinas virtuais não-Azure na nuvem ou nas instalações, tanto para servidores Windows como Linux, instalando o agente Log Analytics nos mesmos. As máquinas virtuais Azure são a provisionadas automaticamente no Centro de Segurança.

Os eventos recolhidos dos agentes e da Azure estão correlacionados no motor de análise de segurança para fornecer-lhe recomendações personalizadas (tarefas de endurecimento), que deve seguir para se certificar de que as suas cargas de trabalho estão seguras e alertas de segurança. Deve investigar tais alertas o mais rápido possível para garantir que ataques maliciosos não ocorram nas suas cargas de trabalho.

Quando ativa o Centro de Segurança, a política de segurança incorporada ao Centro de Segurança reflete-se na Política de Azure como uma iniciativa integrada na categoria Centro de Segurança. A iniciativa incorporada é automaticamente atribuída a todas as subscrições registadas do Security Center (independentemente de terem ou não o Azure Defender habilitado). A iniciativa incorporada contém apenas políticas de auditoria. Para obter mais informações sobre as políticas do Centro de Segurança na Política Azure, consulte [Trabalhar com políticas de segurança.](tutorial-security-policy.md)

## <a name="strengthen-security-posture"></a>Reforçar a postura de segurança

O Centro de Segurança Azure permite-lhe reforçar a sua postura de segurança. Isto significa que o ajuda a identificar e executar as tarefas de endurecimento recomendadas como boas práticas de segurança e implementá-las em todas as suas máquinas, serviços de dados e aplicações. Isto inclui gerir e aplicar as suas políticas de segurança e certificar-se de que as suas máquinas virtuais Azure, servidores não-Azure e serviços Azure PaaS estão em conformidade. O Security Center fornece-lhe as ferramentas necessárias para ter uma visão de pássaro sobre as suas cargas de trabalho, com visibilidade focada na sua propriedade de segurança de rede. 

### <a name="manage-organization-security-policy-and-compliance"></a>Gerir a política de segurança da organização e o cumprimento

É uma segurança básica para saber e certificar-se de que as suas cargas de trabalho estão seguras, e começa por ter políticas de segurança personalizadas em vigor. Como todas as políticas do Centro de Segurança são construídas em cima dos controlos da Política Azure, você está recebendo toda a gama e flexibilidade de uma **solução política de classe mundial.** No Security Center, pode definir as suas políticas para executar em grupos de gestão, através de subscrições, e até mesmo para um inquilino inteiro.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="Página de gestão de políticas":::

O Security Center ajuda-o a **identificar subscrições de TI shadow.** Ao olhar para as subscrições rotuladas **não abrangidas** pelo seu dashboard, pode saber imediatamente quando há subscrições recém-criadas e certificar-se de que estão cobertas pelas suas políticas e protegidas pelo Azure Security Center.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Painel de política do Centro de Segurança":::

### <a name="continuous-assessments"></a>Avaliações contínuas

O Security Center descobre continuamente novos recursos que estão a ser implementados através das suas cargas de trabalho e avalia se estão configurados de acordo com as melhores práticas de segurança, se não, estão sinalizados e obtém uma lista prioritária de recomendações para o que precisa de corrigir para proteger as suas máquinas. Esta lista de recomendações é ativada e apoiada pela [Azure Security Benchmark](../security/benchmarks/introduction.md), o conjunto de orientações específicas da Microsoft para a segurança e conformidade com base em quadros comuns de conformidade. Este referencial amplamente respeitado baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do Instituto Nacional de [Normalização e Tecnologia (NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem.

Para ajudá-lo a entender a importância de cada recomendação para a sua postura de segurança geral, o Security Center agrupe as recomendações em controlos de segurança e adiciona um valor **de pontuação seguro** a cada controlo. Isto é crucial para que você **priorize o seu trabalho de segurança**.

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Pontuação segura do Centro de Segurança":::

### <a name="network-map"></a>Mapa de rede

Uma das ferramentas mais poderosas que o Security Center fornece para monitorizar continuamente o estado de segurança da sua rede é o **mapa da Rede.** O mapa permite-lhe ver a topologia das suas cargas de trabalho, para que possa ver se cada nó está devidamente configurado. Pode ver como os seus nós estão ligados, o que o ajuda a bloquear ligações indesejadas que podem potencialmente facilitar o rastejar por um intruso ao longo da sua rede.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Mapa da rede do Centro de Segurança":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Otimizar e melhorar a segurança configurando os controlos recomendados

O coração do valor do Centro de Segurança Azure está nas suas recomendações. As recomendações são adaptadas às preocupações de segurança encontradas nas suas cargas de trabalho, e o Security Center faz o trabalho de administração de segurança para si, não só encontrando as suas vulnerabilidades, mas fornecendo-lhe instruções específicas sobre como se livrar delas.

Desta forma, o Security Center permite-lhe não só definir políticas de segurança, mas aplicar padrões de configuração seguros em todos os seus recursos.

As recomendações ajudam-no a reduzir a superfície de ataque em cada um dos seus recursos. Isto inclui máquinas virtuais Azure, servidores não-Azure e serviços Azure PaaS, como contas DE SQL e Armazenamento e muito mais - onde cada tipo de recurso é avaliado de forma diferente e tem os seus próprios padrões.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Exemplo de recomendação do Centro de Segurança":::

## <a name="protect-against-threats"></a>Proteger contra ameaças

A proteção contra ameaças do Security Center permite-lhe detetar e prevenir ameaças na Infraestrutura como camada de Serviço (IaaS), servidores não-Azure, bem como para plataformas como um Serviço (PaaS) em Azure.

A proteção contra ameaças do Security Center inclui análises de cadeia de morte de fusão, que correlaciona automaticamente alertas no seu ambiente com base na análise da cadeia de morte cibernética, para ajudá-lo a entender melhor a história completa de uma campanha de ataque, onde começou e que tipo de impacto teve nos seus recursos.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de segurança do Azure Security Center":::

### <a name="integration-with-microsoft-defender-for-endpoint"></a>Integração com o Microsoft Defender para Endpoint

O Azure Defender para servidores inclui integração automática e nativa com o Microsoft Defender para Endpoint. Saiba mais, [Proteja os seus pontos finais com a solução EDR integrada do Security Center: Microsoft Defender for Endpoint](security-center-wdatp.md)


### <a name="protect-paas"></a>Proteger PaaS

O Centro de Segurança ajuda-o a detetar ameaças nos serviços da Azure PaaS. Pode detetar ameaças direcionadas aos serviços Azure, incluindo o Azure App Service, Azure SQL, Azure Storage Account e mais serviços de dados. Também pode aproveitar a integração nativa com o Utilizador e a Entity Behavioral Analytics (UEBA) da Microsoft Cloud App Security para realizar a deteção de anomalias nos seus registos de atividade do Azure.

### <a name="block-brute-force-attacks"></a>Bloquear ataques de força bruta

O Centro de Segurança ajuda-o a limitar a exposição a ataques de força bruta. Ao reduzir o acesso às portas de máquinas virtuais, utilizando o acesso VM just-in-time, pode endurecer a sua rede evitando o acesso desnecessário. Pode definir políticas de acesso seguras em portas selecionadas, apenas para utilizadores autorizados, intervalos de endereços IP de origem permitidas ou endereços IP, e por um período limitado de tempo.

### <a name="protect-data-services"></a>Proteger serviços de dados

O Security Center inclui capacidades que o ajudam a executar a classificação automática dos seus dados em Azure SQL. Também pode obter avaliações de potenciais vulnerabilidades em todos os serviços Azure SQL e Storage, e recomendações para como atenuá-las.

## <a name="get-secure-faster"></a>Fique seguro mais rápido

A integração nativa do Azure (incluindo registos Azure Policy e Azure Monitor) combinada com uma integração perfeita com outras soluções de segurança da Microsoft, como a Microsoft Cloud App Security e o Microsoft Defender for Endpoint ajudam a garantir que a sua solução de segurança é abrangente, bem como simples de embarcar e lançar.

Além disso, pode estender a solução completa para além do Azure até cargas de trabalho que correm noutras nuvens e em centros de dados no local.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Descubra automaticamente e a bordo recursos Azure

O Security Center proporciona uma integração nativa perfeita com os recursos Azure e Azure. Isso significa que você pode reunir uma história de segurança completa envolvendo a Política Azure e políticas do Centro de Segurança incorporado em todos os seus recursos Azure, e certifique-se de que tudo é automaticamente aplicado a recursos recém-descobertos à medida que os cria em Azure.

Extensa recolha de registos - os registos do Windows e do Linux são todos alavancados no motor de análise de segurança e utilizados para criar recomendações e alertas.

## <a name="next-steps"></a>Passos seguintes

- Para começar a utilizar o Centro de Segurança, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).

- O nível de preços gratuitos do Security Center está ativado em todas as subscrições Azure atuais assim que visitar o dashboard do Centro de Segurança Azure no portal Azure pela primeira vez, ou se ativado programáticamente através da API. Para tirar partido das capacidades avançadas de gestão de segurança e deteção de ameaças, tem de ativar o Azure Defender. O Azure Defender pode ser julgado gratuitamente durante 30 dias. Veja a [página de preços do Centro de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/), para obter mais informações.

- Se está pronto para ativar o Azure Defender agora, [Quickstart: Configurar o Centro de Segurança Azure](security-center-get-started.md) leva-o pelos degraus.