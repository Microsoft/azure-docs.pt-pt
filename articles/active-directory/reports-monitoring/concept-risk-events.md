---
title: Eventos de risco do Azure Active Directory | Documentos da Microsoft
description: Este artice dá-lhe uma visão geral detalhada dos eventos de risco.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02fc505c8f14f4cc0e486502060a16af47c68bbc
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439027"
---
# <a name="azure-active-directory-risk-events"></a>Eventos de risco do Azure Active Directory

A grande maioria das falhas de segurança ocorrem quando os atacantes obtêm acesso a um ambiente, por roubo de identidade de um utilizador. Detetar identidades comprometidas não é tarefa fácil. O Azure Active Directory utiliza algoritmos de aprendizagem automática adaptáveis e heurística para detetar as ações suspeitas relacionadas com as contas de utilizador. Cada detetado, ação suspeita é armazenada num registo denominado um **evento de risco**.

Existem dois locais onde revisar os eventos de risco comunicados:

 - **Relatórios do Azure AD** -eventos de risco fazem parte de segurança do Azure AD relatórios. Para obter mais informações, consulte a [utilizadores no relatório de segurança de risco](concept-user-at-risk.md) e o [relatório de inícios de sessão de risco de segurança](concept-risky-sign-ins.md).

 - **O Azure AD Identity Protection** -eventos de risco também fazem parte de capacidades de relatórios do [do Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Além disso, pode utilizar o [eventos de risco do Identity Protection API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) para obter acesso programático aos deteções de segurança com o Microsoft Graph. Para obter mais informações, consulte [introdução ao Azure Active Directory Identity Protection e Microsoft Graph](../identity-protection/graph-get-started.md). 

Atualmente, o Azure Active Directory Deteta seis tipos de eventos de risco:

- [Utilizadores com fuga de credenciais](#leaked-credentials) 
- [Inícios de sessão de endereços IP anónimos](#sign-ins-from-anonymous-ip-addresses) 
- [Deslocação impossível para localizações atípicas](#impossible-travel-to-atypical-locations) 
- [Inícios de sessão de dispositivos infetados](#sign-ins-from-infected-devices) 
- [Inícios de sessão de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Inícios de sessão de localizações desconhecidas](#sign-in-from-unfamiliar-locations) 

![Evento de risco](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Às vezes, pode encontrar um evento de risco sem uma entrada correspondente início de sessão no [relatório de inícios de sessão](concept-sign-ins.md). Isto acontece porque o Identity Protection avalia o risco para ambos **interativo** e **não interativa** inícios de sessão, ao passo que o relatório de inícios de sessão mostra apenas os interativos inícios de sessão.

A informação que obtém um evento de risco detetados está associada à sua subscrição do Azure AD. 

* Com o **edição Azure AD Premium P2**, obtém as informações mais detalhadas sobre todas as detecções subjacentes. 
* Com o **do Azure AD Premium P1 edition**, deteções de Trojans não são cobertas pela sua licença aparecem como o evento de risco **início de sessão com risco adicional detetado**.

Embora a deteção de eventos de risco já representa um aspecto importante de proteger as identidades, tem também a opção a resolvê-los manualmente ou implementar respostas automáticas ao configurar políticas de acesso condicional. Para obter mais informações, consulte [do Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-event-types"></a>Tipos de eventos de risco

O **tipo de evento de risco** propriedade é um identificador para a ação suspeita foi criado para um registo de eventos de risco.

Os investimentos contínuos da Microsoft para o processo de deteção levam a:

- Melhorias na precisão de deteção de eventos de risco existentes 
- Novos tipos de eventos de risco que serão adicionados no futuro

### <a name="leaked-credentials"></a>Fuga de credenciais

Quando cybercriminals comprometer válidas palavras-passe de utilizadores legítimos, partilham muitas vezes, essas credenciais. Normalmente, isso é feito através da publicação destacada-los publicamente nos sites web ou colar escuros ou por comerciais ou as credenciais no mercado negro de vendas. A Microsoft fuga de credenciais do serviço adquire o nome de utilizador / palavra-passe pares ao monitorizar web sites públicos e escuro e ao trabalhar com:

- Investigadores
- Aplicação da lei
- Equipes de segurança da Microsoft
- Outras origens confiáveis 

Quando o serviço adquire o nome de utilizador / pares de palavra-passe, eles são verificados relativamente credenciais válidas de atual dos utilizadores do AAD. Quando é encontrada uma correspondência, significa que a palavra-passe de um utilizador foi comprometida e um **vazamento de evento de risco de credenciais** é criado.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

Este tipo de evento de risco identifica os utilizadores que tenham iniciado sessão com êxito de um endereço IP que foi identificado como um endereço IP de proxy anónimo. Estes proxies são utilizados por pessoas que pretendem ocultar o endereço IP do seu dispositivo e podem ser utilizadas para más intenções.

### <a name="impossible-travel-to-atypical-locations"></a>Deslocação impossível para localizações atípicas

Este tipo de evento de risco identifica dois inícios de sessão provenientes de localizações distantes, em que, pelo menos, uma das localizações pode também ser atípica para o utilizador, tendo em conta comportamento pretérito. Entre vários outros fatores, esse algoritmo de machine learning leva em conta o tempo entre os dois inícios de sessão e o tempo que seria necessário para que o usuário viajam de primeira localização para o segundo, que indica que um utilizador diferente está a utilizar o mesmo credenciais.

O algoritmo ignora "positivos falsos óbvios" que contribuem para as condições de deslocação impossível, como as VPNs e localizações regularmente utilizadas por outros utilizadores na organização. O sistema tem um período de aprendizagem inicial de 14 dias durante o qual ele aprende o comportamento de início de sessão de um novo utilizador. 

### <a name="sign-in-from-unfamiliar-locations"></a>Início de sessão de localizações desconhecidas

Este tipo de evento de risco considera após o início de sessão de localizações (IP, Latitude / Longitude e o ASN) para determinar as localizações de novo / familiarizadas. O sistema armazena informações sobre localizações anteriores, utilizado por um utilizador e considera esses locais "familiares". O evento de risco é acionado quando o início de sessão ocorre a partir de uma localização que não ainda esteja na lista de localizações familiares. O sistema tem um período de aprendizagem inicial de 30 dias, durante o qual não sinaliza quaisquer novas localizações como localizações desconhecidas. O sistema também ignora os inícios de sessão de dispositivos familiares e localizações geograficamente próximas uma localização familiar. 

Proteção de identidade Deteta inícios de sessão de localizações desconhecidas também para a autenticação básica / herdados protocolos. Uma vez que estes protocolos não tiver recursos familiares modernos, como o id de cliente, não existe telemetria suficiente para reduzir os falsos positivos. Para reduzir o número de eventos de risco detetados, deve avançar para a autenticação moderna.   

### <a name="sign-ins-from-infected-devices"></a>Inícios de sessão de dispositivos infetados

Este tipo de evento de risco identifica inícios de sessão de dispositivos infetados com software maligno, que são conhecidos por ativamente a comunicar com um servidor de bot. Tal é determinado ao correlacionar endereços IP do dispositivo do utilizador em relação a endereços IP que estavam em contacto com um servidor de bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inícios de sessão de endereços IP com atividade suspeita
Este tipo de evento de risco identifica os endereços IP do qual um elevado número de tentativas de início de sessão falhadas foram visto, em várias contas de utilizador, durante um curto período de tempo. Isso corresponde a padrões de tráfego de endereços IP utilizados pelos atacantes e é um indicador forte que as contas são já ou estão prestes a ser comprometido. Este é um algoritmo de machine learning que ignora o óbvios falsos positivos, como endereços IP que regularmente são utilizados por outros utilizadores na organização.  O sistema tem um período de aprendizagem inicial de 14 dias em que este Aprenda o comportamento de início de sessão de um novo utilizador e o novo inquilino.

## <a name="detection-type"></a>Tipo de deteção

A propriedade de tipo de deteção é um indicador (**em tempo real** ou **Offline**) para o período de tempo de deteção de um evento de risco. Atualmente, a maioria dos eventos de risco são detetados offline numa operação de pós-processamento após a ocorrência do evento de risco.

A tabela seguinte apresenta a quantidade de tempo que demora para um tipo de deteção, ser apresentado em relatórios relacionados:

| Tipo de deteção | Latência de relatórios |
| --- | --- |
| Em tempo real | 5 a 10 minutos |
| Offline | 2 a 4 horas |


Para os tipos de eventos de risco que do Azure Active Directory Deteta, os tipos de deteção são:

| Tipo de evento de risco | Tipo de deteção |
| :-- | --- | 
| [Utilizadores com fuga de credenciais](#leaked-credentials) | Offline |
| [Inícios de sessão de endereços IP anónimos](#sign-ins-from-anonymous-ip-addresses) | Em tempo real |
| [Deslocação impossível para localizações atípicas](#impossible-travel-to-atypical-locations) | Offline |
| [Inícios de sessão de localizações desconhecidas](#sign-in-from-unfamiliar-locations) | Em tempo real |
| [Inícios de sessão de dispositivos infetados](#sign-ins-from-infected-devices) | Offline |
| [Inícios de sessão de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Nível de risco

A propriedade de nível de risco de um evento de risco é um indicador (**elevada**, **médio**, ou **baixa**) para a gravidade e a confiança de um evento de risco. Esta propriedade ajuda a priorizar as ações que deve efetuar. 

A gravidade do evento de risco representa a força do sinal de como um fator de fugas de identidade. A confiança é um indicador para a possibilidade de falsos positivos. 

Por exemplo, 

* **Alta**: Confiança elevada e o evento de risco de gravidade elevada. Esses eventos são indicadores fortes que a identidade do utilizador foi comprometida e as contas de utilizador afetadas devem ser corrigidas imediatamente.

* **Médio**: Gravidade elevada, mas o evento de risco de confiança inferior, ou vice versa. Esses eventos são potencialmente arriscados e devem ser retificadas as contas de utilizador afetadas.

* **Baixa**: Confiança de baixa e o evento de risco de gravidade baixa. Este evento não, requerer uma ação imediata, mas quando combinada com outros eventos de risco, pode fornecer uma forte indicação de que a identidade é comprometida.

![Nível de risco](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Fuga de credenciais

Fuga de credenciais de eventos de risco são classificados como um **elevada**, porque fornecem uma indicação clara de que o nome de utilizador e palavra-passe estão disponíveis a um invasor.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

É o nível de risco para este tipo de evento de risco **médio** porque um endereço IP anónimo não é uma indicação forte de um comprometimento de conta. Recomendamos que contacte imediatamente o utilizador para verificar se estava a utilizar endereços IP anónimos.


### <a name="impossible-travel-to-atypical-locations"></a>Deslocação impossível para localizações atípicas

Deslocação impossível, normalmente, é um bom indicador de que um hacker conseguiu conseguido iniciar sessão. No entanto, os falsos positivos podem ocorrer quando um utilizador viaja com um novo dispositivo ou com uma VPN que não é normalmente utilizada por outros utilizadores na organização. Outra fonte de Falso-positivos é aplicativos que passam incorretamente servidor IPs como cliente IPs, os quais podem dar a aparência de inícios de sessão está alojado acontecendo a partir do Centro de dados em que o aplicativo do back-end (muitas vezes, estes são os datacenters da Microsoft, que Pode dar a aparência de inícios de sessão que ocorrem da Microsoft propriedade endereços IP). Como resultado destas falsos positivos, é o nível de risco para este evento de risco **médio**.

> [!TIP]
> Pode reduzir a quantidade de Falso-positivos reportados para este tipo de evento de risco, configurando [localizações com nome](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Início de sessão de localizações desconhecidas

Localizações desconhecidas podem fornecer uma indicação forte que um invasor é capaz de utilizar uma identidade roubada. FALSO-positivos podem ocorrer quando um utilizador está em uma viagem, está a experimentar um novo dispositivo ou está a utilizar uma VPN novo. Como resultado destas falsos positivos, é o nível de risco para este tipo de evento **médio**.

### <a name="sign-ins-from-infected-devices"></a>Inícios de sessão de dispositivos infetados

Este evento de risco identifica os endereços IP, não os dispositivos de utilizador. Se vários dispositivos estejam atrás de um único endereço IP e apenas algumas estão controlada por uma rede de bot, inícios de sessão de outros dispositivos meu acionador este evento desnecessariamente, por isso este evento de risco é classificado como **baixa**.  

Recomendamos que contacte o utilizador e analisar todos os dispositivos do utilizador. Também é possível que o dispositivo pessoal de um utilizador está infetado ou que alguém estava a utilizar um dispositivo infetado do mesmo endereço IP que o utilizador. Dispositivos infetados com freqüência infetados com malware que ainda não tenham sido identificadas pelo software de antivírus e também pode indicar qualquer hábitos de usuário incorretos que podem ter causado o dispositivo ser infectados.

Para obter mais informações sobre como infeções de software maligno de endereço, consulte a [Malware Protection Center](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inícios de sessão de endereços IP com atividade suspeita

Recomendamos que contacte o utilizador para verificar se eles realmente iniciou sessão de um endereço IP que foi marcado como suspeito. O nível de risco para este tipo de evento é "**médio**" como vários dispositivos podem ser por trás do mesmo endereço IP, embora apenas algumas podem ser responsáveis pela atividade suspeita. 


## <a name="next-steps"></a>Próximos Passos

* [Utilizadores no relatório de segurança de risco](concept-user-at-risk.md)
* [Relatório de inícios de sessão de risco de segurança](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
