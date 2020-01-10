---
title: Gerenciar credenciais da conta de armazenamento do StorSimple virtual array | Microsoft Docs
description: Explica como você pode usar a página Configurar Device Manager do StorSimple para adicionar, editar, excluir ou girar as chaves de segurança para as credenciais da conta de armazenamento associadas à matriz virtual StorSimple.
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
ms.openlocfilehash: ac539b44f09663e6eac5651646d3c9cd02e45b3c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360009"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Usar o StorSimple Device Manager para gerenciar as credenciais da conta de armazenamento para o StorSimple virtual array

## <a name="overview"></a>Visão geral
A seção de **configuração** da folha de serviço storsimple Device Manager da sua matriz virtual storsimple apresenta os parâmetros de serviço globais que podem ser criados no serviço StorSimple Manager. Esses parâmetros podem ser aplicados a todos os dispositivos conectados ao serviço e incluem:

* Credenciais da conta de armazenamento
* Registros de controle de acesso
  
  ![Painel do serviço Device Manager](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

Este tutorial explica como você pode adicionar, editar ou excluir credenciais de conta de armazenamento para sua matriz virtual StorSimple. As informações neste tutorial se aplicam somente à matriz virtual StorSimple. Para obter informações sobre como gerenciar contas de armazenamento na série 8000, consulte [usar o serviço de StorSimple Manager para gerenciar sua conta de armazenamento](storsimple-manage-storage-accounts.md).

As credenciais da conta de armazenamento contêm as credenciais que o dispositivo usa para acessar sua conta de armazenamento com seu provedor de serviços de nuvem. Para Microsoft Azure contas de armazenamento, essas são credenciais como o nome da conta e a chave de acesso primária.

Na folha **credenciais da conta de armazenamento** , todas as credenciais da conta de armazenamento criadas para a assinatura de cobrança são exibidas em um formato tabular contendo as seguintes informações:

* **Nome** – o nome exclusivo atribuído à conta quando ele foi criado.
* **SSL habilitado** – se o SSL está habilitado e a comunicação do dispositivo para a nuvem é por meio do canal seguro.
  
  ![Seção de configuração](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

As tarefas mais comuns relacionadas às credenciais da conta de armazenamento que podem ser executadas na folha de **credenciais da conta de armazenamento** são:

* Adicionar uma credencial da conta de armazenamento
* Editar uma credencial de conta de armazenamento
* Excluir uma credencial de conta de armazenamento

## <a name="types-of-storage-account-credentials"></a>Tipos de credenciais de conta de armazenamento
Há três tipos de credenciais de conta de armazenamento que podem ser usadas com seu dispositivo StorSimple.

* **Credenciais da conta de armazenamento geradas automaticamente** – como o nome sugere, esse tipo de credencial de conta de armazenamento é gerado automaticamente quando o serviço é criado pela primeira vez. Para saber mais sobre como essa credencial de conta de armazenamento é criada, consulte [criar um novo serviço](storsimple-virtual-array-manage-service.md#create-a-service).
* **credenciais da conta de armazenamento na assinatura do serviço** – essas são as credenciais da conta de armazenamento do Azure associadas à mesma assinatura que o serviço. Para saber mais sobre como essas credenciais de conta de armazenamento são criadas, consulte [sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md).
* **credenciais da conta de armazenamento fora da assinatura do serviço** – essas são as credenciais da conta de armazenamento do Azure que não estão associadas ao seu serviço e que provavelmente existiam antes da criação do serviço.

## <a name="add-a-storage-account-credential"></a>Adicionar uma credencial da conta de armazenamento
Você pode adicionar uma credencial de conta de armazenamento à configuração do serviço StorSimple Device Manager fornecendo um nome amigável exclusivo e credenciais de acesso que estão vinculadas à conta de armazenamento. Você também tem a opção de habilitar o modo SSL (Secure Sockets Layer) para criar um canal seguro para a comunicação de rede entre o dispositivo e a nuvem.

Você pode criar várias contas para um determinado provedor de serviços de nuvem. Enquanto a credencial da conta de armazenamento está sendo salva, o serviço tenta se comunicar com seu provedor de serviços de nuvem. As credenciais e o material de acesso que você forneceu são autenticados neste momento. Uma credencial de conta de armazenamento será criada somente se a autenticação for realizada com sucesso. Se a autenticação falhar, uma mensagem de erro apropriada será exibida.

Use os procedimentos a seguir para adicionar as credenciais da conta de armazenamento do Azure:

* Para adicionar uma credencial de conta de armazenamento que tenha a mesma assinatura do Azure que o serviço de Device Manager
* Para adicionar uma credencial de conta de armazenamento do Azure que está fora da assinatura do serviço de Device Manager

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Para adicionar uma credencial de conta de armazenamento que tenha a mesma assinatura do Azure que o serviço de Device Manager

1. Navegue até o serviço Device Manager, selecione e clique duas vezes nele. Isso abre a folha **visão geral** .
2. Selecione **as credenciais da conta de armazenamento** na seção **configuração** .
3. Clique em **Adicionar**.
4. Na folha **Adicionar uma conta de armazenamento** , faça o seguinte:
   
    1. Para **assinatura**, selecione **atual**.
    2. Forneça o nome da sua conta de armazenamento do Azure.
    3. Selecione **habilitar** para criar um canal seguro para comunicação de rede entre o dispositivo StorSimple e a nuvem. Selecione **desabilitar** somente se você estiver operando em uma nuvem privada.
    4. Clique em **Adicionar**. Você será notificado depois que a conta de armazenamento for criada com êxito.<br></br>
   
        ![Adicionar uma credencial de conta de armazenamento existente](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento do Azure que está fora da assinatura do serviço de Device Manager

1. Navegue até o serviço Device Manager, selecione e clique duas vezes nele. Isso abre a folha **visão geral** .
2. Selecione **as credenciais da conta de armazenamento** na seção **configuração** . Isso lista todas as credenciais de conta de armazenamento existentes associadas ao serviço StorSimple Device Manager.
3. Clique em **Adicionar**.
4. Na folha **Adicionar uma conta de armazenamento** , faça o seguinte:
   
    1. Para **assinatura**, selecione **outro**.
   
    2. Forneça o nome da sua credencial de conta de armazenamento do Azure.
   
    3. Na caixa de texto **chave de acesso da conta de armazenamento** , forneça a chave de acesso primária para sua credencial de conta de armazenamento do Azure. Para obter essa chave, vá para o serviço de armazenamento do Azure, selecione sua credencial de conta de armazenamento e clique em **gerenciar chaves de conta**. Agora você pode copiar a chave de acesso primária.
   
    4. Para habilitar o SSL, clique no botão **habilitar** para criar um canal seguro para comunicação de rede entre o serviço StorSimple Device Manager e a nuvem. Clique no botão **desabilitar** somente se você estiver operando em uma nuvem privada.
   
    5. Clique em **Adicionar**. Você será notificado depois que a credencial da conta de armazenamento for criada com êxito.

5. A credencial da conta de armazenamento recém-criada é exibida na folha de serviço do Device Manager de configuração do StorSimple em **credenciais da conta de armazenamento**.
   
    ![Adicionar uma credencial de conta de armazenamento fora da assinatura do serviço de Device Manager](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Editar uma credencial de conta de armazenamento
Você pode editar uma credencial de conta de armazenamento usada pelo seu dispositivo. Se você editar uma credencial de conta de armazenamento que está em uso no momento, os campos disponíveis para modificar serão a chave de acesso e o modo SSL para a credencial da conta de armazenamento. Você pode fornecer a nova chave de acesso de armazenamento ou modificar a seleção **habilitar modo SSL** e salvar as configurações atualizadas.

#### <a name="to-edit-a-storage-account-credential"></a>Para editar uma credencial de conta de armazenamento
1. Navegue até o serviço Device Manager, selecione e clique duas vezes nele. Isso abre a folha **visão geral** .
2. Selecione **as credenciais da conta de armazenamento** na seção **configuração** . Isso lista todas as credenciais de conta de armazenamento existentes associadas ao serviço StorSimple Device Manager.
3. Na lista tabular de credenciais de conta de armazenamento, selecione e clique duas vezes na conta que você deseja modificar.
4. Na folha **Propriedades** da credencial da conta de armazenamento, faça o seguinte:
   
   1. Se necessário, você pode modificar a seleção **habilitar modo SSL** .
   2. Você pode optar por regenerar suas chaves de acesso de credencial de conta de armazenamento. Para obter mais informações, consulte [gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md). Forneça a nova chave de credencial da conta de armazenamento. Para uma conta de armazenamento do Azure, essa é a chave de acesso primária.
   3. Clique em **salvar** na parte superior da folha **Propriedades** para salvar as configurações. As configurações são atualizadas na folha **credenciais da conta de armazenamento** .
      
      ![Editar uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Excluir uma credencial de conta de armazenamento
> [!IMPORTANT]
> Você só poderá excluir uma credencial de conta de armazenamento se ela não estiver em uso. Se uma credencial de conta de armazenamento estiver em uso, você será notificado.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>Para excluir uma credencial de conta de armazenamento
1. Navegue até o serviço Device Manager, selecione e clique duas vezes nele. Isso abre a folha **visão geral** .
2. Selecione **as credenciais da conta de armazenamento** na seção **configuração** . Isso lista todas as credenciais de conta de armazenamento existentes associadas ao serviço StorSimple Device Manager.
3. Na lista tabular de credenciais de conta de armazenamento, selecione e clique duas vezes na conta que você deseja excluir.
4. Na folha **Propriedades** da credencial da conta de armazenamento, faça o seguinte:
   
   1. Clique em **excluir** para excluir as credenciais.
   2. Quando a confirmação for solicitada, clique em **Sim** para continuar com a exclusão. A listagem de tabela é atualizada para refletir as alterações.
      
      ![Excluir uma credencial de conta de armazenamento](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Sincronizando chaves de credencial de conta de armazenamento
Por motivos de segurança, a rotação de chaves é geralmente um requisito em data centers. Um administrador de Microsoft Azure pode regenerar ou alterar a chave primária ou secundária acessando diretamente a credencial da conta de armazenamento (por meio do serviço Armazenamento do Microsoft Azure). O serviço StorSimple Device Manager não vê essa alteração automaticamente.

Para informar o serviço de Device Manager do StorSimple da alteração, você precisa acessar o serviço de Device Manager do StorSimple, acessar a credencial da conta de armazenamento e sincronizar a chave primária ou secundária (dependendo de qual foi alterada). Em seguida, o serviço Obtém a chave mais recente, criptografa as chaves e envia a chave criptografada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>Para sincronizar chaves para credenciais de conta de armazenamento na mesma assinatura que o serviço (somente Azure)
1. Na folha inicial do serviço, selecione seu serviço, clique duas vezes no nome do serviço e, na seção **configuração** , clique em **credenciais da conta de armazenamento**.
2. Na folha **credenciais da conta de armazenamento** , na lista de credenciais da conta de armazenamento, selecione a credencial da conta de armazenamento cujas chaves você deseja sincronizar.
3. Na folha **Propriedades** da credencial da conta de armazenamento selecionada, faça o seguinte:
   
    1. Clique em **mais**e, em seguida, clique em **sincronizar chave de acesso**.
   
    2. Quando a confirmação for solicitada, clique em **sincronizar chave** para concluir a sincronização.
    
4. No serviço StorSimple Device Manager, você precisa atualizar a chave que foi alterada anteriormente no serviço Armazenamento do Microsoft Azure. Na folha **sincronizar a chave da conta de armazenamento** , se a chave de acesso primária foi alterada (regenerada), clique em primário e em **sincronizar chave**. Se a chave secundária tiver sido alterada, clique em **secundária**e, em seguida, clique em **sincronizar chave**.
   
    ![Sincronizar chave de acesso](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [administrar sua matriz virtual StorSimple](storsimple-ova-web-ui-admin.md).

