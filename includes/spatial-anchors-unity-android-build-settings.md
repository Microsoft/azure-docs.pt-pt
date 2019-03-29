---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 355d89b1fd7d7cc8c9db58122d51c6ff7dff5f3b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622591"
---
No Unity, abra o projeto no `Unity` pasta.

Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Na **plataforma** secção, selecione **Android**. Alterar o **sistema de compilação** para **Gradle** e selecione **exportar projeto**.

Selecione **plataforma de comutador** para alterar a plataforma para **Android**. Unity poderá pedir-lhe para instalar componentes de suporte do Android que estejam em falta.

![Janela de definições de criação do Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Fechar o **definições de criação** janela.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Transferir e importar o SDK de ARCore para Unity

Transferir o `unitypackage` ficheiro a partir de [versões do SDK de ARCore para Unity 1.5](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Novamente no projeto para Unity, selecione **ativos** > **importar pacote** > **pacote personalizado** e, em seguida, selecione o `unitypackage` ficheiro transferiu anteriormente. Na **importar pacote do Unity** caixa de diálogo, certifique-se de que todos os ficheiros estão selecionados e, em seguida, selecione **importação**.
