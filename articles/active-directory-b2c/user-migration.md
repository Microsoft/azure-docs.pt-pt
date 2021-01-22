---
title: Abordagens de migração de utilizadores
titleSuffix: Azure AD B2C
description: Migrar as contas dos utilizadores de outro fornecedor de identidade para Azure AD B2C utilizando os métodos de migração pré-migração ou sem emenda.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 34bc50f5f95725b59c0d2b30b529e12abb6aa7fa
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661158"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrar utilizadores para Azure AD B2C

A migração de outro fornecedor de identidade para o Azure Ative Directory B2C (Azure AD B2C) também pode exigir a migração das contas de utilizadores existentes. São discutidos dois métodos de migração, *pré-migração* e *migração sem emenda.* Com qualquer uma das abordagens, é obrigado a escrever uma aplicação ou script que utilize a [API do Microsoft Graph](microsoft-graph-operations.md) para criar contas de utilizador em Azure AD B2C.

## <a name="pre-migration"></a>Pré-migração

No fluxo de pré-migração, a sua aplicação de migração realiza estes passos para cada conta de utilizador:

1. Leia a conta de utilizador do antigo fornecedor de identidade, incluindo as suas credenciais atuais (nome de utilizador e senha).
1. Crie uma conta correspondente no seu diretório Azure AD B2C com as credenciais atuais.

Utilize o fluxo de pré-migração em qualquer uma destas duas situações:

- Tem acesso às credenciais de texto simples de um utilizador (o seu nome de utilizador e palavra-passe).
- As credenciais estão encriptadas, mas pode desencriptar.

Para obter informações sobre a criação programática de contas de utilizador, consulte [contas de utilizador do Manage AD B2C com o Microsoft Graph](microsoft-graph-operations.md).

## <a name="seamless-migration"></a>Migração sem emenda

Utilize o fluxo de migração sem emenda se as palavras-passe de texto simples no antigo fornecedor de identidade não estiverem acessíveis. Por exemplo, quando:

- A palavra-passe é armazenada num formato criptografado de sentido único, como por exemplo com uma função de haxixe.
- A palavra-passe é armazenada pelo fornecedor de identidade legado de uma forma que não pode aceder. Por exemplo, quando o fornecedor de identidade valida as credenciais chamando um serviço web.

O fluxo de migração sem emenda ainda requer pré-migração das contas dos utilizadores, mas depois usa uma [política personalizada](custom-policy-get-started.md) para consultar uma [API REST](custom-policy-rest-api-intro.md) (que cria) para definir a palavra-passe de cada utilizador no início do sessão.

O fluxo migratório sem emenda tem, assim, duas fases: *pré-migração* e *credenciais definidas.*

### <a name="phase-1-pre-migration"></a>Fase 1: Pré-migração

1. A sua aplicação de migração lê as contas de utilizador do antigo fornecedor de identidade.
1. A aplicação de migração cria contas de utilizador correspondentes no seu diretório Azure AD B2C, mas *não define palavras-passe*.

### <a name="phase-2-set-credentials"></a>Fase 2: Definir credenciais

Após a pré-migração das contas estar concluída, a sua política personalizada e a API REST executam o seguinte quando um utilizador assina:

1. Leia a conta de utilizador Azure AD B2C correspondente ao endereço de e-mail introduzido.
1. Verifique se a conta está sinalizada para migração avaliando um atributo de extensão booleana.
    - Se o atributo de extensão `True` voltar, ligue para a API REST para validar a palavra-passe contra o fornecedor de identidade legado.
      - Se a API REST determinar que a palavra-passe está incorreta, devolva um erro amigável ao utilizador.
      - Se a API REST determinar que a palavra-passe está correta, escreva a palavra-passe para a conta Azure AD B2C e altere o atributo de extensão booleana para `False` .
    - Se o atributo de extensão booleano `False` voltar, continue o processo de inscrição normal.

Para ver uma política personalizada de exemplo e REST API, consulte a [amostra de migração de utilizadores sem emenda](https://aka.ms/b2c-account-seamless-migration) no GitHub.

![Diagrama de fluxograma da abordagem de migração perfeita para a migração dos utilizadores](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagrama: Fluxo migratório sem emenda*

## <a name="best-practices"></a>Melhores práticas

### <a name="security"></a>Segurança

A abordagem de migração sem emenda utiliza a sua própria API REST personalizada para validar as credenciais de um utilizador contra o fornecedor de identidade legado.

**Tens de proteger a tua API de REPOUSO contra ataques de força bruta.** Um intruso pode submeter várias palavras-passe na esperança de eventualmente adivinhar as credenciais de um utilizador. Para ajudar a derrotar tais ataques, pare de servir pedidos à sua API REST quando o número de tentativas de inscrição passar por um determinado limiar. Além disso, proteja a comunicação entre a Azure AD B2C e a sua API REST. Para aprender a proteger as suas APIs RESTful para produção, consulte [API Restful Secure](secure-rest-api.md).

### <a name="user-attributes"></a>Atributos do utilizador

Nem todas as informações do fornecedor de identidade legado devem ser migradas para o seu diretório Azure AD B2C. Identifique o conjunto adequado de atributos do utilizador para armazenar em Azure AD B2C antes de migrar.

- **Loja DO** em Azure AD B2C
  - Nome de utilizador, palavra-passe, endereços de e-mail, números de telefone, números de membro/identificadores.
  - Marcadores de consentimento para a política de privacidade e contratos de licença de utilizador final.
- **NÃO** armazenar em Azure AD B2C
  - Dados sensíveis como números de cartões de crédito, números de segurança social (SSN), registos médicos ou outros dados regulados por entidades governamentais ou de conformidade do setor.
  - Preferências de marketing ou comunicação, comportamentos do utilizador e insights.

### <a name="directory-clean-up"></a>Limpeza de diretório

Antes de iniciar o processo de migração, aproveite para limpar o seu diretório.

- Identifique o conjunto de atributos do utilizador a armazenar em Azure AD B2C e migrar apenas o que precisar. Se necessário, pode criar [atributos personalizados](user-flow-custom-attributes.md) para armazenar mais dados sobre um utilizador.
- Se estiver a migrar de um ambiente com múltiplas fontes de autenticação (por exemplo, cada aplicação tem o seu próprio diretório de utilizadores), migra para uma conta unificada em Azure AD B2C.
- Se várias aplicações tiverem nomes de utilizador diferentes, pode armazenar todas elas numa conta de utilizador Azure AD B2C utilizando a recolha de identidades. No que diz respeito à palavra-passe, deixe o utilizador escolher um e desafiná-lo no diretório. Por exemplo, com a migração sem emenda, apenas a palavra-passe escolhida deve ser armazenada na conta Azure AD B2C.
- Remova as contas de utilizador não utilizadas antes da migração, ou não migrar contas velhas.

### <a name="password-policy"></a>Política de senha

Se as contas que está a migrar tiverem uma força de senha mais fraca do que a [força de senha forte](../active-directory/authentication/concept-sspr-policy.md) aplicada pelo Azure AD B2C, pode desativar o requisito de senha forte. Para mais informações, consulte [a propriedade da política password.](user-profile-attributes.md#password-policy-attribute)

## <a name="next-steps"></a>Passos seguintes

O repositório [azure-ad-b2c/migração do utilizador](https://github.com/azure-ad-b2c/user-migration) no GitHub contém um exemplo de política de migração sem emenda e uma amostra de código REST API:

[Política personalizada de migração de utilizadores sem emenda & amostra de código API REST](https://aka.ms/b2c-account-seamless-migration)
