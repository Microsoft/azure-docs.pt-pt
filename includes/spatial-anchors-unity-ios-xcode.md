---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 36d509bdcd1fda61cb85fae7fa38ed126697f888
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752238"
---
Selecione **criar** para abrir uma caixa de diálogo. Em seguida, selecione uma pasta para exportar o projeto Xcode.

Quando a exportação for concluída, uma pasta será apresentada que contém o projeto Xcode exportado.

### <a name="open-the-xcode-project"></a>Abra o projeto Xcode

Na pasta de projeto Xcode exportada, execute o seguinte comando para instalar os CocoaPods necessários para o projeto:

```bash
pod install
```

Agora pode abrir `Unity-iPhone.xcworkspace` para abrir o projeto no Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Se vir um `library not found for -lPods-Unity-iPhone` erro, provavelmente aberta da `.xcodeproj` de ficheiros em vez do `.xcworkspace`. Abra o `.xcworkspace` e tente novamente.

Selecione a raiz **Unity iPhone** nó para ver as definições do projeto e selecionar o **geral** separador.

Sob **assinatura**, selecione **gerir assinatura automaticamente**. Selecione **ativar automática** na caixa de diálogo que aparece para repor as definições de criar.

Sob **informações de implantação**, certifique-se a **destino da implementação** está definida como `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Implementar a aplicação no seu dispositivo iOS

Ligue o dispositivo iOS para o Mac e o conjunto a **esquema do Active Directory** para o seu dispositivo iOS.

![Selecione o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **criar e, em seguida, executar o esquema atual**.

![Implementar e executar](./media/spatial-anchors-unity/deploy-run.png)