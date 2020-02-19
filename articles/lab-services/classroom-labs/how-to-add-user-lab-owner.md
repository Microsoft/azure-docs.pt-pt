---
title: Como adicionar proprietários adicionais a um laboratório em Serviços de Laboratório Azure
description: Este artigo mostra-lhe como um administrador pode adicionar um utilizador como proprietário a um laboratório em Serviços de Laboratório Azure.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443521"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Como adicionar proprietários adicionais a um laboratório existente nos Serviços de Laboratório Azure
Este artigo mostra-lhe como você, como administrador, pode adicionar donos adicionais a um laboratório existente.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Adicione o utilizador ao papel do leitor para a conta de laboratório
Para adicionar um utilizador como proprietário adicional a um laboratório existente, tem primeiro de dar ao utilizador permissões de **leitura** na conta de laboratório.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu esquerdo. Procure **por Serviços laboratoriais**e, em seguida, selecione-os.
3. Selecione a sua conta de **laboratório** da lista. 
2. Na **página da Conta Lab,** selecione Controlo de **Acesso (IAM)** no menu esquerdo. 
2. Na página de controlo de **acesso (IAM),** selecione **Adicionar** na barra de ferramentas e a **função de adicionar função**de adicionar .

    ![Atribuição de funções para a conta de laboratório ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na página **adicionar uma função** de atribuição, faça os seguintes passos: 
    1. Selecione **Leitor** para o **papel**. 
    2. Selecione o utilizador. 
    3. Selecione **Guardar**. 

        ![Adicione o utilizador ao papel do leitor para a conta de laboratório ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Adicione o utilizador ao papel do proprietário para o laboratório

1. De volta à página **da Conta lab,** selecione **Todos os laboratórios** no menu esquerdo.
2. Selecione o **laboratório** ao qual pretende adicionar o utilizador como proprietário. 
    
    ![Selecionar o laboratório ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Na página **lab,** selecione controlo de **acesso (IAM)** no menu esquerdo.
4. Na página de controlo de **acesso (IAM),** selecione **Adicionar** na barra de ferramentas e a **função de adicionar função**de adicionar .
5. Na página **adicionar uma função** de atribuição, faça os seguintes passos: 
    1. Selecione **Proprietário** para o **papel**. 
    2. Selecione o utilizador. 
    3. Selecione **Guardar**. 

## <a name="next-steps"></a>Passos seguintes
Confirme que o utilizador vê o laboratório ao entrar no [portal dos Serviços](https://labs.azure.com)laboratoriais.