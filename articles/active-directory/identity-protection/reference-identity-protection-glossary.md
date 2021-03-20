---
title: Glossário de Proteção de Identidade Azure Ative
description: Glossário de Proteção de Identidade Azure Ative
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
ms.openlocfilehash: 1a17f52e519a84c58c9ed5b75259cc0b96d0a175
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88949179"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário de Proteção de Identidade Azure Ative

### <a name="at-risk-user"></a>Em risco (Utilizador)
Um utilizador com uma ou mais deteções de risco ativas. 

### <a name="atypical-sign-in-location"></a>Local de início de sípico atípico
Uma sposição a partir de uma localização geográfica que não é típica para o utilizador específico, utilizadores semelhantes ou para o inquilino.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Um módulo de segurança do Azure Ative Directory que fornece uma visão consolidada sobre deteções de riscos e potenciais vulnerabilidades que afetam as identidades de uma organização.

### <a name="conditional-access"></a>Acesso Condicional
Uma política para garantir o acesso aos recursos. As regras de acesso condicional são armazenadas no Diretório Ativo Azure e são avaliadas pela Azure AD antes de conceder acesso ao recurso.  As regras de exemplo incluem restringir o acesso com base na localização do utilizador, na saúde do dispositivo ou no método de autenticação do utilizador.

### <a name="credentials"></a>Credenciais
Informação que inclui identificação e comprovativo de identificação que é utilizado para ter acesso aos recursos locais e de rede. Exemplos de credenciais são nomes de utilizadores e senhas, cartões inteligentes e certificados.

### <a name="event"></a>Evento
Um registo de uma atividade no Azure Ative Directory.

### <a name="false-positive-risk-detection"></a>Falso positivo (deteção de riscos)
Um estado de deteção de risco definido manualmente por um utilizador da Proteção de Identidade, indicando que a deteção de risco foi investigada e foi incorretamente sinalizada como uma deteção de risco.

### <a name="identity"></a>Identidade
Uma pessoa ou entidade que deve ser verificada através da autenticação, com base em critérios como palavra-passe ou certificado.

### <a name="identity-risk-detection"></a>Deteção de risco de identidade
Evento AD AZure que foi sinalizado como anómalo pela Proteção de Identidade, e pode indicar que uma identidade foi comprometida.

### <a name="ignored-risk-detection"></a>Ignorado (deteção de riscos)
Um estado de deteção de risco definido manualmente por um utilizador da Proteção de Identidade, indicando que a deteção de risco está fechada sem tomar medidas de reparação.

### <a name="impossible-travel-from-atypical-locations"></a>Viagens impossíveis de locais atípicos
Uma deteção de risco desencadeada quando são detetados dois sinais de inscrição para o mesmo utilizador, em que pelo menos um deles é proveniente de um local de sinalização atípico, e onde o tempo entre as entradas é mais curto do que o tempo mínimo necessário para viajar fisicamente entre estes locais.  

### <a name="investigation"></a>Investigação
O processo de revisão das atividades, registos e outras informações relevantes relacionadas com uma deteção de risco para decidir se são necessárias medidas de reparação ou mitigação, compreender se e como a identidade foi comprometida, e compreender como a identidade comprometida foi utilizada.

### <a name="leaked-credentials"></a>Fuga de credenciais
Uma deteção de risco desencadeada quando as credenciais de utilizador atuais (nome de utilizador e palavra-passe) são encontradas publicamente na web Dark pelos nossos investigadores.

### <a name="mitigation"></a>Mitigação
Uma ação para limitar ou eliminar a capacidade de um intruso explorar uma identidade ou dispositivo comprometido sem restaurar a identidade ou o dispositivo para um estado seguro. Uma mitigação não resolve as deteções de risco anteriores associadas à identidade ou dispositivo.

### <a name="multi-factor-authentication"></a>Autenticação multifator
Um método de autenticação que exija dois ou mais métodos de autenticação, que podem incluir algo que o utilizador tem, tal certificado; algo que o utilizador saiba, como nomes de utilizador, palavras-passe ou frases de passe; atributos físicos, como uma impressão digital; e atributos pessoais, como uma assinatura pessoal.

### <a name="offline-detection"></a>Deteção offline
A deteção de anomalias e a avaliação do risco de um evento como a tentativa de inscrição após o facto, para um evento que já aconteceu.

### <a name="policy-condition"></a>Condição política
Parte de uma política de segurança, que define as entidades (grupos, utilizadores, apps, plataformas de dispositivos, Estados de Dispositivos, gamas IP, tipos de clientes) incluídas na política ou excluídas da sua política.

### <a name="policy-rule"></a>Regra política
A parte de uma política de segurança que descreve as circunstâncias que desencadeariam a política e as ações tomadas quando a política é desencadeada.

### <a name="prevention"></a>Prevenção
Uma ação para prevenir danos à organização através de abuso de identidade ou dispositivo suspeito ou sabe estar comprometido. Uma ação de prevenção não protege o dispositivo ou a identidade e não resolve as deteções de risco anteriores.

### <a name="privileged-user"></a>Privilegiado (utilizador)
Um utilizador que, no momento de uma deteção de risco, tinha permissões de administração permanentes ou temporárias para um ou mais recursos no Azure Ative Directory, tais como um Administrador Global, Administrador de Faturação, Administrador de Serviço, Administrador de Utilizador e Administrador de Password. 

### <a name="real-time"></a>Em tempo real
Ver deteção em tempo real.

### <a name="real-time-detection"></a>Deteção em tempo real
A deteção de anomalias e a avaliação do risco de um evento como a tentativa de inscrição antes de o evento ser autorizado a prosseguir.

### <a name="remediated-risk-detection"></a>Remediado (deteção de riscos)
Um estado de deteção de risco definido automaticamente pela Proteção de Identidade, indicando que a deteção de risco foi remediada utilizando a ação de reparação padrão para este tipo de deteção de risco. Por exemplo, quando a palavra-passe do utilizador é reposta, muitas deteções de risco que indicam que a palavra-passe anterior foi comprometida são automaticamente remediadas.

### <a name="remediation"></a>Remediação
Uma ação para garantir uma identidade ou um dispositivo que já era suspeito ou conhecido por estar comprometido. Uma ação de reparação restaura a identidade ou o dispositivo num estado seguro e resolve as deteções de risco anteriores associadas à identidade ou dispositivo.

### <a name="resolved-risk-detection"></a>Resolvido (deteção de riscos)
Um estado de deteção de risco definido manualmente por um utilizador da Proteção de Identidade, indicando que o utilizador tomou uma ação de reparação adequada fora da Proteção de Identidade, e que a deteção de risco deve ser considerada fechada.

### <a name="risk-detection-status"></a>Estado de deteção de risco
Uma propriedade de uma deteção de risco, indicando se o evento está ativo, e se fechado, o motivo para fechá-lo.

### <a name="risk-detection-type"></a>Tipo de deteção de risco
Uma categoria para a deteção de risco, indicando o tipo de anomalia que fez com que o evento fosse considerado arriscado.

### <a name="risk-level-risk-detection"></a>Nível de risco (deteção de riscos)
Uma indicação (Alta, Média ou Baixa) da gravidade da deteção de riscos para ajudar os utilizadores da Proteção de Identidade a priorizar as ações que tomam para reduzir o risco para a sua organização. 

### <a name="risk-level-sign-in"></a>Nível de risco (iniciar s-in)
Uma indicação (Alta, Média ou Baixa) da probabilidade de que, para uma sing-in específica, outra pessoa esteja a tentar usar a identidade do utilizador.

### <a name="risk-level-user-compromise"></a>Nível de risco (compromisso do utilizador)
Uma indicação (Alta, Média ou Baixa) da probabilidade de uma identidade ter sido comprometida.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)
Uma indicação (Alta, Média ou Baixa) da gravidade da vulnerabilidade para ajudar os utilizadores da Proteção de Identidade a priorizar as ações que tomam para reduzir o risco para a sua organização.

### <a name="secure-identity"></a>Seguro (identidade)
Tome medidas de reparação como uma alteração de palavra-passe ou reimaging de máquina para restaurar uma identidade potencialmente comprometida para um estado não comprometido.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras e condições políticas. Uma política pode ser aplicada a entidades como utilizadores, grupos, apps, dispositivos, plataformas de dispositivos, estados de dispositivos, gamas IP e tipos de clientes Auth2.0. Quando uma política está ativada, é avaliada sempre que uma entidade incluída na política é emitida um símbolo para um recurso.

### <a name="sign-in-v"></a>Iniciar s inscrição (v)
Para autenticar uma identidade no Diretório Ativo Azure.

### <a name="sign-in-n"></a>S-in (n)
O processo ou ação de autenticação de uma identidade no Diretório Ativo Azure, e o evento que captura esta operação.

### <a name="sign-in-from-anonymous-ip-address"></a>Inscreva-se a partir do endereço IP anónimo
Uma deteção de risco desencadeada após uma sinção bem sucedida do endereço IP que foi identificada como um endereço IP de procuração anónimo.

### <a name="sign-in-from-infected-device"></a>Inscreva-se no dispositivo infetado
Uma deteção de risco desencadeada quando uma insinálise provém de um endereço IP, que é conhecido por ser usado por um ou mais dispositivos comprometidos, que estão ativamente a tentar comunicar com um servidor bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Inscreva-se no endereço IP com atividade suspeita
Uma deteção de risco desencadeada após uma sessão bem sucedida a partir de um endereço IP com um elevado número de tentativas de login falhadas em várias contas de utilizador durante um curto período de tempo.

### <a name="sign-in-from-unfamiliar-location"></a>Inscreva-se a partir de localização desconhecida
Uma deteção de risco desencadeada quando um utilizador assina com sucesso a partir de um novo local (IP, Latitude/Longitude e ASN).

### <a name="sign-in-risk"></a>Risco de início de sessão
Ver nível de risco (iniciar s-in)

### <a name="sign-in-risk-policy"></a>Política de risco do início de sessão
Uma política de acesso condicional que avalia o risco a uma determinada inscrição e aplica mitigações com base em condições e regras predefinidas.

### <a name="user-compromise-risk"></a>Risco de compromisso do utilizador
Ver nível de risco (compromisso do utilizador)

### <a name="user-risk"></a>Risco de utilizador
Ver nível de risco (compromisso do utilizador).

### <a name="user-risk-policy"></a>Política de risco do utilizador
Uma política de Acesso Condicional que considere a inscrição e aplica mitigações com base em condições e regras predefinidas.

### <a name="users-flagged-for-risk"></a>Utilizadores marcados em risco
Utilizadores que têm deteções de risco, que são ativas ou remediadas

### <a name="vulnerability"></a>Vulnerabilidade
Uma configuração ou condição no Azure Ative Directory, o que torna o diretório suscetível a explorações ou ameaças.

## <a name="see-also"></a>Ver também

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)