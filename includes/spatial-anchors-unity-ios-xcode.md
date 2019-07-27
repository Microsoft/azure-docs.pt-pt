---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: d8b6b1bfcbceb1168d0f74c73e72bd42b41bb2ec
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562511"
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

Agora você pode abrir `Unity-iPhone.xcworkspace` o para abrir o projeto no Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Se você vir um `library not found for -lPods-Unity-iPhone` erro, provavelmente você abriu o `.xcodeproj` arquivo em vez do `.xcworkspace` arquivo. 

Selecione o nó raiz **Unity-iPhone** para exibir as configurações do projeto e, em seguida, selecione a guia **geral** .

Em **assinatura**, verifique se a **assinatura gerenciar automaticamente** está habilitada. Se não estiver, habilite-o e, em seguida, selecione **habilitar automático** na caixa de diálogo que aparece para redefinir as configurações de compilação.

Em **informações de implantação**, verifique se o **destino de implantação** está `11.0`definido como.

### <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Conecte o dispositivo iOS ao Mac e defina o **esquema ativo** para seu dispositivo IOS.

![Selecione o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **Compilar e execute o esquema atual**.

![Implantar e executar](./media/spatial-anchors-unity/deploy-run.png)