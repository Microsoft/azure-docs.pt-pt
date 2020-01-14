---
title: Atualizar uma oferta de VM existente no Azure Marketplace
description: Explica como atualizar uma oferta de VM existente no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: MaggiePucciEvans
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/27/2018
ms.author: evansma
ms.openlocfilehash: 1ba2abb3fbeb1d08ed780669fb94a2ef83cbfb1b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934249"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Atualizar uma oferta de VM existente no Azure Marketplace

Este artigo orienta você pelos diferentes aspectos da atualização da sua oferta de VM (máquina virtual) na [portal do Cloud Partner](https://cloudpartner.azure.com/) e, em seguida, a republicação da oferta. 

Há vários motivos comuns para você atualizar sua oferta, incluindo:

-  Adicionar uma nova versão de imagem de VM a SKUs existentes
-  Alterar regiões em que um SKU está disponível
-  Adicionar novas SKUs
-  Atualizar os metadados do Marketplace para a oferta ou SKUs individuais
-  Atualizar os preços das ofertas pagas conforme o uso

Para ajudá-lo nessas modificações, o portal oferece os recursos de **comparação** e **histórico** .  

>[!Note]
>A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível.  Consulte os [provedores de soluções de nuvem](../../cloud-solution-providers.md) para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Alterações não permitidas na oferta de VM ou SKU

Há alguns atributos de uma oferta de VM ou SKU que não podem ser modificados depois que a oferta está ativa no Azure Marketplace, principalmente:

-  ID da **oferta** e **ID do editor** da oferta
-  **ID de SKU** de SKUs existentes
-  Contagem de discos de dados de SKUs existentes
-  Alterações no modelo de cobrança/licença para SKUs existentes
-  Aumentos de preço para um SKU publicado


## <a name="common-update-operations"></a>Operações comuns de atualização

Embora haja uma ampla variedade de características que podem ser alteradas em uma oferta de VM, as operações a seguir são comuns.

### <a name="update-the-vm-image-version-for-a-sku"></a>Atualizar a versão da imagem da VM para uma SKU

É comum que uma imagem de VM seja atualizada periodicamente com patches de segurança, recursos adicionais e assim por diante.  Em tais cenários, você deseja atualizar a imagem de VM que seu SKU faz referência usando as seguintes etapas:

1.  Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Em **todas as ofertas**, encontre a oferta para atualizar.

3.  Na guia **SKUs** , clique no SKU associado à imagem da VM a ser atualizada.

4.  Em **versão do disco**, clique em **+ nova versão do disco** para adicionar uma nova imagem de VM.

5.  Forneça a nova versão de **disco**de imagens de VM. A versão do disco precisa seguir o formato de [versão semântica](https://semver.org/) . As versões devem estar no formato X. Y. Z, em que X, Y e Z são inteiros. Verifique se a nova versão que você fornece é maior do que todas as versões anteriores; caso contrário, depois de republicar a nova versão, o portal ou o Azure Marketplace não será exibido.

6.  Para **URL do VHD do so**, insira o [URI da assinatura de acesso compartilhado (SAS)](./cpp-get-sas-uri.md) criado para o VHD do sistema operacional. 

    > [!WARNING] 
    > A contagem de disco de dados não pode ser alterada entre versões diferentes do SKU. Se as versões anteriores tivessem discos de dados configurados, essa nova versão também deverá ter o mesmo número de discos de dados.

7.  Clique em **publicar** para iniciar o fluxo de trabalho para publicar sua nova versão de VM no Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Alterar a disponibilidade da região de uma SKU

Ao longo do tempo, talvez você queira tornar sua oferta/SKU disponível em mais regiões.  Como alternativa, talvez você queira parar de dar suporte à oferta/SKU em uma determinada região.
Para modificar a disponibilidade, use as seguintes etapas:

1.  Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Em **todas as ofertas** , encontre a oferta que você gostaria de atualizar.

3.  Na guia **SKUs** , clique na SKU para a qual você deseja modificar sua disponibilidade.

4.  Clique no botão **selecionar países** no campo **disponibilidade de país/região** .

5.  No pop-up de disponibilidade da região, adicione ou remova as regiões desta SKU.

6.  Clique em **publicar** para iniciar o fluxo de trabalho de publicação para atualizar sua disponibilidade de região de SKUs.

Se um SKU estiver sendo disponibilizado em uma nova região, você terá a capacidade de especificar os preços para essa região específica por meio da funcionalidade **exportar dados de preços** . Se você estiver adicionando uma região que já estava disponível antes, você não poderá atualizar seu preço, pois as alterações de preço não são permitidas.


### <a name="add-a-new-sku"></a>Adicionar uma nova SKU

Use as etapas a seguir para disponibilizar uma nova SKU para sua oferta existente: 

1.  Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Em **todas as ofertas** , encontre a oferta que você gostaria de atualizar.

3.  Na guia **SKUs** , clique em **Adicionar nova SKU** e forneça uma **ID de SKU** no pop-up.

4.  Republique a VM conforme detalhado no artigo [publicar uma máquina virtual no Azure Marketplace](./cpp-publish-offer.md).

5.  Clique em **publicar** para iniciar o fluxo de trabalho para publicar o novo SKU.


### <a name="update-offer-marketplace-metadata"></a>Atualizar metadados do Marketplace da oferta

Use as etapas a seguir para atualizar os metadados do Marketplace — nome da empresa, logotipos, etc. — associados à sua oferta: 

1.  Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Em **todas as ofertas** , encontre a oferta que você gostaria de atualizar.

3.  Vá para a guia **Marketplace** e siga as instruções no artigo [publicar uma máquina virtual no Azure Marketplace](./cpp-publish-offer.md) para fazer alterações nos metadados.

4.  Clique em **publicar** para iniciar o fluxo de trabalho para publicar suas alterações.


### <a name="update-pricing-on-published-offers"></a>Atualizar os preços das ofertas publicadas

Depois que sua oferta paga conforme o uso for publicada, você não poderá aumentar diretamente os preços de SKU.  (No entanto, você pode criar um novo SKU na mesma oferta, excluir o SKU antigo e, em seguida, republicar sua oferta para novos clientes.)  Por outro lado, você pode diminuir o preço de uma oferta publicada usando as seguintes etapas:

1.  Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/).

2.  Em **todas as ofertas**, encontre a oferta para atualizar.

3.  Clique na SKU para a qual você deseja diminuir os preços.

4.  Se você tiver definido o preço na GUI do 1x1, poderá alterar o preço diretamente na interface do usuário. Se você definir preços por meio da planilha de importação/exportação, só poderá diminuir os preços por meio do recurso de importação/exportação.

3.  Clique em **Guardar**.

4.  Clique em **publicar** para iniciar o fluxo de trabalho para publicar suas alterações.

O novo preço reduzido será visível para novos clientes quando ele estiver ativo no site.  Esse novo preço afetará os clientes das seguintes maneiras:

- Novos clientes serão cobrados por essa nova taxa. 
- Para os clientes existentes, a redução de preço será refletida retroativamente no início do ciclo de cobrança durante o qual o preço se torna efetivo.
Se eles já foram cobrados pelo ciclo durante o qual uma redução de preço ocorreu, eles receberão um reembolso durante o próximo ciclo de cobrança para cobrir o preço reduzido.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Preços de moedas simplificados

A partir de setembro de 1 2018, uma nova seção chamada **preço de moeda simplificado** será adicionada ao Portal. A Microsoft está simplificando o negócio do Azure Marketplace, permitindo um preço e coleções mais previsíveis de seus clientes em todo o mundo. Essa simplificação incluirá a redução do número de moedas nas quais nós faturamos seus clientes.

A nova seção entrará em preço nessas novas moedas. Depois que todos os clientes forem migrados para essas novas moedas de liquidação, a seção de preços original será desativada e somente a seção de preços de moeda simplificada permanecerá.

Você terá até 1º de novembro de 2018 para definir um novo preço para as regiões em que a moeda de liquidação está sendo alterada. Você não poderá aumentar o preço das regiões nas quais a moeda de liquidação não está mudando.

> [!NOTE] 
> Se você usar APIs para publicar sua oferta, poderá ver uma nova seção dentro da oferta JSON. Isso seria anotado como `virtualMachinePricingV2` ou `monthlyPricingV2`, dependendo do tipo de oferta. 

Se você tiver alguma dúvida sobre essa alteração, entre em contato com o [suporte do Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Comparar recurso

Ao fazer alterações em uma oferta já publicada, você pode aproveitar o recurso **comparar** para auditar as alterações feitas. Para usar esse recurso:

1.  Em qualquer ponto do processo de edição, clique no botão **comparar** para sua oferta.

    ![Botão comparar recurso](./media/publishvm_037.png)


2.  Exibir versões lado a lado de metadados e ativos de marketing.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para exibir qualquer atividade de publicação histórica, clique no item de **histórico** na barra de navegação à esquerda de portal do Cloud Partner. Aqui você poderá exibir as ações com carimbo de data/hora que foram executadas durante o tempo de vida de suas ofertas do Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

