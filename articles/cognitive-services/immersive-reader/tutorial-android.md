---
title: 'Tutorial: Lançar o Leitor Imersivo usando as amostras de código Android'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, irá configurar e executar uma aplicação Android de amostra que lança o Leitor Imersivo.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: 637b4d988a4845369a441dce55f0043d873879f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516440"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>Tutorial: Inicie o Leitor Imersivo utilizando a amostra de código Android Java

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. Este tutorial abrange como criar uma aplicação Android que inicia o Leitor Imersivo. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure e execute um aplicativo para Android usando um projeto de amostra.
> * Adquira um símbolo de acesso.
> * Inicie o Leitor Imersivo com o conteúdo da amostra.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para se preparar. Você vai precisar de alguns dos valores criados aqui quando configurar as propriedades ambientais. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [Git.](https://git-scm.com/)
* [Leitor Imersivo SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Estúdio Android](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Configure credenciais de autenticação

1. Inicie o Android Studio e abra o projeto a partir do **diretório imersivo-leitor-sdk/js/samples/quickstart-java-android** (Java) ou do **imersivo-reader-sdk/js/samples/quickstart-kotlin** directory (Kotlin).

1. Crie um ficheiro denominado **env** dentro da pasta **/ativos.** Adicione os seguintes nomes e valores e valores de fornecimento conforme apropriado. Não comprometa este ficheiro em controlo de fontes porque contém segredos que não devem ser tornados públicos.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Inicie o Leitor Imersivo com o conteúdo da amostra

Escolha um emulador de dispositivo do Gestor AVD e execute o projeto.

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência Imersiva SDK](https://github.com/microsoft/immersive-reader-sdk) do leitor e a [referência SDK do leitor imersivo](./reference.md).
* Ver amostras de código no [GitHub.](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)