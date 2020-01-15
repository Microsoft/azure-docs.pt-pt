---
title: Criptografia de dados do Azure para MySQL usando o portal
description: Saiba como configurar e gerenciar a criptografia de dados para o banco de dado do Azure para MySQL usando o portal do Azure.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 6e07b82104e5c7ff2d817a90e2be4aa336cf47ce
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942055"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Criptografia de dados para o servidor do Azure para MySQL usando portal do Azure

Neste artigo, você aprenderá a configurar e gerenciar o para usar o portal do Azure para configurar a criptografia de dados para o banco de dado do Azure para MySQL.

## <a name="prerequisites-for-cli"></a>Pré-requisitos para a CLI

* Você deve ter uma assinatura do Azure e ser um administrador nessa assinatura.
* Crie um Azure Key Vault e uma chave para usar na chave gerenciada pelo cliente.
* O Key Vault deve ter a seguinte propriedade para usar como uma chave gerenciada pelo cliente:
  * [Eliminação de Forma Recuperável](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Limpar protegido](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos a serem usados para a chave gerenciada pelo cliente.
  * Nenhuma data de validade
  * Não desabilitado
  * Capaz de executar operações _Get_, _Wrap Key_, não _encapsular chave_

## <a name="setting-the-right-permissions-for-key-operations"></a>Definindo as permissões corretas para operações de chave

1. Na Azure Key Vault, selecione as **políticas de acesso**e, em seguida, **Adicionar política de acesso**:

   ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selecione as **permissões de chave**e selecione **obter**, **encapsular**, **desencapsular** e a **entidade de segurança**, que é o nome do servidor MySQL. Se a entidade de segurança do servidor não puder ser encontrada na lista de entidades existentes, será necessário registrá-la ao tentar configurar a criptografia de dados pela primeira vez, o que falhará.

   ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **Salve** as configurações.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Configurando a criptografia de dados para o Azure Database para MySQL

1. No **banco de dados do Azure para MySQL**, selecione a **criptografia de dado** para definir a configuração de chave gerenciada pelo cliente.

   ![Definindo a criptografia de dados](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Você pode selecionar um **Key Vault** e um par de **chaves** ou passar um **identificador de chave**.

   ![Key Vault de configuração](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Salve** as configurações.

4. Para garantir que todos os arquivos (incluindo **arquivos temporários**) estejam totalmente criptografados, é **necessária**uma **reinicialização** do servidor.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Restaurando ou criando a réplica do servidor, que tem a criptografia de dados habilitada

Quando um banco de dados do Azure para MySQL é criptografado com a chave gerenciada do cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor, por meio da operação local ou de restauração geográfica ou de uma operação de réplica (local/entre regiões). Portanto, para um servidor MySQL criptografado, você pode seguir as etapas abaixo para criar um servidor de restauração criptografada.

1. No servidor, selecione **visão geral**e, em seguida, selecione **restaurar**.

   ![Iniciar-restaurar](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Ou para um servidor habilitado para replicação, no cabeçalho **configurações** , selecione **replicação**, conforme mostrado aqui:

   ![Iniciar-réplica](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Quando a operação de restauração for concluída, o novo servidor criado será criptografado com a chave usada para criptografar o servidor primário. No entanto, os recursos e as opções no servidor estão desabilitados e o servidor é marcado em um estado **inacessível** . Esse comportamento foi projetado para evitar qualquer manipulação de dados, já que a identidade do novo servidor ainda não recebeu permissão para acessar o Key Vault.

   ![Marcar servidor inacessível](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Para corrigir o estado inacessível, você precisa revalidar a chave no servidor restaurado. Selecione o painel **criptografia de dados** e, em seguida, o botão de **chave revalidar** .

   > [!NOTE]
   > A primeira tentativa de revalidação falhará, pois a entidade de serviço do novo servidor precisa receber acesso ao cofre de chaves. Para gerar a entidade de serviço, selecione **chave de revalidação**, que fornecerá erro, mas gerará a entidade de serviço. Depois disso, consulte as etapas [na seção 2](#setting-the-right-permissions-for-key-operations) acima.

   ![servidor de revalidação](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Você precisará conceder acesso ao novo servidor para a Key Vault.

4. Depois de registrar a entidade de serviço, você precisará revalidar a chave novamente e o servidor retomará sua funcionalidade normal.

   ![Servidor normal restaurado](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Passos seguintes

 Para saber mais sobre a criptografia de dados, consulte [o que é a criptografia de dados do Azure](concepts-data-encryption-mysql.md).
