---
title: O que é o Centro de Segurança do Azure? | Microsoft Docs
description: Saiba mais sobre o Centro de Segurança do Azure, as suas capacidades principais e como funciona.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/06/2019
ms.author: v-mohabe
ms.openlocfilehash: f0ee4a1129246cedd7899056359144cab75f9475
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782443"
---
# <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?

A central de segurança do Azure é um sistema de gerenciamento de segurança de infraestrutura unificado que fortalece a postura de segurança de seus data centers e fornece proteção avançada contra ameaças em suas cargas de trabalho híbridas na nuvem, sejam elas no Azure ou não, bem como no local.

Manter seus recursos seguros é um esforço conjunto entre seu provedor de nuvem, o Azure e você, o cliente. Você precisa certificar-se de que suas cargas de trabalho estejam protegidas enquanto você passa para a nuvem e, ao mesmo tempo, quando você passa para IaaS (infraestrutura como serviço) há mais responsabilidade do cliente do que havia no PaaS (plataforma como serviço) e SaaS (software como serviço). A central de segurança do Azure fornece as ferramentas necessárias para proteger sua rede, proteger seus serviços e verificar se você está sobre sua postura de segurança.

A central de segurança do Azure aborda os três desafios de segurança mais urgentes:

-   **Alteração rápida de cargas de trabalho** – é tanto uma força quanto um desafio da nuvem. Por um lado, os usuários finais são capacitados a fazer mais. No outro, como você garante que os serviços em constante mudança que as pessoas estão usando e que a criação sejam de acordo com seus padrões de segurança e sigam as práticas recomendadas de segurança?

-   **Ataques cada vez**mais sofisticados – sempre que você executa suas cargas de trabalho, os ataques continuam sendo mais sofisticados.  Você precisa proteger suas cargas de trabalho de nuvem pública, que são, na verdade, uma carga de trabalho voltada para a Internet que pode deixá-lo ainda mais vulnerável se você não seguir as práticas recomendadas de segurança.

-   **As habilidades de segurança estão em um fornecimento** curto: o número de alertas de segurança e sistemas de alerta ultrapassa o número de administradores com o plano de fundo necessário e a experiência para garantir que seus ambientes estejam protegidos. Manter-se atualizado com os ataques mais recentes é um desafio constante, o que torna impossível permanecer em vigor, enquanto o mundo da segurança é uma primeira constante mudança.

Para ajudá-lo a se proteger contra esses desafios, a central de segurança fornece as ferramentas para:

-   **Fortaleça a postura de segurança**: A central de segurança avalia seu ambiente e permite que você compreenda o status de seus recursos, eles são seguros ou não?

-   **Proteger contra ameaças**: A central de segurança avalia suas cargas de trabalho e gera recomendações de prevenção de ameaças e alertas de detecção de ameaças.

-   **Fique seguro mais rápido**: Na central de segurança, tudo é feito na velocidade da nuvem. Como é integrado nativamente, a implantação da central de segurança é fácil, fornecendo a você o provisionamento automático e a proteção com os serviços do Azure.

## <a name="architecture"></a>Arquitetura

Como a central de segurança é parte nativa do Azure, os serviços de PaaS no Azure, incluindo Service Fabric, bancos de dados SQL e contas de armazenamento, são monitorados e protegidos pela central de segurança sem precisar de nenhuma implantação.

Além disso, a central de segurança protege servidores não Azure e máquinas virtuais na nuvem ou localmente, para servidores Windows e Linux, instalando o Microsoft Monitoring Agent neles. As máquinas virtuais do Azure são provisionadas automaticamente na central de segurança.

Os eventos coletados dos agentes e do Azure estão correlacionados no mecanismo de análise de segurança para fornecer recomendações personalizadas (tarefas de proteção), que você deve seguir para garantir que suas cargas de trabalho sejam seguras e alertas de detecção de ameaças. Você deve investigar esses alertas assim que possível para garantir que ataques mal-intencionados não estejam ocorrendo em suas cargas de trabalho.

Quando você habilita a central de segurança, a política de segurança interna à central de segurança é refletida em Azure Policy como uma iniciativa interna na categoria da central de segurança. A iniciativa interna é atribuída automaticamente a todas as assinaturas registradas da central de segurança (camadas gratuitas ou Standard). A iniciativa interna contém apenas políticas de auditoria. Para obter mais informações sobre as políticas da central de segurança no Azure Policy, consulte [trabalhando com políticas de segurança](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Reforçar a postura de segurança

A central de segurança do Azure permite reforçar sua postura de segurança. Isso significa que ele ajuda a identificar e executar as tarefas de proteção recomendadas como práticas recomendadas de segurança e implementá-las em seus computadores, serviços de dados e aplicativos. Isso inclui o gerenciamento e a imposição de suas políticas de segurança e a garantia de que as máquinas virtuais do Azure, os servidores não Azure e os serviços de PaaS do Azure estão em conformidade. A central de segurança fornece as ferramentas de que você precisa para ter uma visão geral de suas cargas de trabalho, com visibilidade focada em seu espaço de segurança de rede. 

### <a name="manage-organization-security-policy-and-compliance"></a>Gerenciar a conformidade e a política de segurança da organização

É uma segurança básica saber e certificar-se de que suas cargas de trabalho são seguras e começa com a existência de políticas de segurança personalizadas em vigor. Como todas as políticas na central de segurança são criadas sobre os controles de política do Azure, você está obtendo todo o alcance e a flexibilidade de uma **solução de política de nível mundial**. Na central de segurança, você pode definir suas políticas para executar em grupos de gerenciamento, entre assinaturas e até mesmo para um locatário inteiro.

![Dashboard Centro de Segurança](media/security-center-intro/sc-dashboard.png)

A central de segurança ajuda a **identificar assinaturas de ti de sombra**. Ao examinar as assinaturas rotuladas como **não cobertas** em seu painel, você pode saber imediatamente quando há assinaturas recém-criadas e garantir que elas sejam cobertas por suas políticas e protegidas pela central de segurança do Azure.

![Painel de política da central de segurança](media/security-center-intro/sc-policy-dashboard.png)

Os recursos de monitoramento avançados na central de segurança também permitem que você **acompanhe e gerencie a conformidade e a governança ao longo do tempo**. A **conformidade** geral fornece uma medida de quanto suas assinaturas são compatíveis com as políticas associadas à sua carga de trabalho. 

![Política da central de segurança ao longo do tempo](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Avaliações contínuas

A central de segurança descobre continuamente novos recursos que estão sendo implantados em suas cargas de trabalho e avalia se eles estão configurados de acordo com as práticas recomendadas de segurança, caso contrário, eles são sinalizados e você obtém uma lista priorizada de recomendações sobre o que Você precisa corrigir o para proteger seus computadores.

Uma das ferramentas mais poderosas que a central de segurança fornece para monitorar continuamente o status de segurança de sua rede é o **mapa de rede**. O mapa permite que você veja a topologia de suas cargas de trabalho, para que você possa ver se cada nó está configurado corretamente. Você pode ver como os nós estão conectados, o que ajuda a bloquear conexões indesejadas que podem facilitar o deslizamento de um invasor na rede.

![Mapa de rede da central de segurança](media/security-center-intro/sc-net-map.png)

A central de segurança facilita a mitigação de alertas de segurança mais uma etapa, adicionando uma **Pontuação segura**. As pontuações seguras agora estão associadas a cada recomendação que você recebe para ajudá-lo a entender a importância de cada recomendação para sua postura geral de segurança. Isso é crucial para permitir que você **priorize seu trabalho de segurança**.

![Pontuação segura da central de segurança](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Otimize e aprimore a segurança Configurando os controles recomendados

O coração do valor da central de segurança do Azure está em suas recomendações. As recomendações são adaptadas para as questões de segurança específicas encontradas em suas cargas de trabalho, e a central de segurança faz com que o administrador de segurança trabalhe para você, não apenas encontrando suas vulnerabilidades, mas fornece instruções específicas sobre como eliminá-las.

![Recomendações do Centro de Segurança](media/security-center-intro/sc-recommendations.png)

Dessa forma, a central de segurança permite não apenas definir políticas de segurança, mas aplicar padrões de configuração seguros em seus recursos.

As recomendações ajudam a reduzir a superfície de ataque em cada um de seus recursos. Isso inclui máquinas virtuais do Azure, servidores não Azure e serviços de PaaS do Azure, como contas de armazenamento e SQL e mais, onde cada tipo de recurso é avaliado de forma diferente e tem seus próprios padrões.

![Exemplo de recomendação da central de segurança](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Proteger contra ameaças

A proteção contra ameaças da central de segurança permite que você detecte e impeça ameaças na camada de IaaS (infraestrutura como serviço), servidores não Azure, bem como para PaaS (plataformas como serviço) no Azure.

A proteção contra ameaças da central de segurança inclui a análise de eliminação de cadeias de fusão, que correlaciona automaticamente os alertas em seu ambiente com base na análise do Cyber Kill Chain, para ajudá-lo a entender melhor a história de uma campanha de ataque, onde ele começou e o que tipo de impacto que ele tinha em seus recursos.



![Recomendação de ataque da central de segurança](media/security-center-intro/sc-attack-recommendation.png)

### <a name="advanced-threat-protection"></a>Proteção avançada contra ameaças

Com a central de segurança, você obtém integração nativa com a proteção avançada contra ameaças do Windows Defender pronta para uso. Isso significa que, sem nenhuma configuração, seus servidores e máquinas virtuais do Windows são totalmente integrados às recomendações e avaliações da central de segurança. A detecção avançada de ameaças também é oferecida para servidores e máquinas virtuais Linux.

Além disso, a central de segurança permite automatizar políticas de controle de aplicativos em ambientes de servidor. Os controles de aplicativo adaptáveis na central de segurança habilitam a lista de permissões do aplicativo de ponta a ponta em seus servidores Windows. Você não precisa criar as regras e violações de verificação, tudo isso é feito automaticamente para você.

### <a name="protect-paas"></a>Proteger PaaS

A central de segurança ajuda a detectar ameaças nos serviços de PaaS do Azure. Você pode detectar ameaças direcionadas aos serviços do Azure, incluindo serviço de Azure App, SQL do Azure, conta de armazenamento do Azure e mais serviços de dados. Você também pode aproveitar a integração nativa com a UEBA (análise comportamental de entidade e usuário do Microsoft Cloud App Security) para executar a detecção de anomalias nos logs de atividades do Azure.

### <a name="block-brute-force-attacks"></a>Bloquear ataques de força bruta

A central de segurança ajuda a limitar a exposição a ataques de força bruta. Ao reduzir o acesso a portas de máquina virtual, usando o acesso just-in-time à VM, você pode proteger sua rede impedindo o acesso desnecessário. Você pode definir políticas de acesso seguro em portas selecionadas, somente para usuários autorizados, intervalos de endereços IP de origem permitidos ou endereços IP e por um período limitado.

![Força bruta da central de segurança](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Proteger serviços de dados

A central de segurança inclui recursos que ajudam a executar a classificação automática de seus dados no SQL do Azure. Você também pode obter avaliações de possíveis vulnerabilidades em serviços de armazenamento e SQL do Azure e recomendações sobre como atenuá-las.

### <a name="protect-iot-and-hybrid-cloud-workloads-preview"></a>Proteger cargas de trabalho de nuvem híbrida e IoT (versão prévia)

A central de segurança do Azure para IoT (Internet das Coisas) simplifica a proteção de carga de trabalho híbrida fornecendo visibilidade e controle unificados, prevenção de ameaças adaptáveis e detecção de ameaças inteligentes e resposta entre cargas de trabalho em execução no Edge, no local, em Azure e em outras nuvens. Para obter mais informações, consulte [central de segurança do Azure para IOT (versão prévia)](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Fique seguro mais rápido

A integração nativa do Azure (incluindo logs de Azure Policy e Azure Monitor) combinada com a integração direta com outras soluções de segurança da Microsoft, como Microsoft Cloud App Security e proteção avançada contra ameaças do Windows Defender, ajuda a garantir que seu a solução de segurança é abrangente e simples de carregar e distribuir.

Além disso, você pode estender a solução completa além do Azure para cargas de trabalho em execução em outras nuvens e em data centers locais.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Descubra e integre automaticamente os recursos do Azure

A central de segurança fornece integração nativa e integrada com os recursos do Azure e do Azure. Isso significa que você pode reunir uma história de segurança completa envolvendo Azure Policy e políticas internas da central de segurança em todos os seus recursos do Azure e garantir que tudo seja aplicado automaticamente a recursos recentemente descobertos ao criá-los no Azure.

Coleta de log extensiva – os logs do Windows e do Linux são todos utilizados no mecanismo de análise de segurança e usados para criar recomendações e alertas.

## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar o Centro de Segurança, precisa de uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/free/).
- O escalão de preço Gratuito do Centro de Segurança está ativado com a sua subscrição do Azure. Para tirar partido da gestão de segurança avançada e das capacidades de deteção de ameaças, tem de atualizar para o escalão de preço Standard. A camada Standard pode ser tentada gratuitamente. Veja a [página de preços do Centro de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/), para obter mais informações.
- Se você estiver pronto para habilitar a central de segurança Standard agora [, o início rápido: Integrar sua assinatura do Azure à central de segurança](security-center-get-started.md) Standard orienta você pelas etapas.

