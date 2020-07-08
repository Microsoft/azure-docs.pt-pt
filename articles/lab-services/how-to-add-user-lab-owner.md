---
title: Como adicionar proprietários adicionais a um laboratório em Azure Lab Services
description: Este artigo mostra-lhe como um administrador pode adicionar um utilizador como proprietário a um laboratório nos Serviços Azure Lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6671a3070dae672769eecf59d614d3b75455ef5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445870"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Como adicionar proprietários adicionais a um laboratório existente nos Serviços de Laboratório Azure
Este artigo mostra-lhe como você, como administrador, pode adicionar proprietários adicionais a um laboratório existente.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Adicione o utilizador ao papel de leitor para a conta de laboratório
Para adicionar um utilizador como proprietário adicional a um laboratório existente, deve primeiro dar ao utilizador permissões de **leitura** na conta do laboratório.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu esquerdo. Procure por **Serviços de Laboratório**e, em seguida, selecione-o.
3. Selecione a sua conta de **laboratório** na lista. 
2. Na **página Conta lab,** selecione **Access Control (IAM)** no menu esquerdo. 
2. Na página **'Controlo de Acesso' (IAM),** selecione **Adicionar** na barra de ferramentas e a atribuição de **função selete Add**.

    ![Atribuição de funções para a conta de laboratório ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Na página **De atribuição de funções,** faça os seguintes passos: 
    1. Selecione **Reader** para o **papel**. 
    2. Selecione o utilizador. 
    3. Selecione **Guardar**. 

        ![Adicione o utilizador ao papel de leitor para a conta de laboratório ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Adicione o utilizador à função de proprietário para o laboratório

1. De volta à página **da Conta de Laboratório,** selecione **Todos os laboratórios** no menu esquerdo.
2. Selecione o **laboratório** ao qual pretende adicionar o utilizador como proprietário. 
    
    ![Selecionar o laboratório ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. Na página **Lab,** selecione **Access control (IAM)** no menu esquerdo.
4. Na página **'Controlo de Acesso' (IAM),** selecione **Adicionar** na barra de ferramentas e a atribuição de **função selete Add**.
5. Na página **De atribuição de funções,** faça os seguintes passos: 
    1. Selecione **Proprietário** para a **função**. 
    2. Selecione o utilizador. 
    3. Selecione **Guardar**. 

## <a name="next-steps"></a>Próximos passos
Confirme que o utilizador vê o laboratório ao iniciar sessão no [portal lab services](https://labs.azure.com).