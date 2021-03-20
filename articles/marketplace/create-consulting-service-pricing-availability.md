---
title: Como configurar os preços e disponibilidade do seu serviço de consultoria no Microsoft Partner Center
description: Saiba como configurar o seu serviço de consultoria oferece detalhes de preços e disponibilidade de mercado no mercado comercial da Microsoft usando o Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: 6b386238bd759714bc0c8ad81d67c29aa1774aba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780234"
---
# <a name="how-to-configure-your-consulting-service-pricing-and-availability"></a>Como configurar os preços e disponibilidade do seu serviço de consultoria

Este artigo explica como definir a disponibilidade de mercado e os detalhes de preços para a sua oferta de serviço de consultoria no mercado comercial da Microsoft.

> [!NOTE]
> As ofertas de serviços de consultoria são apenas de listagem. Quaisquer transações devem ser geridas entre si e os seus clientes fora do mercado comercial.

## <a name="markets"></a>Mercados

Na secção **Mercados,** selecione os países ou regiões onde o seu serviço de consultoria estará disponível.

1. Em **Mercados**, selecione a ligação **de mercados de edição.**
2. Na caixa de diálogo que aparece, selecione os locais de mercado onde pretende disponibilizar a sua oferta. Deve selecionar um mínimo de um e máximo de 141 mercados.
3. **Selecione Guardar** para fechar a caixa de diálogo.

## <a name="preview-audience"></a>Audiência de pré-visualização

Ao publicar ou atualizar a sua oferta de serviço de consultoria, o Partner Center cria uma versão de pré-visualização da listagem. Esta pré-visualização é visível apenas para utilizadores que tenham uma **chave de ocultação**.

No campo **'Ocultar',** introduza uma corda alfanumérica que utilizará para aceder à versão de pré-visualização da sua oferta.

## <a name="pricing-informational-only"></a>Preços (apenas informativos)

Na secção **de Preços,** defina se se trata de uma oferta gratuita ou paga.

Para ofertas pagas, especifique se o preço é fixo ou estimado. Se o preço for estimado, a descrição da sua oferta deve descrever quais os fatores que afetarão o preço.

Se escolher um único país ou região na secção **Mercados,** forneça o preço numa moeda suportada para esse mercado e **selecione o projeto Save**. Consulte [a disponibilidade geográfica e o suporte cambial para o mercado comercial](./marketplace-geo-availability-currencies.md) para a lista de moedas apoiadas.

Se você escolheu vários países ou regiões na secção **Mercados,** forneça o preço em dólares dos Estados Unidos (USD) e **selecione Save draft**. O Partner Center converterá esse preço na moeda local de todos os mercados selecionados utilizando as taxas de câmbio disponíveis quando guardou o projeto.

Para validar a conversão ou para definir preços personalizados num mercado individual, é necessário exportar, modificar e, em seguida, importar a folha de cálculo de preços:

1. No **preço,** selecione a **ligação de dados de preços de exportação.** Isto irá descarregar um ficheiro para o seu dispositivo.
1. Abra o ficheiro exportedPrice.xlsx no Microsoft Excel.
1. Na folha de cálculo, pode ajustar preços e moedas para cada mercado. Consulte [a disponibilidade geográfica e o suporte cambial para o mercado comercial](./marketplace-geo-availability-currencies.md) para a lista de moedas apoiadas. Quando terminar, guarde o ficheiro.
1. No Partner Center, em **Preço,** selecione a **ligação de dados de preços de importação.** Importar o ficheiro substituirá as informações de preços anteriores.

> [!IMPORTANT]
> Os preços que define no Partner Center são estáticos e não seguem variações nas taxas de câmbio. Para alterar o preço em um ou mais mercados após a publicação, atualize e reenvia a sua oferta no Partner Center.

**Selecione Guardar o projeto** antes de continuar.

## <a name="next-steps"></a>Passos seguintes

* [Rever e publicar](review-publish-offer.md)