---
title: Azure Lab Services - Guia de Administradores / Microsoft Docs
description: Este guia ajuda administradores que criam e gerem contas de laboratório utilizando os Serviços Azure Lab.
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 08d2fea719ad67f666ea9da09721dc3f7ab54768
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999285"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - Guia de Administradores
Os administradores de tecnologias da informação (TI) que gerem os recursos em nuvem de uma universidade são normalmente responsáveis pela criação da conta de laboratório para a sua escola. Depois de criarem uma conta de laboratório, administradores ou educadores criam os laboratórios que estão contidos na conta. Este artigo fornece uma visão geral de alto nível dos recursos Azure que estão envolvidos e as orientações para a sua criação.

![Diagrama de uma visão de alto nível dos recursos do Azure numa conta de laboratório.](./media/administrator-guide/high-level-view.png)

- Os laboratórios estão hospedados numa subscrição da Azure que é propriedade da Azure Lab Services.
- Contas de laboratório, uma galeria de imagens partilhada e versões de imagem estão hospedadas dentro da sua subscrição.
- Pode ter a sua conta de laboratório e a galeria de imagens partilhada no mesmo grupo de recursos. Neste diagrama, estão em diferentes grupos de recursos.

Para mais informações sobre a arquitetura, consulte os [fundamentos da arquitetura labs.](./classroom-labs-fundamentals.md)

## <a name="subscription"></a>Subscrição
A sua universidade pode ter uma ou mais assinaturas Azure. Utiliza subscrições para gerir faturação e segurança para todos os recursos e serviços da Azure que são utilizados dentro dela, incluindo contas de laboratório.

A relação entre uma conta de laboratório e a sua subscrição é importante porque:

- A faturação é reportada através da subscrição que contém a conta do laboratório.
- Pode conceder aos utilizadores do Azure Ative Directory (Azure AD) acesso ao Azure Lab Services. Pode adicionar um utilizador como uma conta de laboratório Proprietário ou Colaborador, ou como Criador de Laboratório ou Proprietário de laboratório.

Os laboratórios e as suas máquinas virtuais (VMs) são geridos e hospedados para si dentro de uma subscrição que é propriedade da Azure Lab Services.

## <a name="resource-group"></a>Grupo de recursos
Uma subscrição contém um ou mais grupos de recursos. Os grupos de recursos são usados para criar agrupamentos lógicos de recursos Azure que são usados juntos dentro da mesma solução.  

Quando se cria uma conta de laboratório, tem de configurar o grupo de recursos que contém a conta de laboratório. 

Um grupo de recursos também é necessário quando cria uma [galeria de imagens partilhada.](#shared-image-gallery) Pode colocar a sua conta de laboratório e galeria de imagens partilhada no mesmo grupo de recursos ou em dois grupos de recursos separados. Talvez queira seguir esta segunda abordagem se pretender partilhar a galeria de imagens em várias soluções.

Quando cria uma conta de laboratório, pode criar e anexar automaticamente uma galeria de imagens partilhada ao mesmo tempo.  Esta opção resulta na conta de laboratório e na galeria de imagens partilhada a ser criada em grupos de recursos separados. Você verá este comportamento quando seguir os passos descritos na [galeria de imagens partilhadas Configure no momento do tutorial de criação de conta de laboratório.](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) A imagem no início deste artigo utiliza esta configuração. 

Recomendamos que invista tempo adiantado para planear a estrutura dos seus grupos de recursos, porque *não* é possível alterar uma conta de laboratório ou um grupo de recursos de galeria de imagens partilhada uma vez que é criado. Se precisar de alterar o grupo de recursos para estes recursos, terá de eliminar e recriar a sua conta de laboratório ou galeria de imagens partilhada.

## <a name="lab-account"></a>Conta de laboratório

Uma conta de laboratório serve como recipiente para um ou mais laboratórios. Quando se começa com os Serviços Azure Lab, é mais comum ter uma única conta de laboratório. À medida que o seu uso do laboratório aumenta, pode optar por criar mais contas de laboratório mais tarde.

A seguinte lista destaca cenários onde mais de uma conta de laboratório pode ser benéfica:

- **Gerir diferentes requisitos de política em laboratórios**

    Quando crias uma conta de laboratório, defines políticas que se aplicam a *todos os* laboratórios da conta de laboratório, tais como:
    - A rede virtual Azure com recursos partilhados a que o laboratório pode aceder. Por exemplo, você pode ter um conjunto de laboratórios que precisam de acesso a um conjunto de dados compartilhados dentro de uma rede virtual.
    - As imagens de máquina virtual que os laboratórios podem usar para criar VMs. Por exemplo, você pode ter um conjunto de laboratórios que precisam de acesso ao [Data Science VM para](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) a imagem linux Azure Marketplace.

    Se cada um dos seus laboratórios tem requisitos de política únicos, pode ser benéfico criar contas de laboratório separadas para gerir cada laboratório separadamente.

- **Atribua um orçamento separado a cada conta de laboratório**
  
    Em vez de reportar todos os custos do laboratório através de uma única conta de laboratório, talvez precise de um orçamento mais claramente repartido. Por exemplo, pode criar contas de laboratório separadas para o departamento de Matemática da sua universidade, departamento de Ciências da Computação, etc., para distribuir o orçamento por departamentos.  Em seguida, pode ver o custo de cada conta de laboratório através da [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md).

- **Isolar laboratórios-piloto de laboratórios ativos ou de produção**
  
    Pode ter casos em que pretende pilotar mudanças de política para uma conta de laboratório sem afetar potencialmente os seus laboratórios ativos ou de produção. Neste tipo de cenário, a criação de uma conta de laboratório separada para fins de pilotagem permite isolar alterações. 

## <a name="lab"></a>Laboratório

Um laboratório contém VMs que são atribuídos a um único aluno.  Em geral, pode esperar:

- Tenha um laboratório para cada aula.
- Crie um novo conjunto de laboratórios para cada semestre, trimestre ou outro sistema académico que esteja a usar. Para aulas que precisam de usar a mesma imagem, deve utilizar uma [galeria de imagens partilhada.](#shared-image-gallery) Desta forma, pode reutilizar imagens em laboratórios e períodos académicos.

Quando estiver a determinar como estruturar os seus laboratórios, considere os seguintes pontos:

- **Todos os VMs dentro de um laboratório são implantados com a mesma imagem que é publicada**

    Como resultado, se tiver uma classe que exija que diferentes imagens de laboratório sejam publicadas ao mesmo tempo, um laboratório separado deve ser criado para cada imagem.
  
- **A quota de utilização é definida ao nível do laboratório e aplica-se a todos os utilizadores dentro do laboratório**

    Para definir diferentes quotas para os utilizadores, deve criar laboratórios separados. No entanto, é possível adicionar mais horas a utilizadores específicos depois de definir a quota.
  
- **O calendário de arranque ou paragem é definido ao nível do laboratório e aplica-se a todos os VMs dentro do laboratório**

    Semelhante à definição de quota, se precisar de definir horários diferentes para os utilizadores, precisa de criar um laboratório separado para cada horário.

Por padrão, cada laboratório tem a sua própria rede virtual.  Se tiver um espreitamento de rede virtual ativado, cada laboratório terá a sua própria sub-rede espreitada com a rede virtual especificada.

## <a name="shared-image-gallery"></a>Galeria de imagens partilhada

Uma galeria de imagens partilhada é anexada a uma conta de laboratório e serve como um repositório central para armazenar imagens. Uma imagem é guardada na galeria quando um educador escolhe exportá-la a partir do modelo VM de um laboratório. Cada vez que um educador faz alterações no modelo VM e exporta-o, novas versões da imagem são guardadas e as versões anteriores são mantidas.

Os instrutores podem publicar uma versão de imagem da galeria de imagens partilhada quando criarem um novo laboratório. Embora a galeria armazena várias versões de uma imagem, os educadores podem selecionar apenas a versão mais recente durante a criação de laboratório.

O serviço De Imagem Partilhada é um recurso opcional que você pode não precisar imediatamente se você está começando com apenas alguns laboratórios. No entanto, a Shared Image Gallery oferece muitos benefícios que são úteis à medida que se escala para laboratórios adicionais:

- **Pode guardar e gerir versões de uma imagem VM modelo**

    É útil criar uma imagem personalizada ou fazer alterações (software, configuração, e assim por diante) para uma imagem da galeria Azure Marketplace pública.  Por exemplo, é comum os educadores exigirem que sejam instalados software ou ferramentas diferentes. Em vez de exigir que os alunos instalem manualmente estes pré-requisitos por si só, diferentes versões da imagem VM do modelo podem ser exportadas para uma galeria de imagens partilhada. Em seguida, pode utilizar estas versões de imagem quando criar novos laboratórios.

- **Você pode compartilhar e reutilizar imagens VM modelo através de laboratórios**

    Podes guardar e reutilizar uma imagem para que não tenhas de a configurar do zero cada vez que crias um novo laboratório. Por exemplo, se várias classes precisarem de usar a mesma imagem, pode criá-la uma vez e exportá-la para a galeria de imagens partilhada para que possa ser partilhada em laboratórios.

- **A disponibilidade de imagem é assegurada através da replicação automática**

    Quando se guarda uma imagem de laboratório para a galeria de imagens partilhada, é automaticamente replicada para [outras regiões dentro da mesma geografia.](https://azure.microsoft.com/global-infrastructure/regions/) Se houver uma paragem para uma região, a publicação da imagem para o seu laboratório não é afetada, porque uma réplica de imagem de outra região pode ser usada.  A publicação de VMs a partir de várias réplicas também pode ajudar no desempenho.

Para agrupar logicamente imagens partilhadas, pode fazer qualquer uma das seguintes:

- Crie várias galerias de imagens partilhadas. Cada conta de laboratório pode ligar-se a apenas uma galeria de imagens partilhada, por isso esta opção também requer que crie várias contas de laboratório.
- Use uma única galeria de imagens partilhada que seja partilhada por várias contas de laboratório. Neste caso, cada conta de laboratório só pode permitir imagens que sejam aplicáveis aos laboratórios nessa conta.

## <a name="naming"></a>Atribuição de nomes

À medida que começa com os Serviços Azure Lab, recomendamos que estabeleça convenções de nomeação para grupos de recursos, contas de laboratório, laboratórios e galeria de imagens partilhadas. Embora as convenções de nomeação que estabelece sejam únicas às necessidades da sua organização, a tabela a seguir fornece orientações gerais:

| Tipo de recurso | Função | Padrão sugerido | Exemplos |
| ------------- | ---- | ----------------- | -------- | 
| Grupo de recursos | Contém uma ou mais contas de laboratório e uma ou mais galerias de imagens partilhadas | \<organization short name\>-\<environment\>-rg<ul><li>**O nome curto** da organização identifica o nome da organização que o grupo de recursos apoia.</li><li>**O ambiente** identifica o ambiente para o recurso, como *o piloto* ou a *produção.*</li><li>**Rg** significa para o *grupo de recursos* tipo recurso .</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Conta de laboratório | Contém um ou mais laboratórios | \<organization short name\>-\<environment\>-la<ul><li>**O nome curto** da organização identifica o nome da organização que o grupo de recursos apoia.</li><li>**O ambiente** identifica o ambiente para o recurso, como *o piloto* ou a *produção.*</li><li>**La** significa a *conta* de laboratório tipo recurso.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Laboratório | Contém um ou mais VMs |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**O nome** da classe identifica o nome da classe que o laboratório suporta.</li><li>**O prazo** identifica o prazo em que a classe é oferecida.</li>**O identificador de educador** identifica a educadora proprietária do laboratório.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-primavera2019-johndoe |
| Galeria de imagens partilhada | Contém uma ou mais versões de imagem VM | \<organization short name\>galeria | galeria contosouniversitylabsgallery |

Para obter mais informações sobre o nome de outros recursos da Azure, consulte [as convenções de nomeação para recursos Azure.](/azure/architecture/best-practices/naming-conventions)

## <a name="regionslocations"></a>Regiões\localizações

Quando configurar os seus recursos Azure Lab Services, é-lhe exigido que forneça uma região ou localização do datacenter que irá acolher os recursos. As próximas secções descrevem como uma região ou localização pode afetar cada recurso que está envolvido na criação de um laboratório.

### <a name="resource-group"></a>Grupo de recursos

A região especifica o datacenter onde a informação sobre um grupo de recursos é armazenada. Os recursos azuis contidos no grupo de recursos podem estar numa região diferente da dos seus pais.

### <a name="lab-account"></a>Conta de laboratório

A localização de uma conta de laboratório indica a região onde existe um recurso.  

### <a name="lab"></a>Laboratório

A localização em que um laboratório existe varia, dependendo dos seguintes fatores:

  - **A conta do laboratório é espreitada com uma rede virtual**
  
    Podes [espreitar uma conta de laboratório com uma rede virtual](./how-to-connect-peer-virtual-network.md) quando estão na mesma região.  Quando uma conta de laboratório é espreitada com uma rede virtual, os laboratórios são automaticamente criados na mesma região que a conta de laboratório e a rede virtual.

    > [!NOTE]
    > Quando uma conta de laboratório é espreitada com uma rede virtual, o criador do laboratório permite que o criador do laboratório escolha a definição **de localização do laboratório.** Para obter mais informações, consulte [Permitir que o criador do laboratório escolha a localização para o laboratório.](./allow-lab-creator-pick-lab-location.md)
    
  - **Nenhuma rede virtual é espreitada *e* os Criadores de Laboratório não estão autorizados a escolher a localização do laboratório**
  
    Quando *nenhuma* rede virtual é espreitada com a conta de laboratório e os [Criadores de Laboratório não estão *autorizados* a escolher a localização do laboratório, os](./allow-lab-creator-pick-lab-location.md)laboratórios são automaticamente criados numa região que tem capacidade VM disponível.  Especificamente, a Azure Lab Services procura disponibilidade em [regiões que estão dentro da mesma geografia que a conta de laboratório.](https://azure.microsoft.com/global-infrastructure/regions)

  - **Nenhuma rede virtual é espreitada *e* os Criadores de Laboratório são autorizados a escolher a localização do laboratório**
       
    Quando *nenhuma* rede virtual é espreitada e [os Criadores de Laboratório são *autorizados* a escolher a localização do laboratório,](./allow-lab-creator-pick-lab-location.md)os locais que podem ser selecionados pelo Criador de Laboratório dependem da capacidade disponível.

> [!NOTE]
> Para ajudar a garantir que uma região tem capacidade de VM suficiente, é importante primeiro solicitar a capacidade através da conta de laboratório quando estiver a criar o laboratório.

Uma regra geral é definir a região de um recurso para uma que seja mais próxima dos seus utilizadores. Para os laboratórios, isto significa criar o laboratório mais próximo dos seus alunos. Para cursos online cujos alunos estão localizados em todo o mundo, use o seu melhor julgamento para criar um laboratório que esteja localizado centralmente. Ou pode dividir uma aula em vários laboratórios de acordo com as regiões dos seus alunos.

## <a name="vm-sizing"></a>Tamanho de VM

Quando administradores ou Criadores de Laboratório criam um laboratório, podem escolher entre uma variedade de tamanhos VM, dependendo das necessidades da sua sala de aula. Lembre-se que a disponibilidade do tamanho do cálculo depende da região em que a sua conta de laboratório está localizada.

| Tamanho | Especificações | Série | Uso sugerido |
| ---- | ----- | ------ | ------------- |
| Pequeno| <ul><li>2 &nbsp; núcleos</li><li>3,5 gigabytes (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Mais adequado para linha de comando, abertura de navegador web, servidores web de baixo tráfego, bases de dados pequenas a médias. |
| Médio | <ul><li>4 &nbsp; núcleos</li><li>7 &nbsp; GB &nbsp; DE RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Mais adequado para bases de dados relacionais, caching na memória e análise. |
| Médio (virtualização aninhada) | <ul><li>4 &nbsp; núcleos</li><li>16 &nbsp; GB &nbsp; DE RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | Mais adequado para bases de dados relacionais, caching na memória e análise.
| Grande | <ul><li>8 &nbsp; núcleos</li><li>16 &nbsp; GB &nbsp; DE RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | Mais adequado para aplicações que precisam de CPUs mais rápidos, melhor desempenho do disco local, grandes bases de dados, caches de memória grandes.  Este tamanho também suporta a virtualização aninhada. |
| Grande (virtualização aninhada) | <ul><li>8 &nbsp; núcleos</li><li>32 &nbsp; GB &nbsp; DE RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | Mais adequado para aplicações que precisam de CPUs mais rápidos, melhor desempenho do disco local, grandes bases de dados, caches de memória grandes. |
| Pequena GPU (visualização) | <ul><li>6 &nbsp; núcleos</li><li>56 &nbsp; GB &nbsp; DE RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | Mais adequado para visualização remota, streaming, jogos e codificação utilizando quadros como OpenGL e DirectX. |
| Pequena GPU (Computação) | <ul><li>6 &nbsp; núcleos</li><li>56 &nbsp; GB &nbsp; DE RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |Mais adequado para aplicações intensivas em computador, como IA e aprendizagem profunda. |
| GPU médio (visualização) | <ul><li>12 &nbsp; núcleos</li><li>112 &nbsp; GB &nbsp; DE RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | Mais adequado para visualização remota, streaming, jogos e codificação utilizando quadros como OpenGL e DirectX. |

## <a name="manage-identity"></a>Gerir identidade

Ao utilizar [o controlo de acesso baseado em funções (RBAC) da Azure](../role-based-access-control/overview.md) para acesso a contas de laboratório e laboratórios, pode atribuir as seguintes funções:

- Proprietário de conta **de laboratório**

    Um administrador que cria uma conta de laboratório é automaticamente atribuído à conta de laboratório Do Proprietário. A função proprietário pode:
     - Mude as definições da conta do laboratório.
     - Conceda a outros administradores acesso à conta do laboratório como Proprietário ou Contribuinte.
     - Conceder aos educadores acesso a laboratórios como Criador, Proprietário ou Colaborador.
     - Criar e gerir todos os laboratórios na conta do laboratório.

- **Contribuidor** de conta de laboratório

    Um administrador que tenha atribuído a função de Contribuinte pode:
    - Mude as definições da conta do laboratório.
    - Criar e gerir todos os laboratórios na conta do laboratório.

    No entanto, o Contribuinte *não pode* conceder a outros utilizadores acesso a contas de laboratório ou laboratórios.

- **Criador de Laboratório**

    Para criar laboratórios dentro de uma conta de laboratório, um educador deve ser um membro do papel de Criador de Laboratório.  Um educador que cria um laboratório é automaticamente adicionado como dono de laboratório. Para obter mais informações, consulte [Adicionar um utilizador à função Criador de Laboratório](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role). 

- **Proprietário** de laboratório ou **colaborador**
  
    Um educador num papel de proprietário de laboratório ou contribuinte pode ver e alterar as configurações de um laboratório. A pessoa também deve ser um membro do papel de leitor de conta de laboratório.

    Uma diferença fundamental entre as funções de proprietário de laboratório e contribuinte é que apenas um Proprietário pode conceder a outros utilizadores acesso para gerir um laboratório. Um Contribuinte *não pode* dar a outros utilizadores acesso para gerir um laboratório.

- **Galeria de imagens partilhada**

    Quando anexa uma galeria de imagens partilhada a uma conta de laboratório, os proprietários e colaboradores de laboratório e criadores de laboratório, os proprietários de laboratórios e os contribuintes de laboratório têm automaticamente acesso à visualização e guardar imagens na galeria.

Quando atribui funções, ajuda a seguir estas dicas:

   - Normalmente, apenas os administradores devem ser membros de uma conta de laboratório Titular ou colaborador. A conta do laboratório pode ter mais do que um Proprietário ou Colaborador.
   - Para dar aos educadores a capacidade de criar novos laboratórios e gerir os laboratórios que eles criam, só precisa atribuir-lhes o papel de Criador de Laboratório.
   - Para dar aos educadores a capacidade de gerir laboratórios específicos, mas *não* a capacidade de criar novos laboratórios, atribuí-los ao papel de Proprietário ou Contribuinte para cada laboratório que eles vão gerir. Por exemplo, talvez queira permitir que um professor e um assistente de ensino seja co-dono de um laboratório. Para mais informações, consulte [Add Owners to a lab](./how-to-add-user-lab-owner.md).

## <a name="pricing"></a>Preços

### <a name="azure-lab-services"></a>Azure Lab Services

Para saber mais sobre os preços, consulte [os preços dos Serviços Azure Lab.](https://azure.microsoft.com/pricing/details/lab-services/)


### <a name="shared-image-gallery"></a>Galeria de Imagens Partilhada

Também precisa de considerar os preços para o serviço De Imagem Partilhada se planeia utilizar galerias de imagem partilhadas para armazenar e gerir versões de imagem. 

Criar uma galeria de imagens partilhada e anexá-la à sua conta de laboratório é gratuita. Não há qualquer custo até guardar uma versão de imagem para a galeria. O preço para usar uma galeria de imagens partilhada é normalmente bastante insignificante, mas é importante entender como é calculado, porque não está incluído no preço dos Serviços Azure Lab.  

#### <a name="storage-charges"></a>Encargos de armazenamento

Para armazenar versões de imagem, uma galeria de imagens partilhada utiliza discos geridos por discos de disco rígido standard (HDD). O tamanho do disco gerido por HDD que é usado depende do tamanho da versão de imagem que está a ser armazenada. Para aprender sobre os preços, consulte os [preços dos discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Taxas de replicação e de saída de rede

Quando guarda uma versão de imagem utilizando um VM modelo de laboratório, o Azure Lab Services armazena-o primeiro numa região de origem e depois replica automaticamente a versão de imagem de origem para uma ou mais regiões-alvo. 

É importante notar que a Azure Lab Services replica automaticamente a versão de imagem de origem para todas as [regiões-alvo dentro da geografia](https://azure.microsoft.com/global-infrastructure/regions/) onde o laboratório está localizado. Por exemplo, se o seu laboratório estiver na geografia dos EUA, uma versão de imagem é replicada em cada uma das oito regiões que existem nos EUA.

Uma carga de saída de rede ocorre quando uma versão de imagem é replicada da região de origem para regiões-alvo adicionais. O montante cobrado baseia-se no tamanho da versão de imagem quando os dados da imagem são inicialmente transferidos para fora da região de origem.  Para obter detalhes sobre preços, consulte [os detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

As taxas da Egress podem ser dispensadas para os [clientes da Education Solutions.](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) Para saber mais, contacte o seu gestor de conta. 

Para mais informações, consulte "Que programas de transferência de dados existem para clientes académicos e como me qualifico?" na secção faq da página [programas para instituições de ensino.](https://azure.microsoft.com/pricing/details/bandwidth/)

#### <a name="pricing-example"></a>Exemplo de preços

Vejamos um exemplo do custo de guardar uma imagem VM modelo para uma galeria de imagens partilhada. Assuma os seguintes cenários:

- Tem uma imagem VM personalizada.
- Estás a guardar duas versões da imagem.
- O seu laboratório é nos EUA, que tem um total de oito regiões.
- Cada versão de imagem tem 32 GB de tamanho; como resultado, o preço do disco gerido por HDD é de $1,54 por mês.

O custo total por mês é estimado como:

* *Número de imagens &times; número de &times; versões número de réplicas &times; geridas preço do disco = custo total por mês*

Neste exemplo, o custo é:

* 1 imagem personalizada (32 GB) &times; 2 versões &times; 8 regiões dos EUA &times; $1.54 = $24,64 por mês

> [!NOTE]
> O cálculo anterior é apenas para fins. Cobre os custos de armazenamento associados à utilização da Galeria de Imagens Partilhadas e *não* inclui custos de saída. Para obter preços reais para armazenamento, consulte [os preços dos Discos Geridos](https://azure.microsoft.com/en-us/pricing/details/managed-disks/).

#### <a name="cost-management"></a>Gestão de custos

É importante que os administradores de contas de laboratório gerem os custos eliminando rotineiramente versões de imagem não necessidade da galeria. 

Não elimine a replicação em regiões específicas como forma de reduzir os custos, embora esta opção exista na galeria de imagens partilhada. As alterações de replicação podem ter efeitos adversos na capacidade da Azure Lab Services de publicar VMs a partir de imagens guardadas dentro de uma galeria de imagens partilhada.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a criação e gestão de laboratórios, consulte:

- [Guia de configuração de conta de laboratório](account-setup-guide.md)  
- [Guia de configuração do laboratório](setup-guide.md)  
- [Gestão de custos para laboratórios](cost-management-guide.md)  
- [Utilizar serviços de laboratório Azure em equipas](lab-services-within-teams-overview.md)
