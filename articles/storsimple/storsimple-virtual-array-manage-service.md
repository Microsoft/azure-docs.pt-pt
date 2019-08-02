---
title: Implantar serviço de Device Manager do StorSimple | Microsoft Docs
description: Explica como criar e excluir o serviço StorSimple Device Manager no portal do Azure e descreve como gerenciar a chave de registro do serviço.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 2ff987448e3900c2a533789b2f38fd3ced71ce72
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516683"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Implantar o serviço StorSimple Device Manager para o StorSimple virtual array

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

O serviço StorSimple Device Manager é executado em Microsoft Azure e se conecta a vários dispositivos StorSimple. Depois de criar o serviço, você pode usá-lo para gerenciar os dispositivos do portal do Microsoft Azure em execução em um navegador. Isso permite que você monitore todos os dispositivos que estão conectados ao serviço StorSimple Device Manager de um único local central, minimizando assim a carga administrativa.

As tarefas comuns relacionadas a um serviço de Device Manager do StorSimple são:

* Criar um serviço
* Excluir um serviço
* Obter a chave de registo do serviço
* Regenerar a chave de registro do serviço

Este tutorial descreve como executar cada uma das tarefas anteriores. As informações contidas neste artigo são aplicáveis somente a matrizes virtuais StorSimple. Para obter mais informações sobre a série StorSimple 8000, vá para [implantar um serviço de StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Criar um serviço

Para criar um serviço, você precisa ter:

* Uma assinatura com um Enterprise Agreement
* Uma conta de armazenamento do Microsoft Azure ativa
* As informações de cobrança usadas para gerenciamento de acesso

Você também pode optar por gerar uma conta de armazenamento ao criar o serviço.

Um único serviço pode gerenciar vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias de serviço para trabalhar com diferentes assinaturas, organizações ou até mesmo locais de implantação.

> [!NOTE]
> Você precisa de instâncias separadas do serviço de Device Manager do StorSimple para gerenciar dispositivos da série StorSimple 8000 e matrizes virtuais StorSimple.


Execute as etapas a seguir para criar um serviço.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Excluir um serviço

Antes de excluir um serviço, verifique se nenhum dispositivo conectado está usando-o. Se o serviço estiver em uso, desative os dispositivos conectados. A operação de desativação irá romper a conexão entre o dispositivo e o serviço, mas preservar os dados do dispositivo na nuvem.

> [!IMPORTANT]
> Depois que um serviço é excluído, a operação não pode ser revertida. Qualquer dispositivo que estava usando o serviço precisará ser redefinido de fábrica para poder ser usado com outro serviço. Nesse cenário, os dados locais no dispositivo, bem como a configuração, serão perdidos.
 

Execute as etapas a seguir para excluir um serviço.

#### <a name="to-delete-a-service"></a>Para excluir um serviço

1. Vá para **Todos os recursos**. Pesquise seu serviço StorSimple Device Manager. Selecione o serviço que você deseja excluir.
   
    ![Selecionar o serviço a ser excluído](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Vá para o painel de serviço para garantir que não haja nenhum dispositivo conectado ao serviço. Se não houver dispositivos registrados com esse serviço, você também verá uma mensagem de cabeçalho para o efeito. Clique em **Eliminar**.
   
    ![Eliminar serviço](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Quando a confirmação for solicitada, clique em **Sim** na notificação de confirmação. 
   
    ![Confirmar exclusão de serviço](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Pode levar alguns minutos para que o serviço seja excluído. Depois que o serviço for excluído com êxito, você será notificado.
   
    ![Exclusão de serviço bem-sucedida](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

A lista de serviços será atualizada.

 ![Lista atualizada de serviços](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço
Depois de criar um serviço com êxito, você precisará registrar seu dispositivo StorSimple no serviço. Para registrar seu primeiro dispositivo StorSimple, você precisará da chave de registro do serviço. Para registrar dispositivos adicionais com um serviço StorSimple existente, você precisará da chave de registro e da chave de criptografia de dados de serviço (que é gerada no primeiro dispositivo durante o registro). Para obter mais informações sobre a chave de criptografia de dados de serviço, consulte [segurança do StorSimple](storsimple-security.md). Você pode obter a chave de registro acessando a folha **chaves** para seu serviço.

Execute as etapas a seguir para obter a chave de registro do serviço.

#### <a name="to-get-the-service-registration-key"></a>Para obter a chave de registro do serviço
1. Na folha **Device Manager do StorSimple** , vá para **chaves**de **gerenciamento &gt;**  .
   
   ![Painel de chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Na folha **chaves** , uma chave de registro de serviço é exibida. Copie a chave de registro usando o ícone de cópia. 

Mantenha a chave de registro do serviço em um local seguro. Você precisará dessa chave, bem como da chave de criptografia de dados de serviço, para registrar dispositivos adicionais nesse serviço. Depois de obter a chave de registro do serviço, você precisará configurar o dispositivo por meio da interface Windows PowerShell para StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registro do serviço
Você precisará regenerar uma chave de registro de serviço se for necessário executar a rotação de chaves ou se a lista de administradores de serviço tiver mudado. Quando você regenera a chave, a nova chave é usada somente para registrar os dispositivos subsequentes. Os dispositivos que já foram registrados não são afetados por esse processo.

Execute as etapas a seguir para regenerar uma chave de registro de serviço.

#### <a name="to-regenerate-the-service-registration-key"></a>Para regenerar a chave de registro do serviço
1. Na folha **Device Manager do StorSimple** , vá para **chaves**de **gerenciamento &gt;**  .
   
   ![Painel de chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Na folha **chaves** , clique em **regenerar**.
   
   ![Clique em regenerar](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Na folha **regenerar chave de registro de serviço** , examine a ação necessária quando as chaves são regeneradas. Todos os dispositivos subsequentes registrados com esse serviço usarão a nova chave de registro. Clique em regenerar para confirmar. Você será notificado após a conclusão do registro.
   
   ![Confirmar regeneração de chave](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Uma nova chave de registro de serviço será exibida.
   
    ![Confirmar regeneração de chave](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Copie essa chave e salve-a para registrar novos dispositivos com esse serviço.

## <a name="next-steps"></a>Passos seguintes
* Saiba como começar [com uma](storsimple-virtual-array-deploy1-portal-prep.md) matriz virtual StorSimple.
* Saiba como [administrar seu dispositivo StorSimple](storsimple-ova-web-ui-admin.md).

