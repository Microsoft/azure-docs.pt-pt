---
title: Descrição Geral da Automatização do Azure
description: Saiba como utilizar a Automatização do Azure para automatizar o ciclo de vida da infraestrutura e das aplicações.
services: automation
ms.subservice: process-automation
keywords: automatização do azure, DSC, powershell, desired state configuration, gestão de atualizações, controlo de alterações, inventário, runbooks, python, gráfico
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 3359d99d7e20bbced8950171fa34592fd2612500
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930404"
---
# <a name="an-introduction-to-azure-automation"></a>Uma introdução à Automatização do Azure

A Automatização do Azure fornece um serviço de automatização e configuração com base na cloud que assegura uma gestão consistente em todos os seus ambientes, sejam estes do Azure ou outros. Este serviço é composto por funcionalidades de automatização de processos, gestão de atualizações e configuração. A Automatização do Azure fornece controlo total durante a implementação, as operações e a desativação de cargas de trabalho e recursos.
Este artigo fornece uma breve descrição geral da Automatização do Azure e responde a algumas perguntas comuns. Para obter mais informações sobre as diferentes capacidades, visite as ligações disponibilizadas ao longo desta descrição geral.

## <a name="azure-automation-capabilities"></a>Capacidades da Automatização do Azure

![Capacidades da descrição geral da Automatização](media/automation-overview/automation-overview.png)

### <a name="process-automation"></a>Automatização de processos

A Automatização do Azure permite automatizar tarefas de gestão da cloud frequentes, demoradas e propensas a erros. Com esta automatização, pode concentrar os seus esforços em áreas que acrescentam valor ao seu negócio. Ao reduzir os erros e ao aumentar a eficiência, também o ajuda a reduzir os custos operacionais. Pode integrar os serviços do Azure e outros sistemas públicos necessários na implementação, configuração e gestão dos seus processos ponto a ponto. O serviço permite [criar runbooks](automation-runbook-types.md) em gráficos, no PowerShell ou em Python. Se utilizar uma função de trabalho de runbook híbrida, poderá unificar a gestão através da orquestração dos diversos ambientes locais. Os [webhooks](automation-webhooks.md) proporcionam uma forma de satisfazer os pedidos e assegurar as operações e a entrega contínua ao acionarem a automatização a partir do ITSM, do DevOps e de sistemas de monitorização.

### <a name="configuration-management"></a>Gestão de configurações

O [Desired State Configuration](automation-dsc-overview.md) (DSC) da Automatização do Azure é uma solução com base na cloud para o PowerShell DSC que fornece serviços necessários para ambientes empresariais. Faça a gestão dos seus recursos de DSC na Automatização do Azure e aplique configurações nas máquinas virtuais ou físicas de um Servidor de Solicitação do DSC na cloud do Azure. O serviço fornece relatórios detalhados que o informam acerca de eventos importantes como, por exemplo, quando há um desvio dos nós em relação à respetiva configuração atribuída. Pode monitorizar e atualizar automaticamente a configuração dos computadores em máquinas virtuais e físicas, Windows ou Linux, na cloud ou no local.

Pode obter o inventário dos recursos existentes no convidado de modo a ter uma perceção visual das aplicações instaladas e outros itens de configuração. São disponibilizadas capacidades avançadas de pesquisa e de criação de relatórios para encontrar rapidamente informações detalhadas que ajudem a compreender o que está configurado no sistema operativo. Pode controlar as alterações nos diversos serviços, daemons, software, registo e ficheiros para identificar rapidamente as causas que podem estar na origem de problemas. Além disso, o DSC pode ajudar no diagnóstico e emitir alertas quando ocorrem alterações indesejáveis no seu ambiente.

### <a name="update-management"></a>Gestão de atualizações

Atualize os sistemas Windows e Linux em todos os ambientes híbridos com a Automatização do Azure. Dessa forma, obtém uma perceção visual da conformidade das atualizações no Azure, no local e noutras clouds. Pode criar implementações agendadas para orquestrar a instalação das atualizações durante uma janela de manutenção definida. Se não for suposto instalar uma determinada atualização num computador, pode excluir essa atualização de uma implementação.

### <a name="shared-resources"></a>Recursos compartilhados

A Automatização do Azure é composta por um conjunto de recursos partilhados que facilitam a automatização e configuração dos seus ambientes à escala.

* **[Agendas](automation-schedules.md)** – São utilizadas no serviço para acionar a automatização em alturas predefinidas.
* **[Módulos](automation-integration-modules.md)** -módulos são usados para gerenciar o Azure e outros sistemas. Importe para a conta de automação da Microsoft, de terceiros, da Comunidade ou de cmdlets definidos personalizados e recursos de DSC.
* **[Galeria de módulos](automation-runbook-gallery.md)** – integração nativa com o Galeria do PowerShell para exibir runbooks e importá-los para a conta de automação.
* **[Pacotes python 2](python-packages.md)** – Adicione pacotes python 2 à sua conta de automação para usar em seus Runbooks do Python.
* **[Credenciais](automation-credentials.md)** – Armazene em segurança informações confidenciais que podem ser utilizadas por runbooks e configurações em runtime.
* **[Ligações](automation-connections.md)** – Armazene um par nome/valor com informações comuns ao ligar a sistemas em recursos de ligação. As ligações são definidas pelo autor do módulo para utilização em runtime nos runbooks e configurações.
* **[Certificados](automation-certificates.md)** – Armazene e disponibilize em runtime, para que possam ser utilizados na autenticação e proteção de recursos implementados.
* **[Variáveis](automation-variables.md)** – Forneça um meio de manter conteúdo que pode ser utilizado em vários runbooks e configurações. Pode alterar os valores sem ter de modificar quaisquer runbooks e configurações que os referenciam.

### <a name="source-control-integration"></a>Integração do controlo de origem

A automação do Azure tem a capacidade de [se integrar ao controle do código-fonte](source-control-integration.md) , que promove a configuração como código, onde runbooks ou configurações podem ser verificados em um sistema de controle do código-fonte

### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

A automação do Azure dá suporte ao controle de acesso baseado em função para controlar o acesso à conta de automação e seus recursos, para saber mais sobre o RBAC de configuração em sua conta de automação, runbooks e trabalhos, consulte [controle de acesso baseado em função para a automação do Azure](automation-role-based-access-control.md).

### <a name="windows-and-linux"></a>Windows e Linux

A Automatização do Azure foi concebida para funcionar no seu ambiente cloud híbrido, bem como para os sistemas Windows e Linux. Além disso, fornece um método consistente para automatizar e configurar as cargas de trabalho implementadas e o sistema operativo em que são executadas.

### <a name="community-gallery"></a>Galeria da comunidade

Procure runbooks e módulos na [galeria de Automatização](automation-runbook-gallery.md) para começar rapidamente a integrar e criar os seus processos a partir da galeria do PowerShell e do Centro de Scripts da Microsoft.

## <a name="common-scenarios-for-automation"></a>Cenários comuns para a Automatização

A Automatização do Azure gere o ciclo de vida da infraestrutura e das aplicações. Transfira conhecimentos para o sistema relativamente à forma como a organização fornece e mantém as cargas de trabalho. Crie processos em linguagens comuns, como PowerShell, Desired State Configuration, Python e runbooks gráficos. Obtenha um inventário completo dos recursos implementados para efeitos de filtragem, criação de relatórios e conformidade. Identifique as alterações que podem causar erros de configuração e melhore a conformidade operacional.

* **Criar/implementar recursos** – Implemente VMs num ambiente híbrido com Runbooks e modelos do Azure Resource Manager. Promova a integração com ferramentas de desenvolvimento, como o Jenkins e o Azure DevOps.
* **Configurar VMs** – Avalie e configure computadores Windows e Linux com a configuração pretendida para a infraestrutura e a aplicação.
* **Monitorizar** – Identifique alterações nos computadores que estejam a causar problemas e proceda à respetiva remediação ou escalamento para os sistemas de gestão.
* **Proteger** – Coloque uma VM em quarentena se for gerado um alerta de segurança. Defina requisitos no convidado.
* **Governar** – Configure o controlo de acesso baseado em funções para equipas. Recupere recursos não utilizados.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Preços da Automatização

Reveja o preço da Automatização do Azure na página de [preços](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma conta de automatização](automation-quickstart-create-account.md)

