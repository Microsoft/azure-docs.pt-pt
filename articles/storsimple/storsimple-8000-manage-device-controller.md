---
title: Gerir controladores de dispositivos da série StorSimple 8000 [ StorSimple 8000] Microsoft Docs
description: Aprenda a parar, reiniciar, desligar ou redefinir os controladores do dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: ce49dcaa06288ba9e7a4d232338c727064d59685
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366794"
---
# <a name="manage-your-storsimple-device-controllers"></a>Gerencie os seus controladores de dispositivoS StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve as diferentes operações que podem ser realizadas nos seus controladores de dispositivoS StorSimple. Os controladores do seu dispositivo StorSimple são controladores redundantes (peer) numa configuração activa-passiva. Num dado momento, apenas um controlador está ativo e está a processar todas as operações de disco e rede. O outro controlador está em modo passivo. Se o controlador ativo falhar, o controlador passivo torna-se ativa automaticamente.

Este tutorial inclui instruções passo a passo para gerir os controladores do dispositivo utilizando o seguinte:

* **Lâmina de controlador** para o seu dispositivo no serviço StorSimple Device Manager.
* Windows PowerShell para StorSimple.

Recomendamos que gere os controladores do dispositivo através do serviço StorSimple Device Manager. Se uma ação só puder ser realizada utilizando o Windows PowerShell para o StorSimple, o tutorial toma nota da sua.

Depois de ler este tutorial, poderá:

* Reiniciar ou desligar um controlador de dispositivo StorSimple
* Desligue um dispositivo StorSimple
* Redefinir o seu dispositivo StorSimple para os incumprimentos da fábrica

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou desligar um único controlador
Não é necessário reiniciar ou desligar um controlador como parte do funcionamento normal do sistema. As operações de paragem de um único controlador de dispositivos são comuns apenas nos casos em que um componente de hardware do dispositivo falhado requer substituição. Pode também ser necessário um reinício do controlador numa situação em que o desempenho é afetado pelo uso excessivo da memória ou por um controlador avariado. Também pode ser necessário reiniciar um controlador após uma substituição bem sucedida do controlador, se desejar ativar e testar o controlador substituído.

A reinicialização do dispositivo não interrompe os iniciadores ligados, assumindo que o controlador passivo está disponível. Se um controlador passivo não estiver disponível ou estiver desligado, reiniciar o controlador ativo poderá resultar no interrupção do serviço e em tempo de inatividade.

> [!IMPORTANT]
> * **Um controlador de corrida nunca deve ser fisicamente removido, uma vez que isso resultaria numa perda de redundância e num risco acrescido de inatividade.**
> * O procedimento seguinte aplica-se apenas ao dispositivo físico StorSimple. Para obter informações sobre como iniciar, parar e reiniciar o StorSimple Cloud Appliance, consulte [Trabalhar com o aparelho em nuvem](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Pode reiniciar ou desligar um único controlador de dispositivoatravés do portal Azure do serviço StorSimple Device Manager ou do Windows PowerShell para o StorSimple.

Para gerir os controladores do seu dispositivo a partir do portal Azure, execute os seguintes passos.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Para reiniciar ou desligar um controlador no portal Azure
1. No seu serviço StorSimple Device Manager, vá a **Dispositivos**. Selecione o seu dispositivo a partir da lista de dispositivos. 

    ![Escolha um dispositivo](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Ir a **Definições > Controladores**.
   
    ![Verifique se os controladores de dispositivoS StorSimple são saudáveis](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Na lâmina dos **Controladores,** verifique se o estado dos dois controladores no seu dispositivo é **saudável**. Selecione um controlador, clique à direita e, em seguida, selecione **Reiniciar** ou **desligar**.

    ![Selecione reiniciar ou desligar controladores de dispositivoS StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. É criado um trabalho para reiniciar ou desligar o controlador e é-lhe apresentado um aviso aplicável, se houver. Para monitorizar o reinício ou encerramento, aceda aos **registos de Atividade saca-se** e, em seguida, filtre por parâmetros específicos do seu serviço. Se um controlador foi desligado, terá de carregar no botão de alimentação para ligar o controlador para o ligar.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou desligar um controlador no Windows PowerShell para storSimple
Execute as seguintes etapas para desligar ou reiniciar um único controlador no seu dispositivo StorSimple a partir do Windows PowerShell para StorSimple.

1. Aceda ao dispositivo através da consola em série ou de uma sessão de telnet a partir de um computador remoto. Para ligar ao Controlador 0 ou ao Controlador 1, siga os passos em [Use PuTTY para se ligar à consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu de consola em série, escolha a opção 1, **inicie sessão com acesso total**.
3. Na mensagem de banner, tome nota do controlador a que está ligado (Controlador 0 ou Controlador 1) e se é o controlador ativo ou passivo (standby).
   
   * Para desligar um único controlador, a pedido, tipo:
     
       `Stop-HcsController`
     
       Isto desliga o controlador a que está ligado. Se parar o controlador ativo, o dispositivo falha no controlador passivo.

   * Para reiniciar um controlador, à mão, escreva:
     
       `Restart-HcsController`
     
       Este procedimento reinicia o controlador ao qual está ligado. Se reiniciar o controlador ativo, falha no controlador passivo antes do reinício.

## <a name="shut-down-a-storsimple-device"></a>Desligue um dispositivo StorSimple

Esta secção explica como desligar um dispositivo StorSimple avariado a partir de um computador remoto. Um dispositivo é desligado depois de ambos os controladores do dispositivo estarem desligados. Uma paragem do dispositivo é feita quando o dispositivo é fisicamente deslocado, ou é retirado de serviço.

> [!IMPORTANT]
> Antes de desligar o dispositivo, verifique a saúde dos componentes do dispositivo. Navegue para o seu dispositivo e, em seguida, clique em **Definições > Saúde de Hardware**. Na lâmina de **saúde status e hardware,** verifique se o estado LED de todos os componentes é verde. Apenas um dispositivo saudável tem um estatuto verde. Se o seu dispositivo estiver a ser desligado para substituir um componente avariado, verá um estado avariado (vermelho) ou um estado (amarelo) degradado para os respetivos componentes.


#### <a name="to-shut-down-a-storsimple-device"></a>Para desligar um dispositivo StorSimple

1. Utilize o [reinício ou desligue um](#restart-or-shut-down-a-single-controller) procedimento do controlador para identificar e desligar o controlador passivo do seu dispositivo. Pode executar esta operação no portal Azure ou no Windows PowerShell para o StorSimple.
2. Repita o passo acima para desligar o controlador ativo.
3. Agora tens de olhar para o plano de trás do dispositivo. Depois de os dois controladores estarem completamente desligados, os LEDs de estado em ambos os controladores devem piscar de vermelho. Se precisar de desligar completamente o dispositivo neste momento, ligue os interruptores de alimentação e de arrefecimento (PCMs) para a posição OFF. Isto deve desligar o dispositivo.

## <a name="reset-the-device-to-factory-default-settings"></a>Redefinir o dispositivo para as definições predefinidas da fábrica

> [!IMPORTANT]
> Se precisar de redefinir o seu dispositivo para as definições predefinidas da fábrica, contacte o Microsoft Support. O procedimento descrito a seguir deve ser utilizado apenas em conjunto com o Suporte do Microsoft.

Este procedimento descreve como redefinir o seu dispositivo Microsoft Azure StorSimple para configurações predefinidas de fábrica utilizando o Windows PowerShell para o StorSimple.
A reposição de um dispositivo remove todos os dados e definições de todo o cluster por padrão.

Execute os seguintes passos para redefinir o seu dispositivo Microsoft Azure StorSimple para as definições predefinidas da fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para redefinir o dispositivo para definições predefinidas no Windows PowerShell para StorSimple
1. Aceda ao dispositivo através da sua consola em série. Verifique a mensagem do banner para se certificar de que está ligado ao controlador **Ative.**
2. No menu de consola em série, escolha a opção 1, **inicie sessão com acesso total**.
3. A pedido, digite o seguinte comando para redefinir todo o cluster, removendo todos os dados, metadados e definições do controlador:
   
    `Reset-HcsFactoryDefault`
   
    Para, em vez disso, redefinir um único controlador, utilize o cmdlet [Reset-HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) com o parâmetro `-scope`.)
   
    O sistema reiniciará várias vezes. Será notificado quando o reset tiver terminado com sucesso. Dependendo do modelo do sistema, pode levar 45-60 minutos para um dispositivo 8100 e 60-90 minutos para um 8600 terminar este processo.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre a gestão dos controladores de dispositivos
Nesta secção, resumimos algumas das perguntas frequentes sobre a gestão dos controladores de dispositivos StorSimple.

**P.** O que acontece se ambos os controladores do meu dispositivo estiverem saudáveis e ligados e eu reiniciar ou desligar o controlador ativo?

**R.** Se ambos os controladores do seu dispositivo estiverem saudáveis e ligados, é solicitado para confirmação. Pode optar por:

* **Reiniciar o controlador ativo** – É notificado de que reiniciar um controlador ativo fez com que o dispositivo falhasse no controlador passivo. O controlador reinicia.
* **Desligue um controlador ativo** – É notificado de que desligar um controlador ativo resulta em tempo de inatividade. Também é necessário premir o botão de alimentação do dispositivo para ligar o controlador.

**P.** O que acontece se o controlador passivo do meu dispositivo não estiver disponível ou desligado e eu reiniciar ou desligar o controlador ativo?

**R.** Se o controlador passivo do seu dispositivo não estiver disponível ou desligado, e optar por:

* **Reiniciar o controlador ativo** – É notificado de que a continuação da operação resultará numa interrupção temporária do serviço, e é solicitado para confirmação.
* **Desligue um controlador ativo** – É notificado de que a continuação da operação resulta em tempo de inatividade. Também é necessário premir o botão de alimentação de um ou ambos os controladores para ligar o dispositivo. Foi solicitado para confirmação.

**P.** Quando é que o controlador reinicia ou desliga?

**R.** Reiniciar ou desligar um controlador pode falhar se:

* Está em curso uma atualização do dispositivo.
* Um reinício do controlador já está em andamento.
* Uma paragem do controlador já está em andamento.

**P.** Como pode descobrir se um controlador foi reiniciado ou desligado?

**R.** Pode verificar o estado do controlador na lâmina do controlador. O estado do controlador indicará se um controlador está em processo de reiniciar ou desligar. Além disso, a lâmina **alerta** contém um alerta informativo se o controlador for reiniciado ou desligado. As operações de reinício e de paragem do controlador também são registadas nos registos de atividade. Para mais informações sobre os registos de atividade, vá ver [os registos de atividade](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**P.** Existe algum impacto no I/O como resultado da falha do controlador?

**R.** As ligações TCP entre iniciadores e controlador ativo serão recriadas em resultado da falha do controlador, mas serão restabelecidas quando o controlador passivo assumir a operação. Pode haver uma pausa temporária (menos de 30 segundos) na atividade de E/S entre os iniciadores e o dispositivo durante esta operação.

**P.** Como posso devolver o meu controlador ao serviço depois de ter sido desligado e removido?

**R.** Para devolver um controlador ao serviço, deve inseri-lo no chassis conforme descrito em [Substituir um módulo de controlador no seu dispositivo StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Passos seguintes
* Se encontrar algum problema com os controladores do dispositivo StorSimple que não possa resolver utilizando os procedimentos listados neste tutorial, [contacte](storsimple-8000-contact-microsoft-support.md)o Microsoft Support .
* Para saber mais sobre a utilização do serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

