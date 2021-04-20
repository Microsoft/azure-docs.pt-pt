---
title: Use widgets comunitários no portal do desenvolvedor
titleSuffix: Azure API Management
description: Saiba mais sobre widgets comunitários para o portal de desenvolvimento da API Management e como injetá-los e usá-los no seu código.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741825"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Use widgets comunitários no portal do desenvolvedor

Todos os desenvolvedores colocam os seus widgets com contribuições comunitárias na `/community/widgets/` pasta do [repositório](https://github.com/Azure/api-management-developer-portal)do portal de desenvolvimento da API Management GitHub. Cada um foi aceite pela equipa do portal de desenvolvedores. Pode utilizar os widgets injetando-os na sua [versão auto-hospedada](developer-portal-self-host.md) do portal. A versão gerida do portal de desenvolvedores não suporta atualmente widgets comunitários.

> [!NOTE]
> A equipa do portal de desenvolvedores inspeciona minuciosamente os widgets e as suas dependências. No entanto, a equipa não pode garantir que é seguro carregar os widgets. Use o seu próprio julgamento ao decidir usar um widget contribuído pela comunidade. Consulte [as nossas diretrizes de contribuição widget](developer-portal-widget-contribution-guidelines.md#contribution-guidelines) para saber mais sobre as nossas medidas preventivas.

## <a name="inject-and-use-external-widgets"></a>Injetar e utilizar widgets externos

1. Crie um [ambiente local](developer-portal-self-host.md#step-1-set-up-local-environment) para o mais recente lançamento do portal de desenvolvedores.

1. Vá à pasta do widget no `/community/widgets` diretório. Leia a descrição do widget no `readme.md` ficheiro.

1. Registar o widget nos módulos do portal:

    1. `src/apim.design.module.ts` - um módulo que regista dependências do tempo de conceção.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` - um módulo que registe dependências de tempo de publicação.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` - um módulo que regista dependências de tempo de execução.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Verifique se o widget tem um `npm_dependencies` ficheiro.

1. Em caso afirmativo, copie os comandos do ficheiro e execute-os no diretório superior do repositório.

    Ao fazê-lo, instalará as dependências do widget.

1. Execute `npm start`.

Pode ver o widget na categoria **comunitária** no seletor widget.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Screenshot do seletor de widget":::


## <a name="next-steps"></a>Passos seguintes


Saiba mais sobre o portal de desenvolvedores:

- [Descrição geral do portal do programador da Gestão de API do Azure](api-management-howto-developer-portal.md)

- [Contribuir widgets](developer-portal-widget-contribution-guidelines.md)
