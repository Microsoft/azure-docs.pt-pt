---
title: Experiências de gestão entre inquilinos
description: O gerenciamento de recursos delegado do Azure permite uma experiência de gerenciamento entre locatários.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 11/7/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 182970cc39d200c37264a93d5e1b70c8839e5ef7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825811"
---
# <a name="cross-tenant-management-experiences"></a>Experiências de gestão entre inquilinos

Como um provedor de serviços, você pode usar o [Gerenciamento de recursos delegado do Azure](../concepts/azure-delegated-resource-management.md) para gerenciar recursos do Azure para vários clientes de dentro de seu próprio locatário no [portal do Azure](https://portal.azure.com). A maioria das tarefas e serviços pode ser executado em recursos delegados do Azure entre locatários gerenciados. Este artigo descreve alguns dos cenários aprimorados em que o gerenciamento de recursos delegado do Azure pode ser eficaz.

> [!NOTE]
> O gerenciamento de recursos delegado do Azure também pode ser usado em uma empresa que tem vários locatários próprios para simplificar a administração entre locatários.

## <a name="understanding-customer-tenants"></a>Noções básicas sobre locatários do cliente

Um locatário Azure Active Directory (Azure AD) é uma representação de uma organização. É uma instância dedicada do Azure AD que uma organização recebe quando cria uma relação com a Microsoft inscrevendo-se no Azure, Microsoft 365 ou outros serviços. Cada locatário do Azure AD é distinto e separado de outros locatários do Azure AD e tem sua própria ID de locatário (um GUID). Para obter mais informações, consulte [o que é Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

Normalmente, para gerenciar recursos do Azure para um cliente, os provedores de serviço precisam entrar no portal do Azure usando uma conta associada ao locatário do cliente, exigindo que um administrador no locatário do cliente crie e gerencie contas de usuário para o provedor de serviços.

Com o gerenciamento de recursos delegado do Azure, o processo de integração especifica os usuários no locatário do provedor de serviços que poderão acessar e gerenciar assinaturas, grupos de recursos e recursos no locatário do cliente. Esses usuários podem, então, entrar no portal do Azure usando suas próprias credenciais. Dentro do portal do Azure, eles podem gerenciar recursos que pertencem a todos os clientes aos quais eles têm acesso. Isso pode ser feito visitando a página [meus clientes](../how-to/view-manage-customers.md) no portal do Azure ou trabalhando diretamente no contexto da assinatura do cliente, seja na portal do Azure ou via APIs.

O gerenciamento de recursos delegado do Azure permite maior flexibilidade para gerenciar recursos para vários clientes sem precisar entrar em contas diferentes em locatários diferentes. Por exemplo, um provedor de serviços pode ter três clientes, com responsabilidades e níveis de acesso diferentes, como mostrado aqui:

![Três locatários do cliente mostrando responsabilidades do provedor de serviços](../media/azure-delegated-resource-management-customer-tenants.jpg)

Usando o gerenciamento de recursos delegado do Azure, os usuários autorizados podem entrar no locatário do provedor de serviços para acessar esses recursos, conforme mostrado aqui:

![Recursos do cliente gerenciados por meio de um locatário do provedor de serviços](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Suporte a ferramentas de gerenciamento e APIs

Você pode executar tarefas de gerenciamento em recursos delegados diretamente no portal ou usando APIs e ferramentas de gerenciamento (como CLI do Azure e Azure PowerShell). Todas as APIs existentes podem ser usadas ao trabalhar com recursos delegados, desde que a funcionalidade tenha suporte para gerenciamento entre locatários e o usuário tenha as permissões apropriadas.

Também fornecemos APIs para executar tarefas de gerenciamento de recursos delegadas do Azure. Para obter mais informações, consulte a seção de **referência** .

## <a name="enhanced-services-and-scenarios"></a>Cenários e serviços aprimorados

A maioria das tarefas e serviços pode ser executado em recursos delegados entre locatários gerenciados. Abaixo estão alguns dos principais cenários em que o gerenciamento entre locatários pode ser eficaz.

[Arco do Azure para servidores (visualização)](https://docs.microsoft.com/azure/azure-arc/servers/overview):

- [Conectar computadores Windows Server ou Linux fora do Azure](https://docs.microsoft.com/azure/azure-arc/servers/quickstart-onboard-portal) a assinaturas delegadas e/ou grupos de recursos no Azure
- Gerenciar computadores conectados usando construções do Azure, como Azure Policy e marcação

[Automação do Azure](https://docs.microsoft.com/azure/automation/):

- Usar contas de automação para acessar e trabalhar com recursos de cliente delegados

[Backup do Azure](https://docs.microsoft.com/azure/backup/):

- Fazer backup e restaurar dados do cliente em locatários do cliente

[AKs (serviço kubernetes do Azure)](https://docs.microsoft.com//azure/aks/):

- Gerenciar ambientes kubernetes hospedados e implantar e gerenciar aplicativos em contêineres dentro de locatários do cliente

[Azure monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Exibir alertas para assinaturas delegadas, com a capacidade de exibir alertas em todas as assinaturas
- Exibir detalhes do log de atividades para assinaturas delegadas
- Log Analytics: consultar dados de espaços de trabalho de clientes remotos em vários locatários
- Criar alertas em locatários do cliente que disparam a automação, como runbooks de automação do Azure ou Azure Functions, no locatário do provedor de serviços por meio de WebHooks

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/):

- Instantâneos de conformidade mostram detalhes de políticas atribuídas em assinaturas delegadas
- Criar e Editar definições de política em uma assinatura delegada
- Atribuir definições de política definidas pelo cliente dentro da assinatura delegada
- Os clientes veem as políticas criadas pelo provedor de serviços junto com as políticas que eles mesmos criaram
- Pode [corrigir deployIfNotExists ou modificar atribuições dentro do locatário do cliente](../how-to/deploy-policy-remediation.md)

[Grafo de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/):

- Agora inclui a ID do locatário nos resultados da consulta retornados, permitindo que você identifique se uma assinatura pertence ao locatário do cliente ou ao locatário do provedor de serviço

[Central de segurança do Azure](https://docs.microsoft.com/azure/security-center/):

- Visibilidade entre locatários
  - Monitorar a conformidade com as políticas de segurança e garantir a cobertura de segurança em todos os recursos dos locatários
  - Monitoramento contínuo da conformidade regulatória entre vários clientes em uma única exibição
  - Monitorar, fazer triagem e priorizar recomendações de segurança acionáveis com o cálculo de Pontuação seguro
- Gerenciamento de postura de segurança entre locatários
  - Gerenciar políticas de segurança
  - Execute ações em recursos que estão fora de conformidade com recomendações de segurança acionáveis
  - Coletar e armazenar dados relacionados à segurança
- Proteção e detecção de ameaças entre locatários
  - Detectar ameaças nos recursos dos locatários
  - Aplicar controles de proteção avançada contra ameaças, como acesso à VM just-in-time (JIT)
  - Proteger a configuração do grupo de segurança de rede com o fortalecimento de rede adaptável
  - Verifique se os servidores estão executando apenas os aplicativos e processos que eles devem estar com controles de aplicativo adaptáveis
  - Monitorar alterações em arquivos importantes e entradas de registro com o monitoramento de integridade de arquivo (FIM)

[Sentinela do Azure](https://docs.microsoft.com/azure/sentinel/multiple-tenants-service-providers):

- Gerenciar recursos do Azure Sentinel em locatários do cliente

[Integridade do serviço do Azure](https://docs.microsoft.com/azure/service-health/):

- Monitore a integridade dos recursos do cliente com o Azure Resource Health
- Acompanhe a integridade dos serviços do Azure usados por seus clientes

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Gerenciar opções de recuperação de desastre para máquinas virtuais do Azure em locatários do cliente (Observe que você não pode usar contas Executar como para copiar extensões de VM)

[Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/):

- Usar extensões de máquina virtual para fornecer tarefas de configuração e automação de pós-implantação em VMs do Azure em locatários do cliente
- Usar o diagnóstico de inicialização para solucionar problemas de VMs do Azure em locatários do cliente
- Acessar VMs com o console serial em locatários do cliente
- Observe que você não pode usar Azure Active Directory para fazer logon remoto em uma VM e não pode integrar uma VM com uma Key Vault para senhas, segredos ou chaves de criptografia para criptografia de disco

[Rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/):

- Implantar e gerenciar redes virtuais e vNICs (placas de interface de rede virtual) em locatários do cliente

Solicitações de suporte:

- Abrir solicitações de suporte para recursos delegados da folha **ajuda + suporte** na portal do Azure (selecionando o plano de suporte disponível para o escopo delegado)

## <a name="current-limitations"></a>Limitações atuais
Com todos os cenários, esteja ciente das seguintes limitações atuais:

- As solicitações manipuladas pelo Azure Resource Manager podem ser executadas usando o gerenciamento de recursos delegado do Azure. Os URIs de operação para essas solicitações começam com `https://management.azure.com`. No entanto, as solicitações que são manipuladas por uma instância de um tipo de recurso (por exemplo, acesso a dados de armazenamento ou acesso aos segredos do keyvault) não têm suporte com o gerenciamento de recursos delegado do Azure. Os URIs de operação para essas solicitações normalmente começam com um endereço que é exclusivo de sua instância, como `https://myaccount.blob.core.windows.net` ou `https://mykeyvault.vault.azure.net/`. A última opção também são operações de dados em vez de operações de gerenciamento. 
- As atribuições de função devem usar [funções internas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)de RBAC (controle de acesso baseado em função). Atualmente, todas as funções internas têm suporte com o gerenciamento de recursos delegado do Azure, exceto o proprietário ou qualquer função interna com a permissão [Dataactions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) . A função Administrador de acesso do usuário tem suporte apenas para uso limitado na [atribuição de funções a identidades gerenciadas](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Não há suporte para funções personalizadas e [funções de administrador de assinatura clássica](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) .
- No momento, não é possível carregar uma assinatura (ou grupo de recursos em uma assinatura) para o gerenciamento de recursos delegado do Azure se a assinatura usar Azure Databricks. Da mesma forma, se uma assinatura tiver sido registrada para integração com o provedor de recursos **Microsoft. managedservices** , você não poderá criar um espaço de trabalho do databricks para essa assinatura no momento.
- Embora você possa integrar assinaturas e grupos de recursos para o gerenciamento de recursos delegado do Azure que têm bloqueios de recursos, esses bloqueios não impedirão que as ações sejam executadas por usuários no locatário de gerenciamento. As [atribuições de negação](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) que protegem recursos gerenciados pelo sistema, como aquelas criadas por aplicativos gerenciados do Azure ou plantas do Azure (atribuições de negação atribuída pelo sistema), impedem que os usuários no locatário de gerenciamento atuem nesses recursos; no entanto, no momento, os usuários no locatário do cliente não podem criar suas próprias atribuições de negação (atribuições de negação atribuída pelo usuário).

## <a name="next-steps"></a>Passos seguintes

- Integre seus clientes ao gerenciamento de recursos delegado do Azure, seja [usando modelos de Azure Resource Manager](../how-to/onboard-customer.md) ou [publicando uma oferta privada ou pública de serviços gerenciados para o Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Exiba e gerencie clientes](../how-to/view-manage-customers.md) acessando **meus clientes** na portal do Azure.
