---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/14/2020
ms.author: rgarcia
ms.openlocfilehash: b93243a537fafce6d865ec207b12dc2654cafd20
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89536274"
---
Selecione **Build**. Na caixa de diálogo que abre, selecione uma pasta para exportar o projeto Xcode para.

Quando a exportação estiver concluída, aparecerá uma pasta que contenha o projeto Xcode exportado.

> [!NOTE]
> Se aparecer uma janela a perguntar se pretende substituir ou apêndice, recomendamos que selecione **o Apêndice** porque é mais rápido. Só deve selecionar **Substituir** se estiver a mudar de ativos na sua cena. (Por exemplo, se estiver a adicionar, remover ou alterar relações pai/filho, ou se estiver a adicionar, remover ou alterar propriedades.) Se estiver apenas a fazer alterações no código fonte, **o Apêndice** deve ser suficiente.

## <a name="open-the-xcode-project"></a>Abra o projeto Xcode

Agora pode abrir `Unity-iPhone.xcodeproj` no Xcode. Pode lançar o Xcode e abrir o projeto exportado `Unity-iPhone.xcodeproj` ou lançar o projeto no Xcode executando o seguinte comando a partir do local onde exportou o projeto:

```bash
open ./Unity-iPhone.xcodeproj
```

Selecione o nó **raiz Unidade-iPhone** para visualizar as definições do projeto e, em seguida, selecione o separador **Geral.**

Ao **assinar,** certifique-se de que **a assinatura é** ativada automaticamente. Se não estiver, ative-o e, em seguida, selecione **Ative Automatic** na caixa de diálogo que parece redefinir as definições de construção.

Em **Informações de Implantação**, certifique-se de que o **alvo de implantação** está definido para `11.0` .

## <a name="deploy-the-app-to-your-ios-device"></a>Implemente a aplicação para o seu dispositivo iOS

Ligue o dispositivo iOS ao Mac e desaponda o **esquema ativo** ao seu dispositivo iOS.

![Selecione o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **Build e, em seguida, executar o esquema atual**.

![Implementar e executar](./media/spatial-anchors-unity/deploy-run.png)
