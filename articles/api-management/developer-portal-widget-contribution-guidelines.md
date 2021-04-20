---
title: Como contribuir com widgets para o portal de desenvolvimento
titleSuffix: Azure API Management
description: Saiba mais sobre as diretrizes recomendadas a seguir quando contribuir com um widget para o repositório do portal de desenvolvimento da API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741878"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Como contribuir com widgets para o portal de desenvolvimento da API Management

Se você gostaria de contribuir com um widget para o [repositório](https://github.com/Azure/api-management-developer-portal)do portal de desenvolvimento da API Management GitHub, siga este processo em três etapas:

1. Garfo o repositório.

1. Implementar o widget.

1. Abra um pedido de puxar para incluir o seu widget no repositório oficial.

O teu widget herdará a licença do repositório. Estará disponível para [instalação de opt-in](developer-portal-use-community-widgets.md) na versão auto-hospedada do portal. A equipa do portal de desenvolvedores pode decidir incluí-lo também na versão gerida do portal.

Consulte o tutorial de implementação do [widget](developer-portal-implement-widgets.md) para um exemplo de como desenvolver o seu próprio widget.

## <a name="contribution-guidelines"></a>Orientações para a contribuição

Esta orientação destina-se a garantir a segurança e privacidade dos nossos clientes e visitantes aos seus portais. Siga estas orientações para garantir que a sua contribuição seja aceite:

1. Coloque o widget na `community/widgets/<your-widget-name>` pasta.

1. O nome do seu widget deve ser minúsculo e alfanumérico com traços que separam as palavras. Por exemplo, `my-new-widget`.

1. A pasta deve conter uma imagem do seu widget num portal publicado.

1. A pasta deve conter um `readme.md` ficheiro que segue o modelo a partir do `/scaffolds/widget/readme.md` ficheiro.

1. A pasta pode conter um `npm_dependencies` ficheiro com comandos npm para instalar ou gerir as dependências do widget.

    Especificar explicitamente a versão de cada dependência. Por exemplo:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    O seu widget deve requerer dependências mínimas. Todas as dependências serão cuidadosamente inspecionadas pelos revisores. Em particular, a lógica central do seu widget deve ser aberta na pasta do seu widget. Não embrulhe num pacote npm.

1. As alterações a quaisquer ficheiros fora da pasta do widget não são permitidas como parte de uma contribuição widget. Isso inclui, mas não se limita `/package.json` a.o ficheiro.

1. Não é permitido injetar scripts de rastreio ou enviar dados de autoria do cliente para serviços personalizados.

    > [!NOTE]
    > Só é possível recolher dados de autoria do cliente através da `Logger` interface.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre as contribuições, consulte o [repositório do portal de](https://github.com/Azure/api-management-developer-portal/)desenvolvimento da API Management GitHub.

- Consulte [os widgets](developer-portal-implement-widgets.md) implementar para aprender a desenvolver o seu próprio widget, passo a passo.

- Consulte [widgets comunitários](developer-portal-use-community-widgets.md) para aprender a usar widgets contribuídos pela comunidade.