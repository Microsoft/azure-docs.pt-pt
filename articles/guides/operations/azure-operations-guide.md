---
title: Inicie guia para operadores de TI Azure [ Microsoft Docs
description: Inicie guia para operadores de TI Azure
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 4f9da6cbfe8d1e6b92c39148b275de193730c8f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77623569"
---
# <a name="get-started-for-azure-it-operators"></a>Inicie-se para operadores de TI Azure

Este guia introduz conceitos fundamentais relacionados com a implementação e gestão de uma infraestrutura Microsoft Azure. Se você é novo na computação em nuvem, ou o próprio Azure, este guia ajuda a começar rapidamente com conceitos, implementação e detalhes de gestão. Muitas secções deste guia discutem uma operação como a implantação de uma máquina virtual e, em seguida, fornecem um link para detalhes técnicos aprofundados.

## <a name="cloud-computing-overview"></a>Descrição geral da computação na cloud

A computação em nuvem fornece uma alternativa moderna ao centro de dados tradicional no local. Os vendedores públicos de nuvem fornecem e gerem todas as infraestruturas de computação e o software de gestão subjacente. Estes fornecedores fornecem uma grande variedade de serviços na nuvem. Um serviço de nuvem neste caso pode ser uma máquina virtual, um servidor web ou motor de base de dados hospedado em nuvem. Como cliente fornecedor de nuvem, você aluga estes serviços na nuvem de forma necessária. Ao fazê-lo, converte-se as despesas de capital da manutenção de hardware numa despesa operacional. Um serviço na nuvem também fornece estes benefícios:

- Implantação rápida de grandes ambientes de computação

- Desalocação rápida de sistemas que já não são necessários

- Fácil implantação de sistemas tradicionalmente complexos como equilibradores de carga

- Capacidade de fornecer capacidade ou escala de computação flexível quando necessário

- Ambientes de computação mais rentáveis

- Acesso a partir de qualquer lugar com um portal baseado na web ou automatização programática

- Serviços baseados na nuvem para atender a maioria das necessidades de computação e aplicação

Com a infraestrutura no local, você tem total controlo sobre o hardware e software que é implementado. Historicamente, isto levou a decisões de aquisição de hardware que se concentram na escalada. Um exemplo é comprar um servidor com mais núcleos para atender às necessidades de desempenho máxima. Infelizmente, esta infraestrutura pode ser subutilizada fora de uma janela de procura. Com o Azure, só pode implementar a infraestrutura de que necessita, e ajustar isso para cima ou para baixo a qualquer momento. Isto leva a um foco na escala através da implantação de nós de computação adicionais para satisfazer uma necessidade de desempenho. A escala geminação dos serviços na nuvem é mais rentável do que a escala através de hardware caro.

A Microsoft implementou muitos datacenters Azure em todo o mundo, com mais planeados. Além disso, a Microsoft está a aumentar as nuvens soberanas em regiões como a China e a Alemanha. Só as maiores empresas globais podem implantar datacenters desta forma, pelo que a utilização do Azure facilita às empresas de qualquer dimensão a implementação dos seus serviços perto dos seus clientes.

Para as pequenas empresas, o Azure permite um ponto de entrada de baixo custo, com a capacidade de escalar rapidamente à medida que a procura de cálculo aumenta. Isto impede um grande investimento de capital em infraestruturas, proporcionando a flexibilidade aos sistemas arquitetos e rearquitectos, se necessário. O uso da computação em nuvem encaixa-se bem com o modelo rápido de escala e falha do crescimento do arranque.

Para obter mais informações sobre as regiões disponíveis do Azure, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/)

### <a name="cloud-computing-model"></a>Modelo de computação em nuvem

O Azure utiliza um modelo de computação em nuvem baseado em categorias de serviço prestadas aos clientes. As três categorias de serviço incluem Infraestruturas como Serviço (IaaS), Plataforma como Serviço (PaaS) e Software como Serviço (SaaS). Os fornecedores partilham parte ou a toda a responsabilidade pelos componentes na pilha de computação em cada uma destas categorias. Vamos dar uma olhada em cada uma das categorias para computação em nuvem.
![Comparação de pilha de computação em nuvem](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: Infraestrutura saqueada como serviço

Um fornecedor de nuvem IaaS executa e gere todos os recursos de computação física e o software necessário para permitir a virtualização do computador. Um cliente deste serviço implementa máquinas virtuais nestes centros de dados hospedados. Embora as máquinas virtuais estejam localizadas num centro de dados fora do local, o consumidor iaaS tem controlo sobre a configuração e gestão do sistema operativo deixando a infraestrutura subjacente ao fornecedor em nuvem.

O Azure inclui várias soluções IaaS, incluindo máquinas virtuais, conjuntos de escala de máquinas virtuais e a infraestrutura de rede relacionada. As máquinas virtuais são uma escolha popular para inicialmente migrar serviços para O Azure porque permite um modelo de migração "lift and shift". Pode configurar um VM como a infraestrutura que está atualmente a executar os seus serviços no seu datacenter e, em seguida, migrar o seu software para o novo VM. Pode ser necessário fazer atualizações de configuração, como URLs para outros serviços ou armazenamento, mas pode migrar muitas aplicações desta forma.

Os conjuntos de escala de máquinas virtuais são construídos em cima de Máquinas Virtuais Azure e fornecem uma maneira fácil de implementar clusters de VMs idênticos. Os conjuntos de escala de máquinavirtual também suportam a autoscalcificação para que os novos VMs possam ser implantados automaticamente quando necessário. Isto faz com que a escala de máquinas virtuais seja uma plataforma ideal para acolher clusters de computação de microserviçode de alto nível, como o Azure Service Fabric e o Serviço de Contentores Azure.

#### <a name="paas-platform-as-a-service"></a>PaaS: Plataforma como serviço

Com o PaaS, você implementa a sua aplicação num ambiente que o fornecedor de serviços na nuvem fornece. O fornecedor faz toda a gestão da infraestrutura para que possa focar-se no desenvolvimento de aplicações e na gestão de dados.

O Azure fornece várias ofertas de computação PaaS, incluindo a funcionalidade de Aplicações Web do Azure App Service e azure Cloud Services (funções web e de trabalhadores). Em qualquer dos casos, os desenvolvedores têm várias formas de implementar a sua aplicação sem saber nada sobre as porcas e parafusos que a suportam. Os desenvolvedores não têm de criar máquinas virtuais (VMs), usar o Protocolo de Ambiente de Trabalho Remoto (RDP) para iniciar sessão em cada uma delas ou instalar a aplicação. Basta carregar num botão (ou perto dele), e as ferramentas fornecidas pela Microsoft fornecem os VMs e, em seguida, implantam e instalam a aplicação nos mesmos.

#### <a name="saas-software-as-a-service"></a>SaaS: Software como serviço

SaaS é um software que é centralmente hospedado e gerido. É geralmente baseado em uma arquitetura multiarrendatária - uma única versão da aplicação é usada para todos os clientes. Pode ser dimensionado para várias instâncias para garantir o melhor desempenho em todos os locais. O software SaaS é normalmente licenciado através de uma subscrição mensal ou anual. Os fornecedores de software SaaS são responsáveis por todos os componentes da pilha de software, pelo que tudo o que gere são os serviços prestados.

O Microsoft Office 365 é um bom exemplo de uma oferta SaaS. Os subscritores pagam uma taxa de subscrição mensal ou anual, e recebem o Microsoft Exchange, o Microsoft OneDrive e o resto da suite do Microsoft Office como serviço. Os subscritores obtêm sempre a versão mais recente e o servidor Exchange é gerido para si. Em comparação com a instalação e modernização do Office todos os anos, este é mais barato e requer menos esforço.

## <a name="azure-services"></a>Serviços do Azure

O Azure oferece muitos serviços na sua plataforma de computação em nuvem. Estes serviços incluem o seguinte.

### <a name="compute-services"></a>Serviços de computação

Serviços para hospedagem e execução de trabalho de aplicação:

- Máquinas Virtuais Azure - tanto Linux como Windows

- Serviços de Aplicações (Aplicações Web, Aplicações Móveis, Aplicações Lógicas, Aplicações API e Aplicações de Função)

- Lote Azure (para trabalhos de computação paralela e de lote em larga escala)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Serviços de dados

Serviços de armazenamento e gestão de dados:

- Armazenamento Azure (compreende os serviços Azure Blob, Queue, Table e File)

- Base de Dados SQL do Azure

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Cache do Azure para Redis

### <a name="application-services"></a>Serviços de candidatura

Serviços de construção e aplicações operacionais:

- Azure Active Directory (Azure AD)

- Ônibus de serviço Azure para ligar sistemas distribuídos

- Azure HDInsight para o processamento de big data

- Aplicativos azure logic para fluxos de trabalho de integração e orquestração

- Serviços de Multimédia do Azure

### <a name="network-services"></a>Serviços de rede

Serviços de networking tanto no Azure como entre o Azure e os centros de dados no local:

- Rede Virtual do Azure

- Azure ExpressRoute

- DNS fornecidos pelo Azure

- Traffic Manager do Azure

- Rede de Entrega de Conteúdos do Microsoft Azure

Para obter documentação detalhada sobre os serviços do Azure, consulte a documentação do [serviço Azure.](https://docs.microsoft.com/azure)

## <a name="azure-key-concepts"></a>Conceitos-chave azure

### <a name="datacenters-and-regions"></a>Datacenters e regiões

O Azure é uma plataforma global de nuvem que está geralmente disponível em muitas regiões do mundo. Ao fornecer um serviço, aplicação ou VM em Azure, é-lhe pedido que selecione uma região. A região selecionada representa um datacenter específico onde a sua aplicação é executado. Para mais informações, consulte [as regiões de Azure.](https://azure.microsoft.com/regions/)

Um dos benefícios da utilização do Azure é que pode implementar as suas aplicações em vários centros de dados em todo o mundo. A região que escolher pode afetar o desempenho da sua candidatura. É ideal escolher uma região mais próxima da maioria dos seus clientes, para reduzir a latência nos pedidos de rede. Pode também selecionar uma região para cumprir os requisitos legais para a distribuição da sua app em determinados países/regiões.

### <a name="azure-portal"></a>Portal do Azure

O portal Azure é uma aplicação baseada na web que pode ser usada para criar, gerir e remover recursos e serviços Azure. O portal Azure está localizado em [portal.azure.com.](https://portal.azure.com) Inclui um dashboard personalizável e ferramentas para gerir os recursos do Azure. Também fornece informações de faturação e subscrição. Para mais informações, consulte o [portal Microsoft Azure](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) e gere os [recursos do Azure através do portal](https://docs.microsoft.com/azure/azure-portal/resource-group-portal).

### <a name="resources"></a>Recursos

Os recursos do Azure são serviços individuais de computação, networking, dados ou app hosting que foram implantados numa subscrição do Azure. Alguns recursos comuns são máquinas virtuais, contas de armazenamento ou bases de dados SQL. Os serviços Azure consistem frequentemente de vários recursos Azure relacionados. Por exemplo, uma máquina virtual Azure pode incluir um VM, conta de armazenamento, adaptador de rede e endereço IP público. Estes recursos podem ser criados, geridos e eliminados individualmente ou em grupo. Os recursos azure são cobertos mais detalhadamente mais tarde neste guia.

### <a name="resource-groups"></a>Grupos de recursos

Um grupo de recursos Azure é um recipiente que detém recursos relacionados para uma solução Azure. O grupo de recursos pode incluir todos os recursos para a solução, ou apenas recursos que pretende gerir como um grupo. Os grupos de recursos Azure são cobertos mais detalhadamente mais tarde neste guia.

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

Um modelo de Gestor de Recursos Azure é um ficheiro JavaScript Object Notation (JSON) que define um ou mais recursos para implantar num grupo de recursos. Define também as dependências entre os recursos implantados. Os modelos do Gestor de Recursos são cobertos com mais detalhes mais tarde neste guia.

### <a name="automation"></a>Automatização

Além de criar, gerir e eliminar recursos utilizando o portal Azure, pode automatizar estas atividades utilizando a PowerShell ou a interface de linha de comando Azure (CLI).

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell é um conjunto de módulos que fornecem cmdlets para a gestão do Azure. Pode utilizar os cmdlets para criar, gerir e remover os serviços Azure. Os cmdlets podem ajudá-lo a alcançar implementações consistentes, repetíveis e de mãos-livres. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/install-Az-ps).

#### <a name="azure-command-line-interface"></a>Interface de linha de comandos do Azure

A interface de linha de comando Azure é uma ferramenta que pode utilizar para criar, gerir e remover os recursos Azure da linha de comando. O Azure CLI está disponível para Linux, Mac OS X e Windows. Para mais informações e detalhes técnicos, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>APIs REST

O Azure é construído sobre um conjunto de APIs REST que suportam o portal Azure UI. A maioria destas APIs REST também são suportadas para permitir que você disponibilize programáticamente e gere os seus recursos e aplicações Azure a partir de qualquer dispositivo via Internet. Para mais informações, consulte a [Referência SDK Azure REST](https://docs.microsoft.com/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Os administradores podem aceder ao Azure PowerShell e ao Azure CLI através de uma experiência acessível ao navegador chamada Azure Cloud Shell. Esta interface interativa fornece uma ferramenta flexível para os administradores do Linux e do Windows utilizarem a sua interface de escolha de linha de comando, seja a Bash ou a PowerShell. A Azure Cloud Shell pode ter acesso através do portal ,como interface web autónoma em [shell.azure.com](https://shell.azure.com), ou de vários outros pontos de acesso. Para mais informações, consulte [a visão geral da Casca de Nuvem Azure](https://docs.microsoft.com/azure/cloud-shell/overview).

## <a name="azure-subscriptions"></a>Subscrições do Azure

Uma subscrição é um agrupamento lógico de serviços Azure que está ligado a uma conta Azure. Uma única conta Azure pode conter várias subscrições. A faturação dos serviços Azure é feita por subscrição. As subscrições do Azure têm um Administrador de Conta, que tem total controlo sobre a subscrição, e um Administrador de Serviço, que tem controlo sobre todos os serviços na subscrição. Para obter informações sobre administradores de subscrição clássicos, consulte Adicionar ou alterar administradores de [subscrição do Azure.](../../cost-management-billing/manage/add-change-subscription-administrator.md) Para além dos administradores, as contas individuais podem ser concedidas a um controlo detalhado dos recursos do Azure utilizando [o controlo de acesso baseado em funções (RBAC)](../../role-based-access-control/overview.md).

### <a name="select-and-enable-an-azure-subscription"></a>Selecione e ative uma subscrição Azure

Antes de poder trabalhar com os serviços Azure, precisa de uma subscrição. Vários tipos de subscrição estão disponíveis.

**Contas gratuitas**: O link para se inscrever para uma conta gratuita está no site do [Azure](https://azure.microsoft.com/). Isto dá-lhe um crédito ao longo de 30 dias para experimentar qualquer combinação de recursos em Azure. Se exceder o seu valor de crédito, a sua conta está suspensa. No final do julgamento, os seus serviços estão desativados e deixarão de funcionar. Pode fazer upgrade para uma subscrição pay-as-you-go a qualquer momento.

**Subscrições MSDN**: Se tiver uma subscrição MSDN, obtém um valor específico no crédito Azure todos os meses. Por exemplo, se tiver uma Microsoft Visual Studio Enterprise com \$subscrição MSDN, obtém 150 por mês em crédito Azure.

Se exceder o valor do crédito, o seu serviço será desativado até ao início do próximo mês. Pode desligar o limite de gastos e adicionar um cartão de crédito a ser usado para os custos adicionais. Alguns destes custos são descontados para contas MSDN. Por exemplo, paga o preço linux para VMs que executam o Windows Server, e não existe nenhum custo adicional para servidores da Microsoft, como o Microsoft SQL Server. Isto torna as contas da MSDN ideais para cenários de desenvolvimento e teste.

**Contas BizSpark**: O programa Microsoft BizSpark proporciona muitos benefícios às startups. Um desses benefícios é o acesso a todo o software da Microsoft para ambientes de desenvolvimento e teste para até cinco contas MSDN. Você recebe $150 em crédito Azure por cada uma dessas cinco contas MSDN, e você paga taxas reduzidas para vários dos serviços Azure, como Máquinas Virtuais.

**Pay-as-you-go**: Com esta subscrição, paga-se pelo que utiliza anexando um cartão de crédito ou cartão de débito à conta. Se você é uma organização, você também pode ser aprovado para faturação.

**Acordos empresariais**: Com um acordo de empresa, compromete-se a utilizar um certo número de serviços em Azure durante o próximo ano, e paga esse valor com antecedência. O compromisso que faz é consumido ao longo do ano. Se exceder o valor do compromisso, pode pagar a sobrecarga em atraso. Dependendo do valor do compromisso, obtém um desconto nos serviços em Azure.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Conceder acesso administrativo a uma subscrição do Azure

O RBAC tem várias funções incorporadas que pode usar para atribuir permissões. Para fazer de um utilizador um administrador de uma subscrição Azure, atribua-lhes a função [Proprietário](../../role-based-access-control/built-in-roles.md#owner) no âmbito de subscrição. A função Proprietário dá ao utilizador acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas.

Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Ver informações de faturação no portal Azure

Um componente importante da utilização do Azure é a capacidade de visualizar informações de faturação. O portal Azure fornece informações detalhadas sobre a informação de faturação do Azure.

Para mais informações, consulte Como descarregar a fatura da [fatura do Azure e os dados de utilização diária](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Obtenha informações de faturação de APIs de faturação

Além de visualizar a faturação no portal, pode aceder à informação de faturação utilizando um script ou programa através das APIs REST DE Faturação Azure:

- Pode utilizar a API de utilização azure para recuperar os seus dados de utilização. Pode afinar as informações de utilização de faturação marcando recursos Azure relacionados. Por exemplo, pode marcar cada um dos recursos num grupo de recursos com um nome de departamento ou projeto, e, em seguida, rastrear os custos especificamente para essa etiqueta.

- Pode utilizar a API do Cartão De Tarifa Azure para listar todos os recursos disponíveis, juntamente com os metadados e informações sobre os preços de cada um desses recursos.

Para obter mais informações, veja [Gain insights into your Microsoft Azure resource consumption](../../cost-management-billing/manage/usage-rate-card-overview.md) (Obter informações sobre o consumo de recursos do Microsoft Azure).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Custo de previsão com a calculadora de preços

O preço de cada serviço em Azure é diferente. Muitos serviços Azure fornecem níveis Básico, Standard e Premium. Normalmente, cada nível tem vários níveis de preço e desempenho. Ao utilizar a calculadora de [preços online,](https://azure.microsoft.com/pricing/calculator)pode criar estimativas de preços. A calculadora inclui flexibilidade para estimar o custo num único recurso ou num grupo de recursos.

## <a name="azure-resource-manager"></a>Azure Resource Manager

O Azure Resource Manager é um mecanismo de implantação, gestão e organização para os recursos Azure. Ao utilizar o Gestor de Recursos, pode juntar muitos recursos individuais num grupo de recursos.

O Gestor de Recursos também inclui capacidades de implementação que permitem a implementação personalizável e configuração de recursos relacionados. Por exemplo, utilizando o Gestor de Recursos, pode implementar uma aplicação que consiste em múltiplas máquinas virtuais, um equilibrante de carga e uma base de dados SQL como uma única unidade. Desenvolve estas implementações utilizando um modelo de Gestor de Recursos.

O Resource Manager oferece várias vantagens:

- Pode implementar, gerir e monitorizar todos os recursos da sua solução como um grupo, em vez de os processar individualmente.

- Pode implementar repetidamente a sua solução ao longo do ciclo de vida de desenvolvimento e ter confiança de que os seus recursos são implantados num estado consistente.

- Pode gerir a sua infraestrutura através de modelos declarativos em vez de scripts.

- Pode definir as dependências entre os recursos, de modo a que sejam implementados na ordem correta.

- Pode aplicar o controlo de acesso a todos os serviços do seu grupo de recursos porque o RBAC está integrado de forma nativa na plataforma de gestão.

- Pode aplicar etiquetas em recursos para organizar logicamente todos os recursos da sua subscrição.

- Pode clarificar a faturação da sua organização visualizando os custos de um grupo de recursos que partilham a mesma etiqueta.

### <a name="tips-for-creating-resource-groups"></a>Dicas para criar grupos de recursos

Quando estiver a tomar decisões sobre os seus grupos de recursos, considere estas dicas:

- Todos os recursos de um grupo de recursos devem ter o mesmo ciclo de vida.

- Pode atribuir um recurso a apenas um grupo de cada vez.

- Pode adicionar ou remover um recurso de um grupo de recursos a qualquer momento. Todos os recursos devem pertencer a um grupo de recursos. Por isso, se remover um recurso de um grupo, deve adicioná-lo a outro.

- Você pode mover a maioria dos tipos de recursos para um grupo de recursos diferentes a qualquer momento.

- Os recursos de um grupo de recursos podem ser em diferentes regiões.

- Você pode usar um grupo de recursos para controlar o acesso aos recursos nele.

### <a name="building-resource-manager-templates"></a>Modelos de Gestor de Recursos de Construção

Os modelos do Gestor de Recursos definem declarativamente os recursos e configurações de recursos que serão implantados num único grupo de recursos. Pode utilizar modelos do Gestor de Recursos para orquestrar implementações complexas sem a necessidade de excesso de scripts ou configuração manual. Depois de desenvolver um modelo, pode implantá-lo várias vezes — cada vez com um resultado idêntico.

Um modelo de Gestor de Recursos é composto por quatro secções:

- **Parâmetros:** Estas são as inputs para a implantação. Os valores dos parâmetros podem ser fornecidos por um processo humano ou automatizado. Um parâmetro de exemplo pode ser um nome de utilizador administrativo e uma palavra-passe para um VM do Windows. Os valores do parâmetro são utilizados durante toda a implantação quando são especificados.

- **Variáveis**: Estes são utilizados para manter valores que são utilizados durante toda a implantação. Ao contrário dos parâmetros, um valor variável não é fornecido no momento da implantação. Em vez disso, é codificado ou gerado dinamicamente.

- **Recursos**: Esta secção do modelo define os recursos a implantar, tais como máquinas virtuais, contas de armazenamento e redes virtuais.

- **Saída**: Depois de concluída uma implementação, o Gestor de Recursos pode devolver dados tais como cordas de ligação geradas dinamicamente.

Estão disponíveis os seguintes mecanismos para automatização de implantação:

- **Funções**: Pode utilizar várias funções nos modelos do Gestor de Recursos. Estas incluem operações como converter uma cadeia em minúscula, implantar múltiplas instâncias de um recurso definido, e dinamicamente devolver o grupo de recursos-alvo. As funções do Gestor de Recursos ajudam a construir implementações dinâmicas.

- **Dependências de recursos**: Quando estiver a implantar vários recursos, alguns recursos terão uma dependência de outros. Para facilitar a implantação, pode utilizar uma declaração de dependência para que os recursos dependentes sejam implantados antes dos outros.

- **Ligação do modelo**: De dentro de um modelo de Gestor de Recursos, pode ligar-se a outro modelo. Isto permite a decomposição da implementação num conjunto de modelos específicos e específicos para o propósito.

Você pode construir modelos de Gestor de Recursos em qualquer editor de texto. No entanto, o Azure SDK para Estúdio Visual inclui ferramentas para o ajudar. Ao utilizar o Visual Studio, pode adicionar recursos ao modelo através de um assistente, em seguida, implementar e depurar o modelo diretamente de dentro do Visual Studio. Para mais informações, consulte [os modelos de Gestor de Recursos Do Azure.](../../resource-group-authoring-templates.md)

Finalmente, você pode converter os grupos de recursos existentes em um modelo reutilizável do portal Azure. Isto pode ser útil se você quiser criar um modelo desdobrável de um grupo de recursos existente, ou apenas quer examinar o JSON subjacente. Para exportar um grupo de recursos, selecione o botão **Automation Script** a partir das definições do grupo de recursos.

## <a name="security-of-azure-resources-rbac"></a>Segurança dos recursos Azure (RBAC)

Pode conceder acesso operacional às contas de utilizador num âmbito específico: subscrição, grupo de recursos ou recursos individuais. Isto significa que você pode implementar um conjunto de recursos em um grupo de recursos, como uma máquina virtual e todos os recursos relacionados, e conceder permissões a um utilizador ou grupo específico. Esta abordagem limita o acesso apenas aos recursos que pertencem ao grupo de recursos-alvo. Também pode conceder acesso a um único recurso, como uma máquina virtual ou uma rede virtual.

Para conceder acesso, atribui uma função ao utilizador ou grupo de utilizadores. Há muitos papéis predefinidos. Também pode definir os seus próprios papéis personalizados.

Aqui estão alguns [exemplos de papéis incorporados em Azure:](../../role-based-access-control/built-in-roles.md)

- **Proprietário**: Um utilizador com esta função pode gerir tudo, incluindo acesso.

- **Leitor**: Um utilizador com esta função pode ler recursos de todos os tipos (exceto segredos) mas não pode fazer alterações.

- **Colaborador de Máquina virtual**: Um utilizador com esta função pode gerir máquinas virtuais, mas não consegue gerir a rede virtual à qual estão conectados ou a conta de armazenamento onde reside o ficheiro VHD.

- **SQL DB Contributor**: Um utilizador com esta função pode gerir bases de dados SQL, mas não as suas políticas relacionadas com a segurança.

- **SQL Security Manager**: Um utilizador com esta função pode gerir as políticas relacionadas com a segurança dos servidores e bases de dados SQL.

- **Contribuinte da Conta**de Armazenamento : Um utilizador com esta função pode gerir contas de armazenamento, mas não consegue gerir o acesso às contas de armazenamento.

Para obter mais informações, veja [Gerir o acesso através do RBAC e do portal do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

A Azure Virtual Machines é um dos serviços centrais da IaaS em Azure. A Azure Virtual Machines suporta a implementação de máquinas virtuais Windows ou Linux num centro de dados do Microsoft Azure. Com as Máquinas Virtuais Azure, tem controlo total sobre a configuração VM e é responsável por toda a instalação, configuração e manutenção do software.

Quando estiver a implementar um VM Azure, pode selecionar uma imagem do Azure Marketplace, ou pode fornecer-lhe uma imagem generalizada. Esta imagem é utilizada para aplicar o sistema operativo e a configuração inicial. Durante a implementação, o Gestor de Recursos irá lidar com algumas definições de configuração, tais como atribuir o nome do computador, credenciais administrativas e configuração da rede. Pode utilizar extensões de máquinas virtuais Azure para automatizar novas configurações, tais como instalação de software, configuração antivírus e soluções de monitorização.

Pode criar máquinas virtuais em vários tamanhos diferentes. O tamanho da máquina virtual dita a atribuição de recursos, como processamento, memória e capacidade de armazenamento. Em alguns casos, funcionalidades específicas, tais como adaptadores de rede ativados por RDMA e discos SSD estão disponíveis apenas com certos tamanhos vm. Para obter uma lista completa de tamanhos e capacidades VM, consulte "Sizes for virtual machines in Azure" para [Windows](../../virtual-machines/windows/sizes.md) e [Linux](../../virtual-machines/linux/sizes.md).

### <a name="use-cases"></a>Casos de utilização

Uma vez que as máquinas virtuais Azure oferecem controlo completo sobre a configuração, são ideais para uma ampla gama de cargas de trabalho do servidor que não se encaixam num modelo PaaS. Cargas de trabalho do servidor, tais como servidores de base de dados (SQL Server, Oracle ou MongoDB), Diretório Ativo do Windows Server, Microsoft SharePoint, e muitos mais tornam-se possíveis de executar na plataforma Microsoft Azure. Se desejar, pode mover essas cargas de trabalho de um datacenter no local para uma ou mais regiões Azure, sem uma grande quantidade de reconfiguração.

### <a name="deployment-of-virtual-machines"></a>Implantação de máquinas virtuais

Pode implantar máquinas virtuais Azure utilizando o portal Azure, utilizando a automatização com o módulo Azure PowerShell, ou utilizando a automatização com o CLI de plataforma cruzada.

#### <a name="portal"></a>Portal

A implementação de uma máquina virtual utilizando o portal Azure requer apenas uma subscrição ativa do Azure e acesso a um navegador web. Pode selecionar muitas imagens diferentes do sistema operativo com configurações variadas. Todos os requisitos de armazenamento e rede estão configurados durante a implementação. Para mais informações, consulte "Criar uma máquina virtual no portal Azure" para [Windows](../../virtual-machines/windows/quick-create-portal.md) e [Linux](../../virtual-machines/linux/quick-create-portal.md).

Além de implementar uma máquina virtual a partir do portal Azure, pode implementar um modelo de Gestor de Recursos Azure a partir do portal. Isto irá implantar e configurar todos os recursos conforme definido no modelo. Para mais informações, consulte [Implementar recursos com modelos de Gestor](../../azure-resource-manager/templates/deploy-portal.md)de Recursos e portal Azure .

#### <a name="powershell"></a>PowerShell

A implantação de uma máquina virtual Azure utilizando o PowerShell permite uma automatização completa de todos os recursos de máquinas virtuais relacionadas, incluindo armazenamento e networking. Para mais informações, consulte [Criar um VM windows utilizando o Gestor de Recursos e powerShell](../../virtual-machines/windows/quick-create-powershell.md).

Além de implementar recursos de computação Azure individualmente, pode utilizar o módulo Azure PowerShell para implementar um modelo de Gestor de Recursos Azure. Para mais informações, consulte [A implantação de recursos com modelos de Gestor de Recursos e PowerShell Azure](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Interface de Linha de Comandos (CLI)

Tal como acontece com o módulo PowerShell, a interface da linha de comando Azure fornece automatização de implementação e pode ser utilizada nos sistemas Windows, OS X ou Linux. Quando estiver a utilizar o comando de criação rápida Azure CLI **vM,** todos os recursos de máquinas virtuais relacionadas (incluindo armazenamento e networking) e a própria máquina virtual são implantados. Para mais informações, consulte [Create a Linux VM in Azure utilizando o CLI](../../virtual-machines/linux/quick-create-cli.md).

Da mesma forma, pode utilizar o Azure CLI para implementar um modelo de Gestor de Recursos Azure. Para mais informações, consulte [A implantação de recursos com modelos de Gestor de Recursos e ClI Azure](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Acesso e segurança para máquinas virtuais

O acesso a uma máquina virtual a partir da Internet requer que a interface de rede associada, ou equilibrador de carga, se aplicável, seja configurada com um endereço IP público. O endereço IP público inclui um nome DNS que irá resolver para a máquina virtual ou equilibrador de carga. Para mais informações, consulte [endereços IP no Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).

Gere o acesso à máquina virtual através do endereço IP público utilizando um recurso do grupo de segurança da rede (NSG). Um NSG age como uma firewall e permite ou nega o tráfego através da interface de rede ou subnet em um conjunto de portas definidas. Por exemplo, para criar uma sessão de Desktop Remoto com um VM Azure, é necessário configurar o NSG para permitir o tráfego de entrada na porta 3389. Para mais informações, consulte [Abrir portas para um VM em Azure utilizando o portal Azure](../../virtual-machines/windows/nsg-quickstart-portal.md).

Finalmente, tal como acontece com a gestão de qualquer sistema informático, deverá fornecer segurança a uma máquina virtual Azure no sistema operativo utilizando credenciais de segurança e firewalls de software.

## <a name="azure-storage"></a>Storage do Azure

O Azure Storage é um serviço gerido pela Microsoft que fornece armazenamento durável, escalável e redundante. Pode adicionar uma conta de armazenamento Azure como recurso a qualquer grupo de recursos, utilizando qualquer método de implementação de recursos. O Azure inclui quatro tipos de armazenamento: armazenamento de blob, armazenamento de ficheiros, armazenamento de mesa e armazenamento de fila. Ao implementar uma conta de armazenamento, existem dois tipos de conta disponíveis, armazenamento geral e blob. Uma conta de armazenamento de uso geral dá-lhe acesso aos quatro tipos de armazenamento. As contas de armazenamento blob são semelhantes às contas de uso geral, mas contêm bolhas especializadas que incluem níveis de acesso quente e frio. Para obter mais informações sobre o armazenamento de blob, consulte [o armazenamento de Blob Azure](../../storage/blobs/storage-blob-storage-tiers.md).

As contas de armazenamento azure podem ser configuradas com diferentes níveis de redundância:

- **O armazenamento localmente redundante** proporciona uma elevada disponibilidade, garantindo que três cópias de todos os dados são feitas sincronizadamente antes de uma escrita ser considerada bem sucedida. Estas cópias são armazenadas numa única instalação numa única região. As réplicas residem em domínios de falha separados e em domínios de atualização. Isto significa que os dados estão disponíveis mesmo que um nó de armazenamento que esteja a segurar os seus dados falhe ou seja desligado para ser atualizado.

- **O armazenamento geo-redundante** faz três cópias sincronizadas dos dados na região primária para alta disponibilidade, e depois faz assíncronamente três réplicas numa região emparelhada para recuperação de desastres.

- **O armazenamento geo-redundante de acesso de leitura** é um armazenamento geo-redundante mais a capacidade de ler os dados na região secundária. Esta capacidade torna-a adequada para uma recuperação parcial de desastres. Se houver algum problema com a região primária, pode alterar a sua aplicação para ter acesso apenas à região emparelhada.

### <a name="use-cases"></a>Casos de utilização

Cada tipo de armazenamento tem uma caixa de uso diferente.

#### <a name="blob-storage"></a>Armazenamento de blobs

A palavra *blob* é um acrónimo para *objeto grande binário.* As bolhas são ficheiros não estruturados como os que armazena no computador. O Blob Storage pode armazenar qualquer tipo de texto ou de dados binários, tal como um documento, um ficheiro de multimédia ou um instalador da aplicação. O Blob Storage também é referido como um armazenamento de objetos. O armazenamento azure Blob também contém discos de dados da Azure Virtual Machines.

O Armazenamento do Azure suporta três tipos de blobs:

- **As bolhas** de bloco são usadas para conter ficheiros ordinários até 195 GB de tamanho (4 MB × 50.000 blocos). A utilização principal para blobs de blocos é o armazenamento de ficheiros que são lidos do início ao fim, como ficheiros multimédia ou ficheiros de imagem para sites. São nomeadas bolhas de bloco porque ficheiros com mais de 64 MB devem ser carregados como blocos pequenos. Estes blocos são então consolidados (ou comprometidos) na bolha final.

- **As bolhas** de página são usadas para manter ficheiros de acesso aleatório até 1 TB em tamanho. As bolhas de página são usadas principalmente como o armazenamento de apoio para os VHDs que fornecem discos duráveis para máquinas virtuais Azure, o serviço de computação IaaS em Azure. São denominados blobs de páginas porque dão acesso aleatório de leitura/escrita a páginas de 512 bytes.

- **As bolhas** de apêndice consistem em blocos como blocos de blocos, mas são otimizados para operações de apêndice. Estes são frequentemente utilizados para registar informações de uma ou mais fontes para a mesma bolha. Por exemplo, pode escrever todos os seus vestígios a fazer login na mesma bolha de apêndice para uma aplicação que está a funcionar em vários VMs. Um único blob de acréscimo pode ter até 195 GB.

Para mais informações, consulte [Começar com o armazenamento Azure Blob utilizando .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="file-storage"></a>Armazenamento de ficheiros

O armazenamento de ficheiros Azure é um serviço que oferece partilhas de ficheiros na nuvem utilizando o protocolo padrão do Bloco de Mensagens do Servidor (SMB). O serviço suporta tanto smb 2.1 como SMB 3.0. Com o armazenamento do Ficheiro Azure, pode migrar aplicações que dependem de ações de ficheiros para o Azure de forma rápida e sem reescritas dispendiosas. As aplicações em execução em máquinas virtuais Azure, em serviços na nuvem ou a partir de clientes no local podem montar uma partilha de ficheiros na nuvem. Isto é semelhante ao modo como uma aplicação de ambiente de trabalho monta uma quota típica de SMB. Em seguida, quaisquer componentes da aplicação podem montar e aceder à partilha do Armazenamento de ficheiros em simultâneo.

Como uma partilha de armazenamento de ficheiros é uma partilha padrão de ficheiros SMB, as aplicações em execução no Azure podem aceder a dados na partilha através do sistema de ficheiros I/O APIs. Os desenvolvedores podem, portanto, utilizar o seu código e competências existentes para migrar as aplicações existentes. Os profissionais de TI podem usar cmdlets PowerShell para criar, montar e gerir ações de armazenamento de ficheiros como parte da administração de aplicações Azure.

Para mais informações, consulte [Start start with Azure File storage on Windows](../../storage/files/storage-how-to-use-files-windows.md) or How to use [Azure File storage with Linux](../../storage/files/storage-how-to-use-files-linux.md).

#### <a name="table-storage"></a>Table Storage

O Table Storage do Azure é um serviço que armazena dados NoSQL estruturados na nuvem. O armazenamento de mesa é uma loja chave/atributo com um design sem esquemas. Como o armazenamento de mesa é sem esquema, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. O acesso aos dados é rápido e rentável para todos os tipos de aplicações. Geralmente, o custo do Table Storage é significativamente inferior ao SQL tradicional para volumes de dados semelhantes.

Pode utilizar o Table Storage para armazenar conjuntos de dados flexíveis, tais como os dados do utilizador para Web Apps, livros de endereços, informações do dispositivo e qualquer outro tipo de metadados necessários para o seu serviço. Pode armazenar qualquer número de entidades numa mesa. Uma conta de armazenamento pode conter qualquer número de tabelas, até ao limite de capacidade da conta de armazenamento.

Para mais informações, consulte [Começar com o armazenamento da Mesa Azure.](../../cosmos-db/table-storage-how-to-use-dotnet.md)

#### <a name="queue-storage"></a>Armazenamento de filas

O armazenamento de Filas do Azure fornece um serviço de mensagens na nuvem entre componentes da aplicação. Ao conceber aplicações para escala, os componentes da aplicação são muitas vezes dissociados para que possam escalar de forma independente. O Armazenamento de filas fornece um serviço de mensagens assíncrono para uma comunicação entre os componentes da aplicação, quer estejam a ser executados na nuvem, no ambiente de trabalho, num servidor no local ou num dispositivo móvel. O Armazenamento de filas também suporta a gestão das tarefas assíncronas e a criação de fluxos de trabalho do processo.

Para mais informações, consulte Começar com o armazenamento da [Fila Azure.](../../storage/queues/storage-dotnet-how-to-use-queues.md)

### <a name="deploying-a-storage-account"></a>Implementação de uma conta de armazenamento

Existem várias opções para implementar uma conta de armazenamento.

#### <a name="portal"></a>Portal

A implementação de uma conta de armazenamento utilizando o portal Azure requer apenas uma subscrição ativa do Azure e acesso a um navegador web. Pode implementar uma nova conta de armazenamento num novo ou existente grupo de recursos. Depois de criar a conta de armazenamento, pode criar um recipiente de blob ou partilha de ficheiros utilizando o portal. Pode criar entidades de armazenamento de mesa e fila programáticas. Para obter mais informações, veja [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md).

Além de implementar uma conta de armazenamento a partir do portal Azure, pode implementar um modelo de Gestor de Recursos Azure a partir do portal. Isto irá implantar e configurar todos os recursos conforme definido no modelo, incluindo quaisquer contas de armazenamento. Para mais informações, consulte [Implementar recursos com modelos de Gestor](../../azure-resource-manager/templates/deploy-portal.md)de Recursos e portal Azure .

#### <a name="powershell"></a>PowerShell

A implementação de uma conta de armazenamento Azure utilizando o PowerShell permite uma automatização completa da distribuição da conta de armazenamento. Para mais informações, consulte [A Utilização de PowerShell Azure com armazenamento Azure](../../storage/common/storage-powershell-guide-full.md).

Além de implementar recursos Azure individualmente, pode utilizar o módulo Azure PowerShell para implementar um modelo de Gestor de Recursos Azure. Para mais informações, consulte [A implantação de recursos com modelos de Gestor de Recursos e PowerShell Azure](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Interface de Linha de Comandos (CLI)

Tal como acontece com o módulo PowerShell, a Interface da linha de comando Azure fornece automatização de implementação e pode ser utilizada nos sistemas Windows, OS X ou Linux. Pode utilizar a conta de armazenamento Azure CLI **criar** comando para criar uma conta de armazenamento. Para mais informações, consulte [Utilizar o Azure CLI com armazenamento Azure.](../../storage/common/storage-azure-cli.md)

Da mesma forma, pode utilizar o Azure CLI para implementar um modelo de Gestor de Recursos Azure. Para mais informações, consulte [A implantação de recursos com modelos de Gestor de Recursos e ClI Azure](../../resource-group-template-deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Acesso e segurança para armazenamento azure

O Azure Storage é acessado de várias formas, incluindo o portal Azure, durante a criação e operação VM, e a partir de bibliotecas de clientes de Armazenamento.

#### <a name="virtual-machine-disks"></a>Discos de máquinas virtuais

Ao implementar uma máquina virtual, também precisa de criar uma conta de armazenamento para manter o disco do sistema operativo virtual da máquina e quaisquer discos de dados adicionais. Pode selecionar uma conta de armazenamento existente ou criar uma nova. Como o tamanho máximo de uma bolha é de 1.024 GB, um único disco VM tem um tamanho máximo de 1.023 GB. Para configurar um disco de dados maior, pode apresentar vários discos de dados à máquina virtual e reuni-los como um único disco lógico. Para mais informações, consulte "Manage Azure disks" para [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) e [Linux](../../virtual-machines/linux/tutorial-manage-disks.md).

#### <a name="storage-tools"></a>Ferramentas de armazenamento

As contas de armazenamento azure podem ser acedidas através de muitos exploradores de armazenamento diferentes, como visual Studio Cloud Explorer. Estas ferramentas permitem-lhe navegar através de contas de armazenamento e dados. Para mais informações e uma lista de exploradores de armazenamento disponíveis, consulte [as ferramentas do cliente do Azure Storage.](../../storage/common/storage-explorers.md)

#### <a name="storage-api"></a>API de armazenamento

Os recursos de armazenamento podem ser acedidos por qualquer idioma que possa fazer pedidos HTTP/HTTPS. Além disso, o Storage do Azure oferece bibliotecas de programação para vários idiomas populares. Estas bibliotecas simplificam o trabalho com o Armazenamento Azure, manuseando detalhes como a invocação sincronizada e assíncrona, o lote de operações, a gestão de exceções e as repetições automáticas. Para mais informações, consulte o serviço de [armazenamento Azure REFERÊNCIA REST API](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Chaves de acesso ao armazenamento

Cada conta de armazenamento tem duas chaves de autenticação, uma primária e uma secundária. Qualquer um pode ser usado para operações de acesso ao armazenamento. Estas chaves de armazenamento são usadas para ajudar a garantir uma conta de armazenamento e são necessárias para aceder programáticamente aos dados. Há duas chaves para permitir o capotamento ocasional das chaves para aumentar a segurança. É fundamental manter as chaves seguras porque a sua posse, juntamente com o nome da conta, permite o acesso ilimitado a quaisquer dados na conta de armazenamento.

#### <a name="shared-access-signatures"></a>Assinaturas de acesso partilhado

Se precisar de permitir que os utilizadores tenham acesso controlado aos seus recursos de armazenamento, pode criar uma assinatura de acesso partilhado. Uma assinatura de acesso partilhado é um símbolo que pode ser anexado a um URL que permite o acesso delegado a um recurso de armazenamento. Qualquer pessoa que possua o símbolo pode aceder ao recurso que aponta com as permissões que especifica, pelo período de tempo que é válido. Para mais informações, consulte [A utilização de assinaturas de acesso partilhado](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="azure-virtual-network"></a>Rede Virtual do Azure

As redes virtuais são necessárias para apoiar as comunicações entre máquinas virtuais. Pode definir subredes, endereço IP personalizado, definições de DNS, filtragem de segurança e equilíbrio de carga. O Azure suporta diferentes casos de utilizações: redes apenas em nuvem ou redes virtuais híbridas.

### <a name="cloud-only-virtual-networks"></a>Redes virtuais só para nuvem

Uma rede virtual Azure, por padrão, é acessível apenas aos recursos armazenados no Azure. Os recursos ligados à mesma rede virtual podem comunicar entre si. Pode associar interfaces de rede de máquinas virtuais e equilibradores de carga com um endereço IP público para tornar a máquina virtual acessível através da Internet. Pode ajudar a garantir o acesso aos recursos expostos publicamente utilizando um grupo de segurança de rede.

![Rede Virtual Azure para uma aplicação web de 2 níveis](https://docs.microsoft.com/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Redes virtuais híbridas

Pode ligar uma rede no local a uma rede virtual Azure utilizando o ExpressRoute ou uma ligação VPN site-to-site. Nesta configuração, a rede virtual Azure é essencialmente uma extensão baseada na nuvem da sua rede no local.

Como a rede virtual Azure está ligada à sua rede no local, as redes virtuais cross-premises devem usar uma parte única do espaço de endereço que a sua organização utiliza. Da mesma forma que diferentes localizações corporativas são atribuídas a uma subnet IP específica, o Azure torna-se outro local à medida que estende a sua rede.
Existem várias opções para implementar uma rede virtual.

- [Portal](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Interface de Linha de Comandos (CLI)](../../virtual-network/quick-create-cli.md)

- Modelos de gestor de recursos azure

> **Quando utilizar**: Sempre que estiver a trabalhar com VMs em Azure, trabalhará com redes virtuais. Isto permite segmentar os seus VMs em subredes viradas para o público e para as redes privadas similares no local.
>
> **Começar**: Implementar uma rede virtual Azure utilizando o portal Azure requer apenas uma subscrição ativa do Azure e acesso a um navegador web. Pode implantar uma nova rede virtual num novo ou existente grupo de recursos. Quando estiver a criar uma nova máquina virtual a partir do portal, pode selecionar uma rede virtual existente ou criar uma nova. Inicie e [Crie uma rede virtual utilizando o portal Azure.](../../virtual-network/quick-create-portal.md)

### <a name="access-and-security-for-virtual-networks"></a>Acesso e segurança para redes virtuais

Pode ajudar a proteger redes virtuais Azure utilizando um grupo de segurança de rede. Os NSGs contêm uma lista de regras da lista de controlo de acesso (ACL) que permitem ou negam o tráfego de rede às instâncias vM numa rede virtual. Pode associar NSGs a subredes ou a instâncias vm individuais dentro dessa sub-rede. Quando associa um NSG a uma sub-rede, as regras da ACL aplicam-se a todos os casos de VM nessa sub-rede. Além disso, pode restringir ainda mais o tráfego a um VM individual associando um NSG diretamente com esse VM. Para obter mais informações, veja [Filtrar o tráfego de rede com grupos de segurança de rede](../../virtual-network/security-overview.md).

## <a name="next-steps"></a>Passos seguintes

- [Criar uma VM do Windows](../../virtual-machines/windows/quick-create-portal.md)
- [Criar uma VM do Linux](../../virtual-machines/linux/quick-create-portal.md)
