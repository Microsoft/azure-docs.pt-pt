---
title: Região disponibilidade e residência dos dados no Azure Active Directory B2C | Documentos da Microsoft
description: Um tópico sobre os tipos de inquilinos do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 67f87729dbfb792b88f28a772f15e233f0fa60dc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452118"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Disponibilidade de região e residência dos dados
Disponibilidade regional e residência dos dados são dois conceitos muito diferentes que aplicam de forma diferente para o Azure AD B2C do restante do Azure. Este artigo explique as diferenças entre estes dois conceitos e comparar como se aplicam ao Azure em comparação com o Azure AD B2C.

## <a name="summary"></a>Resumo
É o Azure AD B2C **em disponibilidade geral em todo o mundo** com a opção para **residência dos dados nos Estados Unidos ou em Europa**.

## <a name="concepts"></a>Conceitos
* **Disponibilidade de região** refere-se ao qual um serviço está disponível para utilização.
* **Residência dos dados** refere-se para onde são armazenados os dados de utilizador.

## <a name="region-availability"></a>Disponibilidade de região
O Azure AD B2C está disponível em todo o mundo por meio da nuvem pública do Azure. 

Isto é diferente do modelo siga maioria dos outros serviços do Azure que acoplar disponibilidade com residência de dados. Pode ver exemplos disso em ambas do Azure [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) página e o [Calculadora de preços do Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residência dos dados
O Azure AD B2C armazena os dados de utilizador nos Estados Unidos ou na Europa.

Residência dos dados é determinada com base em que país/região está selecionada quando [criar um inquilino do Azure AD B2C](active-directory-b2c-get-started.md).

![Captura de ecrã de um inquilino de pré-visualização](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Os dados residem nos Estados Unidos para os países/regiões seguintes:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trindade e Tobago

Os dados residem na Europa para os países/regiões seguintes:

> Argélia, Áustria, Azerbaijão, Barém, Bielorrússia, Bélgica, Bulgária, Croácia, Chipre, República Checa, Dinamarca, Egipto, Estónia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordan, Cazaquistão, Quénia, Kuwait, Lativa, Líbano, Listenstaine, Lituania, Luxemburgo, Macedónia (ARJM), Malta, Montenegro, Marrocos, Países Baixos, Nigéria, Noruega, Omã, Paquistão, Polónia, Portugal, Catar, Roménia, Rússia, Arábia Saudita, Sérvia, Eslováquia, Eslovénia, África do Sul, Espanha, Suécia, Suíça, Tunísia, Turquia, Ucrânia, Reino Árabes Unidos e o Reino Unido.

Os restantes países/regiões estão a ser adicionado à lista.  Por agora, pode continuar a utilizar do Azure AD B2C ao selecionar qualquer um dos países/regiões acima.

> Afeganistão, Argentina, Austrália, Brasil, Chile, Colômbia, Equador, RAE de Hong Kong, Índia, Indonésia, Iraque, Japão, Coreia, Malásia, Nova Zelândia, Paraguai, Peru, Filipinas, Singapura, Sri Lanka, Taiwan, Tailândia, Uruguai e Venezuela.

## <a name="preview-tenant"></a>Pré-visualizar inquilino
Se tivesse criado um inquilino de B2C durante o período de pré-visualização do Azure AD B2C, é provável que sua **tipo de inquilino** diz **pré-visualizar inquilino**. Se for este o caso, tem de utilizar o inquilino apenas para fins de testes e de desenvolvimento e não para aplicações de produção.

> [!IMPORTANT]
> Não existe nenhum caminho de migração de um inquilino de B2C de pré-visualização para um inquilino de B2C de escala de produção. Tenha em atenção que são conhecidos problemas ao eliminar um inquilino de B2C de pré-visualização e voltar a criar um inquilino de B2C de escala de produção com o mesmo nome de domínio. Terá de criar um inquilino de B2C de escala de produção com um nome de domínio diferente.


![Captura de ecrã de um inquilino de pré-visualização](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
