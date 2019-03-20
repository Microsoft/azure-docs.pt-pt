---
title: Separador de SKUs de máquina virtual no Portal de parceiros da Cloud para o Azure Marketplace | Documentos da Microsoft
description: Descreve a guia de SKUs usada na criação de uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: e05813297707165d59d1b1ed969b54fb0f433277
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57848095"
---
# <a name="virtual-machine-skus-tab"></a>Separador de SKUs de máquina virtual

O **SKUs** separador da **nova oferta** página permite-lhe criar um ou mais SKUs e associá-las a sua nova oferta.  SKUs diferentes podem diferenciar uma solução por conjuntos de recursos, tipos de imagem VM, débito ou escalabilidade, modelos de faturação ou alguns outra característica.

## <a name="create-a-sku"></a>Criação de um SKU

Inicialmente, uma nova oferta não terão qualquer SKUs associados, pelo que irá criar um clicando **novo SKU**.

![Novo botão SKU no separador da nova oferta para máquinas virtuais](./media/publishvm_005.png)

<br/>

O **novo SKU** é apresentada a caixa de diálogo.  Introduza o identificador para o novo SKU, em seguida, clique em **OK**. (Veja abaixo para as convenções de nomenclatura do identificador).  O **SKUs** separador passará a apresentar os campos disponíveis para edição.    Um anexado asterisco (*) no nome do campo indica que é necessário.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Separador SKU para o formulário nova oferta para máquinas virtuais](./media/publishvm_006.png)

A tabela seguinte descreve a finalidade, conteúdo e a formatação desses campos.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
|  *Definições de SKU*   |  |
| **ID DO SKU**       | Identificador para este SKU.  Este nome tem um máximo de 50 caracteres, consistindo em minúsculas, carateres alfanuméricos ou traços (-), mas não pode terminar com um traço.  Não pode ser modificado após a publicação da oferta.  |
|  *Detalhes SKU*   |  |
| **Title** (Título)        | Nome amigável para a oferta para exibição no marketplace. Comprimento máximo de 50 carateres. |
| **Resumo**      | Sucinta descrição da oferta para exibição no marketplace. Comprimento máximo de 100 carateres. |
| **Descrição**  | Texto de descrição que fornece uma explicação mais detalhada da oferta.  <!-- TD: max len/guidance? 3k characters -->  |
| **Ocultar este SKU** | Indica se o SKU deve estar visível no marketplace para clientes.  Pode querer ocultar o SKU, se quiser apenas disponível apenas através de modelos de soluções e não para compra individualmente.  Também pode ser útil para testes iniciais ou para as ofertas temporárias ou sazonais. |
| **Disponibilidade da cloud** | Determina em qual clouds o SKU deve estar disponível.  A predefinição é a versão pública do Azure.  O Microsoft Azure Government é uma cloud de Comunidade governamental com acesso controlado para o Federal, estado, governos locais ou Tribais e respetivos parceiros certificados.  Para obter mais informações sobre a cloud para administração pública, consulte [bem-vindo ao Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Este é um SKU de privada?** | Indica se o SKU é privado ou público. A predefinição é **não** (público).  Para obter mais informações, consulte [pública e privada SKUs](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Disponibilidade de país/região** | Determina que países ou regiões do mundo o SKU estará disponível para compra. Selecione pelo menos uma região/país. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Preços*   |  |
| **Modelo de licença**| Modelo de faturação padronizado para usar.  Se selecionou **baseada na utilização mensal cobrado SKU**, uma seção accordion serão abertas para permitir que especifique os detalhes dos preços por núcleo e se pretender oferecer um período de avaliação gratuita.  Esta secção também permite-lhe exportar e importar este agendamento de preços para o Excel. Para obter mais informações, consulte [opções de faturação no Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Imagens de VM*   |  |
| **Família do sistema operativo** | Indica se a solução de VM é baseado em Windows ou Linux. |
| **Selecione o tipo de sistema operativo** | Fornecedor específico ou a versão do sistema operativo especificado. |
| **Nome amigável de SO** | Nome a apresentar aos clientes do sistema operativo.  |
| **Recomendado tamanhos de VM** | Permite a seleção de tamanhos de VM recomendados até seis de uma lista padronizada.  Embora estas recomendações destaque são apresentadas aos clientes potenciais, eles são capazes de especificar qualquer tamanho de VM que é compatível com a imagem de solução. | 
| **Portas abertas**| Portas para abrir e de protocolo para oferecer suporte para o SKU.  Estas configurações têm de corresponder a rede virtual que configurou para a rede da solução de VM. Estas definições entram em vigor durante a implementação de VM. No entanto, as definições de porta podem ser modificadas depois de publicar um SKU. Para obter mais informações, consulte [como abrir portas para uma máquina virtual com o portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Os seguintes mapeamentos de rede padrão são adicionados a todas as VMs. &emsp; Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Versão de disco**  | Solução associada VM, especificada pelo número de versão do disco e o URL do disco. A versão de disco tem de constar [versão semântica](https://semver.org/) formato: `<major>.<minor>.<patch>`.  O URL é a assinatura de acesso partilhado criado para o sistema de operativo VHD.  Embora, pode adicionar até oito versões de disco por SKU, apenas o maior disco número da versão para um SKU serão apresentados no Azure Marketplace. Outras versões só estará visíveis através de APIs.  <!--TD: Add more specific link to API --> <br/> O **disco de dados novo** accordion secção permite-lhe ligar até 15 discos de dados a sua VM.  Depois de publicar um SKU com uma determinada versão VM e discos de dados associados, esta configuração não pode ser modificada.  Se as versões VM adicionais são adicionadas ao SKU, também tem de suportar o mesmo número de discos de dados. <br/> Se não tiver criado as imagens de VM baseada no Azure, pode adicionar atualizar mais tarde neste campo.  Para obter informações sobre como criar o recurso VM associado, consulte a secção [ativos técnicos de criar VM](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Clique em **guardar** para guardar o seu progresso. No separador seguinte, irá especificar se a sua oferta suporta [Test-Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Preço considerações adicionais

O modelo de preços descrito acima é uma descrição básica.  Ele está passando por alterações e pode ser afetado pelas políticas de preços do Microsoft e regulamentações de imposto sobre o local ou regional. 

### <a name="simplified-currency-pricing"></a>Preços de moeda simplificada

A partir de 1 de Setembro de 2018, chamado de uma nova seção **preços de moeda simplificado** será adicionado ao portal. Microsoft está simplificando o negócio do Azure Marketplace, permitindo que mais previsível em termos de preços e coleções a partir de seus clientes em todo o mundo. Esta otimização incluirá a redução do número de moedas em que podemos criar uma fatura seus clientes.  Para obter mais informações, consulte [atualização de uma VM existente da oferta no Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Informações adicionais sobre impostos e os preços

* Microsoft classifica alguns países, como *imposto remitted países*.  Nesses países, a Microsoft cobra impostos de clientes e paga impostos de (remits) para o Governo.  Em outros países, os parceiros são, normalmente, responsáveis por coletar impostos de seus clientes e pagar impostos ao. Se optar por vender nos países esta última opção, tem de ter a capacidade de calcular e pagar impostos locais.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Os preços não são alteráveis depois de uma oferta entra no ar. No entanto, ainda poderá adicionar ou remover regiões suportadas. 
* A Microsoft cobra as cliente padrão VM do Azure as tarifas de utilização para além de suas taxas SKU agendadas.
* Os preços são definidos para todas as regiões na moeda local, as taxas de moeda disponíveis no momento da definição dos preços.  <!-- TD: Meaning? - Offer created, published, other? -->
* Para definir o preço de cada região individualmente, exportar a folha de cálculo de preços, aplicam-se de preços personalizados e importar. 

<!-- TD: The detailed information in the table and supplemental notes should be centralized in another topic, maybe "Billing Options" in AMP tree. Need to include a common section on export/import pricing-->

