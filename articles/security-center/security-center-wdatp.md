---
title: O Windows Defender proteção avançada contra ameaças no Centro de segurança do Azure
description: Este documento apresenta a integração entre o Centro de segurança do Azure e a proteção de ameaças avançada do Windows Defender.
services: security-center
documentationcenter: na
author: barclayn
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: barclayn
ms.openlocfilehash: ee5ed8871ea5c021ad8c87e35f45e42ad641a324
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124180"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>O Windows Defender proteção avançada contra ameaças no Centro de segurança do Azure

Centro de segurança do Azure é expandir a sua oferta de plataformas de proteção de carga de trabalho na Cloud através da integração com o [a proteção de ameaças avançada do Windows Defender](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Esta alteração oferece capacidades abrangentes de ponto final de deteção e resposta (EDR). Com a integração do Windows Defender ATP, detetar anomalias. Também pode detetar e responder a ataques avançados em pontos finais de servidor monitorizados pelo centro de segurança do Azure.

Os clientes do Centro de segurança do Azure agora podem utilizar funcionalidades do Windows Defender ATP:

- **Sensores de deteção de violação de publicação de próxima geração**: Sensores do Windows Defender ATP para servidores do Windows recolhem uma grande diversidade de sinais comportamentais.

- **Deteção de violação de publicação com base na análise, com tecnologia de cloud**: Windows Defender ATP adapta-se rapidamente aos ameaças em constante mudança. Utiliza análise avançada e macrodados. Windows Defender ATP é Sim os amplificou pelo poder do gráfico de segurança inteligente com sinais no Windows, o Azure e o Office, para detetar ameaças desconhecidas. Ele fornece alertas acionáveis e que lhe permite responder rapidamente.

- **Informações sobre ameaças**: Windows Defender ATP identifica as ferramentas do invasor, técnicas e procedimentos. Quando Deteta estes, gera alertas. Utiliza dados gerados por hunters de ameaças da Microsoft e as equipes de segurança, acrescentadas pela inteligência oferecidas por parceiros.

Estas capacidades estão agora disponíveis no Centro de segurança do Azure:

- **Automatizada integração**: O sensor do Windows Defender ATP é ativado automaticamente para os servidores de Windows que são carregadas para o Centro de segurança do Azure.

- **Painel único de vidro**: A consola do Centro de segurança do Azure apresenta os alertas do Windows Defender ATP.

- **Detalhadas de investigação da máquina**: Os clientes do Centro de segurança do Azure podem aceder a consola do Windows Defender ATP para efetuar uma investigação detalhada para descobrir o âmbito de uma violação.

![Centro de segurança do Azure, exibindo uma lista de alertas e informações gerais sobre cada alerta](media/security-center-wdatp/image1.png)

Pode [investigar o alerta](security-center-investigation.md) no Centro de segurança do Azure:

![O dashboard de investigação de alertas no Centro de segurança do Azure](media/security-center-wdatp/image2.png)

Pode continuar a investigar o alerta por dinamização para o Windows Defender ATP. Aqui pode ver informações adicionais, como a árvore de processos de alerta e o gráfico de incidente. Também pode ver uma linha cronológica de máquina detalhado que mostra cada comportamento durante um período de histórico de até seis meses.

![Página do Windows Defender ATP com informações detalhadas sobre um alerta](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Suporte da plataforma

Esta funcionalidade suporta a deteção no Windows Server 2012 R2 e Windows Server 2016.

Apenas os servidores em subscrições para o escalão de serviço standard são suportados.

## <a name="onboarding-servers-to-security-center"></a>Servidores de integração ao centro de segurança 

A carregar servidores ao centro de segurança, clique em **aceda ao centro de segurança do Azure para carregar servidores** de integração de servidor do Windows Defender ATP.

1. Na **inclusão** painel selecione ou crie uma área de trabalho para armazenar os dados. <br>
2. Se não pode ver todas as suas áreas de trabalho, ele pode ser devido à falta de permissões, certifique-se de que sua área de trabalho está definida para o escalão Standard de segurança do Azure. Para obter mais informações, consulte [atualizar para o escalão Standard do Centro de segurança para uma maior segurança](security-center-pricing.md).
    
3. Selecione **adicionar servidores** para ver instruções sobre como instalar o Microsoft Monitoring Agent. 

4. Após a integração, pode monitorizar as máquinas de **computação e aplicações**.

   ![Integrar computadores](media/security-center-wdatp/onboard-computers.png)


## <a name="enable-windows-defender-atp-integration"></a>Ativar a integração do Windows Defender ATP

Para ver se a integração do Windows Defender ATP estiver ativada, selecione **Centro de segurança** > **política de segurança** > **subscrição**  >  **Editar definições**.

  ![Gestão de políticas do Centro de segurança do Azure](media/security-center-wdatp/policy-management.png)

Aqui pode ver as integrações atualmente ativadas.

  ![Página Definições de deteção de ameaças do Centro de segurança do Azure com a integração do Windows Defender ATP ativada](media/security-center-wdatp/enable-integrations.png)

- Se já carregada os servidores para o escalão standard do Centro de segurança do Azure, não precisa de tomar nenhuma ação adicional. Centro de segurança do Azure irá carregar automaticamente os servidores para o Windows Defender ATP. Isto poderá demorar até 24 horas.

- Se já nunca carregados os servidores para o escalão standard do Centro de segurança do Azure, integrá-los, ao centro de segurança do Azure, como é habitual.

- Se tiver integrado os servidores por meio do Windows Defender ATP:
  - Consulte a documentação para obter orientações sobre [como máquinas de servidor de descarregar](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Carregar estes servidores para o Centro de segurança do Azure.

## <a name="access-to-the-windows-defender-atp-portal"></a>Acesso ao portal do Windows Defender ATP

Siga as instruções em [atribuir acesso de utilizador para o portal](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Definir a configuração de firewall

Se tiver um proxy ou firewall que está a bloquear tráfego anônimo, tal como um sensor do Windows Defender ATP está se conectando do contexto do sistema, certifique-se de que o tráfego anônimo é permitido. Siga as instruções em [ativar o acesso aos URLs do serviço do Windows Defender ATP no servidor de proxy](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>A funcionalidade de teste

Para gerar um alerta de teste do Windows Defender ATP benigno:

1. Utilize o ambiente de trabalho remoto para aceder a uma VM do Windows Server 2012 R2 ou uma VM do Windows Server 2016.  Abra uma janela de Linha de Comandos.

2. Na linha de comandos, copie e execute o seguinte comando. A janela da linha de comando será fechado automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Uma janela de linha de comandos com o comando acima](media/security-center-wdatp/image4.jpeg)

3. Se o comando for bem-sucedida, verá um novo alerta no dashboard do Centro de segurança do Azure e no portal do Windows Defender ATP. Este alerta pode demorar alguns minutos a aparecer.

4. Para rever o alerta no Centro de segurança, aceda a **alertas de segurança** >  **suspeita linha de comandos do Powershell**.

5. Na janela de investigação, selecione a ligação para aceder ao portal do Windows Defender ATP.

## <a name="next-steps"></a>Passos Seguintes

- [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md): Saiba como configurar políticas de segurança para as suas subscrições do Azure e grupos de recursos.
- [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md): Saiba como recomendações ajudá-lo a proteger os seus recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md): Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
