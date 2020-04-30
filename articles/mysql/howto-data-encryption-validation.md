---
title: Como garantir a validação da Base de Dados Azure para MySQL - Encriptação de dados
description: Saiba como validar a encriptação da Base de Dados Azure para MySQL - Encriptação de dados utilizando a chave gerida pelos clientes.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: ee80f50e925bf4545f885d701e70bc21208f1d1f
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515407"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>Validação da encriptação de dados para base de dados Azure para MySQL

Este artigo ajuda-o a validar que a encriptação de dados utilizando a chave gerida pelo cliente para a Base de Dados Azure para o MySQL está a funcionar como esperado.

## <a name="check-the-encryption-status"></a>Verifique o estado da encriptação

### <a name="from-portal"></a>Do portal

1. Se quiser verificar se a chave do cliente é utilizada para encriptação, siga estes passos:

    * No portal Azure, navegue para as -> **Chaves** do **Cofre de Chaves Azure**
    * Selecione a tecla utilizada para encriptação do servidor.
    * Desdefinir o estado da tecla **Ativada** para **O**.
  
       Após algum tempo **(~15 min),** a Base de Dados Azure para o **Estado** do servidor MySQL deve estar **inacessível**. Qualquer operação de I/O feita contra o servidor falhará, o que valida que o servidor está de facto encriptado com a chave dos clientes e a chave não é atualmente válida.
    
       Para disponibilizar o **servidor** contra, pode revalidar a tecla. 
    
    * Desloque o estado da chave no Cofre chave para **Sim**.
    * Na **encriptação**de dados do servidor, selecione **a tecla Revalidação**.
    * Após a revalidação da chave ser bem sucedida, o **Estado** do servidor muda para **Disponível**.

2. No portal Azure, se conseguir garantir que a chave de encriptação está definida, os dados são encriptados utilizando a chave dos clientes utilizada no portal Azure.

  ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/byok-validate.png)

### <a name="from-cli"></a>Do CLI

1. Podemos usar o comando *Az CLI* para validar os recursos-chave utilizados para a Base de Dados Azure para o servidor MySQL.

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Para um servidor sem conjunto de encriptação Data, este comando resulta em conjunto vazio [].

### <a name="azure-audit-reports"></a>Relatórios de auditoria do Azure

[Os Relatórios](https://servicetrust.microsoft.com) de Auditoria também podem ser revistos que fornecem informações sobre o cumprimento das normas de proteção de dados e requisitos regulamentares.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre encriptação de dados, consulte a Base de [Dados Azure para encriptação de dados MySQL com a chave gerida pelo cliente](concepts-data-encryption-mysql.md).