---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cc42c22579346c272ee5a6f41147e6b5b09643ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95557954"
---
#### <a name="to-download-hotfixes"></a>Para transferir correções

Execute os seguintes passos para transferir a atualização de software a partir do Catálogo Microsoft Update.

1. Inicie o Internet Explorer e navegue para [http://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .
2. Se esta for a primeira vez que utiliza o Catálogo Microsoft Update neste computador, clique em **Instalar** quando lhe for pedido para instalar o suplemento do Catálogo Microsoft Update.

    ![Instalar o catálogo](./media/storsimple-install-update2-hotfix/HCS_InstallCatalog-include.png)

3. Na caixa de pesquisa do Catálogo Microsoft Update, introduza o número da Base de Dados de Conhecimento (BDC) de correção que pretende transferir, por exemplo **4011839** e, em seguida, clique em **Pesquisa**.
   
    A lista de correções é apresentada, por exemplo, **Atualização do Pacote de Software Cumulativo 4.0 para StorSimple Série 8000**.
   
    ![Catálogo de pesquisa](./media/storsimple-install-update2-hotfix/HCS_SearchCatalog1-include.png)

4. Clique em **Transferir**. Especifique ou **Pesquise** uma localização local onde pretende que as transferências apareçam. Clique nos ficheiros para descarregar para a localização e pasta especificadas. A pasta também pode ser copiada para uma partilha de rede que é acessível a partir do dispositivo.
5. Procure quaisquer hotfixes adicionais listados na tabela acima **(4011841),** e descarregue os ficheiros correspondentes para as pastas específicas listadas na tabela anterior.

> [!NOTE]
> Os hotfixes devem estar acessíveis a ambos os controladores para detetar eventuais mensagens de erro do controlador de pares.
>
> As correções têm de ser copiadas em três pastas separadas. Por exemplo, a atualização do software do dispositivo/agente Cis/MDS pode ser copiada na pasta _FirstOrderUpdate,_ todas as outras atualizações não disruptivas podem ser copiadas na pasta _SecondOrderUpdate_ e atualizações do modo de manutenção copiadas na pasta _ThirdOrderUpdate._

#### <a name="to-install-and-verify-regular-mode-hotfixes"></a>Para instalar e verificar correções do modo normal

Execute os seguintes passos para instalar e verificar correções do modo normal. Se já as instalou através do portal clássico do Azure, avance para [instalar e verificar correções de modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes).

1. Para instalar as correções, aceda à interface do Windows PowerShell na consola de série do dispositivo StorSimple. Siga as instruções detalhadas em [Utilizar o PuTTY para ligar à consola de série](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Na linha de comandos, prima **Enter**.
2. Selecione opção 1, **faça login com acesso total**. Recomendamos que primeiro instale a correção no controlador passivo.
3. Para instalar a correção, na linha de comandos, escreva:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    Utilize o IP em vez de DNS no caminho de partilha no comando acima. O parâmetro da credencial é utilizado apenas se aceder a uma partilha autenticada.
   
    Recomendamos que utilize o parâmetro da credencial para aceder a partilhas. Mesmo as partilhas abertas para "todos" não são normalmente abertas para utilizadores não autenticados.
   
    Forneça a palavra-passe quando lhe for pedida.
   
    É apresentada abaixo uma saída de exemplo para instalar as primeiras atualizações de pedido. Para a primeira atualização da encomenda, é necessário apontar para o ficheiro específico.
   
    ```output
    Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
    \FirstOrderUpdate\HcsSoftwareUpdate.exe -Credential contoso\John
   
    Confirm
   
    This operation starts the hotfix installation and could reboot one or
    both of the controllers. If the device is serving I/Os, these will not
    be disrupted. Are you sure you want to continue?
    [Y] Yes [N] No [?] Help (default is "Y"): Y

    ```
4. Escreva **Y** quando lhe for pedido para confirmar a instalação da correção.
5. Monitorize a atualização com o cmdlet `Get-HcsUpdateStatus`. A atualização será concluída primeiro no controlador passivo. Assim que o controlador passivo for atualizado, existirá uma ativação pós-falha e a atualização será então aplicada no outro controlador. A atualização é concluída quando ambos os controladores são atualizados.
   
    A seguinte saída de exemplo mostra a atualização em curso. O `RunInprogress` será `True` quando a atualização está em curso.

    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : True
    LastHotfixTimestamp :
    LastUpdateTimestamp : 02/03/2017 2:04:02 AM
    Controller0Events   :
    Controller1Events   :
    ```
   
     A saída de exemplo seguinte indica que a atualização foi concluída. O `RunInProgress` será `False` quando a atualização for concluída.
   
    ```
    Controller0>Get-HcsUpdateStatus
    RunInprogress       : False
    LastHotfixTimestamp : 02/03/2017 9:15:55 AM
    LastUpdateTimestamp : 02/03/2017 9:06:07 AM
    Controller0Events   :
    Controller1Events   :
    ```

    > [!NOTE]
    > Ocasionalmente, o cmdlet comunica `False` quando a atualização ainda está em curso. Para se certificar de que a correção foi concluída, aguarde alguns minutos, execute novamente este comando e certifique-se de que o `RunInProgress` é `False`. Se for, então a correção foi concluída.

6. Assim que a atualização do software for concluída, verifique as versões do software do sistema. Escreva:
   
    `Get-HcsSystem`
   
    Deverá ver as seguintes versões:
   
   * `FriendlySoftwareVersion: StorSimple 8000 Series Update 4.0`
   * `HcsSoftwareVersion: 6.3.9600.17820`
   
     Se o número da versão não se alterar depois de aplicar a atualização, indica que a correção não foi aplicada. Se tal acontecer, entre em contacto com o [Suporte da Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) para obter assistência.
     
     > [!IMPORTANT]
     > Tem de reiniciar o controlador ativo através do `Restart-HcsController` cmdlet antes de aplicar a próxima atualização.
     
7. Repita os passos 3-5 para instalar o agente Cis/MDS descarregado para a sua pasta _FirstOrderUpdate._ 
8. Repita os passos 3 a 5 para instalar as atualizações de segunda ordem. **Para atualizações de segunda encomenda, várias atualizações podem ser instaladas apenas executando e `Start-HcsHotfix cmdlet` apontando para a pasta onde estão localizadas atualizações de segunda encomenda. O cmdlet executará todas as atualizações disponíveis na pasta.** Se já estiver instalado uma atualização, a lógica de atualização irá detetar e não irá aplicar essa atualização. 

Depois de instalar todas as correções, utilize o cmdlet `Get-HcsSystem`. As versões devem ser:

   * `CisAgentVersion:  1.0.9441.0`
   * `MdsAgentVersion: 35.2.2.0`
   * `Lsisas2Version: 2.0.78.00`


#### <a name="to-install-and-verify-maintenance-mode-hotfixes"></a>Para instalar e verificar correções do modo de manutenção
Utilize o KB4011837 para instalar atualizações de firmware do disco. Estas são atualizações disruptivas e demoram cerca de 30 minutos a serem concluídas. Pode optar por instalá-las numa janela de manutenção planeada ao estabelecer uma ligação à consola de série do dispositivo.

Tenha em atenção que se o firmware do disco já estiver atualizado, não terá de instalar estas atualizações. Execute o cmdlet `Get-HcsUpdateAvailability` a partir da consola de série do dispositivo para verificar se as atualizações estão disponíveis e se as atualizações são disruptivas (modo de manutenção) ou não disruptivas (modo normal).

Para instalar as atualizações de firmware do disco, siga as instruções abaixo.

1. Coloque o dispositivo no modo de manutenção. **Tenha em atenção que não deve utilizar o remoing do Windows PowerShell quando se ligar a um dispositivo em modo de manutenção. Em vez disso, passe este cmdlet no controlador do dispositivo quando ligado através da consola em série do dispositivo.** Escreva:
   
    `Enter-HcsMaintenanceMode`
   
    É apresentada abaixo uma saída de exemplo.

    ```output
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
    ```
   
    Ambos os controladores são, em seguida, reiniciados no modo de manutenção.
2. Para instalar a atualização de firmware do disco, escreva:
   
    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`
   
    É apresentada abaixo uma saída de exemplo.

    ```output
    Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\ThirdOrderUpdates\ -Credential contoso\john
    Enter Password:
    WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
    Confirm
    This operation starts a hotfix installation and could reboot one or both of the controllers. By installing new updates you agree to, and accept any additional terms associated with, the new functionality listed in the release notes (https://go.microsoft.com/fwLink/?LinkID=613790). Are you sure you want to continue?
    [Y] Yes [N] No (Default is "Y"): Y
    WARNING: Installation is currently in progress. This operation can take several minutes to complete.
    ```

3. Monitorize o progresso da instalação com o comando `Get-HcsUpdateStatus`. A atualização está completa quando o `RunInProgress` é alterado para `False`.
4. Após a instalação estar concluída, o controlador em que a correção do modo de manutenção foi instalada reinicia. Inicie sessão como opção 1, **faça login com acesso total** e verifique a versão do firmware do disco. Escreva:
   
   `Get-HcsFirmwareVersion`
   
   As versões de firmware do disco esperadas são:
   
   `XMGJ, XGEG, KZ50, F6C2, VR08, N002, 0106`
   
   É apresentada abaixo uma saída de exemplo.

    ```output
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8600
    Name: Update4-8600-mystorsimple
    Software Version: 6.3.9600.17820
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
    ```
   
    Execute o comando `Get-HcsFirmwareVersion` no segundo controlador para se certificar que a versão do software foi atualizada. Em seguida, pode sair do modo de manutenção. Para tal, escreva o seguinte comando em cada controlador de dispositivo:
   
   `Exit-HcsMaintenanceMode`

5. Os controladores reiniciam quando sair do modo de manutenção. Assim que as atualizações do firmware do disco forem aplicadas com êxito e o dispositivo tiver saído do modo de manutenção, regresse ao portal clássico do Azure. Tenha em atenção que o portal pode não mostrar que instalou as atualizações do modo de manutenção durante 24 horas.