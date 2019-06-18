---
title: O Azure Active Directory Identity Protection glossário | Documentos da Microsoft
description: Glossário do Azure Active Directory Identity Protection
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança, Glossário
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c371254f344b321969dcc9b3c36212b7536aa95a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109009"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário do Azure Active Directory Identity Protection
### <a name="at-risk-user"></a>Em risco (utilizador)
Um utilizador com um ou mais eventos de risco ativo. 

### <a name="atypical-sign-in-location"></a>Localização de início de sessão atípica
Um início de sessão a partir de uma localização geográfica que não é comum para o utilizador específico, os utilizadores semelhante ou o inquilino.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Um módulo de segurança do Azure Active Directory que fornece uma vista consolidada de eventos de risco e potenciais vulnerabilidades que afetam as identidades da organização.

### <a name="conditional-access"></a>Acesso Condicional
Uma política para proteger o acesso aos recursos. Regras de acesso condicional são armazenadas no Azure Active Directory e são avaliadas pelo Azure AD antes de conceder acesso ao recurso.  Regras de exemplo incluem restringir o acesso com base na localização do utilizador, o estado de funcionamento do dispositivo ou o método de autenticação de utilizador.

### <a name="credentials"></a>Credenciais
Informações de que inclui a identificação e uma prova de identificação que é utilizada para obter acesso ao local e recursos de rede. Exemplos de credenciais são os nomes de utilizador e palavras-passe, smart cards e certificados.

### <a name="event"></a>Evento
Um registo de uma atividade no Azure Active Directory.

### <a name="false-positive-risk-event"></a>FALSO-positivos (evento de risco)
Um Estado do evento de risco definir manualmente por um utilizador de proteção de identidade, indicando que o evento de risco foi investigou e incorretamente foi sinalizado como um evento de risco.

### <a name="identity"></a>identidade
Uma pessoa ou entidade que deve ser verificada por meio de autenticação, com base em critérios como palavra-passe ou um certificado.

### <a name="identity-risk-event"></a>Evento de risco de identidade
Evento AAD que foi sinalizado como anómala ao Identity Protection e isso poderá indicar que uma identidade foi comprometida.

### <a name="ignored-risk-event"></a>Ignorado (evento de risco)
Um Estado do evento de risco definir manualmente por um utilizador de proteção de identidade, que indica que o evento de risco é fechado sem realizar uma ação de remediação.

### <a name="impossible-travel-from-atypical-locations"></a>Deslocação impossível de localizações atípicas
Um evento de risco acionado quando são detetados dois inícios de sessão para o mesmo utilizador, em que pelo menos uma delas é a partir de uma localização de início de sessão atípica, e em que o tempo entre os inícios de sessão é menor do que o tempo mínimo que demoraria a fisicamente navegam entre estas localizações.  

### <a name="investigation"></a>Investigação
Compreender o processo de revisão das atividades, registos e outras informações relevantes relacionados com um evento de risco para decidir se os passos de remediação ou atenuação são necessários, se e como a identidade foi comprometida e compreender como o comprometido de identidade foi utilizado.

### <a name="leaked-credentials"></a>Fuga de credenciais
Um evento de risco acionado quando as credenciais do utilizador atual (nome de utilizador e palavra-passe) encontram-se publicados publicamente na web escuro, os nossos investigadores a.

### <a name="mitigation"></a>Mitigação
Uma ação para limitar ou elimine a capacidade de um atacante explore uma identidade comprometida ou dispositivo sem restaurar o dispositivo ou a identidade para um estado seguro. Uma atenuação não resolve eventos de risco anteriores associados com a identidade ou dispositivo.

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Um método de autenticação que requer dois ou mais métodos de autenticação, que podem incluir algo que o utilizador tiver, esse certificado; algo o utilizador sabe, como nomes de utilizador, palavras-passe ou frases secretas; atributos físicos, como um thumbprint; e atributos pessoais, como uma assinatura de pessoal.

### <a name="offline-detection"></a>Deteção offline
A deteção de anomalias e edição de avaliação do risco de um evento, como tentativa de início de sessão após o fato, um evento que já aconteceu.

### <a name="policy-condition"></a>Condição de política
Uma parte de uma política de segurança, que define as entidades (grupos, os utilizadores, aplicações, plataformas de dispositivos, Estados de dispositivo, intervalos de IP, tipos de cliente) incluídas na política ou excluídos do mesmo.

### <a name="policy-rule"></a>regra de política
A parte de uma política de segurança que descreve as circunstâncias que acionam a política e as ações executadas quando a política é acionada.

### <a name="prevention"></a>Prevenção
Uma ação para evitar danos à organização por meio de abuso de um dispositivo ou identidade suspeita ou saber para ser comprometido. Uma ação de prevenção não proteger a identidade de dispositivo ou e não resolve eventos de risco anterior.

### <a name="privileged-user"></a>Com privilégios (utilizador)
Um utilizador que, na altura de um evento de risco, teve permissões de administrador permanente ou temporário para um ou mais recursos no Azure Active Directory, tais como um Administrador Global, administrador de faturação, o administrador de serviço, o administrador de utilizador e palavra-passe de administrador. 

### <a name="real-time"></a>Em tempo real
Ver a deteção em tempo real.

### <a name="real-time-detection"></a>Deteção em tempo real
A deteção de anomalias e de avaliação do risco de um evento, como tentativa de início de sessão antes do evento tenha permissão para continuar.

### <a name="remediated-risk-event"></a>Remediado (evento de risco)
Um Estado do evento de risco definido automaticamente ao Identity Protection, que indica que o evento de risco foi remediado utilizando a ação de remediação padrão para este tipo de evento de risco. Por exemplo, quando a palavra-passe do utilizador é reposta, muitos eventos de risco que indicam que a palavra-passe anterior foi comprometida são remediados automaticamente.

### <a name="remediation"></a>Remediação
Uma ação para proteger uma identidade ou um dispositivo que foram anteriormente suspeita ou conhecido por ser comprometido. Uma ação de remediação restaura a identidade ou um dispositivo para um estado seguro e resolve anterior eventos de risco associados com a identidade ou dispositivo.

### <a name="resolved-risk-event"></a>Resolvido (evento de risco)
Um Estado do evento de risco definir manualmente por um utilizador de proteção de identidade, que indica que o utilizador demorou uma ação de remediação adequadas fora Identity Protection e que deve ser considerado o evento de risco fechado.

### <a name="risk-event-status"></a>Estado do evento de risco
Uma propriedade de um evento de risco, que indica se o evento está ativo e se fechada, o motivo para fechá-lo.

### <a name="risk-event-type"></a>Tipo de evento de risco
Uma categoria para o evento de risco, que indica o tipo de anomalias que causou o evento seja considerado arriscado.

### <a name="risk-level-risk-event"></a>Nível de risco (evento de risco)
Uma indicação (elevada, média ou baixa) da gravidade do evento de risco para o ajudar a priorizar as ações de usuários de proteção de identidade eles levam para reduzir o risco para a sua organização. 

### <a name="risk-level-sign-in"></a>Nível de risco (início de sessão)
Uma indicação (elevada, média ou baixa) da probabilidade de que um específico para início de sessão, alguém está a tentar utilizar a identidade do utilizador.

### <a name="risk-level-user-compromise"></a>Nível de risco (compromisso do utilizador)
Uma indicação (elevada, média ou baixa) da probabilidade de que uma identidade foi comprometida.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)
Uma indicação (elevada, média ou baixa) da gravidade da vulnerabilidade para ajudar a priorizar as ações de usuários de proteção de identidade eles levam para reduzir o risco para a sua organização.

### <a name="secure-identity"></a>Proteger (identidade)
Tome medidas de remediação, como uma alteração de palavra-passe ou a máquina para restaurar uma identidade potencialmente comprometida para um Estado de funcionamento não comprometido a recriar a imagem.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras de política e a condição. Uma política pode ser aplicada a entidades, como os utilizadores, grupos, aplicações, dispositivos, plataformas de dispositivos, Estados de dispositivo, intervalos de IP e tipos de cliente Auth2.0. Quando uma política é ativada, esta é avaliada sempre que uma entidade incluída na política é emitida um token para um recurso.

### <a name="sign-in-v"></a>Iniciar sessão (v)
Para autenticar-se para uma identidade no Azure Active Directory.

### <a name="sign-in-n"></a>Início de sessão (n)
O processo ou a ação de autenticar uma identidade no Azure Active Directory e o evento que esta operação de captura.

### <a name="sign-in-from-anonymous-ip-address"></a>Iniciar sessão a partir do endereço IP anónimo
Um evento de risco é acionado após um êxito inícios de sessão de endereço IP que foi identificado como um endereço IP de proxy anónimo.

### <a name="sign-in-from-infected-device"></a>Iniciar sessão a partir de dispositivos infetados
Um evento de risco acionado quando um início de sessão tem origem de um endereço IP, o que é conhecido por ser utilizado por um ou mais dispositivos comprometidos, que estão a tentar ativamente para comunicar com um servidor de bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Iniciar sessão a partir do endereço IP com atividade suspeita
Um evento de risco acionada após um início de sessão-in concluída com êxito de um IP endereços com um elevado número de tentativas de início de sessão em várias contas de utilizador durante um curto período de tempo.

### <a name="sign-in-from-unfamiliar-location"></a>Iniciar sessão a partir da localização desconhecida
Um evento de risco acionado quando um utilizador com êxito inicia sessão a partir de uma nova localização (IP, Latitude/Longitude e ASN).

### <a name="sign-in-risk"></a>Início de sessão de risco
Consulte o risco nível (início de sessão)

### <a name="sign-in-risk-policy"></a>Política de risco de início de sessão
Uma política de acesso condicional que avalia o risco para um início de sessão-in específico e aplica-se mitigações com base em regras e condições predefinidas.

### <a name="user-compromise-risk"></a>Risco de comprometimento de utilizador
Consulte o risco nível (compromisso do utilizador)

### <a name="user-risk"></a>Risco de utilizador
Consulte o risco nível (compromisso do utilizador).

### <a name="user-risk-policy"></a>Política de risco do utilizador
Uma política de acesso condicional que considera o início de sessão e aplica-se mitigações com base em regras e condições predefinidas.

### <a name="users-flagged-for-risk"></a>Utilizadores marcados em risco
Utilizadores que tenham eventos de risco, que são o Active Directory ou remediadas

### <a name="vulnerability"></a>Vulnerabilidade
Uma configuração ou a condição no Azure Active Directory, o que torna o diretório suscetível a explorações ou de ameaças.

## <a name="see-also"></a>Consulte também
* [O Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

