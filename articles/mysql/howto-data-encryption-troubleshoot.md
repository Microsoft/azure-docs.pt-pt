---
title: Encriptação de dados de resolução de problemas na Base de Dados Azure para MySQL
description: Saiba como resolver a encriptação de dados na Base de Dados Azure para o MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 516f0b2080fc894ec00f222c712ffdea4ee74356
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851106"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Encriptação de dados de resolução de problemas na Base de Dados Azure para MySQL

Este artigo descreve como identificar e resolver problemas comuns que podem ocorrer na Base de Dados Azure para o MySQL quando configurado com encriptação de dados usando uma chave gerida pelo cliente.

## <a name="introduction"></a>Introdução

Quando configura a encriptação de dados para utilizar uma chave gerida pelo cliente no Cofre de Chaves Azure, os servidores requerem acesso contínuo à chave. Se o servidor perder o acesso à chave gerida pelo cliente no Cofre de Chaves Azure, negará todas as ligações, devolverá a mensagem de erro apropriada e mudará o seu estado para ***Inacessível*** no portal Azure.

Se já não necessitar de uma base de dados Azure inacessível para o servidor MySQL, pode eliminá-la para parar de incorrer em custos. Nenhuma outra ação no servidor é permitida até que o acesso ao cofre da chave seja restaurado e o servidor esteja disponível. Também não é possível alterar a opção de encriptação de dados de `Yes`(gerida pelo cliente) para `No` (gerido pelo serviço) num servidor inacessível quando é encriptado com uma chave gerida pelo cliente. Terá de revalidar a chave manualmente antes de o servidor voltar a estar acessível. Esta ação é necessária para proteger os dados de acesso não autorizado enquanto as permissões à chave gerida pelo cliente são revogadas.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Erros comuns que fazem com que o servidor se torne inacessível

As seguintes configurações causam a maioria dos problemas com encriptação de dados que usam chaves Azure Key Vault:

- O cofre da chave está indisponível ou não existe:
  - O cofre da chave foi acidentalmente apagado.
  - Um erro intermitente de rede faz com que o cofre da chave não esteja disponível.

- Não tem permissão para aceder ao cofre da chave ou a chave não existe:
  - A chave expirou ou foi acidentalmente eliminada ou desativada.
  - A identidade gerida da Base de Dados Azure para a instância MySQL foi acidentalmente eliminada.
  - A identidade gerida da Base de Dados Azure para a instância MySQL tem permissões-chave insuficientes. Por exemplo, as permissões não incluem Get, Wrap e Desembrulhar.
  - As permissões de identidade geridas para a Base de Dados Azure para a instância MySQL foram revogadas ou eliminadas.

## <a name="identify-and-resolve-common-errors"></a>Identificar e resolver erros comuns

### <a name="errors-on-the-key-vault"></a>Erros no cofre da chave

#### <a name="disabled-key-vault"></a>Cofre de chave para deficientes

- `AzureKeyVaultKeyDisabledMessage`
- **Explicação**: A operação não pôde ser concluída no servidor porque a chave do cofre de chaves Azure está desativada.

#### <a name="missing-key-vault-permissions"></a>Faltando permissões no cofre

- `AzureKeyVaultMissingPermissionsMessage`
- **Explicação**: O servidor não tem as permissões de Get, Wrap e Desembrulhar necessárias para o Cofre de Chaves Azure. Conceda permissões em falta para o diretor de serviço com identificação.

### <a name="mitigation"></a>Mitigação

- Confirme que a chave gerida pelo cliente está presente no cofre chave.
- Identifique o cofre da chave e vá ao cofre chave no portal Azure.
- Certifique-se de que a chave URI identifica uma chave que está presente.

## <a name="next-steps"></a>Passos seguintes

[Utilize o portal Azure para configurar encriptação de dados com uma chave gerida pelo cliente na Base de Dados Azure para MySQL](howto-data-encryption-portal.md)
