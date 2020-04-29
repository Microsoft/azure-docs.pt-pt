---
title: Ligue os dados do Syslog ao Azure Sentinel [ Microsoft Docs
description: Saiba como ligar dados do Syslog ao Azure Sentinel.
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
ms.openlocfilehash: 73fd55fc24fd94dc88bba2f591c32480f77c7d5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588081"
---
# <a name="connect-your-external-solution-using-syslog"></a>Ligue a sua solução externa usando o Syslog

Pode ligar qualquer aparelho no local que suporte o Syslog ao Azure Sentinel. Isto é feito utilizando um agente baseado numa máquina Linux entre o aparelho e o Azure Sentinel. Se a sua máquina Linux estiver em Azure, pode transmitir os registos do seu aparelho ou aplicação para um espaço de trabalho dedicado que cria em Azure e conectá-lo. Se a sua máquina Linux não estiver em Azure, pode transmitir os troncos do seu aparelho para um vídeo ou máquina dedicado nas instalações, no qual instala o Agente para o Linux. 

> [!NOTE]
> Se o seu aparelho suportar o Syslog CEF, a ligação está mais completa e deve escolher esta opção e seguir as instruções de [Ligação dos dados do CEF](connect-common-event-format.md).

## <a name="how-it-works"></a>Como funciona

Syslog é um protocolo de registo de eventos que é comum ao Linux. As aplicações enviarão mensagens que podem ser armazenadas na máquina local ou entregues a um colecionador syslog. Quando o agente Log Analytics para o Linux é instalado, ele confunde o daemon syslog local para encaminhar mensagens para o agente. O agente envia então a mensagem para o Monitor Azure onde é criado um registo correspondente.

Para mais informações, consulte [fontes de dados syslog no Monitor Azure](../azure-monitor/platform/data-sources-syslog.md).

> [!NOTE]
> - O agente pode recolher registos de várias fontes, mas deve ser instalado em máquina de procuração dedicada.
> - Se pretender suportar conectores tanto para CEF como para Syslog no mesmo VM, execute os seguintes passos para evitar duplicar dados:
>    1. Siga as instruções para [ligar o seu CEF](connect-common-event-format.md).
>    2. Para ligar os dados do Syslog, aceda às **definições** > **Workspace settings** > do Workspace**Configurações** > Avançadas De**Dados** > **Syslog** e detetete as Instalações e as suas prioridades para que não sejam as mesmas instalações e propriedades que utilizou na configuração do CEF. <br></br>Se selecionar **Aplicar abaixo da configuração nas minhas máquinas,** aplica estes conjuntos a todos os VMs ligados a este espaço de trabalho.


## <a name="connect-your-syslog-appliance"></a>Ligue o seu aparelho Syslog

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione o conector **Syslog.**

2. Na lâmina **Syslog,** selecione página de **conector Aberto**.

3. Instale o agente Linux:
    
    - Se a sua máquina virtual Linux estiver em Azure, selecione **Descarregar e instalar o agente na máquina virtual Azure Linux**. Na lâmina das **máquinas Virtuais,** selecione as máquinas virtuais para instalar o agente e, em seguida, clique em **Ligar**.
    - Se a sua máquina Linux não estiver em Azure, selecione **Descarregar e instalar o agente na máquina não Azure Linux**. Na lâmina **do agente Direct,** copie o comando para **DOWNLOAD E AGENTE DE BORDO para O LINUX** e execute-o no seu computador. 
    
   > [!NOTE]
   > Certifique-se de configurar as definições de segurança destes computadores de acordo com a política de segurança da sua organização. Por exemplo, pode configurar as definições de rede para alinhar com a política de segurança da sua organização e alterar as portas e protocolos no daemon para alinhar com os requisitos de segurança.

4. **Selecione Abra a configuração avançada do seu espaço de trabalho**.

5. Na lâmina de **definições Avançada,** selecione **Data** > **Syslog**. Em seguida, adicione as instalações para o conector recolher.
    
    Adicione as instalações que o seu aparelho syslog inclui nos seus cabeçalhos de tronco. Pode ver esta configuração no seu aparelho Syslog no `/etc/rsyslog.d/security-config-omsagent.conf` **Syslog-d** na pasta `/etc/syslog-ng/security-config-omsagent.conf`e no **r-Syslog** a partir de .
    
    Se pretender utilizar a deteção anómala de login SSH com os dados que recolhe, adicione **auth** e **authpriv**. Consulte a [secção seguinte](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) para obter mais detalhes.

6. Quando tiver adicionado todas as instalações que pretende monitorizar e ajustar quaisquer opções de gravidade para cada uma, selecione a caixa de verificação Aplicar abaixo da **configuração para as minhas máquinas**.

7. Selecione **Guardar**. 

8. No seu aparelho syslog, certifique-se de que está a enviar as instalações que especificou.

9. Para utilizar o esquema relevante no Monitor Azure para os registos de syslog, procure **syslog**.

10. Pode utilizar a função Kusto descrita na [utilização de funções em](../azure-monitor/log-query/functions.md) consultas de registo do Monitor Azure para analisar as suas mensagens Syslog. Em seguida, pode guardá-los como uma nova função Log Analytics para usar como um novo tipo de dados.

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Configure o conector Syslog para deteção anómala de login SSH

> [!IMPORTANT]
> A deteção de login SSH anómala está atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção.
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O Azure Sentinel pode aplicar machine learning (ML) aos dados de syslog para identificar a atividade anómala de login Secure Shell (SSH). Os cenários incluem:

- Viagem impossível – quando dois eventos de login bem sucedidos ocorrem a partir de dois locais que são impossíveis de alcançar dentro do prazo dos dois eventos de login.
- Localização inesperada – a localização de onde ocorreu um evento de login bem sucedido é suspeita. Por exemplo, a localização não foi vista recentemente.
 
Esta deteção requer uma configuração específica do conector de dados Syslog: 

1. Para o passo 5 do procedimento anterior, **certifique-se** de que tanto auth como **a uthpriv** são selecionadas como instalações para monitorizar. Mantenha as definições predefinidas para as opções de gravidade, de modo a que todas sejam selecionadas. Por exemplo:
    
    > [!div class="mx-imgBorder"]
    > ![Instalações necessárias para deteção anómala de login SSH](./media/connect-syslog/facilities-ssh-detection.png)

2. Dê tempo suficiente para recolher informações sobre syslog. Em seguida, navegue para **Azure Sentinel - Logs,** e copie e cole a seguinte consulta:
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    Mude a gama de **tempo** se necessário e selecione **Executar**.
    
    Se a contagem resultante for nula, confirme a configuração do conector e que os computadores monitorizados tenham uma atividade de login bem sucedida durante o período de tempo especificado para a sua consulta.
    
    Se a contagem resultante for superior a zero, os seus dados de sislog são adequados para a deteção anómala de login SSH. Ativa esta deteção a partir de**modelos** > de regra **de análise** >  **(Pré-visualização) Deteção anómala de Login SSH**.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos Syslog no local ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

