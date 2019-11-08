---
title: Guia SKUs da máquina virtual no Portal do Cloud Partner para o Azure Marketplace
description: Descreve a guia SKUs usada na criação de uma oferta de máquina virtual no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 101f928e296d0b48b4e077676393772829aa76ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808973"
---
# <a name="virtual-machine-skus-tab"></a>Guia SKUs da máquina virtual

A guia **SKUs** da página **nova oferta** permite que você crie uma ou mais SKUs e associe-as à sua nova oferta.  SKUs diferentes podem diferenciar uma solução por conjuntos de recursos, tipos de imagem de VM, taxa de transferência ou escalabilidade, modelos de cobrança ou alguma outra característica.


## <a name="create-a-sku"></a>Criar uma SKU

Inicialmente, uma nova oferta não terá quaisquer SKUs associados, portanto, você criará uma clicando em **nova SKU**.

![Novo botão SKU na guia nova oferta para máquinas virtuais](./media/publishvm_005.png)

<br/>

A caixa de diálogo **nova SKU** é exibida.  Insira o identificador para o novo SKU e clique em **OK**. (Veja abaixo as convenções de nomenclatura de identificadores.)  Agora, a guia **SKUs** exibirá os campos disponíveis para edição.    Um asterisco anexado (*) no nome do campo indica que ele é necessário.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![Guia SKU no formulário nova oferta para máquinas virtuais](./media/publishvm_006.png)

A tabela a seguir descreve a finalidade, o conteúdo e a formatação desses campos.  Os campos obrigatórios são indicted por um asterisco (*).

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
|  *Configurações de SKU*   |    |
| **ID do SKU\***       | Identificador para este SKU.  Esse nome tem um máximo de 50 caracteres, que consistem em caracteres alfanuméricos minúsculos ou traços (-), mas não podem terminar com um traço.  Ele não pode ser modificado depois que a oferta é publicada.  |
|  *Detalhes do SKU*   |  |
| **Título\***        | Nome amigável da oferta para exibição no Marketplace. Comprimento máximo de 50 caracteres. |
| **\* de resumo**      | Descrição sucinta da oferta para exibição no Marketplace. Comprimento máximo de 100 caracteres. |
| **Descrição\***  | Texto de descrição que fornece uma explicação mais detalhada da oferta.  <!-- TD: max len/guidance? 3k characters -->  |
| **Ocultar este\* de SKU** | Indica se o SKU deve estar visível no Marketplace para os clientes.  Talvez você queira ocultar a SKU se quiser apenas ela disponível por meio de modelos de solução e não para compra individualmente.  Ele também pode ser útil para testes iniciais ou para ofertas temporárias ou sazonais. |
| **\* de disponibilidade na nuvem** | Determina em quais nuvens o SKU deve estar disponível.  O padrão é a versão pública do Azure.  Microsoft Azure Governamental é uma nuvem de comunidade governamental com acesso controlado para os governos federal, estadual, local ou tribal dos EUA e seus parceiros certificados.  Para obter mais informações sobre a nuvem governamental, consulte [Bem-vindo ao Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Este é um SKU privado?\*** | Indica se a SKU é privada ou pública. O padrão é **não** (público).  Para obter mais informações, consulte [SKUs públicas e privadas](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Disponibilidade de país/região\*** | Determina quais países ou regiões do mundo seu SKU estará disponível para compra. Selecione pelo menos uma região/país. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Preços*   |  |
| **\* do modelo de licença**| Modelo de cobrança padronizado a ser usado.  Se você selecionar **SKU faturado mensal com base no uso**, uma seção de acordeão será aberta para permitir que você especifique detalhes de preços por núcleo e se deseja oferecer um período de avaliação gratuita.  Esta seção também permite que você exporte e importe essa agenda de preços para o Excel. Para obter mais informações, consulte [Opções de cobrança no Azure Marketplace](../../billing-options-azure-marketplace.md). | 
|  *Imagens de VM*   |  |
| **\* da família do sistema operacional** | Indica se a VM da solução é baseada no Windows ou no Linux. |
| **Selecionar tipo de sistema operacional** | Fornecedor específico ou versão do sistema operacional especificado. |
| **Nome amigável do sistema operacional\*** | Nome do sistema operacional a ser exibido aos clientes.  |
| **Tamanhos de VM recomendados\*** | Permite a seleção de até seis tamanhos de VM recomendados de uma lista padronizada.  Essa lista é passada para o portal do Azure e o Microsoft Marketplaces.  O primeiro tamanho de VM nessa lista que é válido (para essa assinatura de cliente, região, zona etc.) é definido como o padrão para esse cliente potencial.  O usuário pode alterar esse tamanho para qualquer compatível com a imagem da solução. | 
| **Abrir portas**| Portas a abrir e protocolo para dar suporte à SKU.  Essas configurações devem corresponder à rede virtual que você configurou para a rede da VM da solução. Essas configurações entram em vigor durante a implantação da VM. No entanto, as configurações de porta podem ser modificadas depois que você publicar uma SKU. Para obter mais informações, consulte [como abrir portas em uma máquina virtual com o portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>Os mapeamentos de rede padrão a seguir são adicionados a todas as VMs. &emsp; Windows: 3389-> 3389 TCP, 5986-> 5986 TCP; &emsp; Linux: 22-> 22, TCP (SSH). |
| **Versão do disco**  | VM de solução associada, especificada pelo número de versão do disco e URL do disco. A versão do disco deve estar no formato de [versão semântica](https://semver.org/) : `<major>.<minor>.<patch>`.  A URL é o URI de assinatura de acesso compartilhado criado para o VHD do sistema operacional.  Embora você possa adicionar até oito versões de disco por SKU, somente o número de versão de disco mais alto para um SKU será exibido no Azure Marketplace. As outras versões só serão visíveis por meio de APIs.  <!--TD: Add more specific link to API --> <br/> A seção novo acordede de **disco de dados** permite que você anexe até 15 discos de dados à sua VM.  Depois de publicar uma SKU com uma determinada versão de VM e discos de dados associados, essa configuração não pode ser modificada.  Se versões adicionais de VM forem adicionadas à SKU, elas também deverão oferecer suporte ao mesmo número de discos de dados. <br/> Se você não tiver criado suas imagens de VM baseadas no Azure, poderá adicionar atualizar esse campo posteriormente.  Para obter informações sobre como criar o recurso de VM associado, consulte a seção [criar ativos técnicos de VM](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Clique em **salvar** para salvar seu progresso. Na próxima guia, você especificará se sua oferta dá suporte ao [Test Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>Considerações adicionais sobre preços

O modelo de preços descrito acima é uma descrição básica.  Ele está passando por alterações e pode ser afetado por regulamentos de impostos locais ou regionais e por políticas de preços da Microsoft. 

### <a name="new-core-sizes-added-on-722019"></a>Novos tamanhos de núcleo adicionados em 7/2/2019

Os editores de VM foram notificados em 2 de julho de 2019 da adição de novos preços para novos tamanhos de máquina virtual do Azure (com base no número de núcleos).  Os novos preços são para os tamanhos de núcleos 10, 44, 48, 60, 120, 208 e 416.  Para a VM existente oferece novos preços para esses tamanhos de núcleos calculados automaticamente com base nos preços atuais.  Os editores têm até 1º de agosto de 2019 para examinar os preços adicionais e fazer as alterações desejadas.  Após essa data, se ainda não tiver sido publicado novamente pelo Publicador, os preços calculados automaticamente para esses novos tamanhos de núcleo entrarão em vigor.


### <a name="simplified-currency-pricing"></a>Preços de moedas simplificados

A partir de setembro de 1 2018, uma nova seção chamada **preço de moeda simplificado** será adicionada ao Portal. A Microsoft está simplificando o negócio do Azure Marketplace, permitindo um preço e coleções mais previsíveis de seus clientes em todo o mundo. Essa simplificação incluirá a redução do número de moedas nas quais nós faturamos seus clientes.  Para obter mais informações, consulte [atualizar uma oferta de VM existente no Azure Marketplace](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>Informações adicionais sobre impostos e preços

* A Microsoft classifica alguns países/regiões como *países remetidos pelo imposto*.  Em tais países/regiões, a Microsoft coleta impostos de clientes, em seguida, paga (remete) impostos para o governo.  Em outros países/regiões, os parceiros são normalmente responsáveis por coletar impostos de seus clientes e pagar impostos para o governo. Se você optar por vender nos últimos países/regiões, deverá ter a capacidade de calcular e pagar impostos locais.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Os preços não são alteráveis depois que uma oferta é ativada. No entanto, você ainda pode adicionar ou remover regiões com suporte. 
* A Microsoft cobra as tarifas de uso de VM do Azure padrão do cliente, além de suas taxas de SKU agendadas.
* Os preços são definidos para todas as regiões na moeda local em taxas de moeda disponíveis no momento da definição de preços.  <!-- TD: Meaning? - Offer created, published, other? -->
* Para definir o preço de cada região individualmente, exporte a planilha de preços, aplique preços personalizados e importe. 


## <a name="next-steps"></a>Passos seguintes

Opcionalmente, você especificará informações de [unidade de teste](./cpp-test-drive-tab.md) se estiver dando suporte a esse recurso; caso contrário, você fornecerá dados do [Marketplace](./cpp-marketplace-tab.md) para sua oferta.
