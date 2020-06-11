---
title: Implementar exemplo do Unity no Ambiente de Trabalho
description: Quickstart que mostra como obter a amostra de Unidade em um pc de ambiente de trabalho
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 4e9ddeddf616c581b116d48738e110dd8cdac2d3
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659922"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Quickstart: Implementar a amostra de Unidade para desktop

Este quickstart cobre como implementar e executar a aplicação de amostra de arranque rápido para Unidade para um PC de ambiente de trabalho.

Neste arranque rápido aprenderá a:

> [!div class="checklist"]
>
>* Construa o aplicativo de amostra de arranque rápido para desktop
>* Implementar a amostra num PC
>* Executar a amostra num PC

## <a name="prerequisites"></a>Pré-requisitos

Neste quickstart vamos implementar o projeto de amostra da [Quickstart: Render um modelo com Unidade.](render-model.md)

Certifique-se de que as suas credenciais são guardadas corretamente com a cena e que pode ligar-se a uma sessão dentro do editor da Unidade.

## <a name="disable-virtual-reality-support"></a>Desativar o suporte à realidade virtual

Apenas aplicações de ambiente de trabalho planas são atualmente suportadas no ambiente de trabalho, pelo que o suporte vr tem de ser desativado.

1. Abra *as definições > projeto de edição de edição...*
1. Selecione **Player** à esquerda.
1. Selecione o separador **de definições da Plataforma Universal do Windows.**
1. Expandir as **Definições XR**.
1. Desative **a realidade virtual suportada.**
    ![definições de jogador](./media/unity-disable-xr.png)
1. Acima *de Definições XR*, expandir **configurações de publicação**.
1. Nas **Famílias de Dispositivos Suportados,** **certifique-se de que o ambiente de trabalho** está verificado.

## <a name="build-the-sample-project"></a>Construir o projeto de amostra

1. Abrir *definições de construção de > de ficheiros*.
1. *Alterar plataforma* para plataforma **universal windows** **(PC Standalone** também é suportado mas não usado aqui, ver [limitações da plataforma](../reference/limits.md#platform-limitations)).
1. Definir *o dispositivo-alvo* para **PC**.
1. Definir *Arquitetura* para **x86**.
1. Definir *Tipo de Construção* para **Projeto D3D**.
  ![Configurações de construção](./media/unity-build-settings-pc.png)
1. Selecione **Switch para plataforma**.
1. Ao premir **Build** (ou 'Build And Run'), será solicitado que selecione alguma pasta onde a solução deve ser armazenada.
1. Abra o **Quickstart.sln** gerado com o Visual Studio.
1. Altere a configuração para **Release** e **x86**.
1. Mude o modo de depuração para **Máquina Local**.
  ![Configuração de solução](./media/unity-deploy-config-pc.png)
1. Construa a solução (F7).

> [!WARNING]
> Certifique-se de que seleciona **x86**. **A UWP/x64** não está atualmente suportada, ver [limitações da plataforma](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>Lançar o projeto de amostra

Inicie o Debugger no Visual Studio (F5). Irá implantar automaticamente a aplicação para o PC.

A aplicação da amostra deve ser lançada e, em seguida, iniciar uma nova sessão. Passado algum tempo, a sessão está pronta e o modelo renderizado remotamente aparecerá à sua frente.
Se quiser lançar a amostra uma segunda vez mais tarde, também pode encontrá-la no menu Iniciar agora.

## <a name="next-steps"></a>Próximos passos

No próximo quickstart, vamos dar uma olhada na conversão de um modelo personalizado.

> [!div class="nextstepaction"]
> [Quickstart: Converter um modelo para renderização](convert-model.md)
