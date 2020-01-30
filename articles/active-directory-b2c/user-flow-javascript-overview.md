---
title: Versões javaScript e layout de página
titleSuffix: Azure AD B2C
description: Saiba como ativar o JavaScript e utilize as versões de layout da página no Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f6d4849b02f320c7479469b4ee56be50e4f8dee
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840099"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript e versões de layout de página no Diretório Ativo Azure B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure AD B2C fornece um conjunto de conteúdos embalados contendo HTML, CSS e JavaScript para os elementos de interface do utilizador nos fluxos de utilizador e políticas personalizadas. Para ativar o JavaScript para as suas aplicações, deve adicionar um elemento à sua [política personalizada](custom-policy-overview.md) ou capacitá-lo no portal para fluxos de utilizadores, selecionar um layout de página e [usáb2clogin.com](b2clogin.md) nos seus pedidos.

Se pretender ativar o código do lado do cliente [JavaScript,](javascript-samples.md) terá de ter a certeza de que os elementos em que está a basear o seu JavaScript são imutáveis. Caso contrário, quaisquer alterações podem causar comportamentos inesperados nas suas páginas de utilizador. Para evitar estes problemas, pode impor o uso de um layout de página e especificar uma versão de layout da página. Ao fazê-lo, todas as definições de conteúdo em que baseou o seu JavaScript são imutáveis. Mesmo que não pretenda ativar o JavaScript, pode especificar uma versão de layout de página para as suas páginas.

## <a name="user-flows"></a>Fluxos do utilizador

No fluxo do utilizador **Propriedades,** pode ativar o JavaScript, que também aplica a utilização de um layout de página. Em seguida, pode definir a versão de layout da página para o fluxo do utilizador, tal como descrito na secção seguinte.

![Página de propriedades de fluxo do utilizador com definição Enable JavaScript realçada](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Selecione uma versão de layout de página

Quer ative ou não o JavaScript nas propriedades do fluxo do utilizador, pode especificar uma versão de layout de página para as páginas de fluxo do utilizador. Abra o fluxo do utilizador e selecione **os layouts da página**. Em **NOME LAYOUT,** selecione uma página de fluxo do utilizador e escolha a versão layout da **página**.

Para obter informações sobre as diferentes versões de layout da página, consulte o registo de alteração da [versão](page-layout.md#version-change-log).

![Definições de layout da página no portal mostrando a versão de layout da página dropdown](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Políticas personalizadas

Para ativar o JavaScript em políticas personalizadas, adicione o elemento **ScriptExecution** ao elemento **RelyingParty** no seu ficheiro de política personalizado. Para mais informações, consulte [as amostras JavaScript para utilização no Diretório Ativo Azure B2C](javascript-samples.md).

Quer ative ou não o JavaScript nas suas políticas personalizadas, pode especificar uma versão de layout de página para as suas páginas. Para obter mais informações sobre a especificação de um layout de página, consulte [Selecione um layout de página no Azure Ative Directory B2C utilizando políticas personalizadas](page-layout.md).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre as diferentes versões de layout da página, consulte a secção de registo de alteração da **versão** do [layout de página no Azure Ative Directory B2C utilizando políticas personalizadas](page-layout.md#version-change-log).

Pode encontrar exemplos de utilização do JavaScript em [amostras JavaScript para utilização no Diretório Ativo Azure B2C](javascript-samples.md).
