---
title: Disponibilidade de região e residência dos dados
titleSuffix: Azure AD B2C
description: Disponibilidade da região, residência de dados e informação sobre inquilinos de pré-visualização Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 9cb7a97b3f57ee7ac10babc53ee2263d51838777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92309671"
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

> Estados Unidos (EUA), Canadá (CA), Costa Rica (CR), República Dominicana (DO), El Salvador (SV), Guatemala (GT), México (MX), Panamá (PA), Porto Rico (PR) e Trinidad & Tobago (TT)

Os dados residem na **Europa** para os seguintes países/regiões:

> Argélia (DZ), Áustria (AT), Azerbaijão (AZ), Bahrein (B),Bielorrússia (BY), Bélgica (BE), Bulgária (BG), Croácia (HR), Chipre (CY), República Checa (CZ), Dinamarca (DK), Egito (EG), Estónia (EE), Finlândia (FT), França (FR), Alemanha (DE), Grécia (GR), Hungria (HU), Islândia (IS), Irlanda (IE), Israel (IL), Itália (IT), Jordânia (JO), Cazaquistão (KZ), Quénia (KE), Kuwait (KW), Letónia (LV), Líbano (LB), Liechtenstein (LI), Lituânia (LT) , Luxemburgo (LU), Macedónia do Norte (ML), Malta (MT), Montenegro (ME), Marrocos (MA), Holanda (NL), Nigéria (NG), Noruega (NO), Omã (OM), Paquistão (PK), Polónia (PL), Portugal (PT), Qatar (QA), Roménia (RO), Rússia (RU), Arábia Saudita (SA), Sérvia (RS), Eslováquia (SK), Eslovénia (ST), África do Sul (ZA), Espanha (ES), Suécia (SE), Suíça (CH), Tunísia (TN), Turquia (TR), Ucrânia (UA), Emirados Árabes Unidos (AE) e Reino Unido (GB)

Os dados residem na **Ásia-Pacífico** para os seguintes países/regiões:

> Afeganistão (AF), Hong Kong SAR (HK), Índia (IN), Indonésia (ID), Japão (JP), Coreia (KR), Malásia (MY), Filipinas (PH), Singapura (SG), Sri Lanka (LK), Taiwan (TW) e Tailândia (TH).

Os seguintes países/regiões estão em vias de ser adicionados à lista. Por enquanto, você ainda pode usar Azure AD B2C escolhendo qualquer um dos países/regiões acima.

> Argentina, Austrália, Brasil, Chile, Colômbia, Equador, Iraque, Nova Zelândia, Paraguai, Peru, Uruguai e Venezuela.

## <a name="remote-profile-solution"></a>Solução de perfil remoto

Com [políticas personalizadas](custom-policy-overview.md)AZURE AD B2C, pode integrar-se com [serviços RESTful API,](custom-policy-rest-api-intro.md)que lhe permitem armazenar e ler perfis de utilizadores a partir de uma base de dados remota (como uma base de dados de marketing, sistema CRM ou qualquer aplicação de linha de negócio).  
- Durante os fluxos de inscrição e edição de perfis, a Azure AD B2C chama uma API REST personalizada para persistir o perfil do utilizador na fonte de dados remoto. As credenciais do utilizador são armazenadas no diretório Azure AD B2C. 
- Após a sposição, após validação de credenciais com uma conta local ou social, o Azure AD B2C invoca a API REST, que envia o identificador único do utilizador como chave primária do utilizador (endereço de e-mail ou objeto de utilizadorId). A API REST lê os dados a partir da base de dados remota e devolve o perfil do utilizador.  

Após a inscrição, edição de perfis ou sessão de sessão, o Azure AD B2C inclui o perfil do utilizador no token de acesso que é devolvido à aplicação. Para obter mais informações, consulte a [solução de amostra de perfil remoto Azure AD B2C](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) no GitHub.

## <a name="preview-tenant"></a>Pré-visualização inquilino

Se você tinha criado um inquilino B2C durante o período de pré-visualização do Azure AD B2C, é provável que o seu **tipo de inquilino** diga **Pré-visualização inquilino**.

Se for esse o caso, deve utilizar o seu inquilino apenas para fins de desenvolvimento e teste. NÃO utilize um inquilino de pré-visualização para aplicações de produção.

**Não há caminho** de migração de um inquilino B2C de pré-visualização para um inquilino B2C à escala de produção. Deve criar um novo inquilino B2C para as suas aplicações de produção.

Existem problemas conhecidos quando elimina um inquilino B2C de pré-visualização e cria um inquilino B2C em escala de produção com o mesmo nome de domínio. *Você deve criar um inquilino B2C em escala de produção com um nome de domínio diferente*.

![Screenshot de um tipo de inquilino, como pré-visualização inquilino.](./media/data-residency/preview-b2c-tenant.png)

## <a name="next-steps"></a>Passos seguintes

- [Criar um inquilino Azure AD B2C.](tutorial-create-tenant.md)
