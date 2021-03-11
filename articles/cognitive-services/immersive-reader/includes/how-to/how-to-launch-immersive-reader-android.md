---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: d5a483cb239893d04d2c2581fb378f411878f4ba
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620178"
---
## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](../../how-to-create-immersive-reader.md) para se preparar.  Você precisará de alguns dos valores criados aqui ao configurar as propriedades ambientais. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
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

* Explore a [referência Imersiva SDK](https://github.com/microsoft/immersive-reader-sdk) do leitor e a [referência SDK do leitor imersivo](../../reference.md).
* Ver amostras de código no [GitHub.](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)