---
title: Glossário de Proteção de Identidade do Diretório Ativo Azure
description: Glossário de Proteção de Identidade do Diretório Ativo Azure
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74232349"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário de Proteção de Identidade do Diretório Ativo Azure

### <a name="at-risk-user"></a>Em risco (Utilizador)
Um utilizador com uma ou mais deteções de risco ativas. 

### <a name="atypical-sign-in-location"></a>Localização de inscrição atípica
Um sessão de inscrição a partir de uma localização geográfica que não é típica para o utilizador específico, utilizadores similares ou o inquilino.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Um módulo de segurança do Azure Ative Directory que proporciona uma visão consolidada sobre deteções de risco e potenciais vulnerabilidades que afetam as identidades de uma organização.

### <a name="conditional-access"></a>Acesso Condicional
Uma política para garantir o acesso aos recursos. As regras de Acesso Condicional são armazenadas no Diretório Ativo Azure e são avaliadas pela Azure AD antes de conceder acesso ao recurso.  As regras exemplo incluem restringir o acesso com base na localização do utilizador, saúde do dispositivo ou método de autenticação do utilizador.

### <a name="credentials"></a>Credenciais
Informação que inclui identificação e prova de identificação que é usada para aceder aos recursos locais e de rede. Exemplos de credenciais são nomes de utilizadores e senhas, cartões inteligentes e certificados.

### <a name="event"></a>Evento
Um registo de uma atividade no Diretório Ativo Azure.

### <a name="false-positive-risk-detection"></a>Falso positivo (deteção de risco)
Um estado de deteção de risco definido manualmente por um utilizador de Proteção de Identidade, indicando que a deteção de risco foi investigada e foi incorretamente sinalizada como uma deteção de risco.

### <a name="identity"></a>Identidade
Uma pessoa ou entidade que deve ser verificada através de autenticação, com base em critérios como senha ou certificado.

### <a name="identity-risk-detection"></a>Deteção de risco de identidade
Evento da AD Azure que foi sinalizado como anómalo pela Proteção de Identidade, e pode indicar que uma identidade foi comprometida.

### <a name="ignored-risk-detection"></a>Ignorado (deteção de risco)
Um estado de deteção de risco definido manualmente por um utilizador de Proteção de Identidade, indicando que a deteção de risco é encerrada sem tomar uma medida de reparação.

### <a name="impossible-travel-from-atypical-locations"></a>Viagem impossível de locais atípicos
Uma deteção de risco desencadeada quando são detetados dois insins para o mesmo utilizador, em que pelo menos um deles é proveniente de um local de inscrição atípico, e onde o tempo entre os sign-ins é menor do que o tempo mínimo que levaria para viajar fisicamente entre estes locais.  

### <a name="investigation"></a>Investigação
O processo de revisão das atividades, registos e outras informações relevantes relacionadas com uma deteção de risco para decidir se são necessárias medidas de reparação ou mitigação, compreender se e como a identidade foi comprometida, e entender como a identidade comprometida foi usada.

### <a name="leaked-credentials"></a>Credenciais vazadas
Uma deteção de risco desencadeada quando as credenciais atuais do utilizador (nome de utilizador e palavra-passe) são encontradas publicamente na web Dark pelos nossos investigadores.

### <a name="mitigation"></a>Mitigação
Uma ação para limitar ou eliminar a capacidade de um intruso explorar uma identidade ou dispositivo comprometido sem restaurar a identidade ou dispositivo para um estado seguro. Uma mitigação não resolve deteções de risco anteriores associadas à identidade ou dispositivo.

### <a name="multi-factor-authentication"></a>Multi-factor authentication
Um método de autenticação que requer dois ou mais métodos de autenticação, que podem incluir algo que o utilizador tem, tal certificado; algo que o utilizador sabe, como nomes de utilizador, palavras-passe ou frases de passe; atributos físicos, como uma impressão digital; e atributos pessoais, como uma assinatura pessoal.

### <a name="offline-detection"></a>Deteção offline
A deteção de anomalias e a avaliação do risco de um evento como a tentativa de inscrição após o facto, para um evento que já aconteceu.

### <a name="policy-condition"></a>Condição política
Uma parte de uma política de segurança, que define as entidades (grupos, utilizadores, apps, plataformas de dispositivos, estados de dispositivos, gamas IP, tipos de clientes) incluídas na política ou excluídas da mesmas.

### <a name="policy-rule"></a>Regra política
A parte de uma política de segurança que descreve as circunstâncias que desencadeariam a política e as ações tomadas quando a política é desencadeada.

### <a name="prevention"></a>Prevenção
Uma ação para prevenir danos à organização através de abuso de identidade ou dispositivo suspeito ou conhecido por ser comprometido. Uma ação de prevenção não protege o dispositivo ou a identidade e não resolve deteções de risco anteriores.

### <a name="privileged-user"></a>Privilegiado (utilizador)
Um utilizador que, no momento de uma deteção de risco, tivesse permissões administrativas permanentes ou temporárias a um ou mais recursos no Diretório Ativo do Azure, tais como administrador global, administrador de faturação, administrador de serviço, administrador de utilizador e administrador de passwords. 

### <a name="real-time"></a>Tempo real
Ver deteção em tempo real.

### <a name="real-time-detection"></a>Deteção em tempo real
A deteção de anomalias e a avaliação do risco de um evento como a tentativa de inscrição antes do evento é permitido prosseguir.

### <a name="remediated-risk-detection"></a>Remediado (deteção de risco)
Um estado de deteção de risco definido automaticamente pela Proteção de Identidade, indicando que a deteção de risco foi remediada utilizando a ação padrão de reparação para este tipo de deteção de risco. Por exemplo, quando a palavra-passe do utilizador é reposta, muitas deteções de risco que indicam que a senha anterior foi comprometida são automaticamente remediadas.

### <a name="remediation"></a>Remediação
Uma ação para garantir uma identidade ou um dispositivo que antes se suspeitava ou se soubesse que estava comprometida. Uma ação de reparação restaura a identidade ou dispositivo num estado seguro e resolve deteções de risco anteriores associadas à identidade ou dispositivo.

### <a name="resolved-risk-detection"></a>Resolvido (deteção de risco)
Um estado de deteção de risco definido manualmente por um utilizador de Proteção de Identidade, indicando que o utilizador tomou uma ação adequada de reparação fora da Proteção de Identidade, e que a deteção de risco deve ser considerada encerrada.

### <a name="risk-detection-status"></a>Estado de deteção de risco
Uma propriedade de uma deteção de risco, indicando se o evento está ativo, e se fechado, a razão para o fechar.

### <a name="risk-detection-type"></a>Tipo de deteção de risco
Uma categoria para a deteção de riscos, indicando o tipo de anomalia que fez com que o evento fosse considerado arriscado.

### <a name="risk-level-risk-detection"></a>Nível de risco (deteção de risco)
Uma indicação (Alta, Média ou Baixa) da gravidade da deteção de risco para ajudar os utilizadores de Proteção de Identidade a priorizar as ações que tomam para reduzir o risco para a sua organização. 

### <a name="risk-level-sign-in"></a>Nível de risco (inscreveu-se)
Uma indicação (Alta, Média ou Baixa) da probabilidade de que para um sinal específico, alguém esteja a tentar usar a identidade do utilizador.

### <a name="risk-level-user-compromise"></a>Nível de risco (compromisso do utilizador)
Uma indicação (alta, média ou baixa) da probabilidade de uma identidade ter sido comprometida.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)
Uma indicação (Alta, Média ou Baixa) da gravidade da vulnerabilidade para ajudar os utilizadores de Proteção de Identidade a priorizar as ações que tomam para reduzir o risco para a sua organização.

### <a name="secure-identity"></a>Seguro (identidade)
Tome medidas de reparação, tais como uma alteração de senha ou reimaging de máquinas para restaurar uma identidade potencialmente comprometida para um estado descomprometido.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras e condições políticas. Uma política pode ser aplicada a entidades como utilizadores, grupos, apps, dispositivos, plataformas de dispositivos, estados de dispositivos, gamas IP e tipos de clientes Auth2.0. Quando uma política é ativada, é avaliada sempre que uma entidade incluída na política é emitida um símbolo para um recurso.

### <a name="sign-in-v"></a>Iniciar sessão (v)
Autenticar uma identidade no Diretório Ativo Azure.

### <a name="sign-in-n"></a>Iniciar sessão (n)
O processo ou ação de autenticação de uma identidade no Diretório Ativo Azure, e o evento que captura esta operação.

### <a name="sign-in-from-anonymous-ip-address"></a>Inscreva-se no endereço IP anónimo
Uma deteção de risco desencadeada após um sessão de acesso bem sucedido a partir do endereço IP que foi identificado como um endereço IP de procuração anónimo.

### <a name="sign-in-from-infected-device"></a>Inscreva-se no dispositivo infetado
Uma deteção de risco desencadeada quando um sinal de entrada tem origem num endereço IP, que se sabe ser utilizado por um ou mais dispositivos comprometidos, que estão a tentar comunicar ativamente com um servidor bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Inscreva-se no endereço IP com atividade suspeita
Uma deteção de risco desencadeada após um início de sessão bem sucedido a partir de um endereço IP com um elevado número de tentativas de login falhadas em várias contas de utilizador durante um curto período de tempo.

### <a name="sign-in-from-unfamiliar-location"></a>Inscreva-se em local desconhecido
Uma deteção de risco desencadeada quando um utilizador entra com sucesso a partir de um novo local (IP, Latitude/Longitude e ASN).

### <a name="sign-in-risk"></a>Risco de inscrição
Ver Nível de Risco (iniciar sessão)

### <a name="sign-in-risk-policy"></a>Política de risco de inscrição
Uma política de acesso condicional que avalia o risco a um sign-in específico e aplica atenuações com base em condições e regras predefinidas.

### <a name="user-compromise-risk"></a>Risco de compromisso do utilizador
Ver nível de risco (compromisso do utilizador)

### <a name="user-risk"></a>Risco do utilizador
Ver nível de risco (compromisso do utilizador).

### <a name="user-risk-policy"></a>Política de risco do utilizador
Uma política de acesso condicional que considere o sign-in e aplique atenuações com base em condições e regras predefinidas.

### <a name="users-flagged-for-risk"></a>Utilizadores marcados em risco
Utilizadores que tenham detetações de risco, que são ativas ou remediadas

### <a name="vulnerability"></a>Vulnerabilidade
Uma configuração ou condição no Diretório Ativo Azure, o que torna o diretório suscetível a explorações ou ameaças.

## <a name="see-also"></a>Consulte também

- [Proteção de Identidade do Diretório Ativo Azure](../active-directory-identityprotection.md)
