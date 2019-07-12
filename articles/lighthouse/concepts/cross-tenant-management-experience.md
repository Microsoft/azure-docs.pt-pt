---
title: Experiências de gestão entre inquilinos com o Azure Lighthouse
description: Gestão de recursos do Azure de delegado permite uma experiência de gestão entre inquilinos.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 15454d4b3f0abad6166c4b163df6c8652669d649
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809905"
---
# <a name="cross-tenant-management-experiences"></a>Experiências de gestão entre inquilinos

Este artigo descreve os cenários que, como um fornecedor de serviços, pode utilizar com o [Azure delegadas a gestão de recursos](../concepts/azure-delegated-resource-management.md) para gerir recursos do Azure para vários clientes a partir de seu próprio inquilino no [portal do Azure ](https://portal.azure.com).

> [!NOTE]
> Gestão de recursos do Azure de delegado também pode ser utilizado dentro de uma empresa que tem vários inquilinos do seu próprio para simplificar a administração de entre inquilinos.

## <a name="understanding-customer-tenants"></a>Compreender os inquilinos do cliente

Um inquilino do Azure Active Directory (Azure AD) é uma representação de uma organização. É uma instância dedicada do Azure AD que uma organização recebe quando cria uma relação com a Microsoft ao inscrever-se para o Azure, Microsoft 365 ou outros serviços. Cada inquilino do Azure AD é distinto e separado de outros inquilinos do Azure AD e tem seu próprio ID de inquilino (um GUID). Para obter mais informações, consulte [o que é o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

Normalmente, para gerir recursos do Azure para um cliente, os fornecedores de serviços teria de iniciar sessão no portal do Azure com uma conta associada ao inquilino do cliente, exigindo que um administrador no inquilino do cliente para criar e gerir contas de utilizador para o fornecedor de serviços.

Com a gestão de recursos do Azure de delegado, o processo de integração Especifica os utilizadores no inquilino do fornecedor de serviços que poderão aceder e gerir subscrições, grupos de recursos e recursos no inquilino do cliente. Estes utilizadores, em seguida, poderá entrar no portal do Azure com as suas próprias credenciais. No portal do Azure, podem gerir recursos pertencentes a todos os clientes para os quais tenham acesso. Isso pode ser feito ao visitar a [meus clientes](../how-to/view-manage-customers.md) página no portal do Azure ou ao trabalhar diretamente no contexto de subscrição do cliente, no portal do Azure ou através de APIs.

Gestão de recursos do Azure de delegado permite maior flexibilidade gerir os recursos para vários clientes sem ter de iniciar sessão nas contas diferentes em inquilinos diferentes. Por exemplo, um fornecedor de serviços pode ter três clientes, com responsabilidades diferentes e níveis de acesso, como mostrado aqui:

![Três inquilinos de cliente que mostra as responsabilidades do fornecedor de serviço](../media/azure-delegated-resource-management-customer-tenants.jpg)

Utilizando a gestão de recursos do Azure de delegado, os utilizadores autorizados podem iniciar sessão no inquilino do fornecedor de serviços para aceder a estes recursos, como mostrado aqui:

![Recursos de cliente geridos através do inquilino do fornecedor de um serviço](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="supported-services-and-scenarios"></a>Serviços suportados e cenários

Atualmente, a experiência de gestão entre inquilinos suporta os seguintes cenários com recursos de cliente delegado:

[A automatização do Azure](https://docs.microsoft.com/azure/automation/):

- Utilizar contas de automatização para acessar e trabalhar com recursos de cliente delegado

[O Azure Backup](https://docs.microsoft.com/azure/backup/):

- Criar cópias de segurança e restaurar dados do cliente em inquilinos do cliente

[Serviço Kubernetes do Azure (AKS)](https://docs.microsoft.com//azure/aks/):

- Gerir ambientes hospedados do Kubernetes e implementar e gerir aplicações em contentores dentro de inquilinos do cliente

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Ver alertas para o delegado subscrições no portal do Azure ou através de programação por meio de chamadas de REST API, com a capacidade de ver alertas em todas as subscrições
- Ver detalhes de registo de atividade para subscrições de delegado
- Análise de registos: Consultar dados de áreas de trabalho do cliente remoto em vários inquilinos

[O Azure Policy](https://docs.microsoft.com/azure/governance/policy/):

- Instantâneos de conformidade mostram detalhes para as políticas atribuídas nas subscrições delegadas
- Criar e editar as definições de política dentro de uma assinatura do delegado
- Atribuir definições de política definida pelo cliente dentro da subscrição de delegado
- Os clientes veem as políticas criadas pelo provedor de serviços, juntamente com todas as políticas que já criados próprios
- Pode remediar as atribuições de deployIfNotExists dentro os inquilinos do cliente se o cliente tiver configurado a identidade gerida e *roleDefinitionIds* para essa atribuição de política

[Gráfico de recursos do Azure](https://docs.microsoft.com/azure/governance/resource-graph/):

- Agora inclui o ID de inquilino nos resultados de consulta retornado, permitindo-lhe identificar se uma subscrição pertence ao inquilino de cliente ou ao inquilino de fornecedor de serviço

[Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/):

- Visibilidade entre inquilinos
  - Monitorizar a conformidade com as políticas de segurança e certifique-se a cobertura de segurança de todos os recursos de todos os inquilinos
  - Conformidade a normas contínua monitorizar em vários clientes numa única vista
  - Monitorizar, a triagem e a atribuir prioridades a segurança acionáveis recomendações com secure cálculo de pontuação
- Gestão da postura de segurança entre inquilinos
  - Gerir políticas de segurança
  - Tomar medidas em recursos que não estejam em conformidade com as recomendações de segurança acionáveis
  - Recolher e armazenar dados relacionados à segurança
- Deteção de ameaças entre inquilinos e proteção
  - Detete ameaças todos os recursos dos inquilinos
  - Aplicar controlos de proteção avançada contra ameaças, tais como o acesso do just-in-time (JIT) da VM
  - Proteger a configuração de grupo de segurança de rede com o sistema de proteção de rede adaptável
  - Certifique-se de que servidores estiverem a executar apenas as aplicações e os processos que devem ser com controlos de aplicação adaptável
  - Monitorizar as alterações ao arquivos importantes e entradas de registro com o ficheiro de integridade de monitorização (FIM)

[Estado de funcionamento do serviço do Azure](https://docs.microsoft.com/azure/service-health/):

- Monitorizar o estado de funcionamento dos recursos de cliente com o Azure Resource Health
- Controlar o estado de funcionamento dos serviços do Azure utilizados pelos clientes

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Gerir opções de recuperação após desastre para máquinas virtuais do Azure em inquilinos do cliente (Observe que não é possível utilizar contas RunAs para copiar as extensões de VM)

[Máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/):

- Utilizar extensões de máquina virtual para fornecer as tarefas de automatização e configuração de pós-implementação em VMs do Azure em inquilinos do cliente
- Utilize diagnósticos de arranque para resolver problemas de VMs do Azure em inquilinos do cliente
- VMs de acesso à consola de série em inquilinos do cliente
- Tenha em atenção que não é possível utilizar o Azure Active Directory para início de sessão remoto a uma VM e não é possível integrar uma VM com um cofre de chaves para palavras-passe, segredos ou chaves criptográficas para a encriptação de disco

[Rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/):

- Implementar e gerir redes virtuais e placas de interface de rede virtuais (vNICs) dentro de inquilinos do cliente

Pedidos de suporte:

- Abrir pedidos de suporte para recursos delegados a partir da **ajuda + suporte** painel no portal do Azure (selecionando o plano de suporte disponível para o âmbito de delegado)

Com todos os cenários,. tenha em atenção as seguintes limitações atuais:

- Pedidos processados pelo Gestor de recursos do Azure podem ser efetuados com a gestão de recursos do Azure de delegado. A operação de URIs para estes pedidos começam com `https://management.azure.com`. No entanto, os pedidos que são processados por uma instância de um tipo de recurso (por exemplo, ou acesso a dados do armazenamento de acesso de segredos do Cofre de chaves) não são suportados com a gestão de recursos do Azure de delegado. A operação de URIs para estes pedidos normalmente começa com um endereço que é exclusivo para a sua instância, tal como `https://myaccount.blob.core.windows.net` ou `https://mykeyvault.vault.azure.net/`. A última opção também é, normalmente, as operações de dados em vez de operações de gestão. 
- Atribuições de função tem de utilizar o controlo de acesso baseado em funções (RBAC) [funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Todas as funções incorporadas são atualmente suportadas com a gestão de recursos do Azure de delegados, exceto para o proprietário, o administrador de acesso de utilizadores ou quaisquer funções incorporadas com [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) permissão. Funções personalizadas e [funções de administrador de subscrição clássica](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) não também são suportadas.
- Atualmente, não é possível carregar uma subscrição (ou grupo de recursos numa subscrição) para o Azure delegadas a gestão de recursos se a subscrição utiliza o Azure Databricks. Da mesma forma, se uma subscrição tiver sido registrada para a integração com o **Microsoft.ManagedServices** fornecedor de recursos, não poderá criar uma área de trabalho do Databricks para essa subscrição neste momento.

## <a name="using-apis-and-management-tools-with-cross-tenant-management"></a>Usando ferramentas de gestão e de APIs com a gestão de entre inquilinos

Para os serviços suportados e cenários listados acima, pode efetuar tarefas de gestão diretamente no portal ou através de ferramentas de gestão e de APIs (por exemplo, a CLI do Azure e o Azure PowerShell). Todas as APIs existentes podem servir-se ao trabalhar com recursos delegados (para os serviços que são suportados).

Também existem APIs específicas para executar tarefas de gestão de recursos do Azure de delegado. Para obter mais informações, consulte a **referência** secção.


## <a name="next-steps"></a>Passos Seguintes

- Integrar os seus clientes para o Azure delegadas a gestão de recursos, ao [utilizando modelos Azure Resource Manager](../how-to/onboard-customer.md) ou pelo [publicar uma oferta de serviços geridos privado ou público no Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Ver e gerir os clientes](../how-to/view-manage-customers.md) ao aceder **meus clientes** no portal do Azure.