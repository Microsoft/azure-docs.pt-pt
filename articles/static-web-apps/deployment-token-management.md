---
title: Redefinir tokens de implementação em Azure Static Web Apps (Preview)
description: Redefinir fichas num site de aplicações web estáticas Azure
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746519"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Redefinir tokens de implementação em Azure Static Web Apps (Preview)

Quando cria um novo site de Aplicações Web Estáticas Azure, o Azure gera um símbolo usado para identificar a aplicação durante a implementação. Durante o provisionamento, este símbolo é armazenado como um segredo no repositório gitHub. Este artigo explica como usar e gerir este símbolo.

Normalmente, não precisa de se preocupar com o token de implementação, mas são algumas das razões pelas quais poderá precisar de recuperar ou redefinir o token.

* **Compromisso simbólico**: Repor o seu token se estiver exposto a uma parte externa.
* **Implantação a partir de um repositório GitHub separado**: Se estiver a ser implantado manualmente a partir de um repositório gitHub separado, então precisa de definir o token de implantação no novo repositório.

## <a name="prerequisites"></a>Pré-requisitos

- Um repositório gitHub existente configurado com Azure Static Web Apps.
- Consulte [a Construção da sua primeira aplicação estática](getting-started.md) se não tiver uma.

## <a name="reset-a-deployment-token"></a>Redefinir um token de implantação

1. Clique em Gerir o link **de token de implementação** na página _de visão geral_ do seu site de Aplicações Web Estáticas Azure.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Gestão de ficha de implantação":::

1. Clique no botão **Reset token.**

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Reposição do token de implantação":::

1. Depois de apresentar um novo token no campo _token de implementação,_ copie o token clicando em Copiar para o ícone **da área de transferência.**


## <a name="update-a-secret-in-the-github-repository"></a>Atualize um segredo no repositório do GitHub

Para manter a implementação automatizada em funcionamento, depois de repor um token, precisa de definir o novo valor no repositório gitHub correspondente.

1. Navegue no repositório do seu projeto no GitHub e clique no **separador Definições.**
1. Clique no item do menu **Segredos.** Você encontrará um segredo gerado durante o provisionamento estático web app nomeado _AZURE_STATIC_WEB_APPS_API_TOKEN_... na secção de segredos do _Repositório._

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Enumeração de segredos de repositório":::

    > [!NOTE]
    > Se criou o site Azure Static Web Apps contra vários ramos deste repositório, verá vários _AZURE_STATIC_WEB_APPS_API_TOKEN_... segredos nesta lista. Selecione o correto, correspondendo o nome do ficheiro listado no campo de _fluxo de trabalho editar_ no _separador Visão Geral_ do site estática de Aplicações Web.

1. Clique no botão **'Actualizar'** para abrir o editor.
1. **Cole o valor** do token de implantação para o campo _Valor._
1. Clique **em Update secret**.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Atualizar o segredo do repositório":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Publicar a partir de um gerador de sites estáticos](publish-gatsby.md)
