---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 19d2dedc2ccf7015696504a94f5ef7c43a90d3be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184456"
---
#### <a name="to-download-hotfixes"></a>Para transferir correções

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

1. Inicie o Internet [http://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Explorer e navegue para .
2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.

    ![Instalar o catálogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Na caixa de pesquisa do Catálogo de Atualizações da Microsoft, introduza o número da Base de Conhecimento (KB) do hotfix que pretende descarregar, por exemplo **4037264**, e depois clique em **Search**.
   
    A listagem de hotfix aparece, por exemplo, **cumulativa atualização de pacote de software 5.0 para storSimple 8000 Series**.
   
    ![Catálogo de pesquisa](./media/storsimple-install-update5-hotfix/update-catalog-search.png)

4. Clique em **Baixar**. Especifique ou **Pesquise** uma localização local onde pretende que as transferências apareçam. Clique nos ficheiros para descarregar para a localização e pasta especificadas. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.
5. Procure quaisquer artigos de hotfixes adicionais listados na tabela acima **(4037266**) e descarregue os ficheiros correspondentes para as pastas específicas listadas na tabela anterior.

> [!NOTE]
> Os hotfixes devem ser acessíveis a partir de ambos os controladores para detetar eventuais mensagens de erro do controlador de pares.
>
> As correções têm de ser copiadas em três pastas separadas. Por exemplo, a atualização do software/cis/MDS do dispositivo pode ser copiada na pasta _FirstOrderUpdate,_ todas as outras atualizações não disruptivas podem ser copiadas na pasta _SecondOrderUpdate_ e atualizações do modo de manutenção copiadas na pasta _ThirdOrderUpdate._

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Para instalar e verificar correções do modo normal

Execute os seguintes passos para instalar e verificar correções do modo normal. Se já os instalou utilizando o portal Azure, salte para a frente para [instalar e verificar os hotfixes](#to-install-and-verify-maintenance-mode-hotfixes)do modo de manutenção .

1. Para instalar as correções, aceda à interface do Windows PowerShell na consola de série do dispositivo StorSimple. Siga as instruções detalhadas em [Utilizar o PuTTY para ligar à consola de série](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na linha de comandos, prima **Enter**.
2. Selecione opção 1, **inicie sessão com acesso total**. Recomendamos que primeiro instale a correção no controlador passivo.
3. Para instalar a correção, na linha de comandos, escreva:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Utilize o IP em vez de DNS no caminho de partilha no comando acima. O parâmetro da credencial é utilizado apenas se aceder a uma partilha autenticada.
   
    Recomendamos que utilize o parâmetro da credencial para aceder a partilhas. Mesmo as partilhas abertas para "todos" não são normalmente abertas para utilizadores não autenticados.
   
4. Forneça a palavra-passe quando lhe for pedida. É apresentada abaixo uma saída de exemplo para instalar as primeiras atualizações de pedido. Para a primeira atualização da encomenda, é necessário apontar para o ficheiro específico.

    >[!NOTE] 
    > Deve instalar primeiro o _HcsSoftwareUpdate.exe._ Depois de concluída esta instalação, instale _o CisMdsAgentUpdate.exe_.
   
        ```
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
        Confirm
   
        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y
   
        ```
5. Escreva **Y** quando lhe for pedido para confirmar a instalação da correção.
6. Monitorize a atualização com o cmdlet `Get-HcsUpdateStatus`. A atualização será concluída primeiro no controlador passivo. Assim que o controlador passivo for atualizado, existirá uma ativação pós-falha e a atualização será então aplicada no outro controlador. A atualização é concluída quando ambos os controladores são atualizados.
   
    A seguinte saída de exemplo mostra a atualização em curso. É `RunInprogress` `True` quando a atualização está em andamento.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 07/28/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     A saída de exemplo seguinte indica que a atualização foi concluída. É `RunInProgress` `False` quando a atualização estiver completa.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 07/28/2017 9:15:55 AM
    LastUpdateTimestamp : 07/28/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > Ocasionalmente, o cmdlet comunica `False` quando a atualização ainda está em curso. Para se certificar de que a correção foi concluída, aguarde alguns minutos, execute novamente este comando e certifique-se de que o `RunInProgress` é `False`. Se for, então a correção foi concluída.

7. Assim que a atualização do software for concluída, verifique as versões do software do sistema. Escreva:
   
    `Get-HcsSystem`
   
    Deverá ver as seguintes versões:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 5.0`
   * `HcsSoftwareVersion: 6.3.9600.17845`
   
     Se o número da versão não se alterar depois de aplicar a atualização, indica que a correção não foi aplicada. Se tal acontecer, entre em contacto com o [Suporte da Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) para obter assistência.
     
     > [!IMPORTANT]
     > Tem de reiniciar o `Restart-HcsController` controlador ativo através do cmdlet antes de aplicar a próxima atualização.
     
8. Repita os passos 3-6 para instalar o agente _CisMDSAgentupdate.exe_ descarregado para a sua pasta _FirstOrderUpdate._
8. Repita os passos 3-6 para instalar as atualizações da segunda ordem. 

    > [!NOTE] 
    > Para atualizações de segunda ordem, várias atualizações `Start-HcsHotfix cmdlet` podem ser instaladas apenas executando a pasta e apontando para a pasta onde estão localizadas as atualizações da segunda ordem. O cmdlet irá executar todas as atualizações disponíveis na pasta. Se já estiver instalado uma atualização, a lógica de atualização irá detetar e não irá aplicar essa atualização.

    Depois de instalar todas as correções, utilize o cmdlet `Get-HcsSystem`. As versões devem ser:
    
    * `CisAgentVersion:  1.0.9724.0`
    * `MdsAgentVersion: 35.2.2.0`
    * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Para instalar e verificar correções do modo de manutenção

Utilize o KB4037263 para instalar atualizações de firmware de disco. Estas são atualizações disruptivas e demoram cerca de 30 minutos a serem concluídas. Pode optar por instalá-las numa janela de manutenção planeada ao estabelecer uma ligação à consola de série do dispositivo.

> [!NOTE] 
> Se o seu firmware de disco já estiver atualizado, não terá de instalar estas atualizações. Execute o cmdlet `Get-HcsUpdateAvailability` a partir da consola de série do dispositivo para verificar se as atualizações estão disponíveis e se as atualizações são disruptivas (modo de manutenção) ou não disruptivas (modo normal).

Para instalar as atualizações de firmware do disco, siga as instruções abaixo.

1. Coloque o dispositivo no modo de manutenção. 

    > [!NOTE] 
    > Não utilize o remo do Windows PowerShell quando estiver ligado a um dispositivo em modo de manutenção. Em vez disso, execute este cmdlet no controlador de dispositivo quando estiver ligado através da consola de série do dispositivo.

    Para colocar o controlador no modo de manutenção, escreva:
   
    `Enter-HcsMaintenanceMode`
   
    É apresentada abaixo uma saída de exemplo.
   
        Controller0>Enter-HcsMaintenanceMode
        Checking device state...
   
        In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
        [Y] Yes [N] No (Default is "Y"): Y
   
        -----------------------MAINTENANCE MODE------------------------
        Microsoft Azure StorSimple Appliance Model 8600
        Name: Update4-8600-mystorsimple
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Passive
        ---------------------------------------------------------------
   
        Serial Console Menu
        [1] Log in with full access
        [2] Log into peer controller with full access
        [3] Connect with limited access
        [4] Change language
        Please enter your choice>
   
    Ambos os controladores são, em seguida, reiniciados no modo de manutenção.
2. Para instalar a atualização de firmware do disco, escreva:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    É apresentada abaixo uma saída de exemplo.
   
        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
        Enter Password:
        WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
        Confirm
        This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
        [Y] Yes [N] No (Default is "Y"): Y
        WARNING: Installation is currently in progress. This operation can take several minutes to complete.
3. Monitorize o progresso da instalação com o comando `Get-HcsUpdateStatus`. A atualização está completa quando o `RunInProgress` é alterado para `False`.
4. Após a instalação estar concluída, o controlador em que a correção do modo de manutenção foi instalada reinicia. Inicie sessão como opção 1, **inicie sessão com acesso total**e verifique a versão firmware do disco. Escreva:
   
   `Get-HcsFirmwareVersion`
   
   As versões de firmware do disco esperadas são:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`
   
   É apresentada abaixo uma saída de exemplo.
   
       -----------------------MAINTENANCE MODE------------------------
       Microsoft Azure StorSimple Appliance Model 8600
       Name: Update4-8600-mystorsimple
       Software Version: 6.3.9600.17845
       Copyright (C) 2014 Microsoft Corporation. All rights reserved.
       You are connected to Controller1
       ---------------------------------------------------------------
   
       Controller1>Get-HcsFirmwareVersion
   
       Controller0 : TalladegaFirmware
           ActiveBIOS:0.45.0010
              BackupBIOS:0.45.0006
              MainCPLD:17.0.000b
              ActiveBMCRoot:2.0.001F
              BackupBMCRoot:2.0.001F
              BMCBoot:2.0.0002
              LsiFirmware:20.00.04.00
              LsiBios:07.37.00.00
              Battery1Firmware:06.2C
              Battery2Firmware:06.2C
              DomFirmware:X231600
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0x9134777A
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x19
              CanisterVPDCRC:0x142F7DC2
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:1.00|1.05
              PCM1VPDStructure:0x05
              PCM1VPDCRC:0x41BEF99C
              PCM2Firmware:1.00|1.05
              PCM2VPDStructure:0x05
              PCM2VPDCRC:0x41BEF99C

           EbodFirmware
              CanisterFirmware:3.5.0.56
              CanisterBootloader:5.03
              CanisterConfigCRC:0xB23150F8
              CanisterVPDStructure:0x06
              CanisterGEMCPLD:0x14
              CanisterVPDCRC:0xBAA55828
              MidplaneVPDStructure:0x0C
              MidplaneVPDCRC:0xA6BD4F64
              MidplaneCPLD:0x10
              PCM1Firmware:3.11
              PCM1VPDStructure:0x03
              PCM1VPDCRC:0x6B58AD13
              PCM2Firmware:3.11
              PCM2VPDStructure:0x03
              PCM2VPDCRC:0x6B58AD13

           DisksFirmware
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              SmrtStor:TXA2D20800GA6XYR:KZ50
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
              WD:WD4001FYYG-01SL3:VR08
   
    Execute o comando `Get-HcsFirmwareVersion` no segundo controlador para se certificar que a versão do software foi atualizada. Em seguida, pode sair do modo de manutenção. Para tal, escreva o seguinte comando em cada controlador de dispositivo:
   
   `Exit-HcsMaintenanceMode`

5. Os controladores reiniciam quando sair do modo de manutenção. Depois de as atualizações do firmware do disco serem aplicadas com sucesso e o dispositivo ter saído do modo de manutenção, volte ao portal Azure. Tenha em atenção que o portal pode não mostrar que instalou as atualizações do modo de manutenção durante 24 horas.

