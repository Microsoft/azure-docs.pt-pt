---
title: Resolver problemas do sensor e da consola de gestão no local
description: Resolva os problemas do seu sensor e da consola de gestão no local para eliminar quaisquer problemas que possa estar a ter.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 03/14/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: ba68bc3eee94689236792f0270d779357dffde9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103465781"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>Resolver problemas do sensor e da consola de gestão no local

Este artigo descreve ferramentas básicas de resolução de problemas para o sensor e a consola de gestão no local. Além dos itens descritos aqui, pode verificar a saúde do seu sistema das seguintes formas:

**Alertas**: É criado um alerta quando a interface do sensor que monitoriza o tráfego está avariada. 

**SNMP**: A saúde dos sensores é monitorizada através do SNMP. O Azure Defender for IoT responde às consultas do SNMP enviadas a partir de um servidor de monitorização autorizado. 

**Notificações do sistema**: Quando uma consola de gestão controla o sensor, pode encaminhar alertas sobre cópias de segurança do sensor falhadas e sensores desligados.

## <a name="sensor-troubleshooting-tools"></a>Ferramentas de resolução de problemas de sensores

### <a name="investigate-password-failure-at-initial-sign-in"></a>Investigue a falha da palavra-passe no início do início do s-in

Ao iniciar sessão num sensor de seta pré-configurado pela primeira vez, terá de realizar a recuperação da palavra-passe.

Para recuperar a sua palavra-passe:

1. No ecrã de entrada de IoT do Defender, selecione  **a recuperação da palavra-passe**. O ecrã **de recuperação da palavra-passe** abre.

1. Selecione **CyberX** ou **Support**, e copie o identificador único.

1. Navegue até ao portal Azure e selecione **Sites e Sensores.**  

1. Selecione o **separador palavra-passe da consola de gestão recuperar no local.**

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="Selecione o botão de gestão de recuperação no local para descarregar o ficheiro de recuperação.":::

1. Introduza o identificador único que recebeu no ecrã de recuperação da **Palavra-passe** e selecione **Recuperar**. O `password_recovery.zip` ficheiro foi descarregado.

    > [!NOTE]
    > Não altere o ficheiro de recuperação da palavra-passe. É um ficheiro assinado e não funcionará se o adulterares.

1. No ecrã de recuperação da **palavra-passe,** selecione **Upload**. A janela **do Ficheiro de Recuperação de Passwords do Upload** será aberta.

1. **Selecione Procurar** para localizar o seu `password_recovery.zip` ficheiro ou arrastar o para a `password_recovery.zip` janela.

1. Selecione **Next**, e o seu utilizador, e a palavra-passe gerada pelo sistema para a sua consola de gestão aparecerá então.

    > [!NOTE]
    > Quando iniciar sôs o seu sôm numa consola de gestão sensorial ou no local pela primeira vez, estará ligado à subscrição a que o ligou. Se precisar de redefinir a palavra-passe para o utilizador CyberX ou Support, terá de selecionar essa subscrição. Para obter mais informações sobre a recuperação de uma palavra-passe do utilizador CyberX ou Suporte, consulte [as palavras-passe de Reposição](how-to-create-and-manage-users.md#resetting-passwords).

### <a name="investigate-a-lack-of-traffic"></a>Investigar a falta de trânsito

Um indicador aparece na parte superior da consola quando o sensor reconhece que não há tráfego numa das portas configuradas. Este indicador é visível para todos os utilizadores.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="Screenshot do alerta de que não foi detetado tráfego.":::
 
Quando esta mensagem aparecer, pode investigar onde não há trânsito. Certifique-se de que o cabo de envergadura está ligado e que não houve alterações na arquitetura do vão.  

Para obter informações de suporte e resolução de problemas, contacte [o Microsoft Support](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

### <a name="check-system-performance"></a>Verifique o desempenho do sistema 

Quando um novo sensor é implantado ou, por exemplo, o sensor está a funcionar lentamente ou não mostrando quaisquer alertas, pode verificar o desempenho do sistema.

Para verificar o desempenho do sistema:

1. No painel de instrumentos, certifique-se de que `PPS > 0` .

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="Screenshot de um painel de amostras."::: 

1. A partir do menu lateral, selecione **Dispositivos**.

1. Na janela **dispositivos,** certifique-se de que os dispositivos estão a ser descobertos.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="Certifique-se de que os dispositivos são descobertos.":::

1. A partir do menu lateral, selecione **Data Mining**.

1. Na janela **Data Mining,** selecione **ALL** e gere um relatório.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Gere um novo relatório utilizando a mineração de dados.":::

1. Certifique-se de que o relatório contém dados.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="Certifique-se de que o relatório contém dados.":::

1. A partir do menu lateral, selecione **Trends & Statistics**.

1. Na janela **Trends & Statistics,** **selecione Add Widget**.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="Adicione um widget selecionando-o.":::

1. Adicione um widget e certifique-se de que mostra dados.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="Certifique-se de que o widget está a mostrar dados.":::

1. A partir do menu lateral, **selecione Alertas**. A janela **alerta** aparece.

1. Certifique-se de que os alertas foram criados.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="Certifique-se de que foram criados alertas.":::


### <a name="investigate-a-lack-of-expected-alerts"></a>Investigue a falta de alertas esperados

Se a janela **Alertas** não mostrar um alerta que esperava, verifique o seguinte:

- Verifique se o mesmo alerta já aparece na janela **Alertas** como uma reação a uma instância de segurança diferente. Se sim, e este alerta ainda não foi tratado, a consola de sensores não mostra um novo alerta.

- Certifique-se de que não excluiu este alerta utilizando as regras **de Exclusão** de Alerta na consola de gestão. 

### <a name="investigate-widgets-that-show-no-data"></a>Investigue widgets que não mostram dados

Quando os widgets na janela **Trends & Statistics** não mostrarem dados, faça o seguinte:

- [Verifique o desempenho do sistema.](#check-system-performance)

- Certifique-se de que as definições de tempo e de região estão corretamente configuradas e não estão definidas para um futuro. 

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>Investigue um mapa de dispositivos que mostre apenas dispositivos de radiodifusão

Quando os dispositivos apresentados no mapa não parecem estar ligados entre si, pode haver algo de errado com a configuração da porta SPAN. Ou seja, pode estar a ver apenas dispositivos de radiodifusão e sem tráfego unicast.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="Veja os seus dispositivos de radiodifusão.":::

Neste caso, precisa de validar que só pode ver o tráfego de transmissão. Em seguida, peça ao engenheiro de rede para fixar a configuração da porta SPAN para que possa ver o tráfego unicast.

Para validar que está a ver apenas o tráfego de transmissão:

- No ecrã **Data Mining,** crie um relatório utilizando a opção **All.** Em seguida, veja se apenas o tráfego de difusão e multicast (e nenhum tráfego unicast) aparece no relatório.

Ou:

- Grave um PCAP diretamente a partir do interruptor ou ligue um portátil utilizando o Wireshark.

### <a name="connect-the-sensor-to-ntp"></a>Ligue o sensor ao NTP

Pode configurar um sensor autónomo e uma consola de gestão, com os sensores relacionados com ele, para ligar ao NTP.

Para ligar um sensor autónomo à NTP:

- [Contacte a equipa de apoio para obter assistência.](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)

Para ligar um sensor controlado pela consola de gestão à NTP:

- A ligação ao NTP está configurada na consola de gestão. Todos os sensores que a consola de gestão controla obtêm automaticamente a ligação NTP.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>Investigue quando os dispositivos não são mostrados no mapa, ou tem vários alertas relacionados com a Internet

Por vezes, os dispositivos ICS são configurados com endereços IP externos. Estes dispositivos ICS não são apresentados no mapa. Em vez dos dispositivos, uma nuvem de internet aparece no mapa. Os endereços IP destes dispositivos estão incluídos na imagem em nuvem.

Outra indicação do mesmo problema é quando surgem vários alertas relacionados com a Internet.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="Vários alertas relacionados com a Internet.":::

Para corrigir a configuração:

1. Clique com o botão direito no ícone de nuvem no mapa do dispositivo e selecione **Endereços IP de exportação**. Copie as gamas públicas que são privadas e adicione-as à lista de sub-redes. Para obter mais informações, consulte [as sub-redes Configure](how-to-control-what-traffic-is-monitored.md#configure-subnets).

1. Gere um novo relatório de mineração de dados para ligações à Internet.

1. No relatório de mineração de dados, selecione :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: para introduzir o modo de administrador e eliminar os endereços IP dos seus dispositivos ICS.

### <a name="tweak-the-sensors-quality-of-service"></a>Ajuste a qualidade de serviço do sensor

Para guardar os recursos da sua rede, pode limitar a largura de banda de interface que o sensor utiliza para os procedimentos do dia-a-dia.

Para limitar a largura de banda da interface, utilize a `cyberx-xsense-limit-interface` ferramenta CLI que precisa de ser executada com permissões de sudo. A ferramenta obtém os seguintes argumentos:

  - `* -i`: interfaces (exemplo: eth0).

  - `* -l`: limite (exemplo: 30 kbit / 1 mbit). Pode utilizar as seguintes unidades de largura de banda: kbps, mbps, kbit, mbit ou bps.

  - `* -c`: claro (para limpar a limitação da largura de banda da interface).

Para ajustar a qualidade do serviço:

1. Inscreva-se no sensor CLI como Um Defender para utilizador IoT e insira `sudo cyberx-xsense-limit-interface-I eth0 -l value` .

   Por exemplo: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > Para um aparelho físico, utilize a interface em1.

1. Para limpar a limitação da interface, insira `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c` .

## <a name="on-premises-management-console-troubleshooting-tools"></a>Ferramentas de resolução de problemas de consola de gestão no local

### <a name="investigate-a-lack-of-expected-alerts"></a>Investigue a falta de alertas esperados

Se não for apresentado um alerta esperado na janela **Alertas,** verifique o seguinte:

- Verifique se o mesmo alerta já aparece na janela **Alertas** como uma reação a uma instância de segurança diferente. Se sim, e este alerta ainda não foi tratado, um novo alerta não é mostrado.

- Verifique se não excluiu este alerta utilizando as regras **de Exclusão** de Alerta na consola de gestão no local.  

### <a name="tweak-the-quality-of-service"></a>Ajuste a qualidade do serviço

Para poupar os recursos da sua rede, pode limitar o número de alertas enviados para sistemas externos (como e-mails ou SIEM) numa operação de sincronização entre um aparelho e a consola de gestão no local.

O padrão é 50. Isto significa que numa sessão de comunicação entre um aparelho e a consola de gestão no local, não haverá mais de 50 alertas para sistemas externos. 

Para limitar o número de alertas, utilize o `notifications.max_number_to_report` imóvel disponível em `/var/cyberx/properties/management.properties` . Não é necessário reiniciar depois de mudar esta propriedade.

Para ajustar a qualidade do serviço:

1. Inscreva-se como Defensor para utilizador IoT. 

1. Verifique os valores predefinidos:

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   Os seguintes valores predefinidos aparecem:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Editar as definições predefinidos:

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. Editar as definições das seguintes linhas:

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. Guarde as alterações. Não é necessário qualquer reinício.

## <a name="export-information-for-troubleshooting"></a>Informações sobre exportação para resolução de problemas

Além de ferramentas para monitorização e análise da sua rede, pode enviar informações à equipa de apoio para mais investigação. Quando exportar registos, o sensor gerará automaticamente uma palavra-passe única (OTP), única para os registos exportados, num ficheiro de texto separado. 

Para exportar registos:

1. No painel esquerdo, selecione **Definições do Sistema**.

1. Selecione **Exportar Registos**.

    :::image type="content" source="media/how-to-export-information-for-troubleshooting/export-a-log.png" alt-text="Exporte um registo para suporte ao sistema.":::

1. Na caixa Nome de **Ficheiro,** insira o nome do ficheiro que pretende utilizar para a exportação de registos. O padrão é a data atual.

1. Para definir que dados pretende exportar, selecione as categorias de dados:  

    | Categoria de exportação | Description |
    |--|--|
    | **Registos do sistema operativo** | Selecione esta opção para obter informações sobre o estado do sistema operativo. |
    | **Registos de instalação/upgrade** | Selecione esta opção para a investigação dos parâmetros de configuração de instalação e atualização. |
    | **Saída de Sanidade do Sistema** | Selecione esta opção para verificar o desempenho do sistema. |
    | **Registos de dissecação** | Selecione esta opção para permitir uma inspeção avançada da dissecação do protocolo. |
    | **Despejos de KERNEL** | Selecione esta opção para exportar o seu depósito de memória de núcleo. Uma lixeira de memória de núcleo contém toda a memória que o núcleo está a usar no momento do problema que ocorreu neste núcleo. O tamanho do ficheiro de despejo é menor do que o depósito de memória completo. Normalmente, o ficheiro de despejo é cerca de um terço do tamanho da memória física no sistema. |
    | **Reencaminhamento de troncos** | Selecione esta opção para a investigação das regras de encaminhamento. |
    | **Registos SNMP** | Selecione esta opção para receber informações de verificação de saúde SNMP. |
    | **Registos de aplicações de núcleo** | Selecione esta opção para exportar dados sobre a configuração e operação da aplicação principal. |
    | **Comunicação com registos CM** | Selecione esta opção se houver problemas contínuos ou interrupções de ligação com a consola de gestão. |
    | **Registos de aplicações web** | Selecione esta opção para obter informações sobre todos os pedidos enviados a partir da interface web da aplicação. |
    | **Backup do sistema** | Selecione esta opção para exportar uma cópia de segurança de todos os dados do sistema para investigar o estado exato do sistema. |
    | **Estatísticas de dissecação** | Selecione esta opção para permitir uma inspeção avançada das estatísticas do protocolo. |
    | **Registos de dados** | Selecione esta opção para exportar registos a partir da base de dados do sistema. Investigar registos do sistema ajuda a identificar problemas do sistema. |
    | **Configuração** | Selecione esta opção para exportar informações sobre todos os parâmetros configuráveis para se certificar de que tudo foi configurado corretamente. |

1. Para selecionar todas as opções, **selecione All** Next to **Choose Categories**.

1. Selecione **Exportar Registos**.

Os registos exportados são adicionados à lista **de Registos Arquivados.** Envie o OTP à equipa de apoio numa mensagem e meio separados dos registos exportados. A equipa de suporte só poderá extrair registos exportados utilizando o OTP único que é usado para encriptar os registos.

A lista de registos arquivados pode conter até cinco itens. Se o número de itens da lista for além desse número, o item mais antigo é eliminado.

## <a name="see-also"></a>Ver também

- [Ver alertas](how-to-view-alerts.md)

- [Configurar a monitorização MIB do SNMP](how-to-set-up-snmp-mib-monitoring.md)

- [Compreender eventos de desconexão de sensores](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
