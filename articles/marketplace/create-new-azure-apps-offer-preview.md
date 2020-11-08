---
title: Como adicionar um público de pré-visualização para a sua oferta de aplicação Azure
description: Saiba como adicionar um público de pré-visualização para a sua oferta de candidatura Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370255"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Como adicionar um público de pré-visualização para a sua oferta de aplicação Azure

Este artigo descreve como configurar um público de pré-visualização para uma oferta de aplicação Azure no mercado comercial. Você precisa definir um público de pré-visualização que pode rever a sua lista de ofertas antes de ir ao vivo.

## <a name="define-a-preview-audience"></a>Defina um público de pré-visualização

Na página de **audiências preview,** pode definir um público limitado que pode rever a sua oferta de Aplicação Azure antes de publicá-la ao vivo para o público mais amplo do mercado. Você define o público de pré-visualização usando IDs de subscrição Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes. Pode encontrar o seu ID de subscrição Azure na página **de Subscrições** no portal Azure.

Adicione um mínimo de um e até 10 IDs de subscrição Azure, individualmente (até 10) ou carregando um ficheiro CSV (até 100) para definir quem pode pré-visualizar a sua oferta antes de ser publicada ao vivo. Se a sua oferta já estiver ao vivo, poderá ainda definir um público de pré-visualização para testar alterações ou atualizações da sua oferta.

> [!NOTE]
> Um público de pré-visualização difere de uma audiência privada. Um público de pré-visualização tem acesso à sua oferta antes de ser publicado ao vivo nas lojas online. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis apenas para um público privado após a sua oferta ser totalmente publicada no mercado. Pode disponibilizar um plano apenas a um público privado. Um público privado (definido no separador **Disponibilidade** de um plano) tem acesso exclusivo a um determinado plano.

### <a name="add-email-addresses-manually"></a>Adicionar endereços de e-mail manualmente

1. Na página do **público de pré-visualização,** adicione um único ID de subscrição Azure e uma descrição opcional nas caixas fornecidas.
1. Para adicionar outro endereço de e-mail, selecione o link **Add ID (Max 10).**
1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte: Configuração técnica.
1. Ir para os [próximos passos.](#next-steps)

### <a name="add-email-addresses-using-the-csv-file"></a>Adicionar endereços de e-mail usando o ficheiro CSV

1. Na página **do público de pré-visualização,** selecione o link **Export Audience (csv).**
1. Abra o . Ficheiro CSV numa aplicação, como o Microsoft Excel.
1. No. Ficheiro CSV, na coluna **ID,** introduza os IDs de subscrição Azure que pretende adicionar ao público de pré-visualização.
1. Na coluna **Descrição,** pode opcionalmente adicionar uma descrição para cada endereço de e-mail.
1. Na coluna **Tipo,** adicione **SubscriçãoID** a cada linha que tenha um endereço de e-mail.
1. Guarde o ficheiro como um . Ficheiro CSV.
1. Na página **do público de pré-visualização,** selecione o link **Import Audience (csv).**
1. Na caixa de diálogo **Confirmar,** selecione **Sim**.
1. Selecione o . Ficheiro CSV e, em seguida, selecione **Abrir**.
1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte: Configuração técnica.

## <a name="next-steps"></a>Passos seguintes

- [Como adicionar detalhes técnicos para a sua oferta de aplicação Azure](create-new-azure-apps-offer-technical.md)
