---
title: Implementar exemplo do Unity no HoloLens
description: Quickstart que mostra como colocar a amostra de unidade nos HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679738"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Quickstart: Implementar amostra de unidade para HoloLens

Este quickstart cobre como implementar e executar a aplicação de amostra sinuosa para unidade para um HoloLens 2.

Neste arranque rápido, aprenderá a:

> [!div class="checklist"]
>
>* Construa o aplicativo de amostra sinuoso para HoloLens
>* Implementar a amostra para o dispositivo
>* Executar a amostra no dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Neste arranque rápido vamos implementar o projeto de amostra a partir de [Quickstart: Render um modelo com Unidade](render-model.md).

Certifique-se de que as suas credenciais são guardadas corretamente com a cena e pode ligar-se a uma sessão dentro do editor da Unidade.

## <a name="build-the-sample-project"></a>Construir o projeto da amostra

1. Abrir *> construir definições.*
1. Alterar *plataforma* para **plataforma Universal Windows**
1. Definir *dispositivo alvo* para **HoloLens**
1. Definir *arquitetura* para **ARM64**
1. Definir *tipo de construção* para configurações de construção de projeto ![ **d3D**](./media/unity-build-settings.png)
1. Selecione **Switch para Plataforma**
1. Ao premir **Build** (ou 'Construir e Executar'), será-lhe pedido que selecione alguma pasta onde a solução deve ser armazenada
1. Abra o **gerado Quickstart.sln** com Visual Studio
1. Altere a configuração para **Lançamento** e **ARM64**
1. Mude o modo de debugger para a configuração da Solução de **Máquina** ![Remota](media/unity-deploy-config.png)
1. Construir a solução (F7)
1. Para o projeto 'Quickstart', vá ao *Properties > Debugging*
    1. Certifique-se de que o *lançamento* da configuração está ativo
    1. Definir *Debugger para lançar* para máquina **remota**
    1. Mude o nome da *máquina* para o **IP dos seus HoleLens**

## <a name="launch-the-sample-project"></a>Lançar o projeto da amostra

1. Ligue os HoloLens com um cabo USB ao seu PC.
1. Inicie o Debugger no Estúdio Visual (F5). Irá automaticamente implantar a aplicação para o dispositivo.

A aplicação de amostras deve ser lançada e, em seguida, iniciar uma nova sessão. Passado algum tempo, a sessão está pronta e o modelo de forma remota aparecerá à sua frente.
Se quiser lançar a amostra uma segunda vez mais tarde, também pode encontrá-la a partir do menu de início holoLens agora.

## <a name="next-steps"></a>Passos seguintes

No próximo arranque rápido, vamos dar uma olhada na conversão de um modelo personalizado.

> [!div class="nextstepaction"]
> [Quickstart: Converter um modelo para renderização](convert-model.md)
