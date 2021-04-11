---
title: Tutorial para configurar Saviynt com Azure Ative Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial para configurar o Azure Ative Directory B2C com a Saviynt para integração de aplicações cruzadas para simplificar a modernização das TI e promover uma melhor segurança, governação e conformidade. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fe5a2114a99b7867ac5e51b6bf9ec542ad47ca13
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257896"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Tutorial para configurar Saviynt com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como integrar o Azure Ative Directory (AD) B2C com [a Saviynt.](https://saviynt.com/integrations/azure-ad/for-b2c/) A plataforma Saviynt's Security Manager fornece a visibilidade, segurança e governação que as empresas de hoje precisam, numa única plataforma unificada. A Saviynt incorpora risco de aplicação e governação, gestão de infraestruturas, gestão de conta privilegiada e análise de risco de cliente.

Neste tutorial de amostra, você vai configurar a Saviynt para fornecer uma administração delegada baseada em controlo de acesso fino para utilizadores do Azure AD B2C. A Saviynt faz as seguintes verificações para determinar se um utilizador está autorizado a gerir utilizadores Azure AD B2C.

- Segurança de nível de funcionalidade para determinar se um utilizador pode executar uma operação específica. Por exemplo, Criar utilizador, atualizar o utilizador, redefinir a palavra-passe do utilizador, etc.

- Segurança ao nível de campo para determinar se um utilizador pode ler/escrever um atributo específico de outro utilizador durante as operações de gestão do utilizador. Por exemplo, o agente de secretária de ajuda só pode atualizar o número de telefone e todos os outros atributos são apenas de leitura.

- Segurança de nível de dados para determinar se um utilizador pode executar uma determinada operação num utilizador específico. Por exemplo, o administrador de mesa de ajuda para a região do Reino Unido só pode gerir os utilizadores do Reino Unido.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C.](./tutorial-create-tenant.md) O inquilino está ligado à sua assinatura Azure.

- Uma [assinatura](https://saviynt.com/contact-us/) Saviynt

## <a name="scenario-description"></a>Descrição do cenário

A integração Saviynt inclui os seguintes componentes:

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) – A identidade entre empresas e clientes como um serviço que permite o controlo personalizado de como os seus clientes se inscrevem, se inscrevem e gerem os seus perfis.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) – A plataforma de governação de identidade que fornece uma administração delegada fina para gestão do ciclo de vida do utilizador e governação de acesso dos utilizadores do Azure AD B2C.  

- [Microsoft Graph API](/graph/use-the-api) – Esta API fornece as interfaces para a Saviynt gerir os utilizadores AZURE AD B2C e o seu acesso em Azure AD B2C.

O seguinte diagrama de arquitetura mostra a implementação.

![Imagem mostrando diagrama de arquitetura saviynt](./media/partner-saviynt/saviynt-architecture-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | Um administrador delegado inicia uma operação de utilizador Azure AD B2C através da Saviynt.
| 2. | A Saviynt verifica com o seu motor de autorização se o administrador delegado pode fazer a operação específica.
| 3. | O motor de autorização da Saviynt envia uma resposta de sucesso/falha de autorização.
| 4. | Saviynt permite ao administrador delegado fazer a operação necessária.
| 5. | Saviynt invoca Microsoft Graph API juntamente com atributos do utilizador para gerir o utilizador em Azure AD B2C
| 6. | A Microsoft Graph API criará/atualizar/eliminar o utilizador em Azure AD B2C.
| 7. | A Azure AD B2C enviará uma resposta de sucesso/falha.
| 8. | A Microsoft Graph API devolverá então a resposta à Saviynt.

## <a name="onboard-with-saviynt"></a>A bordo com Saviynt

1. Para criar uma conta Saviynt, contacte [a Saviynt](https://saviynt.com/contact-us/)

2. Crie políticas de administração delegadas e atribua os utilizadores como administradores delegados com várias funções.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Configure Azure AD B2C com Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>Criar uma aplicação AD AZure para Saviynt

1. Inicie sessão no [portal do Azure](https://portal.azure.com/#home).

2. Selecione o ícone **de Inscrição + Diretório** na barra de ferramentas do portal e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.

3. No portal Azure, procure e selecione **Azure AD B2C**.

4. Selecione **registos de Aplicações**  >  **Novo registo**.

5. Insira um Nome para a inscrição. Por exemplo, Saviynt e **selecione Criar**.

6. Vá a **permissões API** e selecione **+ Adicione uma permissão.**

7. Aparece a página de permissões da API request. Selecione o separador **APIs da Microsoft** e selecione **o Microsoft Graph** como as APIs da Microsoft comumente utilizadas.

8. Vá para a página seguinte e selecione **permissões de aplicação**.

9. Selecione **Diretório** e selecione **Diretório.Read.All** and **Directory.ReadWrite.All** checkboxes.

10. Selecione **Permissões de Adicionar**. Reveja as permissões adicionadas.

11. Selecione **o consentimento de administração grant para o Diretório padrão**  >  **Save**.

12. Vá a **Certificados e Segredos** e selecione **+ Adicionar Segredo de Cliente.** Introduza a descrição secreta do cliente, selecione a opção de expiração e selecione **Adicionar**.

13. A chave Secreta é gerada e exibida na secção secreta do Cliente.

    >[!NOTE]
    > Vai precisar do segredo do cliente mais tarde.

14. Vá ao **Overview** e obtenha a **identificação** do cliente e **do inquilino.**

15. Identificação do inquilino, identificação de cliente, e segredo de cliente serão necessários para completar a instalação em Saviynt.

### <a name="enable-saviynt-to-delete-users"></a>Permitir que saviynt elimine os utilizadores

Os passos abaixo explicam como permitir que a Saviynt execute operações de eliminação do utilizador em Azure AD B2C.

>[!NOTE]
>[Avalie o risco antes de conceder acesso a um diretor de serviço.](../active-directory/develop/app-objects-and-service-principals.md)

1. Instale a versão mais recente do Módulo PowerShell MSOnline numa estação de trabalho/servidor do Windows.

2. Ligue-se ao módulo AzureAD PowerShell e execute os seguintes comandos:

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>Testar a solução

Navegue pelo seu inquilino de aplicação Saviynt e teste a gestão do ciclo de vida do utilizador e o caso de utilização da governação de acesso.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [Criar uma aplicação web API](./add-web-api-application.md)