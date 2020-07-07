---
title: 'Tutorial: Lançar o Leitor Imersivo usando a amostra de código Swift iOS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai configurar e executar uma aplicação swift amostra que lança o Leitor Imersivo.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050180"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Tutorial: Lançar o Leitor Imersivo usando a amostra de código Swift iOS

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. Este tutorial abrange como criar uma aplicação iOS que lança o Leitor Imersivo. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure e execute uma aplicação Swift para iOS usando um projeto de amostra
> * Adquirir um token de acesso
> * Lançar o Leitor Imersivo com conteúdo de amostra

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para se preparar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades ambientais. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [SDK leitor imersivo](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Configure credenciais de autenticação

1. Abra o Xcode e abra **o imersivo-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
2. No menu superior, clique no **Esquema de > de Produtos > Esquema de Edição...**
3. Na vista **Executar,** clique no **separador Argumentos.**
4. Na secção **Variáveis Ambientais,** adicione os seguintes nomes e valores, fornecendo os valores dados quando criou o seu recurso Leitor Imersivo.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Certifique-se de que não comete a mudança acima em controlo de origem, pois contém segredos que não devem ser tornados públicos.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

1. Em Xcode, pressione **ctrl-R** para executar o projeto.

## <a name="next-steps"></a>Próximos passos

* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)
* Ver amostras de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
