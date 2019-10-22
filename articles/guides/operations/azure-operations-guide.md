---
title: Guia de introdução para operadores de ti do Azure | Microsoft Docs
description: Guia de introdução para operadores de ti do Azure
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 9b23e3b69b4970e765ea1cd5af690cf074bad774
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693456"
---
# <a name="get-started-for-azure-it-operators"></a>Introdução aos operadores de ti do Azure

Este guia apresenta os principais conceitos relacionados à implantação e ao gerenciamento de uma infraestrutura de Microsoft Azure. Se você for novo na computação em nuvem ou no próprio Azure, este guia ajudará você a começar rapidamente com os conceitos, a implantação e os detalhes de gerenciamento. Muitas seções deste guia discutem uma operação como implantar uma máquina virtual e, em seguida, fornecer um link para detalhes técnicos detalhados.

## <a name="cloud-computing-overview"></a>Visão geral da computação em nuvem

A computação em nuvem fornece uma alternativa moderna para o datacenter local tradicional. Os fornecedores de nuvem pública fornecem e gerenciam toda a infraestrutura de computação e o software de gerenciamento subjacente. Esses fornecedores fornecem uma ampla variedade de serviços de nuvem. Nesse caso, um serviço de nuvem pode ser uma máquina virtual, um servidor Web ou um mecanismo de banco de dados hospedado na nuvem. Como cliente do provedor de nuvem, você concede esses serviços de nuvem de acordo com a necessidade. Ao fazer isso, você converte as despesas de capital de manutenção de hardware em uma despesa operacional. Um serviço de nuvem também fornece estes benefícios:

- Implantação rápida de grandes ambientes de computação

- Desalocação rápida de sistemas que não são mais necessários

- Implantação fácil de sistemas tradicionalmente complexos, como balanceadores de carga

- Capacidade de fornecer capacidade de computação flexível ou escala quando necessário

- Ambientes de computação mais econômicos

- Acesso de qualquer lugar com um portal baseado na Web ou automação programática

- Serviços baseados em nuvem para atender à maioria das necessidades de computação e aplicativo

Com a infraestrutura local, você tem controle total sobre o hardware e o software implantado. Historicamente, isso levou a decisões de aquisição de hardware que se concentram no dimensionamento. Um exemplo é a compra de um servidor com mais núcleos para atender às necessidades de desempenho de pico. Infelizmente, essa infraestrutura pode ser subutilizada fora de uma janela de demanda. Com o Azure, você pode implantar apenas a infraestrutura de que precisa e ajustar isso para cima ou para baixo a qualquer momento. Isso leva a um foco na expansão por meio da implantação de nós de computação adicionais para atender a uma necessidade de desempenho. A expansão dos serviços de nuvem é mais econômica do que a expansão por meio de um hardware caro.

A Microsoft implantou muitos data centers do Azure em todo o mundo, com mais planos. Além disso, a Microsoft está aumentando nuvens soberanass em regiões como a China e a Alemanha. Somente as maiores empresas globais podem implantar data centers dessa maneira, por isso, usar o Azure torna mais fácil para empresas de qualquer tamanho implantar seus serviços próximos aos seus clientes.

Para pequenas empresas, o Azure permite um ponto de entrada de baixo custo, com a capacidade de dimensionar rapidamente conforme a demanda dos aumentos de computação. Isso evita um grande investimento de capital inicial na infraestrutura e fornece a flexibilidade para arquitetar e rearquitetar sistemas, conforme necessário. O uso da computação em nuvem se ajusta bem ao modelo de expansão rápida e com falha rápida do crescimento da inicialização.

Para obter mais informações sobre as regiões do Azure disponíveis, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Modelo de computação em nuvem

O Azure usa um modelo de computação em nuvem baseado em categorias de serviço fornecidas aos clientes. As três categorias de serviço incluem IaaS (infraestrutura como serviço), PaaS (plataforma como serviço) e SaaS (software como serviço). Os fornecedores compartilham algumas ou todas as responsabilidades dos componentes na pilha de computação em cada uma dessas categorias. Vamos dar uma olhada em cada uma das categorias para computação em nuvem.
![Cloud de comparação de pilha de computação ](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infraestrutura como serviço

Um fornecedor de nuvem IaaS executa e gerencia todos os recursos de computação física e o software necessário para habilitar a virtualização de computadores. Um cliente desse serviço implanta máquinas virtuais nesses data centers hospedados. Embora as máquinas virtuais estejam localizadas em um datacenter externo, o consumidor IaaS tem controle sobre a configuração e o gerenciamento do sistema operacional, deixando a infraestrutura subjacente para o fornecedor de nuvem.

O Azure inclui várias soluções de IaaS, incluindo máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e a infraestrutura de rede relacionada. As máquinas virtuais são uma opção popular para a migração inicial dos serviços para o Azure, pois ele habilita um modelo de migração de "deslocamento e mudança". Você pode configurar uma VM como a infraestrutura que atualmente executa seus serviços em seu datacenter e, em seguida, migrar o software para a nova VM. Talvez seja necessário fazer atualizações de configuração, como URLs para outros serviços ou armazenamento, mas você pode migrar muitos aplicativos dessa maneira.

Os conjuntos de dimensionamento de máquinas virtuais são criados sobre as máquinas virtuais do Azure e fornecem uma maneira fácil de implantar clusters de VMs idênticas. Os conjuntos de dimensionamento de máquinas virtuais também dão suporte ao dimensionamento automático para que novas VMs possam ser implantadas automaticamente quando necessário. Isso faz com que o dimensionamento de máquinas virtuais defina uma plataforma ideal para hospedar clusters de computação de microserviço de nível mais alto, como o Azure Service Fabric e o serviço de contêiner do Azure.

#### <a name="paas-platform-as-a-service"></a>PaaS: plataforma como serviço

Com o PaaS, você implanta seu aplicativo em um ambiente que o fornecedor do serviço de nuvem fornece. O fornecedor faz todo o gerenciamento de infraestrutura para que você possa se concentrar no desenvolvimento de aplicativos e no gerenciamento de dados.

O Azure fornece várias ofertas de computação PaaS, incluindo o recurso de aplicativos Web do serviço Azure App e serviços de nuvem do Azure (funções Web e de trabalho). Em ambos os casos, os desenvolvedores têm várias maneiras de implantar seu aplicativo sem saber nada sobre os detalhes e os parafusos que dão suporte a ele. Os desenvolvedores não precisam criar máquinas virtuais (VMs), usar protocolo RDP (RDP) para entrar em cada uma delas ou instalar o aplicativo. Eles simplesmente visitam um botão (ou perto dele), e as ferramentas fornecidas pela Microsoft provisionam as VMs e, em seguida, implantam e instalam o aplicativo nelas.

#### <a name="saas-software-as-a-service"></a>SaaS: software como serviço

O SaaS é um software que é hospedado e gerenciado centralmente. Geralmente, ele é baseado em uma arquitetura multilocatário — uma única versão do aplicativo é usada para todos os clientes. Ele pode ser escalado horizontalmente para várias instâncias para garantir o melhor desempenho em todos os locais. O software SaaS normalmente é licenciado por meio de uma assinatura mensal ou anual. Os fornecedores de software SaaS são responsáveis por todos os componentes da pilha de software, de modo que tudo o que você gerencia seja os serviços fornecidos.

Microsoft Office 365 é um bom exemplo de uma oferta de SaaS. Os Assinantes pagam uma taxa de assinatura mensal ou anual e obtêm o Microsoft Exchange, o Microsoft OneDrive e o restante do conjunto de Microsoft Office como um serviço. Os assinantes sempre obtêm a versão mais recente e o Exchange Server é gerenciado para você. Em comparação com a instalação e atualização do Office todos os anos, isso é mais barato e requer menos esforço.

## <a name="azure-services"></a>Serviços do Azure

O Azure oferece muitos serviços em sua plataforma de computação em nuvem. Esses serviços incluem o seguinte.

### <a name="compute-services"></a>Serviços de computação

Serviços para hospedagem e execução da carga de trabalho do aplicativo:

- Máquinas virtuais do Azure — Linux e Windows

- Serviços de aplicativos (aplicativos Web, aplicativos móveis, aplicativos lógicos, aplicativos de API e aplicativos de funções)

- Lote do Azure (para trabalhos de computação em lote e paralelos em grande escala)

- Azure Service Fabric

- Serviço de Contentor do Azure

### <a name="data-services"></a>Serviços de dados

Serviços para armazenar e gerenciar dados:

- Armazenamento do Azure (inclui os serviços BLOB, fila, tabela e arquivo do Azure)

- Base de Dados SQL do Azure

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Cache do Azure para Redis

### <a name="application-services"></a>Serviço de aplicações

Serviços para criação e operação de aplicativos:

- Azure Active Directory (Azure AD)

- Barramento de serviço do Azure para conectar sistemas distribuídos

- Azure HDInsight para processamento Big Data

- Agendador do Azure

- Serviços de Multimédia do Azure

### <a name="network-services"></a>Serviços de rede

Serviços para rede no Azure e entre datacenters locais e do Azure:

- Rede Virtual do Azure

- Azure ExpressRoute

- DNS fornecido pelo Azure

- Traffic Manager do Azure

- Rede de distribuição de conteúdo do Azure

Para obter a documentação detalhada sobre os serviços do Azure, consulte a [documentação do serviço do Azure](https://docs.microsoft.com/azure).

## <a name="azure-key-concepts"></a>Conceitos principais do Azure

### <a name="datacenters-and-regions"></a>Data centers e regiões

O Azure é uma plataforma de nuvem global que geralmente está disponível em muitas regiões em todo o mundo. Ao provisionar um serviço, aplicativo ou VM no Azure, você será solicitado a selecionar uma região. A região selecionada representa um datacenter específico em que seu aplicativo é executado. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

Um dos benefícios de usar o Azure é que você pode implantar seus aplicativos em vários data centers em todo o mundo. A região escolhida pode afetar o desempenho do seu aplicativo. É ideal escolher uma região que esteja mais próxima da maioria dos seus clientes, para reduzir a latência em solicitações de rede. Você também pode selecionar uma região para atender aos requisitos legais para distribuir seu aplicativo em determinados países/regiões.

### <a name="azure-portal"></a>Portal do Azure

O portal do Azure é um aplicativo baseado na Web que pode ser usado para criar, gerenciar e remover recursos e serviços do Azure. O portal do Azure está localizado em [Portal.Azure.com](https://portal.azure.com). Ele inclui um painel personalizável e ferramentas para gerenciar recursos do Azure. Ele também fornece informações de cobrança e assinatura. Para obter mais informações, consulte [portal do Microsoft Azure visão geral](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) e [gerenciar recursos do Azure por meio do portal](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Recursos

Os recursos do Azure são serviços individuais de computação, rede, dados ou Hospedagem de aplicativo que foram implantados em uma assinatura do Azure. Alguns recursos comuns são máquinas virtuais, contas de armazenamento ou bancos de dados SQL. Os serviços do Azure geralmente consistem em vários recursos relacionados do Azure. Por exemplo, uma máquina virtual do Azure pode incluir uma VM, uma conta de armazenamento, um adaptador de rede e um endereço IP público. Esses recursos podem ser criados, gerenciados e excluídos individualmente ou como um grupo. Os recursos do Azure são abordados em mais detalhes posteriormente neste guia.

### <a name="resource-groups"></a>Grupos de recursos

Um grupo de recursos do Azure é um contêiner que mantém recursos relacionados para uma solução do Azure. O grupo de recursos pode incluir todos os recursos da solução ou apenas os recursos que você deseja gerenciar como um grupo. Os grupos de recursos do Azure são abordados em mais detalhes posteriormente neste guia.

### <a name="resource-manager-templates"></a>Modelos do Gestor de Recursos

Um modelo de Azure Resource Manager é um arquivo de JavaScript Object Notation (JSON) que define um ou mais recursos a serem implantados em um grupo de recursos. Ele também define as dependências entre os recursos implantados. Os modelos do Resource Manager são abordados em mais detalhes posteriormente neste guia.

### <a name="automation"></a>Automatização

Além de criar, gerenciar e excluir recursos usando o portal do Azure, você pode automatizar essas atividades usando o PowerShell ou a CLI (interface de linha de comando) do Azure.

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell é um conjunto de módulos que fornece cmdlets para gerenciar o Azure. Você pode usar os cmdlets para criar, gerenciar e remover os serviços do Azure. Os cmdlets podem ajudá-lo a obter implantações consistentes, reproduzíveis e de mãos. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>Interface de linha de comandos do Azure

A interface de linha de comando do Azure é uma ferramenta que você pode usar para criar, gerenciar e remover recursos do Azure da linha de comando. O CLI do Azure está disponível para Linux, Mac OS X e Windows. Para obter mais informações e detalhes técnicos, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>APIs REST

O Azure é criado em um conjunto de APIs REST que dão suporte à interface do usuário do portal do Azure. A maioria dessas APIs REST também tem suporte para permitir que você provisione e gerencie programaticamente seus recursos e aplicativos do Azure de qualquer dispositivo habilitado para Internet. Para obter mais informações, consulte a [referência do SDK REST do Azure](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Os administradores podem acessar Azure PowerShell e CLI do Azure por meio de uma experiência acessível por navegador chamada Azure Cloud Shell. Essa interface interativa fornece uma ferramenta flexível para que os administradores do Linux e do Windows usem a interface de linha de comando escolhida, seja bash ou PowerShell. Azure Cloud Shell pode ser acessado por meio do portal, como uma interface da Web autônoma em [shell.Azure.com](https://shell.azure.com)ou de vários outros pontos de acesso. Para obter mais informações, consulte [visão geral do Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Subscrições do Azure

Uma assinatura é um agrupamento lógico de serviços do Azure que está vinculado a uma conta do Azure. Uma única conta do Azure pode conter várias assinaturas. A cobrança dos serviços do Azure é feita por assinatura. As assinaturas do Azure têm um administrador de conta, que tem controle total sobre a assinatura e um administrador de serviços, que tem controle sobre todos os serviços na assinatura. Para obter informações sobre administradores de assinatura clássicos, consulte [Adicionar ou alterar administradores de assinatura do Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Além dos administradores, contas individuais podem receber controle detalhado dos recursos do Azure usando o [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Selecionar e habilitar uma assinatura do Azure

Antes de poder trabalhar com os serviços do Azure, você precisa de uma assinatura. Vários tipos de assinatura estão disponíveis.

**Contas gratuitas**: o link para se inscrever em uma conta gratuita está no [site do Azure](https://azure.microsoft.com/). Isso lhe dá um crédito ao longo de 30 dias para experimentar qualquer combinação de recursos no Azure. Se você exceder seu valor de crédito, sua conta será suspensa. No final da avaliação, seus serviços serão descomissionados e não funcionarão mais. Você pode atualizar para uma assinatura paga conforme o uso a qualquer momento.

**Assinaturas do MSDN**: se você tiver uma assinatura do MSDN, obterá um valor específico no crédito do Azure a cada mês. Por exemplo, se você tiver um Microsoft Visual Studio Enterprise com assinatura do MSDN, você obterá \$150 por mês no crédito do Azure.

Se você exceder o valor de crédito, seu serviço será desabilitado até que o próximo mês seja iniciado. Você pode desativar o limite de gastos e adicionar um cartão de crédito a ser usado para os custos adicionais. Alguns desses custos são descontados para contas do MSDN. Por exemplo, você paga o preço do Linux para VMs que executam o Windows Server e não há nenhum custo adicional para servidores da Microsoft, como Microsoft SQL Server. Isso torna as contas do MSDN ideais para cenários de desenvolvimento e teste.

**Contas do BizSpark**: o programa Microsoft BizSpark fornece muitos benefícios para as inicializações. Um desses benefícios é o acesso a todos os softwares da Microsoft para ambientes de desenvolvimento e teste para até cinco contas do MSDN. Você obtém $150 no crédito do Azure para cada uma dessas cinco contas do MSDN e paga taxas reduzidas para vários dos serviços do Azure, como máquinas virtuais.

Pré- **pago**: com essa assinatura, você paga pelo que usa anexando um cartão de crédito ou débito à conta. Se você for uma organização, também poderá ser aprovado para faturamento.

**Enterprise Agreements**: com um Enterprise Agreement, você se compromete a usar um determinado número de serviços no Azure no próximo ano e paga esse valor antecipadamente. O compromisso que você faz é consumido ao longo do ano. Se você exceder o valor de compromisso, poderá pagar o excedente em pelos débitos anteriores. Dependendo do valor do compromisso, você receberá um desconto sobre os serviços no Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Conceder acesso administrativo a uma assinatura do Azure

O RBAC tem várias funções internas que você pode usar para atribuir permissões. Para tornar um usuário um administrador de uma assinatura do Azure, atribua a ele a função de [proprietário](../../role-based-access-control/built-in-roles.md#owner) no escopo da assinatura. A função Proprietário dá ao utilizador acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas.

Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Exibir informações de cobrança no portal do Azure

Um componente importante do uso do Azure é a capacidade de exibir informações de cobrança. O portal do Azure fornece uma visão detalhada das informações de cobrança do Azure.

Para obter mais informações, consulte [como baixar sua fatura de cobrança do Azure e dados de uso diário](../../billing/billing-download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Obter informações de cobrança de APIs de cobrança

Além de exibir a cobrança no portal, você pode acessar as informações de cobrança usando um script ou programa por meio das APIs REST de cobrança do Azure:

- Você pode usar a API de uso do Azure para recuperar os dados de uso. Você pode ajustar as informações de uso de cobrança marcando recursos do Azure relacionados. Por exemplo, você pode marcar cada um dos recursos em um grupo de recursos com um nome de departamento ou nome de projeto e, em seguida, acompanhar os custos especificamente para aquela marca.

- Você pode usar a API do cartão de taxas do Azure para listar todos os recursos disponíveis, juntamente com os metadados e as informações de preços sobre cada um desses recursos.

Para obter mais informações, veja [Gain insights into your Microsoft Azure resource consumption](../../billing/billing-usage-rate-card-overview.md) (Obter informações sobre o consumo de recursos do Microsoft Azure).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Custo de previsão com a calculadora de preços

O preço de cada serviço no Azure é diferente. Muitos serviços do Azure fornecem camadas básica, Standard e Premium. Normalmente, cada camada tem vários níveis de preço e desempenho. Usando a [calculadora de preços online](https://azure.microsoft.com/pricing/calculator), você pode criar estimativas de preços. A calculadora inclui flexibilidade para estimar o custo em um único recurso ou em um grupo de recursos.

## <a name="azure-resource-manager"></a>Gestor de Recursos do Azure

Azure Resource Manager é um mecanismo de implantação, gerenciamento e organização para recursos do Azure. Usando o Resource Manager, você pode colocar vários recursos individuais juntos em um grupo de recursos.

O Resource Manager também inclui recursos de implantação que permitem a implantação personalizável e a configuração de recursos relacionados. Por exemplo, usando o Resource Manager, você pode implantar um aplicativo que consiste em várias máquinas virtuais, um balanceador de carga e um banco de dados SQL como uma única unidade. Você desenvolve essas implantações usando um modelo do Resource Manager.

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Você pode implantar repetidamente sua solução em todo o ciclo de vida de desenvolvimento e ter confiança de que seus recursos são implantados em um estado consistente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.

- Você pode aplicar o controle de acesso a todos os serviços em seu grupo de recursos porque o RBAC é integrado nativamente à plataforma de gerenciamento.

- Você pode aplicar marcas em recursos para organizar logicamente todos os recursos em sua assinatura.

- Você pode esclarecer a cobrança da sua organização exibindo os custos de um grupo de recursos que compartilham a mesma marca.

### <a name="tips-for-creating-resource-groups"></a>Dicas para criar grupos de recursos

Quando estiver tomando decisões sobre seus grupos de recursos, considere estas dicas:

- Todos os recursos em um grupo de recursos devem ter o mesmo ciclo de vida.

- Você pode atribuir um recurso a apenas um grupo por vez.

- Você pode adicionar ou remover um recurso de um grupo de recursos a qualquer momento. Cada recurso deve pertencer a um grupo de recursos. Portanto, se você remover um recurso de um grupo, deverá adicioná-lo a outro.

- Você pode mover a maioria dos tipos de recursos para um grupo de recursos diferente a qualquer momento.

- Os recursos em um grupo de recursos podem estar em regiões diferentes.

- Você pode usar um grupo de recursos para controlar o acesso aos recursos nele.

### <a name="building-resource-manager-templates"></a>Criando modelos do Resource Manager

Os modelos do Resource Manager definem declarativamente os recursos e as configurações de recursos que serão implantados em um único grupo de recursos. Você pode usar modelos do Resource Manager para orquestrar implantações complexas sem a necessidade de script excessivo ou configuração manual. Depois de desenvolver um modelo, você pode implantá-lo várias vezes — cada vez com um resultado idêntico.

Um modelo do Resource Manager consiste em quatro seções:

- **Parâmetros**: são entradas para a implantação. Os valores de parâmetro podem ser fornecidos por um processo humano ou automatizado. Um parâmetro de exemplo pode ser um nome de usuário e uma senha de administrador para uma VM do Windows. Os valores de parâmetro são usados em toda a implantação quando eles são especificados.

- **Variáveis**: elas são usadas para armazenar valores que são usados em toda a implantação. Ao contrário dos parâmetros, um valor de variável não é fornecido no momento da implantação. Em vez disso, ele é embutido em código ou gerado dinamicamente.

- **Recursos**: Esta seção do modelo define os recursos a serem implantados, como máquinas virtuais, contas de armazenamento e redes virtuais.

- **Saída**: após a conclusão de uma implantação, o Resource Manager pode retornar dados como cadeias de conexão geradas dinamicamente.

Os seguintes mecanismos estão disponíveis para automação de implantação:

- **Funções**: você pode usar várias funções em modelos do Resource Manager. Isso inclui operações como converter uma cadeia de caracteres em minúsculas, implantar várias instâncias de um recurso definido e retornar dinamicamente o grupo de recursos de destino. As funções do Resource Manager ajudam a criar implantações dinâmicas.

- **Dependências de recursos**: quando você estiver implantando vários recursos, alguns recursos terão uma dependência de outras pessoas. Para facilitar a implantação, você pode usar uma declaração de dependência para que os recursos dependentes sejam implantados antes dos outros.

- **Vinculação de modelo**: de dentro de um modelo do Resource Manager, você pode vincular a outro modelo. Isso permite a decomposição da implantação em um conjunto de modelos direcionados a propósitos específicos.

Você pode criar modelos do Resource Manager em qualquer editor de texto. No entanto, o SDK do Azure para Visual Studio inclui ferramentas para ajudá-lo. Usando o Visual Studio, você pode adicionar recursos ao modelo por meio de um assistente e, em seguida, implantar e depurar o modelo diretamente no Visual Studio. Para obter mais informações, consulte [criando modelos de Azure Resource Manager](../../resource-group-authoring-templates.md).

Por fim, você pode converter grupos de recursos existentes em um modelo reutilizável do portal do Azure. Isso pode ser útil se você quiser criar um modelo implantável de um grupo de recursos existente ou apenas desejar examinar o JSON subjacente. Para exportar um grupo de recursos, selecione o botão **script de automação** nas configurações do grupo de recursos.

## <a name="security-of-azure-resources-rbac"></a>Segurança de recursos do Azure (RBAC)

Você pode conceder acesso operacional a contas de usuário em um escopo especificado: assinatura, grupo de recursos ou recurso individual. Isso significa que você pode implantar um conjunto de recursos em um grupo de recursos, como uma máquina virtual e todos os recursos relacionados, e conceder permissões a um usuário ou grupo específico. Essa abordagem limita o acesso apenas aos recursos que pertencem ao grupo de recursos de destino. Você também pode conceder acesso a um único recurso, como uma máquina virtual ou uma rede virtual.

Para conceder acesso, atribua uma função ao usuário ou ao grupo de usuários. Há muitas funções predefinidas. Você também pode definir suas próprias funções personalizadas.

Aqui estão algumas [funções internas de exemplo no Azure](../../role-based-access-control/built-in-roles.md):

- **Proprietário**: um usuário com essa função pode gerenciar tudo, incluindo o acesso.

- **Leitor**: um usuário com essa função pode ler recursos de todos os tipos (exceto segredos), mas não pode fazer alterações.

- **Colaborador da máquina virtual**: um usuário com essa função pode gerenciar máquinas virtuais, mas não pode gerenciar a rede virtual à qual elas estão conectadas ou a conta de armazenamento onde o arquivo VHD reside.

- **Colaborador do banco**de dados SQL: um usuário com essa função pode gerenciar bancos de dados SQL, mas não suas políticas relacionadas à segurança.

- **Gerenciador de segurança do SQL**: um usuário com essa função pode gerenciar as políticas relacionadas à segurança de servidores e bancos de dados SQL.

- **Colaborador da conta de armazenamento**: um usuário com essa função pode gerenciar contas de armazenamento, mas não pode gerenciar o acesso às contas de armazenamento.

Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

As máquinas virtuais do Azure são um dos serviços de IaaS central no Azure. As máquinas virtuais do Azure dão suporte à implantação de máquinas virtuais Windows ou Linux em um datacenter Microsoft Azure. Com as máquinas virtuais do Azure, você tem controle total sobre a configuração da VM e é responsável por toda a instalação, configuração e manutenção de software.

Quando você estiver implantando uma VM do Azure, poderá selecionar uma imagem do Azure Marketplace ou você poderá fornecer uma imagem generalizada. Essa imagem é usada para aplicar o sistema operacional e a configuração inicial. Durante a implantação, o Resource Manager tratará algumas definições de configuração, como atribuir o nome do computador, as credenciais administrativas e a configuração de rede. Você pode usar extensões de máquina virtual do Azure para automatizar ainda mais as configurações, como instalação de software, configuração de antivírus e soluções de monitoramento.

Você pode criar máquinas virtuais em vários tamanhos diferentes. O tamanho da máquina virtual determina a alocação de recursos, como capacidade de processamento, memória e armazenamento. Em alguns casos, recursos específicos, como adaptadores de rede habilitados para RDMA e discos SSD, estão disponíveis apenas com determinados tamanhos de VM. Para obter uma lista completa de tamanhos e recursos de VM, consulte "tamanhos para máquinas virtuais no Azure" para [Windows](../../virtual-machines/windows/sizes.md) e [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Casos de utilização

Como as máquinas virtuais do Azure oferecem controle total sobre a configuração, elas são ideais para uma ampla gama de cargas de trabalho de servidor que não se ajustam a um modelo de PaaS. As cargas de trabalho do servidor, como servidores de banco de dados (SQL Server, Oracle ou MongoDB), o Windows Server Active Directory, o Microsoft SharePoint e muito mais tornam-se possíveis para serem executadas na plataforma Microsoft Azure. Se desejar, você pode mover essas cargas de trabalho de um datacenter local para uma ou mais regiões do Azure, sem uma grande quantidade de reconfiguração.

### <a name="deployment-of-virtual-machines"></a>Implantação de máquinas virtuais

Você pode implantar máquinas virtuais do Azure usando o portal do Azure, usando a automação com o módulo Azure PowerShell ou usando a automação com a CLI de plataforma cruzada.

#### <a name="portal"></a>Portal

Implantar uma máquina virtual usando o portal do Azure requer apenas uma assinatura ativa do Azure e o acesso a um navegador da Web. Você pode selecionar várias imagens diferentes do sistema operacional com configurações variadas. Todos os requisitos de armazenamento e rede são configurados durante a implantação. Para obter mais informações, consulte "criar uma máquina virtual no portal do Azure" para [Windows](../../virtual-machines/windows/quick-create-portal.md) e [Linux](../../virtual-machines/linux/quick-create-portal.md).

Além de implantar uma máquina virtual do portal do Azure, você pode implantar um modelo de Azure Resource Manager do Portal. Isso implantará e configurará todos os recursos conforme definido no modelo. Para obter mais informações, consulte [implantar recursos com modelos e portal do Azure do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Implantar uma máquina virtual do Azure usando o PowerShell permite a automação completa da implantação de todos os recursos de máquina virtual relacionados, incluindo armazenamento e rede. Para obter mais informações, consulte [criar uma VM do Windows usando o Resource Manager e o PowerShell](../../virtual-machines/windows/quick-create-powershell.md).

Além de implantar os recursos de computação do Azure individualmente, você pode usar o módulo Azure PowerShell para implantar um modelo de Azure Resource Manager. Para obter mais informações, consulte [implantar recursos com modelos e Azure PowerShell do Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>CLI (interface de linha de comando)

Assim como no módulo do PowerShell, a interface de linha de comando do Azure fornece automação de implantação e pode ser usada em sistemas Windows, OS X ou Linux. Quando você estiver usando o comando CLI do Azure **VM Quick-Create** , todos os recursos de máquina virtual relacionados (incluindo armazenamento e rede) e a própria máquina virtual serão implantados. Para obter mais informações, consulte [criar uma VM do Linux no Azure usando a CLI](../../virtual-machines/linux/quick-create-cli.md).

Da mesma forma, você pode usar o CLI do Azure para implantar um modelo de Azure Resource Manager. Para obter mais informações, consulte [implantar recursos com modelos e CLI do Azure do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Acesso e segurança para máquinas virtuais

O acesso a uma máquina virtual pela Internet requer a interface de rede associada ou o balanceador de carga, se aplicável, a ser configurado com um endereço IP público. O endereço IP público inclui um nome DNS que será resolvido para a máquina virtual ou o balanceador de carga. Para obter mais informações, consulte [endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Você gerencia o acesso à máquina virtual por meio do endereço IP público usando um recurso de NSG (grupo de segurança de rede). Um NSG atua como um firewall e permite ou nega o tráfego pela interface de rede ou sub-rede em um conjunto de portas definidas. Por exemplo, para criar uma sessão de Área de Trabalho Remota com uma VM do Azure, você precisa configurar o NSG para permitir o tráfego de entrada na porta 3389. Para obter mais informações, consulte [abrindo portas para uma VM no Azure usando o portal do Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Por fim, como no gerenciamento de qualquer sistema de computador, você deve fornecer segurança para uma máquina virtual do Azure no sistema operacional usando credenciais de segurança e firewalls de software.

## <a name="azure-storage"></a>Armazenamento do Azure

O armazenamento do Azure é um serviço gerenciado pela Microsoft que fornece armazenamento durável, escalonável e redundante. Você pode adicionar uma conta de armazenamento do Azure como um recurso a qualquer grupo de recursos usando qualquer método de implantação de recurso. O Azure inclui quatro tipos de armazenamento: armazenamento de BLOBs, armazenamento de arquivos, armazenamento de tabelas e armazenamento de filas. Ao implantar uma conta de armazenamento, dois tipos de conta estão disponíveis, uso geral e armazenamento de BLOBs. Uma conta de armazenamento de uso geral fornece acesso a todos os quatro tipos de armazenamento. As contas de armazenamento de BLOBs são semelhantes às contas de uso geral, mas contêm BLOBs especializados que incluem camadas de acesso quente e frio. Para obter mais informações sobre o armazenamento de BLOBs, consulte [armazenamento de BLOBs do Azure](../../storage/blobs/storage-blob-storage-tiers.md).

As contas de armazenamento do Azure podem ser configuradas com diferentes níveis de redundância:

- O **armazenamento com redundância local** fornece alta disponibilidade, garantindo que três cópias de todos os dados sejam feitas de forma síncrona antes que uma gravação seja considerada bem-sucedida. Essas cópias são armazenadas em um único recurso em uma única região. As réplicas residem em domínios de falha e domínios de atualização separados. Isso significa que os dados estarão disponíveis mesmo que um nó de armazenamento que esteja mantendo seus dados falhe ou seja colocado offline para ser atualizado.

- O **armazenamento com redundância geográfica** faz três cópias síncronas dos dados na região primária para alta disponibilidade e, em seguida, torna, de forma assíncrona, três réplicas em uma região emparelhada para recuperação de desastres.

- O **armazenamento com redundância geográfica com acesso de leitura** é o armazenamento com redundância geográfica, além da capacidade de ler os dados na região secundária. Essa capacidade o torna adequado para a recuperação de desastre parcial. Se houver um problema com a região primária, você poderá alterar seu aplicativo para ter acesso somente leitura à região emparelhada.

### <a name="use-cases"></a>Casos de utilização

Cada tipo de armazenamento tem um caso de uso diferente.

#### <a name="blob-storage"></a>Armazenamento de blobs

O *blob* do Word é um acrônimo para *objeto binário grande*. Os BLOBs são arquivos não estruturados como aqueles que você armazena em seu computador. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos. O armazenamento de BLOBs do Azure também contém discos de dados de máquinas virtuais do Azure.

O armazenamento do Azure dá suporte a três tipos de BLOBs:

- **Blobs de blocos** são usados para manter arquivos comuns de até 195 GB de tamanho (blocos de 4 MB × 50.000). O caso de uso primário para BLOBs de blocos é o armazenamento de arquivos que são lidos do início ao fim, como arquivos de mídia ou arquivos de imagem para sites. Elas são nomeadas blobs de blocos porque os arquivos com mais de 64 MB devem ser carregados como pequenos blocos. Esses blocos são então consolidados (ou confirmados) no blob final.

- Os **blobs de páginas** são usados para manter arquivos de acesso aleatório de até 1 TB de tamanho. Os blobs de páginas são usados principalmente como o armazenamento de backup para os VHDs que fornecem discos duráveis para máquinas virtuais do Azure, o serviço de computação IaaS no Azure. Eles são chamados de blobs de páginas porque fornecem acesso aleatório de leitura/gravação a páginas de 512 bytes.

- Os **blobs de acréscimo** consistem em blocos como BLOBs de blocos, mas são otimizados para operações de acréscimo. Eles são usados frequentemente para registrar informações de uma ou mais fontes no mesmo BLOB. Por exemplo, você pode gravar todo o log de rastreamento no mesmo blob de acréscimo para um aplicativo em execução em várias VMs. Um único blob de acréscimo pode ter até 195 GB.

Para obter mais informações, consulte Introdução [ao armazenamento de BLOBs do Azure usando o .net](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>Armazenamento de ficheiros

O armazenamento de arquivos do Azure é um serviço que oferece compartilhamentos de arquivos na nuvem usando o protocolo SMB (Server Message Block) padrão. O serviço dá suporte a SMB 2,1 e SMB 3,0. Com o armazenamento de arquivos do Azure, você pode migrar aplicativos que dependem de compartilhamentos de arquivos para o Azure rapidamente e sem regravações dispendiosas. Os aplicativos executados em máquinas virtuais do Azure, em serviços de nuvem ou em clientes locais, podem montar um compartilhamento de arquivos na nuvem. Isso é semelhante a como um aplicativo de desktop monta um compartilhamento SMB típico. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do File Storage em simultâneo.

Como um compartilhamento de armazenamento de arquivos é um compartilhamento de arquivos SMB padrão, os aplicativos em execução no Azure podem acessar dados no compartilhamento por meio de APIs de e/s do sistema de arquivos. Portanto, os desenvolvedores podem usar seu código e suas habilidades existentes para migrar os aplicativos existentes. Os profissionais de ti podem usar os cmdlets do PowerShell para criar, montar e gerenciar compartilhamentos de armazenamento de arquivos como parte da administração de aplicativos do Azure.

Para obter mais informações, consulte Introdução [ao armazenamento de arquivos do Azure no Windows](../../storage/files/storage-how-to-use-files-windows.md) ou [como usar o armazenamento de arquivos do Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Armazenamento de tabela

O Table Storage do Azure é um serviço que armazena dados NoSQL estruturados na nuvem. O armazenamento de tabela é um repositório de chave/atributo com um design sem esquema. Como o armazenamento de tabela é sem esquema, é fácil adaptar seus dados à medida que as necessidades do seu aplicativo evoluem. O acesso aos dados é rápido e rentável para todos os tipos de aplicações. Geralmente, o custo do Table Storage é significativamente inferior ao SQL tradicional para volumes de dados semelhantes.

Pode utilizar o Table Storage para armazenar conjuntos de dados flexíveis, tais como os dados do utilizador para Web Apps, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados necessários para o seu serviço. Você pode armazenar qualquer número de entidades em uma tabela. Uma conta de armazenamento pode conter qualquer número de tabelas, até o limite de capacidade da conta de armazenamento.

Para obter mais informações, consulte Introdução [ao armazenamento de tabelas do Azure](../../cosmos-db/table-storage-how-to-use-dotnet.md).

#### <a name="queue-storage"></a>Armazenamento de filas

O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Na criação de aplicativos para escala, os componentes do aplicativo geralmente são dissociados para que possam ser dimensionados de forma independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Para obter mais informações, consulte Introdução [ao armazenamento de filas do Azure](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Implantando uma conta de armazenamento

Há várias opções para implantar uma conta de armazenamento.

#### <a name="portal"></a>Portal

A implantação de uma conta de armazenamento usando o portal do Azure requer apenas uma assinatura ativa do Azure e o acesso a um navegador da Web. Você pode implantar uma nova conta de armazenamento em um grupo de recursos novo ou existente. Depois de criar a conta de armazenamento, você pode criar um contêiner de BLOB ou compartilhamento de arquivos usando o Portal. Você pode criar entidades de armazenamento de tabela e fila programaticamente. Para obter mais informações, veja [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md).

Além de implantar uma conta de armazenamento do portal do Azure, você pode implantar um modelo de Azure Resource Manager do Portal. Isso implantará e configurará todos os recursos conforme definido no modelo, incluindo qualquer conta de armazenamento. Para obter mais informações, consulte [implantar recursos com modelos e portal do Azure do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Implantar uma conta de armazenamento do Azure usando o PowerShell permite a automação de implantação completa da conta de armazenamento. Para obter mais informações, consulte [usando Azure PowerShell com o armazenamento do Azure](../../storage/common/storage-powershell-guide-full.md).

Além de implantar os recursos do Azure individualmente, você pode usar o módulo Azure PowerShell para implantar um modelo de Azure Resource Manager. Para obter mais informações, consulte [implantar recursos com modelos e Azure PowerShell do Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

#### <a name="command-line-interface-cli"></a>CLI (interface de linha de comando)

Assim como no módulo do PowerShell, a interface de linha de comando do Azure fornece automação de implantação e pode ser usada em sistemas Windows, OS X ou Linux. Você pode usar o comando CLI do Azure **criar conta de armazenamento** para criar uma conta de armazenamento. Para obter mais informações, consulte [usando o CLI do Azure com o armazenamento do Azure.](../../storage/common/storage-azure-cli.md)

Da mesma forma, você pode usar o CLI do Azure para implantar um modelo de Azure Resource Manager. Para obter mais informações, consulte [implantar recursos com modelos e CLI do Azure do Resource Manager](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Acesso e segurança para o armazenamento do Azure

O armazenamento do Azure é acessado de várias maneiras, incluindo o portal do Azure, durante a criação e operação da VM e de bibliotecas de cliente de armazenamento.

#### <a name="virtual-machine-disks"></a>Discos de máquina virtual

Quando você estiver implantando uma máquina virtual, também precisará criar uma conta de armazenamento para manter o disco do sistema operacional da máquina virtual e quaisquer discos de dados adicionais. Você pode selecionar uma conta de armazenamento existente ou criar uma nova. Como o tamanho máximo de um blob é de 1.024 GB, um único disco de VM tem um tamanho máximo de 1.023 GB. Para configurar um disco de dados maior, você pode apresentar vários discos de dados à máquina virtual e agrupá-los como um único disco lógico. Para obter mais informações, consulte "gerenciar discos do Azure" para [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) e [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Ferramentas de armazenamento

As contas de armazenamento do Azure podem ser acessadas por meio de vários gerenciadores de armazenamento diferentes, como o Visual Studio Cloud Explorer. Essas ferramentas permitem que você navegue pelas contas de armazenamento e dados. Para obter mais informações e uma lista de gerenciadores de armazenamento disponíveis, consulte [ferramentas de cliente de armazenamento do Azure](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>API de armazenamento

Os recursos de armazenamento podem ser acessados por qualquer linguagem que possa fazer solicitações HTTP/HTTPS. Além disso, o Storage do Azure oferece bibliotecas de programação para vários idiomas populares. Essas bibliotecas simplificam o trabalho com o armazenamento do Azure manipulando detalhes como invocação síncrona e assíncrona, envio em lote de operações, gerenciamento de exceções e tentativas automáticas. Para obter mais informações, consulte [referência da API REST do serviço de armazenamento do Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Chaves de acesso de armazenamento

Cada conta de armazenamento tem duas chaves de autenticação, uma primária e uma secundária. Pode ser usado para operações de acesso de armazenamento. Essas chaves de armazenamento são usadas para ajudar a proteger uma conta de armazenamento e são necessárias para acessar os dados programaticamente. Há duas chaves para permitir a substituição ocasional das chaves para aumentar a segurança. É essencial manter as chaves seguras porque sua posse, juntamente com o nome da conta, permite acesso ilimitado a todos os dados na conta de armazenamento.

#### <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado

Se você precisar permitir que os usuários tenham acesso controlado aos recursos de armazenamento, poderá criar uma assinatura de acesso compartilhado. Uma assinatura de acesso compartilhado é um token que pode ser anexado a uma URL que habilita o acesso delegado a um recurso de armazenamento. Qualquer pessoa que possua o token pode acessar o recurso que ele aponta com as permissões que ele especifica, pelo período de tempo em que é válido. Para obter mais informações, consulte [usando assinaturas de acesso compartilhado](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Rede Virtual do Azure

As redes virtuais são necessárias para dar suporte às comunicações entre máquinas virtuais. Você pode definir sub-redes, endereço IP personalizado, configurações de DNS, filtragem de segurança e balanceamento de carga. O Azure dá suporte a casos de usos diferentes: redes somente em nuvem ou redes virtuais híbridas.

### <a name="cloud-only-virtual-networks"></a>Redes virtuais somente em nuvem

Uma rede virtual do Azure, por padrão, é acessível somente para os recursos armazenados no Azure. Os recursos conectados à mesma rede virtual podem se comunicar entre si. Você pode associar as interfaces de rede da máquina virtual e os balanceadores de carga a um endereço IP público para tornar a máquina virtual acessível pela Internet. Você pode ajudar a proteger o acesso aos recursos expostos publicamente usando um grupo de segurança de rede.

![Rede virtual do Azure para um aplicativo Web de duas camadas](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Redes virtuais híbridas

Você pode conectar uma rede local a uma rede virtual do Azure usando o ExpressRoute ou uma conexão VPN site a site. Nessa configuração, a rede virtual do Azure é essencialmente uma extensão baseada em nuvem de sua rede local.

Como a rede virtual do Azure está conectada à sua rede local, as redes virtuais entre instalações devem usar uma parte exclusiva do espaço de endereço que sua organização usa. Da mesma forma que diferentes locais corporativos são atribuídos a uma sub-rede IP específica, o Azure torna-se outro local à medida que você estende sua rede.
Há várias opções para implantar uma rede virtual.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [CLI (interface de linha de comando)](../../virtual-network/quick-create-cli.md)

- Modelos de Azure Resource Manager

> **Quando usar**: a qualquer momento que você estiver trabalhando com VMs no Azure, você trabalhará com redes virtuais. Isso permite segmentar suas VMs em sub-redes públicas e privadas, de forma semelhante, de data centers locais semelhantes.
> 
> **Introdução**: implantar uma rede virtual do Azure usando o portal do Azure requer apenas uma assinatura ativa do Azure e o acesso a um navegador da Web. Você pode implantar uma nova rede virtual em um grupo de recursos novo ou existente. Quando você estiver criando uma nova máquina virtual no portal, poderá selecionar uma rede virtual existente ou criar uma nova. Comece e [crie uma rede virtual usando o portal do Azure](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Acesso e segurança para redes virtuais

Você pode ajudar a proteger redes virtuais do Azure usando um grupo de segurança de rede. NSGs contém uma lista de regras de ACL (lista de controle de acesso) que permitem ou negam o tráfego de rede para suas instâncias de VM em uma rede virtual. Você pode associar NSGs a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando você associa um NSG a uma sub-rede, as regras de ACL se aplicam a todas as instâncias de VM nessa sub-rede. Além disso, você pode restringir ainda mais o tráfego para uma VM individual associando um NSG diretamente a essa VM. Para obter mais informações, veja [Filtrar o tráfego de rede com grupos de segurança de rede](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma VM do Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Criar uma VM de Linux](../../virtual-machines/linux/quick-create-portal.md)
