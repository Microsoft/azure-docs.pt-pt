---
title: Autenticação do legado do bloco - Diretório Ativo Azure
description: Aprenda a melhorar a sua postura de segurança bloqueando a autenticação do legado utilizando o Acesso Condicional Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f7f6f31c4d2f67660fef507ce101b2d15897d51
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620853"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como: Bloquear a autenticação legado à Azure AD com acesso condicional   

Para dar aos seus utilizadores um acesso fácil às suas aplicações na nuvem, o Azure Ative Directory (Azure AD) suporta uma ampla variedade de protocolos de autenticação, incluindo a autenticação do legado. No entanto, os protocolos legados não suportam a autenticação de vários fatores (MFA). O MFA é, em muitos ambientes, um requisito comum para lidar com o roubo de identidade. 

Se o seu ambiente estiver pronto para bloquear a autenticação do legado para melhorar a proteção do seu inquilino, pode realizar esse objetivo com acesso condicional. Este artigo explica como pode configurar políticas de Acesso Condicional que bloqueiam a autenticação do legado para o seu inquilino.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que está familiarizado com: 

- Os [conceitos básicos](overview.md) de Acesso Condicional Azure AD 
- As [melhores práticas](best-practices.md) para configurar políticas de acesso condicional no portal Azure

## <a name="scenario-description"></a>Descrição do cenário

A Azure AD suporta vários dos protocolos de autenticação e autorização mais utilizados, incluindo a autenticação do legado. A autenticação do legado refere-se a protocolos que utilizam a autenticação básica. Normalmente, estes protocolos não podem impor qualquer tipo de autenticação de segundo fator. Exemplos para aplicações baseadas na autenticação do legado são:

- Aplicativos mais antigos do Microsoft Office
- Aplicativos que usam protocolos de correio como POP, IMAP e SMTP

A autenticação de um único fator (por exemplo, nome de utilizador e palavra-passe) não é suficiente nos dias de hoje. As palavras-passe são más porque são fáceis de adivinhar e nós (humanos) somos maus na escolha de boas senhas. As palavras-passe também são vulneráveis a uma variedade de ataques como phishing e spray de senha. Uma das coisas mais fáceis que pode fazer para proteger contra ameaças de senha é implementar MFA. Com o MFA, mesmo que um intruso obtenha a posse da palavra-passe de um utilizador, a palavra-passe por si só não é suficiente para autenticar e aceder com sucesso aos dados.

Como evitar que as aplicações que utilizem a autenticação do legado acedam aos recursos do seu inquilino? A recomendação é apenas bloqueá-los com uma política de acesso condicional. Se necessário, permite que apenas certos utilizadores e localizações específicas da rede utilizem aplicações baseadas na autenticação do legado.

As políticas de Acesso Condicional são aplicadas após a autenticação do primeiro fator ter sido concluída. Por isso, o Acesso Condicional não se destina a uma defesa de primeira linha para cenários como ataques de negação de serviço (DoS), mas pode utilizar sinais destes eventos (por exemplo, o nível de risco de inscrição, localização do pedido, e assim por diante) para determinar o acesso.

## <a name="implementation"></a>Implementação

Esta secção explica como configurar uma política de Acesso Condicional para bloquear a autenticação do legado. 

### <a name="legacy-authentication-protocols"></a>Protocolos de autenticação legado

As seguintes opções são consideradas protocolos de autenticação legado

- SMTP autenticado - Usado pelos clientes POP e IMAP para enviar mensagens de correio eletrónico.
- Autodiscover - Usado pelos clientes Outlook e EAS para encontrar e ligar-se a caixas de correio no Exchange Online.
- Troca online PowerShell - Usado para ligar a Exchange Online com powerShell remoto. Se bloquear a autenticação Básica para troca de PowerShell online, tem de utilizar o Módulo Exchange Online PowerShell para se ligar. Para obter instruções, consulte Connect to Exchange Online PowerShell utilizando a [autenticação de vários fatores](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Exchange Web Services (EWS) - Uma interface de programação que é usada pelo Outlook, Outlook for Mac e aplicações de terceiros.
- IMAP4 - Usado por clientes de e-mail IMAP.
- MAPI sobre HTTP (MAPI/HTTP) - Usado pelo Outlook 2010 e mais tarde.
- Offline Address Book (OAB) - Uma cópia das coleções da lista de endereços que são descarregadas e utilizadas pelo Outlook.
- Outlook Anywhere (RPC over HTTP) - Usado pelo Outlook 2016 e mais cedo.
- Outlook Service - Usado pelo aplicativo Mail and Calendar para windows 10.
- POP3 - Usado por clientes de e-mail POP.
- Reporting Web Services - Usado para recuperar dados de relatório sintetizados em Exchange Online.
- Outros clientes - Outros protocolos identificados como utilização da autenticação do legado.

### <a name="identify-legacy-authentication-use"></a>Identificar o uso da autenticação do legado

Antes de poder bloquear a autenticação do legado no seu diretório, tem de perceber primeiro se os seus utilizadores têm aplicações que utilizam a autenticação do legado e como isso afeta o seu diretório geral. Os registos de entrada de AD Azure podem ser usados para entender se está a usar a autenticação do legado.

1. Navegue para o **portal Azure** > **Diretório Ativo Azure** > **Sign-ins.**
1. Adicione a coluna da Aplicação cliente se não for mostrada clicando em **Colunas** > **Aplicação do Cliente**.
1. **Adicione filtros** > **App cliente** > selecione todos os protocolos de autenticação legados e clique **em Aplicar**.

A filtragem só lhe mostrará tentativas de inscrição que foram feitas por protocolos de autenticação legados. Clicar em cada tentativa de inscrição individual irá mostrar-lhe detalhes adicionais. O campo **de aplicações do cliente** ao abrigo do separador **Informação Básica** indicará qual o protocolo de autenticação legado utilizado.

Estes registos indicarão quais os utilizadores que ainda dependem da autenticação do legado e quais as aplicações que estão a utilizar protocolos legados para fazer pedidos de autenticação. Para utilizadores que não apareçam nestes registos e que se confirme que não estão a utilizar a autenticação do legado, implemente uma política de Acesso Condicional apenas para estes utilizadores.

### <a name="block-legacy-authentication"></a>Bloquear a autenticação legada 

Numa política de Acesso Condicional, pode definir uma condição que está ligada às aplicações de clientes que são usadas para aceder aos seus recursos. A condição de aplicações do cliente permite-lhe reduzir o âmbito de aplicações usando a autenticação legado, selecionando **Outros clientes** para **aplicações Móveis e clientes de desktop.**

![Outros clientes](./media/block-legacy-authentication/01.png)

Para bloquear o acesso a estas aplicações, é necessário selecionar **o acesso**ao Bloco .

![Acesso ao bloco](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Selecione utilizadores e aplicações na nuvem

Se quiser bloquear a autenticação do legado para a sua organização, provavelmente acha que pode fazê-lo selecionando:

- Todos os utilizadores
- Todas as aplicações em nuvem
- Acesso ao bloco

![Atribuições](./media/block-legacy-authentication/03.png)

O Azure tem uma funcionalidade de segurança que o impede de criar uma política como esta porque esta configuração viola as [melhores práticas](best-practices.md) para as políticas de Acesso Condicional.
 
![Configuração de política não suportada](./media/block-legacy-authentication/04.png)

A funcionalidade de segurança é necessária porque *bloqueie todos os utilizadores e todas as aplicações na nuvem* tem o potencial de impedir que toda a sua organização se inscreva no seu inquilino. Deve excluir pelo menos um utilizador para satisfazer o requisito mínimo de boas práticas. Também pode excluir um papel de diretório.

![Configuração de política não suportada](./media/block-legacy-authentication/05.png)

Pode satisfazer esta função de segurança excluindo um utilizador da sua apólice. Idealmente, deve definir [algumas contas administrativas de acesso de emergência em Azure AD e excluí-las](../users-groups-roles/directory-emergency-access.md) da sua política.

## <a name="policy-deployment"></a>Implementação de política

Antes de colocar a sua política em produção, cuide-se:
 
- **Contas** de serviço - Identifique as contas de utilizador que são usadas como contas de serviço ou por dispositivos, como telefones de sala de conferências. Certifique-se de que estas contas têm senhas fortes e adicione-as a um grupo excluído.
- **Relatórios de inscrição** - Reveja o relatório de inscrição e procure outro tráfego **de clientes.** Identifique o uso de topo e investigue por que está em uso. Normalmente, o tráfego é gerado por clientes mais antigos do Office que não usam autenticação moderna, ou algumas aplicações de correio de terceiros. Faça um plano para afastar o uso destas aplicações, ou se o impacto for baixo, notifique os seus utilizadores de que já não podem usar estas aplicações.
 
Para mais informações, veja [como deve implementar uma nova política?](best-practices.md#how-should-you-deploy-a-new-policy)

## <a name="what-you-should-know"></a>O que deve saber

Bloquear o acesso utilizando **Outros clientes** também bloqueiam o Exchange Online PowerShell e a Dynamics 365 utilizando auth básica.

Configurar uma política para **outros clientes** bloqueia toda a organização de certos clientes como o SPConnect. Este bloco acontece porque os clientes mais velhos autenticam de formainesperada. A questão não se aplica a grandes aplicações do Office, como os clientes mais velhos do Office.

Pode levar até 24 horas para a política entrar em vigor.

Pode selecionar todos os controlos de subvenção disponíveis para a condição **de Outros clientes;** no entanto, a experiência do utilizador final é sempre a mesma - acesso bloqueado.

Se bloquear a autenticação do legado utilizando a condição **de Outros clientes,** também pode definir a plataforma do dispositivo e o estado de localização. Por exemplo, se pretender bloquear a autenticação do legado para dispositivos móveis, detete a condição das plataformas do **dispositivo** selecionando:

- Android
- iOS
- Windows Phone

![Configuração de política não suportada](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Passos seguintes

- Se ainda não está familiarizado com a configuração das políticas de Acesso Condicional, consulte [o MFA para aplicações específicas com acesso condicional de diretório ativo Azure,](app-based-mfa.md) por exemplo.
- Para mais informações sobre o suporte moderno à autenticação, consulte como funciona a autenticação moderna para as [aplicações de clientes do Office 2013 e do Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
