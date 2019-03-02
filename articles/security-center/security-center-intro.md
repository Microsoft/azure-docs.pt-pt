---
title: O que é o Centro de Segurança do Azure? | Microsoft Docs
description: Saiba mais sobre o Centro de Segurança do Azure, as suas capacidades principais e como funciona.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 13526268480c69798ee52078f7448605fc1afa9a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242042"
---
# <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?

Centro de segurança do Azure é um sistema de gerenciamento de segurança de infra-estrutura unificada que reforça a postura de segurança dos seus centros de dados e fornece proteção avançada contra ameaças entre cargas de trabalho híbrida na cloud - independentemente de estarem no Azure ou não -, bem como no local.

Manter os seus recursos seguro é uma iniciativa conjunta entre o seu fornecedor de cloud, Azure e, o cliente. Tem de certificar-se de que as cargas de trabalho são seguras à medida que se move para a cloud e, ao mesmo tempo, quando move para o IaaS (infraestrutura como serviço) há mais responsabilidade do cliente que ocorreu no PaaS (plataforma como serviço) e SaaS (software como serviço). Centro de segurança do Azure fornece-lhe as ferramentas necessárias para proteger sua rede, proteja os seus serviços e certifique-se de que está sobre a sua postura de segurança.

Centro de segurança do Azure aborda os desafios de segurança mais urgentes três:

-   **Alterar rapidamente as cargas de trabalho** – é uma força e um desafio da nuvem. Por um lado, os utilizadores finais estão capacitados a fazer mais. No outro, como Certifique-se de que as pessoas de serviços constante mudança estão a utilizar e criar até seus padrões de segurança e siga as melhores práticas de segurança?

-   **Cada vez mais sofisticados ataques** - sempre que executar cargas de trabalho, os ataques de continuar a receber mais sofisticadas. Precisa proteger as cargas de trabalho de cloud pública, que são, na verdade, uma Internet com acesso à carga de trabalho que pode deixá-lo ainda mais vulneráveis se não seguir segurança melhores práticas.

-   **Habilidades de segurança são, em Resumo, fornecimento** -o número de alertas de segurança e sistemas de alerta outnumbers muito o número de administradores com o histórico necessário e a experiência até agora para se certificar de que os seus ambientes estão protegidos. Permaneça atualizado com os ataques mais recente é um desafio constante, tornando impossível permanecer em vigor, enquanto o mundo da segurança é uma frente em constante mudança.

Para ajudar a se proteger contra esses desafios, o Centro de segurança fornece as ferramentas para:

-   **Reforce a postura de segurança**: Centro de segurança avalia seu ambiente e permite-lhe compreender o estado dos seus recursos, são seguro ou não?

-   **Proteção contra ameaças**: Centro de segurança avalia as cargas de trabalho e gera as recomendações de prevenção de ameaças e alertas de deteção de ameaças.

-   **A mais rapidamente seguro**: No Centro de segurança, tudo o que é feito na velocidade da cloud. Porque ele é integrado de forma nativa, a implementação do Centro de segurança é fácil, dando-lhe autoprovisioning e a proteção com serviços do Azure.

## <a name="architecture"></a>Arquitetura

Uma vez que o Centro de segurança nativamente faz parte do Azure, serviços de PaaS no Azure - incluindo o Service Fabric, bases de dados SQL e contas de armazenamento - são monitorizadas e protegidas pelo centro de segurança sem a necessidade de qualquer implementação.

Além disso, o Centro de segurança protege servidores não pertencente ao Azure e máquinas virtuais na cloud ou no local, para os servidores do Windows e Linux, ao instalar o Microsoft Monitoring Agent nos mesmos. Máquinas virtuais do Azure são aprovisionados no Centro de segurança.

Os eventos recolhidos dos agentes e do Azure são correlacionados no mecanismo de análise de segurança para fornecer a que adaptadas recomendações (sistema de proteção de tarefas), que deve seguir para se certificar de que as cargas de trabalho são seguras bem como alertas de deteção de ameaças. Deve investigar esses alertas logo que possível para se certificar de que os ataques maliciosos, se não estiverem ocorrendo em suas cargas de trabalho.

Quando ativar o Centro de segurança, a política de segurança incorporada ao centro de segurança é refletida na política do Azure como uma iniciativa incorporada na categoria de centro de segurança. A iniciativa incorporada é atribuída automaticamente a todas as subscrições do Centro de segurança registado (escalões gratuito ou Standard). A iniciativa interna contém apenas as políticas de auditoria. Para obter mais informações sobre as políticas do Centro de segurança na política do Azure, consulte [trabalhar com políticas de segurança](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Reforçar a postura de segurança

Centro de segurança do Azure permite-lhe reforce a sua postura de segurança. Isso significa que ele ajuda a identificar e executar as tarefas de proteção recomendadas como melhores práticas de segurança e implementá-los em suas máquinas, os serviços de dados e aplicações. Isto inclui a gerir e impor suas diretivas de segurança e tornar-se de que suas máquinas virtuais do Azure, não pertencente ao Azure servidores e serviços PaaS do Azure estão em conformidade. Centro de segurança fornece as ferramentas que tem de ter um panorama geral nas cargas de trabalho, com uma visibilidade focada no seu património de segurança de rede. 

### <a name="manage-organization-security-policy-and-compliance"></a>Gerir política de segurança da organização e a conformidade

É uma segurança básica saber e certificar-se de que as cargas de trabalho são seguras, e ele começa com ter adaptadas a políticas de segurança. Uma vez que todas as políticas no Centro de segurança são criadas sobre controles de política do Azure, esteja tirando o intervalo completo e a flexibilidade de um **solução de classe mundial de política**. No Centro de segurança, pode definir as políticas para executar em grupos de gestão, entre subscrições e até mesmo para um inquilino de todo.

![Dashboard Centro de Segurança](media/security-center-intro/sc-dashboard.png)

Centro de segurança ajuda-o **identificar subscrições Shadow IT**. Ao consultar subscrições rotuladas **não abrangido** no seu dashboard, pode saber imediatamente quando são recentemente criadas subscrições e certificar-se de que estão abrangidas por suas políticas e protegidas pelo centro de segurança do Azure.

![Dashboard de política do Centro de segurança](media/security-center-intro/sc-policy-dashboard.png)

As capacidades avançadas de monitorização no Centro de segurança também permitem-lhe **controlar e gerir a compatibilidade e governação ao longo do tempo**. O **gerais de conformidade** fornece-lhe uma medida de quanto as suas subscrições estão em conformidade com as políticas associadas com a carga de trabalho. 

![Política do Centro de segurança ao longo do tempo](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Avaliações contínuas

Centro de segurança Deteta os novos recursos que estão a ser implementados em suas cargas de trabalho continuamente e avalia se estiverem configuradas conforme as melhores práticas de segurança, caso contrário, eles são sinalizados e obtém uma lista prioritária de recomendações para o que precisa corrigir para proteger as suas máquinas.

Uma das ferramentas mais poderosas Centro de segurança oferece para monitorizar continuamente o estado de segurança da sua rede é o **mapa de rede**. O mapa permite-lhe ver a topologia das cargas de trabalho, pode ver se cada nó está corretamente configurado. Pode ver como os nós estão ligados, que ajuda a bloquear ligações indesejadas que poderiam potencialmente seja mais fácil para um atacante aumentar ao longo da sua rede.

![Mapa de rede do Centro de segurança](media/security-center-intro/sc-net-map.png)

Mitigar a segurança torna o Centro de segurança de alertas um pouco mais fácil, adicionando um **pontuação segura**. As pontuações seguras agora estão associados a cada recomendação receber para ajudar a compreender qual a importância cada recomendação é a sua postura de segurança geral. Isso é crucial no permitindo que **priorizar seu trabalho de segurança**.

![Pontuação de segurança do Centro de segurança](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Otimizar e melhorar a segurança através da configuração recomendada de controles

O coração do valor do Centro de segurança do Azure está nas suas recomendações. As recomendações são adaptadas para as preocupações de segurança específico encontradas em suas cargas de trabalho e o Centro de segurança faz o trabalho de administrador de segurança para, por não apenas encontrar seu vulnerabilidades, mas o fornecimento de instruções específicas sobre como removê-las.

![Recomendações do Centro de Segurança](media/security-center-intro/sc-recommendations.png)

Dessa forma, o Centro de segurança permite que não só para definir políticas de segurança, mas como aplicar uma configuração segura padrões todos os seus recursos.

As recomendações de ajudá-lo a reduzir a superfície de ataque em cada um dos seus recursos. Que inclui máquinas virtuais do Azure, servidores não pertencente ao Azure e os serviços de PaaS do Azure, como as contas SQL e armazenamento e muito mais - onde cada tipo de recurso é avaliado de forma diferente e tem seus próprio padrões.

![Exemplo de recomendação do Centro de segurança](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Proteger contra ameaças

Proteção contra ameaças do Centro de segurança permite-lhe detetar e prevenir ameaças da infraestrutura como uma camada de serviço (IaaS), servidores não pertencente ao Azure, bem como para plataformas como serviço (PaaS) no Azure.

Proteção contra ameaças do Centro de segurança inclui a análise de cadeia de eliminação de fusion, que automaticamente alertas no seu ambiente com base na análise de cadeia de eliminação de cibersegurança, para o ajudar a compreender melhor a história completa de uma campanha de ataque, quando começou e o que tipo de impacto que ele tinha nos seus recursos.



![Recomendação de ataque do Centro de segurança](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Proteção avançada contra ameaças

Centro de segurança, obtém uma integração nativa com a proteção de ameaças avançada do Windows Defender prontos a utilizar. Isso significa que sem qualquer configuração, as máquinas de virtuais do Windows e os servidores estão totalmente integrados com avaliações e as recomendações do Centro de segurança. Deteção de ameaças avançada também está disponível de imediato para servidores e máquinas virtuais do Linux.

Além disso, o Centro de segurança permite-lhe automatizar as políticas de controlo de aplicações em ambientes de servidor. Os controlos de aplicações adaptativos no Centro de segurança permitem listas de permissões de aplicação de ponto-a-ponto em todos os servidores do Windows. Não precisa de criar as regras e violações de verificação, tudo isso é feito automaticamente por si.

### <a name="protect-paas"></a>Proteger o PaaS

Centro de segurança ajuda-o a detetar ameaças em todos os serviços de PaaS do Azure. Pode detetar ameaças direcionadas para os serviços do Azure, incluindo o serviço de aplicações do Azure, SQL do Azure, conta de armazenamento do Azure e, mais serviços de dados. Também pode tirar partido da integração nativa com o do Microsoft Cloud App Security utilizador e análise comportamental de entidade (UEBA) para executar a deteção de anomalias nos seus registos de atividades do Azure.

### <a name="block-brute-force-attacks"></a>Ataques de força bruta de bloco

Centro de segurança ajuda-o a limitar a exposição a ataques de força bruta. Ao reduzir o acesso às portas de máquina virtual, através do acesso VM just-in-time, pode proteger sua rede, impedindo o acesso desnecessário. Pode definir políticas de acesso seguro em portas selecionadas, apenas usuários autorizados, permitido de intervalos de endereços IP de origem ou endereços IP e durante um período limitado de tempo.

![Força bruta de centro de segurança](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Proteger serviços de dados

Centro de segurança inclui capacidades que o ajudam a realizar a classificação automática dos seus dados no Azure SQL. Também pode obter avaliações de vulnerabilidades em potencial em serviços do Azure SQL e armazenamento e recomendações sobre como atenuá-las.

## <a name="get-secure-faster"></a>A proteger mais rapidamente

Integração nativa no Azure (incluindo registos de política do Azure e o Azure Monitor) combinada com uma integração perfeita com outras soluções de segurança da Microsoft, como o Microsoft Cloud App Security e a proteção de ameaças avançada do Windows Defender ajudam a tornar-se de que sua solução de segurança é abrangente, bem como simples para carregar e implementar.

Além disso, pode estender a solução completa para além do Azure para cargas de trabalho em execução no noutras clouds e nos centros de dados no local.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Detetar automaticamente e carregar recursos do Azure

Centro de segurança fornece integração totalmente integrada, nativa com o Azure e Azure recursos. Isso significa que pode reúnem uma história de segurança completa que envolvem a política do Azure e as políticas do Centro de segurança incorporadas por todos os recursos do Azure e certifique-se de que tudo isso é aplicado automaticamente para os recursos detetados recentemente medida que são criados no Azure.

Recolha de registos extensa - registos a partir do Windows e Linux estão obtida no mecanismo de análise de segurança e utilizado para criar recomendações e alertas.

## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar o Centro de Segurança, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- O escalão de preço Gratuito do Centro de Segurança está ativado com a sua subscrição do Azure. Para tirar partido da gestão de segurança avançada e das capacidades de deteção de ameaças, tem de atualizar para o escalão de preço Standard. O escalão Standard pode ser tentado gratuitamente. Veja a [página de preços do Centro de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/), para obter mais informações.
- Se estiver pronto para ativar o Centro de segurança Standard agora, o [início rápido: Carregar sua subscrição do Azure para o Centro de segurança Standard](security-center-get-started.md) explica-lhe os passos.

