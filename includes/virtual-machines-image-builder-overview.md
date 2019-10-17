---
author: cynthn
ms.author: cynthn
ms.date: 04/30/2019
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2bd40db51d82bd2278bd716615636968adf8277b
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391749"
---
As imagens de VM (máquina virtual) padronizadas permitem que as organizações migrem para a nuvem e garantam a consistência nas implantações. As imagens normalmente incluem configurações predefinidas de segurança e configuração e software necessário. Configurar seu próprio pipeline de geração de imagens requer tempo, infraestrutura e configuração, mas com o construtor de imagem de VM do Azure, basta fornecer uma configuração simples que descreva sua imagem, enviá-la ao serviço, e a imagem seja criada e distribuída.
 
O construtor de imagem de VM do Azure (Construtor de imagens do Azure) permite que você comece com uma imagem do Azure Marketplace baseada em Windows ou Linux, imagens personalizadas existentes ou Red Hat Enterprise Linux (RHEL) ISO e comece a adicionar suas próprias personalizações. Como o construtor de imagem se baseia no [HashiCorp Packer](https://packer.io/), você também pode importar os scripts de provisionamento do shell do Pack existente. Você também pode especificar onde deseja que suas imagens sejam hospedadas, na [Galeria de imagens compartilhadas do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), como uma imagem gerenciada ou um VHD.

> [!IMPORTANT]
> O construtor de imagem do Azure está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Funcionalidades de pré-visualização

Para a versão prévia, há suporte para esses recursos:

- Criação de imagens de linha de base Golden, que inclui a segurança mínima e as configurações corporativas, e permite que os departamentos a personalizem ainda mais para suas necessidades.
- Aplicação de patch de imagens existentes, o Image Builder permitirá que você corrija continuamente as imagens personalizadas existentes.
- A integração com a Galeria de imagens compartilhadas do Azure permite distribuir, apresentar e dimensionar imagens globalmente e fornece um sistema de gerenciamento de imagens.
- Integração com pipelines de Build de imagem existentes, basta chamar o Image Builder de seu pipeline ou usar a tarefa Simple DevOps do Azure Image Builder.
- Migre um pipeline de personalização de imagem existente para o Azure. Use seus scripts, comandos e processos existentes para personalizar imagens.
- Use a Red Hat para dar suporte à sua própria assinatura. Crie imagens do Red Hat Enterprise para uso com suas assinaturas do Red Hat qualificadas e não utilizadas.
- Criação de imagens no formato VHD.
 

## <a name="regions"></a>Regiões
O serviço do construtor de imagens do Azure estará disponível para visualização nessas regiões. As imagens podem ser distribuídas fora dessas regiões.
- Este dos E.U.A.
- Este dos E.U.A. 2
- E.U.A. Centro-Oeste
- Oeste dos E.U.A.
- E.U.A. Oeste 2

## <a name="os-support"></a>Suporte do so
O AIB dará suporte a imagens do sistema operacional base do Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6
- CentOS 7,6
- Windows 10 RS5 Enterprise/Professional/Enterprise para área de trabalho virtual (EVD) 
- Windows 2016
- Windows 2019

O AIB dará suporte a ISO do RHEL, como uma fonte para:
- RHEL 7,3
- RHEL 7,4
- RHEL 7.5

RHEL 7,6 ISOs não têm suporte, mas estão sendo testados.

## <a name="how-it-works"></a>Como funciona


![Desenho conceitual do construtor de imagens do Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

O construtor de imagens do Azure é um serviço do Azure totalmente gerenciado que é acessível por um provedor de recursos do Azure. O processo do construtor de imagens do Azure tem três partes principais: origem, personalizar e distribuir, que são representadas em um modelo. O diagrama a seguir mostra os componentes, com algumas de suas propriedades. 
 


**Processo do construtor de imagem** 

![Desenho conceitual do processo do construtor de imagem do Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Crie o modelo de imagem como um arquivo. JSON. Esse arquivo. JSON contém informações sobre a origem, as personalizações e a distribuição da imagem. Há vários exemplos no [repositório GitHub do Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Enviá-lo para o serviço; isso criará um artefato de modelo de imagem no grupo de recursos que você especificar. Em segundo plano, o Image Builder baixará a imagem de origem ou a ISO e os scripts, conforme necessário. Eles são armazenados em um grupo de recursos separado que é criado automaticamente em sua assinatura, no formato: IT_ @ no__t-0DestinationResourceGroup > _ @ no__t-1TemplateName >. 
1. Depois que o modelo de imagem for criado, você poderá criar a imagem. No construtor de imagem de plano de fundo usa o modelo e os arquivos de origem para criar uma VM (D1v2), rede, IP público e armazenamento no grupo de recursos IT_ @ no__t-0DestinationResourceGroup > _ @ no__t-1TemplateName >.
1. Como parte da criação da imagem, o Image Builder distribui a imagem de acordo com o modelo e, em seguida, exclui os recursos adicionais no grupo de recursos IT_ @ no__t-0DestinationResourceGroup > _ @ no__t-1TemplateName > que foi criado para o processo.


## <a name="permissions"></a>Permissões

Para permitir que o construtor de imagens de VM do Azure distribua imagens para as imagens gerenciadas ou para uma galeria de imagens compartilhadas, você precisará fornecer permissões de ' colaborador ' para o serviço "Construtor de imagem de máquina virtual do Azure" (ID do aplicativo: cf32a0cc-373c-47c9-9156-0db11f6a6dfc ) nos grupos de recursos. 

Se você estiver usando uma imagem gerenciada personalizada ou uma versão de imagem existente, o construtor de imagens do Azure precisará de, no mínimo, o acesso de "leitor" a esses grupos de recursos.

Você pode atribuir acesso usando o CLI do Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Se a conta de serviço não for encontrada, isso pode significar que a assinatura em que você está adicionando a atribuição de função ainda não foi registrada para o provedor de recursos.


## <a name="costs"></a>Custos
Você incorrerá em alguns custos de computação, rede e armazenamento ao criar, criar e armazenar imagens com o Azure Image Builder. Esses custos são semelhantes aos custos incorridos na criação manual de imagens personalizadas. Para os recursos, você será cobrado com suas tarifas do Azure. 

Durante o processo de criação de imagem, os arquivos são baixados e armazenados no grupo de recursos `IT_<DestinationResourceGroup>_<TemplateName>`, o que incorrerá em um pequeno custo de armazenamento. Se você não quiser mantê-los, exclua o **modelo de imagem** após a criação da imagem.
 
O Image Builder cria uma VM usando um tamanho de VM D1v2 e o armazenamento e a rede necessários para a VM. Esses recursos durarão por último a duração do processo de compilação e serão excluídos assim que o construtor de imagem terminar de criar a imagem. 
 
O construtor de imagens do Azure distribuirá a imagem para as regiões escolhidas, o que pode incorrer em encargos de saída de rede.
 
## <a name="next-steps"></a>Passos seguintes 
 
Para experimentar o construtor de imagens do Azure, consulte os artigos para criar imagens do [Linux](../articles/virtual-machines/linux/image-builder.md) ou do [Windows](../articles/virtual-machines/windows/image-builder.md) .
 
 
