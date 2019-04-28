---
title: Gerir credenciais de conta de armazenamento do StorSimple Virtual Array | Documentos da Microsoft
description: Explica como pode utilizar a página de configuração do StorSimple Device Manager para adicionar, editar, eliminar ou rodar as chaves de segurança de credenciais de conta de armazenamento associadas a matriz Virtual StorSimple.
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
ms.openlocfilehash: a992851deda0659509c0ee4ea5de76b19734f017
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128843"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Utilize o StorSimple Device Manager para gerir as credenciais da conta de armazenamento do StorSimple Virtual Array

## <a name="overview"></a>Descrição geral
O **configuração** seção do painel do serviço StorSimple Device Manager do StorSimple Virtual Array apresenta os parâmetros de serviço global que podem ser criados no serviço StorSimple Manager. Esses parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço e incluem:

* Credenciais da conta de armazenamento
* Registos de controlo de acesso
  
  ![Dashboard de serviço Gestor de dispositivos](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Este tutorial explica como pode adicionar, editar ou eliminar as credenciais da conta de armazenamento para a sua matriz Virtual StorSimple. As informações neste tutorial aplica-se apenas para a matriz Virtual StorSimple. Para obter informações sobre como gerir contas de armazenamento na 8000 série, consulte [utilizar o serviço StorSimple Manager para gerir a sua conta de armazenamento](storsimple-manage-storage-accounts.md).

As credenciais da conta de armazenamento contém as credenciais que o dispositivo utiliza para aceder à sua conta de armazenamento com o seu fornecedor de serviços cloud. Para contas de armazenamento do Microsoft Azure, estas são as credenciais, tais como o nome da conta e a chave de acesso primária.

Sobre o **as credenciais da conta de armazenamento** painel, todas as credenciais de conta de armazenamento que são criadas para a subscrição de faturação são apresentadas em formato de tabela que contém as seguintes informações:

* **Nome** – o nome exclusivo atribuído à conta em que foi criada.
* **SSL ativado** – SSL o se estiver ativada e a comunicação do dispositivo para a cloud é através do canal seguro.
  
  ![Seção de configuração](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

As tarefas mais comuns relacionados com as credenciais da conta de armazenamento podem ser realizadas no **as credenciais da conta de armazenamento** painel são:

* Adicionar uma credencial da conta de armazenamento
* Editar uma credencial de conta de armazenamento
* Eliminar uma credencial de conta de armazenamento

## <a name="types-of-storage-account-credentials"></a>Tipos de credenciais de conta de armazenamento
Existem três tipos de credenciais de conta de armazenamento que podem ser utilizados com o dispositivo StorSimple.

* **As credenciais da conta de armazenamento gerados automaticamente** – como o nome sugere, este tipo de credencial da conta de armazenamento é gerado automaticamente quando o serviço é criado. Para saber mais sobre como esta credencial de conta de armazenamento é criado, veja [criar um novo serviço](storsimple-virtual-array-manage-service.md#create-a-service).
* **as credenciais da conta de armazenamento na subscrição do serviço** – estas são as credenciais da conta de armazenamento do Azure que estão associadas a mesma subscrição do serviço. Para saber mais sobre o armazenamento de como essas credenciais da conta são criadas, veja [sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* **as credenciais da conta de armazenamento fora da subscrição do serviço** – estas são as credenciais da conta de armazenamento do Azure que não estão associadas ao seu serviço e provavelmente existia antes do serviço foi criado.

## <a name="add-a-storage-account-credential"></a>Adicionar uma credencial da conta de armazenamento
Pode adicionar uma credencial de conta de armazenamento para a sua configuração do serviço StorSimple Device Manager, fornecendo um nome amigável exclusivo e credenciais de acesso que estão ligadas à conta de armazenamento. Tem também a opção de ativar o modo do secure sockets layer (SSL) para criar um canal seguro para a comunicação de rede entre o seu dispositivo e a cloud.

Pode criar várias contas para fornecedor de serviços cloud determinado. Enquanto a credencial da conta de armazenamento está a ser guardada, o serviço tenta comunicar com o seu fornecedor de serviços cloud. As credenciais e o material de acesso que forneceu são autenticados neste momento. Uma credencial de conta de armazenamento é criada apenas se a autenticação for concluída com êxito. Se a autenticação falhar, em seguida, é apresentada uma mensagem de erro apropriada.

Utilize os procedimentos seguintes para adicionar as credenciais da conta de armazenamento do Azure:

* Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição do Azure como o serviço de Gestor de dispositivos
* Para adicionar uma credencial de conta de armazenamento do Azure que está fora da subscrição do serviço de Gestor de dispositivos

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição do Azure como o serviço de Gestor de dispositivos

1. Navegue para o serviço de Gestor de dispositivos, selecione e clique duas vezes nele. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro do **configuração** secção.
3. Clique em **Adicionar**.
4. Na **adicionar uma conta de armazenamento** painel, faça o seguinte:
   
    1. Para **subscrição**, selecione **atual**.
    2. Forneça o nome da sua conta de armazenamento do Azure.
    3. Selecione **ativar** para criar um canal seguro para comunicação de rede entre o dispositivo StorSimple e a cloud. Selecione **desativar** apenas se estiver a utilizar uma nuvem privada.
    4. Clique em **Adicionar**. Será notificado depois da conta de armazenamento for criada com êxito.<br></br>
   
        ![Adicionar uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento do Azure que está fora da subscrição do serviço de Gestor de dispositivos

1. Navegue para o serviço de Gestor de dispositivos, selecione e clique duas vezes nele. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro do **configuração** secção. Isto apresenta uma lista de quaisquer credenciais de conta de armazenamento existentes associados ao serviço StorSimple Device Manager.
3. Clique em **Adicionar**.
4. Na **adicionar uma conta de armazenamento** painel, faça o seguinte:
   
    1. Para **subscrição**, selecione **outros**.
   
    2. Forneça o nome da credencial da conta de armazenamento do Azure.
   
    3. Na **chave de acesso da conta de armazenamento** texto caixa, forneça a chave de acesso primária para a credencial da conta de armazenamento do Azure. Para obter esta chave, vá para o serviço de armazenamento do Azure, selecione a credencial da conta de armazenamento e clique em **gerir chaves de conta**. Agora, pode copiar a chave de acesso primária.
   
    4. Para ativar o SSL, clique nas **ativar** botão para criar um canal seguro para comunicação de rede entre o serviço StorSimple Device Manager e a cloud. Clique nas **desativar** botão apenas se estiver a utilizar uma nuvem privada.
   
    5. Clique em **Adicionar**. Será notificado quando a credencial da conta de armazenamento é criada com êxito.

5. A credencial da conta de armazenamento recentemente criada é apresentada no painel do serviço do Gestor de dispositivos de configurar o StorSimple sob **as credenciais da conta de armazenamento**.
   
    ![Adicionar uma credencial de conta de armazenamento fora da subscrição do serviço de Gestor de dispositivos](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Editar uma credencial de conta de armazenamento
Pode editar uma credencial de conta de armazenamento utilizada pelo seu dispositivo. Se editar uma credencial de conta de armazenamento que está atualmente em utilização, os campos disponíveis para modificar são a chave de acesso e o modo SSL para a credencial da conta de armazenamento. Pode fornecer a chave de acesso de armazenamento novo ou modificar os **modo de ativar SSL** seleção e guardar as definições atualizadas.

#### <a name="to-edit-a-storage-account-credential"></a>Para editar uma credencial de conta de armazenamento
1. Navegue para o serviço de Gestor de dispositivos, selecione e clique duas vezes nele. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro do **configuração** secção. Isto apresenta uma lista de quaisquer credenciais de conta de armazenamento existentes associados ao serviço StorSimple Device Manager.
3. Na lista tabular de credenciais de conta de armazenamento, selecione e clique duas vezes a conta que pretende modificar.
4. Na credencial da conta de armazenamento **propriedades** painel, faça o seguinte:
   
   1. Se necessário, pode modificar os **ativar SSL** seleção do modo.
   2. Pode optar por voltar a gerar as chaves de acesso de credencial de conta de armazenamento. Para obter mais informações, consulte [regenerar as chaves de conta de armazenamento](../storage/common/storage-account-manage.md#access-keys). Forneça a chave de credencial de conta de armazenamento nova. Para uma conta de armazenamento do Azure, esta é a chave de acesso primária.
   3. Clique em **salvar** na parte superior a **propriedades** painel para guardar as definições. As definições são atualizadas sobre o **as credenciais da conta de armazenamento** painel.
      
      ![Editar uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Eliminar uma credencial de conta de armazenamento
> [!IMPORTANT]
> Pode eliminar uma credencial de conta de armazenamento apenas se não está em utilização. Se uma credencial de conta de armazenamento estiver em uso, será notificado.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Para eliminar uma credencial de conta de armazenamento
1. Navegue para o serviço de Gestor de dispositivos, selecione e clique duas vezes nele. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro do **configuração** secção. Isto apresenta uma lista de quaisquer credenciais de conta de armazenamento existentes associados ao serviço StorSimple Device Manager.
3. Na lista tabular de credenciais de conta de armazenamento, selecione e clique duas vezes a conta que pretende eliminar.
4. Na credencial da conta de armazenamento **propriedades** painel, faça o seguinte:
   
   1. Clique em **eliminar** para eliminar as credenciais.
   2. Quando lhe for pedida confirmação, clique em **Sim** para continuar com a eliminação. Da lista tabular é atualizada para refletir as alterações.
      
      ![Eliminar uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Sincronizar as chaves de credenciais de conta de armazenamento
Por motivos de segurança, rotação de chaves, muitas vezes, é um requisito nos centros de dados. Um administrador do Microsoft Azure pode voltar a gerar ou alterar a chave primária ou secundária ao aceder diretamente a credencial da conta de armazenamento (através do serviço de armazenamento do Microsoft Azure). O serviço StorSimple Device Manager não vê esta alteração automaticamente.

Para informar o serviço de Gestor de dispositivos do StorSimple da alteração, precisa para aceder ao serviço StorSimple Device Manager, a credencial da conta de armazenamento de acesso e, em seguida, sincronizar a chave primária ou secundária (dependendo do que foi alterado). O serviço, em seguida, obtém a chave mais recente, criptografa as chaves e envia a chave de encriptação no dispositivo.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Ao sincronizar as chaves para as credenciais da conta de armazenamento na mesma subscrição que o serviço (apenas para o Azure)
1. No painel serviço de destino, selecione o seu serviço, faça duplo clique o nome do serviço e, em seguida, no **Configuration** secção, clique em **credenciais de conta de armazenamento**.
2. Sobre o **as credenciais da conta de armazenamento** painel, na lista de credenciais, selecionadas a conta de armazenamento de credenciais cujas chaves que pretende sincronizar.
3. Na **propriedades** painel para a credencial da conta de armazenamento selecionada, efetue o seguinte procedimento:
   
    1. Clique em **mais**e, em seguida, clique em **chave de acesso de sincronização**.
   
    2. Quando lhe for pedida confirmação, clique em **sincronizar chave** para concluir a sincronização.
    
4. No serviço StorSimple Device Manager, terá de atualizar a chave que anteriormente foi alterada no serviço de armazenamento do Microsoft Azure. Na **sincronizar a chave de conta de armazenamento** painel, se a chave de acesso primária foi alterada (regenerada), clique em principal e, em seguida, clique em **sincronizar chave**. Se a chave secundária tiver sido alterada, clique em **secundário**e, em seguida, clique em **sincronizar chave**.
   
    ![Chave de acesso de sincronização](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [administrar a sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

