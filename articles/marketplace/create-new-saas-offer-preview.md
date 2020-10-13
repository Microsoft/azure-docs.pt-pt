---
title: Como adicionar um público de pré-visualização para a sua oferta SaaS no mercado comercial da Microsoft
description: Como adicionar um público de pré-visualização para o seu software como uma oferta de serviço (SaaS) no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: ed0c7ef98e70774350c9a3ff12b0cc3f4186e1bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89381188"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Como adicionar um público de pré-visualização para a sua oferta SaaS

Ao criar o seu software como uma oferta de serviço (SaaS) no Partner Center, precisa de definir um público de pré-visualização que possa rever a sua lista de ofertas antes de entrar em direto. Este artigo explica como configurar uma audiência de pré-visualização.

> [!NOTE]
> Se optar por processar transações de forma independente, não verá esta opção. Em vez disso, salte para [como comercializar a sua oferta SaaS.](create-new-saas-offer-marketing.md)

## <a name="define-a-preview-audience"></a>Defina um público de pré-visualização

No **separador de audiências preview,** pode definir um público limitado que pode rever a sua oferta SaaS antes de publicá-la ao vivo para o público mais amplo do mercado. Pode enviar convites para endereços de e-mail da Conta Microsoft (MSA) ou do Azure Ative Directory (Azure AD). Adicione um mínimo de um e até 10 endereços de e-mail manualmente ou importe até 20 com um ficheiro .csv. Pode atualizar a lista de audiências de pré-visualização a qualquer momento.

> [!NOTE]
> Um público de pré-visualização difere de uma audiência privada. Um público de pré-visualização tem acesso à sua oferta antes de ser publicado ao vivo nas lojas online. Você também pode optar por criar um plano e disponibilizá-lo apenas para um público privado. Os planos privados são explicados em [Como criar planos para a sua oferta SaaS.](create-new-saas-offer-plans.md)

### <a name="add-email-addresses-manually"></a>Adicionar endereços de e-mail manualmente

1. Na página **do Pré-Visualização Do Público,** adicione um único endereço de e-mail Azure AD ou MSA e uma descrição opcional nas caixas fornecidas.
1. Para adicionar outro endereço de e-mail, selecione o **Link de e-mail Adicionar.**
1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte: **Configuração técnica**.
1. Continue a [adicionar detalhes técnicos para a sua oferta SaaS.](create-new-saas-offer-technical.md)

### <a name="add-email-addresses-using-the-csv-file"></a>Adicionar endereços de e-mail usando o ficheiro CSV

1. Na página **do Pré-Visualização Do Público,** selecione o link **Export Audience (csv).**
1. Abra o . Ficheiro CSV numa aplicação, como o Microsoft Excel.
1. No. Ficheiro CSV, na coluna **ID,** insira os endereços de e-mail que pretende adicionar ao público de pré-visualização.
1. Na coluna **Descrição,** pode opcionalmente adicionar uma descrição para cada endereço de e-mail.
1. Na coluna **Tipo,** adicione **MixedAadMsaId** a cada linha que tenha um endereço de e-mail.
1. Guarde o ficheiro como um . Ficheiro CSV.
1. Na página **do Audience de Pré-visualização,** selecione o link **Import Audience (csv).**
1. Na caixa de diálogo **Confirmar,** selecione **Sim**.
1. Selecione o . Ficheiro CSV e, em seguida, selecione **Abrir**.
1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte: **Configuração técnica**.

## <a name="next-steps"></a>Passos seguintes

- [Como adicionar detalhes técnicos para a sua oferta SaaS](create-new-saas-offer-technical.md)
