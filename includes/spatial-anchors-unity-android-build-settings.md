---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752244"
---
Abra o Unity e abra o projeto no `Unity` pasta.

Open **definições de criação** ao selecionar **ficheiro** -> **definições de criação**.

Na **plataforma** secção, selecione **Android**. Em seguida, altere o **criar sistema** para **Gradle** e verificar o **exportar projeto** opção.

Selecione **plataforma de comutador** para alterar a plataforma para **Android**. Unity poderá pedir-lhe para instalar componentes de suporte do Android que estejam em falta.

![Definições de compilação do Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Fechar o **definições de criação** janela.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Transferir e importar o SDK de ARCore para Unity

Transferir o `unitypackage` ficheiro a partir de [versões do SDK de ARCore para Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Novamente no projeto para Unity, selecione **ativos** -> **importar pacote** -> **pacote personalizado...**  e, em seguida, selecione o `unitypackage` ficheiros anteriormente transferidos. Na **importar pacote do Unity** caixa de diálogo, certifique-se de que todos os ficheiros estão selecionados e, em seguida, selecione **importação**.
