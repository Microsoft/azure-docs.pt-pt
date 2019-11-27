---
title: Bloquear autenticação herdada-Azure Active Directory
description: Saiba como melhorar sua postura de segurança bloqueando a autenticação herdada usando o acesso condicional do Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a65145fe9752a90e3328c308ce603c8626d8708
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380866"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como bloquear a autenticação herdada no Azure AD com acesso condicional   

Para dar aos usuários acesso fácil aos seus aplicativos de nuvem, o Azure Active Directory (Azure AD) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo autenticação herdada. No entanto, os protocolos herdados não dão suporte à MFA (autenticação multifator). A MFA está em muitos ambientes um requisito comum para abordar o roubo de identidade. 

Se o seu ambiente estiver pronto para bloquear a autenticação herdada para melhorar a proteção do locatário, você poderá atingir esse objetivo com o acesso condicional. Este artigo explica como você pode configurar políticas de acesso condicional que bloqueiam a autenticação herdada para seu locatário.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- Os [conceitos básicos](overview.md) do acesso condicional do Azure AD 
- As [práticas recomendadas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure

## <a name="scenario-description"></a>Descrição do cenário

O Azure AD dá suporte a vários dos protocolos de autenticação e autorização mais amplamente usados, incluindo autenticação herdada. A autenticação herdada refere-se aos protocolos que usam a autenticação básica. Normalmente, esses protocolos não podem impor nenhum tipo de autenticação de segundo fator. Exemplos de aplicativos baseados em autenticação herdada são:

- Aplicativos Microsoft Office mais antigos
- Aplicativos que usam protocolos de email como POP, IMAP e SMTP

A autenticação de fator único (por exemplo, nome de usuário e senha) não é suficiente atualmente. As senhas são ruins, pois são fáceis de adivinhar e nós (humanos) são ruins na escolha de senhas boas. As senhas também são vulneráveis a uma variedade de ataques, como phishing e spray de senha. Uma das coisas mais fáceis que você pode fazer para se proteger contra ameaças à senha é implementar a MFA. Com o MFA, mesmo que um invasor fique em posse da senha de um usuário, a senha sozinha não é suficiente para autenticar e acessar os dados com êxito.

Como você pode impedir que aplicativos que usam autenticação herdada acessem os recursos do seu locatário? A recomendação é simplesmente bloqueá-los com uma política de acesso condicional. Se necessário, você permite que apenas determinados usuários e locais de rede específicos usem aplicativos baseados na autenticação herdada.

As políticas de acesso condicional são impostas após a conclusão da autenticação de primeiro fator. Portanto, o acesso condicional não se destina como uma defesa de primeira linha para cenários como ataques de DoS (negação de serviço), mas pode utilizar sinais desses eventos (por exemplo, o nível de risco de entrada, o local da solicitação e assim por diante) para determinar o acesso.

## <a name="implementation"></a>Implementação

Esta seção explica como configurar uma política de acesso condicional para bloquear a autenticação herdada. 

### <a name="identify-legacy-authentication-use"></a>Identificar o uso de autenticação herdada

Antes de poder bloquear a autenticação herdada em seu diretório, primeiro você precisa entender se os usuários têm aplicativos que usam autenticação herdada e como ele afeta o diretório geral. Os logs de entrada do Azure AD podem ser usados para entender se você está usando a autenticação herdada.

1. Navegue até o **portal do Azure** > **Azure Active Directory** > **entradas**.
1. Adicione a coluna aplicativo cliente se ela não for exibida clicando em **colunas** > **aplicativo cliente**.
1. **Adicionar filtros** > **aplicativo cliente** > selecione todas as opções para **outros clientes** e clique em **aplicar**.

A filtragem mostrará apenas as tentativas de entrada feitas por protocolos de autenticação herdados. Clicar em cada tentativa de entrada individual mostrará detalhes adicionais. O campo **aplicativo cliente** na guia **informações básicas** indicará qual protocolo de autenticação herdado foi usado.

Esses logs indicarão quais usuários ainda estão dependendo da autenticação herdada e quais aplicativos estão usando protocolos herdados para fazer solicitações de autenticação. Para usuários que não aparecem nesses logs e são confirmados para não usar a autenticação herdada, implemente uma política de acesso condicional somente para esses usuários.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada 

Em uma política de acesso condicional, você pode definir uma condição que esteja vinculada aos aplicativos cliente que são usados para acessar seus recursos. A condição aplicativos cliente permite que você restrinja o escopo a aplicativos usando a autenticação herdada selecionando **outros clientes** para **aplicativos móveis e clientes de desktop**.

![Outros clientes](./media/block-legacy-authentication/01.png)

Para bloquear o acesso a esses aplicativos, você precisa selecionar **bloquear acesso**.

![Bloquear acesso](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selecionar usuários e aplicativos de nuvem

Se você quiser bloquear a autenticação herdada para sua organização, provavelmente imagina que possa fazer isso selecionando:

- Todos os utilizadores
- Todos os aplicativos de nuvem
- Bloquear acesso

![Atribuições](./media/block-legacy-authentication/03.png)

O Azure tem um recurso de segurança que impede a criação de uma política como essa, pois essa configuração viola as [práticas recomendadas](best-practices.md) para políticas de acesso condicional.
 
![Configuração de política sem suporte](./media/block-legacy-authentication/04.png)

O recurso de segurança é necessário porque *bloquear todos os usuários e todos os aplicativos de nuvem* tem o potencial de bloquear toda a sua organização de se conectar ao seu locatário. Você deve excluir pelo menos um usuário para atender ao requisito mínimo de práticas recomendadas. Você também pode excluir uma função de diretório.

![Configuração de política sem suporte](./media/block-legacy-authentication/05.png)

Você pode atender a esse recurso de segurança, excluindo um usuário da sua política. O ideal é que você defina algumas [contas administrativas de acesso de emergência no Azure ad](../users-groups-roles/directory-emergency-access.md) e as exclua da sua política.

## <a name="policy-deployment"></a>Implantação de política

Antes de colocar a política em produção, tome cuidado:
 
- **Contas de serviço** – identifique as contas de usuário que são usadas como contas de serviço ou por dispositivos, como telefones da sala de conferência. Verifique se essas contas têm senhas fortes e adicione-as a um grupo excluído.
- **Relatórios de entrada** -examine o relatório de entrada e procure outro tráfego de **cliente** . Identifique o uso superior e investigue por que ele está em uso. Normalmente, o tráfego é gerado por clientes mais antigos do Office que não usam autenticação moderna ou alguns aplicativos de email de terceiros. Faça um plano para mover o uso para fora desses aplicativos ou, se o impacto for baixo, notifique os usuários de que eles não podem mais usar esses aplicativos.
 
Para obter mais informações, consulte [como você deve implantar uma nova política?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>O que deve saber

O bloqueio de acesso usando **outros clientes** também bloqueia o Exchange Online PowerShell e o Dynamics 365 usando a autenticação básica.

Configurar uma política para **outros clientes** bloqueia toda a organização de determinados clientes como o OnConnect. Esse bloco ocorre porque os clientes mais antigos se autenticam de maneiras inesperadas. O problema não se aplica aos principais aplicativos do Office, como os clientes mais antigos do Office.

Pode levar até 24 horas para que a política entre em vigor.

Você pode selecionar todos os controles de concessão disponíveis para a condição **outros clientes** ; no entanto, a experiência do usuário final é sempre o mesmo acesso bloqueado.

Se você bloquear a autenticação herdada usando a condição **outros clientes** , também poderá definir a plataforma do dispositivo e a condição de local. Por exemplo, se você quiser apenas bloquear a autenticação herdada para dispositivos móveis, defina a condição **plataformas de dispositivo** selecionando:

- Android
- iOS
- Windows Phone

![Configuração de política sem suporte](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Passos seguintes

- Se você ainda não estiver familiarizado com a configuração de políticas de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md) para obter um exemplo.
- Para obter mais informações sobre o suporte à autenticação moderna, consulte [como funciona a autenticação moderna para aplicativos cliente do office 2013 e do office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
