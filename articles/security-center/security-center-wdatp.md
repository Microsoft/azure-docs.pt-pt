---
title: Proteção avançada contra ameaças do Microsoft defender – central de segurança do Azure
description: Este documento apresenta a integração entre a central de segurança do Azure e a proteção avançada contra ameaças do Microsoft defender.
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
ms.openlocfilehash: 46b9fe5c6a038aa98cf8df64c40bf8ea1747efec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663596"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Proteção avançada contra ameaças do Microsoft defender com a central de segurança do Azure

A central de segurança do Azure está estendendo sua oferta de plataformas de proteção de carga de trabalho de nuvem integrando com [o Microsoft defender ATP (proteção avançada contra ameaças](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp)
Esta alteração oferece capacidades abrangentes de Deteção e Resposta de Ponto Final (EDR). Com a integração do Microsoft defender ATP, você pode identificar anormalidades. Você também pode detectar e responder a ataques avançados em pontos de extremidade de servidor monitorados pela central de segurança do Azure.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Recursos do Microsoft defender ATP na central de segurança

Ao usar o Microsoft defender ATP, você obtém:

- **Sensores avançados de detecção de pós-violação**: os sensores do Microsoft defender ATP para Windows Servers coletam uma vasta gama de sinais comportamentais.

- **Detecção de pós-violação com base em análise, baseada em nuvem**: o Microsoft defender ATP se adapta rapidamente às ameaças em constante mudança. Ele usa análise avançada e Big Data. O Microsoft defender ATP é amplificado pelo poder do Gráfico de Segurança Inteligente com sinais entre o Windows, o Azure e o Office para detectar ameaças desconhecidas. Ele fornece alertas acionáveis e permite que você responda rapidamente.

- **Inteligência contra ameaças**: o Microsoft defender ATP gera alertas ao identificar ferramentas, técnicas e procedimentos do invasor. Ele usa dados gerados pelas equipes de segurança e caçadores de ameaças da Microsoft, aumentados por inteligência fornecida pelos parceiros.

Os seguintes recursos agora estão disponíveis na central de segurança do Azure:

- **Integração automatizada**: o sensor do Microsoft defender ATP é habilitado automaticamente para servidores Windows que são integrados à central de segurança do Azure.

- **Painel único de vidro**: o console da central de segurança do Azure exibe alertas do Microsoft defender ATP.

- **Investigação de máquina detalhada**: os clientes da central de segurança do Azure podem usar o console do Microsoft defender ATP para realizar uma investigação detalhada para descobrir o escopo de uma violação.

![Central de segurança do Azure, exibindo uma lista de alertas e informações gerais sobre cada alerta](media/security-center-wdatp/image1.png)

Para investigar ainda mais, use o Microsoft defender ATP. O Microsoft defender ATP fornece informações adicionais, como a árvore de processo de alerta e o grafo de incidentes. Você também pode ver uma linha do tempo de máquina detalhada que mostra todos os comportamentos de um período histórico de até seis meses.

![Página do Microsoft defender ATP com informações detalhadas sobre um alerta](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Suporte da plataforma

O Microsoft defender ATP na central de segurança dá suporte à detecção no Windows Server 2016, 2012 R2 e 2008 R2 SP1, para VMs do Azure, você precisa de uma assinatura de camada Standard e para VMs não Azure, você precisa da camada Standard somente no nível do espaço de trabalho.

> [!NOTE]
> Quando você usa a central de segurança do Azure para monitorar servidores, um locatário do Microsoft defender ATP é criado automaticamente e os dados do Microsoft defender ATP são armazenados na Europa por padrão. Se você precisar mover seus dados para outro local, precisará entrar em contato com Suporte da Microsoft para redefinir o locatário. O monitoramento de ponto de extremidade do servidor que utiliza essa integração foi desabilitado para clientes do Office 365 GCC.

## <a name="onboarding-servers-to-security-center"></a>Integração de servidores à central de segurança 

Para integrar servidores à central de segurança, clique em **ir para a central de segurança do Azure para integrar servidores** da integração do servidor do Microsoft defender ATP.

1. Na área de **integração** , selecione ou crie um espaço de trabalho no qual armazenar os dados. <br>
2. Se você não conseguir ver todos os seus espaços de trabalho, pode ser devido à falta de permissões, verifique se o espaço de trabalho está definido como camada Standard de segurança do Azure. Para obter mais informações, consulte [atualizar para a camada Standard da central de segurança para aumentar a segurança](security-center-pricing.md).
    
3. Selecione **adicionar servidores** para exibir instruções sobre como instalar o Microsoft Monitoring Agent. 

4. Após a integração, você pode monitorar os computadores em **computação e aplicativos**.

   ![Computadores integrados](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Habilitar a integração do Microsoft defender ATP

Para exibir se a integração do Microsoft defender ATP está habilitada, selecione **central de segurança** > **preço & configurações** > clique em sua assinatura.
Aqui você pode ver as integrações habilitadas no momento.

  ![Página de configurações de detecção de ameaças da central de segurança do Azure com integração do Microsoft defender ATP habilitada](media/security-center-wdatp/enable-integrations.png)

- Se você já tiver integrado os servidores à camada Standard da central de segurança do Azure, não precisará executar nenhuma ação adicional. A central de segurança do Azure integrará automaticamente os servidores ao Microsoft defender ATP. A integração pode levar até 24 horas.

- Se você nunca tiver integrado os servidores à camada Standard da central de segurança do Azure, integre-os à central de segurança do Azure como de costume.

- Se você tiver integrado os servidores por meio do Microsoft defender ATP:
  - Consulte a documentação para obter orientação sobre [como transferir máquinas de servidor](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Integre esses servidores à central de segurança do Azure.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Acesso ao portal do Microsoft defender ATP

Siga as instruções em [atribuir acesso de usuário ao portal](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Definir a configuração do firewall

Se você tiver um proxy ou firewall que esteja bloqueando o tráfego anônimo, como um sensor do Microsoft defender ATP está se conectando a partir do contexto do sistema, verifique se o tráfego anônimo é permitido. Siga as instruções em [habilitar o acesso às URLs de serviço do Microsoft defender ATP no servidor proxy](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testar o recurso

Para gerar um alerta de teste benigno do Microsoft defender ATP:

1. Use o Área de Trabalho Remota para acessar uma VM do Windows Server 2012 R2 ou uma VM do Windows Server 2016. Abra uma janela de Linha de Comandos.

2. No prompt, copie e execute o comando a seguir. A janela de prompt de comando será fechada automaticamente.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Uma janela de prompt de comando com o comando acima](media/security-center-wdatp/image4.jpeg)

3. Se o comando for bem-sucedido, você verá um novo alerta no painel da central de segurança do Azure e no portal do Microsoft defender ATP. Esse alerta pode levar alguns minutos para aparecer.

4. Para examinar o alerta na central de segurança, acesse **alertas de segurança** > **linha de comando do PowerShell suspeito**.

5. Na janela investigação, selecione o link para acessar o portal do Microsoft defender ATP.

## <a name="next-steps"></a>Passos seguintes

- [Plataformas e funcionalidades suportadas pelo Centro de Segurança do Azure](security-center-os-coverage.md)
- [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
- [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md): saiba como as recomendações ajudam a proteger seus recursos do Azure.
- [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md): saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
