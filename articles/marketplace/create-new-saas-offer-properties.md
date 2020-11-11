---
title: Como configurar as suas propriedades de oferta SaaS no Microsoft Partner Center
description: Saiba como configurar as propriedades do seu software como um serviço (SaaS) oferta de mercado comercial da Microsoft no Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 7d90c9b9b2c9aa97083e17d5ab7d20fc6b471658
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94492128"
---
# <a name="how-to-configure-your-saas-offer-properties"></a>Como configurar as suas propriedades de oferta SaaS

Este artigo descreve como configurar as propriedades de um software como uma oferta de serviço (SaaS) no mercado comercial da Microsoft.

No separador **Propriedades,** definirá as categorias e indústrias aplicáveis à sua oferta, versão da sua aplicação e contratos legais. Certifique-se de fornecer detalhes completos e precisos sobre a sua oferta nesta página, de modo que seja exibido adequadamente e oferecido ao conjunto de clientes certo.

## <a name="select-a-category-for-your-offer"></a>Selecione uma categoria para a sua oferta

Na **categoria** , selecione pelo menos uma e até duas categorias para agrupar a sua oferta nas áreas de pesquisa de mercado apropriadas. Com base nas categorias que escolhe, determinamos quais as lojas online que listar a sua oferta: ou Azure Marketplace, Microsoft AppSource ou ambas.

## <a name="select-industries-optional"></a>Selecione Indústrias (opcional)

No âmbito **das Indústrias,** pode selecionar até duas indústrias e até duas sub-indústrias (também chamadas verticais) para cada indústria. Estas indústrias são usadas para mostrar a sua oferta quando os clientes filtram a sua pesquisa em indústrias e sub-indústrias na loja online.

> [!NOTE]
> Se a sua oferta não for específica da indústria, deixe esta secção em branco.

1. Under **Industries** , selecione a ligação **+ Indústrias.**
1. Selecione uma indústria da lista **da Indústria.**
1. Selecione pelo menos um e um máximo de dois verticais da lista **de sub-indústrias.** Utilize a tecla Ctrl para selecionar várias sub-indústrias.
1. Para adicionar outra indústria e vertical, selecione **+ Indústrias** e, em seguida, repita os passos 1 a 3.

## <a name="specify-an-app-version-optional"></a>Especifique uma versão de aplicação (opcional)

 Na caixa de **versão App,** introduza um número de versão. A versão da aplicação é utilizada no mercado appSource para identificar o número de versão da sua oferta.

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

Existem dois tipos de alterações disponíveis: *universal* e *costume.*

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
2. **Selecione Guardar o projeto** antes de continuar.

### <a name="use-your-own-terms-and-conditions"></a>Use os seus próprios termos e condições

Pode optar por fornecer os seus próprios termos e condições, em vez do contrato padrão. Os clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

1. De acordo com a **Legal** , certifique-se de que **o Uso do Contrato Padrão para** a caixa de verificação do mercado comercial da Microsoft está limpo.
1. Na caixa **de Termos e Condições,** introduza até 10.000 caracteres de texto.
1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte, oferecer a **listagem**.

## <a name="next-steps"></a>Passos seguintes

- [Como configurar os seus dados de listagem de oferta saaS](create-new-saas-offer-listing.md)
