---
title: Encriptação de dados para Base de Dados Azure para resolução de problemas mySQL
description: Saiba como resolver a encriptação de dados para a sua Base de Dados Azure para MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 4b517a463ec949d804798787ad4b35b53145a4a8
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371556"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-mysql"></a>Encriptação de dados de resolução de problemas com chaves geridas pelo cliente na Base de Dados Azure para MySQL
Este artigo descreve como identificar e resolver problemas/erros comuns que ocorrem numa Base de Dados Azure para O MySQL configurada com encriptação de dados utilizando a chave gerida pelo cliente.

## <a name="introduction"></a>Introdução
Quando a encriptação de dados é configurada para utilizar uma chave gerida pelo cliente no Cofre de Chaves Azure, é necessário um acesso contínuo a esta chave para que o servidor se mantenha disponível. Se o servidor perder o acesso à chave gerida pelo cliente no Cofre de Chaves Azure, o servidor começará a negar todas as ligações com a mensagem de erro apropriada e a mudar o seu estado para ***Inacessível*** no portal Azure.

Se uma base de dados Azure inacessível para o servidor MySQL já não for necessária, pode ser eliminada imediatamente para parar de incorrer em custos. Todas as outras ações no servidor não são permitidas até que o acesso ao cofre da chave Azure tenha sido restaurado e o servidor esteja de volta disponível. A alteração da opção de encriptação de dados de 'Sim' (gerida pelo cliente) para 'Não' (gerida pelo serviço) num servidor inacessível também não é possível enquanto um servidor é encriptado com gerido pelo cliente. Tem de revalidar a chave manualmente para voltar a estar disponível o servidor. Isto é necessário para proteger os dados de acesso não autorizado enquanto as permissões à chave gerida pelo cliente foram revogadas.

## <a name="common-errors-causing-server-to-become-inaccessible"></a>Erros comuns que causam o inacessível do servidor

A maioria dos problemas que ocorrem quando se utiliza a encriptação de dados com o Cofre chave Azure são causados por uma das seguintes configurações erradas.

O cofre chave está indisponível ou não existe

* O cofre da chave foi acidentalmente apagado.
* Um erro intermitente de rede faz com que o cofre da chave não esteja disponível.

Não há permissões para aceder ao cofre da chave ou a chave não existe

* A chave foi acidentalmente eliminada, desativada ou a chave expirou.
* A base de dados Azure para a identidade gerida por instância mySQL foi acidentalmente eliminada.
* As permissões concedidas à Base de Dados Azure para a identidade gerida pelo servidor MySQL para as teclas não são suficientes (não incluem Get, Wrap e Desembrulhar).
* Foram revogadas as permissões para a Base de Dados Azure para a identidade gerida por exemplo do servidor MySQL.

## <a name="identify-and-resolve-common-errors"></a>Identificar e resolver erros comuns
### <a name="errors-on-the-key-vault"></a>Erros no cofre da chave

#### <a name="disabled-key-vault"></a>Cofre de chave para deficientes
* Mensagem de deficientes azureKeyVaultKey
* **Explicação** : A operação não pôde ser concluída no servidor porque a chave do cofre de chaves Azure está desativada.

#### <a name="missing-key-vault-permissions"></a>Faltando permissões no cofre
* Mensagem de falta de permissões AzureKeyVault
* O servidor não tem as permissões necessárias de Get, Wrap e Desembrulhar para as permissões do Cofre de Chaves Azure. Conceda permissões em falta para o diretor de serviço com identificação.

### <a name="mitigation"></a>Mitigação
* Confirme que a chave gerida pelo cliente está presente no Cofre chave:
* Identifique o cofre da chave e vá ao cofre chave no portal Azure.
* Certifique-se de que a chave identificada pela chave URI está presente.


## <a name="next-steps"></a>Passos seguintes
[Configurar a encriptação de dados com uma chave gerida pelo cliente para a sua base de dados Azure para o MySQL utilizando o portal Azure](howto-data-encryption-portal.md).