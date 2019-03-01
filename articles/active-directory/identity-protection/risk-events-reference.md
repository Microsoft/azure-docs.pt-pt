---
title: Referência de eventos de risco do Azure Active Directory Identity Protection | Documentos da Microsoft
description: Referência de eventos de risco do Azure Active Directory Identity Protection.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1c69705131cfea4e5ace9b5b9e829b3fdfa87e4
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991653"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Referência de eventos de risco do Azure Active Directory Identity Protection

A grande maioria das falhas de segurança ocorrem quando os atacantes obtêm acesso a um ambiente, por roubo de identidade de um utilizador. Detetar identidades comprometidas não é tarefa fácil. O Azure Active Directory utiliza algoritmos de aprendizagem automática adaptáveis e heurística para detetar as ações suspeitas relacionadas com as contas de utilizador. Cada detetado, ação suspeita é armazenada num evento de risco de chamada de registo.


## <a name="anonymous-ip-address"></a>Endereços IP anónimos

**Tipo de Deteção:** Em tempo real  
**Nome antigo:** Inícios de sessão de endereço IP anónimo


Esse tipo de evento de risco indica inícios de sessão de um endereço IP anónimo (por exemplo, browser de Tor, anonymizer VPNs).
Estes endereços IP são normalmente utilizados por atores que pretendem ocultar sua telemetria de início de sessão (endereço IP, localização, dispositivo, etc.) para potencialmente mal-intencionados.


## <a name="atypical-travel"></a>Percurso atípico

**Tipo de Deteção:** Offline  
**Nome antigo:** Deslocação impossível para localizações atípicas


Este tipo de evento de risco identifica dois inícios de sessão provenientes de localizações distantes, em que, pelo menos, uma das localizações pode também ser atípica para o utilizador, tendo em conta comportamento pretérito. Entre vários outros fatores, esse algoritmo de machine learning leva em conta o tempo entre os dois inícios de sessão e o tempo que seria necessário para que o usuário viajam de primeira localização para o segundo, que indica que um utilizador diferente está a utilizar o mesmo credenciais.

O algoritmo ignora "positivos falsos óbvios" que contribuem para as condições de deslocação impossível, como as VPNs e localizações regularmente utilizadas por outros utilizadores na organização. O sistema tem um período de aprendizagem inicial de quanto mais antiga de 14 dias ou 10 inícios de sessão, durante o qual ele aprende o comportamento de início de sessão de um novo utilizador.


## <a name="leaked-credentials"></a>Fuga de credenciais

**Tipo de Deteção:** Offline  
**Nome antigo:** Utilizadores com fuga de credenciais


Este tipo de evento de risco indica que as credenciais do usuário válido tem sofreu vazamento.
Quando cybercriminals comprometer válidas palavras-passe de utilizadores legítimos, os criminosos partilham, muitas vezes, essas credenciais. Isto é geralmente feito através da publicação destacada-los publicamente nos sites web ou colar escuros ou por comerciais ou as credenciais no mercado negro de vendas. A Microsoft fuga de credenciais do serviço adquire o nome de utilizador / palavra-passe pares ao monitorizar web sites públicos e escuro e ao trabalhar com:

- Investigadores

- Aplicação da lei

- Equipes de segurança da Microsoft

- Outras origens confiáveis

Quando o serviço de adquire as credenciais de utilizador da dark web, sites de colar ou as origens de acima, eles são verificados relativamente credenciais válidas atual dos utilizadores do Azure AD para localizar correspondências válidas.


## <a name="malware-linked-ip-address"></a>Endereço IP ligado a software maligno

**Tipo de Deteção:** Offline  
**Nome antigo:** Inícios de sessão de dispositivos infetados


Esse tipo de evento de risco indica inícios de sessão de endereços IP infetados com software maligno que conhece ativamente a comunicar com um servidor de bot. Tal é determinado ao correlacionar endereços IP do dispositivo do utilizador em relação a endereços IP que estavam em contacto com um servidor de bot, enquanto o servidor de bot ter estado ativo.


## <a name="unfamiliar-sign-in-properties"></a>Propriedades de início de sessão não familiar

**Tipo de Deteção:** Em tempo real  
**Nome antigo:** Inícios de sessão de localizações desconhecidas

Este tipo de evento de risco considera anteriores histórico de início de sessão (IP, Latitude / Longitude e o ASN) para procurar por inícios de sessão anómalos. O sistema armazena informações sobre localizações anteriores, utilizado por um utilizador e considera esses locais "familiares". O evento de risco é acionado quando o início de sessão ocorre a partir de uma localização que não ainda esteja na lista de localizações familiares. Criado recentemente os utilizadores serão no "modo de aprendizagem" durante um período de tempo em que propriedades de início de sessão não familiares eventos de risco serão desativados enquanto os nossos algoritmos saiba o comportamento do utilizador. A aprendizagem é dinâmica, duração de modo e depende em quanto tempo demora o algoritmo para recolher informações suficientes sobre os padrões de início de sessão do utilizador. A duração mínima é de cinco dias. Um utilizador pode voltar atrás no modo de aprendizagem após um longo período de inatividade. O sistema também ignora os inícios de sessão de dispositivos familiares e localizações geograficamente próximas uma localização familiar. 

Executamos também esta deteção para a autenticação básica (ou protocolos herdados). Uma vez que estes protocolos não tem propriedades modernos, como o ID de cliente, não existe telemetria limitada para reduzir os falsos positivos. Recomendamos que os nossos clientes para mover para a autenticação moderna.

