---
title: Gerir as suas credenciais de conta de armazenamento do StorSimple para dispositivos da série StorSimple 8000 do Microsoft Azure | Documentos da Microsoft
description: Explica como pode utilizar a página de configuração do StorSimple Device Manager para adicionar, editar, eliminar ou rodar as chaves de segurança para uma conta de armazenamento.
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
ms.openlocfilehash: 53aa442b86f5c82ded2f212a64f43852e6b3d2c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60632782"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Utilizar o serviço StorSimple Device Manager para gerir as credenciais da conta de armazenamento

## <a name="overview"></a>Descrição geral

O **configuração** secção no painel do serviço StorSimple Device Manager apresenta todos os parâmetros de serviço global que podem ser criados no serviço StorSimple Device Manager. Esses parâmetros podem ser aplicados a todos os dispositivos ligados ao serviço e incluem:

* Credenciais da conta de armazenamento
* Modelos de largura de banda 
* Registos de controlo de acesso 

Este tutorial explica como adicionar, editar, eliminar as credenciais da conta de armazenamento ou rodar as chaves de segurança para uma conta de armazenamento.

 ![Lista das credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Contas de armazenamento contém as credenciais que o dispositivo StorSimple utiliza para aceder à sua conta de armazenamento com o seu fornecedor de serviços cloud. Para contas de armazenamento do Microsoft Azure, estas são as credenciais, tais como o nome da conta e a chave de acesso primária. 

Sobre o **as credenciais da conta de armazenamento** painel, todas as contas de armazenamento que são criadas para a subscrição de faturação são apresentadas em formato de tabela que contém as seguintes informações:

* **Nome** – o nome exclusivo atribuído à conta em que foi criada.
* **SSL ativado** – SSL o se estiver ativada e a comunicação do dispositivo para a cloud é através do canal seguro.
* **Utilizado por** – o número de volumes com a conta de armazenamento.

As tarefas mais comuns relacionadas com as contas de armazenamento que podem ser executadas são:

* Adicionar uma conta de armazenamento 
* Editar uma conta de armazenamento 
* Eliminar uma conta do Storage 
* Rotação de chaves de contas de armazenamento 

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

Existem três tipos de contas de armazenamento que podem ser utilizados com o dispositivo StorSimple.

* **Contas de armazenamento gerados automaticamente** – como o nome sugere, este tipo de conta de armazenamento é gerado automaticamente quando o serviço é criado. Para saber mais sobre como criar esta conta de armazenamento, consulte [passo 1: Criar um novo serviço](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) no [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md). 
* **Contas de armazenamento na subscrição do serviço** – estas são as contas de armazenamento do Azure que estão associadas a mesma subscrição do serviço. Para saber mais sobre o armazenamento de como essas contas são criadas, veja [sobre contas de armazenamento do Azure](../storage/common/storage-create-storage-account.md). 
* **Contas de armazenamento fora da subscrição do serviço** – estas são as contas de armazenamento do Azure que não estão associadas ao seu serviço e provavelmente existia antes do serviço foi criado.

## <a name="add-a-storage-account"></a>Adicionar uma conta de armazenamento

Pode adicionar uma conta de armazenamento, fornecendo um nome amigável exclusivo e credenciais de acesso que estão ligadas à conta de armazenamento (com o fornecedor de serviços em nuvem especificado). Tem também a opção de ativar o modo do secure sockets layer (SSL) para criar um canal seguro para a comunicação de rede entre o seu dispositivo e a cloud.

Pode criar várias contas para fornecedor de serviços cloud determinado. Tenha em atenção, no entanto, que depois é criada uma conta de armazenamento, é possível alterar o fornecedor de serviços cloud.

Enquanto a conta de armazenamento está a ser guardada, o serviço tenta comunicar com o seu fornecedor de serviços cloud. As credenciais e o material de acesso que especificou serão autenticados neste momento. Apenas se a autenticação for bem-sucedida, é criada uma conta de armazenamento. Se a autenticação falhar, será apresentada a mensagem de erro apropriada.

Utilize os procedimentos seguintes para adicionar as credenciais da conta de armazenamento do Azure:

* Para adicionar uma credencial de conta de armazenamento que tenha a mesma subscrição do Azure como o serviço de Gestor de dispositivos
* Para adicionar uma credencial de conta de armazenamento do Azure que está fora da subscrição do serviço de Gestor de dispositivos

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>Para adicionar uma credencial de conta de armazenamento do Azure fora da subscrição do serviço StorSimple Device Manager

1. Navegue para o serviço StorSimple Device Manager, selecione e clique duas vezes nele. Esta ação abre o **descrição geral** painel.
2. Selecione **as credenciais da conta de armazenamento** dentro do **configuração** secção. Isto apresenta uma lista de quaisquer credenciais de conta de armazenamento existentes associados ao serviço StorSimple Device Manager.
3. Clique em **Adicionar**.
4. Na **adicionar uma credencial de conta de armazenamento** painel, faça o seguinte:
   
    1. Para **subscrição**, selecione **outros**.
   
    2. Forneça o nome da credencial da conta de armazenamento do Azure.
   
    3. Na **chave de acesso da conta de armazenamento** texto caixa, forneça a chave de acesso primária para a credencial da conta de armazenamento do Azure. Para obter esta chave, vá para o serviço de armazenamento do Azure, selecione a credencial da conta de armazenamento e clique em **gerir chaves de conta**. Agora, pode copiar a chave de acesso primária.
   
    4. Para ativar o SSL, clique nas **ativar** botão para criar um canal seguro para comunicação de rede entre o serviço StorSimple Device Manager e a cloud. Clique nas **desativar** botão apenas se estiver a utilizar uma nuvem privada.
   
    5. Clique em **Adicionar**. Será notificado quando a credencial da conta de armazenamento é criada com êxito.

5. A credencial da conta de armazenamento recentemente criada é apresentada no painel do serviço do Gestor de dispositivos de configurar o StorSimple sob **as credenciais da conta de armazenamento**.
   


## <a name="edit-a-storage-account"></a>Editar uma conta de armazenamento

Pode editar uma conta de armazenamento que é utilizada por um contentor de volume. Se editar conta de armazenamento que está atualmente em utilização, o único campo disponível para modificar é a chave de acesso para a conta de armazenamento. Pode fornecer a nova chave de acesso de armazenamento e guardar as definições atualizadas.

#### <a name="to-edit-a-storage-account"></a>Para editar uma conta de armazenamento

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

    ![Credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Na **as credenciais da conta de armazenamento** painel, na lista de credenciais da conta de armazenamento, selecione e clique naquele que pretende editar. 

3. Pode modificar os **ativar SSL** seleção. Também pode clicar em **mais...**  e, em seguida, selecione **chave de acesso de sincronização para girar** as chaves de acesso da conta de armazenamento. Aceda a [rotação de contas de armazenamento da chave](#key-rotation-of-storage-accounts) para obter mais informações sobre como efetuar a rotação de chaves. Depois de modificar as definições, clique em **guardar**. 

    ![Guardar as credenciais de conta de armazenamento editado](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Quando lhe for pedida a confirmação, clique em **Sim**. 

    ![Confirmar modificação](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

As definições serão atualizadas e guardadas para a sua conta de armazenamento. 

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

> [!IMPORTANT]
> Pode eliminar uma conta de armazenamento apenas se não for utilizada por um contentor de volume. Se uma conta de armazenamento está a ser utilizada por um contentor de volume, primeiro de eliminar o contentor de volumes e, em seguida, eliminar a conta de armazenamento associado.

#### <a name="to-delete-a-storage-account"></a>Para eliminar uma conta de armazenamento

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.

2. Na lista tabular de contas de armazenamento, coloque o cursor sobre a conta que pretende eliminar. Botão direito do mouse para invocar o menu de contexto e clique em **eliminar**.

    ![Eliminar as credenciais da conta de armazenamento](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Quando lhe for pedida confirmação, clique em **Sim** para continuar com a eliminação. Da lista tabular será atualizada para refletir as alterações.

    ![Confirmar eliminação](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Rotação de chaves de contas de armazenamento

Por motivos de segurança, rotação de chaves, muitas vezes, é um requisito nos centros de dados. Cada subscrição do Microsoft Azure pode ter uma ou mais contas de armazenamento associado. O acesso a estas contas é controlado pelas chaves de acesso e de subscrição para cada conta de armazenamento. 

Quando cria uma conta de armazenamento, o Microsoft Azure gera duas chaves de acesso de armazenamento de 512 bits que são utilizadas para autenticação quando a conta de armazenamento é acessada. Ter duas chaves de acesso de armazenamento permite-lhe gerar as chaves sem nenhuma interrupção para seu serviço de armazenamento ou de acesso a esse serviço. A chave que está atualmente em uso é o *primário* chave e a chave de cópia de segurança é conhecido como o *secundário* chave. Um destas duas chaves tem de ser fornecido quando o dispositivo do Microsoft Azure StorSimple acede ao seu fornecedor de serviços de armazenamento na cloud.

## <a name="what-is-key-rotation"></a>O que é a rotação de chaves?

Normalmente, há aplicações que utilizam apenas uma das chaves para aceder aos seus dados. Após um determinado período de tempo, pode ter seus aplicativos que mudem para a segunda chave a utilizar. Depois de mudar a seus aplicativos para a chave secundária, pode extinguir a primeira chave e, em seguida, gerar uma nova chave. Usar as duas chaves dessa forma permite o acesso de aplicações para os dados sem incorrer em qualquer período de inatividade.

As chaves de conta de armazenamento são sempre armazenadas no serviço em formato criptografado. No entanto, estes podem ser repostas através do serviço StorSimple Device Manager. O serviço pode obter a chave primária e chave secundária para todas as contas de armazenamento na mesma subscrição, incluindo as contas criadas no serviço de armazenamento, bem como as contas de armazenamento de predefinido geradas quando o serviço StorSimple Device Manager foi inicialmente criado. O serviço StorSimple Device Manager será sempre obter estas chaves do portal clássico do Azure e, em seguida, armazená-las de forma encriptada.

## <a name="rotation-workflow"></a>Fluxo de trabalho de rotação

Um administrador do Microsoft Azure pode voltar a gerar ou alterar a chave primária ou secundária ao aceder diretamente a conta de armazenamento (através do serviço de armazenamento do Microsoft Azure). O serviço StorSimple Device Manager não vê esta alteração automaticamente.

Para informar o serviço StorSimple Device Manager da alteração, precisa para aceder ao serviço StorSimple Device Manager, aceder à conta de armazenamento e, em seguida, sincronizar a chave primária ou secundária (dependendo do que foi alterado). O serviço, em seguida, obtém a chave mais recente, criptografa as chaves e envia a chave de encriptação no dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>Ao sincronizar as chaves das contas de armazenamento na mesma subscrição que o serviço 
1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Na secção **Configuração**, clique em **Credenciais da conta de armazenamento**.
2. Na lista tabular de contas de armazenamento, clique em que pretende modificar. 

    ![sincronizar as chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Clique em **... Obter mais** e, em seguida, selecione **chave de acesso de sincronização para girar**.   

    ![sincronizar as chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. No serviço StorSimple Device Manager, terá de atualizar a chave que anteriormente foi alterada no serviço de armazenamento do Microsoft Azure. Se a chave de acesso primária foi alterada (regenerada), selecione **primário** chave. Se a chave secundária tiver sido alterada, selecione **secundário** chave. Clique em **sincronizar chave**.
      
      ![sincronizar as chaves](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Será notificado depois da chave está sincronizada com êxito.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Ao sincronizar as chaves das contas de armazenamento fora da subscrição do serviço
1. Sobre o **serviços** página, clique no **configurar** separador.
2. Clique em **adicionar/editar contas de armazenamento**.
3. Na caixa de diálogo, faça o seguinte:
   
   1. Selecione a conta de armazenamento com a chave de acesso que pretende atualizar.
   2. Terá de atualizar a chave de acesso de armazenamento no serviço StorSimple Device Manager. Neste caso, pode ver a chave de acesso de armazenamento. Introduza a nova chave no **chave de acesso da conta de armazenamento** caixa. 
   3. Guarde as alterações. A chave de acesso da conta de armazenamento deve ser atualizada.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [StorSimple segurança](storsimple-8000-security.md).
* Saiba mais sobre [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

