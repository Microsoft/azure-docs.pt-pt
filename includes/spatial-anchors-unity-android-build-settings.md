---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 53f480b8858e2bbe7d4699d8637ecaa5ab0c08a3
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305171"
---
No Unity, abra o projeto no `Unity` pasta.

Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Na **plataforma** secção, selecione **Android**. Alterar o **sistema de compilação** para **Gradle** e selecione **exportar projeto**.

Selecione **plataforma de comutador** para alterar a plataforma para **Android**. Unity poderá pedir-lhe para instalar componentes de suporte do Android que estejam em falta.

![Janela de definições de criação do Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Fechar o **definições de criação** janela.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Transferir e importar o SDK de ARCore para Unity

Transferir o `unitypackage` ficheiro a partir de [versões do SDK de ARCore para Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Novamente no projeto para Unity, selecione **ativos** > **importar pacote** > **pacote personalizado** e, em seguida, selecione o `unitypackage` ficheiro transferiu anteriormente. Na **importar pacote do Unity** caixa de diálogo, certifique-se de que todos os ficheiros estão selecionados e, em seguida, selecione **importação**.
