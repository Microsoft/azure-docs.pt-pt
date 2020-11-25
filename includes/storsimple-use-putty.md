---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995438"
---
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

