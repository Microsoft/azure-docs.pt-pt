---
title: Configure máquina virtual oferecer propriedades no Azure Marketplace
description: Saiba como configurar a máquina virtual oferece propriedades no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629534"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Como configurar a máquina virtual oferece propriedades

Na página **Propriedades** (selecione a partir do menu de navegação à esquerda no Partner Center), define as categorias utilizadas para agrupar a sua oferta de máquina virtual (VM) no Azure Marketplace, a sua versão de aplicação e os contratos legais que suportam a sua oferta.

## <a name="select-a-category"></a>Selecione uma categoria

Selecione categorias e subcategorias para colocar a sua oferta nas áreas de pesquisa apropriadas do Azure Marketplace. Não se esqueça de descrever mais tarde na descrição da oferta como a sua oferta suporta estas categorias.

- Selecione uma categoria Primária.
- Para adicionar uma segunda categoria opcional (Secundária), selecione o link **+Categorias.**
- Selecione até duas subcategorias para a categoria Primária e/ou Secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável**. Utilize ctrl+clique para selecionar uma segunda subcategoria.

Consulte a lista completa de categorias e subcategorias na [Listagem de Ofertas Boas Práticas.](gtm-offer-listing-best-practices.md) As ofertas de máquinas virtuais aparecem sempre na categoria **Compute** no Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Fornecer termos e condições

Em **Legal,** forneça termos e condições para a sua oferta. Tem duas opções:

- [Utilize o contrato padrão com alterações opcionais](#use-the-standard-contract)
- [Use os seus próprios termos e condições](#use-your-own-terms-and-conditions)

Para conhecer o contrato padrão e as alterações opcionais, consulte [o Contrato Standard para o mercado comercial da Microsoft.](standard-contract.md) Pode descarregar o PDF [Standard Contract](https://go.microsoft.com/fwlink/?linkid=2041178) (certifique-se de que o seu bloqueador pop-up está desligado).

### <a name="use-the-standard-contract"></a>Use o contrato padrão

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para os fornecedores de software, a Microsoft oferece um contrato padrão que pode usar para as suas ofertas no mercado comercial. Quando oferece o seu software ao abrigo do contrato padrão, os clientes só precisam de o ler e aceitar uma vez, e não tem de criar termos e condições personalizados.

1. Selecione o Use the Standard Contract para a caixa de verificação **comercial do mercado da Microsoft.**

   ![Ilustra a utilização do Contrato Padrão para a caixa de verificação do mercado comercial da Microsoft.](partner-center-portal/media/use-standard-contract.png)

1. Na caixa de diálogo **de confirmação,** selecione **Aceitar**. Dependendo do tamanho do seu ecrã, poderá ter de deslocar-se para o ver.
1. **Selecione Guardar o projeto** antes de continuar.

   > [!NOTE]
   > Depois de publicar uma oferta usando o Contrato Padrão para o mercado comercial, não pode usar os seus próprios termos e condições personalizados. Ou oferece a sua solução ao abrigo do contrato padrão com alterações opcionais ou nos seus próprios termos e condições.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Adicione alterações ao contrato-padrão (opcional)

Existem dois tipos de alterações disponíveis: *universal* e *costume.*

##### <a name="add-universal-amendment-terms"></a>Adicionar termos de emenda universal

Nos termos da **emenda Universal ao contrato padrão para a** caixa de mercado comercial da Microsoft, insira os seus termos de emenda universal. Pode introduzir um número ilimitado de caracteres nesta caixa. Estes termos são apresentados aos clientes no AppSource, Azure Marketplace e/ou portal Azure durante o fluxo de descoberta e compra.

##### <a name="add-one-or-more-custom-amendments"></a>Adicione uma ou mais alterações personalizadas

1. De acordo com **os termos de alterações personalizadas ao Contrato Padrão para o mercado comercial da Microsoft,** selecione o **termo de emenda personalizada Add (Max 10).**
2. Insira os **termos de emenda personalizado** na caixa.
3. Insira a **identificação** do inquilino na caixa. Apenas os clientes associados aos IDs do inquilino que especifique para estes termos personalizados os verão no fluxo de compra da oferta no portal Azure.

   > [!TIP]
   > Uma identificação do inquilino identifica o seu cliente em Azure. Pode pedir ao seu cliente este ID e eles podem encontrá-lo indo para [**https://portal.azure.com**](https://portal.azure.com)  >  **a Azure Ative Directory**  >  **Properties**. O valor de ID do diretório é o ID do inquilino (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ). Você também pode procurar o iD do inquilino da organização do seu cliente usando o seu nome de domínio URL em [Qual é o meu ID de inquilino Microsoft Azure e Office 365?](https://www.whatismytenantid.com/)

4. Opcionalmente, insira uma **descrição** amigável para o ID do inquilino. Esta descrição ajuda-o a identificar o cliente que está a ser alvo com a emenda.
5. Para adicionar outro ID do inquilino, selecione o link **Add a Customer's Tenant ID (Max 10)** e repita os passos 3 e 4. Pode adicionar até 20 identificações de inquilinos.
6. Para adicionar outro termo de emenda, repita os passos 1 a 5. Pode fornecer até dez termos de emenda personalizados por oferta.
7. **Selecione Guardar o projeto** antes de continuar.

### <a name="use-your-own-terms-and-conditions"></a>Use os seus próprios termos e condições

Pode optar por fornecer os seus próprios termos e condições, em vez do contrato padrão. Os clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

1. De acordo com o **Legal,** **limpe o Use the Standard Contract para a** caixa de verificação do mercado comercial da Microsoft.
1. Na caixa **de Termos e Condições,** introduza até 10.000 caracteres de texto.

   > [!NOTE]
   > Se necessitar de uma descrição mais longa, insira um único endereço web que indicar onde os seus termos e condições podem ser encontrados. Será exibido aos clientes como um link ativo.

1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte no menu de **navegação** à esquerda, ofereça a listagem .

## <a name="next-steps"></a>Passos seguintes

- [Configurar uma lista de ofertas de VM](azure-vm-create-listing.md)
