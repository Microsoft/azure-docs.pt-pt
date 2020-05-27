---
title: Serviços Azure Lab - Guia de administradores Microsoft Docs
description: Este guia ajuda administradores que criam e gerem contas de laboratório usando os Serviços de Laboratório Azure.
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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: ce9f8ee592c1fb2f7ac98339bbd14ce57440bc1a
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815711"
---
# <a name="azure-lab-services---administrator-guide"></a>Serviços Azure Lab - Guia de administrador
Os administradores de Tecnologias de Informação (TI) que gerem os recursos em nuvem de uma universidade são normalmente responsáveis pela criação da conta de laboratório para a sua escola. Uma vez criada uma conta de laboratório, administradores ou educadores criam laboratórios de sala de aula que estão contidos na conta do laboratório. Este artigo fornece uma visão geral de alto nível dos recursos azure envolvidos e a orientação para a sua criação.

![Visão de alto nível dos recursos do Azure numa conta de laboratório](../media/administrator-guide/high-level-view.png)

- Os laboratórios de sala de aula estão hospedados dentro de uma subscrição Azure propriedade da Azure Lab Services.
- Contas de laboratório, galeria de imagens partilhadas e versões de imagem estão alojadas dentro da sua subscrição.
- Pode ficar com a sua conta de laboratório e a galeria de imagens partilhadas no mesmo grupo de recursos. Neste diagrama, estão em diferentes grupos de recursos. 

## <a name="subscription"></a>Subscrição
A sua universidade tem uma ou mais subscrições Azure. Uma subscrição é usada para gerir a faturação e segurança de todos os recursos Azure\services que são usados dentro dela, incluindo contas de laboratório.

A relação entre uma conta de laboratório e a sua subscrição é importante porque:

- A faturação é reportada através da assinatura que contém a conta do laboratório.
- Pode dar aos utilizadores do Azure Ative Directory (AD) da subscrição acesso ao acesso ao Azure Lab Services. Você pode adicionar um utilizador como proprietário de uma conta de laboratório\contributor, criador de laboratório de sala de aula ou proprietário de laboratório de sala de aula.

Os laboratórios em sala de aula e as suas máquinas virtuais (VMs) são geridos e hospedados para si dentro de uma subscrição propriedade da Azure Lab Services.

## <a name="resource-group"></a>Grupo de recursos
Uma subscrição contém um ou mais grupos de recursos. Os grupos de recursos são usados para criar agrupamentos lógicos de recursos Azure que são usados juntos dentro da mesma solução.  

Quando criauma conta de laboratório, tem de configurar o grupo de recursos que contém a conta de laboratório. 

Um grupo de recursos também é necessário na criação de uma galeria de [imagens partilhadas.](#shared-image-gallery) Você pode optar por colocar a sua conta de laboratório e galeria de imagens partilhadas em dois grupos de recursos separados, o que é típico se você planeja partilhar a galeria de imagens através de diferentes soluções. Ou pode optar por colocá-los no mesmo grupo de recursos.

Quando cria uma conta de laboratório, pode criar e anexar automaticamente uma galeria de imagens partilhadas ao mesmo tempo.  Esta opção resulta na conta de laboratório e na galeria de imagens partilhadas sendo criada em grupos de recursos separados. Você verá este comportamento ao usar os passos descritos neste tutorial: [Configure galeria de imagens partilhadano momento da criação de conta de laboratório](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A imagem no topo deste artigo também usa esta configuração. 

Recomendamos investir tempo adiantado para planear a estrutura dos seus grupos de recursos, uma vez que *não* é possível alterar o grupo de recursos de uma conta de laboratório ou uma galeria de imagens partilhada uma vez que é criado. Se precisar de alterar o grupo de recursos para estes recursos, terá de apagar e recriar a sua conta de laboratório e a galeria de imagens partilhada.

## <a name="lab-account"></a>Conta de laboratório

Uma conta de laboratório serve como recipiente para um ou mais laboratórios de sala de aula. Quando começar com os Serviços de Laboratório Azure, é comum ter apenas uma única conta de laboratório. À medida que o seu laboratório escala, poderá mais tarde optar por criar mais contas de laboratório.

A lista a seguir destaca cenários em que mais de uma conta de laboratório pode ser benéfica:

- **Gerir diferentes requisitos de política em laboratórios de sala de aula**

    Quando crias uma conta de laboratório, defines políticas que se aplicam a *todos os* laboratórios de sala de aula sob a conta do laboratório, tais como:
    - A rede virtual Azure com recursos partilhados a que o laboratório de sala de aula pode aceder. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam de acesso a um conjunto de dados partilhado dentro de uma rede virtual.
    - A máquina virtual (VM) imagens que os laboratórios de sala de aula podem usar para criar VMs. Por exemplo, você pode ter um conjunto de laboratórios de sala de aula que precisam de acesso ao VM de Ciência de Dados para imagem [de Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Marketplace.

    Se você tem laboratórios de sala de aula que têm requisitos políticos únicos uns dos outros, pode ser benéfico criar contas de laboratório separadas para gerir estes laboratórios de sala de aula separadamente.

- **Orçamento separado por conta de laboratório**
  
    Em vez de reportar todos os custos do laboratório através de uma única conta de laboratório, pode precisar de um orçamento mais claramente separado. Por exemplo, pode criar contas laboratoriais para o departamento de Matemática da sua universidade, departamento de Ciências da Computação, e assim por diante, para separar o orçamento entre departamentos.  Em seguida, pode visualizar o custo de cada conta de laboratório usando a [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview).

- **Isolar laboratórios piloto de laboratórios de produção ativa**
  
    Pode ter casos em que pretende pilotar mudanças de política para uma conta de laboratório sem ter potencial impacto nos laboratórios de produção ativa. Neste tipo de cenário, criar uma conta de laboratório separada para fins de pilotagem permite isolar alterações. 

## <a name="classroom-lab"></a>Laboratório de sala de aula

Um laboratório de sala de aula contém máquinas virtuais (VMs) que são atribuídas a um único aluno.  Em geral, pode esperar:

- Tenha um laboratório de sala de aula para cada aula.
- Crie um novo conjunto de laboratórios de sala de aula a cada semestre (ou para cada período de tempo que a sua aula é oferecida). Normalmente para aulas com as mesmas necessidades de imagem, você deve usar uma galeria de [imagens partilhada](#shared-image-gallery) para reutilizar imagens em laboratórios e semestres.

Considere os seguintes pontos ao determinar como estruturar os seus laboratórios de sala de aula:

- **Todos os VMs dentro de um laboratório de sala de aula são implantados com a mesma imagem que é publicada**

    Como resultado, se você tem uma classe que requer diferentes imagens de laboratório ser publicada ao mesmo tempo, laboratórios de sala de aula separados devem ser criados para cada um.
  
- **A quota de utilização é definida ao nível do laboratório e aplica-se a todos os utilizadores dentro do laboratório**

    Para definir diferentes quotas para os utilizadores, você deve criar laboratórios de sala de aula separados. No entanto, é possível adicionar mais horas a um utilizador específico depois de ter definido a quota.
  
- **O horário de arranque ou encerramento é definido ao nível do laboratório e aplica-se a todos os VMs dentro do laboratório**

    À semelhança do ponto anterior, se precisar de definir horários diferentes para os utilizadores, precisa de criar laboratórios de sala de aula separados.

Por padrão, cada laboratório de sala de aula terá a sua própria rede virtual.  Se tiver o vnet peering ativado, cada laboratório de sala de aula terá a sua própria sub-rede observada para a rede virtual especificada.

## <a name="shared-image-gallery"></a>Galeria de imagens partilhadas

Uma galeria de imagens partilhada é anexada a uma conta de laboratório e serve como um repositório central para armazenar imagens. Uma imagem é guardada na galeria quando um educador escolhe exportar a partir de uma máquina virtual modelo de laboratório de sala de aula (VM). Cada vez que um educador faz alterações no modelo vM e exporta, novas versões da imagem são guardadas mantendo versões anteriores.

Os instrutores podem publicar uma versão de imagem a partir da galeria de imagens partilhadas quando criam um novo laboratório de sala de aula. Embora a galeria armazene várias versões de uma imagem, os educadores só podem selecionar a versão mais recente durante a criação de laboratório.

A galeria de imagens partilhadas é um recurso opcional que você pode não precisar imediatamente quando começar com apenas alguns laboratórios de sala de aula. No entanto, a utilização de uma galeria de imagens partilhadas tem muitos benefícios que são úteis à medida que se escala para ter mais laboratórios de sala de aula:

- **Permite-lhe guardar e gerir versões de uma imagem VM modelo**

    É útil criar uma imagem personalizada ou fazer alterações (software, configuração, e assim por diante) para uma imagem da galeria do Marketplace público.  Por exemplo, é comum que os educadores exijam a instalação de diferentes ferramentas de software. Em vez de exigir que os alunos instalam manualmente estes pré-requisitos por si só, diferentes versões da imagem VM do modelo podem ser exportadas para uma galeria de imagens partilhadas. Estas versões de imagem podem então ser usadas na criação de novos laboratórios de sala de aula.
- **Permite a partilha\reutilização de imagens vM modelo em laboratórios de sala de aula**

    Pode guardar e reutilizar uma imagem para não ter de configurar a imagem do zero cada vez que criar um novo laboratório de sala de aula. Por exemplo, se várias classes estão sendo oferecidas que precisam da mesma imagem, esta imagem só precisa ser criada uma vez e exportada para a galeria de imagens partilhadas para que possa ser partilhada em laboratórios de sala de aula.
- **Garante a disponibilidade de imagem através da replicação**

    Quando se guarda para a galeria de imagens partilhada de um laboratório de sala de aula, a sua imagem é automaticamente replicada para [outras regiões dentro da mesma geografia.](https://azure.microsoft.com/global-infrastructure/regions/) No caso de haver uma paragem para uma região, publicar a imagem no seu laboratório de sala de aula não é afetado, uma vez que uma réplica de imagem de outra região pode ser usada.  Publicar VMs de várias réplicas também pode ajudar no desempenho.

Para logicamente agrupar imagens partilhadas, você tem algumas opções:

- Criar várias galerias de imagem partilhadas. Cada conta de laboratório só pode ligar-se a uma galeria de imagens partilhada, pelo que esta opção também exigirá que crie várias contas de laboratório.
- Ou, pode usar uma única galeria de imagens partilhada que é partilhada por várias contas de laboratório. Neste caso, cada conta de laboratório só pode permitir as imagens aplicáveis aos laboratórios de sala de aula que contém.

## <a name="naming"></a>Atribuição de nomes

À medida que começa com os Serviços azure lab, recomendamos que estabeleça convenções de nomeação para grupos de recursos, contas de laboratório, laboratórios de sala de aula e a galeria de imagens partilhadas. Embora as convenções de nomeação que estabelecer sejam únicas às necessidades da sua organização, a tabela que se segue descreve as orientações gerais.

| Tipo de recurso | Função | Padrão sugerido | Exemplos |
| ------------- | ---- | ----------------- | -------- | 
| Grupo de recursos | Contém uma ou mais contas de laboratório e uma ou mais galerias de imagem partilhadas | \<organização ambiente de nome curto \> - \< \> -rg<ul><li>**Nome curto** da organização identifica o nome da organização que o grupo de recursos apoia</li><li>**Ambiente** identifica o ambiente para o recurso, como o Piloto ou a Produção</li><li>**Rg** significa o tipo de recursos: grupo de recursos.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Conta de laboratório | Contém um ou mais laboratórios | \<organização ambiente de nome curto \> - \< \> -la<ul><li>**Nome curto** da organização identifica o nome da organização que o grupo de recursos apoia</li><li>**Ambiente** identifica o ambiente para o recurso, como o Piloto ou a Produção</li><li>**La** significa o tipo de recurso: conta de laboratório.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Laboratório de sala de aula | Contém um ou mais VMs |\<\> - \< \> - identificador de tempo de \< nome de classe\><ul><li>**O nome** da classe identifica o nome da classe que o laboratório suporta.</li><li>**O prazo** identifica o prazo em que a classe é oferecida.</li>**O identificador** de educação identifica o educador que é dono do laboratório.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-primavera2019-johndoe |
| Galeria de imagens partilhadas | Contém uma ou mais versões de imagem VM | \<galeria de nome curto organização \> | Galeria contosouniversitylabs |

Para obter mais informações sobre a nomeação de outros recursos Do Iae, consulte [as convenções de nomeação para os recursos do Azure.](/azure/architecture/best-practices/naming-conventions)

## <a name="regionslocations"></a>Regiões\locais

Ao configurar os recursos dos seus Serviços De Laboratório Azure, é-lhe exigido que forneça uma região (ou localização) do centro de dados que acolherá o recurso. Aqui estão mais detalhes sobre como a região impacta cada um dos recursos envolvidos na criação de um laboratório.

### <a name="resource-group"></a>Grupo de recursos

A região especifica o centro de dados onde são armazenadas informações sobre o grupo de recursos. Os recursos azure contidos no grupo de recursos podem estar em diferentes regiões dos seus pais.

### <a name="lab-account"></a>Conta de laboratório

A localização de uma conta de laboratório indica a região onde este recurso existe.  

### <a name="classroom-lab"></a>Laboratório de sala de aula

A localização em que existe um laboratório de sala de aula varia com base nos seguintes fatores:

  - **A conta de laboratório é espartada para uma rede virtual (VNet)**
  
    Uma conta de laboratório pode ser [observada com um VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) quando estão na mesma região.  Quando uma conta de laboratório é espreitada com um VNet, os laboratórios de sala de aula são automaticamente criados na mesma região que a conta de laboratório e vNet.

    > [!NOTE]
    > Quando uma conta de laboratório é espreitada com um VNet, a configuração para permitir que o **criador do laboratório escolha** a localização do laboratório é desativada. Informações adicionais podem ser encontradas sobre este cenário no artigo: Permitir que o criador do [laboratório escolha o local para o laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Nenhum VNet é espreitado ***e*** os criadores de laboratório não estão autorizados a escolher a localização do laboratório.**
  
    Quando **não** há VNet espreitada com a conta de laboratório *e* os criadores de [laboratório **não** estão autorizados a escolher a localização do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), os laboratórios de sala de aula são automaticamente criados numa região que tem capacidade vM disponível.  Especificamente, a Azure Lab Services procura disponibilidade em [regiões que estão dentro da mesma geografia que a conta de laboratório.](https://azure.microsoft.com/global-infrastructure/regions)

  - **Nenhum VNet é espreitado ***e*** os criadores de laboratório estão autorizados a escolher a localização do laboratório**
       
    Quando **não** há VNet e os criadores de [laboratório são autorizados a escolher a localização do laboratório](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), os locais que podem ser selecionados pelo criador do laboratório são baseados na capacidade disponível.

> [!NOTE]
> Para ajudar a garantir que existe capacidade vm suficiente para uma região, é importante que você primeiro solicite capacidade através da conta de laboratório ou ao criar o laboratório.

Uma regra geral é definir a região de um recurso para uma que seja mais próxima dos seus utilizadores. Para laboratórios de sala de aula, isto significa criar o laboratório de sala de aula mais próximo dos seus alunos. Para cursos online onde os alunos estão localizados em todo o mundo, você precisa usar o seu melhor julgamento para criar um laboratório de sala de aula que esteja localizado centralmente. Ou dividir uma aula em vários laboratórios de sala de aula baseados na região dos seus alunos.

### <a name="shared-image-gallery"></a>Galeria de imagens partilhadas

A região indica a região de origem onde a primeira versão de imagem é armazenada antes de ser automaticamente replicada para as regiões-alvo.

## <a name="vm-sizing"></a>Tamanho VM

Quando administradores ou criadores de laboratório criam um laboratório de sala de aula, podem escolher entre os seguintes tamanhos de VM com base nas necessidades da sua sala de aula. Lembre-se que os tamanhos da computação disponíveis dependem da região em que a sua conta de laboratório está localizada em:

| Tamanho | Especificações | Série | Uso sugerido |
| ---- | ----- | ------ | ------------- |
| Pequeno| <ul><li>2 núcleos</li><li>3.5 GB DE CARNEIRO</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para linha de comando, abertura do navegador web, servidores web de tráfego baixo, bases de dados pequenas a médias. |
| Médio | <ul><li>4 Núcleos</li><li>7 GB DE CARNEIRO</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para bases de dados relacionais, caches na memória e análise. |
| Médio (virtualização aninhada) | <ul><li>4 Núcleos</li><li>16 GB DE CARNEIRO</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Este tamanho é mais adequado para bases de dados relacionais, caches na memória e análise.
| Grande | <ul><li>8 núcleos</li><li>16 GB DE CARNEIRO</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápido, melhor desempenho do disco local, grandes bases de dados, grandes caches de memória.  Este tamanho também suporta a virtualização aninhada. |
| Grande (Virtualização Aninhada) | <ul><li>8 núcleos</li><li>16 GB DE CARNEIRO</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápido, melhor desempenho do disco local, grandes bases de dados, grandes caches de memória. |
| Pequena GPU (Visualização) | <ul><li>6 Núcleos</li><li>56 GB DE CARNEIRO</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Este tamanho é mais adequado para visualização remota, streaming, gaming, codificação usando quadros como OpenGL e DirectX. |
| Pequena GPU (Computação) | <ul><li>6 Núcleos</li><li>56 GB DE CARNEIRO</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Este tamanho é mais adequado para aplicações intensivas em computador, como Inteligência Artificial e Deep Learning. |
| Média GPU (Visualização) | <ul><li>12 Núcleos</li><li>112 GB DE RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Este tamanho é mais adequado para visualização remota, streaming, gaming, codificação usando quadros como OpenGL e DirectX. |

## <a name="manage-identity"></a>Gerir a identidade

Utilizando o controlo de [acesso baseado no papel do Azure,](https://docs.microsoft.com/azure/role-based-access-control/overview)as seguintes funções podem ser atribuídas para dar acesso a contas de laboratório e laboratórios de sala de aula:

- **Proprietário da conta de laboratório**

    O administrador que cria a conta de laboratório é automaticamente adicionado ao papel de **Proprietário** da conta de laboratório.  Um administrador que tenha atribuído o papel de **Proprietário** pode:
     - Mude as definições da conta do laboratório.
     - Dê a outros administradores acesso à conta de laboratório como proprietários ou contribuintes.
     - Dar aos educadores acesso a laboratórios de sala de aula como criadores, proprietários ou colaboradores.
     - Crie e gerencie todos os laboratórios de sala de aula dentro da conta do laboratório.

- **Contribuinte de conta de laboratório**

    Um administrador que tenha atribuído o papel de **Contribuinte** pode:
    - Mude as definições da conta do laboratório.
    - Crie e gerencie todos os laboratórios dentro da conta do laboratório.

    No entanto, *não podem* dar a outros utilizadores acesso a contas de laboratório ou laboratórios de sala de aula.

- **Criador de laboratório de sala de aula**

    Para criar laboratórios de sala de aula dentro de uma conta de laboratório, um educador deve ser membro do papel de Criador de **Laboratório.**  Quando um educador cria um laboratório de sala de aula, são automaticamente adicionados como donos do laboratório.  Consulte o tutorial sobre como adicionar um utilizador ao papel de Criador de [ **Laboratório** ](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Proprietário de laboratório de sala de aula\contribuinte**
  
    Um educador pode ver e alterar as configurações de um laboratório de sala de aula quando são membros do papel de **Proprietário** ou **Colaborador** de um laboratório; devem também ser membros do papel de **Leitor** da conta de laboratório.

    Uma diferença fundamental entre as funções de **Proprietário** e **Colaborador** de um laboratório é que um colaborador *não pode* dar a outros utilizadores acesso para gerir o laboratório - apenas os proprietários podem dar a outros utilizadores acesso para gerir o laboratório.

    Além disso, um educador *não pode* criar novos laboratórios de sala de aula a menos que também seja membro do papel de Criador de **Laboratório.**

- **Galeria de imagens partilhadas**

    Quando você anexa uma galeria de imagem partilhada a uma conta de laboratório, os proprietários de contas de laboratório\colaboradores e criadores de laboratório\os colaboradores\os colaboradores são automaticamente dados acesso a visualização e economizam imagens na galeria.

Aqui ficam algumas dicas para ajudar na atribuição de funções:
   - Normalmente, apenas os administradores devem ser membros das funções de **Proprietário** ou **Colaborador** de uma conta de laboratório; pode ter mais do que um proprietário\contribuinte.
   - Dar a um educador a capacidade de criar novos laboratórios de sala de aula e gerir os laboratórios que criam; só precisa atribuir acesso ao papel de Criador de **Laboratório.**
   - Dar a um educador a capacidade de gerir laboratórios específicos em sala de aula, mas *não* a capacidade de criar novos laboratórios; deve atribuir acesso ao papel **proprietário** ou **colaborador** de cada um dos laboratórios de sala de aula que irão gerir.  Por exemplo, talvez queira permitir que um professor e um assistente de ensino sejam co-donos de um laboratório de sala de aula.  Consulte o guia sobre como [adicionar um utilizador como proprietário a um laboratório de sala de aula](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Preços

### <a name="azure-lab-services"></a>Azure Lab Services

Os preços dos Serviços De Laboratório Azure são descritos no seguinte artigo: Preços dos [Serviços De Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

Também precisa de considerar os preços da galeria de imagens partilhadas se pretende utilizá-lo para armazenar e gerir versões de imagem. 

### <a name="shared-image-gallery"></a>Galeria de imagens partilhadas

Criar uma galeria de imagens partilhadae anexá-la à sua conta de laboratório é gratuita. Os custos não são incorridos até guardar uma versão de imagem para a galeria. Tipicamente, o preço para usar uma galeria de imagens partilhadas é bastante insignificante, mas é importante entender como é calculado, uma vez que não está incluído nos preços dos Serviços de Laboratório Azure.  

#### <a name="storage-charges"></a>Encargos de armazenamento

Para armazenar versões de imagem, uma galeria de imagens partilhada utiliza discos geridos por HDD padrão. O tamanho do disco gerido pelo HDD que é usado depende do tamanho da versão de imagem que está a ser armazenada. Consulte o seguinte artigo para ver os preços: Preços dos [discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Taxas de replicação e rede de egress

Quando se guarda uma versão de imagem utilizando uma máquina virtual de modelo de laboratório de sala de aula (VM), a Azure Lab Services armazena-a primeiro numa região de origem e, em seguida, replica automaticamente a versão de imagem de origem para uma ou mais regiões-alvo. É importante notar que o Azure Lab Services replica automaticamente a versão de imagem de origem para todas as [regiões-alvo dentro da geografia](https://azure.microsoft.com/global-infrastructure/regions/) onde o laboratório de sala de aula está localizado. Por exemplo, se o seu laboratório de sala de aula estiver na geografia dos EUA, uma versão de imagem é replicada para cada uma das oito regiões que existem nos EUA.

Uma carga de saída de rede ocorre quando uma versão de imagem é replicada da região de origem para regiões-alvo adicionais. O montante cobrado baseia-se no tamanho da versão de imagem quando os dados da imagem são inicialmente transferidos para fora da região de origem.  Para obter detalhes sobre preços, consulte o seguinte artigo: Detalhes de preços de [largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

[Soluções educativas](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) os clientes podem ser dispensados do pagamento de taxas de egress. Fale com o seu gestor de conta para saber mais.  Para mais informações, consulte a secção **de PERGUNTAS FREQUENTEs** no documento vinculado, especificamente a questão "Que programas de transferência de dados existem para clientes académicos e como me qualifico?".

#### <a name="pricing-example"></a>Exemplo de preços

Para recapitular os preços acima descritos, vamos olhar para um exemplo de salvar a nossa imagem VM modelo para galeria de imagens partilhada. Assuma os seguintes cenários:

- Tem uma imagem VM personalizada.
- Estás a guardar duas versões da imagem.
- O seu laboratório está nos E.U.A., que tem um total de oito regiões.
- Cada versão de imagem tem 32 GB de tamanho; como resultado, o preço do disco gerido pelo HDD é de $1,54 por mês.

O custo total é estimado como:

Número de imagens × número de versões × número de réplicas × preço do disco gerido

Neste exemplo, o custo é:

1 imagem personalizada (32 GB) x 2 versões x 8 regiões dos EUA x $1,54 = $24,64 por mês

#### <a name="cost-management"></a>Gestão de custos

É importante para o administrador de conta de laboratório gerir os custos, apagando rotineiramente versões de imagem desnecessárias da galeria. 

Não deve excluir a replicação a regiões específicas como forma de reduzir os custos (esta opção existe na galeria de imagens partilhadas). As alterações de replicação podem ter efeitos adversos na capacidade do Serviço de Laboratório azure de publicar VMs a partir de imagens guardadas dentro de uma galeria de imagens partilhadas.

## <a name="next-steps"></a>Passos seguintes

Consulte o tutorial para obter instruções passo a passo para criar uma conta de laboratório e um laboratório: [Definir Guia](tutorial-setup-lab-account.md)
