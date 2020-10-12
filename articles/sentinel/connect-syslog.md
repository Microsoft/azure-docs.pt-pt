---
title: Ligue os dados do Syslog ao Azure Sentinel ! Microsoft Docs
description: Ligue qualquer máquina ou aparelho que suporte o Syslog ao Azure Sentinel utilizando um agente numa máquina Linux entre o aparelho e o Sentinel. 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 7670d00a2dd25961a51d18c50c102e0f92b30975
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88566153"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Recolher dados de fontes baseadas em Linux utilizando o Syslog

Pode transmitir eventos a partir de máquinas ou aparelhos baseados em Linux, syslog-support ou aparelhos para a Azure Sentinel, utilizando o agente Log Analytics para o Linux (anteriormente conhecido como agente OMS). Pode fazê-lo para qualquer máquina que lhe permita instalar o agente Log Analytics diretamente na máquina. O daemon syslog nativo da máquina irá recolher eventos locais dos tipos especificados, e reencaminhá-los localmente para o agente, que irá transmiti-los para o seu espaço de trabalho Log Analytics.

> [!NOTE]
> - Se o seu aparelho suportar **o Formato Comum de Eventos (CEF) sobre o Syslog,** é recolhido um conjunto de dados mais completo e os dados são analisados na recolha. Deve escolher esta opção e seguir as instruções em [Ligar a sua solução externa utilizando CEF](connect-common-event-format.md).
>
> - O Log Analytics suporta a recolha de mensagens enviadas pelos **daemons rsyslog** **ou syslog-ng,** onde o rsyslog é o padrão. O syslog daemon padrão na versão 5 da Red Hat Enterprise Linux (RHEL), CentOS e versão Oracle Linux **(sysklog)** não é suportado para a coleção de eventos syslog. Para recolher dados syslog desta versão destas distribuições, o anão de rsyslog deve ser instalado e configurado para substituir o sysklog.

## <a name="how-it-works"></a>Como funciona

**Syslog** é um protocolo de registo de eventos que é comum ao Linux. Quando o **agente Log Analytics do Linux** é instalado no seu VM ou no seu aparelho, a rotina de instalação configura o daemon Syslog local para transmitir mensagens ao agente na porta TCP 25224. Em seguida, o agente envia a mensagem para o seu espaço de trabalho Log Analytics sobre HTTPS, onde é analisada numa entrada de registo de eventos na tabela Syslog em **Azure Sentinel > Logs**.

Para obter mais informações, consulte [fontes de dados do Syslog no Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Coleção Configurar syslog

### <a name="configure-your-linux-machine-or-appliance"></a>Configure a sua máquina ou aparelho Linux

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, selecione o conector **Syslog.**

1. Na lâmina **Syslog,** selecione **Abrir a página do conector**.

1. Instale o agente Linux. Em **Escolha onde instalar o agente:**
    
    **Para um Azure Linux VM:**
      
    1. Selecione **Instalar o agente na máquina virtual Azure Linux**.
    
    1. Clique no **agente de instalação de descarregamento & para máquinas virtuais Azure Linux >** link. 
    
    1. Na lâmina das **máquinas Virtuais,** clique numa máquina virtual para instalar o agente e, em seguida, clique em **Connect**. Repita este passo para cada VM que pretende ligar.
    
    **Para qualquer outra máquina Linux:**

    1. Selecione **o agente de instalação numa máquina linux não-Azure**

    1. Clique no **Agente de instalação de descarregamento & para máquinas não-Azure Linux >** link. 

    1. Na lâmina **de gestão** de agentes, clique no separador **servidores Linux** e, em seguida, copie o comando para **Download e agente de bordo para Linux** e execute-o na sua máquina Linux. 
    
   > [!NOTE]
   > Certifique-se de configurar as definições de segurança para estes computadores de acordo com a política de segurança da sua organização. Por exemplo, pode configurar as definições de rede para alinhar com a política de segurança de rede da sua organização e alterar as portas e protocolos no daemon para alinhar com os requisitos de segurança.

### <a name="configure-the-log-analytics-agent"></a>Configure o agente Log Analytics

1. Na parte inferior da lâmina do conector Syslog, clique na configuração de **configuração avançada do espaço de trabalho >** ligação.

1. Na lâmina **de definições avançadas,** selecione **Data**  >  **Syslog**. Em seguida, adicione as instalações para o conector recolher.
    
    - Adicione as instalações que o seu aparelho de syslog inclui nos seus cabeçalhos de madeira. 
    
    - Se pretender utilizar a deteção de login anómala SSH com os dados que recolhe, adicione **auth** e **authpriv**. Consulte a [seguinte secção](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) para mais detalhes.

1. Quando adicionar todas as instalações que pretende monitorizar e ajustar quaisquer opções de gravidade para cada uma, selecione a caixa de verificação **Aplicar abaixo da configuração para as minhas máquinas**.

1. Selecione **Guardar**. 

1. No seu VM ou aparelho, certifique-se de que está a enviar as instalações que especificou.

1. Para consultar os dados de registo de syslog em **Logs,** digite `Syslog` na janela de consulta.

1. Pode utilizar os parâmetros de consulta descritos na [utilização de funções em consultas de registo do Azure Monitor](../azure-monitor/log-query/functions.md) para analisar as suas mensagens Syslog. Em seguida, pode guardar a consulta como uma nova função Log Analytics e usá-la como um novo tipo de dados.

> [!NOTE]
> **Usando a mesma máquina para encaminhar mensagens simples de Syslog *e* CEF**
>
>
> Pode utilizar a máquina de [reencaminhamento cef](connect-cef-agent.md) existente para recolher e encaminhar registos de fontes simples do Syslog. No entanto, deve executar os seguintes passos para evitar o envio de eventos em ambos os formatos para Azure Sentinel, pois isso resultará na duplicação de eventos.
>
>    Tendo já configurado [a recolha de dados a partir das suas fontes CEF,](connect-common-event-format.md)e tendo configurado o agente Log Analytics como acima:
>
> 1. Em cada máquina que envia registos no formato CEF, tem de editar o ficheiro de configuração Syslog para remover as instalações que estão a ser utilizadas para enviar mensagens CEF. Desta forma, as instalações que são enviadas no CEF também não serão enviadas em Syslog. Consulte [o Configure Syslog no agente Linux](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) para obter instruções detalhadas sobre como fazê-lo.
>
> 1. Tem de executar o seguinte comando nessas máquinas para desativar a sincronização do agente com a configuração Syslog em Azure Sentinel. Isto garante que a alteração de configuração efetuada no passo anterior não seja substituída.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`


### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configure o conector Syslog para deteção anómala de login SSH

> [!IMPORTANT]
> A deteção de login anómala SSH está atualmente em visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel pode aplicar machine learning (ML) aos dados do syslog para identificar a atividade de login anómala Secure Shell (SSH). Os cenários incluem:

- Viagem impossível – quando dois eventos de login bem-sucedidos ocorrem a partir de dois locais que são impossíveis de alcançar dentro do prazo dos dois eventos de login.
- Localização inesperada – o local de onde ocorreu um evento de login bem sucedido é suspeito. Por exemplo, a localização não foi vista recentemente.
 
Esta deteção requer uma configuração específica do conector de dados Syslog: 

1. Para o passo 5 no procedimento anterior, certifique-se de que tanto **a auth** como **authpriv** são selecionados como instalações para monitorizar. Mantenha as definições predefinidos para as opções de gravidade, de modo a que todas sejam selecionadas. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![Instalações necessárias para deteção anómala de login SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Permitir a recolha de informações syslog. Em seguida, navegue até **Azure Sentinel - Logs**, e copie e cole a seguinte consulta:
    
    ```console
    Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    ```
    
    Altere o **intervalo de tempo,** se necessário, e selecione **Executar**.
    
    Se a contagem resultante for zero, confirme a configuração do conector e que os computadores monitorizados têm uma atividade de login bem sucedida durante o período de tempo especificado para a sua consulta.
    
    Se a contagem resultante for superior a zero, os seus dados de syslog são adequados para a deteção anómala de login SSH. Ativa esta deteção a partir de modelos de regras de **análise**  >   **Rule templates**  >  **(pré-visualização) Deteção de Login anómala**.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos Syslog no local ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

