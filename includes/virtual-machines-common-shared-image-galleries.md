---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 4063e79a9415ac35b09cc77d0110c04e191b49c7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546760"
---
Galeria de imagens partilhado é um serviço que ajuda a criar a estrutura e a organização em torno de suas imagens VM geridas personalizadas. Forneça a galerias de imagem partilhada:

- Gerenciado os replicação global de imagens.
- Controle de versão e o agrupamento de imagens para uma gestão mais fácil.
- Tornar suas imagens de elevada disponibilidade com contas de armazenamento com redundância de zona (ZRS) em regiões que suportam as zonas de disponibilidade. O ZRS oferece uma maior resiliência contra falhas zonais.
- Partilha entre subscrições e até mesmo entre inquilinos, utilizando o RBAC.

Utilizar uma galeria de imagens partilhado pode partilhar suas imagens para diferentes utilizadores, principais de serviço ou grupos do AD na sua organização. Imagens partilhadas podem ser replicadas para várias regiões, mais rápido dimensionamento das suas implementações.

Uma imagem gerida é uma cópia de uma VM completa em como (incluindo qualquer discos de dados anexados) ou apenas o disco do SO, dependendo de como criar a imagem. Quando criar uma VM a partir da imagem, uma cópia dos VHDs na imagem são utilizados para criar os discos para a nova VM. A imagem gerida permanece no armazenamento e pode ser utilizada várias vezes para criar novas VMs.

Se tiver um grande número de imagens gerenciadas que tem de manter e gostaria de disponibilizá-los em toda a sua empresa, pode utilizar uma galeria de imagens partilhado como um repositório que torna mais fácil de compartilhar suas imagens. 

O recurso da Galeria de imagens de partilhado tem vários tipos de recursos:

| Resource | Descrição|
|----------|------------|
| **Imagem gerida** | Uma imagem básica que pode ser usada sozinha ou utilizada para criar uma **versão da imagem** na Galeria de imagens. Imagens geridas são criadas a partir de VMs generalizadas. Uma imagem gerida é um tipo especial de VHD que pode ser utilizado para fazer várias VMs e pode agora ser utilizado para criar versões de imagem partilhada. |
| **Galeria de imagens** | Como o Azure Marketplace, um **Galeria de imagens** é um repositório de gerenciamento e compartilhamento de imagens, mas controlar quem tem acesso. |
| **Definição de imagem** | As imagens são definidas dentro de uma galeria e transportar informações sobre os requisitos para utilizá-lo na sua organização e a imagem. Pode incluir informações como se a imagem é Windows ou Linux, os requisitos de memória mínima e máxima e notas de versão. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é o que utiliza para criar uma VM ao utilizar uma galeria. Pode ter várias versões de uma imagem, conforme necessário para o seu ambiente. Como uma imagem gerida, quando utiliza um **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser utilizadas várias vezes. |

<br>


![Gráfico que mostra como pode ter várias versões de uma imagem da sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definições de imagem

As definições de imagem são um agrupamento lógico das versões de uma imagem. A definição de imagem contém informações sobre por que a imagem foi criada, o que OS destina-se e informações sobre como utilizar a imagem. Uma definição de imagem é como um plano para todos os detalhes sobre a criação de uma imagem específica. Não implemente uma VM a partir de uma definição de imagem, mas a partir da versão de imagem criado a partir da definição.


Há três parâmetros para cada definição de imagem que são utilizados em combinação - **publicador**, **oferecer** e **SKU**. Estes são utilizados para encontrar uma definição de imagem específica. Pode ter versões de imagem que compartilham um ou dois, mas nem todos os três valores.  Por exemplo, Eis três definições de imagem e os respetivos valores:

|Definição da Imagem|Editor|Oferta|SKU|
|---|---|---|---|
|myImage1|Contoso|Finanças|Back-end|
|myImage2|Contoso|Finanças|Front-end|
|myImage3|A testar|Finanças|Front-end|

Todos os três deles têm exclusivos conjuntos de valores. O formato é semelhante à forma como pode atualmente especificar publicador, oferta e SKU para [imagens do Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) no Azure PowerShell para obter a versão mais recente de uma imagem do Marketplace. Cada definição de imagem tem de ter um conjunto exclusivo desses valores.

Seguem-se outros parâmetros que podem ser definidos na definição da imagem, para que pode controlar mais facilmente seus recursos:

* Estado do sistema operativo - é possível definir o estado do sistema operacional como generalizada ou especializada, mas apenas generalizada é atualmente suportada. Tem de ser criadas imagens de VMs que tenham sido generalizadas com o Sysprep para Windows ou `waagent -deprovision` para Linux.
* Pode ser o sistema operativo - Windows ou Linux.
* Descrição – descrição de utilização para dar informações mais detalhadas sobre por que motivo a definição de imagem existe. Por exemplo, pode ter uma definição de imagem para o seu servidor front-end que tenha a aplicação foi instalada previamente.
* EULA - pode ser utilizado para apontar para um contrato de licença do utilizador final, específico para a definição de imagem.
* Declaração de privacidade e notas de versão - armazenar notas de versão e declarações de privacidade no armazenamento do Azure e forneça um URI para aceder ao-los como parte da definição de imagem.
* Fim de vida de datas - anexar uma data de fim-de-vida à sua definição de imagem para que seja possível utilizar a automatização para eliminar as definições de imagem antiga.
* Etiqueta – pode adicionar etiquetas ao criar a definição de imagem. Para obter mais informações sobre etiquetas, consulte [utilizando etiquetas para organizar os recursos](../articles/azure-resource-manager/resource-group-using-tags.md)
* Mínimo e máximo vCPU e recomendações de memória - se a sua imagem tiver recomendações de memória e vCPU, pode anexar essas informações para a definição de imagem.
* Não são permitidas tipos de disco - pode fornecer informações sobre as necessidades de armazenamento para a sua VM. Por exemplo, se a imagem não é adequada para os discos standard HDD, adicioná-los à lista não permitir.


## <a name="regional-support"></a>Suporte regional

Regiões de origem estão listados na tabela abaixo. Todas as regiões públicas podem ser regiões de destino, mas para replicar a Austrália Central e da Austrália Central 2 tem de ter a sua subscrição na lista de permissões. Para pedir a lista de permissões, aceda a: https://www.microsoft.com/en-au/central-regions-eligibility/


| Regiões de origem |
|---------------------|-----------------|------------------|-----------------|
| Austrália Central   | E.U.A. Central EUAP | Coreia do Sul Central    | Sul do R.U. 2      |
| Austrália Central 2 | Ásia Oriental       | Sul da Coreia do Sul      | Oeste do R.U.         |
| Leste da Austrália      | EUA Leste         | EUA Centro-Norte | EUA Centro-Oeste |
| Sudeste da Austrália | EUA Leste 2       | Europa do Norte     | Europa Ocidental     |
| Sul do Brasil        | E.U.A. Leste 2 EUAP  | EUA Centro-Sul | Oeste da Índia      |
| Canadá Central      | França Central  | Sul da Índia      | EUA Oeste         |
| Leste do Canadá         | Sul de França    | Sudeste Asiático   | EUA Oeste         |
| Índia Central       | Leste do Japão      | Norte do R.U.         | E.U.A. Oeste 2       |
| EUA Central          | Oeste do Japão      | Reino Unido Sul         |                 |



## <a name="limits"></a>Limits 

Existem limites, por subscrição, para implementar recursos através de galerias de imagem partilhada:
- 100 galerias de imagem partilhada, por subscrição, por região
- 1000 definições de imagem, por subscrição, por região
- 10 000 versões de imagem, por subscrição, por região

Para obter mais informações, consulte [verificar a utilização de recursos em relação a limites](https://docs.microsoft.com/azure/networking/check-usage-against-limits) para obter exemplos sobre como verificar a sua utilização atual.
 

## <a name="scaling"></a>Dimensionamento
Galeria de imagens partilhado permite-lhe especificar o número de réplicas que pretende que o Azure para manter as imagens. Isto ajuda a cenários de implementação de várias VMS à medida que as implementações de VM podem propagar-se réplicas diferentes, reduzindo a probabilidade de criação de uma instância de processamento a ser limitado devido à sobrecarga de uma única réplica.

![Gráfico que mostra como pode dimensionar imagens](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replicação
Galeria de imagens partilhada também permite-lhe replicar automaticamente suas imagens para outras regiões do Azure. Cada versão de imagem partilhada pode ser replicado para regiões diferentes, dependendo do que é adequado para sua organização. Um exemplo é replicar sempre a imagem mais recente em várias regiões, enquanto todas as versões mais antigas só estão disponíveis na região de 1. Isso pode ajudar a poupar nos custos de armazenamento para versões de imagem partilhada. 

As regiões de que uma versão de imagem partilhada é replicada para podem ser atualizadas após a hora de criação. O tempo que demora a replicar para diferentes regiões depende da quantidade de dados a ser copiados e o número de regiões, que a versão é replicada para. Esta ação pode demorar algumas horas em alguns casos. Enquanto a replicação está a acontecer, pode ver o estado de replicação por região. Quando a replicação de imagem é concluída numa região, em seguida, pode implementar uma VM ou um conjunto de dimensionamento com essa versão de imagem na região.

![Gráfico que mostra como pode replicar imagens](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Aceder

Como a Galeria de imagens de partilhado, a imagem partilhada e a versão de imagem partilhada estão todos os recursos, eles podem ser compartilhados com incorporada que RBAC do Azure nativo controla. Utilizar o RBAC pode partilhar estes recursos para outros utilizadores, principais de serviço e grupos. Pode até compartilhar acesso aos indivíduos fora do inquilino que foram criados. Assim que um utilizador tem acesso para a versão de imagem partilhada, podem implementar uma VM ou um conjunto de dimensionamento de Máquina Virtual.  Segue-se a matriz de partilha que o ajuda a compreender o que o utilizador obtém acesso a:

| Partilhado com utilizador     | Galeria de Imagens Partilhadas | Imagem partilhada | Versão de imagem partilhada |
|----------------------|----------------------|--------------|----------------------|
| Galeria de Imagens Partilhadas | Sim                  | Sim          | Sim                  |
| Imagem partilhada         | Não                   | Sim          | Sim                  |
| Versão de imagem partilhada | Não                   | Não           | Sim                  |

Recomendamos que ao nível da Galeria para a melhor experiência de partilha. Para obter mais informações sobre RBAC, veja [gerir o acesso aos recursos do Azure através do RBAC](../articles/role-based-access-control/role-assignments-portal.md).

Imagens também podem ser partilhadas, à escala, em inquilinos com o registo de aplicação multi-inquilino. Para obter mais informações sobre a partilha de imagens em inquilinos, consulte [partilhar imagens de VM de galeria em inquilinos do Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Faturação
Não é sem custo adicional para utilizar o serviço de Galeria de imagens de partilhado. Será cobrado para os seguintes recursos:
- Custos de armazenamento de armazenar as versões de imagem partilhada. Custo depende do número de réplicas da versão de imagem e o número de regiões, que a versão é replicada para. Por exemplo, se tiver 2 imagens e ambos são replicadas para 3 regiões, em seguida, será alterada para 6 discos geridos, com base em seu tamanho. Para obter mais informações, consulte [preços dos Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Custos de saída de rede para a replicação da primeira versão da imagem da região de origem para regiões replicadas. Réplicas subsequentes são processadas na região, pelo que existem sem custos adicionais. 

## <a name="updating-resources"></a>Atualizar os recursos

Depois de criado, pode fazer algumas alterações para os recursos de Galeria de imagens. Estes estão limitados a:
 
Galeria de imagem partilhada:
- Descrição

definição de imagem:
- VCPUs recomendada
- Memória recomendada
- Descrição
- Data de vida de fim de

Versão da imagem:
- Contagem de réplicas regional
- Regiões de destino
- Exclusão de versão mais recente
- Data de vida de fim de


## <a name="sdk-support"></a>Suporte do SDK

Os SDKs seguintes suportam a criação de galerias de imagem partilhada:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/azure-arm-compute/?view=azure-node-latest)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/go/azure/)

## <a name="templates"></a>Modelos

Pode criar recursos de Galeria de imagens de partilhado através de modelos. Vários modelos de início rápido do Azure estão disponíveis: 

- [Criar uma galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes 

**P.** Como listar todos os recursos de Galeria de imagens de partilhado entre subscrições? 
 
 R. Para listar todos os recursos de Galeria de imagens de partilhado entre subscrições que têm acesso a no portal do Azure, siga os passos abaixo:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Aceda a **todos os recursos**.
1. Selecione todas as subscrições sob a qual pretende listar todos os recursos.
1. Procurar por recursos do tipo **Galeria privada**.
 
   Para ver as definições de imagem e versões de imagem, deve também selecionar **mostrar tipos ocultos**.
 
   Para listar todos os recursos de Galeria de imagens de partilhado entre subscrições não tem permissões para, utilize o seguinte comando na CLI do Azure:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```


**P.** Posso mover a minha imagem existente para a Galeria de imagem partilhada?
 
 R. Sim. Existem 3 cenários com base nos tipos de imagens, que pode ter.

 Cenário 1: Se tiver uma imagem gerida, em seguida, pode criar uma definição de imagem e a versão da imagem do mesmo.

 Cenário 2: Se tiver uma imagem generalizada não gerida, pode criar uma imagem gerida a partir do mesmo e, em seguida, crie uma definição de imagem e a versão da imagem do mesmo. 

 Cenário 3: Se tiver um VHD no sistema de arquivos local, em seguida, terá de carregar o VHD, criar uma imagem gerida, em seguida, pode criar e a imagem de definição e a versão da imagem do mesmo.
- Se for o VHD de uma VM do Windows, veja [carregar um VHD generalizado](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se for o VHD para uma VM do Linux, veja [carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**P.** Pode criar uma versão de imagem de um disco especializado?

 R. Não, nós não suportam atualmente discos especializados como imagens. Se tiver um disco especializado, terá [criar uma VM a partir do VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) ao anexar o disco especializado para uma nova VM. Assim que tiver uma VM em execução, tem de seguir as instruções para criar uma imagem gerida a partir da [VM do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) ou [VM do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Depois de ter uma imagem gerida generalizada, pode iniciar o processo para criar uma descrição de imagem partilhada e a versão da imagem.

 
**P.** Depois de criado, posso mover o recurso da Galeria de imagens de partilhado para uma subscrição diferente?

 R. Não, não é possível mover o recurso da Galeria de imagem partilhada para uma subscrição diferente. No entanto, será capaz de replicar as versões de imagem na Galeria para outras regiões conforme necessário.

**P.** Pode replicar minhas versões de imagem em clouds – Azure China 21Vianet, Azure Alemanha e a Cloud do Azure Government? 

 R. Não, não é possível replicar versões de imagem em várias clouds.

**P.** Pode replicar minhas versões de imagem entre subscrições? 

 R. Não, pode replicar as versões de imagem para regiões através de uma subscrição e utilizá-los com outras subscrições através do RBAC.

**P.** Pode partilhar as versões de imagem em inquilinos do Azure AD? 

 R. Sim, pode utilizar o RBAC para partilhar a indivíduos em inquilinos. Mas, para partilhar em escala, consulte "Galeria de partilha de imagens em inquilinos do Azure" usando [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) ou [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).


**P.** Quanto tempo demora a replicar as versões de imagem em todas as regiões de destino?

 R. O tempo de replicação de versão de imagem é totalmente dependente do tamanho da imagem e o número de regiões que está a ser replicada para. No entanto, como melhor prática, é recomendado que mantenha a imagem pequena, e as regiões de origem e destino fechar-se para obter melhores resultados. Pode verificar o estado da replicação usando o sinalizador - ReplicationStatus.


**P.** O que é a diferença entre a região de origem e a região de destino?

 R. Região de origem é a região em que sua versão de imagem será criado e regiões de destino são as regiões em que uma cópia da sua versão de imagem será armazenada. Para cada versão de imagem, só pode ter uma região de origem. Além disso, certifique-se de que passe a localização de região de origem como uma das regiões de destino quando criar uma versão de imagem.  


**P.** Como posso especificar a região de origem ao criar a versão da imagem?

 R. Ao criar uma versão de imagem, pode utilizar o **– localização** etiqueta na CLI e o **-localização** marca no PowerShell para especificar a região de origem. Certifique-se de que a imagem gerida que está a utilizar como a imagem de base para criar a versão da imagem está na mesma localização como a localização na qual pretende criar a versão da imagem. Além disso, certifique-se de que passe a localização de região de origem como uma das regiões de destino quando criar uma versão de imagem.  


**P.** Como posso especificar o número de réplicas de versão da imagem a ser criada em cada região?

 R. Existem duas formas, pode especificar o número de réplicas de versão da imagem a ser criada em cada região:
 
1. A contagem de réplica regionais que especifica o número de réplicas que pretende criar por região. 
2. A contagem de réplica comum que é o padrão por contagem de região no caso de contagem de réplicas regionais não for especificada. 

Para especificar a contagem de réplica regionais, passe a localização, juntamente com o número de réplicas que pretende criar nessa região: "Dos E.U.A. centro-Sul = 2". 

Se a contagem de réplicas regionais não for especificada com cada localização, o número predefinido de réplicas será a contagem de réplica comuns que especificou. 

Para especificar a contagem de réplica comuns na CLI, utilize o **– contagem de réplicas** argumento no `az sig image-version create` comando.


**P.** Pode criar a Galeria de imagem partilhada numa localização diferente em que pretende criar a definição de imagem e a versão da imagem?

 R. Sim, é possível. No entanto, como melhor prática, recomendamos que mantenha a grupo de recursos, a Galeria de imagem partilhada, a definição de imagem e a versão da imagem na mesma localização.


**P.** Quais são os custos para utilizar a Galeria de imagens partilhado?

 R. Não existem custos para utilizar o serviço de Galeria de imagens de partilhado, exceto os custos de armazenamento para armazenar as versões de imagem e os custos de saída de rede para replicar as versões de imagem a partir da região de origem para regiões de destino.

**P.** Qual versão de API deve usar para criar a Galeria de imagens de partilhado, definição de imagem, versão da imagem e VM/VMSS fora a versão da imagem?

 R. Para implementações de VM e conjunto de dimensionamento de Máquina Virtual a utilizar uma versão de imagem, recomendamos que utilize a versão de 2018-04-01 de API ou superior. Para trabalhar com galerias de imagem partilhada, definições de imagem e versões de imagem, recomendamos que utilize a API versão 2018-06-01. Armazenamento com redundância de zona (ZRS) requer a versão de 2019-03-01 ou posterior.
