---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72933478"
---
Selecione **Construir**. Na caixa de diálogo que abre, selecione uma pasta para exportar o projeto Xcode para.

Quando a exportação estiver concluída, aparecerá uma pasta que contenha o projeto Xcode exportado.

> [!NOTE]
> Se aparecer uma janela a perguntar-lhe se pretende substituir ou anexar, recomendamos que selecione **O Apêndice** porque é mais rápido. Só precisa selecionar **Substitua** se estiver a mudar de ativo na sua cena. (Por exemplo, se estiver a adicionar, remover ou alterar relações entre pais e filhos, ou se estiver a adicionar, remover ou alterar propriedades.) Se estiver apenas a fazer alterações de código fonte, o **Apêndice** deve ser suficiente.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Converter o projeto Xcode em xcworkspace contendo referências de âncoras espaciais Azure

Na pasta do projeto Xcode exportada, execute este comando no Terminal para instalar os CocoaPods necessários para o projeto:

```bash
pod install --repo-update
```

Agora pode `Unity-iPhone.xcworkspace` abrir o projeto em Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Veja aqui os [passos](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) de resolução de problemas se tiver problemas com o CocoaPod depois de atualizar para macOS Catalina (10.15).

Selecione o nó de **unidade-iPhone** raiz para visualizar as definições do projeto e, em seguida, selecione o separador **Geral.**

Em **'Assinatura', certifique-se**de que a assinatura está ativada **automaticamente.** Se não for, ative-o e, em seguida, **selecione Ativar Automático** na caixa de diálogo que parece redefinir as definições de construção.

Em informações de **implantação,** certifique-se de que o alvo de **implantação** está definido para `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Implemente a aplicação para o seu dispositivo iOS

Ligue o dispositivo iOS ao Mac e detete **tede** o esquema ativo para o seu dispositivo iOS.

![Selecione o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **Construir e, em seguida, executar o esquema atual**.

![Implementar e executar](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Se vir `library not found for -lPods-Unity-iPhone` um erro, provavelmente abriu o `.xcodeproj` ficheiro em vez do `.xcworkspace` ficheiro.
