---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: ec1b77118f94501363d950d72a65a67ece79ff77
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748886"
---
As imagens de máquinas virtuais padronizadas (VM) permitem às organizações migrar para a nuvem e garantir a consistência nas implementações. As imagens normalmente incluem definições de segurança e configuração predefinidas e software necessário. A configuração do seu próprio gasoduto de imagem requer tempo, infraestrutura e configuração, mas com o Azure VM Image Builder, basta fornecer uma configuração simples descrevendo a sua imagem, submeta-a ao serviço, e a imagem é construída e distribuída.
 
O Azure VM Image Builder (Azure Image Builder) permite-lhe começar com uma imagem azure marketplace baseada no Windows ou linux, imagens personalizadas existentes ou Red Hat Enterprise Linux (RHEL) ISO e começar a adicionar as suas próprias personalizações. Como o Image Builder é construído em [HashiCorp Packer,](https://packer.io/)você também pode importar seus scripts de provisão packer existentes. Também pode especificar onde gostaria que as suas imagens fossem hospedadas, na Galeria de [Imagem Partilhada Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)como uma imagem gerida ou um VHD.

> [!IMPORTANT]
> O construtor de imagem do Azure está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funcionalidades de pré-visualização

Para a pré-visualização, estas funcionalidades são suportadas:

- Criação de imagens de base dourada, que incluem a sua segurança mínima e configurações corporativas, e permitem que os departamentos as personalizem ainda mais para as suas necessidades.
- Patching de imagens existentes, O Image Builder permitir-lhe-á corrigir continuamente as imagens personalizadas existentes.
- A integração com a Azure Shared Image Gallery permite-lhe distribuir, versão e escalar imagens globalmente, e dá-lhe um sistema de gestão de imagem.
- Integração com os oleodutos de construção de imagem existentes, basta ligar para o Image Builder do seu pipeline ou utilizar a simples Tarefa de Preview Image Builder Azure DevOps.
- Migrar um oleoduto de personalização de imagem existente para Azure. Utilize os seus scripts, comandos e processos existentes para personalizar imagens.
- Use o Chapéu Vermelho Traga o seu próprio suporte de subscrição. Crie imagens Red Hat Enterprise para uso com as suas subscrições de Chapéu Vermelho elegíveis e não utilizadas.
- Criação de imagens em formato VHD.
 

## <a name="regions"></a>Regiões
O Azure Image Builder Service estará disponível para pré-visualização nestas regiões. As imagens podem ser distribuídas fora destas regiões.
- E.U.A. Leste
- EUA Leste 2
- E.U.A. Centro-Oeste
- EUA Oeste
- EUA Oeste 2

## <a name="os-support"></a>Apoio ao OS
O AIB apoiará as imagens ossia base do Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- Centos 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Professional/Enterprise for Virtual Desktop (EVD) 
- Windows 2016
- Windows 2019

O AIB apoiará o RHEL ISO's, como fonte para:
- RHEL 7,3
- RHEL 7,4
- RHEL 7.5

Os ISOs RHEL 7.6 não são suportados, mas estão a ser testados.

## <a name="how-it-works"></a>Como funciona


![Desenho conceptual do Construtor de Imagem Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

O Azure Image Builder é um serviço Azure totalmente gerido que é acessível por um fornecedor de recursos Azure. O processo Azure Image Builder tem três partes principais: fonte, personalizar e distribuir, estas estão representadas num modelo. O diagrama abaixo mostra os componentes, com algumas das suas propriedades. 
 


**Processo de Construtor de Imagem** 

![Desenho conceptual do processo azure image builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Crie o Modelo de Imagem como um ficheiro .json. Este ficheiro .json contém informações sobre a fonte de imagem, personalizações e distribuição. Existem vários exemplos no [repositório Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Submeta-o ao serviço, isto criará um artefacto modelo de imagem no grupo de recursos que especifica. Em segundo plano, o Image Builder irá descarregar a imagem de origem ou ISO, e scripts conforme necessário. Estes são armazenados num grupo de recursos separado que é automaticamente criado na sua subscrição, no formato: IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. Uma vez criado o Modelo de Imagem, pode então construir a imagem. Em segundo plano, o Image Builder utiliza o modelo e os ficheiros de origem para criar um VM (tamanho padrão: Standard_D1_v2), rede, IP público, NSG e armazenamento no IT_\<DestinationResourceGroup>_\<TemplateName> grupo de recursos.
1. Como parte da criação de imagem, o construtor de imagem distribui a imagem de acordo com o modelo, e depois elimina os recursos adicionais no IT_\<DestinationResourceGroup>_\<TemplateName> grupo de recursos que foi criado para o processo.


## <a name="permissions"></a>Permissões

Para permitir que o Azure VM Image Builder distribua imagens para as imagens geridas ou para uma Galeria de Imagem Partilhada, terá de fornecer permissões 'Contributiva' para o serviço "Azure Virtual Machine Image Builder" (id da aplicação: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ), nos grupos de recursos. 

Se estiver a utilizar uma versão de imagem ou imagem personalizada existente, então o Azure Image Builder necessitará de um mínimo de acesso 'Reader' a esses grupos de recursos.

Pode atribuir acesso utilizando o Azure CLI:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Pode atribuir acesso utilizando o PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Se a conta de serviço não for encontrada, isso pode significar que a subscrição onde está a adicionar a atribuição de funções ainda não se registou para o fornecedor de recursos.


## <a name="costs"></a>Custos
Incorrerá em alguns custos de computação, networking e armazenamento ao criar, construir e armazenar imagens com o Azure Image Builder. Estes custos são semelhantes aos custos incorridos na criação manual de imagens personalizadas. Pelos recursos, será cobrado às suas tarifas Azure. 

Durante o processo de criação de imagem, os ficheiros são descarregados e armazenados no grupo de recursos `IT_<DestinationResourceGroup>_<TemplateName>`, o que incorrerá em pequenos custos de armazenamento. Se não quiser mantê-los, elimine o Modelo de **Imagem** após a construção da imagem.
 
O Image Builder cria um VM utilizando um tamanho D1v2 VM, e o armazenamento e a rede necessários para o VM. Estes recursos durarão durante a duração do processo de construção, e serão eliminados assim que o Image Builder terminar de criar a imagem. 
 
O Azure Image Builder distribuirá a imagem para as regiões escolhidas, o que poderá incorrer em cargas de egress de rede.
 
## <a name="next-steps"></a>Passos Seguintes 
 
Para experimentar o Azure Image Builder, consulte os artigos para construir imagens [linux](../articles/virtual-machines/linux/image-builder.md) ou [Windows.](../articles/virtual-machines/windows/image-builder.md)
 
 
