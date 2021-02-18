---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: c5c98d7a067107673b4dafd1897f56804085a297
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654219"
---
## <a name="best-practices"></a>Melhores práticas

Esta secção fornece-lhe algumas diretrizes de boas práticas para definir ACLs de forma recursiva. 

#### <a name="handling-runtime-errors"></a>Manipulação de erros de tempo de execução

Um erro de tempo de execução pode ocorrer por muitas razões (Por exemplo: uma falha ou um problema de conectividade do cliente). Se encontrar um erro de tempo de execução, reinicie o processo ACL recursivo. Os ACLs podem ser reaplicados em itens sem causar um impacto negativo. 

#### <a name="handling-permission-errors-403"></a>Erros de permissão de manuseamento (403)

Se encontrar uma exceção ao controlo de acesso durante a execução de um processo ACL recursivo, o seu [diretor de segurança](/azure/role-based-access-control/overview#security-principal) AD pode não ter autorização suficiente para aplicar um ACL a um ou mais itens infantis na hierarquia do diretório. Quando ocorre um erro de permissão, o processo para e é fornecido um sinal de continuação. Corrija o problema da permissão e, em seguida, use o token de continuação para processar o conjunto de dados restante. Os diretórios e ficheiros que já foram processados com sucesso não terão de ser processados novamente. Também pode optar por reiniciar o processo ACL recursivo. Os ACLs podem ser reaplicados em itens sem causar um impacto negativo. 

#### <a name="credentials"></a>Credenciais 

Recomendamos que você forja um principal de segurança Azure AD que foi atribuído a função [de Proprietário de Dados de Armazenamento blob](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) no âmbito da conta de armazenamento alvo ou do recipiente. 

#### <a name="performance"></a>Desempenho 

Para reduzir a latência, recomendamos que execute o processo ACL recursivo numa Máquina Virtual Azure (VM) que está localizada na mesma região que a sua conta de armazenamento. 

#### <a name="acl-limits"></a>Limites da ACL

O número máximo de ACLs que pode aplicar a um diretório ou ficheiro é de 32 ACLs de acesso e 32 ACLs predefinidos. Para obter mais informações, veja [Controlo de acesso no Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-access-control).