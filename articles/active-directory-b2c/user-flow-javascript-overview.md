---
title: JavaScript e página Contrato versões para fluxos de utilizador no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como ativar o JavaScript e usar as versões de contrato de página para personalizar um fluxo de utilizador no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 5102755c9e830f43fa92e8546e5125960e0a2f9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401563"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>Como utilizar versões de contrato de página e JavaScript num fluxo de utilizador

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure AD B2C fornece um conjunto de conteúdo de pacote que contém HTML, CSS e JavaScript para os elementos de interface de utilizador nos seus fluxos de utilizador. Se pretender ativar [JavaScript](javascript-samples.md) fluxos de código do lado do cliente no seu usuário, desejará para garantir que os elementos estiver basear o JavaScript em são imutáveis. Caso contrário, todas as alterações podem causar um comportamento inesperado no seu usuário páginas de fluxo. Para evitar estes problemas, pode impor a utilização de um contrato de página para um fluxo de utilizador e especificar uma versão de contrato de página. Fazer isto irá garantir que todas as definições de conteúdo que baseia sua JavaScript são imutáveis. Mesmo se não pretender ativar o JavaScript para um fluxo de utilizador, pode especificar uma versão de contrato de página para as páginas de fluxo de utilizador.

> [!NOTE]
> Este artigo discute o JavaScript para fluxos de utilizador, mas também pode usar o JavaScript e selecione as versões de contrato de página, quando estiver usando [políticas personalizadas](page-contract.md).

## <a name="enable-javascript"></a>Ativar JavaScript

Nas propriedades do fluxo de utilizador, pode ativar o JavaScript, que também impõe a utilização de um contrato de página. Em seguida, pode definir a versão de contrato de página, conforme descrito na secção seguinte.

![Ativar a definição de JavaScript](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>Especifique uma versão de contrato de página

Se é ou não ative o JavaScript nas propriedades do seu fluxo de utilizador, pode especificar uma versão de contrato de página para as páginas de fluxo de utilizador. Abra o fluxo de utilizador e selecione **Layouts de página**. Sob **nome de Layout**, selecione uma página de fluxo de utilizador e escolha o **versão de contrato de página**.

![Ativar a definição de JavaScript](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>Passos Seguintes
Consulte a [exemplos de JavaScript para utilização no Azure Active Directory B2C](javascript-samples.md).
