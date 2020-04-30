---
title: Implementar exemplo do Unity no Ambiente de Trabalho
description: Quickstart que mostra como colocar a amostra de Unidade em um PC de ambiente de trabalho
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 95993a54d321c9581d35f12b56d60e4ea052e505
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81415712"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Quickstart: Implementar amostra de unidade para desktop

Este quickstart cobre como implementar e executar a aplicação de amostra sinuosa para a Unidade para um PC de ambiente de trabalho.

Neste arranque rápido, aprenderá a:

> [!div class="checklist"]
>
>* Construa o aplicativo de amostra sintetizador rápido para desktop
>* Desdobrar a amostra para um PC
>* Executar a amostra em um PC

## <a name="prerequisites"></a>Pré-requisitos

Neste arranque rápido vamos implementar o projeto de amostra a partir de [Quickstart: Render um modelo com Unidade](render-model.md).

Certifique-se de que as suas credenciais são guardadas corretamente com a cena e pode ligar-se a uma sessão dentro do editor da Unidade.

## <a name="disable-virtual-reality-support"></a>Desativar o suporte à realidade virtual

Apenas aplicações planas para desktop são suportadas no ambiente de trabalho, pelo que o suporte vr tem de ser desativado.

1. Open *Edit > Project Settings...*
1. Selecione **Jogador** à esquerda.
1. Selecione o separador de definições da **Plataforma Universal do Windows.**
1. Expandir as **Definições XR**.
1. Desativar **a realidade virtual suportada**.
    ![definições de jogador](./media/unity-disable-xr.png)
1. Acima *das definições de XR,* expanda **as Definições de Publicação.**
1. Em **Famílias de Dispositivos Suportados,** **certifique-se** de que o Ambiente de Trabalho é verificado.

## <a name="build-the-sample-project"></a>Construir o projeto da amostra

1. Abrir *> construir definições.*
1. Alterar *plataforma* para **plataforma Universal Windows**.
1. Definir *dispositivo alvo* para **PC**.
1. Definir *arquitetura* para **x86**.
1. Definir *Tipo de construção* para **projeto D3D**.
  ![Construir configurações](./media/unity-build-settings-pc.png)
1. Selecione **Switch para Plataforma**.
1. Ao premir **Build** (ou 'Build And Run'), será-lhe pedido que selecione alguma pasta onde a solução deve ser armazenada.
1. Abra o **gerado Quickstart.sLN** com o Visual Studio.
1. Altere a configuração para **Lançamento** e **x86**.
1. Mude o modo de debugger para **Local Machine**.
  ![Configuração da solução](./media/unity-deploy-config-pc.png)
1. Construir a solução (F7).

> [!WARNING]
> Certifique-se de selecionar **x86**. **x64** não é suportado atualmente, ver [limitações da plataforma](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>Lançar o projeto da amostra

Inicie o Debugger no Estúdio Visual (F5). Irá automaticamente implantar a aplicação para o PC.

A aplicação de amostras deve ser lançada e, em seguida, iniciar uma nova sessão. Passado algum tempo, a sessão está pronta e o modelo de forma remota aparecerá à sua frente.
Se quiser lançar a amostra uma segunda vez mais tarde, também pode encontrá-la a partir do menu Iniciar agora.

## <a name="next-steps"></a>Passos seguintes

No próximo arranque rápido, vamos dar uma olhada na conversão de um modelo personalizado.

> [!div class="nextstepaction"]
> [Quickstart: Converter um modelo para renderização](convert-model.md)
