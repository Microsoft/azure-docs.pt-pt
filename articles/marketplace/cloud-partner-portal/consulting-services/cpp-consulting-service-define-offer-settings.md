---
title: Defina as definições de oferta para uma oferta de serviço de consultoria [ Mercado Azure
description: Defina as definições de oferta numa oferta de serviço de consultoria Azure ou Dynamics 365 no Portal do Parceiro cloud para o Mercado Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: ac7ac2cc049c87b3f619f68a9a93a2268d961114
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278574"
---
# <a name="offer-settings-tab"></a>Separador Definições de Oferta

No ecrã **New Offer,** o primeiro passo é criar a identidade da oferta. A identidade da oferta é composta por três partes: **ID de oferta,** **ID da editora,** e **nome**. Cada uma destas peças está coberta nas seguintes secções.

![Criar uma nova oferta de serviço de consultoria - Oferta De Definições](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Oferta ID*

Este identificador é um nome único que se cria quando submete a oferta pela primeira vez. Deve consistir apenas em caracteres alfanuméricos minúsculos, traços ou sublinhados. O **ID da Oferta** é visível no URL e afeta os resultados do motor de busca. Um exemplo é *yourcompanyname_exampleservice.*

Como mostra o exemplo, o ID da **Oferta** está anexado ao SEU ID de editor para criar um identificador único. Este identificador único é exposto como um elo permanente que pode ser reservado e é indexado pelos motores de busca.

>[!Note]
>Depois de uma oferta ao vivo, o seu identificador não pode ser atualizado.


### <a name="publisher-id"></a>ID da editora*

Este identificador está relacionado com a sua conta. Depois de iniciar sessão com a sua conta organizacional, o seu **ID** editor aparece no menu suspenso.


### <a name="name"></a>Nome*

Esta cadeia exibe como nome de oferta no AppSource ou no Azure Marketplace. A caixa **de nomes** está limitada a 50 caracteres. O revisor poderá ter de editar o seu título para anexar a duração e oferecer o tipo de oferta ao seu nome de oferta.

O exemplo que se segue mostra como o nome da oferta é montado. 

![Criar uma nova oferta de serviço de consultoria](media/cppsampleconsultingoffer.png)

O nome da oferta é composto por quatro partes:

-   **Duração:** Definido no separador **Storefront Details** do editor. A duração pode ser expressa em horas, dias ou semanas.
-   **Tipo de serviço:** Definido no separador **Storefront Details** do editor. Os tipos `Assessment`de `Briefing` `Implementation`serviços são, e `Proof of concept` `Workshop`.
-   **Preposição:** Inserida pelo revisor.
-   **Nome:** Definido na página Definições de **Oferta.**

>[!Note]
>A caixa **de nomes** está limitada a 50 caracteres. O revisor poderá ter de editar o seu título para anexar a duração e oferecer o tipo de oferta ao seu nome de oferta.

A seguinte lista fornece vários nomes de ofertabem nomeados:

-   Essencial para Serviços Profissionais: Briefing 1-Hr
-   Plataforma de Migração em Nuvem: Briefing de 1-Hr
-   PowerApps e Microsoft Flow: Workshop de 1 dia
-   Aprendizagem automática azure: 3-Wk PoC
-   Brick and Click Retail Solution: 1-Hr Briefing
-   Traga os seus próprios dados: Workshop 1-Wk
-   Cloud Analytics: Workshop de 3 dias
-   Formação Power BI: Workshop de 3 dias
-   Solução de Gestão de Vendas: Implementação de 1 semana
-   CRM Quickstart: Workshop de 1 Dia
-   Dinâmica 365 para Vendas: Avaliação de 2 dias

Depois de preencher o separador Definições de **Oferta,** guarde a sua submissão. O nome da oferta aparece agora acima do editor, e você pode encontrá-lo em **Todas as Ofertas.**

## <a name="next-steps"></a>Passos seguintes

Agora pode introduzir [detalhes da montra e determinar se deve publicar no Azure Marketplace ou no AppSource](./cpp-consulting-service-storefront-details.md).
