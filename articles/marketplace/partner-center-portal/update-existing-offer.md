---
title: Atualize uma oferta existente - Mercado comercial da Microsoft
description: Como fazer atualizações para uma oferta ou plano de mercado comercial existente, sincronizar audiências privadas e remover uma oferta.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: trkeya
ms.author: trkeya
ms.date: 10/27/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c28ab557f6aac94fa9acde78c7433b850ecba0be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097263"
---
# <a name="update-existing-offers-in-the-commercial-marketplace"></a>Atualizar as ofertas existentes no mercado comercial

Este artigo explica como fazer atualizações às ofertas e planos existentes, e também como remover uma oferta do mercado comercial. Pode ver as suas ofertas no **separador Visão Geral** do [portal Mercado Comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center.

## <a name="update-a-published-offer"></a>Atualizar uma oferta publicada

Use estes passos para atualizar uma oferta que foi publicada com sucesso para visualização ou estado ao vivo.

1. Selecione o nome da oferta que gostaria de atualizar. O estado da oferta pode ser listado como **Preview**, **Live**, **Publish em curso**, **Draft**, **Atenção necessária**, ou **Não disponível** (se já escolheu deixar de vender a oferta). Uma vez selecionado, a página geral da **Oferta** para essa oferta será aberta.
1. Selecione a página de oferta que pretende atualizar, como Propriedades , **Listagem de Ofertas** ou **Pré-visualização** (ou selecione **Update** a partir do cartão aplicável na página geral da **Oferta).** 
1. Faça as alterações e **selecione Guardar o rascunho**. Repita este processo até que todas as alterações estejam completas.
1. Reveja as suas alterações na página **[Compare.](#compare-changes-to-your-offer)**
1. Quando estiver pronto para publicar a sua oferta atualizada, selecione **'Rever' e publicar** a partir de qualquer página. A **página de Revisão e publicação** abrirá. Nesta página verá o estado de conclusão das secções da oferta que atualizou: 
    - **Alterações não publicadas**: A secção foi atualizada e está completa. Todos os dados necessários foram fornecidos e não foram introduzidos erros nas atualizações.
    - **Incompleto**: As atualizações efetuadas à secção introduziram erros que precisam de ser corrigidos ou exigem mais informações.
2. Selecione **Publicar** para submeter a oferta atualizada para publicação. A sua oferta passará então pelas etapas de [validação e publicação](../review-publish-offer.md#validation-and-publishing-steps)padrão.

## <a name="changing-offer-type"></a>Alterar tipo de oferta

[!INCLUDE [change-offer-type](../includes/change-offer-type.md)]

> [!IMPORTANT]
> Tem de rever a pré-visualização da sua oferta assim que estiver disponível e selecione **Go-live** para publicar a sua oferta atualizada para o seu público pretendido (público ou privado).

## <a name="add-a-plan-to-an-existing-offer"></a>Adicione um plano a uma oferta existente

Complete estes passos para adicionar um novo plano a uma oferta que já publicou.

1. Com a página geral da **Oferta** para a sua oferta existente aberta, vá à página **de visão geral** do Plano e, em seguida, selecione Criar novo **plano**.
1. Crie um novo plano de acordo com as [diretrizes](../plans-pricing.md) utilizando o modelo de preços dos **planos existentes.**
1. **Selecione Guardar o projeto** depois de alterar o nome do plano.
1. **Selecione Publicar** quando estiver pronto para publicar as suas atualizações. A página **[de Revisão e publicação](../review-publish-offer.md)** abre e fornece um estado de conclusão para as suas atualizações.

## <a name="update-a-plan-for-an-existing-offer"></a>Atualizar um plano para uma oferta existente

Complete estes passos para fazer alterações a um plano para uma oferta que já publicou.

1. Com a página geral da **Oferta** para a sua oferta existente aberta, escolha o plano que pretende alterar. Se o plano não estiver acessível a partir da lista **de visão geral** do Plano, selecione Ver todos os **planos**.
1. Selecione o modelo **de plano,** **preço** ou **disponibilidade**. *Atualmente, os planos estão disponíveis apenas em inglês (Estados Unidos)*.
1. **Selecione Guardar o rascunho** depois de escoar quaisquer alterações ao nome do plano, descrição ou disponibilidade do público.
1. Selecione **'Rever' e publique** quando estiver pronto para publicar as suas atualizações. A página **[de Revisão e publicação](../review-publish-offer.md)** abre e fornece um estado de conclusão para as suas atualizações.
1. Selecione **Publicar** para submeter o plano atualizado de publicação. Enviaremos um e-mail quando estiver disponível uma versão de pré-visualização da oferta atualizada para que possa rever e aprovar.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Ofereça um plano de máquina virtual a um novo preço

Depois de publicado um plano de máquina virtual, o seu preço não pode ser alterado. Para oferecer o mesmo plano a um preço diferente, você deve esconder o plano e criar um novo com o preço atualizado. Primeiro, esconda o plano com o preço que quer mudar:

1. Com a página geral da **Oferta** para a sua oferta existente aberta, escolha o plano que pretende alterar. Se o plano não estiver acessível a partir da lista **de visão geral** do Plano, selecione Ver todos os **planos**.
1. Selecione a caixa de verificação **do plano Oculta.** Guarde o rascunho antes de continuar.

Agora que escondeu o plano com o preço antigo, crie uma cópia desse plano com o preço atualizado:

1. No Partner Center, volte à **visão geral do Plano.**
2. **Selecione Criar novo plano.** Introduza um **ID do plano** e um **nome de Plano** e, em seguida, selecione **Criar**.
1. Para reutilizar a configuração técnica do plano que ocultou, selecione a caixa **de verificação de configuração técnica de Reutilização.** Leia [Criar planos para uma oferta de VM](../azure-vm-create-plans.md) para saber mais.
    > [!IMPORTANT]
    > Se selecionar **Este plano reutiliza a configuração técnica de outro plano,** não poderá deixar de vender o plano dos pais mais tarde. Não use esta opção se quiser parar de vender o plano dos pais.
3. Complete todas as secções necessárias para o novo plano, incluindo o novo preço.
1. Selecione **Guardar rascunho**.
1. Depois de ter concluído todas as secções necessárias para o novo plano, selecione **'Rever' e publicar**. Isto submeterá a sua oferta para revisão e publicação. Leia [a Revisão e publique uma oferta no mercado comercial](../review-publish-offer.md) para mais detalhes.

## <a name="sync-private-plan-audiences"></a>Sync público de plano privado

Se a sua oferta inclui um ou mais planos configurados para estarem apenas disponíveis para um público restrito privado, é possível atualizar apenas o público que pode aceder a esse plano privado sem publicar outras alterações à oferta. 

Para atualizar e sincronizar o público privado para o seu plano):

- Modifique o público em um ou mais planos privados utilizando o botão + **Add ID** ou **Import (csv)** e, em seguida, guarde as alterações.
- Selecione **Sync audiência privada** a partir da página de visão geral do **Plano.**

**O público privado sincronizado** publicará apenas as alterações ao seu público privado, sem publicar quaisquer outras atualizações que possa ter feito à oferta de projeto.

## <a name="compare-changes-to-your-offer"></a>Compare alterações à sua oferta

Antes de publicar atualizações para a sua oferta ao vivo ou [pré-visualização,](#compare-changes-to-a-preview-offer) pode auditar as alterações guardadas na página **Compare.** Pode aceder à página **Compare** no canto superior direito de qualquer página de oferta, como a página de listagem **Propriedades** ou **Oferta.** A página **Compare** mostra versões lado a lado das alterações guardadas desta oferta e da oferta de mercado publicada.

- Pode utilizar **Compare** em qualquer ponto durante o processo de edição.
- Selecione um campo na página **Compare** para navegar para o valor que pretende modificar.
- Para ver os valores de todos os campos, mesmo campos não atualizados, selecione o filtro **Todos os campos.** Pode modificar filtros dentro destes campos selecionando **campos modificados** e selecionando um dos filtros abaixo:
    - O filtro **de valores removidos** apresenta campos que publicou e está agora completamente removido.
    - O filtro de **valores adicionados** exibe campos que não publicou originalmente e que agora está a adicionar.
    - O filtro de **valores editados** exibe campos que tinham sido publicados mas agora atualizou os conteúdos.

      >[!NOTE]
      > Se um destes filtros não estiver disponível, indica que não fez uma atualização desse tipo.

- Para ver apenas valores que não tenham sido atualizados, selecione o filtro **de campos inalterados.** Os valores de campo apresentados para a versão publicada e projeto serão os mesmos.

  ![Filtros para comparar atualizações com a sua oferta publicada ou de pré-visualização](./media/compare-changes-marketplace.png)

>[!NOTE]
> As páginas seguintes não **suportam** atualmente Compare:
>- Público revendedor de CSP
>- Configuração técnica da unidade de teste
>- Lista de mercado de unidade de teste
>- Co-vender
>- Ficheiros suplementares

Lembre-se de republicar a sua oferta depois de fazer atualizações para que as alterações produzam efeitos.

### <a name="compare-changes-to-a-preview-offer"></a>Compare alterações a uma oferta de pré-visualização

Se tiver alterações na pré-visualização que não estão ao vivo, pode comparar novas alterações com a oferta de pré-visualização do mercado.

1. **Selecione Compare** na barra de comando da página.
2. Selecione o **Com** dropdown e altere-o da **oferta ao vivo** para a oferta de **pré-visualização**. Se a sua oferta ainda não foi ao vivo, não verá a opção **de oferta ao vivo.**
3. A página **Compare** fornece versões lado a lado que mostram as alterações.

Lembre-se de republicar a sua oferta depois de fazer atualizações para que as alterações produzam efeitos.

## <a name="stop-selling-an-offer-or-plan"></a>Pare de vender uma oferta ou plano

Pode remover as listas de ofertas e planos do mercado comercial da Microsoft, o que impedirá que novos clientes os encontrem e os comprem. Qualquer cliente que tenha adquirido previamente a oferta ou plano ainda pode usá-la, e podem descarregá-la novamente se necessário. No entanto, não receberão atualizações se decidir reeditar a oferta ou planear mais tarde.

- Para parar de vender uma oferta depois de a publicar, selecione Pare de **vender** na página geral da **Oferta.** Poucas horas após a sua confirmação, a oferta deixará de ser visível no mercado comercial.

- Para parar de vender um plano, selecione Pare de **vender** na página **de visão geral** do Plano. A opção de parar de vender um plano só está disponível se tiver mais do que um plano na oferta. Pode optar por parar de vender um plano sem afetar outros planos dentro da sua oferta.
     >[!NOTE]
     > Uma vez confirmado que quer parar de vender o plano, deve republicar a oferta para que a mudança entre em vigor.

Depois de deixar de vender uma oferta ou plano, ainda a verá no Partner Center com um estatuto **de Não disponível.** Se decidir listar ou vender esta oferta ou planear novamente, siga as instruções para [atualizar uma oferta publicada](#update-a-published-offer). Não se esqueça que terá de **publicar** novamente a oferta ou planear depois de escamar quaisquer alterações.

## <a name="remove-offers-from-existing-customers"></a>Remova as ofertas dos clientes existentes

Para remover as ofertas dos clientes existentes, [faça login um pedido de suporte](https://aka.ms/marketplacepublishersupport). Na lista de tópicos de suporte, selecione **Oferta de Mercado Comercial** ou  >  **Deslistação de Aplicações, Remoção ou Rescisão** e envie o pedido. A equipa de apoio irá guiá-lo através do processo de remoção da oferta.

## <a name="next-steps"></a>Passos seguintes

- [Verifique o estado editorial da sua oferta de mercado comercial](../review-publish-offer.md)
