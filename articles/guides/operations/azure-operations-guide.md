---
title: Inicie o guia para os operadores de TI da Azure | Microsoft Docs
description: Inicie o guia para os operadores de TI da Azure
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 1eb0bf2c0e214c3e076895903176d15ee792c70c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093042"
---
# <a name="get-started-for-azure-it-operators"></a>Começar pelos operadores de TI da Azure

Este guia introduz conceitos fundamentais relacionados com a implementação e gestão de uma infraestrutura Microsoft Azure. Se você é novo na computação em nuvem, ou em si Azure, este guia ajuda rapidamente a começar com detalhes de conceitos, implementação e gestão. Muitas secções deste guia discutem uma operação como a implantação de uma máquina virtual e, em seguida, fornecem uma ligação para detalhes técnicos aprofundados.

## <a name="cloud-computing-overview"></a>Descrição geral da computação na cloud

A computação em nuvem fornece uma alternativa moderna ao tradicional datacenter no local. Os fornecedores públicos de nuvem fornecem e gerem todas as infraestruturas de computação e o software de gestão subjacente. Estes fornecedores fornecem uma grande variedade de serviços na nuvem. Um serviço de nuvem neste caso pode ser uma máquina virtual, um servidor web ou um motor de base de dados hospedado na nuvem. Como cliente fornecedor de nuvem, você aluga estes serviços na nuvem como necessário. Ao fazê-lo, converte-se a despesa de capital da manutenção de hardware numa despesa operacional. Um serviço de nuvem também fornece estes benefícios:

- Implantação rápida de grandes ambientes computacional

- Rápida negociação de sistemas que já não são necessários

- Fácil implantação de sistemas tradicionalmente complexos como os equilibradores de carga

- Capacidade de fornecer capacidade ou escala de computação flexível quando necessário

- Ambientes de computação mais rentáveis

- Acesso a partir de qualquer lugar com um portal baseado na Web ou automatização programática

- Serviços baseados em nuvem para atender à maioria das necessidades de computação e aplicação

Com infraestruturas no local, você tem controlo total sobre o hardware e software que é implementado. Historicamente, isto levou a decisões de aquisição de hardware que se concentram em aumentar. Um exemplo é comprar um servidor com mais núcleos para atender às necessidades de desempenho máximas. Infelizmente, esta infraestrutura pode ser subutilizada fora de uma janela de procura. Com o Azure, podes implantar apenas a infraestrutura de que precisas, e ajustar isto para cima ou para baixo a qualquer momento. Isto leva a um foco na escala através da implementação de nós computacional adicionais para satisfazer uma necessidade de desempenho. Aumentar os serviços em nuvem é mais rentável do que aumentar através de hardware caro.

A Microsoft implementou muitos datacenters da Azure em todo o mundo, com mais planejados. Além disso, a Microsoft está a aumentar as nuvens soberanas em regiões como a China e a Alemanha. Apenas as maiores empresas globais podem implementar datacenters desta forma, pelo que a utilização do Azure facilita a implantação dos seus serviços junto dos seus clientes por parte das empresas.

Para as pequenas empresas, o Azure permite um ponto de entrada de baixo custo, com a capacidade de escalar rapidamente à medida que a procura de cálculo aumenta. Isto impede um grande investimento de capital em infraestruturas, e proporciona a flexibilidade aos sistemas de arquitetos e recontecnáticos, se necessário. O uso da computação em nuvem encaixa-se bem com o modelo de crescimento rápido e rápido de escala.

Para obter mais informações sobre as regiões de Azure disponíveis, consulte as [regiões de Azure.](https://azure.microsoft.com/regions/)

### <a name="cloud-computing-model"></a>Modelo de computação em nuvem

O Azure usa um modelo de computação em nuvem baseado em categorias de serviços fornecidos aos clientes. As três categorias de serviço incluem Infraestrutura como Serviço (IaaS), Plataforma como Serviço (PaaS) e Software como Serviço (SaaS). Os fornecedores partilham parte ou a responsabilidade por componentes na pilha de computação em cada uma destas categorias. Vamos dar uma olhada em cada uma das categorias para a computação em nuvem.
![Comparação de pilhas de computação em nuvem](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infraestrutura como serviço

Um fornecedor de nuvem IaaS executa e gere todos os recursos de computação física e o software necessário para permitir a virtualização do computador. Um cliente deste serviço implementa máquinas virtuais nestes centros de dados hospedados. Embora as máquinas virtuais estejam localizadas num datacenter offsite, o consumidor do IaaS tem controlo sobre a configuração e gestão do sistema operativo deixando a infraestrutura subjacente ao fornecedor de nuvem.

O Azure inclui várias soluções IaaS, incluindo máquinas virtuais, conjuntos de escala de máquinas virtuais e a infraestrutura de networking relacionada. As máquinas virtuais são uma escolha popular para inicialmente migrar serviços para Azure porque permite um modelo de migração "elevador e mudança". Pode configurar um VM como a infraestrutura que atualmente gere os seus serviços no seu datacenter e, em seguida, migrar o seu software para o novo VM. Pode precisar de fazer atualizações de configuração, como URLs para outros serviços ou armazenamento, mas pode migrar muitas aplicações desta forma.

Os conjuntos de escala de máquinas virtuais são construídos em cima de Máquinas Virtuais Azure e fornecem uma maneira fácil de implantar aglomerados de VMs idênticos. Os conjuntos de escala de máquinas virtuais também suportam a autoscalagem para que os novos VMs possam ser implantados automaticamente quando necessário. Isto faz com que a escala de máquinas virtuais estabeleça uma plataforma ideal para hospedar clusters de cálculo de microserviços de alto nível, como o Azure Service Fabric e o Azure Container Service.

#### <a name="paas-platform-as-a-service"></a>PaaS: Plataforma como serviço

Com o PaaS, você implanta a sua aplicação num ambiente que o fornecedor de serviço de nuvem fornece. O fornecedor faz toda a gestão da infraestrutura para que possa focar-se no desenvolvimento de aplicações e gestão de dados.

O Azure fornece várias ofertas de computação PaaS, incluindo a funcionalidade de Aplicações Web do Azure App Service e da Azure Cloud Services (funções web e trabalhadora). Em qualquer dos casos, os desenvolvedores têm várias formas de implementar a sua aplicação sem saber nada sobre as porcas e parafusos que a suportam. Os desenvolvedores não têm de criar máquinas virtuais (VMs), usar o Protocolo de Ambiente de Trabalho Remoto (PDR) para iniciar sessão em cada um deles ou instalar a aplicação. Basta carregar num botão (ou perto dele) e as ferramentas fornecidas pela Microsoft fornecem os VMs e, em seguida, implantam e instalam a aplicação neles.

#### <a name="saas-software-as-a-service"></a>SaaS: Software como serviço

O SaaS é um software que é centralmente hospedado e gerido. É geralmente baseado numa arquitetura multitenant - uma única versão da aplicação é usada para todos os clientes. Pode ser dimensionado para várias instâncias para garantir o melhor desempenho em todos os locais. O software SaaS é normalmente licenciado através de uma subscrição mensal ou anual. Os fornecedores de software SaaS são responsáveis por todos os componentes da pilha de software, pelo que tudo o que gere são os serviços prestados.

O Microsoft 365 é um bom exemplo de uma oferta saaS. Os subscritores pagam uma taxa de subscrição mensal ou anual, e recebem o Microsoft Exchange, o Microsoft OneDrive e o resto da suite do Microsoft Office como um serviço. Os subscritores obtêm sempre a versão mais recente e o servidor Exchange é gerido por si. Comparando com a instalação e modernização do Escritório todos os anos, este é menos dispendioso e requer menos esforço.

## <a name="azure-services"></a>Serviços do Azure

O Azure oferece muitos serviços na sua plataforma de computação em nuvem. Estes serviços incluem o seguinte.

### <a name="compute-services"></a>Serviços de computação

Serviços de hospedagem e execução da carga de trabalho das aplicações:

- Máquinas Virtuais Azure - linux e Windows

- Serviços de Aplicações (Aplicativos Web, Aplicativos Móveis, Aplicações Lógicas, Apps API e Apps de Função)

- Azure Batch (para trabalhos de computação paralela e de lote em larga escala)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Serviços de dados

Serviços de armazenamento e gestão de dados:

- Azure Storage (compreende os serviços Azure Blob, Queue, Table e File)

- Base de Dados SQL do Azure

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Cache do Azure para Redis

### <a name="application-services"></a>Serviços de candidatura

Serviços para aplicações de construção e operação:

- Azure Active Directory (Azure AD)

- Autocarro de serviço Azure para ligar sistemas distribuídos

- Azure HDInsight para o processamento de grandes dados

- Aplicativos Azure Logic para fluxos de trabalho de integração e orquestração

- Serviços de Multimédia do Azure

### <a name="network-services"></a>Serviços de rede

Serviços de ligação em rede tanto no interior do Azure como entre o Azure e os centros de dados no local:

- Rede Virtual do Azure

- Azure ExpressRoute

- DNS fornecido pelo Azure

- Gestor de Tráfego do Azure

- Rede de Entrega de Conteúdos do Azure

Para obter documentação detalhada sobre os serviços da Azure, consulte [a documentação do serviço Azure](/azure).

## <a name="azure-key-concepts"></a>Conceitos-chave azul

### <a name="datacenters-and-regions"></a>Datacenters e regiões

O Azure é uma plataforma global de nuvem que está geralmente disponível em muitas regiões do mundo. Ao providenciar um serviço, aplicação ou VM em Azure, é-lhe pedido que selecione uma região. A região selecionada representa um datacenter específico onde a sua aplicação é executado. Para mais informações, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/)

Um dos benefícios de usar o Azure é que pode implementar as suas aplicações em vários datacenters em todo o mundo. A região que escolher pode afetar o desempenho da sua aplicação. É ideal escolher uma região mais próxima da maioria dos seus clientes, para reduzir a latência nos pedidos de rede. Você também pode selecionar uma região para cumprir os requisitos legais para a distribuição da sua app em determinados países/regiões.

### <a name="azure-portal"></a>Portal do Azure

O portal Azure é uma aplicação baseada na web que pode ser usada para criar, gerir e remover recursos e serviços Azure. O portal Azure está localizado em [portal.azure.com.](https://portal.azure.com) Inclui um dashboard personalizável e ferramentas para gerir os recursos da Azure. Também fornece informações de faturação e subscrição. Para obter mais informações, consulte a [visão geral do portal Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) e [gerencie os recursos do Azure através do portal](../../azure-resource-manager/management/manage-resources-portal.md).

### <a name="resources"></a>Recursos

Os recursos Azure são serviços individuais de computação, networking, dados ou hospedagem de aplicações que foram implantados numa subscrição do Azure. Alguns recursos comuns são máquinas virtuais, contas de armazenamento ou bases de dados SQL. Os serviços Azure consistem frequentemente em vários recursos Azure relacionados. Por exemplo, uma máquina virtual Azure pode incluir um VM, conta de armazenamento, adaptador de rede e endereço IP público. Estes recursos podem ser criados, geridos e eliminados individualmente ou em grupo. Os recursos azuis são cobertos com mais detalhes mais tarde neste guia.

### <a name="resource-groups"></a>Grupos de recursos

Um grupo de recursos Azure é um contentor que detém recursos relacionados para uma solução Azure. O grupo de recursos pode incluir todos os recursos para a solução, ou apenas recursos que você quer gerir como um grupo. Os grupos de recursos Azure são cobertos com mais detalhes mais tarde neste guia.

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

Um modelo de Gestor de Recursos Azure é um ficheiro JavaScript Object Notation (JSON) que define um ou mais recursos para implantar num grupo de recursos. Define também as dependências entre os recursos mobilizados. Os modelos do Gestor de Recursos são cobertos com mais detalhes mais tarde neste guia.

### <a name="automation"></a>Automatização

Além de criar, gerir e eliminar recursos utilizando o portal Azure, pode automatizar estas atividades utilizando o PowerShell ou a interface de linha de comando Azure (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell é um conjunto de módulos que fornecem cmdlets para a gestão do Azure. Pode utilizar os cmdlets para criar, gerir e remover os serviços Azure. Os cmdlets podem ajudá-lo a conseguir implementações consistentes, repetíveis e hands-off. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>Interface de linha de comandos do Azure

A interface de linha de comando Azure é uma ferramenta que pode usar para criar, gerir e remover recursos Azure da linha de comando. O Azure CLI está disponível para Linux, Mac OS X e Windows. Para obter mais informações e detalhes técnicos, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>APIs REST

O Azure é construído sobre um conjunto de APIs REST que suportam o portal Azure UI. A maioria destes APIs REST também são suportados para permitir que você provisa programáticamente e gere os seus recursos e aplicações Azure a partir de qualquer dispositivo ativado pela Internet. Para mais informações, consulte a [Referência Azure REST SDK](/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Os administradores podem aceder ao Azure PowerShell e ao Azure CLI através de uma experiência acessível ao navegador chamada Azure Cloud Shell. Esta interface interativa fornece uma ferramenta flexível para os administradores do Linux e do Windows utilizarem a sua interface de linha de comando de eleição, seja bash ou PowerShell. O Azure Cloud Shell pode ser acedida através do portal, como uma interface web autónoma [em shell.azure.com](https://shell.azure.com), ou a partir de vários outros pontos de acesso. Para mais informações, consulte [a visão geral da Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="azure-subscriptions"></a>Subscrições do Azure

Uma subscrição é um agrupamento lógico de serviços Azure que está ligado a uma conta Azure. Uma única conta Azure pode conter várias subscrições. A faturação dos serviços Azure é feita por subscrição. As subscrições do Azure têm um Administrador de Conta, que tem controlo total sobre a subscrição, e um Administrador de Serviço, que tem controlo sobre todos os serviços na subscrição. Para obter informações sobre administradores de subscrição clássicos, consulte [adicionar ou alterar administradores de subscrição do Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). Para além dos administradores, as contas individuais podem ser concedidas a um controlo pormenorizado dos recursos da Azure utilizando [o controlo de acesso baseado em funções da Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Selecione e ative uma subscrição Azure

Antes de poder trabalhar com os serviços Azure, precisa de uma subscrição. Vários tipos de subscrição estão disponíveis.

**Contas gratuitas**: O link para se inscrever numa conta gratuita está no site da [Azure.](https://azure.microsoft.com/) Isto dá-lhe um crédito ao longo de 30 dias para experimentar qualquer combinação de recursos em Azure. Se exceder o seu valor de crédito, a sua conta está suspensa. No final do julgamento, os seus serviços estão desativados e deixarão de funcionar. Pode fazer upgrade para uma subscrição pay-as-you-go a qualquer momento.

**Subscrições da MSDN**: Se tiver uma subscrição msdn, obtém um valor específico no crédito Azure todos os meses. Por exemplo, se tiver uma Microsoft Visual Studio Enterprise com subscrição MSDN, obtém \$ 150 por mês em crédito Azure.

Se exceder o valor do crédito, o seu serviço está desativado até ao início do próximo mês. Pode desligar o limite de gastos e adicionar um cartão de crédito para ser usado para os custos adicionais. Alguns destes custos são descontados para contas MSDN. Por exemplo, paga o preço linux para VMs que executam o Windows Server, e não existe qualquer custo adicional para servidores da Microsoft, como o Microsoft SQL Server. Isto torna as contas da MSDN ideais para cenários de desenvolvimento e teste.

**Contas BizSpark**: O programa Microsoft BizSpark oferece muitos benefícios às startups. Um desses benefícios é o acesso a todo o software da Microsoft para ambientes de desenvolvimento e teste para até cinco contas MSDN. Você recebe $150 em crédito Azure por cada uma dessas cinco contas MSDN, e você paga taxas reduzidas para vários dos serviços Azure, como Máquinas Virtuais.

**Pay-as-you-go**: Com esta subscrição, paga pelo que utiliza ao anexar um cartão de crédito ou cartão de débito à conta. Se você é uma organização, você também pode ser aprovado para faturação.

**Acordos empresariais**: Com um acordo de empresa, compromete-se a utilizar um certo número de serviços em Azure durante o próximo ano, e paga esse valor com antecedência. O compromisso que assumiu é consumido durante todo o ano. Se exceder o valor do compromisso, pode pagar a sobreatenção em atraso. Dependendo do valor do compromisso, você obtém um desconto nos serviços em Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Conceder acesso administrativo a uma assinatura Azure

O Azure RBAC tem vários papéis incorporados que pode usar para atribuir permissões. Para tornar um utilizador um administrador de uma subscrição Azure, atribua-lhes a função [De Proprietário](../../role-based-access-control/built-in-roles.md#owner) no âmbito de subscrição. A função Proprietário dá ao utilizador acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas.

Para obter mais informações, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Ver informações de faturação no portal Azure

Um componente importante da utilização do Azure é a capacidade de visualizar informações de faturação. O portal Azure fornece informações detalhadas sobre a informação de faturação do Azure.

Para mais informações, consulte [Como baixar a fatura da Azure e os dados de utilização diários.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

### <a name="get-billing-information-from-billing-apis"></a>Obtenha informações de faturação de APIs de faturação

Além de visualizar a faturação no portal, pode aceder às informações de faturação utilizando um script ou programa através das APIs de Azure Billing REST:

- Pode utilizar a API de Utilização Azure para recuperar os seus dados de utilização. Pode afinar as informações de utilização da faturação marcando recursos Azure relacionados. Por exemplo, você pode marcar cada um dos recursos em um grupo de recursos com um nome de departamento ou nome de projeto, e, em seguida, rastrear os custos especificamente para essa etiqueta.

- Pode utilizar a visão geral da [Azure consumption API](../../cost-management-billing/manage/consumption-api-overview.md) para listar todos os recursos disponíveis, juntamente com os metadados. Para obter mais informações sobre os preços, consulte [a visão geral dos preços de venda a retalho da Azure.](/rest/api/cost-management/retail-prices/azure-retail-prices)

### <a name="forecast-cost-with-the-pricing-calculator"></a>Custo de previsão com a calculadora de preços

O preço de cada serviço em Azure é diferente. Muitos serviços Azure fornecem níveis básicos, standard e premium. Normalmente, cada nível tem vários níveis de preço e desempenho. Ao utilizar a [calculadora de preços online,](https://azure.microsoft.com/pricing/calculator)pode criar estimativas de preços. A calculadora inclui flexibilidade para estimar o custo num único recurso ou num grupo de recursos.

## <a name="azure-resource-manager"></a>Azure Resource Manager

O Azure Resource Manager é um mecanismo de implantação, gestão e organização para os recursos da Azure. Ao utilizar o Resource Manager, pode juntar muitos recursos individuais num grupo de recursos.

O Gestor de Recursos também inclui capacidades de implementação que permitem uma implementação personalizável e a configuração de recursos relacionados. Por exemplo, utilizando o Resource Manager, pode implementar uma aplicação composta por várias máquinas virtuais, um equilibrador de carga e uma base de dados na Base de Dados Azure SQL como uma única unidade. Desenvolve estas implementações utilizando um modelo de Gestor de Recursos.

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Pode implementar repetidamente a sua solução ao longo do ciclo de vida do desenvolvimento e ter confiança de que os seus recursos são implantados num estado consistente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.

- Pode aplicar o controlo de acesso a todos os serviços do seu grupo de recursos porque o Azure RBAC está nativamente integrado na plataforma de gestão.

- Pode aplicar etiquetas em recursos para organizar logicamente todos os recursos da sua subscrição.

- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos que partilham a mesma etiqueta.

### <a name="tips-for-creating-resource-groups"></a>Dicas para criar grupos de recursos

Quando estiver a tomar decisões sobre os seus grupos de recursos, considere estas dicas:

- Todos os recursos de um grupo de recursos devem ter o mesmo ciclo de vida.

- Pode atribuir um recurso a apenas um grupo de cada vez.

- Pode adicionar ou remover um recurso de um grupo de recursos a qualquer momento. Todos os recursos devem pertencer a um grupo de recursos. Por isso, se removeres um recurso de um grupo, tens de o adicionar a outro.

- Pode mover a maioria dos tipos de recursos para um grupo de recursos diferente a qualquer momento.

- Os recursos de um grupo de recursos podem ser em diferentes regiões.

- Pode utilizar um grupo de recursos para controlar o acesso aos recursos nele.

### <a name="building-resource-manager-templates"></a>Modelos de gestor de recursos de construção

Os modelos do Gestor de Recursos definem declarativamente os recursos e configurações de recursos que serão implantados num único grupo de recursos. Pode utilizar modelos de Gestor de Recursos para orquestrar implementações complexas sem a necessidade de scripts em excesso ou configuração manual. Depois de desenvolver um modelo, pode implantá-lo várias vezes - cada vez com um resultado idêntico.

Um modelo de Gestor de Recursos é composto por quatro secções:

- **Parâmetros**: Estas são entradas para a implantação. Os valores dos parâmetros podem ser fornecidos por um ser humano ou por um processo automatizado. Um parâmetro de exemplo pode ser um nome de utilizador administrativo e senha para um VM do Windows. Os valores dos parâmetros são utilizados durante toda a implantação quando especificados.

- **Variáveis**: Estes são utilizados para manter valores que são utilizados ao longo da implantação. Ao contrário dos parâmetros, um valor variável não é fornecido no momento da implementação. Em vez disso, é codificado duro ou gerado dinamicamente.

- **Recursos**: Esta secção do modelo define os recursos a implementar, tais como máquinas virtuais, contas de armazenamento e redes virtuais.

- **Saída**: Após a implementação ter terminado, o Gestor de Recursos pode devolver dados como cadeias de ligação geradas dinamicamente.

Estão disponíveis os seguintes mecanismos para a automatização da implantação:

- **Funções**: Pode utilizar várias funções nos modelos do Gestor de Recursos. Estas incluem operações como a conversão de uma cadeia em minúsculas, a implantação de múltiplas instâncias de um recurso definido e a devolução dinâmica do grupo de recursos-alvo. As funções de Gestor de Recursos ajudam a construir implementações dinâmicas.

- **Dependências de recursos**: Quando estiver a mobilizar vários recursos, alguns recursos terão uma dependência dos outros. Para facilitar a implementação, pode utilizar uma declaração de dependência para que os recursos dependentes sejam implantados antes dos outros.

- **Ligação do modelo**: De dentro de um modelo de Gestor de Recursos, pode ligar-se a outro modelo. Isto permite a decomposição da implementação num conjunto de modelos específicos e específicos para o propósito.

Pode construir modelos de Gestor de Recursos em qualquer editor de texto. No entanto, o Azure SDK para Visual Studio inclui ferramentas para o ajudar. Ao utilizar o Visual Studio, pode adicionar recursos ao modelo através de um assistente, em seguida, implementar e depurar o modelo diretamente de dentro do Visual Studio. Para obter mais informações, consulte [os modelos do Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md)

Finalmente, pode converter grupos de recursos existentes num modelo reutilizável a partir do portal Azure. Isto pode ser útil se você quiser criar um modelo implantável de um grupo de recursos existente, ou você apenas quer examinar o JSON subjacente. Para exportar um grupo de recursos, selecione o botão **Script** Automation a partir das definições do grupo de recursos.

## <a name="security-of-azure-resources-azure-rbac"></a>Segurança dos recursos Azure (Azure RBAC)

Pode conceder acesso operacional às contas dos utilizadores num âmbito especificado: subscrição, grupo de recursos ou recurso individual. Isto significa que pode implantar um conjunto de recursos num grupo de recursos, como uma máquina virtual e todos os recursos relacionados, e conceder permissões a um utilizador ou grupo específico. Esta abordagem limita o acesso apenas aos recursos que pertencem ao grupo de recursos-alvo. Também pode conceder acesso a um único recurso, como uma máquina virtual ou uma rede virtual.

Para conceder acesso, atribui uma função ao utilizador ou grupo de utilizadores. Há muitos papéis predefinidos. Também pode definir os seus próprios papéis personalizados.

Aqui estão alguns exemplos de [papéis embutidos em Azure:](../../role-based-access-control/built-in-roles.md)

- **Proprietário**: Um utilizador com esta função pode gerir tudo, incluindo acesso.

- **Leitor:** Um utilizador com esta função pode ler recursos de todos os tipos (exceto segredos) mas não pode fazer alterações.

- **Contribuinte de Máquinas Virtuais**: Um utilizador com esta função pode gerir máquinas virtuais mas não consegue gerir a rede virtual à qual estão ligadas ou a conta de armazenamento onde reside o ficheiro VHD.

- **SQL DB Contribuinte**: Um utilizador com esta função pode gerir bases de dados SQL, mas não as suas políticas relacionadas com a segurança.

- **SQL Security Manager**: Um utilizador com esta função pode gerir as políticas relacionadas com a segurança dos servidores e bases de dados SQL.

- **Contribuinte da Conta de Armazenamento**: Um utilizador com esta função pode gerir contas de armazenamento mas não consegue gerir o acesso às contas de armazenamento.

Para obter mais informações, consulte [as funções De Atribuição Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Máquinas Virtuais do Microsoft Azure

Azure Virtual Machines é um dos serviços centrais da IaaS em Azure. A Azure Virtual Machines suporta a implementação de máquinas virtuais Windows ou Linux num datacenter Microsoft Azure. Com as Máquinas Virtuais Azure, tem total controlo sobre a configuração VM e é responsável por toda a instalação, configuração e manutenção do software.

Ao implementar um Azure VM, pode selecionar uma imagem a partir do Azure Marketplace, ou pode fornecer-lhe uma imagem generalizada. Esta imagem é utilizada para aplicar o sistema operativo e a configuração inicial. Durante a implementação, o Gestor de Recursos manuseará algumas definições de configuração, tais como a atribuição do nome do computador, credenciais administrativas e configuração de rede. Pode utilizar extensões de máquinas virtuais Azure para automatizar ainda mais configurações como instalação de software, configuração antivírus e soluções de monitorização.

Pode criar máquinas virtuais em vários tamanhos diferentes. O tamanho da máquina virtual dita a atribuição de recursos, tais como o processamento, a memória e a capacidade de armazenamento. Em alguns casos, funcionalidades específicas como adaptadores de rede ativados por RDMA e discos SSD estão disponíveis apenas com certos tamanhos VM. Para obter uma lista completa de tamanhos e capacidades VM, consulte "Tamanhos para máquinas virtuais em Azure" para [Windows](../../virtual-machines/sizes.md) e [Linux](../../virtual-machines/sizes.md).

### <a name="use-cases"></a>Casos de utilização

Como as máquinas virtuais Azure oferecem controlo total sobre a configuração, são ideais para uma ampla gama de cargas de trabalho do servidor que não se encaixam num modelo PaaS. As cargas de trabalho dos servidores, tais como servidores de bases de dados (SQL Server, Oracle ou MongoDB), Windows Server Ative Directory, Microsoft SharePoint e muitos mais tornam-se possíveis de funcionar na plataforma Microsoft Azure. Se desejar, pode mover essas cargas de trabalho de um datacenter no local para uma ou mais regiões de Azure, sem uma grande quantidade de reconfiguração.

### <a name="deployment-of-virtual-machines"></a>Implantação de máquinas virtuais

Pode implantar máquinas virtuais Azure utilizando o portal Azure, utilizando a automatização com o módulo Azure PowerShell, ou utilizando a automatização com o CLI de plataforma cruzada.

#### <a name="portal"></a>Portal

A implementação de uma máquina virtual utilizando o portal Azure requer apenas uma subscrição ativa do Azure e acesso a um navegador web. Pode selecionar muitas imagens diferentes do sistema operativo com configurações variadas. Todos os requisitos de armazenamento e networking são configurados durante a implantação. Para obter mais informações, consulte "Criar uma máquina virtual no portal Azure" para [Windows](../../virtual-machines/windows/quick-create-portal.md) e [Linux.](../../virtual-machines/linux/quick-create-portal.md)

Além de implementar uma máquina virtual a partir do portal Azure, pode implementar um modelo de Gestor de Recursos Azure a partir do portal. Isto irá implantar e configurar todos os recursos definidos no modelo. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e portal Azure.](../../azure-resource-manager/templates/deploy-portal.md)

#### <a name="powershell"></a>PowerShell

A implementação de uma máquina virtual Azure utilizando o PowerShell permite uma automatização completa de todos os recursos de máquinas virtuais relacionados, incluindo armazenamento e networking. Para obter mais informações, consulte [Criar um VM do Windows utilizando o Gestor de Recursos e o PowerShell.](../../virtual-machines/windows/quick-create-powershell.md)

Além de implementar recursos computacional Azure individualmente, pode utilizar o módulo Azure PowerShell para implementar um modelo de Gestor de Recursos Azure. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Interface de Linha de Comandos (CLI)

Tal como acontece com o módulo PowerShell, a interface de linha de comando Azure fornece automatização de implementação e pode ser utilizada em sistemas Windows, OS X ou Linux. Quando estiver a utilizar o comando Azure CLI **vm quick-create,** todos os recursos de máquinas virtuais relacionados (incluindo armazenamento e networking) e a própria máquina virtual são implantados. Para obter mais informações, consulte [Criar um VM Linux em Azure utilizando o CLI](../../virtual-machines/linux/quick-create-cli.md).

Da mesma forma, pode utilizar o CLI Azure para implementar um modelo de Gestor de Recursos Azure. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Acesso e segurança para máquinas virtuais

O acesso a uma máquina virtual a partir da Internet requer que a interface de rede associada, ou equilibrador de carga, se aplicável, seja configurada com um endereço IP público. O endereço IP público inclui um nome DNS que irá resolver para a máquina virtual ou balanceador de carga. Para mais informações, consulte [endereços IP em Azure.](../../virtual-network/public-ip-addresses.md)

Gere o acesso à máquina virtual através do endereço IP público utilizando um recurso de grupo de segurança de rede (NSG). Um NSG age como uma firewall e permite ou nega o tráfego através da interface de rede ou sub-rede em um conjunto de portas definidas. Por exemplo, para criar uma sessão de Desktop Remoto com um Azure VM, é necessário configurar o NSG para permitir o tráfego de entrada na porta 3389. Para obter mais informações, consulte [portas de abertura para um VM em Azure utilizando o portal Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Finalmente, tal como acontece com a gestão de qualquer sistema informático, deverá fornecer segurança para uma máquina virtual Azure no sistema operativo, utilizando credenciais de segurança e firewalls de software.

## <a name="azure-storage"></a>Armazenamento do Azure

O Azure Storage é um serviço gerido pela Microsoft que fornece armazenamento durável, escalável e redundante. Pode adicionar uma conta de armazenamento Azure como recurso a qualquer grupo de recursos utilizando qualquer método de implementação de recursos. O Azure inclui quatro tipos de armazenamento: armazenamento de bolhas, armazenamento de ficheiros, armazenamento de mesa e armazenamento de fila. Ao implementar uma conta de armazenamento, estão disponíveis dois tipos de conta, para fins gerais e armazenamento de bolhas. Uma conta de armazenamento para fins gerais dá-lhe acesso a todos os quatro tipos de armazenamento. As contas de armazenamento blob são semelhantes às contas de uso geral, mas contêm bolhas especializadas que incluem níveis de acesso quente e frio. Para obter mais informações sobre o armazenamento de bolhas, consulte [o armazenamento da Azure Blob](../../storage/blobs/storage-blob-storage-tiers.md).

As contas de armazenamento Azure podem ser configuradas com diferentes níveis de redundância:

- **O armazenamento localmente redundante** proporciona uma elevada disponibilidade, garantindo que três cópias de todos os dados são feitas sincronizadamente antes de uma escrita ser considerada bem sucedida. Estas cópias são armazenadas numa única instalação numa única região. As réplicas residem em domínios de avaria separados e domínios de atualização. Isto significa que os dados estão disponíveis mesmo que um nó de armazenamento que esteja a reter os seus dados falhe ou seja retirado offline para ser atualizado.

- **O armazenamento geo-redundante** faz três cópias sincronizadas dos dados na região primária para uma elevada disponibilidade, e depois assíncronamente faz três réplicas numa região emparelhada para recuperação de desastres.

- **O armazenamento geo-redundante de acesso** à leitura é um armazenamento geo-redundante, além da capacidade de ler os dados na região secundária. Esta capacidade torna-o adequado para a recuperação parcial de desastres. Se houver algum problema com a região primária, pode alterar a sua aplicação para ter acesso apenas à região emparelhada.

### <a name="use-cases"></a>Casos de utilização

Cada tipo de armazenamento tem uma caixa de utilização diferente.

#### <a name="blob-storage"></a>Armazenamento de blobs

A palavra *blob* é um acrónimo para *objeto binário grande.* As bolhas são ficheiros não estruturados como os que armazenas no teu computador. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos. O armazenamento Azure Blob também contém discos de dados Azure Virtual Machines.

O Armazenamento do Azure suporta três tipos de blobs:

- **As bolhas de bloco** são usadas para conter ficheiros comuns até 195 GB de tamanho (4 MB × 50.000 blocos). A utilização principal para blobs de blocos é o armazenamento de ficheiros que são lidos do início ao fim, como ficheiros multimédia ou ficheiros de imagem para sites. São chamadas blobs de bloco porque os ficheiros maiores que 64 MB devem ser carregados como blocos pequenos. Em seguida, estes blocos são consolidados no blob final.

- **As bolhas de página** são usadas para conter ficheiros de acesso aleatório até 1 TB de tamanho. As bolhas de página são usadas principalmente como armazenamento de suporte para os VHDs que fornecem discos duráveis para Máquinas Virtuais Azure, o serviço de computação IaaS em Azure. São denominados blobs de páginas pois dão um acesso aleatório de leitura/escrita para páginas de 512 bytes.

- **As bolhas de apêndice** consistem em blocos como bolhas de bloco, mas são otimizadas para operações de apêndice. Estes são frequentemente utilizados para registar informações de uma ou mais fontes para a mesma bolha. Por exemplo, pode escrever todos os seus vestígios de registo no mesmo apêndice blob para uma aplicação que está em funcionamento em vários VMs. Um único blob de acréscimo pode ter até 195 GB.

Para obter mais informações, consulte [Começar com o armazenamento Azure Blob utilizando .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

#### <a name="file-storage"></a>Armazenamento de ficheiros

O armazenamento de ficheiros Azure é um serviço que oferece partilhas de ficheiros na nuvem utilizando o protocolo padrão do Bloco de Mensagens do Servidor (SMB). O serviço suporta tanto sMB 2.1 como SMB 3.0. Com o armazenamento do Ficheiro Azure, pode migrar aplicações que dependem de ações de ficheiros para a Azure de forma rápida e sem reescritas dispendiosas. As aplicações em execução em máquinas virtuais Azure, em serviços na nuvem ou a partir de clientes no local podem montar uma partilha de ficheiros na nuvem. Isto é semelhante ao modo como uma aplicação de ambiente de trabalho monta uma partilha típica de SMB. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do Armazenamento de ficheiros em simultâneo.

Como uma partilha de armazenamento de ficheiros é uma partilha de ficheiros SMB padrão, as aplicações em execução no Azure podem aceder aos dados da partilha através do sistema de ficheiros I/O APIs. Os desenvolvedores podem, portanto, usar o seu código e competências existentes para migrar as aplicações existentes. Os profissionais de TI podem usar cmdlets PowerShell para criar, montar e gerir ações de armazenamento de ficheiros como parte da administração de aplicações Azure.

Para obter mais informações, consulte [Começar com o armazenamento de ficheiros Azure no Windows](../../storage/files/storage-how-to-use-files-windows.md) ou como utilizar o armazenamento de [ficheiros Azure com o Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Table Storage

O Table Storage do Azure é um serviço que armazena dados NoSQL estruturados na nuvem. O armazenamento de mesa é uma loja chave/atributo com um design sem esquema. Como o armazenamento de mesa é sem esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O acesso aos dados é rápido e rentável para todos os tipos de aplicações. Geralmente, o custo do Table Storage é significativamente inferior ao SQL tradicional para volumes de dados semelhantes.

Pode utilizar o Table Storage para armazenar conjuntos de dados flexíveis, tais como os dados do utilizador para Web Apps, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados necessários para o seu serviço. Pode armazenar várias entidades numa mesa. Uma conta de armazenamento pode conter qualquer número de tabelas, até ao limite de capacidade da conta de armazenamento.

Para mais informações, consulte [Começar com o armazenamento da tabela Azure.](../../cosmos-db/tutorial-develop-table-dotnet.md)

#### <a name="queue-storage"></a>Armazenamento de filas

O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Na conceção de aplicações para escala, os componentes de aplicação são muitas vezes dissociados para que possam escalar de forma independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Para mais informações, consulte [Começar com o armazenamento da Fila Azure.](../../storage/queues/storage-dotnet-how-to-use-queues.md)

### <a name="deploying-a-storage-account"></a>Implantação de uma conta de armazenamento

Existem várias opções para a implementação de uma conta de armazenamento.

#### <a name="portal"></a>Portal

A implementação de uma conta de armazenamento utilizando o portal Azure requer apenas uma subscrição ativa do Azure e acesso a um navegador web. Pode colocar uma nova conta de armazenamento num grupo de recursos novo ou existente. Depois de criar a conta de armazenamento, pode criar um recipiente blob ou partilha de ficheiros utilizando o portal. Pode criar entidades de armazenamento de mesa e fila programáticamente. Para obter mais informações, veja [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

Além de implementar uma conta de armazenamento a partir do portal Azure, pode implementar um modelo de Gestor de Recursos Azure a partir do portal. Isto irá implantar e configurar todos os recursos definidos no modelo, incluindo quaisquer contas de armazenamento. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e portal Azure.](../../azure-resource-manager/templates/deploy-portal.md)

#### <a name="powershell"></a>PowerShell

A implementação de uma conta de armazenamento Azure utilizando o PowerShell permite uma automatização completa da conta de armazenamento. Para obter mais informações, consulte [a Utilização de Azure PowerShell com armazenamento Azure](/powershell/module/az.storage/).

Além de implementar recursos Azure individualmente, pode utilizar o módulo Azure PowerShell para implementar um modelo de Gestor de Recursos Azure. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Interface de Linha de Comandos (CLI)

Tal como acontece com o módulo PowerShell, a Interface da linha de comando Azure fornece automatização de implementação e pode ser utilizada em sistemas Windows, OS X ou Linux. Pode utilizar a **conta de armazenamento** Azure CLI criar comando para criar uma conta de armazenamento. Para obter mais informações, consulte [utilizar o Azure CLI com armazenamento Azure.](../../storage/blobs/storage-quickstart-blobs-cli.md)

Da mesma forma, pode utilizar o CLI Azure para implementar um modelo de Gestor de Recursos Azure. Para obter mais informações, consulte [implementar recursos com modelos de Gestor de Recursos e Azure CLI](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Acesso e segurança para armazenamento Azure

O Azure Storage é acedido de várias formas, incluindo o portal Azure, durante a criação e operação de VM, e a partir de bibliotecas de clientes de armazenamento.

#### <a name="virtual-machine-disks"></a>Discos de máquinas virtuais

Quando está a implementar uma máquina virtual, também precisa de criar uma conta de armazenamento para manter o disco do sistema operativo da máquina virtual e quaisquer discos de dados adicionais. Pode selecionar uma conta de armazenamento existente ou criar uma nova. Como o tamanho máximo de uma bolha é de 1.024 GB, um único disco VM tem um tamanho máximo de 1.023 GB. Para configurar um disco de dados maior, pode apresentar vários discos de dados à máquina virtual e junto-os como um único disco lógico. Para obter mais informações, consulte "Gerir discos Azure" para [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) e [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Ferramentas de armazenamento

As contas de armazenamento Azure podem ser acedidas através de muitos exploradores de armazenamento diferentes, como o Visual Studio Cloud Explorer. Estas ferramentas permitem-lhe navegar através de contas de armazenamento e dados. Para obter mais informações e uma lista de exploradores de armazenamento disponíveis, consulte [as ferramentas do cliente do Azure Storage](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>API de Armazenamento

Os recursos de armazenamento podem ser acedidos por qualquer idioma que possa fazer pedidos HTTP/HTTPS. Além disso, o Storage do Azure oferece bibliotecas de programação para vários idiomas populares. Estas bibliotecas simplificam o trabalho com o Azure Storage, manuseando detalhes como invocação sincronizada e assíncrona, lote de operações, gestão de exceções e retrações automáticas. Para mais informações, consulte [a referência API do serviço de armazenamento Azure.](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference)

#### <a name="storage-access-keys"></a>Chaves de acesso ao armazenamento

Cada conta de armazenamento tem duas chaves de autenticação, uma primária e uma secundária. Qualquer um pode ser usado para operações de acesso ao armazenamento. Estas chaves de armazenamento são usadas para ajudar a proteger uma conta de armazenamento e são necessárias para aceder programáticamente aos dados. Há duas chaves para permitir o capotamento ocasional das chaves para aumentar a segurança. É fundamental manter as chaves seguras porque a sua posse, juntamente com o nome da conta, permite o acesso ilimitado a quaisquer dados na conta de armazenamento.

#### <a name="shared-access-signatures"></a>Assinaturas de acesso compartilhado

Se precisar de permitir que os utilizadores tenham acesso controlado aos seus recursos de armazenamento, pode criar uma assinatura de acesso partilhado. Uma assinatura de acesso partilhado é um símbolo que pode ser anexado a um URL que permite o acesso delegado a um recurso de armazenamento. Qualquer pessoa que possua o símbolo pode aceder ao recurso a que aponta com as permissões que especifica, pelo período de tempo que é válido. Para obter mais informações, consulte [utilização de assinaturas de acesso partilhado.](../../storage/common/storage-sas-overview.md)

## <a name="azure-virtual-network"></a>Rede Virtual do Azure

As redes virtuais são necessárias para suportar comunicações entre máquinas virtuais. Pode definir sub-redes, endereço IP personalizado, definições de DNS, filtragem de segurança e equilíbrio de carga. O Azure suporta diferentes casos de uso: redes apenas em nuvem ou redes virtuais híbridas.

### <a name="cloud-only-virtual-networks"></a>Redes virtuais apenas em nuvem

Uma rede virtual Azure, por padrão, só é acessível a recursos armazenados em Azure. Os recursos ligados à mesma rede virtual podem comunicar entre si. Pode associar interfaces de rede de máquinas virtuais e equilibradores de carga com um endereço IP público para tornar a máquina virtual acessível através da Internet. Pode ajudar a garantir o acesso aos recursos expostos publicamente utilizando um grupo de segurança de rede.

![Rede Virtual Azure para uma aplicação web de 2 níveis](/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Redes virtuais híbridas

Pode ligar uma rede no local a uma rede virtual Azure utilizando o ExpressRoute ou uma ligação VPN site-to-site. Nesta configuração, a rede virtual Azure é essencialmente uma extensão baseada na nuvem da sua rede no local.

Como a rede virtual Azure está ligada à sua rede no local, as redes virtuais de instalações cruzadas devem utilizar uma parte única do espaço de endereço que a sua organização utiliza. Da mesma forma que diferentes localizações corporativas são atribuídas a uma sub-rede IP específica, a Azure torna-se outra localização à medida que estende a sua rede.
Existem várias opções para implantar uma rede virtual.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Interface de Linha de Comandos (CLI)](../../virtual-network/quick-create-cli.md)

- Modelos do Azure Resource Manager

> **Quando utilizar:** Sempre que estiver a trabalhar com VMs em Azure, trabalhará com redes virtuais. Isto permite segmentar os seus VMs em sub-redes públicas e privadas similares nos centros de dados no local.
>
> **Começar**: Implementar uma rede virtual Azure utilizando o portal Azure requer apenas uma subscrição ativa do Azure e acesso a um navegador web. Pode implantar uma nova rede virtual num grupo de recursos novo ou existente. Quando estiver a criar uma nova máquina virtual a partir do portal, pode selecionar uma rede virtual existente ou criar uma nova. Inicie-se e [Crie uma rede virtual utilizando o portal Azure](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Acesso e segurança para redes virtuais

Pode ajudar a proteger as redes virtuais do Azure utilizando um grupo de segurança de rede. Os NSGs contêm uma lista de regras de controlo de acesso (ACL) que permitem ou negam o tráfego de rede às suas instâncias VM numa rede virtual. Pode associar NSGs a sub-redes ou a instâncias VM individuais nessa sub-rede. Quando associa um NSG a uma sub-rede, as regras da ACL aplicam-se a todas as instâncias VM nessa sub-rede. Além disso, pode restringir ainda mais o tráfego a um VM individual associando um NSG diretamente com esse VM. Para obter mais informações, veja [Filtrar o tráfego de rede com grupos de segurança de rede](../../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma VM do Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Criar uma VM do Linux](../../virtual-machines/linux/quick-create-portal.md)