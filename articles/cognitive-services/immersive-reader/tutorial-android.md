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
ms.openlocfilehash: d847b4ab9f3c06634390e1f67dfed36df938c3ad
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050167"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-android-java-code-sample"></a>Tutorial: Lançar o Leitor Imersivo usando a amostra de código Android Java

Na [visão geral,](./overview.md)aprendeu sobre o que é o Leitor Imersivo e como implementa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem. Este tutorial abrange como criar uma aplicação Android que lança o Leitor Imersivo. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure e execute um aplicativo para Android usando um projeto de amostra
> * Adquirir um token de acesso
> * Lançar o Leitor Imersivo com conteúdo de amostra

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um recurso imersivo do Leitor configurado para a autenticação do Azure Ative Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para se preparar. Você precisará de alguns dos valores criados aqui ao configurar as propriedades ambientais. Guarde a saída da sua sessão num ficheiro de texto para referência futura.
* [Git](https://git-scm.com/)
* [SDK leitor imersivo](https://github.com/microsoft/immersive-reader-sdk)
* [Android Studio](https://developer.android.com/studio)

## <a name="configure-authentication-credentials"></a>Configure credenciais de autenticação

1. Lançar o Android Studio e abrir o projeto a partir do **diretório imersivo-leitor-sdk/js/samples/quickstart-java-android** (Java) ou **imersivo-reader-sdk/js/samples/quickstart-kotlin** diretório (Kotlin).

2. Crie um ficheiro nomeado **env** para a pasta **/ativos** e adicione os seguintes valores, fornecendo os seguintes valores conforme apropriado. Certifique-se de que não compromete este ficheiro em controlo de fontes, pois contém segredos que não devem ser tornados públicos.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET=<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Lançar o Leitor Imersivo com conteúdo de amostra

1. Escolha um emulador de dispositivo do Gestor AVD e execute o projeto.

## <a name="next-steps"></a>Próximos passos

* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](./reference.md)
* Ver amostras de código no [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)