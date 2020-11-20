---
title: Gerir controladores de dispositivos da série StorSimple 8000 Microsoft Docs
description: Aprenda a parar, reiniciar, desligar ou redefinir os controladores de dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956998"
---
# <a name="manage-your-storsimple-device-controllers"></a>Gerir os seus controladores de dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve as diferentes operações que podem ser realizadas nos seus controladores de dispositivo StorSimple. Os controladores do seu dispositivo StorSimple são controladores redundantes (pares) numa configuração activa-passiva. Num dado momento, apenas um controlador está ativo e está a processar todas as operações de disco e rede. O outro controlador está em modo passivo. Se o controlador ativo falhar, o controlador passivo torna-se automaticamente ativo.

Este tutorial inclui instruções passo a passo para gerir os controladores do dispositivo utilizando o seguinte:

* **Lâmina de controlador** para o seu dispositivo no serviço StorSimple Device Manager.
* Windows PowerShell para StorSimple.

Recomendamos que gere os controladores do dispositivo através do serviço StorSimple Device Manager. Se uma ação só puder ser executada utilizando o Windows PowerShell para StorSimple, o tutorial toma nota do mesmo.

Depois de ler este tutorial, poderá:

* Reiniciar ou desligar um controlador de dispositivo StorSimple
* Desligue um dispositivo StorSimple
* Reinicie o seu dispositivo StorSimple para padrão de fábrica

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou desligar um único controlador
Um comando reinicia ou desliga o sistema como parte do funcionamento normal do sistema. As operações de paragem de um único controlador de dispositivos só são comuns nos casos em que um componente de hardware do dispositivo falhado requer substituição. Um reinício do controlador também pode ser necessário numa situação em que o desempenho seja afetado por uma utilização excessiva da memória ou por um controlador avariado. Também pode ter de reiniciar um controlador após uma substituição bem sucedida do controlador, se desejar ativar e testar o controlador substituído.

A reinicialização do dispositivo não interrompe os iniciadores ligados, assumindo que o controlador passivo está disponível. Se um controlador passivo não estiver disponível ou estiver desligado, reiniciar o controlador ativo poderá resultar no interrupção do serviço e em tempo de inatividade.

> [!IMPORTANT]
> * **Um controlador de funcionamento nunca deve ser fisicamente removido, uma vez que tal resultaria numa perda de redundância e num risco acrescido de inatividade.**
> * O procedimento a seguir aplica-se apenas ao dispositivo físico StorSimple. Para obter informações sobre como iniciar, parar e reiniciar o Aparelho StorSimple Cloud, consulte [Trabalhar com o aparelho em nuvem](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Pode reiniciar ou desligar um único controlador de dispositivos através do portal Azure do serviço StorSimple Device Manager ou do Windows PowerShell para storSimple.

Para gerir os controladores do dispositivo a partir do portal Azure, execute os seguintes passos.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Reiniciar ou desligar um controlador no portal Azure
1. No seu serviço StorSimple Device Manager, aceda aos **Dispositivos**. Selecione o seu dispositivo na lista de dispositivos. 

    ![Escolha um dispositivo](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Ir para **Definições > Controladores**.
   
    ![Verifique se os controladores de dispositivo storSimple estão saudáveis](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Na lâmina **dos controladores,** verifique se o estado de ambos os controladores do seu dispositivo é **saudável**. Selecione um controlador, clique à direita e, em seguida, **selecione Reiniciar** ou **desligar**.

    ![Selecione reiniciar ou desligar controladores de dispositivo StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. É criado um trabalho para reiniciar ou desligar o controlador e é-lhe apresentado avisos aplicáveis, caso exista. Para monitorizar o reinício ou paragem, aceda aos **registos de atividade de Serviço >** e, em seguida, filtre por parâmetros específicos do seu serviço. Se um controlador foi desligado, então terá de carregar no botão de alimentação para ligar o controlador para o ligar.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Reiniciar ou desligar um controlador no Windows PowerShell para storSimple
Execute os seguintes passos para desligar ou reiniciar um único controlador no seu dispositivo StorSimple a partir do Windows PowerShell para StorSimple.

1. Aceda ao dispositivo através da consola em série ou de uma sessão de telnet a partir de um computador remoto. Para ligar ao Controlador 0 ou ao Controlador 1, siga os passos em [Use PuTTY para ligar à consola em série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu de consola em série, escolha a opção 1, **faça login com acesso total**.
3. Na mensagem de banner, tome nota do controlador a que está ligado (Controlador 0 ou Controlador 1) e se é o controlador ativo ou passivo (standby).
   
   * Para desligar um único controlador, na origem, escreva:
     
       `Stop-HcsController`
     
       Isto desliga o controlador a que está ligado. Se parar o controlador ativo, o dispositivo falha no controlador passivo.

   * Para reiniciar um controlador, na origem, escreva:
     
       `Restart-HcsController`
     
       Este procedimento reinicia o controlador ao qual está ligado. Se reiniciar o controlador ativo, este falha no controlador passivo antes do reinício.

## <a name="shut-down-a-storsimple-device"></a>Desligue um dispositivo StorSimple

Esta secção explica como desligar um dispositivo StorSimple em funcionamento ou um dispositivo StorSimple falhado a partir de um computador remoto. Um dispositivo é desligado depois de ambos os controladores do dispositivo serem desligados. Um desligar do dispositivo é feito quando o dispositivo é movido fisicamente ou é retirado de serviço.

> [!IMPORTANT]
> Antes de desligar o aparelho, verifique a saúde dos componentes do dispositivo. Navegue para o seu dispositivo e, em seguida, clique em **Definições > saúde do hardware**. Na lâmina **de saúde status e hardware,** verifique se o estado do LED de todos os componentes é verde. Só um dispositivo saudável tem um estado verde. Se o seu dispositivo estiver a ser desligado para substituir um componente avariado, verá um estado de avaria (vermelho) ou um estado (amarelo) degradado para os respetivos componentes.


#### <a name="to-shut-down-a-storsimple-device"></a>Para desligar um dispositivo StorSimple

1. Utilize o [reinício ou desligue um](#restart-or-shut-down-a-single-controller) procedimento do controlador para identificar e desligar o controlador passivo do seu dispositivo. Pode efetuar esta operação no portal Azure ou no Windows PowerShell para StorSimple.
2. Repita o passo acima para desligar o controlador ativo.
3. Agora tens de olhar para o plano de trás do dispositivo. Depois de os dois controladores estarem completamente desligados, os LEDs de estado em ambos os comandos devem piscar a vermelho. Se necessitar de desligar completamente o dispositivo neste momento, ligue os interruptores de alimentação tanto dos Módulos de Alimentação como dos Módulos de Arrefecimento (PCMs) para a posição OFF. Isto deve desligar o dispositivo.

## <a name="reset-the-device-to-factory-default-settings"></a>Reset the device to factory default settings (Repor o dispositivo para as predefinições de fábrica)

> [!IMPORTANT]
> Se necessitar de reiniciar o seu dispositivo para as definições predefinidos da fábrica, contacte o Microsoft Support. O procedimento descrito abaixo deve ser utilizado apenas em conjunto com o Microsoft Support.

Este procedimento descreve como redefinir o seu dispositivo Microsoft Azure StorSimple para definições padrão de fábrica utilizando o Windows PowerShell para StorSimple.
A reposição de um dispositivo remove todos os dados e configurações de todo o cluster por predefinição.

Execute os seguintes passos para redefinir o seu dispositivo Microsoft Azure StorSimple para definições padrão de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para redefinir as definições padrão do Windows PowerShell para StorSimple
1. Aceda ao dispositivo através da sua consola em série. Verifique a mensagem de banner para se certificar de que está ligado ao controlador **Ative.**
2. No menu de consola em série, escolha a opção 1, **faça login com acesso total**.
3. Na origem, digite o seguinte comando para redefinir todo o cluster, removendo todos os dados, metadados e definições do controlador:
   
    `Reset-HcsFactoryDefault`
   
    Em vez disso, para redefinir um único controlador, utilize o  [cmdlet Reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) com o `-scope` parâmetro.)
   
    O sistema reiniciará várias vezes. Será notificado quando o reset tiver sido concluído com sucesso. Dependendo do modelo do sistema, pode levar 45-60 minutos para um dispositivo 8100 e 60-90 minutos para um 8600 terminar este processo.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre a gestão dos controladores de dispositivos
Nesta secção, resumimos algumas das perguntas frequentes sobre a gestão dos controladores de dispositivos StorSimple.

**Q.** O que acontece se ambos os controladores do meu dispositivo estiverem saudáveis e ligados e eu reiniciar ou desligar o controlador ativo?

**A.** Se ambos os controladores do seu dispositivo estiverem saudáveis e ligados, é-lhe solicitada a confirmação. Pode optar por:

* **Reinicie o controlador ativo** – É-lhe notificado que o reinício de um controlador ativo fez com que o dispositivo falhasse no controlador passivo. O controlador reinicia.
* **Desligue um controlador ativo** – É-lhe notificado que desligar um controlador ativo resulta em tempo de inativo. Também é necessário premir o botão de alimentação do dispositivo para ligar o controlador.

**Q.** O que acontece se o controlador passivo do meu dispositivo estiver indisponível ou desligado e eu reiniciar ou desligar o controlador ativo?

**A.** Se o controlador passivo do seu dispositivo não estiver disponível ou desligado, e optar por:

* **Reinicie o controlador ativo** – É-lhe comunicado que a continuação da operação resultará numa interrupção temporária do serviço, e é solicitado a confirmação.
* **Desligue um controlador ativo** – É-lhe notificado que a continuação da operação resulta em tempo de inatividade. Também é necessário premir o botão de alimentação de um ou ambos os controladores para ligar o dispositivo. Foi solicitado para confirmação.

**Q.** Quando é que o controlador reinicia ou desliga?

**A.** Reiniciar ou desligar um controlador pode falhar se:

* Uma atualização do dispositivo está em andamento.
* Um reinício do controlador já está em andamento.
* Uma paragem do controlador já está em andamento.

**Q.** Como pode descobrir se um controlador foi reiniciado ou desligado?

**A.** Pode verificar o estado do controlador na lâmina do controlador. O estado do controlador indicará se um controlador está em processo de reiniciar ou desligar. Além disso, a lâmina **alertas** contém um alerta informativo se o controlador for reiniciado ou desligado. As operações de reinício e de encerramento do controlador também são registadas nos registos de atividade. Para obter mais informações sobre registos de atividades, consulte [os registos de atividade .](storsimple-8000-service-dashboard.md#view-the-activity-logs)

**Q.** Existe algum impacto no I/S como resultado do falha do controlador?

**A.** As ligações TCP entre iniciadores e controlador ativo serão reiniciadas em resultado da falha do controlador, mas serão restabelecidas quando o controlador passivo assumir o funcionamento. Pode haver uma pausa temporária (menos de 30 segundos) na atividade de E/S entre os iniciadores e o dispositivo durante esta operação.

**Q.** Como posso devolver o meu controlador ao serviço depois de ter sido desligado e removido?

**A.** Para devolver um controlador ao serviço, deve inseri-lo no chassis descrito no [Substituto de um módulo controlador no seu dispositivo StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Passos seguintes
* Se encontrar algum problema com os seus controladores de dispositivo StorSimple que não possa resolver utilizando os procedimentos listados neste tutorial, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Para saber mais sobre a utilização do serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).