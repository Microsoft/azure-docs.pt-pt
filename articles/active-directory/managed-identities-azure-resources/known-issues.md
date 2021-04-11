---
title: Questões conhecidas com identidades geridas - Azure Ative Directory
description: Problemas conhecidos com identidades geridas para recursos Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226927"
---
# <a name="known-issues-with-managed-identities"></a>Questões conhecidas com identidades geridas

Este artigo discute algumas questões em torno das identidades geridas e como abordá-las. As questões comuns sobre identidades geridas estão documentadas no nosso artigo [de perguntas frequentes.](managed-identities-faq.md)
## <a name="vm-fails-to-start-after-being-moved"></a>VM falha no arranque depois de ser movido 

Se mover um VM num estado de funcionamento de um grupo de recursos ou subscrição, ele continua a funcionar durante a mudança. No entanto, após a mudança, se o VM for parado e reiniciado, não começará. Esta questão acontece porque o VM não está a atualizar a referência às identidades geridas para a identidade dos recursos Azure e continua a apontar-lhe no antigo grupo de recursos.

**Solução** 
 
Desencadeie uma atualização sobre o VM para que possa obter valores corretos para as identidades geridas para os recursos Azure. Pode fazer uma alteração de propriedade em VM para atualizar a referência às identidades geridas para identidade de recursos Azure. Por exemplo, pode definir um novo valor de etiqueta no VM com o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando define uma nova etiqueta "fixVM" com um valor de 1 no VM. 
 
Ao definir esta propriedade, o VM atualiza com as identidades geridas corretas para o recurso de recursos Azure URI, e então você deve ser capaz de iniciar o VM. 
 
Uma vez iniciado o VM, a etiqueta pode ser removida utilizando o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transferência de uma subscrição entre diretórios AD da Azure

As identidades geridas não são atualizadas quando uma subscrição é transferida/transferida para outro diretório. Como resultado, quaisquer identidades geridas atribuídas pelo sistema ou atribuídas pelo utilizador serão quebradas. 

Solução alternativa para identidades geridas numa subscrição que foi transferida para outro diretório:

 - Para identidades geridas atribuídas pelo sistema: desativar e reativar. 
 - Para as identidades geridas atribuídas pelo utilizador: eliminar, recriar e ligá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

Para obter mais informações, veja [Transferir uma subscrição do Azure para outro diretório do Azure AD](../../role-based-access-control/transfer-subscription.md).


## <a name="next-steps"></a>Passos seguintes

Pode rever o nosso artigo listando os [serviços que suportam identidades geridas](services-support-managed-identities.md) e as [nossas perguntas frequentes](managed-identities-faq.md)
