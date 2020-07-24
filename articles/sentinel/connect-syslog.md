---
title: Ligue os dados do Syslog ao Azure Sentinel ! Microsoft Docs
description: Ligue qualquer aparelho no local que suporte o Syslog ao Azure Sentinel utilizando um agente numa máquina Linux entre o aparelho e o Sentinel. 
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 38e47469723d767561dd778b8f175780ab181fd4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076262"
---
# <a name="connect-your-external-solution-using-syslog"></a>Ligue a sua solução externa utilizando o Syslog

Pode ligar qualquer aparelho no local que suporte o Syslog ao Azure Sentinel. Isto é feito utilizando um agente baseado numa máquina Linux entre o aparelho e o Azure Sentinel. Se a sua máquina Linux estiver em Azure, pode transmitir os registos do seu aparelho ou aplicação para um espaço de trabalho dedicado que cria no Azure e conectá-lo. Se a sua máquina Linux não estiver em Azure, pode transmitir os registos do seu aparelho para um VM ou máquina dedicado nas instalações em que instala o Agente para o Linux. 

> [!NOTE]
> Se o seu aparelho suportar o Syslog CEF, a ligação é mais completa e deve escolher esta opção e seguir as instruções [de ligação dos dados do CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Como funciona

Syslog é um protocolo de registo de eventos que é comum ao Linux. As aplicações enviarão mensagens que podem ser armazenadas na máquina local ou entregues a um colecionador Syslog. Quando o agente Log Analytics do Linux é instalado, configura o daemon Syslog local para transmitir mensagens ao agente. Em seguida, o agente envia a mensagem para o Azure Monitor, onde é criado um registo correspondente.

Para obter mais informações, consulte [fontes de dados do Syslog no Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - O agente pode recolher registos de várias fontes, mas deve ser instalado numa máquina de procuração dedicada.
> - Se pretender apoiar os conectores tanto para o CEF como para o Syslog no mesmo VM, execute os seguintes passos para evitar a duplicação de dados:
>    1. Siga as instruções para [ligar o seu CEF](connect-common-event-format.md).
>    2. Para ligar os dados do Syslog, vá a **Definições**  >  **de Espaço de Trabalho**  >  **Definições Desídus Definições**  >  **Data**  >  **Desíduo Desímanos** de Dados e defina as Instalações e as suas prioridades para que não sejam as mesmas instalações e propriedades utilizadas na sua configuração CEF. <br></br>Se selecionar **Aplicar abaixo a configuração às minhas máquinas,** aplica estas configurações a todos os VMs ligados a este espaço de trabalho.


## <a name="connect-your-syslog-appliance"></a>Ligue o seu aparelho Syslog

1. Em Azure Sentinel, selecione **os conectores de dados** e, em seguida, selecione o conector **Syslog.**

2. Na lâmina **Syslog,** selecione **Abrir a página do conector**.

3. Instale o agente Linux:
    
    - Se a sua máquina virtual Linux estiver em Azure, selecione **Descarregar e instalar o agente na máquina virtual Azure Linux**. Na lâmina **das máquinas Virtuais,** selecione as máquinas virtuais para instalar o agente e, em seguida, clique em **Connect**.
    - Se a sua máquina Linux não estiver no Azure, selecione **Descarregar e instalar o agente na máquina Linux non-Azure**. Na lâmina do **agente direct,** copie o comando para **DOWNLOAD AND ONBOARD AGENT FOR LINUX** e execute-o no seu computador. 
    
   > [!NOTE]
   > Certifique-se de configurar as definições de segurança para estes computadores de acordo com a política de segurança da sua organização. Por exemplo, pode configurar as definições de rede para alinhar com a política de segurança de rede da sua organização e alterar as portas e protocolos no daemon para alinhar com os requisitos de segurança.

4. Selecione **Abra a configuração de configurações avançadas do seu espaço de trabalho**.

5. Na lâmina **de definições avançadas,** selecione **Data**  >  **Syslog**. Em seguida, adicione as instalações para o conector recolher.
    
    Adicione as instalações que o seu aparelho de syslog inclui nos seus cabeçalhos de madeira. Pode ver esta configuração no seu aparelho Syslog em **Syslog-d** na `/etc/rsyslog.d/security-config-omsagent.conf` pasta e em **r-Syslog** a partir de `/etc/syslog-ng/security-config-omsagent.conf` .
    
    Se pretender utilizar a deteção de login anómala SSH com os dados que recolhe, adicione **auth** e **authpriv**. Consulte a [seguinte secção](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) para mais detalhes.

6. Quando adicionar todas as instalações que pretende monitorizar e ajustar quaisquer opções de gravidade para cada uma, selecione a caixa de verificação **Aplicar abaixo da configuração para as minhas máquinas**.

7. Selecione **Guardar**. 

8. No seu aparelho sislog, certifique-se de que está a enviar as instalações que especificou.

9. Para utilizar o esquema relevante no Azure Monitor para os registos de syslog, procure **syslog**.

10. Pode utilizar a função Kusto descrita na [utilização de funções em consultas de registo do Azure Monitor](../azure-monitor/log-query/functions.md) para analisar as suas mensagens Syslog. Pode então guardá-los como uma nova função Log Analytics para usar como um novo tipo de dados.

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

