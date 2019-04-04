---
title: O que é o Azure Stack? | Microsoft Docs
description: Saiba como o Azure Stack permite-lhe executar serviços do Azure no seu datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: e5e05ad4f2ae7e718e160916144f03bfb74a2a52
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631316"
---
# <a name="azure-stack-overview"></a>Descrição geral do Azure Stack

O Azure Stack é uma extensão do Azure que fornece uma forma de executar aplicações num ambiente no local e fornecer serviços do Azure no seu datacenter. Com uma plataforma de cloud consistente, as organizações conseguir podem tomar as decisões de tecnologia com base nos requisitos de negócios, em vez de decisões de negócios com base em limitações de tecnologia.

## <a name="why-use-azure-stack"></a>Porquê utilizar o Azure Stack?

O Azure fornece uma plataforma avançada para os desenvolvedores criem aplicativos modernos. No entanto, alguns aplicativos baseado na nuvem enfrentam obstáculos como latência, conectividade intermitente e regulamentos. Azure e o Azure Stack desbloquear novos casos de utilização de cloud híbrida para tanto destinada aos clientes internos e aplicativos de linha de negócio:

- **Edge e soluções desligadas**. Abordar a latência e requisitos de conectividade ao processar dados localmente no Azure Stack e, em seguida, agregá-la no Azure para uma análise adicional, com a lógica comum da aplicação entre ambos. Pode até implementar o Azure Stack desligado da internet sem conectividade para o Azure. Acho que de chão de fábrica, o cruzeiro é fornecido e mina shafts como exemplos.

- **Na nuvem que cumpram os regulamentos variados**. Desenvolver e implementar aplicações no Azure, com flexibilidade total para implementar no local com o Azure Stack para atender às normas ou requisitos da política, sem alterações de código necessários. Os exemplos de aplicativos incluem global auditoria, relatórios financeiros, comércio de câmbio, jogos online e relatório de despesas.

- **Aplicação modelo no local da cloud**. Utilize serviços do Azure, contentores, sem servidor e arquiteturas de microsserviços para atualizar e ampliar as aplicações existentes ou crie novas aplicações. Processos de DevOps consistente de utilização em todo o Azure na nuvem e do Azure Stack no local para acelerar a modernização de aplicações para os aplicativos de missão crítica de principais.

O Azure Stack permite que esses cenários de utilização ao fornecer:

- Uma experiência de entrega integrada para assegurar o funcionamento e a funcionar rapidamente com os do Azure Stack sistemas integrados da parceiros de hardware fidedigno. Após a entrega, o Azure Stack fácil integração com o Centro de dados com a monitorização através da extensão do pacote de gestão do System Center Operations Manager ou Nagios.

- Gestão de identidades flexível a utilizar o Azure Active Directory (Azure AD) para ambientes de híbridos do Azure e o Azure Stack e tirar partido de serviços de Federação do Active Directory (AD FS) para desligado implementações. 

- Se aproxima de um ambiente de desenvolvimento de aplicativos consistentes do Azure para maximizar a produtividade do desenvolvedor e ativar DevOps comuns em ambientes híbridos.

- Entrega de serviços do Azure a partir de locais usando o poder de informática na cloud híbrida. Adote práticas operacionais comuns no Azure e o Azure Stack para implantar e operar serviços do Azure IaaS e PaaS, usando as mesmas experiências administrativas e as ferramentas do Azure. A Microsoft fornece a inovação contínua do Azure para o Azure Stack, incluindo novos serviços do Azure, as atualizações para os serviços existentes e de aplicações do Azure Marketplace e imagens adicionais.

## <a name="azure-stack-architecture"></a>Arquitetura de pilha do Azure
O Azure Stack, sistemas integrados são compostos por em racks de servidores de 4 a 16 criados por parceiros de hardware fidedignos e entregues diretamente para o seu datacenter. Após a entrega, um fornecedor de soluções irá trabalhar para implementar o sistema integrado e certifique-se que a solução do Azure Stack satisfaz os requisitos de negócio. Terá de preparar o seu datacenter ao garantir a energia tudo necessários e resfriamento, conectividade de limite e outro requisito de integração do Centro de dados necessários estão em vigor. 

> Para obter mais informações sobre a experiência de integração de datacenter do Azure Stack, veja [integração no datacenter do Azure Stack](azure-stack-customer-journey.md).

O Azure Stack baseia-se em hardware padrão do setor e é gerido com as mesmas ferramentas que já utiliza para gerir as subscrições do Azure. Como resultado, pode aplicar processos de DevOps consistentes, se estiver ligado ao Azure ou não. 

Arquitetura do Azure Stack permite-lhe fornecer serviços do Azure na borda de localizações remotas ou conectividade intermitente, desligado da internet. Pode criar soluções híbridas que processam dados localmente no Azure Stack e, em seguida, agregá-la no Azure para processamento adicional e análise. Por fim, como o Azure Stack é instalado no local, pode satisfazer requisitos de regulamentação ou de política específicos com a flexibilidade de implementação na cloud aplicações no local sem alterar nenhum código. 

## <a name="deployment-options"></a>Opções de implementação

### <a name="production-or-evaluation-environments"></a>Ambientes de produção ou avaliação
O Azure Stack está disponível em duas opções de implementação para atender às suas necessidades, sistemas integrados do Azure Stack para utilização em produção e o Azure Stack Development Kit (ASDK) para avaliar o Azure Stack:

- **Sistemas integrados do Azure Stack**. O Azure Stack, sistemas integrados estão disponíveis através de uma parceria de parceiros Microsoft e de hardware, criação de uma solução que oferece inovação personalizado de cloud e a simplicidade de gerenciamento de computação. Por o Azure Stack ser oferecido como um sistema de hardware e software integrados, tem a flexibilidade e controlo de que precisa, juntamente com a capacidade de inovar na cloud. Sistemas de pilha integrado do Azure vão de tamanho de 4 a 16 nós e, em conjunto, são suportados pela Microsoft e parceiros de hardware. Utilize os sistemas do Azure Stack integrados para criar novos cenários e implementar novas soluções para as cargas de trabalho de produção.

- **Kit de desenvolvimento do Azure Stack**. O [do Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) é uma implementação gratuita e de nó único do Azure Stack que pode utilizar para avaliar e aprender sobre o Azure Stack. Também pode utilizar o ASDK como um ambiente de programação para criar aplicações com as APIs e ferramentas que seja consistente com o Azure. No entanto, o ASDK não se destina a ser utilizado como um ambiente de produção e tem as seguintes limitações em comparação com a implementação de produção completa de sistemas integrados:

    - O ASDK só pode ser associado a um único do Azure Active Directory (Azure AD) ou o fornecedor de identidade de serviços de Federação do Active Directory (AD FS).
    - Como os componentes da pilha do Azure são implementados num computador anfitrião único, existem limitados de recursos físicos disponíveis para os recursos de inquilino. Esta configuração não se destina à escala ou de avaliação de desempenho.
    - Os cenários de rede estão limitados devido aos requisitos de implementação NIC e de único anfitrião.

### <a name="connection-models"></a>Modelos de ligação
Pode optar por implementar o Azure Stack tanto **ligados** à internet (e para o Azure) ou **desligado** do mesmo. Esta opção define quais opções estão disponíveis para o armazenamento de identidade (Azure AD ou AD FS) e o modelo de faturação (pague à medida que com base em utilização com base na capacidade de faturação ou faturação).

> Para obter mais informações, veja as considerações para [ligados](azure-stack-connected-deployment.md) e [desligado](azure-stack-disconnected-deployment.md) modelos de implementação. 

### <a name="identity-provider"></a>Fornecedor de identidade 
O Azure Stack utiliza o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS) como fornecedor de identidade para estabelecer identidades do Azure Stack. 

> [!IMPORTANT]
> Esse é um ponto de decisão principais! Ao escolher o Azure AD ou AD FS, como o fornecedor de identidade é uma decisão única que é necessário efetuar no momento da implementação. Não pode alterar isto mais tarde sem voltar a implementar todo o sistema.

O Azure AD é o fornecedor de identidade baseada na cloud, multi-inquilino da Microsoft. A maioria dos cenários híbridos com implementações ligados à internet utilizam o Azure AD como o armazenamento de identidade. No entanto, pode optar por utilizar os serviços de Federação do Active Directory (AD FS) para implementações desconectadas do Azure Stack. Fornecedores de recursos do Azure Stack e outros aplicativos, funcionam quase da mesma forma com o AD FS, tal como com o Azure AD. O Azure Stack inclui sua própria instância do Active Directory e uma API de gráfico do Active Directory. 

> Pode saber mais sobre as considerações de identidade do Azure Stack na [descrição geral de identidade para o Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Como é gerido do Azure Stack?
Após a implantação do Azure Stack numa implementação de sistemas integrados ou uma instalação de ASDK, os principais métodos de interação com o Azure Stack são o portal de administração, o portal de utilizador e o PowerShell. Os portais do Azure Stack são apoiados cada por instâncias separadas do Gestor de recursos do Azure. Uma **operador do Azure Stack** utiliza o portal de administração para gerir o Azure Stack e para fazer coisas como criar ofertas de inquilino e manter o estado de funcionamento e o monitor de estado do sistema integrado. O portal de utilizador (também referido como o portal de inquilinos) fornece uma experiência self-service para consumo de recursos de nuvem, como máquinas virtuais, contas de armazenamento e aplicações web. 

> Para obter mais informações sobre a gestão com o portal de administração do Azure Stack, veja a utilização a [início rápido o portal de administração do Azure Stack](azure-stack-manage-portals.md).

Como um operador de pilha do Azure, pode fornecer uma ampla variedade de serviços e aplicações, tal como [máquinas virtuais](azure-stack-tutorial-tenant-vm.md), [aplicativos web](azure-stack-app-service-overview.md)altamente disponível [do SQL Server](azure-stack-tutorial-sql.md), e [servidor MySQL](azure-stack-tutorial-mysql.md) bases de dados. Também pode utilizar [modelos do Azure Stack início rápido do Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) para implantar o SharePoint, Exchange e muito mais. 

Utilizar o portal de administração, pode [configurar o Azure Stack para fornecer serviços](azure-stack-plan-offer-quota-overview.md) para inquilinos que utilizam os planos, quotas, ofertas e subscrições. Os utilizadores de inquilino podem subscrever várias ofertas. Ofertas podem ter um ou mais planos e planos podem ter um ou mais serviços. Operadores também capacidade de gerir e respondem a alertas. 

Quando é configurado o Azure Stack, uma **utilizadores do Azure Stack** (também referido como um inquilino) consome serviços que oferece o operador. Os utilizadores podem aprovisionar, monitorizar e gerir os serviços subscreveu, tais como aplicações web, armazenamento e máquinas virtuais.

> Para saber mais sobre a gestão do Azure Stack, incluindo o que contas para utilizar where, responsabilidades de operador típicas, o que dizer aos utilizadores e como obter ajuda, reveja [Noções básicas de administração do Azure Stack](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Fornecedores de recursos 
Fornecedores de recursos são serviços da web que formam a base para todos os IaaS de pilha do Azure e serviços PaaS. O Azure Resource Manager conta com fornecedores de recursos diferentes para fornecer acesso aos serviços. Cada fornecedor de recursos ajuda-o a configurar e controlar os respetivos recursos. Os administradores de serviços também podem adicionar novos fornecedores de recursos personalizado. 

### <a name="foundational-resource-providers"></a>Fornecedores de recursos básicos 
Existem três fornecedores de recursos de IaaS fundamentais: Computação, rede e armazenamento:

- **Computação**. O fornecedor de recursos de computação permite que os inquilinos do Azure Stack criar suas próprias máquinas virtuais. O fornecedor de recursos de computação inclui a capacidade de criar máquinas virtuais, bem como as extensões de Máquina Virtual. O serviço de extensão de Máquina Virtual ajuda a fornecer recursos de IaaS para máquinas virtuais Windows e Linux.  Por exemplo, pode utilizar o fornecedor de recursos de computação para Aprovisionar uma máquina virtual do Linux e executar scripts de Bash durante a implementação para configurar a VM.
- **Fornecedor de recursos de rede**. O fornecedor de recursos de rede fornece uma série de recursos definida pelo Software (SDN) e Virtualização de função de rede (NFV) para a nuvem privada. Pode utilizar o fornecedor de recursos de rede para criar recursos, como balanceadores de carga de software, IPs públicos, os grupos de segurança de rede e redes virtuais.
- **Fornecedor de recursos de armazenamento**. O fornecedor de recursos de armazenamento fornece quatro serviços de armazenamento do Azure consistente: [blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [fila](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabela](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)e funções de gerenciamento de gestão de contas de Cofre de chaves e auditoria de segredos, tais como palavras-passe e certificados. O fornecedor de recursos de armazenamento também oferece um serviço de administração do armazenamento na cloud para facilitar a administração de fornecedor de serviço dos serviços de armazenamento do Azure consistente. O armazenamento do Azure fornece a flexibilidade para armazenar e obter grandes quantidades de dados não estruturados, como documentos e ficheiros de multimédia com Blobs do Azure, e NoSQL estruturado com base em dados com as tabelas do Azure. 

### <a name="optional-resource-providers"></a>Fornecedores de recursos opcionais
Existem três fornecedores de recursos PaaS opcionais que pode implementar e utilizar com o Azure Stack: Fornecedores de recursos do serviço de aplicações, o SQL Server e o servidor MySQL:

- **Serviço de aplicações**. [Serviço de aplicações do Azure no Azure Stack](azure-stack-app-service-overview.md) é uma oferta de plataforma-como-serviço (PaaS) do Microsoft Azure disponíveis para o Azure Stack. O serviço ativa a seus clientes internos ou externos criar as funções do Azure, API e web, aplicativos para qualquer plataforma ou dispositivo. 
- **SQL Server**. Utilize o [fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider.md) para oferecer bases de dados SQL como um serviço do Azure Stack. Depois de instalar o fornecedor de recursos e ligá-la a uma ou mais instâncias do SQL Server, e seus usuários podem criar bases de dados para aplicações nativas da cloud, Web sites que utilizam o SQL e outras cargas de trabalho que usam SQL.
- **Servidor MySQL**. Utilize o [fornecedor de recursos do servidor MySQL](azure-stack-mysql-resource-provider-deploy.md) expor bases de dados MySQL como um serviço do Azure Stack. O fornecedor de recursos do MySQL é executado como um serviço numa máquina virtual (VM) do Windows Server 2016 Server Core.

## <a name="providing-high-availability"></a>Fornecendo alta disponibilidade
Para assegurar elevada disponibilidade de um sistema de produção de várias VMS no Azure, as VMs são colocadas num [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) que propaga-las em vários domínios de falha e domínios de atualização. Na escala mais pequena do Azure Stack, um domínio de falha num conjunto de disponibilidade é definido como um único nó na unidade de escala.  

Enquanto a infraestrutura do Azure Stack já está a ser resiliente a falhas, a tecnologia subjacente (clustering de ativação pós-falha) ainda incorre num período de indisponibilidade para as VMs num servidor físico afetado se houver uma falha de hardware. O Azure Stack oferece suporte a ter um conjunto de disponibilidade com um máximo de três domínios de falha para ser consistente com o Azure.

- **Domínios de falha**. Serão fisicamente isoladas umas das outras VMs colocadas num conjunto de disponibilidade ao propagá-los de forma mais uniforme possível através de vários domínios de falha (nós do Azure Stack). Se houver uma falha de hardware, VMs a partir do domínio de falha com falha irão ser reiniciadas em outros domínios de falha, mas, se possível, mantidas em domínios de falha de outras VMs no mesmo conjunto de disponibilidade. Quando o hardware estiver online novamente, as VMs vão ser reequilibradas para manter uma elevada disponibilidade. 
 
- **Domínios de atualização**. Domínios de atualização são outro conceito do Azure que fornece elevada disponibilidade em conjuntos de disponibilidade. Um domínio de atualização é um grupo lógico de hardware subjacente que pode entrar em manutenção ao mesmo tempo. VMs localizadas no mesmo domínio de atualização serão reiniciadas em conjunto durante a manutenção planeada. Como os inquilinos cria VMs num conjunto de disponibilidade, a plataforma Azure distribui automaticamente as VMs por estes domínios de atualização. No Azure Stack, as VMs estão no ar migrados entre os outros anfitriões no cluster online antes do anfitrião subjacente é atualizado. Uma vez que não existe nenhum tempo de inatividade de inquilino durante uma atualização de anfitrião, a funcionalidade do domínio de atualização no Azure Stack só existe para compatibilidade com o modelo com o Azure. 

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
Pode usar o controle de acesso baseado em ' (RBAC) da função para conceder acesso de sistema para os utilizadores autorizados, grupos e serviços ao atribuir-lhes funções numa subscrição, grupo de recursos ou ao nível de recursos individuais. Cada função define o nível de acesso que um utilizador, grupo ou serviço tem sobre recursos do Microsoft Azure Stack.

RBAC do Azure Stack tem três funções básicas que se aplicam a todos os tipos de recursos: Proprietário, Contribuidor e leitor. O proprietário tem acesso total a todos os recursos, incluindo o direito de delegar o acesso a outras pessoas. Contribuidor pode criar e gerir todos os tipos de recursos do Azure, mas não é possível conceder acesso a outras pessoas. Leitor só pode ver os recursos existentes. O restante das funções do RBAC permitir a gestão de recursos específicos do Azure. Por exemplo, a função de contribuinte de Máquina Virtual permite a criação e gestão de máquinas virtuais, mas não permite a gestão de rede virtual ou a sub-rede que a máquina virtual se liga a.

> Ver [controlo de acesso de Manage Role-Based](azure-stack-manage-permissions.md) para obter mais informações. 

## <a name="reporting-usage-data"></a>Dados de utilização de relatórios
Microsoft Azure Stack recolhe e agrega dados de utilização em todos os fornecedores de recursos e transmite-lo para o Azure para processamento pelo Azure commerce. Os dados de utilização recolhidos no Azure Stack podem ser visualizados por meio de uma API REST. Existe uma API de inquilino do Azure consistente, bem como fornecedor e APIs do fornecedor de delegado para obter dados de utilização em todas as subscrições de inquilino. Estes dados podem ser utilizados para integrar com uma ferramenta externa ou o serviço de faturação ou estorno. Assim que a utilização tiver sido processada pelo Azure commerce, podem ser visualizado no portal de faturação do Azure.

> Saiba mais sobre [relatórios de dados de utilização do Azure Stack para o Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Passos Seguintes

[Comparar o Azure Stack e o global Azure](compare-azure-azure-stack.md)

[Noções básicas de administração](azure-stack-manage-basics.md)

[Início rápido: utilizar o portal de administração do Azure Stack](azure-stack-manage-portals.md)