---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 50a5c8d515e27db7c2c65b484cdecad8ff00baf8
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165914"
---
<!--author=SharS last changed: 9/17/15-->

#### <a name="to-connect-through-the-serial-console"></a>Para ligar através da consola de série
1. Ligue o cabo de série ao dispositivo (diretamente ou através de um adaptador USB de série).
2. Abra o **Painel de Controlo** e, em seguida, abra o **Gestor de Dispositivos**.
3. Identifique a porta COM, como mostrado na ilustração seguinte.
   
     ![Ligar através da consola de série](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Inicie o PuTTY. 
5. No painel da direita, altere o **Tipo de ligação** para **Série**.
6. No painel da direita, escreva a porta COM correta. Verifique se os parâmetros de configuração de série estão definidos do seguinte modo:
   
   * Velocidade: 115.200
   * Bits de dados: 8
   * Bits de paragem: 1
   * Paridade: Nenhuma
   * Fluxo de controlo: Nenhum
     
     Estas definições são apresentadas na ilustração seguinte.
     
     ![Definições do PuTTY](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Se o controlo de fluxo predefinido não funcionar, experimente defini-lo como XON/XOFF
     > 
     > 
7. Clique em **Abrir** para iniciar uma sessão de série.

