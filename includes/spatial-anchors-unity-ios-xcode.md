---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: b052772bbfe9d69e430d9f722d8db56b48db7610
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933478"
---
Selecione **Compilar**. Na caixa de diálogo que é aberta, selecione uma pasta para a qual exportar o projeto do Xcode.

Quando a exportação for concluída, uma pasta que contém o projeto Xcode exportado será exibida.

> [!NOTE]
> Se uma janela perguntando se você deseja substituir ou anexar aparecer, recomendamos que você selecione **acrescentar** porque ele é mais rápido. Você só precisará selecionar **substituir** se estiver alterando ativos em sua cena. (Por exemplo, se você estiver adicionando, removendo ou alterando relações pai/filho, ou se estiver adicionando, removendo ou alterando Propriedades.) Se você estiver apenas fazendo alterações no código-fonte, **Append** deverá ser suficiente.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Converter o projeto Xcode em xcworkspace contendo referências de âncoras espaciais do Azure

Na pasta do projeto Xcode exportada, execute este comando no terminal para instalar o CocoaPods necessário para o projeto:

```bash
pod install --repo-update
```

Agora você pode abrir `Unity-iPhone.xcworkspace` para abrir o projeto no Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Consulte as etapas de solução de problemas [aqui](../articles/spatial-anchors/quickstarts/get-started-unity-ios.md#cocoapods-issues-on-macos-catalina-1015) se você estiver tendo problemas de CocoaPod após a atualização para o MacOS Catalina (10,15).

Selecione o nó raiz **Unity-iPhone** para exibir as configurações do projeto e, em seguida, selecione a guia **geral** .

Em **assinatura**, verifique se a **assinatura gerenciar automaticamente** está habilitada. Se não estiver, habilite-o e, em seguida, selecione **habilitar automático** na caixa de diálogo que aparece para redefinir as configurações de compilação.

Em **informações de implantação**, verifique se o **destino de implantação** está definido como `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Conecte o dispositivo iOS ao Mac e defina o **esquema ativo** para seu dispositivo IOS.

![Selecionar o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **Compilar e execute o esquema atual**.

![Implantar e executar](./media/spatial-anchors-unity/deploy-run.png)

> [!NOTE]
> Se você vir um erro `library not found for -lPods-Unity-iPhone`, provavelmente você abriu o arquivo `.xcodeproj` em vez do arquivo `.xcworkspace`.
