---
title: Descrição geral da Automatização do Azure
description: Saiba como utilizar a Automatização do Azure para automatizar o ciclo de vida da infraestrutura e das aplicações.
services: automation
ms.subservice: process-automation
keywords: automação azul, DSC, powershell, configuração do estado, gestão de atualizações, rastreio de alterações, DSC, inventário, livros de execução, python, gráfico
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81010244"
---
# <a name="an-introduction-to-azure-automation"></a>Uma introdução à Automatização do Azure

Este artigo fornece uma breve descrição geral da Automatização do Azure e responde a algumas perguntas comuns. Para obter mais informações sobre as diferentes capacidades, visite as ligações disponibilizadas ao longo desta descrição geral.

## <a name="about-azure-automation"></a>Sobre a Automação Azure

A Azure Automation oferece um serviço de automação e configuração baseado na nuvem que suporta uma gestão consistente em todos os seus ambientes Azure e não-Azure. Inclui automatização de processos, gestão de configuração, gestão de atualizações, capacidades partilhadas e funcionalidades heterogéneas. A automatização confere-lhe controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos.

![Capacidades de automatização](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automatização de processos

A automatização de processos na Automatização Azure permite automatizar tarefas frequentes, morosas e propensas a erros de gestão de nuvens. Este serviço ajuda-o a focar-se no trabalho que acrescenta valor ao negócio. A redução de erros e o reforço da eficiência também ajudam a baixar os custos operacionais. O ambiente operacional de automatização de processos é detalhado na execução do Livro de [Ensaios na Automação Azure.](automation-runbook-execution.md)

A automatização de processos suporta a integração de serviços Azure e outros sistemas públicos necessários na implementação, configuração e gestão dos seus processos de ponta a ponta. O serviço permite-lhe autor de [livros de execução](automation-runbook-types.md) graficamente, no PowerShell, ou usando python. Ao utilizar um [Trabalhador De RaquinaDo Híbrido,](automation-hybrid-runbook-worker.md)pode unificar a gestão orquestrando em ambientes no local. [Os webhooks](automation-webhooks.md) permitem-lhe cumprir pedidos e garantir a entrega e operações contínuas, desencadeando automatização a partir de SISTEMAS ITSM, DevOps e monitorização. 

## <a name="configuration-management"></a>Gestão da configuração

A [configuração do estado](automation-dsc-overview.md) da Azure Automation é uma solução baseada na nuvem para a configuração do estado desejada pela PowerShell (DSC) que fornece serviços para ambientes empresariais. Utilizando esta funcionalidade, pode gerir os seus recursos DSC na Automação Azure e aplicar configurações a máquinas virtuais ou físicas a partir de um servidor de puxar DSC na nuvem Azure. Pode monitorizar e atualizar automaticamente as configurações das máquinas através de máquinas físicas e virtuais, no Windows ou linux, na nuvem ou no local. O suporte de inventário permite-lhe consultar os recursos do hóspede para obter visibilidade em aplicações instaladas e outros itens de configuração.
 
O serviço de configuração do estado Azure Automation fornece capacidades de reporte e pesquisa ricas. Pode utilizar estas funcionalidades para localizar informações detalhadas sobre o que está configurado dentro de um sistema operativo. O serviço suporta a alteração de rastreio através de serviços, daemons, software, registo e ficheiros no seu ambiente para ajudá-lo a diagnosticar alterações indesejadas e a levantar alertas. Uma característica importante relacionada é a comunicação de grandes eventos, por exemplo, eventos emitidos quando os nós se desviam das suas configurações atribuídas. 

## <a name="update-management"></a>Gestão de atualizações

A Azure Automation inclui a solução de gestão de [atualizações](automation-update-management.md) para sistemas Windows e Linux em ambientes híbridos. Com esta solução, obtém visibilidade na conformidade de atualização através de Azure e outras nuvens, e no local. A gestão da atualização permite-lhe criar implementações programadas que orquestram a instalação de atualizações dentro de uma janela de manutenção definida. Se uma atualização não for instalada numa máquina, pode utilizar funcionalidades de gestão de atualização para a excluir de uma implementação.

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

A Azure Automation permite a integração do controlo de [fontes.](source-control-integration.md) Esta funcionalidade promove a configuração como código onde os livros de execução ou configurações podem ser verificados num sistema de controlo de fonte.

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

## <a name="pricing-for-automation"></a>Preços da Automatização

Pode rever os preços associados à Azure Automation na página [de preços.](https://azure.microsoft.com/pricing/details/automation/)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma conta de automação](automation-quickstart-create-account.md)

