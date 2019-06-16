---
title: Linha de base política bloco antigos de autenticação (pré-visualização) - Azure Active Directory
description: Política de acesso condicional para bloquear protocolos de autenticação
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
ms.openlocfilehash: e55bf4a66dcd8c1c7cfbc881d6f93c3a12f5dc00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112308"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Política de linha de base: Bloco antigos de autenticação (pré-visualização)

Conceder acesso fácil aos seus utilizadores para as suas aplicações na cloud, Azure Active Directory (Azure AD) oferece suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação. Autenticação é um termo que se refere a um pedido de autenticação feito por:

* Clientes com Office antigos que não utilizam autenticação moderna (por exemplo, cliente do Office 2010)
* Qualquer cliente que utiliza protocolos de email herdados, como o IMAP/SMTP/POP3

Hoje em dia, a maioria de todos os comprometer início de sessão tentativas provenientes da autenticação. Autenticação de legado não suporta a autenticação multifator (MFA). Mesmo que tenha uma política MFA ativada no seu diretório, um ator indevido pode autenticar com um protocolo de legado e ignorar o MFA.

É a melhor forma de proteger a sua conta de pedidos de autenticação malicioso efetuados por protocolos legados bloquear estas tentativas tudo junto. Para facilitar para impedir todos os pedidos de início de sessão feitos por protocolos legados, criamos uma política de linha de base que faz exatamente isso.

![Bloco autenticação com o acesso condicional](./media/howto-baseline-protect-legacy-auth/baseline-policy-block-legacy-authentication.png)

**Autenticação de legado do bloco** é [política de linha de base](concept-baseline-protection.md) que bloqueia todos os pedidos de autenticação feitos a partir de protocolos legados. Autenticação moderna deve ser utilizada para se inscrever com êxito todos os utilizadores. Utilizado em conjunto com as políticas de linha de base, todos os pedidos provenientes de protocolos legados serão bloqueados e todos os utilizadores serão obrigados a MFA sempre que for necessário. Esta política não bloqueia o Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identificar a utilização de autenticação

Antes de pode bloquear a autenticação no seu diretório, terá de compreender primeiro se os utilizadores têm aplicações que utilizam a autenticação e como ele afeta o seu diretório global. Registos do Azure AD início de sessão podem ser utilizados para compreender se estiver a utilizar autenticação.

1. Navegue para o **portal do Azure** > **do Azure Active Directory** > **inícios de sessão**.
1. Adicionar a coluna de aplicação de cliente se não for apresentado, clicando em **colunas** > **aplicações de cliente**.
1. Filtrar por **aplicação de cliente** > **outros clientes** e clique em **aplicar**.

Filtragem será apenas mostrar que iniciar sessão tenta que foram feitas pelos protocolos de autenticação. Clicar em cada tentativa de início de sessão individual, mostrarei detalhes adicionais. O **aplicação de cliente** no **informações básicas** separador indica qual protocolo de autenticação foi utilizado.

Estes registos irão indicar os utilizadores que ainda são dependendo antigos de autenticação e as aplicações que estiver a utilizar protocolos legados para fazer pedidos de autenticação. Para os utilizadores que não são apresentados nestes registos e forem confirmados como para não estar a utilizar autenticação, implementar uma política de acesso condicional ou ativar a **política de linha de base: autenticação de legado do bloco** para estes utilizadores apenas.

## <a name="moving-away-from-legacy-authentication"></a>Ao se afastar antigos de autenticação

Depois de ter uma ideia melhor de quem está usando autenticação no seu diretório e as aplicações que dependem dele, o passo seguinte é atualizar os utilizadores a utilizar autenticação moderna. Autenticação moderna é um método de gestão de identidades que oferece mais seguro de autenticação de utilizador e autorização. Se tiver uma política da MFA no local no seu diretório, a autenticação moderna garante que é pedido ao utilizador para a MFA quando necessário. É a alternativa mais segura para seus protocolos de autenticação.

Esta secção fornece uma descrição geral do passo a passo sobre como atualizar o seu ambiente para autenticação moderna. Leia os passos abaixo antes de ativar uma autenticação bloquear a política na sua organização.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passo 1: Ativar a autenticação moderna no seu diretório

O primeiro passo para ativar a autenticação moderna está fazendo-se de que o diretório suporta a autenticação moderna. Autenticação moderna está ativada por predefinição para diretórios criada em ou após 1 de Agosto de 2017. Se seu diretório foi criado antes desta data, terá de ativar manualmente a autenticação moderna para o seu diretório com os seguintes passos:

1. Verifique se o diretório já oferece suporte a autenticação moderna, executando `Get-CsOAuthConfiguration` partir a [Skype para empresas Online PowerShell módulo](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se o comando devolve vazio `OAuthServers` propriedade, em seguida, autenticação moderna está desativada. Atualizar a definição para ativar a autenticação moderna com `Set-CsOAuthConfiguration`. Se sua `OAuthServers` propriedade contém uma entrada, está pronto para começar.

Certifique-se de que concluir este passo antes de prosseguir. É fundamental que as configurações de diretório são alteradas em primeiro lugar porque elas ditam qual protocolo será utilizado por todos os clientes do Office. Mesmo se estiver a utilizar os clientes do Office que suportam a autenticação moderna, eles serão predefinido para utilizar protocolos legados, se a autenticação moderna está desactivada no seu diretório.

### <a name="step-2-office-applications"></a>Passo 2: Aplicativos do Office

Uma vez que ativou a autenticação moderna no seu diretório, pode começar a atualização de aplicativos, permitindo que a autenticação moderna para os clientes do Office. Office 2016 ou clientes posteriores suportam a autenticação moderna por predefinição. Não existem passos adicionais são necessários.

Se estiver a utilizar os clientes do Office 2013 Windows ou mais, recomendamos que Atualize para o Office 2016 ou posterior. Mesmo depois de concluir o passo anterior de ativação da autenticação moderna no seu diretório, os aplicativos do Office mais antigos continuará a ter de utilizar protocolos de autenticação. Se estiver a utilizar os clientes do Office 2013 e que não é possível atualizar imediatamente para o Office 2016 ou posterior, siga os passos no seguinte artigo para [ativar autenticação moderna do Office 2013 em dispositivos Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Para ajudar a proteger a sua conta enquanto estiver a utilizar autenticação, recomendamos que utilize palavras-passe seguras em seu diretório. Confira [proteção de palavra-passe do Azure AD](../authentication/concept-password-ban-bad.md) proibir o uso de senhas fracas em seu diretório.

Office 2010 não suporta a autenticação moderna. Precisará atualizar todos os utilizadores com o Office 2010 para uma versão mais recente do Office. Recomendamos que Atualize para o Office 2016 ou posterior, como ele bloqueia antigos de autenticação por predefinição.

Se estiver a utilizar o MacOS, recomendamos que Atualize para o Office para Mac 2016 ou posterior. Se estiver a utilizar o cliente de e-mail nativa, terá de ter o MacOS versão 10.14 ou posteriormente, todos os dispositivos.

### <a name="step-3-exchange-and-sharepoint"></a>Passo 3: Exchange e SharePoint

Para clientes do Outlook com base no Windows utilizar a autenticação moderna, Exchange Online têm de ser autenticação moderna também ativada. Se a autenticação moderna está desativada para o Exchange Online, os clientes do Outlook com base em Windows que suportam autenticação moderna (Outlook 2013 ou posterior) irá utilizar a autenticação básica para ligar a caixas de correio do Exchange Online.

SharePoint Online está ativado para padrão de autenticação moderna. Para diretórios criados após 1 de Agosto de 2017, a autenticação moderna está ativada por predefinição no Exchange Online. No entanto, se tivesse desabilitado anteriormente autenticação moderna ou está a utilizar um diretório criado antes desta data, siga os passos no seguinte artigo para [ativar autenticação moderna no Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Passo 4: Skype para Empresas

Para impedir que os pedidos de autenticação legada feitos pelo Skype para empresas, é necessário ativar a autenticação moderna do Skype para empresas Online. Para diretórios criados após 1 de Agosto de 2017, a autenticação moderna do Skype para empresas está ativada por predefinição.

Para ativar a autenticação moderna no Skype para empresas, sugerimos que faz a transição para o Microsoft Teams, que suporta a autenticação moderna por predefinição. No entanto, se conseguir tr neste momento, terá de ativar a autenticação moderna do Skype para empresas Online, para que o Skype para clientes empresariais começa com autenticação moderna. Siga estes passos no artigo [Skype para topologias de negócio com autenticação moderna](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported), para obter passos para ativar a autenticação moderna para o Skype para empresas.

Além de permitir a autenticação moderna do Skype para empresas Online, recomendamos a moderna autenticação estar ativada para o Exchange Online ao ativar a autenticação moderna do Skype para empresas. Este processo ajudará a sincronizar o estado da autenticação moderna, no Exchange Online e Skype para empresas online e irá impedir que vários pedidos de início de sessão para o Skype para clientes empresariais.

### <a name="step-5-using-mobile-devices"></a>Passo 5: Utilizar dispositivos móveis

Aplicações no seu dispositivo móvel precisam bloquear antigos de autenticação também. Recomendamos que utilize o Outlook para dispositivos móveis. Outlook Mobile suporta a autenticação moderna por predefinição e irá satisfazer outras políticas de proteção de linha de base do MFA.

Para utilizar o cliente de e-mail nativa no iOS, terá de ter o iOS versão 11.0 ou posterior para garantir que o cliente de correio foi atualizado para bloquear antigos de autenticação.

### <a name="step-6-on-premises-clients"></a>Passo 6: Clientes no local

Se for um cliente de híbrida com o Exchange Server no local e o Skype para empresas no local, ambos os serviços precisará ser atualizado para ativar a autenticação moderna. Ao utilizar a autenticação moderna num ambiente híbrido, ainda está de autenticar os utilizadores no local. A história de autorizar o acesso às alterações de recursos (ficheiros ou e-mails).

Antes de poder ativar a autenticação moderna no local, certifique-se de que cumpre theIf cumprir os requisitos, agora, está pronto para ativar a autenticação moderna no local.

Passos para ativar a autenticação moderna encontrar nos seguintes artigos:

* [Como configurar o Exchange Server no local para utilizar autenticação moderna híbrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Como utilizar a autenticação moderna (ADAL) com o Skype para empresas](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Ativar a política de linha de base

A política **política de linha de base: Bloco antigos de autenticação (pré-visualização)** vem pré-configurada e será apresentada na parte superior ao navegar para o painel de acesso condicional no portal do Azure.

Para ativar esta política e proteger a sua organização:

1. Inicie sessão para o **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **do Azure Active Directory** > **acesso condicional**.
1. Na lista de políticas, selecione **política de linha de base: Bloco antigos de autenticação (pré-visualização)** .
1. Definir **ativar política** ao **utilizar a política imediatamente**.
1. Adicionar exclusões utilizador ao clicar em **usuários** > **selecionar utilizadores excluídos** e escolher os utilizadores que têm de ser excluídos. Clique em **selecionar** , em seguida, **feito**.
1. Clique em **guardar**.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

* [Políticas de proteção de linha de base de acesso condicional](concept-baseline-protection.md)
* [Cinco etapas para proteger a sua infraestrutura de identidade](../../security/azure-ad-secure-steps.md)
* [O que é o acesso condicional no Azure Active Directory?](overview.md)
