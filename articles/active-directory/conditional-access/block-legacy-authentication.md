---
title: Como bloquear antigos de autenticação para o Azure Active Directory (Azure AD) com acesso condicional | Documentos da Microsoft
description: Saiba como melhorar a sua postura de segurança bloqueando antigos de autenticação através do acesso condicional do Azure AD.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: be66ead6521bdb21626caaecd582fac4da6f664b
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351274"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como: Autenticação de legado de bloco para o Azure AD com o acesso condicional   

Conceder acesso fácil aos seus utilizadores para as suas aplicações na cloud, Azure Active Directory (Azure AD) oferece suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação. No entanto, os protocolos legados não suportam autenticação multifator (MFA). MFA é, em muitos ambientes, um requisito comum para roubo de identidade de endereço. 


Se o ambiente está pronto para autenticação de legado do bloco para melhorar a proteção do seu inquilino, pode realizar esse objetivo com o acesso condicional. Este artigo explica como pode configurar políticas de acesso condicional desse bloco antigos de autenticação para o seu inquilino.



## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com: 

- O [conceitos básicos](overview.md) de acesso condicional do Azure AD 
- O [melhores práticas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure



## <a name="scenario-description"></a>Descrição do cenário

O Azure AD suporta vários protocolos de autenticação e autorização mais amplamente utilizados incluindo antigos de autenticação. Autenticação refere-se aos protocolos que utilizam a autenticação básica. Normalmente, esses protocolos não é possível impor qualquer tipo de autenticação de segundo fator. Exemplos de aplicações que se baseiam em antigos de autenticação são:

- Aplicações do Microsoft Office mais antigas

- Aplicações através de protocolos de email, como POP, IMAP e SMTP

Autenticação de fator único (por exemplo, nome de utilizador e palavra-passe) não é suficiente nos dias de hoje. As palavras-passe que são ruins à medida que são fáceis de adivinhar e nós (humanos) que são ruins da escolha de palavras-passe boa. As palavras-passe também são vulneráveis a uma variedade de ataques, como spray de phishing e a palavra-passe. Uma das coisas mais fácil, que pode fazer para proteger contra ameaças de palavra-passe é implementar o MFA. Com a MFA, mesmo que um atacante obtém na posse de palavra-passe de um utilizador, a palavra-passe sozinha não é suficiente para autenticar e acessar os dados com êxito.

Como pode impedir a aplicações que utilizam autenticação legada de aceder a recursos do seu inquilino? A recomendação é simplesmente bloqueá-los com uma política de acesso condicional. Se necessário, permite que apenas determinados utilizadores e localizações de rede específicas utilizar as aplicações que se baseiam em antigos de autenticação.

Políticas de acesso condicional são aplicadas após o primeiro--factor authentication foi concluído. Por conseguinte, acesso condicional não se destina como uma defesa de linha de primeira para cenários como denial-of-service (DoS) distribuídos, mas pode utilizar os sinais desses eventos (por exemplo, o nível de risco de início de sessão, a localização do pedido e assim por diante) para determinar o acesso.




## <a name="implementation"></a>Implementação

Esta secção explica como configurar uma política de acesso condicional para bloquear antigos de autenticação. 

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada 

Numa política de acesso condicional, pode definir uma condição que está associada às aplicações de cliente que são utilizados para aceder aos seus recursos. A condição de aplicações de cliente permite-lhe restringir o âmbito para aplicações que utilizam a autenticação de legado, selecionando **outros clientes** para **aplicações móveis e clientes de ambiente de trabalho**.

![Outros clientes](./media/block-legacy-authentication/01.png)

Para bloquear o acesso para estas aplicações, tem de selecionar **bloquear o acesso**.

![Bloquear acesso](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>Selecione utilizadores e aplicações na cloud

Se deseja bloquear antigos de autenticação para a sua organização, provavelmente pensa que é possível fazer isso selecionando:

- Todos os utilizadores

- Todas as aplicações na cloud

- Bloquear acesso
 

![Atribuições](./media/block-legacy-authentication/03.png)



O Azure tem uma funcionalidade de segurança que o impede de criar uma política como esta, porque esta configuração viola os [melhores práticas](best-practices.md) para políticas de acesso condicional.
 
![Configuração de política não é suportada](./media/block-legacy-authentication/04.png)


A funcionalidade de segurança é necessária porque *bloquear todos os utilizadores e todas as aplicações na cloud* tem o potencial para bloquear toda a organização de iniciar sessão seu inquilino. Tem de excluir pelo menos um utilizador para satisfazer o requisito mínimo de práticas recomendado. Também pode excluir uma função de diretório.

![Configuração de política não é suportada](./media/block-legacy-authentication/05.png)


Pode atender esse recurso de segurança excluindo um utilizador da sua política. Idealmente, deve definir algumas [contas administrativas de acesso de emergência no Azure AD](../users-groups-roles/directory-emergency-access.md) e excluí-los da sua política.
 

## <a name="policy-deployment"></a>Implementação da política

Antes de colocar sua diretiva em produção, cuide de:
 
- **Contas de serviço** -identificar contas de utilizador que são utilizadas como contas de serviço ou por dispositivos, como telemóveis de sala de conferência. Certifique-se de que estas contas têm as palavras-passe fortes e adicioná-los a um grupo excluído.
 
- **Relatórios de início de sessão** - rever o relatório de início de sessão e procure **outro cliente** tráfego. Identifique a utilização superior e investigar por que motivo está a ser utilizado. Normalmente, o tráfego é gerado por clientes mais antigos do Office que não utilizam autenticação moderna, ou algumas aplicações de e-mail de terceiros. Criar um plano para mover a utilização na direção oposta estas aplicações, ou se o impacto é baixo, notifique os utilizadores que não podem utilizar estas aplicações mais.
 
Para obter mais informações, consulte [como deve implementar uma nova política?](best-practices.md#how-should-you-deploy-a-new-policy).



## <a name="what-you-should-know"></a>O que deve saber

Bloquear o acesso à através de **outros clientes** também bloqueia o Exchange Online PowerShell usando autenticação básica

Configurar uma política para **outros clientes** bloqueia toda a organização de determinados clientes, como SPConnect. Este bloco acontece porque os clientes antigos autenticar-se de maneiras inesperadas. O problema não se aplica a aplicativos do Office principais, como os clientes do Office mais antigos.

Pode demorar até 24 horas para a política para entrar em vigor.

Pode selecionar todos os controlos de concessão disponíveis para a outra condição de clientes; No entanto, a experiência de utilizador final é sempre o mesmo - bloqueou o acesso.

Se bloquear antigos de autenticação com a outra condição de clientes, também pode definir a condição de plataforma e a localização do dispositivo. Por exemplo, se apenas pretender bloquear antigos de autenticação para dispositivos móveis, defina o **plataformas de dispositivos** condição ao selecionar:

- Android

- iOS

- Windows Phone

![Configuração de política não é suportada](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>Passos Seguintes

Se não estiver familiarizado com a configuração de políticas de acesso condicional ainda, veja [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md) para obter um exemplo.
