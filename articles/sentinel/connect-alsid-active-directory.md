---
title: Ligue o seu Alsid para Diretório Ativo ao | Azure Sentinel Microsoft Docs
description: Aprenda a usar o conector Alsid para Ative Directory para puxar os troncos alsid para o Azure Sentinel. Ver dados da Alsid em livros, criar alertas e melhorar a investigação.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566797"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Ligue o seu Alsid para Diretório Ativo (AD) a Azure Sentinel

> [!IMPORTANT]
> O conector Alsid for Ative Directory encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

Este artigo explica como ligar o seu Alsid para solução AD ao Azure Sentinel. O conector de dados alsid para diretório ativo permite-lhe conectar facilmente o seu Alsid para registos AD com Azure Sentinel, para que possa ver os dados em livros, questioná-lo para criar alertas personalizados e incorporá-lo para melhorar a investigação. A integração entre a Alsid para AD e o Azure Sentinel faz uso de um servidor Syslog com o agente Log Analytics instalado. Também usa um parser de log personalizado baseado numa função Kusto.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter permissão para escrever no espaço de trabalho do Azure Sentinel.

- A sua solução Alsid para AD deve ser configurada para exportar registos via Syslog.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Envie a Alsid para registos de AD para Azure Sentinel através do agente Syslog

Configure a Alsid para a AD para encaminhar mensagens Syslog para o seu espaço de trabalho Azure Sentinel através do agente Syslog.

1. No menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. A partir da galeria **de conectores de dados,** selecione o conector **Alsid para Ative Directory (Preview)** e, em seguida, **Abra a página do conector**.

1. Siga as instruções na página do conector **Alsid para o Diretório Ativo:**

    1. Configurar um servidor Syslog

        1. Se ainda não tiver um, crie um servidor Linux Syslog para a Alsid para a AD enviar registos para. Azure Sentinel suporta os **daemons rsyslog** **e syslog-ng.** 

        1. Configure o seu servidor Syslog para a saída do Alsid para registos de AD num ficheiro separado.

    1. Configure a Alsid para a AD para enviar registos para o seu servidor Syslog

        1. No seu **portal Alsid for AD,** vá ao *Sistema,* *Configuração* e depois *Syslog*. A partir daí pode criar um novo alerta Syslog para o seu servidor Syslog. Para o servidor remoto, utilize o endereço IP da máquina Linux em que instalou o agente Linux.

        1. Verifique se os registos estão corretamente recolhidos no seu servidor num ficheiro separado (para o fazer, pode utilizar o botão **de configuração Test** na configuração de alerta *Syslog* em Alsid para AD).

    1. Instale e embarque no agente Log Analytics para Linux

        - Escolha um Azure Linux VM ou uma máquina linux não-Azure (física ou virtual). Siga os links e instruções no ecrã. Consulte [a sua máquina Linux ou o seu aparelho](connect-syslog.md#configure-your-linux-machine-or-appliance) para mais detalhes.

    1. Configure os registos a serem recolhidos pelo agente Log Analytics

        - Selecione as instalações e as severidades na configuração de configurações avançadas do espaço de trabalho.

            1. Clique na configuração de **configurações avançadas do seu espaço de trabalho >** link na página do conector.

            1. No ecrã **de definições avançadas,** selecione **Dados** e, em seguida, **Registos Personalizados**.

            1. Marque a **configuração abaixo da configuração para as minhas máquinas linux** caixa de verificação e clique em **Adicionar**.

            1. Clique **em Escolher O Ficheiro** para carregar uma amostra alsid para o ficheiro Syslog AD a partir da máquina Linux que executa o servidor Syslog e clique em **Seguinte**.

            1. Verifique se **o delimiter de recorde** definido está definido para Nova **linha** e clique em **Seguinte**.

            1. Selecione **Linux** e introduza o caminho do ficheiro para o ficheiro Syslog, clique **+** e, em seguida, **em seguida.**

            1. No tipo de campo Nome *AlsidForADLog* antes do sufixo de _CL e, em seguida, clique em **Fazer**.
    
Pode demorar até 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Registos**, em **Registos Personalizados** na tabela *AlsidForADLog_CL.*

Este conector de dados depende de um parser baseado numa Função Kusto para funcionar como esperado. Utilize os seguintes passos para configurar o **afad_parser** a Função Kusto para utilizar em consultas e livros de trabalho.

1. A partir do menu de navegação Azure Sentinel, selecione **Logs**.

1. Copie a seguinte consulta e cole-a na janela de consulta.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Clique no **Drop-down Save** e clique em **Guardar**. No painel **Save,**

    1. Under **Name**, **insira afad_parser**.

    1. Em **Guardar como**, escolha **Função**.

    1. Em **Alias de Função,** **introduza afad_parser**.

    1. Na **categoria**, insira **funções**.

    1. Clique em **Guardar**.

    As aplicações de função normalmente demoram entre 10 e 15 minutos a ser ativadas.

Agora está pronto para consultar a Alsid para obter dados de AD, introduzindo `afad_parser` na linha superior da janela de consulta.

Consulte o separador **passos seguintes** na página do conector para obter mais amostras de consulta.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar a Alsid para AD ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.
