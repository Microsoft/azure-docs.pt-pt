---
title: Como planear uma implantação em escala de servidores ativados pelo Azure Arc
description: Saiba como permitir que um grande número de máquinas para o Azure Arc permitiu que os servidores simplificassem a configuração das capacidades essenciais de segurança, gestão e monitorização em Azure.
ms.date: 02/23/2021
ms.topic: conceptual
ms.openlocfilehash: 0e77fc00f94f2f46c60bb2c5dcecc10a4e2e3bc5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032233"
---
# <a name="planing-for-an-at-scale-deployment-of-azure-arc-enabled-servers"></a>Planar para uma implantação em escala de servidores ativados do Arco Azure

A implantação de um serviço de infraestrutura de TI ou de uma aplicação de negócios é um desafio para qualquer empresa. Para executá-lo bem e evitar quaisquer surpresas indesejáveis e custos não planeados, você precisa planejar cuidadosamente para que ele garanta que está o mais pronto possível. Para planear a implementação de servidores ativados pelo Azure Arc em escala, deve cobrir os critérios de conceção e implementação que precisam de ser cumpridos para completar com sucesso as tarefas de suporte a uma implementação em escala.

Para que a implantação decorra sem problemas, o seu plano deve estabelecer uma compreensão clara de:

* Papéis e responsabilidades.
* Inventário de servidores físicos ou máquinas virtuais para verificar se cumprem os requisitos de rede e sistema.
* O conjunto de competências e a formação necessárias para permitir a implementação e gestão em curso com sucesso.
* Critérios de aceitação e como acompanha o seu sucesso.
* Ferramentas ou métodos a utilizar para automatizar as implementações.
* Riscos identificados e planos de mitigação para evitar atrasos, perturbações, etc.
* Como evitar perturbações durante a colocação.
* Qual é o caminho da escalada quando ocorre um problema significativo?

O objetivo deste artigo é garantir que está preparado para uma implementação bem sucedida de servidores ativados pelo Azure Arc em vários servidores físicos de produção ou máquinas virtuais no seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

* As suas máquinas executam um sistema operativo suportado para o agente Máquina [Conectada.](agent-overview.md#supported-operating-systems)
* As suas máquinas têm conectividade desde a sua rede no local ou outro ambiente em nuvem até recursos em Azure, quer diretamente quer através de um servidor proxy.
* Para instalar e configurar o Arc ativou o agente da Máquina Conectada, uma conta com privilégios elevados (isto é, um administrador ou como raiz) nas máquinas.
* Para máquinas a bordo, você é membro da **função de azure connected machine onboarding.**
* Para ler, modificar e eliminar uma máquina, é membro da função de Administrador de **Recursos da Máquina Azure.**

## <a name="pilot"></a>Piloto

Antes de se deslocar a todas as máquinas de produção, comece por avaliar este processo de implantação antes de o adotar amplamente no seu ambiente. Para um piloto, identifique uma amostra representativa de máquinas que não sejam críticas para a capacidade das suas empresas de realizarem negócios. Você vai querer ter certeza de dar tempo suficiente para executar o piloto e avaliar o seu impacto: recomendamos um mínimo de 30 dias.

Estabeleça um plano formal que descreva o âmbito e os detalhes do piloto. Segue-se uma amostra do que um plano deve incluir para ajudar a começar.

* Objetivos - Descreve os condutores empresariais e técnicos que levaram à decisão de que um piloto é necessário.
* Critérios de seleção - Especifica os critérios utilizados para selecionar quais os aspetos da solução que serão demonstrados através de um piloto.
* Âmbito - Descreve o âmbito do piloto, que inclui, mas não se limitando a componentes de solução, horário previsto, duração do piloto e número de máquinas a atingir.
* Critérios e métricas de sucesso - Definir os critérios de sucesso do piloto e as medidas específicas utilizadas para determinar o nível de sucesso.
* Plano de formação - Descreve o plano para engenheiros de sistemas de formação, administradores, etc. que são novos no Azure e que prestam serviços durante o piloto.
* Plano de transição - Descreve a estratégia e os critérios utilizados para orientar a transição de piloto para produção.
* Reversão - Descreve os procedimentos para reverter um piloto para estado de pré-implantação.
* Riscos - Enumerar todos os riscos identificados para a realização do piloto e associados à implantação da produção.

## <a name="phase-1-build-a-foundation"></a>Fase 1: Construir uma fundação

Nesta fase, os engenheiros ou administradores de sistemas permitem que as funcionalidades centrais nas suas organizações Azure iniciem a fundação antes de permitirem que as suas máquinas para gestão por servidores ativados pela Arc e outros serviços Azure.

|Tarefa |Detalhes |Duração |
|-----|-------|---------|
| [Criar um grupo de recursos](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Um grupo de recursos dedicado para incluir apenas servidores ativados pela Arc e centralizar a gestão e monitorização destes recursos. | Uma hora |
| Aplicar [Tags](../../azure-resource-manager/management/tag-resources.md) para ajudar a organizar máquinas. | Avaliar e desenvolver uma estratégia de [marcação](/cloud-adoption-framework/decision-guides/resource-tagging/) alinhada com TI que possa ajudar a reduzir a complexidade de gerir os seus servidores ativados pelo Arco e simplificar as decisões de gestão. | Um dia |
| Conceber e implementar [registos de monitores Azure](../../azure-monitor/logs/data-platform-logs.md) | Avalie [considerações de design e implementação](../../azure-monitor/logs/design-logs-deployment.md) para determinar se a sua organização deve usar um espaço de trabalho existente ou implementar outro espaço de trabalho do Log Analytics para armazenar dados de registo recolhidos de servidores e máquinas híbridos. <sup>1</sup> | Um dia |
| Desenvolver um plano de governação [da Política Azure](../../governance/policy/overview.md) | Determine como implementará a governação de servidores e máquinas híbridos no âmbito de subscrição ou grupo de recursos com a Política Azure. | Um dia |
| Configure [controlo de acesso baseado em funções](../../role-based-access-control/overview.md) (RBAC) | Desenvolver um plano de acesso para controlar quem tem acesso a gerir servidores ativados pela Arc e capacidade de visualizar os seus dados a partir de outros serviços e soluções Azure. | Um dia |
| Identificar máquinas com agente Log Analytics já instalado | Executar a seguinte consulta de registo no [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) para apoiar a conversão das implementações de agentes de Log Analytics existentes para o agente gerido por extensões:<br> Heartbeat <br> &#124; onde o Tempogerado > (30d) <br> &#124; onde o ResourceType == "máquinas" e (ComputerEnvironment == "Non-Azure") <br> &#124; resumido por Computador, ResourceProvider, ResourceType, ComputerEnvironment | Uma hora |

<sup>1</sup> Uma consideração importante como parte da avaliação do seu design de espaço de trabalho Log Analytics, é a integração com a Azure Automation em apoio à sua funcionalidade de Gestão de Atualização e Rastreio e Inventário de Alterações, bem como do Azure Security Center e do Azure Sentinel. Se a sua organização já tem uma conta Automation e ativou as suas funcionalidades de gestão ligadas a um espaço de trabalho Log Analytics, avalie se pode centralizar e agilizar as operações de gestão, bem como minimizar os custos, utilizando os recursos existentes em vez de criar uma conta duplicada, espaço de trabalho, etc.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fase 2: Implementar servidores ativados pelo Arco

Em seguida, adicionamos à fundação estabelecida na fase 1, preparando a implantação, e realizando a instalação do agente.

|Tarefa |Detalhes |Duração |
|-----|-------|---------|
| Descarregue o script de instalação pré-definido | Reveja e personalize o script de instalação pré-definido para a implantação à escala do agente da Máquina Conectada para suportar os seus requisitos de implementação automatizados.<br><br> Amostra de recursos de bordo à escala:<br><br> <ul><li> [Script de implementação básica em escala](onboard-service-principal.md)</ul></li> <ul><li>[VMware vMware vS de vMware vS de vMware do Windows à escala](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_win.md)</ul></li> <ul><li>[PMware vSMware vSphere Linux VMs à escala](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_linux.md)</ul></li> <ul><li>[Instâncias AWS EC2 de bordo à escala utilizando ansible](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/aws_scale_ansible.md)</ul></li> <ul><li>[Implementação em escala utilizando o remoting PowerShell](https://docs.microsoft.com/azure/azure-arc/servers/onboard-powershell) (apenas windows)</ul></li>| Um ou mais dias dependendo dos requisitos, processos organizacionais (por exemplo, Gestão de Alterações e Libertação) e método de automatização utilizado. |
| [Criar um principal de serviço](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Crie um principal de serviço para ligar máquinas não interativamente usando a Azure PowerShell ou a partir do portal.| Uma hora |
| Implemente o agente 'Máquina Conectada' nos servidores e máquinas-alvo |Utilize a sua ferramenta de automatização para implantar os scripts nos seus servidores e conectá-los ao Azure.| Um ou mais dias dependendo do seu plano de libertação e se seguir um lançamento faseado. |

## <a name="phase-3-manage-and-operate"></a>Fase 3: Gerir e operar

A fase 3 vê administradores ou engenheiros de sistema que permitem automatizar tarefas manuais para gerir e operar o agente da Máquina Conectada e a máquina durante o seu ciclo de vida.

|Tarefa |Detalhes |Duração |
|-----|-------|---------|
|Criar um alerta de saúde de recursos |Se um servidor parar de enviar batimentos cardíacos para Azure por mais de 15 minutos, pode significar que está offline, a ligação de rede foi bloqueada ou o agente não está em funcionamento. Desenvolva um plano para como irá responder e investigar estes incidentes e use [alertas de Saúde de Recursos](../..//service-health/resource-health-alert-monitor-guide.md) para ser notificado quando começarem.<br><br> Especificar o seguinte ao configurar o alerta:<br> Tipo de **recurso**  =  **Servidores ativados Azure Arc**<br> **Estado atual do recurso**  =  **Indisponível**<br> **Estado dos recursos**  =  anteriores **Disponível** | Uma hora |
|Criar um alerta de Conselheiro Azure | Para obter a melhor experiência e as correções mais recentes de segurança e bugs, recomendamos manter o agente de servidores ativado pelo Arco Azure atualizado. Agentes desatualizados serão identificados com um [alerta de Azure Advisor](../../advisor/advisor-alerts-portal.md).<br><br> Especificar o seguinte ao configurar o alerta:<br> Tipo de **recomendação**  =  **Upgrade para a versão mais recente do Azure Connected Machine Agent** | Uma hora |
|[Atribua as políticas Azure](../../governance/policy/assign-policy-portal.md) ao seu âmbito de subscrição ou grupo de recursos |Atribua o **Enable Azure Monitor para** a [política](../../azure-monitor/vm/vminsights-enable-policy.md) de VMs (e outros que atendam às suas necessidades) para o âmbito de subscrição ou grupo de recursos. A Azure Policy permite-lhe atribuir definições de política que instalam os agentes necessários para o Azure Monitor para VMs em todo o seu ambiente.| Varia |
|[Ativar a Gestão de Atualização para os servidores ativados pelo Arco](../../automation/update-management/enable-from-automation-account.md) |Configure Update Management in Azure Automation para gerir as atualizações do sistema operativo para as suas máquinas virtuais Windows e Linux registadas com servidores ativados pelo Arc. | 15 minutos |

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

* Saiba como simplificar a implementação com outros serviços Azure, como [a Azure](../../automation/automation-dsc-overview.md) Automation State Configuration e outras [extensões de VM](manage-vm-extensions.md)suportadas.