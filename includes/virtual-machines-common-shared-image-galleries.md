---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 01/09/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 8c7da8d04b456642b158dda77d9c745891aa18e6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58051607"
---
Galeria de imagens partilhado é um serviço que ajuda a criar a estrutura e a organização em torno de suas imagens VM geridas personalizadas. Utilizar uma galeria de imagens partilhado pode partilhar suas imagens para diferentes utilizadores, principais de serviço ou grupos do AD na sua organização. Imagens partilhadas podem ser replicadas para várias regiões, mais rápido dimensionamento das suas implementações.

Uma imagem gerida é uma cópia de uma VM completa em como (incluindo qualquer discos de dados anexados) ou apenas o disco do SO, dependendo de como criar a imagem. Quando criar uma VM a partir da imagem, uma cópia dos VHDs na imagem são utilizados para criar os discos para a nova VM. A imagem gerida permanece no armazenamento e pode ser utilizada várias vezes para criar novas VMs.

Se tiver um grande número de imagens gerenciadas que tem de manter e gostaria de disponibilizá-los em toda a sua empresa, pode utilizar uma galeria de imagens partilhado como um repositório que torna mais fácil atualizar e compartilhar suas imagens. Os custos para a utilização de uma galeria de imagens partilhado são apenas os custos para o armazenamento utilizado pelas imagens, além de quaisquer custos de saída de rede para replicar imagens a partir da região de origem para as regiões publicadas.

O recurso da Galeria de imagens de partilhado tem vários tipos de recursos:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerida** | Esta é uma imagem básica que pode ser usada sozinha ou utilizada para criar uma **versão da imagem** na Galeria de imagens. Imagens geridas são criadas a partir de VMs generalizadas. Uma imagem gerida é um tipo especial de VHD que pode ser utilizado para fazer várias VMs e pode agora ser utilizado para criar versões de imagem partilhada. |
| **Galeria de imagens** | Como o Azure Marketplace, um **Galeria de imagens** é um repositório de gerenciamento e compartilhamento de imagens, mas controlar quem tem acesso. |
| **Definição de imagem** | As imagens são definidas dentro de uma galeria e transportar informações sobre a imagem e os requisitos para utilizá-lo internamente. Isto inclui se a imagem é Windows ou Linux, notas de versão e os requisitos de memória mínimos e máximos. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é o que utiliza para criar uma VM ao utilizar uma galeria. Pode ter várias versões de uma imagem, conforme necessário para o seu ambiente. Como uma imagem gerida, quando utiliza um **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser utilizadas várias vezes. |

<br>


![Gráfico que mostra como pode ter várias versões de uma imagem da sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

### <a name="regional-support"></a>Suporte regional

Suporte regional para galerias de imagem partilhada está em pré-visualização limitada, mas irá expandir ao longo do tempo. Para a pré-visualização limitada, eis a lista de regiões onde pode criar galerias e a lista de regiões onde pode replicar qualquer imagem de galeria: 

| Criar galeria no  | Versão para replicar |
|--------------------|----------------------|
| EUA Centro-Oeste    |Todas as regiões públicas&#42;|
| EUA Leste 2          ||
| EUA Centro-Sul   ||
| Sudeste Asiático     ||
| Europa Ocidental        ||
| EUA Oeste            ||
| EUA Leste            ||
| Canadá Central     ||
|                    ||



&#42;Para replicar a Austrália Central e da Austrália Central 2 tem de ter sua lista de permissões de subscrição. Para pedir a lista de permissões, aceda a: https://www.microsoft.com/en-au/central-regions-eligibility/

## <a name="scaling"></a>Dimensionamento
Galeria de imagens partilhado permite-lhe especificar o número de réplicas que pretende que o Azure para manter as imagens. Isto ajuda a cenários de implementação de várias VMS à medida que as implementações de VM podem propagar-se réplicas diferentes, reduzindo a probabilidade de criação de uma instância de processamento a ser limitado devido à sobrecarga de uma única réplica.

![Gráfico que mostra como pode dimensionar imagens](./media/shared-image-galleries/scaling.png)


## <a name="replication"></a>Replicação
Galeria de imagens partilhada também permite-lhe replicar automaticamente suas imagens para outras regiões do Azure. Cada versão de imagem partilhada pode ser replicado para regiões diferentes, dependendo do que é adequado para sua organização. Um exemplo é replicar sempre a imagem mais recente em várias regiões, enquanto todas as versões mais antigas só estão disponíveis na região de 1. Isso pode ajudar a poupar nos custos de armazenamento para versões de imagem partilhada. 

As regiões de que uma versão de imagem partilhada é replicada para podem ser atualizadas após a hora de criação. O tempo que demora a replicar para diferentes regiões depende da quantidade de dados a ser copiados e o número de regiões, que a versão é replicada para. Esta ação pode demorar algumas horas em alguns casos. Enquanto a replicação está a acontecer, pode ver o estado de replicação por região. Quando a replicação de imagem é concluída numa região, em seguida, pode implementar uma VM ou VMSS com essa versão de imagem na região.

![Gráfico que mostra como pode replicar imagens](./media/shared-image-galleries/replication.png)


## <a name="access"></a>Access
Como a Galeria de imagens de partilhado, a imagem partilhada e a versão de imagem partilhada estão todos os recursos, eles podem ser compartilhados com incorporada que RBAC do Azure nativo controla. Utilizar o RBAC pode partilhar estes recursos para outros utilizadores, principais de serviço e grupos na sua organização. É o âmbito de partilhar estes recursos no mesmo inquilino do Azure AD. Assim que um utilizador tem acesso para a versão de imagem partilhada, podem implementar uma VM ou um conjunto de dimensionamento de Máquina Virtual em qualquer uma das subscrições que têm acesso no mesmo Azure AD de inquilino como a versão de imagem partilhada.  Segue-se a matriz de partilha que o ajuda a compreender o que o utilizador obtém acesso a:

| Partilhado com utilizador     | Galeria de Imagens Partilhada | Imagem partilhada | Versão de imagem partilhada |
|----------------------|----------------------|--------------|----------------------|
| Galeria de Imagens Partilhada | Sim                  | Sim          | Sim                  |
| Imagem partilhada         | Não                   | Sim          | Sim                  |
| Versão de imagem partilhada | Não                   | Não           | Sim                  |



## <a name="billing"></a>Faturação
Não é sem custo adicional para utilizar o serviço de Galeria de imagens de partilhado. Será cobrado para os seguintes recursos:
- Custos de armazenamento de armazenar as versões de imagem partilhada. Isso depende do número de réplicas da versão e o número de regiões, que a versão é replicada para.
- Custos de saída de rede para replicação a partir da região de origem da versão para regiões replicadas.

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

**P.** Como me inscrevo para o partilhado imagem Galeria pré-visualização pública?
 
 R. Para inscrever-se na pré-visualização pública da Galeria de imagens de partilhado, precisa se registrar para o recurso, executando os seguintes comandos a partir de cada uma das subscrições no qual pretende criar uma galeria de imagens de partilhado, a definição de imagem ou a recursos da versão de imagem, e também onde pretende implementar máquinas virtuais com as versões de imagem.

**CLI**: 

```bash 
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register --name Microsoft.Compute
```

**PowerShell**: 

```powershell
Register-AzProviderFeature -FeatureName GalleryPreview -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

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


**P.** Como posso partilhar as minhas imagens entre subscrições?
 
 R. Pode partilhar imagens entre subscrições através do controlo de acesso baseado em ' (RBAC) da função. Qualquer utilizador que tem permissões de leitura para uma versão de imagem, até mesmo entre subscrições, será capaz de implantar uma Máquina Virtual utilizando a versão da imagem.


**P.** Posso mover a minha imagem existente para a Galeria de imagem partilhada?
 
 R. Sim. Existem 3 cenários com base nos tipos de imagens, que pode ter.

 Cenário 1: Se tiver uma imagem gerida, em seguida, pode criar uma definição de imagem e a versão da imagem do mesmo.

 Cenário 2: Se tiver uma imagem generalizada não gerida, pode criar uma imagem gerida a partir do mesmo e, em seguida, crie uma definição de imagem e a versão da imagem do mesmo. 

 Cenário 3: Se tiver um VHD no sistema de arquivos local, em seguida, terá de carregar o VHD, criar uma imagem gerida, em seguida, pode criar e a imagem de definição e a versão da imagem do mesmo.
- Se for o VHD de uma VM do Windows, veja [carregar um VHD generalizado](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se for o VHD para uma VM do Linux, veja [carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)


**P.** Pode criar uma versão de imagem de um disco especializado?

 R. Não, nós não suportam atualmente discos especializados como imagens. Se tiver um disco especializado, terá [criar uma VM a partir do VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal#create-a-vm-from-a-disk) ao anexar o disco especializado para uma nova VM. Assim que tiver uma VM em execução, tem de seguir as instruções para criar uma imagem gerida a partir da [VM do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-custom-images) ou [VM do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Depois de ter uma imagem gerida generalizada, pode iniciar o processo para criar uma descrição de imagem partilhada e a versão da imagem.


**P.** É possível criar uma galeria de imagem partilhada, a definição de imagem e a versão da imagem através do portal do Azure?

 R. Não, atualmente não suportamos a criação de qualquer um dos recursos de Galeria de imagens de partilhado através do portal do Azure. No entanto, suportamos a criação dos recursos de Galeria de imagens de partilhado através da CLI, modelos e SDKs. PowerShell também será lançado em breve.

 
**P.** Depois de criado, posso atualizar a definição de imagem ou a versão da imagem? Que tipo de detalhes posso modificar?

 R. Os detalhes que podem ser atualizados em cada um dos recursos são mencionados a seguir:
 
Galeria de imagem partilhada:
- Descrição

definição de imagem:
- VCPUs recomendada
- Memória
- Descrição
- Data de vida de fim de

Versão da imagem:
- Contagem de réplicas regional
- Regiões de destino
- Exclusão de versão mais recente
- Data de vida de fim de


**P.** Depois de criado, posso mover o recurso da Galeria de imagens de partilhado para uma subscrição diferente?

 R. Não, não é possível mover o recurso da Galeria de imagem partilhada para uma subscrição diferente. No entanto, será capaz de replicar as versões de imagem na Galeria para outras regiões conforme necessário.

**P.** Pode replicar minhas versões de imagem em clouds – Azure China 21Vianet, Azure Alemanha e a Cloud do Azure Government? 

 R. Não, não é possível replicar versões de imagem em várias clouds.

**P.** Pode replicar minhas versões de imagem entre subscrições? 

 R. Não, pode replicar as versões de imagem para regiões através de uma subscrição e utilizá-los com outras subscrições através do RBAC.

**P.** Pode partilhar as versões de imagem em inquilinos do Azure AD? 

 R. Não, a Galeria de imagens atualmente partilhada não suporta a partilha de versões de imagem em inquilinos do Azure AD. No entanto, pode utilizar a funcionalidade de ofertas de privado no Azure Marketplace para atingir esse objetivo.


**P.** Quanto tempo demora a replicar as versões de imagem em todas as regiões de destino?

 R. O tempo de replicação de versão de imagem é totalmente dependente do tamanho da imagem e o número de regiões que está a ser replicada para. No entanto, como melhor prática, é recomendado que mantenha a imagem pequena, e as regiões de origem e destino fechar-se para obter melhores resultados. Pode verificar o estado da replicação usando o sinalizador - ReplicationStatus.


**P.** O número de imagem partilhada de galerias podem criar uma subscrição?

 R. A quota predefinida é: 
- 10 galerias de imagem partilhada, por subscrição, por região
- 200 definições de imagem, por subscrição, por região
- versões de imagem de 2000, por subscrição, por região


**P.** O que é a diferença entre a região de origem e a região de destino?

 R. Região de origem é a região em que sua versão de imagem será criado e regiões de destino são as regiões em que uma cópia da sua versão de imagem será armazenada. Para cada versão de imagem, só pode ter uma região de origem. Além disso, certifique-se de que passe a localização de região de origem como uma das regiões de destino quando criar uma versão de imagem.  


**P.** Como posso especificar a região de origem ao criar a versão da imagem?

 R. Ao criar uma versão de imagem, pode utilizar o **– localização** etiqueta na CLI e o **-localização** marca no PowerShell para especificar a região de origem. Certifique-se de que a imagem gerida que está a utilizar como a imagem de base para criar a versão da imagem está na mesma localização como a localização na qual pretende criar a versão da imagem. Além disso, certifique-se de que passe a localização de região de origem como uma das regiões de destino quando criar uma versão de imagem.  


**P.** Como posso especificar o número de réplicas de versão da imagem a ser criada em cada região?

 R. Existem duas formas, pode especificar o número de réplicas de versão da imagem a ser criada em cada região:
 
1. A contagem de réplica regionais que especifica o número de réplicas que pretende criar por região. 
2. A contagem de réplica comum que é o padrão por contagem de região no caso de contagem de réplicas regionais não for especificada. 

Para especificar a contagem de réplica regionais, passe a localização, juntamente com o número de réplicas que pretende criar nessa região como este: "Dos E.U.A. centro-Sul = 2". 

Se a contagem de réplicas regionais não for especificada com cada localização, o número predefinido de réplicas será a contagem de réplica comuns que especificou. 

Para especificar a contagem de réplica comuns na CLI, utilize o **– contagem de réplicas** argumento no `az sig image-version create` comando.


**P.** Pode criar a Galeria de imagem partilhada numa localização diferente em que pretende criar a definição de imagem e a versão da imagem?

 R. Sim, isso é possível. Mas, como melhor prática, recomendamos que mantenha o grupo de recursos, a Galeria de imagens partilhado, a definição de imagem e a versão da imagem na mesma localização.


**P.** Quais são os custos para utilizar a Galeria de imagens partilhado?

 R. Não existem custos para utilizar o serviço de Galeria de imagens de partilhado, exceto os custos de armazenamento para armazenar as versões de imagem e os custos de saída de rede para replicar as versões de imagem a partir da região de origem para regiões de destino.

**P.** Qual versão de API deve usar para criar a Galeria de imagens de partilhado, definição de imagem, versão da imagem e VM/VMSS fora a versão da imagem?

 R. Para implementações de VM e conjunto de dimensionamento de Máquina Virtual a utilizar uma versão de imagem, recomendamos que utilize a versão de 2018-04-01 de API ou superior. Para trabalhar com galerias de imagem partilhada, definições de imagem e versões de imagem, recomendamos que utilize a API versão 2018-06-01. 
