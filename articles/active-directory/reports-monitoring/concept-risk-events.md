---
title: Deteções de risco do Diretório Ativo Azure Microsoft Docs
description: Este artifício dá-lhe uma visão detalhada do que são as deteções de risco.
services: active-directory
keywords: proteção de identidade de diretório ativo azure, segurança, risco, nível de risco, vulnerabilidade, política de segurança
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
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383961"
---
# <a name="azure-active-directory-risk-detections"></a>Deteção de risco de Diretório Ativo Azure

A grande maioria das falhas de segurança ocorrem quando os atacantes têm acesso a um ambiente roubando a identidade de um utilizador. Descobrir identidades comprometidas não é tarefa fácil. O Azure Ative Directory utiliza algoritmos adaptativos de aprendizagem automática e heurística para detetar ações suspeitas relacionadas com as suas contas de utilizador. Cada ação suspeita detetada é armazenada num registo chamado **deteção de risco.**

Há dois locais onde analisa as deteções de risco reportadas:

 - **Relatório supor a AD Azure** - As deteções de risco fazem parte dos relatórios de segurança da Azure AD. Para obter mais informações, consulte os [utilizadores do relatório](concept-user-at-risk.md) de segurança de risco e o relatório de segurança de [inscrição arriscado](concept-risky-sign-ins.md).

 - **Azure AD Identity Protection** - As deteções de risco também fazem parte das capacidades de reporte da Proteção de Identidade do [Diretório Ativo azure.](../active-directory-identityprotection.md)

Além disso, pode utilizar a API de deteção de risco de [Proteção de Identidade](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) para obter acesso programático a deteções de segurança usando o Microsoft Graph. Para mais informações, consulte [Start start with Azure Ative Directory Identity Protection and Microsoft Graph](../identity-protection/graph-get-started.md). 

Atualmente, o Azure Ative Directory deteta seis tipos de deteções de risco:

- [Utilizadores com fuga de credenciais](#leaked-credentials) 
- [Inícios de sessão de endereços IP anónimos](#sign-ins-from-anonymous-ip-addresses) 
- [Deslocação impossível para localizações atípicas](#impossible-travel-to-atypical-locations) 
- [Inícios de sessão de dispositivos infetados](#sign-ins-from-infected-devices) 
- [Inícios de sessão de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Inícios de sessão de localizações desconhecidas](#sign-in-from-unfamiliar-locations) 

![Deteção de riscos](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Por [vezes,](concept-sign-ins.md)pode encontrar uma deteção de risco sem uma entrada correspondente no relatório de inscrição . Isto porque a Proteção de Identidade avalia o risco tanto para os signos **interativos** como para os não **interativos,** enquanto o relatório de inscrição mostra apenas os sign-ins interativos.

A perceção que obtém para uma deteção de risco detetada está ligada à sua subscrição de AD Azure. 

* Com a **edição Azure AD Premium P2,** obtém-se a informação mais detalhada sobre todas as deteções subjacentes. 
* Com a **edição Azure AD Premium P1,** as deteções avançadas (tais como propriedades de entrada desconhecida) não estão cobertas pela sua licença, e aparecerão sob o nome **Sign-in com risco adicional detetado.** Além disso, o nível de risco e os campos de detalhes de risco estão escondidos.

Embora a deteção de deteções de risco já represente um aspeto importante de proteção das suas identidades, também tem a opção de as abordar manualmente ou implementar respostas automatizadas através da configuração de políticas de Acesso Condicional. Para obter mas informações, veja [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Tipos de deteção de riscos

A propriedade do tipo de **deteção** de risco é um identificador para a ação suspeita para a criação de um registo de deteção de risco.

Os investimentos contínuos da Microsoft no processo de deteção levam a:

- Melhorias na precisão de deteção das deteções de risco existentes 
- Novos tipos de deteção de risco que serão adicionados no futuro

### <a name="leaked-credentials"></a>Credenciais vazadas

Quando os cibercriminosos comprometem senhas válidas de utilizadores legítimos, muitas vezes partilham essas credenciais. Isto é geralmente feito publicamente publicamente na web ou sites de pasta escuros ou através da negociação ou venda das credenciais no mercado negro. O serviço de credenciais vazados da Microsoft adquire pares de nomes de utilizador/password através da monitorização de web sites públicos e escuros e trabalhando com:

- Investigadores
- Aplicação da lei
- Equipas de segurança na Microsoft
- Outras fontes fidedignas 

Quando o serviço adquire pares de nomes de utilizador/palavra-passe, são verificados com as credenciais válidas dos utilizadores aad. Quando uma correspondência é encontrada, significa que a palavra-passe de um utilizador foi comprometida, e uma deteção de risco de **credenciais vazadas** é criada.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

Este tipo de deteção de risco identifica utilizadores que assinaram com sucesso a partir de um endereço IP que foi identificado como um endereço IP de procuração anónimo. Estes proxies são usados por pessoas que querem esconder o endereço IP do seu dispositivo, e podem ser usados para intenções maliciosas.

### <a name="impossible-travel-to-atypical-locations"></a>Deslocação impossível para localizações atípicas

Este tipo de deteção de risco identifica dois sign-ins originários de locais geograficamente distantes, onde pelo menos um dos locais também pode ser atípico para o utilizador, dado o comportamento passado. Entre vários outros fatores, este algoritmo de aprendizagem automática tem em conta o tempo entre os dois sign-ins e o tempo que o utilizador teria demorado a viajar do primeiro local para o segundo, indicando que um utilizador diferente está a usar o mesmo credenciais.

O algoritmo ignora "falsos positivos" óbvios que contribuem para as condições de viagem impossíveis, como VPNs e locais regularmente utilizados por outros utilizadores da organização. O sistema tem um período inicial de aprendizagem de 14 dias durante os quais aprende o comportamento de entrada de um novo utilizador. 

### <a name="sign-in-from-unfamiliar-locations"></a>Iniciar sessão em locais desconhecidos

Este tipo de deteção de risco considera locais de inscrição passados (IP, Latitude / Longitude e ASN) para determinar locais novos/desconhecidos. O sistema armazena informações sobre locais anteriores utilizados por um utilizador, e considera estas localizações "familiares". A deteção de risco é desencadeada quando o sinal de sessão ocorre a partir de um local que ainda não está na lista de locais familiares. O sistema tem um período inicial de aprendizagem de 30 dias, durante o qual não sinaliza novos locais como locais desconhecidos. O sistema também ignora os sign-ins de dispositivos familiares, e locais que estão geograficamente perto de um local familiar. 

A Proteção de Identidade deteta inscrições de locais desconhecidos também para protocolos básicos de autenticação/legado. Como estes protocolos não têm características familiares modernas, como a identificação do cliente, não há telemetria suficiente para reduzir falsos positivos. Para reduzir o número de deteções de risco detetadas, deve passar para a autenticação moderna.   

> [!NOTE]
> Se o nome de utilizador de login e a palavra-passe não corresponderem, o login falhará e a deteção de risco não ocorre. O registo de deteções de risco de localização desconhecidas só é desencadeado em logins succesful.

### <a name="sign-ins-from-infected-devices"></a>Inícios de sessão de dispositivos infetados

Este tipo de deteção de risco identifica os sign-ins de dispositivos infetados com malware, que são conhecidos por comunicarem ativamente com um servidor de bot. Isto é determinado por correlacionar endereços IP do dispositivo do utilizador contra endereços IP que estavam em contacto com um servidor bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inícios de sessão de endereços IP com atividade suspeita
Este tipo de deteção de risco identifica endereços IP a partir dos quais um elevado número de tentativas de entrada falhadas foram vistos, através de várias contas de utilizador, durante um curto período de tempo. Isto corresponde aos padrões de tráfego dos endereços IP utilizados pelos atacantes, e é um forte indicador de que as contas já estão ou estão prestes a ser comprometidas. Este é um algoritmo de aprendizagem automática que ignora óbvios falsos positivos, como endereços IP que são usados regularmente por outros utilizadores da organização.  O sistema tem um período inicial de aprendizagem de 14 dias onde aprende o comportamento de entrada de um novo utilizador e novo inquilino.

## <a name="detection-type"></a>Tipo de deteção

A propriedade do tipo de deteção é um indicador **(em tempo real** ou **offline**) para o período de deteção de uma deteção de risco. Atualmente, a maioria das deteções de risco são detetadas offline numa operação pós-processamento após a deteção de risco.

A tabela que se segue enumera o tempo que um tipo de deteção demora a aparecer num relatório relacionado:

| Tipo de Deteção | Reportagem Latência |
| --- | --- |
| Tempo real | 5 a 10 minutos |
| Offline | 2 a 4 horas |


Para os tipos de deteção de risco detetados pelo Diretório Ativo Azure, os tipos de deteção são:

| Tipo de Deteção de Risco | Tipo de Deteção |
| :-- | --- | 
| [Utilizadores com fuga de credenciais](#leaked-credentials) | Offline |
| [Inícios de sessão de endereços IP anónimos](#sign-ins-from-anonymous-ip-addresses) | Tempo real |
| [Deslocação impossível para localizações atípicas](#impossible-travel-to-atypical-locations) | Offline |
| [Inícios de sessão de localizações desconhecidas](#sign-in-from-unfamiliar-locations) | Tempo real |
| [Inícios de sessão de dispositivos infetados](#sign-ins-from-infected-devices) | Offline |
| [Inícios de sessão de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Nível de risco

A propriedade de nível de risco de uma deteção de risco é um indicador (**Alto,** **Médio,** ou **Baixo**) para a gravidade e a confiança de uma deteção de risco. Esta propriedade ajuda-o a priorizar as ações que deve tomar. 

A gravidade da deteção de risco representa a força do sinal como um preditor de compromisso de identidade. A confiança é um indicador para a possibilidade de falsos positivos. 

Por exemplo, 

* **Alta**: Alta confiança e deteção de risco de alta gravidade. Estes eventos são fortes indicadores de que a identidade do utilizador foi comprometida, e quaisquer contas de utilizador impactadas devem ser remediadas imediatamente.

* **Médio**: Alta gravidade, mas menor deteção de risco de confiança, ou vice-versa. Estes eventos são potencialmente arriscados e quaisquer contas de utilizador impactadas devem ser remediadas.

* **Baixa**: Baixa confiança e baixa severidade de deteção de risco. Este evento pode não exigir uma ação imediata, mas quando combinado com outras deteções de risco, pode fornecer uma forte indicação de que a identidade está comprometida.

![Nível de risco](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Credenciais vazadas

As credenciais vazadas as deteções de risco são classificadas como **High,** pois fornecem uma indicação clara de que o nome de utilizador e a palavra-passe estão disponíveis para um intruso.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

O nível de risco para este tipo de deteção de risco é **Médio** porque um endereço IP anónimo não é uma forte indicação de um compromisso de conta. Recomendamos que contacte imediatamente o utilizador para verificar se utilizava endereços IP anónimos.


### <a name="impossible-travel-to-atypical-locations"></a>Deslocação impossível para localizações atípicas

Viajar impossível é geralmente um bom indicador de que um hacker foi capaz de assinar com sucesso. No entanto, podem ocorrer falsos positivos quando um utilizador viaja usando um novo dispositivo ou usando uma VPN que normalmente não é usada por outros utilizadores da organização. Outra fonte de falsos positivos são as aplicações que incorretamente passam os IPs do servidor como IPs do cliente, que podem dar a aparência de entradas em olugar a partir do centro de dados onde o back-end dessa aplicação é hospedado (muitas vezes estes são centros de dados da Microsoft, que podem dar a aparência de entradas em omôl-do-centro de dados onde o back-end dessa aplicação é hospedado (muitas vezes estes são centros de dados da Microsoft, que pode dar a aparência de inscrições em então a partir de endereços IP propriedade da Microsoft). Como resultado destes falsos positivos, o nível de risco para esta deteção de risco é **Médio**.

> [!TIP]
> Pode reduzir a quantidade de falsos positivos reportados para este tipo de deteção de risco configurando [localizações nomeadas](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Iniciar sessão em locais desconhecidos

Locais desconhecidos podem fornecer uma forte indicação de que um intruso é capaz de usar uma identidade roubada. Podem ocorrer falsos positivos quando um utilizador está a viajar, está a experimentar um novo dispositivo ou está a utilizar um novo VPN. Como resultado destes falsos positivos, o nível de risco para este tipo de evento é **Médio**.

### <a name="sign-ins-from-infected-devices"></a>Inícios de sessão de dispositivos infetados

Esta deteção de risco identifica endereços IP, não dispositivos de utilizador. Se vários dispositivos estiverem por detrás de um único endereço IP, e apenas alguns forem controlados por uma rede bot, os sign-ins de outros dispositivos são o meu gatilho desnecessariamente este evento, razão pela qual esta deteção de risco é classificada como **Low**.  

Recomendamos que contacte o utilizador e scaneie todos os dispositivos do utilizador. Também é possível que o dispositivo pessoal de um utilizador esteja infetado ou que outra pessoa estivesse a utilizar um dispositivo infetado a partir do mesmo endereço IP que o utilizador. Os dispositivos infetados são frequentemente infetados por malware que ainda não foi identificado por software antivírus, e também podem indicar quaisquer maus hábitos de utilizador que possam ter causado a infeção do dispositivo.

Para obter mais informações sobre como lidar com infeções por malware, consulte o Centro de [Proteção](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)de Malware .

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inícios de sessão de endereços IP com atividade suspeita

Recomendamos que contacte o utilizador para verificar se realmente assinou a partir de um endereço IP que foi marcado como suspeito. O nível de risco para este tipo de evento é "**Medium**" porque vários dispositivos podem estar por trás do mesmo endereço IP, enquanto apenas alguns podem ser responsáveis pela atividade suspeita. 


## <a name="next-steps"></a>Passos Seguintes

* [Utilizadores em relatório de segurança de risco](concept-user-at-risk.md)
* [Relatório de segurança de inscrições de risco](concept-risky-sign-ins.md)
* [Proteção de Identidade Azure AD.](../active-directory-identityprotection.md)
