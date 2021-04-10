---
title: Saiba mais sobre o Azure Image Builder (pré-visualização)
description: Saiba mais sobre o Azure Image Builder para máquinas virtuais em Azure.
author: danielsollondon
ms.author: danis
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 20bb6925f859d497046eb42bbafb5264826b77b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604071"
---
# <a name="preview-azure-image-builder-overview"></a>Pré-visualização: Visão geral do Azure Image Builder

As imagens padronizadas da máquina virtual (VM) permitem às organizações migrarem para a nuvem e garantirem a consistência nas implementações. As imagens normalmente incluem definições de segurança e configuração predefinidas e software necessário. Configurar o seu próprio pipeline de imagem requer tempo, infraestrutura e configuração, mas com o Azure VM Image Builder, basta fornecer uma configuração que descreva a sua imagem, submetê-la ao serviço, e a imagem é construída e distribuída.
 
O Azure VM Image Builder (Azure Image Builder) permite-lhe começar com uma imagem Azure Marketplace baseada em Windows ou Linux, imagens personalizadas existentes e começar a adicionar as suas próprias personalizações. Como o Image Builder é construído no [HashiCorp Packer,](https://packer.io/) você verá algumas semelhanças, mas terá o benefício de um serviço gerido. Também pode especificar onde gostaria que as suas imagens fossem hospedadas, na Galeria de [Imagens Partilhadas Azure,](shared-image-galleries.md)como uma imagem gerida ou um VHD.

> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funcionalidades de pré-visualização

Para a pré-visualização, estas funcionalidades são suportadas:

- Criação de imagens de base, que incluem a sua segurança mínima e configurações corporativas, e permite que os departamentos a personalizem ainda mais.
- Integração de aplicações core, para que o VM possa assumir cargas de trabalho após a criação, ou adicionar configurações para suportar imagens de Desktop Virtual do Windows.
- Remendar as imagens existentes, o Image Builder permitir-lhe-á corrigir continuamente as imagens personalizadas existentes.
- Conecte o construtor de imagens às suas redes virtuais existentes, para que possa ligar-se aos servidores de configuração existentes (DSC, Chef, Puppet, etc.), partilhas de ficheiros ou quaisquer outros servidores/serviços encaminháveis.
- A integração com a Galeria de Imagens Partilhadas Azure permite-lhe distribuir, ver e escalar imagens globalmente, e dá-lhe um sistema de gestão de imagem.
- Integração com os oleodutos de construção de imagem existentes, basta chamar O Construtor de Imagem do seu oleoduto ou usar a simples Tarefa de Pré-Visualização do Construtor de DevOps.
- Migrar um pipeline de personalização de imagem existente para Azure. Use os scripts, comandos e processos existentes para personalizar imagens.
- Criação de imagens em formato VHD para suportar Azure Stack.
 

## <a name="regions"></a>Regiões
O Azure Image Builder Service estará disponível para pré-visualização nestas regiões. As imagens podem ser distribuídas fora destas regiões.
- E.U.A. Leste
- E.U.A. Leste 2
- E.U.A. Centro-Oeste
- E.U.A. Oeste
- E.U.A. Oeste 2
- Europa do Norte
- Europa Ocidental

## <a name="os-support"></a>Suporte ao OS
O AIB apoiará imagens os OS base do Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multi-sessão/Profissional
- Windows 2016
- Windows 2019

## <a name="how-it-works"></a>Como funciona

O Azure VM Image Builder é um serviço Azure totalmente gerido que é acessível por um fornecedor de recursos Azure. Fornecer uma configuração para o serviço que especifica a imagem de origem, personalização para executar e onde a nova imagem deve ser distribuída, o diagrama abaixo mostra um fluxo de trabalho de alto nível:

![Desenho conceptual do processo Azure Image Builder mostrando as fontes (Windows/Linux), personalizações (Shell, PowerShell, Windows Restart & Update, adicionando ficheiros) e distribuição global com a Galeria de Imagem Partilhada Azure](./media/image-builder-overview/image-builder-flow.png)

As configurações do modelo podem ser passadas utilizando modelos PowerShell, Az CLI, ARM e utilizando a tarefa Azure VM Image Builder DevOps, quando o submeter ao serviço, criaremos um Recurso de Modelo de Imagem. Quando o Recurso do Modelo de Imagem for criado, verá um grupo de recursos de encenação criado na sua subscrição, no formato: IT_ \<DestinationResourceGroup> _\<TemplateName>_ \( GUID). O grupo de recursos de encenação contém ficheiros e scripts referenciados na personalização de File, Shell, PowerShell na propriedade ScriptURI.

Para executar a construção irá invocar `Run` no recurso Modelo de Imagem, o serviço irá então implementar recursos adicionais para a construção, tais como um VM, Rede, Disco, Adaptador de Rede, etc. Se construir uma imagem sem utilizar um VNET Image Builder existente também implementará um IP público e NSG, o serviço conecta-se à construção VM utilizando SSH ou WinRM. Se selecionar um VNET existente, o serviço será implantado utilizando o Azure Private Link, e não é necessário um endereço IP público, para obter mais detalhes sobre a rede de construtores de [imagens](./linux/image-builder-networking.md)rever os detalhes .

Quando a construção terminar todos os recursos serão eliminados, com exceção do grupo de recursos de encenação e da conta de armazenamento, para os remover, eliminará o recurso do Modelo de Imagem, ou poderá deixá-los lá para executar novamente a construção.

Existem vários exemplos e guias passo a passo nesta documentação, que referenciam modelos e soluções no [repositório gitHub do construtor de imagem Azure.](https://github.com/azure/azvmimagebuilder)

### <a name="move-support"></a>Suporte de movimento
O recurso do modelo de imagem é imutável e contém ligações com os recursos e o grupo de recursos de encenação, pelo que o tipo de recurso não suporta a deslocação. Se desejar mover o recurso do modelo de imagem, certifique-se de que tem uma cópia do modelo de configuração (extrair a configuração existente do recurso se não o tiver), crie um novo recurso de modelo de imagem no novo grupo de recursos com um novo nome e elimine o recurso do modelo de imagem anterior. 

## <a name="permissions"></a>Permissões
Quando se regista para o (AIB), este concede ao Serviço AIB permissão para criar, gerir e eliminar um grupo de recursos de encenação (IT_*), e tem direitos de adicionar recursos a ele, que são necessários para a construção de imagem. Isto é feito por um Nome Principal de Serviço AIB (SPN) sendo disponibilizado na sua subscrição durante uma inscrição bem sucedida.

Para permitir que o Azure VM Image Builder distribua imagens para as imagens geridas ou para uma Galeria de Imagens Partilhadas, terá de criar uma identidade atribuída ao utilizador Azure que tenha permissões para ler e escrever imagens. Se estiver a aceder ao armazenamento do Azure, então isto necessitará de permissões para ler contentores privados e públicos.

As permissões são explicadas mais detalhadamente para [PowerShell](./linux/image-builder-permissions-powershell.md)e [AZ CLI](./linux/image-builder-permissions-cli.md).

## <a name="costs"></a>Custos
Incorrerá em alguns custos de computação, networking e armazenamento ao criar, construir e armazenar imagens com o Azure Image Builder. Estes custos são semelhantes aos custos incorridos na criação manual de imagens personalizadas. Pelos recursos, será cobrado às suas tarifas Azure. 

Durante o processo de criação de imagens, os ficheiros são descarregados e armazenados no `IT_<DestinationResourceGroup>_<TemplateName>` grupo de recursos, o que incorrerá em pequenos custos de armazenamento. Se não quiser guardá-las, elimine o **Modelo de Imagem** após a construção da imagem.
 
O Image Builder cria um VM utilizando um tamanho D1v2 VM, e o armazenamento e networking necessários para o VM. Estes recursos durarão a duração do processo de construção, e serão eliminados assim que o Image Builder terminar de criar a imagem. 
 
O Azure Image Builder distribuirá a imagem para as regiões escolhidas, o que poderá incorrer em encargos de saída de rede.

## <a name="hyper-v-generation"></a>Geração Hiper-V
Atualmente, o Image Builder apenas suporta de forma nativa a criação de imagens de geração Hyper-V (Gen1) 1 para a Galeria de Imagem Partilhada Azure (SIG) ou Imagem Gerida. 
 
## <a name="next-steps"></a>Passos seguintes 
 
Para experimentar o Azure Image Builder, consulte os artigos para a construção de imagens [Linux](./linux/image-builder.md) ou [Windows.](./windows/image-builder.md)