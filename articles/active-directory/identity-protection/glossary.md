---
title: Glossário de Azure Active Directory Identity Protection | Microsoft Docs
description: Glossário de Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6751fe74bfd9b8a07aec2263582d9f1a4bc0b2ac
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333980"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário de Azure Active Directory Identity Protection

### <a name="at-risk-user"></a>Em risco (usuário)
Um usuário com um ou mais eventos de risco ativos. 

### <a name="atypical-sign-in-location"></a>Local de entrada do atípicos
Uma entrada de uma localização geográfica que não é típica para o usuário específico, usuários semelhantes ou o locatário.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Um módulo de segurança do Azure Active Directory que fornece uma exibição consolidada dos eventos de risco e possíveis vulnerabilidades que afetam as identidades de uma organização.

### <a name="conditional-access"></a>Acesso Condicional
Uma política para proteger o acesso aos recursos. As regras de acesso condicional são armazenadas no Azure Active Directory e são avaliadas pelo Azure AD antes de conceder acesso ao recurso.  As regras de exemplo incluem a restrição do acesso com base no local do usuário, na integridade do dispositivo ou no método de autenticação do usuário.

### <a name="credentials"></a>Credenciais
Informações que incluem identificação e prova de identificação que são usadas para obter acesso a recursos locais e de rede. Exemplos de credenciais são nomes de usuário e senhas, cartões inteligentes e certificados.

### <a name="event"></a>Evento
Um registro de uma atividade no Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falso-positivo (evento de risco)
Um status de evento de risco definido manualmente por um usuário de proteção de identidade, indicando que o evento de risco foi investigado e foi sinalizado incorretamente como um evento de risco.

### <a name="identity"></a>identidade
Uma pessoa ou entidade que deve ser verificada por meio de autenticação, com base em critérios como senha ou um certificado.

### <a name="identity-risk-event"></a>Evento de risco de identidade
Evento do AAD que foi sinalizado como anômala pela proteção de identidade e pode indicar que uma identidade foi comprometida.

### <a name="ignored-risk-event"></a>Ignorado (evento de risco)
Um status de evento de risco definido manualmente por um usuário de proteção de identidade, indicando que o evento de risco está fechado sem realizar uma ação de correção.

### <a name="impossible-travel-from-atypical-locations"></a>Viagem impossível de locais atípicoss
Um evento de risco disparado quando duas entradas para o mesmo usuário são detectadas, em que pelo menos uma delas é de um local de entrada do atípicos e onde o tempo entre as entradas é menor do que o tempo mínimo necessário para viajar fisicamente entre esses locais.  

### <a name="investigation"></a>Investigação
O processo de revisar as atividades, os logs e outras informações relevantes relacionadas a um evento de risco para decidir se as etapas de correção ou mitigação são necessárias, entender se e como a identidade foi comprometida e entender como a identidade comprometida foi usado.

### <a name="leaked-credentials"></a>Fuga de credenciais
Um evento de risco disparado quando as credenciais do usuário atual (nome de usuário e senha) são encontradas publicamente na Web escura por nossos pesquisadores.

### <a name="mitigation"></a>Mitigação
Uma ação para limitar ou eliminar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometido sem restaurar a identidade ou o dispositivo para um estado seguro. Uma mitigação não resolve eventos de risco anteriores associados à identidade ou ao dispositivo.

### <a name="multi-factor-authentication"></a>Autenticação multifator
Um método de autenticação que requer dois ou mais métodos de autenticação, que podem incluir algo que o usuário tem, tal certificado; algo que o usuário sabe, como nomes de usuário, senhas ou frases secretas; atributos físicos, como uma impressão digital; e atributos pessoais, como uma assinatura pessoal.

### <a name="offline-detection"></a>Detecção offline
A detecção de anomalias e a avaliação do risco de um evento, como a tentativa de entrada após o fato, para um evento que já aconteceu.

### <a name="policy-condition"></a>Condição da política
Uma parte de uma política de segurança, que define as entidades (grupos, usuários, aplicativos, plataformas de dispositivo, Estados de dispositivo, intervalos de IP, tipos de cliente) incluídos na política ou excluídos dela.

### <a name="policy-rule"></a>regra de política
A parte de uma política de segurança que descreve as circunstâncias que dispararia a política e as ações tomadas quando a política é disparada.

### <a name="prevention"></a>Prevenção
Uma ação para evitar danos à organização por meio do abuso de uma identidade ou um dispositivo suspeito ou de saber que está comprometido. Uma ação de prevenção não protege o dispositivo ou a identidade e não resolve os eventos de risco anteriores.

### <a name="privileged-user"></a>Com privilégios (usuário)
Um usuário que, no momento de um evento de risco, tinha permissões de administrador permanentes ou temporárias para um ou mais recursos no Azure Active Directory, como um administrador global, administrador de cobrança, administrador de serviços, administrador de usuários e administrador de senha. 

### <a name="real-time"></a>Em tempo real
Consulte detecção em tempo real.

### <a name="real-time-detection"></a>Detecção em tempo real
A detecção de anomalias e a avaliação do risco de um evento, como a tentativa de entrada, antes que o evento possa continuar.

### <a name="remediated-risk-event"></a>Corrigido (evento de risco)
Um status de evento de risco definido automaticamente pela proteção de identidade, indicando que o evento de risco foi corrigido usando a ação de correção padrão para esse tipo de evento de risco. Por exemplo, quando a senha do usuário é redefinida, muitos eventos de risco que indicam que a senha anterior foi comprometida são automaticamente corrigidos.

### <a name="remediation"></a>Remediação
Uma ação para proteger uma identidade ou um dispositivo que foi suspeito ou comprometido anteriormente. Uma ação de correção restaura a identidade ou o dispositivo para um estado seguro e resolve os eventos de risco anteriores associados à identidade ou ao dispositivo.

### <a name="resolved-risk-event"></a>Resolvido (evento de risco)
Um status de evento de risco definido manualmente por um usuário de proteção de identidade, indicando que o usuário realizou uma ação de correção apropriada fora da proteção de identidade e que o evento de risco deve ser considerado fechado.

### <a name="risk-event-status"></a>Status do evento de risco
Uma propriedade de um evento de risco, que indica se o evento está ativo e, se fechado, o motivo para fechá-lo.

### <a name="risk-event-type"></a>Tipo de evento de risco
Uma categoria para o evento de risco, que indica o tipo de anomalia que fez com que o evento fosse considerado arriscado.

### <a name="risk-level-risk-event"></a>Nível de risco (evento de risco)
Uma indicação (alta, média ou baixa) da severidade do evento de risco para ajudar os usuários de proteção de identidade a priorizar as ações que eles levam para reduzir o risco para sua organização. 

### <a name="risk-level-sign-in"></a>Nível de risco (entrada)
Uma indicação (alta, média ou baixa) da probabilidade de uma entrada específica, outra pessoa está tentando usar a identidade do usuário.

### <a name="risk-level-user-compromise"></a>Nível de risco (comprometimento do usuário)
Uma indicação (alta, média ou baixa) da probabilidade de que uma identidade tenha sido comprometida.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)
Uma indicação (alta, média ou baixa) da severidade da vulnerabilidade para ajudar os usuários de proteção de identidade a priorizar as ações que eles levam para reduzir o risco para sua organização.

### <a name="secure-identity"></a>Seguro (identidade)
Faça uma ação de correção como uma alteração de senha ou recriação de imagem de computador para restaurar uma identidade potencialmente comprometida para um estado descomprometido.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras e condições de política. Uma política pode ser aplicada a entidades como usuários, grupos, aplicativos, dispositivos, plataformas de dispositivo, Estados de dispositivo, intervalos de IP e tipos de cliente auth 2.0. Quando uma política é habilitada, ela é avaliada sempre que uma entidade incluída na política recebe um token para um recurso.

### <a name="sign-in-v"></a>Entrar (v)
Para autenticar em uma identidade no Azure Active Directory.

### <a name="sign-in-n"></a>Entrada (n)
O processo ou a ação de autenticar uma identidade no Azure Active Directory e o evento que captura essa operação.

### <a name="sign-in-from-anonymous-ip-address"></a>Entrar de um endereço IP anônimo
Um evento de risco disparado após uma entrada bem-sucedida do endereço IP que foi identificado como um endereço IP de proxy anônimo.

### <a name="sign-in-from-infected-device"></a>Entrar do dispositivo infectado
Um evento de risco disparado quando uma entrada se origina de um endereço IP, que é conhecido por ser usado por um ou mais dispositivos comprometidos, que estão tentando ativamente se comunicar com um servidor de bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Entrar do endereço IP com atividade suspeita
Um evento de risco disparado após uma entrada bem-sucedida de um endereço IP com um número alto de tentativas de logon com falha em várias contas de usuário em um curto período de tempo.

### <a name="sign-in-from-unfamiliar-location"></a>Entrar de um local desconhecido
Um evento de risco disparado quando um usuário entra com êxito de um novo local (IP, latitude/longitude e ASN).

### <a name="sign-in-risk"></a>Risco de início de sessão
Ver o nível de risco (entrar)

### <a name="sign-in-risk-policy"></a>Política de risco de início de sessão
Uma política de acesso condicional que avalia o risco para uma entrada específica e aplica mitigações com base em condições e regras predefinidas.

### <a name="user-compromise-risk"></a>Risco de comprometimento do usuário
Ver o nível de risco (comprometimento do usuário)

### <a name="user-risk"></a>Risco de utilizador
Consulte nível de risco (comprometimento do usuário).

### <a name="user-risk-policy"></a>Política de risco de utilizador
Uma política de acesso condicional que considera a entrada e aplica mitigações com base em condições e regras predefinidas.

### <a name="users-flagged-for-risk"></a>Utilizadores marcados em risco
Usuários que têm eventos de risco, que são ativos ou corrigidos

### <a name="vulnerability"></a>Vulnerabilidade
Uma configuração ou condição no Azure Active Directory, o que torna o diretório suscetível a explorações ou ameaças.

## <a name="see-also"></a>Consulte também
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
