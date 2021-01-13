---
title: Implementar exemplo do Unity no HoloLens
description: Início rápido que mostra como obter o exemplo do Unity no HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: a5a44b9d27901fcd00f8813096de6bbae5f2310d
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179950"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Início Rápido: Implementar exemplo do Unity no HoloLens

Este início rápido mostra como implementar e executar a aplicação de início rápido de exemplo do Unity num HoloLens 2.

Neste início rápido, vai aprender a:

> [!div class="checklist"]
>
>* Compilar a aplicação de início rápido de exemplo para o HoloLens
>* Implementar o exemplo no dispositivo
>* Executar o exemplo no dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Neste início rápido, vamos implementar o projeto de exemplo de [Início rápido: Compor um modelo com o Unity](render-model.md).

Confirme que as suas credenciais estão corretamente guardadas com a cena e que se pode ligar a uma sessão a partir do editor do Unity.

## <a name="build-the-sample-project"></a>Compilar o projeto de exemplo

1. Abra *File > Build Settings* (Ficheiro > Definições de Criação).
1. Altere *Platform* (Plataforma) para **Universal Windows Platform** (Plataforma Universal do Windows)
1. Defina *Target Device* (Dispositivo de Destino) como **HoloLens**
1. Defina *Architecture* (Arquitetura) como **ARM64**
1. Defina *Build Type* (Tipo de Compilação) como **D3D Project**\ (Projeto D3D)
    ![Definições da compilação](./media/unity-build-settings.png)
1. Selecione **Switch to Platform** (Mudar para Plataforma)
1. Quando premir **Build** (Compilar) ou "Build And Run" ("Compilar e Executar"), é-lhe pedido que selecione uma pasta na qual a solução pode ser armazenada
1. Abra o **Quickstart.sln** gerado com o Visual Studio
1. Altere a configuração para **Release** (Versão) e **ARM64**
1. Mude o modo do depurador para **Remote Machine**\ (Máquina Remota)
    ![Configuração da solução](media/unity-deploy-config.png)
1. Compilar a solução
1. No projeto "Quickstart", aceda a *Properties > Debugging* (Propriedades > Depuração)
    1. Confirme que a configuração *Release* (Versão) está ativa
    1. Defina *Debugger to Launch* (Depurador para Iniciar) como **Remote Machine** (Máquina Remota)
    1. Alterar *nome da máquina* para o IP dos seus **HoloLens**

## <a name="launch-the-sample-project"></a>Iniciar o projeto de exemplo

1. Ligue o HoloLens com um cabo USB ao seu computador.
1. Inicie o Depurador no Visual Studio (F5). A aplicação é implementada automaticamente no dispositivo.

A aplicação de exemplo deverá ser iniciada; em seguida, inicie uma sessão nova. Ao fim de alguns instantes, a sessão fica pronta e o modelo composto remotamente aparece à sua frente.
Se quiser iniciar o exemplo uma segunda vez mais tarde, agora também pode aceder ao mesmo no menu de início do HoloLens.

## <a name="next-steps"></a>Passos seguintes

No próximo início rápido, vamos ver como converter um modelo personalizado.

> [!div class="nextstepaction"]
> [Início Rápido: Converter um modelo para composição](convert-model.md)
