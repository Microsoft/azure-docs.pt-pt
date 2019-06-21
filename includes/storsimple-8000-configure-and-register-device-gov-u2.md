---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 7700f1c92aecab76dbc347814b7b161bc3d822a0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184315"
---
### <a name="to-configure-and-register-the-device"></a>Para configurar e registar o dispositivo
1. Aceda à interface do Windows PowerShell na consola de série do dispositivo StorSimple. Para obter as instruções, veja [Utilizar o PuTTY para ligar à consola de série do dispositivo](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console). **Confirme que está a seguir o procedimento rigorosamente. Caso contrário, não conseguirá aceder à consola.**
2. Na sessão apresentada, prima **Enter** uma vez para obter uma linha de comandos.
3. Ser-lhe-á solicitado que escolha o idioma que pretende configurar para o dispositivo. Especifique o idioma e prima **Enter**.
   
    ![Configurar e registar o dispositivo 1 do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. No menu da consola de série apresentado, selecione a opção 1, **Iniciar sessão com acesso total**.
   
    ![Registar o dispositivo 2 do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Execute os seguintes passos para configurar as definições de rede mínimas necessárias para o seu dispositivo.
   
   > [!IMPORTANT]
   > Estes passos de configuração têm de ser executados no controlador ativo do dispositivo. O menu da consola de série indica o estado do controlador na mensagem de faixa. Se não estão a ligar ao controlador ativo, desligue e, em seguida, ligar para o controlador ativo.
   
   1. Na linha de comandos, escreva a sua palavra-passe. A palavra-passe predefinida do dispositivo é **Password1**.
   2. Escreva o seguinte comando:
      
        `Invoke-HcsSetupWizard`
   3. Será apresentado um assistente de configuração para o ajudar a configurar as definições de rede do dispositivo. Insira as seguintes informações:
      
      * Endereço IP para a interface rede DATA 0
      * Máscara de sub-rede
      * Gateway
      * Endereço IP do servidor DNS Primário
      * Endereço IP do servidor NTP Primário
      
      > [!NOTE]
      > Poderá ter de aguardar alguns minutos para que a máscara de sub-rede e as definições de DNS a ser aplicado.
    
   4. Opcionalmente, configure o servidor de proxy da web.
      
      > [!IMPORTANT]
      > Apesar de configuração do proxy web é opcional, lembre-se de que se utilizar um proxy da web, pode apenas configurá-lo aqui. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](../articles/storsimple/storsimple-configure-web-proxy.md).
     
6. Prima Ctrl + C para sair do Assistente de configuração.
8. Execute o seguinte cmdlet para apontar o dispositivo ao portal do Microsoft Azure Government (porque ele aponta para o portal clássico do Azure público por predefinição). Esta ação irá reiniciar os dois controladores. Recomendamos que utilize duas sessões PuTTY para ligar em simultâneo para ambos os controladores para que pode ver quando cada controlador é reiniciado.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   Verá uma mensagem de confirmação. Aceite a predefinição (**Y**).
9. Execute o cmdlet seguinte para retomar a configuração:
   
    `Invoke-HcsSetupWizard`
   
    ![Assistente de configuração de retomar](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Aceite as definições de rede. Verá uma mensagem de validação depois de aceitar cada definição.
11. Por motivos de segurança, a palavra-passe de administrador do dispositivo expira após a primeira sessão e terá de a alterar agora. Quando lhe for solicitado, forneça uma palavra-passe de administrador do dispositivo. Uma palavra-passe de administrador do dispositivo válida tem de ter entre 8 e 15 carateres. A palavra-passe tem de conter três dos seguintes: minúsculas, maiúsculas, números e carateres especiais.
    
    <br/>![Registar o dispositivo 5 do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. O último passo do assistente de configuração regista o dispositivo com o serviço Gestor de Dispositivos do StorSimple. Para isso, terá da chave de registo de serviço que obteve no [passo 2: Obter a chave de registo do serviço](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key). Depois de fornecer a chave de registo, poderá ter de aguardar 2 a 3 minutos até o dispositivo ficar registado.
    
    > [!NOTE]
    > Pode premir Ctrl + C em qualquer momento para sair do assistente de configuração. Se tiver introduzido todas as definições de rede (Endereço IP para Data 0, Máscara de sub-rede e Gateway), as entradas serão mantidas.
    
    ![Progresso de registo do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Uma vez registado o dispositivo, aparecerá uma chave de Encriptação de Dados do Serviço. Copie esta chave e guarde-a numa localização segura. **Esta chave é necessária com a chave de registo de serviço para registar dispositivos adicionais com o serviço StorSimple Device Manager.** Veja [Segurança do StorSimple](../articles/storsimple/storsimple-8000-security.md) para obter mais informações sobre esta chave.
    
    ![Registar o dispositivo 7 do StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Para copiar o texto da janela da consola de série, basta selecioná-lo. Em seguida, poderá colá-lo na área de transferência ou num editor de texto.
    > 
    > Não utilize **Ctrl + C** para copiar a chave de encriptação de dados do serviço. Usando **Ctrl + C** fará com que sair do Assistente de configuração. Como resultado, a palavra-passe de administrador do dispositivo não será alterada e o dispositivo irá reverter para a palavra-passe predefinida.
    
14. Feche a consola de série.
15. Regresse ao Portal de administração pública do Azure e conclua os seguintes passos:
    
    1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple.
    2. Clique em **Dispositivos**. Na lista de dispositivos, identificar o dispositivo que está a implementar. Certifique-se de que o dispositivo foi ligado com êxito para o serviço, vendo o estado. O estado do dispositivo deve ser **Online**.
            
        Se o estado do dispositivo for **Offline**, aguarde alguns minutos para que o dispositivo fique online.
       
        Se o dispositivo continuar offline mesmo após alguns minutos, terá então de verificar se a sua rede de firewall foi configurada do modo descrito em [Requisitos de rede para o seu dispositivo StorSimple](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Verifique se a porta 9354 está aberta para comunicação de saída, uma vez que é utilizada pelo barramento de serviço para a comunicação Serviço Gestor de Dispositivos do StorSimple.

