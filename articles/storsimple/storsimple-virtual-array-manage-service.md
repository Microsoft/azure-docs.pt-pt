---
title: Implementar o serviço StorSimple Device Manager | Documentos da Microsoft
description: Explica como criar e eliminar o serviço StorSimple Device Manager no portal do Azure e descreve como gerir a chave de registo do serviço.
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
ms.date: 11/29/2016
ms.author: alkohli
ms.openlocfilehash: 9f6e5b606caa661429a3c4d4a53e2021d57730aa
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116960"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Implementar o serviço StorSimple Device Manager do StorSimple Virtual Array
## <a name="overview"></a>Descrição geral

O serviço StorSimple Device Manager é executado no Microsoft Azure e liga-se a diversos dispositivos StorSimple. Depois de criar o serviço, pode usá-lo para gerir os dispositivos a partir do portal do Microsoft Azure em execução num browser. Isto permite-lhe monitorizar todos os dispositivos que estão ligados ao serviço StorSimple Device Manager de um único local central, minimizando, assim, a carga administrativa.

As tarefas comuns relacionadas a um serviço StorSimple Device Manager são:

* Criar um serviço
* Eliminar um serviço
* Obter a chave de registo do serviço
* Regenerar a chave de registo do serviço

Este tutorial descreve como realizar cada uma das tarefas anteriores. As informações contidas neste artigo são aplicáveis apenas para as matrizes virtuais do StorSimple. Para obter mais informações sobre a série StorSimple 8000, aceda a [implementar um serviço StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Criar um serviço

Para criar um serviço, tem de ter:

* Uma subscrição com um contrato Enterprise
* Uma conta de armazenamento do Microsoft Azure Active Directory
* As informações de faturas que são utilizadas para gestão de acesso

Também pode optar por gerar uma conta de armazenamento ao criar o serviço.

Um único serviço pode gerir diversos dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter várias instâncias do serviço para trabalhar com diferentes subscrições, as organizações ou até mesmo as localizações de implementação.

> [!NOTE]
> Terá de instâncias separadas do serviço StorSimple Device Manager para gerir dispositivos da série StorSimple 8000 e as matrizes virtuais do StorSimple.


Execute os seguintes passos para criar um serviço.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminar um serviço

Antes de eliminar um serviço, certifique-se de que não existem dispositivos ligados a estiver a utilizar. Se o serviço estiver em uso, desative os dispositivos ligados. A operação de desativar será a ligação entre o dispositivo e o serviço do servidor, mas preservar os dados do dispositivo na cloud.

> [!IMPORTANT]
> Depois de eliminar um serviço, a operação não pode ser revertida. Todos os dispositivos que estava a utilizar o serviço tem de ser antes de poder ser utilizada com outro serviço de reposição de fábrica. Neste cenário, os dados locais no dispositivo, bem como a configuração, serão perdidos.
 

Execute os passos seguintes para eliminar um serviço.

#### <a name="to-delete-a-service"></a>Para eliminar um serviço

1. Vá para **Todos os recursos**. Pesquisa para o seu serviço StorSimple Device Manager. Selecione o serviço que pretende eliminar.
   
    ![Selecione o serviço para eliminar](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Aceda ao seu dashboard de serviço para garantir que não há nenhum dispositivo ligado ao serviço. Se não existirem não existem dispositivos registados com este serviço, também verá uma mensagem de faixa para o efeito. Clique em **Eliminar**.
   
    ![Eliminar serviço](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Quando lhe for pedida confirmação, clique em **Sim** da notificação de confirmação. 
   
    ![Confirmar a eliminação de serviço](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Pode demorar alguns minutos para que o serviço seja eliminado. Depois do serviço foi eliminado com êxito, será notificado.
   
    ![Eliminação de serviço com êxito](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

A lista de serviços é atualizada.

 ![Lista atualizada de serviços](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço
Depois de criar um serviço com êxito, terá de registar o dispositivo StorSimple com o serviço. Para registar o seu primeiro dispositivo StorSimple, terá da chave de registo do serviço. Para registar dispositivos adicionais com um serviço existente do StorSimple, terá a chave de registo e a chave de encriptação de dados do serviço (que é gerado no primeiro dispositivo durante o registo). Para obter mais informações sobre a chave de encriptação de dados do serviço, consulte [StorSimple segurança](storsimple-security.md). Pode obter a chave de registo ao aceder a **chaves** painel para o seu serviço.

Execute os passos seguintes para obter a chave de registo do serviço.

#### <a name="to-get-the-service-registration-key"></a>Para obter a chave de registo do serviço
1. Na **StorSimple Device Manager** painel, aceda à **Management &gt;**  **chaves**.
   
   ![Painel de chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Na **chaves** aparece do painel, uma chave de registo do serviço. Copie a chave de registo com o ícone de cópia. 

Tenha a chave de registo do serviço num local seguro. Precisará desta chave, bem como a chave de encriptação de dados de serviço, para registar dispositivos adicionais com este serviço. Depois de obter a chave de registo do serviço, terá de configurar o seu dispositivo através do Windows PowerShell para StorSimple interface.

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registo do serviço
Terá de voltar a gerar uma chave de registo do serviço, se for necessário para efetuar a rotação de chaves ou se a lista de administradores de serviço tiver sido alterado. Quando voltar a gerar a chave, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos que foram registados já não são afetados por este processo.

Execute os seguintes passos para voltar a gerar uma chave de registo do serviço.

#### <a name="to-regenerate-the-service-registration-key"></a>Para voltar a gerar a chave de registo do serviço
1. Na **StorSimple Device Manager** painel, aceda à **Management &gt;**  **chaves**.
   
   ![Painel de chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Na **chaves** painel, clique em **voltar a gerar**.
   
   ![Clique em voltar a gerar](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Na **chave de registo do serviço de voltar a gerar** painel, verifique a ação é necessária quando as chaves são gerados novamente. Todos os dispositivos subsequentes que estão registados com este serviço irão utilizar a nova chave de registo. Clique em **regenerar** para confirmar. Será notificado quando o registo estiver concluído.
   
   ![Confirmar a regenerar chave](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Será apresentada uma nova chave de registo do serviço.
   
    ![Confirmar a regenerar chave](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Copie esta chave e guarde-a para registar novos dispositivos com este serviço.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como [começar](storsimple-virtual-array-deploy1-portal-prep.md) com uma matriz Virtual StorSimple.
* Saiba como [administrar o seu dispositivo StorSimple](storsimple-ova-web-ui-admin.md).

