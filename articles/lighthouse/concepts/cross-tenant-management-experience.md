---
title: Experiências de gestão entre inquilinos
description: A gestão de recursos delegados da Azure permite uma experiência de gestão de inquilinos cruzados.
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 66e2b2f7fbbae5940b576ead22a416625af3c058
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418524"
---
# <a name="cross-tenant-management-experiences"></a>Experiências de gestão entre inquilinos

Como prestador de serviços, pode utilizar a [gestão de recursos delegados do Azure](../concepts/azure-delegated-resource-management.md) para gerir os recursos do Azure para vários clientes de dentro do seu próprio inquilino no [portal Azure.](https://portal.azure.com) A maioria das tarefas e serviços podem ser realizados em recursos azure delegados através de inquilinos geridos. Este artigo descreve alguns dos cenários melhorados em que a gestão de recursos delegados do Azure pode ser eficaz.

> [!NOTE]
> A gestão de recursos delegados da Azure também pode ser utilizada dentro de [uma empresa que tem vários inquilinos da Azure AD para](enterprise.md) simplificar a administração de inquilinos transversais.

## <a name="understanding-customer-tenants"></a>Compreender os inquilinos dos clientes

Um inquilino azure Ative Directory (Azure AD) é uma representação de uma organização. É um exemplo dedicado do Azure AD que uma organização recebe quando cria uma relação com a Microsoft ao inscrever-se no Azure, Microsoft 365 ou outros serviços. Cada inquilino da Azure AD é distinto e separado de outros inquilinos da AD Azure, e tem o seu próprio ID de inquilino (um GUID). Para mais informações, consulte o que é o [Azure Ative Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

Normalmente, para gerir os recursos do Azure para um cliente, os prestadores de serviços teriam de se inscrever no portal Azure utilizando uma conta associada ao inquilino desse cliente, exigindo que um administrador no inquilino do cliente criasse e gerisse as contas de utilizador para o prestador de serviços.

Com a gestão de recursos delegados da Azure, o processo de embarque especifica os utilizadores dentro do inquilino do prestador de serviços que poderão aceder e gerir subscrições, grupos de recursos e recursos no inquilino do cliente. Estes utilizadores podem então iniciar sessão no portal Azure utilizando as suas próprias credenciais. Dentro do portal Azure, podem gerir recursos pertencentes a todos os clientes a que têm acesso. Isto pode ser feito visitando a página [dos meus clientes](../how-to/view-manage-customers.md) no portal Azure, ou trabalhando diretamente no contexto da subscrição desse cliente, seja no portal Azure ou através de APIs.

A gestão de recursos delegados da Azure permite uma maior flexibilidade para gerir recursos para vários clientes sem ter de assinar contas diferentes em diferentes inquilinos. Por exemplo, um prestador de serviços pode ter dois clientes com diferentes responsabilidades e níveis de acesso. Utilizando a gestão de recursos delegados do Azure, os utilizadores autorizados podem inscrever-se no inquilino do prestador de serviços para aceder a esses recursos.

![Recursos do cliente geridos através de um inquilino prestador de serviços](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>APIs e suporte a ferramentas de gestão

Pode executar tarefas de gestão em recursos delegados diretamente no portal ou utilizando APIs e ferramentas de gestão (como o Azure CLI e o Azure PowerShell). Todas as APIs existentes podem ser utilizadas quando trabalham com recursos delegados, desde que a funcionalidade seja suportada para gestão de inquilinos transversais e o utilizador tenha as permissões adequadas.

O [Cmdlet](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0) De Subscrição De PowerShell Azure PowerShell mostra o ID do **inquilino** para cada subscrição, permitindo-lhe identificar se uma subscrição devolvida pertence ao seu inquilino fornecedor de serviços ou a um inquilino de cliente gerido.

Da mesma forma, comandos Azure CLI, como a lista de [contas az,](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) mostram os atributos **homeTenantId** e **geridosByTenants.**

> [!TIP]
> Se não vir estes valores ao utilizar o Azure CLI, tente limpar a cache executando `az account clear` seguido por `az login --identity`.

Também fornecemos APIs específicos para executar tarefas de gestão de recursos delegados azure. Para mais informações, consulte a secção **referência.**

## <a name="enhanced-services-and-scenarios"></a>Serviços e cenários melhorados

A maioria das tarefas e serviços podem ser realizados em recursos delegados através de inquilinos geridos. Abaixo estão alguns dos cenários-chave em que a gestão de inquilinos cruzados pode ser eficaz.

[Arco Azure para servidores (pré-visualização)](../../azure-arc/servers/overview.md):

- [Ligue as máquinas Windows Server ou Linux fora](../../azure-arc/servers/quickstart-onboard-portal.md) do Azure a subscrições delegadas e/ou grupos de recursos em Azure
- Gerir máquinas conectadas utilizando construções Azure, tais como a Política Azure e a marcação

[Automação Azure:](../../automation/index.yml)

- Utilize contas de automação para aceder e trabalhar com recursos de clientes delegados

[Backup Azure:](../../backup/index.yml)

- Back up e restaurar os dados dos clientes nos inquilinos dos clientes
- Utilize o [Backup Explorer](../../backup/monitor-azure-backup-with-backup-explorer.md) para ajudar a visualizar informações operacionais de itens de backup (incluindo recursos Azure ainda não configurados para backup) e monitorizando informações (empregos e alertas) para subscrições delegadas. O Backup Explorer está atualmente disponível apenas para dados De VM Azure.
- Utilize [relatórios](../../backup/configure-reports.md) de backup através de subscrições delegadas para acompanhar tendências históricas, analisar o consumo de armazenamento de backup e auditar backups e restauros.

[Serviço Azure Kubernetes (AKS)](../../aks/index.yml):

- Gerir ambientes kubernetes hospedados e implementar e gerir aplicações contentorizadas dentro dos inquilinos dos clientes

[Monitor Azure:](../../azure-monitor/index.yml)

- Ver alertas para subscrições delegadas, com a capacidade de ver alertas em todas as subscrições
- Ver detalhes do registo de atividade para subscrições delegadas
- Análise de registo: Dados de consulta de espaços de trabalho remotos de clientes em vários inquilinos
- Crie alertas em inquilinos clientes que desencadeiem automação, como os livros de execução da Azure Automation ou as Funções Azure, no inquilino do prestador de serviços através de webhooks

[Política Azure:](../../governance/policy/index.yml)

- Os instantâneos de conformidade mostram detalhes para as políticas atribuídas dentro das subscrições delegadas
- Criar e editar definições de políticas dentro de uma subscrição delegada
- Atribuir definições de política definidas pelo cliente dentro da subscrição delegada
- Os clientes vêem políticas da autoria do prestador de serviços ao lado de quaisquer políticas que tenham sido autoras
- Pode [remediar o deployIfNotExists ou modificar atribuições dentro do inquilino do cliente](../how-to/deploy-policy-remediation.md)

[Gráfico de recursos azure:](../../governance/resource-graph/index.yml)

- Agora inclui o ID do inquilino em resultados de consulta devolvido, permitindo-lhe identificar se uma subscrição pertence ao inquilino do cliente ou inquilino prestador de serviços

[Centro de Segurança Azure:](../../security-center/index.yml)

- Visibilidade entre inquilinos
  - Monitorizar o cumprimento das políticas de segurança e garantir a cobertura de segurança em todos os recursos dos inquilinos
  - Monitorização contínua da conformidade regulamentar entre vários clientes numa única perspetiva
  - Monitor, triagem e priorizar recomendações de segurança atoveis com cálculo de pontuação segura
- Gestão da postura de segurança entre inquilinos
  - Gerir políticas de segurança
  - Tome medidas sobre recursos que estão fora do cumprimento de recomendações de segurança acionáveis
  - Recolher e armazenar dados relacionados com a segurança
- Deteção e proteção de ameaças entre inquilinos
  - Detetar ameaças nos recursos dos inquilinos
  - Aplicar controlos avançados de proteção contra ameaças, tais como acesso vm just-in-time (JIT)
  - Configuração do grupo de segurança da rede Harden com Endurecimento de Rede Adaptive
  - Certifique-se de que os servidores estão a executar apenas as aplicações e processos que devem estar com controlos de aplicação adaptativo
  - Monitorize alterações a ficheiros importantes e entradas de registo com Monitorização da Integridade do Ficheiro (FIM)

[Sentinela Azure:](../../sentinel/multiple-tenants-service-providers.md)

- Gerir os recursos da Azure Sentinel [nos inquilinos](../../sentinel/multiple-tenants-service-providers.md) dos clientes
- [Acompanhe os ataques e veja alertas de segurança em vários inquilinos de clientes](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Saúde do Serviço Azure:](../../service-health/index.yml)

- Monitorizar a saúde dos recursos dos clientes com a Azure Resource Health
- Acompanhe a saúde dos serviços Azure utilizados pelos seus clientes

[Recuperação do site Azure:](../../site-recovery/index.yml)

- Gerencie opções de recuperação de desastres para máquinas virtuais Azure em inquilinos de clientes (note que não pode usar contas RunAs para copiar extensões VM)

[Máquinas Virtuais Azure:](../../virtual-machines/index.yml)

- Utilize extensões de máquinas virtuais para fornecer tarefas de configuração e automação pós-implantação em VMs Azure em inquilinos de clientes
- Use diagnósticos de botas para resolver os VMs azure em inquilinos de clientes
- Aceder a VMs com consola em série nos inquilinos dos clientes
- Note que não pode usar o Diretório Ativo Azure para iniciar sessão remota a um VM, e não pode integrar um VM com um Cofre chave para senhas, segredos ou chaves criptográficas para encriptação de disco

[Rede Virtual Azure:](../../virtual-network/index.yml)

- Implementar e gerir redes virtuais e cartões de interface de rede virtual (vNICs) dentro dos inquilinos dos clientes

Pedidos de apoio:

- Abrir pedidos de apoio para recursos delegados a partir da lâmina de **apoio Ajuda +** no portal Azure (selecionando o plano de suporte disponível para o âmbito delegado)

## <a name="current-limitations"></a>Limitações atuais
Com todos os cenários, esteja ciente das seguintes limitações atuais:

- Os pedidos realizados pelo Gestor de Recursos Azure podem ser realizados utilizando a gestão de recursos delegados do Azure. A operação URIs para estes `https://management.azure.com`pedidos começa com . No entanto, os pedidos que são tratados por uma instância de um tipo de recurso (como o acesso a segredos keyVault ou o acesso a dados de armazenamento) não são suportados com a gestão de recursos delegados do Azure. A operação URIs para estes pedidos normalmente começa com um endereço `https://myaccount.blob.core.windows.net` `https://mykeyvault.vault.azure.net/`único para a sua instância, como ou . Estes últimos também são normalmente operações de dados em vez de operações de gestão. 
- As atribuições de funções devem utilizar [funções](../../role-based-access-control/built-in-roles.md)de controlo de acesso baseado seleções em funções (RBAC). Todas as funções incorporadas são atualmente suportadas com a gestão de recursos delegados da Azure, exceto para owner ou quaisquer funções incorporadas com permissão [DataActions.](../../role-based-access-control/role-definitions.md#dataactions) A função de Administrador de Acesso ao Utilizador é suportada apenas para uso limitado na atribuição de [funções a identidades geridas](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  As funções personalizadas e as [funções clássicas](../../role-based-access-control/classic-administrators.md) de administrador de subscrição não são suportadas.
- Enquanto pode embarcar subscrições que utilizam o Azure Databricks, os utilizadores do inquilino gerente não podem lançar espaços de trabalho Azure Databricks numa subscrição delegada neste momento.
- Embora possa participar em subscrições e grupos de recursos para a gestão de recursos delegados do Azure que tenham fechaduras de recursos, esses bloqueios não impedirão que as ações sejam realizadas pelos utilizadores no inquilino gestor. [Negar atribuições](../../role-based-access-control/deny-assignments.md) que protejam recursos geridos pelo sistema, como os criados pela Azure geriu aplicações ou plantas Azure (atribuições de negação atribuídas pelo sistema), impedir que os utilizadores do inquilino gerente agissem com esses recursos; no entanto, neste momento os utilizadores do inquilino do cliente não podem criar as suas próprias atribuições de negação (atribuições de negação atribuídas pelo utilizador).
- Os utilizadores do inquilino gerente não terão acesso a visualização de informações de faturação para uma subscrição de cliente delegada, mesmo que tenham uma função incorporada que normalmente permita o acesso. Isto porque o acesso à informação de faturação requer passos adicionais que atualmente são apenas suportados para os utilizadores dentro do mesmo inquilino.

## <a name="next-steps"></a>Passos seguintes

- A bordo dos seus clientes para a gestão de recursos delegados do Azure, quer utilizando modelos de [Gestor de Recursos Azure,](../how-to/onboard-customer.md) quer [publicando uma oferta de serviços geridos pelo público ou privado ao Azure Marketplace.](../how-to/publish-managed-services-offers.md)
- [Ver e gerir os clientes](../how-to/view-manage-customers.md) indo para os meus **clientes** no portal Azure.
