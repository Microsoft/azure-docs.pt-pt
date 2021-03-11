---
title: Partilhar imagens VM com Galerias Partilhadas
description: Aprenda a usar galerias de imagens partilhadas para partilhar imagens Linux VM em toda a sua organização.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 10/14/2020
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 225aca8c4695db33e504a5857acb856f4e01d1f1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551067"
---
# <a name="shared-image-galleries-overview"></a>Visão geral das Galerias de Imagem Partilhadas

O Shared Image Gallery é um serviço que ajuda a criar a estrutura e a organização à volta das imagens. As Galerias de Imagem Partilhadas fornecem:

- Replicação global de imagens.
- Versão e agrupamento de imagens para uma gestão mais fácil.
- Imagens altamente disponíveis com contas de Armazenamento Redundante (ZRS) em regiões que suportam Zonas de Disponibilidade. O ZRS oferece uma melhor resiliência contra falhas zonais.
- Suporte de armazenamento premium (Premium_LRS).
- Partilha entre subscrições, e até mesmo entre inquilinos do Ative Directory (AD), utilizando o Azure RBAC.
- Escalar as suas implementações com réplicas de imagem em cada região.

Utilizando uma Galeria de Imagens Partilhadas, pode partilhar as suas imagens com diferentes utilizadores, diretores de serviço ou grupos de AD dentro da sua organização. As imagens partilhadas podem ser replicadas em várias regiões, para uma escala mais rápida das suas implementações.

Uma imagem é uma cópia de um VM completo (incluindo quaisquer discos de dados anexados) ou apenas do disco DE, dependendo da forma como é criado. Quando se cria um VM a partir da imagem, uma cópia dos VHDs na imagem é usada para criar os discos para o novo VM. A imagem permanece armazenada e pode ser usada uma e outra vez para criar novos VMs.

Se tiver um grande número de imagens que precisa de manter, e quiser disponibilizá-las em toda a sua empresa, pode utilizar uma Galeria de Imagens Partilhadas como repositório. 

A funcionalidade Image Gallery partilhada tem vários tipos de recursos:

| Recurso | Descrição|
|----------|------------|
| **Fonte de imagem** | Este é um recurso que pode ser usado para criar uma **versão de imagem** numa galeria de imagens. Uma fonte de imagem pode ser um Azure VM existente que é [generalizado ou especializado](#generalized-and-specialized-images), uma imagem gerida, um instantâneo, um VHD ou uma versão de imagem em outra galeria de imagens. |
| **Galeria de imagens** | Tal como o Azure Marketplace, uma **galeria de imagens** é um repositório para gerir e partilhar imagens, mas você controla quem tem acesso. |
| **Definição de imagem** | As definições de imagem são criadas dentro de uma galeria e transportam informações sobre a imagem e requisitos para a sua utilização interna. Isto inclui se a imagem é Windows ou Linux, notas de lançamento e requisitos mínimos e máximo de memória. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão de imagem** é o que se usa para criar um VM quando se utiliza uma galeria. Pode ter várias versões de uma imagem necessária para o seu ambiente. Como uma imagem gerida, quando se usa uma **versão de imagem** para criar um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes. |

<br>

![Gráfico mostrando como pode ter várias versões de uma imagem na sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definições de imagem

As definições de imagem são um agrupamento lógico para versões de uma imagem. A definição de imagem contém informações sobre o porquê da imagem ter sido criada, para que é o SISTEMA, e outras informações sobre a utilização da imagem. Uma definição de imagem é como um plano para todos os detalhes em torno da criação de uma imagem específica. Não se implanta um VM a partir de uma definição de imagem, mas a partir das versões de imagem criadas a partir da definição.

Existem três parâmetros para cada definição de imagem que são usados em combinação - **Publisher,** **Offer** e **SKU**. Estes são usados para encontrar uma definição de imagem específica. Pode ter versões de imagem que partilham um ou dois, mas não os três valores.  Por exemplo, aqui estão três definições de imagem e os seus valores:

|Definição da Imagem|Publisher|Oferta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finance|Back-end|
|myImage2|Contoso|Finance|Front-end|
|myImage3|Testar|Finance|Front-end|

Todos estes três têm conjuntos únicos de valores. O formato é semelhante ao que pode especificar atualmente as imagens de editor, oferta e SKU para [Azure Marketplace](./windows/cli-ps-findimage.md) em Azure PowerShell para obter a versão mais recente de uma imagem do Marketplace. Cada definição de imagem precisa de ter um conjunto único destes valores.

Os seguintes parâmetros determinam quais os tipos de versões de imagem que podem conter:

- Estado do sistema operativo - Pode definir o estado de SO para [generalizado ou especializado](#generalized-and-specialized-images). Este campo é obrigatório.
- Sistema operativo - pode ser Windows ou Linux. Este campo é obrigatório.
-   Geração Hyper-V - especifique se a imagem foi criada a partir de uma geração 1 ou [geração 2](generation-2.md) Hyper-V VHD. O padrão é a geração 1.


Seguem-se outros parâmetros que podem ser definidos na definição de imagem para que possa acompanhar mais facilmente os seus recursos:

- Descrição - utilize a descrição para dar informações mais detalhadas sobre a existência da definição de imagem. Por exemplo, pode ter uma definição de imagem para o seu servidor frontal que tem a aplicação pré-instalada.
- Eula - pode ser usado para apontar para um contrato de licença de utilizador final específico para a definição de imagem.
- Privacy Statement and Release notes - armazenar notas de lançamento e declarações de privacidade no armazenamento Azure e fornecer um URI para aceder a elas como parte da definição de imagem.
- Data de fim de vida - anexe uma data de fim de vida à sua definição de imagem para poder utilizar a automatização para eliminar definições de imagem antigas.
- Tag - pode adicionar tags quando criar a definição de imagem. Para obter mais informações sobre tags, consulte [Usando tags para organizar os seus recursos](../azure-resource-manager/management/tag-resources.md)
- Recomendações mínimas e máximas de vCPU e memória - se a sua imagem tiver vCPU e recomendações de memória, pode anexar essa informação à sua definição de imagem.
- Tipos de disco não permitidos - pode fornecer informações sobre as necessidades de armazenamento para o seu VM. Por exemplo, se a imagem não for adequada para discos HDD padrão, adicione-os à lista de não-adesecedores.
- Informação do plano de compra para imagens do Marketplace - `-PurchasePlanPublisher` `-PurchasePlanName` , e `-PurchasePlanProduct` . Para obter mais informações sobre informações sobre planos de compra, consulte [encontrar imagens no Azure Marketplace](./windows/cli-ps-findimage.md) e fornecer informações do [plano de compra do Azure Marketplace ao criar imagens.](marketplace-images.md)


## <a name="image-versions"></a>Versões de imagem

Uma **versão de imagem** é o que se usa para criar um VM. Pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se utiliza uma **versão de imagem** para criar um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes.

As propriedades de uma versão de imagem são:

- Número da versão. Isto é usado como o nome da versão de imagem. Está sempre no formato: MajorVersion.MinorVersion.Patch. Quando especifica para usar o **mais recente** ao criar um VM, a imagem mais recente é escolhida com base na maiorversão, em seguida, MinorVersion, em seguida, Patch. 
- A fonte. A fonte pode ser um VM, disco gerido, instantâneo, imagem gerida ou outra versão de imagem. 
- Excluir das últimas. Pode evitar que uma versão seja usada como a versão mais recente da imagem. 
- Fim da vida. Data após a qual os VM não podem ser criados a partir desta imagem.


## <a name="generalized-and-specialized-images"></a>Imagens generalizadas e especializadas

Existem dois estados do sistema operativo apoiados pela Shared Image Gallery. Normalmente, as imagens requerem que o VM usado para criar a imagem tenha sido generalizado antes de tirar a imagem. Generalizar é um processo que remove informações específicas da máquina e do utilizador do VM. Para o Windows, a ferramenta Sysprep é utilizada. Para o Linux, pode utilizar [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou `-deprovision+user` parâmetros.

Os VM especializados não passaram por um processo para remover informações e contas específicas da máquina. Além disso, os VMs criados a partir de imagens especializadas não têm uma `osProfile` associada a elas. Isto significa que as imagens especializadas terão algumas limitações para além de alguns benefícios.

- VMs e conjuntos de escala criados a partir de imagens especializadas podem ser em funcionamento mais rápido. Porque são criados a partir de uma fonte que já passou pela primeira bota, os VMs criados a partir destas imagens iniciam-se mais rapidamente.
- As contas que poderiam ser usadas para iniciar sessão no VM também podem ser usadas em qualquer VM criada usando a imagem especializada que é criada a partir desse VM.
- Os VMs terão o **nome de computador** do VM de onde a imagem foi tirada. Deve alterar o nome do computador para evitar colisões.
- É `osProfile` assim que algumas informações sensíveis são transmitidas ao VM, utilizando `secrets` . Isto pode causar problemas usando KeyVault, WinRM e outras funcionalidades que usam `secrets` no `osProfile` . Em alguns casos, pode utilizar identidades de serviço geridas (MSI) para contornar estas limitações.

## <a name="regional-support"></a>Apoio Regional

Todas as regiões públicas podem ser regiões-alvo, mas para replicar para a Austrália Central e Austrália Central 2 você precisa ter a sua subscrição adicionada à lista de permitis. Para solicitar que uma subscrição seja adicionada à lista de autorizações, aceda a: https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#regional-support

## <a name="limits"></a>Limites 

Existem limites, por subscrição, para a implantação de recursos utilizando galerias de imagem partilhadas:
- 100 galerias de imagem partilhadas, por subscrição, por região
- 1.000 definições de imagem, por subscrição, por região
- 10.000 versões de imagem, por subscrição, por região
- 10 réplicas de versão de imagem, por subscrição, por região
- Qualquer disco ligado à imagem deve ser inferior ou igual a 1TB de tamanho

Para obter mais informações, [consulte a utilização do recurso contra limites,](../networking/check-usage-against-limits.md) por exemplo, sobre como verificar a sua utilização atual.
 
## <a name="scaling"></a>Dimensionamento
A Galeria de Imagens Partilhada permite especificar o número de réplicas que pretende que o Azure guarde as imagens. Isto ajuda em cenários de implantação multi-VM, uma vez que as implementações de VM podem ser espalhadas para diferentes réplicas reduzindo a possibilidade de processamento de criação de instância ser estrangulado devido à sobrecarga de uma única réplica.

Com a Shared Image Gallery, pode agora implementar até 1.000 vM instâncias numa escala de máquina virtual (até 600 com imagens geridas). As réplicas de imagem proporcionam um melhor desempenho de implantação, fiabilidade e consistência.  Pode definir uma contagem de réplicas diferente em cada região alvo, com base nas necessidades de escala para a região. Uma vez que cada réplica é uma cópia profunda da sua imagem, isto ajuda a escalar as suas implementações linearmente com cada réplica extra. Embora compreendamos que não há duas imagens ou regiões iguais, eis a nossa orientação geral sobre como usar réplicas numa região:

- Para implementações de conjunto de balança de máquina não virtual - Para cada 20 VMs que cria simultaneamente, recomendamos que mantenha uma réplica. Por exemplo, se estiver a criar 120 VMs simultaneamente usando a mesma imagem numa região, sugerimos que guarde pelo menos 6 réplicas da sua imagem. 
- Para implementações de conjunto de escala de máquina virtual - Para cada escala definida com até 600 instâncias, recomendamos que mantenha pelo menos uma réplica. Por exemplo, se estiver a criar conjuntos de 5 escalas simultaneamente, cada um com 600 VM de instâncias usando a mesma imagem numa única região, sugerimos que guarde pelo menos 5 réplicas da sua imagem. 

Recomendamos sempre que exercê o número de réplicas em excesso devido a fatores como tamanho de imagem, conteúdo e tipo de SO.

![Gráfico mostrando como pode escalar imagens](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Torne as suas imagens altamente disponíveis

[O armazenamento redundante da Zona Azure (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) proporciona resiliência contra uma falha da Zona de Disponibilidade na região. Com a disponibilidade geral da Galeria de Imagens Partilhadas, pode optar por armazenar as suas imagens em contas ZRS em regiões com Zonas de Disponibilidade. 

Também pode escolher o tipo de conta para cada uma das regiões-alvo. O tipo de conta de armazenamento predefinido é Standard_LRS, mas pode escolher Standard_ZRS para regiões com Zonas de Disponibilidade. Consulte [aqui](../storage/common/storage-redundancy.md)a disponibilidade regional de ZRS.

![ZRS de exibição gráfica](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicação
A Shared Image Gallery também permite replicar as suas imagens para outras regiões do Azure automaticamente. Cada versão Imagem Partilhada pode ser replicada em diferentes regiões, dependendo do que faz sentido para a sua organização. Um exemplo é replicar sempre a mais recente imagem em várias regiões, enquanto todas as versões mais antigas só estão disponíveis em 1 região. Isto pode ajudar a economizar nos custos de armazenamento das versões De Imagem Partilhada. 

As regiões para ver uma imagem partilhada são replicadas para serem atualizadas após o tempo de criação. O tempo que leva para replicar em diferentes regiões depende da quantidade de dados que estão a ser copiados e do número de regiões a que a versão é replicada. Isto pode levar algumas horas em alguns casos. Enquanto a replicação está acontecendo, você pode ver o estado de replicação por região. Uma vez que a replicação da imagem esteja completa numa região, pode então implementar um VM ou um conjunto de escalas utilizando essa versão de imagem na região.

![Gráfico mostrando como pode replicar imagens](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

Como a Galeria de Imagens Partilhada, Definição de Imagem e Versão Image são todos recursos, podem ser partilhados usando os controlos Azure RBAC nativos incorporados. Utilizando o Azure RBAC, pode partilhar estes recursos com outros utilizadores, diretores de serviços e grupos. Pode até partilhar o acesso a indivíduos fora do inquilino onde foram criados. Uma vez que um utilizador tenha acesso à versão Imagem Partilhada, pode implementar um VM ou um Conjunto de Escala de Máquina Virtual.  Aqui está a matriz de partilha que ajuda a entender a que o utilizador tem acesso:

| Partilhado com o Utilizador     | Galeria de Imagens Partilhada | Definição da Imagem | Versão da imagem |
|----------------------|----------------------|--------------|----------------------|
| Galeria de Imagens Partilhada | Yes                  | Yes          | Yes                  |
| Definição da Imagem     | No                   | Yes          | Yes                  |

Recomendamos a partilha ao nível da Galeria para obter a melhor experiência. Não recomendamos a partilha de versões de imagem individuais. Para obter mais informações sobre o Azure RBAC, consulte [as funções De Atribuição Azure](../role-based-access-control/role-assignments-portal.md).

As imagens também podem ser partilhadas, em escala, mesmo através de inquilinos usando um registo de app multi-inquilino. Para obter mais informações sobre a partilha de imagens entre os inquilinos, consulte "Partilhar imagens VM da galeria através dos inquilinos da Azure" utilizando o [Azure CLI](./linux/share-images-across-tenants.md) ou [o PowerShell](./windows/share-images-across-tenants.md).

## <a name="billing"></a>Faturação
Não existe qualquer custo extra para a utilização do serviço Image Gallery Partilhado. Serão cobrados os seguintes recursos:
-   Custos de armazenamento de armazenamento de cada réplica. O custo de armazenamento é cobrado como instantâneo e baseia-se no tamanho ocupado da versão de imagem, no número de réplicas da versão de imagem e no número de regiões a que a versão é replicada. 
-   A rede cobra taxas para a replicação da primeira versão de imagem da região origem para as regiões replicadas. As réplicas subsequentes são tratadas dentro da região, pelo que não há encargos adicionais. 

Por exemplo, digamos que tem uma imagem de um disco oss de 127 GB, que ocupa apenas 10GB de armazenamento, e um disco de dados vazio de 32 GB. O tamanho ocupado de cada imagem seria de apenas 10 GB. A imagem é replicada em 3 regiões e cada região tem duas réplicas. Haverá seis instantâneos totais, cada um com 10GB. Será cobrado o custo de armazenamento de cada instantâneo com base no tamanho ocupado de 10 GB. Pagará taxas de saída de rede para a primeira réplica ser copiada para as duas regiões adicionais. Para obter mais informações sobre o preço dos instantâneos em cada região, consulte [os preços dos discos geridos.](https://azure.microsoft.com/pricing/details/managed-disks/) Para obter mais informações sobre a saída da rede, consulte [os preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="updating-resources"></a>Atualizar recursos

Uma vez criado, pode fazer algumas alterações nos recursos da galeria de imagens. Estes limitam-se a:
 
Galeria de imagens partilhada:
- Description

Definição de imagem:
- VCPUs recomendados
- Memória recomendada
- Description
- Data de fim de vida

Versão de imagem:
- Contagem de réplicas regionais
- Regiões-alvo
- Excluir das últimas
- Data de fim de vida

## <a name="sdk-support"></a>Suporte de SKDs

Os seguintes SDKs apoiam a criação de Galerias de Imagem Partilhada:

- [.NET](/dotnet/api/overview/azure/virtualmachines/management)
- [Java](/java/azure/)
- [Node.js](/javascript/api/@azure/arm-compute)
- [Python](/python/api/overview/azure/virtualmachines)
- [Ir](/azure/go/)

## <a name="templates"></a>Modelos

Pode criar o recurso Image Gallery partilhado utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

* [Como posso listar todos os recursos da Galeria de Imagem Partilhada através de subscrições?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Posso mover a minha imagem existente para a galeria de imagens partilhada?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Posso criar uma versão de imagem a partir de um disco especializado?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Posso mover o recurso da Galeria de Imagens Partilhada para uma subscrição diferente depois de ter sido criada?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Posso replicar as minhas versões de imagem através de nuvens como Azure China 21Vianet ou Azure Germany ou Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Posso replicar as minhas versões de imagem através de subscrições?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Posso partilhar versões de imagem em inquilinos da AD Azure?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo demora a replicar versões de imagem em todas as regiões-alvo?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual é a diferença entre a região de origem e a região-alvo?](#what-is-the-difference-between-source-region-and-target-region)
* [Como especifico a região de origem ao criar a versão de imagem?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Como especifiquei o número de réplicas de versão de imagem a criar em cada região?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Posso criar a galeria de imagens partilhada num local diferente daquele para a definição de imagem e versão de imagem?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quais são as acusações de utilização da Galeria de Imagens Partilhadas?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Que versão API devo usar para criar a Galeria de Imagem Partilhada e a Definição de Imagem e a Versão de Imagem?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Que versão API devo usar para criar VM compartilhado ou escala de máquina virtual definida fora da versão de imagem?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [Posso atualizar o meu Conjunto de Escala de Máquina Virtual criado usando uma imagem gerida para utilizar imagens da Galeria de Imagens Partilhadas?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Como posso listar todos os recursos da Galeria de Imagem Partilhada através de subscrições?

Para listar todos os recursos da Galeria de Imagens Partilhadas através de subscrições a que tem acesso no portal Azure, siga os passos abaixo:

1. Abra o [portal do Azure](https://portal.azure.com).
1. Percorra a página e selecione **Todos os recursos**.
1. Selecione todas as subscrições sob as quais gostaria de listar todos os recursos.
1. Procure recursos de galeria de **imagens partilhadas,**.
  
Para listar todos os recursos da Galeria de Imagens Partilhadas através de subscrições às para as as que tem permissões, utilize o seguinte comando no CLI Azure:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Para obter mais informações, consulte **Gerir os recursos** da galeria utilizando o [Azure CLI](update-image-resources-cli.md) ou [o PowerShell.](update-image-resources-powershell.md)

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Posso mover a minha imagem existente para a galeria de imagens partilhada?
 
Sim. Existem 3 cenários baseados nos tipos de imagens que pode ter.

 Cenário 1: Se tiver uma imagem gerida, então pode criar uma definição de imagem e versão de imagem a partir dela. Para obter mais informações, consulte **Migrar de uma imagem gerida para uma versão de imagem** utilizando o [Azure CLI](image-version-managed-image-cli.md) ou [PowerShell](image-version-managed-image-powershell.md).

 Cenário 2: Se tiver uma imagem não gerida, pode criar uma imagem gerida a partir dela e, em seguida, criar uma definição de imagem e versão de imagem a partir dela. 

 Cenário 3: Se tiver um VHD no seu sistema de ficheiros local, então precisa de carregar o VHD para uma imagem gerida, então pode criar uma definição de imagem e versão de imagem a partir dele.

- Se o VHD for de um VM do Windows, consulte [o Upload a VHD](./windows/upload-generalized-managed.md).
- Se o VHD for para um Linux VM, consulte [upload a VHD](./linux/upload-vhd.md#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Posso criar uma versão de imagem a partir de um disco especializado?

Sim, pode criar um VM a partir de uma imagem especializada usando o [CLI,](vm-specialized-image-version-cli.md) [PowerShell](vm-specialized-image-version-powershell.md)ou API. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Posso mover o recurso da Galeria de Imagens Partilhada para uma subscrição diferente depois de ter sido criada?

Não, não pode mover o recurso da galeria de imagens partilhada para uma subscrição diferente. Pode replicar as versões de imagem na galeria para outras regiões ou copiar uma imagem de outra galeria utilizando o [Azure CLI](image-version-another-gallery-cli.md) ou [o PowerShell](image-version-another-gallery-powershell.md).

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Posso replicar as minhas versões de imagem através de nuvens como Azure China 21Vianet ou Azure Germany ou Azure Government Cloud?

Não, não se pode replicar versões de imagem através das nuvens.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Posso replicar as minhas versões de imagem através de subscrições?

Não, pode replicar as versões de imagem em todas as regiões numa subscrição e usá-la noutras subscrições através do RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Posso partilhar versões de imagem em inquilinos da AD Azure? 

Sim, você pode usar o RBAC para compartilhar indivíduos entre inquilinos. Mas, para partilhar em escala, veja "Partilhe imagens de galerias através dos inquilinos do Azure" usando [o PowerShell](./windows/share-images-across-tenants.md) ou [o CLI.](./linux/share-images-across-tenants.md)

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Quanto tempo demora a replicar versões de imagem em todas as regiões-alvo?

O tempo de replicação da versão de imagem depende inteiramente do tamanho da imagem e do número de regiões a que está a ser replicada. No entanto, como uma boa prática, recomenda-se que mantenha a imagem pequena, e as regiões de origem e alvo próximas para obter os melhores resultados. Pode verificar o estado da replicação utilizando a bandeira -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Qual é a diferença entre a região de origem e a região-alvo?

A região de origem é a região em que a sua versão de imagem será criada, e as regiões-alvo são as regiões em que uma cópia da sua versão de imagem será armazenada. Para cada versão de imagem, só pode ter uma região de origem. Além disso, certifique-se de que passa a localização da região de origem como uma das regiões-alvo quando criar uma versão de imagem.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Como especifico a região de origem ao criar a versão de imagem?

Ao criar uma versão de imagem, pode utilizar a etiqueta **de localização** em CLI e a tag **-Localização** em PowerShell para especificar a região de origem. Certifique-se de que a imagem gerida que está a usar como imagem base para criar a versão de imagem encontra-se no mesmo local que a localização em que pretende criar a versão de imagem. Além disso, certifique-se de que passa a localização da região de origem como uma das regiões-alvo quando criar uma versão de imagem.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Como especifiquei o número de réplicas de versão de imagem a criar em cada região?

Há duas formas de especificar o número de réplicas de versão de imagem a serem criadas em cada região:
 
1. A contagem de réplicas regionais que especifica o número de réplicas que pretende criar por região. 
2. A contagem comum de réplicas que é o padrão por região conta no caso de não ser especificada a contagem de réplicas regionais. 

Para especificar a contagem de réplicas regionais, passe a localização juntamente com o número de réplicas que pretende criar nessa região: "South Central US=2". 

Se a contagem de réplicas regionais não for especificada em cada local, então o número padrão de réplicas será a contagem de réplicas comum que especificou. 

Para especificar a contagem comum de réplicas no CLI, utilize o argumento **da contagem de réplicas** no `az sig image-version create` comando.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Posso criar a galeria de imagens partilhada num local diferente daquele para a definição de imagem e versão de imagem?

Sim, é possível. Mas, como uma boa prática, encorajamo-lo a manter o grupo de recursos, galeria de imagens partilhada, definição de imagem e versão de imagem no mesmo local.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quais são as acusações de utilização da Galeria de Imagens Partilhadas?

Não existem encargos para a utilização do serviço De Imagem Partilhada, exceto os custos de armazenamento para armazenar as versões de imagem e os encargos de saída de rede para replicar as versões de imagem da região de origem para as regiões-alvo.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Que versão API devo usar para criar a Galeria de Imagem Partilhada e a Definição de Imagem e a Versão de Imagem?

Para trabalhar com galerias de imagem partilhadas, definições de imagem e versões de imagem, recomendamos que utilize a versão API 2018-06-01. O Armazenamento Redundante de Zona (ZRS) requer a versão 2019-03-01 ou posterior.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Que versão API devo usar para criar VM compartilhado ou escala de máquina virtual definida fora da versão de imagem?

Para implementações de VM e Conjunto de Escala de Máquina Virtual utilizando uma versão de imagem, recomendamos que utilize a versão API 2018-04-01 ou superior.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Posso atualizar o meu Conjunto de Escala de Máquina Virtual criado usando uma imagem gerida para utilizar imagens da Galeria de Imagens Partilhadas?

Sim, pode atualizar a referência de imagem definida em escala de uma imagem gerida para uma imagem de galeria de imagens partilhada, desde que o tipo de SO, geração Hyper-V e o layout do disco de dados correspondam entre as imagens.

## <a name="troubleshoot-shared-image-gallery-issues"></a>Problemas com questões da Galeria de Imagem Partilhada
Se tiver problemas com a realização de quaisquer operações nos recursos da galeria de imagens partilhadas, consulte a lista de erros comuns no [guia de resolução de problemas](troubleshooting-shared-images.md).

Além disso, pode publicar e marcar a sua pergunta `azure-virtual-machines-images` no Q&[A](/answers/topics/azure-virtual-machines-images.html).

## <a name="next-steps"></a>Passos seguintes

Saiba como implementar imagens partilhadas utilizando o [Azure CLI](shared-images-cli.md) ou [o PowerShell](shared-images-powershell.md).
