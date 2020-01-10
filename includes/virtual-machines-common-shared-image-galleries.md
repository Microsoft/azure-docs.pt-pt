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
ms.openlocfilehash: 5618757f4bf5eaf1afc5ef0ce1735eb4ae94e1d2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75663031"
---
A Galeria de imagens compartilhadas é um serviço que ajuda você a criar estrutura e organização em suas imagens gerenciadas. As galerias de imagens compartilhadas fornecem:

- Replicação global gerenciada de imagens.
- Controle de versão e agrupamento de imagens para facilitar o gerenciamento.
- Imagens altamente disponíveis com contas de ZRS (armazenamento com redundância de zona) em regiões que dão suporte a Zonas de Disponibilidade. O ZRS oferece uma melhor resiliência em relação a falhas zonais.
- Compartilhamento entre assinaturas e até mesmo entre locatários de Active Directory (AD), usando o RBAC.
- Dimensionando suas implantações com réplicas de imagem em cada região.

Usando uma galeria de imagens compartilhadas, você pode compartilhar suas imagens para diferentes usuários, entidades de serviço ou grupos do AD dentro da sua organização. As imagens compartilhadas podem ser replicadas para várias regiões, para um dimensionamento mais rápido de suas implantações.

Uma imagem gerenciada é uma cópia de uma VM completa (incluindo quaisquer discos de dados anexados) ou apenas do disco do sistema operacional, dependendo de como você cria a imagem. Quando você cria uma VM a partir da imagem, uma cópia dos VHDs na imagem é usada para criar os discos para a nova VM. A imagem gerenciada permanece no armazenamento e pode ser usada repetidamente para criar novas VMs.

Se você tiver um grande número de imagens gerenciadas que precisa manter e desejar disponibilizá-las em toda a empresa, poderá usar uma galeria de imagens compartilhadas como um repositório que facilita o compartilhamento de suas imagens. 

O recurso da Galeria de imagens compartilhadas tem vários tipos de recursos:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerenciada** | Uma imagem básica que pode ser usada sozinha ou usada para criar uma **versão de imagem** em uma galeria de imagens. As imagens gerenciadas são criadas a partir de VMs [generalizadas](#generalized-and-specialized-images) . Uma imagem gerenciada é um tipo especial de VHD que pode ser usado para fazer várias VMs e agora pode ser usado para criar versões de imagens compartilhadas. |
| **Instantânea** | Uma cópia de um VHD que pode ser usado para criar uma **versão de imagem**. Os instantâneos podem ser obtidos de uma VM [especializada](#generalized-and-specialized-images) (uma que não tenha sido generalizada) em seguida, usados sozinhos ou com instantâneos de discos de dados, para criar uma versão de imagem especializada.
| **Galeria de imagens** | Como o Azure Marketplace, uma **Galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição de imagem** | As imagens são definidas em uma galeria e contêm informações sobre a imagem e os requisitos para usá-la em sua organização. Você pode incluir informações como se a imagem é generalizada ou especializada, o sistema operacional, os requisitos mínimos e máximos de memória e notas de versão. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão de imagem** é o que você usa para criar uma VM ao usar uma galeria. Você pode ter várias versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão de imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. As versões de imagem podem ser usadas várias vezes. |

<br>

![Gráfico mostrando como você pode ter várias versões de uma imagem em sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definições de imagem

As definições de imagem são um agrupamento lógico de versões de uma imagem. A definição de imagem contém informações sobre por que a imagem foi criada, em qual sistema operacional ela é e informações sobre como usar a imagem. Uma definição de imagem é como um plano para todos os detalhes sobre a criação de uma imagem específica. Você não implanta uma VM com base em uma definição de imagem, mas a partir da versão de imagem criada a partir da definição.

Há três parâmetros para cada definição de imagem que são usados em combinação- **Publicador**, **oferta** e **SKU**. Eles são usados para localizar uma definição de imagem específica. Você pode ter versões de imagem que compartilham um ou dois, mas não todos os três valores.  Por exemplo, aqui estão três definições de imagem e seus valores:

|Definição da Imagem|Publicador|Oferta|SKU|
|---|---|---|---|
|myImage1|Contoso|Finanças|Back-end|
|myImage2|Contoso|Finanças|Front-end|
|myImage3|Testes|Finanças|Front-end|

Todos esses três têm conjuntos de valores exclusivos. O formato é semelhante a como você pode especificar atualmente Publicador, oferta e SKU para [imagens do Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) em Azure PowerShell para obter a versão mais recente de uma imagem do Marketplace. Cada definição de imagem precisa ter um conjunto exclusivo desses valores.

Veja a seguir outros parâmetros que podem ser definidos na definição de imagem para que você possa controlar seus recursos com mais facilidade:

* Estado do sistema operacional – você pode definir o estado do so como [generalizado ou especializado](#generalized-and-specialized-images).
* Sistema operacional – pode ser Windows ou Linux.
* Descrição – use a descrição para fornecer informações mais detalhadas sobre por que a definição da imagem existe. Por exemplo, você pode ter uma definição de imagem para o servidor front-end que tem o aplicativo pré-instalado.
* EULA – pode ser usado para apontar para um contrato de licença de usuário final específico para a definição de imagem.
* Política de privacidade e notas de versão – armazenar notas de versão e declarações de privacidade no armazenamento do Azure e fornecer um URI para acessá-las como parte da definição da imagem.
* Data de fim da vida útil – anexe uma data de fim da vida útil à sua definição de imagem para poder usar a automação para excluir definições de imagem antigas.
* Marca-você pode adicionar marcas ao criar a definição de imagem. Para obter mais informações sobre marcas, consulte [usando marcas para organizar seus recursos](../articles/azure-resource-manager/resource-group-using-tags.md)
* Recomendações de memória e vCPU mínimas e máximas – se sua imagem tiver recomendações de memória e vCPU, você poderá anexar essas informações à definição de imagem.
* Tipos de disco não permitidos-você pode fornecer informações sobre as necessidades de armazenamento para sua VM. Por exemplo, se a imagem não for adequada para discos HDD padrão, você as adicionará à lista de não permitir.

## <a name="generalized-and-specialized-images"></a>Imagens generalizadas e especializadas

Há dois Estados do sistema operacional com suporte pela galeria de imagens compartilhadas. Normalmente, as imagens exigem que a VM usada para criar a imagem tenha sido generalizada antes de tirar a imagem. Generalizar é um processo que remove informações específicas do computador e do usuário da VM. Para o Windows, o Sysprep também é usado. Para o Linux, você pode usar [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` ou `-deprovision+user` parâmetros.

VMs especializadas não passaram por um processo para remover informações e contas específicas do computador. Além disso, as VMs criadas a partir de imagens especializadas não têm um `osProfile` associado a elas. Isso significa que imagens especializadas terão algumas limitações.

- As contas que poderiam ser usadas para fazer logon na VM também podem ser usadas em qualquer VM criada usando a imagem especializada que é criada a partir dessa VM.
- As VMs terão o **nome do computador** da VM da qual a imagem foi obtida. Você deve alterar o nome do computador para evitar colisões.
- A `osProfile` é como algumas informações confidenciais são passadas para a VM, usando `secrets`. Isso pode causar problemas ao usar o keyvault, o WinRM e outras funcionalidades que usam `secrets` no `osProfile`. Em alguns casos, você pode usar as identidades de serviço gerenciadas (MSI) para contornar essas limitações.

> [!IMPORTANT]
> As imagens especializadas estão atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitações de visualização conhecidas** As VMs só podem ser criadas a partir de imagens especializadas usando o portal ou a API. O não é um suporte de CLI ou PowerShell para a versão prévia.


## <a name="regional-support"></a>Suporte regional

As regiões de origem são listadas na tabela a seguir. Todas as regiões públicas podem ser regiões de destino, mas para replicação na Austrália Central e na Austrália Central 2, você precisa ter sua assinatura na lista de permissões. Para solicitar a lista de permissões, acesse: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Regiões de origem        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Austrália Central     | Norte da China        | Sul da Índia        | Europa Ocidental        |
| Austrália Central 2   | Leste da China 2      | Sudeste Asiático     | Sul do Reino Unido           |
| Leste da Austrália        | China Norte       | Este do Japão         | Oeste do Reino Unido            |
| Sudeste da Austrália   | Norte da China 2     | Oeste do Japão         | US DoD - Centro     |
| Sul do Brasil          | Este Asiático         | Coreia do Sul Central      | US Gov - Arizona        |
| Canadá Central        | Este dos E.U.A.           | Sul da Coreia do Sul        | US Gov - Texas     |
| Leste do Canadá           | Este dos E.U.A. 2         | E.U.A. Centro-Norte   | Gov (US) - Texas       |
| Centro da Índia         | Leste dos EUA 2 EUAP    | Europa do Norte       | Gov dos E.U.A. Virginia    |
| Centro dos E.U.A.            | França Central    | E.U.A. Centro-Sul   | Oeste da Índia         |
| EUA Central EUAP       | Sul de França      | E.U.A. Centro-Oeste    | Oeste dos E.U.A.            |
|                       |                   |                    | E.U.A. Oeste 2          |



## <a name="limits"></a>Limites 

Há limites, por assinatura, para implantar recursos usando galerias de imagens compartilhadas:
- 100 galerias de imagens compartilhadas, por assinatura, por região
- 1\.000 definições de imagem, por assinatura, por região
- 10.000 versões de imagem, por assinatura, por região
- Qualquer disco anexado à imagem deve ser menor ou igual a 1 TB de tamanho

Para obter mais informações, consulte [verificar o uso de recursos em relação aos limites](https://docs.microsoft.com/azure/networking/check-usage-against-limits) para obter exemplos de como verificar seu uso atual.
 
## <a name="scaling"></a>Dimensionamento
A Galeria de imagens compartilhadas permite que você especifique o número de réplicas que deseja que o Azure mantenha das imagens. Isso ajuda em cenários de implantação de várias VMS, pois as implantações de VM podem ser distribuídas para réplicas diferentes, reduzindo a chance de processamento de criação de instância ser limitado devido à sobrecarga de uma única réplica.

Com a Galeria de imagens compartilhadas, agora você pode implantar até uma instância de VM 1.000 em um conjunto de dimensionamento de máquinas virtuais (acima de 600 com imagens gerenciadas). As réplicas de imagem oferecem melhor desempenho, confiabilidade e consistência da implantação.  Você pode definir uma contagem de réplicas diferente em cada região de destino, com base nas necessidades de escala para a região. Como cada réplica é uma cópia profunda da imagem, isso ajuda a dimensionar suas implantações linearmente com cada réplica extra. Embora não entendamos que duas imagens ou regiões são as mesmas, aqui está nossa orientação geral sobre como usar réplicas em uma região:

- Para implantações de VMSS (conjunto de dimensionamento de máquinas virtuais) – para cada 20 VMs que você cria simultaneamente, recomendamos que você mantenha uma réplica. Por exemplo, se você estiver criando VMs 120 simultaneamente usando a mesma imagem em uma região, sugerimos que você mantenha pelo menos 6 réplicas da sua imagem. 
- Para implantações de VMSS (conjunto de dimensionamento de máquinas virtuais) – para cada implantação de conjunto de dimensionamento com até 600 instâncias, recomendamos que você mantenha pelo menos uma réplica. Por exemplo, se você estiver criando cinco conjuntos de dimensionamento simultaneamente, cada um com instâncias de VM 600 usando a mesma imagem em uma única região, sugerimos que você mantenha pelo menos 5 réplicas de sua imagem. 

Sempre recomendamos que você provisione o número de réplicas devido a fatores como tamanho da imagem, conteúdo e tipo de so.

![Gráfico mostrando como você pode dimensionar imagens](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Tornar suas imagens altamente disponíveis

O [armazenamento com redundância de zona do Azure (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) fornece resiliência contra uma falha de zona de disponibilidade na região. Com a disponibilidade geral da Galeria de imagens compartilhadas, você pode optar por armazenar suas imagens em contas do ZRS em regiões com Zonas de Disponibilidade. 

Você também pode escolher o tipo de conta para cada uma das regiões de destino. O tipo de conta de armazenamento padrão é Standard_LRS, mas você pode escolher Standard_ZRS para regiões com Zonas de Disponibilidade. Verifique a disponibilidade regional do ZRS [aqui](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Gráfico mostrando ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicação
A Galeria de imagens compartilhadas também permite replicar suas imagens para outras regiões do Azure automaticamente. Cada versão de imagem compartilhada pode ser replicada em regiões diferentes, dependendo do que faz sentido para sua organização. Um exemplo é sempre replicar a imagem mais recente em várias regiões, enquanto todas as versões mais antigas estão disponíveis apenas em 1 região. Isso pode ajudar a economizar em custos de armazenamento para versões de imagens compartilhadas. 

As regiões nas quais uma versão de imagem compartilhada é replicada podem ser atualizadas após a hora de criação. O tempo necessário para replicar para regiões diferentes depende da quantidade de dados que estão sendo copiados e do número de regiões para as quais a versão é replicada. Isso pode levar algumas horas em alguns casos. Enquanto a replicação está acontecendo, você pode exibir o status de replicação por região. Depois que a replicação de imagem for concluída em uma região, você poderá implantar uma VM ou um conjunto de dimensionamento usando essa versão de imagem na região.

![Gráfico mostrando como você pode replicar imagens](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Acesso

Como a Galeria de imagens compartilhadas, a definição de imagem e a versão de imagem são todos os recursos, elas podem ser compartilhadas usando controles nativos do Azure RBAC internos. Usando o RBAC, você pode compartilhar esses recursos para outros usuários, entidades de serviço e grupos. Você pode até compartilhar o acesso a pessoas fora do locatário em que foram criadas. Quando um usuário tem acesso à versão da imagem compartilhada, ele pode implantar uma VM ou um conjunto de dimensionamento de máquinas virtuais.  Aqui está a matriz de compartilhamento que ajuda a entender a que o usuário obtém acesso:

| Compartilhado com usuário     | Galeria de Imagens Partilhadas | Definição da Imagem | Versão da imagem |
|----------------------|----------------------|--------------|----------------------|
| Galeria de Imagens Partilhadas | Sim                  | Sim          | Sim                  |
| Definição da Imagem     | Não                   | Sim          | Sim                  |

É recomendável compartilhar no nível da galeria para obter a melhor experiência. Não recomendamos o compartilhamento de versões de imagem individuais. Para obter mais informações sobre o RBAC, consulte [gerenciar o acesso aos recursos do Azure usando o RBAC](../articles/role-based-access-control/role-assignments-portal.md).

As imagens também podem ser compartilhadas, em escala, mesmo entre locatários usando um registro de aplicativo multilocatário. Para obter mais informações sobre como compartilhar imagens entre locatários, consulte [compartilhar imagens de VM de galeria entre locatários do Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Faturação
não há cobranças adicionais pela utilização do serviço de Galeria de Imagens Partilhadas. Você será cobrado pelos seguintes recursos:
- Os custos de armazenamento para armazenar as versões de imagens compartilhadas. O custo depende do número de réplicas da versão da imagem e do número de regiões para as quais a versão é replicada. Por exemplo, se você tiver duas imagens e ambas forem replicadas para três regiões, você será alterado para 6 discos gerenciados com base em seu tamanho. Para obter mais informações, consulte [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Encargos de saída de rede para replicação da primeira versão de imagem da região de origem para as regiões replicadas. As réplicas subsequentes são tratadas dentro da região, portanto, não há encargos adicionais. 

## <a name="updating-resources"></a>Atualizando recursos

Depois de criado, você pode fazer algumas alterações nos recursos da Galeria de imagens. Elas são limitadas a:
 
Galeria de imagens compartilhadas:
- Descrição

Definição de imagem:
- VCPUs recomendado
- Memória recomendada
- Descrição
- Data de término da vida útil

Versão da imagem:
- Contagem de réplica regional
- Regiões de destino
- Excluir da versão mais recente
- Data de término da vida útil

## <a name="sdk-support"></a>Suporte de SKDs

Os SDKs a seguir dão suporte à criação de galerias de imagens compartilhadas:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Modelos

Você pode criar recursos da Galeria de imagens compartilhadas usando modelos. Há vários modelos de início rápido do Azure disponíveis: 

- [Criar uma galeria de imagens compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem em uma galeria de imagens compartilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem em uma galeria de imagens compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM com base na versão da imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

* [Como posso listar todos os recursos da Galeria de imagens compartilhadas entre assinaturas?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Posso mover minha imagem existente para a Galeria de imagens compartilhadas?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Posso criar uma versão de imagem de um disco especializado?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Posso mover o recurso da Galeria de imagens compartilhadas para uma assinatura diferente depois que ela tiver sido criada?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Posso replicar minhas versões de imagem entre nuvens, como Azure China 21Vianet ou Azure Alemanha ou nuvem do Azure governamental?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Posso replicar minhas versões de imagem entre assinaturas?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Posso compartilhar versões de imagem entre locatários do Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo leva para replicar versões de imagem entre as regiões de destino?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual é a diferença entre a região de origem e a região de destino?](#what-is-the-difference-between-source-region-and-target-region)
* [Como fazer especificar a região de origem ao criar a versão da imagem?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Como fazer especificar o número de réplicas de versão da imagem a serem criadas em cada região?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Posso criar a Galeria de imagens compartilhadas em um local diferente daquele para a definição da imagem e a versão da imagem?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quais são os encargos para usar a Galeria de imagens compartilhadas?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Qual versão de API devo usar para criar a Galeria de imagens compartilhada e a imagem e a versão da imagem?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Qual versão de API devo usar para criar uma VM compartilhada ou um conjunto de dimensionamento de máquinas virtuais da versão da imagem?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Como posso listar todos os recursos da Galeria de imagens compartilhadas entre assinaturas?

Para listar todos os recursos da Galeria de imagens compartilhadas nas assinaturas às quais você tem acesso na portal do Azure, siga as etapas abaixo:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Vá para **todos os recursos**.
1. Selecione todas as assinaturas sob as quais você gostaria de listar todos os recursos.
1. Procure recursos do tipo **galeria privada**.
 
   Para ver as definições de imagem e as versões de imagem, você também deve selecionar **Mostrar tipos ocultos**.
 
   Para listar todos os recursos da Galeria de imagens compartilhadas nas assinaturas às quais você tem permissões, use o seguinte comando no CLI do Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Posso mover minha imagem existente para a Galeria de imagens compartilhadas?
 
Sim. Há três cenários com base nos tipos de imagens que você pode ter.

 Cenário 1: se você tiver uma imagem gerenciada na mesma assinatura que o SIG, poderá criar uma definição de imagem e uma versão de imagem a partir dela.

 Cenário 2: se você tiver uma imagem não gerenciada na mesma assinatura que o SIG, poderá criar uma imagem gerenciada a partir dela e, em seguida, criar uma definição de imagem e uma versão de imagem a partir dela. 

 Cenário 3: se você tiver um VHD em seu sistema de arquivos local, precisará carregar o VHD em uma imagem gerenciada e, em seguida, poderá criar uma definição de imagem e uma versão de imagem a partir dela.

- Se o VHD for de uma VM do Windows, consulte [carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se o VHD for para uma VM do Linux, consulte [carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Posso criar uma versão de imagem de um disco especializado?

Sim, o suporte para discos especializados como imagens está em versão prévia. Você só pode criar uma VM de uma imagem especializada usando o portal ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) ou [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) e a API. Não há suporte do PowerShell para a versão prévia.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Posso mover o recurso da Galeria de imagens compartilhadas para uma assinatura diferente depois que ela tiver sido criada?

Não, você não pode mover o recurso da Galeria de imagens compartilhadas para uma assinatura diferente. No entanto, você poderá replicar as versões da imagem na galeria para outras regiões, conforme necessário.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Posso replicar minhas versões de imagem entre nuvens, como Azure China 21Vianet ou Azure Alemanha ou nuvem do Azure governamental?

Não, você não pode replicar versões de imagem entre nuvens.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Posso replicar minhas versões de imagem entre assinaturas?

Não, você pode replicar as versões da imagem entre regiões em uma assinatura e usá-las em outras assinaturas por meio do RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Posso compartilhar versões de imagem entre locatários do Azure AD? 

Sim, você pode usar o RBAC para compartilhar com indivíduos entre locatários. Mas, para compartilhar em escala, consulte "compartilhar imagens da Galeria entre locatários do Azure" usando o [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) ou a [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Quanto tempo leva para replicar versões de imagem entre as regiões de destino?

O tempo de replicação da versão da imagem é totalmente dependente do tamanho da imagem e do número de regiões em que ele está sendo replicado. No entanto, como prática recomendada, é recomendável manter a imagem pequena, e as regiões de origem e destino são fechadas para obter melhores resultados. Você pode verificar o status da replicação usando o sinalizador-ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Qual é a diferença entre a região de origem e a região de destino?

Região de origem é a região em que a versão da imagem será criada e as regiões de destino são as regiões nas quais uma cópia da versão da imagem será armazenada. Para cada versão de imagem, você só pode ter uma região de origem. Além disso, certifique-se de passar o local da região de origem como uma das regiões de destino ao criar uma versão de imagem.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Como fazer especificar a região de origem ao criar a versão da imagem?

Ao criar uma versão de imagem, você pode usar a marca **--Location** na CLI e a marca **-Location** no PowerShell para especificar a região de origem. Verifique se a imagem gerenciada que você está usando como a imagem base para criar a versão da imagem está no mesmo local que o local no qual você pretende criar a versão da imagem. Além disso, certifique-se de passar o local da região de origem como uma das regiões de destino ao criar uma versão de imagem.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Como fazer especificar o número de réplicas de versão da imagem a serem criadas em cada região?

Há duas maneiras de especificar o número de réplicas de versão da imagem a serem criadas em cada região:
 
1. A contagem de réplicas regionais que especifica o número de réplicas que você deseja criar por região. 
2. A contagem de réplica comum que é a contagem padrão por região em caso de contagem de réplica regional não é especificada. 

Para especificar a contagem de réplica regional, passe o local junto com o número de réplicas que você deseja criar nessa região: "South EUA Central = 2". 

Se a contagem de réplica regional não for especificada em cada local, o número padrão de réplicas será a contagem de réplicas comum que você especificou. 

Para especificar a contagem de réplicas comuns na CLI, use o argumento **--réplica-Count** no comando `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Posso criar a Galeria de imagens compartilhadas em um local diferente daquele para a definição da imagem e a versão da imagem?

Sim, é possível. Mas, como prática recomendada, incentivamos você a manter o grupo de recursos, a Galeria de imagens compartilhadas, a definição da imagem e a versão da imagem no mesmo local.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quais são os encargos para usar a Galeria de imagens compartilhadas?

Não há encargos para usar o serviço da Galeria de imagens compartilhadas, exceto os encargos de armazenamento para armazenar as versões de imagem e encargos de saída de rede para replicar as versões da imagem da região de origem para regiões de destino

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Qual versão de API devo usar para criar a Galeria de imagens compartilhada e a imagem e a versão da imagem?

Para trabalhar com galerias de imagens compartilhadas, definições de imagem e versões de imagem, recomendamos que você use a versão de API 2018-06-01. O ZRS (armazenamento com redundância de zona) requer a versão 2019-03-01 ou posterior.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Qual versão de API devo usar para criar uma VM compartilhada ou um conjunto de dimensionamento de máquinas virtuais da versão da imagem?

Para implantações de VM e conjunto de dimensionamento de máquinas virtuais usando uma versão de imagem, recomendamos que você use a versão de API 2018-04-01 ou superior.
