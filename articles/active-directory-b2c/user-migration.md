---
title: Abordagens de migração dos utilizadores
titleSuffix: Azure AD B2C
description: Migrar contas de utilizador de outro fornecedor de identidade para O AD B2C Azure utilizando os métodos de importação a granel ou de migração sem emenda.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 90ef9402e0891915be4ed6bb89573eced546c59a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183147"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrar utilizadores para Azure AD B2C

A migração de outro fornecedor de identidade para o Azure Ative Directory B2C (Azure AD B2C) também pode exigir a migração das contas de utilizador existentes. São aqui discutidos dois métodos de migração, *a importação* a granel e a *migração sem emenda.* Com qualquer uma das abordagens, é-lhe exigido que escreva uma aplicação ou script que utilize a [API](manage-user-accounts-graph-api.md) do Microsoft Graph para criar contas de utilizador em Azure AD B2C.

## <a name="bulk-import"></a>Importação a granel

No fluxo de importação a granel, o seu pedido de migração executa estes passos para cada conta de utilizador:

1. Leia a conta de utilizador do antigo fornecedor de identidade, incluindo as suas credenciais atuais (nome de utilizador e palavra-passe).
1. Crie uma conta correspondente no seu diretório Azure AD B2C com as credenciais atuais.

Utilize o fluxo de importação a granel em qualquer uma destas duas situações:

- Tem acesso às credenciais de texto simples de um utilizador (o seu nome de utilizador e palavra-passe).
- As credenciais estão encriptadas, mas pode desencriptar.

Para obter informações sobre a criação programática de contas de utilizador, consulte gerir as contas de [utilizador do AD B2C do Manage Azure com](manage-user-accounts-graph-api.md)o Microsoft Graph .

## <a name="seamless-migration"></a>Migração sem emenda

Utilize o fluxo de migração sem emenda se as palavras-passe de texto simples no antigo fornecedor de identidade não estiverem acessíveis. Por exemplo, quando:

- A palavra-passe é armazenada num formato encriptado de ida, como por exemplo com uma função hash.
- A palavra-passe é armazenada pelo fornecedor de identidade legado de uma forma a que não pode aceder. Por exemplo, quando o fornecedor de identidade valida credenciais ligando para um serviço web.

O fluxo de migração sem emenda ainda requer migração a granel das contas dos utilizadores, mas depois usa uma [política personalizada](restful-technical-profile.md) para consultar uma [API REST](rest-api-claims-exchange-dotnet.md) (que cria) para definir a palavra-passe de cada utilizador no primeiro início de sessão.

O fluxo de migração sem emenda tem, assim, duas fases: importação a *granel* e *credenciais definidas.*

### <a name="phase-1-bulk-import"></a>Fase 1: Importação a granel

1. A sua aplicação de migração lê as contas do utilizador do antigo fornecedor de identidade.
1. A aplicação de migração cria contas de utilizador correspondentes no seu diretório Azure AD B2C, mas *não define palavras-passe*.

### <a name="phase-2-set-credentials"></a>Fase 2: Definir credenciais

Após a migração a granel das contas estar completa, a sua política personalizada e a API REST então executam o seguinte quando um utilizador assina:

1. Leia a conta de utilizador Azure AD B2C correspondente ao endereço de e-mail introduzido.
1. Verifique se a conta está sinalizada para a migração avaliando um atributo de extensão booleana.
    - Se o atributo de extensão for `True`, ligue para a Sua API REST para validar a palavra-passe contra o fornecedor de identidade legado.
      - Se a API REST determinar que a palavra-passe está incorreta, devolva um erro amigável ao utilizador.
      - Se a API REST determinar que a palavra-passe está correta, escreva a palavra-passe na conta Azure AD B2C e altere o atributo de extensão booleana para `False`.
    - Se o atributo de extensão booleana voltar `False`, continue o processo de entrada normal.

Para ver uma política personalizada de exemplo e API REST, consulte a amostra de [migração de utilizadores sem emenda](https://aka.ms/b2c-account-seamless-migration) no GitHub.

![diagrama de Flowchart da abordagem de migração perfeita para a migração dos utilizadores](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagrama: Fluxo de migração sem emenda*

## <a name="best-practices"></a>Melhores práticas

### <a name="security"></a>Segurança

A abordagem de migração sem emenda usa a sua própria API REST personalizada para validar as credenciais de um utilizador contra o fornecedor de identidade legado.

**Tens de proteger a tua API do REST contra ataques de força bruta.** Um intruso pode enviar várias palavras-passe na esperança de eventualmente adivinhar as credenciais de um utilizador. Para ajudar a derrotar tais ataques, pare de servir pedidos à sua API REST quando o número de tentativas de inscrição passar um determinado limiar. Além disso, proteja a comunicação entre o Azure AD B2C e o seu REST API utilizando um certificado de [cliente](secure-rest-api-dotnet-certificate-auth.md).

### <a name="user-attributes"></a>Atributos do utilizador

Nem todas as informações do fornecedor de identidade do legado devem ser migradas para o seu diretório Azure AD B2C. Identifique o conjunto adequado de atributos do utilizador para armazenar em Azure AD B2C antes de migrar.

- **Loja DO** em Azure AD B2C
  - Nome de utilizador, palavra-passe, endereços de e-mail, números de telefone, números de membro/identificadores.
  - Marcadores de consentimento para a política de privacidade e acordos de licença de utilizador final.
- **NÃO** armazenar em Azure AD B2C
  - Dados sensíveis como números de cartões de crédito, números de segurança social (SSN), registos médicos ou outros dados regulados por organismos governamentais ou de conformidade da indústria.
  - Preferências de marketing ou comunicação, comportamentos do utilizador e insights.

### <a name="directory-clean-up"></a>Limpeza do diretório

Antes de iniciar o processo de migração, aproveite para limpar o seu diretório.

- Identifique o conjunto de atributos do utilizador a serem armazenados em Azure AD B2C e emigra apenas o que precisa. Se necessário, pode criar [atributos personalizados](custom-policy-custom-attributes.md) para armazenar mais dados sobre um utilizador.
- Se estiver a migrar de um ambiente com múltiplas fontes de autenticação (por exemplo, cada aplicação tem o seu próprio diretório de utilizador), migra para uma conta unificada em Azure AD B2C.
- Se várias aplicações tiverem nomes de utilizador diferentes, pode armazená-las todas numa conta de utilizador Azure AD B2C utilizando a recolha de identidades. No que diz respeito à palavra-passe, deixe o utilizador escolher um e desprole-o no diretório. Por exemplo, com a migração perfeita, apenas a palavra-passe escolhida deve ser armazenada na conta Azure AD B2C.
- Remova as contas de utilizador não utilizadas antes da migração ou não emigra contas velhas.

### <a name="password-policy"></a>Política de senha

Se as contas que está a migrar tiverem uma força de senha mais fraca do que a [forte força](../active-directory/authentication/concept-sspr-policy.md) de senha imposta pelo Azure AD B2C, pode desativar o requisito de senha forte. Para mais informações, consulte a [propriedade da política password](manage-user-accounts-graph-api.md#password-policy-property).

## <a name="next-steps"></a>Passos seguintes

O repositório [azure-ad-b2c/user-migração](https://github.com/azure-ad-b2c/user-migration) no GitHub contém um exemplo de política de migração sem emenda e amostra de código REST API:

[Política personalizada de migração de utilizadores sem emenda e amostra de código API REST](https://aka.ms/b2c-account-seamless-migration)
