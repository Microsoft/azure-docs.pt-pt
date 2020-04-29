---
title: Implementar o serviço StorSimple Device Manager [ StorSimple Device Manager ] Microsoft Docs
description: Explica como criar e eliminar o serviço StorSimple Device Manager no portal Azure, e descreve como gerir a chave de registo de serviço.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79267459"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Implemente o serviço StorSimple Device Manager para StorSimple Virtual Array

## <a name="overview"></a>Descrição geral

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

O serviço StorSimple Device Manager funciona no Microsoft Azure e conecta-se a vários dispositivos StorSimple. Depois de criar o serviço, pode usá-lo para gerir os dispositivos a partir do portal Microsoft Azure que funciona num browser. Isto permite-lhe monitorizar todos os dispositivos que estão ligados ao serviço StorSimple Device Manager a partir de uma localização única e central, minimizando assim os encargos administrativos.

As tarefas comuns relacionadas com um serviço StorSimple Device Manager são:

* Criar um serviço
* Apagar um serviço
* Obter a chave de registo do serviço
* Regenerar a chave de registo de serviço

Este tutorial descreve como executar cada uma das tarefas anteriores. As informações contidas neste artigo aplicam-se apenas às Matrizes Virtuais StorSimple. Para mais informações sobre a série StorSimple 8000, vá implementar [um serviço StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Criar um serviço

Para criar um serviço, precisa de ter:

* Uma subscrição com um Acordo de Empresa
* Uma conta de armazenamento ativa do Microsoft Azure
* A informação de faturação que é usada para a gestão de acesso

Também pode optar por gerar uma conta de armazenamento quando criar o serviço.

Um único serviço pode gerir vários dispositivos. No entanto, um dispositivo não pode abranger vários serviços. Uma grande empresa pode ter múltiplas instâncias de serviço para trabalhar com diferentes subscrições, organizações ou mesmo localizações de implementação.

> [!NOTE]
> Precisa de instâncias separadas do serviço StorSimple Device Manager para gerir dispositivos da série StorSimple 8000 e Matrizes Virtuais StorSimple.


Execute os seguintes passos para criar um serviço.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Apagar um serviço

Antes de apagar um serviço, certifique-se de que nenhum dispositivo ligado está a utilizá-lo. Se o serviço estiver a ser utilizado, desative os dispositivos ligados. A operação de desativação cortará a ligação entre o dispositivo e o serviço, mas preservará os dados do dispositivo na nuvem.

> [!IMPORTANT]
> Após a eliminação de um serviço, a operação não pode ser invertida. Qualquer dispositivo que estivesse a utilizar o serviço terá de ser reiniciado antes de poder ser utilizado com outro serviço. Neste cenário, os dados locais sobre o dispositivo, bem como a configuração, serão perdidos.
 

Efetue os seguintes passos para eliminar um serviço.

#### <a name="to-delete-a-service"></a>Para apagar um serviço

1. Vá para **Todos os recursos**. Procure o seu serviço StorSimple Device Manager. Selecione o serviço que pretende eliminar.
   
    ![Selecione o serviço para eliminar](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Vá ao seu painel de assistência para garantir que não há dispositivos ligados ao serviço. Se não houver dispositivos registados neste serviço, também verá uma mensagem de banner no sentido. Clique em **Apagar**.
   
    ![Eliminar o serviço](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Quando solicitado para confirmação, **clique** sim na notificação de confirmação. 
   
    ![Confirmar a supressão do serviço](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Pode levar alguns minutos para que o serviço seja apagado. Após a eliminação do serviço com sucesso, será notificado.
   
    ![Eliminação de serviço bem sucedida](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

A lista de serviços será refrescada.

 ![Lista atualizada de serviços](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Obter a chave de registo do serviço
Depois de ter criado com sucesso um serviço, terá de registar o seu dispositivo StorSimple com o serviço. Para registar o seu primeiro dispositivo StorSimple, necessitará da chave de registo de serviço. Para registar dispositivos adicionais com um serviço StorSimple existente, necessitará tanto da chave de registo como da chave de encriptação de dados de serviço (que é gerada no primeiro dispositivo durante o registo). Para obter mais informações sobre a chave de encriptação de dados do serviço, consulte a [segurança StorSimple](storsimple-security.md). Pode obter a chave de registo acedendo à lâmina **Keys** para o seu serviço.

Execute os seguintes passos para obter a chave de registo de serviço.

#### <a name="to-get-the-service-registration-key"></a>Para obter a chave de registo de serviço
1. Na lâmina **StorSimple Device Manager,** vá para **as Teclas**de **Gestão &gt; ** .
   
   ![Painel de chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Na lâmina **Keys,** aparece uma chave de registo de serviço. Copie a chave de registo utilizando o ícone da cópia. 

Mantenha a chave de registo de serviço num local seguro. Necessitará desta chave, bem como da chave de encriptação de dados de serviço, para registar dispositivos adicionais com este serviço. Depois de obter a chave de registo de serviço, terá de configurar o seu dispositivo através da interface StorSimple do Windows PowerShell.

## <a name="regenerate-the-service-registration-key"></a>Regenerar a chave de registo de serviço
Terá de regenerar uma chave de registo de serviço se for obrigado a realizar a rotação da chave ou se a lista de administradores de serviços tiver mudado. Quando regenera a tecla, a nova chave é utilizada apenas para registar dispositivos subsequentes. Os dispositivos que já estavam registados não são afetados por este processo.

Execute os seguintes passos para regenerar uma chave de registo de serviço.

#### <a name="to-regenerate-the-service-registration-key"></a>Para regenerar a chave de registo de serviço
1. Na lâmina **StorSimple Device Manager,** vá para **as Teclas**de **Gestão &gt; ** .
   
   ![Painel de chaves](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Na lâmina **keys,** clique em **Regenerar**.
   
   ![Clique em regenerar](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Na lâmina de registo de **serviço Regenerar,** reveja a ação necessária quando as teclas forem regeneradas. Todos os dispositivos subsequentes que estejam registados neste serviço utilizarão a nova chave de registo. Clique em **Regenerar** para confirmar. Será notificado após a inscrição estar completa.
   
   ![Confirmar a chave regenerada](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Aparecerá uma nova chave de registo de serviço.
   
    ![Confirmar a chave regenerada](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Copie esta tecla e guarde-a para registar quaisquer novos dispositivos com este serviço.

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [começar](storsimple-virtual-array-deploy1-portal-prep.md) com um StorSimple Virtual Array.
* Aprenda a [administrar o seu dispositivo StorSimple](storsimple-ova-web-ui-admin.md).

