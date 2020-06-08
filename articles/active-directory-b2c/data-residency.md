---
title: Disponibilidade da região e residência de dados
titleSuffix: Azure AD B2C
description: Disponibilidade da região, residência de dados e informação sobre inquilinos de pré-visualização Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: e7776317b80dbe6d927cf33792d1a180a546fa05
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484223"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Ative Directory B2C: Disponibilidade da região & residência de dados

Disponibilidade da região e residência de dados são dois conceitos muito diferentes que se aplicam de forma diferente ao Azure AD B2C do resto do Azure. Este artigo explica as diferenças entre estes dois conceitos, e compara a forma como se aplicam ao Azure versus Azure AD B2C.

O Azure AD B2C está **geralmente disponível em todo** o mundo com a opção de residência de **dados** nos **Estados Unidos, Europa ou Ásia-Pacífico.**

[Disponibilidade da região](#region-availability) refere-se ao local onde um serviço está disponível para uso.

[A residência de dados](#data-residency) refere-se ao local onde os dados dos utilizadores são armazenados.

## <a name="region-availability"></a>Disponibilidade de região

Azure AD B2C está disponível em todo o mundo através da nuvem pública Azure.

Isto difere do modelo seguido pela maioria dos outros serviços Azure, que normalmente acoplam *disponibilidade* com *residência de dados.* Pode ver exemplos disso tanto na página de [Produtos Disponíveis por Região](https://azure.microsoft.com/regions/services/) da Azure como na [calculadora de preços do Ative Directory B2C.](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="data-residency"></a>Residência dos dados

O Azure AD B2C armazena dados de utilizadores nos Estados Unidos, na Europa ou na região da Ásia-Pacífico.

A residência de dados é determinada pelo país/região que seleciona quando [cria um inquilino Azure AD B2C:](tutorial-create-tenant.md)

![Screenshot de um formulário Create Tenant, escolhendo país ou região.](./media/data-residency/data-residency-b2c-tenant.png)

Os dados residem nos **Estados Unidos** para os seguintes países/regiões:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trinidad & Tobago

Os dados residem na **Europa** para os seguintes países/regiões:

> Argélia, Áustria, Azerbaijão, Bahrein, Bielorrússia, Bélgica, Bulgária, Croácia, Chipre, República Checa, Dinamarca, Egito, Estónia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordânia, Cazaquistão, Quénia, Kuwait, Letónia, Líbano, Liechtenstein, Lituânia, Luxemburgo, Macedónia do Norte, Malta, Montenegro, Marrocos, Países Baixos, Nigéria, Noruega, Omã, Paquistão, Polónia, Portugal, Qatar, Roménia, Rússia, Arábia Saudita, Sérvia, Eslovénia, Eslovénia, África do Sul, Espanha,

Os dados residem na **Ásia-Pacífico** para os seguintes países/regiões:

> Afeganistão, Hong Kong SAR, Índia, Indonésia, Japão, Coreia, Malásia, Filipinas, Singapura, Sri Lanka, Taiwan e Tailândia.

Os seguintes países/regiões estão em vias de ser adicionados à lista. Por enquanto, você ainda pode usar Azure AD B2C escolhendo qualquer um dos países/regiões acima.

> Argentina, Austrália, Brasil, Chile, Colômbia, Equador, Iraque, Nova Zelândia, Paraguai, Peru, Uruguai e Venezuela.

## <a name="preview-tenant"></a>Pré-visualização inquilino

Se você tinha criado um inquilino B2C durante o período de pré-visualização do Azure AD B2C, é provável que o seu **tipo de inquilino** diga **Pré-visualização inquilino**.

Se for esse o caso, deve utilizar o seu inquilino apenas para fins de desenvolvimento e teste. NÃO utilize um inquilino de pré-visualização para aplicações de produção.

**Não há caminho** de migração de um inquilino B2C de pré-visualização para um inquilino B2C à escala de produção. Deve criar um novo inquilino B2C para as suas aplicações de produção.

Existem problemas conhecidos quando elimina um inquilino B2C de pré-visualização e cria um inquilino B2C em escala de produção com o mesmo nome de domínio. *Você deve criar um inquilino B2C em escala de produção com um nome de domínio diferente*.

![Screenshot de um tipo de inquilino, como pré-visualização inquilino.](./media/data-residency/preview-b2c-tenant.png)