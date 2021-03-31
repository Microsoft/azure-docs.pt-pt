---
title: Como configurar a sua aplicação Azure oferece propriedades
description: Saiba como configurar as propriedades para a sua oferta de aplicação Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 6f92c138a02c9dbdc1d22ca04c733cfbee69dcd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94488524"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>Como configurar a sua aplicação Azure oferece propriedades

Este artigo descreve como configurar os imóveis para uma oferta de aplicação Azure no mercado comercial.

Na página **Propriedades,** definirá as categorias aplicáveis à sua oferta e os contratos legais. Certifique-se de fornecer detalhes completos e precisos sobre a sua oferta nesta página, de modo que seja exibido adequadamente e oferecido ao conjunto de clientes certo.

## <a name="select-a-category-for-your-offer"></a>Selecione uma categoria para a sua oferta

Em **Categorias**, selecione o link **Categories** e, em seguida, escolha pelo menos uma e até duas categorias para agrupar a sua oferta nas áreas de pesquisa de mercado comercial apropriadas. Selecione até duas subcategorias para cada categoria primária e secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável**.

## <a name="provide-terms-and-conditions"></a>Fornecer termos e condições

Em **Legal,** forneça termos e condições para a sua oferta. Tem duas opções:

- [Utilize o contrato padrão com alterações opcionais](#use-the-standard-contract)
- [Use os seus próprios termos e condições](#use-your-own-terms-and-conditions)

Para conhecer o contrato padrão e as alterações opcionais, consulte [o Contrato Standard para o mercado comercial da Microsoft.](standard-contract.md) Pode descarregar o PDF [Standard Contract](https://go.microsoft.com/fwlink/?linkid=2041178) (certifique-se de que o seu bloqueador pop-up está desligado).

### <a name="use-the-standard-contract"></a>Use o contrato padrão

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para os fornecedores de software, a Microsoft oferece um contrato padrão que pode usar para as suas ofertas no mercado comercial. Quando oferece o seu software ao abrigo do contrato padrão, os clientes só precisam de o ler e aceitar uma vez, e não tem de criar termos e condições personalizados.

1. Selecione o Use the Standard Contract para a caixa de verificação **comercial do mercado da Microsoft.**

   ![Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.](partner-center-portal/media/use-standard-contract.png)

1. Na caixa de diálogo **de confirmação,** selecione **Aceitar**. Talvez tenha que rolar para vê-lo.
1. **Selecione Guardar o projeto** antes de continuar.

> [!NOTE]
> Depois de publicar uma oferta usando o Contrato Padrão para o mercado comercial, não pode usar os seus próprios termos e condições personalizados. Ou oferece a sua solução ao abrigo do contrato padrão com alterações opcionais ou nos seus próprios termos e condições.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Adicione alterações ao contrato-padrão (opcional)

Existem dois tipos de alterações disponíveis: _universal_ e _costume._

#### <a name="add-universal-amendment-terms"></a>Adicionar termos de emenda universal

Nos termos da **emenda Universal ao contrato padrão para a** caixa de mercado comercial da Microsoft, insira os seus termos de emenda universal. Pode introduzir um número ilimitado de caracteres nesta caixa. Estes termos são apresentados aos clientes no AppSource, Azure Marketplace e/ou portal Azure durante o fluxo de descoberta e compra.

#### <a name="add-one-or-more-custom-amendments"></a>Adicione uma ou mais alterações personalizadas

1. De acordo com **os termos de alterações personalizadas ao Contrato Padrão para o mercado comercial da Microsoft,** selecione o **termo de emenda personalizada Add (Max 10).**
1. Na caixa de **termos de emenda personalizada,** insira os termos da sua emenda.
1. Na caixa de identificação do **inquilino,** insira uma identificação do inquilino. Apenas os clientes associados aos IDs do inquilino que especifique para estes termos personalizados os verão no fluxo de compra da oferta no portal Azure.

   > [!TIP]
   > Uma identificação do inquilino identifica o seu cliente em Azure. Pode pedir ao seu cliente este ID e eles podem encontrá-lo indo para [**https://portal.azure.com**](https://portal.azure.com)  >  **a Azure Ative Directory**  >  **Properties**. O valor de ID do diretório é o ID do inquilino (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ). Você também pode procurar o iD do inquilino da organização do seu cliente usando o seu nome de domínio URL em [Qual é o meu ID de inquilino Microsoft Azure e Office 365?](https://www.whatismytenantid.com/)

1. Na caixa **Descrição,** insira opcionalmente uma descrição amigável para o ID do inquilino. Esta descrição ajuda-o a identificar o cliente que está a ser alvo com a emenda.
1. Para adicionar outro ID do inquilino, selecione o link **de identificação do inquilino do cliente** e repita os passos 3 e 4. Pode adicionar até 20 identificações de inquilinos.
1. Para adicionar outro termo de emenda, repita os passos 1 a 5. Pode fornecer até dez termos de emenda personalizados por oferta.
1. **Selecione Guardar o projeto** antes de continuar.

### <a name="use-your-own-terms-and-conditions"></a>Use os seus próprios termos e condições

Pode optar por fornecer os seus próprios termos e condições, em vez do contrato padrão. Os clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

1. De acordo com a **Legal**, certifique-se de que **o Uso do Contrato Padrão para** a caixa de verificação do mercado comercial da Microsoft está limpo.
1. Na caixa **de Termos e Condições,** introduza até 10.000 caracteres de texto.
1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte: Oferta de listagem.

## <a name="next-steps"></a>Passos seguintes

- [Como configurar a sua oferta de oferta de aplicação Azure](create-new-azure-apps-offer-listing.md)
