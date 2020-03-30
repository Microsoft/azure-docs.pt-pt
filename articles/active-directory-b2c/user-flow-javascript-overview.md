---
title: Versões javaScript e layout de página
titleSuffix: Azure AD B2C
description: Saiba como ativar o JavaScript e utilize as versões de layout da página no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185846"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript e versões de layout de página no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure AD B2C fornece um conjunto de conteúdos embalados contendo HTML, CSS e JavaScript para os elementos de interface do utilizador nos fluxos de utilizador e políticas personalizadas.

Para ativar o JavaScript para as suas aplicações:

* Ative-o no fluxo do utilizador utilizando o portal Azure
* Selecione um layout de [página](page-layout.md)
* Use [b2clogin.com](b2clogin.md) nos seus pedidos

Se pretender ativar o código do lado do cliente [JavaScript,](javascript-samples.md) os elementos em que baseia o seu JavaScript devem ser imutáveis. Se não forem imutáveis, quaisquer alterações podem causar comportamentos inesperados nas suas páginas de utilizador. Para prevenir estes problemas, faça cumprir o uso de um layout de página e especifique uma versão de layout da página para garantir que as definições de conteúdo em que baseou o seu JavaScript são imutáveis. Mesmo que não pretenda ativar o JavaScript, pode especificar uma versão de layout de página para as suas páginas.

## <a name="enable-javascript"></a>Ativar JavaScript

No fluxo do utilizador **Propriedades,** pode ativar o JavaScript. Ativar o JavaScript também aplica o uso de um layout de página. Em seguida, pode definir a versão de layout da página para o fluxo do utilizador, tal como descrito na secção seguinte.

![Página de propriedades de fluxo do utilizador com definição Enable JavaScript realçada](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Selecione uma versão de layout de página

Quer ative ou não o JavaScript nas propriedades do fluxo do utilizador, pode especificar uma versão de layout de página para as páginas de fluxo do utilizador. Abra o fluxo do utilizador e selecione **os layouts da página**. Em **NOME LAYOUT,** selecione uma página de fluxo do utilizador e escolha a versão layout da **página**.

Para obter informações sobre as diferentes versões de layout da página, consulte o registo de alteração da versão de [layout da página](page-layout.md).

![Definições de layout da página no portal mostrando a versão de layout da página dropdown](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Passos seguintes

Pode encontrar exemplos de utilização do JavaScript em [amostras JavaScript para utilização no Diretório Ativo Azure B2C](javascript-samples.md).
