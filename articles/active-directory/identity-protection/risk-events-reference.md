---
title: Referência de eventos de risco do Azure Active Directory Identity Protection | Microsoft Docs
description: Referência de eventos de risco do Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d2247aab872a71f250bd0b4b52714e402d2102d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905201"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Referência de eventos de risco do Azure Active Directory Identity Protection

A grande maioria das violações de segurança ocorre quando os invasores têm acesso a um ambiente, roubando a identidade de um usuário. Descobrir identidades comprometidas não é uma tarefa fácil. O Azure Active Directory usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar ações suspeitas relacionadas às suas contas de usuário. Cada ação suspeita detectada é armazenada em um registro chamado evento de risco.

## <a name="anonymous-ip-address"></a>Endereços IP anónimos

**Tipo de detecção:** Tempo real  
**Nome antigo:** Entradas de endereço IP anônimo

Esse tipo de evento de risco indica entradas de um endereço IP anônimo (por exemplo, Tor browser, Anonymizer VPNs).
Esses endereços IP normalmente são usados por atores que desejam ocultar sua telemetria de logon (endereço IP, local, dispositivo, etc.) para uma intenção potencialmente mal-intencionada.

## <a name="atypical-travel"></a>Percurso atípico

**Tipo de detecção:** Offline  
**Nome antigo:** Deslocação impossível para localizações atípicas

Esse tipo de evento de risco identifica duas entradas provenientes de locais geograficamente distantes, em que pelo menos um dos locais também pode ser atípicos para o usuário, dado o comportamento passado. Entre vários outros fatores, esse algoritmo de aprendizado de máquina leva em conta o tempo entre as duas entradas e o tempo que seria levado para o usuário viajar do primeiro local para o segundo, indicando que um usuário diferente está usando o mesmo fornecidas.

O algoritmo ignora os "falsos positivos" óbvios que contribuem para as condições de viagem impossíveis, como VPNs e locais usados regularmente por outros usuários na organização. O sistema tem um período de aprendizado inicial do anterior de 14 dias ou 10 logons, durante o qual ele aprende o comportamento de entrada de um novo usuário.

## <a name="leaked-credentials"></a>Credenciais vazadas

**Tipo de detecção:** Offline  
**Nome antigo:** Utilizadores com fuga de credenciais

Esse tipo de evento de risco indica que as credenciais válidas do usuário foram vazadas.
Quando cibercriminosos comprometer senhas válidas de usuários legítimos, os criminosos geralmente compartilham essas credenciais. Normalmente, isso é feito postando-os publicamente na Web escura ou colando sites ou negociando ou vendendo as credenciais no mercado preto. O serviço de credenciais vazadas da Microsoft adquire os pares de nome de usuário/senha monitorando sites públicos e escuros e trabalhando com:

- Investigadores
- Aplicação de leis
- Equipes de segurança na Microsoft
- Outras fontes confiáveis

Quando o serviço adquire credenciais de usuário da Web escura, colar sites ou as fontes acima, eles são verificados em relação às credenciais válidas atuais dos usuários do Azure AD para encontrar correspondências válidas.

## <a name="malware-linked-ip-address"></a>Endereço IP ligado a software maligno

**Tipo de detecção:** Offline  
**Nome antigo:** Inícios de sessão de dispositivos infetados

Esse tipo de evento de risco indica entradas de endereços IP infectados com malware que é conhecido por se comunicar ativamente com um servidor de bot. Isso é determinado pela correlação de endereços IP do dispositivo do usuário em endereços IP que estavam em contato com um servidor de bot enquanto o servidor de bot estava ativo.

## <a name="unfamiliar-sign-in-properties"></a>Propriedades de entrada desconhecidas

**Tipo de detecção:** Tempo real  
**Nome antigo:** Inícios de sessão de localizações desconhecidas

Esse tipo de evento de risco considera o histórico de entrada anterior (IP, latitude/longitude e ASN) para procurar por entradas anormais. O sistema armazena informações sobre os locais anteriores usados por um usuário e considera esses locais "familiares". O evento de risco é disparado quando a entrada ocorre de um local que ainda não está na lista de locais familiares. Os usuários recém-criados estarão no "modo de aprendizagem" por um período de tempo em que as propriedades de entrada desconhecidas eventos de risco serão desativadas enquanto nossos algoritmos aprendem o comportamento do usuário. A duração do modo de aprendizado é dinâmica e depende de quanto tempo leva o algoritmo para reunir informações suficientes sobre os padrões de entrada do usuário. A duração mínima é de cinco dias. Um usuário pode voltar para o modo de aprendizado após um longo período de inatividade. O sistema também ignora entradas de dispositivos familiares e locais que estão geograficamente próximos a um local familiar. 

Também executamos essa detecção para autenticação básica (ou protocolos herdados). Como esses protocolos não têm propriedades modernas, como ID do cliente, há telemetria limitada para reduzir falsos positivos. Recomendamos que nossos clientes migrem para a autenticação moderna.

## <a name="azure-ad-threat-intelligence"></a>Inteligência contra ameaças do Azure AD

**Tipo de detecção:** Offline <br>
**Nome antigo:** Essa detecção será mostrada nos relatórios de Azure AD Identity Protection herdados (usuários sinalizados para risco, eventos de risco) como ' usuários com credenciais vazadas '

Esse tipo de evento de risco indica a atividade do usuário que é incomum para o usuário determinado ou é consistente com padrões de ataque conhecidos com base nas fontes de inteligência contra ameaças internas e externas da Microsoft.

## <a name="admin-confirmed-user-compromised"></a>Administrador confirmou utilizador comprometido

**Tipo de detecção:** Offline <br>
Essa detecção indica que um administrador selecionou ' confirmar o usuário comprometido ' na interface do usuário de usuários arriscados ou usando a API riskyUsers. Para ver qual administrador confirmou que esse usuário está comprometido, verifique o histórico de risco do usuário (por meio da interface de usuário ou API).
