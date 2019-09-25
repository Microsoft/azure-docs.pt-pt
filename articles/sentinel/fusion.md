---
title: Detecção avançada de ataques de multiestágio no Azure Sentinel
description: Use a tecnologia Fusion no Azure Sentinel para reduzir o fadiga de alertas e criar incidentes acionáveis que se baseiam na detecção avançada de ataques de multiestágio.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/24/2019
ms.author: cabailey
ms.openlocfilehash: e6ddb1b01b705d2a7857682bd84e9482e064a8db
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240073"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Detecção avançada de ataques de multiestágio no Azure Sentinel

Usando a tecnologia de fusão baseada no aprendizado de máquina, o Azure Sentinel pode detectar automaticamente ataques de várias etapas combinando comportamentos anormais e atividades suspeitas que são observadas em vários estágios da cadeia de eliminação. Em seguida, o Azure Sentinel gera incidentes que, de outra forma, seriam muito difíceis de detectar. Esses incidentes encerrarãom dois ou mais alertas ou atividades. Por design, esses incidentes são de baixo volume, alta fidelidade e alta gravidade.

Personalizado para seu ambiente, essa detecção não apenas reduz as taxas de falsos positivos, mas também pode detectar ataques com informações limitadas ou ausentes.

Para obter detalhes sobre os alertas para os cenários com suporte, consulte a seção [cenários com suporte para detecção de ataque de multiestágio](#scenarios-supported-for-advanced-multistage-attack-detection) nesta página.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuração para detecção avançada de ataque de multiestágio

Essa detecção é habilitada por padrão no Azure Sentinel. Para verificar o status ou para desabilitá-lo, talvez porque você esteja usando uma solução alternativa para criar incidentes com base em vários alertas, use as seguintes instruções:

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Navegue até a**análise** de**configuração** >  **do Azure Sentinel** > 

3. Selecione **regras ativas** e localize **detecção avançada de ataque** de várias etapas na coluna **nome** . Verifique a coluna **status** para confirmar se essa detecção está habilitada ou desabilitada.

4. Para alterar o status, selecione essa entrada e, na folha **detecção avançada de ataques de multiestágio** , selecione **Editar**.

5. Na folha **Assistente para criação de regras** , a alteração de status é selecionada automaticamente para você; portanto **, selecione Avançar: Examine**e, em seguida, **salve**. 

Os modelos de regra não são aplicáveis para a detecção avançada de ataques de multiestágio.

## <a name="scenarios-supported-for-advanced-multistage-attack-detection"></a>Cenários com suporte para detecção avançada de ataques de multiestágio

Usando a detecção avançada de ataques de multiestágio, o Azure Sentinel dá suporte aos seguintes cenários que combinam eventos de anomalias de Azure Active Directory Identity Protection e Microsoft Cloud App Security:

- [Viagem impossível para o local atípicos seguido pela atividade anômala do Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Atividade de entrada para um local não familiar seguido pela atividade anômala do Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Atividade de entrada do dispositivo infectado seguido pela atividade anormal do Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Atividade de entrada do endereço IP anônimo seguido pela atividade anormal do Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Atividade de entrada do usuário com credenciais vazadas, seguidas pela atividade anormal do Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Você deve ter o [conector de dados Azure ad Identity Protection](connect-azure-ad-identity-protection.md) e os conectores de [Cloud app Security](connect-cloud-app-security.md) configurados.

Nas descrições a seguir, o Azure Sentinel exibirá o valor real de seus dados representados nesta página como variáveis entre colchetes. Por exemplo, o nome de exibição real de uma conta em \<vez do *nome da conta*> e o número real \<em vez de *número*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Viagem impossível para o local atípicos seguido pela atividade anômala do Office 365

Há sete possíveis incidentes do Azure Sentinel que combinam a viagem impossível a alertas de local atípicos de alertas Azure AD Identity Protection e anormais do Office 365 gerados pelo Microsoft Cloud App Security:

- **Viagem impossível a locais atípicoss que levam ao Office 365 caixa de correio vazamento**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de uma viagem impossível para \<o *local*>, um local atípicos, seguido por uma regra de encaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome \<da *conta*de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todos \<os emails de entrada para o endereço de *email*do endereço externo >.

- **Viagem impossível a locais atípicoss que levam à atividade administrativa suspeita do aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de uma viagem impossível para \<o *local*>, um local atípicos.
    
    Em seguida, o \< *nome da conta*de conta \<> executado sobre o *número*> atividades administrativas em uma única sessão.

- **Viagem impossível a locais atípicoss que levam à exclusão em massa de arquivos**
    
    Esse alerta é uma indicação de um evento \<de entrada pelo *nome da conta*> para \<o *local*>, um local atípicos. 
    
    Em seguida, o \< *nome da conta*de \<conta > *número excluído de*> arquivos exclusivos em uma única sessão.

- **Viagem impossível a locais atípicoss que levam ao download de arquivos em massa**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de uma viagem impossível para \<o *local*>, um local atípicos. 
    
    Em seguida, o \< *nome da conta*de conta \<> baixado sobre o *número de*> arquivos exclusivos em uma única sessão.

- **Viagem impossível a locais atípicoss que levam à representação do Office 365**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de uma viagem impossível para \<o *local*>, um local atípicos. 
    
    Em seguida, o \< *nome da conta*de conta > realizou uma\<quantidade incomum (*número de atividades*>) de atividades de representação em uma única sessão.

- **Viagem impossível a locais atípicoss que levam ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de uma viagem impossível para \<o *local*>, um local atípicos. 
    
    Em seguida, o \< *nome da conta*de conta \<> compartilhado por *número de*> arquivos exclusivos em uma única sessão.

- **Viagem impossível a locais atípicoss que levam ao ransomware no aplicativo em nuvem**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de uma viagem impossível para \<o *local*>, um local atípicos. 
    
    Em seguida, o \< *nome da conta*da \<conta > o *número carregado de arquivos de*> e \<excluiu um número total de arquivos *de*>. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Atividade de entrada para um local não familiar seguido pela atividade anômala do Office 365

Há sete possíveis incidentes do Azure Sentinel que combinam a atividade de entrada para alertas de local desconhecidos de Azure AD Identity Protection e alertas anormais do Office 365 gerados pelo Microsoft Cloud App Security.

- **Evento de entrada de um local desconhecido que leva à caixa de correio do Exchange Online vazamento**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> do \< *local*>, um local desconhecido, seguido por uma regra de encaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome \<da *conta*de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todos \<os emails de entrada para o endereço de *email*do endereço externo >. 

- **Evento de entrada de um local desconhecido que leva à atividade administrativa do aplicativo de nuvem suspeito**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> do \< *local*>, um local desconhecido. 
    
    Em seguida, o \< *nome da conta*de conta \<> executado sobre o *número de*> atividades administrativas em uma única sessão.

- **Evento de entrada de um local não familiar que leva à exclusão de arquivos em massa**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> do \< *local*>, um local desconhecido. 
    
    Em seguida, o \< *nome da conta*de \<conta > *número excluído de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um local desconhecido que leva ao download de arquivo em massa**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> do \< *local*>, um local desconhecido. 
    
    Em seguida, o \< *nome da conta*de conta \<> baixado sobre o *número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um local desconhecido que leva à representação do Office 365**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> do \< *local*>, um local desconhecido.
    
    Em seguida, o \< *nome da conta*de conta > \<representado com o *número de*> contas diferentes em uma única sessão.

- **Evento de entrada de um local desconhecido que leva ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> do \< *local*>, um local desconhecido. 
    
    Em seguida, o \< *nome da conta*de conta \<> compartilhado por *número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um local desconhecido que leva ao ransomware no aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> do \< *local*>, um local desconhecido. 
    
    Em seguida, o \< *nome da conta*da \<conta > o *número carregado de arquivos de*> e \<excluiu um número total de arquivos *de*>. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Atividade de entrada do dispositivo infectado seguido pela atividade anormal do Office 365

Há sete possíveis incidentes do Azure sentinela que combinam a atividade de entrada de alertas de dispositivo infectados dos alertas Azure AD Identity Protection e anormal Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de entrada de um dispositivo infectado que leva à caixa de correio do Office 365 vazamento**
    
    Esse alerta é uma indicação de um evento de entrada por \< *nome de conta*> de um dispositivo potencialmente infectado com malware, seguido por uma regra de encaminhamento de caixa de entrada suspeita definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome \<da *conta*de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todos \<os emails de entrada para o endereço de *email*do endereço externo >. 

- **Evento de entrada de um dispositivo infectado que leva à atividade administrativa suspeita do aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \< *nome de conta*> de um dispositivo potencialmente infectado com malware.
    
    Em seguida, o \< *nome da conta*de conta \<> executado sobre o *número de*> atividades administrativas em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva à exclusão em massa de arquivos**
    
    Esse alerta é uma indicação de um evento de entrada por \< *nome de conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, o \< *nome da conta*de \<conta > *número excluído de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva ao download de arquivo em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \< *nome de conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, o \< *nome da conta*de conta \<> baixado sobre o *número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva à representação do Office 365**
    
    Esse alerta é uma indicação de um evento de entrada por \< *nome de conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, o \< *nome da conta*de conta > \<representado com o *número de*> contas diferentes em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de um evento de entrada por \< *nome de conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, o \< *nome da conta*de conta \<> compartilhado por *número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um dispositivo infectado que leva ao ransomware no aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento de entrada por \< *nome de conta*> de um dispositivo potencialmente infectado com malware. 
    
    Em seguida, o \< *nome da conta*da \<conta > o *número carregado de arquivos de*> e \<excluiu um número total de arquivos *de*>. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Atividade de entrada do endereço IP anônimo seguido pela atividade anormal do Office 365

Há sete possíveis incidentes do Azure Sentinel que combinam a atividade de entrada de alertas de endereço IP anônimo de Azure AD Identity Protection e de alertas anormais do Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de entrada de um endereço IP anônimo que leva à caixa de correio do Office 365 vazamento**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de um endereço IP de proxy \<anônimo >, seguido por uma regra de encaminhamento de caixa de entrada suspeita definida na caixa de entrada de um usuário.
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome \<da *conta*de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todos \<os emails de entrada para o endereço de *email*do endereço externo >. 

- **Evento de entrada de um endereço IP anônimo que leva à atividade administrativa suspeita do aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de um endereço IP de endereço \<IP de proxy anônimo >. 
    
    Em seguida, o \< *nome da conta*de conta \<> executado sobre o *número de*> atividades administrativas em uma única sessão.

- **Evento de entrada de um endereço IP anônimo que leva à exclusão de arquivos em massa**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de um endereço IP de endereço \<IP de proxy anônimo >. 
    
    Em seguida, o \< *nome da conta*de \<conta > *número excluído de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um endereço IP anônimo que leva ao download de arquivo em massa**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de um endereço IP de endereço \<IP de proxy anônimo >. 
    
    Em seguida, o \< *nome da conta*de conta \<> baixado sobre o *número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um endereço IP anônimo que leva à representação do Office 365**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de um endereço IP de endereço \<IP de proxy anônimo >. 
    
    Em seguida, o \< *nome da conta*de conta > \<representado com o *número de*> contas diferentes em uma única sessão.

- **Evento de entrada de um endereço IP anônimo que leva ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de um endereço IP de endereço \<IP de proxy anônimo >. 
    
    Em seguida, o \< *nome da conta*de conta \<> compartilhado por *número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada de um endereço IP anônimo para ransomware no aplicativo de nuvem**
    
    Esse alerta é uma indicação de um evento \<de entrada por *nome de conta*> de um endereço IP de endereço \<IP de proxy anônimo >. 
    
    Em seguida, o \< *nome da conta*da \<conta > o *número carregado de arquivos de*> e \<excluiu um número total de arquivos *de*>. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Atividade de entrada do usuário com credenciais vazadas, seguidas pela atividade anormal do Office 365

Há sete possíveis incidentes do Azure Sentinel que combinam a atividade de entrada do usuário com alertas de credenciais vazadas de alertas Azure AD Identity Protection e anormais do Office 365 gerados pelo Microsoft Cloud App Security:

- **Evento de entrada do usuário com credenciais vazadas que levam à caixa de correio do Office 365 vazamento**
    
    Esse alerta é uma indicação de que o evento de entrada por \< *nome de conta*> usou credenciais vazadas, seguido por uma regra de encaminhamento de caixa de entrada suspeita definida na caixa de entrada de um usuário. 
    
    Isso pode indicar que a conta está comprometida e que a caixa de correio está sendo usada para exfiltrar informações de sua organização. O nome \<da *conta*de usuário > criou ou atualizou uma regra de encaminhamento de caixa de entrada que encaminha todos \<os emails de entrada para o endereço de *email*do endereço externo >. 

- **Evento de entrada do usuário com credenciais vazadas que levam à atividade administrativa do aplicativo de nuvem suspeito**
    
    Esse alerta é uma indicação de que o evento de entrada por \< *nome de conta*> usou credenciais vazadas.
    
    Em seguida, o \< *nome da conta*de conta \<> executado sobre o *número de*> atividades administrativas em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas levando à exclusão de arquivos em massa**
    
    Esse alerta é uma indicação de que o evento de entrada por \< *nome de conta*> usou credenciais vazadas.
    
    Em seguida, o \< *nome da conta*de \<conta > *número excluído de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas levando ao download de arquivo em massa**
    
    Esse alerta é uma indicação de que o evento de entrada por \< *nome de conta*> usou credenciais vazadas.
    
    Em seguida, o \< *nome da conta*de conta \<> baixado sobre o *número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas que levam à representação do Office 365**
    
    Esse alerta é uma indicação de que o evento de entrada por \< *nome de conta*> usou credenciais vazadas. 
    
    Em seguida, o \< *nome da conta*de conta > \<representado com o *número de*> contas diferentes em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas que levam ao compartilhamento de arquivos em massa**
    
    Esse alerta é uma indicação de que o evento de entrada por \< *nome de conta*> usou credenciais vazadas.
    
    Em seguida, o \< *nome da conta*de conta \<> compartilhado por *número de*> arquivos exclusivos em uma única sessão.

- **Evento de entrada do usuário com credenciais vazadas para ransomware no aplicativo de nuvem**
    
    Esse alerta é uma indicação de que o evento de entrada por \< *nome de conta*> usou credenciais vazadas. 
    
    Em seguida, o \< *nome da conta*da \<conta > o *número carregado de arquivos de*> e \<excluiu um número total de arquivos *de*>. 
    
    Esse padrão de atividade é um indício de um ataque potencial de ransomware.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu mais sobre a detecção avançada de ataques de multiestágio, talvez esteja interessado no início rápido a seguir para saber como obter visibilidade de seus dados e ameaças potenciais: [Introdução ao Azure Sentinel](quickstart-get-visibility.md).

Se você estiver pronto para investigar os incidentes que são criados para você, consulte o seguinte tutorial: [Investigue incidentes com o Azure Sentinel](tutorial-investigate-cases.md).

