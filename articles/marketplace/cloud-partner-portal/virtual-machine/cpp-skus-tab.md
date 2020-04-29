---
title: Tab SKUs de máquina virtual no Portal do Parceiro cloud para o Mercado Azure
description: Descreve o separador SKUs usado na criação de uma oferta de máquina virtual no Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 6ca2ebf9d02c43c3fe6bb3abec6bc0d5815fac74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143021"
---
# <a name="virtual-machine-skus-tab"></a>Tab SKUs máquina virtual

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create a Azure Virtual Machine para](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) gerir as suas ofertas migratórias.

O separador **SKUs** da página **New Offer** permite-lhe criar uma ou mais SKUs e associá-las à sua nova oferta.  Diferentes SKUs podem diferenciar uma solução por conjuntos de funcionalidades, tipos de imagem VM, entrada ou escalabilidade, modelos de faturação ou alguma outra característica.


## <a name="create-a-sku"></a>Criar um SKU

Inicialmente, uma nova oferta não terá nenhuma SKUs associada, por isso irá criar uma clicando em **New SKU**.

![Novo botão SKU no separador New Offer para máquinas virtuais](./media/publishvm_005.png)

<br/>

A nova caixa de diálogo **SKU** é exibida.  Introduza o identificador para o novo SKU **e,** em seguida, clique OK . (Ver abaixo as convenções de nomeação de identificadores.)  O separador **SKUs** irá agora exibir os campos disponíveis para edição.    Um asterisco anexado (*) no nome do campo indica que é necessário.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Separador SKU no formulário New Offer para máquinas virtuais](./media/publishvm_006.png)

A tabela que se segue descreve o propósito, o conteúdo e a formatação destes campos.  Os campos necessários são indiciados por um asterisco (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
|  *Definições SKU*   |    |
| **SKU ID\***       | Identificador para este SKU.  Este nome tem um máximo de 50 caracteres, compostopor caracteres alfanuméricos inferiores ou traços (-), mas não pode terminar com um traço.  Não pode ser modificado após a publicação da oferta.  |
|  *Detalhes da SKU*   |  |
| **Título\***        | Nome amigável para a oferta para exibição no mercado. Comprimento máximo de 50 caracteres. |
| **Resumo\***      | Descrição sucinta da oferta para exibição no mercado. Comprimento máximo de 100 caracteres. |
| **Descrição\***  | Texto de descrição que fornece uma explicação mais detalhada da oferta.  <!-- TD: max len/guidance? 3k characters -->  |
| **Esconda este SKU\*** | Indica se o SKU deve ser visível no mercado para os clientes.  Você pode querer esconder o SKU se você só o deseja disponível apenas através de modelos de solução e não para compra individual.  Também pode ser útil para testes iniciais ou para ofertas temporárias ou sazonais. |
| **Disponibilidade de Nuvem\*** | Determina quais nuvens o SKU deve estar disponível.  O padrão é a versão pública do Azure.  O Governo microsoft Azure é uma nuvem governamental com acesso controlado aos governos federal, estadual, local ou tribal dos EUA e seus parceiros certificados.  Para mais informações sobre a nuvem governamental, consulte [Bem-vindo ao Governo de Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) |
| **Isto é um SKU privado?\*** | Indica se o SKU é privado ou público. O padrão é **Não** (público).  Para mais informações, consulte [SKUs público e privado.](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) |
| **Disponibilidade país/região\*** | Determina quais países ou regiões mundiais o seu SKU estará disponível para compra. Selecione pelo menos uma região/país. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Preços*   |  |
| **Modelo de Licença\***| Modelo de faturação padronizado para usar.  Se selecionar **SKU mensalmente faturado com base em Utilização,** uma secção de acordeão estará aberta para permitir especificar detalhes de preços por núcleo e se pretende oferecer um período experimental gratuito.  Esta secção também permite exportar e importar este calendário de preços para o Excel. Para mais informações, consulte [as opções de Faturação no Mercado Azure.](../../billing-options-azure-marketplace.md) | 
|  *Imagens VM*   |  |
| **Família do sistema operativo\*** | Indica se a solução VM é baseada no Windows ou no Linux. |
| **Selecione Tipo de Sistema Operativo** | Fornecedor específico ou libertação do Sistema Operativo especificado. |
| **Nome amigável do OS\*** | Nome do sistema operativo a ser apresentado aos clientes.  |
| **Tamanhos VM recomendados\*** | Permite a seleção de até seis tamanhos VM recomendados a partir de uma lista padronizada.  Esta lista é transmitida para os mercados do portal Azure e microsoft.  O primeiro tamanho vm nesta lista que é válido (para essa subscrição de cliente, região, zona, etc.) é definido como o padrão para esse potencial cliente.  O utilizador pode alterar este tamanho para qualquer compatível com a imagem da solução. | 
| **Portas Abertas**| Portas para abrir e protocolo para apoiar o SKU.  Estas configurações devem corresponder à rede virtual que configuraste para a rede da solução VM. Estas definições entram em vigor durante a implantação da VM. No entanto, as definições do Porto podem ser modificadas depois de publicar um SKU. Para obter mais informações, veja [How to open ports to a virtual machine with the Azure portal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal) (Como abrir portas para uma máquina virtual com o portal do Azure). <br/>Os seguintes mapeamentos de rede padrão são adicionados a todos os VMs. &emsp;Janelas: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Versão do disco**  | Solução associada VM, especificada pelo número da versão do disco e URL do disco. A versão do disco deve estar `<major>.<minor>.<patch>`em formato de [versão semântica:](https://semver.org/) .  O URL é a assinatura de acesso partilhado URI criada para o sistema operativo VHD.  Embora possa somar oito versões de disco por SKU, apenas o número de versão de disco mais alto para um SKU aparecerá no Azure Marketplace. As outras versões só serão visíveis através de APIs.  <!--TD: Add more specific link to API --> <br/> A nova secção de acordeão do disco de **dados** permite-lhe anexar até 15 discos de dados ao seu VM.  Uma vez publicado um SKU com uma versão VM e discos de dados associados, esta configuração não pode ser modificada.  Se as versões VM adicionais forem adicionadas ao SKU, também devem suportar o mesmo número de discos de dados. <br/> Se não criou as suas imagens VM baseadas no Azure, pode adicionar atualização deste campo mais tarde.  Para obter informações sobre a criação do recurso VM associado, consulte a secção [Criar ativos técnicos VM](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Clique em **Guardar** para salvar o seu progresso. No separador seguinte, irá especificar se a sua oferta suporta [test drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Considerações adicionais de preços

O modelo de preços acima descrito é uma descrição básica.  Está a sofrer alterações e pode ser afetado por regulamentos fiscais locais ou regionais e pelas políticas de preços da Microsoft. 

### <a name="new-core-sizes-added-on-722019"></a>Novos tamanhos de núcleo adicionados em 7/2/2019

As editoras vM foram notificadas em 2 de julho de 2019 da adição de novos preços para novos tamanhos de máquinas virtuais Azure (com base no número de núcleos).  Os novos preços são para os tamanhos 10, 44, 48, 60, 120, 208 e 416.  Para as ofertas vm existentes, os novos preços para estes tamanhos de núcleoforam automaticamente calculados com base nos preços correntes.  As editoras têm até 1 de agosto de 2019 para rever os preços adicionais e fazer quaisquer alterações desejadas.  Após esta data, se ainda não for republicado pela editora, os preços calculados automaticamente para estes novos tamanhos de núcleo entrarão em vigor.


### <a name="simplified-currency-pricing"></a>Preços simplificados da moeda

A partir de 1 de setembro de 2018, será adicionada ao portal uma nova secção chamada **Preços Simplificados** da Moeda. A Microsoft está a dinamizar o negócio do Azure Marketplace, permitindo preços e coleções mais previsíveis dos seus clientes em todo o mundo. Esta racionalização incluirá a redução do número de moedas em que faturamos os seus clientes.  Para mais informações, consulte [Atualizar uma oferta vm existente no Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Informações adicionais sobre impostos e preços

* A Microsoft classifica alguns países/regiões como *países remetidos*em impostos.  Nesses países/regiões, a Microsoft cobra impostos aos clientes e depois paga impostos (remitas) ao governo.  Noutros países/regiões, os parceiros são normalmente responsáveis pela cobrança de impostos dos seus clientes e pelo pagamento de impostos ao governo. Se optar por vender nos últimos países/regiões, tem de ter a capacidade de calcular e pagar impostos locais.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Os preços não são mutáveis uma vez que uma oferta vai ao vivo. No entanto, ainda pode adicionar ou remover regiões apoiadas. 
* A Microsoft cobra as taxas de utilização padrão do cliente Azure VM para além das taxas sku programadas.
* Os preços são fixados para todas as regiões da moeda local sobre as taxas de câmbio disponíveis no momento da fixação dos preços.  <!-- TD: Meaning? - Offer created, published, other? -->
* Para definir o preço de cada região individualmente, por favor exporte a folha de cálculo de preços, aplique preços personalizados e, em seguida, importe. 


## <a name="next-steps"></a>Passos seguintes

Opcionalmente, irá especificar as informações do [Test Drive](./cpp-test-drive-tab.md) se estiver a apoiar esta funcionalidade; caso contrário, fornece dados de [marketplace](./cpp-marketplace-tab.md) para a sua oferta.
