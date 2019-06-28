---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 5884bb5dc389e6abfd4fa23d28dd71ecd16ff423
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67347118"
---
Imagens de padronizada de máquinas virtuais (VM) permitem às organizações migrar para a cloud e garantir a consistência nas Implantações. Imagens incluem, geralmente, as definições predefinidas de segurança e a configuração e de software necessárias. Definir o seu pipeline de processamento de imagens exige tempo, a infraestrutura e a configuração, mas com o construtor de imagens de VM do Azure, basta indicar uma configuração simples que descrevem a sua imagem, submetê-lo para o serviço e a imagem é criada e distribuída.
 
O construtor de imagens de VM do Azure (construtor de imagens do Azure) permite-lhe começar com um Windows ou imagem da baseado em Linux do Azure Marketplace, as imagens personalizadas existentes ou ISO do Red Hat Enterprise Linux (RHEL) e começar a adicionar suas próprias personalizações. Porque o construtor de imagens baseia [HashiCorp Packer](https://packer.io/), também pode importar seus scripts de provisioner de shell Packer existentes. Também pode especificar onde pretende que suas imagens alojadas no Azure partilhado Galeria de imagens (virtual-machines-common-shared-image-galleries.md), como uma imagem gerida ou um VHD.

> [!IMPORTANT]
> Construtor de imagens do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funcionalidades de pré-visualização

Para a pré-visualização, estas funcionalidades são suportadas:

- Criação de imagens de linha de base dourada, que inclui o mínimo de segurança e configurações de empresa e permitir que os departamentos de TI personalizá-lo ainda mais para as suas necessidades.
- Aplicação de patches de imagens existentes, construtor de imagens permitirá que continuamente aplicar patches em imagens personalizadas existentes.
- Integração com a Galeria de imagens do Azure partilhado, permite que distribua, versão, e o dimensionamento imagens globalmente e dá-lhe um sistema de gestão de imagem.
- Integração com a imagem existente Criar pipelines, basta chamar o construtor de imagens a partir do seu pipeline ou utilizar a pré-visualização imagem construtor de Azure DevOps tarefa simples.
- Migre um pipeline de personalização de imagem existente para o Azure. Use seus scripts existentes, comandos e processos para personalizar imagens.
- Utilize o suporte de traga a sua própria subscrição do Red Hat. Crie imagens de Red Hat Enterprise para utilização com subscrições do Red Hat elegíveis, não utilizadas.
- Criação de imagens no formato VHD.
 

## <a name="regions"></a>Regions
O serviço de construtor de imagem do Azure vai estar disponível para pré-visualização nestas regiões. Imagens podem ser distribuídas fora nestas regiões.
- EUA Leste
- EUA Leste 2
- EUA Centro-Oeste
- EUA Oeste
- EUA Oeste 2

## <a name="os-support"></a>Suporte do sistema operacional
AIB oferecerá suporte a imagens de sistema operacional de bases do Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6
- CentOS 7.6
- Windows 2016
- Windows 2019


## <a name="how-it-works"></a>Como funciona


![Desenho conceptual do construtor de imagens do Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

O construtor de imagens do Azure é um serviço completamente gerido do Azure que pode ser acessado por um fornecedor de recursos do Azure. O processo de construtor de imagens do Azure tem três partes principais: de origem, personalizar e distribuir, estes são representados num modelo. O diagrama abaixo mostra os componentes, com algumas das suas propriedades. 
 


**Processo de construtor de imagem** 

![Desenho conceptual do processo do construtor de imagens do Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Crie o modelo de imagem como um ficheiro. JSON. Este ficheiro. JSON contém informações sobre a origem da imagem, personalizações e a distribuição. Existem vários exemplos de [repositório do GitHub de construtor de imagem do Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Enviá-lo para o serviço, isto irá criar um artefato de modelo de imagem no grupo de recursos que especificar. Em segundo plano, o construtor de imagens irá transferir a imagem de origem ou ISO e scripts conforme necessário. Eles são armazenados num grupo de recursos separado que é criado automaticamente na sua subscrição, no formato: IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. Depois de criar o modelo de imagem, em seguida, pode criar a imagem. Em segundo plano o construtor de imagens usa os modelo e arquivos de origem para criar uma VM, rede e armazenamento no IT_\<DestinationResourceGroup > _\<TemplateName > grupo de recursos.
1. Como parte da criação de imagem, o construtor de imagens distribui a imagem de acordo com o modelo, em seguida, elimina os recursos adicionais no IT_\<DestinationResourceGroup > _\<TemplateName > grupo de recursos que foi criado para o processo.


## <a name="permissions"></a>Permissões

Para permitir que o construtor de imagens de VM do Azure distribuir imagens para as imagens de gerido ou a uma galeria de imagens de partilhado, terá de fornecer permissões de "Contribuinte" para o serviço "Construtor de imagens de Máquina Virtual do Azure" (ID da aplicação: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) sobre os grupos de recursos. 

Se estiver a utilizar uma imagem gerida personalizada existente ou a versão da imagem, em seguida, o construtor de imagens do Azure terá um mínimo de acesso de "Reader" aos grupos de recursos.

É possível atribuir o acesso ao utilizar a CLI do Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Se a conta de serviço não for encontrada, isso pode significar que a subscrição em que estiver a adicionar a atribuição de função não tem ainda registada para o fornecedor de recursos.


## <a name="costs"></a>Custos
Incorrerá em alguns computação, rede e os custos de armazenamento ao criar, criar e armazenar imagens com o construtor de imagens do Azure. Esses custos são semelhantes dos custos inerentes ao criar manualmente as imagens personalizadas. Para os recursos, será cobrada às tarifas do Azure. 

Durante o processo de criação de imagem, os ficheiros são transferidos e armazenados no `IT_<DestinationResourceGroup>_<TemplateName>` grupo de recursos que irá incorrer em custos de armazenamento pequeno. f não desejar manter estas, eliminar o modelo de imagem após a compilação de imagem.
 
Construtor de imagens cria uma VM com um tamanho de D1v2 VM e o armazenamento e de rede necessários para a VM. Esses recursos duração durante o processo de compilação e serão eliminados quando o construtor de imagens acabou de criar a imagem. 
 
Construtor de imagens do Azure irá distribuir a imagem para as regiões escolhidas, o que pode implicar custos de saída de rede.
 
## <a name="next-steps"></a>Passos Seguintes 
 
Para experimentar o construtor de imagens do Azure, veja os artigos para construção [Linux](../articles/virtual-machines/linux/image-builder.md) ou [Windows](../articles/virtual-machines/windows/image-builder.md) imagens.
 
 
