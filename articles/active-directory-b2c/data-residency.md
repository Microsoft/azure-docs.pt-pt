---
title: Disponibilidade da região e residência de dados
titleSuffix: Azure AD B2C
description: Disponibilidade da região, residência de dados e informações sobre os inquilinos de pré-visualização do Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 532136be69356b634959a762f4eeb1c1c4af5a82
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849178"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Diretório Ativo Azure B2C: Disponibilidade e residência de dados da região

A disponibilidade da região e a residência de dados são dois conceitos muito diferentes que se aplicam de forma diferente ao Azure AD B2C do resto do Azure. Este artigo explica as diferenças entre estes dois conceitos, e compara como se aplicam ao Azure versus Azure AD B2C.

O Azure AD B2C está **geralmente disponível em todo o mundo** com a opção de residência de **dados** nos **Estados Unidos, Europa ou Ásia-Pacífico.**

[A disponibilidade da região](#region-availability) refere-se ao local onde um serviço está disponível para utilização.

[A residência](#data-residency) de dados refere-se ao local onde os dados do utilizador são armazenados.

## <a name="region-availability"></a>Disponibilidade regional

O Azure AD B2C está disponível em todo o mundo através da nuvem pública de Azure.

Isto difere do modelo seguido pela maioria dos outros serviços Azure, que normalmente acopem *a disponibilidade* com a residência de *dados.* Pode ver exemplos disso tanto na página de Produtos do Azure [disponíveis por região](https://azure.microsoft.com/regions/services/) como na calculadora de [preços do Diretório Ativo B2C.](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="data-residency"></a>Residência dos dados

O Azure AD B2C armazena dados de utilizadores nos Estados Unidos, na Europa ou na região da Ásia-Pacífico.

A residência de dados é determinada pelo país/região que seleciona quando [cria um inquilino Azure AD B2C:](tutorial-create-tenant.md)

![Screenshot de um inquilino de pré-visualização](./media/data-residency/data-residency-b2c-tenant.png)

Os dados residem nos **Estados Unidos** para os seguintes países/regiões:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trinidad & Tobago

Os dados residem na **Europa** para os seguintes países/regiões:

> Argélia, Áustria, Azerbaijão, Bahrein, Bielorrússia, Bélgica, Bulgária, Croácia, Chipre, República Checa, Dinamarca, Egito, Estónia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordânia, Cazaquistão, Quénia, Kuwait, Letónia, Líbano, Liechtenstein, Lituânia, Luxemburgo, Macedónia do Norte, Malta, Montenegro, Marrocos, Países Baixos, Nigéria, Noruega, Omã, Paquistão, Polónia, Portugal, Qatar, Roménia, Rússia, Arábia Saudita, Sérvia, Eslováquia, Eslovénia, África do Sul, Espanha, Suécia, Suíça, Tunísia, Turquia, Ucrânia, Emirados Árabes Unidos e Reino Unido.

Os dados residem na **Ásia-Pacífico** para os seguintes países/regiões:

> Afeganistão, Hong Kong SAR, Índia, Indonésia, Japão, Coreia, Malásia, Filipinas, Singapura, Sri Lanka, Taiwan e Tailândia.

Os países/regiões seguintes estão em vias de ser adicionados à lista. Por enquanto, ainda pode utilizar o Azure AD B2C escolhendo qualquer um dos países/regiões acima.

> Argentina, Austrália, Brasil, Chile, Colômbia, Equador, Iraque, Nova Zelândia, Paraguai, Peru, Uruguai e Venezuela.

## <a name="preview-tenant"></a>Pré-visualização inquilino

Se criou um inquilino B2C durante o período de pré-visualização do Azure AD B2C, é provável que o seu **tipo de Inquilino** diga inquilino de **pré-visualização.**

Se for esse o caso, deve utilizar o seu inquilino apenas para fins de desenvolvimento e teste. NÃO utilize um inquilino de pré-visualização para aplicações de produção.

**Não há caminho** de migração de um inquilino B2C pré-visualizado para um inquilino B2C em escala de produção. Você deve criar um novo inquilino B2C para as suas aplicações de produção.

Existem questões conhecidas quando se elimina um inquilino B2C de pré-visualização e cria um inquilino B2C em escala de produção com o mesmo nome de domínio. *Você deve criar um inquilino B2C em escala de produção com um nome de domínio diferente*.

![Screenshot de um inquilino de pré-visualização](./media/data-residency/preview-b2c-tenant.png)