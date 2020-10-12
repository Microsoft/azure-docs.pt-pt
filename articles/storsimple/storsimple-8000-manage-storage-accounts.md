---
title: Gerir credenciais de conta de armazenamento, dispositivo da série StorSimple 8000
description: Explica como pode utilizar a página StorSimple Device Manager Configure para adicionar, editar, eliminar ou rodar as teclas de segurança para uma conta de armazenamento.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 306d3a14d759ac73e78626a4c2ec6abc69d3df85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85515034"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Utilize o serviço StorSimple Device Manager para gerir as credenciais da sua conta de armazenamento

## <a name="overview"></a>Descrição geral

A secção **de Configuração** na lâmina de serviço StorSimple Device Manager apresenta todos os parâmetros de serviço globais que podem ser criados no serviço StorSimple Device Manager. Estes parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço, e incluem:

* Credenciais da conta de armazenamento
* Modelos de largura de banda 
* Registos de controlo de acessos 

Este tutorial explica como adicionar, editar ou apagar credenciais de conta de armazenamento ou rodar as chaves de segurança para uma conta de armazenamento.

 ![Lista das credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

As contas de armazenamento contêm as credenciais que o dispositivo StorSimple utiliza para aceder à sua conta de armazenamento com o seu fornecedor de serviços na nuvem. Para as contas de armazenamento do Microsoft Azure, estas são credenciais como o nome da conta e a chave de acesso primário. 

Na lâmina de **credenciais de conta de Armazenamento,** todas as contas de armazenamento criadas para a subscrição de faturação são apresentadas num formato tabular contendo as seguintes informações:

* **Nome** – O nome único atribuído à conta quando foi criado.
* **SSL ativado** – Se o TLS está ativado e a comunicação dispositivo-nuvem está sobre o canal seguro.
* **Utilizado por** – O número de volumes utilizando a conta de armazenamento.

As tarefas mais comuns relacionadas com contas de armazenamento que podem ser realizadas são:

* Adicionar uma conta de armazenamento 
* Editar uma conta de armazenamento 
* Eliminar uma conta do Storage 
* Rotação chave das contas de armazenamento 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Existem três tipos de contas de armazenamento que podem ser usadas com o seu dispositivo StorSimple.

* **Contas de armazenamento geradas automaticamente** – Como o nome sugere, este tipo de conta de armazenamento é gerado automaticamente quando o serviço é criado pela primeira vez. Para saber mais sobre como esta conta de armazenamento é criada, consulte [o Passo 1: Crie um novo serviço](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) no Dispositivo [StorSimple no local.](storsimple-8000-deployment-walkthrough-u2.md) 
* **Contas de armazenamento na subscrição do serviço** – Estas são as contas de armazenamento Azure que estão associadas à mesma subscrição que a do serviço. Para saber mais sobre como estas contas de armazenamento são criadas, consulte [as Contas de Armazenamento do Azure.](../storage/common/storage-create-storage-account.md) 
* **Contas de armazenamento fora da subscrição do serviço** – Estas são as contas de armazenamento Azure que não estão associadas ao seu serviço e provavelmente existiram antes da criação do serviço.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento

Pode adicionar uma conta de armazenamento fornecendo um nome e credenciais de acesso únicos que estão ligados à conta de armazenamento (com o fornecedor de serviços de nuvem especificado). Também tem a opção de permitir que o modo de Segurança da Camada de Transporte (TLS), anteriormente conhecido como modo Secure Sockets Layer (SSL), crie um canal seguro para a comunicação de rede entre o seu dispositivo e a nuvem.

Pode criar várias contas para um determinado fornecedor de serviços na nuvem. Esteja ciente, no entanto, que depois de uma conta de armazenamento ser criada, não pode alterar o fornecedor de serviços de nuvem.

Enquanto a conta de armazenamento está a ser guardada, o serviço tenta comunicar com o seu fornecedor de serviços na nuvem. As credenciais e o material de acesso que forneceu serão autenticados neste momento. Uma conta de armazenamento só é criada se a autenticação for bem sucedida. Se a autenticação falhar, será apresentada uma mensagem de erro apropriada.

Utilize os seguintes procedimentos para adicionar credenciais de conta de armazenamento Azure:

* Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição Azure que o serviço Gestor de Dispositivos
* Para adicionar uma credencial de conta de armazenamento Azure que está fora da subscrição do serviço Device Manager

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento Azure fora da subscrição do serviço StorSimple Device Manager

1. Navegue para o seu serviço StorSimple Device Manager, selecione e clique duas vezes nele. Isto abre a lâmina **de visão geral.**
2. Selecione **credenciais de conta de armazenamento** dentro da secção **Configuração.** Isto lista quaisquer credenciais de conta de armazenamento existentes associadas ao serviço StorSimple Device Manager.
3. Clique em **Adicionar**.
4. Na **lâmina credencial** de conta de armazenamento Adicionar uma folha de credencial de conta de armazenamento, faça o seguinte:
   
    1. Para **subscrição**, selecione **Outros**.
   
    2. Forneça o nome da sua credencial de conta de armazenamento Azure.
   
    3. Na caixa de texto de acesso à **conta de armazenamento,** forneça a chave de acesso primária para a sua credencial de conta de armazenamento Azure. Para obter esta chave, vá ao serviço de Armazenamento Azure, selecione a credencial da sua conta de armazenamento e clique em **Gerir as teclas de conta**. Agora pode copiar a chave de acesso primário.
   
    4. Para ativar o TLS, clique no botão **Ativar** para criar um canal seguro para a comunicação de rede entre o seu serviço StorSimple Device Manager e a nuvem. Clique no botão **Desativar** apenas se estiver a operar dentro de uma nuvem privada.
   
    5. Clique em **Adicionar**. É notificado após a criação com sucesso da credencial da conta de armazenamento.

5. A credencial de conta de armazenamento recentemente criada é exibida na lâmina de serviço StorSimple Configure Device Manager sob **credenciais de conta de armazenamento**.
   


## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Pode editar uma conta de armazenamento que é utilizada por um recipiente de volume. Se editar uma conta de armazenamento que está atualmente em uso, o único campo disponível para modificar é a chave de acesso para a conta de armazenamento. Pode fornecer a nova chave de acesso ao armazenamento e guardar as definições atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

    ![Credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Na lâmina de credenciais de **conta de Armazenamento,** a partir da lista de credenciais de conta de armazenamento, selecione e clique na que deseja editar. 

3. Pode modificar a seleção **Enable SSL.** Também pode clicar em **Mais...** e, em seguida, selecionar **a tecla de acesso Sync para rodar** as teclas de acesso à conta de armazenamento. Aceda à [rotação chave das contas de armazenamento](#key-rotation-of-storage-accounts) para obter mais informações sobre como realizar a rotação da chave. Depois de modificar as definições, clique em **Guardar**. 

    ![Guardar credenciais de conta de armazenamento editadas](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Quando lhe for pedida a confirmação, clique em **Sim**. 

    ![Confirmar modificações](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

As definições serão atualizadas e guardadas para a sua conta de armazenamento. 

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

> [!IMPORTANT]
> Só pode apagar uma conta de armazenamento se não for utilizada por um recipiente de volume. Se uma conta de armazenamento estiver a ser utilizada por um recipiente de volume, primeiro elimine o recipiente de volume e, em seguida, elimine a conta de armazenamento associada.

#### <a name="to-delete-a-storage-account"></a>Para apagar uma conta de armazenamento

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

2. Na lista tabular de contas de armazenamento, paire sobre a conta que pretende eliminar. Clique com o botão direito para invocar o menu de contexto e clique em **Eliminar**.

    ![Eliminar credenciais de conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Quando solicitado para confirmação, clique em **Sim** para continuar com a eliminação. A listagem tabular será atualizada para refletir as alterações.

    ![Confirmar eliminação](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotação chave das contas de armazenamento

Por razões de segurança, a rotação chave é muitas vezes um requisito nos centros de dados. Cada subscrição do Microsoft Azure pode ter uma ou mais contas de armazenamento associadas. O acesso a estas contas é controlado pelas chaves de subscrição e acesso a cada conta de armazenamento. 

Quando cria uma conta de armazenamento, o Microsoft Azure gera duas teclas de acesso de armazenamento de 512 bits que são utilizadas para autenticação quando a conta de armazenamento é acedida. Ter duas teclas de acesso ao armazenamento permite-lhe regenerar as chaves sem interrupção do seu serviço de armazenamento ou acesso a esse serviço. A chave que está atualmente a ser utilizada é a chave *primária* e a chave de backup é referida como a chave *secundária.* Uma destas duas chaves deve ser fornecida quando o seu dispositivo Microsoft Azure StorSimple aceder ao seu fornecedor de serviços de armazenamento em nuvem.

## <a name="what-is-key-rotation"></a>O que é a rotação da chave?

Normalmente, as aplicações utilizam apenas uma das chaves para aceder aos seus dados. Após um certo período de tempo, pode mudar as suas aplicações para utilizar a segunda tecla. Depois de ter trocado as suas aplicações para a chave secundária, pode retirar a primeira chave e, em seguida, gerar uma nova chave. A utilização das duas teclas desta forma permite que as suas aplicações tenham acesso aos dados sem incorrer em qualquer tempo de inatividade.

As chaves da conta de armazenamento são sempre armazenadas no serviço de forma encriptada. No entanto, estes podem ser reiniciados através do serviço StorSimple Device Manager. O serviço pode obter a chave primária e a chave secundária para todas as contas de armazenamento na mesma subscrição, incluindo contas criadas no serviço de Armazenamento, bem como as contas de armazenamento padrão geradas quando o serviço StorSimple Device Manager foi criado pela primeira vez. O serviço StorSimple Device Manager irá sempre obter estas chaves do portal clássico Azure e, em seguida, armazená-las de forma encriptada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação

Um administrador do Microsoft Azure pode regenerar ou alterar a chave primária ou secundária acedendo diretamente à conta de armazenamento (através do serviço microsoft Azure Storage). O serviço StorSimple Device Manager não vê esta alteração automaticamente.

Para informar o serviço StorSimple Device Manager da alteração, terá de aceder ao serviço StorSimple Device Manager, aceder à conta de armazenamento e, em seguida, sincronizar a tecla primária ou secundária (dependendo da qual foi alterada). Em seguida, o serviço obtém a chave mais recente, encripta as chaves e envia a chave encriptada para o dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Para sincronizar chaves para contas de armazenamento na mesma subscrição que o serviço 
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.
2. A partir da listagem tabular das contas de armazenamento, clique na que pretende modificar. 

    ![sincronizar teclas](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. **Clique... Mais** e, em seguida, **selecione Sync a tecla de acesso para rodar**.   

    ![sincronizar teclas](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. No serviço StorSimple Device Manager, é necessário atualizar a chave que foi previamente alterada no serviço de armazenamento Microsoft Azure. Se a chave de acesso primária foi alterada (regenerada), selecione a chave **primária.** Se a chave secundária foi alterada, selecione a **tecla secundária.** Clique **na tecla Sync**.
      
      ![sincronizar teclas](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Será notificado depois de a chave ser sincronizada com sucesso.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar chaves para contas de armazenamento fora da subscrição de serviço
1. Na página **Serviço,** clique no **separador Configurar.**
2. Clique em **Adicionar/Editar Contas de Armazenamento.**
3. Na caixa de diálogo, faça o seguinte:
   
   1. Selecione a conta de armazenamento com a chave de acesso que pretende atualizar.
   2. Terá de atualizar a chave de acesso ao armazenamento no serviço StorSimple Device Manager. Neste caso, pode ver a chave de acesso ao armazenamento. Introduza a nova chave na caixa **chave de acesso à conta de armazenamento.** 
   3. Guarde as alterações. A chave de acesso à sua conta de armazenamento deve agora ser atualizada.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a segurança StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

