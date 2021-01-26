---
title: Configurar o Identity Protection e o Acesso Condicional no Azure AD B2C
description: Saiba como configurar a Proteção de Identidade e o Acesso Condicional para você, inquilino Azure AD B2C, para ver o acesso de risco e outros eventos de risco e criar políticas baseadas em deteções de risco.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 654206bccd25bf09fcdc5c3e7ee72ba97c75af2a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785486"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Configurar o Identity Protection e o Acesso Condicional no Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

A Proteção de Identidade fornece uma deteção contínua de riscos para o seu inquilino Azure AD B2C. Se o seu nível de preços de inquilino Azure AD B2C for Premium P2, pode ver eventos de risco de proteção de identidade detalhados no portal Azure. Também pode utilizar políticas [de Acesso Condicional](../active-directory/conditional-access/overview.md) baseadas nestas deteções de riscos para determinar ações e impor políticas organizacionais.

## <a name="prerequisites"></a>Pré-requisitos

- O seu inquilino Azure AD B2C deve estar [ligado a uma subscrição da AD Azure.](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)
- O Azure AD B2C Premium P2 é obrigado a utilizar o acesso condicional baseado no risco do utilizador. Se necessário, [altere o seu nível de preços Azure AD B2C para Premium P2](./billing.md). 
- Para gerir a Proteção de Identidade e o Acesso Condicional no seu inquilino B2C, precisará de uma conta que seja atribuída à função de Administrador Global ou ao cargo de administrador de Segurança.
- Para utilizar estas funcionalidades no seu inquilino, primeiro tem de mudar para o nível de preços Azure AD B2C Premium P2.

## <a name="set-up-identity-protection"></a>Configurar proteção de identidade

A Proteção de Identidade está em padrão. Para poder ver os eventos de risco de Proteção de Identidade no seu inquilino Azure AD B2C, basta ligar o seu inquilino Azure AD B2C a uma subscrição AD AZure e selecionar o nível de preços Azure AD B2C Premium P2. Pode ver relatórios detalhados de eventos de risco no portal Azure.

### <a name="supported-identity-protection-risk-detections"></a>Deteções de riscos de proteção de identidade apoiadas

As seguintes deteções de risco são atualmente suportadas para a Azure AD B2C:  

|Tipo de deteção de risco  |Descrição  |
|---------|---------|
| Viagem atípica     | Inscreva-se a partir de uma localização atípica com base nas recentes insusagens do utilizador.        |
|Endereço IP anónimo     | Inscreva-se a partir de um endereço IP anónimo (por exemplo: navegador Tor, VPNs anonimizadores).        |
|Endereço IP ligado a malware     | Inscreva-se a partir de um endereço IP ligado a malware.         |
|Propriedades de inícios de sessão desconhecidos     | Inscreva-se com propriedades que não vimos recentemente para o utilizador dado.        |
|Admin confirmou utilizador comprometido    | Um administrador indicou que um utilizador foi comprometido.             |
|Spray de senha     | Inscreva-se através de um ataque de spray de senha.      |
|Inteligência de ameaça Azure AD     | Fontes internas e externas da Microsoft identificaram um padrão de ataque conhecido.        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Veja eventos de risco para o seu inquilino Azure AD B2C

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

1. No portal Azure, procure e selecione **Azure AD B2C**.

1. Em **Segurança**, selecione **utilizadores arriscados (Pré-visualização)**.

   ![Utilizadores de risco](media/conditional-access-identity-protection-setup/risky-users.png)

1. Em **Segurança**, selecione **deteções de risco (pré-visualização)**.

   ![Deteções de riscos](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>Adicionar uma política de acesso condicional 

Para adicionar uma política de acesso condicional baseada nas deteções de risco de Proteção de Identidade, certifique-se de que os padrãos de segurança são desativadas para o seu inquilino Azure AD B2C e, em seguida, criar políticas de Acesso Condicional.

### <a name="to-disable-security-defaults"></a>Para desativar os incumprimentos de segurança

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

3. No portal Azure, procure e selecione **O Diretório Ativo Azure**.

4. Selecione **Propriedades** e, em seguida, **selecione Gerir as predefinições de Segurança**.

   ![Desativar os incumprimentos de segurança](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. Em Ativar as predefinições de Segurança, selecione Nº. 

   ![Desafine o alternância de segurança de Ativação para Não](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

1. No portal Azure, procure e selecione **Azure AD B2C**.

1. Em **Segurança**, selecione **Acesso Condicional (Pré-visualização)**. Abre a página **Políticas de Acesso Condicional.** 

1. Selecione **Nova política** e siga a documentação de Acesso Condicionado AZure AD para criar uma nova política. Para políticas baseadas no risco, terá de configurar políticas separadas com base no risco de [utilizador](../active-directory/conditional-access/howto-conditional-access-policy-risk-user.md#enable-with-conditional-access-policy) ou [no risco de inscrição,](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy) dependendo do tipo de risco que pretende utilizar como condição. Não recomendamos a utilização de ambos os tipos de risco numa única política.

   > [!IMPORTANT]
   > Ao selecionar os utilizadores a quem pretende aplicar a política, não selecione **apenas todos os utilizadores,** ou pode bloquear-se a fazer a sua sessão.

## <a name="test-the-conditional-access-policy"></a>Testar a Política de Acesso Condicional

1. Criar uma política de acesso condicional, conforme referido acima, com as seguintes definições:
   
   - Para **Utilizadores e grupos,** selecione o utilizador de teste. Não selecione **Todos os utilizadores** ou irá impedir-se de iniciar sessão.
   - Para **aplicações ou ações cloud**, escolha **apps Selecione** e, em seguida, escolha a sua aplicação de partido em gestão.
   - Para as condições, selecione **o risco de inscrição** e os níveis de risco **elevados,** **médios** e **baixos.**
   - Para **Grant,** escolha **o acesso ao Bloco.**

      ![Escolha o acesso ao Bloco](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. Ativar a política de acesso condicional de teste selecionando **Criar**.

1. Simular um sinal de risco utilizando o [navegador Tor](https://www.torproject.org/download/). 

1. No jwt.ms ficha descodificada para a tentativa de inscrição, deve ver que o sinal foi bloqueado:

   ![Teste um sinal bloqueado](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Revisão dos resultados do acesso condicional no Relatório de Auditoria

Para rever o resultado de um evento de Acesso Condicional:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

3. No portal Azure, procure e selecione **Azure AD B2C**.

4. No âmbito **de Atividades**, selecione **registos de auditoria.**

5. Filtrar o registo de auditoria definindo **a categoria** para **B2C** e definindo o **Tipo de Recurso de Atividade** para a **Proteção de Identidade**. Em seguida, selecione **Aplicar**.

6. Reveja a atividade de auditoria até aos últimos 7 dias. Os seguintes tipos de atividade estão incluídos:

   - **Avaliar políticas de acesso condicional**: Esta entrada de registo de auditoria indica que foi realizada uma avaliação de Acesso Condicional durante uma autenticação.
   - **Utilizador remediado**: Esta entrada indica que a concessão ou requisitos de uma política de acesso condicional foram cumpridos pelo utilizador final, tendo esta atividade sido comunicada ao motor de risco para reduzir o risco de (mitigar) o utilizador.

7. Selecione uma entrada **de registo de política de acesso condicional** na lista para abrir a página De **Dados de Atividade: Audit log,** que mostra os identificadores de registo de auditoria, juntamente com esta informação na secção **Detalhes Adicionais:**

   - CondicionalAccessResult: A subvenção exigida pela avaliação da política condicional.
   - Políticas Aplicadas: Uma lista de todas as políticas de Acesso Condicional onde as condições foram satisfeitas e as políticas estão ON.
   - ReportingPolicies: Uma lista das políticas de acesso condicional que foram definidas para o modo apenas reporte e onde as condições foram satisfeitas.

## <a name="next-steps"></a>Próximos passos

[Adicionar Acesso Condicional a um fluxo de utilizador.](conditional-access-user-flow.md)
