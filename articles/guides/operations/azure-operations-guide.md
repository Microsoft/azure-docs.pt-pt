---
title: Guia de introdução para operadores de TI do Azure | Documentos da Microsoft
description: Obtenha o guia de introdução para operadores de TI do Azure
services: ''
documentationcenter: ''
author: themichaelbender-ms
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: overview
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 08/24/2018
ms.author: mibender
ms.openlocfilehash: c1840e2102a0a08e73490319a5633d4db8e0f0bd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767945"
---
# <a name="get-started-for-azure-it-operators"></a>Começar a utilizar para os operadores de TI do Azure

Este guia apresenta conceitos essenciais relacionados com a implementação e gestão de uma infraestrutura do Microsoft Azure. Se estiver familiarizado com a nuvem, computação ou o Azure em si, este guia ajuda rapidamente a ajudá-lo aos conceitos, implementação e detalhes de gestão. Muitas secções deste guia discutem uma operação como implementar uma máquina virtual e, em seguida, fornecem uma ligação para detalhes técnicos aprofundados.

## <a name="cloud-computing-overview"></a>Descrição geral de informática na cloud

Fornece uma alternativa moderna para o datacenter tradicional no local a informática na cloud. Fornecedores de cloud pública fornecerem e gerir todos os infraestrutura de computação e o software de gestão subjacente. Esses fornecedores oferecem uma grande variedade de serviços cloud. Neste caso um serviço em nuvem pode ser uma máquina virtual, um servidor web ou o motor de base de dados alojado na cloud. Como um cliente do fornecedor de cloud, alugar destes serviços cloud de forma conforme necessário. Ao fazer isso, é possível converter a despesa de capital de manutenção de hardware numa despesas operacionais. Um serviço em nuvem também oferece estes benefícios:

- Implementação rápida de ambientes de computação de grandes dimensões

- Rápido Desalocação de sistemas que já não são necessárias

- Facilitar a implementação de sistemas tradicionalmente complexas, como balanceadores de carga

- Capacidade para fornecer a capacidade de computação flexíveis ou dimensionar quando necessário

- Ambientes informáticos mais rentáveis

- Aceder a partir de qualquer lugar com um portal baseado na web ou a automatização programática

- Serviços baseados na cloud para satisfazer a maioria das necessidades de computação e de aplicação

Com a infraestrutura no local, tem controlo total sobre o hardware e software que é implementada. Historicamente, isso levou a decisões de aquisição de hardware que se concentram no aumento vertical. Um exemplo está a comprar um servidor com o maior número de núcleos para atender às necessidades de desempenho de pico. Infelizmente, essa infra-estrutura poderá APARENTAREM ser pouco utilizada fora de uma janela de procura. Com o Azure, pode implementar apenas a infraestrutura que necessita e ajustar isso ou reduzir verticalmente em qualquer altura. Isso nos leva a um foco sobre como aumentar horizontalmente por meio da implantação de nós de computação adicionais para satisfazer uma necessidade de desempenho. Dimensionar os serviços cloud é mais rentável do que o aumento vertical, por meio de hardware dispendiosa.

A Microsoft implantou o vários datacenters do Azure em todo o mundo, com mais planeada. Além disso, a Microsoft está aumentando clouds soberanas nas regiões, como a China e Alemanha. As maiores empresas globais podem implementar a centros de dados desta forma, para que utilizar o Azure facilita para as empresas de qualquer tamanho para implementar os serviços próximos dos seus clientes.

Para pequenas empresas, o Azure permite para um ponto de entrada de baixo custo, com a capacidade de dimensionar rapidamente a pedido para aumentos de computação. Isto impede que um grande investimento de capital inicial na infraestrutura, e fornece a flexibilidade para arquitetar e Refazer a arquitetura sistemas, conforme necessário. O uso de ajusta-se bem com o modelo de dimensionamento rápido e falha rápida de crescimento de inicialização de informática na cloud.

Para obter mais informações sobre as regiões do Azure disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Modelo de computação em nuvem

O Azure utiliza um modelo com base em categorias de serviço fornecido aos clientes de informática de na cloud. As três categorias de serviço incluem a infraestrutura como serviço (IaaS), plataforma como serviço (PaaS) e Software como serviço (SaaS). Fornecedores de partilham alguns ou todos a responsabilidade de componentes na pilha de informática em cada uma destas categorias. Vamos dar uma olhada em cada uma das categorias para a nuvem de informática.
![Comparação de pilha informática na cloud](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infraestrutura como serviço

Um fornecedor de nuvem de IaaS é executado e gerencia todos os recursos de computação física e o software necessário para ativar a virtualização do computador. Um cliente deste serviço implementa máquinas virtuais nesses datacenters alojados. Embora as máquinas virtuais estiverem localizadas num datacenter fora do local, o consumidor do IaaS tem controlo sobre a configuração e gestão do sistema operativo, deixando a infraestrutura subjacente para o fornecedor de cloud.

Azure inclui várias soluções de IaaS, incluindo máquinas virtuais, os conjuntos de dimensionamento de máquinas virtuais e a infraestrutura de rede relacionada. As máquinas virtuais são um populares dos serviços de escolha para inicialmente a migrar para o Azure porque ela permite a um modelo de migração "lift- and -shift". Pode configurar uma VM, como a infra-estrutura atualmente em execução os serviços no seu datacenter e, em seguida, migre o seu software para a nova VM. Poderá ter de fazer atualizações de configuração, como URLs para outros serviços ou armazenamento, mas é possível migrar muitos aplicativos dessa forma.

Os conjuntos de dimensionamento de máquinas virtuais são criados por cima das máquinas de virtuais do Azure e fornecem uma forma fácil de implementar clusters de VMs idênticas. Conjuntos de dimensionamento de máquina virtual também suportam o dimensionamento automático, para que as novas VMs podem ser implementadas automaticamente, quando necessário. Desta forma, os conjuntos de dimensionamento de máquinas virtuais numa plataforma ideal para o nível mais alto microsserviços computação clusters de anfitriões, como o Azure Service Fabric e o Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Plataforma como serviço

Com PaaS, vai implementar a aplicação num ambiente que fornece o fornecedor de serviço cloud. O fornecedor faz todo o gerenciamento de infraestrutura para que possa se concentrar na programação de aplicações e gestão de dados.

O Azure fornece que vários PaaS computação ofertas, incluindo a funcionalidade de aplicações Web do serviço de aplicações do Azure e serviços Cloud do Azure (funções web e de trabalho). Em ambos os casos, os desenvolvedores têm várias formas de implementar a sua aplicação sem saber nada sobre os detalhes práticos que o suportam. Os desenvolvedores não precisam criar máquinas virtuais (VMs), utiliza o protocolo RDP (Remote Desktop) para iniciar sessão cada um deles ou instalar a aplicação. Eles apenas apertar um botão (ou fechar a ele), e as ferramentas fornecidas pelo Microsoft aprovisionar as VMs e, em seguida, implementar e instalar a aplicação nos mesmos.

#### <a name="saas-software-as-a-service"></a>SaaS: Software como serviço

SaaS é software que está alojado e gerido centralmente. Normalmente, com base numa arquitetura multi-inquilino — uma única versão da aplicação é utilizada para todos os clientes. Pode ser aumentada horizontalmente para várias instâncias para garantir o melhor desempenho em todas as localizações. Normalmente, o software de SaaS é licenciado através de uma subscrição mensal ou anual. Normalmente, o software de SaaS é licenciado através de uma subscrição mensal ou anual. Fornecedores de software de SaaS são responsáveis por todos os componentes da pilha de software, por isso é tudo o que gere os serviços fornecidos.

Microsoft Office 365 é um bom exemplo de um oferta de SaaS. Os assinantes pagam uma taxa de subscrição mensal ou anual e recebem o Microsoft Exchange, Microsoft OneDrive e o resto do pacote do Microsoft Office como um serviço. Os subscritores recebem sempre a versão mais recente e o Exchange server é gerenciado por si. Em comparação com a instalação e atualização do Office todos os anos, isso é mais barato e requer menos esforço.

## <a name="azure-services"></a>Serviços do Azure

O Azure oferece vários serviços na sua plataforma de informática na cloud. Esses serviços incluem o seguinte.

### <a name="compute-services"></a>Serviços de computação

Serviços para alojar e executar carga de trabalho de aplicação:

- Máquinas virtuais do Azure, Linux e Windows

- Serviços de aplicações (aplicações Web, aplicações móveis, aplicações lógicas, aplicações API e aplicações de funções)

- O Azure Batch (para paralelas em grande escala e tarefas de computação do batch)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Serviços de dados

Serviços para armazenar e gerir dados:

- Armazenamento do Azure (abrange os serviços de Blobs do Azure, fila, tabela e ficheiro)

- Base de Dados SQL do Azure

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Cache do Azure para Redis

### <a name="application-services"></a>Serviço de aplicações

Serviços para criar e operar aplicativos:

- Azure Active Directory (Azure AD)

- O Azure Service Bus para conectar sistemas distribuídos

- O Azure HDInsight para processar grandes volumes de dados

- O Azure Scheduler

- Serviços de multimédia do Azure

### <a name="network-services"></a>Serviços de rede

Serviços de rede no Azure e entre os datacenters do Azure e no local:

- Rede Virtual do Azure

- Azure ExpressRoute

- DNS fornecida pelo Azure

- Traffic Manager do Azure

- Rede de Entrega de Conteúdos do Azure

Para obter documentação detalhada sobre os serviços do Azure, consulte [documentação de serviço do Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Conceitos-chave do Azure

### <a name="datacenters-and-regions"></a>Os Datacenters e regiões

O Azure é uma plataforma de nuvem global, que está disponível em geral em muitas regiões em todo o mundo. Quando aprovisiona um serviço, uma aplicação ou uma VM no Azure, é-lhe perguntado para selecionar uma região. A região selecionada representa um datacenter speciﬁc onde a aplicação é executada. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

Um dos todos os benefícios de utilizar o Azure é que pode implantar seus aplicativos em vários centros de dados em todo o mundo. A região que escolher pode aﬀect o desempenho da sua aplicação. É ideal para escolher uma região mais próxima mais os seus clientes, para reduzir a latência nos pedidos de rede. Também pode selecionar uma região para cumprir os requisitos legais para distribuir a sua aplicação em determinados países.

### <a name="azure-portal"></a>Portal do Azure

O portal do Azure é uma aplicação baseada na web que pode ser utilizada para criar, gerir e remover recursos do Azure e serviços. O portal do Azure está localizado em [portal.azure.com](https://portal.azure.com). Ele inclui um dashboard personalizável e as ferramentas para gerir recursos do Azure. Também fornece informações de faturação e subscrição. Para obter mais informações, consulte [descrição geral do Microsoft Azure portal](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) e [recursos de gerir o Azure através do portal](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Recursos

Recursos do Azure são computação individual, redes, dados ou serviços que tenham sido implantados numa subscrição do Azure de alojamento de aplicações. Alguns recursos comuns são as máquinas virtuais, contas de armazenamento ou bases de dados SQL. Serviços do Azure consistem, muitas vezes, vários recursos relacionados do Azure. Por exemplo, uma máquina virtual do Azure pode incluir uma VM, a conta de armazenamento, o adaptador de rede e o endereço IP público. Estes recursos podem ser criados, geridos e eliminados individualmente ou como um grupo. Recursos do Azure são abordados em mais detalhes posteriormente neste guia.

### <a name="resource-groups"></a>Grupos de recursos

Um grupo de recursos do Azure é um contentor que retém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que pretende gerir como um grupo. Grupos de recursos do Azure são abordados em mais detalhes posteriormente neste guia.

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

Um modelo Azure Resource Manager é um arquivo JavaScript Object Notation (JSON) que define um ou mais recursos para implementar um grupo de recursos. Define as dependências entre os recursos implementados. Modelos do Resource Manager são abordados em mais detalhes posteriormente neste guia.

### <a name="automation"></a>Automatização

Além de criar, gerir e eliminar a recursos com o portal do Azure, pode automatizar essas atividades com o PowerShell ou a interface de linha de comandos (CLI) do Azure.

#### <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell é um conjunto de módulos que fornecem cmdlets para gerir o Azure. Pode utilizar os cmdlets para criar, gerir e remover serviços do Azure. Os cmdlets podem ajudá-lo pode obter implementações consistentes, econômicas e automáticas. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/azurerm/install-azurerm-ps).

#### <a name="azure-command-line-interface"></a>Interface de linha de comandos do Azure

A interface de linha de comandos do Azure é uma ferramenta que pode utilizar para criar, gerir e remover recursos do Azure a partir da linha de comando. A CLI do Azure está disponível para Linux, Mac OS X e Windows. Para obter mais informações e detalhes técnicos, veja [instalar a CLI do Azure](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>APIs REST

Azure baseia-se um conjunto de APIs REST que suportam o portal do Azure da interface do Usuário. A maioria dessas APIs REST também é suportada para permitem-lhe aprovisionar e gerir os recursos do Azure e as aplicações a partir de qualquer dispositivo habilitado para Internet através de programação. Para obter mais informações, consulte a [referência do SDK de REST de Azure](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Os administradores podem aceder a Azure PowerShell e CLI do Azure através de uma experiência acessível para o navegador chamada Azure Cloud Shell. Essa interface interativa fornece uma ferramenta flexível para Linux e Windows que os administradores usem sua interface de linha de comandos de escolha, o Bash ou o PowerShell. O Azure Cloud Shell pode ser o acesso através do portal, como uma interface web autónoma [shell.azure.com](https://shell.azure.com), ou a partir de um número de outros pontos de acesso. Para obter mais informações, consulte [descrição geral do Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Subscrições do Azure

Uma subscrição é um agrupamento lógico de serviços do Azure que está ligado a uma conta do Azure. Uma única conta do Azure pode conter várias subscrições. A faturação de serviços do Azure é feita numa base por subscrição. As subscrições do Azure têm um administrador de conta, que tem controlo total sobre a subscrição, e um administrador de serviço, que tem controlo sobre todos os serviços na subscrição. Para obter informações sobre os administradores de subscrição clássica, consulte [adicionar ou alterar os administradores de subscrição do Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Além dos administradores, contas individuais podem ser concedidas detalhadas de controlo de recursos do Azure, utilizando [controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Selecionar e ativar uma subscrição do Azure

Antes de pode trabalhar com serviços do Azure, precisa de uma subscrição. Vários tipos de subscrição estão disponíveis.

**Contas gratuitas**: A ligação para se inscrever para uma conta gratuita está no [Web site do Azure](https://azure.microsoft.com/). Isso lhe dá um crédito ao longo de 30 dias para experimentar qualquer combinação de recursos no Azure. Se ultrapassar o montante de crédito, a sua conta está suspensa. No final da avaliação, os serviços são descontinuados em deixará de funcionar. Pode atualizar para uma subscrição pay as you go a qualquer momento.

**As assinaturas do MSDN**: Se tiver uma assinatura do MSDN, obtém uma quantidade específica de crédito do Azure por mês. Por exemplo, se tiver um Microsoft Visual Studio Enterprise com a assinatura do MSDN, obtém \$150 $ por mês em créditos do Azure.

Se ultrapassar o montante do crédito, seu serviço estão desativadas até que inicia o mês seguinte. Pode desativar o limite de gastos e adicionar um cartão de crédito a ser utilizado para os custos adicionais. Alguns desses custos são com desconto para contas do MSDN. Por exemplo, paga o preço do Linux para VMs com o Windows Server, e não implica custos adicionais para os servidores da Microsoft como o Microsoft SQL Server. Isso torna ideal para cenários de desenvolvimento e teste as contas do MSDN.

**Contas do BizSpark**: O programa Microsoft BizSpark muitas vantagens para startups. Uma dessas vantagens é o acesso a todo o software da Microsoft para ambientes de desenvolvimento e teste para até cinco contas do MSDN. Obtenha us $150 em créditos do Azure para cada uma dessas cinco contas do MSDN e pagam tarifas reduzidas para várias dos serviços do Azure, como as máquinas virtuais.

**Pay as you go**: Com esta subscrição, paga o que utiliza ao anexar um cartão de crédito ou débito para a conta. Se uma organização, também pode ser aprovada para a faturação.

**Contratos Enterprise**: Com um enterprise agreement, consolide a utilizar um determinado número de serviços no Azure durante o próximo ano e paga essa quantidade antes do tempo. O compromisso que fizer é consumido ao longo do ano. Se ultrapassar o montante de alocação, pode pagar o excedente após o consumo Efetivo. Dependendo da quantidade do compromisso, receberá um desconto sobre os serviços no Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Conceder acesso administrativo para uma subscrição do Azure

RBAC tem várias funções incorporadas que pode utilizar para atribuir permissões. Para tornar um utilizador administrador de uma subscrição do Azure, atribuí-los a [proprietário](../../role-based-access-control/built-in-roles.md#owner) função no âmbito da subscrição. A função de proprietário dá ao usuário acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas.

Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Ver informações de faturação no portal do Azure

Um componente importante da utilização do Azure é a capacidade de ver informações de faturação. O portal do Azure fornece informações detalhadas sobre as informações de faturação do Azure.

Para obter mais informações, consulte [como transferir a nota fiscal e diário de dados de utilização de faturação do Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Obter informações de faturação de APIs de faturação

Além de visualizar a faturação no portal, pode aceder as informações de faturação, utilizando um script ou programa através das APIs de REST de faturação do Azure:

- Pode utilizar a API de utilização do Azure para obter os dados de utilização. Pode ajustar as informações de utilização de faturação por identificação de recursos relacionados do Azure. Por exemplo, pode etiquetar cada um dos recursos num grupo de recursos com um nome de departamento ou o nome do projeto e, em seguida, controlar os custos especificamente para esse uma marca.

- Pode utilizar a API de cartão de taxa do Azure para listar todos os recursos disponíveis, juntamente com os metadados e informações sobre cada um desses recursos de preços.

Para obter mais informações, consulte [obter informações sobre o consumo de recursos do Microsoft Azure](../../billing/billing-usage-rate-card-overview.md).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Montante dos custos com a Calculadora de preços

Os preços para cada serviço no Azure são diferente. Muitos serviços do Azure fornecem escalões Basic, Standard e Premium. Normalmente, cada escalão tem vários níveis de preço e desempenho. Ao utilizar o [Calculadora de preços online](https://azure.microsoft.com/pricing/calculator), pode criar as estimativas de preços. A Calculadora inclui flexibilidade para estimar o custo num único recurso ou um grupo de recursos.

## <a name="azure-resource-manager"></a>Azure Resource Manager

O Azure Resource Manager é um mecanismo de implantação, gerenciamento e organização para recursos do Azure. Com o Resource Manager, pode fazer muitos recursos individuais num grupo de recursos.

Gestor de recursos também inclui capacidades de implementação que permitem a implementação personalizável e a configuração de recursos relacionados. Por exemplo, utilizando o Gestor de recursos, podem implementar uma aplicação que consiste em várias máquinas virtuais, um balanceador de carga e uma base de dados SQL como uma única unidade. Desenvolva estas implementações utilizando um modelo do Resource Manager.

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Pode implementar a solução durante todo o ciclo de vida de desenvolvimento e ter a confiança de que os recursos são implementados num estado consistente repetidamente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.

- Pode aplicar o controlo de acesso a todos os serviços no seu grupo de recursos porque o RBAC é integrado de forma nativa na plataforma de gestão.

- Pode aplicar marcas em recursos para organizar logicamente todos os recursos na sua subscrição.

- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos que partilham a mesma etiqueta.

### <a name="tips-for-creating-resource-groups"></a>Sugestões para a criação de grupos de recursos

Quando estiver a tomar decisões sobre seus grupos de recursos, considere estas dicas:

- Todos os recursos num grupo de recursos devem ter o mesmo ciclo de vida.

- Pode atribuir um recurso para apenas um grupo por vez.

- Pode adicionar ou remover um recurso de um grupo de recursos em qualquer altura. Cada recurso tem de pertencer a um grupo de recursos. Portanto, se remover um recurso de um grupo, deve adicioná-lo para outro.

- Pode mover a maioria dos tipos de recursos para um grupo de recursos diferente em qualquer altura.

- Os recursos num grupo de recursos podem ser em regiões diferentes.

- Pode utilizar um grupo de recursos para controlar o acesso para os recursos nele.

### <a name="building-resource-manager-templates"></a>Modelos do Resource Manager de criação

Modelos do Resource Manager declarativamente definem os recursos e configurações de recursos que serão implementadas num grupo de recursos. Pode utilizar modelos do Resource Manager para organizar implementações complexas sem a necessidade de configuração manual ou de script em excesso. Depois de desenvolver um modelo, pode implementá-la várias vezes, cada vez com um resultado idêntico.

Um modelo do Resource Manager consiste em quatro seções:

- **Parâmetros**: Essas são entradas para a implementação. Valores de parâmetros podem ser fornecidos por uma pessoa ou um processo automatizado. Um parâmetro de exemplo pode ser um nome de utilizador administrador e a palavra-passe para uma VM do Windows. Os valores de parâmetro são usados na implantação quando eles estão especificados.

- **Variáveis**: Estes são utilizados para armazenar os valores que são utilizados em toda a implementação. Ao contrário de parâmetros, um valor da variável não é fornecido no momento da implementação. Em vez disso, é difícil codificado ou gerado dinamicamente.

- **recursos**: Esta secção do modelo define os recursos a serem implantados, como máquinas virtuais, contas de armazenamento e redes virtuais.

- **Saída**: Depois de concluir uma implementação, o Gestor de recursos pode retornar dados como cadeias de ligação geradas dinamicamente.

Os mecanismos seguintes estão disponíveis para a automatização de implementação:

- **Funções**: Pode usar várias funções em modelos do Resource Manager. Estes incluem operações como converter uma cadeia em minúsculas, implementação de várias instâncias de um recurso definido e retornando dinamicamente o grupo de recursos de destino. As funções do Gestor de recursos ajudam a criar implementações dinâmicas.

- **Dependências de recursos**: Quando estiver a implementar vários recursos, alguns recursos terão uma dependência em outras pessoas. Para facilitar a implementação, pode utilizar uma declaração de dependência para que os recursos dependentes são implementados antes de outros.

- **Modelo de ligação**: De dentro de um modelo do Resource Manager, pode ligar a outro modelo. Isso permite que a decomposição de implantação num conjunto de modelos direcionados e com uma finalidade específica.

Pode criar modelos do Resource Manager em qualquer editor de texto. No entanto, o Azure SDK para Visual Studio inclui ferramentas para ajudá-lo. Usando o Visual Studio, pode adicionar recursos para o modelo num assistente, em seguida, implementar e depurar o modelo diretamente a partir do Visual Studio. Para obter mais informações, consulte [modelos Authoring Azure Resource Manager](../../resource-group-authoring-templates.md).

Por fim, pode converter os grupos de recursos existente num modelo reutilizável no portal do Azure. Isto pode ser útil se pretender criar um modelo implementável do grupo de recursos existente ou apenas pretender examinar o JSON subjacente. Para exportar um grupo de recursos, selecione o **Script de automação** botão das definições do grupo de recursos.

## <a name="security-of-azure-resources-rbac"></a>Segurança de recursos do Azure (RBAC)

Pode conceder acesso operacional para contas de utilizador num âmbito especificado: subscrição, grupo de recursos ou recurso individual. Isso significa que pode implementar um conjunto de recursos num grupo de recursos, como uma máquina virtual e todos os recursos relacionados e conceder permissões a um grupo ou utilizador específico. Esta abordagem limita o acesso apenas aos recursos que pertencem ao grupo de recursos de destino. Também pode conceder acesso a um recurso único, como uma máquina virtual ou uma rede virtual.

Para conceder acesso, atribuir uma função ao utilizador ou grupo de utilizadores. Existem muitas funções predefinidas. Também pode definir suas próprias funções personalizadas.

Aqui estão alguns de exemplo [funções incorporadas no Azure](../../role-based-access-control/built-in-roles.md):

- **Proprietário**: Um utilizador com esta função pode gerir tudo, incluindo o acesso.

- **Leitor**: Um utilizador com esta função pode ler os recursos de todos os tipos (exceto segredos), mas não pode fazer alterações.

- **Contribuinte de máquina virtual**: Um utilizador com esta função podem gerir máquinas virtuais, mas não é possível gerir a rede virtual a que estão ligados ou a conta de armazenamento onde reside o ficheiro VHD.

- **Contribuinte da BD SQL**: Um utilizador com esta função pode gerir bases de dados SQL, mas não as políticas relacionadas com segurança.

- **Gestor de segurança SQL**: Um utilizador com esta função pode gerir as políticas relacionadas com a segurança dos servidores SQL e bancos de dados.

- **Contribuinte de conta de armazenamento**: Um utilizador com esta função pode gerir contas de armazenamento, mas não é possível gerir o acesso às contas de armazenamento.

Para obter mais informações, consulte [gerir o acesso com RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

Máquinas virtuais do Azure é um dos serviços de IaaS centrais no Azure. Máquinas virtuais do Azure suporta a implementação de máquinas virtuais Windows ou Linux num datacenter do Microsoft Azure. Com máquinas de virtuais do Azure, tem controle total sobre a configuração de VM e é responsáveis por todos os instalação de software, configuração e manutenção.

Quando estiver a implementar uma VM do Azure, pode selecionar uma imagem do Azure Marketplace ou pode fornecer-lhe própria imagem generalizada. Esta imagem é utilizada para aplicar o sistema operativo e a configuração inicial. Durante a implementação do Resource Manager irá processar algumas definições de configuração, tais como atribuir o nome do computador, as credenciais administrativas e configuração de rede. Pode utilizar extensões de máquina virtual do Azure para automatizar ainda mais configurações, como a instalação de software, configuração antivírus e soluções de monitorização.

Pode criar máquinas virtuais em muitos tamanhos diferentes. O tamanho da máquina virtual determina a alocação de recursos, tais como a capacidade de processamento, memória e armazenamento. Em alguns casos, os recursos específicos, tais como placas de rede com capacidade RDMA e discos SSD estão disponíveis apenas com determinados tamanhos de VM. Para obter uma lista completa de capacidades e tamanhos de VM, consulte "Tamanhos das máquinas virtuais no Azure" para [Windows](../../virtual-machines/windows/sizes.md) e [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Casos de utilização

Uma vez que máquinas virtuais do Azure oferecem controle completo sobre a configuração, eles são ideais para uma vasta gama de cargas de trabalho do servidor que não se encaixam num modelo de PaaS. Cargas de trabalho do servidor, como servidores de base de dados (SQL Server, Oracle ou MongoDB), Windows Server Active Directory, Microsoft SharePoint e muitos mais se tornam possíveis executar na plataforma Microsoft Azure. Se assim o desejar, pode mover destas cargas de trabalho de um datacenter no local para um ou mais regiões do Azure, sem uma grande quantidade de reconfiguração.

### <a name="deployment-of-virtual-machines"></a>Implementação de máquinas virtuais

Pode implementar máquinas virtuais do Azure com o portal do Azure, ao utilizar a automatização com o módulo Azure PowerShell ou usar a automação com a CLI para várias plataformas.

#### <a name="portal"></a>Portal

Implementar uma máquina virtual com o portal do Azure requer apenas uma subscrição do Azure Active Directory e o acesso a um navegador da web. Pode selecionar várias imagens de sistema operacional diferente com configurações diferentes. Todos os requisitos de sistema de rede e armazenamento são configurados durante a implantação. Para obter mais informações, consulte "Criar uma máquina virtual no portal do Azure" para [Windows](../../virtual-machines/windows/quick-create-portal.md) e [Linux](../../virtual-machines/linux/quick-create-portal.md).

Para além de implementar uma máquina virtual a partir do portal do Azure, pode implementar um modelo Azure Resource Manager do portal. Isso implementará e configurará todos os recursos, conforme definido no modelo. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Implementar uma máquina virtual do Azure com o PowerShell permite a automação de implantação completa de todos os recursos de máquina de virtual relacionados, incluindo o armazenamento e rede. Para obter mais informações, consulte [criar uma VM do Windows com o Resource Manager e PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Para além de implementar recursos de computação do Azure individualmente, pode utilizar o módulo Azure PowerShell para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Interface de linha de comandos (CLI)

Tal como acontece com o módulo do PowerShell, a interface de linha de comandos do Azure fornece automação de implantação e pode ser utilizada em sistemas Windows, OS X ou Linux. Quando estiver a utilizar a CLI do Azure **criação rápida de vm** de comando, todos os recursos de máquina virtual (incluindo o armazenamento e rede) relacionados e a própria máquina virtual são implementadas. Para obter mais informações, consulte [criar uma VM do Linux no Azure com a CLI](../../virtual-machines/linux/quick-create-cli.md).

Da mesma forma, pode utilizar a CLI do Azure para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Acesso e segurança para máquinas virtuais

Aceder a uma máquina virtual da Internet requer que a rede associada de interface ou, se aplicável, ser configurado com um endereço IP público de Balanceador de carga. O endereço IP público inclui um nome DNS que irá resolver para a máquina virtual ou Balanceador de carga. Para obter mais informações, consulte [endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Gerir o acesso à máquina virtual através do endereço IP público através de um recurso de grupo (NSG) de segurança de rede. Um NSG funciona como uma firewall e permite ou nega o tráfego através da interface de rede ou de sub-rede num conjunto das portas definidas. Por exemplo, para criar uma sessão de ambiente de trabalho remoto com uma VM do Azure, terá de configurar o NSG para permitir tráfego de entrada na porta 3389. Para obter mais informações, consulte [abrir portas para uma VM no Azure através do portal do Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Por fim, assim como acontece com a gestão de qualquer sistema de computador, deve fornecer segurança para uma máquina virtual do Azure no sistema operacional usando as credenciais de segurança e firewalls de software.

## <a name="azure-storage"></a>Storage do Azure

Armazenamento do Azure é um serviço gerido pela Microsoft que oferece armazenamento durável, dimensionável e redundante. Pode adicionar uma conta de armazenamento do Azure como um recurso para qualquer grupo de recursos utilizando um método de implementação de recursos. Azure inclui quatro tipos de armazenamento: Armazenamento de BLOBs, armazenamento de ficheiros, armazenamento de tabelas e armazenamento de filas. Ao implementar uma conta de armazenamento, dois tipos de conta são disponível, para fins gerais e armazenamento de Blobs. Uma conta de armazenamento para fins gerais dá-lhe acesso a todos os quatro tipos de armazenamento. Contas de armazenamento de BLOBs são semelhantes às contas para fins gerais, mas contém blobs especializadas que incluem escalões de acesso frequente e esporádica. Para obter mais informações sobre o armazenamento de BLOBs, veja [armazenamento de Blobs do Azure](../../storage/blobs/storage-blob-storage-tiers.md).

Contas de armazenamento do Azure podem ser configuradas com diferentes níveis de redundância:

- **Armazenamento localmente redundante** fornece elevada disponibilidade, garantindo que três cópias de todos os dados são efetuadas de forma síncrona antes de uma gravação será considerada concluída com êxito. Estas cópias são armazenadas num único local e numa única região. As réplicas residem em domínios de falha e domínios de atualização. Isso significa que os dados estão disponíveis mesmo que um nó de armazenamento que está mantendo a falha de dados ou é colocado offline para ser atualizado.

- **Armazenamento georredundante** faz três cópias síncronas dos dados na região primária para elevada disponibilidade e, em seguida, faz três réplicas de forma assíncrona numa região emparelhada para recuperação após desastre.

- **Armazenamento georredundante com acesso de leitura** é armazenamento georredundante e a capacidade de ler os dados na região secundária. Esta capacidade torna adequada para recuperação após desastre parcial. Se houver um problema com a região primária, é possível alterar a sua aplicação para ter acesso só de leitura para a região emparelhada.

### <a name="use-cases"></a>Casos de utilização

Cada tipo de armazenamento tem um caso de utilização diferentes.

#### <a name="blob-storage"></a>Armazenamento de blobs

A palavra *blob* é um acrônimo dos *objeto binário grande*. Os BLOBs são arquivos de dados não estruturados, como aquelas que armazena no seu computador. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos. Armazenamento de Blobs do Azure também contém discos de dados de máquinas virtuais do Azure.

O armazenamento do Azure suporta três tipos de blobs:

- **Blobs de blocos** são utilizados para armazenar comum ficheiros 195 GB de tamanho (blocos de 4 MB x 50.000). O principal motivo para blobs de blocos é o armazenamento de ficheiros que são lidos a partir do início ao fim, como ficheiros de multimédia ou ficheiros de imagem de Web sites. Eles são chamados blobs de blocos, porque os ficheiros maiores do que 64 MB tem de ser carregados como blocos de pequenas. Esses blocos são, em seguida, consolidados (ou consolidados) para o blob final.

- **Blobs de páginas** são utilizados para armazenar acesso aleatório ficheiros até 1 TB de tamanho. Blobs de páginas são utilizados principalmente como o armazenamento de backup para os VHDs que fornecem duráveis discos para máquinas de virtuais do Azure, as IaaS computação serviço no Azure. Eles são nomeados blobs de páginas porque fornecem acesso de leitura/escrita aleatórias para páginas de 512 bytes.

- **Blobs de acréscimo** consistem em blocos, como blobs de blocos, mas estão otimizados para operações de acréscimo. Estes são frequentemente utilizados para informações de registo de uma ou mais origens para o mesmo blob. Por exemplo, pode escrever todo o registo de rastreio para o mesmo blob de acréscimo para uma aplicação que está a executar em várias VMs. Um único blob de acréscimo pode ter até 195 GB.

Para obter mais informações, consulte [introdução ao armazenamento de Blobs do Azure com o .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>Armazenamento de ficheiros

Armazenamento de ficheiros do Azure é um serviço que oferece partilhas de ficheiros na cloud utilizando o protocolo de bloco de mensagem de servidor (SMB) padrão. O serviço suporta tanto o SMB 2.1 e o SMB 3.0. Com o armazenamento de ficheiros do Azure, pode migrar aplicativos que se baseiam em partilhas de ficheiros para o Azure rapidamente e sem reescritas dispendiosas. Aplicativos executados em máquinas virtuais do Azure, nos serviços cloud ou no local os clientes podem montar uma partilha de ficheiros na cloud. Isso é semelhante a como uma aplicação de ambiente de trabalho monta uma partilha SMB típica. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do File Storage em simultâneo.

Como uma partilha de armazenamento de ficheiros é uma partilha de ficheiros SMB padrão, as aplicações em execução no Azure podem aceder a dados na partilha através de APIs de e/s de sistema de ficheiros. Os desenvolvedores podem usar, portanto, seu código existente e competências para migrar aplicativos já existentes. Os profissionais de TI podem utilizar cmdlets do PowerShell para criar, montar e gerir partilhas de armazenamento de ficheiros como parte da administração de aplicações do Azure.

Para obter mais informações, consulte [introdução ao armazenamento de ficheiros do Azure no Windows](../../storage/files/storage-how-to-use-files-windows.md) ou [como utilizar o armazenamento de ficheiros do Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Table Storage

O Table Storage do Azure é um serviço que armazena dados NoSQL estruturados na nuvem. Armazenamento de tabelas é um arquivo de chaves/atributos com um design sem esquemas. Como o armazenamento de tabelas é sem esquemas, é fácil adaptar os seus dados conforme as necessidades da sua aplicação evoluem. O acesso aos dados é rápido e rentável para todos os tipos de aplicações. Geralmente, o custo do Table Storage é significativamente inferior ao SQL tradicional para volumes de dados semelhantes.

Pode utilizar o Table Storage para armazenar conjuntos de dados flexíveis, tais como os dados do utilizador para Web Apps, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados necessários para o seu serviço. Pode armazenar quantas entidades pretender numa tabela. Uma conta de armazenamento pode conter qualquer número de tabelas, até ao limite de capacidade da conta de armazenamento.

Para obter mais informações, consulte [introdução ao armazenamento de tabelas do Azure](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Armazenamento de filas

O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Conceber aplicações para dimensionamento, os componentes da aplicação são frequentemente desacoplados, para que possam dimensionar de forma independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Para obter mais informações, consulte [introdução ao armazenamento de filas do Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Implementar uma conta de armazenamento

Existem várias opções para implementar uma conta de armazenamento.

#### <a name="portal"></a>Portal

A implementação de uma conta de armazenamento com o portal do Azure requer apenas uma subscrição do Azure Active Directory e o acesso a um navegador da web. Pode implementar uma nova conta de armazenamento para um grupo de recursos novo ou existente. Depois de criar a conta de armazenamento, pode criar uma partilha de ficheiro ou de contentor de Blobs com o portal. Pode criar a tabela e fila entidades de armazenamento por meio de programação. Para obter mais informações, veja [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md).

Para além de implementar uma conta de armazenamento no portal do Azure, pode implementar um modelo Azure Resource Manager do portal. Isto irá implementar e configurar todos os recursos, conforme definido no modelo, incluindo as contas de armazenamento. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Implementar uma conta de armazenamento do Azure com o PowerShell permite a automação de implantação completa da conta de armazenamento. Para obter mais informações, consulte [utilizar o Azure PowerShell com armazenamento do Azure](../../storage/common/storage-powershell-guide-full.md).

Para além de implementar recursos do Azure individualmente, pode utilizar o módulo Azure PowerShell para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>Interface de linha de comandos (CLI)

Tal como acontece com o módulo do PowerShell, a Interface de linha de comandos do Azure fornece automação de implantação e pode ser utilizado em sistemas Windows, OS X ou Linux. Pode utilizar a CLI do Azure **criar conta de armazenamento** comando para criar uma conta de armazenamento. Para obter mais informações, consulte [utilizar a CLI do Azure com o armazenamento do Azure.](../../storage/common/storage-azure-cli.md)

Da mesma forma, pode utilizar a CLI do Azure para implementar um modelo Azure Resource Manager. Para obter mais informações, consulte [implementar recursos com modelos do Resource Manager e a CLI do Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Acesso e segurança do armazenamento do Azure

O armazenamento do Azure é acessado de várias formas, porém incluindo o portal do Azure, durante a criação da VM e o funcionamento e de bibliotecas de cliente de armazenamento.

#### <a name="virtual-machine-disks"></a>Discos da máquina virtual

Quando estiver a implementar uma máquina virtual, terá também de criar uma conta de armazenamento para armazenar o disco de sistema operativo da máquina virtual e quaisquer discos de dados adicionais. Pode selecionar uma conta de armazenamento existente ou crie um novo. Uma vez que o tamanho máximo de um blob é 1024 GB, um único disco VM tem um tamanho máximo de 1,023 GB. Para configurar um disco de dados maior, pode apresentar vários discos de dados para a máquina virtual e pool com eles em conjunto como um único disco lógico. Para obter mais informações, consulte "Gerir discos do Azure" para [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) e [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Ferramentas de armazenamento

Contas de armazenamento do Azure podem ser acedidas através de muitos exploradores de armazenamento diferentes como, por exemplo, o Gerenciador de nuvem do Visual Studio. Essas ferramentas permitem-lhe navegar pelos dados e contas de armazenamento. Para obter mais informações e uma lista de exploradores de armazenamento disponível, consulte [ferramentas de cliente de armazenamento do Azure](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>API de armazenamento

Recursos de armazenamento podem ser acedidos por qualquer linguagem que possa fazer pedidos HTTP/HTTPS. Além disso, o Storage do Azure oferece bibliotecas de programação para vários idiomas populares. Estas bibliotecas simplificam o trabalho com armazenamento do Azure ao processar detalhes como a invocação síncrona e assíncrona, a criação de batches de operações, gestão de exceções e as tentativas automáticas. Para obter mais informações, consulte [referência de REST API do serviço de armazenamento do Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Chaves de acesso de armazenamento

Cada conta de armazenamento tem duas chaves de autenticação, um site primário e um secundário. Qualquer um pode ser utilizado para operações de acesso de armazenamento. Essas chaves de armazenamento são utilizados para ajudar a proteger uma conta de armazenamento e são necessárias para acessar programaticamente os dados. Existem duas chaves para permitir ocasional rollover das chaves para melhorar a segurança. É fundamental para proteger as chaves porque sua posse, juntamente com o nome da conta, permite acesso ilimitado a todos os dados na conta de armazenamento.

#### <a name="shared-access-signatures"></a>Assinaturas de acesso partilhado

Se precisar de permitir que os usuários tiver de ser controlado acesso aos seus recursos de armazenamento, pode criar uma assinatura de acesso partilhado. Uma assinatura de acesso partilhado é um token que pode ser anexado a uma URL que ativa o acesso delegado a um recurso de armazenamento. Qualquer pessoa que tiver o token pode aceder ao recurso que aponta para com as permissões que especificar, para o período de tempo que a TI válido. Para obter mais informações, consulte [assinaturas de acesso partilhado do Using](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Rede Virtual do Azure

Redes virtuais são necessárias para suportar comunicações entre máquinas virtuais. Pode definir sub-redes, endereço IP personalizado, as definições de DNS, a filtragem de segurança e o balanceamento de carga. O Azure suporta diferentes casos de uso: redes apenas na cloud ou redes virtuais híbridas.

### <a name="cloud-only-virtual-networks"></a>Redes virtuais apenas na cloud

Uma rede virtual do Azure, por predefinição, é acessível apenas a recursos armazenados no Azure. Recursos ligados à mesma rede virtual podem comunicar entre si. Pode associar a interfaces de rede de máquinas virtuais e Balanceadores com um endereço IP público para tornar a máquina virtual acessível através da Internet de carga. Pode ajudar a proteger o acesso aos recursos expostos publicamente através da utilização de um grupo de segurança de rede.

![Rede Virtual do Azure para uma aplicação Web de 2 camadas](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Redes virtuais híbridas

Pode ligar uma rede no local a uma rede virtual do Azure com o ExpressRoute ou uma ligação de VPN de site a site. Nesta configuração, a rede virtual do Azure é, essencialmente, um com base na cloud extensão da sua rede no local.
![Rede Virtual híbrida através de VPN](https://docs.microsoft.com/azure/architecture/reference-architectures/_images/blueprints/hybrid-network-vpn.png)

Uma vez que a rede virtual do Azure está ligada à sua rede no local, em vários locais redes virtuais têm de utilizar uma parte exclusiva do espaço de endereços que a organização utiliza. Da mesma forma que diferentes localizações da empresa são atribuídas uma sub-rede IP específica, Azure torna-se outro local à medida que expande a sua rede.
Existem várias opções para implementar uma rede virtual.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Interface de linha de comandos (CLI)](../../virtual-network/quick-create-cli.md)

- Modelos Azure Resource Manager

>**Quando utilizar**: Sempre que estiver a trabalhar com VMs no Azure, irá trabalhar com redes virtuais. Assim, para segmentar as suas VMs em datacenters no local semelhante do sub-redes públicas e privadas.

>**Introdução ao**: A implementação de uma rede virtual do Azure com o portal do Azure requer apenas uma subscrição do Azure Active Directory e o acesso a um navegador da web. Pode implementar uma nova rede virtual para um grupo de recursos novo ou existente. Quando estiver a criar uma nova máquina virtual do portal, pode selecionar uma rede virtual existente ou crie um novo. Começar a utilizar e [criar uma rede virtual com o portal do Azure](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Acesso e segurança para redes virtuais

Pode ajudar a proteger redes virtuais do Azure através de um grupo de segurança de rede. Os NSGs contêm uma lista de regras de (ACL lista) de controle de acesso que permitem ou negam o tráfego de rede para as instâncias VM numa rede virtual. Pode associar NSGs a sub-redes ou instâncias de VM individuais dentro dessa sub-rede. Quando associa um NSG uma sub-rede, as regras ACL são aplicadas a todas as instâncias VM nessa sub-rede. Além disso, pode restringir mais o tráfego para uma VM individual ao associar um NSG diretamente com essa VM. Para obter mais informações, consulte [filtrar o tráfego de rede com grupos de segurança de rede](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Passos Seguintes

- [Criar um VM do Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Criar uma VM de Linux](../../virtual-machines/linux/quick-create-portal.md)
