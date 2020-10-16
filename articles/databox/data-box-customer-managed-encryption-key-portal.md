---
title: Utilize o portal Azure para configurar chaves geridas pelo cliente para a Azure Data Box
description: Aprenda a usar o portal Azure para configurar chaves geridas pelo cliente com cofre de chave Azure para Azure Data Box. As teclas geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 31147d534109e0d74d33d102075c69eeb703496e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739940"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Utilize chaves geridas pelo cliente no Cofre da Chave Azure para a Azure Data Box

A Azure Data Box protege a chave de desbloqueio do dispositivo (também conhecida como palavra-passe do dispositivo) que é usada para bloquear o dispositivo através de uma chave de encriptação. Por predefinição, a chave de desbloqueio do dispositivo para uma ordem de Caixa de Dados é encriptada com uma chave gerida pela Microsoft. Para um controlo adicional sobre a chave de desbloqueio do dispositivo, também pode fornecer uma chave gerida pelo cliente. 

As chaves geridas pelo cliente devem ser criadas e armazenadas num Cofre de Chaves Azure. Para mais informações sobre o Azure Key Vault, veja [o que é Azure Key Vault?](../key-vault/general/overview.md)

Este artigo mostra como utilizar as chaves geridas pelo cliente com a Azure Data Box no [portal Azure](https://portal.azure.com/). Este artigo aplica-se tanto aos dispositivos Azure Data Box como aos dispositivos Azure Data Box Heavy.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que:

1. Criou uma encomenda Azure Data Box de acordo com as instruções em [Tutorial: Order Azure Data Box](data-box-deploy-ordered.md).

2. Tem um Cofre de Chaves Azure existente com uma chave que pode usar para proteger a chave de desbloqueio do seu dispositivo. Para aprender a criar um cofre-chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault using the Azure Portal](../key-vault/secrets/quick-create-portal.md).

    - **Apagou suavemente** e **não expurgará** o cofre da chave existente. Estas propriedades não são ativadas por padrão. Para ativar estas propriedades, consulte as secções intituladas **Permitir a eliminação suave** e a **proteção da purga ativada** num dos seguintes artigos:

        - [Como utilizar o soft-delete com PowerShell](../key-vault/general/soft-delete-powershell.md).
        - [Como utilizar o soft-delete com CLI](../key-vault/general/soft-delete-cli.md).
    - O cofre-chave existente deve ter uma chave RSA de tamanho igual ou superior a 2048. Para obter mais informações sobre as teclas, consulte [as teclas do Cofre da Chave Azure](../key-vault/keys/about-keys.md).
    - O cofre-chave deve estar na mesma região que as contas de armazenamento utilizadas para os seus dados. Várias contas de armazenamento podem ser ligadas com o seu recurso Azure Data Box.
    - Se não tiver um cofre de chaves existente, também pode criá-lo em linha, conforme descrito na secção seguinte.

## <a name="enable-keys"></a>Ativar chaves

Configurar a chave gerida pelo cliente para a sua Caixa de Dados Azure é opcional. Por predefinição, a Data Box utiliza uma chave gerida pela Microsoft para proteger a sua tecla BitLocker. Para ativar uma chave gerida pelo cliente no portal Azure, siga estes passos:

1. Aceda à lâmina **de visão geral** para a sua encomenda de Caixa de Dados.

    ![Folha geral da encomenda da Caixa de Dados](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Ir para **Definições > Encriptação**. No **tipo de encriptação,** pode escolher como pretende proteger a tecla de desbloqueio do seu dispositivo. Por predefinição, uma chave gerida pela Microsoft é utilizada para proteger a palavra-passe do seu dispositivo. 

    ![Escolha a opção de encriptação](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Selecione o tipo de encriptação como **a chave gerida pelo Cliente**. Depois de ter selecionado a chave gerida pelo cliente, **selecione um cofre e uma chave de teclas**.

    ![Selecione a chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. Na **tecla Select da lâmina Azure Key Vault,** a subscrição é automaticamente povoada. Para **o cofre key,** pode selecionar um cofre de chaves existente na lista de dropdown.

    ![Criar novo Cofre de Chaves Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    Também pode selecionar **Criar novo** para criar um novo cofre de chaves. Na lâmina do **cofre da chave Create,** insira o grupo de recursos e o nome do cofre da chave. Certifique-se de que a proteção **para eliminar** e **purgar** suavemente está ativada. Aceite todos os outros incumprimentos. Selecione **Review + Criar**.

    ![Criar novo Cofre da Chave Azure 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Reveja as informações associadas ao cofre de chaves e selecione **Criar**. Espere alguns minutos para a criação do cofre de chaves para completar.

    ![Criar cofre de chaves Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. Na **tecla Select a partir do Azure Key Vault,** pode selecionar uma chave no cofre de chaves existente.

    ![Criar nova chave no Cofre da Chave Azure 3](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Se quiser criar uma nova chave, selecione **Criar nova** para criar uma chave. O tamanho da chave RSA pode ser 2048 ou maior.

    ![Criar nova chave no Cofre da Chave Azure 4](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Forneça o nome da sua chave, aceite as outras predefinições e selecione **Criar**. 

    ![Criar nova chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. Foi-lhe notificada que uma chave foi criada no seu cofre. Selecione a **versão** e, em seguida, escolha **Selecione**.

    ![Nova chave criada no cofre chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. No painel **do tipo encriptação,** pode ver o cofre da chave e a chave selecionada para a chave gerida pelo cliente.

    ![Chave e cofre chave para chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Guarde a chave. 

    ![Salvar a chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    O URL-chave é apresentado sob **o tipo de encriptação**.

    ![URL chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Pode desativar a chave gerida pela Microsoft e passar para a chave gerida pelo cliente em qualquer fase da encomenda data box. No entanto, uma vez criada a chave gerida pelo cliente, não pode voltar a ser gerida pela Microsoft.

## <a name="troubleshoot-errors"></a>Resolver erros

Se receber quaisquer erros relacionados com a sua chave gerida pelo cliente, utilize a seguinte tabela para resolver problemas.

| Código de erro| Detalhes do erro| Recuperável?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Não foi possível obter a chave-passe, uma vez que a chave gerida pelo cliente está desativada.| Sim, ativando a versão chave.|
| SsemUserErrorEncryptionKeyExpired| Não foi possível ir buscar a chave-passe, uma vez que a chave gerida pelo cliente expirou.| Sim, ativando a versão chave.|
| SsemUserErrorKeyDetailsNotFound| Não foi possível ir buscar a chave de passagem, uma vez que a chave gerida pelo cliente não foi encontrada.| Se apagou o cofre da chave, não poderá recuperar a chave gerida pelo cliente.  Se você emigrou o cofre chave para um inquilino diferente, consulte [Change a key vault iD após um movimento de subscrição](../key-vault/general/move-subscription.md). Se apagar o cofre da chave:<ol><li>Sim, se estiver na duração da proteção da purga, utilizando os passos da [Recuperar um cofre de chaves.](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)</li><li>Não, se for para além da duração da proteção da purga.</li></ol><br>Caso contrário, se o cofre-chave sofreu uma migração de inquilinos, sim, pode ser recuperado usando um dos passos abaixo: <ol><li>Reverta o cofre de volta para o velho inquilino.</li><li>Desa `Identity = None` parte e, em seguida, reesuse o valor para `Identity = SystemAssigned` . Isto elimina e recria a identidade uma vez criada a nova identidade. Ativar `Get` `Wrap` , e `Unwrap` permissões para a nova identidade na política de acesso do cofre-chave.</li></ol> |
| SsemUserErrorKeyVaultBadRequestExcepção| Não foi possível obter a chave de acesso à chave, uma vez que o acesso à chave gerida pelo cliente é revogado.| Sim, verifique se: <ol><li>O cofre ainda tem o MSI na política de acesso.</li><li>A política de acesso fornece permissões para Obter, Embrulhar, Desembrulhar.</li><li>Se o cofre de chaves estiver num vNet atrás da firewall, verifique se **o Microsoft Trust Services** está ativado.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Não foi possível ir buscar a chave-chave, uma vez que o cofre-chave associado para a chave gerida pelo cliente não foi encontrado. | Se apagou o cofre da chave, não poderá recuperar a chave gerida pelo cliente.  Se você emigrou o cofre chave para um inquilino diferente, consulte [Change a key vault iD após um movimento de subscrição](../key-vault/general/move-subscription.md). Se apagar o cofre da chave:<ol><li>Sim, se estiver na duração da proteção da purga, utilizando os passos da [Recuperar um cofre de chaves.](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)</li><li>Não, se for para além da duração da proteção da purga.</li></ol><br>Caso contrário, se o cofre-chave sofreu uma migração de inquilinos, sim, pode ser recuperado usando um dos passos abaixo: <ol><li>Reverta o cofre de volta para o velho inquilino.</li><li>Desa `Identity = None` parte e, em seguida, reesuse o valor para `Identity = SystemAssigned` . Isto elimina e recria a identidade uma vez criada a nova identidade. Ativar `Get` `Wrap` , e `Unwrap` permissões para a nova identidade na política de acesso do cofre-chave.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Não foi possível ir buscar a chave de passagem, uma vez que a chave gerida pelo cliente não foi encontrada.| Sim, verifique se: <ol><li>O cofre ainda tem o MSI na política de acesso.</li><li>A identidade é do tipo sistema atribuído.</li><li>Ativar permissões de Obter, Embrulhar e Desembrulhar a identidade na política de acesso do cofre-chave.</li></ol>|
| Erro genérico  | Não consegui pegar a chave.| Isto é um erro genérico. Contacte o Microsoft Support para resolver o erro e determinar os próximos passos.|


## <a name="next-steps"></a>Passos seguintes

- [O que é Azure Key Vault?](../key-vault/general/overview.md)
