---
title: Encriptação de dados - Portal Azure - Base de Dados Azure para MySQL
description: Saiba como configurar e gerir a encriptação de dados para a sua Base de Dados Azure para o MySQL utilizando o portal Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8dfc34699bb973dc1f5b74807043e9f208d64f4c
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242152"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Encriptação de dados para Azure Database para MySQL utilizando o portal Azure

Saiba como utilizar o portal Azure para configurar e gerir a encriptação de dados para a sua Base de Dados Azure para o MySQL.

## <a name="prerequisites-for-azure-cli"></a>Pré-requisitos para O Azure CLI

* Você deve ter uma subscrição Azure e ser um administrador nessa subscrição.
* No Azure Key Vault, crie um cofre chave e uma chave para usar para uma chave gerida pelo cliente.
* O cofre-chave deve ter as seguintes propriedades para usar como chave gerida pelo cliente:
  * [Excluir suave](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Purga protegida](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* A chave deve ter os seguintes atributos para usar como chave gerida pelo cliente:
  * Sem data de validade
  * Não incapacitado
  * Capaz de realizar obter, embrulhar a chave, desembrulhar as operações-chave

## <a name="set-the-right-permissions-for-key-operations"></a>Definir as permissões certas para operações chave

1. No Cofre de Chaves, selecione **Políticas de acesso** Adicionar Política de  >  **Acesso** .

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

2. Selecione **permissões chave** , e selecione **Get** , **Wrap,** **Desembrulhar,** e **o Principal** , que é o nome do servidor MySQL. Se o seu servidor principal não puder ser encontrado na lista de principais existentes, tem de o registar. É-lhe pedido que registe o seu principal servidor quando tenta configurar a encriptação de dados pela primeira vez, e falha.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

3. Selecione **Guardar** .

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Definir encriptação de dados para Azure Database para MySQL

1. Na Base de Dados Azure para o MySQL, selecione **encriptação de dados** para configurar a chave gerida pelo cliente.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

2. Pode selecionar um cofre de chaves e um par de chaves ou introduzir um identificador de chave.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

3. Selecione **Guardar** .

4. Para garantir que todos os ficheiros (incluindo ficheiros temporários) estão totalmente encriptados, reinicie o servidor.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Utilização da encriptação de dados para restaurar ou replicar servidores

Depois de a Azure Database for MySQL ser encriptada com a chave gerida de um cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor também é encriptada. Pode fazer esta nova cópia através de uma operação local ou de geo-restauro, ou através de uma operação de réplica (local/cross-region). Assim, para um servidor MySQL encriptado, pode usar os seguintes passos para criar um servidor restaurado encriptado.

1. No seu servidor, selecione **Overview**  >  **Restore** .

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

   Ou para um servidor ativado por replicação, na rubrica **Definições,** selecione **Replication** .

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysql-replica.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

2. Após a operação de restauro estar concluída, o novo servidor criado é encriptado com a chave do servidor primário. No entanto, as funcionalidades e opções no servidor estão desativadas e o servidor está inacessível. Isto impede qualquer manipulação de dados, porque a identidade do novo servidor ainda não foi dada permissão para aceder ao cofre de chaves.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

3. Para tornar o servidor acessível, revalidar a chave no servidor restaurado. Selecione **a chave** de  >  **revalidato de** encriptação de dados.

   > [!NOTE]
   > A primeira tentativa de revalidar falhará, porque o diretor de serviço do novo servidor precisa de ter acesso ao cofre de chaves. Para gerar o principal serviço, selecione **a tecla Revalidate,** que mostrará um erro mas gera o principal do serviço. A partir daí, consulte [estes passos](#set-the-right-permissions-for-key-operations) mais cedo neste artigo.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

   Terá de dar acesso ao cofre chave para o novo servidor.

4. Depois de registar o principal de serviço, volte a revalidar a chave e o servidor retoma a sua funcionalidade normal.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Screenshot do Key Vault, com políticas de acesso e Política de Acesso Em destaque":::

## <a name="next-steps"></a>Passos seguintes

 Para saber mais sobre encriptação de dados, consulte [a Base de Dados Azure para encriptação de dados do MySQL com a chave gerida pelo cliente](concepts-data-encryption-mysql.md).
