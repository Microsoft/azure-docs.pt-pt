---
title: Utilize o portal Azure para configurar as chaves geridas pelo cliente para a Caixa de Dados Azure
description: Saiba como utilizar o portal Azure para configurar chaves geridas pelo cliente com o Cofre de Chaves Azure para a Caixa de Dados Azure. As chaves geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 6f543b3f5c2bb7d4949c431580771c4b0d965e4d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125489"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Utilize chaves geridas pelo cliente no Cofre de Chaves Azure para caixa de dados Azure

A Azure Data Box protege a chave de desbloqueio do dispositivo (também conhecida como palavra-passe do dispositivo) que é usada para bloquear o dispositivo através de uma chave de encriptação. Por defeito, a chave de desbloqueio do dispositivo para uma ordem data box é encriptada com uma chave gerida pela Microsoft. Para um controlo adicional sobre a chave de desbloqueio do dispositivo, também pode fornecer uma chave gerida pelo cliente. 

As chaves geridas pelo cliente devem ser criadas e armazenadas num Cofre de Chaves Azure. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](../key-vault/general/overview.md)

Este artigo mostra como usar chaves geridas pelo cliente com a Caixa de Dados Azure no [portal Azure](https://portal.azure.com/). Este artigo aplica-se tanto aos dispositivos Azure Data Box como aos dispositivos Azure Data Box Heavy.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que:

1. Criou uma ordem de caixa de dados Azure de acordo com as instruções no [Tutorial: Order Azure Data Box](data-box-deploy-ordered.md).

2. Tem um Cofre de Chave Azure existente com uma chave que pode utilizar para proteger a chave de desbloqueio do dispositivo. Para aprender a criar um cofre chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault utilizando o portal Azure](../key-vault/secrets/quick-create-portal.md).

    - **Eliminação suave** e **não expurgar** estão definidos no cofre da chave existente. Estas propriedades não são ativadas por defeito. Para ativar estas propriedades, consulte as secções intituladas **Enableing soft-delete** and **Enableing Purge Protection** num dos seguintes artigos:

        - [Como utilizar soft-delete com powerShell](../key-vault/general/soft-delete-powershell.md).
        - Como utilizar o [soft-delete com o CLI](../key-vault/general/soft-delete-cli.md).
    - O cofre-chave existente deve ter uma chave RSA de tamanho 2048 ou mais. Para mais informações sobre as chaves, consulte as chaves do cofre de [chaves Azure](../key-vault/keys/about-keys.md).
    - O cofre de chaves deve estar na mesma região que as contas de armazenamento utilizadas para os seus dados. Várias contas de armazenamento podem ser ligadas com o seu recurso Azure Data Box.
    - Se não tiver um cofre de chaves existente, também pode criá-lo em linha, como descrito na secção seguinte.

## <a name="enable-keys"></a>Ativar chaves

Configurar a chave gerida pelo cliente para a sua Caixa de Dados Azure é opcional. Por padrão, a Data Box utiliza uma chave gerida pela Microsoft para proteger a sua chave BitLocker. Para permitir uma chave gerida pelo cliente no portal Azure, siga estes passos:

1. Vá à lâmina **de visão geral** para a sua encomenda da Caixa de Dados.

    ![Lâmina de visão geral da ordem da Caixa de Dados](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Ir para **Definições > Encriptação**. Sob o tipo de **Encriptação,** pode escolher como deseja proteger a chave de desbloqueio do dispositivo. Por predefinição, uma chave gerida pela Microsoft é utilizada para proteger o seu dispositivo de desbloquear palavra-passe. 

    ![Escolha opção de encriptação](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Selecione o tipo de encriptação como **chave gerida pelo Cliente**. Depois de ter selecionado a chave gerida pelo cliente, **selecione um cofre e uma chave.**

    ![Selecione chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. Na tecla Select da lâmina **Do Cofre de Chaves Azure,** a subscrição é automaticamente povoada. Para o **cofre key,** pode selecionar um cofre chave existente da lista de dropdown.

    ![Criar novo Cofre chave Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    Também pode selecionar **Criar novo** para criar um novo cofre chave. Na lâmina do **cofre da chave Criar,** introduza o grupo de recursos e o nome do cofre chave. Certifique-se de que a **proteção** **soft delete** e purga está ativada. Aceite todos os outros incumprimentos. Selecione **Rever + Criar**.

    ![Criar novo Cofre chave Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Reveja as informações associadas ao seu cofre chave e selecione **Criar**. Espere uns minutos para que a criação do cofre esteja completa.

    ![Criar cofre de chaves azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. Na **tecla Select do Cofre de Chaves Azure,** pode selecionar uma chave no cofre de chaves existente.

    ![Criar nova chave no Cofre chave Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Se quiser criar uma nova tecla, selecione **Criar nova** para criar uma chave. O tamanho da chave RSA pode ser 2048 ou superior.

    ![Criar nova chave no Cofre chave Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Forneça o nome para a sua chave, aceite os outros predefinições e selecione **Criar**. 

    ![Criar nova chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. Foi notificado de que uma chave foi criada no seu cofre chave. Selecione a **Versão** e, em seguida, escolha **Selecionar**.

    ![Nova chave criada no cofre chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. No painel do **tipo Encriptação,** pode ver o cofre da chave e a chave selecionada para a sua chave gerida pelo cliente.

    ![Cofre chave e chave para chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Guarde a chave. 

    ![Salvar a chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    O URL da chave é apresentado sob o tipo de **encriptação**.

    ![URL de chave gerido pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Pode desativar a chave gerida pela Microsoft e passar para a chave gerida pelo cliente em qualquer fase da encomenda data Box. No entanto, uma vez criada a chave gerida pelo cliente, não pode voltar à chave gerida pela Microsoft.

## <a name="troubleshoot-errors"></a>Resolver erros

Se receber algum erro relacionado com a chave gerida pelo cliente, utilize a tabela seguinte para resolver problemas.

| Código de erro| Mensagem de erro| Detalhes|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Não conseguiu obter a chave-passe, uma vez que a chave gerida pelo cliente está desativada.| Sim, permitindo a versão chave.|
| SsemUserErrorCryptonKeyExpired| Não conseguiu obter a chave-passe uma vez que a chave gerida pelo cliente expirou.| Sim, permitindo a versão chave.|
| SsemUserErrorKeyDetailsNotFound| Não foi possível obter a chave-passe, uma vez que a chave gerida pelo cliente não foi encontrada.| Se apagou o cofre da chave, não pode recuperar a chave gerida pelo cliente.  Se você emigrou o cofre chave para outro inquilino, veja [Change um cofre chave id inquilino após um movimento de assinatura](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). Se apagar o cofre da chave:<ol><li>Sim, se estiver na duração da proteção da purga, utilizando os passos em [Recuperar um cofre chave](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault).</li><li>Não, se for além da duração da proteção da purga.</li></ol><br>Caso contrário, se o cofre principal foi submetido a uma migração de inquilinos, sim, pode ser recuperado usando um dos degraus abaixo: <ol><li>Reverta o cofre de volta para o velho inquilino.</li><li>Definir `Identity = None` e, em seguida, repor o valor para `Identity = SystemAssigned` . Isto elimina e recria a identidade uma vez criada a nova identidade. Ativar, `Get` `Wrap` e `Unwrap` permissões para a nova identidade na política de acesso do cofre chave.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| Não conseguiu obter a chave-passe, uma vez que o acesso à chave gerido pelo cliente é revogado.| Sim, verifique se: <ol><li>O cofre ainda tem o MSI na política de acesso.</li><li>A política de acesso fornece permissões para obter, embrulhar, desembrulhar.</li><li>Se o cofre da chave estiver num vNet atrás da firewall, verifique se o **Allow Microsoft Trust Services** está ativado.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Não foi possível obter a chave-passe, uma vez que o cofre-chave associado para a chave gerida pelo cliente não foi encontrado. | Se apagou o cofre da chave, não pode recuperar a chave gerida pelo cliente.  Se você emigrou o cofre chave para outro inquilino, veja [Change um cofre chave id inquilino após um movimento de assinatura](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). Se apagar o cofre da chave:<ol><li>Sim, se estiver na duração da proteção da purga, utilizando os passos em [Recuperar um cofre chave](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault).</li><li>Não, se for além da duração da proteção da purga.</li></ol><br>Caso contrário, se o cofre principal foi submetido a uma migração de inquilinos, sim, pode ser recuperado usando um dos degraus abaixo: <ol><li>Reverta o cofre de volta para o velho inquilino.</li><li>Definir `Identity = None` e, em seguida, repor o valor para `Identity = SystemAssigned` . Isto elimina e recria a identidade uma vez criada a nova identidade. Ativar, `Get` `Wrap` e `Unwrap` permissões para a nova identidade na política de acesso do cofre chave.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Não foi possível obter a chave-passe, uma vez que a chave gerida pelo cliente não foi encontrada.| Sim, verifique se: <ol><li>O cofre ainda tem o MSI na política de acesso.</li><li>Identidade é de tipo sistema atribuído.</li><li>Enable Get, Wrap e Desembrulhar permissões para a identidade na política de acesso do cofre chave.</li></ol>|
| Erro genérico  | Não consegui a chave da chave.| Isto é um erro genérico. Contacte o Microsoft Support para resolver o erro e determinar os próximos passos.|


## <a name="next-steps"></a>Passos seguintes

- [O que é o Cofre chave Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)