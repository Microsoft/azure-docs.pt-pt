---
title: Definir configurações de oferta para uma oferta de serviço de consultoria | Azure Marketplace
description: Defina as configurações de oferta em uma oferta de serviço de consultoria do Azure ou Dynamics 365 no Portal do Cloud Partner para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: e505f9aa1ec08708b85176d5b05fc5b40ceb295e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818133"
---
# <a name="offer-settings-tab"></a>Separador Definições de Oferta

Na nova tela de **oferta** , a primeira etapa é criar a identidade da oferta. A identidade da oferta consiste em três partes: **ID da oferta**, ID do **Editor**e **nome**. Cada uma dessas partes é abordada nas seções a seguir.

![Criar uma nova oferta de serviço de consultoria – guia Configurações de oferta](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>ID da oferta *

Esse identificador é um nome exclusivo que você cria ao enviar pela primeira vez a oferta. Ele deve consistir apenas em caracteres alfanuméricos minúsculos, traços ou sublinhados. A **ID da oferta** é visível na URL e afeta os resultados do mecanismo de pesquisa. Um exemplo é *yourcompanyname_exampleservice*.

Conforme mostrado no exemplo, a **ID da oferta** é anexada à sua ID do editor para criar um identificador exclusivo. Esse identificador exclusivo é exposto como um link permanente que pode ser reservado e indexado pelos mecanismos de pesquisa.

>[!Note]
>Depois que uma oferta estiver ativa, seu identificador não poderá ser atualizado.


### <a name="publisher-id"></a>ID do Publicador *

Esse identificador está relacionado à sua conta. Depois de entrar com sua conta institucional, a **ID do editor** aparecerá no menu suspenso.


### <a name="name"></a>Nomes

Essa cadeia de caracteres é exibida como o nome da oferta em AppSource ou no Azure Marketplace. A caixa **nome** é limitada a 50 caracteres. O revisor pode precisar editar seu título para acrescentar o tipo de duração e oferta ao seu nome de oferta.

O exemplo a seguir mostra como o nome da oferta é montado. 

![Criar uma nova oferta de serviço de consultoria](media/cppsampleconsultingoffer.png)

O nome da oferta é composto de quatro partes:

-   **Duração:** Definido na guia **detalhes da vitrine** do editor. A duração pode ser expressa em horas, dias ou semanas.
-   **Tipo de serviço:** Definido na guia **detalhes da vitrine** do editor. Os tipos de serviços são `Assessment`, `Briefing`, `Implementation`, `Proof of concept`e `Workshop`.
-   **Preposição:** Inserido pelo revisor.
-   **Nome:** Definido na página de **configurações da oferta** .

>[!Note]
>A caixa **nome** é limitada a 50 caracteres. O revisor pode precisar editar seu título para acrescentar o tipo de duração e oferta ao seu nome de oferta.

A lista a seguir fornece vários nomes de oferta bem nomeados:

-   Conceitos básicos para serviços profissionais: Resumo de 1 a h
-   Plataforma Migração na Nuvem: Resumo de 1 a HR
-   PowerApps e Microsoft Flow: Workshop de 1 dia
-   Azure Machine Learning: 3-WK de VDC
-   Brick e clique em solução de varejo: Resumo de 1 a HR
-   Traga seus próprios dados: Workshop 1-WK
-   Análise de nuvem: Workshop de três dias
-   Treinamento de Power BI: Workshop de três dias
-   Solução de gerenciamento de vendas: implementação de 1 semana
-   Início rápido do CRM: Workshop de 1 dia
-   Dynamics 365 para vendas: avaliação de 2 dias

Depois de preencher a guia **configurações da oferta** , salve o envio. O nome da oferta agora aparece acima do editor e você pode encontrá-lo em **todas as ofertas**.

## <a name="next-steps"></a>Passos seguintes

Agora você pode inserir [detalhes da vitrine e determinar se deseja publicar no Azure Marketplace ou em AppSource](./cpp-consulting-service-storefront-details.md).
