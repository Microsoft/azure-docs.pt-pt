---
title: Gerir dispositivos com o StorSimple Snapshot Manager | Documentos da Microsoft
description: Descreve como utilizar o snap-in do MMC do Snapshot Manager do StorSimple para ligar e gerir os dispositivos do StorSimple.
services: storsimple
documentationcenter: ''
author: SharS
manager: timlt
editor: ''
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 51632b8b68640814fc113a94925b6d6deaca4c5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64682493"
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Utilizar o Snapshot Manager do StorSimple para ligar e gerir os dispositivos do StorSimple
## <a name="overview"></a>Descrição geral
Pode utilizar nós no Snapshot Manager do StorSimple **âmbito** painel para verificar se os dados de dispositivos do StorSimple importados e atualizar os dispositivos de armazenamento ligada. Além disso, ao clicar o **dispositivos** nó, pode ver uma lista de dispositivos ligados e informações de estado correspondentes no **resultados** painel.

![Dispositivos ligados](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Dispositivo ligado do Snapshot Manager do StorSimple** 

Consoante a **exibição** seleções, o **resultados** painel mostra as seguintes informações sobre cada dispositivo. (Para obter mais informações sobre como configurar um modo de exibição, aceda a [menu Ver](storsimple-use-snapshot-manager.md#view-menu).

| Coluna de resultados | Descrição |
|:--- |:--- |
| Name |O nome do dispositivo como configurado no portal clássico do Azure |
| Modelo |O número de modelo do dispositivo |
| Version |A versão do software instalado no dispositivo |
| Estado |Se o dispositivo está disponível |
| Última sincronização |Data e hora quando o dispositivo última sincronização |
| Não Serial. |O número de série do dispositivo |

Se com o botão direito a **dispositivos** nó a **âmbito** painel, pode selecionar as seguintes ações:

* Adicionar ou substituir um dispositivo
* Ligar um dispositivo e certifique-se de importações
* Atualizar dispositivos ligados

Se clicar o **dispositivos** nome do nó e o botão direito do mouse em seguida, um dispositivo na **resultados** painel, pode selecionar as seguintes ações:

* Autenticar um dispositivo
* Ver detalhes do dispositivo
* Atualizar um dispositivo
* Eliminar uma configuração de dispositivo
* Alterar uma senha de dispositivo

> [!NOTE]
> Todas essas ações também estão disponíveis no **ações** painel.


Este tutorial explica como utilizar o Snapshot Manager do StorSimple para ligar e gerir dispositivos e executar as seguintes tarefas:

* Adicionar ou substituir um dispositivo
* Ligar um dispositivo e certifique-se de importações
* Atualizar dispositivos ligados
* Autenticar um dispositivo
* Ver detalhes do dispositivo
* Atualizar um dispositivo individual
* Eliminar uma configuração de dispositivo
* Alterar uma senha de dispositivo expirada
* Substituir um dispositivo com falhas

> [!NOTE]
> Para obter informações gerais sobre como utilizar a interface do Snapshot Manager do StorSimple, aceda a [interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Adicionar ou substituir um dispositivo
Utilize o procedimento seguinte para adicionar ou substituir um dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Para adicionar ou substituir um dispositivo
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com botão direito a **dispositivos** nó e, em seguida, clique **configurar um dispositivo**. O **Configure um dispositivo** é apresentada a caixa de diálogo.
   
    ![Configurar um dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Na **dispositivo** pendente caixa, selecione o endereço IP do dispositivo ou dispositivo virtual. 
4. Na **palavra-passe** texto, escreva a palavra-passe do Snapshot Manager do StorSimple que criou para o dispositivo no portal clássico do Azure. Clique em **OK**. Pesquisa do Snapshot Manager do StorSimple para o dispositivo que identificou. 
   
   * Se o dispositivo estiver disponível, o Snapshot Manager do StorSimple adiciona uma ligação.
   * Se o dispositivo estiver indisponível por qualquer motivo, o Snapshot Manager do StorSimple devolve uma mensagem de erro. Clique em **OK** para fechar a mensagem de erro e, em seguida, clique em **Cancelar** para fechar o **configurar um dispositivo** caixa de diálogo.

## <a name="connect-a-device-and-verify-imports"></a>Ligar um dispositivo e certifique-se de importações
Utilize o procedimento seguinte para se ligar um dispositivo StorSimple e certifique-se de que todos os grupos existentes de volume que associou a cópias de segurança são importados.

#### <a name="to-connect-a-device-and-verify-imports"></a>Para ligar um dispositivo e verifique se importa
1. Para ligar um dispositivo para o StorSimple Snapshot Manager, siga as instruções em Adicionar ou substituir um dispositivo. Quando se liga a um dispositivo, o Snapshot Manager do StorSimple responde da seguinte forma:
   
   * Se o dispositivo estiver indisponível por qualquer motivo, o Snapshot Manager do StorSimple devolve uma mensagem de erro. 
   
   * Se o dispositivo estiver disponível, o Snapshot Manager do StorSimple adiciona uma ligação. Quando o dispositivo é selecionado, ele aparece no **resultados** painel e o campo de estado indica que o dispositivo é **disponível**. Snapshot Manager do StorSimple importa quaisquer grupos de volumes configurados para o dispositivo, desde que os grupos de volume associou-se as cópias de segurança. Políticas de cópia de segurança não são importadas. Grupos de volumes que não têm cópias de segurança associadas não são importados.
2. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
3. Com o botão direito no nó superior na **âmbito** painel e, em seguida, clique **Ativar/desativar exibição de importações**.
   
    ![Exibição de importações de alternância selecione](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. O **alternância Imports apresentar** caixa de diálogo aparece e mostra o estado dos grupos de importados volumes e cópias de segurança. Clique em **OK**.

Depois dos grupos de volumes e cópias de segurança são importadas com êxito, pode utilizar o Snapshot Manager do StorSimple para geri-los, apenas ao gerenciamento de grupos de volumes e cópias de segurança que criar e configurar o StorSimple Snapshot Manager. 

## <a name="refresh-connected-devices"></a>Atualizar dispositivos ligados
Utilize o procedimento seguinte para sincronizar os dispositivos do StorSimple ligados com o StorSimple Snapshot Manager.

#### <a name="to-refresh-connected-devices"></a>Para atualizar os dispositivos ligados
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique com botão direito **dispositivos**e, em seguida, clique em **atualizar dispositivos**. Esta sincroniza os dispositivos ligados com o StorSimple Snapshot Manager para que possa visualizar os grupos de volumes e cópias de segurança, incluindo qualquer acréscimos recentes. 
   
    ![Atualizar os dispositivos do StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

O **atualizar dispositivos** ação obtém quaisquer novos grupos de volume e quaisquer cópias de segurança associadas de dispositivos ligados. Ao contrário do **reanalisar volumes** ação disponível para o **Volumes** nó, **atualizar dispositivos** não restaura o registo de cópia de segurança.

## <a name="authenticate-a-device"></a>Autenticar um dispositivo
Utilize o procedimento seguinte para autenticar um dispositivo com StorSimple Snapshot Manager do StorSimple.

#### <a name="to-authenticate-a-device"></a>Para autenticar um dispositivo
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique em **dispositivos**.
3. Na **resultados** painel, faça duplo clique o nome do dispositivo e, em seguida, clique em **Authenticate**.
4. O **Authenticate** é apresentada a caixa de diálogo. Escreva a palavra-passe do dispositivo e, em seguida, clique em **OK**.
   
    ![Autenticar a caixa de diálogo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Ver detalhes do dispositivo
Utilize o procedimento seguinte para ver os detalhes de um dispositivo StorSimple e, se necessário, volte a sincronizar o dispositivo StorSimple Snapshot Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Para ver e ressincronizar detalhes do dispositivo
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique em **dispositivos**.
3. Na **resultados** painel, faça duplo clique o nome do dispositivo e, em seguida, clique em **detalhes**.

4. o **detalhes do dispositivo** é apresentada a caixa de diálogo. Esta caixa mostra o nome, modelo, versão, número de série, estado, destino iSCSI nome qualificado (IQN) e última data de sincronização e tempo.

* Clique em **ressincronizar** para sincronizar o dispositivo.
* Clique em **OK** ou **Cancelar** para fechar a caixa de diálogo.
  
  ![Detalhes do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Atualizar um dispositivo individual
Utilize o procedimento seguinte para ressincronizar a um dispositivo StorSimple individual com o StorSimple Snapshot Manager.

#### <a name="to-refresh-a-device"></a>Para atualizar um dispositivo
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple. 
2. Na **âmbito** painel, clique em **dispositivos**. 
3. Na **resultados** painel, faça duplo clique o nome do dispositivo e, em seguida, clique em **atualizar dispositivo**. Sincroniza o dispositivo StorSimple Snapshot Manager.

## <a name="delete-a-device-configuration"></a>Eliminar uma configuração de dispositivo
Utilize o procedimento seguinte para eliminar uma configuração de dispositivos do StorSimple individuais do Snapshot Manager do StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Para eliminar uma configuração de dispositivo
1. Clique no ícone de área de trabalho para iniciar o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique em **dispositivos**. 
3. Na **resultados** painel, faça duplo clique o nome do dispositivo e, em seguida, clique em **eliminar**. 
4. É apresentada a seguinte mensagem. Clique em **Sim** para eliminar a configuração ou clique em **não** para cancelar a exclusão.
   
    ![Eliminar configuração do dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Alterar uma senha de dispositivo expirada
Tem de introduzir uma palavra-passe para autenticar um dispositivo com StorSimple Snapshot Manager do StorSimple. Configurar esta palavra-passe quando utiliza a interface do Windows PowerShell para configurar o dispositivo. No entanto, a palavra-passe pode expirar. Se isto acontecer, pode utilizar o portal clássico do Azure para alterar a palavra-passe. Em seguida, uma vez que o dispositivo tiver sido configurado no Snapshot Manager do StorSimple antes da palavra-passe expirou, tem de autenticar novamente o dispositivo no Snapshot Manager do StorSimple.

#### <a name="to-change-the-expired-password"></a>Para alterar a palavra-passe expirada
1. No portal clássico do Azure, inicie o serviço StorSimple Manager.
2. Clique em **dispositivos** > **configurar** para o dispositivo.
3. Desloque-se para baixo para a secção do Snapshot Manager do StorSimple. Introduza uma palavra-passe é 14 e 15 carateres. Certifique-se de que a palavra-passe contém uma combinação de carateres em maiúsculas, minúsculas, numéricos e especiais.
4. Volte a introduzir a palavra-passe para confirmar que este.
5. Clique em **Guardar** na parte inferior da página.

#### <a name="to-re-authenticate-the-device"></a>Para voltar a autenticar o dispositivo
1. Inicie o Snapshot Manager do StorSimple.
2. Na **âmbito** painel, clique em **dispositivos**. Aparece uma lista de dispositivos configurados no **resultados** painel.
3. Selecione o dispositivo, botão direito do mouse e clique em **Authenticate**.
4. Na **Authenticate** janela, introduza a palavra-passe nova.
5. Selecione o dispositivo, botão direito do mouse e selecione **atualização dispositivo**. Sincroniza o dispositivo StorSimple Snapshot Manager.

## <a name="replace-a-failed-device"></a>Substituir um dispositivo com falhas
Se um dispositivo StorSimple falhar e é substituído por um dispositivo de modo de espera (ativação pós-falha), utilize os seguintes passos para ligar ao dispositivo novo e ver as cópias de segurança associadas.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Para ligar a um novo dispositivo após a ativação pós-falha
1. Reconfigure a ligação de iSCSI para o novo dispositivo. Para obter instruções, consulte "passo 7: Montar, inicializar e formatar um volume"na [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Se o dispositivo StorSimple novo tem o mesmo endereço IP que o antigo, poderá conseguir ligar-se a configuração antiga.


1. Pare o serviço de gestão do Microsoft StorSimple:
   
   1. Inicie o Gestor de servidor.
   2. No Dashboard do Gestor de servidores, sobre o **ferramentas** menu, selecione **serviços**.
   3. Sobre o **serviços** janela, selecione a **serviço de gestão do Microsoft StorSimple**.
   4. No painel direito, sob **serviço de gestão do Microsoft StorSimple**, clique em **parar o serviço**.
2. Remova as informações de configuração relacionados com o dispositivo antigo:
   
   1. No Explorador de ficheiros, navegue para C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Elimine os ficheiros na pasta BACatalog.
3. Reinicie o serviço de gestão do Microsoft StorSimple:
   
   1. No Dashboard do Gestor de servidores, sobre o **ferramentas** menu, selecione **serviços**.
   2. Sobre o **serviços** janela, selecione a **serviço de gestão do Microsoft StorSimple**.
   3. No painel direito, sob **serviço de gestão do Microsoft StorSimple**, clique em **reiniciar o serviço**.
4. Inicie o Snapshot Manager do StorSimple.
5. Para configurar o novo dispositivo StorSimple, conclua os passos no passo 2: Ligar um dispositivo StorSimple no [Snapshot Manager do StorSimple implementar](storsimple-snapshot-manager-deployment.md).
6. Com o botão direito no nó de nível superior na **âmbito** painel (Snapshot Manager do StorSimple no exemplo) e, em seguida, clique **Ativar/desativar exibição de importações**. 
7. É apresentada uma mensagem quando os grupos de volumes importados e cópias de segurança são visíveis no Snapshot Manager do StorSimple. Clique em **OK**.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [utilizar o Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [utilizar o Snapshot Manager do StorSimple para ver e gerir volumes](storsimple-snapshot-manager-manage-volumes.md).

