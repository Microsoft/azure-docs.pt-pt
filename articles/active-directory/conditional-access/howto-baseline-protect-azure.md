---
title: Política de linha de base exigir a MFA para a gestão de serviço (pré-visualização) - Azure Active Directory
description: Política de acesso condicional para exigir a MFA do Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b54a3645fe97903219841dd148c0942dfcda76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112388"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Política de linha de base: Exigir a MFA para gestão de serviço (pré-visualização)

Talvez esteja usando uma variedade de serviços do Azure na sua organização. Estes serviços podem ser geridos pela API do Azure Resource Manager:

* Portal do Azure
* Azure PowerShell
* CLI do Azure

Com o Azure Resource Manager para gerir os seus serviços é uma ação com privilégios elevados. O Azure Resource Manager pode alterar as configurações de ao nível do inquilino, como definições de serviço e de faturação de subscrição. A autenticação de fator único é vulnerável a ataques, como o phishing e a palavra-passe spray diversas. Portanto, é importante verificar a identidade de utilizadores que pretendem aceder do Azure Resource Manager e atualizar configurações, exigindo autenticação multifator antes de permitir o acesso.

**Exigir a MFA para gestão de serviço** é um [política de linha de base](concept-baseline-protection.md) que irá exigir a MFA para qualquer utilizador aceder ao portal do Azure, Azure PowerShell ou da CLI do Azure. Esta política aplica-se a todos os utilizadores acedam ao Azure Resource Manager, independentemente de se eles são um administrador.

Quando esta política estiver ativada num inquilino, todos os utilizadores iniciar sessão nos recursos de gestão do Azure irão ser submetidos à autenticação multifator. Se o utilizador não está registado para MFA, o utilizador será necessário registar-se com a aplicação Microsoft Authenticator para continuar.

![Exigir a MFA para o Azure Resource Manager](./media/howto-baseline-protect-azure/baseline-policy-require-mfa-for-service-management.png)

Para efetuar interativo início de sessão usando [Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), utilize o [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet.

```PowerShell
Connect-AzAccount
```

Quando é executado, este cmdlet apresenta uma cadeia de token. Para iniciar sessão, essa cadeia de caracteres de copiar e colá-lo no [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  num browser. A sua sessão do PowerShell será autenticada para se ligar ao Azure.

Para efetuar interativo início de sessão usando [CLI do Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), execute o [início de sessão az](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) comando.

```azurecli
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, terá de abrir uma página do browser e siga as instruções na linha de comandos para introduzir um código de autorização depois de navegar para [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) no seu browser. Em seguida, inicie sessão com as credenciais da conta no browser.

## <a name="deployment-considerations"></a>Considerações sobre implementação

Uma vez que o **exigir a MFA para a gestão de serviço** política aplica-se a todos os utilizadores do Azure Resource Manager, várias considerações precisam ser feitas para assegurar uma implantação tranqüila. Essas considerações incluem a identificar os utilizadores e os princípios de serviço no Azure AD que não é possível ou não deve realizar MFA, bem como aplicações e clientes utilizados pela sua organização que não suportam a autenticação moderna.

### <a name="user-exclusions"></a>Exclusões de utilizador

Esta política de linha de base fornece-lhe a opção para excluir usuários. Antes de ativar a política para o seu inquilino, recomendamos excluir as seguintes contas:

* **Acesso de emergência** ou **break glass** contas para evitar o bloqueio de conta ao nível do inquilino. No cenário improvável que todos os administradores são impedidos de aceder ao seu inquilino, sua conta de administrador de acesso de emergência pode ser utilizada para iniciar sessão para os passos de take de inquilino para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [gerir contas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas de serviço** e **princípios de serviço**, tais como a conta do Azure AD Connect Sync. Contas de serviço são contas não interativas que não estão associadas a qualquer utilizador específico. Eles são normalmente utilizados pelos serviços de back-end e permitem acesso programático aos aplicativos. Contas de serviço devem ser excluídas, uma vez que o MFA não pode ser concluída por meio de programação.
   * Se sua organização tiver estas contas em uso em scripts ou código, considere substituí-los com [geridos identidades](../managed-identities-azure-resources/overview.md). Como solução temporária, pode excluir essas contas específicas da política de linha de base.
* Utilizadores que não têm ou não será possível usar um Smartphone.
   * Esta política requer que os utilizadores para se registar para MFA com a aplicação Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Ativar a política de linha de base

A política **política de linha de base: Exigir a MFA para gestão de serviço (pré-visualização)** vem pré-configurada e será apresentada na parte superior ao navegar para o painel de acesso condicional no portal do Azure.

Para ativar esta política e proteger os seus administradores:

1. Inicie sessão para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Exigir a MFA para gestão de serviço (pré-visualização)** .
1. Definir **ativar política** ao **utilizar a política imediatamente**.
1. Adicionar exclusões utilizador ao clicar em **usuários** > **selecionar utilizadores excluídos** e escolher os utilizadores que têm de ser excluídos. Clique em **selecionar** , em seguida, **feito**.
1. Clique em **guardar**.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger a sua infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)