---
title: Atualização de uma VM existente da oferta no Azure Marketplace | Documentos da Microsoft
description: Explica como atualizar uma oferta VM existente no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 08/27/2018
ms.author: Ankit.Sud
ms.openlocfilehash: ee828c908cb3b103befa3890b43bb2c1f04f7cf0
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214049"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Atualizar uma oferta VM existente no Azure Marketplace

Este artigo explica-lhe os diferentes aspectos da atualizar a sua oferta de máquina virtual (VM) no [Cloud Partner Portal](https://cloudpartner.azure.com/) e, em seguida, republicar a oferta. 

Há uma série de motivos comuns para que possa atualizar a sua oferta, incluindo:

-  Adicionar uma nova versão de imagem VM para SKUs existentes
-  Regiões de alterar um SKU está disponível
-  Adicionar novos SKUs
-  Atualizar os metadados do marketplace para a oferta ou SKUs individuais
-  Atualizar preços nas ofertas de pay as you go

Para ajudá-lo a essas modificações, o portal oferece-a **Compare** e **histórico** funcionalidades.  

>[!Note]
>Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora está disponível.  Veja [fornecedores de soluções Cloud](../../cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Alterações unpermitted a oferta de VM ou SKU

Existem alguns atributos de uma oferta de VM ou SKU que não pode ser modificado depois da oferta está em direto no Azure Marketplace, principalmente:

-  **ID da oferta** e **ID de publicador** da oferta
-  **ID do SKU** dos SKUs existentes
-  Contagem dos SKUs existentes do disco de dados
-  Modelo de faturação/licença é alterado para SKUs existentes
-  Preço aumenta para um SKU publicado


## <a name="common-update-operations"></a>Operações de atualização comuns

Embora haja uma grande variedade de características que é possível alterar uma oferta de VM, as seguintes operações são comuns.

### <a name="update-the-vm-image-version-for-a-sku"></a>Atualizar a versão de imagem VM para uma SKU

É comum para uma imagem de VM ser atualizado periodicamente com patches de segurança, funcionalidades adicionais e assim por diante.  Em tais cenários, que pretende atualizar a imagem de VM que faça referência a SKU utilizando os seguintes passos:

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas**, localize a oferta de atualização.

3.  Na **SKUs** separador, clique no SKU associado à imagem VM para atualizar.

4.  Sob **versão de disco**, clique em **+ nova versão de disco** para adicionar uma nova imagem VM.

5.  Fornecer as novas imagens de VM **versão de disco**. A versão de disco tem de cumprir os [versão semântica](http://semver.org/) formato. Versões devem ter o formato x.y. z, em que X, Y e Z são números inteiros. Certifique-se de que a nova versão que fornece é maior do que as versões de todas as anteriores; caso contrário, após republicar a nova versão não serão apresentadas no portal do ou no Azure Marketplace.

6.  Para **URL de VHD do SO**, introduza o [assinatura de acesso partilhado (SAS) URI](./cpp-get-sas-uri.md) criado para o sistema de operativo VHD. 

    > [!WARNING] 
    > O número de discos de dados não é possível alterar entre diferentes versões do SKU. Se as versões anteriores tinham discos de dados configurados, esta nova versão também tem de ter o mesmo número de discos de dados.

7.  Clique em **publicar** para iniciar o fluxo de trabalho para publicar a nova versão VM no Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Disponibilidade de região de alteração de um SKU

Ao longo do tempo, talvez queira tornar a sua oferta/SKU disponível em mais regiões.  Em alternativa, pode querer parar o/SKU de oferta de suporte numa determinada região.
Para modificar a disponibilidade, utilize os seguintes passos:

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas** encontrar a oferta que pretende atualizar.

3.  Na **SKUs** separador, clique na SKU que pretende modificar sua disponibilidade.

4.  Clique no **selecionar países** botão sob os **disponibilidade de país/região** campo.

5.  A disponibilidade de região pop-up, adicionar ou remover as regiões para este SKU.

6.  Clique em **publicar** para iniciar o fluxo de trabalho de publicar para atualizar a disponibilidade de região de SKUs.

Se um SKU está a ser disponibilizado numa nova região, terá a capacidade de especificar os preços para essa região específica através da **exportar dados de preços** funcionalidade. Se estiver a adicionar novamente uma região que era uma vez disponível antes, não será possível atualizar o seu preço porque não são permitidas alterações aos preços.


### <a name="add-a-new-sku"></a>Adicionar um novo SKU

Utilize os seguintes passos para disponibilizar um SKU de novo para a sua oferta existente: 

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas** encontrar a oferta que pretende atualizar.

3.  Sob o **SKUs** separador, clique em **adicionar novo SKU** e fornecer uma **ID de SKU** no pop-up.

4.  Voltar a publicar a VM conforme detalhado no artigo [publique uma máquina virtual no Azure Marketplace](./cpp-publish-offer.md).

5.  Clique em **publicar** para iniciar o fluxo de trabalho para publicar o seu novo SKU.


### <a name="update-offer-marketplace-metadata"></a>Atualizar os metadados do marketplace de oferta

Utilize os seguintes passos para atualizar os metadados de marketplace — da empresa nome, logótipos, etc. — associadas a sua oferta: 

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas** encontrar a oferta que pretende atualizar.

3.  Goto a **Marketplace** separador, em seguida, siga as instruções no artigo [publicar uma máquina virtual no Azure Marketplace](./cpp-publish-offer.md) para fazer alterações de metadados.

4.  Clique em **publicar** para iniciar o fluxo de trabalho para publicar as alterações.


### <a name="update-pricing-on-published-offers"></a>Atualizar dos preços das ofertas publicadas

Assim que a oferta pay as you go é publicada, não é possível diretamente aumentar os preços do SKU.  (No entanto, pode criar um novo SKU relacionados à oferta mesmo, eliminar o SKU antigo e, em seguida, voltar a publicar a sua oferta para os novos clientes.)  Por outro lado, pode diminuir o preço de uma oferta publicada através dos seguintes passos:

1.  Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

2.  Sob **todas as ofertas**, localize a oferta de atualização.

3.  Clique no SKU para o qual deseja diminuir a preços.

4.  Se tiver definido os preços a 1 x 1 GUI, pode alterar o preço diretamente na interface do Usuário. Se definir preços por meio da folha de cálculo de importação/exportação, só pode reduzir os preços por meio do recurso de importação/exportação.

3.  Clique em **Guardar**.

4.  Clique em **publicar** para iniciar o fluxo de trabalho para publicar as alterações.

Novos preços menor vão ficar visível para os novos clientes depois de ser publicada no Web site.  Este novo preço afetará os clientes das seguintes formas:

- Novos clientes são cobrados essa taxa de novo. 
- Para clientes existentes, a diminuição do preço será refletida retroativamente para o início do ciclo de faturação durante o qual a diminuição do preço entrou em vigor.
Se já ter sido faturadas pelo ciclo durante o qual uma diminuição do preço ocorreu, receberá um reembolso durante o próximo ciclo de faturação para cobrir o preço reduzido.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Preços de moeda simplificada

A partir de 1 de Setembro de 2018, chamado de uma nova seção **preços de moeda simplificado** será adicionado ao portal. Microsoft está simplificando o negócio do Azure Marketplace, permitindo que mais previsível em termos de preços e coleções a partir de seus clientes em todo o mundo. Esta otimização incluirá a redução do número de moedas em que podemos criar uma fatura seus clientes.

A nova secção, irá demorar preços nestas novas moedas. Depois de todos os clientes terem sido migrados para nestas novas moedas de liquidação, vai ser descontinuada a secção de preços original e apenas a secção simplificado moeda preços irão permanecer.

Terá até 1 de Novembro de 2018 para definir um novo preço para as regiões em que a moeda de liquidação está mudando. Não será capaz de aumentar o preço para regiões em que a moeda de liquidação não está a mudar.

> [!NOTE] 
> Se usar APIs para publicar a sua oferta, poderá ver uma nova seção no JSON oferecem. Isso poderia ser anotado como `virtualMachinePricingV2` ou `monthlyPricingV2`, dependendo do tipo de oferta. 

Se tiver dúvidas sobre esta alteração, contacte [suporte do Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Compare recursos

Quando fizer alterações numa oferta já publicada, pode aproveitar o **comparar** funcionalidade para auditar as alterações que foram feitas. Para utilizar esta funcionalidade:

1.  Em qualquer momento no processo de edição, clique nas **comparar** botão para a sua oferta.

    ![Comparar o botão de funcionalidade](./media/publishvm_037.png)


2.  Ver versões lado a lado dos ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver qualquer atividade de publicação histórica, clique nas **histórico** item na barra de menus de navegação à esquerda do Portal de parceiro de Cloud. Aqui, poderá ver timestamped ações que foram executadas durante o tempo de vida das suas ofertas do Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

