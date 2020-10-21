---
title: Configurar a oferta de máquina virtual no Azure Marketplace
description: Saiba como configurar a oferta de máquinas virtuais no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 2af96c0c2e850d8e1be67c23e71aa677773c1e03
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284857"
---
# <a name="how-to-configure-virtual-machine-offer-listing-details"></a>Como configurar a oferta de máquinas virtuais

Na página de **listagem da Oferta,** define os detalhes da oferta, tais como nome de oferta, descrição, links e contactos.

> [!NOTE]
> A sua oferta de listagem de conteúdos, como a descrição, documentos, imagens e termos de uso, não tem de estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas \<non-English language> em." Também pode fornecer um URL para ligar a um site que oferece conteúdo num idioma diferente daquele que é usado no conteúdo da listagem de ofertas.

## <a name="marketplace-details"></a>Detalhes do mercado

### <a name="name"></a>Nome

O nome que aqui entra é mostrado aos clientes como o título da sua listagem de oferta. Este campo é preenchido automaticamente com o nome que inseriu na caixa **de pseudónimos Oferta** quando criou a oferta. Pode alterar este nome posteriormente. O nome:

- Pode ser marcado. Pode incluir símbolos de marca registada e direitos autorais.
- Não pode conter mais de 50 caracteres.
- Não pode incluir emojis.

### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta, a ser exibida nos resultados da pesquisa do Azure Marketplace. Pode conter até 100 caracteres.

### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta, a ser exibida nos resultados da pesquisa do Azure Marketplace. Pode conter até 256 caracteres.

### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Utilize tags HTML para formatar a sua descrição para que seja mais envolvente. Para obter uma lista de etiquetas permitidas, consulte [as etiquetas HTML suportadas](supported-html-tags.md).

### <a name="privacy-policy-link"></a>Ligação de política de privacidade

Insira o endereço web (URL) da política de privacidade da sua organização. Certifique-se de que a sua oferta está em conformidade com as leis e regulamentos de privacidade. Também deve publicar uma política de privacidade válida no seu website.

## <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares sobre a sua oferta. Para adicionar um link, **selecione Adicionar um link**e, em seguida, completar os seguintes campos:

- **Nome**: Os clientes verão o nome na página de detalhes.
- **Link (URL)**: Introduza um link que permite aos clientes visualizar o seu documento online.

## <a name="customer-support-links"></a>Links de apoio ao cliente

Forneça o site de suporte onde os clientes podem chegar à sua equipa de apoio.

- Website de suporte Azure Global
- Site de apoio do Governo de Azure

## <a name="partner-support-contact"></a>Contacto de apoio ao parceiro

Forneça informações de contacto para os parceiros da Microsoft utilizarem quando os seus clientes abrirem um bilhete de suporte. Esta informação não está listada no Azure Marketplace.

- Nome
- E-mail
- Telefone

## <a name="engineering-contact"></a>Contacto de engenharia

Forneça informações de contacto para que a Microsoft possa utilizar quando houver problemas com a sua oferta, incluindo problemas com a certificação. Esta informação não está listada no Azure Marketplace.

- Nome
- E-mail
- Telefone

## <a name="azure-marketplace-media"></a>Meios de comunicação Azure Marketplace

Forneça logotipos e imagens para utilizar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas farão com que a sua submissão seja rejeitada.

[!INCLUDE [logo tips](includes/graphics-suggestions.md)]

>[!NOTE]
>Se tiver um problema de upload de ficheiros, certifique-se de que a sua rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

### <a name="azure-marketplace-logos"></a>Logotipos do Azure Marketplace

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center utilizará isto para criar um logótipo **Pequeno** e **Médio.** Pode substituir opcionalmente estas imagens por diferentes imagens mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-azure-marketplace-only](includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Capturas de ecrã

Adicione cinco imagens que mostram como a sua oferta funciona. Cada screenshot deve ter 1280 x 720 pixels em tamanho e em formato PNG. Cada imagem deve incluir uma legenda.

### <a name="videos"></a>Vídeos

Adicione até cinco vídeos que demonstram a sua oferta. Os vídeos devem ser hospedados num serviço de vídeo externo. Insira o nome de cada vídeo, endereço web e uma imagem PNG de miniatura do vídeo a 1280 x 720 pixels.

Para obter recursos adicionais de listagem de mercado, consulte [as melhores práticas para anúncios de oferta de mercado.](gtm-offer-listing-best-practices.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="next-steps"></a>Passos seguintes

- [Criar planos](azure-vm-create-plans.md)
