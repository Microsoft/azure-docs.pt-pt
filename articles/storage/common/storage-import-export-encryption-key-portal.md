---
title: Utilize o portal Azure para configurar chaves geridas pelo cliente para o serviço de importação/exportação
description: Saiba como utilizar o portal Azure para configurar chaves geridas pelo cliente com o Azure Key Vault para o serviço de importação/exportação Azure. As chaves geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d3e4535c05ef077d14ef74310459a84af0f02fd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176333"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Utilize chaves geridas pelo cliente no Cofre chave Azure para serviço de importação/exportação

A Azure Import/Export protege as teclas BitLocker utilizadas para bloquear as unidades através de uma chave de encriptação. Por predefinição, as teclas BitLocker são encriptadas com chaves geridas pela Microsoft. Para um controlo adicional sobre as chaves de encriptação, também pode fornecer chaves geridas pelo cliente.

As chaves geridas pelo cliente devem ser criadas e armazenadas num Cofre de Chaves Azure. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](../../key-vault/general/overview.md)

Este artigo mostra como utilizar chaves geridas pelo cliente com o serviço de importação/exportação no [portal Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que:

1. Criou um trabalho de importação ou exportação de acordo com as instruções em:

    - [Criar um trabalho de importação para bolhas.](storage-import-export-data-to-blobs.md)
    - [Criar um trabalho de importação para ficheiros.](storage-import-export-data-to-files.md)
    - [Criar um trabalho de exportação para bolhas](storage-import-export-data-from-blobs.md)

2. Tem um Cofre de Chave Azure existente com uma chave que pode usar para proteger a sua chave BitLocker. Para aprender a criar um cofre chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault utilizando o portal Azure](../../key-vault/secrets/quick-create-portal.md).

    - **Elimine suavemente** e **não expurgar** estão definidos no cofre de chaves existente. Estas propriedades não são ativadas por defeito. Para ativar estas propriedades, consulte as secções intituladas **Enableing soft-delete** and **Enableing Purge Protection** num dos seguintes artigos:

        - [Como utilizar soft-delete com powerShell](../../key-vault/general/soft-delete-powershell.md).
        - Como utilizar o [soft-delete com o CLI](../../key-vault/general/soft-delete-cli.md).
    - O cofre-chave existente deve ter uma chave RSA de tamanho 2048 ou mais. Para mais informações sobre as chaves, consulte **as chaves do Cofre chave** em [Chaves, Segredos e Certificados do Cofre de Chaves do Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)
    - O cofre deve estar na mesma região que a conta de armazenamento dos seus dados.  
    - Se não tiver um Cofre chave Azure existente, também pode criá-lo em linha, como descrito na secção seguinte.

## <a name="enable-keys"></a>Ativar chaves

Configurar a chave gerida pelo cliente para o seu serviço de importação/exportação é opcional. Por predefinição, o serviço import/exportação utiliza uma chave gerida pela Microsoft para proteger a sua chave BitLocker. Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Vá à lâmina **de visão geral** para o seu trabalho de Importação.
2. No painel direito, selecione Escolha como as **suas teclas BitLocker estão encriptadas**.

    ![Escolha opção de encriptação](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. Na lâmina **de encriptação,** pode visualizar e copiar a tecla BitLocker do dispositivo. Sob o tipo de **Encriptação,** pode escolher como pretende proteger a sua chave BitLocker. Por padrão, é utilizada uma chave gerida pela Microsoft.

    ![Ver tecla BitLocker](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Tem a opção de especificar uma chave gerida pelo cliente. Depois de ter selecionado a chave gerida pelo cliente, **selecione o cofre da chave e uma chave**.

    ![Selecione chave gerida pelo cliente](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Na tecla Select da lâmina **Do Cofre de Chaves Azure,** a subscrição é automaticamente povoada. Para o **cofre key,** pode selecionar um cofre chave existente da lista de dropdown.

    ![Selecione ou crie o cofre de chaves Azure](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Também pode selecionar **Criar novo** para criar um novo cofre chave. Na lâmina do **cofre da chave Criar,** introduza o grupo de recursos e o nome do cofre chave. Aceite todos os outros incumprimentos. Selecione **Rever + Criar**.

    ![Criar novo Cofre chave Azure](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Reveja as informações associadas ao seu cofre chave e selecione **Criar**. Espere uns minutos para que a criação do cofre esteja completa.

    ![Criar cofre de chaves azure](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Na **tecla Select do Cofre de Chaves Azure,** pode selecionar uma chave no cofre de chaves existente.

9. Se criou um novo cofre de chaves, selecione **Criar novo** para criar uma chave. O tamanho da chave RSA pode ser 2048 ou superior.

    ![Criar nova chave no Cofre chave Azure](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Se a proteção de eliminação suave e purga não estiver ativada quando criar o cofre da chave, o cofre da chave será atualizado para ter uma proteção suave de eliminação e purga ativada.

10. Forneça o nome para a sua chave, aceite os outros predefinições e selecione **Criar**.

    ![Criar nova chave](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Selecione a **Versão** e, em seguida, escolha **Selecionar**. Foi notificado de que uma chave foi criada no seu cofre chave.

    ![Nova chave criada no cofre chave](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

Na lâmina **de encriptação,** pode ver o cofre da chave e a chave selecionada para a chave gerida pelo cliente.

## <a name="disable-keys"></a>Desativar as chaves

Só pode desativar as chaves geridas pela Microsoft e mudar-se para as chaves geridas pelo cliente em qualquer fase do trabalho de importação/exportação. No entanto, não é possível desativar a chave gerida pelo cliente depois de a ter criado.

## <a name="troubleshoot-customer-managed-key-errors"></a>Problemas geridos pelo cliente erros-chave

Se receber algum erro relacionado com a chave gerida pelo cliente, utilize a seguinte tabela para resolver problemas:

| Código de erro     |Detalhes     | Recuperável?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevogad | Aplicou uma chave gerida pelo cliente, mas o acesso chave é atualmente revogado. Para mais informações, consulte como [ativar o acesso à chave](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).                                                      | Sim, verifique se: <ol><li>O cofre ainda tem o MSI na política de acesso.</li><li>A política de acesso fornece permissões para obter, embrulhar, desembrulhar.</li><li>Se o cofre da chave estiver num vNet atrás da firewall, verifique se o **Allow Microsoft Trust Services** está ativado.</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | Aplicou uma chave gerida pelo cliente, mas a chave está desativada. Para mais informações, consulte como [ativar a tecla](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate).                                                                             | Sim, permitindo a versão chave     |
| CmkErrorKeyNotFound      | Aplicou uma chave gerida pelo cliente, mas não encontra o cofre associado à chave.<br>Se apagou o cofre da chave, não pode recuperar a chave gerida pelo cliente.  Se você emigrou o cofre chave para outro inquilino, veja [Change um cofre chave id inquilino após um movimento de assinatura](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix). |   Se apagar o cofre da chave:<ol><li>Sim, se estiver na duração da proteção da purga, utilizando os passos em [Recuperar um cofre chave](https://docs.microsoft.com/azure/key-vault/general/soft-delete-powershell#recovering-a-key-vault).</li><li>Não, se for além da duração da proteção da purga.</li></ol><br>Caso contrário, se o cofre principal foi submetido a uma migração de inquilinos, sim, pode ser recuperado usando um dos degraus abaixo: <ol><li>Reverta o cofre de volta para o velho inquilino.</li><li>Definir `Identity = None` e, em seguida, repor o valor para `Identity = SystemAssigned`. Isto elimina e recria a identidade uma vez criada a nova identidade. `Get`Ativar, `Wrap`e `Unwrap` permissões para a nova identidade na política de acesso do cofre chave.</li></ol>|

## <a name="next-steps"></a>Passos seguintes

- [O que é o Cofre chave Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
