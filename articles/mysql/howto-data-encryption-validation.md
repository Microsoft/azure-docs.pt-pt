---
title: Como garantir a validação da Base de Dados Azure para o MySQL - Encriptação de Dados
description: Saiba como validar a encriptação da Base de Dados Azure para o MySQL - Encriptação de dados utilizando a chave gerida pelos clientes.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: e7b747bd2babeeccb210bb30c3eb28f22d4befe7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905791"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Validação da encriptação de dados para a base de dados Azure para o MySQL

Este artigo ajuda-o a validar essa encriptação de dados utilizando a chave gerida pelo cliente para a Azure Database para o MySQL está a funcionar como esperado.

## <a name="check-the-encryption-status"></a>Verifique o estado de encriptação

### <a name="from-portal"></a>No portal

1. Se quiser verificar se a chave do cliente é utilizada para encriptação, siga estes passos:

    * No portal Azure, navegue até as chaves do **cofre da chave Azure**  ->  **Keys**
    * Selecione a chave utilizada para encriptação do servidor.
    * Desa estarita o estado da tecla **Ativada** para **o Nº**.
  
       Após algum tempo **(~15 min),** a Base de Dados Azure para o **estado do** servidor MySQL deve ser **inacessível**. Qualquer operação de E/S feita contra o servidor falhará, o que valida que o servidor está de facto encriptado com a chave dos clientes e que a chave não é atualmente válida.
    
       Para tornar o servidor **disponível,** pode revalidar a chave. 
    
    * Desa estale o estado da chave no Cofre da Chave para **Sim**.
    * Na encriptação de **dados**do servidor, selecione **a tecla Revalidate**.
    * Após a revalidação da chave ter sido bem sucedida, o **estado** do servidor muda para **Disponível**.

2. No portal Azure, se conseguir garantir que a chave de encriptação está definida, os dados são encriptados utilizando a chave dos clientes utilizada no portal Azure.

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="Visão geral da política de acesso":::

### <a name="from-cli"></a>Do CLI

1. Podemos usar o comando *AZ CLI* para validar os recursos-chave que estão a ser utilizados para a Base de Dados Azure para o servidor MySQL.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Para um servidor sem conjunto de encriptação de dados, este comando resulta em conjunto vazio [].

### <a name="azure-audit-reports"></a>Relatórios de auditoria do Azure

[Os Relatórios de Auditoria](https://servicetrust.microsoft.com) também podem ser revistos que fornecem informações sobre o cumprimento das normas de proteção de dados e requisitos regulamentares.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre encriptação de dados, consulte [a Base de Dados Azure para encriptação de dados do MySQL com a chave gerida pelo cliente](concepts-data-encryption-mysql.md).