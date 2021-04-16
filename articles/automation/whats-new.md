---
title: Novidades na Azure Automation
description: Atualizações significativas para a Azure Automation atualizadas todos os meses.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 04/09/2021
ms.custom: references_regions
ms.openlocfilehash: f8b4d6965a8a1f046fd2459ce9fe5cce8ea45443
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531072"
---
# <a name="whats-new-in-azure-automation"></a>O que há de novo na Azure Automation?

A Azure Automation recebe melhorias contínuas. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros

Esta página é atualizada mensalmente, por isso revisite-a regularmente.

## <a name="march-2021"></a>março de 2021

### <a name="new-azure-automation-built-in-policies"></a>Novas políticas incorporadas da Azure Automation

**Tipo:** Nova funcionalidade

A Azure Automation adicionou 5 novas políticas incorporadas:

- As contas de automação devem desativar o acesso à rede pública,
- As contas da Azure Automation devem usar chaves geridas pelo cliente para encriptar dados em repouso
- Configurar contas da Azure Automation para desativar o acesso à rede pública
- Configure ligações de ponto final privado nas contas da Azure Automation
- Devem ser ativadas ligações privadas de ponto final nas Contas de Automação.

Consulte o artigo [de referência da política](./policy-reference.md) para mais detalhes.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Suporte à Automação e Configuração do Estado declarado GA no Sul da Índia

**Tipo:** Nova funcionalidade

Utilize capacidades de automatização de processos e de configuração do Estado no Sul da Índia. Leia o [anúncio](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) para mais informações.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Suporte para Automação e Configuração do Estado declarado GA no Reino Unido West

**Tipo:** Nova funcionalidade

Utilize capacidades de automatização de processos e de configuração do Estado no Reino Unido Oeste. Para mais informações, leia [o anúncio.](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/)

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Suporte à Automação e Configuração do Estado declarado GA na UAE Central

**Tipo:** Nova funcionalidade

Utilize capacidades de automatização de processos e de configuração do Estado na UAE Central. Leia o [anúncio](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) para mais informações.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2--norway-west-and-france-south"></a>Suporte para Automação e Configuração estatal disponível na Austrália Central 2 , Noruega West e França Sul

**Tipo:** Nova funcionalidade

Consulte mais informações na [página de residência dos Dados](https://azure.microsoft.com/global-infrastructure/data-residency/) selecionando a geografia para cada região.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Novos scripts adicionados para instalar trabalhador híbrido no Windows e Linux

**Tipo:** Nova funcionalidade

Dois novos scripts foram adicionados ao [repositório Azure Automation GitHub,](https://github.com/azureautomation) abordando um dos cenários-chave da Azure Automation de criar um Trabalhador de Runbook Híbrido num Windows ou numa máquina Linux. O script cria um novo VM ou utiliza um existente, cria um espaço de trabalho Log Analytics se necessário, instala o agente Log Analytics para Windows ou Log Analytics para o Linux, e regista a máquina no espaço de trabalho Do Log Analytics. O script windows chama-se **Create Automation Windows HybridWorker** e o script Linux é **Create Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Invoque o runbook através de um webhook de modelo do Azure Resource Manager

**Tipo:** Nova funcionalidade

Consulte [um webhook a partir de um modelo ARM](./automation-webhooks.md#use-a-webhook-from-an-arm-template) para obter mais detalhes.

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>A Azure Update Management agora suporta Centos 8.x, Red Hat Enterprise Linux Server 8.x e SUSE Linux Enterprise Server 15

**Tipo:** Nova funcionalidade

Consulte a [lista completa](./update-management/overview.md#supported-operating-systems) dos sistemas operativos Linux suportados para obter mais detalhes.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Apoio à residência de dados na região para o Brasil Sul e Sudeste Asiático 

**Tipo:** Nova funcionalidade

Em todas as regiões, com exceção do Brasil Sul e Sudeste Asiático, os dados da Azure Automation são armazenados numa região diferente (região emparelhada Azure) para fornecer continuidade de negócios e recuperação de desastres (BCDR). Apenas para as regiões do Brasil e do Sudeste Asiático, armazenamos agora dados da Azure Automation na mesma região para acomodar os requisitos de residência de dados para estas regiões. Consulte [a geo-replicação na Azure Automation](./automation-managing-data.md#geo-replication-in-azure-automation) para obter mais detalhes.

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Apoio à Automação e Configuração estatal declarado GA no Japão Ocidental

**Tipo:** Nova funcionalidade

Disponibilidade de conta de automação e configuração do Estado na região oeste do Japão. Para mais informações, leia [o anúncio.](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/)

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Introduziu a conformidade personalizada da Política Azure para impor a execução de runbook no Trabalhador Híbrido

**Tipo:** Nova funcionalidade

Você pode usar a nova regra de conformidade da Política Azure para permitir a criação de empregos, webhooks e horários de trabalho para funcionar apenas em grupos operários híbridos.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Atualização da disponibilidade de gestão nas regiões leste dos EUA, França Central e Norte da Europa

**Tipo:** Nova funcionalidade

A funcionalidade de Gestão de Atualização de Automação está disponível nas regiões leste dos EUA, França Central e Norte da Europa. Consulte [o mapeamento da região suportada](how-to/region-mappings.md) para atualizações da documentação que reflita esta alteração.

## <a name="january-2021"></a>Janeiro de 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Apoio à Automação e Configuração do Estado declarado GA na Suíça Oeste

**Tipo:** Nova funcionalidade

Disponibilidade de conta de automação e configuração do Estado na região oeste da Suíça. Para mais informações, leia o [anúncio.](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/)

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Adicionado script Python 3 para importar módulo com várias dependências

**Tipo:** Nova funcionalidade

O script está disponível para download a partir do nosso [repositório GitHub.](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py) 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Suporte de função do trabalhador híbrido runbook para centos 8.x/RHEL 8.x/SLES 15

**Tipo.** Nova funcionalidade

A funcionalidade Hybrid Runbook Worker suporta distribuições CentOS 8.x, REHL 8.x e SLES 15 para apenas automatização de processos em Trabalhadores De Runbook Híbridos. Consulte [sistemas operativos suportados](automation-linux-hrw-install.md#supported-linux-operating-systems) para obter atualizações da documentação para refletir estas alterações.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Disponibilidade de gestão e de rastreio de alterações na Austrália Leste, Ásia Oriental, Eua Ocidental e Centro dos EUA

**Tipo:** Nova funcionalidade

A conta de automação, o Change Tracking and Inventory e a Update Management estão disponíveis nas regiões do Leste, Leste da Ásia, Eua Ocidental e Central dos EUA. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Introduzido pré-visualização pública de livros python 3 na nuvem do governo dos EUA

**Tipo:** Nova funcionalidade A Azure Automation introduz suporte público de pré-visualização da execução de livros de nuvem e híbrido Python 3 em regiões de nuvem do governo dos EUA. Para mais informações, consulte o [anúncio.](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/)

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Os runbooks da Azure Automation mudaram-se do TechNet Script Center para o GitHub

**Tipo:** Plano de mudança

O TechNet Script Center está a reformar-se e todos os livros de corridas alojados na galeria Runbook foram transferidos para a nossa [organização Automation GitHub.](https://github.com/azureautomation) Para mais informações, leia [os Runbooks da Azure Automation movendo-se para GitHub.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="december-2020"></a>Dezembro de 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation and Update Management Private Link GA

**Tipo:** Nova funcionalidade

Suporte de Azure Automation and Update Management anunciado como GA para nuvens globais e governamentais do Azure. A Azure Automation permitiu o suporte private link para garantir a execução de um runbook numa função de trabalhador híbrido, utilizando a Gestão de Atualização para corrigir máquinas, invocando um runbook através de um webhook, e usando o serviço de Configuração do Estado para manter as suas máquinas reclamar. Para mais informações, leia o suporte para [Azure Automation Private Link](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation classificado como Grau C certificado em Acessibilidade

**Tipo:** Nova funcionalidade

As funcionalidades de acessibilidade dos produtos Microsoft ajudam as agências a abordar os requisitos globais de acessibilidade. Na página de [anúncio do blog,](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) procure a **Azure Automation** para ler o relatório de conformidade de Acessibilidade para o serviço Demôm automação.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Suporte para Automação e Configuração Estatal GA em UAE Norte

**Tipo:** Nova funcionalidade

Disponibilidade de conta de automação e configuração do Estado na região norte dos Emirados Árabes Unidos. Para mais informações, leia [o anúncio.](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Suporte para Automação e Configuração Estatal GA na Alemanha West Central

**Tipo:** Nova funcionalidade

Disponibilidade de conta de automação e configuração do Estado na região oeste da Alemanha. Para mais informações, leia [o anúncio.](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)

### <a name="dsc-support-for-oracle-6-and-7"></a>Suporte DSC para o Oráculo 6 e 7

**Tipo:** Nova funcionalidade

Gerir as máquinas Oracle Linux 6 e 7 com Configuração do Estado de Automação. Consulte [os distros do Linux suportados](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) para obter atualizações da documentação para refletir estas alterações.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Pré-visualização pública para livros python3 em Automação

**Tipo:** Nova funcionalidade

A Azure Automation suporta agora a execução de livros de nuvem e híbrido Python 3 em pré-visualização pública em todas as regiões da nuvem global de Azure. Consulte o [anúncio](para https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) mais detalhes.

## <a name="november-2020"></a>Novembro de 2020

### <a name="dsc-support-for-ubuntu-1804"></a>Suporte DSC para Ubuntu 18.04

**Tipo:** Nova funcionalidade

Consulte [Os Distros Linux suportados](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) para atualizações na documentação que reflita estas alterações.

## <a name="october-2020"></a>Outubro de 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Suporte para Automação e Configuração Estatal GA na Suíça Norte

**Tipo:** Nova funcionalidade

Disponibilidade de conta de automação e configuração do Estado na Suíça Norte. Para mais informações, leia [o anúncio.](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Apoio à Automação e Configuração do Estado GA no Sudeste Do Brasil

**Tipo:** Nova funcionalidade

Conta de automação e disponibilidade de Configuração do Estado no Sudeste do Brasil. Para mais informações, leia [o anúncio.](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)

### <a name="update-management-availability-in-south-central-us"></a>Disponibilidade de gestão de atualização em South Central US

**Tipo:** Nova funcionalidade

Mapeamento da região da Azure Automation atualizado para apoiar a funcionalidade de Gestão de Atualização na região sul-americana. Consulte [o mapeamento da região suportada](how-to/region-mappings.md#supported-mappings) para obter atualizações da documentação para refletir esta alteração.

## <a name="september-2020"></a>Setembro de 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>VMs de início/paragem durante os runbooks fora de horas atualizados para utilizar módulos Azure Az

**Tipo:** Nova funcionalidade

Os livros de execução Start/Stop VM foram atualizados para utilizar módulos Az no lugar dos módulos Azure Resource Manager. Consulte [VMs de início/paragem durante a](automation-solution-vm-management.md) visão geral fora de horas para obter atualizações da documentação para refletir estas alterações.

## <a name="august-2020"></a>Agosto de 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Publicado a extensão do DSC para apoiar O Arco de Azure

**Tipo:** Nova funcionalidade

Utilize a Configuração do Estado da Automação Azure para armazenar centralmente as configurações e manter o estado desejado das máquinas híbridas conectadas ativadas através da extensão DSC VM ativada pelos servidores Azure Arc. Para obter mais informações, leia a visão geral das [extensões VM ativadas do Arc.](../azure-arc/servers/manage-vm-extensions.md)

### <a name="july-2020"></a>Julho de 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Introduzido pré-visualização pública do suporte de ligações privadas na Automação

**Tipo:** Nova funcionalidade

Utilize o Azure Private Link para ligar de forma segura redes virtuais à Azure Automation utilizando pontos finais privados. Para mais informações, leia o [anúncio.](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Suporte do trabalhador do runbook híbrido para o Windows Server 2008 R2

**Tipo:** Nova funcionalidade

Automation Hybrid Runbook Worker suporta o sistema operativo Windows Server 2008 R2. Consulte [sistemas operativos suportados](automation-windows-hrw-install.md#supported-windows-operating-system) para obter atualizações da documentação para refletir estas alterações.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Suporte de gestão de atualização para Windows Server 2008 R2

**Tipo:** Nova funcionalidade

A Atualização Management suporta a avaliação e correção do sistema operativo Windows Server 2008 R2. Consulte [sistemas operativos suportados](update-management/overview.md#clients) para obter atualizações da documentação para refletir estas alterações.

### <a name="automation-diagnostic-logs-schema-update"></a>Atualização de esquema de registos de diagnóstico de automatização

**Tipo:** Nova funcionalidade

Alterou o esquema dos dados de registo da Azure Automation no serviço Log Analytics. Para saber mais, consulte [os dados de trabalho da Forward Azure Automation para os registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse suporta gestão de atualização de automação

**Tipo:** Nova funcionalidade

O Azure Lighthouse permite a gestão de recursos delegada com a Gestão de Atualização para prestadores de serviços e clientes. Leia mais [aqui](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Junho de 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Disponibilidade de Gestão de Automação e Atualização na região do Gov Arizona dos EUA

**Tipo:** Nova funcionalidade

A conta de automação e a Gestão de Atualização estão disponíveis no Us Gov Arizona. Para mais informações, consulte [o anúncio.](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/)

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Script de runbook híbrido de bordo atualizado para usar módulos Az

**Tipo:** Nova funcionalidade

O New-OnPremiseHybridWorker runbook foi atualizado para suportar os módulos Az. Para mais informações, consulte o pacote na [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Disponibilidade de gestão de atualização na China East 2

**Tipo:** Nova funcionalidade

Mapeamento da região da Azure Automation atualizado para apoiar a funcionalidade de Gestão de Atualização na região do China East 2. Consulte [o mapeamento da região suportada](how-to/region-mappings.md#supported-mappings) para obter atualizações da documentação para refletir esta alteração.

## <a name="may-2020"></a>Maio de 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Registos DNS do serviço de automação atualizado de URLs específicos da região para urls específicos da conta de automação

**Tipo:** Nova funcionalidade

Os registos DNS da Azure Automation foram atualizados para suportar links privados. Para mais informações, leia o [anúncio.](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Capacidade adicional para manter os scripts de Automação e DSC encriptados por padrão

**Tipo:** Nova funcionalidade

Além de melhorar a segurança dos ativos, os guiões e os scripts DSC também são encriptados para aumentar a segurança da Azure Automation.

## <a name="april-2020"></a>Abril de 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Aposentadoria da tarefa de vigilante da Automação

**Tipo:** Plano de mudança

A azure Logic Apps é agora a forma recomendada e suportada de monitorizar eventos, agendar tarefas recorrentes e desencadear ações. Não haverá mais investimentos na funcionalidade de tarefa do Observador. Para saber mais, consulte [Agendar e executar tarefas automáticas recorrentes com Aplicações Lógicas.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

## <a name="march-2020"></a>Março de 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Suporte ao isolamento computacional do Nível de Impacto 5 (IL5) na nuvem comercial e governamental do Azure

**Tipo:**

O Azure Automation Hybrid Runbook Worker pode ser usado no Governo Azure para suportar cargas de trabalho de nível de impacto 5. Para saber mais, consulte a nossa [documentação.](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)

## <a name="february-2020"></a>Fevereiro de 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Suporte introduzido para tags de serviço de rede virtual Azure

**Tipo:** Nova funcionalidade

O suporte de automatização das etiquetas de serviço permite ou nega o tráfego para o serviço de Automação, para um subconjunto de cenários. Para saber mais, consulte a [documentação.](automation-hybrid-runbook-worker.md#service-tags)

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Ativar suporte TLS 1.2 para o serviço de Automação Azure

**Tipo:** Plano de mudança

A Azure Automation suporta totalmente o TLS 1.2 e todas as chamadas dos clientes (através de webhooks, nóns DSC e trabalhador híbrido). Os TLS 1.1 e TLS 1.0 continuam a ser suportados para retrocompatibilidade com clientes mais velhos até que os clientes normalizem e migram totalmente para tLS 1.2.

## <a name="january-2020"></a>Janeiro de 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Introduzido Pré-visualização pública das chaves geridas pelo cliente para a Azure Automation

**Tipo:** Nova funcionalidade

Os clientes podem gerir e proteger a encriptação dos ativos da Azure Automation utilizando as suas próprias chaves geridas. Para obter mais informações, consulte [a utilização das chaves geridas pelo cliente.](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Aposentadoria da Azure Service Management (ASM) REST APIs para Azure Automation

**Tipo:** Aposentar-se

AZure Service Management (ASM) REST APIs for Azure Automation será reformado e deixará de ser suportado após 30 de janeiro de 2020. Para saber mais, veja o [anúncio.](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)

## <a name="next-steps"></a>Passos seguintes

Se quiser contribuir para a documentação da Azure Automation, consulte o [Guia de Contribuintes docs](/contribute/).
