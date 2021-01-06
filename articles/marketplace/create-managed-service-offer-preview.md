---
title: Como adicionar um público de pré-visualização para a sua oferta de Serviço Gerido
description: Saiba como adicionar um público de pré-visualização para a sua oferta de Serviço Gerido no Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918420"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Como adicionar um público de pré-visualização para a sua oferta de Serviço Gerido

Este artigo descreve como configurar um público de pré-visualização para uma oferta de Serviço Gerido no mercado comercial usando o Partner Center. O público de pré-visualização pode rever a sua oferta antes de ir ao vivo.

## <a name="define-a-preview-audience"></a>Defina um público de pré-visualização

Na página de **audiências preview,** pode definir um público limitado que pode rever a sua oferta de Serviço Gerido antes de publicá-la ao vivo para o público mais amplo do mercado. Você define o público de pré-visualização usando IDs de subscrição Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes. Pode encontrar o seu ID de subscrição Azure na página **de Subscrições** no portal Azure.

Adicione pelo menos um ID de subscrição Azure, individualmente (até 10) ou carregando um ficheiro CSV (até 100) para definir quem pode pré-visualizar a sua oferta antes de ser publicada ao vivo. Se a sua oferta já estiver ao vivo, poderá ainda definir um público de pré-visualização para testar atualizações à sua oferta.

> [!NOTE]
> Um *público de pré-visualização* difere de uma audiência *privada.* Um público de pré-visualização tem acesso à sua oferta antes de ser publicado ao vivo nas lojas online. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis apenas para um público privado após a sua oferta ser totalmente publicada no mercado. Pode disponibilizar um plano apenas a um público privado. Um público privado (definido no separador Disponibilidade de um plano) tem acesso exclusivo a um determinado plano.

## <a name="add-email-addresses-manually"></a>Adicionar endereços de e-mail manualmente

1. Na página do **público de pré-visualização,** adicione um único ID de assinatura Azure e uma descrição opcional nas caixas fornecidas.
2. Para adicionar outro endereço de e-mail, selecione o link **Add ID (Max 10).**
3. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte.

## <a name="add-email-addresses-using-a-csv-file"></a>Adicionar endereços de e-mail usando um ficheiro CSV

1. Na página do **público de pré-visualização,** selecione o link **Export Audience (csv).**
2. Abra o ficheiro CSV. Na coluna **Id,** insira os IDs de subscrição Azure que pretende adicionar ao público de pré-visualização.
3. Na coluna **Descrição,** tem a opção de adicionar uma descrição para cada entrada.
4. Na coluna **Tipo,** adicione **SubscriptionId** a cada linha que tenha um ID.
5. Guarde o ficheiro como ficheiro CSV.
6. Na página **do público de pré-visualização,** selecione o link **Import Audience (csv).**
7. Na caixa de diálogo **Confirmar,** selecione **Sim** e, em seguida, carrequiva o ficheiro CSV.
8. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte.

## <a name="next-steps"></a>Passos seguintes

* [Criar planos](create-managed-service-offer-plans.md)
