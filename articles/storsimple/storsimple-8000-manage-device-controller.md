---
title: Gerir controladores de dispositivo do StorSimple 8000 series | Documentos da Microsoft
description: Saiba como parar, reiniciar, desligar ou repor os controladores de dispositivo do StorSimple.
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
ms.openlocfilehash: 5e461f340e1c58f64c6d645a1e47cfd811bc4de5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505997"
---
# <a name="manage-your-storsimple-device-controllers"></a>Gerir os controladores de dispositivo do StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve as diferentes operações que podem ser executadas em seus controladores de dispositivo do StorSimple. Os controladores de dispositivo StorSimple são os controladores de redundantes (ponto) numa configuração ativa-passiva. Num determinado momento, apenas um controlador está ativo e está a processar todas as operações de disco e rede. É outro controlador num modo passivo. Se o controlador ativo falhar, o controlador passivo é automaticamente ativo.

Este tutorial inclui instruções passo a passo para gerir os controladores de dispositivo com o:

* **Controladores** painel para o seu dispositivo no serviço StorSimple Device Manager.
* Windows PowerShell para StorSimple.

Recomendamos que gerencie os controladores de dispositivo por meio do serviço StorSimple Device Manager. Se uma ação só pode ser efetuada utilizando o Windows PowerShell para StorSimple, o tutorial faz uma nota do mesmo.

Depois de ler este tutorial, será capaz de:

* Reiniciar ou encerrar um controlador de dispositivo do StorSimple
* Encerrar um dispositivo StorSimple
* Repor o dispositivo StorSimple para as predefinições de fábrica

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou encerrar um único controlador
Um reinício do controlador ou encerramento não é necessário como parte da operação de normais do sistema. Operações de desligamento de um controlador de dispositivo único são comuns apenas em casos em que um componente de hardware do dispositivo com falha requer substituição. Também pode ser necessário um reinício de controlador numa situação em que o desempenho é afetado pela utilização de memória excessiva, ou um controlador com funcionamento incorreto. Também poderá reiniciar um controlador após uma substituição de controlador com êxito, se pretender ativar e testar o controlador substituído.

Reiniciar um dispositivo não é perturbador para os iniciadores conectados, partindo do princípio de que o controlador passivo não está disponível. Se um controlador passivo não está disponível ou não desativado, em seguida, reiniciar o controlador ativo pode resultar na interrupção de serviço e o tempo de inatividade.

> [!IMPORTANT]
> * **Nunca deve ser removido fisicamente um controlador em execução como isso poderia resultar numa perda de redundância e um maior risco de períodos de indisponibilidade.**
> * O procedimento seguinte aplica-se apenas para o dispositivo físico StorSimple. Para obter informações sobre como iniciar, parar e reiniciar a StorSimple Cloud Appliance, consulte [trabalhar com a aplicação da cloud](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Pode reiniciar ou encerrar um controlador de dispositivo único através do portal do Azure do serviço StorSimple Device Manager ou do Windows PowerShell para StorSimple.

Para gerir os controladores de dispositivo do portal do Azure, execute os seguintes passos.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Para reiniciar ou encerrar um controlador no portal do Azure
1. No seu serviço StorSimple Device Manager, aceda a **dispositivos**. Selecione o seu dispositivo da lista de dispositivos. 

    ![Escolha um dispositivo](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Aceda a **definições > controladores**.
   
    ![Certifique-se de controladores de dispositivo do StorSimple estão em bom Estados](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Na **controladores** painel, certifique-se de que o estado de ambos os controladores no seu dispositivo é **bom estado de funcionamento**. Selecione um controlador, com o botão direito e, em seguida, selecione **reinicie** ou **Encerrar**.

    ![Selecione reiniciar ou encerrar a controladores de dispositivo do StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. É criada uma tarefa para reiniciar ou encerrar o controlador e é apresentada com avisos aplicável, se aplicável. Para monitorizar o reinício ou encerramento, aceda a **serviço > registos de atividades** e, em seguida, filtrar por parâmetros específicos ao seu serviço. Se um controlador foi encerrado corretamente, terá de enviar o botão de energia para ativar o controlador para ativá-la.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou encerrar um controlador do Windows PowerShell para StorSimple
Execute os seguintes passos para encerrar ou reiniciar um único controlador do seu dispositivo StorSimple a partir do Windows PowerShell para StorSimple.

1. Aceda ao dispositivo através da consola de série ou de uma sessão telnet a partir de um computador remoto. Para ligar ao controlador 0 ou 1 do controlador, siga os passos em [utilizar o PuTTY para ligar a consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
3. Na mensagem de faixa, tome nota do controlador que está ligado à (controlador 0 ou controlador 1) e seja o Active Directory ou o controlador passivo (em espera).
   
   * Para encerrar um único controlador, na linha de comandos, escreva:
     
       `Stop-HcsController`
     
       Isso encerra o controlador que está ligado. Se parar o controlador ativo, em seguida, o dispositivo não consegue sobre o controlador passivo.

   * Para reiniciar um controlador, na linha de comandos, escreva:
     
       `Restart-HcsController`
     
       Isto reinicia o controlador que está ligado à. Se reiniciar o controlador ativo, ele executa failover para o controlador passivo antes da reinicialização.

## <a name="shut-down-a-storsimple-device"></a>Encerrar um dispositivo StorSimple

Esta secção explica como encerrar uma em execução ou um dispositivo StorSimple com falha de um computador remoto. Um dispositivo está a ser desativado depois dos controladores de dispositivo são encerrados. Um encerramento de dispositivo é feito quando o dispositivo for movido fisicamente ou está a ser utilizado fora de serviço.

> [!IMPORTANT]
> Antes de encerrar o dispositivo, verifique o estado de funcionamento dos componentes do dispositivo. Navegue para o seu dispositivo e, em seguida, clique em **definições > Estado de funcionamento do Hardware**. Na **estado de funcionamento do Estado e o hardware** painel, certifique-se de que o estado de LED de todos os componentes é verde. Apenas um dispositivo em bom estado de funcionamento tem o estado verde. Se o seu dispositivo está a ser desligado para substituir um componente com funcionamento incorreto, verá um falha (vermelho) ou um Estado degradado (amarelo) para os respetivos componentes.


#### <a name="to-shut-down-a-storsimple-device"></a>Para encerrar um dispositivo StorSimple

1. Utilize o [reiniciar ou encerrar um controlador](#restart-or-shut-down-a-single-controller) procedimento para identificar e encerrar o controlador passivo no seu dispositivo. Pode efetuar esta operação no portal do Azure ou no Windows PowerShell para StorSimple.
2. Repita o passo anterior para encerrar o controlador ativo.
3. Agora precisa considerar o plano de fundo do dispositivo. Depois dos dois controladores são totalmente encerrados, o estado LEDs em ambos os controladores deve ser intermitente vermelho. Se precisar de desativar completamente a dispositivo neste momento, inverta as opções de energia em energia e refrigeração módulos (PCMs) para a posição de OFF. Isso deve desativar o dispositivo.

## <a name="reset-the-device-to-factory-default-settings"></a>Repor o dispositivo para as predefinições de fábrica

> [!IMPORTANT]
> Se precisar de repor o dispositivo para as predefinições de fábrica, contacte o Support da Microsoft. O procedimento descrito abaixo deve ser usado apenas em conjunto com Support da Microsoft.

Este procedimento descreve como repor o dispositivo do Microsoft Azure StorSimple para as predefinições de fábrica com o Windows PowerShell para StorSimple.
Repor um dispositivo remove todos os dados e definições de todo o cluster por predefinição.

Execute os seguintes passos para repor o dispositivo do Microsoft Azure StorSimple para as predefinições de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para repor o dispositivo às predefinições no Windows PowerShell para StorSimple
1. Aceda ao dispositivo por meio de sua consola de série. Verifique a mensagem de faixa para se certificar de que está ligado para o **Active Directory** controlador.
2. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
3. Na linha de comandos, escreva o seguinte comando para repor a todo o cluster, remover todas as definições de dados, metadados e controlador:
   
    `Reset-HcsFactoryDefault`
   
    Para repor a um único controlador em vez disso, utilize o [reposição-HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) cmdlet com o `-scope` parâmetro.)
   
    O sistema será reiniciado várias vezes. Será notificado quando a reposição foi concluída com êxito. Consoante o modelo de sistema, pode demorar 45 a 60 minutos para um dispositivo 8100 e 60 a 90 minutos para um 8600 concluir este processo.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre a gestão de controladores de dispositivo
Nesta secção, resumimos algumas das perguntas mais frequentes relativas ao gerir controladores de dispositivo do StorSimple.

**P.** O que acontece se ambos os controladores no meu dispositivo estão em bom estado e ativados no e eu reiniciar ou encerrar o controlador ativo?

**R.** Se ambos os controladores no seu dispositivo são ativados e em bom estado, lhe for pedida confirmação. Pode optar por:

* **Reiniciar o controlador ativo** – são notificados de que a reiniciar um controlador ativo causou o dispositivo para a ativação pós-falha para o controlador passivo. O reinício do controlador.
* **Encerrar um controlador ativo** – são notificados de que um controlador ativo a ser encerrado resulta em tempo de inatividade. Também terá de enviar o botão de energia no dispositivo para ativar o controlador.

**P.** O que acontece se o controlador passivo no meu dispositivo estiver indisponível ou não desativado e eu reiniciar ou encerrar o controlador ativo?

**R.** Se o controlador passivo no seu dispositivo está indisponível ou não desativado e optar por:

* **Reiniciar o controlador ativo** – será notificado que continuar a operação resulta numa interrupção temporária do serviço e lhe for pedida a confirmação.
* **Encerrar um controlador ativo** – será notificado que continuar a operação resulta em tempo de inatividade. Também terá de enviar o botão de energia num ou ambos os controladores para ativar o dispositivo. For pedida a confirmação.

**P.** Quando faz a reinício do controlador ou o encerramento não progride?

**R.** Reiniciar ou encerrar um controlador poderá falhar se:

* Uma atualização do dispositivo está em curso.
* Já está em curso um reinício do controlador.
* Um encerramento do controlador já está em curso.

**P.** Como descobrir se um controlador foi reiniciado ou encerrado?

**R.** Pode verificar o estado do controlador no painel de controlador. O estado do controlador indica se um controlador está no processo de reiniciar ou encerrar. Além disso, o **alertas** painel contêm um alerta informativo, se o controlador é reiniciado ou encerrado. As operações de reinício e encerramento do controlador também são registadas nos registos de atividade. Para obter mais informações sobre os registos de atividade, aceda a [ver os registos de atividade](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**P.** Existe qualquer impacto para a e/s em decorrência de ativação pós-falha de controlador?

**R.** As conexões TCP entre iniciadores e o controlador ativo serão repostas como resultado de ativação pós-falha de controlador, mas são restabelecidas quando o controlador passivo pressupõe que a operação. Pode haver uma pausa de temporária (menos de 30 segundos) na atividade de e/s entre iniciadores e o dispositivo no decorrer desta operação.

**P.** Como posso retornar meu controlador de serviço depois de serem encerrado e removida?

**R.** Para devolver um controlador de serviço, tem de inseri-lo para os chassis conforme descrito em [substituir um módulo de controlador do seu dispositivo StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Passos Seguintes
* Se encontrar algum problema com os controladores de dispositivo do StorSimple que não é possível resolver através dos procedimentos listados neste tutorial, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Para saber mais sobre como utilizar o serviço StorSimple Device Manager, aceda à [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

