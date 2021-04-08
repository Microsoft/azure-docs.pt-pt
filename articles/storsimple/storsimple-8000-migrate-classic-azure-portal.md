---
title: Migrar contas de armazenamento do Gestor de Dispositivos StorSimple, subscrições
description: Saiba como migrar subscrições, contas de armazenamento para o seu serviço StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 523788dc727fc5fa32b2f57250fb738352ff6d31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491525"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrar subscrições e contas de armazenamento associadas ao Serviço de Gestor de Dispositivos do StorSimple

Poderá ter de transferir o seu serviço StorSimple para uma nova inscrição ou para uma nova subscrição. Estes cenários de migração são alterações de conta ou alterações no datacenter. Utilize a tabela seguinte para entender quais destes cenários são suportados, incluindo os passos detalhados a mover.

## <a name="account-changes"></a>Alterações na conta

| Pode mover-se...| Suportado| Tempo de inatividade| Processo de Apoio Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Uma subscrição completa (inclui contas de serviço storSimple e de armazenamento) para outra inscrição? | Yes       | No       | **Transferência de Inscrição**<br>Utilização:<li>Quando comprar um novo compromisso da Azure ao abrigo de um novo acordo.</li><li>Pretende migrar todas as contas e subscrições da antiga inscrição para a nova. Isto inclui todos os serviços Azure sob a subscrição antiga.</li> | **Passo 1: Abra um bilhete de Apoio à Operação Azure Enterprise.**<li>Vai [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) para.</li><li> Selecione **A Administração** de Inscrição e, em seguida, selecione **Transferência de uma inscrição para uma nova inscrição**.<br>**Passo 2: Fornecer as informações solicitadas**<br>Incluir:<li>número de inscrição fonte</li><li> número de inscrição de destino</li><li>transferir data efetiva|
| Serviço StorSimple de uma conta existente para uma nova inscrição?    | Yes       | No       | **Transferência de Conta**<br>Utilização:<li>Quando não quiser uma transferência completa de inscrição.</li><li>Só quer mover contas específicas para uma nova matrícula.</li>| **Passo 1: Abra um bilhete de Apoio à Operação Azure Enterprise.**<li>Vai [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) para.</li><li>Selecione **A Administração de Inscrição** e, em seguida, selecione **Transferir uma Conta EA para uma nova inscrição**.<br>**Passo 2: Fornecer as informações solicitadas**<br>Incluir:<li>número de inscrição fonte</li><li> número de inscrição de destino</li><li>transferir data efetiva|
| Serviço StorSimple de uma subscrição para outra subscrição?      | No        |    Yes         | Nenhum, processo manual|<li>Migrar dados do dispositivo StorSimple.</li><li>Efetuar um reset de fábrica do dispositivo, eliminando quaisquer dados locais no dispositivo.</li><li>Registe o dispositivo com a nova subscrição de um serviço StorSimple Device Manager.</li><li>Migrar os dados de volta para o dispositivo.|
|Posso transferir a propriedade de uma assinatura Azure para outro diretório? | Yes       | No       | Associe uma subscrição existente ao seu diretório AD Azure | Consulte [Para associar uma subscrição existente ao seu diretório AD Azure](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Poderá demorar até 10 minutos para que tudo seja apresentado corretamente.|
| Dispositivo StorSimple de um serviço StorSimple Device Manager para outro serviço numa região diferente?      | No        | Yes            | Nenhum, processo manual |Mesmo que acima.|
| Conta de armazenamento de um novo grupo de subscrição ou recursos?     | Yes        | No             |Mover conta de armazenamento para diferentes grupos de subscrição ou recursos |Após a mudança, se as teclas de acesso à conta de armazenamento forem atualizadas, o utilizador terá de configurar manualmente as teclas de acesso para a conta de armazenamento migrada através do serviço StorSimple Device Manager.|
| Conta de armazenamento clássica para uma conta de armazenamento do Azure Resource Manager      | Yes        | No             |Migrar de clássico para o Azure Resource Manager |<li>Para instruções detalhadas sobre como migrar uma conta de armazenamento de clássico para Azure Resource Manager, vá à [Migração uma conta de armazenamento clássica.](../virtual-machines/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account)</li><li> Se as teclas de acesso à conta de armazenamento forem atualizadas após a migração, o utilizador terá de sincronizar as teclas de acesso da conta de armazenamento migrada através do serviço StorSimple Device Manager. Isto é para garantir que os dispositivos StorSimple continuem a funcionar normalmente e sejam capazes de nivelar os dados primários/de backup para o Azure. Para obter instruções detalhadas sobre a sincronização das teclas de acesso, aceda ao [fluxo de trabalho de rotação](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> No caso de um Aparelho em Nuvem StorSimple, se a conta de armazenamento clássica for migrada mas a máquina virtual subjacente continuar a ser clássica, o aparelho deve funcionar corretamente. Se a máquina virtual subjacente ao aparelho em nuvem for migrada, então a funcionalidade desativar e eliminar não funcionará.</li><li> Tem de criar um novo StorSimple Cloud Appliances no portal Azure e depois falhar a partir dos aparelhos de nuvem mais antigos. Não é possível criar um StorSimple Cloud Appliance no novo portal Azure utilizando uma conta de armazenamento clássica, eles precisam de ter uma conta de armazenamento Azure Resource Manager. Para obter mais informações, aceda ao [Implementar e gerencie um StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Alterações no Datacenter

| Pode mover-se...| Suportado|Tempo de inatividade| Processo de Apoio Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Um serviço StorSimple de um datacenter Azure para outro? | No | Yes |Nenhum, processo manual  |<li>Migrar dados do dispositivo StorSimple.</li><li>Efetuar um reset de fábrica do dispositivo, eliminando quaisquer dados locais no dispositivo.</li><li>Registe o dispositivo com a nova subscrição de um novo serviço StorSimple Device Manager.</li><li>Migrar os dados de volta para o dispositivo.|
| Uma conta de armazenamento de um datacenter Azure para outro? | No |Yes  |Nenhum, processo manual  | Mesmo que acima.|

## <a name="next-steps"></a>Passos seguintes

* [Implementar o serviço StorSimple Device Manager](storsimple-8000-manage-service.md)
* [Implementar dispositivo da série StorSimple 8000 no portal Azure](storsimple-8000-deployment-walkthrough-u2.md)