---
title: Encriptação de dados de resolução de problemas - Base de dados Azure para PostgreSQL - Servidor Único
description: Saiba como resolver problemas na encriptação de dados na sua Base de Dados Azure para PostgreSQL - Servidor Único
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: c315e1df473f3d23bab7e2a78ce166f22272ee70
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242250"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Encriptação de dados de resolução de problemas na Base de Dados Azure para PostgreSQL - Servidor Único

Este artigo ajuda-o a identificar e resolver problemas comuns que podem ocorrer na implementação de um único servidor da Base de Dados Azure para PostgreSQL quando configurado com encriptação de dados usando uma chave gerida pelo cliente.

## <a name="introduction"></a>Introdução

Quando configurar a encriptação de dados para utilizar uma chave gerida pelo cliente no Cofre de Chaves Azure, o servidor requer acesso contínuo à chave. Se o servidor perder o acesso à chave gerida pelo cliente no Cofre da Chave Azure, negará todas as ligações, devolverá a mensagem de erro apropriada e mudará o seu estado para * **Inacessível** _ no portal Azure.

Se já não precisar de uma Base de Dados Azure inacessível para o servidor PostgreSQL, pode eliminá-la para parar de incorrer em custos. Não são permitidas outras ações no servidor até que o acesso ao cofre da chave seja restaurado e o servidor esteja disponível. Também não é possível alterar a opção de encriptação de dados de `Yes` (gerido pelo cliente) para `No` (gerido pelo serviço) num servidor inacessível quando é encriptado com uma chave gerida pelo cliente. Terá de revalidar a chave manualmente antes de o servidor voltar a estar acessível. Esta ação é necessária para proteger os dados do acesso não autorizado enquanto as permissões à chave gerida pelo cliente são revogadas.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Erros comuns que tornam o servidor inacessível

As seguintes configurações erradas causam a maioria dos problemas com a encriptação de dados que usam as teclas Azure Key Vault:

- O cofre da chave está indisponível ou não existe:
  - O cofre foi acidentalmente apagado.
  - Um erro de rede intermitente faz com que o cofre da chave não esteja disponível.

- Não tem permissões para aceder ao cofre ou a chave não existe:
  - A chave expirou ou foi acidentalmente eliminada ou desativada.
- A identidade gerida da Base de Dados Azure para o caso PostgreSQL foi acidentalmente eliminada.
  - A identidade gerida da Base de Dados Azure para o caso PostgreSQL não tem permissões-chave suficientes. Por exemplo, as permissões não incluem Get, Wrap e Desembrulhar.
  - As permissões de identidade geridas para a Base de Dados Azure para a instância PostgreSQL foram revogadas ou eliminadas.

## <a name="identify-and-resolve-common-errors"></a>Identificar e resolver erros comuns

### <a name="errors-on-the-key-vault"></a>Erros no cofre da chave

#### <a name="disabled-key-vault"></a>Cofre de chaves desativado

- `AzureKeyVaultKeyDisabledMessage`
- _*Explicação**: A operação não pôde ser concluída no servidor porque a tecla Azure Key Vault está desativada.

#### <a name="missing-key-vault-permissions"></a>Permissões de cofre em falta

- `AzureKeyVaultMissingPermissionsMessage`
- **Explicação** : O servidor não tem as permissões necessárias para Obter, Wrap e Desembrulhar para o Cofre da Chave Azure. Conceda todas as permissões em falta ao diretor de serviço com identificação.

### <a name="mitigation"></a>Mitigação

- Confirme se a chave gerida pelo cliente está presente no cofre da chave.
- Identifique o cofre da chave e vá para o cofre chave no portal Azure.
- Certifique-se de que a chave URI identifica uma chave que está presente.

## <a name="next-steps"></a>Passos seguintes

[Utilize o portal Azure para configurar encriptação de dados com uma chave gerida pelo cliente na Base de Dados Azure para PostgreSQL](howto-data-encryption-portal.md)
