---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012127"
---
Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Na **plataforma** secção, selecione **Android**. Alterar o **sistema de compilação** para **Gradle** e selecione **exportar projeto**.

Selecione **plataforma de comutador** para alterar a plataforma para **Android**. Unity poderá pedir-lhe para instalar componentes de suporte do Android que estejam em falta.

![Janela de definições de criação do Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Fechar o **definições de criação** janela.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Transferir e importar o SDK de ARCore para Unity

Transferir o `unitypackage` ficheiro a partir de [versões do SDK de ARCore para Unity 1.7](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). Novamente no projeto para Unity, selecione **ativos** > **importar pacote** > **pacote personalizado** e, em seguida, selecione o `unitypackage` ficheiro transferiu anteriormente. Na **importar pacote do Unity** caixa de diálogo, certifique-se de que todos os ficheiros estão selecionados e, em seguida, selecione **importação**.
