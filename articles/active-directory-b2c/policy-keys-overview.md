---
title: Visão geral das chaves da política - Azure Ative Directory B2C
description: Saiba mais sobre os tipos de chaves de política de encriptação que podem ser usadas no Azure Ative Directory B2C para assinar e validar fichas, segredos de clientes, certificados e senhas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ff3300935e90304754b5f2b375df0adecdaefa
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256247"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Visão geral das chaves de política no Azure Ative Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) armazena segredos e certificados sob a forma de chaves políticas para estabelecer confiança nos serviços com os quais se integra. Estes fundos consistem em:

- Fornecedores de identidade externos
- Conectar-se com [os serviços de API REST](restful-technical-profile.md)
- Assinatura e encriptação de token

 Este artigo discute o que precisa de saber sobre as chaves de política que são usadas pelo Azure AD B2C.

> [!NOTE]
> Atualmente, a configuração das chaves de política está limitada apenas às [políticas personalizadas.](./user-flow-overview.md)

Pode configurar segredos e certificados para estabelecer confiança entre serviços no portal Azure no menu **de chaves Da Política.** As chaves podem ser simétricas ou assimétricas. A criptografia *simétrica,* ou criptografia de chaves privadas, é onde um segredo partilhado é usado para encriptar e desencriptar os dados. A criptografia *assimétrica,* ou criptografia de chaves públicas, é um sistema criptográfico que utiliza pares de chaves, consistindo de chaves públicas que são partilhadas com a aplicação do partido e chaves privadas que são conhecidas apenas por Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Teclas e teclas de política

O recurso de nível superior para chaves de política em Azure AD B2C é o recipiente **Keyset.** Cada tecla contém pelo menos uma **tecla.** Uma chave tem os seguintes atributos:

| Atributo |  Necessário | Observações |
| --- | --- |--- |
| `use` | Yes | Utilização: Identifica a utilização pretendida da chave pública. Encriptar dados `enc` , ou verificar a assinatura nos dados `sig` .|
| `nbf`| No | Data e hora de ativação. |
| `exp`| No | Data de validade e hora. |

Recomendamos definir os valores de ativação e expiração da chave de acordo com os seus padrões PKI. Pode ser necessário rodar estes certificados periodicamente por razões de segurança ou política. Por exemplo, pode ter uma política para rodar todos os seus certificados todos os anos.

Para criar uma chave, pode escolher um dos seguintes métodos:

- **Manual** - Crie um segredo com uma corda que define. O segredo é uma chave simétrica. Pode definir as datas de ativação e de validade.
- **Gerado** - Gerar automaticamente uma chave. Pode definir datas de ativação e validade. Existem duas opções:
  - **Secret** - Gera uma chave simétrica.
  - **RSA** - Gera um par de chaves (teclas assimétricas).
- **Upload** - Faça upload de um certificado ou uma chave PKCS12. O certificado deve conter as chaves privadas e públicas (chaves assimétricas).

## <a name="key-rollover"></a>Capotamento de chaves

Por razões de segurança, o Azure AD B2C pode passar as chaves periodicamente ou imediatamente em caso de emergência. Qualquer aplicação, fornecedor de identidade ou API REST que se integre com a Azure AD B2C deve estar preparada para lidar com um evento de capotamento chave, não importa com que frequência possa ocorrer. Caso contrário, se a sua aplicação ou Azure AD B2C tentar utilizar uma chave caducada para efetuar uma operação criptográfica, o pedido de inscrição falhará.

Se um conjunto de teclas AD B2C Azure tiver várias teclas, apenas uma das teclas está ativa a qualquer momento, com base nos seguintes critérios:

- A ativação da chave baseia-se na **data de ativação**.
  - As teclas são classificadas por data de ativação por ordem ascendente. As teclas com datas de ativação mais para o futuro aparecem mais baixas na lista. As chaves sem data de ativação estão localizadas na parte inferior da lista.
  - Quando a data e hora atuais for maior do que a data de ativação de uma chave, o Azure AD B2C ativará a tecla e deixará de utilizar a tecla ativa anterior.
- Quando o tempo de validade da tecla atual tiver decorrido e o recipiente de teclas contiver uma nova chave com prazos válidos *e não antes* e prazos de *validade,* a nova tecla ficará ativa automaticamente.
- Quando o tempo de validade da tecla atual tiver decorrido e o recipiente-chave *não* conter uma nova chave com prazos válidos e *expirados,* o Azure AD B2C não poderá utilizar a chave caducada.  O Azure AD B2C levantará uma mensagem de erro dentro de um componente dependente da sua política personalizada. Para evitar este problema, pode criar uma chave predefinida sem datas de ativação e validade como uma rede de segurança.
- O ponto final da chave (JWKS URI) do ponto final de configuração bem conhecido do OpenId Connect reflete as teclas configuradas no Recipiente chave, quando a chave é referenciada no [Perfil Técnico JwtIssuer](./jwt-issuer-technical-profile.md). Uma aplicação que utilize uma biblioteca OIDC irá automaticamente buscar estes metadados para garantir que utiliza as chaves corretas para validar fichas. Para mais informações, saiba como utilizar a [Microsoft Authentication Library](../active-directory/develop/msal-b2c-overview.md), que sempre recolhe automaticamente as teclas de assinatura de token mais recentes.

## <a name="policy-key-management"></a>Gestão de chaves de política

Para obter a chave ativa atual dentro de um recipiente chave, utilize o ponto final da Microsoft Graph API [getActiveKey.](/graph/api/trustframeworkkeyset-getactivekey)

Para adicionar ou eliminar as chaves de assinatura e encriptação:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No portal Azure, procure e selecione **Azure AD B2C**.
1. Na página geral, no âmbito de **Políticas,** selecione **Identity Experience Framework**.
1. Selecione **teclas de política** 
    1. Para adicionar uma nova chave, **selecione Adicionar**.
    1. Para remover uma nova chave, selecione a chave e, em seguida, **selecione Delete**. Para eliminar a chave, digite o nome do recipiente da chave para eliminar. O Azure AD B2C apagará a chave e criará uma cópia da chave com o sufixo .bak.

### <a name="replace-a-key"></a>Substitua uma chave

As teclas num tecla não são substituíveis ou removíveis. Se precisar de alterar uma chave existente:

- Recomendamos a adição de uma nova chave com a **data de ativação** definida para a data e hora atuais. O Azure AD B2C ativará a nova chave e deixará de utilizar a tecla ativa anterior.
- Em alternativa, pode criar um novo conjunto de teclas com as teclas corretas. Atualize a sua política para utilizar o novo tecla e, em seguida, remova o conjunto de teclas antigo. 

## <a name="next-steps"></a>Passos seguintes

- Saiba como utilizar o Microsoft Graph para automatizar uma implementação [de teclas](microsoft-graph-operations.md#trust-framework-policy-keyset) e [teclas de política.](microsoft-graph-operations.md#trust-framework-policy-key)