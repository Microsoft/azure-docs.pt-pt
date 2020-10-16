---
title: Microsoft Defender Proteção avançada de ameaças - Azure Security Center
description: Este documento introduz a integração entre o Azure Security Center e o Microsoft Defender Advanced Threat Protection.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: memildin
ms.openlocfilehash: 92feb159fe4c893a55d37fa90c34acf4c4c93631
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826174"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Proteção avançada de ameaças do Microsoft Defender com Centro de Segurança Azure

O Azure Security Center integra-se com [o Microsoft Defender Advanced Threat Protection (ATP)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) para fornecer capacidades abrangentes de Deteção e Resposta de Pontos De Final (EDR).


## <a name="availability"></a>Disponibilidade

|Aspeto|Detalhes|
|----|:----|
|Estado de libertação:|Geralmente disponível (GA)|
|Preços:|Requer [Azure Defender](security-center-pricing.md)|
|Máquinas suportadas:|![Sim](./media/icons/yes-icon.png) Máquinas Azure executando Janelas<br>![Sim](./media/icons/yes-icon.png) Máquinas Azure Arc executando janelas|
|Funções e permissões necessárias:|Para ativar/desativar a integração: **Administrador de segurança** ou **Proprietário**<br>Para ver alertas de MDATP no Centro de Segurança: **Leitor de segurança,** **leitor,** **contribuinte do grupo de recursos,** **proprietário do grupo de recursos,** **administrador de segurança,** **proprietário de assinatura,** ou **colaborador de subscrição**|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais.<br>![Não](./media/icons/no-icon.png) Clientes da GCC que executam cargas de trabalho em nuvens públicas do Azure<br>![Sim](./media/icons/yes-icon.png) US Gov<br>![Não](./media/icons/no-icon.png) China Gov, Outro Gov|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>Funcionalidades ATP do Microsoft Defender no Security Center

O Microsoft Defender ATP fornece:

- **Sensores avançados de deteção pós-violação**: Os sensores ATP do Microsoft Defender para servidores Windows recolhem uma vasta gama de sinais comportamentais.

- **Deteção de falhas postais baseada em analíticas e alimentadas por nuvem**: O Microsoft Defender ATP adapta-se rapidamente a ameaças em mudança. Usa análises avançadas e big data. O Microsoft Defender ATP é amplificado pela potência do Smart Security Graph com sinais através do Windows, Azure e Office para detetar ameaças desconhecidas. Fornece alertas accuíveis e permite-lhe responder rapidamente.

- **Inteligência de ameaça**: O Microsoft Defender ATP gera alertas quando identifica ferramentas, técnicas e procedimentos atacantes. Utiliza dados gerados por caçadores de ameaças da Microsoft e equipas de segurança, aumentados pela inteligência fornecida pelos parceiros.


Ao integrar o Defender ATP com o Azure Security Center, também pode beneficiar das seguintes capacidades adicionais:

- **Embarque automatizado**: A integração permite automaticamente o sensor ATP do Microsoft Defender para servidores Windows monitorizados pelo Azure Security Center (a menos que estejam a executar o Windows Server 2019).

- **Painel único de vidro**: A consola Azure Security Center exibe alertas ATP do Microsoft Defender. Para investigar mais, utilize o Microsoft Defender ATP. O Microsoft Defender ATP fornece informações adicionais, como a árvore do processo de alerta e o gráfico de incidentes. Você também pode ver uma linha do tempo de máquina detalhada que mostra todos os comportamentos por um período histórico de até seis meses.

    ![Página ATP do Microsoft Defender com informações detalhadas sobre um alerta](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Suporte da plataforma

O Microsoft Defender ATP no Security Center suporta a deteção no Windows Server 2016, 2012 R2 e 2008 R2 SP1. Para O Azure VMs precisa de um Para ativar o Azure Defender na sua subscrição, e para VMs não-Azure precisa de Azure Defender apenas ao nível do espaço de trabalho.

A monitorização do ponto final do servidor utilizando esta integração foi desativada para os clientes do Office 365 GCC.

## <a name="data-storage-location"></a>Localização de armazenamento de dados

Quando utiliza o Azure Security Center para monitorizar os servidores, é criado automaticamente um inquilino ATP do Microsoft Defender. Os dados recolhidos pelo Microsoft Defender ATP são armazenados na geolocalização do inquilino identificado durante o provisionamento. Os dados dos clientes em forma pseudónimo também podem ser armazenados nos sistemas centrais de armazenamento e processamento nos Estados Unidos. 

Uma vez configurados, não é possível alterar o local onde os seus dados são armazenados. Se precisar de mover os seus dados para outro local, contacte o Microsoft Support para redefinir o inquilino.


## <a name="onboard-servers-to-security-center"></a>Servidores a bordo do Centro de Segurança 

Para servidores a bordo do Security Center, clique em **Ir ao Azure Security Center para servidores** a bordo do servidor ATP do Microsoft Defender no embarque.

1. Na área **de Embarque,** selecione ou crie um espaço de trabalho para armazenar os dados.

2. Se não consegue ver todos os seus espaços de trabalho, pode ser devido à falta de permissões, certifique-se de que o seu espaço de trabalho está protegido pelo Azure Defender.
    
3. **Selecione Adicionar servidores** para visualizar instruções sobre como instalar o agente Log Analytics. 

4. Após o embarque, pode monitorizar as máquinas no [inventário de ativos.](asset-inventory.md)

   ![Computadores a bordo](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Ativar a integração ATP do Microsoft Defender

Para ver se a integração ATP do Microsoft Defender está ativada, selecione as definições de preços do centro de **segurança**  >  **&** > selecione a sua subscrição.

Aqui pode ver as integrações atualmente ativadas.

  ![Página de definições de deteção de ameaças do Azure Security Center com integração ATP do Microsoft Defender ativada](media/security-center-wdatp/enable-integrations.png)

- Se já ativou o Azure Defender, não são necessárias mais medidas. O Azure Security Center irá automaticamente embarcar nos servidores do Microsoft Defender ATP. O embarque pode demorar até 24 horas.

- Se nunca entrou no ativo dos servidores para o Azure Security Center, a bordo do Azure Security Center e ativar o Azure Defender como de costume.

- Se tiver entrado nos servidores através do Microsoft Defender ATP:
  - Consulte a documentação para obter orientações sobre [como desligar as máquinas do servidor](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - A bordo destes servidores para o Centro de Segurança Azure.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Acesso ao portal ATP do Microsoft Defender

1. Siga as instruções no [Atribuir acesso ao portal do utilizador.](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)

1. Verifique se tem um representante ou uma firewall que bloqueie o tráfego anónimo. O sensor ATP Defender liga-se a partir do contexto do sistema, pelo que deve ser permitido tráfego anónimo. Para garantir o acesso sem entraves ao portal ATP do Microsoft Defender, siga as instruções em Ativar o [acesso aos URLs](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)de serviço ATP do Microsoft Defender no servidor proxy .

## <a name="test-the-feature"></a>Teste a funcionalidade

Para gerar um alerta de teste ATP do Microsoft Defender benigno:

1. Criar uma pasta 'C:\test-MDATP-test'.

1. Utilize o Ambiente de Trabalho Remoto para aceder a um VM R2 do Windows Server 2012 ou a um VM do Windows Server 2016. Abra uma janela da linha de comandos.

1. No pedido, copie e execute o seguinte comando. A janela de pedido de comando fecha-se automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Uma janela de pedido de comando com o comando acima](media/security-center-wdatp/image4.jpeg)

1. Se o comando for bem sucedido, verá um novo alerta no painel do Azure Security Center e no portal ATP do Microsoft Defender. Este alerta pode levar alguns minutos a aparecer.

1. Para rever o alerta no Centro de Segurança, vá a **Alertas de Segurança**  >  **Alertas Suspeitos powerShell CommandLine**.

1. A partir da janela de investigação, selecione o link para ir ao portal ATP do Microsoft Defender.

## <a name="next-steps"></a>Passos seguintes

- [Plataformas e funcionalidades suportadas pelo Centro de Segurança do Azure](security-center-os-coverage.md)
- [Definição de políticas de segurança no Azure Security Center](tutorial-security-policy.md): Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
- [Gerir recomendações de segurança no Azure Security Center](security-center-recommendations.md): Saiba como as recomendações o ajudam a proteger os seus recursos Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md): saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.