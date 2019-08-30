---
title: Azure Active Directory de detecções de riscos | Microsoft Docs
description: Este artice fornece uma visão geral detalhada de quais detecções de risco são.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, segurança, risco, nível de risco, vulnerabilidade, política de segurança
author: cawrites
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 523ae8e1ba31a4fe2c9683007f717149dfdc3bc6
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127326"
---
# <a name="azure-active-directory-risk-detections"></a>Azure Active Directory de detecções de risco

A grande maioria das violações de segurança ocorre quando os invasores têm acesso a um ambiente, roubando a identidade de um usuário. Descobrir identidades comprometidas não é uma tarefa fácil. O Azure Active Directory usa algoritmos de aprendizado de máquina adaptáveis e heurística para detectar ações suspeitas relacionadas às suas contas de usuário. Cada ação suspeita detectada é armazenada em um registro chamado **detecção de risco**.

Há dois locais em que você revisa as detecções de risco relatadas:

 - **Relatórios do Azure ad** – as detecções de risco fazem parte dos relatórios de segurança do Azure AD. Para obter mais informações, consulte o [relatório segurança de usuários em risco](concept-user-at-risk.md) e o [relatório de segurança de entradas arriscadas](concept-risky-sign-ins.md).

 - As detecções de **Azure ad Identity Protection** risco também fazem parte dos recursos de relatório do [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Além disso, você pode usar a [API de detecções de risco da proteção de identidade](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) para obter acesso programático a detecções de segurança usando o Microsoft Graph. Para obter mais informações, consulte Introdução [ao Azure Active Directory Identity Protection e Microsoft Graph](../identity-protection/graph-get-started.md). 

Atualmente, Azure Active Directory detecta seis tipos de detecções de risco:

- [Usuários com credenciais vazadas](#leaked-credentials) 
- [Entradas de endereços IP anônimos](#sign-ins-from-anonymous-ip-addresses) 
- [Viagem impossível a locais atípicoss](#impossible-travel-to-atypical-locations) 
- [Entradas de dispositivos infectados](#sign-ins-from-infected-devices) 
- [Entradas de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Entradas de locais desconhecidos](#sign-in-from-unfamiliar-locations) 

![Detecção de riscos](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Às vezes, você pode encontrar uma detecção de risco sem uma entrada de logon correspondente no [relatório](concept-sign-ins.md)de entradas. Isso ocorre porque a proteção de identidade avalia o risco de entradas interativas e **não** interativas, enquanto o relatório de entradas mostra apenas as entradas interativas.

O insight que você obtém para uma detecção de risco detectado está vinculado à sua assinatura do Azure AD. 

* Com a **edição do Azure ad Premium P2**, você obtém as informações mais detalhadas sobre todas as detecções subjacentes. 
* Com a **edição P1 do Azure ad Premium**, as detecções avançadas (como propriedades de entrada desconhecidas) não são cobertas por sua licença e aparecerão sob a entrada do nome **com risco adicional detectado**. Além disso, os campos nível de risco e detalhe de risco estão ocultos.

Embora a detecção de detecções de riscos já represente um aspecto importante da proteção de suas identidades, você também tem a opção de solucioná-los manualmente ou implementar respostas automatizadas Configurando políticas de acesso condicional. Para obter mais informações, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Tipos de detecção de risco

A propriedade **tipo de detecção de risco** é um identificador para a ação suspeita para a qual um registro de detecção de risco foi criado.

Os investimentos contínuos da Microsoft no processo de detecção levam a:

- Melhorias na precisão de detecção de detecções de risco existentes 
- Novos tipos de detecção de riscos que serão adicionados no futuro

### <a name="leaked-credentials"></a>Fuga de credenciais

Quando o cibercriminosos compromete senhas válidas de usuários legítimos, eles geralmente compartilham essas credenciais. Normalmente, isso é feito postando-os publicamente na Web escura ou colando sites ou negociando ou vendendo as credenciais no mercado preto. O serviço de credenciais vazadas da Microsoft adquire os pares de nome de usuário/senha monitorando sites públicos e escuros e trabalhando com:

- Investigadores
- Aplicação de leis
- Equipes de segurança na Microsoft
- Outras fontes confiáveis 

Quando o serviço adquire os pares de nome de usuário/senha, eles são verificados em relação às credenciais válidas atuais dos usuários do AAD. Quando uma correspondência é encontrada, isso significa que a senha de um usuário foi comprometida e uma **detecção de risco de credenciais vazadas** é criada.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

Esse tipo de detecção de risco identifica os usuários que entraram com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Estes proxies são utilizados por pessoas que pretendem ocultar o endereço IP do seu dispositivo e podem ser utilizadas para más intenções.

### <a name="impossible-travel-to-atypical-locations"></a>Deslocação impossível para localizações atípicas

Esse tipo de detecção de risco identifica duas entradas provenientes de locais geograficamente distantes, em que pelo menos um dos locais também pode ser atípicos para o usuário, dado o comportamento passado. Entre vários outros fatores, esse algoritmo de aprendizado de máquina leva em conta o tempo entre as duas entradas e o tempo que seria levado para o usuário viajar do primeiro local para o segundo, indicando que um usuário diferente está usando o mesmo fornecidas.

O algoritmo ignora os "falsos positivos" óbvios que contribuem para as condições de viagem impossíveis, como VPNs e locais usados regularmente por outros usuários na organização. O sistema tem um período de aprendizado inicial de 14 dias durante o qual aprende o comportamento de entrada de um novo usuário. 

### <a name="sign-in-from-unfamiliar-locations"></a>Entrada de locais desconhecidos

Esse tipo de detecção de risco considera os locais de entrada anteriores (IP, latitude/longitude e ASN) para determinar os locais novos/desconhecidos. O sistema armazena informações sobre os locais anteriores usados por um usuário e considera esses locais "familiares". A detecção de risco é disparada quando a entrada ocorre de um local que ainda não está na lista de locais familiares. O sistema tem um período de aprendizado inicial de 30 dias, durante o qual ele não sinaliza novos locais como locais desconhecidos. O sistema também ignora entradas de dispositivos familiares e locais que estão geograficamente próximos a um local familiar. 

A proteção de identidade detecta entradas de locais desconhecidos também para protocolos herdados/de autenticação básica. Como esses protocolos não têm recursos conhecidos modernos, como ID do cliente, não há telemetria suficiente para reduzir os falsos positivos. Para reduzir o número de detecções de risco detectadas, você deve mudar para a autenticação moderna.   

### <a name="sign-ins-from-infected-devices"></a>Inícios de sessão de dispositivos infetados

Esse tipo de detecção de risco identifica entradas de dispositivos infectados com malware, que são conhecidos por se comunicar ativamente com um servidor de bot. Isso é determinado pela correlação de endereços IP do dispositivo do usuário em endereços IP que estavam em contato com um servidor de bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inícios de sessão de endereços IP com atividade suspeita
Esse tipo de detecção de risco identifica os endereços IP dos quais um grande número de tentativas de entrada com falha foram vistas, em várias contas de usuário, em um curto período de tempo. Isso corresponde aos padrões de tráfego dos endereços IP usados pelos invasores e é um indicador forte de que as contas já estão ou estão prestes a ser comprometidas. Esse é um algoritmo de aprendizado de máquina que ignora falsos positivos claros, como endereços IP que são usados regularmente por outros usuários na organização.  O sistema tem um período de aprendizado inicial de 14 dias em que ele aprende o comportamento de entrada de um novo usuário e novo locatário.

## <a name="detection-type"></a>Tipo de deteção

A propriedade de tipo de detecção é um indicador (**em tempo real** ou **offline**) para o período de tempo de detecção de uma detecção de risco. Atualmente, a maioria das detecções de riscos é detectada offline em uma operação de pós-processamento após a detecção de riscos.

A tabela a seguir lista a quantidade de tempo que leva para um tipo de detecção aparecer em um relatório relacionado:

| Tipo de detecção | Latência de relatórios |
| --- | --- |
| Tempo real | 5 a 10 minutos |
| Offline | 2 a 4 horas |


Para os tipos de detecção de risco Azure Active Directory detecta, os tipos de detecção são:

| Tipo de detecção de risco | Tipo de detecção |
| :-- | --- | 
| [Usuários com credenciais vazadas](#leaked-credentials) | Offline |
| [Entradas de endereços IP anônimos](#sign-ins-from-anonymous-ip-addresses) | Tempo real |
| [Viagem impossível a locais atípicoss](#impossible-travel-to-atypical-locations) | Offline |
| [Entradas de locais desconhecidos](#sign-in-from-unfamiliar-locations) | Tempo real |
| [Entradas de dispositivos infectados](#sign-ins-from-infected-devices) | Offline |
| [Entradas de endereços IP com atividade suspeita](#sign-ins-from-ip-addresses-with-suspicious-activity) | Offline|


## <a name="risk-level"></a>Nível de risco

A propriedade nível de risco de uma detecção de risco é um indicador (**alta**, **média**ou **baixa**) para a severidade e a confiança de uma detecção de risco. Essa propriedade ajuda a priorizar as ações que você deve executar. 

A severidade da detecção de riscos representa a intensidade do sinal como um indicador de comprometimento de identidade. A confiança é um indicador para a possibilidade de falsos positivos. 

Por exemplo, 

* **Alta**: Alta confiança e detecção de risco de alta gravidade. Esses eventos são indicadores fortes que a identidade do usuário foi comprometida e qualquer conta de usuário afetada deve ser corrigida imediatamente.

* **Médio**: Alta gravidade, mas a detecção de riscos de confiança mais baixa ou vice-versa. Esses eventos são potencialmente arriscados e qualquer conta de usuário afetada deve ser corrigida.

* **Baixo**: Baixa confiança e detecção de risco de severidade baixa. Esse evento pode não exigir uma ação imediata, mas, quando combinado com outras detecções de risco, pode fornecer uma forte indicação de que a identidade é comprometida.

![Nível de risco](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Fuga de credenciais

As detecções de risco de credenciais vazadas são classificadas como **altas**, pois elas fornecem uma clara indicação de que o nome de usuário e a senha estão disponíveis para um invasor.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

O nível de risco para esse tipo de detecção de risco é **médio** porque um endereço IP anônimo não é uma indicação forte de um comprometimento de conta. Recomendamos que você entre em contato imediatamente com o usuário para verificar se ele estava usando endereços IP anônimos.


### <a name="impossible-travel-to-atypical-locations"></a>Deslocação impossível para localizações atípicas

A viagem impossível é geralmente um bom indicador de que um hacker conseguiu entrar com êxito. No entanto, falsos positivos podem ocorrer quando um usuário está viajando usando um novo dispositivo ou usando uma VPN que normalmente não é usada por outros usuários na organização. Outra fonte de falsos positivos são os aplicativos que incorretamente transmitem IPs de servidor como IPs de cliente, que podem dar a aparência das entradas que ocorrem a partir do data center em que o back-end do aplicativo é hospedado (geralmente são datacenters da Microsoft, que pode dar a aparência de entradas que ocorrem a partir de endereços IP de propriedade da Microsoft). Como resultado desses falsos positivos, o nível de risco para essa detecção de risco é **médio**.

> [!TIP]
> Você pode reduzir a quantidade de falsos positivos relatados para esse tipo de detecção de risco Configurando [locais nomeados](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Entrada de locais desconhecidos

Locais desconhecidos podem fornecer uma forte indicação de que um invasor é capaz de usar uma identidade roubada. Falsos positivos podem ocorrer quando um usuário está viajando, está experimentando um novo dispositivo ou usando uma nova VPN. Como resultado desses falsos positivos, o nível de risco para esse tipo de evento é **médio**.

### <a name="sign-ins-from-infected-devices"></a>Inícios de sessão de dispositivos infetados

Essa detecção de risco identifica os endereços IP e não os dispositivos de usuário. Se vários dispositivos estiverem atrás de um único endereço IP e apenas alguns forem controlados por uma rede de bot, as entradas de outros dispositivos meu disparam esse evento desnecessariamente, e é por isso que essa detecção de risco é classificada como **baixa**.  

Recomendamos que você entre em contato com o usuário e verifique todos os dispositivos do usuário. Também é possível que o dispositivo pessoal de um usuário esteja infectado ou que outra pessoa esteja usando um dispositivo infectado do mesmo endereço IP que o usuário. Os dispositivos infectados são frequentemente infectados por malware que ainda não foram identificados pelo software antivírus e também podem indicar qualquer hábitos de usuário inadequado que possa ter feito com que o dispositivo fosse infectado.

Para obter mais informações sobre como abordar infecções de malware, consulte o [centro de proteção contra malware](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/).

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Inícios de sessão de endereços IP com atividade suspeita

Recomendamos que você entre em contato com o usuário para verificar se ele realmente entrou a partir de um endereço IP que foi marcado como suspeito. O nível de risco para esse tipo de evento é "**médio**" porque vários dispositivos podem estar atrás do mesmo endereço IP, enquanto apenas alguns podem ser responsáveis pela atividade suspeita. 


## <a name="next-steps"></a>Próximos Passos

* [Relatório de segurança de usuários em risco](concept-user-at-risk.md)
* [Relatório de segurança de entradas arriscadas](concept-risky-sign-ins.md)
* [Azure ad Identity Protection](../active-directory-identityprotection.md).
