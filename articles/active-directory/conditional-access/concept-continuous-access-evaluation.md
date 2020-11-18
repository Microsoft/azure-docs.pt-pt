---
title: Avaliação contínua de acesso em Azure AD
description: Responder às alterações no estado do utilizador mais rapidamente com avaliação contínua de acesso em Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: daa3f3b1687b01005f32cbd2665c84b933c993b8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837622"
---
# <a name="continuous-access-evaluation"></a>Avaliação contínua de acesso

A expiração e a atualização de token são um mecanismo padrão na indústria. Quando uma aplicação de clientes como o Outlook se conecta a um serviço como o Exchange Online, os pedidos da API são autorizados a usar fichas de acesso OAuth 2.0. Por padrão, esses tokens de acesso são válidos por uma hora, quando expiram, o cliente é redirecionado de volta para Azure AD para refrescê-los. Esse período de atualização proporciona uma oportunidade para reavaliar políticas de acesso ao utilizador. Por exemplo: podemos optar por não atualizar o token por causa de uma política de Acesso Condicional, ou porque o utilizador foi desativado no diretório. 

Os clientes têm manifestado preocupação com o desfasamento entre quando as condições mudam para o utilizador, como a localização da rede ou roubo de credenciais, e quando as políticas podem ser aplicadas relacionadas com essa mudança. Experimentámos a abordagem "objeto contundente" de vidas reduzidas, mas descobrimos que podem degradar as experiências e a fiabilidade dos utilizadores sem eliminar os riscos.

Uma resposta oportuna a violações de políticas ou questões de segurança requer realmente uma "conversa" entre o emitente simbólico, como a Azure AD, e a parte que conta, como o Exchange Online. Esta conversa bidirecionais dá-nos duas capacidades importantes. A parte que conta pode notar quando as coisas mudaram, como um cliente vindo de um novo local, e dizer ao emitente simbólico. Também dá ao emitente token uma forma de dizer à parte que conta para parar de respeitar as fichas para um determinado utilizador devido a compromisso de conta, desativação ou outras preocupações. O mecanismo para esta conversação é a avaliação contínua do acesso (CAE). O objetivo é que a resposta seja quase em tempo real, mas em alguns casos a latência de até 15 minutos pode ser observada devido ao tempo de propagação do evento.

A implementação inicial da avaliação de acesso contínuo centra-se no Exchange, Teams e SharePoint Online. 

### <a name="key-benefits"></a>Principais vantagens

- A rescisão do utilizador ou a alteração/reset da palavra-passe: A revogação da sessão do utilizador será executada em tempo real.
- Alteração da localização da rede: As políticas de localização de acesso condicional serão aplicadas em tempo real.
- A exportação de token para uma máquina fora de uma rede de confiança pode ser impedida com políticas de localização de acesso condicional.

## <a name="scenarios"></a>Cenários 

Existem dois cenários que compõem a avaliação contínua do acesso, a avaliação crítica de eventos e a avaliação da política de acesso condicional.

### <a name="critical-event-evaluation"></a>Avaliação crítica de eventos

A avaliação contínua do acesso é implementada através da capacitação de serviços, como Exchange Online, SharePoint Online e Teams, para subscrever eventos críticos em AZure AD para que esses eventos possam ser avaliados e aplicados perto do tempo real. A avaliação crítica do evento não depende das políticas de Acesso Condicional, pelo que está disponível em qualquer inquilino. Os seguintes eventos são atualmente avaliados:

- A Conta de Utilizador é eliminada ou desativada
- A palavra-passe para um utilizador é alterada ou reiniciada
- A autenticação de vários fatores está ativada para o utilizador
- Administrador revoga explicitamente todos os tokens de atualização para um utilizador
- Risco elevado de utilizador detetado pela Azure AD Identity Protection

Este processo permite o cenário em que os utilizadores perdem acesso a ficheiros organizacionais sharePoint Online, e-mail, calendário ou tarefas, e equipas de aplicações de clientes microsoft 365 dentro de mins após um desses eventos críticos. 

### <a name="conditional-access-policy-evaluation-preview"></a>Avaliação da política de acesso condicional (pré-visualização)

O Exchange e o SharePoint são capazes de sincronizar as principais políticas de Acesso Condicional para que possam ser avaliadas dentro do próprio serviço.

Este processo permite o cenário em que os utilizadores perdem acesso a ficheiros organizacionais, e-mail, calendário ou tarefas a partir de aplicações de clientes microsoft 365 ou SharePoint Online imediatamente após alterações na localização da rede.

> [!NOTE]
> Nem todas as combinações de aplicativos e fornecedores de recursos são suportadas. Veja a tabela abaixo. Office refere-se a Word, Excel e PowerPoint

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Suportado | Suportado | Não suportado | Não suportado | Suportado |
| **Exchange Online** | Suportado | Suportado | Suportado | Suportado | Suportado |

| | Aplicativos web de escritório | Aplicativos Win32 do Office Win32 | Escritório para iOS | Escritório para Android | Escritório para Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Não suportado | Suportado | Suportado | Suportado | Suportado |
| **Exchange Online** | Não suportado | Suportado | Suportado | Suportado | Suportado |

### <a name="client-side-claim-challenge"></a>Desafio de reclamação do lado do cliente

Antes da avaliação contínua do acesso, os clientes tentavam sempre reproduzir o token de acesso a partir da sua cache, desde que não expirasse. Com a CAE, estamos a introduzir um novo caso que um fornecedor de recursos pode rejeitar um símbolo mesmo quando não está expirado. A fim de informar os clientes para contornarem a sua cache, mesmo que os tokens em cache não tenham expirado, introduzimos um mecanismo chamado **desafio de reivindicação** para indicar que o token foi rejeitado e que um novo token de acesso deve ser emitido pela Azure AD. O CAE requer uma atualização do cliente para compreender o desafio da reclamação. A versão mais recente das seguintes aplicações abaixo do desafio de reivindicação de suporte:

- Outlook Windows
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Equipas para Windows (apenas para recursos de equipas)
- Equipas iOS (Apenas para recursos de equipas)
- Equipas Android (Apenas para recursos de Equipas)
- Equipas Mac (Apenas para recursos de equipas)
- Word/Excel/PowerPoint para Windows
- Word/Excel/PowerPoint para iOS
- Word/Excel/PowerPoint para Android
- Palavra/Excel/PowerPoint para Mac

### <a name="token-lifetime"></a>Vida útil simbólica

Como o risco e a política são avaliados em tempo real, os clientes que negoceiam sessões de avaliação contínua de acessos conscientes dependerão da CAE em vez das políticas de vida de acesso estático existentes, o que significa que a política de vida de token configurável não será mais honrada para clientes capazes de CAE que negoceiam sessões conscientes do CAE.

A vida útil simbólica é aumentada para ser vivida longamente, até 28 horas, em sessões cae. A revogação é impulsionada por acontecimentos críticos e avaliação política, e não apenas por um período de tempo arbitrário. Esta alteração aumenta a estabilidade das aplicações sem afetar a postura de segurança. 

Se não estiver a utilizar clientes com capacidade CAE, o seu prazo de vida de acesso por defeito permanecerá 1 hora a menos que tenha configurado o seu tempo de vida útil com a funcionalidade de pré-visualização [Configurable Token Lifetime (CTL).](../develop/active-directory-configurable-token-lifetimes.md)

## <a name="example-flows"></a>Fluxos de exemplo

### <a name="user-revocation-event-flow"></a>Fluxo de evento de revogação do utilizador:

![Fluxo de evento de revogação do utilizador](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Um cliente com capacidade cae apresenta credenciais ou um token de atualização para Azure AD pedindo um sinal de acesso para algum recurso.
1. Um sinal de acesso é devolvido juntamente com outros artefactos ao cliente.
1. Um administrador revoga explicitamente [todos os tokens de atualização para o utilizador](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Será enviado um evento de revogação ao fornecedor de recursos da Azure AD.
1. Um sinal de acesso é apresentado ao fornecedor de recursos. O fornecedor de recursos avalia a validade do token e verifica se existe algum evento de revogação para o utilizador. O fornecedor de recursos utiliza esta informação para decidir conceder ou não acesso ao recurso.
1. Neste caso, o fornecedor de recursos nega o acesso e envia um desafio de reclamação de 401+ para o cliente.
1. O cliente capaz de CAE compreende o desafio de reclamação de 401+. Contorna os caches e volta ao passo 1, enviando o seu token de atualização juntamente com o desafio de reivindicação de volta para Azure AD. O Azure AD reavaliará todas as condições e solicitará ao utilizador que reautore neste caso.

### <a name="user-condition-change-flow-preview"></a>Fluxo de alteração da condição do utilizador (pré-visualização):

No exemplo seguinte, um administrador de acesso condicional configura uma política de acesso condicional baseada na localização para permitir apenas o acesso a partir de intervalos IP específicos:

![Fluxo de evento de condição do utilizador](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Um cliente com capacidade cae apresenta credenciais ou um token de atualização para Azure AD pedindo um sinal de acesso para algum recurso.
1. A Azure AD avalia todas as políticas de Acesso Condicional para ver se o utilizador e o cliente cumprem as condições.
1. Um sinal de acesso é devolvido juntamente com outros artefactos ao cliente.
1. O utilizador sai de uma gama de IP permitida
1. O cliente apresenta um sinal de acesso ao fornecedor de recursos de fora de uma gama de IP permitida.
1. O fornecedor de recursos avalia a validade do token e verifica a política de localização sincronizada a partir da Azure AD.
1. Neste caso, o fornecedor de recursos nega o acesso e envia um desafio de reclamação de 401+ para o cliente porque não vem da gama IP permitida.
1. O cliente capaz de CAE compreende o desafio de reclamação de 401+. Contorna os caches e volta ao passo 1, enviando o seu token de atualização juntamente com o desafio de reivindicação de volta para Azure AD. A AZure AD reavalia todas as condições e negará o acesso neste caso.

## <a name="enable-or-disable-cae-preview"></a>Ativar ou desativar CAE (Pré-visualização)

1. Inscreva-se no **portal Azure** como administrador de acesso condicional, administrador de segurança ou administrador global
1. Consulte a avaliação contínua de acesso contínuo **do Diretório Azure**  >  **Security**  >  **Continuous access evaluation** Ative.
1. Escolha **Ativar a pré-visualização**.

A partir desta página, pode opcionalmente limitar os utilizadores e grupos que estarão sujeitos à pré-visualização.

![Habilitar a pré-visualização do CAE no portal Azure](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="supported-location-policies"></a>Políticas de localização apoiadas

Para o CAE, só temos informações sobre locais nomeados com base em IP. Não temos informações sobre outras definições de localização, como [IPs fidedignos de MFA](../authentication/howto-mfa-mfasettings.md#trusted-ips) ou localizações baseadas em país. Quando o utilizador vem de um IP fidedigno da MFA ou de localizações fidedignas que incluem IPs fidedignos MFA ou localização do país, o CAE não será aplicado após a mudança do utilizador para um local diferente. Nesses casos, emitiremos um token CAE de 1 hora sem verificação instantânea da aplicação da legislação em matéria de IP.

> [!IMPORTANT]
> Ao configurar locais para avaliação contínua de acesso, utilize apenas a [condição de localização de acesso condicional baseada em IP](../conditional-access/location-condition.md#preview-features) e configure todos os endereços IP, **incluindo o IPv4 e o IPv6,** que podem ser vistos pelo seu fornecedor de identidade e fornecedor de recursos. Não utilize condições de localização do país ou a funcionalidade ips fidedigna que esteja disponível na página de serviços de autenticação multi-factor Azure AD.

### <a name="ip-address-configuration"></a>Configuração do endereço IP

O seu fornecedor de identidade e fornecedores de recursos podem ver diferentes endereços IP. Esta incompatibilidade pode ocorrer devido a implementações de procuração de rede na sua organização ou configurações incorretas do IPv4/IPv6 entre o seu fornecedor de identidade e fornecedor de recursos. Por exemplo:

- O seu fornecedor de identidade vê um endereço IP do cliente.
- O seu fornecedor de recursos vê um endereço IP diferente do cliente depois de passar por um representante.
- O endereço IP que o seu fornecedor de identidade vê faz parte de uma gama de IP permitida na política, mas o endereço IP do fornecedor de recursos não é.

Se este cenário existir no seu ambiente para evitar loops infinitos, o Azure AD emitirá um token CAE de uma hora e não imporá a mudança de localização do cliente. Mesmo neste caso, a segurança é melhorada em comparação com os tradicionais tokens de uma hora, uma vez que ainda estamos a avaliar os [outros eventos](#critical-event-evaluation) além de eventos de mudança de localização do cliente.

### <a name="office-and-web-account-manager-settings"></a>Definições de Gestor de Escritórios e Contas Web

| Canal de atualização de escritório | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Canal Semi-Annual Enterprise | Se estiver programado para ativar ou 1, o CAE não deve ser suportado. | Se estiver programado para ativar ou 1, o CAE não deve ser suportado. |
| Canal atual <br> ou <br> Canal Mensal da Empresa | CAE é suportado independentemente da configuração | CAE é suportado independentemente da configuração |

Para obter uma explicação dos canais de atualização do office, consulte [a visão geral dos canais de atualização para as aplicações da Microsoft 365](/deployoffice/overview-update-channels). Recomenda-se que as organizações não desativem o Gestor de Conta Web (WAM).

### <a name="policy-change-timing"></a>Tempo de mudança de política

Devido ao potencial de atraso de replicação entre a Azure AD e os fornecedores de recursos, as alterações de política efetuadas pelos administradores podem demorar até 2 horas a ser eficazes para o Exchange Online.

Exemplo: O administrador adiciona uma política para bloquear uma série de endereços IP a partir do acesso ao e-mail às 11:00, um utilizador que tenha vindo dessa gama ip antes poderia possivelmente continuar a aceder ao e-mail até às 13:00.

### <a name="coauthoring-in-office-apps"></a>Coautoria em aplicativos office

Quando vários utilizadores estão a colaborar no mesmo documento ao mesmo tempo, o acesso do utilizador ao documento não pode ser imediatamente revogado pela CAE com base em eventos de revogação ou alteração de políticas. Neste caso, o utilizador perde o acesso completamente depois de fechar o documento, fechar Word, Excel ou PowerPoint, ou após um período de 10 horas.

Para reduzir desta vez, um Administrador SharePoint pode opcionalmente reduzir o tempo máximo de duração das sessões de coautoria de documentos armazenados no SharePoint Online e no OneDrive for Business, configurando uma política de [localização de rede no SharePoint Online.](/sharepoint/control-access-based-on-network-location) Uma vez alterada esta configuração, o tempo máximo de duração das sessões de coautoria será reduzido para 15 minutos, podendo ser ajustado ainda mais utilizando o comando SharePoint Online PowerShell "Set-SPOTenant –IPAddressWACTokenLifetime"

### <a name="enable-after-a-user-is-disabled"></a>Ativar depois de um utilizador ser desativado

Se ativar um utilizador logo após o desativar. Haverá alguma latência antes de a conta poder ser ativada. SPO e Equipas terão 15 minutos de atraso. O atraso é de 35-40 minutos para o EXO.

## <a name="faqs"></a>FAQs

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Como é que o CAE vai funcionar com a frequência de inscrição?

A frequência de inscrição será honrada com ou sem CAE.

## <a name="next-steps"></a>Passos seguintes

[Anunciando a avaliação contínua do acesso](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)