---
title: Como garantir a validação da Base de Dados Azure para PostgreSQL - Encriptação de Dados
description: Saiba como validar a encriptação da Base de Dados Azure para PostgreSQL - Encriptação de dados utilizando a chave gerida pelos clientes.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: be725c574c54dfc298a900d3c043559d484d1bc7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117853"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Validação da encriptação de dados para a base de dados Azure para PostgreSQL

Este artigo ajuda-o a validar essa encriptação de dados utilizando a chave gerida pelo cliente para a Base de Dados Azure para PostgreSQL está a funcionar como esperado.

## <a name="check-the-encryption-status"></a>Verifique o estado de encriptação

### <a name="from-portal"></a>Do portal

1. Se quiser verificar se a chave do cliente é utilizada para encriptação, siga estes passos:

    * No portal Azure, navegue até as chaves do **cofre da chave Azure**  ->  **Keys**
    * Selecione a chave utilizada para encriptação do servidor.
    * Desa estarita o estado da tecla **Ativada** para **o Nº**.
  
       Após algum tempo **(~15 min),** a Base de Dados Azure para o **Estado do** servidor PostgreSQL deve ser **inacessível**. Qualquer operação de E/S feita contra o servidor falhará, o que valida que o servidor está de facto encriptado com a chave dos clientes e que a chave não é atualmente válida.
    
        Para tornar o servidor **disponível,** pode revalidar a chave. 
    
    * Desa estale o estado da chave no Cofre da Chave para **Sim**.
    * Na encriptação de **dados**do servidor, selecione **a tecla Revalidate**.
    * Após a revalidação da chave ser bem sucedida, o **estado** do servidor muda para **Disponível**

2. No portal Azure, se conseguir garantir que a chave de encriptação está definida, os dados são encriptados utilizando a chave dos clientes utilizada no portal Azure.

  ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/byok-validate.png)

### <a name="from-cli"></a>Do CLI

1. Podemos usar o comando *AZ CLI* para validar os recursos-chave que estão a ser utilizados para a Base de Dados Azure para o servidor PostgreSQL.

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Para um servidor sem conjunto de encriptação de dados, este comando resultará em conjunto vazio [].

### <a name="azure-audit-reports"></a>Relatórios de auditoria do Azure

[Os Relatórios de Auditoria](https://servicetrust.microsoft.com) também podem ser revistos que fornecem informações sobre o cumprimento das normas de proteção de dados e requisitos regulamentares.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre encriptação de dados, consulte [a Base de Dados Azure para encriptação de dados de servidor único postgresQL com a chave gerida pelo cliente](concepts-data-encryption-postgresql.md).