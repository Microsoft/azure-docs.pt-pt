---
title: Guia do Azure Lab Services do administrador | Microsoft Docs
description: Este guia ajuda os administradores que criam e gerenciam contas de laboratório usando Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 638a90615d248b3c2829770432dd6a08eb4bb2fb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771739"
---
# <a name="azure-lab-services---administrator-guide"></a>Guia do Azure Lab Services do administrador
Os administradores de ti (tecnologia da informação) que gerenciam os recursos de nuvem de uma organização também são responsáveis por configurar a conta de laboratório para sua organização. Os administradores ou educadores criam laboratórios de sala de aula na conta do laboratório. Este artigo fornece uma visão geral de alto nível dos recursos do Azure envolvidos e as diretrizes para criá-los.

![Exibição de alto nível dos recursos do Azure em uma conta de laboratório](../media/administrator-guide/high-level-view.png)

- Os laboratórios de sala de aula são hospedados em uma assinatura do Azure de Propriedade do Azure Lab Services
- As contas de laboratório, a Galeria de imagens compartilhadas e as versões de imagem são hospedadas em sua assinatura
- Você pode ter a conta de laboratório e a Galeria de imagens de fragmento no mesmo grupo de recursos. Neste diagrama, eles estão em grupos de recursos diferentes. 

## <a name="subscription"></a>Subscrição
Sua organização tem uma ou mais assinaturas do Azure. Uma assinatura é usada para gerenciar a cobrança e a segurança de todas as resources\services do Azure que são usadas dentro dela, incluindo contas de laboratório.

A relação entre uma conta de laboratório e sua assinatura é importante porque:

- A cobrança é relatada por meio da assinatura que contém a conta do laboratório.
- Você pode fornecer aos usuários o locatário do Azure Active Directory (AD) associado à assinatura o acesso ao Azure Lab Services. Você pode adicionar o usuário como uma conta de laboratório owner\contributor ou como um criador de laboratório de sala de aula.

Os laboratórios de sala de aula e suas VMs (máquinas virtuais) são totalmente gerenciados para você. Para serem específicos, eles são hospedados em uma assinatura dedicada de propriedade de Azure Lab Services.

## <a name="resource-group"></a>Grupo de recursos
Uma assinatura contém um ou mais grupos de recursos. Os grupos de recursos são usados para criar agrupamentos lógicos de recursos do Azure que são usados juntos na mesma solução.  

Ao criar uma conta de laboratório, você deve configurar o grupo de recursos que contém a conta do laboratório. 

Um grupo de recursos também é necessário ao criar uma [Galeria de imagens compartilhada](#shared-image-gallery). Você pode optar por colocar sua conta de laboratório e a Galeria de imagens compartilhadas em dois grupos de recursos separados, o que é típico se você planeja compartilhar a Galeria de imagens em diferentes soluções. Ou, você pode optar por colocá-los no mesmo grupo de recursos.

Quando você cria uma conta de laboratório e automaticamente cria e anexa uma galeria de imagens compartilhada ao mesmo tempo, a conta de laboratório e a Galeria de imagens compartilhadas são criadas em grupos de recursos separados por padrão. Você verá esse comportamento ao usar as etapas descritas neste tutorial: [Configurar a Galeria de imagens compartilhadas no momento da criação da conta do laboratório](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A imagem na parte superior deste artigo também usa essa configuração. 

É recomendável investir tempo de investimento antecipadamente para planejar a estrutura de seus grupos de recursos, pois não é possível alterar o grupo de recursos de uma galeria de imagens compartilhada ou de uma conta de laboratório após sua criação. Se você precisar alterar o grupo de recursos para esses recursos, será necessário excluir e recriar sua conta de laboratório e/ou galeria de imagens compartilhadas.

## <a name="lab-account"></a>Conta de laboratório
Uma conta de laboratório serve como um contêiner para um ou mais laboratórios de sala de aula. Ao começar a usar o Azure Lab Services, é comum ter apenas uma única conta de laboratório. À medida que o uso do laboratório for dimensionado, você poderá optar por criar mais contas de laboratório.

A lista a seguir realça os cenários em que mais de uma conta de laboratório pode ser benéfica:

- **Gerenciar requisitos de política diferentes em laboratórios de sala de aula** 

    Ao configurar uma conta de laboratório, você define as políticas que se aplicam a todos os laboratórios de sala de aula na conta do laboratório, como:
    - A rede virtual do Azure com recursos compartilhados que o laboratório da sala de aula pode acessar. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam de acesso a um conjunto de dados compartilhado em uma rede virtual.
    - As imagens de VM (máquina virtual) que os laboratórios de sala de aula podem usar para criar VMs. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam acessar o [VM de ciência de dados para a imagem do Marketplace do Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) .  

    Se você tiver laboratórios de sala de aula com requisitos de política exclusivos uns dos outros, poderá ser benéfico criar contas de laboratório separadas para gerenciar esses laboratórios de sala de aula separadamente.
- **Restringir o acesso de criadores de laboratório a laboratórios de sala de aula específicos**  

    Quando um usuário é adicionado como um criador de laboratório, ele recebe acesso a todos os laboratórios de sala de aula dentro da conta do laboratório, incluindo os laboratórios criados por outros criadores de laboratório. Para restringir os criadores de laboratório ao gerenciamento de laboratórios específicos, você pode criar contas de laboratório separadas para restringir o escopo de seu acesso. Por exemplo, você pode criar uma conta de laboratório separada para cada departamento dentro de uma universidade. Por exemplo: uma conta de laboratório para o departamento de ciência e outra para o departamento de matemática, e assim por diante.   
- **Orçamento separado por conta de laboratório**

    Em vez de ter todos os custos de laboratório de sala de aula relatados para uma única conta de laboratório, talvez seja necessário ter um orçamento mais claramente separado. Continuando com o exemplo no marcador acima, você pode criar uma conta de laboratório para cada departamento universitário para separar o orçamento de acordo. Usando o gerenciamento de custos do Azure, você pode exibir o custo de cada conta de laboratório individual.
- **Isole os laboratórios piloto dos laboratórios ativos**

    Você pode ter casos em que deseja fazer alterações de política de piloto em uma conta de laboratório sem afetar potencialmente os laboratórios ativos. Nesse tipo de cenário, a criação de uma conta de laboratório separada para fins de piloto permite isolar as alterações. 

## <a name="classroom-lab"></a>Laboratório de sala de aula
Um laboratório de sala de aula contém uma ou mais VMs (máquinas virtuais) que são atribuídas a um aluno específico. Em geral, você pode esperar:

- Ter um laboratório de sala de aula para cada classe.
- Crie um novo conjunto de laboratórios de sala de aula a cada semestre (ou para cada período de tempo em que sua classe é oferecida). Normalmente, para classes que têm as mesmas necessidades de imagem, você usaria uma [Galeria de imagens compartilhadas](#shared-image-gallery) para compartilhar imagens entre laboratórios e semestrees.

Considere os seguintes pontos ao determinar como estruturar seus laboratórios de sala de aula:

- **Todas as VMs em um laboratório de sala de aula são implantadas com a mesma imagem publicada**. Como resultado, se você tiver uma classe que exija que imagens de laboratório diferentes sejam publicadas ao mesmo tempo, os laboratórios de sala de aula separados deverão ser criados para cada um.
- A **cota de uso é definida no nível de laboratório e se aplica a todos os usuários no laboratório**. Por exemplo, você pode ter um conjunto de educadores que precisam de acesso às VMs de uma classe para se preparar para ensinar, mas os educadores exigem apenas uma cota de 10 horas, enquanto os alunos inscritos na classe exigem uma cota de 40 horas. Para definir cotas diferentes para usuários, você deve criar laboratórios de sala de aula separados. No entanto, é possível adicionar mais horas a um usuário específico depois de definir a cota.
- **A agenda de inicialização ou desligamento é definida no nível do laboratório e se aplica a todas as VMs no laboratório**. Semelhante ao ponto anterior, se você precisar definir agendamentos diferentes para os usuários, precisará criar laboratórios de sala de aula separados. 

## <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas
Uma galeria de imagens compartilhada é anexada a uma conta de laboratório e serve como um repositório central para armazenar imagens. Uma imagem é salva na Galeria quando um professor opta por salvar de uma VM (máquina virtual) de modelo de um laboratório de sala de aula. Cada vez que um professor faz alterações na VM de modelo e salva, novas versões da imagem são salvas mantendo as versões anteriores.

Os instrutores podem publicar uma versão de imagem da Galeria de imagens compartilhadas ao criar um novo laboratório de sala de aula. Embora a Galeria possa armazenar várias versões de uma imagem, educadores só podem selecionar a versão mais recente durante a criação do laboratório.

A Galeria de imagens compartilhadas é um recurso opcional que talvez não seja necessário imediatamente ao começar com apenas alguns laboratórios de sala de aula. No entanto, usar a Galeria de imagens compartilhadas tem muitos benefícios que são úteis à medida que você dimensiona para ter mais laboratórios de sala de aula:

- **Permite salvar e gerenciar versões de uma imagem de VM de modelo**.

    É útil ao criar uma imagem personalizada ou fazer alterações (software, configuração e assim por diante) em uma imagem da galeria do Marketplace público.  Por exemplo, é comum que educadores exijam que software\tooling diferentes sejam instalados. Em vez de exigir que os alunos instalem manualmente esses pré-requisitos por conta própria, diferentes versões da imagem de VM de modelo podem ser salvas em uma galeria de imagens compartilhada. Essas versões de imagem podem ser usadas ao criar novos laboratórios de sala de aula.
- **Habilita sharing\reuse de imagens de VM de modelo em laboratórios de sala de aula**.

    Ele impede que você precise configurar uma imagem do zero sempre que criar um novo laboratório de sala de aula. Por exemplo, se várias classes estiverem sendo oferecidas que precisam da mesma imagem, essa imagem só precisará ser criada uma vez e salva na Galeria de imagens compartilhadas para que possa ser compartilhada entre os laboratórios da sala de aula.
- **Garante a disponibilidade da imagem por meio da replicação**.

    Ao salvar na Galeria de imagens compartilhadas de um laboratório de sala de aula, sua imagem é replicada automaticamente para outras regiões na mesma geografia. Caso haja uma interrupção para uma região, a publicação da VM de modelo em seu laboratório de sala de aula não é afetada com o uso de uma réplica de imagem em outras regiões. Além disso, ele ajuda com o desempenho em cenários de publicação de várias VMs espalhando-se para usar réplicas diferentes.

Para agrupar logicamente as imagens compartilhadas, você tem algumas opções:

- Criar várias galerias de imagens compartilhadas. Cada conta de laboratório só pode se conectar a uma galeria de imagens compartilhadas, portanto, essa opção também exigirá que você crie várias contas de laboratório.
- Ou, você pode usar uma única Galeria de imagens compartilhadas que é compartilhada por várias contas de laboratório. Nesse caso, cada conta de laboratório pode habilitar apenas as imagens aplicáveis aos laboratórios de sala de aula que ela contém.

## <a name="naming"></a>Atribuição de nomes
Ao começar a usar o Azure Lab Services, recomendamos que você estabeleça convenções de nomenclatura para grupos de recursos, contas de laboratório, laboratórios de sala de aula e a Galeria de imagens compartilhadas. Embora as convenções de nomenclatura estabelecidas sejam exclusivas às necessidades da sua organização, a tabela a seguir descreve as diretrizes gerais.

| Tipo de recurso | Função | Padrão sugerido | Exemplos |
| ------------- | ---- | ----------------- | -------- | 
| Grupo de recursos | Contém uma ou mais contas de laboratório e uma ou mais galerias de imagens compartilhadas | \<nome curto da organização\>-\<ambiente\>-RG<ul><li>**Nome curto da organização** identifica o nome da organização à qual o grupo de recursos dá suporte</li><li>O **ambiente** identifica o ambiente para o recurso, como teste ou produção</li><li>**RG** significa o tipo de recurso: grupo de recursos.</li></ul> | contosouniversitylabs-RG<br/>contosouniversitylabs-Test-RG<br/>contosouniversitylabs-prod-RG |
| Conta de laboratório | Contém um ou mais laboratórios | \<nome curto da organização\>-\<ambiente\>-la<ul><li>**Nome curto da organização** identifica o nome da organização à qual o grupo de recursos dá suporte</li><li>O **ambiente** identifica o ambiente para o recurso, como teste ou produção</li><li>**La** significa o tipo de recurso: conta de laboratório.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-Test-la<br/>sciencedeptlabs-prod-la |
| Laboratório de sala de aula | Contém uma ou mais VMs |\<nome da classe\>-\<período de tempo\>-\<o identificador do professor\><ul><li>**Nome da classe** identifica o nome da classe à qual o laboratório dá suporte.</li><li>**Período de tempo** identifica o período de tempo no qual a classe é oferecida.</li>O **identificador de educação** identifica o educador que possui o laboratório.</li></ul> | CS1234-fall2019-davibarros<br/>CS1234-spring2019-davibarros | 
| Galeria de imagens compartilhadas | Contém uma ou mais versões de imagem de VM | Galeria de\>de nome curto da organização \< | contosouniversitylabsgallery |

Para obter mais informações sobre como nomear outros recursos do Azure, consulte [convenções de nomenclatura para recursos do Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regions-or-locations"></a>Regiões ou locais
Ao configurar os recursos do Azure Lab Services, você precisa fornecer uma região, ou local, do data center que hospedará o recurso. Aqui estão mais detalhes sobre como o region\location afeta cada um dos seguintes recursos usados em sua implantação de serviços de laboratório:

- **Grupo de recursos**

    A região especifica a data center em que as informações sobre o grupo de recursos são armazenadas. Os recursos do Azure contidos no grupo de recursos podem estar em regiões diferentes do seu pai.
- **Conta de laboratório ou laboratório de sala de aula**

    O local da conta do laboratório indica a região deste recurso. Os laboratórios de sala de aula criados na conta de laboratório podem ser implantados em qualquer região na mesma geografia. A região específica em que as VMs do laboratório são implantadas é selecionada automaticamente com base na capacidade disponível na região naquele momento.  
    Se um administrador permitir que os criadores de laboratório escolham o local de seu laboratório de sala de aula, os locais disponíveis para seleção serão baseados na capacidade regional disponível ao criar o laboratório.

    O local do laboratório da sala de aula também determina quais tamanhos de computação da VM estão disponíveis para seleção. Determinados tamanhos de computação só estão disponíveis em locais específicos.
- **Galeria de imagens compartilhadas**

    A região indica a região de origem em que a primeira versão da imagem é armazenada antes de ser replicada automaticamente para as regiões de destino.
    
Uma regra geral é definir o region\location de um recurso para um que seja mais próximo de seus usuários. Para os laboratórios de sala de aula, isso significa criar o laboratório de sala de aula mais próximo de seus alunos. Para cursos online em que os alunos estão localizados em todo o mundo, você precisa usar o melhor julgamento para criar um laboratório de sala de aula que esteja localizado centralmente. Ou, dividir uma classe em vários laboratórios de sala de aula com base na região dos seus alunos.

## <a name="vm-sizing"></a>Dimensionamento de VM
Quando os administradores ou os criadores de laboratório criam um laboratório de sala de aula, eles podem escolher entre os seguintes tamanhos de VM com base nas necessidades de sua sala de aula. Lembre-se de que os tamanhos de computação disponíveis dependem da região em que sua conta de laboratório está localizada:

| Tamanho | Especificações | Uso sugerido |
| ---- | ----- | ------------- |
| Pequeno| <ul><li>2 núcleos</li><li>3,5 GB RAM</li></ul> | Esse tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores Web de tráfego baixo, bancos de dados pequenos a médios. |
| Médio | <ul><li>4 núcleos</li><li>7 GB RAM</li></ul> | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise. |
| Médio (virtualização aninhada) | <ul><li>4 núcleos</li><li>16 GB DE RAM</li></ul> | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise.  Esse tamanho também dá suporte à virtualização aninhada. |
| Grande | <ul><li>8 núcleos</li><li>32 GB DE RAM</li></ul> | Esse tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grande.  Esse tamanho também dá suporte à virtualização aninhada. |
| GPU pequena (visualização) | <ul><li>6 núcleos</li><li>56 GB DE RAM</li> | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. |
| GPU pequena (computação) | <ul><li>6 núcleos</li><li>56 GB DE RAM</li></ul> |Esse tamanho é mais adequado para aplicativos com uso intensivo de computadores, como inteligência artificial e aprendizado profundo. |
| GPU média (visualização) | <ul><li>12 núcleos</li><li>112 GB DE RAM</li></ul> | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. |

## <a name="manage-identity"></a>Gerenciar identidade
Há dois tipos de funções que um administrador de conta de laboratório pode ter:

- **Proprietário**

    Um administrador que é atribuído à função **proprietário** tem acesso total à conta de laboratório, incluindo o direito de conceder a outros usuários acesso à conta de laboratório e Adicionar criadores de laboratório. O administrador que cria a conta de laboratório por padrão é adicionado como o proprietário.
- **Contribuidor**

    Um administrador que é atribuído à função Colaborador pode alterar as configurações de conta do laboratório, mas eles não podem conceder acesso a outros usuários; Nem, eles podem adicionar criadores de laboratório.

Quando você anexa uma galeria de imagens compartilhada a uma conta de laboratório, o acesso é fornecido automaticamente para os criadores de administrador e de laboratório para que eles possam exibir e salvar imagens na galeria. 

## <a name="pricing"></a>Preços

### <a name="azure-lab-services"></a>Azure Lab Services
O preço para Azure Lab Services é descrito no seguinte artigo: [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

Você também precisa considerar o preço da Galeria de imagens compartilhadas se planeja usá-la para armazenar e gerenciar versões de imagem. 

### <a name="shared-image-gallery"></a>Galeria de imagens compartilhadas
Criar uma galeria de imagens compartilhada e anexá-la à sua conta de laboratório é gratuita. Os custos não são incorridos até que você salve uma versão de imagem na galeria. Normalmente, o preço para usar uma galeria de imagens compartilhadas é razoavelmente insignificante, mas é importante entender como ele é calculado, pois não está incluído no preço de Azure Lab Services.  

### <a name="storage-charges"></a>Encargos de armazenamento
Para armazenar versões de imagem, uma galeria de imagens compartilhadas usa discos gerenciados por HDD padrão. O tamanho do disco gerenciado por HDD que é usado depende do tamanho da versão da imagem que está sendo armazenada. Consulte o artigo a seguir para exibir o preço: [preços do Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).


### <a name="replication-and-network-egress-charges"></a>Encargos de saída de rede e replicação
Quando você salva uma versão de imagem usando uma VM (máquina virtual) de modelo de laboratório de sala de aula, o Lab Services primeiro a armazena em uma região de origem e, em seguida, replica automaticamente a versão da imagem de origem para uma ou mais regiões de destino. É importante observar que Azure Lab Services replica automaticamente a versão da imagem de origem para todas as regiões de destino na localização geográfica onde o laboratório da sala de aula está localizado. Por exemplo, se o laboratório da sala de aula estiver na localização geográfica dos EUA, uma versão de imagem será replicada para cada uma das oito regiões existentes nos EUA.

Um encargo de egresso de rede ocorre quando uma versão de imagem é replicada da região de origem para outras regiões de destino. O valor cobrado é baseado no tamanho da versão da imagem quando os dados da imagem são transferidos inicialmente de saída da região de origem.  Para obter detalhes de preços, consulte o seguinte artigo: [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Os clientes de [soluções educacionais](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) podem ser renunciados ao pagamento de encargos de egresso. Fale com seu gerente de conta para saber mais.  Para obter mais informações, consulte **consulte a seção de perguntas frequentes** no documento vinculado, especificamente a pergunta "quais programas de transferência de dados existem para clientes acadêmicos e como posso me qualificar?").

### <a name="pricing-example"></a>Exemplo de preços
Para recapitular o preço descrito acima, vejamos um exemplo de como salvar nossa imagem de VM de modelo na Galeria de imagens compartilhadas. Suponha os seguintes cenários:

- Você tem uma imagem de VM personalizada.
- Você está salvando duas versões da imagem.
- Seu laboratório está nos EUA, que tem um total de oito regiões.
- Cada versão de imagem tem 32 GB de tamanho; Como resultado, o preço do disco gerenciado por HDD é $1.54 por mês.

O custo total é estimado como:

Número de imagens × número de versões × número de réplicas × preço de disco gerenciado

Neste exemplo, o custo é:

1 imagem personalizada (32 GB) x 2 versões x 8 regiões dos EUA x $1.54 = $24.64 por mês

### <a name="cost-management"></a>Gestão de custos
É importante que o administrador da conta do laboratório gerencie os custos, excluindo rotineiramente as versões de imagem desnecessárias da galeria. 

Você não deve excluir a replicação para regiões específicas como uma maneira de reduzir os custos (essa opção existe na Galeria de imagens compartilhadas). As alterações de replicação podem ter efeitos adversos na capacidade do serviço de laboratório do Azure de publicar VMs de imagens salvas em uma galeria de imagens compartilhadas.

## <a name="next-steps"></a>Passos seguintes
Consulte o tutorial para obter instruções passo a passo para criar uma conta de laboratório e um laboratório: [tutorial: configurar uma conta de laboratório](tutorial-setup-lab-account.md)
