---
title: JavaScript e página Contrato versões - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como ativar o JavaScript e utilizar versões do contrato de página no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef474bec71a9015209b5748b6947816002bd4a5d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511982"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Versões de contrato de página e JavaScript no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

O Azure AD B2C fornece um conjunto de conteúdo de pacote que contém HTML, CSS e JavaScript para os elementos de interface de utilizador nos seus fluxos de utilizador e as políticas personalizadas. Para ativar o JavaScript para as suas aplicações, tem de adicionar um elemento para sua [política personalizada](active-directory-b2c-overview-custom.md) ou ativá-la no portal para os fluxos de utilizador, selecione um contrato de página e utilize [b2clogin.com](b2clogin.md) nos seus pedidos.

Se pretender ativar [JavaScript](javascript-samples.md) código do lado do cliente, poderá ser útil para garantir que os elementos estiver basear o JavaScript em são imutáveis. Caso contrário, todas as alterações podem causar um comportamento inesperado em suas páginas de utilizador. Para evitar estes problemas, pode impor a utilização de um contrato de página e especificar uma versão de contrato de página. Fazer isto garante que todas as definições de conteúdo que baseia sua JavaScript são imutáveis. Mesmo se não pretender ativar o JavaScript, pode especificar uma versão de contrato de página para as suas páginas.

## <a name="user-flows"></a>Fluxos do utilizador

Nas propriedades do fluxo de utilizador, pode ativar o JavaScript, que também impõe a utilização de um contrato de página. Em seguida, pode definir a versão de contrato de página, conforme descrito na secção seguinte.

![Ativar a definição de JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

Se é ou não ative o JavaScript nas propriedades do seu fluxo de utilizador, pode especificar uma versão de contrato de página para as páginas de fluxo de utilizador. Abra o fluxo de utilizador e selecione **Layouts de página**. Sob **nome de Layout**, selecione uma página de fluxo de utilizador e escolha o **versão de contrato de página**.

![Ativar a definição de JavaScript](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Políticas personalizadas

Para ativar o JavaScript nas políticas personalizadas, adiciona os **ScriptExecution** elemento para a **RelyingParty** elemento em seu arquivo de política personalizada. Para obter mais informações, consulte [exemplos de JavaScript para utilização no Azure Active Directory B2C](javascript-samples.md).

Se é ou não ative o JavaScript em suas políticas personalizadas, pode especificar uma versão de contrato de página para as suas páginas. Para obter mais informações sobre como especificar um contrato de página, consulte [selecionar um contrato de página com as políticas personalizadas no Azure Active Directory B2C](page-contract.md).

## <a name="next-steps"></a>Passos Seguintes

Consulte a [exemplos de JavaScript para utilização no Azure Active Directory B2C](javascript-samples.md).
