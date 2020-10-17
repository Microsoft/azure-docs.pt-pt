---
title: A autenticação do legado do bloco - Azure Ative Directory
description: Aprenda a melhorar a sua postura de segurança bloqueando a autenticação do legado utilizando o Acesso Condicional Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c3107be1c36f1c15a1bcb27c5e0dcf851cfb946
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145543"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como: Bloquear a autenticação do legado para Azure AD com acesso condicional   

Para facilitar o acesso dos seus utilizadores às suas aplicações na nuvem, o Azure Ative Directory (Azure AD) suporta uma grande variedade de protocolos de autenticação, incluindo a autenticação antiga. No entanto, os protocolos legados não suportam a autenticação de vários fatores (MFA). MFA é em muitos ambientes um requisito comum para abordar o roubo de identidade. 

Alex Weinert, Diretor de Segurança de Identidade da Microsoft, no seu post de 12 de março de 2020 [Novas ferramentas para bloquear a autenticação de legados na sua organização](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) enfatizam porque é que as organizações devem bloquear a autenticação de legados e que ferramentas adicionais a Microsoft fornece para realizar esta tarefa:

> Para que o MFA seja eficaz, também é necessário bloquear a autenticação do legado. Isto porque protocolos de autenticação de legados como POP, SMTP, IMAP e MAPI não podem impor MFA, tornando-os pontos de entrada preferidos para adversários que atacam a sua organização...
> 
>... Os números sobre a autenticação de legados a partir de uma análise do tráfego do Azure Ative Directory (Azure AD) são gritantes:
> 
> - Mais de 99% dos ataques de spray de senha usam protocolos de autenticação legado
> - Mais de 97% dos ataques de recheio credenciais usam autenticação antiga
> - Contas AZURE AD em organizações que desativaram a autenticação de legados 67 por cento menos compromissos do que aqueles onde a autenticação do legado está ativada
>

Se o seu ambiente estiver pronto para bloquear a autenticação do legado para melhorar a proteção do seu inquilino, pode atingir este objetivo com acesso condicional. Este artigo explica como pode configurar políticas de Acesso Condicional que bloqueiam a autenticação do legado para o seu inquilino.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que está familiarizado com os [conceitos básicos](overview.md) de Acesso Condicional Azure AD.

## <a name="scenario-description"></a>Descrição do cenário

A Azure AD suporta vários dos protocolos de autenticação e autorização mais utilizados, incluindo a autenticação do legado. A autenticação do legado refere-se a protocolos que utilizam a autenticação básica. Normalmente, estes protocolos não podem impor qualquer tipo de autenticação de segundo fator. Exemplos para apps baseadas na autenticação do legado são:

- Aplicativos mais antigos do Microsoft Office
- Aplicativos que usam protocolos de correio como POP, IMAP e SMTP

A autenticação de um único fator (por exemplo, nome de utilizador e palavra-passe) não é suficiente nos dias de hoje. As palavras-passe são más porque são fáceis de adivinhar e nós (humanos) somos maus na escolha de boas senhas. As palavras-passe também são vulneráveis a uma variedade de ataques como phishing e spray de senha. Uma das coisas mais fáceis que pode fazer para proteger contra ameaças de senha é implementar a autenticação de vários fatores (MFA). Com o MFA, mesmo que um intruso obtenha na posse da senha de um utilizador, a palavra-passe por si só não é suficiente para autenticar e aceder com sucesso aos dados.

Como evitar que as aplicações que utilizam a autenticação antiga acedam aos recursos do seu inquilino? A recomendação é apenas bloqueá-los com uma política de acesso condicional. Se necessário, permite que certos utilizadores e localizações específicas da rede utilizem aplicações baseadas na autenticação antiga.

As políticas de acesso condicional são aplicadas após a autenticação do primeiro fator ter sido concluída. Portanto, o Acesso Condicional não se destina a ser uma defesa de primeira linha para cenários como ataques de negação de serviço (DoS), mas pode utilizar sinais destes eventos (por exemplo, o nível de risco de entrada, localização do pedido, e assim por diante) para determinar o acesso.

## <a name="implementation"></a>Implementação

Esta secção explica como configurar uma política de Acesso Condicional para bloquear a autenticação do legado. 

### <a name="legacy-authentication-protocols"></a>Protocolos de autenticação de legados

As seguintes opções são consideradas protocolos de autenticação de legados

- SMTP autenticado - Usado pelos clientes POP e IMAP para enviar mensagens de correio eletrónico.
- Autodiscover - Usado pelos clientes Outlook e EAS para encontrar e ligar às caixas de correio em Exchange Online.
- Exchange ActiveSync (EAS) - Usado para ligar a caixas de correio em Exchange Online.
- Exchange Online PowerShell - Usado para ligar-se a Exchange Online com powerShell remoto. Se bloquear a autenticação básica para Exchange Online PowerShell, tem de utilizar o Módulo Exchange Online PowerShell para se ligar. Para obter instruções, consulte [Connect to Exchange Online PowerShell utilizando a autenticação de vários fatores.](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
- Exchange Web Services (EWS) - Uma interface de programação que é usada pelo Outlook, Outlook for Mac e aplicações de terceiros.
- IMAP4 - Usado por clientes de e-mail IMAP.
- MAPI over HTTP (MAPI/HTTP) - Usado pelo Outlook 2010 e posteriormente.
- Offline Address Book (OAB) - Uma cópia das coleções de listas de endereços que são descarregadas e usadas pelo Outlook.
- Outlook Anywhere (RPC over HTTP) - Usado pelo Outlook 2016 e mais cedo.
- Serviço Outlook - Usado pela aplicação De Correio e Calendário para o Windows 10.
- POP3 - Usado por clientes de e-mail POP.
- Reportar Serviços Web - Usado para recuperar dados de relatório em Exchange Online.
- Outros clientes - Outros protocolos identificados como utilizando a autenticação do legado.

Para obter mais informações sobre estes protocolos e serviços de autenticação, consulte [os relatórios de atividades de inscrição no portal Azure Ative Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identificar o uso da autenticação de legados

Antes de bloquear a autenticação do legado no seu diretório, tem de primeiro compreender se os seus utilizadores têm aplicações que utilizam a autenticação antiga e como isso afeta o seu diretório geral. Os registos de login AD do Azure podem ser utilizados para entender se está a utilizar a autenticação antiga.

1. Navegue para o **portal**  >  **Azure Ative Directory**  >  **Ins**.
1. Adicione a coluna 'App' cliente se não for mostrada clicando na App do Cliente **das Colunas.**  >  **Client App**
1. **Adicionar filtros**  >  **A App do Cliente** > selecione todos os protocolos de autenticação do legado. Selecione fora da caixa de diálogo de filtragem para aplicar as suas seleções e feche a caixa de diálogo.

A filtragem só lhe mostrará as tentativas de inscrição que foram feitas por protocolos de autenticação de legados. Clicar em cada tentativa de inscrição individual irá mostrar-lhe detalhes adicionais. O campo **de Aplicação do Cliente** no separador **Informações Básicas** indicará qual o protocolo de autenticação legado utilizado.

Estes registos indicam quais os utilizadores que ainda estão dependentes da autenticação antiga e quais as aplicações que estão a utilizar protocolos legados para fazer pedidos de autenticação. Para os utilizadores que não aparecem nestes registos e que se confirmam não estarem a utilizar a autenticação antiga, implemente uma política de Acesso Condicional apenas para estes utilizadores.

## <a name="block-legacy-authentication"></a>Bloquear a autenticação legada 

Existem duas formas de usar políticas de acesso condicional para bloquear a autenticação do legado.

- [Bloqueando diretamente a autenticação do legado](#directly-blocking-legacy-authentication)
- [Bloqueando indiretamente a autenticação do legado](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Bloqueando diretamente a autenticação do legado

A forma mais fácil de bloquear a autenticação de legados em toda a sua organização é configurar uma política de Acesso Condicional que se aplica especificamente aos clientes de autenticação antiga e bloqueia o acesso. Ao atribuir utilizadores e aplicações à apólice, certifique-se de excluir os utilizadores e contas de serviço que ainda precisam de iniciar sessão com a autenticação do legado. Configure a condição de aplicações de cliente selecionando **clientes Exchange ActiveSync** e **Outros clientes.** Para bloquear o acesso a estas aplicações de clientes, configurar os controlos de acesso ao acesso ao Bloco.

![Aplicações de clientes condicionam configurado para bloquear legado auth](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Bloqueando indiretamente a autenticação do legado

Mesmo que a sua organização não esteja pronta para bloquear a autenticação de legados em toda a organização, deve garantir que os sign-ins que usam a autenticação antiga não estão a contornar as políticas que requerem controlos de concessão, tais como a necessidade de autenticação multi-factor ou dispositivos de ad Azure adúlde/híbrido. Durante a autenticação, os clientes de autenticação legado não suportam o envio de MFA, conformidade do dispositivo ou aderir a informações do Estado para a Azure AD. Por isso, aplique políticas com controlos de concessão a todas as aplicações do cliente para que as inscrições baseadas na autenticação legado que não satisfaçam os controlos da concessão sejam bloqueadas. Com a disponibilidade geral das aplicações do cliente em agosto de 2020, as políticas de Acesso Condicional recentemente criadas aplicam-se a todas as aplicações do cliente por padrão.

![Configuração padrão de condição de aplicativos de cliente](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>O que deve saber

Bloquear o acesso usando **Outros clientes** também bloqueia o Exchange Online PowerShell e o Dynamics 365 usando auth básico.

Configurar uma política para **outros clientes** bloqueia toda a organização de certos clientes como o SPConnect. Este bloco acontece porque os clientes mais velhos autenticam de formas inesperadas. A questão não se aplica a grandes aplicações do Office, como os clientes mais antigos do Office.

Pode levar até 24 horas para a apólice entrar em vigor.

Pode selecionar todos os controlos de subvenção disponíveis para a condição de **Outros clientes;** no entanto, a experiência do utilizador final é sempre a mesma - acesso bloqueado.

## <a name="next-steps"></a>Passos seguintes

- [Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)
- Se ainda não está familiarizado com a configuração das políticas de Acesso Condicional, consulte [o MFA para aplicações específicas com acesso condicional do Azure Ative Directory,](../authentication/tutorial-enable-azure-mfa.md) por exemplo.
- Para obter mais informações sobre suporte de autenticação moderna, consulte [como funciona a autenticação moderna para as aplicações de clientes do Office 2013 e office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [Como configurar um dispositivo ou aplicação multifunções para enviar e-mail usando o Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
