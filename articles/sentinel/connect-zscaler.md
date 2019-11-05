---
title: Conectar dados do Zscaler ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Zscaler ao Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: fe7ba0f6daec0b85ec73611ba4e48d72f16146e3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515138"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Conectar o acesso à Internet do Zscaler ao Azure Sentinel

> [!IMPORTANT]
> O conector de dados do Zscaler no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica como conectar seu dispositivo de acesso à Internet do Zscaler ao Azure Sentinel. O conector de dados do Zscaler permite que você conecte facilmente seus logs do ZIA (Zscaler Internet Access) com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. Usar o Zscaler no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 


## <a name="how-it-works"></a>Como funciona

Você precisa implantar um agente em um computador Linux dedicado (VM ou local) para dar suporte à comunicação entre o acesso à Internet Zscaler e o Azure Sentinel. O diagrama a seguir descreve a configuração no caso de uma VM do Linux no Azure.

 ![CEF no Azure](./media/connect-cef/cef-syslog-azure.png)

Como alternativa, essa configuração existirá se você usar uma VM em outra nuvem ou em um computador local. 

 ![CEF local](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Considerações de segurança

Certifique-se de configurar a segurança da máquina de acordo com a política de segurança da sua organização. Por exemplo, você pode configurar sua rede para se alinhar com sua política de segurança de rede corporativa e alterar as portas e protocolos no daemon para se alinhar com seus requisitos. Você pode usar as seguintes instruções para melhorar sua configuração de segurança de computador:  [VM segura no Azure](../virtual-machines/linux/security-policy.md), [práticas recomendadas para segurança de rede](../security/fundamentals/network-best-practices.md).

Para usar a comunicação TLS entre a solução de segurança e a máquina de syslog, você precisará configurar o daemon de syslog (rsyslog ou syslog-ng) para se comunicar em TLS: [criptografando o tráfego de syslog com TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [criptografando mensagens de log com TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Pré-requisitos
Verifique se o computador Linux que você usa como proxy está executando um dos seguintes sistemas operacionais:

- 64 bits
  - CentOS 6 e 7
  - Amazon Linux 2017, 9
  - Oracle Linux 6 e 7
  - Red Hat Enterprise Linux Server 6 e 7
  - Debian GNU/Linux 8 e 9
  - Ubuntu Linux 14, 4 LTS, 16, 4 LTS e 18, 4 LTS
  - SUSE Linux Enterprise Server 12
- 32 bits
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 e 9
   - Ubuntu Linux 14, 4 LTS e 16, 4 LTS
 
 - Versões do daemon
   - Syslog-ng: 2,1-3.22.1
   - Rsyslog: V8
  
 - RFCs do syslog com suporte
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Verifique se o computador também atende aos seguintes requisitos: 
- Permissões
    - Você deve ter permissões elevadas (sudo) em seu computador. 
- Requisitos de software
    - Verifique se você tem o Python em execução no seu computador
## <a name="step-1-deploy-the-agent"></a>ETAPA 1: implantar o agente

Nesta etapa, você precisa selecionar o computador Linux que atuará como um proxy entre o Azure Sentinel e sua solução de segurança. Você precisará executar um script no computador proxy que:
- Instala o agente de Log Analytics e o configura conforme necessário para escutar mensagens de syslog na porta 514 sobre TCP e enviar as mensagens CEF para seu espaço de trabalho do Azure Sentinel.
- Configura o daemon do syslog para encaminhar mensagens CEF para o agente de Log Analytics usando a porta 25226.
- Define o agente de syslog para coletar os dados e enviá-los com segurança para Log Analytics, onde são analisados e aprimorados.
 
 
1. No portal do Azure Sentinel, clique em **conectores de dados** e selecione **Zscaler** e **abra a página conector**. 

1. Em **instalar e configurar o agente de syslog**, selecione o tipo de computador, o Azure, outra nuvem ou local. 
   > [!NOTE]
   > Como o script na próxima etapa instala o agente de Log Analytics e conecta o computador ao seu espaço de trabalho do Azure Sentinel, verifique se este computador não está conectado a nenhum outro espaço de trabalho.
1. Você deve ter permissões elevadas (sudo) em seu computador. Verifique se você tem o Python em seu computador usando o seguinte comando: `python –version`

1. Execute o script a seguir no computador proxy.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Enquanto o script estiver em execução, verifique se você não recebe mensagens de erro ou de aviso.


## <a name="step-2-configure-your-zscaler-to-send-cef-messages"></a>ETAPA 2: configurar seu Zscaler para enviar mensagens CEF

1. No dispositivo Zscaler, você precisa definir esses valores para que o dispositivo envie os logs necessários no formato necessário para o agente de syslog do Azure Sentinel, com base no agente de Log Analytics. Você pode modificar esses parâmetros em seu dispositivo, contanto que também os modifique no daemon do syslog no agente do Azure Sentinel.
    - Protocolo = TCP
    - Porta = 514
    - Formato = CEF
    - Endereço IP-certifique-se de enviar as mensagens CEF para o endereço IP da máquina virtual que você dedicaram para essa finalidade.
 Para obter mais informações, consulte o [Guia de integração do Azure Sentinel Zscaler](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Esta solução oferece suporte a RFC 3164 ou a RFC 5424 do syslog.


1. Para usar o esquema relevante em Log Analytics para os eventos CEF, procure `CommonSecurityLog`.

## <a name="step-3-validate-connectivity"></a>ETAPA 3: validar a conectividade

1. Abra Log Analytics para garantir que os logs sejam recebidos usando o esquema CommonSecurityLog.<br> Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 

1. Antes de executar o script, recomendamos que você envie mensagens de sua solução de segurança para certificar-se de que elas estão sendo encaminhadas para o computador proxy de syslog configurado. 
1. Você deve ter permissões elevadas (sudo) em seu computador. Verifique se você tem o Python em seu computador usando o seguinte comando: `python –version`
1. Execute o script a seguir para verificar a conectividade entre o agente, o Azure Sentinel e sua solução de segurança. Ele verifica se o encaminhamento do daemon está configurado corretamente, escuta as portas corretas e se nada está bloqueando a comunicação entre o daemon e o agente de Log Analytics. O script também envia as mensagens de simulação ' TestCommonEventFormat ' para verificar a conectividade de ponta a ponta. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar o acesso à Internet do Zscaler ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).

