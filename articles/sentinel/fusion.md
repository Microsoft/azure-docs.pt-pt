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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 23e116eba6393f834b3368901d4440e668b16fca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724290"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Deteção avançada de ataques em vários palcos em Azure Sentinel

> [!IMPORTANT]
> Algumas deteções de fusão (ver as indicadas abaixo) estão atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Ao utilizar a tecnologia Fusion baseada na aprendizagem automática, o Azure Sentinel pode detetar automaticamente ataques de vários estágios, identificando combinações de comportamentos anómalos e atividades suspeitas que são observadas em várias fases da cadeia de morte. Com base nestas descobertas, a Azure Sentinel gera incidentes que de outra forma seriam difíceis de apanhar. Estes incidentes compreendem dois ou mais alertas ou atividades. Por design, estes incidentes são de baixo volume, alta fidelidade e alta gravidade.

Personalizada para o seu ambiente, esta tecnologia de deteção não só reduz as taxas falsas positivas como também pode detetar ataques com informações limitadas ou em falta.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Configuração da deteção avançada de ataques em várias fases

Esta deteção é ativada por padrão em Azure Sentinel. Para verificar o estado, ou desativá-lo no caso de estar a utilizar uma solução alternativa para criar incidentes baseados em vários alertas, utilize as seguintes instruções:

1. Se ainda não o fez, inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Navegue para **Azure Sentinel**  >  **Configuration**  >  **Analytics**

1. Selecione **as regras Ative** e, em seguida, localize **a Deteção avançada de ataques multiestatos** na coluna **NAME** filtrando a lista para o tipo de regra **de fusão.** Verifique se a coluna **STATUS** confirma se esta deteção está ativada ou desativada.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="{alt-text}":::

1. Para alterar o estado, selecione esta entrada e na lâmina **avançada de deteção de ataques multiestage,** selecione **Editar**.

1. Na lâmina do assistente de **criação regra,** a alteração de estado é selecionada automaticamente para si, por isso selecione **Seguinte: Revisão** e, em seguida, **Guarde**. 

 Uma vez que o tipo de regra **de fusão** contém apenas uma regra que não pode ser modificada, os modelos de regra não são aplicáveis a este tipo de regra.

> [!NOTE]
> A Azure Sentinel utiliza atualmente 30 dias de dados históricos para treinar os sistemas de aprendizagem automática. Estes dados são sempre encriptados utilizando as chaves da Microsoft à medida que passam pelo pipeline de aprendizagem automática. No entanto, os dados de formação não são encriptados utilizando [as Chaves Geridas pelo Cliente (CMK)](customer-managed-keys.md) se tiver ativado a CMK no seu espaço de trabalho Azure Sentinel. Para excluir a Fusão, navegue para **Azure Sentinel** \> **Configuration** \> **Analytics Ative rules \> Advanced \> Multistage Attack Detection** e na coluna **Status,** selecione **Desativar.**

## <a name="attack-detection-scenarios"></a>Cenários de deteção de ataques

A secção seguinte lista os tipos de cenários de correlação, agrupados pela classificação de ameaças, que a Azure Sentinel procura para usar a tecnologia Fusion.

Como mencionado acima, uma vez que a Fusion correlaciona vários alertas de segurança de vários produtos para detetar ataques avançados de vários pontos, as deteções bem sucedidas de Fusão são apresentadas como **incidentes de fusão** na página de **Incidentes** do Sentinela Azure, e não como **alertas** na tabela **alertas de segurança** em **Registos.**

Para permitir estes cenários de deteção de ataques movidos a fusão, quaisquer fontes de dados listadas devem ser ingeridas utilizando os conectores de dados Azure Sentinel associados.

> [!NOTE]
> Alguns destes cenários estão em **PREVIEW**. Serão tão indicados.

## <a name="compute-resource-abuse"></a>Abuso de recursos computacional

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Múltiplas atividades de criação de VM após o sinal de Azure Ative Directory suspeito
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Acesso Inicial, Impacto 

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Sequestro de Recursos (T1496)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que um número anómalo de VMs foi criado numa única sessão após um sinal suspeito para uma conta AD Azure. Este tipo de alerta indica, com um elevado grau de confiança, que a conta indicada na descrição do incidente de Fusão foi comprometida e usada para criar novos VMs para fins não autorizados, como executar operações de mineração de criptomoedas. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de múltiplas atividades de criação de VM são:

- **Viagem impossível para um local atípico que leva a múltiplas atividades de criação de VM**

- **Evento de inscrição a partir de um local desconhecido que leva a múltiplas atividades de criação de VM**

- **Evento de inscrição a partir de um dispositivo infetado que conduz a múltiplas atividades de criação de VM**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a múltiplas atividades de criação de VM**

- **Evento de inscrição do utilizador com credenciais vazadas que conduzem a múltiplas atividades de criação de VM**

## <a name="credential-harvesting-new-threat-classification"></a>Colheita credencial (classificação de novas ameaças)

### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Execução de ferramenta de roubo de credencial maliciosa após sinal suspeito

**MITRE ATT&táticas CK:** Acesso Inicial, Acesso Credencial

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Dumping credencial de OS (T1003)

**Fontes de conector de dados:** Azure Ative Directory Identity Protection, Microsoft Defender for Endpoint

**Descrição:** Incidentes de fusão deste tipo indicam que uma ferramenta de roubo de credenciais conhecida foi executada na sequência de um sinal de AD Azure suspeito. Isto fornece uma indicação de alta confiança de que a conta de utilizador notada na descrição do alerta foi comprometida e pode ter usado com sucesso uma ferramenta como **Mimikatz** para recolher credenciais como chaves, palavras-passe de texto simples e/ou hashes de palavra-passe do sistema. As credenciais colhidas podem permitir que um intruso aceda a dados sensíveis, aumente os privilégios e/ou se mova lateralmente através da rede. As permutações de alertas de entrada de Ad Azure suspeitos com o alerta de ferramenta de roubo de credencial maliciosa são:

- **Viagem impossível para locais atípicos que levam à execução de ferramentas de roubo de credenciais maliciosas**

- **Evento de entrada de um local desconhecido que conduz à execução de ferramentas de roubo de credenciais maliciosas**

- **Evento de entrada de um dispositivo infetado que conduz à execução de ferramentas de roubo de credenciais maliciosas**

- **Evento de entrada a partir de um endereço IP anónimo que conduz à execução de ferramentas de roubo de credenciais maliciosas**

- **Evento de entrada de utilizador com credenciais vazadas que conduzem à execução de ferramenta de roubo de credencial maliciosa**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Suspeita de atividade de roubo de credencial após sessão suspeita

**MITRE ATT&táticas CK:** Acesso Inicial, Acesso Credencial

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Credenciais de Lojas de Passwords (T1555), Dumping Credencial DE OS (T1003)

**Fontes de conector de dados:** Azure Ative Directory Identity Protection, Microsoft Defender for Endpoint

**Descrição:** Incidentes de fusão deste tipo indicam que a atividade associada a padrões de roubo credencial ocorreu na sequência de um sinal de AD Azure suspeito. Isto fornece uma indicação de alta confiança de que a conta de utilizador indicada na descrição do alerta foi comprometida e usada para roubar credenciais como chaves, palavras-passe de texto simples, hashes de palavra-passe, e assim por diante. As credenciais roubadas podem permitir que um intruso aceda a dados sensíveis, aumente os privilégios e/ou se mova lateralmente através da rede. As permutações de alertas suspeitos de ad AD com o alerta de atividade de roubo credencial são:

- **Viagem impossível para locais atípicos que levam a suspeitas de atividade de roubo de credenciais**

- **Evento de inscrição a partir de um local desconhecido que leva a suspeita de atividade de roubo de credenciais**

- **Evento de inscrição a partir de um dispositivo infetado que conduz a suspeita de atividade de roubo de credenciais**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a suspeita de atividade de roubo de credenciais**

- **Evento de inscrição de utilizador com credenciais vazadas que conduzam a suspeitas de atividade de roubo de credenciais**

## <a name="crypto-mining-new-threat-classification"></a>Cripto-mineração (Classificação de nova ameaça)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Atividade cripto-mineira após o sign-in suspeito

**MITRE ATT&táticas CK:** Acesso Inicial, Acesso Credencial

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Sequestro de Recursos (T1496)

**Fontes de conector de dados:** Azure Ative Directory Identity Protection, Azure Defender (Azure Security Center)

**Descrição:** Incidentes de fusão deste tipo indicam atividade cripto-mineração associada a um sinal suspeito de uma conta AD Azure. Isto fornece uma indicação de alta confiança de que a conta de utilizador indicada na descrição do alerta foi comprometida e foi usada para sequestrar recursos no seu ambiente para extrair criptomoeda. Isto pode passar fome os seus recursos de poder de computação e/ou resultar em faturas de utilização em nuvem significativamente mais altas do que o esperado. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de atividade de cripto-mineração são:  

- **Viagem impossível para locais atípicos que conduzem à atividade cripto-mineira**

- **Evento de inscrição a partir de um local desconhecido que leva à atividade cripto-mineração**

- **Evento de inscrição a partir de um dispositivo infetado que conduz à atividade de cripto-mineração**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à atividade de cripto-mineração**

- **Evento de inscrição do utilizador com credenciais vazadas que conduzem à atividade de cripto-mineração**

## <a name="data-exfiltration"></a>Transferência de dados não autorizada

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Exfiltração da caixa de correio do Office 365 na sequência de um sinal de AD Azure suspeito

**MITRE ATT&táticas CK:** Acesso Inicial, Exfiltração, Coleção

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Recolha de E-mail (T1114), Exfiltração Automatizada (T1020)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que uma regra de reencaminhamento de caixa de entrada suspeita foi definida na caixa de entrada de um utilizador na sequência de um início de súming suspeito para uma conta AZure AD. Esta indicação proporciona uma grande confiança de que a conta do utilizador (indicada na descrição do incidente de Fusão) foi comprometida e que foi usada para exfiltrar dados da rede da sua organização, permitindo uma regra de reencaminhamento de caixas de correio sem o conhecimento do verdadeiro utilizador. As permutações de alertas de entrada de Azure AD suspeitos com o alerta de exfiltração da caixa de correio do Office 365 são:

- **Viagem impossível para um local atípico que leva à exfiltração da caixa de correio do Office 365**

- **Evento de inscrição a partir de um local desconhecido que conduz à exfiltração da caixa de correio do Office 365**

- **Evento de entrada de um dispositivo infetado que conduz à exfiltração da caixa de correio do Office 365**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à exfiltração da caixa de correio do Office 365**

- **Evento de entrada do utilizador com credenciais vazadas que conduzam à exfiltração da caixa de correio do Office 365**

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Download de ficheiros em massa após o sinal de Ad Azure suspeito

**MITRE ATT&táticas CK:** Acesso Inicial, Exfiltração

**Técnicas MITRE ATT&CK:** Conta Válida (T1078)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que um número anómalo de ficheiros foi descarregado por um utilizador na sequência de um início de sação suspeito para uma conta AD Azure. Esta indicação fornece uma alta confiança de que a conta indicada na descrição do incidente de Fusão foi comprometida e foi usada para exfiltrar dados da rede da sua organização. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de descarregamento de ficheiros em massa são:  

- **Viagem impossível para um local atípico que leva ao download de ficheiros em massa**

- **Evento de inscrição a partir de um local desconhecido que leva ao download de ficheiros em massa**

- **Evento de inscrição a partir de um dispositivo infetado que leva ao download de ficheiros em massa**

- **Evento de inscrição a partir de um IP anónimo que leva ao download de ficheiros em massa**

- **Evento de inscrição do utilizador com credenciais vazadas que levam ao download de ficheiros em massa**

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Partilha de ficheiros em massa após a assinatura suspeita da AD Azure

**MITRE ATT&táticas CK:** Acesso Inicial, Exfiltração

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Exfiltração sobre o Serviço Web (T1567)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que uma série de ficheiros acima de um determinado limiar foram partilhados com outros na sequência de um sinal suspeito para uma conta AD Azure. Esta indicação proporciona uma grande confiança de que a conta indicada na descrição do incidente de Fusão foi comprometida e usada para exfiltrar dados da rede da sua organização através da partilha de ficheiros como documentos, folhas de cálculo, etc., com utilizadores não autorizados para fins maliciosos. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de partilha de ficheiros em massa são:  

- **Viagem impossível para um local atípico que leva à partilha de ficheiros em massa**

- **Evento de inscrição a partir de um local desconhecido que leva à partilha de ficheiros em massa**

- **Evento de inscrição a partir de um dispositivo infetado que conduz à partilha de ficheiros em massa**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à partilha de ficheiros em massa**

- **Evento de inscrição do utilizador com credenciais vazadas que conduzem à partilha de ficheiros em massa**

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Regras suspeitas de manipulação de caixas de entrada definidas após o sinal de Ad Azure suspeito
Este cenário pertence a duas classificações de ameaça nesta lista: **exfiltração de dados** e **movimento lateral**. Por uma questão de clareza, aparece em ambas as secções.

Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Acesso Inicial, Movimento Lateral, Exfiltração

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Phishing De Lança Interna (T1534)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que as regras anómalas da caixa de entrada foram definidas na caixa de entrada de um utilizador na sequência de um início de súming suspeito numa conta AZure AD. Isto fornece uma indicação de alta confiança de que a conta indicada na descrição do incidente de Fusão foi comprometida e foi usada para manipular as regras da caixa de entrada de e-mail do utilizador para fins maliciosos. Isto pode ser uma tentativa de um intruso de exfiltrar dados da rede da organização. Em alternativa, o intruso pode estar a tentar gerar e-mails de phishing dentro da organização (contornando mecanismos de deteção de phishing direcionados para o e-mail de fontes externas) com o objetivo de se mover lateralmente, obtendo acesso a contas adicionais de utilizador e/ou privilegiadas. As permutações de alertas de entrada de Azure AD suspeitos com o alerta de regras suspeitas de manipulação de caixas de entrada são:  

- **Viagem impossível para um local atípico levando a regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de um local desconhecido que conduz a regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada a partir de um dispositivo infetado que conduz a regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada a partir de um endereço IP anónimo que conduz a regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de utilizador com credenciais vazadas que conduzam a regra de manipulação de caixa de entrada suspeita**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Múltiplos relatórios de bi de energia partilham atividades após o sinal suspeito de Azure AD 
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Acesso Inicial, Exfiltração 

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Exfiltração sobre o Serviço Web (T1567)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que um número anómalo de relatórios do Power BI foram partilhados numa única sessão após um sinal suspeito para uma conta AD Azure. Esta indicação fornece uma alta confiança de que a conta indicada na descrição do incidente de Fusão foi comprometida e foi usada para exfiltrar dados da rede da sua organização, partilhando relatórios de Power BI com utilizadores não autorizados para fins maliciosos. As permutações de alertas de sinal de Azure AD suspeitos com as múltiplas atividades de partilha de relatórios do Power BI são:  

- **Viagem impossível para um local atípico que leva a múltiplas atividades de partilha de relatórios do Power BI**

- **Evento de inscrição a partir de um local desconhecido que leva a múltiplas atividades de partilha de relatórios do Power BI**

- **Evento de inscrição a partir de um dispositivo infetado que conduz a múltiplas atividades de partilha de relatórios do Power BI**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a múltiplas atividades de partilha de relatórios do Power BI**

- **Evento de inscrição de utilizador com credenciais vazadas que levam a múltiplas atividades de partilha de relatórios do Power BI**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Relatório de bi de energia suspeito partilha após a assinatura suspeita de Azure AD
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Acesso Inicial, Exfiltração 

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Exfiltração sobre o Serviço Web (T1567)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que uma atividade suspeita de partilha de relatórios do Power BI ocorreu na sequência de um sinal suspeito para uma conta AD Azure. A atividade de partilha foi identificada como suspeita porque o relatório do Power BI continha informações sensíveis identificadas através do processamento de linguagem natural, e porque foi partilhada com um endereço de e-mail externo, publicada na web, ou entregue como instantâneo para um endereço de e-mail subscrito externamente. Este alerta indica com grande confiança que a conta indicada na descrição do incidente de Fusão foi comprometida e foi usada para exfiltrar dados sensíveis da sua organização, partilhando relatórios power bi com utilizadores não autorizados para fins maliciosos. As permutações de alertas de sinal de Azure AD suspeitos com a partilha suspeita de relatório do Power BI são:  

- **Viagem impossível para um local atípico levando à partilha suspeita de relatório do Power BI**

- **Evento de inscrição a partir de um local desconhecido que leva à partilha suspeita de relatório do Power BI**

- **Evento de inscrição a partir de um dispositivo infetado que leva à partilha suspeita de relatório do Power BI**

- **Evento de inscrição a partir de um endereço IP anónimo que leva à partilha suspeita de relatório do Power BI**

- **Evento de inscrição de utilizador com credenciais vazadas que levam à partilha suspeita de relatório do Power BI**

## <a name="data-destruction"></a>Destruição de dados

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Eliminação de ficheiros em massa após a assinatura suspeita da AD Azure

**MITRE ATT&táticas CK:** Acesso Inicial, Impacto

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Destruição de Dados (T1485)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que um número anómalo de ficheiros únicos foi eliminado na sequência de um início de sação suspeito numa conta AD Azure. Isto fornece uma indicação de que a conta indicada na descrição do incidente de Fusão pode ter sido comprometida e foi usada para destruir dados para fins maliciosos. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de eliminação de ficheiros em massa são:  

- **Viagem impossível para um local atípico que leva à eliminação de ficheiros em massa**

- **Evento de inscrição a partir de um local desconhecido que leva à eliminação de ficheiros em massa**

- **Evento de inscrição a partir de um dispositivo infetado que conduz à eliminação de ficheiros em massa**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à eliminação em massa de ficheiros**

- **Evento de inscrição do utilizador com credenciais vazadas que conduzem à eliminação de ficheiros em massa**

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Atividade suspeita de eliminação de e-mails após a assinatura suspeita de Azure AD
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Acesso Inicial, Impacto 

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Destruição de Dados (T1485)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que um número anómalo de e-mails foi eliminado numa única sessão após um início de sessão suspeito numa conta AD Azure. Isto fornece uma indicação de que a conta indicada na descrição do incidente de Fusão pode ter sido comprometida e foi usada para destruir dados para fins maliciosos, tais como prejudicar a organização ou esconder atividade de e-mail relacionada com spam. As permutações de alertas de sinal de Azure AD suspeitos com o alerta suspeito de atividade de eliminação de e-mails são:   

- **Viagem impossível para um local atípico que leva a atividade suspeita de eliminação de e-mails**

- **Evento de inscrição a partir de um local desconhecido que leva a atividade suspeita de eliminação de e-mails**

- **Evento de inscrição a partir de um dispositivo infetado que conduz a atividade suspeita de eliminação de e-mails**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a atividade suspeita de eliminação de e-mails**

- **Evento de inscrição do utilizador com credenciais vazadas que conduzem a atividade de eliminação de emails suspeitos**

## <a name="denial-of-service"></a>Denial-of-service

### <a name="multiple-vm-delete-activities-following-suspicious-azure-ad-sign-in"></a>Múltiplas atividades de eliminação de VM após o sinal de Ad Azure suspeito
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Acesso Inicial, Impacto

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Negação de Serviço endpoint (T1499)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que um número anómalo de VMs foi eliminado numa única sessão após um sinal suspeito para uma conta AD Azure. Esta indicação fornece uma alta confiança de que a conta notada na descrição do incidente de Fusão foi comprometida e foi usada para tentar perturbar ou destruir o ambiente de nuvem da organização. As permutações de alertas de sinal de Ad Ad suspeitos com o alerta de várias atividades de eliminação de VM são:  

- **Viagem impossível para um local atípico que leva a múltiplas atividades de eliminação de VM**

- **Evento de inscrição a partir de um local desconhecido que leva a múltiplas atividades de eliminação de VM**

- **Evento de inscrição a partir de um dispositivo infetado que conduz a múltiplas atividades de eliminação de VM**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a múltiplas atividades de eliminação de VM**

- **Evento de inscrição do utilizador com credenciais vazadas que conduzam a múltiplas atividades de eliminação de VM**

## <a name="lateral-movement"></a>Movimento lateral

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Imitação do Office 365 após a assinatura suspeita da Azure AD

**MITRE ATT&táticas CK:** Acesso Inicial, Movimento Lateral

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Phishing De Lança Interna (T1534)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que um número anómalo de ações de imitação ocorreu na sequência de um sinal suspeito de uma conta AD Azure. Em alguns softwares, existem opções que permitem aos utilizadores personificar outros utilizadores. Por exemplo, os serviços de e-mail permitem que os utilizadores autorizem outros utilizadores a enviar e-mails em seu nome. Este alerta indica com maior confiança que a conta notada na descrição do incidente de Fusão foi comprometida e foi usada para realizar atividades de imitação para fins maliciosos, como o envio de e-mails de phishing para distribuição de malware ou movimento lateral. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de imitação do Office 365 são:  

- **Viagem impossível para um local atípico que leva à personificação do Office 365**

- **Evento de inscrição a partir de um local desconhecido que leva à personificação do Office 365**

- **Evento de inscrição a partir de um dispositivo infetado que conduz à personificação do Office 365**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz à personificação do Office 365**

- **Evento de inscrição do utilizador com credenciais vazadas que conduzem à personificação do Office 365**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Regras suspeitas de manipulação de caixas de entrada definidas após o sinal de Ad Azure suspeito
Este cenário pertence a duas classificações de ameaça nesta lista: **movimento lateral** e **exfiltração de dados**. Por uma questão de clareza, aparece em ambas as secções.

Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Acesso Inicial, Movimento Lateral, Exfiltração

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Phishing De Lança Interna (T1534), Exfiltração Automatizada (T1020)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que as regras anómalas da caixa de entrada foram definidas na caixa de entrada de um utilizador na sequência de um início de súming suspeito numa conta AZure AD. Esta indicação fornece uma alta confiança de que a conta indicada na descrição do incidente de Fusão foi comprometida e foi usada para manipular as regras da caixa de entrada de e-mail do utilizador para fins maliciosos. Isto pode ser uma tentativa de um intruso de exfiltrar dados da rede da organização. Em alternativa, o intruso pode estar a tentar gerar e-mails de phishing dentro da organização (contornando mecanismos de deteção de phishing direcionados para o e-mail de fontes externas) com o objetivo de se mover lateralmente, obtendo acesso a contas adicionais de utilizador e/ou privilegiadas. As permutações de alertas de entrada de Azure AD suspeitos com o alerta de regras suspeitas de manipulação de caixas de entrada são:

- **Viagem impossível para um local atípico levando a regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de um local desconhecido que conduz a regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada a partir de um dispositivo infetado que conduz a regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada a partir de um endereço IP anónimo que conduz a regra de manipulação de caixa de entrada suspeita**

- **Evento de entrada de utilizador com credenciais vazadas que conduzam a regra de manipulação de caixa de entrada suspeita**

## <a name="malicious-administrative-activity"></a>Atividade administrativa maliciosa

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Atividade administrativa de aplicação de nuvem suspeita após o sinal suspeito de Azure AD

**MITRE ATT&táticas CK:** Acesso Inicial, Persistência, Evasão de Defesa, Movimento Lateral, Recolha, Exfiltração e Impacto

**Técnicas MITRE ATT&CK:** N/A

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que um número anómalo de atividades administrativas foram realizadas numa única sessão após um sinal de Ad Azure suspeito da mesma conta. Isto fornece uma indicação de que a conta indicada na descrição do incidente de Fusão pode ter sido comprometida e foi usada para fazer qualquer número de ações administrativas não autorizadas com intenção maliciosa. Isto também indica que uma conta com privilégios administrativos pode ter sido comprometida. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de atividade administrativa de aplicações em nuvem suspeitas são:  

- **Viagem impossível para um local atípico levando a atividade administrativa de aplicações em nuvem suspeita**

- **Evento de inscrição a partir de um local desconhecido que leva a atividade administrativa de aplicações de nuvem suspeita**

- **Evento de inscrição a partir de um dispositivo infetado que leva a atividade administrativa de aplicações em nuvem suspeita**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a atividade administrativa de aplicações em nuvem suspeita**

- **Evento de inscrição de utilizador com credenciais vazadas que levam a atividade administrativa de aplicações em nuvem suspeita**

## <a name="malicious-execution-with-legitimate-process"></a>Execução maliciosa com processo legítimo

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>A PowerShell fez uma ligação de rede suspeita, seguida de tráfego anómalo sinalizado pela firewall da Palo Alto Networks.
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Execução

**Técnicas MITRE ATT&CK:** Intérprete de Comando e Scripting (T1059)

**Fontes de conector de dados:** Microsoft Defender para Endpoint (anteriormente Microsoft Defender Advanced Threat Protection, ou MDATP), Palo Alto Networks 

**Descrição:** Incidentes de fusão deste tipo indicam que um pedido de ligação de saída foi feito através de um comando PowerShell, e na sequência disso, a atividade de entrada anómala foi detetada pela Firewall das Redes Palo Alto. Isto fornece uma indicação de que um intruso provavelmente teve acesso à sua rede e está a tentar realizar ações maliciosas. As tentativas de ligação por parte do PowerShell que seguem este padrão podem ser uma indicação da atividade de comando e controlo de malware, pedidos para o descarregamento de malware adicional ou um intruso que estabeleça acesso remoto interativo. Como em todos os ataques de "viver fora da terra", esta atividade pode ser um uso legítimo do PowerShell. No entanto, a execução do comando PowerShell, seguida de uma atividade de firewall de entrada suspeita, aumenta a confiança de que a PowerShell está a ser usada de forma maliciosa e deve ser investigada mais aprofundadamente. Nos registos de Palo Alto, o Azure Sentinel foca-se em [registos de ameaças,](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando são permitidas ameaças (dados suspeitos, ficheiros, inundações, pacotes, digitalizações, spyware, URLs, vírus, vulnerabilidades, vírus, incêndios florestais, incêndios florestais). Consulte também o Registo de Ameaça palo Alto correspondente ao [Tipo de Ameaça/Conteúdo](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de Fusão para mais detalhes de alerta.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Execução remota suspeita do WMI seguida de tráfego anómalo sinalizado pela firewall da Palo Alto Networks
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Execução, Descoberta

**Técnicas MITRE ATT&CK:** Instrumentação de Gestão do Windows (T1047)

**Fontes de conector de dados:** Microsoft Defender para Endpoint (anteriormente MDATP), Palo Alto Networks 

**Descrição:** Incidentes de fusão deste tipo indicam que os comandos da Interface de Gestão do Windows (WMI) foram executados remotamente num sistema, e na sequência disso, a atividade de entrada suspeita foi detetada pela Firewall das Redes Palo Alto. Isto fornece uma indicação de que um intruso pode ter tido acesso à sua rede e está a tentar mover-se lateralmente, aumentar privilégios e/ou executar cargas maliciosas. Como em todos os ataques de "viver fora da terra", esta atividade pode ser um uso legítimo da WMI. No entanto, a execução remota do comando do WMI, seguida de uma atividade de firewall de entrada suspeita, aumenta a confiança de que o WMI está a ser usado de forma maliciosa e deve ser investigado mais aprofundadamente. Nos registos de Palo Alto, o Azure Sentinel foca-se em [registos de ameaças,](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando são permitidas ameaças (dados suspeitos, ficheiros, inundações, pacotes, digitalizações, spyware, URLs, vírus, vulnerabilidades, vírus, incêndios florestais, incêndios florestais). Consulte também o Registo de Ameaça palo Alto correspondente ao [Tipo de Ameaça/Conteúdo](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de Fusão para mais detalhes de alerta.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Linha de comando Suspeito PowerShell após sindução suspeita

**MITRE ATT&táticas CK:** Acesso Inicial, Execução

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Intérprete de Comando e Scripting (T1059)

**Fontes de conector de dados:** Azure Ative Directory Identity Protection, Microsoft Defender for Endpoint (anteriormente MDATP)

**Descrição:** Incidentes de fusão deste tipo indicam que um utilizador executou comandos PowerShell potencialmente maliciosos na sequência de um início de sação suspeito numa conta AD Azure. Isto fornece uma indicação de alta confiança de que a conta indicada na descrição do alerta foi comprometida e foram tomadas outras ações maliciosas. Os atacantes muitas vezes aproveitam o PowerShell para executar cargas maliciosas na memória sem deixar artefactos no disco, de modo a evitar a deteção por mecanismos de segurança baseados em discos, como scanners de vírus. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de comando suspeito da PowerShell são:

- **Viagem impossível para locais atípicos que levam a uma linha de comando suspeita da PowerShell**

- **Evento de inscrição a partir de um local desconhecido que leva a linha de comando suspeita powerShell**

- **Evento de inscrição a partir de um dispositivo infetado que conduz a linha de comando suspeita powerShell**

- **Evento de inscrição a partir de um endereço IP anónimo que conduz a linha de comando suspeita powerShell**

- **Evento de inscrição do utilizador com credenciais vazadas que conduzem a linha de comando suspeitas da PowerShell**

## <a name="malware-c2-or-download"></a>Malware C2 ou download

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Pedido de rede ao serviço de anonimização TOR seguido de tráfego anómalo sinalizado pela firewall da Palo Alto Networks.
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Comando e Controlo

**Técnicas MITRE ATT&CK:** Canal encriptado (T1573), Proxy (T1090)

**Fontes de conector de dados:** Microsoft Defender para Endpoint (anteriormente MDATP), Palo Alto Networks 

**Descrição:** Incidentes de fusão deste tipo indicam que foi feito um pedido de ligação de saída ao serviço de anonimização TOR, e na sequência disso, a atividade de entrada anómala foi detetada pela Firewall das Redes Palo Alto. Isto fornece uma indicação de que um intruso provavelmente teve acesso à sua rede e está a tentar ocultar as suas ações e intenções. As ligações à rede TOR seguindo este padrão podem ser uma indicação de atividade de comando e controlo de malware, pedidos para o descarregamento de malware adicional, ou um intruso que estabeleça acesso remoto interativo. Nos registos de Palo Alto, o Azure Sentinel foca-se em [registos de ameaças,](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando são permitidas ameaças (dados suspeitos, ficheiros, inundações, pacotes, digitalizações, spyware, URLs, vírus, vulnerabilidades, vírus, incêndios florestais, incêndios florestais). Consulte também o Registo de Ameaça palo Alto correspondente ao [Tipo de Ameaça/Conteúdo](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de Fusão para mais detalhes de alerta.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Ligação de saída ao IP com histórico de tentativas de acesso não autorizadas seguidas de tráfego anómalo sinalizado pela firewall da Palo Alto Networks
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Comando e Controlo

**Técnicas MITRE ATT&CK:** Não aplicável

**Fontes de conector de dados:** Microsoft Defender para Endpoint (anteriormente MDATP), Palo Alto Networks 

**Descrição:** Incidentes de fusão deste tipo indicam que foi estabelecida uma ligação de saída a um endereço IP com um histórico de tentativas de acesso não autorizadas, e na sequência disso, a atividade anómala foi detetada pela Firewall das Redes Palo Alto. Isto fornece uma indicação de que um intruso provavelmente teve acesso à sua rede. As tentativas de ligação seguindo este padrão podem ser uma indicação de atividade de comando e controlo de malware, pedidos para o descarregamento de malware adicional, ou um intruso que estabeleça acesso remoto interativo. Nos registos de Palo Alto, o Azure Sentinel foca-se em [registos de ameaças,](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando são permitidas ameaças (dados suspeitos, ficheiros, inundações, pacotes, digitalizações, spyware, URLs, vírus, vulnerabilidades, vírus, incêndios florestais, incêndios florestais). Consulte também o Registo de Ameaça palo Alto correspondente ao [Tipo de Ameaça/Conteúdo](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de Fusão para mais detalhes de alerta.

## <a name="ransomware"></a>Ransomware

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Execução de ransomware após a assinatura suspeita de Azure AD

**MITRE ATT&táticas CK:** Acesso Inicial, Impacto

**Técnicas MITRE ATT&CK:** Conta Válida (T1078), Dados Encriptados para Impacto (T1486)

**Fontes de conector de dados:** Microsoft Cloud App Security, Azure Ative Directory Identity Protection

**Descrição:** Incidentes de fusão deste tipo indicam que o comportamento anómalo do utilizador indicando um ataque de ransomware foi detetado na sequência de um início de sação suspeito numa conta AZure AD. Esta indicação fornece uma alta confiança de que a conta indicada na descrição do incidente de Fusão foi comprometida e foi usada para encriptar dados para efeitos de extorquir o titular dos dados ou negar o acesso do titular dos dados aos seus dados. As permutações de alertas de sinal de Azure AD suspeitos com o alerta de execução do ransomware são:  

- **Viagem impossível para um local atípico que leva ao ransomware na aplicação cloud**

- **Evento de inscrição a partir de um local desconhecido que leva ao ransomware na aplicação cloud**

- **Evento de início de saúde a partir de um dispositivo infetado que leva ao ransomware na aplicação cloud**

- **Evento de inscrição a partir de um endereço IP anónimo que leva ao ransomware na aplicação cloud**

- **Evento de início de s nota de utilizadores com credenciais vazadas que levam ao ransomware na aplicação cloud**

## <a name="remote-exploitation"></a>Exploração remota

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Suspeito de uso de quadro de ataque seguido de tráfego anómalo sinalizado por firewall da Rede Palo Alto
Este cenário encontra-se atualmente em **PREVIEW**.

**MITRE ATT&táticas CK:** Acesso Inicial, Execução, Movimento Lateral, Escalada de Privilégio

**Técnicas MITRE ATT&CK:** Explorar Public-Facing Aplicação (T1190), Exploração para Execução de Clientes (T1203), Exploração de Serviços Remotos (T1210), Exploração para Escalada de Privilégios (T1068)

**Fontes de conector de dados:** Microsoft Defender para Endpoint (anteriormente MDATP), Palo Alto Networks 

**Descrição:** Incidentes de fusão deste tipo indicam que foram detetadas utilizações não padrão de protocolos, assemelhando-se ao uso de estruturas de ataque como o Metasploit, e, na sequência disso, a atividade de entrada suspeita foi detetada pela Firewall das Redes palo Alto. Esta pode ser uma indicação inicial de que um intruso explorou um serviço para aceder aos recursos da sua rede ou que um intruso já teve acesso e está a tentar explorar ainda mais os sistemas/serviços disponíveis para mover-se lateralmente e/ou aumentar os privilégios. Nos registos de Palo Alto, o Azure Sentinel foca-se em [registos de ameaças,](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)e o tráfego é considerado suspeito quando são permitidas ameaças (dados suspeitos, ficheiros, inundações, pacotes, digitalizações, spyware, URLs, vírus, vulnerabilidades, vírus, incêndios florestais, incêndios florestais). Consulte também o Registo de Ameaça palo Alto correspondente ao [Tipo de Ameaça/Conteúdo](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) listado na descrição do incidente de Fusão para mais detalhes de alerta.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu mais sobre a deteção avançada de ataques em vários estágios, pode estar interessado no seguinte quickstart para aprender a obter visibilidade nos seus dados e potenciais ameaças: [Começar com o Azure Sentinel](quickstart-get-visibility.md).

Se está pronto para investigar os incidentes que são criados para si, consulte o seguinte tutorial: [Investigue incidentes com Azure Sentinel.](tutorial-investigate-cases.md)

