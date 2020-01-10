---
title: Como adicionar o usuário como um proprietário de laboratório no Azure Lab Services
description: Este artigo mostra como um administrador pode adicionar um usuário como um proprietário a um laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480856"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>Como adicionar um usuário como um proprietário de um laboratório de sala de aula no Azure Lab Services
Este artigo mostra como adicionar um usuário como um proprietário de um laboratório no Azure Lab Services.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Adicionar usuário à função leitor da conta do laboratório
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu à esquerda. Procure **serviços de laboratório**e, em seguida, selecione-o.
3. Selecione sua **conta de laboratório** na lista. 
2. Na **página conta do laboratório**, selecione **controle de acesso (iam)** no menu à esquerda. 
2. Na página **controle de acesso (iam)** , selecione **Adicionar** na barra de ferramentas e selecione **Adicionar atribuição de função**.

    ![Atribuição de função para a conta do laboratório ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na página **Adicionar uma atribuição de função** , execute as seguintes etapas: 
    1. Selecione **leitor** para a **função**. 
    2. Selecione o utilizador. 
    3. Selecione **Guardar**. 

        ![Adicionar usuário à função leitor da conta do laboratório ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Adicionar usuário à função de proprietário do laboratório

1. De volta à página **conta do laboratório** , selecione **todos os laboratórios** no menu à esquerda.
2. Selecione o **laboratório** ao qual você deseja adicionar o usuário como um proprietário. 
    
    ![Selecionar o laboratório ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Na página do **laboratório** , selecione **controle de acesso (iam)** no menu à esquerda.
4. Na página **controle de acesso (iam)** , selecione **Adicionar** na barra de ferramentas e selecione **Adicionar atribuição de função**.
5. Na página **Adicionar uma atribuição de função** , execute as seguintes etapas: 
    1. Selecione **proprietário** para a **função**. 
    2. Selecione o utilizador. 
    3. Selecione **Guardar**. 

## <a name="next-steps"></a>Passos seguintes
Confirme se o usuário vê o laboratório após fazer logon no [portal de serviços de laboratório](https://labs.azure.com).