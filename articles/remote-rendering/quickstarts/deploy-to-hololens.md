---
title: Implementar exemplo do Unity no HoloLens
description: Quickstart que mostra como colocar a amostra de Unidade nos HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: d6acc16780179654975d63ab2c0b04caf141510c
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85557051"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Quickstart: Implementar a amostra de unidade para hololens

Este quickstart cobre como implementar e executar a aplicação de amostra de arranque rápido para Unidade para um HoloLens 2.

Neste arranque rápido aprenderá a:

> [!div class="checklist"]
>
>* Construa o aplicativo de amostra de arranque rápido para HoloLens
>* Desloque a amostra para o dispositivo
>* Executar a amostra no dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Neste quickstart vamos implementar o projeto de amostra da [Quickstart: Render um modelo com Unidade.](render-model.md)

Certifique-se de que as suas credenciais são guardadas corretamente com a cena e que pode ligar-se a uma sessão dentro do editor da Unidade.

## <a name="build-the-sample-project"></a>Construir o projeto de amostra

1. Abrir *definições de construção de > de ficheiros*.
1. Alterar *plataforma* para **plataforma universal do Windows**
1. Definir *dispositivo alvo* para **HoloLens**
1. Definir *Arquitetura* para **ARM64**
1. Definir *tipo de construção* para **projeto D3D**\
    ![Configurações de construção](./media/unity-build-settings.png)
1. Selecione **Mudar para plataforma**
1. Ao pressionar **Build** (ou 'Construir e Executar'), será solicitado que selecione alguma pasta onde a solução deve ser armazenada
1. Abra o **gerado Quickstart.sln** com Visual Studio
1. Alterar a configuração para **Release** e **ARM64**
1. Mude o modo de depuração para **Máquina Remota**\
    ![Configuração de solução](media/unity-deploy-config.png)
1. Construir a solução (F7)
1. Para o projeto 'Quickstart', vá à *Properties > Debugging*
    1. Certifique-se de que a configuração *Desbloqueia*
    1. Definir *Debugger para lançar* para máquina **remota**
    1. Alterar *nome da máquina* para o IP dos seus **HoleLens**

## <a name="launch-the-sample-project"></a>Lançar o projeto de amostra

1. Ligue os HoloLens com um cabo USB ao seu PC.
1. Inicie o Debugger no Visual Studio (F5). Irá implantar automaticamente a aplicação no dispositivo.

A aplicação da amostra deve ser lançada e, em seguida, iniciar uma nova sessão. Passado algum tempo, a sessão está pronta e o modelo renderizado remotamente aparecerá à sua frente.
Se quiser lançar a amostra uma segunda vez mais tarde, também pode encontrá-la no menu inicial holoLens agora.

## <a name="next-steps"></a>Passos seguintes

No próximo quickstart, vamos dar uma olhada na conversão de um modelo personalizado.

> [!div class="nextstepaction"]
> [Quickstart: Converter um modelo para renderização](convert-model.md)
