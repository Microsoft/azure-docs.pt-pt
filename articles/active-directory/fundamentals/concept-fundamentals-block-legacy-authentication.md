---
title: Bloquear protocolos de autenticação legado em Azure AD
description: Saiba como e por que as organizações devem bloquear protocolos de autenticação do legado
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
ms.openlocfilehash: 1799f676e8971726832cc50598e119f029bc331d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196396"
---
# <a name="blocking-legacy-authentication"></a>Bloquear a autenticação do legado
 
Para dar aos seus utilizadores um acesso fácil às suas aplicações na nuvem, o Azure Ative Directory (Azure AD) suporta uma ampla variedade de protocolos de autenticação, incluindo a autenticação do legado. A autenticação do legado é um termo que se refere a um pedido de autenticação feito por:

- Clientes mais antigos do Office que não usam autenticação moderna (por exemplo, cliente do Office 2010)
- Qualquer cliente que utilize protocolos de correio antigo, tais como IMAP/SMTP/POP3

Hoje, a maioria de todas as tentativas comprometedoras de inscrição vêm da autenticação do legado. A autenticação do legado não suporta a autenticação de vários fatores (MFA). Mesmo que tenha uma política de MFA ativada no seu diretório, um mau ator pode autenticar usando um protocolo legado e contornar o MFA. A melhor forma de proteger a sua conta de pedidos de autenticação maliciosos feitos por protocolos legados é bloquear completamente estas tentativas.

## <a name="identify-legacy-authentication-use"></a>Identificar o uso da autenticação do legado

Antes de poder bloquear a autenticação do legado no seu diretório, tem de perceber primeiro se os seus utilizadores têm aplicações que utilizam a autenticação do legado e como isso afeta o seu diretório geral. Os registos de entrada de AD Azure podem ser usados para entender se está a usar a autenticação do legado.

1. Navegue para o **portal**   >  **Azure Ative Directory**   >  **Sign-ins**.
1. Adicione a coluna da **Aplicação cliente** se não for mostrada clicando na App do Cliente das **Colunas**   >  **Client App**.
1. Filtrar por **Aplicação cliente**> verificar todas as opções de Clientes de   **Autenticação Legado apresentadas.**
1. Filtrar **Status**por  >  **Status Success**. 
1. Aumente a sua gama de datas, se necessário, utilizando o filtro **Date.**

A filtragem só lhe mostrará tentativas de inscrição bem sucedidas que foram feitas pelos protocolos de autenticação do legado selecionados. Clicar em cada tentativa de inscrição individual irá mostrar-lhe detalhes adicionais. A coluna da Aplicação do Cliente ou o campo de Aplicações do Cliente sob o separador Informação Básica após a seleção de uma linha de dados individuais indicarão qual o protocolo de autenticação legado utilizado. Estes registos indicarão quais os utilizadores que ainda dependem da autenticação do legado e quais as aplicações que estão a utilizar protocolos legados para fazer pedidos de autenticação. Para os utilizadores que não aparecem nestes registos e que se confirme que não estão a utilizar a autenticação do legado, implementem uma política de Acesso Condicional ou permitam a política de Base: bloquear a autenticação do legado apenas para estes utilizadores.

## <a name="moving-away-from-legacy-authentication"></a>Afastando-se da autenticação do legado 

Uma vez que tenha uma ideia melhor de quem está a usar a autenticação do legado no seu diretório e quais as aplicações que dependem dele, o próximo passo é atualizar os seus utilizadores para usar a autenticação moderna. A autenticação moderna é um método de gestão de identidade que oferece autenticação e autorização mais seguras do utilizador. Se tiver uma política de MFA em vigor no seu diretório, a autenticação moderna garante que o utilizador é solicitado para MFA quando necessário. É a alternativa mais segura aos protocolos de autenticação do legado.

Esta secção dá uma visão geral passo a passo sobre como atualizar o seu ambiente à autenticação moderna. Leia os passos abaixo antes de permitir uma política de bloqueio de autenticação legado na sua organização.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Passo 1: Permitir a autenticação moderna no seu diretório

O primeiro passo para permitir a autenticação moderna é garantir que o seu diretório suporta a autenticação moderna. A autenticação moderna é ativada por padrão para diretórios criados em ou depois de 1 de agosto de 2017. Se o seu diretório foi criado antes desta data, terá de ativar manualmente a autenticação moderna para o seu diretório utilizando os seguintes passos:

1. Verifique se o seu diretório já suporta a autenticação moderna, funcionando  `Get-CsOAuthConfiguration`   a partir do Módulo Skype para Business [Online PowerShell](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Se o seu comando devolver uma  `OAuthServers`   propriedade vazia, então a Autenticação Moderna é desativada. Atualize a definição para permitir a autenticação moderna utilizando  `Set-CsOAuthConfiguration` . Se a sua  `OAuthServers`   propriedade contiver uma entrada, está pronto para ir.

Certifique-se de completar este passo antes de avançar. É fundamental que as configurações do seu diretório sejam alteradas primeiro porque ditam qual o protocolo que será usado por todos os clientes do Office. Mesmo que esteja a utilizar clientes do Office que suportam a autenticação moderna, eles não usarão protocolos antigos se a autenticação moderna for desativada no seu diretório.

### <a name="step-2-office-applications"></a>Passo 2: Candidaturas ao escritório

Uma vez que tenha ativado a autenticação moderna no seu diretório, pode começar a atualizar aplicações permitindo a autenticação moderna para clientes do Office. O Office 2016 ou posteriormente os clientes apoiam a autenticação moderna por defeito. Não são necessários passos extras.

Se estiver a utilizar clientes windows do Office 2013 ou mais velhos, recomendamos a atualização para o Office 2016 ou mais tarde. Mesmo depois de completar o passo anterior de permitir a autenticação moderna no seu diretório, as aplicações mais antigas do Office continuarão a utilizar protocolos de autenticação legado. Se estiver a utilizar clientes do Office 2013 e não conseguir fazer o upgrade imediato para o Office 2016 ou mais tarde, siga os passos no seguinte artigo para permitir a autenticação moderna para o [Office 2013 em dispositivos Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Para ajudar a proteger a sua conta enquanto utiliza a autenticação do legado, recomendamos a utilização de senhas fortes em todo o seu diretório. Consulte a proteção de [senhas azure AD](../authentication/concept-password-ban-bad.md)   para proibir senhas fracas em todo o seu diretório.

O Office 2010 não suporta a autenticação moderna. Terá de atualizar qualquer utilizador com o Office 2010 para uma versão mais recente do Office. Recomendamos a atualização para o Office 2016 ou mais tarde, uma vez que bloqueia a autenticação do legado por defeito.

Se estiver a utilizar o macOS, recomendamos a atualização para o Office para o Mac 2016 ou mais tarde. Se estiver a utilizar o cliente de correio nativo, terá de ter a versão 10.14 do macOS ou posteriormente em todos os dispositivos.

### <a name="step-3-exchange-and-sharepoint"></a>Passo 3: Troca e Ponto de Partilha

Para que os clientes do Outlook baseados no Windows utilizem a autenticação moderna, o Exchange Online também deve ser uma autenticação moderna. Se a autenticação moderna for desativada para o Exchange Online, os clientes do Outlook baseados no Windows que suportam a autenticação moderna (Outlook 2013 ou mais tarde) usarão a autenticação básica para ligar às caixas de correio Exchange Online.

O SharePoint Online está ativado para o padrão de autenticação moderna. Para os diretórios criados após 1 de agosto de 2017, a autenticação moderna é ativada por padrão no Exchange Online. No entanto, se já desativou a autenticação moderna ou se estiver a utilizar um diretório criado antes desta data, siga os passos no seguinte artigo para permitir a [autenticação moderna no Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Passo 4: Skype para negócios

Para evitar pedidos de autenticação legado sacados pelo Skype para o Negócios, é necessário permitir a autenticação moderna para o Skype para o Business Online. Para os diretórios criados após 1 de agosto de 2017, a autenticação moderna para skype para negócios é ativada por padrão.

Sugerimos a sua transição para as Equipas Microsoft, que suporta a autenticação moderna por padrão. No entanto, se não conseguir migrar neste momento, terá de ativar a autenticação moderna para o Skype para o Business Online para que os clientes skype para empresas comecem a usar a autenticação moderna. Siga os passos deste artigo [Skype para topoologias empresariais suportadas com Autenticação Moderna,](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)para permitir a Autenticação Moderna para skype para negócios.

Além de permitir a autenticação moderna para o Skype para o Business Online, recomendamos a autenticação moderna para o Exchange Online ao permitir a autenticação moderna para o Skype para negócios. Este processo ajudará a sincronizar o estado de autenticação moderna no Exchange Online e skype para negócios online e impedirá múltiplos pedidos de inscrição para skype para clientes empresariais.

### <a name="step-5-using-mobile-devices"></a>Passo 5: Utilização de dispositivos móveis

As aplicações no seu dispositivo móvel também precisam de bloquear a autenticação do legado. Recomendamos a utilização do Outlook for Mobile. As perspetivas para mobile suportam a autenticação moderna por defeito e irão satisfazer outras políticas de proteção de base do MFA.

Para utilizar o cliente de correio eletrónico nativo iOS, terá de executar a versão 11.0 do iOS ou posteriormente para garantir que o cliente de correio foi atualizado para bloquear a autenticação do legado.

### <a name="step-6-on-premises-clients"></a>Passo 6: Clientes no local

Se é um cliente híbrido que utiliza o Exchange Server no local e o Skype para negócios no local, ambos os serviços terão de ser atualizados para permitir a autenticação moderna. Ao utilizar a autenticação moderna num ambiente híbrido, ainda está a autenticar os utilizadores no local. A história de autorizar o seu acesso a recursos (ficheiros ou e-mails) muda.

Antes de começar a permitir a autenticação moderna no local, certifique-se de que cumpriu os requisitos prévios. Está pronto para permitir a autenticação moderna no local.

Os passos para permitir a autenticação moderna podem ser encontrados nos seguintes artigos:

* [Como configurar o Exchange Server no local para utilizar a autenticação moderna híbrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Como utilizar a Autenticação Moderna (ADAL) com o Skype para negócios](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Passos seguintes

- [Como configurar o Exchange Server no local para utilizar a autenticação moderna híbrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Como utilizar a Autenticação Moderna (ADAL) com o Skype para negócios](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Bloquear a autenticação legada](../conditional-access/block-legacy-authentication.md)
