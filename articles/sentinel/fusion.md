---
title: Deteção avançada de ataques multiestágios em Azure Sentinel
description: Utilize a tecnologia Fusion em Azure Sentinel para reduzir a fadiga de alerta e criar incidentes atores baseados na deteção avançada de ataques multiestágios.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: rkarlin
ms.openlocfilehash: ada2ad67bc3634d8e6a31d3c8a69fc0c8b08a93a
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77369700"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Deteção avançada de ataques multiestágios em Azure Sentinel

Ao utilizar a tecnologia Fusion baseada na aprendizagem automática, o Azure Sentinel pode detetar automaticamente ataques em vários palcos, combinando comportamentos anómalos e atividades suspeitas que são observadas em várias fases da cadeia de morte. O Azure Sentinel gera então incidentes que, de outra forma, seriam muito difíceis de apanhar. Estes incidentes encerram dois ou mais alertas ou atividades. Por design, estes incidentes são de baixo volume, alta fidelidade e alta gravidade.

Personalizada para o seu ambiente, esta deteção não só reduz as taxas falsas positivas, como também pode detetar ataques com informações limitadas ou em falta.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuração para deteção avançada de ataques multiestágios

Esta deteção é ativada por padrão no Azure Sentinel. Para verificar o estado, ou para desativá-lo talvez porque está a usar uma solução alternativa para criar incidentes com base em múltiplos alertas, utilize as seguintes instruções:

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Navegue até à Configuração > **Do Sentinel a** **Nata > Analytics**

3. Selecione **regras ativas** e localize a **Deteção avançada de ataques multiestágios** na coluna **NAME.** Verifique a coluna **STATUS** para confirmar se esta deteção está ativada ou desativada.

4. Para alterar o estado, selecione esta entrada e na lâmina de deteção de **ataques multiestágio avançado,** selecione **Editar**.

5. Na lâmina do assistente de **criação da Regra,** a alteração de estado é selecionada automaticamente para si, por isso selecione **Next: Review**, e, em seguida, **Guardar**. 

Os modelos de regras não são aplicáveis para a deteção avançada de ataques multiestágios.

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusão usando Redes Palo Alto e Microsoft Defender ATP

- Pedido de rede ao serviço de anoinização tor seguido de tráfego anómalo sinalizado pela firewall palo Alto Networks

- PowerShell fez uma ligação de rede suspeita seguida de tráfego anómalo sinalizado pela firewall palo Alto Networks

- Ligação de saída à IP com histórico de tentativas de acesso não autorizadas seguida de tráfego anómalo sinalizado pela firewall palo Alto Networks



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusão usando proteção de identidade e segurança de aplicações da Microsoft Cloud

Utilizando a deteção avançada de ataques multiestágios, o Azure Sentinel suporta os seguintes cenários que combinam eventos de anomalia saturado de Proteção de Identidade de Diretório Ativo Azure e Segurança de Aplicações microsoft Cloud:

- [Viagem impossível para localização atípica seguida por atividade anómala do Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Atividade de inscrição para localização desconhecida seguida de atividade anómala do Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Atividade de login de dispositivo infetado seguida de atividade anómala do Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Atividade de login a partir de endereço IP anónimo seguido de atividade anómala do Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Atividade de login de utilizador com credenciais vazadas seguida de atividade anómala do Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Deve ter configurado o [conector](connect-azure-ad-identity-protection.md) de dados de Proteção de Identidade Azure AD e os conectores [cloud App Security.](connect-cloud-app-security.md)

Nas descrições que se seguem, o Azure Sentinel apresentará o valor real dos seus dados que está representado nesta página como variáveis nos parênteses. Por exemplo, o nome real de exibição de uma conta em vez de \<nome de *conta*>, e o número real em vez de \<*número*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Viagem impossível para localização atípica seguida por atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam viagens impossíveis a alertas de localização atípicos da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Viagem impossível a locais atípicos que levam à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de um evento de entrada \<nome de *conta*> de uma viagem impossível para \<*localização*>, uma localização atípica, seguida de uma regra de encaminhamento suspeita de caixa de entrada foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<nome de *conta*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço externo \<endereço de *e-mail*>.

- **Viagem impossível para locais atípicos que conduzem a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de uma viagem impossível para \<*localização*>, uma localização atípica.
    
    Em seguida, a conta \<nome de *conta*> realizada sobre \<*número*> atividades administrativas numa única sessão.

- **Viagem impossível para locais atípicos que levam à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> para \<*localização*>, uma localização atípica. 
    
    Em seguida, a conta \<nome de *conta*> apagado \<*número de*ficheiros > únicos numa única sessão.

- **Viagem impossível para locais atípicos que levam ao download de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de uma viagem impossível para \<*localização*>, uma localização atípica. 
    
    Em seguida, a conta \<nome de *conta*> descarregado sobre \<*número de*ficheiros > únicos numa única sessão.

- **Viagem impossível a locais atípicos que levam à personificação do Office 365**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de uma viagem impossível para \<*localização*>, uma localização atípica. 
    
    Em seguida, a conta \<nome de *conta*> realizou um valor invulgar (\<*número de atividades*>) de atividades de personificação numa única sessão.

- **Viagem impossível para locais atípicos que levam à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de uma viagem impossível para \<*localização*>, uma localização atípica. 
    
    Em seguida, a conta \<nome de *conta*> partilhada sobre \<*número de*ficheiros > únicos numa única sessão.

- **Viagem impossível a locais atípicos que levam ao ransomware na aplicação cloud**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de uma viagem impossível para \<*localização*>, uma localização atípica. 
    
    Em seguida, a conta \<nome de *conta*> carregou \<*número de*ficheiros > e apagou um total de \<número *de*ficheiros > 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Atividade de inscrição para localização desconhecida seguida de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de login para alertas de localização desconhecidos da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security.

- **Evento de entrada a partir de um local desconhecido que leva à exfiltração de caixa de correio Online**
    
    Este alerta é uma indicação de um evento de entrada \<nome de *conta*> de \<*localização*>, um local desconhecido, seguido de uma regra de encaminhamento suspeita de caixa de entrada foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<nome de *conta*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço externo \<endereço de *e-mail*>. 

- **Evento de inscrição a partir de um local desconhecido que conduz a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> a partir de \<*localização*>, um local desconhecido. 
    
    Em seguida, a conta \<nome de *conta*> realizada ao longo de \<*número de*atividades administrativas numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> a partir de \<*localização*>, um local desconhecido. 
    
    Em seguida, a conta \<nome de *conta*> apagado \<*número de*ficheiros > únicos numa única sessão.

- **Evento de início de sessão a partir de um local desconhecido que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> a partir de \<*localização*>, um local desconhecido. 
    
    Em seguida, a conta \<nome de *conta*> descarregado sobre \<*número de*ficheiros > únicos numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva à personificação do Office 365**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> a partir de \<*localização*>, um local desconhecido.
    
    Em seguida, a conta \<nome de *conta*> personificada sobre \<*número de*contas > diferentes numa única sessão.

- **Evento de início de sessão a partir de um local desconhecido que leva à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> a partir de \<*localização*>, um local desconhecido. 
    
    Em seguida, a conta \<nome de *conta*> partilhada sobre \<*número de*ficheiros > únicos numa única sessão.

- **Evento de sessão a partir de um local desconhecido que leva ao ransomware na aplicação cloud**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> a partir de \<*localização*>, um local desconhecido. 
    
    Em seguida, a conta \<nome de *conta*> carregou \<*número de*ficheiros > e apagou um total de \<número *de*ficheiros > 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Atividade de login de dispositivo infetado seguida de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de login a partir de alertas de dispositivos infetados da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de entrada a partir de um dispositivo infetado que leva à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de um evento de entrada \<nome de *conta*> de um dispositivo potencialmente infetado com malware, seguido de uma regra de encaminhamento suspeita de caixa de entrada foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<nome de *conta*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço externo \<endereço de *e-mail*>. 

- **Evento de inscrição a partir de um dispositivo infetado que conduz a atividade administrativa suspeita de aplicações em nuvem**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> de um dispositivo potencialmente infetado com malware.
    
    Em seguida, a conta \<nome de *conta*> realizada ao longo de \<*número de*atividades administrativas numa única sessão.

- **Evento de início de sessão a partir de um dispositivo infetado que conduz à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<nome de *conta*> apagado \<*número de*ficheiros > únicos numa única sessão.

- **Evento de início de sessão a partir de um dispositivo infetado que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<nome de *conta*> descarregado sobre \<*número de*ficheiros > únicos numa única sessão.

- **Evento de inscrição a partir de um dispositivo infetado que leva à personificação do Office 365**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<nome de *conta*> personificada sobre \<*número de*contas > diferentes numa única sessão.

- **Evento de início de sessão a partir de um dispositivo infetado que conduz à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<nome de *conta*> partilhada sobre \<*número de*ficheiros > únicos numa única sessão.

- **Evento de sessão a partir de um dispositivo infetado que leva ao ransomware na aplicação cloud**
    
    Este alerta é uma indicação de um evento de início de sessão \<nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<nome de *conta*> carregou \<*número de*ficheiros > e apagou um total de \<número *de*ficheiros > 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Atividade de login a partir de endereço IP anónimo seguido de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de entrada a partir de alertas de endereçoip anónimos da Proteção de Identidade Azure AD e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de entrada a partir de um endereço IP anónimo que conduz à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de um evento de inscrição por \<nome de *conta*> de um endereço IP de procuração anónimo \<*endereço IP*>, seguido de uma regra de reencaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<nome de *conta*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço externo \<endereço de *e-mail*>. 

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de um endereço IP de procuração anónimo \<*endereço IP*>. 
    
    Em seguida, a conta \<nome de *conta*> realizada ao longo de \<*número de*atividades administrativas numa única sessão.

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de um endereço IP de procuração anónimo \<*endereço IP*>. 
    
    Em seguida, a conta \<nome de *conta*> apagado \<*número de*ficheiros > únicos numa única sessão.

- **Evento de início de sessão a partir de um endereço IP anónimo que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de um endereço IP de procuração anónimo \<*endereço IP*>. 
    
    Em seguida, a conta \<nome de *conta*> descarregado sobre \<*número de*ficheiros > únicos numa única sessão.

- **Evento de inscrição a partir de um endereço IP anónimo que leva à personificação do Office 365**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de um endereço IP de procuração anónimo \<*endereço IP*>. 
    
    Em seguida, a conta \<nome de *conta*> personificada sobre \<*número de*contas > diferentes numa única sessão.

- **Evento de início de sessão a partir de um endereço IP anónimo que conduz à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de um endereço IP de procuração anónimo \<*endereço IP*>. 
    
    Em seguida, a conta \<nome de *conta*> partilhada sobre \<*número de*ficheiros > únicos numa única sessão.

- **Evento de sessão a partir de um endereço IP anónimo para ransomware na aplicação cloud**
    
    Este alerta é uma indicação de um evento de inscrição \<nome de *conta*> de um endereço IP de procuração anónimo \<*endereço IP*>. 
    
    Em seguida, a conta \<nome de *conta*> carregou \<*número de*ficheiros > e apagou um total de \<número *de*ficheiros > 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Atividade de login de utilizador com credenciais vazadas seguida de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de login do utilizador com alertas de credenciais vazados da Proteção de Identidade Da Azure AD e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de entrada de utilizador com credenciais vazadas que levam à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de que o evento de entrada \<pelo nome de *conta*> usou credenciais vazadas, seguido de uma regra de encaminhamento suspeita da caixa de entrada na caixa de entrada de um utilizador. 
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<nome de *conta*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço externo \<endereço de *e-mail*>. 

- **Evento de inscrição de utilizador com credenciais vazadas que conduzem a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de que o evento de inscrição \<nome de *conta*> usou credenciais vazadas.
    
    Em seguida, a conta \<nome de *conta*> realizada ao longo de \<*número de*atividades administrativas numa única sessão.

- **Evento de início de sessão do Utilizador com credenciais vazadas que conduzem à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de que o evento de inscrição \<nome de *conta*> usou credenciais vazadas.
    
    Em seguida, a conta \<nome de *conta*> apagado \<*número de*ficheiros > únicos numa única sessão.

- **Evento de início de sessão do Utilizador com credenciais vazadas que levam ao download de ficheiros em massa**
    
    Este alerta é uma indicação de que o evento de inscrição \<nome de *conta*> usou credenciais vazadas.
    
    Em seguida, a conta \<nome de *conta*> descarregado sobre \<*número de*ficheiros > únicos numa única sessão.

- **Evento de inscrição de utilizador com credenciais vazadas que conduzem à personificação do Office 365**
    
    Este alerta é uma indicação de que o evento de inscrição \<nome de *conta*> usou credenciais vazadas. 
    
    Em seguida, a conta \<nome de *conta*> personificada sobre \<*número de*contas > diferentes numa única sessão.

- **Evento de início de sessão do Utilizador com credenciais vazadas que conduzem à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de que o evento de inscrição \<nome de *conta*> usou credenciais vazadas.
    
    Em seguida, a conta \<nome de *conta*> partilhada sobre \<*número de*ficheiros > únicos numa única sessão.

- **Evento de início de sessão do Utilizador com credenciais vazadas para ransomware na aplicação cloud**
    
    Este alerta é uma indicação de que o evento de inscrição \<nome de *conta*> usou credenciais vazadas. 
    
    Em seguida, a conta \<nome de *conta*> carregou \<*número de*ficheiros > e apagou um total de \<número *de*ficheiros > 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu mais sobre a deteção avançada de ataques multipalco, pode estar interessado no seguinte início rápido para aprender a obter visibilidade nos seus dados e ameaças potenciais: [Comece com O Sentinel.](quickstart-get-visibility.md)

Se estiver pronto para investigar os incidentes que são criados para si, consulte o seguinte tutorial: [Investigue incidentes com Azure Sentinel](tutorial-investigate-cases.md).

