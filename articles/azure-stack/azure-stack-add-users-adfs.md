---
title: Adicionar utilizadores ao ADFS do Azure Stack | Documentos da Microsoft
description: Saiba como adicionar utilizadores para as implementações ADFS do Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 1b47739200c79317273ea0c788f21a7ee4a3b818
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648511"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Adicionar utilizadores do Azure Stack no AD FS
Pode utilizar o **Active Directory Users and Computers** snap-in para adicionar utilizadores adicionais a um ambiente do Azure Stack tirar partido do AD FS como respetivo fornecedor de identidade.

## <a name="add-windows-server-active-directory-users"></a>Adicionar utilizadores do Active Directory do Windows Server
> [!TIP]
> Este exemplo utiliza o padrão azurestack ASDK active directory. 

1. Inicie sessão no computador com uma conta que fornecer acesso a ferramentas administrativas do Windows e abra uma consola de gestão do novo Microsoft (MMC).
2. Selecione **ficheiro > Adicionar ou remover snap-in**.
3. Selecione **utilizadores e computadores do Active Directory** > **Azurestack** > **utilizadores**.
4. Selecione **ação** > **nova** > **utilizador**.
5. Novo objeto – utilizador, fornecem detalhes de informações do utilizador. Selecione **Seguinte**.
6. Forneça e confirme uma palavra-passe.
7. Selecione **seguinte** para finalizar os valores. Selecione **concluir** para criar o utilizador.


## <a name="next-steps"></a>Passos Seguintes
[Criar principais de serviço](azure-stack-create-service-principals.md)