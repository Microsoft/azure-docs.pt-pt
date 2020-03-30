---
title: Microsoft Defender Advanced Threat Protection - Azure Security Center
description: Este documento introduz a integração entre o Azure Security Center e o Microsoft Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 13852acb39a420e2f0da84e18bef4df823c1fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206270"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Advanced Threat Protection com Azure Security Center

O Azure Security Center está a alargar a sua oferta de Plataformas de Proteção de Carga de Trabalho em Nuvem, integrando-se com a [Microsoft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Esta alteração oferece capacidades abrangentes de Deteção e Resposta de Ponto Final (EDR). Com a integração ATP do Microsoft Defender, pode detetar anomalias. Também pode detetar e responder a ataques avançados em pontos finais do servidor monitorizados pelo Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Funcionalidades ATP do Microsoft Defender no Centro de Segurança

Quando utiliza o Microsoft Defender ATP, obtém-se:

- **Sensores avançados de deteção de pós-violação**: Os sensores ATP do Microsoft Defender para servidores Windows recolhem uma vasta gama de sinais comportamentais.

- Deteção de falhas de pós baseada **em análise, alimentada por nuvem:** O Microsoft Defender ATP adapta-se rapidamente às ameaças em mudança. Usa análises avançadas e big data. O Microsoft Defender ATP é amplificado pela potência do Gráfico de Segurança Inteligente com sinais através do Windows, Azure e Office para detetar ameaças desconhecidas. Fornece alertas atolheiros e permite-lhe responder rapidamente.

- **Inteligência de ameaça**: O Microsoft Defender ATP gera alertas quando identifica ferramentas, técnicas e procedimentos atacantes. Utiliza dados gerados por caçadores de ameaças da Microsoft e equipas de segurança, aumentados pela inteligência fornecida pelos parceiros.

As seguintes capacidades estão agora disponíveis no Centro de Segurança Azure:

- **Embarque automatizado**: O sensor ATP do Microsoft Defender está automaticamente ativado para servidores Windows que estão a bordo do Azure Security Center.

- **Painel único de vidro**: A consola Azure Security Center exibe alertas ATP do Microsoft Defender.

- **Investigação detalhada da máquina**: Os clientes do Azure Security Center podem usar a consola ATP do Microsoft Defender para conduzir uma investigação detalhada para descobrir o âmbito de uma violação.

![Centro de Segurança Azure, exibindo uma lista de alertas e informações gerais sobre cada alerta](media/security-center-wdatp/image1.png)

Para investigar mais, utilize o Microsoft Defender ATP. O Microsoft Defender ATP fornece informações adicionais, como a árvore do processo de alerta e o gráfico do incidente. Você também pode ver uma linha de tempo detalhada da máquina que mostra cada comportamento por um período histórico de até seis meses.

![Página ATP do Microsoft Defender com informações detalhadas sobre um alerta](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Suporte da plataforma

O Microsoft Defender ATP no Security Center suporta a deteção no Windows Server 2016, 2012 R2 e 2008 R2 SP1, para VMs Azure precisa de uma subscrição de nível Standard e para VMs não-Azure precisa apenas de nível Standard no nível do espaço de trabalho.

> [!NOTE]
> Quando utiliza o Azure Security Center para monitorizar servidores, é criado automaticamente um inquilino ATP do Microsoft Defender e os dados ATP do Microsoft Defender são armazenados na Europa por padrão. Se precisar de transferir os seus dados para outro local, tem de contactar o Microsoft Support para redefinir o inquilino. A monitorização do ponto final do servidor utilizando esta integração foi desativada para os clientes do Office 365 GCC.

## <a name="onboarding-servers-to-security-center"></a>Inclusão de servidores no Centro de Segurança 

Para embarcar nos servidores do Security Center, clique em **Ir para o Azure Security Center para os servidores de bordo** do servidor ATP do Microsoft Defender a bordo.

1. Na área de **Embarque,** selecione ou crie um espaço de trabalho para armazenar os dados. <br>
2. Se não consegue ver todos os seus espaços de trabalho, pode ser devido à falta de permissões, certifique-se de que o seu espaço de trabalho está definido para o nível Azure Security Standard. Para mais informações, consulte [o upgrade para o nível Standard do Security Center para uma maior segurança](security-center-pricing.md).
    
3. Selecione **Adicionar servidores** para visualizar instruções sobre como instalar o Agente de Monitorização da Microsoft. 

4. Depois de embarcar, pode monitorizar as máquinas em **Compute e aplicações**.

   ![Computadores a bordo](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Ativar a integração ATP do Microsoft Defender

Para ver se a integração ATP do Microsoft Defender está ativada, selecione **Definições** > de preços do centro de segurança **&** > clique na sua subscrição.
Aqui pode ver as integrações atualmente ativadas.

  ![Página de definições de deteção de ameaças do Azure Security Center com integração ATP do Microsoft Defender ativada](media/security-center-wdatp/enable-integrations.png)

- Se já abordou os servidores para o nível padrão do Azure Security Center, não precisa de tomar mais medidas. O Azure Security Center irá automaticamente a bordo dos servidores para o Microsoft Defender ATP. O embarque pode demorar até 24 horas.

- Se nunca abordou os servidores para o nível padrão do Azure Security Center, a bordo deles para o Azure Security Center, como de costume.

- Se tiver embarcado nos servidores através do Microsoft Defender ATP:
  - Consulte a documentação para obter orientações sobre [como offboard server machines](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - A bordo destes servidores para o Centro de Segurança Azure.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Acesso ao portal ATP microsoft Defender

Siga as instruções em [Atribuir o acesso do utilizador ao portal](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Definir a configuração da firewall

Se tiver um proxy ou firewall que esteja a bloquear o tráfego anónimo, uma vez que um sensor ATP do Microsoft Defender está a ligar-se do contexto do sistema, certifique-se de que o tráfego anónimo é permitido. Siga as instruções em [Ativar o acesso aos URLs do serviço ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)do Microsoft Defender no servidor proxy .

## <a name="test-the-feature"></a>Testar a característica

Para gerar um alerta benigno de teste ATP do Microsoft Defender:

1. Crie uma pasta 'C:\test-MDATP-test'.

1. Utilize o Remote Desktop para aceder a um VM Do Windows Server 2012 R2 ou a um VM Windows Server 2016. Abra uma janela da linha de comando.

1. No pedido, copie e execute o seguinte comando. A janela 'Solicitação de Comando' fecha-se automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Uma janela de comando com o comando acima](media/security-center-wdatp/image4.jpeg)

3. Se o comando for bem sucedido, verá um novo alerta no painel do Azure Security Center e no portal ATP do Microsoft Defender. Este alerta pode levar alguns minutos para aparecer.

4. Para rever o alerta no Centro de Segurança, vá a **Alertas** > de Segurança**Suspeitos PowerShell CommandLine**.

5. A partir da janela de investigação, selecione o link para ir ao portal ATP microsoft Defender.

## <a name="next-steps"></a>Passos seguintes

- [Plataformas e funcionalidades suportadas pelo Centro de Segurança do Azure](security-center-os-coverage.md)
- [Definição de políticas](tutorial-security-policy.md)de segurança no Azure Security Center : Saiba configurar políticas de segurança para as suas subscrições e grupos de recursos Do Azure.
- [Gerir recomendações de segurança no Azure Security Center](security-center-recommendations.md): Saiba como as recomendações ajudam a proteger os seus recursos Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md): saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
