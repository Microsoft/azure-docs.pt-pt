---
title: Utilize o portal Azure para configurar chaves geridas pelo cliente para o serviço de importação/exportação
description: Saiba como utilizar o portal Azure para configurar chaves geridas pelo cliente com o Cofre chave Azure para o serviço Azure Import/Export. As teclas geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 4362b579b7f01570a2b5fd072bf53ad495797cd8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783781"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Utilize chaves geridas pelo cliente no Cofre chave Azure para o serviço de importação/exportação

A Azure Import/Export protege as teclas BitLocker utilizadas para bloquear as unidades através de uma chave de encriptação. Por predefinição, as teclas BitLocker são encriptadas com as teclas geridas pela Microsoft. Para controlo adicional sobre as chaves de encriptação, também pode fornecer chaves geridas pelo cliente.

As chaves geridas pelo cliente devem ser criadas e armazenadas num Cofre de Chaves Azure. Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../../key-vault/general/overview.md)

Este artigo mostra como utilizar chaves geridas pelo cliente com o serviço de importação/exportação no [portal Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que:

1. Criou uma importação ou um trabalho de exportação de acordo com as instruções:

    - [Criar um trabalho de importação para bolhas.](storage-import-export-data-to-blobs.md)
    - [Criar um trabalho de importação para ficheiros.](storage-import-export-data-to-files.md)
    - [Criar um trabalho de exportação para bolhas](storage-import-export-data-from-blobs.md)

2. Tem um Cofre de Chaves Azure existente com uma chave que pode usar para proteger a sua chave BitLocker. Para aprender a criar um cofre-chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault using the Azure Portal](../../key-vault/secrets/quick-create-portal.md).

    - **Apagou suavemente** e **não expurgará** o cofre de teclas existente. Estas propriedades não são ativadas por padrão. Para ativar estas propriedades, consulte as secções intituladas **Permitir a eliminação suave** e a **proteção da purga ativada** num dos seguintes artigos:

        - [Como utilizar o soft-delete com PowerShell](../../key-vault/general/key-vault-recovery.md).
        - [Como utilizar o soft-delete com CLI](../../key-vault/general/key-vault-recovery.md).
    - O cofre-chave existente deve ter uma chave RSA de tamanho igual ou superior a 2048. Para obter mais informações sobre as teclas, consulte [sobre as teclas.](../../key-vault/keys/about-keys.md)
    - O cofre-chave deve estar na mesma região que a conta de armazenamento dos seus dados.  
    - Se não tiver um Cofre de Chave Azure existente, também pode criá-lo em linha como descrito na secção seguinte.

## <a name="enable-keys"></a>Ativar chaves

Configurar a chave gerida pelo cliente para o seu serviço de importação/exportação é opcional. Por predefinição, o serviço De importação/Exportação utiliza uma chave gerida pela Microsoft para proteger a sua chave BitLocker. Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Vá à lâmina **de visão geral** para o seu trabalho de Importação.
2. No painel direito, **selecione Escolha como as suas teclas BitLocker estão encriptadas** .

    ![Escolha a opção de encriptação](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Na lâmina **de encriptação,** pode visualizar e copiar a tecla BitLocker do dispositivo. No **tipo de encriptação,** pode escolher como pretende proteger a sua tecla BitLocker. Por padrão, é utilizada uma chave gerida pela Microsoft.

    ![Ver tecla BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Tem a opção de especificar uma chave gerida pelo cliente. Depois de ter selecionado a chave gerida pelo cliente, **selecione** o cofre de chaves e uma chave .

    ![Selecione chave gerida pelo cliente](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Na **tecla Select da lâmina Azure Key Vault,** a subscrição é automaticamente povoada. Para **o cofre key,** pode selecionar um cofre de chaves existente na lista de dropdown.

    ![Selecione ou crie o Cofre da Chave Azure](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Também pode selecionar **Criar novo** para criar um novo cofre de chaves. Na lâmina do **cofre da chave Create,** insira o grupo de recursos e o nome do cofre da chave. Aceite todos os outros incumprimentos. Selecione **Review + Criar** .

    ![Criar novo Cofre de Chaves Azure](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Reveja as informações associadas ao cofre de chaves e selecione **Criar** . Espere alguns minutos para a criação do cofre de chaves para completar.

    ![Criar cofre de chaves Azure](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Na **tecla Select a partir do Azure Key Vault,** pode selecionar uma chave no cofre de chaves existente.

9. Se criou um novo cofre de chaves, selecione **Criar novo** para criar uma chave. O tamanho da chave RSA pode ser 2048 ou maior.

    ![Criar nova chave no Cofre da Chave Azure](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Se a proteção para eliminar e purgar suavemente não estiver ativada quando criar o cofre de teclas, o cofre da chave será atualizado para ter uma proteção de eliminação e purga suave ativada.

10. Forneça o nome da sua chave, aceite as outras predefinições e selecione **Criar** .

    ![Criar nova chave](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Selecione a **versão** e, em seguida, escolha **Selecione** . Foi-lhe notificada que uma chave foi criada no seu cofre.

    ![Nova chave criada no cofre chave](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Na lâmina **de encriptação,** pode ver o cofre da chave e a chave selecionada para a chave gerida pelo seu cliente.

> [!IMPORTANT]
> Só é possível desativar as chaves geridas pela Microsoft e mudar-se para as chaves geridas pelo cliente em qualquer fase do trabalho de importação/exportação. No entanto, não é possível desativar a chave gerida pelo cliente uma vez que a tenha criado.

## <a name="troubleshoot-customer-managed-key-errors"></a>Resolução de problemas do cliente geriu erros-chave

Se receber quaisquer erros relacionados com a tecla gerida pelo seu cliente, utilize a seguinte tabela para resolver problemas:

| Código de erro     |Detalhes     | Recuperável?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | O acesso à chave gerida pelo cliente é revogado.                                                       | Sim, verifique se: <ol><li>O cofre ainda tem o MSI na política de acesso.</li><li>A política de acesso tem permissões get, Wrap e Desembrulhar ativadas.</li><li>Se o cofre de chaves estiver num VNet atrás da firewall, verifique se **o Microsoft Trust Services** está ativado.</li><li>Verifique se o MSI do recurso de trabalho foi reiniciado para `None` a utilização de APIs.<br>Se sim, então volte a definir o valor para `Identity = SystemAssigned` . Isto recria a identidade para o recurso de trabalho.<br>Uma vez criada a nova identidade, permita, `Get` `Wrap` e `Unwrap` permissões para a nova identidade na política de acesso do cofre-chave</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | A chave gerida pelo cliente está desativada.                                         | Sim, ao permitir a versão chave     |
| CmkErrorKeyNotFound      | Não é possível encontrar a chave gerida pelo cliente. | Sim, se a chave tiver sido eliminada, mas ainda estiver dentro da duração da purga, utilizando [a remoção da chave do cofre do Undo Key](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval).<br>Senão, <ol><li>Sim, se o cliente tiver a chave apoiada e a restaurar.</li><li>Não, caso contrário.</li></ol>
| CmkErrorVaultNotFound |Não é possível encontrar o cofre chave da chave gerida pelo cliente. |   Se o cofre da chave tiver sido apagado:<ol><li>Sim, se estiver na duração da proteção da purga, utilizando os passos da [Recuperar um cofre de chaves.](../../key-vault/general/soft-delete-overview.md#key-vault-recovery)</li><li>Não, se for para além da duração da proteção da purga.</li></ol><br>Senão, se o cofre chave foi migrado para outro inquilino, sim, pode ser recuperado usando um dos passos abaixo:<ol><li>Reverta o cofre de volta para o velho inquilino.</li><li>Desa `Identity = None` parte e, em seguida, reesuse o valor para `Identity = SystemAssigned` . Isto elimina e recria a identidade uma vez criada a nova identidade. Ativar `Get` `Wrap` , e `Unwrap` permissões para a nova identidade na política de acesso do cofre-chave.</li></ol>|

## <a name="next-steps"></a>Passos seguintes

- [O que é Azure Key Vault?](../../key-vault/general/overview.md)