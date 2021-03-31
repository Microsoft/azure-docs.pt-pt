---
title: Como criar planos para a sua oferta de aplicação Azure
description: Saiba como criar planos para a sua oferta de aplicação Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370267"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Como criar planos para a sua oferta de aplicação Azure

As ofertas vendidas através do mercado comercial da Microsoft devem ter pelo menos um plano para listar a sua oferta no mercado comercial. Você pode criar uma variedade de planos com diferentes opções dentro da mesma oferta. Estes planos (por vezes referidos como SKUs) podem diferir em termos de tipo de plano _(modelo_ de solução ou _aplicação gerida),_ rentabilização ou audiência. Para orientação geral sobre planos, consulte [planos e preços para ofertas de mercado comercial.](plans-pricing.md)

## <a name="create-a-plan"></a>Criar um plano

1. Perto do topo do separador **Plan,** selecione **+ Crie um novo plano.**
1. Na caixa de diálogo que aparece, na caixa **de identificação** do plano, introduza um ID de plano único. Este ID será visível para os clientes no URL do produto. Utilize até 50 caracteres alfanuméricos minúsculos, traços ou sublinhados. Não é possível modificar o ID do plano depois de selecionar **Criar**.
1. Na caixa de **nomes do Plano,** insira um nome único para este plano. Os clientes verão este nome ao decidir qual o plano a selecionar dentro da sua oferta. Use um máximo de 50 caracteres.
1. Selecione **Criar**.

## <a name="define-the-plan-setup"></a>Definir a configuração do plano

O separador **de configuração do Plano** permite-lhe definir o tipo de plano, quer reutilizar a configuração técnica de outro plano, e em que regiões do Azure o plano deve estar disponível. As suas respostas neste separador afetarão quais os campos apresentados em outros separadores para este plano.

### <a name="select-the-plan-type"></a>Selecione o tipo de plano

- A partir da lista **de planos,** selecione **o modelo de solução** ou **a aplicação gerida.**

Um plano **de modelo de solução** é gerido inteiramente pelo cliente. Um plano **de aplicação gerido** permite aos editores gerir a aplicação em nome do cliente. Para mais detalhes sobre estes dois tipos de planos, consulte [tipos de planos.](plan-azure-application-offer.md#types-of-plans)

### <a name="re-use-technical-configuration-optional"></a>Reutilização da configuração técnica (opcional)

Se criou mais de um plano do mesmo tipo dentro desta oferta e a configuração técnica é idêntica entre eles, pode reutilizar a configuração técnica a partir de outro plano. Esta definição não pode ser alterada após a publicação deste plano.

#### <a name="to-re-use-technical-configuration"></a>Para reutilizar a configuração técnica

1. Selecione o **Este plano reutiliza a configuração técnica a partir de outro plano da mesma** caixa de verificação do tipo.
1. Na lista que aparece, selecione o plano base que pretende.

> [!NOTE]
> Quando reutiliza pacotes de outro plano, todo o **separador de configuração técnica** desaparece deste plano. Os detalhes de configuração técnica do outro plano, incluindo quaisquer atualizações que faça no futuro, também são utilizados para este plano.

### <a name="select-azure-regions-clouds"></a>Selecione regiões de Azure (nuvens)

O seu plano deve ser disponibilizado em pelo menos uma região de Azure. Depois de o seu plano ser publicado e disponível numa região específica de Azure, não pode retirar essa região da sua oferta.

#### <a name="azure-global-region"></a>Região Azure Global

A caixa de verificação **Azure Global** é selecionada por padrão. Isto disponibiliza o seu plano aos clientes de todas as regiões da Azure Global que têm integração comercial no mercado. Para planos de Aplicação Gerida, pode selecionar com mercados que pretende disponibilizar o seu plano.

Para remover a sua oferta desta região, limpe a caixa de verificação **Azure Global.**

#### <a name="azure-government-region"></a>Região do Governo de Azure

Esta região fornece acesso controlado a clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como parceiros elegíveis para os servir. Você, como editora, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo Azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA).

Os serviços do Governo Azure tratam dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para consciencializar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem diretamente no programa ou links para descrições da sua conformidade com eles nos seus próprios websites. Estas ligações são visíveis apenas para clientes do Governo Azure.

##### <a name="to-select-the-azure-government-region"></a>Para selecionar a região do Governo de Azure

1. Selecione a caixa de verificação **do Governo Azure.**
1. Sob **certificações do Governo Azure,** selecione **+ Adicionar certificação (máx. 100)**.
1. Nas caixas que aparecem, forneça um nome e ligue-se a uma certificação.
1. Para adicionar outra certificação, repita os passos 2 e 3.

**Selecione Guardar o rascunho** antes de continuar para o separador seguinte: Plan listing.

## <a name="define-the-plan-listing"></a>Definir a listagem de planos

O **separador de listagem do Plano** é onde configura os detalhes do plano. Este separador apresenta informações específicas que mostram a diferença entre os planos na mesma oferta. Pode definir o nome do plano, resumo e descrição como pretende que apareçam no mercado comercial.

1. Na caixa de nomes do **Plano,** o nome que forneceu anteriormente para este plano aparece aqui. Pode mudá-lo a qualquer momento. Este nome aparecerá no mercado comercial como o título do plano de software da sua oferta e está limitado a 100 caracteres.
1. Na caixa de resumo do **Plano,** forneça um breve resumo do seu plano (não a oferta). Este resumo está limitado a 100 caracteres.
1. Na caixa **de descrição** do Plano, explique o que torna este plano de software único e quaisquer diferenças em parte de outros planos dentro da sua oferta. Não descreva a oferta, só o plano. Esta descrição pode conter até 2.000 caracteres.
1. **Selecione Guardar o projeto** antes de continuar.

## <a name="next-steps"></a>Passos seguintes

Faça um dos seguintes:

- Se estiver a configurar um plano de modelo de solução, vá para [configurar um plano de modelo de solução](create-new-azure-apps-offer-solution.md).
- Se estiver a configurar um plano de candidatura gerido, vá ao [Configure um plano de candidatura gerido](create-new-azure-apps-offer-managed.md).
