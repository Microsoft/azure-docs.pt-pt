---
title: Experiências de gestão entre inquilinos
description: A gestão de recursos delegados Azure permite uma experiência de gestão de inquilinos cruzados.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 225a7f7725762c6887858366b134a7ad9a7b8eb6
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052676"
---
# <a name="cross-tenant-management-experiences"></a>Experiências de gestão entre inquilinos

Como prestador de serviços, pode utilizar a [gestão de recursos delegados da Azure](../concepts/azure-delegated-resource-management.md) para gerir os recursos da Azure para vários clientes dentro do seu próprio inquilino no [portal Azure.](https://portal.azure.com) A maioria das tarefas e serviços podem ser realizados em recursos delegados da Azure através de inquilinos geridos. Este artigo descreve alguns dos cenários melhorados onde a gestão de recursos delegada do Azure pode ser eficaz.

> [!NOTE]
> A gestão de recursos delegados Azure também pode ser usada [dentro de uma empresa que tem vários inquilinos AD Azure próprios](enterprise.md) para simplificar a administração de inquilinos cruzados.

## <a name="understanding-customer-tenants"></a>Compreender os inquilinos dos clientes

Um inquilino do Azure Ative Directory (Azure AD) é uma representação de uma organização. É um caso dedicado de Azure AD que uma organização recebe quando cria uma relação com a Microsoft ao inscrever-se no Azure, Microsoft 365 ou outros serviços. Cada inquilino da AD Azure é distinto e separado de outros inquilinos da AD Azure, e tem o seu próprio ID de inquilino (um GUID). Para mais informações, veja [o que é o Diretório Ativo Azure?](../../active-directory/fundamentals/active-directory-whatis.md)

Normalmente, para gerir os recursos da Azure para um cliente, os prestadores de serviços teriam de se inscrever no portal Azure utilizando uma conta associada ao inquilino desse cliente, exigindo que um administrador no arrendatário do cliente criasse e gerisse contas de utilizador para o prestador de serviços.

Com a gestão de recursos delegada da Azure, o processo de embarque especifica os utilizadores dentro do inquilino do prestador de serviços que poderão aceder e gerir subscrições, grupos de recursos e recursos no inquilino do cliente. Estes utilizadores podem então iniciar seduca no portal Azure usando as suas próprias credenciais. Dentro do portal Azure, podem gerir recursos pertencentes a todos os clientes a que têm acesso. Isto pode ser feito visitando a página [dos Meus clientes](../how-to/view-manage-customers.md) no portal Azure, ou trabalhando diretamente no contexto da subscrição desse cliente, seja no portal Azure ou através de APIs.

A gestão de recursos delegados da Azure permite uma maior flexibilidade na gestão de recursos para vários clientes sem ter de se inscrever em contas diferentes em diferentes inquilinos. Por exemplo, um prestador de serviços pode ter dois clientes com responsabilidades diferentes e níveis de acesso. Utilizando a gestão de recursos delegada da Azure, os utilizadores autorizados podem inscrever-se no inquilino do prestador de serviços para aceder a estes recursos.

![Recursos do cliente geridos através de um inquilino prestador de serviços](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>APIs e suporte de ferramentas de gestão

Pode executar tarefas de gestão em recursos delegados, quer diretamente no portal, quer utilizando APIs e ferramentas de gestão (como Azure CLI e Azure PowerShell). Todas as APIs existentes podem ser utilizadas quando trabalham com recursos delegados, desde que a funcionalidade seja suportada para a gestão de inquilinos cruzados e o utilizador tenha as permissões adequadas.

O cmdlet Azure PowerShell [Get-AzSubscription](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) mostra o **inquilinoID** para cada subscrição, permitindo-lhe identificar se uma subscrição devolvida pertence ao seu inquilino prestador de serviços ou a um inquilino de cliente gerido.

Da mesma forma, os comandos Azure CLI, tais como [a lista de conta az](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) mostram os atributos **homeTenantId** e **geridos PorTenants.**

> [!TIP]
> Se não vir estes valores ao utilizar o Azure CLI, tente limpar o cache correndo `az account clear` seguido por `az login --identity` .

Também fornecemos APIs que são específicas para executar tarefas delegadas de gestão de recursos Azure. Para mais informações, consulte a secção **Referência.**

## <a name="enhanced-services-and-scenarios"></a>Serviços e cenários melhorados

A maioria das tarefas e serviços podem ser realizados em recursos delegados entre inquilinos geridos. Abaixo estão alguns dos cenários-chave onde a gestão interina pode ser eficaz.

[Azure Arc para servidores (pré-visualização)](../../azure-arc/servers/overview.md):

- [Ligue máquinas windows server ou Linux fora do Azure](../../azure-arc/servers/quickstart-onboard-portal.md) a subscrições delegadas e/ou grupos de recursos em Azure
- Gerir máquinas conectadas utilizando construções Azure, tais como Azure Policy e marcação

[Azure Automation:](../../automation/index.yml)

- Utilize contas de automação para aceder e trabalhar com recursos delegados do cliente

[Azure Backup:](../../backup/index.yml)

- Fazer o back up e restaurar os dados dos clientes nos inquilinos dos clientes
- Utilize o [Backup Explorer](../../backup/monitor-azure-backup-with-backup-explorer.md) para ajudar a visualizar informações operacionais de itens de backup (incluindo recursos Azure ainda não configurados para backup) e informações de monitorização (empregos e alertas) para subscrições delegadas. O Backup Explorer está atualmente disponível apenas para dados Azure VM.
- Use [Relatórios de Backup](../../backup/configure-reports.md) em subscrições delegadas para acompanhar tendências históricas, analisar o consumo de armazenamento de backup e auditar backups e restauros.

[Gestão de Custos Azure + Faturação:](../../cost-management-billing/index.yml)

- A partir do inquilino gestor, os parceiros da CSP podem ver, gerir e analisar os custos de consumo antes de impostos (não incluindo as compras) para clientes que estão ao abrigo do plano Azure. O custo basear-se-á nas tarifas de retalho e no acesso rbac Azure que o parceiro tem para a subscrição do cliente.

[Serviço Azure Kubernetes (AKS)](../../aks/index.yml):

- Gerir ambientes kubernetes hospedados e implementar e gerir aplicações contentorizadas dentro dos inquilinos dos clientes

[Monitor Azure:](../../azure-monitor/index.yml)

- Ver alertas para subscrições delegadas, com a capacidade de ver alertas em todas as subscrições
- Ver detalhes do registo de atividade para subscrições delegadas
- Análise de registo: Dados de consulta de espaços de trabalho remotos do cliente em vários inquilinos
- Crie alertas em inquilinos de clientes que desencadeiem a automação, como os runbooks da Azure Automation ou as Funções Azure, no inquilino do prestador de serviços através de webhooks

[Rede Azure:](../../networking/networking-overview.md)

- Implementar e gerir cartões [de interface de rede virtual (VNet)](../../virtual-network/index.yml) e de rede virtual (vNICs) dentro dos inquilinos do cliente
- Implementar e configurar [a Azure Firewall](../../firewall/overview.md) para proteger os recursos da Rede Virtual dos clientes
- Gerir serviços de conectividade como [Azure Virtual WAN,](../../virtual-wan/virtual-wan-about.md) [ExpressRoute](../../expressroute/expressroute-introduction.md)e [VPN Gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md) para clientes
- Utilize o Farol Azure para apoiar cenários-chave para o [Programa MSP de rede Azure](../../networking/networking-partners-msp.md)


[Política de Azure:](../../governance/policy/index.yml)

- Os instantâneos de conformidade mostram detalhes para políticas atribuídas dentro de subscrições delegadas
- Criar e editar definições de política dentro de uma subscrição delegada
- Atribuir definições de política definidas pelo cliente na subscrição delegada
- Os clientes vêem políticas da autoria do prestador de serviços ao lado de quaisquer políticas que tenham sido autores
- Pode [remediar o implementaçãoIfNotExists ou modificar atribuições dentro do cliente inquilino](../how-to/deploy-policy-remediation.md)

[Gráfico de recursos Azure:](../../governance/resource-graph/index.yml)

- Agora inclui o ID do inquilino em resultados de consulta devolvido, permitindo-lhe identificar se uma subscrição pertence ao inquilino do cliente ou inquilino do prestador de serviços

[Centro de Segurança Azure:](../../security-center/index.yml)

- Visibilidade inter-inquilino
  - Monitorizar o cumprimento das políticas de segurança e garantir a cobertura de segurança em todos os recursos dos inquilinos
  - Monitorização contínua da conformidade regulamentar entre vários clientes numa única vista
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

[Sentinela Azure:](../../sentinel/multiple-tenants-service-providers.md)

- Gerir os recursos do Azure Sentinel [em inquilinos de clientes](../../sentinel/multiple-tenants-service-providers.md)
- [Acompanhe os ataques e veja alertas de segurança em vários inquilinos de clientes](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Ver incidentes](../../sentinel/multiple-workspace-view.md) em vários espaços de trabalho da Sentinel, distribuídos por inquilinos de clientes

[Saúde do Serviço Azure:](../../service-health/index.yml)

- Monitorize a saúde dos recursos dos clientes com a Azure Resource Health
- Acompanhe a saúde dos serviços Azure utilizados pelos seus clientes

[Recuperação do local de Azure:](../../site-recovery/index.yml)

- Gerir opções de recuperação de desastres para máquinas virtuais Azure em inquilinos de clientes (note que não pode usar contas RunAs para copiar extensões VM)

[Máquinas Virtuais Azure:](../../virtual-machines/index.yml)

- Utilize extensões de máquinas virtuais para fornecer tarefas de configuração e automatização pós-implantação em VMs Azure em inquilinos de clientes
- Use diagnósticos de boot para resolver problemas Azure VMs em inquilinos de clientes
- Acesso VMs com consola em série em inquilinos de clientes
- Integre VMs com Azure KeyVault para senhas, segredos ou chaves criptográficas para encriptação de discos utilizando [a identidade gerida através da política,](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)garantindo que os segredos são armazenados num Cofre-Chave em inquilinos de clientes
- Note que não pode usar o Azure Ative Directory para login remoto em VMs em inquilinos de clientes

Pedidos de apoio:

- Pedidos de apoio abertos para recursos delegados a partir da lâmina de **suporte Help +** no portal Azure (selecionando o plano de apoio disponível para o âmbito delegado)

## <a name="current-limitations"></a>Limitações atuais
Com todos os cenários, esteja atento às seguintes limitações atuais:

- Os pedidos tratados pelo Azure Resource Manager podem ser realizados utilizando a gestão de recursos delegada da Azure. A operação URIs para estes pedidos começa com `https://management.azure.com` . No entanto, os pedidos que são tratados por um tipo de recurso (como o acesso a segredos KeyVault ou o acesso a dados de armazenamento) não são suportados com a gestão de recursos delegados da Azure. A operação URIs para estes pedidos normalmente começa com um endereço exclusivo do seu caso, como `https://myaccount.blob.core.windows.net` ou `https://mykeyvault.vault.azure.net/` . Estes últimos são também operações de dados em vez de operações de gestão. 
- As atribuições de funções devem utilizar funções de controlo de acesso [baseados em funções](../../role-based-access-control/built-in-roles.md)(RBAC). Todas as funções incorporadas são atualmente suportadas com a gestão de recursos delegados da Azure, exceto para o Proprietário ou quaisquer funções incorporadas com permissão [DataActions.](../../role-based-access-control/role-definitions.md#dataactions) A função de Administrador de Acesso ao Utilizador é suportada apenas para uso limitado na [atribuição de funções a identidades geridas](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  As funções personalizadas e [as funções clássicas de administrador de subscrição](../../role-based-access-control/classic-administrators.md) não são suportadas.
- Enquanto você pode a bordo subscrições que usam Azure Databricks, os utilizadores no inquilino gerente não podem lançar espaços de trabalho Azure Databricks em uma subscrição delegada neste momento.
- Embora possa embarcar subscrições e grupos de recursos para a Azure delegada gestão de recursos que têm bloqueios de recursos, esses bloqueios não impedirão que as ações sejam executadas pelos utilizadores no inquilino gerente. [Negar atribuições](../../role-based-access-control/deny-assignments.md) que protejam os recursos geridos pelo sistema, como as criadas pela Azure ou pela Azure Blueprints (atribuições de negação atribuídas ao sistema), impedem os utilizadores do inquilino gerente de agir em função desses recursos; no entanto, neste momento, os utilizadores do inquilino do cliente não podem criar as suas próprias atribuições de negação (atribuições de negação atribuídas pelo utilizador).

## <a name="next-steps"></a>Passos seguintes

- A bordo dos seus clientes para a Azure delegada gestão de recursos, quer [através da utilização de modelos do Azure Resource Manager,](../how-to/onboard-customer.md) quer através [da publicação de uma oferta de serviços geridos por empresas privadas ou públicas ao Azure Marketplace.](../how-to/publish-managed-services-offers.md)
- [Ver e gerir clientes](../how-to/view-manage-customers.md) indo para **os meus clientes** no portal Azure.
