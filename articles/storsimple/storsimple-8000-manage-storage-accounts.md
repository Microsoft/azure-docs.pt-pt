---
title: Gerir credenciais de conta de armazenamento, dispositivo da série StorSimple 8000
description: Explica como pode utilizar a página Configuração do Gestor de Dispositivos StorSimple para adicionar, editar, excluir ou rodar as chaves de segurança para uma conta de armazenamento.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 65aa83731be97b59a36a5baf60ea308690a0dcf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297753"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Utilize o serviço StorSimple Device Manager para gerir as credenciais da sua conta de armazenamento

## <a name="overview"></a>Descrição geral

A secção **de Configuração** da lâmina de serviço StorSimple Device Manager apresenta todos os parâmetros de serviço globais que podem ser criados no serviço StorSimple Device Manager. Estes parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço, e incluem:

* Credenciais da conta de armazenamento
* Modelos de largura de banda 
* Registos de controlo de acesso 

Este tutorial explica como adicionar, editar ou apagar credenciais de conta de armazenamento, ou rodar as chaves de segurança para uma conta de armazenamento.

 ![Lista das credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

As contas de armazenamento contêm as credenciais que o dispositivo StorSimple utiliza para aceder à sua conta de armazenamento com o seu fornecedor de serviços na nuvem. Para as contas de armazenamento do Microsoft Azure, estas são credenciais como o nome da conta e a chave de acesso primária. 

Na folha de **credenciais da conta de Armazenamento,** todas as contas de armazenamento criadas para a subscrição de faturação são apresentadas num formato tabular contendo as seguintes informações:

* **Nome** – O nome único atribuído à conta quando foi criado.
* **SSL ativado** – Se o TLS está ativado e a comunicação dispositivo-nuvem está sobre o canal de segurança.
* **Utilizado por** – O número de volumes que utilizam a conta de armazenamento.

As tarefas mais comuns relacionadas com contas de armazenamento que podem ser executadas são:

* Adicione uma conta de armazenamento 
* Editar uma conta de armazenamento 
* Eliminar uma conta do Storage 
* Rotação chave das contas de armazenamento 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Existem três tipos de contas de armazenamento que podem ser usadas com o seu dispositivo StorSimple.

* **Contas de armazenamento geradas automaticamente** – Como o nome sugere, este tipo de conta de armazenamento é automaticamente gerada quando o serviço é criado pela primeira vez. Para saber mais sobre como esta conta de armazenamento é criada, consulte [passo 1: Crie um novo serviço](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) no Implementar o seu dispositivo [StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md). 
* Contas de **armazenamento na subscrição de serviço** – Estas são as contas de armazenamento Azure que estão associadas à mesma subscrição que a do serviço. Para saber mais sobre como estas contas de armazenamento são criadas, consulte sobre as Contas de [Armazenamento Azure](../storage/common/storage-create-storage-account.md). 
* **Contas de armazenamento fora da subscrição do serviço** – Estas são as contas de armazenamento Azure que não estão associadas ao seu serviço e provavelmente existiram antes da criação do serviço.

## <a name="add-a-storage-account"></a>Adicione uma conta de armazenamento

Pode adicionar uma conta de armazenamento fornecendo um nome único e credenciais de acesso amigáveis que estão ligadas à conta de armazenamento (com o prestador de serviços na nuvem especificado). Também tem a opção de ativar o modo De Segurança da Camada de Transporte (TLS), anteriormente conhecido como modo Secure Sockets Layer (SSL), para criar um canal seguro para a comunicação de rede entre o seu dispositivo e a nuvem.

Pode criar várias contas para um determinado fornecedor de serviços na nuvem. Esteja ciente, no entanto, que após a criação de uma conta de armazenamento, não pode alterar o fornecedor de serviços na nuvem.

Enquanto a conta de armazenamento está a ser guardada, o serviço tenta comunicar com o seu fornecedor de serviços na nuvem. As credenciais e o material de acesso que forneceu serão autenticados neste momento. Uma conta de armazenamento só é criada se a autenticação for bem sucedida. Se a autenticação falhar, será apresentada uma mensagem de erro apropriada.

Utilize os seguintes procedimentos para adicionar credenciais de conta de armazenamento Azure:

* Para adicionar uma credencial de conta de armazenamento que tem a mesma subscrição Azure que o serviço Gestor de Dispositivos
* Para adicionar uma credencial de conta de armazenamento Azure que está fora da subscrição do serviço Do Gestor de Dispositivos

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento Azure fora da subscrição do serviço StorSimple Device Manager

1. Navegue para o seu serviço StorSimple Device Manager, selecione e clique duas vezes nele. Isto abre a lâmina **de visão geral.**
2. Selecione **credenciais** de conta de armazenamento dentro da secção **Configuração.** Isto lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço StorSimple Device Manager.
3. Clique em **Adicionar**.
4. Na **conta de armazenamento Adicionar uma lâmina credencial** de conta de armazenamento, faça o seguinte:
   
    1. Para **subscrição,** selecione **Outros**.
   
    2. Forneça o nome da sua conta de armazenamento Azure credencial.
   
    3. Na caixa de texto chave de acesso à **conta de armazenamento,** forneça a chave de acesso primária para a sua credencial de conta de armazenamento Azure. Para obter esta chave, vá ao serviço de Armazenamento Azure, selecione a credencial da sua conta de armazenamento e clique em **Gerir as teclas da conta**. Agora pode copiar a chave de acesso principal.
   
    4. Para ativar o TLS, clique no botão **'Activar'** para criar um canal seguro para a comunicação de rede entre o serviço StorSimple Device Manager e a nuvem. Clique no botão **Desativar** apenas se estiver a funcionar dentro de uma nuvem privada.
   
    5. Clique em **Adicionar**. É notificado após a credencial da conta de armazenamento ser criada com sucesso.

5. A credencial da conta de armazenamento recém-criada é exibida na lâmina de serviço StorSimple Configure Device Manager sob **credenciais de conta de armazenamento**.
   


## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Pode editar uma conta de armazenamento que é utilizada por um recipiente de volume. Se editar uma conta de armazenamento que está atualmente a ser utilizada, o único campo disponível para modificar é a chave de acesso para a conta de armazenamento. Pode fornecer a nova chave de acesso ao armazenamento e guardar as definições atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

    ![Credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Na lâmina de credenciais de **conta de Armazenamento,** a partir da lista de credenciais de conta de armazenamento, selecione e clique na que pretende editar. 

3. Pode modificar a seleção **Enable SSL.** Também pode clicar em **Mais...** e, em seguida, selecionar **a chave de acesso Sync para rodar** as chaves de acesso à sua conta de armazenamento. Vá para a [rotação chave das contas de armazenamento](#key-rotation-of-storage-accounts) para obter mais informações sobre como realizar a rotação da chave. Depois de ter modificado as definições, clique em **Guardar**. 

    ![Guardar credenciais de conta de armazenamento editadas](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Quando lhe for pedida a confirmação, clique em **Sim**. 

    ![Confirmar modificações](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

As definições serão atualizadas e guardadas para a sua conta de armazenamento. 

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

> [!IMPORTANT]
> Só é possível eliminar uma conta de armazenamento se não for utilizada por um recipiente de volume. Se uma conta de armazenamento estiver a ser utilizada por um recipiente de volume, primeiro elimine o recipiente de volume e, em seguida, elimine a conta de armazenamento associada.

#### <a name="to-delete-a-storage-account"></a>Para apagar uma conta de armazenamento

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

2. Na lista tabular de contas de armazenamento, paire sobre a conta que pretende eliminar. Clique à direita para invocar o menu de contexto e clique **em Eliminar**.

    ![Eliminar credenciais de conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Quando solicitado para confirmação, clique **em Sim** para continuar com a eliminação. A listagem tabular será atualizada para refletir as alterações.

    ![Confirmar eliminação](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotação chave das contas de armazenamento

Por razões de segurança, a rotação da chave é muitas vezes um requisito nos centros de dados. Cada subscrição do Microsoft Azure pode ter uma ou mais contas de armazenamento associadas. O acesso a estas contas é controlado pelas chaves de subscrição e acesso de cada conta de armazenamento. 

Ao criar uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits que são usadas para autenticação quando a conta de armazenamento é acedida. Ter duas chaves de acesso de armazenamento permite-lhe regenerar as chaves sem interrupção do seu serviço de armazenamento ou acesso a esse serviço. A chave que está atualmente a ser utilizada é a chave *principal* e a chave de reserva é referida como a chave *secundária.* Uma destas duas teclas deve ser fornecida quando o seu dispositivo Microsoft Azure StorSimple aceder ao seu fornecedor de serviços de armazenamento em nuvem.

## <a name="what-is-key-rotation"></a>O que é a rotação da chave?

Normalmente, as aplicações utilizam apenas uma das chaves para aceder aos seus dados. Após um certo período de tempo, pode fazer com que as suas aplicações passem para utilizar a segunda tecla. Depois de ter mudado as suas aplicações para a chave secundária, pode retirar a primeira chave e depois gerar uma nova chave. A utilização das duas teclas desta forma permite às suas aplicações o acesso aos dados sem incorrer em qualquer tempo de inatividade.

As chaves da conta de armazenamento são sempre armazenadas no serviço de forma encriptada. No entanto, estes podem ser reiniciados através do serviço StorSimple Device Manager. O serviço pode obter a chave primária e a chave secundária para todas as contas de armazenamento na mesma subscrição, incluindo contas criadas no serviço de Armazenamento, bem como as contas de armazenamento predefinidas geradas quando o serviço StorSimple Device Manager foi o primeiro criado. O serviço StorSimple Device Manager receberá sempre estas chaves do portal clássico Azure e, em seguida, armazená-las-á de forma encriptada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação

Um administrador do Microsoft Azure pode regenerar ou alterar a chave primária ou secundária acedendo diretamente à conta de armazenamento (através do serviço de armazenamento Microsoft Azure). O serviço StorSimple Device Manager não vê esta alteração automaticamente.

Para informar o serviço StorSimple Device Manager da alteração, terá de aceder ao serviço StorSimple Device Manager, aceder à conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo da alteração de uma delas). O serviço recebe então a chave mais recente, encripta as chaves e envia a chave encriptada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Sincronizar as chaves para contas de armazenamento na mesma subscrição que o serviço 
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.
2. A partir da listagem tabular das contas de armazenamento, clique na que pretende modificar. 

    ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. **Clique... Mais** e depois selecione **a tecla de acesso Sync para rodar**.   

    ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. No serviço StorSimple Device Manager, é necessário atualizar a chave que foi previamente alterada no serviço de Armazenamento Microsoft Azure. Se a chave de acesso primária tiver sido alterada (regenerada), selecione a chave **primária.** Se a chave secundária foi alterada, selecione a tecla **secundária.** Clique na **tecla Sync**.
      
      ![sincronizar chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Será notificado depois de a chave ser sincronizada com sucesso.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Sincronizar chaves para contas de armazenamento fora da subscrição do serviço
1. Na página **Serviços,** clique no separador **Configurar.**
2. Clique em **Adicionar/Editar Contas de Armazenamento**.
3. Na caixa de diálogo, faça o seguinte:
   
   1. Selecione a conta de armazenamento com a chave de acesso que pretende atualizar.
   2. Terá de atualizar a chave de acesso ao armazenamento no serviço StorSimple Device Manager. Neste caso, pode ver a chave de acesso ao armazenamento. Introduza a nova tecla na caixa chave de acesso à conta de **armazenamento.** 
   3. Guarde as alterações. A chave de acesso à sua conta de armazenamento deve agora ser atualizada.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [segurança StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

