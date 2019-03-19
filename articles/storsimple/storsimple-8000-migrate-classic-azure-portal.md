---
title: Migrar contas de armazenamento, as subscrições para o seu serviço StorSimple Device Manager | Documentos da Microsoft
description: Saiba como migrar as subscrições, contas de armazenamento para sua service8000 StorSimple Device Manager.
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
ms.openlocfilehash: 3cce18fa1890fc9e518294e294cc43e0e55065aa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002492"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrar as subscrições e contas de armazenamento associadas ao serviço StorSimple Device Manager

Poderá ter de mover o seu serviço do StorSimple para um novo registro de ou para uma nova subscrição. Esses cenários de migração são alterações de conta ou alterações de Data Center. Utilize a seguinte tabela para compreender de que estes cenários são suportados incluindo os passos detalhados para mover.

## <a name="account-changes"></a>Alterações de conta

| Pode mover...| Suportadas| Tempo de inatividade| Processo de suporte do Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Uma subscrição completa (inclui contas de serviço e de armazenamento do StorSimple) para outra inscrição? | Sim       | Não       | **Transferência de inscrição**<br>Utilização:<li>Ao comprar uma nova alocação do Azure sob um contrato de novo.</li><li>Quer migrar todas as contas e subscrições da inscrição antiga para o novo. Isto inclui todos os serviços do Azure sob a subscrição antigo.</li> | **Passo 1: Abra um pedido de suporte de operação do Azure Enterprise.**<li>Vá para [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li> Selecione **administração de inscrição** e, em seguida, selecione **transferir a partir de uma inscrição para um novo registro de**.<br>**Passo 2: Forneça as informações pedidas**<br>Incluem:<li>número de inscrição de origem</li><li> número de inscrição de destino</li><li>Data efetiva de transferência|
| Serviço do StorSimple a partir de uma conta existente para um novo Registro?    | Sim       | Não       | **Transferência de conta**<br>Utilização:<li>Quando não pretender que uma transferência de inscrição completa.</li><li>Apenas pretender mover contas específicas para um novo Registro.</li>| **Passo 1: Abra um pedido de suporte de operação do Azure Enterprise.**<li>Vá para [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li>Selecione **administração de inscrição** e, em seguida, selecione **transferir uma conta de EA para um novo registro de**.<br>**Passo 2: Forneça as informações pedidas**<br>Incluem:<li>número de inscrição de origem</li><li> número de inscrição de destino</li><li>Data efetiva de transferência|
| Serviço do StorSimple a partir de uma subscrição para outra subscrição?      | Não        |    Sim         | Nenhum processo manual|<li>Migre dados fora do dispositivo StorSimple.</li><li>Efetuar uma reposição de fábrica do dispositivo, esta ação elimina quaisquer dados locais no dispositivo.</li><li>Registe o dispositivo com a nova subscrição a um serviço StorSimple Device Manager.</li><li>Migre os dados de volta para o dispositivo.|
|Pode transferir a propriedade de uma subscrição do Azure para outro diretório? | Sim       | Não       | Associar uma subscrição existente ao diretório do Azure AD | Consultar [para associar uma subscrição existente ao diretório do Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Poderá demorar até 10 minutos para que tudo seja apresentado corretamente.|
| Dispositivo do StorSimple a partir de um serviço de Gestor de dispositivos do StorSimple para outro serviço numa região diferente?      | Não        | Sim            | Nenhum processo manual |Mesmo que acima.|
| Conta de armazenamento para uma nova subscrição ou grupo de recursos?     | Sim        | Não             |Mover conta de armazenamento para diferente subscrição ou grupo de recursos |Após a mudança, se as chaves de acesso da conta de armazenamento forem atualizadas, o utilizador terá de configurar as chaves de acesso manualmente para a conta de armazenamento migrados através do serviço StorSimple Device Manager.|
| Conta de armazenamento clássica a uma conta de armazenamento do Azure Resource Manager      | Sim        | Não             |Migrar de clássico para o Azure Resource Manager |<li>Para obter instruções detalhadas sobre como migrar uma conta de armazenamento do clássico para o Azure Resource Manager, aceda a [migrar uma conta de armazenamento clássicas](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Se as chaves de acesso da conta de armazenamento são atualizadas após a migração, o utilizador terá de sincronizar as chaves de acesso para a conta de armazenamento migrados através do serviço StorSimple Device Manager. Isso é para garantir que os dispositivos do StorSimple continuam a funcionar normalmente e são capazes de camada de dados principal/cópia de segurança para o Azure. Para obter instruções detalhadas sobre a sincronizar as chaves de acesso, aceda a [fluxo de trabalho de rotação](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> No caso de uma StorSimple Cloud Appliance, se a conta de armazenamento clássica é migrada, mas a máquina virtual subjacente ainda permanece no modelo clássico, o dispositivo de deve funcionar corretamente. Se a máquina virtual subjacente para a aplicação da cloud é migrada, em seguida, a funcionalidade de desativar e eliminar não funcionará.</li><li> Tem de criar uma StorSimple Cloud Appliances novo no portal do Azure e, em seguida, efetuar a ativação pós-falha de aplicações de cloud mais antigas. Não é possível criar uma StorSimple Cloud Appliance no novo portal do Azure através de uma conta de armazenamento clássicas, elas precisam ter uma conta de armazenamento do Azure Resource Manager. Para obter mais informações, aceda a [implementar e gerir uma StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Alterações de Data Center

| Pode mover...| Suportadas|Tempo de inatividade| Processo de suporte do Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Um serviço do StorSimple a partir de um datacenter do Azure para outra? | Não | Sim |Nenhum processo manual  |<li>Migre dados fora do dispositivo StorSimple.</li><li>Efetuar uma reposição de fábrica do dispositivo, esta ação elimina quaisquer dados locais no dispositivo.</li><li>Registe o dispositivo com a nova subscrição para um novo serviço StorSimple Device Manager.</li><li>Migre os dados de volta para o dispositivo.|
| Uma conta de armazenamento de um datacenter do Azure para outra? | Não |Sim  |Nenhum processo manual  | Mesmo que acima.|

## <a name="next-steps"></a>Passos Seguintes

* [Implementar o serviço StorSimple Device Manager](storsimple-8000-manage-service.md)
* [Implementar o dispositivo da série StorSimple 8000 no portal do Azure](storsimple-8000-deployment-walkthrough-u2.md)
