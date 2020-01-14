---
title: Gerenciar dispositivos com o StorSimple Snapshot Manager | Microsoft Docs
description: Descreve como usar o snap-in do StorSimple Snapshot Manager MMC para conectar e gerenciar dispositivos StorSimple.
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
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931734"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Usar o Snapshot Manager StorSimple para conectar e gerenciar dispositivos StorSimple
## <a name="overview"></a>Visão geral
Você pode usar nós no painel **escopo** de snapshot Manager do storsimple para verificar os dados do dispositivo storsimple importados e atualizar os dispositivos de armazenamento conectados. Além disso, ao clicar no nó **dispositivos** , você poderá ver uma lista de dispositivos conectados e informações de status correspondentes no painel de **resultados** .

![Dispositivos ligados](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: dispositivo conectado do StorSimple Snapshot Manager** 

Dependendo das seleções de **exibição** , o painel **resultados** mostra as seguintes informações sobre cada dispositivo. (Para obter mais informações sobre como configurar uma exibição, vá para o [menu Exibir](storsimple-use-snapshot-manager.md#view-menu).

| Coluna de resultados | Descrição |
|:--- |:--- |
| Nome |O nome do dispositivo conforme configurado no portal clássico do Azure |
| Modelo |O número do modelo do dispositivo |
| Versão |A versão do software instalado no dispositivo |
| Estado |Se o dispositivo está disponível |
| Última sincronização |Data e hora em que o dispositivo foi sincronizado pela última vez |
| N º de série |O número de série do dispositivo |

Se você clicar com o botão direito do mouse no nó **dispositivos** no painel **escopo** , poderá selecionar uma das seguintes ações:

* Adicionar ou substituir um dispositivo
* Conectar um dispositivo e verificar as importações
* Atualizar dispositivos conectados

Se você clicar no nó **dispositivos** e clicar com o botão direito do mouse em um nome de dispositivo no painel de **resultados** , poderá selecionar uma das seguintes ações:

* Autenticar um dispositivo
* Ver detalhes do dispositivo
* Atualizar um dispositivo
* Excluir uma configuração de dispositivo
* Alterar uma senha de dispositivo

> [!NOTE]
> Todas essas ações também estão disponíveis no painel **ações** .


Este tutorial explica como usar o Snapshot Manager do StorSimple para conectar e gerenciar dispositivos e executar as seguintes tarefas:

* Adicionar ou substituir um dispositivo
* Conectar um dispositivo e verificar as importações
* Atualizar dispositivos conectados
* Autenticar um dispositivo
* Ver detalhes do dispositivo
* Atualizar um dispositivo individual
* Excluir uma configuração de dispositivo
* Alterar uma senha de dispositivo expirada
* Substituir um dispositivo com falha

> [!NOTE]
> Para obter informações gerais sobre como usar a interface de Snapshot Manager do StorSimple, acesse [storsimple snapshot Manager interface do usuário](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adicionar ou substituir um dispositivo
Use o procedimento a seguir para adicionar ou substituir um dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Para adicionar ou substituir um dispositivo
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse no nó **dispositivos** e clique em **configurar um dispositivo**. A caixa de diálogo **configurar um dispositivo** é exibida.
   
    ![Configurar um dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Na caixa suspensa **dispositivo** , selecione o endereço IP do dispositivo ou dispositivo virtual. 
4. Na caixa de texto **senha** , digite a senha do snapshot Manager do StorSimple que você criou para o dispositivo no portal clássico do Azure. Clique em **OK**. O StorSimple Snapshot Manager pesquisa pelo dispositivo que você identificou. 
   
   * Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adicionará uma conexão.
   * Se o dispositivo não estiver disponível por algum motivo, o StorSimple Snapshot Manager retornará uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar a caixa de diálogo **configurar um dispositivo** .

## <a name="connect-a-device-and-verify-imports"></a>Conectar um dispositivo e verificar as importações
Use o procedimento a seguir para conectar um dispositivo StorSimple e verificar se todos os grupos de volumes existentes que têm backups associados são importados.

#### <a name="to-connect-a-device-and-verify-imports"></a>Para conectar um dispositivo e verificar as importações
1. Para conectar um dispositivo ao StorSimple Snapshot Manager, siga as instruções em Adicionar ou substituir um dispositivo. Quando ele se conecta a um dispositivo, o StorSimple Snapshot Manager responde da seguinte maneira:
   
   * Se o dispositivo não estiver disponível por algum motivo, o StorSimple Snapshot Manager retornará uma mensagem de erro. 
   
   * Se o dispositivo estiver disponível, o StorSimple Snapshot Manager adicionará uma conexão. Quando você seleciona o dispositivo, ele aparece no painel **resultados** e o campo status indica que o dispositivo está **disponível**. O StorSimple Snapshot Manager importa todos os grupos de volumes configurados para o dispositivo, desde que os grupos de volumes tenham backups associados. As políticas de backup não são importadas. Os grupos de volumes que não têm backups associados não são importados.
2. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
3. Clique com o botão direito do mouse no nó superior no painel **escopo** e clique em **alternar exibição de importações**.
   
    ![Selecione alternar exibição de importações](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. A caixa de diálogo **alternar exibição de importações** é exibida, mostrando o status dos grupos de volume e backups importados. Clique em **OK**.

Depois que os grupos de volumes e os backups forem importados com êxito, você poderá usar o StorSimple Snapshot Manager para gerenciá-los, assim como gerenciaria os grupos de volumes e os backups que você criou e configurou com o StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Atualizar dispositivos conectados
Use o procedimento a seguir para sincronizar os dispositivos do StorSimple conectados com o StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Para atualizar dispositivos conectados
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique com o botão direito do mouse em **dispositivos**e clique em **Atualizar dispositivos**. Isso sincroniza os dispositivos conectados com o StorSimple Snapshot Manager para que você possa exibir os grupos de volume e backups, incluindo quaisquer adições recentes. 
   
    ![Atualizar os dispositivos StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

A ação **Atualizar dispositivos** recupera todos os novos grupos de volumes e quaisquer backups associados dos dispositivos conectados. Ao contrário da ação **examinar volumes novamente** disponível para o nó **volumes** , **Atualizar dispositivos** não restaura o registro de backup.

## <a name="authenticate-a-device"></a>Autenticar um dispositivo
Use o procedimento a seguir para autenticar um dispositivo StorSimple com o StorSimple Snapshot Manager.

#### <a name="to-authenticate-a-device"></a>Para autenticar um dispositivo
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique em **dispositivos**.
3. No painel de **resultados** , clique com o botão direito do mouse no nome do dispositivo e clique em **autenticar**.
4. A caixa de diálogo **autenticar** é exibida. Digite a senha do dispositivo e clique em **OK**.
   
    ![Caixa de diálogo autenticar](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Ver detalhes do dispositivo
Use o procedimento a seguir para exibir os detalhes de um dispositivo StorSimple e, se necessário, sincronizar novamente o dispositivo com o StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Para exibir e ressincronizar os detalhes do dispositivo
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique em **dispositivos**.
3. No painel de **resultados** , clique com o botão direito do mouse no nome do dispositivo e clique em **detalhes**.

4. a caixa de diálogo **detalhes do dispositivo** é exibida. Essa caixa mostra o nome, o modelo, a versão, o número de série, o status, o nome qualificado iSCSI de destino (IQN) e a data e a hora da última sincronização.

* Clique em **ressincronizar** para sincronizar o dispositivo.
* Clique em **OK** ou em **Cancelar** para fechar a caixa de diálogo.
  
  ![Detalhes do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Atualizar um dispositivo individual
Use o procedimento a seguir para ressincronizar um dispositivo StorSimple individual com o StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Para atualizar um dispositivo
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. No painel **escopo** , clique em **dispositivos**. 
3. No painel de **resultados** , clique com o botão direito do mouse no nome do dispositivo e clique em **Atualizar dispositivo**. Isso sincroniza o dispositivo com o StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Excluir uma configuração de dispositivo
Use o procedimento a seguir para excluir uma configuração de dispositivo StorSimple individual do StorSimple Snapshot Manager.

#### <a name="to-delete-a-device-configuration"></a>Para excluir uma configuração de dispositivo
1. Clique no ícone da área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. No painel **escopo** , clique em **dispositivos**. 
3. No painel de **resultados** , clique com o botão direito do mouse no nome do dispositivo e clique em **excluir**. 
4. A mensagem a seguir é exibida. Clique em **Sim** para excluir a configuração ou clique em **não** para cancelar a exclusão.
   
    ![Eliminar configuração do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Alterar uma senha de dispositivo expirada
Você deve inserir uma senha para autenticar um dispositivo StorSimple com o StorSimple Snapshot Manager. Você configura essa senha quando usa a interface do Windows PowerShell para configurar o dispositivo. No entanto, a senha pode expirar. Se isso acontecer, você poderá usar o portal clássico do Azure para alterar a senha. Em seguida, como o dispositivo foi configurado no StorSimple Snapshot Manager antes da senha expirada, você deve autenticar novamente o dispositivo no StorSimple Snapshot Manager.

#### <a name="to-change-the-expired-password"></a>Para alterar a senha expirada
1. No portal clássico do Azure, inicie o serviço StorSimple Manager.
2. Clique em **dispositivos** > **Configurar** para o dispositivo.
3. Role para baixo até a seção Snapshot Manager do StorSimple. Insira uma senha que tenha 14-15 caracteres. Verifique se a senha contém uma mistura de caracteres maiúsculos, minúsculos, numéricos e especiais.
4. Insira a senha novamente para confirmá-la.
5. Clique em **Guardar** na parte inferior da página.

#### <a name="to-re-authenticate-the-device"></a>Para autenticar novamente o dispositivo
1. Inicie o Snapshot Manager StorSimple.
2. No painel **escopo** , clique em **dispositivos**. Uma lista de dispositivos configurados aparece no painel **resultados** .
3. Selecione o dispositivo, clique com o botão direito do mouse e clique em **autenticar**.
4. Na janela **autenticar** , insira a nova senha.
5. Selecione o dispositivo, clique com o botão direito do mouse e selecione **Atualizar dispositivo**. Isso sincroniza o dispositivo com o StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Substituir um dispositivo com falha
Se um dispositivo StorSimple falhar e for substituído por um dispositivo em espera (failover), use as etapas a seguir para se conectar ao novo dispositivo e exibir os backups associados.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Para se conectar a um novo dispositivo após o failover
1. Reconfigure a conexão iSCSI para o novo dispositivo. Para obter instruções, vá para "etapa 7: montar, inicializar e formatar um volume" em [implantar seu dispositivo StorSimple local](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Se o novo dispositivo StorSimple tiver o mesmo endereço IP que o antigo, você poderá conectar a configuração antiga.


1. Pare o serviço de gerenciamento do Microsoft StorSimple:
   
   1. Inicie o Gestor de Servidores.
   2. No painel Gerenciador do Servidor, no menu **ferramentas** , selecione **Serviços**.
   3. Na janela **Serviços** , selecione o **serviço de gerenciamento do Microsoft StorSimple**.
   4. No painel direito, em **serviço de gerenciamento do Microsoft StorSimple**, clique em **parar o serviço**.
2. Remova as informações de configuração relacionadas ao dispositivo antigo:
   
   1. No explorador de arquivos, navegue até C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Exclua os arquivos na pasta BACatalog
3. Reinicie o serviço de gerenciamento do Microsoft StorSimple:
   
   1. No painel Gerenciador do Servidor, no menu **ferramentas** , selecione **Serviços**.
   2. Na janela **Serviços** , selecione o **serviço de gerenciamento do Microsoft StorSimple**.
   3. No painel direito, em **serviço de gerenciamento do Microsoft StorSimple**, clique em **reiniciar o serviço**.
4. Inicie o Snapshot Manager StorSimple.
5. Para configurar o novo dispositivo StorSimple, conclua as etapas na etapa 2: conectar um dispositivo StorSimple em [implantar o storsimple snapshot Manager](storsimple-snapshot-manager-deployment.md).
6. Clique com o botão direito do mouse no nó de nível superior no painel **escopo** (StorSimple snapshot Manager no exemplo) e clique em **alternar exibição de importações**. 
7. Uma mensagem é exibida quando os backups e grupos de volumes importados são visíveis no StorSimple Snapshot Manager. Clique em **OK**.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [usar o snapshot Manager StorSimple para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o snapshot Manager do StorSimple para exibir e gerenciar volumes](storsimple-snapshot-manager-manage-volumes.md).

