---
title: Azure Lab Services - Guia de Administradores / Microsoft Docs
description: Este guia ajuda administradores que criam e gerem contas de laboratório usando os Serviços Azure Lab.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 8670a9d56575dbfb6d3e565ec97191581dc612a8
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491040"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - Guia de Administradores
Os administradores de Tecnologias de Informação (TI) que gerem os recursos em nuvem de uma universidade são normalmente responsáveis pela criação da conta de laboratório para a sua escola. Uma vez criada uma conta de laboratório, administradores ou educadores criam laboratórios que estão contidos na conta do laboratório. Este artigo fornece uma visão geral de alto nível dos recursos Azure envolvidos e as orientações para a sua criação.

![Vista de alto nível dos recursos do Azure numa conta de laboratório](./media/administrator-guide/high-level-view.png)

- Os laboratórios estão hospedados numa subscrição da Azure propriedade da Azure Lab Services.
- Contas de laboratório, galeria de imagens partilhadas e versões de imagem estão hospedadas dentro da sua subscrição.
- Pode ter a sua conta de laboratório e a galeria de imagens partilhada no mesmo grupo de recursos. Neste diagrama, estão em diferentes grupos de recursos.

Para mais detalhes sobre a arquitetura, leia o artigo: [Fundamentos da arquitetura labs](https://docs.microsoft.com/azure/lab-services/classroom-labs-fundamentals)

## <a name="subscription"></a>Subscrição
A sua universidade tem uma ou mais assinaturas Azure. Uma subscrição é usada para gerir faturação e segurança para todos os recursos Azure\serviços que são usados dentro dela, incluindo contas de laboratório.

A relação entre uma conta de laboratório e a sua subscrição é importante porque:

- A faturação é reportada através da subscrição que contém a conta do laboratório.
- Pode dar aos utilizadores do Azure Ative Directory (AD) acesso ao Azure Lab Services. Pode adicionar um utilizador como proprietário de uma conta de laboratório\colaborador, criador de laboratório ou dono de laboratório.

Os laboratórios e as suas máquinas virtuais (VMs) são geridos e hospedados para si dentro de uma subscrição propriedade da Azure Lab Services.

## <a name="resource-group"></a>Grupo de recursos
Uma subscrição contém um ou mais grupos de recursos. Os grupos de recursos são usados para criar agrupamentos lógicos de recursos Azure que são usados juntos dentro da mesma solução.  

Quando se cria uma conta de laboratório, tem de configurar o grupo de recursos que contém a conta de laboratório. 

Um grupo de recursos também é necessário ao criar uma [galeria de imagens partilhada.](#shared-image-gallery) Pode optar por colocar a sua conta de laboratório e partilhar a galeria de imagens em dois grupos de recursos separados, o que é típico se pretender partilhar a galeria de imagens em diferentes soluções. Ou, pode optar por colocá-los no mesmo grupo de recursos.

Quando cria uma conta de laboratório, pode criar e anexar automaticamente uma galeria de imagens partilhada ao mesmo tempo.  Esta opção resulta na conta de laboratório e na galeria de imagens partilhada a ser criada em grupos de recursos separados. Você verá este comportamento ao usar os passos descritos neste tutorial: [Configurar galeria de imagens partilhada no momento da criação de conta de laboratório](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A imagem no topo deste artigo também utiliza esta configuração. 

Recomendamos investir tempo adiantado para planear a estrutura dos seus grupos de recursos, uma vez que *não* é possível alterar o grupo de recursos de uma conta de laboratório ou de uma galeria de imagens partilhada uma vez que é criado. Se precisar de alterar o grupo de recursos para estes recursos, terá de apagar e recriar a sua conta de laboratório e a galeria de imagens partilhada.

## <a name="lab-account"></a>Conta de laboratório

Uma conta de laboratório serve como recipiente para um ou mais laboratórios. Quando se começa com os Serviços Azure Lab, é comum ter apenas uma conta de laboratório. À medida que o seu laboratório escala, poderá mais tarde optar por criar mais contas de laboratório.

A seguinte lista destaca cenários em que mais de uma conta de laboratório pode ser benéfica:

- **Gerir diferentes requisitos de política em laboratórios**

    Quando crias uma conta de laboratório, defines políticas que se aplicam a *todos os* laboratórios da conta de laboratório, tais como:
    - A rede virtual Azure com recursos partilhados a que o laboratório pode aceder. Por exemplo, você pode ter um conjunto de laboratórios que precisam de acesso a um conjunto de dados compartilhados dentro de uma rede virtual.
    - As imagens da máquina virtual (VM) que os laboratórios podem usar para criar VMs. Por exemplo, você pode ter um conjunto de laboratórios que precisam de acesso ao [VM data science para](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) imagem Linux Marketplace.

    Se tiver laboratórios que tenham requisitos políticos únicos uns dos outros, pode ser benéfico criar contas de laboratório separadas para gerir estes laboratórios separadamente.

- **Orçamento separado por conta de laboratório**
  
    Em vez de reportar todos os custos do laboratório através de uma única conta de laboratório, pode precisar de um orçamento mais claramente separado. Por exemplo, pode criar contas de laboratório para o departamento de Matemática da sua universidade, departamento de Ciências da Computação, etc., para separar o orçamento entre departamentos.  Em seguida, pode ver o custo de cada conta de laboratório usando [a Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Isolar laboratórios piloto de laboratórios de produção ativa**
  
    Pode ter casos em que pretende pilotar mudanças de política para uma conta de laboratório sem potencial impacto nos laboratórios de produção ativa. Neste tipo de cenário, a criação de uma conta de laboratório separada para fins de pilotagem permite isolar alterações. 

## <a name="lab"></a>Laboratório

Um laboratório contém máquinas virtuais (VMs) que são atribuídas a um único aluno.  Em geral, pode esperar:

- Tenha um laboratório para cada aula.
- Crie um novo conjunto de laboratórios a cada semestre (ou para cada período de tempo que a sua aula é oferecida). Normalmente para aulas que tenham as mesmas necessidades de imagem, você deve usar uma [galeria de imagens partilhada](#shared-image-gallery) para reutilizar imagens em laboratórios e semestres.

Considere os seguintes pontos ao determinar como estruturar os seus laboratórios:

- **Todos os VMs dentro de um laboratório são implantados com a mesma imagem que é publicada**

    Como resultado, se tiver uma classe que exija que diferentes imagens de laboratório sejam publicadas ao mesmo tempo, devem ser criados laboratórios separados para cada um deles.
  
- **A quota de utilização é definida ao nível do laboratório e aplica-se a todos os utilizadores dentro do laboratório**

    Para definir diferentes quotas para os utilizadores, deve criar laboratórios separados. No entanto, é possível adicionar mais horas a um utilizador específico depois de ter definido a quota.
  
- **O calendário de arranque ou paragem é definido ao nível do laboratório e aplica-se a todos os VMs dentro do laboratório**

    À semelhança do ponto anterior, se precisar de definir horários diferentes para os utilizadores, precisa de criar laboratórios separados.

Por padrão, cada laboratório terá a sua própria rede virtual.  Se tiver o espremiado vnet ativado, cada laboratório terá a sua própria sub-rede espreitada para a rede virtual especificada.

## <a name="shared-image-gallery"></a>Galeria de imagens partilhada

Uma galeria de imagens partilhada é anexada a uma conta de laboratório e serve como um repositório central para armazenar imagens. Uma imagem é guardada na galeria quando um educador escolhe exportar a partir da máquina virtual de modelo de um laboratório (VM). Cada vez que um educador faz alterações no modelo VM e exporta, novas versões da imagem são guardadas mantendo as versões anteriores.

Os instrutores podem publicar uma versão de imagem da galeria de imagens partilhada quando criarem um novo laboratório. Embora a galeria armazena várias versões de uma imagem, os educadores só podem selecionar a versão mais recente durante a criação do laboratório.

A galeria de imagens partilhada é um recurso opcional que pode não precisar imediatamente quando começar com apenas alguns laboratórios. No entanto, a utilização de uma galeria de imagens partilhada tem muitos benefícios que são úteis à medida que escala para ter mais laboratórios:

- **Permite-lhe guardar e gerir versões de uma imagem VM modelo**

    É útil criar uma imagem personalizada ou fazer alterações (software, configuração, e assim por diante) para uma imagem da galeria do Mercado público.  Por exemplo, é comum os educadores exigirem a instalação de software diferentes. Em vez de exigir que os alunos instalem manualmente estes pré-requisitos por si só, diferentes versões da imagem VM do modelo podem ser exportadas para uma galeria de imagens partilhada. Estas versões de imagem podem então ser usadas na criação de novos laboratórios.
- **Permite a partilha\reutilização de imagens VM do modelo em laboratórios**

    Pode guardar e reutilizar uma imagem para que não tenha de configurar a imagem do zero de cada vez que cria um novo laboratório. Por exemplo, se várias classes estão sendo oferecidas que precisam da mesma imagem, esta imagem só precisa ser criada uma vez e exportada para a galeria de imagens partilhada para que possa ser partilhada em laboratórios.
- **Garante a disponibilidade de imagem através da replicação**

    Quando guarda para a galeria de imagens partilhada de um laboratório, a sua imagem é automaticamente replicada para [outras regiões dentro da mesma geografia.](https://azure.microsoft.com/global-infrastructure/regions/) No caso de haver uma paragem para uma região, a publicação da imagem no seu laboratório não é afetada, uma vez que uma réplica de imagem de outra região pode ser usada.  A publicação de VMs a partir de várias réplicas também pode ajudar no desempenho.

Para grupos logicamente imagens partilhadas, você tem algumas opções:

- Crie várias galerias de imagens partilhadas. Cada conta de laboratório só pode ligar-se a uma galeria de imagens partilhada, por isso esta opção também requer que crie várias contas de laboratório.
- Ou pode usar uma única galeria de imagens partilhada que é partilhada por várias contas de laboratório. Neste caso, cada conta de laboratório só pode permitir as imagens que são aplicáveis aos laboratórios que contém.

## <a name="naming"></a>Atribuição de nomes

À medida que começa com os Serviços Azure Lab, recomendamos que estabeleça convenções de nomeação para grupos de recursos, contas de laboratório, laboratórios e galeria de imagens partilhadas. Embora as convenções de nomeação que estabelece sejam únicas às necessidades da sua organização, a tabela que se segue descreve as diretrizes gerais.

| Tipo de recurso | Função | Padrão sugerido | Exemplos |
| ------------- | ---- | ----------------- | -------- | 
| Grupo de recursos | Contém uma ou mais contas de laboratório e uma ou mais galerias de imagens partilhadas | \<organization short name\>-\<environment\>-rg<ul><li>**Nome curto da organização** identifica o nome da organização que o grupo de recursos apoia</li><li>**O ambiente** identifica o ambiente para o recurso, como o Piloto ou a Produção</li><li>**Rg** significa o tipo de recurso: grupo de recursos.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Conta de laboratório | Contém um ou mais laboratórios | \<organization short name\>-\<environment\>-la<ul><li>**Nome curto da organização** identifica o nome da organização que o grupo de recursos apoia</li><li>**O ambiente** identifica o ambiente para o recurso, como o Piloto ou a Produção</li><li>**La** significa o tipo de recurso: conta de laboratório.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Laboratório | Contém um ou mais VMs |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**O nome** da classe identifica o nome da classe que o laboratório suporta.</li><li>**O prazo** identifica o prazo em que a classe é oferecida.</li>**O identificador de educação** identifica o educador que é dono do laboratório.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-primavera2019-johndoe |
| Galeria de imagens partilhada | Contém uma ou mais versões de imagem VM | \<organization short name\>galeria | galeria contosouniversitylabsgallery |

Para obter mais informações sobre o nome de outros recursos da Azure, consulte [as convenções de nomeação para recursos Azure.](/azure/architecture/best-practices/naming-conventions)

## <a name="regionslocations"></a>Regiões\localizações

Ao configurar os recursos dos seus Serviços Azure Lab, é-lhe exigido que forneça uma região (ou localização) do centro de dados que irá acolher o recurso. Aqui estão mais detalhes sobre como a região impacta cada um dos recursos envolvidos na criação de um laboratório.

### <a name="resource-group"></a>Grupo de recursos

A região especifica o centro de dados onde a informação sobre o grupo de recursos é armazenada. Os recursos azuis contidos no grupo de recursos podem estar em diferentes regiões do seu progenitor.

### <a name="lab-account"></a>Conta de laboratório

A localização de uma conta de laboratório indica a região onde este recurso existe.  

### <a name="lab"></a>Laboratório

A localização em que um laboratório existe varia com base nos seguintes fatores:

  - **A conta de laboratório é espremiada para uma rede virtual (VNet)**
  
    Uma conta de laboratório pode ser [espreitada com um VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) quando estão na mesma região.  Quando uma conta de laboratório é espremiada com um VNet, os laboratórios são automaticamente criados na mesma região que a conta de laboratório e vNet.

    > [!NOTE]
    > Quando uma conta de laboratório é espremiada com um VNet, a definição para permitir que o **criador do laboratório escolha a localização do laboratório** é desativada. Informações adicionais podem ser encontradas sobre esta configuração no artigo: Permitir que o [criador do laboratório escolha a localização para o laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Nenhum VNet é espreitado *_* e_* _ criadores de laboratório não estão autorizados a escolher o laboratório location_*
  
    Quando **não** há VNet espreitado com a conta de laboratório *e* [os criadores de laboratório **não** estão autorizados a escolher a localização do laboratório, os](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)laboratórios são automaticamente criados numa região que tem capacidade VM disponível.  Especificamente, a Azure Lab Services procura disponibilidade em [regiões que estão dentro da mesma geografia que a conta de laboratório.](https://azure.microsoft.com/global-infrastructure/regions)

  - **Nenhum VNet é espreitado * *_e_* _ criadores de laboratório são autorizados a escolher o laboratório location_*
       
    Quando **não** há VNet e os [criadores de laboratório são autorizados a escolher a localização do laboratório,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)os locais que podem ser selecionados pelo criador do laboratório baseiam-se na capacidade disponível.

> [!NOTE]
> Para ajudar a garantir que existe capacidade de VM suficiente para uma região, é importante que solicite primeiro a capacidade através da conta de laboratório ou ao criar o laboratório.

Uma regra geral é definir a região de um recurso para uma região mais próxima dos seus utilizadores. Para os laboratórios, isto significa criar o laboratório mais próximo dos seus alunos. Para cursos online onde os alunos estão localizados em todo o mundo, você precisa usar o seu melhor julgamento para criar um laboratório que esteja localizado centralmente. Ou dividir uma aula em vários laboratórios baseados na região dos seus alunos.

### <a name="shared-image-gallery"></a>Galeria de imagens partilhada

A região indica a região de origem onde a primeira versão de imagem é armazenada antes de ser automaticamente replicada para regiões-alvo.

## <a name="vm-sizing"></a>Tamanho de VM

Quando administradores ou criadores de laboratório criam um laboratório, podem escolher entre os seguintes tamanhos de VM baseados nas necessidades da sua sala de aula. Lembre-se que os tamanhos de cálculo disponíveis dependem da região em que a sua conta de laboratório está localizada:

| Tamanho | Especificações | Série | Uso sugerido |
| ---- | ----- | ------ | ------------- |
| Pequeno| <ul><li>2 núcleos</li><li>3,5 GB DE RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para linha de comando, abertura de navegador, servidores web de baixo tráfego, bases de dados pequenas a médias. |
| Médio | <ul><li>4 Núcleos</li><li>7 GB DE RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para bases de dados relacionais, caching na memória e análise. |
| Médio (virtualização aninhada) | <ul><li>4 Núcleos</li><li>16 GB DE RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Este tamanho é mais adequado para bases de dados relacionais, caching na memória e análise.
| Grande | <ul><li>8 núcleos</li><li>16 GB DE RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápidos, melhor desempenho do disco local, grandes bases de dados, caches de memória grandes.  Este tamanho também suporta a virtualização aninhada. |
| Grande (Virtualização Aninhada) | <ul><li>8 núcleos</li><li>32 GB de RAM</li></ul>  | [Standard_D8s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápidos, melhor desempenho do disco local, grandes bases de dados, caches de memória grandes. |
| Pequeno GPU (Visualização) | <ul><li>6 Núcleos</li><li>56 GB DE RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando quadros como OpenGL e DirectX. |
| Pequena GPU (Computação) | <ul><li>6 Núcleos</li><li>56 GB DE RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Este tamanho é mais adequado para aplicações intensivas em computador, como Inteligência Artificial e Deep Learning. |
| GPU médio (Visualização) | <ul><li>12 Núcleos</li><li>112 GB DE RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando quadros como OpenGL e DirectX. |

## <a name="manage-identity"></a>Gerir identidade

Utilizando [o controlo de acesso baseado em funções Azure (Azure RBAC),](https://docs.microsoft.com/azure/role-based-access-control/overview)as seguintes funções podem ser atribuídas para dar acesso a contas de laboratório e laboratórios:

- **Proprietário da conta de laboratório**

    O administrador que cria a conta de laboratório é automaticamente adicionado à função **de Proprietário** da conta de laboratório.  Um administrador que tenha atribuído a função **de Proprietário** pode:
     - Mude as definições da conta do laboratório.
     - Dar a outros administradores acesso à conta do laboratório como proprietários ou contribuintes.
     - Dar aos educadores acesso a laboratórios como criadores, proprietários ou contribuintes.
     - Crie e gere todos os laboratórios na conta do laboratório.

- **Colaborador de conta de laboratório**

    Um administrador que tenha atribuído a função **de Contribuinte** pode:
    - Mude as definições da conta do laboratório.
    - Criar e gerir todos os laboratórios dentro da conta do laboratório.

    No entanto, *não podem* dar a outros utilizadores acesso a contas de laboratório ou laboratórios.

- **Criador do laboratório**

    Para criar laboratórios dentro de uma conta de laboratório, um educador deve ser um membro do papel de Criador de **Laboratório.**  Quando um educador cria um laboratório, são automaticamente adicionados como donos do laboratório.  Consulte o tutorial sobre como [adicionar um utilizador à função De Criador de **Laboratório**](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Proprietário do laboratório\colaborador**
  
    Um educador pode ver e alterar as definições de um laboratório quando é membro do **papel de Proprietário** ou **Colaborador** de um laboratório; devem também ser membros do papel de **Leitor** da conta de laboratório.

    Uma diferença fundamental entre as funções **de Proprietário** de laboratório e **Contribuinte** é que um contribuinte *não pode* dar a outros utilizadores acesso para gerir o laboratório - só os proprietários podem dar a outros utilizadores acesso para gerir o laboratório.

    Além disso, um educador *não pode* criar novos laboratórios a menos que seja também um membro do papel de Criador de **Laboratório.**

- **Galeria de imagens partilhada**

    Quando anexa uma galeria de imagens partilhada a uma conta de laboratório, os proprietários de contas de laboratório\colaboradores e criadores de laboratório\os colaboradores têm automaticamente acesso à visualização e guardar imagens na galeria.

Aqui ficam algumas dicas para ajudar na atribuição de funções:
   - Normalmente, apenas os administradores devem ser membros das funções **de Proprietário** ou **Contribuinte** de uma conta de laboratório; pode ter mais do que um proprietário\colaborador.
   - Dar a um educador a capacidade de criar novos laboratórios e gerir os laboratórios que criam; basta atribuir acesso ao papel de Criador de **Laboratório.**
   - Dar a um educador a capacidade de gerir laboratórios específicos, mas *não* a capacidade de criar novos laboratórios; deve atribuir acesso à função **Proprietário** ou **Contribuinte** para cada um dos laboratórios que eles irão gerir.  Por exemplo, talvez queira permitir que um professor e um assistente de ensino seja co-dono de um laboratório.  Consulte o guia sobre como [adicionar um utilizador como proprietário a um laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Preços

### <a name="azure-lab-services"></a>Azure Lab Services

O preço dos Serviços Azure Lab é descrito no seguinte artigo: [Azure Lab Services Priceing](https://azure.microsoft.com/pricing/details/lab-services/).

Também precisa de considerar o preço da galeria de imagens partilhada se planeia usá-lo para armazenar e gerir versões de imagem. 

### <a name="shared-image-gallery"></a>Galeria de imagens partilhada

Criar uma galeria de imagens partilhada e anexá-la à sua conta de laboratório é gratuita. Os custos não são incorridos até guardar uma versão de imagem para a galeria. Tipicamente, o preço para usar uma galeria de imagens partilhada é bastante insignificante, mas é importante entender como é calculado uma vez que não está incluído no preço dos Serviços Azure Lab.  

#### <a name="storage-charges"></a>Encargos de armazenamento

Para armazenar versões de imagem, uma galeria de imagens partilhada utiliza discos geridos por HDD padrão. O tamanho do disco gerido por HDD que é usado depende do tamanho da versão de imagem armazenada. Consulte o seguinte artigo para ver os preços: [Preços dos discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Taxas de replicação e de saída de rede

Quando guarda uma versão de imagem utilizando a máquina virtual de modelo de um laboratório (VM), a Azure Lab Services armazena-a primeiro numa região de origem e depois replica automaticamente a versão de imagem de origem para uma ou mais regiões-alvo. É importante notar que a Azure Lab Services replica automaticamente a versão de imagem de origem para todas as [regiões-alvo dentro da geografia](https://azure.microsoft.com/global-infrastructure/regions/) onde o laboratório está localizado. Por exemplo, se o seu laboratório estiver na geografia dos EUA, uma versão de imagem é replicada em cada uma das oito regiões que existem dentro dos EUA.

Uma carga de saída de rede ocorre quando uma versão de imagem é replicada da região de origem para regiões-alvo adicionais. O montante cobrado baseia-se no tamanho da versão de imagem quando os dados da imagem são inicialmente transferidos para fora da região de origem.  Para obter detalhes sobre os preços, consulte o seguinte artigo: [Detalhes sobre preços de largura de](https://azure.microsoft.com/pricing/details/bandwidth/)banda .

[Soluções de educação](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) Os clientes podem ser dispensados do pagamento de taxas de saída. Fale com o seu gestor de conta para saber mais.  Para mais informações, consulte consulte a secção **faq** no documento vinculado, especificamente a questão "Que programas de transferência de dados existem para clientes académicos e como me qualifico?".

#### <a name="pricing-example"></a>Exemplo de preços

Para recapitular os preços acima descritos, vejamos um exemplo de salvar a imagem VM do nosso modelo para a galeria de imagens partilhada. Assuma os seguintes cenários:

- Tem uma imagem VM personalizada.
- Estás a guardar duas versões da imagem.
- O seu laboratório está nos E.U.A., que tem um total de oito regiões.
- Cada versão de imagem tem 32 GB de tamanho; como resultado, o preço do disco gerido por HDD é de $1,54 por mês.

O custo total é estimado como:

Número de imagens × número de versões × número de réplicas × preço do disco gerido

Neste exemplo, o custo é:

1 imagem personalizada (32 GB) x 2 versões x 8 regiões dos EUA x $1,54 = $24,64 por mês

#### <a name="cost-management"></a>Gestão de custos

É importante que o administrador de contas de laboratório gere os custos eliminando rotineiramente versões de imagem não importadas da galeria. 

Não deve eliminar a replicação em regiões específicas como forma de reduzir os custos (esta opção existe na galeria de imagens partilhada). As alterações de replicação podem ter efeitos adversos na capacidade do Azure Lab Service de publicar VMs a partir de imagens guardadas dentro de uma galeria de imagens partilhada.

## <a name="next-steps"></a>Passos seguintes

Próximos passos comuns para criar um ambiente de laboratório.

- [Guia de configuração de conta de laboratório](account-setup-guide.md)
- [Guia de configuração do laboratório](setup-guide.md)
- [Gestão de custos para laboratórios](cost-management-guide.md)
- [Utilização de serviços de laboratório Azure dentro de equipas](lab-services-within-teams-overview.md)

