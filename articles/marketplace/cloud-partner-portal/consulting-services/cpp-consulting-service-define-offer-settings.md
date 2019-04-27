---
title: O Azure e do Dynamics 365 consultoria oferta de serviço - definir definições da oferta | Documentos da Microsoft
description: Guia para a definição de definições numa Azure ou do Dynamics 365 consultoria serviço oferta no Portal de parceiros de nuvem da oferta.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 590aa440f35f97e854fa827b0d8db1c3f9211faf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749662"
---
# <a name="offer-settings-tab"></a>Separador Definições de Oferta

Sobre o **nova oferta** ecrã, a primeira etapa é criar a identidade de oferta. A identidade de oferta consiste em três partes: **ID da oferta**, **ID de publicador**, e **nome**. Cada uma dessas partes é descrita nas seções a seguir.

![Criar uma nova oferta de serviço de consultoria - separador de definições da oferta](media/consultingoffer-settings-tab.png)

### <a name="offer-id"></a>ID de Oferta

Este identificador é um nome exclusivo criado por primeiro, submeta a oferta. Tem de consistir apenas carateres alfanuméricos em minúsculas, traços ou carateres de sublinhado. O **ID da oferta** está visível no URL e afeta os resultados do motor de pesquisa. Um exemplo é *yourcompanyname_exampleservice*.

Conforme mostrado no exemplo, o **ID da oferta** é acrescentado ao seu ID de publicador para criar um identificador exclusivo. Este identificador exclusivo é exposto como uma ligação permanente que pode ser reservada e é indexada por motores de busca.

>[!Note]
>Depois de uma oferta em direto, não é possível atualizar seu identificador.

### <a name="publisher-id"></a>ID de publicador

Este identificador está relacionado com a sua conta. Depois de iniciar sessão com a sua conta organizacional, sua **ID de publicador** aparece no menu pendente.

### <a name="name"></a>Name

Esta cadeia apresenta como o nome da oferta no AppSource ou no Azure Marketplace. O **nome** caixa está limitada a 50 carateres. O revisor poderá ter de editar o título para acrescentar a duração e o tipo para o seu nome de oferta de oferta.

O exemplo seguinte mostra como o nome da oferta é reunido. 

![Criar uma nova oferta de serviço de consultoria](media/cppsampleconsultingoffer.png)

O nome da oferta é composto de quatro partes:

-   **Duração:** Definidos na **detalhes de loja** separador do editor. Duração pode ser expresso em horas, dias ou semanas.
-   **Tipo de serviço:** Definidos na **detalhes de loja** separador do editor. Tipos de serviços são `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, e `Workshop`.
-   **Preposition:** Inseridos pelo revisor.
-   **Nome:** Definidos na **oferecer definições** página.

>[!Note]
>O **nome** caixa está limitada a 50 carateres. O revisor poderá ter de editar o título para acrescentar a duração e o tipo para o seu nome de oferta de oferta.

A lista seguinte fornece vários nomes de oferta bem com nome:

-   Fundamentos de serviços profissionais: Resumo de 1 hora
-   Migração de plataforma: Resumo de 1 hora
-   PowerApps e Microsoft Flow: Workshop de 1 dia
-   Azure Machine Learning Services: Prova de conceito de 3 semanas
-   Solução de revenda de módulo e clique em: Resumo de 1 hora
-   Traga os seus dados: Workshop de semanas de 1
-   Análise da cloud: Workshop de 3 dias
-   Treinamento do Power BI: Workshop de 3 dias
-   Solução de gerenciamento de vendas: Implementação de 1 semana
-   Guia de introdução do CRM: Workshop de 1 dia
-   Dynamics 365 para vendas: Avaliação de 2 dias

Depois de preencher os **oferecer definições** separador, guarde a sua submissão. O nome da oferta é agora apresentado acima do editor, e pode encontrá-lo na **oferece todos os**.

## <a name="next-steps"></a>Passos Seguintes

Agora, pode introduzir [vitrine detalhes e determinar se pretende publicar no Azure Marketplace ou no AppSource](./cpp-consulting-service-storefront-details.md).
