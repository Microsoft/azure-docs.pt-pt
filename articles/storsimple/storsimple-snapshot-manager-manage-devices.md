---
title: Gerir dispositivos com StorSimple Snapshot Manager [ StorSimple Snapshot Manager] Microsoft Docs
description: Descreve como utilizar o snap-in StorSimple Snapshot Manager MMC para ligar e gerir dispositivos StorSimple.
services: storsimple
documentationcenter: ''
author: twooley
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: a037ddc5a43b288349b17dc7d954991a2c634aa3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254693"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Utilize o StorSimple Snapshot Manager para ligar e gerir dispositivos StorSimple
## <a name="overview"></a>Descrição geral
Pode utilizar nódosos no painel StorSimple Snapshot Manager **Scope** para verificar os dados do dispositivo StorSimple importados e atualizar dispositivos de armazenamento conectados. Além disso, ao clicar no nó dos **Dispositivos,** pode ver uma lista de dispositivos conectados e informações de estado correspondentes no painel **de resultados.**

![Dispositivos ligados](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Dispositivo conectado storSimple Snapshot Manager** 

Dependendo das suas seleções **de Visualização,** o painel **de resultados** mostra as seguintes informações sobre cada dispositivo. (Para mais informações sobre a configuração de uma vista, vá ao [menu Ver](storsimple-use-snapshot-manager.md#view-menu).

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |O nome do dispositivo configurado no portal clássico azure |
| Modelo |O número de modelo do dispositivo |
| Versão |A versão do software instalado no dispositivo |
| Estado |Se o dispositivo está disponível |
| Última Sincronizada |Data e hora em que o dispositivo foi sincronizado pela última vez |
| Série Nº. |O número de série do dispositivo |

Se clicar no nó dos **Dispositivos** na **vidraça** scope, pode selecionar entre as seguintes ações:

* Adicione ou substitua um dispositivo
* Ligar um dispositivo e verificar as importações
* Atualizar dispositivos conectados

Se clicar no nó dos **Dispositivos** e clicar no nome de um dispositivo no painel **resultados,** pode selecionar entre as seguintes ações:

* Autenticar um dispositivo
* Ver detalhes do dispositivo
* Atualizar um dispositivo
* Eliminar uma configuração do dispositivo
* Alterar uma palavra-passe do dispositivo

> [!NOTE]
> Todas estas ações também estão disponíveis no painel **ações.**


Este tutorial explica como usar o StorSimple Snapshot Manager para ligar e gerir dispositivos e executar as seguintes tarefas:

* Adicione ou substitua um dispositivo
* Ligar um dispositivo e verificar as importações
* Atualizar dispositivos conectados
* Autenticar um dispositivo
* Ver detalhes do dispositivo
* Atualizar um dispositivo individual
* Eliminar uma configuração do dispositivo
* Alterar uma senha de dispositivo expirada
* Substitua um dispositivo falhado

> [!NOTE]
> Para obter informações gerais sobre a utilização da interface StorSimple Snapshot Manager, vá à interface de [utilizador do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adicione ou substitua um dispositivo
Utilize o seguinte procedimento para adicionar ou substituir um dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Para adicionar ou substituir um dispositivo
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique no nó dos **Dispositivos** e, em seguida, clique em **Configurar um dispositivo**. Aparece a caixa de diálogo **Configuração de um Dispositivo.**
   
    ![Configure um dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Na caixa de desvendada do **Dispositivo,** selecione o endereço IP do dispositivo ou dispositivo virtual. 
4. Na caixa de texto **Password,** digite a palavra-passe StorSimple Snapshot Manager que criou para o dispositivo no portal clássico Azure. Clique em **OK**. O StorSimple Snapshot Manager procura o dispositivo que identificou. 
   
   * Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adiciona uma ligação.
   * Se o dispositivo não estiver disponível por qualquer motivo, o StorSimple Snapshot Manager devolve uma mensagem de erro. Clique **em OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo **Configurar um Dispositivo.**

## <a name="connect-a-device-and-verify-imports"></a>Ligar um dispositivo e verificar as importações
Utilize o seguinte procedimento para ligar um dispositivo StorSimple e verificar se são importados quaisquer grupos de volume existentes que tenham cópias de segurança associadas.

#### <a name="to-connect-a-device-and-verify-imports"></a>Para ligar um dispositivo e verificar as importações
1. Para ligar um dispositivo ao StorSimple Snapshot Manager, siga as instruções em Adicionar ou substitua um dispositivo. Quando se conecta a um dispositivo, o StorSimple Snapshot Manager responde da seguinte forma:
   
   * Se o dispositivo não estiver disponível por qualquer motivo, o StorSimple Snapshot Manager devolve uma mensagem de erro. 
   
   * Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adiciona uma ligação. Quando seleciona o dispositivo, aparece no painel **resultados** e o campo de estado indica que o dispositivo está **disponível**. O StorSimple Snapshot Manager importa quaisquer grupos de volume configurados para o dispositivo, desde que os grupos de volume tenham cópias de segurança associadas. As políticas de reserva não são importadas. Não são importados grupos de volume que não possuam cópias de segurança associadas.
2. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
3. Clique no nó superior no painel **scope** e, em seguida, clique em **Toggle Imports Display**.
   
    ![Selecione Toggle Imports Display](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Aparece a caixa de diálogo **Toggle Imports Display,** mostrando o estado dos grupos de volume e cópias de segurança importados. Clique em **OK**.

Depois de os grupos de volume e cópias de segurança serem importados com sucesso, pode utilizar o StorSimple Snapshot Manager para os gerir, tal como geriria grupos de volume e backups que criou e configurado com o StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Atualizar dispositivos conectados
Utilize o seguinte procedimento para sincronizar os dispositivos StorSimple ligados com o StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Para atualizar dispositivos conectados
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique à direita **dispositivos**, e, em seguida, clique em **Dispositivos de Atualização**. Isto sincroniza os dispositivos conectados com o StorSimple Snapshot Manager para que possa ver os grupos de volume e cópias de segurança, incluindo quaisquer adições recentes. 
   
    ![Atualizar os dispositivos StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

A ação **Refresh Devices** recupera quaisquer novos grupos de volume e quaisquer cópias de segurança associadas de dispositivos conectados. Ao contrário da ação de **volumes Rescan** disponível para o nó **volumes,** os **Dispositivos de Atualização** não restauram o registo de backup.

## <a name="authenticate-a-device"></a>Autenticar um dispositivo
Utilize o seguinte procedimento para autenticar um dispositivo StorSimple com o StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Para autenticar um dispositivo
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique em **Dispositivos**.
3. No painel **resultados,** clique no nome do dispositivo e clique em **Authenticate**.
4. Aparece a caixa de diálogo **Authenticate.** Digite a palavra-passe do dispositivo e, em seguida, clique **OK**.
   
    ![Caixa de diálogo autenticado](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Ver detalhes do dispositivo
Utilize o seguinte procedimento para visualizar os detalhes de um dispositivo StorSimple e, se necessário, ressincronizar o dispositivo com o StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Para ver e resincronizar detalhes do dispositivo
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique em **Dispositivos**.
3. No painel **resultados,** clique no nome do dispositivo e clique em **Detalhes**.

4.A caixa de diálogo **de detalhes** do dispositivo aparece. Esta caixa mostra o nome, modelo, versão, número de série, estado, nome qualificado do iSCSI (IQN) e última data e hora de sincronização.

* Clique em **Resincronizar** para sincronizar o dispositivo.
* Clique em **OK** ou **Cancele** para fechar a caixa de diálogo.
  
  ![Detalhes do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Atualizar um dispositivo individual
Utilize o seguinte procedimento para resincronizar um dispositivo StorSimple individual com o StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Para refrescar um dispositivo
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Scope,** clique em **Dispositivos**. 
3. No painel **resultados,** clique no nome do dispositivo e clique no **Dispositivo de Atualização**. Isto sincroniza o dispositivo com o StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Eliminar uma configuração do dispositivo
Utilize o seguinte procedimento para eliminar uma configuração individual do dispositivo StorSimple do StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Para eliminar uma configuração do dispositivo
1. Clique no ícone do ambiente de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Scope,** clique em **Dispositivos**. 
3. No painel **resultados,** clique no nome do dispositivo e clique em **Eliminar**. 
4. Aparece a seguinte mensagem. Clique em **Sim** para eliminar a configuração ou clique **em Não** para cancelar a eliminação.
   
    ![Eliminar configuração do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Alterar uma senha de dispositivo expirada
Tem de introduzir uma palavra-passe para autenticar um dispositivo StorSimple com o StorSimple Snapshot Manager. Configura esta palavra-passe quando utiliza a interface Do Windows PowerShell para configurar o dispositivo. No entanto, a palavra-passe pode expirar. Se isso acontecer, pode utilizar o portal clássico Azure para alterar a palavra-passe. Em seguida, como o dispositivo foi configurado no StorSimple Snapshot Manager antes da caducidade da palavra-passe, tem de reautenticar o dispositivo no StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Para alterar a senha expirada
1. No portal clássico azure, inicie o serviço StorSimple Manager.
2. Clique em**Configurar** **dispositivos** > para o dispositivo.
3. Desloque-se até à secção StorSimple Snapshot Manager. Introduza uma palavra-passe com 14-15 caracteres. Certifique-se de que a palavra-passe contém uma mistura de caracteres maiúsculos, minúsculos, numéricos e especiais.
4. Reintroduza a senha para confirmá-la.
5. Clique em **Guardar** na parte inferior da página.

#### <a name="to-re-authenticate-the-device"></a>Para reautenticar o dispositivo
1. Inicie o StorSimple Snapshot Manager.
2. No painel **Scope,** clique em **Dispositivos**. Uma lista de dispositivos configurados aparece no painel **resultados.**
3. Selecione o dispositivo, clique à direita e, em seguida, clique em **Authenticate**.
4. Na janela **Authenticate,** introduza a nova senha.
5. Selecione o dispositivo, clique à direita e selecione **dispositivo Refresh**. Isto sincroniza o dispositivo com o StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Substitua um dispositivo falhado
Se um dispositivo StorSimple falhar e for substituído por um dispositivo de espera (failover), utilize os seguintes passos para se ligar ao novo dispositivo e visualizar as cópias de segurança associadas.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Para ligar a um novo dispositivo após a falha
1. Reconfigurar a ligação iSCSI ao novo dispositivo. Para instruções, vá ao "Passo 7: Monte, inicie e forme um volume" no [dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Se o novo dispositivo StorSimple tiver o mesmo endereço IP que o antigo, poderá ligar a configuração antiga.


1. Pare o Serviço de Gestão Microsoft StorSimple:
   
   1. Inicie o Gestor de Servidores.
   2. No Dashboard do Gestor de Servidores, no menu **Ferramentas,** selecione **Serviços**.
   3. Na janela **Serviços,** selecione o Serviço de **Gestão Microsoft StorSimple**.
   4. No painel certo, no **microsoft StorSimple Management Service,** clique em **Parar o serviço**.
2. Remova as informações de configuração relacionadas com o antigo dispositivo:
   
   1. No File Explorer, navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Elimine os ficheiros na pasta BACatalog.
3. Reiniciar o Serviço de Gestão Microsoft StorSimple:
   
   1. No Dashboard do Gestor de Servidores, no menu **Ferramentas,** selecione **Serviços**.
   2. Na janela **Serviços,** selecione o Serviço de **Gestão Microsoft StorSimple**.
   3. No painel certo, no **microsoft StorSimple Management Service,** clique **em reiniciar o serviço**.
4. Inicie o StorSimple Snapshot Manager.
5. Para configurar o novo dispositivo StorSimple, complete os passos no Passo 2: Ligue um dispositivo StorSimple no [Implemento Simple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Clique no nó de nível superior no painel **Scope** (StorSimple Snapshot Manager no exemplo) e, em seguida, clique no **Toggle Imports Display**. 
7. Uma mensagem aparece quando os grupos de volume importados e cópias de segurança são visíveis no StorSimple Snapshot Manager. Clique em **OK**.

## <a name="next-steps"></a>Passos seguintes
* Aprenda a utilizar o [StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Aprenda a usar o [StorSimple Snapshot Manager para visualizar e gerir volumes](storsimple-snapshot-manager-manage-volumes.md).

