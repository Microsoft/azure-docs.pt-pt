---
title: Gerenciar controladores de dispositivo do StorSimple 8000 Series | Microsoft Docs
description: Saiba como parar, reiniciar, desligar ou redefinir os controladores de dispositivo StorSimple.
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
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894834"
---
# <a name="manage-your-storsimple-device-controllers"></a>Gerenciar seus controladores de dispositivo StorSimple

## <a name="overview"></a>Visão geral

Este tutorial descreve as diferentes operações que podem ser executadas em seus controladores de dispositivo StorSimple. Os controladores em seu dispositivo StorSimple são controladores redundantes (pares) em uma configuração ativa-passiva. Em um determinado momento, apenas um controlador está ativo e está processando todas as operações de disco e rede. O outro controlador está em um modo passivo. Se o controlador ativo falhar, o controlador passivo se tornará ativo automaticamente.

Este tutorial inclui instruções passo a passo para gerenciar os controladores de dispositivo usando o:

* A folha **controladores** para seu dispositivo no serviço StorSimple Device Manager.
* Windows PowerShell para StorSimple.

Recomendamos que você gerencie os controladores de dispositivo por meio do serviço StorSimple Device Manager. Se uma ação só puder ser executada usando Windows PowerShell para StorSimple, o tutorial fará uma anotação.

Depois de ler este tutorial, você poderá:

* Reiniciar ou desligar um controlador de dispositivo StorSimple
* Desligar um dispositivo StorSimple
* Redefinir o dispositivo StorSimple para os padrões de fábrica

## <a name="restart-or-shut-down-a-single-controller"></a>Reiniciar ou desligar um único controlador
Uma reinicialização ou um desligamento do controlador não é necessário como parte da operação normal do sistema. As operações de desligamento para um único controlador de dispositivo são comuns apenas em casos em que um componente de hardware de dispositivo com falha requer substituição. Uma reinicialização do controlador também pode ser necessária em uma situação em que o desempenho é afetado pelo uso excessivo de memória ou por um controlador com defeito. Talvez você também precise reiniciar um controlador após uma substituição bem-sucedida do controlador, se desejar habilitar e testar o controlador substituído.

A reinicialização do dispositivo não interrompe os iniciadores ligados, assumindo que o controlador passivo está disponível. Se um controlador passivo não estiver disponível ou estiver desligado, reiniciar o controlador ativo poderá resultar no interrupção do serviço e em tempo de inatividade.

> [!IMPORTANT]
> * **Um controlador em execução nunca deve ser fisicamente removido, pois isso resultaria em uma perda de redundância e um maior risco de tempo de inatividade.**
> * O procedimento a seguir aplica-se somente ao dispositivo físico StorSimple. Para obter informações sobre como iniciar, parar e reiniciar o dispositivo de nuvem StorSimple, consulte [trabalhar com o dispositivo de nuvem](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Você pode reiniciar ou desligar um único controlador de dispositivo por meio da portal do Azure do serviço Device Manager do StorSimple ou Windows PowerShell para StorSimple.

Para gerenciar os controladores de dispositivo do portal do Azure, execute as etapas a seguir.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Para reiniciar ou desligar um controlador no portal do Azure
1. Em seu serviço de Device Manager do StorSimple, vá para **dispositivos**. Selecione seu dispositivo na lista de dispositivos. 

    ![Escolha um dispositivo](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Vá para **configurações > controladores**.
   
    ![Verificar se os controladores de dispositivo StorSimple estão íntegros](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Na folha **controladores** , verifique se o status de ambos os controladores em seu dispositivo está **íntegro**. Selecione um controlador, clique com o botão direito do mouse e selecione **reiniciar** ou **desligar**.

    ![Selecione reiniciar ou desligar controladores de dispositivo StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Um trabalho é criado para reiniciar ou desligar o controlador e você verá os avisos aplicáveis, se houver. Para monitorar a reinicialização ou o desligamento, acesse **serviço > logs de atividade** e, em seguida, filtre por parâmetros específicos ao seu serviço. Se um controlador foi desligado, será necessário pressionar o botão de energia para ligar o controlador para ativá-lo.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Para reiniciar ou desligar um controlador no Windows PowerShell para StorSimple
Execute as etapas a seguir para desligar ou reiniciar um único controlador em seu dispositivo StorSimple do Windows PowerShell para StorSimple.

1. Acesse o dispositivo por meio do console serial ou de uma sessão Telnet de um computador remoto. Para se conectar ao controlador 0 ou controlador 1, siga as etapas em usar a saída [para se conectar ao console serial do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. No menu do console serial, escolha a opção 1, **faça logon com acesso completo**.
3. Na mensagem de faixa, anote o controlador ao qual você está conectado (controlador 0 ou controlador 1) e se ele é o controlador ativo ou passivo (em espera).
   
   * Para desligar um único controlador, no prompt, digite:
     
       `Stop-HcsController`
     
       Isso desliga o controlador ao qual você está conectado. Se você parar o controlador ativo, o dispositivo passará por failover para o controlador passivo.

   * Para reiniciar um controlador, no prompt, digite:
     
       `Restart-HcsController`
     
       Este procedimento reinicia o controlador ao qual está ligado. Se você reiniciar o controlador ativo, ele faz failover para o controlador passivo antes da reinicialização.

## <a name="shut-down-a-storsimple-device"></a>Desligar um dispositivo StorSimple

Esta seção explica como desligar um dispositivo StorSimple em execução ou com falha de um computador remoto. Um dispositivo é desligado depois que ambos os controladores de dispositivo são desligados. Um desligamento de dispositivo é feito quando o dispositivo é movido fisicamente ou é retirado do serviço.

> [!IMPORTANT]
> Antes de desligar o dispositivo, verifique a integridade dos componentes do dispositivo. Navegue até o dispositivo e clique em **configurações > integridade do hardware**. Na folha **status e integridade do hardware** , verifique se o status do LED de todos os componentes está verde. Somente um dispositivo íntegro tem um status verde. Se o dispositivo estiver sendo desligado para substituir um componente com problemas de funcionamento, você verá um status de falha (vermelho) ou degradado (amarelo) para os respectivos componentes.


#### <a name="to-shut-down-a-storsimple-device"></a>Para desligar um dispositivo StorSimple

1. Use o procedimento de [reinicializar ou desligar um controlador](#restart-or-shut-down-a-single-controller) para identificar e desligar o controlador passivo em seu dispositivo. Você pode executar essa operação no portal do Azure ou no Windows PowerShell para StorSimple.
2. Repita a etapa acima para desligar o controlador ativo.
3. Agora você deve examinar o plano de fundo do dispositivo. Depois que os dois controladores forem completamente desligados, os LEDs de status em ambos os controladores devem estar piscando em vermelho. Se você precisar desligar o dispositivo completamente no momento, inverta os interruptores de energia em ambos os módulos de energia e resfriamento (PCMs) para a posição desligado. Isso deve desligar o dispositivo.

## <a name="reset-the-device-to-factory-default-settings"></a>Redefinir o dispositivo para as configurações padrão de fábrica

> [!IMPORTANT]
> Se você precisar redefinir o dispositivo para as configurações padrão de fábrica, entre em contato com Suporte da Microsoft. O procedimento descrito abaixo deve ser usado somente em conjunto com Suporte da Microsoft.

Este procedimento descreve como redefinir o dispositivo de Microsoft Azure StorSimple para as configurações padrão de fábrica usando Windows PowerShell para StorSimple.
A redefinição de um dispositivo remove todos os dados e configurações de todo o cluster por padrão.

Execute as seguintes etapas para redefinir o dispositivo de Microsoft Azure StorSimple para as configurações padrão de fábrica:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Para redefinir o dispositivo para as configurações padrão no Windows PowerShell para StorSimple
1. Acesse o dispositivo por meio de seu console serial. Verifique a mensagem de faixa para garantir que você está conectado ao controlador **ativo** .
2. No menu do console serial, escolha a opção 1, **faça logon com acesso completo**.
3. No prompt, digite o seguinte comando para redefinir o cluster inteiro, removendo todos os dados, metadados e configurações do controlador:
   
    `Reset-HcsFactoryDefault`
   
    Em vez disso, para redefinir um único controlador, use o cmdlet [Reset-HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) com o parâmetro `-scope`.)
   
    O sistema será reinicializado várias vezes. Você será notificado quando a redefinição for concluída com êxito. Dependendo do modelo do sistema, pode levar de 45-60 minutos para um dispositivo 8100 e 60-90 minutos para que um 8600 conclua esse processo.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Perguntas e respostas sobre o gerenciamento de controladores de dispositivo
Nesta seção, Resumimos algumas das perguntas frequentes sobre o gerenciamento de controladores de dispositivo StorSimple.

**P.** O que acontece se ambos os controladores em meu dispositivo estiverem íntegros e ligados e eu reiniciar ou desligar o controlador ativo?

**R.** Se ambos os controladores em seu dispositivo estiverem íntegros e ativados, será solicitada a confirmação. Você pode optar por:

* **Reiniciar o controlador ativo** – você é notificado de que reiniciar um controlador ativo fez o failover do dispositivo para o controlador passivo. O controlador é reiniciado.
* **Desligar um controlador ativo** – você é notificado de que desligar um controlador ativo resulta em tempo de inatividade. Você também precisa pressionar o botão de energia no dispositivo para ligar o controlador.

**P.** O que acontece se o controlador passivo em meu dispositivo estiver indisponível ou desligado e eu reiniciar ou desligar o controlador ativo?

**R.** Se o controlador passivo em seu dispositivo estiver indisponível ou desativado, e você optar por:

* **Reiniciar o controlador ativo** – você será notificado de que continuar a operação resultará em uma interrupção temporária do serviço e será solicitada a confirmação.
* **Desligar um controlador ativo** – você é notificado de que continuar a operação resulta em tempo de inatividade. Você também precisa pressionar o botão de energia em um ou ambos os controladores para ligar o dispositivo. Você será solicitado a confirmar.

**P.** Quando a reinicialização ou o desligamento do controlador falha em andamento?

**R.** A reinicialização ou o desligamento de um controlador poderá falhar se:

* Uma atualização de dispositivo está em andamento.
* Uma reinicialização do controlador já está em andamento.
* Um desligamento do controlador já está em andamento.

**P.** Como você pode descobrir se um controlador foi reiniciado ou desligado?

**R.** Você pode verificar o status do controlador na folha do controlador. O status do controlador indicará se um controlador está no processo de reinicialização ou desligamento. Além disso, a folha **alertas** conterá um alerta informativo se o controlador for reiniciado ou desligado. As operações de reinicialização e desligamento do controlador também são registradas nos logs de atividade. Para obter mais informações sobre logs de atividade, acesse [exibir os logs de atividade](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**P.** Há algum impacto na e/s como resultado do failover do controlador?

**R.** As conexões TCP entre os iniciadores e o controlador ativo serão redefinidas como resultado do failover do controlador, mas serão restabelecidas quando o controlador passivo assumir a operação. Pode haver uma pausa temporária (menos de 30 segundos) na atividade de e/s entre os iniciadores e o dispositivo no decorrer dessa operação.

**P.** Como fazer retornar meu controlador para o serviço depois que ele tiver sido desligado e removido?

**R.** Para retornar um controlador ao serviço, você deve inseri-lo no chassi, conforme descrito em [substituir um módulo de controlador em seu dispositivo StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Passos seguintes
* Se você encontrar problemas com os controladores de dispositivo StorSimple que você não pode resolver usando os procedimentos listados neste tutorial, [entre em contato com suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).
* Para saber mais sobre como usar o serviço de Device Manager do StorSimple, acesse [usar o serviço storsimple Device Manager para administrar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).

