---
title: Disponibilidade de região & residência de dados em Azure Active Directory B2C
description: Um tópico sobre os tipos de locatários Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bb7a7ef2aabd7f93531d9afa662d7e582e05550
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273573"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Disponibilidade de região e residência dos dados

A disponibilidade de região e a residência de dados são dois conceitos muito diferentes que se aplicam de forma diferente a Azure AD B2C do restante do Azure. Este artigo explica as diferenças entre esses dois conceitos e compara como eles se aplicam ao Azure versus Azure AD B2C.

O Azure AD B2C **geralmente está disponível em todo o mundo** com a opção de **residência de dados** no **Estados Unidos, na Europa ou no Pacífico Asiático**.

[Disponibilidade de região](#region-availability) refere-se a onde um serviço está disponível para uso.

[Residência de dados](#data-residency) refere-se a onde os dados do usuário são armazenados.

## <a name="region-availability"></a>Disponibilidade de região

Azure AD B2C está disponível em todo o mundo por meio da nuvem pública do Azure.

Isso difere do modelo seguido pela maioria dos outros serviços do Azure, que normalmente combinam a *disponibilidade* com a *residência de dados*. Você pode ver exemplos disso na página produtos do Azure [disponíveis por região](https://azure.microsoft.com/regions/services/) e na calculadora de [preços Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residência dos dados

O Azure AD B2C armazena dados do usuário nas regiões Estados Unidos, Europa ou Pacífico Asiático.

A residência de dados é determinada pelo país/região que você selecionar ao [criar um locatário de Azure ad B2C](active-directory-b2c-get-started.md):

![Captura de tela de um locatário de visualização](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Os dados residem no **Estados Unidos** para os seguintes países/regiões:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trinidad & Tobago

Os dados residem na **Europa** para os seguintes países/regiões:

> Argélia, Áustria, Azerbaijão, Bahrein, Belarus, Bélgica, Bulgária, Croácia, Chipre, República Tcheca, Dinamarca, Egito, Estônia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordânia, Cazaquistão, Quênia, Kuwait, Letônia, Líbano, Liechtenstein, Lituânia, Luxemburgo, nordeste da Macedônia, Malta, Montenegro, Marrocos, Países Baixos, Nigéria, Noruega, Omã, Paquistão, Polônia, Portugal, Catar, Romênia, Rússia, Arábia Saudita, Sérvia, Eslováquia, Eslovênia, África do Sul, Espanha, Suécia, Suíça, Tunísia, Turquia, Ucrânia, Emirados Árabes Unidos e Reino Unido.

Os dados residem em **Pacífico Asiático** para os seguintes países/regiões:

> Afeganistão, RAE de Hong Kong, Índia, Indonésia, Japão, Coreia, Malásia, Filipinas, Cingapura, Sri Lanka, Taiwan e Tailândia.

Os seguintes países/regiões estão no processo de serem adicionados à lista. Por enquanto, você ainda pode usar Azure AD B2C selecionando qualquer um dos países/regiões acima.

> Argentina, Austrália, Brasil, Chile, Colômbia, Equador, Iraque, Nova Zelândia, Paraguai, Peru, Uruguai e Venezuela.

## <a name="preview-tenant"></a>Pré-visualizar inquilino

Se você tiver criado um locatário B2C durante o período de visualização do Azure AD B2C, é provável que o **tipo de locatário** informe o **locatário de visualização**.

Se esse for o caso, você deve usar seu locatário somente para fins de desenvolvimento e teste. Não use um locatário de visualização para aplicativos de produção.

Não **há nenhum caminho de migração** de um locatário do B2C de visualização para um locatário B2C de escala de produção. Você deve criar um novo locatário B2C para seus aplicativos de produção.

Há problemas conhecidos quando você exclui um locatário do B2C de visualização e cria um locatário B2C de escala de produção com o mesmo nome de domínio. *Você deve criar um locatário B2C de escala de produção com um nome de domínio diferente*.

![Captura de tela de um locatário de visualização](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)