---
author: cynthn
ms.author: cynthn
ms.date: 08/03/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: daberry
ms.openlocfilehash: 6024dd595166afe24a75720d8c7593afd451b610
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554893"
---
As imagens padronizadas da máquina virtual (VM) permitem às organizações migrarem para a nuvem e garantirem a consistência nas implementações. As imagens normalmente incluem definições de segurança e configuração predefinidas e software necessário. Configurar o seu próprio pipeline de imagem requer tempo, infraestrutura e configuração, mas com o Azure VM Image Builder, basta fornecer uma configuração simples descrevendo a sua imagem, submetê-la ao serviço, e a imagem é construída e distribuída.
 
O Azure VM Image Builder (Azure Image Builder) permite-lhe começar com uma imagem Azure Marketplace baseada em Windows ou Linux, imagens personalizadas existentes ou Red Hat Enterprise Linux (RHEL) ISO e começar a adicionar as suas próprias personalizações. Como o Image Builder é construído no [HashiCorp Packer,](https://packer.io/)também pode importar os seus scripts de provisões packer existentes. Também pode especificar onde gostaria que as suas imagens fossem hospedadas, na Galeria de [Imagens Partilhadas Azure,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)como uma imagem gerida ou um VHD.

> [!IMPORTANT]
> O Azure Image Builder está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funcionalidades de pré-visualização

Para a pré-visualização, estas funcionalidades são suportadas:

- Criação de imagens de base dourada, que incluem a sua segurança mínima e configurações corporativas, e permite que os departamentos personalizem ainda mais para as suas necessidades.
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

O apoio da RHEL ISOs já não é apoiado.

## <a name="how-it-works"></a>Como funciona


![Desenho conceptual do Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder.png)

O Azure Image Builder é um serviço Azure totalmente gerido que é acessível por um fornecedor de recursos Azure. O processo Azure Image Builder tem três partes principais: fonte, personalização e distribuição, estas estão representadas num modelo. O diagrama abaixo mostra os componentes, com algumas das suas propriedades. 
 


**Processo de construtor de imagem** 

![Desenho conceptual do processo Azure Image Builder](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Crie o Modelo de Imagem como um ficheiro .json. Este ficheiro .json contém informações sobre a fonte de imagem, personalizações e distribuição. Existem vários exemplos no [repositório gitHub do construtor de imagens Azure.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)
1. Submeta-o ao serviço, isto criará um artefacto de Modelo de Imagem no grupo de recursos que especifica. Em segundo plano, o Image Builder irá descarregar a imagem de origem ou ISO, e scripts conforme necessário. Estes são armazenados num grupo de recursos separado que é automaticamente criado na sua subscrição, no formato: IT_ \<DestinationResourceGroup> _ \<TemplateName> . 
1. Uma vez criado o Modelo de Imagem, pode então construir a imagem. Em segundo plano, o Image Builder utiliza o modelo e os ficheiros de origem para criar um VM (tamanho padrão: Standard_D1_v2), rede, IP público, NSG e armazenamento no grupo de recursos IT_ \<DestinationResourceGroup> \<TemplateName> _.
1. Como parte da criação de imagem, o construtor de imagem distribui a imagem de acordo com o modelo, em seguida, elimina os recursos adicionais no grupo de recursos IT_ \<DestinationResourceGroup> _ que foi criado para o \<TemplateName> processo.


## <a name="permissions"></a>Permissões
Quando se regista para o (AIB), este concede ao Serviço AIB permissão para criar, gerir e eliminar um grupo de recursos de encenação (IT_*), e tem direitos de adicionar recursos a ele, que são necessários para a construção de imagem. Isto é feito por um Nome Principal de Serviço AIB (SPN) sendo disponibilizado na sua subscrição durante uma inscrição bem sucedida.

Para permitir que o Azure VM Image Builder distribua imagens para as imagens geridas ou para uma Galeria de Imagens Partilhadas, terá de criar uma identidade atribuída ao utilizador Azure que tenha permissões para ler e escrever imagens. Se estiver a aceder ao armazenamento do Azure, então isto necessitará de permissões para ler recipientes privados.

Inicialmente deve seguir [criar documentação de identidade gerida atribuída pelo utilizador Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli) sobre como criar uma identidade.

Uma vez que tenha a identidade, precisa de lhe conceder permissões, para o fazer, pode utilizar uma Definição de Função Personalizada Azure e, em seguida, atribuir a identidade gerida atribuída ao utilizador para utilizar a Definição de Função Personalizada.

As permissões são explicadas mais detalhadamente [aqui](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements), e os exemplos mostram como isso é implementado.

> [!Note]
> Anteriormente com o AIB, utilizaria o AIB SPN e concederia as permissões SPN aos grupos de recursos de imagem. Estamos a afastar-nos deste modelo, para permitir capacidades futuras. A partir de 26 de maio de 2020, o Image Builder não aceitará modelos que não tenham uma identidade atribuída ao utilizador, os modelos existentes terão de ser reenviados ao serviço com [identidade de utilizador.](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#identity) Os exemplos aqui já mostram como pode criar uma identidade atribuída ao utilizador e adicioná-las a um modelo. Para mais informações, por favor reveja esta [documentação](https://github.com/danielsollondon/azvmimagebuilder#service-updates-and-latest-release-information) sobre esta alteração e lança atualizações.

## <a name="costs"></a>Custos
Incorrerá em alguns custos de computação, networking e armazenamento ao criar, construir e armazenar imagens com o Azure Image Builder. Estes custos são semelhantes aos custos incorridos na criação manual de imagens personalizadas. Pelos recursos, será cobrado às suas tarifas Azure. 

Durante o processo de criação de imagens, os ficheiros são descarregados e armazenados no `IT_<DestinationResourceGroup>_<TemplateName>` grupo de recursos, o que incorrerá em pequenos custos de armazenamento. Se não quiser guardá-las, elimine o **Modelo de Imagem** após a construção da imagem.
 
O Image Builder cria um VM utilizando um tamanho D1v2 VM, e o armazenamento e networking necessários para o VM. Estes recursos durarão a duração do processo de construção, e serão eliminados assim que o Image Builder terminar de criar a imagem. 
 
O Azure Image Builder distribuirá a imagem para as regiões escolhidas, o que poderá incorrer em encargos de saída de rede.

## <a name="hyper-v-generation"></a>Geração Hiper-V
O Image Builder atualmente suporta imagens e VMs da geração Hyper-V.
 
## <a name="next-steps"></a>Próximos passos 
 
Para experimentar o Azure Image Builder, consulte os artigos para a construção de imagens [Linux](../articles/virtual-machines/linux/image-builder.md) ou [Windows.](../articles/virtual-machines/windows/image-builder.md)
 
