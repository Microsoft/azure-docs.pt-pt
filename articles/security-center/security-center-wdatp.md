---
title: Utilização da licença Microsoft Defender para Endpoint incluída no Azure Security Center
description: Saiba mais sobre o Microsoft Defender para Endpoint e implemente-o a partir do Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: memildin
ms.openlocfilehash: 9fde0ecd8b6670e43aa3970b2df27a402b9c1a5c
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747245"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Proteja os seus pontos finais com a solução EDR integrada do Security Center: Microsoft Defender for Endpoint

O Microsoft Defender for Endpoint é uma solução holística de segurança de ponto final. As suas principais características são:

- Gestão e avaliação de vulnerabilidades baseadas em riscos 
- Attack surface reduction (Redução da superfície de ataque)
- Proteção comportamental e alimentada por nuvens
- Deteção e resposta de pontos finais (EDR)
- Investigação automática e reparação
- Serviços de caça geridos

> [!TIP]
> Originalmente lançado como **Windows Defender ATP**, este produto Endpoint Detection and Response (EDR) foi renomeado em 2019 como Microsoft Defender **ATP**.
>
> Na Ignite 2020, lançámos a [suite Microsoft Defender XDR](https://www.microsoft.com/security/business/threat-protection) e este componente EDR foi renomeado **Microsoft Defender para Endpoint**.


## <a name="availability"></a>Disponibilidade

| Aspeto                          | Detalhes                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado de libertação:                  | Geralmente disponível (GA)                                                                                                                                                                                                                                                                                      |
| Preços:                        | Requer [Azure Defender para servidores](security-center-pricing.md)                                                                                                                                                                                                                                             |
| Plataformas suportadas:            | Máquinas Azure executando Janelas<br>Máquinas Azure Arc executando janelas|
| Versões suportadas do Windows:  |  • Security Center suporta a deteção no Windows Server 2016, 2012 R2 e 2008 R2 SP1<br> • A monitorização do ponto final do servidor utilizando esta integração foi desativada para os clientes do Office 365 GCC<br> • Sem suporte para Windows Server 2019, Windows 10 1703 (e mais recente) ou Linux|
| Funções e permissões necessárias: | Para ativar/desativar a integração: **Administrador de segurança** ou **Proprietário**<br>Para ver alertas de MDATP no Centro de Segurança: **Leitor de segurança,** **leitor,** **contribuinte do grupo de recursos,** **proprietário do grupo de recursos,** **administrador de segurança,** **proprietário de assinatura,** ou **colaborador de subscrição**|
| Nuvens:                         | ![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) US Gov<br>![Não](./media/icons/no-icon.png) China Gov, Outro Gov<br>![Não](./media/icons/no-icon.png) Clientes da GCC que executam cargas de trabalho em nuvens azure globais                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |


## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Microsoft Defender para funcionalidades de Endpoint no Security Center

O Microsoft Defender for Endpoint fornece:

- **Sensores avançados de deteção pós-violação.** Os sensores do Defender para as máquinas do Windows recolhem uma vasta gama de sinais comportamentais.

- **Deteção pós-violação baseada em analítica, alimentada em nuvem.** O Defender for Endpoint adapta-se rapidamente às ameaças em mudança. Usa análises avançadas e big data. É amplificado pela potência do Smart Security Graph com sinais através do Windows, Azure e Office para detetar ameaças desconhecidas. Fornece alertas accuíveis e permite-lhe responder rapidamente.

- **Informações sobre ameaças**. O Defender for Endpoint gera alertas quando identifica ferramentas, técnicas e procedimentos atacantes. Utiliza dados gerados por caçadores de ameaças da Microsoft e equipas de segurança, aumentados pela inteligência fornecida pelos parceiros.

Ao integrar o Defender para Endpoint com o Security Center, irá beneficiar das seguintes capacidades adicionais:

- **Embarque automatizado.** O Security Center ativa automaticamente o microsoft Defender para o sensor Endpoint para todos os servidores windows monitorizados pelo Security Center. Exceto aqueles que estão a executar o Windows Server 2019, que deve ser a bordo através de script local, Group Policy Object (GPO) ou [Microsoft Endpoint Configuration Manager](https://docs.microsoft.com/mem/configmgr/) (anteriormente SCCM).

- **Uma única vidraça de vidro.** A consola Do Centro de Segurança apresenta alertas microsoft Defender para endpoint. Para investigar mais aprofundadamente, utilize o Microsoft Defender para as páginas do portal do Endpoint, onde verá informações adicionais, como a árvore do processo de alerta e o gráfico do incidente. Você também pode ver uma linha do tempo de máquina detalhada que mostra todos os comportamentos por um período histórico de até seis meses.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Microsoft Defender para o próprio Centro de Segurança endpoint" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="microsoft-defender-for-endpoint-tenant-location"></a>Microsoft Defender para localização do inquilino endpoint

Quando utiliza o Azure Security Center para monitorizar os seus servidores, é criado automaticamente um Microsoft Defender para o inquilino Endpoint. Os dados recolhidos pelo Defender for Endpoint são armazenados na geolocalização do arrendatário identificado durante o provisionamento. Os dados dos clientes - em forma pseudónimo - também podem ser armazenados nos sistemas centrais de armazenamento e processamento nos Estados Unidos. 

Depois de configurar o local, não pode mudá-lo. Se tiver a sua própria licença para o Microsoft Defender para Endpoint e precisar de mover os seus dados para outra localização, contacte o Microsoft Support para redefinir o inquilino.


## <a name="enabling-the-microsoft-defender-for-endpoint-integration"></a>Permitir a integração do Microsoft Defender para a integração de Endpoint

1. Ativar **o Azure Defender para servidores**. Ver [Preços do Centro de Segurança Azure](security-center-pricing.md#enable-azure-defender).

    > [!NOTE]
    > Para proteger as máquinas ativadas pelo Arco Azure, utilize as instruções em [Quickstart: Ligue a máquina híbrida com servidores ativados Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).

1. Se já licenciou e implementou o Microsoft Defender para pontos finais nos seus servidores, remova-o utilizando o procedimento descrito nos [servidores do Windows offboard](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers).
1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.
1. Selecione a subscrição que pretende alterar.
1. Selecione **deteção de ameaças**.
1. Selecione **Permitir que o Microsoft Defender para o Ponto Final aceda aos meus dados** e selecione **Guardar**.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Permitir a integração entre o Azure Security Center e a solução EDR da Microsoft, Microsoft Defender for Endpoint":::

    O Azure Security Center irá automaticamente embarcar os seus servidores no Microsoft Defender para Endpoint. O embarque pode demorar até 24 horas.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Aceda ao Microsoft Defender para o portal Endpoint

1. Certifique-se de que a conta de utilizador tem as permissões necessárias. [Saiba mais](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Verifique se tem um representante ou uma firewall que bloqueie o tráfego anónimo. O sensor Defender for Endpoint liga-se a partir do contexto do sistema, pelo que deve ser permitido tráfego anónimo. Para garantir o acesso sem entraves ao portal Defender para o Endpoint, siga as instruções em Permitir o [acesso aos URLs de serviço no servidor proxy](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

1. Abra o [portal Microsoft Defender Security Center](https://securitycenter.windows.com/). Saiba mais sobre as funcionalidades e ícones do portal, na [visão geral do portal do Microsoft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Enviar um alerta de teste

Para gerar um alerta de teste do Microsoft Defender benigno para o ponto final:

1. Criar uma pasta 'C:\test-MDATP-test'.
1. Utilize o Ambiente de Trabalho Remoto para aceder a um VM R2 do Windows Server 2012 ou a um VM do Windows Server 2016.
1. Abra uma janela da linha de comandos.
1. No pedido, copie e execute o seguinte comando. A janela de pedido de comando fecha-se automaticamente.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Uma janela de ordem com o comando para gerar um alerta de teste.":::

1. Se o comando for bem sucedido, verá um novo alerta no painel do Azure Security Center e no Microsoft Defender para o portal Endpoint. Este alerta pode levar alguns minutos a aparecer.
1. Para rever o alerta no Centro de Segurança, vá a **Alertas de Segurança**  >  **Alertas Suspeitos powerShell CommandLine**.
1. A partir da janela de investigação, selecione o link para ir ao microsoft Defender para o portal Endpoint.

    > [!TIP]
    > O alerta é desencadeado com **severidade informacional.**

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>FAQ para o Microsoft Defender integrado do Security Center para Endpoint

- [Quais são os requisitos de licenciamento para o Microsoft Defender para Endpoint?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Se já tenho licença para o Microsoft Defender para o Endpoint, posso ter um desconto para o Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Como posso mudar de uma ferramenta EDR de terceiros?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Quais são os requisitos de licenciamento para o Microsoft Defender para Endpoint?
O Defender for Endpoint está incluído sem custos adicionais com **o Azure Defender para servidores**. Alternativamente, pode ser comprado separadamente para 50 máquinas ou mais.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Se já tenho licença para o Microsoft Defender para o Endpoint, posso ter um desconto para o Azure Defender?
Se já tem uma licença para o Microsoft Defender para o Endpoint, não terá de pagar essa parte da licença do Azure Defender.

Para confirmar o seu desconto, contacte a equipa de apoio do Security Center e forneça o ID do espaço de trabalho relevante, região e informações de licença para cada licença relevante.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Como posso mudar de uma ferramenta EDR de terceiros?
As instruções completas para mudar de uma solução de ponto final não microsoft estão disponíveis na documentação do Microsoft Defender for Endpoint: [Visão geral da migração](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Próximos passos

- [Plataformas e funcionalidades suportadas pelo Centro de Segurança do Azure](security-center-os-coverage.md)
- [Gerir recomendações de segurança no Azure Security Center](security-center-recommendations.md): Saiba como as recomendações o ajudam a proteger os seus recursos Azure.