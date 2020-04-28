---
title: Configurar pontos finais
description: Como encontrar os pontos finais de autenticação para uma aplicação personalizada que está a desenvolver ou a registar-se com a Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 28d85736019a6fea6d977d813fdc1c9be6429748
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80883275"
---
# <a name="how-to-configure-endpoints"></a>Como configurar pontos finais

Pode encontrar os pontos finais de autenticação para a sua aplicação no [portal Azure](https://portal.azure.com).

-   Navegue para o [portal Azure.](https://portal.azure.com)

-   A partir do painel de navegação esquerdo, clique no **Diretório Ativo Azure**.

-   Clique em **Registos de Aplicações** e escolha **Pontos Finais**.

-   Isto abre a página **Endpoints,** que lista todos os pontos finais de autenticação para o seu inquilino.

-   Utilize o ponto final específico do protocolo de autenticação que está a utilizar, em conjunto com o ID da aplicação para elaborar o pedido de autenticação específico da sua aplicação.

## <a name="next-steps"></a>Passos seguintes
[Guia para programadores do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
