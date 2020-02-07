---
title: Encriptação de dados para base de dados Azure para servidor Single PostgreSQL utilizando o portal Azure
description: Aprenda a configurar e gerir a encriptação de dados para a sua Base de Dados Azure para o servidor Single PostgreSQL utilizando o portal Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 4be80e9ded2fe4009c05a2b699342f848491994a
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046130"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Encriptação de dados para base de dados Azure para servidor Single PostgreSQL utilizando o portal Azure

Aprenda a usar o portal Azure para configurar e gerir a encriptação de dados para a sua Base de Dados Azure para o servidor Single PostgreSQL.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para o Azure CLI

* Você deve ter uma assinatura do Azure e ser um administrador nessa assinatura.
* No Cofre de Chaves Azure, crie um cofre chave e uma chave para usar para uma chave gerida pelo cliente.
* O cofre-chave deve ter as seguintes propriedades para usar como chave gerida pelo cliente:
  * [Eliminação suave](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Purga protegida](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos a utilizar como chave gerida pelo cliente:
  * Nenhuma data de validade
  * Não desabilitado
  * Capaz de realizar operações chave de get, wrap e desembrulhar

## <a name="set-the-right-permissions-for-key-operations"></a>Detete as permissões certas para operações-chave

1. No Cofre chave, selecione **as políticas** de acesso > adicionar a política de **acesso**.

   ![Screenshot do cofre chave, com políticas de acesso e política de acesso adicionado em destaque](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Selecione **permissões chave**, e selecione **Get,** **Wrap,** **Desembrulhar,** e o **Principal,** que é o nome do servidor PostgreSQL. Se o seu diretor de servidor não puder ser encontrado na lista de diretores existentes, tem de o registar. É-lhe pedido que registe o seu servidor principal quando tentar configurar a encriptação de dados pela primeira vez, e falha.  

   ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Selecione **Guardar**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Detete a encriptação de dados para a Base de Dados Azure para o servidor single PostgreSQL

1. Na Base de Dados Azure para PostgreSQL, selecione **encriptação de dados** para configurar a chave gerida pelo cliente.

   ![Screenshot da Base de Dados Azure para PostgreSQL, com encriptação de dados em destaque](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Pode selecionar um cofre chave e um par de chaves, ou introduzir um identificador chave.

   ![Screenshot da Base de Dados Azure para PostgreSQL, com opções de encriptação de dados destacadas](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Selecione **Guardar**.

4. Para garantir que todos os ficheiros (incluindo ficheiros temporários) estão totalmente encriptados, reinicie o servidor.

## <a name="restore-or-create-a-replica-of-the-server"></a>Restaurar ou criar uma réplica do servidor

Depois de a Base de Dados Azure para o servidor Single PostgreSQL ser encriptada com a chave gerida por um cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é encriptada. Pode fazer esta nova cópia através de uma operação local ou geo-restauro, ou através de uma operação de réplica (local/região transversal). Assim, para um servidor PostgreSQL encriptado, pode utilizar os seguintes passos para criar um servidor restaurado encriptado.

1. No seu servidor, selecione **Visão geral** > **Restaurar**.

   ![Screenshot da Base de Dados Azure para PostgreSQL, com visão geral e restauro em destaque](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Ou para um servidor ativado por replicação, sob a rubrica **Definições,** **selecione Replicação**.

   ![Screenshot da Base de Dados Azure para PostgreSQL, com Replicação em destaque](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Após a operação de restauro estar concluída, o novo servidor criado é encriptado com a chave do servidor principal. No entanto, as funcionalidades e opções no servidor estão desativadas e o servidor está inacessível. Isto impede qualquer manipulação de dados, porque a identidade do novo servidor ainda não foi dada permissão para aceder ao cofre chave.

   ![Screenshot da Base de Dados Azure para PostgreSQL, com estatuto inacessível em destaque](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Para tornar o servidor acessível, revalidar a chave no servidor restaurado. Selecione a **encriptação de dados** > **chave Revalidar**.

   > [!NOTE]
   > A primeira tentativa de revalidação falhará, porque o diretor de serviço do novo servidor precisa de ter acesso ao cofre chave. Para gerar o diretor de serviço, selecione a **tecla Revalidação,** que mostrará um erro mas gera o diretor de serviço. Posteriormente, consulte [estes passos](#set-the-right-permissions-for-key-operations) mais cedo neste artigo.

   ![Screenshot da Base de Dados Azure para PostgreSQL, com passo de revalidação destacado](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Terá de dar acesso ao cofre chave do novo servidor.

4. Depois de registar o diretor de serviço, revalidar novamente a chave e o servidor retoma a sua funcionalidade normal.

   ![Screenshot da Base de Dados Azure para PostgreSQL, mostrando funcionalidade restaurada](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Passos seguintes

 Para saber mais sobre encriptação de dados, consulte a Base de [Dados Azure para encriptação de dados do servidor single PostgreSQL com a chave gerida pelo cliente](concepts-data-encryption-postgresql.md).
