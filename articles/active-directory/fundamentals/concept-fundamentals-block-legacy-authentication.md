---
title: Bloquear protocolos de autenticação de legados em Azure AD
description: Saiba como e por que as organizações devem bloquear protocolos de autenticação de legados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: b50c942d2e05d7f5234a17f1cf36137309c7ce97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320958"
---
# <a name="blocking-legacy-authentication"></a>Bloquear a autenticação do legado
 
Para facilitar o acesso dos seus utilizadores às suas aplicações na nuvem, o Azure Ative Directory (Azure AD) suporta uma grande variedade de protocolos de autenticação, incluindo a autenticação antiga. Autenticação de legado é um termo que se refere a um pedido de autenticação feito por:

- Clientes do Escritório mais antigos que não utilizam a autenticação moderna (por exemplo, cliente do Office 2010)
- Qualquer cliente que utilize protocolos de correio antigos como IMAP/SMTP/POP3

Hoje, a maioria de todas as tentativas de inscrição comprometedoras provêm da autenticação do legado. A autenticação do legado não suporta a autenticação de vários fatores (MFA). Mesmo que tenha uma política de MFA ativada no seu diretório, um mau ator pode autenticar usando um protocolo legado e contornar o MFA. A melhor forma de proteger a sua conta de pedidos de autenticação maliciosa feitos por protocolos antigos é bloquear completamente estas tentativas.

## <a name="identify-legacy-authentication-use"></a>Identificar o uso da autenticação de legados

Antes de bloquear a autenticação do legado no seu diretório, tem de primeiro compreender se os seus utilizadores têm aplicações que utilizam a autenticação antiga e como isso afeta o seu diretório geral. Os registos de login AD do Azure podem ser utilizados para entender se está a utilizar a autenticação antiga.

1. Navegue para o **portal**  >  **Azure Ative Directory**  >  **Ins**.
1. Adicione a coluna **'App' cliente** se não for mostrada clicando na App do Cliente **das Colunas.**   >  **Client App**
1. Filter by **Client App** > verificar todas as opções de Clientes de Autenticação Legacy **apresentadas.**
1. Filtrar por **Status**  >  **Success**. 
1. Aumente o seu intervalo de datas, se necessário, utilizando o filtro **Data.**

A filtragem só lhe mostrará tentativas de entrada bem sucedidas que foram feitas pelos protocolos de autenticação de legados selecionados. Clicar em cada tentativa de inscrição individual irá mostrar-lhe detalhes adicionais. A coluna da App cliente ou o campo de Aplicação do Cliente no separador Informações Básicas após a seleção de uma linha individual de dados indicará qual o protocolo de autenticação legado utilizado. Estes registos indicam quais os utilizadores que ainda estão dependentes da autenticação antiga e quais as aplicações que estão a utilizar protocolos legados para fazer pedidos de autenticação. Para os utilizadores que não aparecem nestes registos e que se confirmam não estar a utilizar a autenticação antiga, implemente uma política de Acesso Condicional ou permita a política de Base: bloqueie a autenticação de legado apenas para estes utilizadores.

## <a name="moving-away-from-legacy-authentication"></a>Afastando-se da autenticação do legado 

Uma vez que tenha uma ideia melhor de quem está a usar a autenticação antiga no seu diretório e quais as aplicações que dependem dele, o próximo passo é atualizar os seus utilizadores para utilizarem a autenticação moderna. A autenticação moderna é um método de gestão de identidade que oferece uma autenticação e autorização mais seguras do utilizador. Se tiver uma política de MFA em vigor no seu diretório, a autenticação moderna garante que o utilizador é solicitado para MFA quando necessário. É a alternativa mais segura aos protocolos de autenticação de legados.

Esta secção dá uma visão geral passo a passo sobre como atualizar o seu ambiente para a autenticação moderna. Leia os passos abaixo antes de ativar uma política de bloqueio de autenticação na sua organização.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passo 1: Ativar a autenticação moderna no seu diretório

O primeiro passo para permitir a autenticação moderna é garantir que o seu diretório suporta a autenticação moderna. A autenticação moderna é ativada por padrão para diretórios criados em ou após 1 de agosto de 2017. Se o seu diretório foi criado antes desta data, terá de ativar manualmente a autenticação moderna para o seu diretório utilizando os seguintes passos:

1. Verifique se o seu diretório já suporta a autenticação moderna, funcionando  `Get-CsOAuthConfiguration`   a partir do módulo Skype for Business [Online PowerShell](/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se o seu comando devolver uma  `OAuthServers`   propriedade vazia, então a Autenticação Moderna será desativada. Atualize a definição para permitir a autenticação moderna utilizando  `Set-CsOAuthConfiguration` . Se a sua  `OAuthServers`   propriedade contiver uma entrada, está pronto para ir.

Certifique-se de completar este passo antes de avançar. É fundamental que as configurações do seu diretório sejam alteradas primeiro porque ditam qual o protocolo que será usado por todos os clientes do Office. Mesmo que esteja a utilizar clientes do Office que suportam a autenticação moderna, eles não utilizarão protocolos antigos se a autenticação moderna for desativada no seu diretório.

### <a name="step-2-office-applications"></a>Passo 2: Candidaturas ao escritório

Uma vez ativada a autenticação moderna no seu diretório, pode começar a atualizar as aplicações, permitindo a autenticação moderna para clientes do Office. O Office 2016 ou posteriormente os clientes suportam a autenticação moderna por padrão. Não são necessários passos adicionais.

Se estiver a utilizar clientes Windows ou mais velhos do Office 2013, recomendamos o upgrade para o Office 2016 ou mais tarde. Mesmo depois de concluída a etapa anterior de permitir a autenticação moderna no seu diretório, as aplicações antigas do Office continuarão a utilizar protocolos de autenticação legado. Se estiver a utilizar clientes do Office 2013 e não conseguir fazer o upgrade imediatamente para o Office 2016 ou posteriormente, siga os passos do seguinte artigo para Permitir a [Autenticação Moderna para o Office 2013 em dispositivos Windows](/office365/admin/security-and-compliance/enable-modern-authentication). Para ajudar a proteger a sua conta enquanto utiliza a autenticação antiga, recomendamos a utilização de senhas fortes em todo o seu diretório. Consulte a [proteção de senha Azure AD](../authentication/concept-password-ban-bad.md)   para proibir senhas fracas em todo o seu diretório.

O Office 2010 não suporta a autenticação moderna. Terá de atualizar qualquer utilizadores com o Office 2010 para uma versão mais recente do Office. Recomendamos o upgrade para o Office 2016 ou mais tarde, uma vez que bloqueia a autenticação do legado por padrão.

Se estiver a utilizar o macOS, recomendamos o upgrade para o Office for Mac 2016 ou mais tarde. Se estiver a utilizar o cliente de correio nativo, terá de ter a versão 10.14 do macOS ou posterior em todos os dispositivos.

### <a name="step-3-exchange-and-sharepoint"></a>Passo 3: Troca e SharePoint

Para que os clientes do Outlook baseados no Windows utilizem a autenticação moderna, o Exchange Online também deve estar habilitado para autenticação moderna. Se a autenticação moderna for desativada para os clientes Exchange Online, os clientes do Outlook baseados no Windows que suportam a autenticação moderna (Outlook 2013 ou posterior) utilizarão a autenticação básica para se ligarem às caixas de correio do Exchange Online.

O SharePoint Online está ativado para o padrão de autenticação moderna. Para diretórios criados após 1 de agosto de 2017, a autenticação moderna é ativada por padrão no Exchange Online. No entanto, se já tinha desativado a autenticação moderna ou se está a utilizar um diretório criado antes desta data, siga os passos do seguinte artigo para Permitir a [autenticação moderna no Exchange Online.](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

### <a name="step-4-skype-for-business"></a>Passo 4: Skype para negócios

Para evitar pedidos de autenticação legado feitos pelo Skype para negócios, é necessário permitir a autenticação moderna do Skype para o Business Online. Para diretórios criados após 1 de agosto de 2017, a autenticação moderna para o Skype para negócios é ativada por padrão.

Sugerimos a transição para o Microsoft Teams, que suporta a autenticação moderna por padrão. No entanto, se não conseguir migrar neste momento, terá de ativar a autenticação moderna do Skype para o Business Online para que os clientes skype para negócios comecem a utilizar a autenticação moderna. Siga os passos neste artigo [Skype for Business toplogies suportados com Autenticação Moderna,](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)para permitir a Autenticação Moderna para o Skype para Negócios.

Além de permitir a autenticação moderna para o Skype para o Business Online, recomendamos permitir a autenticação moderna para Exchange Online ao permitir a autenticação moderna para o Skype para negócios. Este processo ajudará a sincronizar o estado da autenticação moderna em Exchange Online e Skype para Negócios online e evitará múltiplas solicitações de login para clientes do Skype para clientes empresariais.

### <a name="step-5-using-mobile-devices"></a>Passo 5: Utilização de dispositivos móveis

As aplicações no seu dispositivo móvel também precisam de bloquear a autenticação do legado. Recomendamos a utilização do Outlook for Mobile. O Outlook for Mobile suporta a autenticação moderna por padrão e irá satisfazer outras políticas de proteção de base MFA.

Para utilizar o cliente de correio iOS nativo, terá de estar a executar a versão 11.0 do iOS ou posterior para garantir que o cliente de correio foi atualizado para bloquear a autenticação do legado.

### <a name="step-6-on-premises-clients"></a>Passo 6: Clientes no local

Se for um cliente híbrido que utiliza o Exchange Server no local e o Skype para negócios no local, ambos os serviços terão de ser atualizados para permitir a autenticação moderna. Ao utilizar a autenticação moderna num ambiente híbrido, ainda está a autenticar os utilizadores no local. A história de autorizar o seu acesso a recursos (ficheiros ou e-mails) muda.

Antes de começar a ativar a autenticação moderna no local, certifique-se de que cumpriu os requisitos prévios. Está agora pronto para permitir a autenticação moderna no local.

Os passos para permitir a autenticação moderna podem ser encontrados nos seguintes artigos:

* [Como configurar o Exchange Server no local para utilizar a autenticação moderna híbrida](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Como utilizar a Autenticação Moderna (ADAL) com o Skype para o negócio](/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Passos seguintes

- [Como configurar o Exchange Server no local para utilizar a autenticação moderna híbrida](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Como utilizar a Autenticação Moderna (ADAL) com o Skype para o negócio](/skypeforbusiness/manage/authentication/use-adal)
- [Bloquear a autenticação legada](../conditional-access/block-legacy-authentication.md)