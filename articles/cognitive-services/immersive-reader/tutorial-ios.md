---
title: 'Tutorial: Inicie o Leitor Imersivo utilizando a amostra de código Swift iOS'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você vai configurar e executar uma aplicação swift amostra que inicia o Leitor Imersivo.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: d9bb3a5fb41215f2e839efc7989ea7854f254f16
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537940"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>Tutorial: Inicie o Leitor Imersivo utilizando a amostra de código Swift iOS

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. Este tutorial abrange como criar uma aplicação iOS que inicia o Leitor Imersivo. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure e execute uma aplicação Swift para iOS utilizando um projeto de amostra.
> * Adquira um símbolo de acesso.
> * Inicie o Leitor Imersivo com o conteúdo da amostra.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para se preparar. Você vai precisar de alguns dos valores criados aqui quando configurar as propriedades ambientais. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [macOS](https://www.apple.com/macos).
* [Git.](https://git-scm.com/)
* [Leitor Imersivo SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Configure credenciais de autenticação

1. Abra o Xcode e abra **o imersivo-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
1. No menu superior, selecione **Product**  >  **Scheme**  >  **Edit Scheme Scheme**.
1. Na vista **Executar,** selecione o **separador Argumentos.**
1. Na secção **Variáveis Ambientais,** adicione os seguintes nomes e valores. Forneça os valores dados quando criou o seu recurso Leitor Imersivo.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Não comprometas esta mudança no controlo de fontes porque contém segredos que não devem ser tornados públicos.

## <a name="start-the-immersive-reader-with-sample-content"></a>Inicie o Leitor Imersivo com o conteúdo da amostra

Em Xcode, selecione **Ctrl+R** para executar o projeto.

## <a name="next-steps"></a>Próximos passos

* Explore a [referência Imersiva SDK](https://github.com/microsoft/immersive-reader-sdk) do leitor e a [referência SDK do leitor imersivo](./reference.md).
* Ver amostras de código no [GitHub.](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
