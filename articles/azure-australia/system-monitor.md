---
title: Monitoramento do sistema para segurança na Austrália do Azure
description: Orientação sobre como configurar o monitoramento do sistema dentro das regiões australianas para atender aos requisitos específicos da política, regulamentos e legislações do governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779314"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Monitoramento do sistema para segurança na Austrália do Azure

Ter estratégias de segurança robustas que incluem monitoramento em tempo real e avaliações de segurança rotineiras são essenciais para que você aprimore a segurança operacional diária dos seus ambientes de ti, incluindo a nuvem.

A segurança na nuvem é um esforço conjunto entre o cliente e o provedor de nuvem. Há quatro serviços que o Microsoft Azure fornece para facilitar esses requisitos com a consideração das recomendações contidas no ISM [(controles manuais de segurança de informações) do acsc (Cyber Security Centre) da Austrália](https://acsc.gov.au/infosec/ism/index.htm) , especificamente, a implementação de log de eventos centralizado, auditoria de log de eventos e avaliação e gerenciamento de vulnerabilidades de segurança. Os serviços de Microsoft Azure são:

* Centro de Segurança do Azure
* Azure Monitor
* Azure Advisor
* Política do Azure

O ACSC recomenda que você use esses serviços para dados **protegidos** . Usando esses serviços, você pode monitorar e analisar proativamente seus ambientes de ti e tomar decisões informadas sobre onde alocar melhor os recursos para melhorar sua segurança. Cada um desses serviços faz parte de uma solução combinada para fornecer a você a melhor percepção, recomendações e proteção possível.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

A [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornece um console de gerenciamento de segurança unificado que você usa para monitorar e aprimorar a segurança dos recursos do Azure e seus dados hospedados. A central de segurança do Azure inclui pontuação segura, uma pontuação baseada em uma análise do estado de configuração de práticas recomendadas do Azure Advisor e a conformidade geral do Azure Policy.

A central de segurança do Azure fornece aos clientes do Azure os seguintes recursos:

* Política de segurança, avaliação e recomendações
* Coleção e pesquisa de eventos de segurança
* Controles de acesso e aplicativo
* Detecção avançada de ameaças
* Controle de acesso de máquinas virtuais just-in-time
* Segurança híbrida

O escopo de recursos monitorados pela central de segurança do Azure pode ser expandido para incluir recursos locais com suporte em um ambiente de nuvem híbrida. Isso inclui recursos locais que estão sendo monitorados por uma versão com suporte do System Center Operations Manager.

A camada "padrão" da central de segurança também fornece controles de segurança baseados em nuvem exigidos pelo [ASD Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm). Isso inclui a lista de permissões de aplicativos e a restrição de privilégio administrativo por meio de acesso just-in-time.

### <a name="azure-monitor"></a>Azure Monitor

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) é a solução de registro em log centralizada para todos os recursos do Azure e inclui Log Analytics e Application insights. Dois tipos de dados principais são coletados de recursos do Azure: logs e métricas. Depois de coletadas no Azure Monitor, as informações de registro podem ser usadas por uma ampla gama de ferramentas e para uma variedade de finalidades.

![Descrição geral do Azure Monitor](media/overview.png)

Azure Monitor também inclui o "log de atividades do Azure". O log SActivity armazena todos os eventos de nível de assinatura que ocorreram no Azure. Ele permite que os clientes do Azure vejam as operações "quem, o que e quando" por trás dos seus recursos do Azure. O log baseado em recurso enviado para Azure Monitor e eventos do log de atividades do Azure podem ser analisados usando a linguagem de consulta Kusto incorporada. Esses logs podem ser exportados, usados para criar painéis e exibições personalizados e configurados para disparar alertas e notificações.

### <a name="azure-advisor"></a>Azure Advisor

O [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) analisa recursos do Azure com suporte, arquivos de log gerados pelo sistema e configurações de recursos atuais em sua assinatura do Azure. A análise fornecida no Azure Advisor é gerada em tempo real e baseada nas práticas recomendadas da Microsoft. Todos os recursos do Azure com suporte adicionados ao seu ambiente serão analisados e as recomendações apropriadas serão fornecidas. As recomendações do Azure Advisor são categorised em quatro categorias de práticas recomendadas:

* Segurança
* Elevada Disponibilidade
* Desempenho
* Custo

As recomendações de segurança geradas pelo Azure Advisor formam parte da análise de segurança geral fornecida pela central de segurança do Azure.

As informações coletadas pelo Azure Advisor fornecem aos administradores:

* Informações sobre a configuração de recursos que não atende às práticas recomendadas
* Diretrizes sobre ações de correção específicas a serem responsabilizadas
* Classificações que indicam quais ações de correção devem ser executadas como uma prioridade alta

### <a name="azure-policy"></a>Política do Azure

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) fornece a capacidade de aplicar regras que regem os tipos de recursos do Azure e sua configuração permitida. A política pode ser usada para controlar a criação e configuração de recursos ou pode ser usada para auditar definições de configuração em um ambiente. Esses resultados de auditoria podem ser usados para formar a base das atividades de correção. Azure Policy difere do RBAC (controle de acesso baseado em função); Azure Policy é usado para restringir os recursos e sua configuração, o RBAC é usado para restringir o acesso privilegiado aos usuários do Azure.

Se a política específica está sendo imposta ou se o efeito da política está sendo auditado, a conformidade da política é monitorada continuamente e as informações de conformidade específicas do recurso e de recursos são fornecidas aos administradores. Azure Policy dados de conformidade são fornecidos à central de segurança do Azure e a parte de formulários da Pontuação segura.

## <a name="key-design-considerations"></a>Considerações de design-chave

Ao implementar uma estratégia de log de eventos, o ISM do ACSC realça as seguintes considerações:

* Instalações de log centralizadas
* Eventos específicos a serem registrados
* Proteção do log de eventos
* Retenção do log de eventos
* Auditoria de log de eventos

Além de coletar e gerenciar logs, o ISM também recomenda a avaliação de vulnerabilidades rotineiras do ambiente de ti de uma organização.

### <a name="centralised-logging"></a>Registro em log centralizado

Qualquer solução de registro em log deve, sempre que possível, consolidar os logs capturados em um único repositório de dados. Isso não apenas reduz a complexidade operacional e impede a criação de vários silos de dados, permite que os dados coletados de várias fontes sejam analisados juntos, permitindo que todos os eventos correlacionados sejam identificados. Isso é essencial para detectar e gerenciar o escopo de quaisquer incidentes de segurança cibernéticos.

Esse requisito é atendido para todos os clientes do Azure com Azure Monitor. Essa oferta não apenas fornece um repositório de log centralizado no Azure para todos os recursos do Azure, além de permitir que você transmita seus dados para um hub de eventos do Azure. Os hubs de eventos do Azure fornecem um serviço de ingestão de dados em tempo real totalmente gerenciado. Depois que os dados de Azure Monitor são transmitidos para um hub de eventos do Azure, os dados também podem ser facilmente conectados a repositórios de SIEM (informações de segurança e gerenciamento de eventos) com suporte e outras ferramentas de monitoramento de terceiros.

A Microsoft também oferece sua própria solução SIEM nativa do Azure, Azure Sentinel. O Azure Sentinel dá suporte a uma ampla variedade de conectores de dados e pode ser usado para monitorar eventos de segurança em toda a empresa. Ao combinar os dados de conectores de [dados](https://docs.microsoft.com/azure/sentinel/connect-data-sources)com suporte, o aprendizado de máquina interno do Sentinela do Azure e a linguagem de consulta Kusto, os administradores de segurança são fornecidos com uma única solução para detecção de alertas, visibilidade de ameaças, busca proativa, e resposta a ameaças. O Sentinel também fornece um recurso de busca e bloco de anotações que permite aos administradores de segurança registrar todas as etapas realizadas como parte de uma investigação de segurança em um manual reutilizável que pode ser compartilhado em uma organização. Os administradores de segurança podem até mesmo usar a [análise de usuário](https://docs.microsoft.com/azure/sentinel/user-analytics) interna para investigar as ações de um único usuário indicado.

### <a name="logged-events-and-log-detail"></a>Eventos registrados e detalhes do log

O ISM fornece uma lista detalhada dos tipos de log de eventos que devem ser incluídos em qualquer estratégia de registro em log. Todos os logs capturados devem conter detalhes suficientes para serem de qualquer uso prático na condução de análises e investigações.

Os logs coletados no Azure se enquadram em uma das três categorias a seguir:

* **Logs de controle e gerenciamento**: Esses logs fornecem informações sobre Azure Resource Manager criar, atualizar e excluir operações.

* **Logs do plano de dados**: Eles contêm eventos gerados como parte do uso de recursos do Azure. Inclui fontes como logs de eventos do Windows, incluindo logs de aplicativos, segurança e sistema.

* **Eventos processados**: Esses eventos contêm informações sobre eventos e alertas que foram processados automaticamente em nome do cliente pelo Azure. Um exemplo de um evento processado é um alerta da central de segurança do Azure.

O monitoramento de máquinas virtuais do Azure é aprimorado pela implantação do agente de máquina virtual para Windows e Linux. Esse marcador aumenta a amplitude das informações de registro em log coletadas. A implantação desse agente pode ser configurada para ocorrer automaticamente por meio da central de segurança do Azure.

A Microsoft fornece informações detalhadas sobre os logs específicos de recursos do Azure e seus [esquemas](https://docs.microsoft.com/azure/security/fundamentals/log-audit).

### <a name="log-retention-and-protection"></a>Retenção de log e proteção

 Os logs de eventos devem ser armazenados com segurança para o período de retenção necessário. O ISM aconselha que os logs sejam retidos por um mínimo de sete anos. O Azure fornece vários meios para garantir a longa vida de seus logs coletados. Por padrão, os eventos de log do Azure são armazenados por 90 dias. Os dados de log capturados por Azure Monitor podem ser movidos e armazenados em uma conta de armazenamento do Azure, conforme necessário para retenção de longo prazo. Os logs de atividades armazenados em uma conta de armazenamento do Azure podem ser mantidos por um número definido de dias ou indefinidamente, se necessário.

As contas de armazenamento do Azure usadas para armazenar eventos de log do Azure podem se tornar com redundância geográfica e o backup pode ser feito usando o backup do Azure. Depois de capturados pelo backup do Azure, qualquer exclusão de backups que contenham logs requer aprovação administrativa e os backups marcados para exclusão ainda são mantidos por 14 dias, permitindo a recuperação. O backup do Azure permite cópias de 9999 de uma instância protegida, fornecendo mais de 27 anos de backups diários.

O controle de acesso baseado em função deve ser usado para controlar o acesso aos recursos usados para o log do Azure. Azure Monitor, as contas de armazenamento do Azure e os backups do Azure devem ser configurados com controles de acesso baseados em função para garantir a segurança dos dados contidos nos logs.

### <a name="log-auditing"></a>Auditoria de log

O valor verdadeiro dos logs é real quando eles são analisados. O uso de análises automatizadas e manuais e a familiaridade com as ferramentas disponíveis ajudará você a detectar e gerenciar violações de política de segurança organizacional e incidentes de segurança cibernéticos. O Azure Monitor fornece um conjunto avançado de ferramentas para analisar os logs coletados. O resultado dessa análise pode ser compartilhado entre sistemas, visualizados ou disseminados em vários formatos.

Os dados de log armazenados no Azure Monitor são mantidos em um espaço de trabalho Log Analytics. Todas as análises começam com uma consulta. Azure Monitor consultas são gravadas na linguagem de consulta Kusto. As consultas formam a base de todas as saídas de Azure Monitor, de painéis do Azure para regras de alerta.

![Visão geral das consultas de log do Azure](media/queries-overview.png)

A auditoria de logs pode ser aprimorada por meio do uso de soluções de monitoramento. Essas são soluções predefinidas que contêm lógica de coleta, consultas e exibições de visualização de dados. A Microsoft [fornece](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory) uma série de soluções de monitoramento e soluções adicionais de fornecedores de produtos podem ser encontradas no Azure Marketplace.

### <a name="vulnerability-assessment-and-management"></a>Avaliação e gerenciamento de vulnerabilidades

O ISM observa que a avaliação e o gerenciamento de vulnerabilidades rotineiras são essenciais. Seu ambiente de ti está evoluindo constantemente e a ameaça de segurança externa está mudando de forma inalterada. Com a central de segurança do Azure, você pode realizar avaliações de vulnerabilidade automatizadas e obter orientação sobre como planejar e executar atividades de correção.

A pontuação segura na central de segurança do Azure fornece uma lista de recomendações que, quando aplicadas, melhorará a segurança do seu ambiente. A lista é classificada pelo impacto na pontuação de segurança geral da mais alta para a mais baixa. A ordenação da lista por impacto permite que você se concentre nas recomendações de prioridade mais alta que apresentam o maior valor no aprimoramento de sua segurança.

Azure Policy também desempenha uma parte fundamental na avaliação de vulnerabilidade contínua. Os tipos de política disponíveis em Azure Policy intervalo impondo marcas e valores de recursos, para restringir as regiões do Azure nas quais os recursos podem ser criados, para bloquear totalmente a criação de determinados tipos de recursos. Um conjunto de políticas do Azure pode ser agrupado em iniciativas. As iniciativas são usadas para aplicar políticas do Azure relacionadas que, quando aplicadas em conjunto como um grupo, formam a base de um objetivo de conformidade ou de segurança específico.

Azure Policy tem uma biblioteca de definições de política interna que está crescendo constantemente. Portal do Azure também oferece a opção de criar suas próprias definições de Azure Policy personalizadas. Depois de encontrar uma política na biblioteca existente ou criar uma nova, você poderá atribuir a política aos recursos do Azure. Essas atribuições podem [](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) ser delimitadas em vários níveis na hierarquia de gerenciamento de recursos. A atribuição de política é herdada, o que significa que todos os recursos filho em um escopo recebem a mesma atribuição de política. Os recursos também podem ser excluídos da atribuição de política com escopo conforme necessário.

Todas as políticas implantadas do Azure contribuem para a pontuação segura de uma organização. Em um ambiente de alta definição, as definições de Azure Policy personalizadas podem ser criadas e implantadas para fornecer informações de auditoria adaptadas a cargas de trabalho específicas.

## <a name="getting-started"></a>Introdução

Para começar com a central de segurança do Azure e fazer uso completo de Azure Monitor, Advisor e Policy, a Microsoft recomenda as seguintes etapas iniciais:

* Habilitar a central de segurança do Azure
* Atualizar para a camada Standard
* Habilitar o provisionamento automático do Microsoft Monitoring Agent para máquinas virtuais do Azure com suporte
* Revisar, prioritiser e atenuar as recomendações e alertas de segurança no painel do centro de segurança

## <a name="next-steps"></a>Passos Seguintes

Leia [Azure Policy e plantas do Azure](azure-policy.md) para obter detalhes sobre como implementar governança e controle sobre seus recursos da Austrália do Azure para garantir a conformidade com políticas e regulamentações.
