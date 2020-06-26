---
title: Versões javaScript e layout de página
titleSuffix: Azure AD B2C
description: Saiba como ativar o JavaScript e use versões de layout de página no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cbc0f607af31d0e3b3715e73ddbde19143bd1599
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85384146"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Versões javaScript e layout de página em Azure Ative Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure AD B2C fornece um conjunto de conteúdo embalado contendo HTML, CSS e JavaScript para os elementos de interface do utilizador nos fluxos do utilizador e políticas personalizadas.

Para ativar o JavaScript para as suas aplicações:

* Ativa-o no fluxo do utilizador utilizando o portal Azure
* Selecione um [layout de página](page-layout.md)
* Utilize [b2clogin.com](b2clogin.md) nos seus pedidos

Se pretende ativar o código do lado do cliente [JavaScript,](javascript-samples.md) os elementos em que baseia o JavaScript devem ser imutáveis. Se não forem imutáveis, quaisquer alterações podem causar comportamentos inesperados nas suas páginas de utilizador. Para evitar estes problemas, imponha o uso de um layout de página e especifique uma versão de layout de página para garantir que as definições de conteúdo em que baseou o seu JavaScript são imutáveis. Mesmo que não pretenda ativar o JavaScript, pode especificar uma versão de layout de página para as suas páginas.

## <a name="enable-javascript"></a>Ativar JavaScript

Nas **propriedades**de fluxo do utilizador, pode ativar o JavaScript. Ativar o JavaScript também impõe o uso de um layout de página. Em seguida, pode definir a versão de layout da página para o fluxo do utilizador, conforme descrito na secção seguinte.

![Página de propriedades de fluxo do utilizador com definição de Ativa JavaScript realçada](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Selecione uma versão de layout de página

Quer ative ou não o JavaScript nas propriedades do fluxo do utilizador, pode especificar uma versão de layout de página para as páginas de fluxo do utilizador. Abra o fluxo do utilizador e selecione **os layouts de página**. Em **DESIGN NAME**, selecione uma página de fluxo do utilizador e escolha a versão layout da **página**.

Para obter informações sobre as diferentes versões de layout de página, consulte o registo de alteração da [versão do layout da página](page-layout.md).

![Definições de layout de página no portal mostrando versão de layout de página dropdown](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Passos seguintes

Pode encontrar exemplos de utilização do JavaScript em [amostras JavaScript para utilização no Azure Ative Directory B2C](javascript-samples.md).
