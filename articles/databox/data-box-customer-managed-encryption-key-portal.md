---
title: Utilize o portal Azure para gerir as chaves geridas pelo cliente para a Azure Data Box
description: Aprenda a usar o portal Azure para criar e gerir chaves geridas pelo cliente com o Azure Key Vault para uma Caixa de Dados Azure. As teclas geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: e6b588ddea5bf4b4c92e89d9cebb37b09b9a86af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791549"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Utilize chaves geridas pelo cliente no Cofre da Chave Azure para a Azure Data Box

A Azure Data Box protege a chave de desbloqueio do dispositivo (também conhecida como palavra-passe do dispositivo), que é usada para bloquear um dispositivo, através de uma chave de encriptação. Por padrão, esta chave de encriptação é uma chave gerida pela Microsoft. Para controlo adicional, pode utilizar uma chave gerida pelo cliente.

A utilização de uma chave gerida pelo cliente não afeta a forma como os dados do dispositivo são encriptados. Só afeta a forma como a chave de desbloqueio do dispositivo é encriptada.

Para manter este nível de controlo durante todo o processo de encomenda, utilize uma chave gerida pelo cliente quando criar a sua encomenda. Para mais informações, consulte [Tutorial: Encomende caixa de dados Azure](data-box-deploy-ordered.md).

Este artigo mostra como ativar uma chave gerida pelo cliente para a sua encomenda de Caixa de Dados existente no [portal Azure](https://portal.azure.com/). Você vai descobrir como alterar o cofre chave, chave, versão ou identidade para a sua chave gerida pelo cliente atual, ou voltar a usar uma chave gerida pela Microsoft.

Este artigo aplica-se a dispositivos Azure Data Box e Azure Data Box Heavy.

## <a name="requirements"></a>Requisitos

A chave gerida pelo cliente para uma encomenda de Caixa de Dados deve satisfazer os seguintes requisitos:

- A chave deve ser criada e armazenada num Cofre de Chave Azure que tenha **exclusão suave** e **não purgue** ativado. Para obter mais informações, veja [O que é o Azure Key Vault?](../key-vault/general/overview.md) Pode criar um cofre e uma chave chave enquanto cria ou atualiza o seu pedido.

- A chave deve ser uma chave RSA de tamanho igual ou superior a 2048.

## <a name="enable-key"></a>Ativar a chave

Para ativar uma chave gerida pelo cliente para a sua encomenda de Caixa de Dados existente no portal Azure, siga estes passos:

1. Aceda ao ecrã **de visão geral** para a sua encomenda caixa de dados.

    ![Ecrã geral de uma encomenda de Caixa de Dados - 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Vá a **Definições > Encriptação** e selecione **a tecla gerida pelo Cliente**. Em seguida, **selecione selecione uma chave e um cofre de chaves**.

    ![Selecione a opção de encriptação de chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Na **tecla Select do ecrã Azure Key Vault,** a sua subscrição é automaticamente povoada.

 3. Para **o cofre key**, pode selecionar um cofre de teclas existente na lista de dropdown ou selecionar Criar **novo** e criar um novo cofre de chaves.

     ![Opções de cofre chave ao selecionar uma chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Para criar um novo cofre de chaves, insira a subscrição, grupo de recursos, nome do cofre chave e outras informações no novo ecrã do **cofre da chave Create.** Nas **opções de recuperação,** certifique-se de que a proteção **de eliminação** e **purga** macia está ativada. Em seguida, selecione **Review + Create**.

      ![Reveja e crie o Cofre da Chave Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Reveja as informações para o seu cofre chave e selecione **Criar**. Espere alguns minutos para a criação do cofre para completar.

       ![Crie o Cofre de Chaves Azure com as suas definições](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. Na **tecla Select a partir do ecrã Azure Key Vault,** pode selecionar uma chave existente a partir do cofre de teclas ou criar uma nova.

    ![Selecione a chave do Cofre da Chave Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Se pretender criar uma nova chave, **selecione Criar nova**. Tem de usar uma chave RSA. O tamanho pode ser 2048 ou maior.

    ![Criar nova chave no Cofre da Chave Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Introduza um nome para a sua nova tecla, aceite as outras predefinições e selecione **Criar**. Será notificado de que foi criada uma chave no seu cofre.

    ![Nomeie nova chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. Para **a versão**, pode selecionar uma versão chave existente na lista de drop-down.

    ![Selecione versão para nova chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Se pretender gerar uma nova versão chave, selecione **Criar novo**.

    ![Abra uma caixa de diálogo para criar uma nova versão chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Escolha as definições para a nova versão chave e selecione **Criar**.

    ![Criar uma nova versão chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Quando tiver selecionado um cofre, chave e versão chave, escolha **Select**.

    ![Uma chave em um cofre de chave Azure](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    As definições **do tipo Encriptação** mostram o cofre e a chave que escolheu.

    ![Chave e cofre chave para uma chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Selecione o tipo de identidade a utilizar para gerir a chave gerida pelo cliente para este recurso. Pode utilizar a identidade atribuída ao **sistema** que foi gerada durante a criação da encomenda ou escolher uma identidade atribuída ao utilizador.

    Uma identidade atribuída ao utilizador é um recurso independente que pode usar para gerir o acesso aos recursos. Para obter mais informações, consulte [os tipos de identidade geridos.](../active-directory/managed-identities-azure-resources/overview.md)

    ![Selecione o tipo de identidade](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Para atribuir uma identidade de utilizador, selecione **Utilizador designado**. Em seguida, **selecione Selecione uma identidade de utilizador** e selecione a identidade gerida que pretende utilizar.

    ![Selecione uma identidade para usar](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    Não pode criar uma nova identidade de utilizador aqui. Para saber como criar um, consulte [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador utilizando o portal Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

    A identidade do utilizador selecionada é mostrada nas definições do **tipo encriptação.**

    ![Uma identidade de utilizador selecionada mostrada nas definições do tipo de encriptação](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. **Selecione Guardar** para guardar as definições atualizadas **do tipo de encriptação.**

     ![Guarde a sua chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    O URL-chave é apresentado sob **o tipo de encriptação**.

    ![URL chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Alterar chave

Para alterar o cofre, chave e/ou versão chave para a chave gerida pelo cliente que está a utilizar, siga estes passos:

1. No **ecrã de visão geral** para a sua encomenda de Caixa de **Dados,** aceda à encriptação de Definições  >  e clique na **tecla 'Alterar'.**

    ![Ecrã geral de uma encomenda de Caixa de Dados com chave gerida pelo cliente - 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Escolha **Selecione um cofre e uma chave diferentes.**

    ![Ecrã geral de uma encomenda de Caixa de Dados, Selecione uma chave diferente e opção de cofre chave](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. A **tecla Select do** ecrã do cofre de chaves mostra a subscrição, mas nenhuma chave, chave ou versão chave. Pode es fazer qualquer uma das seguintes alterações:

   - Selecione uma chave diferente do mesmo cofre de chaves. Terá de selecionar o cofre de chaves antes de selecionar a chave e a versão.

   - Selecione um cofre de chave diferente e atribua uma nova chave.

   - Altere a versão para a tecla atual.
   
    Quando terminar as alterações, escolha **Select**.

    ![Escolha a opção de encriptação - 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Selecione **Guardar**.

    ![Guardar definições de encriptação atualizadas - 1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Alterar identidade

Para alterar a identidade utilizada para gerir o acesso à chave gerida pelo cliente para esta encomenda, siga estes passos:

1. No ecrã **de visão geral** para a sua encomenda de Caixa de Dados concluída, aceda à encriptação **de Definições**  >  .

2. Faça uma das seguintes alterações:

     - Para alterar para uma identidade de utilizador diferente, clique em **Selecionar uma identidade de utilizador diferente**. Em seguida, selecione uma identidade diferente no painel do lado direito do ecrã e escolha **Selecione**.

       ![Opção para alterar a identidade atribuída ao utilizador para uma chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Para mudar para a identidade atribuída ao sistema gerada durante a criação de encomendas, selecione **Sistema atribuído** por Tipo de **identidade Select**.

     ![Opção para alterar para um sistema atribuído a uma chave gerida pelo cliente](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Selecione **Guardar**.

    ![Guardar definições de encriptação atualizadas - 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Utilize a chave gerida pela Microsoft

Para mudar de usar uma chave gerida pelo cliente para a chave gerida pela Microsoft para a sua encomenda, siga estes passos:

1. No ecrã **de visão geral** para a sua encomenda de Caixa de Dados concluída, aceda à encriptação **de Definições**  >  .

2. Por **Seleção do tipo**, selecione a **tecla gerida pela Microsoft**.

    ![Ecrã geral de uma encomenda de Caixa de Dados - 5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Selecione **Guardar**.

    ![Guarde as definições de encriptação atualizadas para uma chave gerida pela Microsoft](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Resolver erros

Se receber quaisquer erros relacionados com a sua chave gerida pelo cliente, utilize a seguinte tabela para resolver problemas.

| Código de erro| Detalhes do erro| Recuperável?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Não foi possível obter a chave-passe, uma vez que a chave gerida pelo cliente está desativada.| Sim, ativando a versão chave.|
| SsemUserErrorEncryptionKeyExpired| Não foi possível ir buscar a chave-passe, uma vez que a chave gerida pelo cliente expirou.| Sim, ativando a versão chave.|
| SsemUserErrorKeyDetailsNotFound| Não foi possível ir buscar a chave de passagem, uma vez que a chave gerida pelo cliente não foi encontrada.| Se apagou o cofre da chave, não poderá recuperar a chave gerida pelo cliente.  Se você emigrou o cofre chave para um inquilino diferente, consulte [Change a key vault iD após um movimento de subscrição](../key-vault/general/move-subscription.md). Se apagar o cofre da chave:<ol><li>Sim, se estiver na duração da proteção da purga, utilizando os passos da [Recuperar um cofre de chaves.](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)</li><li>Não, se for para além da duração da proteção da purga.</li></ol><br>Caso contrário, se o cofre-chave sofreu uma migração de inquilinos, sim, pode ser recuperado usando um dos passos abaixo: <ol><li>Reverta o cofre de volta para o velho inquilino.</li><li>Desa `Identity = None` parte e, em seguida, reesuse o valor para `Identity = SystemAssigned` . Isto elimina e recria a identidade uma vez criada a nova identidade. Ativar `Get` `Wrap` , e `Unwrap` permissões para a nova identidade na política de acesso do cofre-chave.</li></ol> |
| SsemUserErrorKeyVaultBadRequestExcepção | Aplicou uma chave gerida pelo cliente, mas o acesso à chave não foi concedido ou foi revogado, ou não foi possível aceder ao cofre de chaves devido à ativação da firewall. | Adicione a identidade selecionada no cofre de chaves para permitir o acesso à chave gerida pelo cliente. Se o cofre-chave tiver a firewall ativada, mude para uma identidade atribuída ao sistema e adicione uma chave gerida pelo cliente. Para mais informações, consulte como [ativar a chave.](#enable-key) |
| SsemUserErrorKeyVaultDetailsNotFound| Não foi possível ir buscar a chave-chave, uma vez que o cofre-chave associado para a chave gerida pelo cliente não foi encontrado. | Se apagou o cofre da chave, não poderá recuperar a chave gerida pelo cliente.  Se você emigrou o cofre chave para um inquilino diferente, consulte [Change a key vault iD após um movimento de subscrição](../key-vault/general/move-subscription.md). Se apagar o cofre da chave:<ol><li>Sim, se estiver na duração da proteção da purga, utilizando os passos da [Recuperar um cofre de chaves.](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)</li><li>Não, se for para além da duração da proteção da purga.</li></ol><br>Caso contrário, se o cofre-chave sofreu uma migração de inquilinos, sim, pode ser recuperado usando um dos passos abaixo: <ol><li>Reverta o cofre de volta para o velho inquilino.</li><li>Desa `Identity = None` parte e, em seguida, reesuse o valor para `Identity = SystemAssigned` . Isto elimina e recria a identidade uma vez criada a nova identidade. Ativar `Get` `Wrap` , e `Unwrap` permissões para a nova identidade na política de acesso do cofre-chave.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Não foi possível ir buscar a chave de passagem, uma vez que a chave gerida pelo cliente não foi encontrada.| Sim, verifique se: <ol><li>O cofre ainda tem o MSI na política de acesso.</li><li>A identidade é do tipo sistema atribuído.</li><li>Ativar permissões de Obter, Embrulhar e Desembrulhar a identidade na política de acesso do cofre-chave.</li></ol>|
| SsemUserErrorUserAssignedLimitEdReached | A adição de uma nova Identidade Atribuída ao Utilizador falhou uma vez que atingiu o limite do número total de identidades atribuídas pelo utilizador que podem ser adicionadas. | Por favor, relemisse a operação com menos identidades de utilizador ou remova algumas identidades atribuídas pelo utilizador do recurso antes de voltar a tentar. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | A operação de acesso à identidade gerida falhou. <br> Nota: Isto é para o cenário em que a subscrição é transferida para um inquilino diferente. O cliente tem de mover manualmente a identidade para novo inquilino. Correio de PFA para mais detalhes. | Por favor, mova a identidade selecionada para o novo inquilino sob o qual a subscrição está presente. Para mais informações, consulte como [ativar a chave.](#enable-key) |
| SsemUserErrorKekUserIdentityNotFound | Aplicou uma chave gerida pelo cliente, mas a identidade atribuída pelo utilizador que tem acesso à chave não foi encontrada no diretório ativo. <br> Nota: Isto acontece quando a identidade do utilizador é eliminada do Azure.| Por favor, tente adicionar uma identidade diferente atribuída ao utilizador selecionado para o seu cofre chave para permitir o acesso à chave gerida pelo cliente. Para mais informações, consulte como [ativar a chave.](#enable-key) |
| SsemUserErrorUserAssignedIdentityAbsent | Não foi possível ir buscar a chave de passagem, uma vez que a chave gerida pelo cliente não foi encontrada. | Não consegui aceder à chave gerida pelo cliente. Ou a Identidade Atribuída ao Utilizador (UAI) associada à chave é eliminada ou o tipo de UAI mudou. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | A operação de acesso à identidade gerida falhou. <br> Nota: Isto é para o cenário em que a subscrição é transferida para um inquilino diferente. O cliente tem de mover manualmente a identidade para novo inquilino. Correio de PFA para mais detalhes. | Por favor, tente adicionar uma identidade diferente atribuída ao utilizador selecionado para o seu cofre chave para permitir o acesso à chave gerida pelo cliente. Para mais informações, consulte como [ativar a chave.](#enable-key)|
| SsemUserErrorKeyVaultBadRequestExcepção | Aplicou uma chave gerida pelo cliente, mas o acesso à chave não foi concedido ou foi revogado, ou não foi possível aceder ao cofre de chaves devido à ativação da firewall. | Adicione a identidade selecionada no cofre de chaves para permitir o acesso à chave gerida pelo cliente. Se o cofre-chave tiver a firewall ativada, mude para uma identidade atribuída ao sistema e adicione uma chave gerida pelo cliente. Para mais informações, consulte como [ativar a chave.](#enable-key) |
| Erro genérico  | Não consegui pegar a chave.| Isto é um erro genérico. Contacte o Microsoft Support para resolver o erro e determinar os próximos passos.|

## <a name="next-steps"></a>Passos seguintes

- [O que é o Azure Key Vault?](../key-vault/general/overview.md)
- [Início Rápido: Definir e obter um segredo do Azure Key Vault com o portal do Azure](../key-vault/secrets/quick-create-portal.md)
