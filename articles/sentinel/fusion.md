---
title: Deteção avançada de ataques em vários palcos em Azure Sentinel
description: Use a tecnologia Fusion em Azure Sentinel para reduzir a fadiga de alerta e criar incidentes acccáveis baseados na deteção avançada de ataques em vários estágios.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 1f415294c77b743996993f1f00be45e36f9d6002
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660671"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Deteção avançada de ataques em vários palcos em Azure Sentinel


> [!IMPORTANT]
> Algumas funcionalidades da Fusion em Azure Sentinel estão atualmente em pré-visualização pública.
> Estas funcionalidades são fornecidas sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Ao usar a tecnologia Fusion baseada na aprendizagem automática, o Azure Sentinel pode detetar automaticamente ataques de vários estágios, combinando comportamentos anómalos e atividades suspeitas que são observadas em várias fases da cadeia de morte. Azure Sentinel gera então incidentes que de outra forma seriam muito difíceis de apanhar. Estes incidentes envolvem dois ou mais alertas ou atividades. Por design, estes incidentes são de baixo volume, alta fidelidade e alta gravidade.

Personalizada para o seu ambiente, esta deteção não só reduz taxas falsas positivas como também pode detetar ataques com informações limitadas ou em falta.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuração da deteção avançada de ataques em várias fases

Esta deteção é ativada por padrão em Azure Sentinel. Para verificar o estado, ou desativá-lo talvez porque está a usar uma solução alternativa para criar incidentes baseados em vários alertas, use as seguintes instruções:

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Navegue para **Azure Sentinel**  >  **Configuration**  >  **Analytics**

3. Selecione **as regras Ativas** e localize **a Deteção avançada de ataques multistage** na coluna **NAME.** Verifique se a coluna **STATUS** confirma se esta deteção está ativada ou desativada.

4. Para alterar o estado, selecione esta entrada e na lâmina **avançada de deteção de ataques multiestage,** selecione **Editar**.

5. Na lâmina do assistente de **criação regra,** a alteração de estado é selecionada automaticamente para si, por isso selecione **Seguinte: Revisão**e, em seguida, **Guarde**. 

Os modelos de regras não são aplicáveis para a deteção avançada de ataques em vários estágios.

> [!NOTE]
> A Azure Sentinel utiliza atualmente 30 dias de dados históricos para treinar os sistemas de aprendizagem automática. Estes dados são sempre encriptados utilizando as chaves da Microsoft à medida que passam pelo pipeline de aprendizagem automática. No entanto, os dados de formação não são encriptados utilizando [as Chaves Geridas pelo Cliente (CMK)](customer-managed-keys.md) se tiver ativado a CMK no seu espaço de trabalho Azure Sentinel. Para excluir a Fusão, navegue para **Azure Sentinel**   \>  **Configuration**   \>  **Analytics Ative rules \> Advanced \> Multistage Attack Detection** and in the **Status** column,select **Disable.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Fusão utilizando palo alto networks e Microsoft Defender para Endpoint (anteriormente Microsoft Defender ATP)

Estes cenários combinam dois dos registos fundamentais utilizados pelos analistas de segurança: registos de firewall das Redes Palo Alto e registos de deteção de pontos finais do Microsoft Defender para Endpoint. Em todos os cenários listados abaixo, uma atividade suspeita é detetada no ponto final que envolve um endereço IP externo, então, este é seguido por tráfego anómalo do endereço IP externo de volta para a firewall. Nos registos de Palo Alto, o Azure Sentinel foca-se em [registos de ameaças,](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando são permitidas ameaças (dados suspeitos, ficheiros, inundações, pacotes, digitalizações, spyware, URLs, vírus, vulnerabilidades, vírus, incêndios florestais, incêndios florestais).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Pedido de rede ao serviço de anonimização TOR seguido de tráfego anómalo sinalizado pela firewall da Palo Alto Networks.

Neste cenário, o Azure Sentinel deteta pela primeira vez um alerta de que o Microsoft Defender for Endpoint (anteriormente Microsoft Defender para ATP) detetou um pedido de rede para um serviço de anonimização TOR que leva a uma atividade anómala. Isto foi iniciado sob conta {nome de conta} com SID ID {sid} em {time}. O endereço IP de saída para a ligação foi {IndividualIp}.
Em seguida, a atividade incomum foi detetada pela Firewall das Redes Palo Alto em {TimeGenerated}. Isto indica tráfego malicioso introduzido na sua rede O endereço IP de destino para o tráfego da rede é {DestinationIP}.

Este cenário encontra-se atualmente em pré-visualização pública.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>A PowerShell fez uma ligação de rede suspeita, seguida de tráfego anómalo sinalizado pela firewall da Palo Alto Networks.

Neste cenário, o Azure Sentinel deteta pela primeira vez um alerta de que o Microsoft Defender for Endpoint (anteriormente Microsoft Defender para ATP) detetou que o PowerShell fez uma ligação de rede suspeita levando a uma atividade anómala que foi detetada por uma Firewall da Rede Palo Alto. Isto foi iniciado pela conta {nome da conta} com SID ID {sid} em {time}. O endereço IP de saída para a ligação foi {IndividualIp}. Em seguida, a atividade incomum foi detetada pela Firewall das Redes Palo Alto em {TimeGenerated}. Isto indica que o tráfego malicioso entrou na sua rede. O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário encontra-se atualmente em pré-visualização pública.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Ligação de saída ao IP com histórico de tentativas de acesso não autorizadas seguidas de tráfego anómalo sinalizado pela firewall da Palo Alto Networks

Neste cenário, o Azure Sentinel deteta um alerta de que o Microsoft Defender for Endpoint (anteriormente Microsoft Defender para ATP) detetou uma ligação de saída a um endereço IP com um histórico de tentativas de acesso não autorizadas que levam a uma atividade anómala a ser detetada pelo Firewall das Redes Palo Alto. Isto foi iniciado pela conta {nome da conta} com SID ID {sid} em {time}. O endereço IP de saída para a ligação foi {IndividualIp}. Depois disso, a atividade invulgar foi detetada pela Firewall das Redes Palo Alto em {TimeGenerated}. Isto indica que o tráfego malicioso entrou na sua rede. O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário encontra-se atualmente em pré-visualização pública.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusão usando proteção de identidade e segurança de aplicativos microsoft cloud

Utilizando a deteção avançada de ataques em vários estágios, o Azure Sentinel suporta os seguintes cenários que combinam eventos de anomalias da Azure Ative Directory Identity Protection e da Microsoft Cloud App Security:

- [Viagem impossível para local atípico seguido de atividade anómala office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Atividade de inscrição para localização desconhecida seguida de atividade anómala do Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Atividade de inscrição de dispositivo infetado seguido de atividade anómala do Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Atividade de inscrição a partir de endereço IP anónimo seguido de atividade anómala office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Atividade de inscrição do utilizador com credenciais vazadas seguida de atividade anómala do Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Tem de [configurar o conector de dados de proteção de identidade Azure AD](connect-azure-ad-identity-protection.md) e os conectores [cloud App Security.](connect-cloud-app-security.md)

Nas descrições que se seguem, o Azure Sentinel apresentará o valor real dos seus dados que estão representados nesta página como variáveis nos parênteses. Por exemplo, o nome real de exibição de uma conta e não \<*account name*> , e o número real em vez de \<*number*> .

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Viagem impossível para local atípico seguido de atividade anómala office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam viagens impossíveis a alertas de localização atípica da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Viagens impossíveis para locais atípicos que conduzem à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de um evento de entrada de sinal por \<*account name*>  de uma viagem impossível para \<*location*> , uma localização atípica, seguido de uma regra de reencaminhamento de caixa de entrada suspeita foi definido na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<*account name*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails recebidos para o endereço externo \<*email address*> .

- **Viagem impossível para locais atípicos que levam a atividade administrativa de aplicações em nuvem suspeita**
    
    Este alerta é uma indicação de um evento de \<*account name*> inscrição de uma viagem impossível para \<*location*> , um local atípico.
    
    Em seguida, a conta \<*account name*> realizada sobre \<*number*> as atividades administrativas numa única sessão.

- **Viagem impossível para locais atípicos que levam à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> \<*location*> por, um local atípico. 
    
    Em seguida, a conta \<*account name*> apagou \<*number of*> ficheiros únicos numa única sessão.

- **Viagem impossível para locais atípicos que levam ao download de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de \<*account name*> inscrição de uma viagem impossível para \<*location*> , um local atípico. 
    
    Em seguida, a conta \<*account name*> descarregou sobre \<*number of*> ficheiros únicos numa única sessão.

- **Viagem impossível para locais atípicos que levam à personificação do Office 365**
    
    Este alerta é uma indicação de um evento de \<*account name*> inscrição de uma viagem impossível para \<*location*> , um local atípico. 
    
    Em seguida, a conta \<*account name*> realizou um valor incomum ( ) de \<*number of activities*> atividades de imitação numa única sessão.

- **Viagem impossível para locais atípicos que levam à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de \<*account name*> inscrição de uma viagem impossível para \<*location*> , um local atípico. 
    
    Em seguida, a conta \<*account name*> partilhou \<*number of*> ficheiros únicos numa única sessão.

- **Impossível viajar para locais atípicos que levam ao ransomware na cloud app**
    
    Este alerta é uma indicação de um evento de \<*account name*> inscrição de uma viagem impossível para \<*location*> , um local atípico. 
    
    Em seguida, a conta \<*account name*> carregou \<*number of*> ficheiros e apagou um total de \<*number of*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Atividade de inscrição para localização desconhecida seguida de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de sinal para alertas de localização desconhecidos da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security.

- **Evento de entrada de um local desconhecido que conduz à exfiltração da caixa de correio online Exchange Online**
    
    Este alerta é uma indicação de um evento de entrada por \<*account name*> \<*location*> , um local desconhecido, seguido de uma regra de reencaminhamento de caixa de entrada suspeita foi definido na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<*account name*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails recebidos para o endereço externo \<*email address*> . 

- **Evento de inscrição a partir de um local desconhecido que leva a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> \<*location*> por, de um local desconhecido. 
    
    Em seguida, a conta \<*account name*> realizada sobre \<*number of*> as atividades administrativas numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> \<*location*> por, de um local desconhecido. 
    
    Em seguida, a conta \<*account name*> apagou \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> \<*location*> por, de um local desconhecido. 
    
    Em seguida, a conta \<*account name*> descarregou sobre \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva à personificação do Office 365**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> \<*location*> por, de um local desconhecido.
    
    Em seguida, a conta \<*account name*> personificada sobre \<*number of*> contas diferentes numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> \<*location*> por, de um local desconhecido. 
    
    Em seguida, a conta \<*account name*> partilhou \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva ao ransomware na aplicação cloud**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> \<*location*> por, de um local desconhecido. 
    
    Em seguida, a conta \<*account name*> carregou \<*number of*> ficheiros e apagou um total de \<*number of*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Atividade de inscrição de dispositivo infetado seguido de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de sinalização a partir de alertas de dispositivos infetados da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de entrada de um dispositivo infetado que conduz à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de um evento de entrada \<*account name*> de um dispositivo potencialmente infetado com malware, seguido de uma regra de reencaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<*account name*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails recebidos para o endereço externo \<*email address*> . 

- **Evento de inscrição a partir de um dispositivo infetado que leva a atividade administrativa de aplicações em nuvem suspeita**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> por um dispositivo potencialmente infetado com malware.
    
    Em seguida, a conta \<*account name*> realizada sobre \<*number of*> as atividades administrativas numa única sessão.

- **Evento de inscrição a partir de um dispositivo infetado que conduz à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> por um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<*account name*> apagou \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um dispositivo infetado que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> por um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<*account name*> descarregou sobre \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um dispositivo infetado que conduz à personificação do Office 365**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> por um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<*account name*> personificada sobre \<*number of*> contas diferentes numa única sessão.

- **Evento de inscrição a partir de um dispositivo infetado que conduz à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> por um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<*account name*> partilhou \<*number of*> ficheiros únicos numa única sessão.

- **Evento de início de saúde a partir de um dispositivo infetado que leva ao ransomware na aplicação cloud**
    
    Este alerta é uma indicação de um evento de inscrição \<*account name*> por um dispositivo potencialmente infetado com malware. 
    
    Em seguida, a conta \<*account name*> carregou \<*number of*> ficheiros e apagou um total de \<*number of*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Atividade de inscrição a partir de endereço IP anónimo seguido de atividade anómala office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de inscrição a partir de alertas de endereços IP anónimos da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de um evento de inscrição através \<*account name*> de um endereço IP de procuração anónimo , seguido de uma regra de \<*IP address*> reencaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<*account name*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails recebidos para o endereço externo \<*email address*> . 

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a atividade administrativa de aplicações em nuvem suspeita**
    
    Este alerta é uma indicação de um evento de inscrição através \<*account name*> de um endereço IP de procuração anónimo \<*IP address*> . 
    
    Em seguida, a conta \<*account name*> realizada sobre \<*number of*> as atividades administrativas numa única sessão.

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à eliminação em massa de ficheiros**
    
    Este alerta é uma indicação de um evento de inscrição através \<*account name*> de um endereço IP de procuração anónimo \<*IP address*> . 
    
    Em seguida, a conta \<*account name*> apagou \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um endereço IP anónimo que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição através \<*account name*> de um endereço IP de procuração anónimo \<*IP address*> . 
    
    Em seguida, a conta \<*account name*> descarregou sobre \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à personificação do Office 365**
    
    Este alerta é uma indicação de um evento de inscrição através \<*account name*> de um endereço IP de procuração anónimo \<*IP address*> . 
    
    Em seguida, a conta \<*account name*> personificada sobre \<*number of*> contas diferentes numa única sessão.

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de um evento de inscrição através \<*account name*> de um endereço IP de procuração anónimo \<*IP address*> . 
    
    Em seguida, a conta \<*account name*> partilhou \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição de um endereço IP anónimo para ransomware em app cloud**
    
    Este alerta é uma indicação de um evento de inscrição através \<*account name*> de um endereço IP de procuração anónimo \<*IP address*> . 
    
    Em seguida, a conta \<*account name*> carregou \<*number of*> ficheiros e apagou um total de \<*number of*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Atividade de inscrição do utilizador com credenciais vazadas seguida de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de sing-in do utilizador com alertas de credenciais vazados da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de entrada do utilizador com credenciais vazadas que conduzam à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de que o evento de inscrição através de \<*account name*> credenciais vazadas usadas, seguido de uma regra de reencaminhamento de caixa de entrada suspeita foi definido na caixa de entrada de um utilizador. 
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O utilizador \<*account name*> criou ou atualizou uma regra de reencaminhamento de caixa de entrada que reencaminha todos os e-mails recebidos para o endereço externo \<*email address*> . 

- **Evento de inscrição de utilizador com credenciais vazadas que levam a atividade administrativa de aplicações em nuvem suspeita**
    
    Este alerta é uma indicação de que o evento de inscrição é usado por \<*account name*> credenciais vazadas.
    
    Em seguida, a conta \<*account name*> realizada sobre \<*number of*> as atividades administrativas numa única sessão.

- **Evento de inscrição do Utilizador com credenciais vazadas que conduzem à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de que o evento de inscrição é usado por \<*account name*> credenciais vazadas.
    
    Em seguida, a conta \<*account name*> apagou \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição do Utilizador com credenciais vazadas que levam ao download de ficheiros em massa**
    
    Este alerta é uma indicação de que o evento de inscrição é usado por \<*account name*> credenciais vazadas.
    
    Em seguida, a conta \<*account name*> descarregou sobre \<*number of*> ficheiros únicos numa única sessão.

- **Evento de inscrição do utilizador com credenciais vazadas que conduzem à personificação do Office 365**
    
    Este alerta é uma indicação de que o evento de inscrição é usado por \<*account name*> credenciais vazadas. 
    
    Em seguida, a conta \<*account name*> personificada sobre \<*number of*> contas diferentes numa única sessão.

- **Evento de inscrição do Utilizador com credenciais vazadas que conduzem à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de que o evento de inscrição é usado por \<*account name*> credenciais vazadas.
    
    Em seguida, a conta \<*account name*> partilhou \<*number of*> ficheiros únicos numa única sessão.

- **Evento de início de s nota do Utilizador com credenciais vazadas para ransomware em app cloud**
    
    Este alerta é uma indicação de que o evento de inscrição é usado por \<*account name*> credenciais vazadas. 
    
    Em seguida, a conta \<*account name*> carregou \<*number of*> ficheiros e apagou um total de \<*number of*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu mais sobre a deteção avançada de ataques em vários estágios, pode estar interessado no seguinte quickstart para aprender a obter visibilidade nos seus dados e potenciais ameaças: [Começar com o Azure Sentinel](quickstart-get-visibility.md).

Se está pronto para investigar os incidentes que são criados para si, consulte o seguinte tutorial: [Investigue incidentes com Azure Sentinel.](tutorial-investigate-cases.md)

