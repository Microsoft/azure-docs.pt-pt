---
title: Gerir credenciais de conta de armazenamento StorSimple Virtual Array / Microsoft Docs
description: Explica como pode utilizar a página Configuração do Gestor de Dispositivos StorSimple para adicionar, editar, excluir ou rodar as chaves de segurança para credenciais de conta de armazenamento associadas ao StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 5cedde1e7daa49aaa7a2786c9ad8a65fb8e452f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297581"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Use o StorSimple Device Manager para gerir credenciais de conta de armazenamento para StorSimple Virtual Array

## <a name="overview"></a>Descrição geral
A secção de **Configuração** da lâmina de serviço StorSimple Device Manager da sua StorSimple Virtual Array apresenta os parâmetros de serviço globais que podem ser criados no serviço StorSimple Manager. Estes parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço, e incluem:

* Credenciais da conta de armazenamento
* Registos de controlo de acesso
  
  ![Painel de assistência ao gestor de dispositivos](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Este tutorial explica como pode adicionar, editar ou eliminar credenciais de conta de armazenamento para o seu StorSimple Virtual Array. A informação neste tutorial aplica-se apenas ao StorSimple Virtual Array. Para obter informações sobre como gerir contas de armazenamento em série8000, consulte [Use o serviço StorSimple Manager para gerir a sua conta](storsimple-manage-storage-accounts.md)de armazenamento .

As credenciais de conta de armazenamento contêm as credenciais que o dispositivo utiliza para aceder à sua conta de armazenamento com o seu fornecedor de serviços na nuvem. Para as contas de armazenamento do Microsoft Azure, estas são credenciais como o nome da conta e a chave de acesso primária.

Na lâmina de **credenciais da conta de armazenamento,** todas as credenciais de conta de armazenamento criadas para a subscrição de faturação são apresentadas num formato tabular contendo as seguintes informações:

* **Nome** – O nome único atribuído à conta quando foi criado.
* **SSL ativado** – Se o TLS está ativado e a comunicação dispositivo-nuvem está sobre o canal de segurança.
  
  ![Secção de configuração](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

As tarefas mais comuns relacionadas com credenciais de conta de armazenamento que podem ser executadas na lâmina de **credenciais** da conta de armazenamento são:

* Adicionar uma credencial da conta de armazenamento
* Editar uma credencial de conta de armazenamento
* Eliminar uma credencial de conta de armazenamento

## <a name="types-of-storage-account-credentials"></a>Tipos de credenciais de conta de armazenamento
Existem três tipos de credenciais de conta de armazenamento que podem ser usadas com o seu dispositivo StorSimple.

* **Credenciais** de conta de armazenamento geradas automaticamente – Como o nome sugere, este tipo de credencial de conta de armazenamento é automaticamente gerado quando o serviço é criado pela primeira vez. Para saber mais sobre como esta credencial de conta de armazenamento é criada, consulte [Criar um novo serviço.](storsimple-virtual-array-manage-service.md#create-a-service)
* **credenciais** de conta de armazenamento na subscrição do serviço – Estas são as credenciais da conta de armazenamento Azure que estão associadas à mesma subscrição que a do serviço. Para saber mais sobre como estas credenciais de conta de armazenamento são criadas, consulte sobre as Contas de [Armazenamento Azure](../storage/common/storage-create-storage-account.md).
* **credenciais de conta de armazenamento fora da subscrição do serviço** – Estas são as credenciais da conta de armazenamento Azure que não estão associadas ao seu serviço e provavelmente existiram antes da criação do serviço.

## <a name="add-a-storage-account-credential"></a>Adicionar uma credencial da conta de armazenamento
Pode adicionar uma credencial de conta de armazenamento à configuração do seu serviço StorSimple Device Manager, fornecendo um nome amigável único e credenciais de acesso que estão ligadas à conta de armazenamento. Também tem a opção de ativar o modo De Segurança da Camada de Transporte (TLS), anteriormente conhecido como modo Secure Sockets Layer (SSL), para criar um canal seguro para a comunicação de rede entre o seu dispositivo e a nuvem.

Pode criar várias contas para um determinado fornecedor de serviços na nuvem. Enquanto a credencial da conta de armazenamento está a ser guardada, o serviço tenta comunicar com o seu fornecedor de serviços na nuvem. As credenciais e o material de acesso que forneceu são autenticados neste momento. A credencial da conta de armazenamento só é criada se a autenticação for bem sucedida. Se a autenticação falhar, é apresentada uma mensagem de erro apropriada.

Utilize os seguintes procedimentos para adicionar credenciais de conta de armazenamento Azure:

* Para adicionar uma credencial de conta de armazenamento que tem a mesma subscrição Azure que o serviço Gestor de Dispositivos
* Para adicionar uma credencial de conta de armazenamento Azure que está fora da subscrição do serviço Do Gestor de Dispositivos

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento que tem a mesma subscrição Azure que o serviço Gestor de Dispositivos

1. Navegue para o seu serviço De Gestor de Dispositivos, selecione e clique duas vezes nele. Isto abre a lâmina **de visão geral.**
2. Selecione **credenciais** de conta de armazenamento dentro da secção **Configuração.**
3. Clique em **Adicionar**.
4. Na lâmina adicionar uma conta de **armazenamento,** faça o seguinte:
   
    1. Para **subscrição,** selecione **Current**.
    2. Forneça o nome da sua conta de armazenamento Azure.
    3. Selecione **Ativar** para criar um canal seguro para a comunicação de rede entre o seu dispositivo StorSimple e a nuvem. Selecione **Desativar** apenas se estiver a operar dentro de uma nuvem privada.
    4. Clique em **Adicionar**. É notificado após a criação da conta de armazenamento com sucesso.<br></br>
   
        ![Adicione uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento Azure que está fora da subscrição do serviço Do Gestor de Dispositivos

1. Navegue para o seu serviço De Gestor de Dispositivos, selecione e clique duas vezes nele. Isto abre a lâmina **de visão geral.**
2. Selecione **credenciais** de conta de armazenamento dentro da secção **Configuração.** Isto lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço StorSimple Device Manager.
3. Clique em **Adicionar**.
4. Na lâmina adicionar uma conta de **armazenamento,** faça o seguinte:
   
    1. Para **subscrição,** selecione **Outros**.
   
    2. Forneça o nome da sua conta de armazenamento Azure credencial.
   
    3. Na caixa de texto chave de acesso à **conta de armazenamento,** forneça a chave de acesso primária para a sua credencial de conta de armazenamento Azure. Para obter esta chave, vá ao serviço de Armazenamento Azure, selecione a credencial da sua conta de armazenamento e clique em **Gerir as teclas da conta**. Agora pode copiar a chave de acesso principal.
   
    4. Para ativar o TLS, clique no botão **'Activar'** para criar um canal seguro para a comunicação de rede entre o serviço StorSimple Device Manager e a nuvem. Clique no botão **Desativar** apenas se estiver a funcionar dentro de uma nuvem privada.
   
    5. Clique em **Adicionar**. É notificado após a credencial da conta de armazenamento ser criada com sucesso.

5. A credencial da conta de armazenamento recém-criada é exibida na lâmina de serviço StorSimple Configure Device Manager sob **credenciais de conta de armazenamento**.
   
    ![Adicione uma credencial de conta de armazenamento fora da subscrição do serviço Gestor de Dispositivos](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Editar uma credencial de conta de armazenamento
Pode editar uma credencial de conta de armazenamento utilizada pelo seu dispositivo. Se editar uma credencial de conta de armazenamento que está atualmente em uso, os campos disponíveis para modificar são a chave de acesso e o modo TLS para a credencial da conta de armazenamento. Pode fornecer a nova chave de acesso ao armazenamento ou modificar a seleção do **modo Enable SSL** e guardar as definições atualizadas.

#### <a name="to-edit-a-storage-account-credential"></a>Para editar uma credencial de conta de armazenamento
1. Navegue para o seu serviço De Gestor de Dispositivos, selecione e clique duas vezes nele. Isto abre a lâmina **de visão geral.**
2. Selecione **credenciais** de conta de armazenamento dentro da secção **Configuração.** Isto lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço StorSimple Device Manager.
3. Na lista tabular de credenciais de conta de armazenamento, selecione e clique em dobro na conta que pretende modificar.
4. Na conta de armazenamento credencial **Properties** blade, faça o seguinte:
   
   1. Se necessário, pode modificar a seleção do modo **Enable SSL.**
   2. Pode optar por regenerar as chaves de acesso credenciais da sua conta de armazenamento. Para mais informações, consulte Gerir as chaves de [acesso à conta](../storage/common/storage-account-keys-manage.md)de armazenamento . Forneça a nova chave credencial da conta de armazenamento. Para uma conta de armazenamento Azure, esta é a chave de acesso principal.
   3. Clique em **Guardar** na parte superior da lâmina **Propriedades** para guardar as definições. As definições são atualizadas na lâmina de credenciais da **conta de armazenamento.**
      
      ![Editar uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Eliminar uma credencial de conta de armazenamento
> [!IMPORTANT]
> Só pode eliminar uma credencial de conta de armazenamento se não estiver a ser utilizada. Se estiver em uso uma credencial de conta de armazenamento, é notificado.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Para eliminar uma credencial de conta de armazenamento
1. Navegue para o seu serviço De Gestor de Dispositivos, selecione e clique duas vezes nele. Isto abre a lâmina **de visão geral.**
2. Selecione **credenciais** de conta de armazenamento dentro da secção **Configuração.** Isto lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço StorSimple Device Manager.
3. Na lista tabular de credenciais de conta de armazenamento, selecione e clique em dobro na conta que pretende eliminar.
4. Na conta de armazenamento credencial **Properties** blade, faça o seguinte:
   
   1. Clique em **Apagar** para apagar as credenciais.
   2. Quando solicitado para confirmação, clique **em Sim** para continuar com a eliminação. A listagem tabular é atualizada para refletir as alterações.
      
      ![Eliminar uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Chaves credenciais de conta de armazenamento sincronizada
Por razões de segurança, a rotação da chave é muitas vezes um requisito nos centros de dados. Um administrador do Microsoft Azure pode regenerar ou alterar a chave primária ou secundária acedendo diretamente à credencial da conta de armazenamento (através do serviço de armazenamento Microsoft Azure). O serviço StorSimple Device Manager não vê esta alteração automaticamente.

Para informar o serviço StorSimple Device Manager da alteração, é necessário aceder ao serviço StorSimple Device Manager, aceder à credencial da conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo da qual foi alterada). O serviço recebe então a chave mais recente, encripta as chaves e envia a chave encriptada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Sincronizar as chaves para credenciais de conta de armazenamento na mesma subscrição que o serviço (apenas Azure)
1. Na lâmina de aterragem de serviço, selecione o seu serviço, clique duas vezes no nome do serviço e, em seguida, na secção **Configuração,** clique nas **credenciais da conta de armazenamento**.
2. Na **conta de Armazenamento credenciais** lâmina, na lista de credenciais da conta de armazenamento, selecione a credencial da conta de armazenamento cujas chaves pretende sincronizar.
3. Na lâmina **Propriedades** para a credencial da conta de armazenamento selecionada, faça o seguinte:
   
    1. Clique **em Mais,** e depois clique na **tecla de acesso sincronizado**.
   
    2. Quando solicitado para confirmação, clique na **tecla Sync** para completar a sincronização.
    
4. No serviço StorSimple Device Manager, é necessário atualizar a chave que foi previamente alterada no serviço de Armazenamento Microsoft Azure. Na lâmina da chave da conta de **armazenamento Synchronize,** se a chave de acesso primária foi alterada (regenerada), clique em Primary e, em seguida, clique em **Synckey**. Se a chave secundária foi alterada, clique em **Secundário,** e, em seguida, clique em **Sync Key**.
   
    ![Chave de acesso sincronizado](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Passos seguintes
* Aprenda a administrar o [seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

