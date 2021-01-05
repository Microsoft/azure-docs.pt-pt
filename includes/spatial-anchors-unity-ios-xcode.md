---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 81d2804d99896200ea6f68592ea168112e172c20
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762619"
---
Selecione **Build**. No painel que abre, selecione uma pasta para exportar o projeto Xcode para.

   Quando a exportação estiver concluída, aparece uma pasta que contém o projeto Xcode exportado.

   > [!NOTE]
   > Se aparecer uma janela com uma mensagem a perguntar se pretende substituir ou anexar, recomendamos que selecione **o Apêndice**, porque é mais rápido. Deve selecionar **Substitua-o** apenas se estiver a mudar de ativos na sua cena. Por exemplo, pode estar a adicionar, remover ou alterar relações entre pais e filhos, ou pode estar a adicionar, remover ou alterar propriedades. Se estiver apenas a fazer alterações no código fonte, **o Apêndice** deve ser suficiente.

## <a name="open-the-xcode-project"></a>Abra o projeto Xcode

Agora pode abrir o seu `Unity-iPhone.xcodeproj` projeto no Xcode. 

Pode lançar o Xcode e abrir o projeto exportado `Unity-iPhone.xcodeproj` ou lançar o projeto no Xcode executando o seguinte comando a partir do local onde exportou o projeto:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Selecione o nó **raiz Unidade-iPhone** para visualizar as definições do projeto e, em seguida, selecione o separador **Geral.**

Em **Informação de Implantação**, certifique-se de que o alvo de implantação está definido para **iOS 11.0**.

Selecione o **separador 'Assinatura & Capacidades'** e certifique-se de que **a assinatura é gerida automaticamente.** Se não estiver, ative-o e, em seguida, reinicie as definições de construção selecionando **Ativar Automático** no painel que aparece.

## <a name="deploy-the-app-to-your-ios-device"></a>Implemente a aplicação para o seu dispositivo iOS

Ligue o dispositivo iOS ao Mac e desaponda o **esquema ativo** ao seu dispositivo iOS.

   ![Screenshot do botão My iPhone para selecionar o dispositivo.](./media/spatial-anchors-unity/select-device.png)

Selecione **Build e, em seguida, executar o esquema atual**.

   ![Screenshot do botão de seta "Implementar e executar".](./media/spatial-anchors-unity/deploy-run.png)
