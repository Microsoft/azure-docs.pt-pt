---
title: Deteção avançada de ataques multiestágios em Azure Sentinel
description: Utilize a tecnologia Fusion em Azure Sentinel para reduzir a fadiga de alerta e criar incidentes atores baseados na deteção avançada de ataques multiestágios.
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
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587928"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Deteção avançada de ataques multiestágios em Azure Sentinel


> [!IMPORTANT]
> Algumas funcionalidades de Fusion no Azure Sentinel estão atualmente em pré-visualização pública.
> Estas funcionalidades são fornecidas sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .



Ao utilizar a tecnologia Fusion baseada na aprendizagem automática, o Azure Sentinel pode detetar automaticamente ataques em vários palcos, combinando comportamentos anómalos e atividades suspeitas que são observadas em várias fases da cadeia de morte. O Azure Sentinel gera então incidentes que, de outra forma, seriam muito difíceis de apanhar. Estes incidentes encerram dois ou mais alertas ou atividades. Por design, estes incidentes são de baixo volume, alta fidelidade e alta gravidade.

Personalizada para o seu ambiente, esta deteção não só reduz as taxas falsas positivas, como também pode detetar ataques com informações limitadas ou em falta.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuração da deteção avançada de ataques em várias fases

Esta deteção é ativada por padrão no Azure Sentinel. Para verificar o estado, ou para desativá-lo talvez porque está a usar uma solução alternativa para criar incidentes com base em múltiplos alertas, utilize as seguintes instruções:

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Navegue para**Analítica** de**Configuração** >  **Digital** > 

3. Selecione **regras ativas** e localize a **Deteção avançada de ataques multiestágios** na coluna **NAME.** Verifique a coluna **STATUS** para confirmar se esta deteção está ativada ou desativada.

4. Para alterar o estado, selecione esta entrada e na lâmina de deteção de **ataques multiestágio avançado,** selecione **Editar**.

5. Na lâmina do assistente de **criação da Regra,** a alteração de estado é selecionada automaticamente para si, por isso selecione **Next: Review**, e, em seguida, **Guardar**. 

Os modelos de regras não são aplicáveis para a deteção avançada de ataques multiestágios.

> [!NOTE]
> O Azure Sentinel utiliza atualmente 30 dias de dados históricos para treinar os sistemas de aprendizagem automática. Estes dados são sempre encriptados utilizando as chaves da Microsoft à medida que passam pelo pipeline de aprendizagem automática. No entanto, os dados de formação não são encriptados utilizando chaves geridas pelo [cliente (CMK)](customer-managed-keys.md) se ativar a CMK no seu espaço de trabalho Azure Sentinel. Para não se fundir, navegue para **azure Sentinel** \> **Configuração** \> ** \> Analytics Ative regras \> Avançadas de Deteção** de Ataques Multistage e na coluna **'Status',** selecione **Disable.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusão usando Redes Palo Alto e Microsoft Defender ATP

Estes cenários combinam dois dos registos fundamentais utilizados pelos analistas de segurança: registos de firewall da Palo Alto Networks e registos de deteção de pontos finais do MICROSOFT Defender ATP. Em todos os cenários listados abaixo, é detetada uma atividade suspeita no ponto final que envolve um endereço IP externo, então, este é seguido por tráfego anómalo do endereço IP externo de volta para a firewall. Nos registos de Palo Alto, o Azure Sentinel foca-se em [registos](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)de ameaças , e o tráfego é considerado suspeito quando as ameaças são permitidas (dados suspeitos, ficheiros, inundações, pacotes, digitalizações, spyware, URLs, vírus, vulnerabilidades, vírus de incêndio silvestre, incêndios florestais).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Pedido de rede ao serviço de anoinização TOR seguido de tráfego anómalo sinalizado pela firewall palo Alto Networks.

Neste cenário, o Azure Sentinel deteta primeiro um alerta de que a Microsoft Defender Advanced Threat Protection detetou um pedido de rede a um serviço de anoinização TOR que conduz a atividades anómalas. Isto foi iniciado sob conta {nome da conta} com SID ID {sid} em {time}. O endereço IP de saída para a ligação foi {IndividualIp}.
Em seguida, a atividade invulgar foi detetada pela Firewall palo Alto Networks em {TimeGenerated}. Isto indica que o tráfego malicioso entrou na sua rede O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário encontra-se atualmente em pré-visualização pública.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>A PowerShell fez uma ligação de rede suspeita seguida de tráfego anómalo sinalizado pela firewall palo Alto Networks.

Neste cenário, o Azure Sentinel deteta primeiro um alerta de que o Microsoft Defender Advanced Threat Protection detetou que a PowerShell fez uma ligação de rede suspeita levando a uma atividade anómala que foi detetada por uma Firewall de Rede Palo Alto. Isto foi iniciado pela conta {nome da conta} com SID ID {sid} em {time}. O endereço IP de saída para a ligação foi {IndividualIp}. Em seguida, a atividade invulgar foi detetada pela Firewall palo Alto Networks em {TimeGenerated}. Isto indica que o tráfego malicioso entrou na sua rede. O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário encontra-se atualmente em pré-visualização pública.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Ligação de saída à IP com histórico de tentativas de acesso não autorizadas seguida de tráfego anómalo sinalizado pela firewall palo Alto Networks

Neste cenário, o Azure Sentinel deteta um alerta de que a Microsoft Defender Advanced Threat Protection detetou uma ligação de saída a um endereço IP com um histórico de tentativas de acesso não autorizadas que levam a que a atividade anómala seja detetada pelo Palo Alto. Redes Firewall. Isto foi iniciado pela conta {nome da conta} com SID ID {sid} em {time}. O endereço IP de saída para a ligação foi {IndividualIp}. Depois disso, a atividade invulgar foi detetada pela Firewall palo Alto Networks em {TimeGenerated}. Isto indica que o tráfego malicioso entrou na sua rede. O endereço IP de destino para o tráfego de rede é {DestinationIP}.

Este cenário encontra-se atualmente em pré-visualização pública.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusão usando proteção de identidade e segurança de aplicações da Microsoft Cloud

Utilizando a deteção avançada de ataques multiestágios, o Azure Sentinel suporta os seguintes cenários que combinam eventos de anomalia saturado de Proteção de Identidade de Diretório Ativo Azure e Segurança de Aplicações microsoft Cloud:

- [Viagem impossível para localização atípica seguida por atividade anómala do Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Atividade de inscrição para localização desconhecida seguida de atividade anómala do Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Atividade de login de dispositivo infetado seguida de atividade anómala do Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Atividade de login a partir de endereço IP anónimo seguido de atividade anómala do Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Atividade de login de utilizador com credenciais vazadas seguida de atividade anómala do Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Deve ter configurado o [conector](connect-azure-ad-identity-protection.md) de dados de Proteção de Identidade Azure AD e os conectores [cloud App Security.](connect-cloud-app-security.md)

Nas descrições que se seguem, o Azure Sentinel apresentará o valor real dos seus dados que está representado nesta página como variáveis nos parênteses. Por exemplo, o nome real de \<exibição de uma conta \<em vez do nome da *conta*>, e o número real em vez de *número*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Viagem impossível para localização atípica seguida por atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam viagens impossíveis a alertas de localização atípicos da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Viagem impossível a locais atípicos que levam à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de \<um evento de inscrição por nome de *conta*> de uma viagem impossível para \<o *local*>, uma localização atípica, seguida de uma regra de encaminhamento suspeita de caixa de entrada foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O \< *nome* da conta de utilizador> criou ou atualizou uma regra \<de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço de *endereço* externo>.

- **Viagem impossível para locais atípicos que conduzem a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de \<um evento de inscrição por nome de *conta*> de uma viagem impossível para \<o *local*>, um local atípico.
    
    Em seguida, \<o nome \<da *conta*> realizado sobre *o número*> atividades administrativas numa única sessão.

- **Viagem impossível para locais atípicos que levam à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento \<de inscrição por nome de *conta*> para *localização*>, uma localização atípica. 
    
    Em seguida, \<o nome \<da *conta*> número eliminado *de*> ficheiros únicos numa única sessão.

- **Viagem impossível para locais atípicos que levam ao download de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento de inscrição por nome de *conta*> de uma viagem impossível para \<o *local*>, um local atípico. 
    
    Em seguida, \<o nome da \< *conta*> descarregado sobre o *número de*> ficheiros únicos numa única sessão.

- **Viagem impossível a locais atípicos que levam à personificação do Office 365**
    
    Este alerta é uma indicação de \<um evento de inscrição por nome de *conta*> de uma viagem impossível para \<o *local*>, um local atípico. 
    
    Em seguida, o \< *nome* da conta\<> realizou um valor incomum *(número de atividades*>) de atividades de personificação numa única sessão.

- **Viagem impossível para locais atípicos que levam à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento de inscrição por nome de *conta*> de uma viagem impossível para \<o *local*>, um local atípico. 
    
    Em seguida, \<o nome \<da *conta*> partilhado sobre o *número de*> ficheiros únicos numa única sessão.

- **Viagem impossível a locais atípicos que levam ao ransomware na aplicação cloud**
    
    Este alerta é uma indicação de \<um evento de inscrição por nome de *conta*> de uma viagem impossível para \<o *local*>, um local atípico. 
    
    Em seguida, \<o nome \<da *conta*> número de ficheiros *>* carregados e apagou um total \< *de*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Atividade de inscrição para localização desconhecida seguida de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de login para alertas de localização desconhecidos da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security.

- **Evento de entrada a partir de um local desconhecido que leva à exfiltração de caixa de correio Online**
    
    Este alerta é uma indicação de \<um evento \<de entrada por *conta*> de *localização*>, um local desconhecido, seguido de uma regra de encaminhamento suspeita da caixa de entrada foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O \< *nome* da conta de utilizador> criou ou atualizou uma regra \<de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço de *endereço* externo>. 

- **Evento de inscrição a partir de um local desconhecido que conduz a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de \<um evento \<de inscrição por nome de *conta*> a partir de> *local,* um local desconhecido. 
    
    Em seguida, \<o nome \<da *conta*> realizado sobre o *número de*> atividades administrativas numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento \<de inscrição por nome de *conta*> a partir de> *local,* um local desconhecido. 
    
    Em seguida, \<o nome \<da *conta*> número eliminado *de*> ficheiros únicos numa única sessão.

- **Evento de início de sessão a partir de um local desconhecido que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento \<de inscrição por nome de *conta*> a partir de> *local,* um local desconhecido. 
    
    Em seguida, \<o nome da \< *conta*> descarregado sobre o *número de*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um local desconhecido que leva à personificação do Office 365**
    
    Este alerta é uma indicação de \<um evento \<de inscrição por nome de *conta*> a partir de> *local,* um local desconhecido.
    
    Em seguida, \<o nome da \< *conta*> personificado sobre o *número de*> contas diferentes numa única sessão.

- **Evento de início de sessão a partir de um local desconhecido que leva à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento \<de inscrição por nome de *conta*> a partir de> *local,* um local desconhecido. 
    
    Em seguida, \<o nome \<da *conta*> partilhado sobre o *número de*> ficheiros únicos numa única sessão.

- **Evento de sessão a partir de um local desconhecido que leva ao ransomware na aplicação cloud**
    
    Este alerta é uma indicação de \<um evento \<de inscrição por nome de *conta*> a partir de> *local,* um local desconhecido. 
    
    Em seguida, \<o nome \<da *conta*> número de ficheiros *>* carregados e apagou um total \< *de*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Atividade de login de dispositivo infetado seguida de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de login a partir de alertas de dispositivos infetados da Azure AD Identity Protection e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de entrada a partir de um dispositivo infetado que leva à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um dispositivo potencialmente infetado com malware, seguido de uma regra de encaminhamento suspeita da caixa de entrada foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O \< *nome* da conta de utilizador> criou ou atualizou uma regra \<de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço de *endereço* externo>. 

- **Evento de inscrição a partir de um dispositivo infetado que conduz a atividade administrativa suspeita de aplicações em nuvem**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um dispositivo potencialmente infetado com malware.
    
    Em seguida, \<o nome \<da *conta*> realizado sobre o *número de*> atividades administrativas numa única sessão.

- **Evento de início de sessão a partir de um dispositivo infetado que conduz à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, \<o nome \<da *conta*> número eliminado *de*> ficheiros únicos numa única sessão.

- **Evento de início de sessão a partir de um dispositivo infetado que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, \<o nome da \< *conta*> descarregado sobre o *número de*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um dispositivo infetado que leva à personificação do Office 365**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, \<o nome da \< *conta*> personificado sobre o *número de*> contas diferentes numa única sessão.

- **Evento de início de sessão a partir de um dispositivo infetado que conduz à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, \<o nome \<da *conta*> partilhado sobre o *número de*> ficheiros únicos numa única sessão.

- **Evento de sessão a partir de um dispositivo infetado que leva ao ransomware na aplicação cloud**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um dispositivo potencialmente infetado com malware. 
    
    Em seguida, \<o nome \<da *conta*> número de ficheiros *>* carregados e apagou um total \< *de*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Atividade de login a partir de endereço IP anónimo seguido de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de entrada a partir de alertas de endereçoip anónimos da Proteção de Identidade Azure AD e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de entrada a partir de um endereço IP anónimo que conduz à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um \< *endereço IP* de endereço IP de procuração anónimo>, seguido de uma regra de encaminhamento suspeita da caixa de entrada foi definida na caixa de entrada de um utilizador.
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O \< *nome* da conta de utilizador> criou ou atualizou uma regra \<de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço de *endereço* externo>. 

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um \< *endereço IP* de endereço IP de procuração anónimo>. 
    
    Em seguida, \<o nome \<da *conta*> realizado sobre o *número de*> atividades administrativas numa única sessão.

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um \< *endereço IP* de endereço IP de procuração anónimo>. 
    
    Em seguida, \<o nome \<da *conta*> número eliminado *de*> ficheiros únicos numa única sessão.

- **Evento de início de sessão a partir de um endereço IP anónimo que leva ao download de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um \< *endereço IP* de endereço IP de procuração anónimo>. 
    
    Em seguida, \<o nome da \< *conta*> descarregado sobre o *número de*> ficheiros únicos numa única sessão.

- **Evento de inscrição a partir de um endereço IP anónimo que leva à personificação do Office 365**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um \< *endereço IP* de endereço IP de procuração anónimo>. 
    
    Em seguida, \<o nome da \< *conta*> personificado sobre o *número de*> contas diferentes numa única sessão.

- **Evento de início de sessão a partir de um endereço IP anónimo que conduz à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um \< *endereço IP* de endereço IP de procuração anónimo>. 
    
    Em seguida, \<o nome \<da *conta*> partilhado sobre o *número de*> ficheiros únicos numa única sessão.

- **Evento de sessão a partir de um endereço IP anónimo para ransomware na aplicação cloud**
    
    Este alerta é uma indicação de \<um evento de registo por nome de *conta*> de um \< *endereço IP* de endereço IP de procuração anónimo>. 
    
    Em seguida, \<o nome \<da *conta*> número de ficheiros *>* carregados e apagou um total \< *de*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Atividade de login de utilizador com credenciais vazadas seguida de atividade anómala do Office 365

Existem sete possíveis incidentes do Azure Sentinel que combinam atividade de login do utilizador com alertas de credenciais vazados da Proteção de Identidade Da Azure AD e alertas anómalos do Office 365 gerados pela Microsoft Cloud App Security:

- **Evento de entrada de utilizador com credenciais vazadas que levam à exfiltração da caixa de correio do Office 365**
    
    Este alerta é uma indicação de \<que o evento de entrada por nome de *conta*> usou credenciais vazadas, seguida de uma regra de encaminhamento suspeita da caixa de entrada na caixa de entrada de um utilizador. 
    
    Isto pode indicar que a conta está comprometida e que a caixa de correio está a ser usada para exfiltrar informações da sua organização. O \< *nome* da conta de utilizador> criou ou atualizou uma regra \<de reencaminhamento de caixa de entrada que reencaminha todos os e-mails de entrada para o endereço de *endereço* externo>. 

- **Evento de inscrição de utilizador com credenciais vazadas que conduzem a atividade administrativa de aplicações de nuvem suspeita**
    
    Este alerta é uma indicação de \<que o evento de inscrição por nome de *conta*> usou credenciais vazadas.
    
    Em seguida, \<o nome \<da *conta*> realizado sobre o *número de*> atividades administrativas numa única sessão.

- **Evento de início de sessão do Utilizador com credenciais vazadas que conduzem à eliminação de ficheiros em massa**
    
    Este alerta é uma indicação de \<que o evento de inscrição por nome de *conta*> usou credenciais vazadas.
    
    Em seguida, \<o nome \<da *conta*> número eliminado *de*> ficheiros únicos numa única sessão.

- **Evento de início de sessão do Utilizador com credenciais vazadas que levam ao download de ficheiros em massa**
    
    Este alerta é uma indicação de \<que o evento de inscrição por nome de *conta*> usou credenciais vazadas.
    
    Em seguida, \<o nome da \< *conta*> descarregado sobre o *número de*> ficheiros únicos numa única sessão.

- **Evento de inscrição de utilizador com credenciais vazadas que conduzem à personificação do Office 365**
    
    Este alerta é uma indicação de \<que o evento de inscrição por nome de *conta*> usou credenciais vazadas. 
    
    Em seguida, \<o nome da \< *conta*> personificado sobre o *número de*> contas diferentes numa única sessão.

- **Evento de início de sessão do Utilizador com credenciais vazadas que conduzem à partilha de ficheiros em massa**
    
    Este alerta é uma indicação de \<que o evento de inscrição por nome de *conta*> usou credenciais vazadas.
    
    Em seguida, \<o nome \<da *conta*> partilhado sobre o *número de*> ficheiros únicos numa única sessão.

- **Evento de início de sessão do Utilizador com credenciais vazadas para ransomware na aplicação cloud**
    
    Este alerta é uma indicação de \<que o evento de inscrição por nome de *conta*> usou credenciais vazadas. 
    
    Em seguida, \<o nome \<da *conta*> número de ficheiros *>* carregados e apagou um total \< *de*> ficheiros. 
    
    Este padrão de atividade é indicativo de um potencial ataque de ransomware.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu mais sobre a deteção avançada de ataques multipalco, pode estar interessado no seguinte início rápido para aprender a obter visibilidade nos seus dados e ameaças potenciais: [Comece com O Sentinel.](quickstart-get-visibility.md)

Se estiver pronto para investigar os incidentes que são criados para si, consulte o seguinte tutorial: [Investigue incidentes com Azure Sentinel](tutorial-investigate-cases.md).

