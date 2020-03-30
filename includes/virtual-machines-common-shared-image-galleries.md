---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a477114bda7d138a6860d21f2fad75e27d968833
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117080"
---
A Shared Image Gallery é um serviço que o ajuda a construir estrutura e organização em torno das suas imagens geridas. Galerias de Imagem Partilhada fornecem:

- Geriu a replicação global das imagens.
- Versão e agrupamento de imagens para uma gestão mais fácil.
- Imagens altamente disponíveis com contas de Armazenamento Redundante da Zona (ZRS) em regiões que suportam Zonas de Disponibilidade. O ZRS oferece uma melhor resiliência contra falhas zonais.
- Partilha de subscrições, e até entre inquilinos do Ative Directory (AD), utilizando o RBAC.
- Dimensionando as suas implementações com réplicas de imagem em cada região.

Utilizando uma Galeria de Imagem Partilhada, pode partilhar as suas imagens com diferentes utilizadores, diretores de serviço ou grupos de AD dentro da sua organização. Imagens partilhadas podem ser replicadas em várias regiões, para uma escala mais rápida das suas implementações.

Uma imagem gerida é uma cópia de um VM completo (incluindo quaisquer discos de dados anexados) ou apenas do disco OS, dependendo da forma como cria a imagem. Quando se cria um VM a partir da imagem, uma cópia dos VHDs na imagem é usada para criar os discos para o novo VM. A imagem gerida permanece no armazenamento e pode ser usada uma e outra vez para criar novos VMs.

Se tiver um grande número de imagens geridas que precisa de manter e gostaria de as disponibilizar em toda a sua empresa, pode utilizar uma Galeria de Imagem Partilhada como um repositório que facilita a partilha das suas imagens. 

A funcionalidade Da Galeria de Imagem Partilhada tem vários tipos de recursos:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerida** | Uma imagem básica que pode ser usada sozinha ou usada para criar uma versão de **imagem** numa galeria de imagens. As imagens geridas são criadas a partir de VMs [generalizados.](#generalized-and-specialized-images) Uma imagem gerida é um tipo especial de VHD que pode ser usado para fazer vários VMs e agora pode ser usado para criar versões de imagem partilhada. |
| **Instantâneo** | Uma cópia de um VHD que pode ser usado para fazer uma versão de **imagem**. Os instantâneos podem ser retirados de um VM [especializado](#generalized-and-specialized-images) (que não foi generalizado) e depois utilizados sozinhos ou com instantâneos de discos de dados, para criar uma versão de imagem especializada.
| **Galeria de imagens** | Tal como o Azure Marketplace, uma galeria de **imagens** é um repositório para gerir e partilhar imagens, mas controla quem tem acesso. |
| **Definição de imagem** | As imagens são definidas dentro de uma galeria e transportam informações sobre a imagem e requisitos para a sua utilização dentro da sua organização. Pode incluir informações como se a imagem é generalizada ou especializada, o sistema operativo, requisitos mínimos e máximos de memória e notas de lançamento. É uma definição de um tipo de imagem. |
| **Versão de imagem** | Uma **versão de imagem** é o que se usa para criar um VM quando se utiliza uma galeria. Pode ter várias versões de uma imagem necessária para o seu ambiente. Como uma imagem gerida, quando se usa uma versão de **imagem** para criar um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes. |

<br>

![Gráfico mostrando como você pode ter várias versões de uma imagem na sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definições de imagem

As definições de imagem são um agrupamento lógico para versões de uma imagem. A definição de imagem contém informações sobre o porquê da imagem ter sido criada, para que ser o SISTEMA de Identificação e informações sobre o uso da imagem. Uma definição de imagem é como um plano para todos os detalhes em torno da criação de uma imagem específica. Não se implementa um VM a partir de uma definição de imagem, mas a partir da versão de imagem criada a partir da definição.

Existem três parâmetros para cada definição de imagem que são usados em combinação - **Publisher,** **Offer** e **SKU**. Estes são usados para encontrar uma definição de imagem específica. Pode ter versões de imagem que partilham um ou dois valores, mas não os três valores.  Por exemplo, aqui estão três definições de imagem e os seus valores:

|Definição da Imagem|Publicador|Oferta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanças|Back-end|
|myImage2|Contoso|Finanças|Front-end|
|myImage3|Testar|Finanças|Front-end|

Todos estes três têm conjuntos únicos de valores. O formato é semelhante ao que pode atualmente especificar a editora, a oferta e a SKU para [imagens do Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) no Azure PowerShell para obter a versão mais recente de uma imagem do Marketplace. Cada definição de imagem precisa de ter um conjunto único destes valores.

Seguem-se outros parâmetros que podem ser definidos na definição de imagem para que possa acompanhar mais facilmente os seus recursos:

* Estado do sistema operativo - Pode definir o estado de Os para [generalizado ou especializado](#generalized-and-specialized-images).
* Sistema operativo - pode ser Windows ou Linux.
* Descrição - utilize a descrição para dar informações mais detalhadas sobre a razão pela qual a definição de imagem existe. Por exemplo, pode ter uma definição de imagem para o seu servidor frontal que tenha a aplicação pré-instalada.
* Eula - pode ser usada para apontar para um contrato de licença de utilizador final específico da definição de imagem.
* Privacy Statement and Release notes - guarde notas de lançamento e declarações de privacidade no armazenamento do Azure e forneça um URI para aceder às mesmas como parte da definição de imagem.
* Data de fim de vida - fixe uma data de fim de vida à sua definição de imagem para poder utilizar a automação para eliminar definições de imagem antigas.
* Tag - pode adicionar tags quando criar a sua definição de imagem. Para mais informações sobre tags, consulte [Usar etiquetas para organizar os seus recursos](../articles/azure-resource-manager/management/tag-resources.md)
* Recomendações mínimas e máximas de vCPU e memória - se a sua imagem tiver recomendações vCPU e memória, pode anexar essa informação à sua definição de imagem.
* Tipos de disco não autorizados - pode fornecer informações sobre as necessidades de armazenamento para o seu VM. Por exemplo, se a imagem não for adequada para discos HDD padrão, adicione-os à lista de não permitir.

## <a name="generalized-and-specialized-images"></a>Imagens generalizadas e especializadas

Existem dois estados do sistema operativo apoiados pela Shared Image Gallery. Normalmente, as imagens requerem que o VM usado para criar a imagem tenha sido generalizado antes de tirar a imagem. Generalizar é um processo que remove informações específicas da máquina e do utilizador do VM. Para windows, o Sysprep também é usado. Para o Linux, pode `-deprovision+user` utilizar [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou parâmetros.

Os VMs especializados não passaram por um processo para remover informações e contas específicas da máquina. Além disso, os VMs criados `osProfile` a partir de imagens especializadas não têm um associado a eles. Isto significa que imagens especializadas terão algumas limitações.

- As contas que poderiam ser usadas para iniciar sessão no VM também podem ser usadas em qualquer VM criado usando a imagem especializada que é criada a partir desse VM.
- VMs terão o nome de **computador** do VM de onde a imagem foi tirada. Deve alterar o nome do computador para evitar colisões.
- É `osProfile` assim que algumainformação sensível é `secrets`passada para o VM, usando . Isto pode causar problemas usando KeyVault, WinRM `secrets` e `osProfile`outras funcionalidades que usam na . Em alguns casos, pode utilizar identidades de serviço geridas (MSI) para contornar estas limitações.

> [!IMPORTANT]
> Imagens especializadas estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
>
> **Limitações de pré-visualização conhecidas** Os VMs só podem ser criados a partir de imagens especializadas utilizando o portal ou API. O suporte não é CLI ou PowerShell para a pré-visualização.


## <a name="regional-support"></a>Apoio Regional

As regiões-fonte estão listadas na tabela abaixo. Todas as regiões públicas podem ser regiões-alvo, mas para replicar para a Austrália Central e Austrália Central 2 você precisa ter a sua subscrição listada em branco. Para solicitar a lista geminada, vá a:https://azure.microsoft.com/global-infrastructure/australia/contact/


| Regiões-fonte        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Austrália Central     | Leste da China        | Sul da Índia        | Europa ocidental        |
| Austrália Central 2   | China Leste 2      | Ásia Sudeste     | Sul do Reino Unido           |
| Leste da Austrália        | Norte da China       | Leste do Japão         | Oeste do Reino Unido            |
| Austrália Sudeste   | China Norte 2     | Oeste do Japão         | US DoD Centro     |
| Sul do Brasil          | Ásia Leste         | Coreia do Sul Central      | US DoD - Leste        |
| Canadá Central        | E.U.A. Leste           | Sul da Coreia do Sul        | US Gov - Arizona     |
| Leste do Canadá           | E.U.A. Leste 2         | E.U.A. Centro-Norte   | US Gov - Texas       |
| Índia Central         | LESTE DOS EUA 2 EUAP    | Europa do Norte       | US Gov - Virginia    |
| E.U.A. Central            | França Central    | E.U.A. Centro-Sul   | Oeste da Índia         |
| EUA Centrais EUA       | Sul de França      | E.U.A. Centro-Oeste    | E.U.A. Oeste            |
|                       |                   |                    | E.U.A.Oeste 2          |



## <a name="limits"></a>Limites 

Existem limites, por subscrição, para a implantação de recursos utilizando galerias de imagem partilhada:
- 100 galerias de imagem partilhada, por subscrição, por região
- 1.000 definições de imagem, por subscrição, por região
- 10.000 versões de imagem, por subscrição, por região
- Qualquer disco ligado à imagem deve ser inferior ou igual a 1TB em tamanho

Para mais informações, consulte Verifique a [utilização de recursos contra limites,](https://docs.microsoft.com/azure/networking/check-usage-against-limits) por exemplo, sobre como verificar a sua utilização atual.
 
## <a name="scaling"></a>Dimensionamento
A Galeria de Imagem Partilhada permite especificar o número de réplicas que pretende que o Azure mantenha nas imagens. Isto ajuda em cenários de implantação multi-VM, uma vez que as implementações vm podem ser espalhadas para diferentes réplicas reduzindo a possibilidade de o processamento da criação de instâncias ser estrangulado devido à sobrecarga de uma única réplica.

Com a Galeria de Imagem Partilhada, pode agora implantar até 1.000 casos de VM numa escala de máquina virtual (até 600 com imagens geridas). As réplicas de imagem proporcionam um melhor desempenho de implementação, fiabilidade e consistência. Pode definir uma contagem de réplicas diferente em cada região-alvo, com base nas necessidades de escala para a região. Uma vez que cada réplica é uma cópia profunda da sua imagem, isto ajuda a escalar as suas implementações linearmente com cada réplica extra. Embora compreendamos que não há duas imagens ou regiões iguais, aqui está a nossa orientação geral sobre como usar réplicas numa região:

- Para implementações não virtuais de escala de máquina (VMSS) - Para cada 20 VMs que cria simultaneamente, recomendamos que mantenha uma réplica. Por exemplo, se estiver a criar 120 VMs simultaneamente usando a mesma imagem numa região, sugerimos que mantenha pelo menos 6 réplicas da sua imagem. 
- Para as implementações de conjuntos de escala de máquina virtual (VMSS) - Para cada conjunto de escala com até 600 instâncias, recomendamos que mantenha pelo menos uma réplica. Por exemplo, se estiver a criar 5 conjuntos de escala em simultâneo, cada um com 600 casos de VM usando a mesma imagem numa única região, sugerimos que mantenha pelo menos 5 réplicas da sua imagem. 

Recomendamos sempre que oferecê-lo em excesso o número de réplicas devido a fatores como tamanho de imagem, conteúdo e tipo de SO.

![Gráfico mostrando como pode escalar imagens](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Torne as suas imagens altamente disponíveis

[O Armazenamento Redundante da Zona Azure (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) proporciona resiliência contra uma falha na Zona de Disponibilidade na região. Com a disponibilidade geral da Galeria de Imagem Partilhada, pode optar por armazenar as suas imagens em contas ZRS em regiões com Zonas de Disponibilidade. 

Pode também escolher o tipo de conta para cada uma das regiões-alvo. O tipo de conta de armazenamento padrão é Standard_LRS, mas pode escolher Standard_ZRS para regiões com Zonas de Disponibilidade. Consulte aqui a disponibilidade regional [here](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)do ZRS.

![Gráfico mostrando ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicação
A Galeria de Imagem Partilhada também permite replicar automaticamente as suas imagens para outras regiões do Azure. Cada versão De Imagem Partilhada pode ser replicada para diferentes regiões dependendo do que faz sentido para a sua organização. Um exemplo é replicar sempre a imagem mais recente em várias regiões, enquanto todas as versões mais antigas só estão disponíveis em 1 região. Isto pode ajudar a economizar nos custos de armazenamento para versões de Imagem Partilhada. 

As regiões que uma versão De Imagem Partilhada é replicada para ser atualizada após o tempo de criação. O tempo que demora a replicar-se em diferentes regiões depende da quantidade de dados que estão a ser copiados e do número de regiões a que a versão é replicada. Isto pode levar algumas horas em alguns casos. Enquanto a replicação está a acontecer, pode ver o estado da replicação por região. Uma vez que a replicação da imagem esteja completa numa região, pode então implantar um VM ou um conjunto de escala utilizando essa versão de imagem na região.

![Gráfico mostrando como pode replicar imagens](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Acesso

Como a Galeria de Imagem Partilhada, definição de imagem e versão imagem são todos os recursos, podem ser partilhados usando os controlos Azure RBAC nativo incorporados. Utilizando o RBAC pode partilhar estes recursos com outros utilizadores, diretores de serviço e grupos. Você pode até partilhar acesso a indivíduos fora do inquilino que foram criados dentro. Uma vez que um utilizador tenha acesso à versão Shared Image, pode implementar um VM ou um Conjunto de Escala de Máquina Virtual.  Aqui está a matriz de partilha que ajuda a entender a que o utilizador tem acesso:

| Partilhado com o Utilizador     | Galeria de Imagens Partilhada | Definição da Imagem | Versão da imagem |
|----------------------|----------------------|--------------|----------------------|
| Galeria de Imagens Partilhada | Sim                  | Sim          | Sim                  |
| Definição da Imagem     | Não                   | Sim          | Sim                  |

Recomendamos a partilha ao nível da Galeria para a melhor experiência. Não recomendamos a partilha de versões de imagem individuais. Para mais informações sobre o RBAC, consulte [Gerir o acesso aos recursos do Azure utilizando o RBAC](../articles/role-based-access-control/role-assignments-portal.md).

As imagens também podem ser partilhadas, em escala, mesmo em inquilinos usando uma inscrição de aplicativo multi-inquilino. Para obter mais informações sobre a partilha de imagens entre inquilinos, consulte [imagens VM da galeria Share em todos os inquilinos do Azure.](../articles/virtual-machines/linux/share-images-across-tenants.md)

## <a name="billing"></a>Faturação
Não existe nenhuma taxa extra para a utilização do serviço De imagem partilhada Gallery. Ser-lhe-á cobrado pelos seguintes recursos:
- Custos de armazenamento de armazenamento das versões Shared Image. O custo depende do número de réplicas da versão de imagem e do número de regiões a que a versão é replicada. Por exemplo, se tiver 2 imagens e ambas forem replicadas para 3 regiões, então será cobrado por 6 discos geridos com base no seu tamanho. Para mais informações, consulte [os preços dos Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks/).
- A rede egress charges para a replicação da primeira versão de imagem da região de origem para as regiões replicadas. As réplicas subsequentes são manuseadas dentro da região, pelo que não existem encargos adicionais. 

## <a name="updating-resources"></a>Atualização de recursos

Uma vez criado, pode fazer algumas alterações nos recursos da galeria de imagens. Estes limitam-se a:
 
Galeria de imagens partilhadas:
- Descrição

Definição de imagem:
- VCPUs recomendado
- Memória recomendada
- Descrição
- Data de fim de vida

Versão de imagem:
- Contagem de réplicas regionais
- Regiões-alvo
- Excluir dos mais recentes
- Data de fim de vida

## <a name="sdk-support"></a>Suporte de SKDs

Os seguintes SDKs apoiam a criação de Galerias de Imagem Partilhada:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Nó.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Pitão](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Ir](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Modelos

Pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar uma Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem numa Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar um VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

* [Como posso listar todos os recursos da Galeria de Imagem Partilhada através de subscrições?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Posso mover a minha imagem existente para a galeria de imagens partilhadas?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Posso criar uma versão de imagem a partir de um disco especializado?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Posso mover o recurso da Galeria da Imagem Partilhada para uma subscrição diferente depois de ter sido criado?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Posso replicar as minhas versões de imagem através de nuvens como o Azure China 21Vianet ou Azure Germany ou Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Posso replicar as minhas versões de imagem através de subscrições?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Posso partilhar versões de imagem em inquilinos da AD Azure?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo demora a replicar versões de imagem em todas as regiões-alvo?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual é a diferença entre a região-alvo e a região-alvo?](#what-is-the-difference-between-source-region-and-target-region)
* [Como especifico a região de origem enquanto crio a versão de imagem?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Como especifico o número de réplicas de versão de imagem a criar em cada região?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Posso criar a galeria de imagens partilhadas num local diferente do da definição de imagem e versão de imagem?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quais são as acusações por usar a Galeria de Imagem Partilhada?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Que versão API devo usar para criar galeria de imagem partilhada e definição de imagem e versão de imagem?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Que versão API devo usar para criar VM partilhado ou escala de máquina virtual definida a partir da versão de imagem?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Como posso listar todos os recursos da Galeria de Imagem Partilhada através de subscrições?

Para listar todos os recursos da Galeria de Imagem Partilhada através de subscrições a que tem acesso no portal Azure, siga os passos abaixo:

1. Abra o [portal Azure.](https://portal.azure.com)
1. Ir a **Todos os Recursos.**
1. Selecione todas as subscrições sob as quais gostaria de listar todos os recursos.
1. Procure recursos de **tipo galeria privada.**
 
   Para ver as definições de imagem e as versões de imagem, também deve selecionar **tipos ocultos do Show**.
 
   Para listar todos os recursos da Galeria de Imagem Partilhada através de subscrições a que tem permissões, utilize o seguinte comando no Azure CLI:

   ```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Posso mover a minha imagem existente para a galeria de imagens partilhadas?
 
Sim. Existem 3 cenários baseados nos tipos de imagens que pode ter.

 Cenário 1: Se tiver uma imagem gerida na mesma subscrição que o seu SIG, então pode criar uma definição de imagem e versão de imagem a partir dele.

 Cenário 2: Se tiver uma imagem não gerida na mesma subscrição que o seu SIG, pode criar uma imagem gerida a partir dele e, em seguida, criar uma definição de imagem e versão de imagem a partir dele. 

 Cenário 3: Se tiver um VHD no seu sistema de ficheiros local, então precisa de carregar o VHD para uma imagem gerida, então pode criar uma definição de imagem e versão de imagem a partir dele.

- Se o VHD for de um VM windows, consulte [o upload de um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se o VHD é para um VM Linux, consulte [Upload um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Posso criar uma versão de imagem a partir de um disco especializado?

Sim, suporte para discos especializados como imagens está em pré-visualização. Só é possível criar um VM a partir de uma imagem especializada utilizando o portal[(Windows](../articles/virtual-machines/linux/shared-images-portal.md) ou [Linux)](../articles/virtual-machines/linux/shared-images-portal.md)e a API. Não existe suporte powerShell para a pré-visualização.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Posso mover o recurso da Galeria da Imagem Partilhada para uma subscrição diferente depois de ter sido criado?

Não, não pode mover o recurso da galeria de imagens partilhadas para uma subscrição diferente. No entanto, poderá replicar as versões de imagem na galeria para outras regiões, conforme necessário.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Posso replicar as minhas versões de imagem através de nuvens como o Azure China 21Vianet ou Azure Germany ou Azure Government Cloud?

Não, não se pode replicar versões de imagem através das nuvens.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Posso replicar as minhas versões de imagem através de subscrições?

Não, pode replicar as versões de imagem em todas as regiões numa subscrição e usá-la noutras subscrições através do RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Posso partilhar versões de imagem em inquilinos da AD Azure? 

Sim, você pode usar RBAC para partilhar com indivíduos entre inquilinos. Mas, para partilhar em escala, veja "Partilhar imagens de galerias em todos os inquilinos do Azure" usando [powerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) ou [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Quanto tempo demora a replicar versões de imagem em todas as regiões-alvo?

O tempo de replicação da versão de imagem depende inteiramente do tamanho da imagem e do número de regiões a que está a ser replicado. No entanto, como uma boa prática, recomenda-se que mantenha a imagem pequena, e as regiões de origem e alvo fecham para obter os melhores resultados. Pode verificar o estado da replicação utilizando a bandeira -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Qual é a diferença entre a região-alvo e a região-alvo?

A região de origem é a região em que a sua versão de imagem será criada, e as regiões-alvo são as regiões em que uma cópia da sua versão de imagem será armazenada. Para cada versão de imagem, só pode ter uma região de origem. Além disso, certifique-se de que passa a localização da região de origem como uma das regiões-alvo quando criar uma versão de imagem.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Como especifico a região de origem enquanto crio a versão de imagem?

Ao criar uma versão de imagem, pode utilizar a etiqueta **de localização** no CLI e na etiqueta **-Localização** no PowerShell para especificar a região de origem. Certifique-se de que a imagem gerida que está a usar como imagem base para criar a versão de imagem está no mesmo local que o local em que pretende criar a versão de imagem. Além disso, certifique-se de que passa a localização da região de origem como uma das regiões-alvo quando criar uma versão de imagem.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Como especifico o número de réplicas de versão de imagem a criar em cada região?

Existem duas formas de especificar o número de réplicas da versão de imagem a criar em cada região:
 
1. A contagem de réplicas regionais que especifica o número de réplicas que pretende criar por região. 
2. A contagem de réplicas comum, que é a contagem padrão por região, caso não seja especificada a contagem de réplicas regionais. 

Para especificar a contagem de réplicas regionais, passe o local juntamente com o número de réplicas que pretende criar naquela região: "Centro Sul US=2". 

Se a contagem de réplicas regionais não for especificada em cada local, então o número padrão de réplicas será a contagem comum de réplicas que especificou. 

Para especificar a contagem comum de réplicas no CLI, utilize o argumento **de --réplica-contagem** no `az sig image-version create` comando.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Posso criar a galeria de imagens partilhadas num local diferente do da definição de imagem e versão de imagem?

Sim, é possível. Mas, como uma boa prática, encorajamo-lo a manter o grupo de recursos, galeria de imagem partilhada, definição de imagem e versão de imagem no mesmo local.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quais são as acusações por usar a Galeria de Imagem Partilhada?

Não existem encargos para a utilização do serviço Shared Image Gallery, exceto os custos de armazenamento para armazenar as versões de imagem e as taxas de rede para replicar as versões de imagem da região de origem para as regiões-alvo.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Que versão API devo usar para criar galeria de imagem partilhada e definição de imagem e versão de imagem?

Para trabalhar com galerias de imagem partilhadas, definições de imagem e versões de imagem, recomendamos que utilize a versão API 2018-06-01. Zona Redundant Storage (ZRS) requer versão 2019-03-01 ou mais tarde.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Que versão API devo usar para criar VM partilhado ou escala de máquina virtual definida a partir da versão de imagem?

Para implementações vm e virtual machine scale set usando uma versão de imagem, recomendamos que utilize a versão API 2018-04-01 ou superior.
