---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: e8daaaf5b6b15eb3095f11e94c707a33b4b18e28
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305302"
---
Selecione **criar**. Na caixa de diálogo que se abre, selecione uma pasta para exportar o projeto Xcode para.

Quando a exportação for concluída, será apresentada uma pasta que contém o projeto Xcode exportado.

> [!NOTE]
> Se for apresentada uma janela que lhe pergunta se pretende substituir ou de acréscimo, recomendamos que seleciona **acréscimo** porque é mais rápido. Só deverá precisar de selecionar **substitua** se estiver a alterar ativos em seu cenário. (Por exemplo, se estiver a adicionar, remover, ou alterar relações pai/filho ou se estiver a adicionar, remover ou alterar as propriedades.) Se estiver a fazer alterações no código-fonte, somente **acréscimo** deve ser suficiente.

### <a name="open-the-xcode-project"></a>Abra o projeto Xcode

Na pasta de projeto Xcode exportada, execute este comando no Terminal para instalar os CocoaPods necessários para o projeto:

```bash
pod install --repo-update
```

Agora pode abrir `Unity-iPhone.xcworkspace` para abrir o projeto no Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Se vir um `library not found for -lPods-Unity-iPhone` erro, provavelmente abriu o `.xcodeproj` de ficheiros em vez do `.xcworkspace` ficheiro. 

Selecione a raiz **Unity iPhone** nó para ver as definições do projeto e, em seguida, selecione a **geral** separador.

Sob **assinatura**, certifique-se **gerir assinatura automaticamente** está ativada. Se não for, ative-a e, em seguida, selecione **ativar automática** na caixa de diálogo que aparece para repor as definições de compilação.

Sob **informações de implantação**, certifique-se a **destino da implementação** está definida como `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Implementar a aplicação no seu dispositivo iOS

Ligue o dispositivo iOS para o Mac e o conjunto a **esquema do Active Directory** para o seu dispositivo iOS.

![Selecione o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **criar e, em seguida, executar o esquema atual**.

![Implementar e executar](./media/spatial-anchors-unity/deploy-run.png)