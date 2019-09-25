---
title: Conectar dados do syslog ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do syslog ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: b2be563efa3c09cffaf14dec2b871f3881af1a7a
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240040"
---
# <a name="connect-your-external-solution-using-syslog"></a>Conectar sua solução externa usando o syslog

Você pode conectar qualquer dispositivo local que dê suporte a syslog para o Azure Sentinel. Isso é feito usando um agente baseado em um computador Linux entre o dispositivo e o Azure Sentinel. Se seu computador Linux estiver no Azure, você poderá transmitir os logs de seu dispositivo ou aplicativo para um espaço de trabalho dedicado criado no Azure e conectá-lo. Se o seu computador Linux não estiver no Azure, você poderá transmitir os logs de seu dispositivo para uma VM local dedicada ou computador no qual você instala o agente para Linux. 

> [!NOTE]
> Se o seu dispositivo der suporte ao syslog CEF, a conexão será mais completa e você deverá escolher essa opção e seguir as instruções em [conectando dados do CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Como funciona

Syslog é um protocolo de registo de eventos que é comum para Linux. Aplicativos enviará as mensagens que podem ser armazenadas no computador local ou entregues a um recoletor de Syslog. Quando o agente de Log Analytics para Linux é instalado, ele configura o daemon do syslog local para encaminhar mensagens para o agente. Em seguida, o agente envia a mensagem para Azure Monitor onde um registro correspondente é criado.

Para obter mais informações, consulte [syslog Data Sources in Azure monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> O agente pode coletar logs de várias fontes, mas deve ser instalado no computador proxy dedicado.

## <a name="connect-your-syslog-appliance"></a>Conectar seu dispositivo syslog

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione o conector **syslog** .

2. Na folha **syslog** , selecione **abrir a página conector**.

3. Instale o agente do Linux:
    
    - Se sua máquina virtual do Linux estiver no Azure, selecione **baixar e instalar o agente na máquina virtual Linux do Azure**. Na folha **máquinas virtuais** , selecione as máquinas virtuais nas quais instalar o agente e, em seguida, clique em **conectar**.
    - Se seu computador Linux não estiver no Azure, selecione **baixar e instalar o agente no computador Linux não Azure**. Na folha **agente direto** , copie o comando para o **download e o agente integrado para Linux** e execute-o no computador. 
    
   > [!NOTE]
   > Certifique-se de definir as configurações de segurança para esses computadores de acordo com a política de segurança da sua organização. Por exemplo, você pode definir as configurações de rede para se alinhar com a política de segurança de rede da sua organização e alterar as portas e protocolos no daemon para se alinhar com os requisitos de segurança.

4. Selecione **abrir a configuração de configurações avançadas do espaço de trabalho**.

5. Na folha **Configurações avançadas** , selecione**syslog**de **dados** > . Em seguida, adicione os recursos para o conector coletar.
    
    Adicione os recursos que seu dispositivo de syslog inclui em seus cabeçalhos de log. Você pode ver essa configuração em seu dispositivo de syslog no **syslog-d** na `/etc/rsyslog.d/security-config-omsagent.conf` pasta e em **r-syslog** de `/etc/syslog-ng/security-config-omsagent.conf`.
    
    Se você quiser usar a detecção de logon de SSH anormal com os dados coletados, adicione **auth** e **authpriv**. Consulte a [seção a seguir](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) para obter detalhes adicionais.

6. Depois de adicionar todos os recursos que você deseja monitorar e ajustar as opções de severidade para cada um deles, marque a caixa de seleção **aplicar a configuração abaixo a meus computadores**.

7. Selecione **Guardar**. 

8. No seu dispositivo de syslog, certifique-se de que você está enviando os recursos que você especificou.

9. Para usar o esquema relevante no Azure Monitor para os logs de syslog, pesquise por **syslog**.

10. Você pode usar a função Kusto descrita em [usando funções em Azure monitor consultas de log](../azure-monitor/log-query/functions.md) para analisar suas mensagens de syslog. Em seguida, você pode salvá-los como uma nova função Log Analytics para usar como um novo tipo de dados.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configurar o conector syslog para detecção de logon de SSH anormal

> [!IMPORTANT]
> A detecção de logon SSH anômala está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel pode aplicar o ML (aprendizado de máquina) aos dados do syslog para identificar a atividade de logon do SSH (Secure Shell anormal). Os cenários incluem:

- Viagem impossível – quando dois eventos de logon bem-sucedidos ocorrem em dois locais que são impossíveis de alcançar dentro do período de dois eventos de logon.
- Local inesperado – o local de onde ocorreu um evento de logon bem-sucedido é suspeito. Por exemplo, o local não foi visto recentemente.
 
Essa detecção requer uma configuração específica do conector de dados syslog: 

1. Para a etapa 5 no procedimento anterior, verifique se **auth** e **authpriv** estão selecionados como instalações a serem monitoradas. Mantenha as configurações padrão para as opções de gravidade, para que todas estejam selecionadas. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![Instalações necessárias para a detecção de logon de SSH anormal](./media/connect-syslog/facilities-ssh-detection.png)

2. Aguarde tempo suficiente para que as informações de syslog sejam coletadas. Em seguida, navegue até **Azure Sentinel-logs**e copie e cole a seguinte consulta:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Altere o **intervalo de tempo** , se necessário, e selecione **executar**.
    
    Se a contagem resultante for zero, confirme a configuração do conector e se os computadores monitorados têm atividade de logon bem-sucedida para o período de tempo especificado para a consulta.
    
    Se a contagem resultante for maior que zero, os dados do syslog serão adequados para a detecção de logon de SSH anormal. Você habilita essa detecção na**detecção de logon do ssh anômala (versão prévia)** dos**modelos** > de regra de **análise** >  .

## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar os dispositivos locais do syslog ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
