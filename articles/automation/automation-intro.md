---
title: Uma introdução à Automatização do Azure
description: Este artigo diz o que é a Azure Automation e como usá-lo para automatizar o ciclo de vida de infraestruturas e aplicações.
services: automation
ms.subservice: process-automation
keywords: automação azul, DSC, powershell, configuração do estado, gestão de atualizações, rastreio de alterações, DSC, inventário, livros de execução, python, gráfico
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 5e9d680e2f7d4a0ed3439d5acb6106fa1415aec1
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83712779"
---
# <a name="an-introduction-to-azure-automation"></a>Uma introdução à Automatização do Azure

A Azure Automation oferece um serviço de automação e configuração baseado na nuvem que suporta uma gestão consistente em todos os seus ambientes Azure e não-Azure. Inclui automatização de processos, gestão de configuração, gestão de atualizações, capacidades partilhadas e funcionalidades heterogéneas. A automatização confere-lhe controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos.

![Capacidades de automatização](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automatização de Processos

A Automatização de Processos em Automação Azure permite automatizar tarefas frequentes, morosas e propensas a erros de gestão de nuvens. Este serviço ajuda-o a focar-se no trabalho que acrescenta valor ao negócio. A redução de erros e o reforço da eficiência também ajudam a baixar os custos operacionais. O ambiente operacional de automatização de processos é detalhado na execução do Livro de [Ensaios na Automação Azure.](automation-runbook-execution.md)

A automatização de processos suporta a integração de serviços Azure e outros sistemas públicos necessários na implementação, configuração e gestão dos seus processos de ponta a ponta. O serviço permite-lhe autor de [livros de execução](automation-runbook-types.md) graficamente, no PowerShell, ou usando python. Ao utilizar um [Trabalhador De RaquinaDo Híbrido,](automation-hybrid-runbook-worker.md)pode unificar a gestão orquestrando em ambientes no local. [Os webhooks](automation-webhooks.md) permitem-lhe cumprir pedidos e garantir a entrega e operações contínuas, desencadeando automatização a partir de SISTEMAS ITSM, DevOps e monitorização. 

## <a name="configuration-management"></a>Gestão da Configuração

A Gestão de Configuração na Automatização Azure permite o acesso a duas funcionalidades:

* Controlo de Alterações e Inventário
* State Configuration da Automatização do Azure

### <a name="change-tracking-and-inventory"></a>Controlo de Alterações e Inventário

Alterar o rastreio e o inventário combina funções de rastreio e inventário de mudança para permitir rastrear alterações de infraestruturas de máquinas virtuais e servidores. O serviço suporta a alteração de rastreio através de serviços, daemons, software, registo e ficheiros no seu ambiente para ajudá-lo a diagnosticar alterações indesejadas e a levantar alertas. O suporte de inventário permite-lhe consultar os recursos do hóspede para obter visibilidade em aplicações instaladas e outros itens de configuração. Para mais detalhes sobre esta funcionalidade, consulte [Change Tracking e Inventory](change-tracking.md).

### <a name="azure-automation-state-configuration"></a>State Configuration da Automatização do Azure

[A Configuração do Estado da Automação Azure](automation-dsc-overview.md) é uma funcionalidade baseada na nuvem para a configuração do estado desejada pela PowerShell (DSC) que fornece serviços para ambientes empresariais. Utilizando esta funcionalidade, pode gerir os seus recursos DSC na Automação Azure e aplicar configurações a máquinas virtuais ou físicas a partir de um servidor de puxar DSC na nuvem Azure. 

## <a name="update-management"></a>Gestão de atualizações

A Azure Automation inclui a funcionalidade [Update Management](automation-update-management.md) para sistemas Windows e Linux em ambientes híbridos. A Update Management dá-lhe visibilidade na conformidade da atualização através do Azure e de outras nuvens, e no local. A funcionalidade permite-lhe criar implementações programadas que orquestram a instalação de atualizações dentro de uma janela de manutenção definida. Se uma atualização não for instalada numa máquina, pode utilizar a funcionalidade Update Management para a excluir de uma implementação.

## <a name="shared-capabilities"></a>Capacidades partilhadas

A Azure Automation oferece uma série de capacidades partilhadas, incluindo recursos partilhados, controlo de acesso baseado em papéis, agendamento flexível, integração de controlo de fontes, auditoria e marcação.

### <a name="shared-resources"></a><a name="shared-resources"></a>Recursos partilhados

A Automatização do Azure é composta por um conjunto de recursos partilhados que facilitam a automatização e configuração dos seus ambientes à escala.

* **[Horários](automation-schedules.md)** - Operações de Automação de Gatilho em horários predefinidos.
* **[Módulos](automation-integration-modules.md)** - Gerir o Azure e outros sistemas. Pode importar módulos para a conta Automation para os recursos da Microsoft, de terceiros, de comunidade e de císertos e DSC definidos sob medida.
* **[Galeria de Módulos](automation-runbook-gallery.md)** - Apoia a integração nativa com a PowerShell Gallery para que possa ver livros de execução e importá-los para a conta Automation. A galeria permite-lhe começar rapidamente a integrar e a autoria dos seus processos a partir da galeria PowerShell e do Microsoft Script Center.
* **[Pacotes Python 2](python-packages.md)** - Suporte os livros de execução Python 2 para a sua conta de Automação.
* **[Credenciais](automation-credentials.md)** - Armazene de forma segura informações sensíveis que os livros e configurações podem utilizar no tempo de execução.
* **[Conexões](automation-connections.md)** - Guarde pares de informações comuns para ligações aos sistemas. O autor do módulo define ligações em livros de execução e configurações para utilização em tempo de execução.
* **[Certificados](automation-certificates.md)** - Defina informações a utilizar na autenticação e na segurança dos recursos implantados quando acedidas por livros de execução ou configurações dSC em tempo de execução. 
* **[Variáveis](automation-variables.md)** - Mantenha o conteúdo que pode ser usado em livros de execução e configurações. Pode alterar valores variáveis sem ter de modificar nenhum dos livros ou configurações que os referenciam.

### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

A Azure Automation apoia o controlo de acesso baseado em papéis (RBAC) para regular o acesso à conta Automation e seus recursos. Para saber mais sobre a configuração do RBAC na sua conta de Automação, livros de execução e empregos, consulte o controlo de acesso baseado em funções para a [Automação Azure](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integração do controlo de origem

A Azure Automation suporta a [integração do controlo](source-control-integration.md)de fontes. Esta funcionalidade promove a configuração como código onde os livros de execução ou configurações podem ser verificados num sistema de controlo de fonte.

## <a name="heterogeneous-support-windows-and-linux"></a>Suporte heterogéneo (Windows e Linux)

A automação foi concebida para funcionar em todo o seu ambiente híbrido em nuvem e também nos seus sistemas Windows e Linux. Fornece uma forma consistente de automatizar e configurar cargas de trabalho implementadas e os sistemas operativos que as executam.

## <a name="common-scenarios-for-automation"></a>Cenários comuns para a Automatização

A Azure Automation apoia a gestão ao longo do ciclo de vida da sua infraestrutura e aplicações. Os cenários comuns incluem:

* **Escreva livros** de execução - Author PowerShell, PowerShell Workflow, graphical, Python 2 e DSC em línguas comuns. 
* **Construir e implementar recursos** - Desloque máquinas virtuais através de um ambiente híbrido utilizando livros de corridas e modelos de Gestor de Recursos Azure. Integre em ferramentas de desenvolvimento, como Jenkins e Azure DevOps.
* **Configure VMs** - Avalie e configure as máquinas Windows e Linux com configurações para a infraestrutura e aplicação.
* **Partilhar conhecimentos** - Transfira conhecimentos para o sistema sobre como a sua organização fornece e mantém cargas de trabalho. 
* **Recuperar o inventário** - Obtenha um inventário completo dos recursos implantados para direcionar, reportar e cumprir. 
* **Localizar alterações** - Identificar alterações que possam causar uma configuração errada e melhorar a conformidade operacional.
* **Monitor** - Isolar as alterações da máquina que estão a causar problemas e remediar ou aumentá-los para sistemas de gestão.
* **Proteger** - Máquinas de quarentena se forem levantados alertas de segurança. Defina requisitos no convidado.
* **Governar** - Criar RBAC para equipas. Recupere recursos não utilizados.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Preços para Automação Azure

Pode rever os preços associados à Azure Automation na página [de preços.](https://azure.microsoft.com/pricing/details/automation/)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Criar uma conta de automação](automation-quickstart-create-account.md)

