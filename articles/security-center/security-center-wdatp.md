---
title: Proteção avançada contra ameaças do Windows defender com a central de segurança do Azure
description: Este documento apresenta a integração entre a central de segurança do Azure e a proteção avançada contra ameaças do Windows Defender.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2019
ms.author: v-mohabe
ms.openlocfilehash: 5342ec14e32b87c5cccdb36206122ce1168aeb56
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061429"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Proteção avançada contra ameaças do Windows defender com a central de segurança do Azure

O Centro de Segurança do Azure está a expandir a sua oferta de Plataformas de Proteção de Carga de Trabalho da Cloud através da integração com o [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Esta alteração oferece capacidades abrangentes de Deteção e Resposta de Ponto Final (EDR). Com a integração do Windows Defender ATP, você pode identificar anormalidades. Você também pode detectar e responder a ataques avançados em pontos de extremidade de servidor monitorados pela central de segurança do Azure.

## <a name="windows-defender-atp-features-in-security-center"></a>Recursos do Windows Defender ATP na central de segurança

Ao usar o Windows Defender ATP, você obtém:

- **Sensores de detecção da próxima geração pós-violação**: Os sensores do Windows Defender ATP para Windows Servers coletam uma vasta gama de sinais comportamentais.

- **Detecção de violações de postagem com base em análise de nuvem**: O Windows Defender ATP se adapta rapidamente às ameaças em constante mudança. Ele usa análise avançada e Big Data. O Windows Defender ATP é amplificado pelo poder do Gráfico de Segurança Inteligente com sinais entre o Windows, o Azure e o Office para detectar ameaças desconhecidas. Ele fornece alertas acionáveis e permite que você responda rapidamente.

- **Inteligência contra ameaças**: O Windows Defender ATP identifica ferramentas, técnicas e procedimentos do invasor. Quando detecta esses, ele gera alertas. Ele usa dados gerados pelas equipes de segurança e caçadores de ameaças da Microsoft, aumentados por inteligência fornecida pelos parceiros.

Estas capacidades já estão disponíveis no Centro de Segurança do Azure:

- **Integração automatizada**: O sensor do Windows Defender ATP é habilitado automaticamente para servidores Windows que são integrados à central de segurança do Azure.

- **Painel único de vidro**: O console da central de segurança do Azure exibe alertas do Windows Defender ATP.

- **Investigação de máquina detalhada**: Os clientes da central de segurança do Azure podem acessar o console do Windows Defender ATP para executar uma investigação detalhada para descobrir o escopo de uma violação.

![Central de segurança do Azure, exibindo uma lista de alertas e informações gerais sobre cada alerta](media/security-center-wdatp/image1.png)

Você pode investigar ainda mais o alerta ao dinamizar para o Windows Defender ATP. Lá, você pode ver informações adicionais, como a árvore de processo de alerta e o grafo de incidentes. Você também pode ver uma linha do tempo de máquina detalhada que mostra todos os comportamentos de um período histórico de até seis meses.

![Página do Windows Defender ATP com informações detalhadas sobre um alerta](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Suporte da plataforma

O Windows Defender ATP na central de segurança dá suporte à detecção nos sistemas operacionais Windows Server 2016, 2012 R2 e 2008 R2 SP1 em uma assinatura de serviço padrão.

> [!NOTE]
> Quando você usa a central de segurança do Azure para monitorar servidores, um locatário do Windows Defender ATP é criado automaticamente e os dados do Windows Defender ATP são armazenados na Europa por padrão. Se você precisar mover seus dados para outro local, precisará entrar em contato com Suporte da Microsoft para redefinir o locatário.

## <a name="onboarding-servers-to-security-center"></a>Integração de servidores à central de segurança 

Para integrar servidores à central de segurança, clique em **ir para a central de segurança do Azure para integrar servidores** da integração do servidor do Windows Defender ATP.

1. Na folha **integração** , selecione ou crie um espaço de trabalho no qual armazenar os dados. <br>
2. Se você não conseguir ver todos os seus espaços de trabalho, pode ser devido à falta de permissões, verifique se o espaço de trabalho está definido como camada Standard de segurança do Azure. Para obter mais informações, consulte [atualizar para a camada Standard da central de segurança para aumentar a segurança](security-center-pricing.md).
    
3. Selecione **adicionar servidores** para exibir instruções sobre como instalar o Microsoft Monitoring Agent. 

4. Após a integração, você pode monitorar os computadores em **computação e aplicativos**.

   ![Computadores integrados](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>Habilitar a integração do Windows Defender ATP

Para exibir se a integração do Windows Defender ATP está habilitada, selecione preços da **central** > de segurança **& configurações** > clique em sua assinatura.

  ![Gerenciamento de política da central de segurança do Azure](media/security-center-wdatp/policy-management.png)

Aqui você pode ver as integrações habilitadas no momento.

  ![Página de configurações de detecção de ameaças da central de segurança do Azure com integração do Windows Defender ATP habilitada](media/security-center-wdatp/enable-integrations.png)

- Se você já tiver integrado os servidores à camada Standard da central de segurança do Azure, não precisará executar nenhuma ação adicional. A central de segurança do Azure integrará automaticamente os servidores ao Windows Defender ATP. Isso pode levar até 24 horas.

- Se você nunca tiver integrado os servidores à camada Standard da central de segurança do Azure, integre-os à central de segurança do Azure como de costume.

- Se você tiver integrado os servidores por meio do Windows Defender ATP:
  - Consulte a documentação para obter orientação sobre [como transferir máquinas de servidor](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Integre esses servidores à central de segurança do Azure.

## <a name="access-to-the-windows-defender-atp-portal"></a>Acesso ao portal do Windows Defender ATP

Siga as instruções em [atribuir acesso de usuário ao portal](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Definir a configuração do firewall

Se você tiver um proxy ou firewall que esteja bloqueando o tráfego anônimo, como um sensor do Windows Defender ATP está se conectando a partir do contexto do sistema, certifique-se de que o tráfego anônimo seja permitido. Siga as instruções em [habilitar o acesso às URLs de serviço do Windows Defender ATP no servidor proxy](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testar o recurso

Para gerar um alerta de teste do Windows Defender ATP benigno:

1. Use o Área de Trabalho Remota para acessar uma VM do Windows Server 2012 R2 ou uma VM do Windows Server 2016.  Abra uma janela de Linha de Comandos.

2. No prompt, copie e execute o comando a seguir. A janela de prompt de comando será fechada automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Uma janela de prompt de comando com o comando acima](media/security-center-wdatp/image4.jpeg)

3. Se o comando for bem-sucedido, você verá um novo alerta no painel da central de segurança do Azure e no portal do Windows Defender ATP. Esse alerta pode levar alguns minutos para aparecer.

4. Para examinar o alerta na central de segurança, acesse **alertas de segurança aviso** >  de**linha de comando do PowerShell suspeito**.

5. Na janela investigação, selecione o link para acessar o portal do Windows Defender ATP.

## <a name="next-steps"></a>Passos Seguintes

- [Plataformas e funcionalidades suportadas pelo Centro de Segurança do Azure](security-center-os-coverage.md)
- [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md): Saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md): Saiba como as recomendações ajudam a proteger seus recursos do Azure.
- [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md): Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
