---
title: Contas de armazenamento do Gestor de Dispositivos Emigrados, subscrições
description: Saiba como migrar subscrições, contas de armazenamento para o seu serviço StorSimple Device Manager.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 428c336d98e278910b229e9c0d877a9ae6268c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169712"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrar subscrições e contas de armazenamento associadas ao serviço StorSimple Device Manager

Poderá ter de transferir o seu serviço StorSimple para uma nova inscrição ou para uma nova subscrição. Estes cenários de migração são alterações de conta ou alterações no datacenter. Utilize a tabela seguinte para entender quais destes cenários são suportados, incluindo as medidas detalhadas a mover.

## <a name="account-changes"></a>Alterações na conta

| Pode mexer-se...| Suportado| Tempo de inatividade| Processo de Apoio ao Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Uma subscrição inteira (inclui contas de serviço StorSimple e armazenamento) para outra inscrição? | Sim       | Não       | **Transferência de Inscrições**<br>Utilização:<li>Quando comprar um novo compromisso Azure ao abrigo de um novo acordo.</li><li>Você quer migrar todas as contas e subscrições da antiga inscrição para a nova. Isto inclui todos os serviços Azure sob a antiga subscrição.</li> | **Passo 1: Abra um bilhete de suporte à Operação Azure Enterprise.**<li>Vai [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)para.</li><li> Selecione **A Administração** de Inscrição e, em seguida, selecione **Transferência de uma matrícula para uma nova inscrição**.<br>**Passo 2: Fornecer as informações solicitadas**<br>Incluir:<li>número de inscrição fonte</li><li> número de inscrição de destino</li><li>transferência data efetiva|
| Serviço StorSimple de uma conta existente para uma nova inscrição?    | Sim       | Não       | **Transferência de Conta**<br>Utilização:<li>Quando não quiser uma transferência completa de inscrição.</li><li>Só quer mover contas específicas para uma nova matrícula.</li>| **Passo 1: Abra um bilhete de suporte à Operação Azure Enterprise.**<li>Vai [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)para.</li><li>Selecione **A Administração de Inscrição** e, em seguida, selecione **Transferir uma Conta EA para uma nova inscrição**.<br>**Passo 2: Fornecer as informações solicitadas**<br>Incluir:<li>número de inscrição fonte</li><li> número de inscrição de destino</li><li>transferência data efetiva|
| Serviço StorSimple de uma subscrição para outra subscrição?      | Não        |    Sim         | Nenhum, processo manual|<li>Migrar dados do dispositivo StorSimple.</li><li>Efetuar um reset de fábrica do dispositivo, isto elimina quaisquer dados locais no dispositivo.</li><li>Registe o dispositivo com a nova subscrição de um serviço StorSimple Device Manager.</li><li>Migrar os dados de volta para o dispositivo.|
|Posso transferir a propriedade de uma assinatura Azure para outro diretório? | Sim       | Não       | Associe uma subscrição existente ao seu diretório Azure AD | Consulte [Para associar uma subscrição existente ao seu diretório Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Poderá demorar até 10 minutos para que tudo seja apresentado corretamente.|
| Dispositivo StorSimple de um serviço StorSimple Device Manager para outro serviço numa região diferente?      | Não        | Sim            | Nenhum, processo manual |O mesmo que acima.|
| Conta de armazenamento para uma nova subscrição ou grupo de recursos?     | Sim        | Não             |Mover conta de armazenamento para diferentes subscrições ou grupo de recursos |Após a mudança, se as chaves de acesso à conta de armazenamento forem atualizadas, o utilizador terá de configurar manualmente as chaves de acesso para a conta de armazenamento migrada através do serviço StorSimple Device Manager.|
| Conta de armazenamento clássica para uma conta de armazenamento do Gestor de Recursos Azure      | Sim        | Não             |Migrar do clássico para o Gestor de Recursos Azure |<li>Para instruções detalhadas sobre como migrar uma conta de armazenamento do clássico para o Gestor de Recursos Azure, vá migrar [uma conta de armazenamento clássica.](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account)</li><li> Se as chaves de acesso à conta de armazenamento forem atualizadas após a migração, o utilizador terá de sincronizar as chaves de acesso da conta de armazenamento migrada através do serviço StorSimple Device Manager. Isto é para garantir que os dispositivos StorSimple continuem a funcionar normalmente e são capazes de diferenciar os dados primários/de backup para o Azure. Para instruções detalhadas sobre as teclas de acesso sincronizadas, aceda ao [fluxo de trabalho rotativo](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> No caso de um StorSimple Cloud Appliance, se a conta de armazenamento clássica for migrada, mas a máquina virtual subjacente ainda permanecer no clássico, o aparelho deve funcionar corretamente. Se a máquina virtual subjacente ao aparelho em nuvem for migrada, a funcionalidade de sactivação e eliminação não funcionará.</li><li> Tem de criar um novo StorSimple Cloud Appliances no portal Azure e depois falhar nos aparelhos de nuvem mais antigos. Não é possível criar um StorSimple Cloud Appliance no novo portal Azure utilizando uma conta de armazenamento clássica, precisam de ter uma conta de armazenamento do Gestor de Recursos Azure. Para mais informações, vá para [a Implementação e gerencie um StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Alterações no datacenter

| Pode mexer-se...| Suportado|Tempo de inatividade| Processo de Apoio ao Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Um serviço StorSimple de um datacenter Azure para outro? | Não | Sim |Nenhum, processo manual  |<li>Migrar dados do dispositivo StorSimple.</li><li>Efetuar um reset de fábrica do dispositivo, isto elimina quaisquer dados locais no dispositivo.</li><li>Registe o dispositivo com a nova subscrição de um novo serviço StorSimple Device Manager.</li><li>Migrar os dados de volta para o dispositivo.|
| Uma conta de armazenamento de um centro de dados Azure para outro? | Não |Sim  |Nenhum, processo manual  | O mesmo que acima.|

## <a name="next-steps"></a>Passos seguintes

* [Implementar o serviço StorSimple Device Manager](storsimple-8000-manage-service.md)
* [Implementar dispositivo da série StorSimple 8000 no portal Azure](storsimple-8000-deployment-walkthrough-u2.md)
