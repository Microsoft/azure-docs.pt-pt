---
title: Criptografia de dados para um servidor único do banco de dado do Azure para PostgreSQL usando o portal
description: Saiba como configurar e gerenciar a criptografia de dados para o servidor único para PostgreSQL para o banco de dado do Azure usando o portal do Azure
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 51c99ca0788900397c922e31e44f121a7ae9caa6
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904266"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Criptografia de dados para um servidor único do banco de dado do Azure para PostgreSQL usando o portal

Neste artigo, você aprenderá a configurar e gerenciar o para usar o portal do Azure para configurar a criptografia de dados para o banco de dado do Azure para PostgreSQL servidor único.

## <a name="prerequisites-for-powershell"></a>Pré-requisitos para o PowerShell

* Você deve ter uma assinatura do Azure e ser um administrador nessa assinatura.
* Você deve ter Azure PowerShell instalado e em execução.
* Crie um Azure Key Vault e uma chave para usar na chave gerenciada pelo cliente.
* O cofre de chaves deve ter a seguinte propriedade para usar como uma chave gerenciada pelo cliente
    * [Eliminação de Forma Recuperável](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [Limpar protegido](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```

* A chave deve ter os seguintes atributos a serem usados para a chave gerenciada pelo cliente.
    * Nenhuma data de validade
    * Não desabilitado
    * Capaz de executar operações Get, wrap Key, não encapsular chave

## <a name="setting-the-right-permissions-for-key-operations"></a>Definindo as permissões corretas para operações de chave

1. Na Azure Key Vault, selecione as **políticas de acesso** e **Adicionar política de acesso** 

   ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Sob as **permissões de chave** , selecione **obter**, **encapsular**, **desencapsular** e a **entidade de segurança** que é o nome do servidor PostgreSQL.

   ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Salve** as configurações.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Configurando a criptografia de dados para o servidor único para PostgreSQL

1. No **banco de dados do Azure para PostgreSQL**, selecione a **criptografia de dado** para definir a configuração de chave gerenciada pelo cliente.

   ![Definindo a criptografia de dados](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Você pode selecionar um **Key Vault** e um par de **chaves** ou passar um **identificador de chave**.

   ![Key Vault de configuração](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Salve** as configurações.

4. Para garantir que todos os arquivos (incluindo arquivos temporários) estejam totalmente criptografados, é necessária uma reinicialização do servidor.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Restaurando ou criando a réplica do servidor que tem a criptografia de dados habilitada

Quando um servidor único do banco de dados do Azure para PostgreSQL é criptografado com a chave gerenciada do cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor, por meio da operação de restauração local ou geográfica ou de uma operação de réplica (local/entre regiões). Portanto, para um servidor PostgreSQL criptografado, você pode seguir as etapas abaixo para criar um servidor de restauração criptografada.

1. No servidor, selecione **visão geral**e, em seguida, selecione **restaurar**.

   ![Iniciar-restaurar](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Ou para um servidor habilitado para replicação, no título **configurações** , selecione **replicação**

   ![Iniciar-réplica](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Quando a operação de restauração for concluída, o novo servidor criado será criptografado com a chave do servidor primário. No entanto, os recursos e as opções no servidor estão desabilitados e o servidor é marcado em um estado **inacessível** . Isso é para evitar qualquer manipulação de dados, já que a identidade do novo servidor ainda não recebeu permissão para acessar o Key Vault.

   ![Marcar servidor inacessível](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)


3. Para corrigir o estado inacessível, você precisa revalidar a chave no servidor restaurado.

   ![servidor de revalidação](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Você precisará conceder acesso ao novo servidor para a Key Vault. 

4. Depois de revalidar a chave, o servidor retoma sua funcionalidade normal.

   ![Servidor normal restaurado](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="next-steps"></a>Passos seguintes

 Para saber mais sobre a criptografia de dados, consulte [o que é a criptografia de dados do Azure](concepts-data-encryption-postgresql.md).