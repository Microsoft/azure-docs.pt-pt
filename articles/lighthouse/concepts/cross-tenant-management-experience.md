---
title: Experiências de gestão entre inquilinos
description: A gestão de recursos delegados Azure permite uma experiência de gestão de inquilinos cruzados.
ms.date: 01/26/2021
ms.topic: conceptual
ms.openlocfilehash: 9af90e10d889c3d30375e001891ce0dc70767ab4
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881406"
---
# <a name="cross-tenant-management-experiences"></a>Experiências de gestão entre inquilinos

Como prestador de serviços, pode utilizar [o Azure Lighthouse](../overview.md) para gerir recursos para vários clientes dentro do seu próprio inquilino Azure Ative Directory (Azure AD). Muitas tarefas e serviços podem ser realizados através de inquilinos geridos, utilizando [a gestão de recursos delegada da Azure.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> A gestão de recursos delegados Azure também pode ser usada [dentro de uma empresa que tem vários inquilinos AD Azure próprios](enterprise.md) para simplificar a administração de inquilinos cruzados.

## <a name="understanding-tenants-and-delegation"></a>Compreensão dos inquilinos e delegação

Um inquilino da AD Azure é uma representação de uma organização. É um caso dedicado de Azure AD que uma organização recebe quando cria uma relação com a Microsoft ao inscrever-se no Azure, Microsoft 365 ou outros serviços. Cada inquilino da AD Azure é distinto e separado de outros inquilinos da AD Azure, e tem o seu próprio ID de inquilino (um GUID). Para mais informações, veja [o que é o Diretório Ativo Azure?](../../active-directory/fundamentals/active-directory-whatis.md)

Normalmente, para gerir os recursos da Azure para um cliente, os prestadores de serviços teriam de se inscrever no portal Azure utilizando uma conta associada ao inquilino desse cliente, exigindo que um administrador no arrendatário do cliente criasse e gerisse contas de utilizador para o prestador de serviços.

Com o Azure Lighthouse, o processo de embarque especifica os utilizadores dentro do inquilino do prestador de serviços que poderão trabalhar em assinaturas delegadas e grupos de recursos no arrendatário do cliente. Estes utilizadores podem então iniciar seduca no portal Azure usando as suas próprias credenciais. Dentro do portal Azure, podem gerir recursos pertencentes a todos os clientes a que têm acesso. Isto pode ser feito visitando a página [dos Meus clientes](../how-to/view-manage-customers.md) no portal Azure, ou trabalhando diretamente no contexto da subscrição desse cliente, seja no portal Azure ou através de APIs.

O Farol Azure permite uma maior flexibilidade na gestão de recursos para vários clientes sem ter de se inscrever em contas diferentes em diferentes inquilinos. Por exemplo, um prestador de serviços pode ter dois clientes com responsabilidades diferentes e níveis de acesso. Utilizando o Farol Azure, os utilizadores autorizados podem inscrever-se no inquilino do prestador de serviços para aceder a estes recursos.

![Diagrama mostrando recursos do cliente geridos através de um inquilino prestador de serviços.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>APIs e suporte de ferramentas de gestão

Pode executar tarefas de gestão em recursos delegados, quer diretamente no portal, quer utilizando APIs e ferramentas de gestão (como Azure CLI e Azure PowerShell). Todas as APIs existentes podem ser utilizadas quando trabalham com recursos delegados, desde que a funcionalidade seja suportada para a gestão de inquilinos cruzados e o utilizador tenha as permissões adequadas.

O cmdlet Azure PowerShell [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) mostra os `HomeTenantId` atributos e `ManagedByTenantIds` atributos para cada subscrição, permitindo-lhe identificar se uma subscrição devolvida pertence a um inquilino gerido ou ao seu inquilino gerente.

Da mesma forma, os comandos Azure CLI, como [a lista de conta az,](/cli/azure/account#az-account-list) mostram os `homeTenantId` atributos e `managedByTenants` atributos. Se não vir estes valores ao utilizar o Azure CLI, tente limpar o cache correndo `az account clear` seguido por `az login --identity` .

Na API Azure REST, as [Assinaturas - Obter](/rest/api/resources/subscriptions/get) e [Assinaturas - Os](/rest/api/resources/subscriptions/list) comandos da lista incluem `ManagedByTenant` .

> [!NOTE]
> Para além das informações dos inquilinos relacionadas com o Farol de Azure, os inquilinos apresentados por estas APIs podem também refletir os inquilinos parceiros para os pedidos geridos pela Azure Databricks ou para a Azure.

Também fornecemos APIs que são específicas para executar tarefas do Farol Azure. Para mais informações, consulte a secção **Referência.**

## <a name="enhanced-services-and-scenarios"></a>Serviços e cenários melhorados

A maioria das tarefas e serviços podem ser realizados em recursos delegados entre inquilinos geridos. Abaixo estão alguns dos cenários-chave onde a gestão inter-inquilino pode ser especialmente eficaz.

[Arco azul:](../../azure-arc/index.yml)

- Gerir servidores híbridos em escala - [Azure Arc ativado servidores](../../azure-arc/servers/overview.md):
  - [Gerir máquinas Windows Server ou Linux fora do Azure que estão ligadas](../../azure-arc/servers/onboard-portal.md) a subscrições delegadas e/ou grupos de recursos em Azure
  - Gerir máquinas conectadas utilizando construções Azure, tais como Azure Policy e marcação
  - Garantir que o mesmo conjunto de políticas são aplicadas em todos os ambientes híbridos dos clientes
  - Utilize o Azure Security Center para monitorizar a conformidade entre os ambientes híbridos dos clientes
- Gerir clusters híbridos de Kubernetes em escala - [Azure Arc habilitado Kubernetes (pré-visualização)](../../azure-arc/kubernetes/overview.md):
  - [Gerir clusters Kubernetes que estão ligados](../../azure-arc/kubernetes/connect-cluster.md) a subscrições delegadas e/ou grupos de recursos em Azure
  - [Use GitOps](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) para clusters conectados
  - Impor políticas em todos os clusters conectados

[Azure Automation:](../../automation/index.yml)

- Utilizar contas de automação para aceder e trabalhar com recursos delegados

[Azure Backup:](../../backup/index.yml)

- Faça o back up e restaure os [dados dos clientes a partir de cargas de trabalho no local, VMs Azure, ações de ficheiros Azure, e muito mais](../..//backup/backup-overview.md#what-can-i-back-up)
- Utilize o [Backup Explorer](../../backup/monitor-azure-backup-with-backup-explorer.md) para ajudar a visualizar informações operacionais de itens de backup (incluindo recursos Azure ainda não configurados para backup) e informações de monitorização (empregos e alertas) para subscrições delegadas. O Backup Explorer está atualmente disponível apenas para dados Azure VM.
- Use [Relatórios de Backup](../../backup/configure-reports.md) em subscrições delegadas para acompanhar tendências históricas, analisar o consumo de armazenamento de backup e auditar backups e restauros.

[Plantas Azure:](../../governance/blueprints/index.yml)

- Utilize plantas Azure para orquestrar a implementação de modelos de recursos e outros artefactos (requer [acesso adicional](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) para preparar a subscrição do cliente)

[Gestão de Custos Azure + Faturação:](../../cost-management-billing/index.yml)

- A partir do inquilino gerente, os parceiros da CSP podem ver, gerir e analisar os custos de consumo antes de impostos (não incluindo as compras) para clientes que estão ao abrigo do plano Azure. O custo basear-se-á nas tarifas de retalho e no acesso ao controlo de acesso baseado em funções (Azure RBAC) que o parceiro tem para a subscrição do cliente.

[Cofre da Chave Azure:](../../key-vault/general/index.yml)

- Criar cofres chave em inquilinos de clientes
- Use uma identidade gerida para criar Cofres Chave em inquilinos de clientes

[Serviço Azure Kubernetes (AKS)](../../aks/index.yml):

- Gerir ambientes kubernetes hospedados e implementar e gerir aplicações contentorizadas dentro dos inquilinos dos clientes
- Implementar e gerir clusters em inquilinos de clientes
-   Utilize o Azure Monitor para contentores para monitorizar o desempenho entre os inquilinos do cliente

[Azure Migrate:](../../migrate/index.yml)

- Crie projetos de migração no inquilino do cliente e migrar VMs

[Monitor Azure:](../../azure-monitor/index.yml)

- Ver alertas para subscrições delegadas, com a capacidade de visualizar e atualizar alertas em todas as subscrições
- Ver detalhes do registo de atividade para subscrições delegadas
- [Análise de registos](../../azure-monitor/platform/service-providers.md): Os dados de consulta de espaços de trabalho remotos em vários inquilinos (note que as contas de automação utilizadas para aceder a dados de espaços de trabalho em inquilinos de clientes devem ser criadas no mesmo inquilino)
- Crie alertas em inquilinos de clientes que desencadeiem a automação, como os runbooks da Azure Automation ou as Funções Azure, no inquilino gerente através de webhooks
- Criar [definições de diagnóstico](../..//azure-monitor/platform/diagnostic-settings.md) nos inquilinos dos clientes para enviar registos de recursos para espaços de trabalho no inquilino gerente
- Para cargas de trabalho SAP, [monitorize as métricas da SAP Solutions com uma visão agregada entre os inquilinos do cliente](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Rede Azure:](../../networking/networking-overview.md)

- Implementar e gerir [a Rede Virtual Azure](../../virtual-network/index.yml) e cartões de interface de rede virtual (vNICs) dentro de inquilinos geridos
- Implementar e configurar [a Azure Firewall](../../firewall/overview.md) para proteger os recursos da Rede Virtual dos clientes
- Gerir serviços de conectividade como [Azure Virtual WAN,](../../virtual-wan/virtual-wan-about.md) [ExpressRoute](../../expressroute/expressroute-introduction.md)e [VPN Gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Utilize o Farol Azure para apoiar cenários-chave para o [Programa MSP de rede Azure](../../networking/networking-partners-msp.md)

[Política de Azure:](../../governance/policy/index.yml)

- Criar e editar definições de políticas dentro de subscrições delegadas
- Atribuir definições de política definidas pelo cliente dentro de subscrições delegadas
- Os clientes vêem políticas da autoria do prestador de serviços ao lado de quaisquer políticas que tenham sido autores
- Pode [remediar o implementaçãoIfNotExists ou modificar atribuições dentro do inquilino gerido](../how-to/deploy-policy-remediation.md)

[Gráfico de recursos Azure:](../../governance/resource-graph/index.yml)

- Agora inclui o ID do inquilino em resultados de consulta devolvido, permitindo-lhe identificar se uma subscrição pertence a um inquilino gerido

[Centro de Segurança Azure:](../../security-center/index.yml)

- Visibilidade inter-inquilino
  - Monitorizar o cumprimento das políticas de segurança e garantir a cobertura de segurança em todos os recursos dos inquilinos
  - Monitorização contínua da conformidade regulamentar em vários inquilinos numa única vista
  - Monitor, triagem e priorização de recomendações de segurança accitivas com cálculo de pontuação segura
- Gestão da postura de segurança de inquilinos cruzados
  - Manage security policies (Gerir políticas de segurança)
  - Tomar medidas sobre recursos que estão fora do cumprimento de recomendações de segurança accitórias
  - Recolher e armazenar dados relacionados com a segurança
- Deteção e proteção de ameaças de inquilinos cruzados
  - Detetar ameaças através dos recursos dos inquilinos
  - Aplicar controlos avançados de proteção contra ameaças, tais como acesso vm just-in-time (JIT)
  - Configuração do grupo de segurança de rede Harden com Endurecimento da Rede Adaptive
  - Garantir que os servidores estão a executar apenas as aplicações e processos que devem estar com controlos de aplicações adaptativos
  - Monitorize alterações em ficheiros importantes e entradas de registo com Monitorização da Integridade do Ficheiro (FIM)
- Note que a subscrição total deve ser delegada ao arrendatário gestor; Os cenários do Centro de Segurança Azure não são suportados com grupos de recursos delegados

[Sentinela Azure:](../../sentinel/multiple-tenants-service-providers.md)

- Gerir os recursos do Azure Sentinel [em inquilinos de clientes](../../sentinel/multiple-tenants-service-providers.md)
- [Acompanhe os ataques e veja alertas de segurança em vários inquilinos](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Ver incidentes](../../sentinel/multiple-workspace-view.md) em vários espaços de trabalho sentinela espalhados por inquilinos

[Saúde do Serviço Azure:](../../service-health/index.yml)

- Monitorize a saúde dos recursos dos clientes com a Azure Resource Health
- Acompanhe a saúde dos serviços Azure utilizados pelos seus clientes

[Recuperação do local de Azure:](../../site-recovery/index.yml)

- Gerir opções de recuperação de desastres para máquinas virtuais Azure em inquilinos de clientes (note que não pode usar `RunAs` contas para copiar extensões VM)

[Máquinas Virtuais Azure:](../../virtual-machines/index.yml)

- Utilize extensões de máquinas virtuais para fornecer tarefas de configuração e automatização pós-implantação em VMs Azure
- Use diagnósticos de arranque para resolver problemas Azure VMs
- Acesso VMs com consola em série
- Integre VMs com Azure Key Vault para senhas, segredos ou chaves criptográficas para encriptação de discos utilizando [a identidade gerida através da política,](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)garantindo que os segredos são armazenados num Cofre-Chave nos inquilinos geridos
- Note que não pode usar o Azure Ative Directory para login remoto em VMs

Pedidos de apoio:

- [Aberto pedidos de apoio da **Ajuda + suporte**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) no portal Azure para recursos delegados (selecionando o plano de apoio disponível para o âmbito delegado)

## <a name="current-limitations"></a>Limitações atuais

Com todos os cenários, esteja atento às seguintes limitações atuais:

- Os pedidos tratados pelo Azure Resource Manager podem ser realizados através do Farol Azure. A operação URIs para estes pedidos começa com `https://management.azure.com` . No entanto, os pedidos que são tratados por um tipo de recurso (como o acesso de segredos do Cofre chave ou o acesso a dados de armazenamento) não são suportados com o Farol de Azure. A operação URIs para estes pedidos normalmente começa com um endereço exclusivo do seu caso, como `https://myaccount.blob.core.windows.net` ou `https://mykeyvault.vault.azure.net/` . Estes últimos são também operações de dados em vez de operações de gestão.
- As atribuições de funções devem utilizar [funções incorporadas do Azure.](../../role-based-access-control/built-in-roles.md) Todas as funções incorporadas são atualmente suportadas com a gestão de recursos delegados da Azure, exceto para o Proprietário ou quaisquer funções incorporadas com [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) permissão. A função de Administrador de Acesso ao Utilizador é suportada apenas para uso limitado na [atribuição de funções a identidades geridas](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  As funções personalizadas e [as funções clássicas de administrador de subscrição](../../role-based-access-control/classic-administrators.md) não são suportadas.
- Enquanto você pode a bordo subscrições que usam Azure Databricks, os utilizadores no inquilino gerente não podem lançar espaços de trabalho Azure Databricks em uma subscrição delegada neste momento.
- Embora possa embarcar subscrições e grupos de recursos que tenham bloqueios de recursos, esses bloqueios não impedirão que as ações sejam executadas pelos utilizadores no inquilino gerente. [Negar atribuições](../../role-based-access-control/deny-assignments.md) que protejam os recursos geridos pelo sistema, como as criadas pela Azure ou pela Azure Blueprints (atribuições de negação atribuídas ao sistema), impedem os utilizadores do inquilino gerente de agir em função desses recursos; no entanto, neste momento, os utilizadores do inquilino do cliente não podem criar as suas próprias atribuições de negação (atribuições de negação atribuídas pelo utilizador).
- A delegação de assinaturas através de uma [nuvem nacional](../../active-directory/develop/authentication-national-cloud.md) e da nuvem pública de Azure, ou através de duas nuvens nacionais separadas, não é apoiada.

## <a name="next-steps"></a>Próximos passos

- A bordo dos seus clientes para o Azure Lighthouse, quer [utilizando modelos de Gestor de Recursos Azure,](../how-to/onboard-customer.md) quer [publicando uma oferta de serviços geridos por empresas privadas ou públicas ao Azure Marketplace.](../how-to/publish-managed-services-offers.md)
- [Ver e gerir clientes](../how-to/view-manage-customers.md) indo para **os meus clientes** no portal Azure.
