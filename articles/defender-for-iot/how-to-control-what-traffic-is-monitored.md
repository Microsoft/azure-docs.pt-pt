---
title: Controlar que tráfego é monitorizado
description: Os sensores executam automaticamente a deteção de pacotes profundos para tráfego de TI e OT e resolvem informações sobre dispositivos de rede, tais como atributos de dispositivos e comportamento de rede. Várias ferramentas estão disponíveis para controlar o tipo de tráfego que cada sensor deteta.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: b80ef4a4a1fca7383da80fda6fdf42831913f2bd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784446"
---
# <a name="control-what-traffic-is-monitored"></a>Controlar que tráfego é monitorizado

Os sensores executam automaticamente a deteção de pacotes profundos para tráfego de TI e OT e resolvem informações sobre dispositivos de rede, tais como atributos e comportamento do dispositivo. Várias ferramentas estão disponíveis para controlar o tipo de tráfego que cada sensor deteta.

## <a name="learning-and-smart-it-learning-modes"></a>Modos de Aprendizagem e Aprendizagem de TI Inteligentes

O modo Learning instrui o seu sensor para aprender a atividade habitual da sua rede. Exemplos são dispositivos descobertos na sua rede, protocolos detetados na rede, transferências de ficheiros entre dispositivos específicos e muito mais. Esta atividade torna-se a sua linha de base de rede.

O modo Learning é automaticamente ativado após a instalação e permanecerá ativado até ser desligado. O período de modo de aprendizagem aproximado é entre duas a seis semanas, dependendo do tamanho e complexidade da rede. Após este período, quando o modo de aprendizagem for desativado, qualquer nova atividade detetada irá desencadear alertas. Os alertas são acionados quando o motor da política descobre desvios em comparação com a linha de base aprendida.

Após o período de aprendizagem estar concluído e o modo Learning ser desativado, o sensor pode detetar um nível invulgarmente elevado de alterações de base que são o resultado da atividade normal de TI, como pedidos DNS e HTTP. A atividade é chamada comportamento não determinístico de TI. O comportamento também pode desencadear alertas de violação de políticas desnecessários e notificações do sistema. Para reduzir a quantidade destes alertas e notificações, pode ativar a função **Smart IT Learning.**

Quando o Smart IT Learning está ativado, o sensor rastreia o tráfego de rede que gera comportamento não detetíno de TI baseado em cenários de alerta específicos.

O sensor monitoriza este tráfego durante sete dias. Se detetar o mesmo tráfego de TI não desdeterminístico nos sete dias, continua a monitorizar o tráfego por mais sete dias. Quando o tráfego não é detetado durante sete dias, o Smart IT Learning é desativado para esse cenário. O novo tráfego detetado para este cenário só irá gerar alertas e notificações.

Trabalhar com o Smart IT Learning ajuda-o a reduzir o número de alertas e notificações desnecessários causados por cenários de TI ruidosos.

Se o seu sensor for controlado pela consola de gestão no local, não poderá desativar os modos de aprendizagem. Em casos como este, o modo de aprendizagem só pode ser desativado a partir da consola de gestão.

As capacidades de aprendizagem (Learning and Smart IT Learning) são ativadas por padrão.

Para ativar ou desativar a aprendizagem:

- Selecione **Definições do Sistema** e altere as opções **de Aprendizagem** e **Aprendizagem de TI Inteligentes.**

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Definições do sistema para alternar ecrã.":::

## <a name="configure-subnets"></a>Subespoiações de configuração

As configurações da sub-rede afetam a forma como vê os dispositivos no mapa do dispositivo.

Por predefinição, o sensor descobre a configuração da sub-rede e povoa a caixa de diálogo **de configuração sub-rede** com esta informação.

Para ativar o foco nos dispositivos OT, os dispositivos de TI são automaticamente agregados por sub-rede no mapa do dispositivo. Cada sub-rede é apresentada como uma única entidade no mapa, incluindo uma capacidade de colapso/expansão interativa para "perfurar" numa sub-rede de TI e de volta.

Quando estiver a trabalhar com sub-redes, selecione as sub-redes ICS para identificar as sub-redes OT. Em seguida, pode focar a vista do mapa nas redes OT e ICS e entrar em colapso ao mínimo a apresentação de elementos de rede de TI. Este esforço reduz o número total de dispositivos apresentados no mapa e fornece uma imagem clara dos elementos da rede OT e ICS.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Screenshot que mostra filtragem para uma rede.":::

Pode alterar a configuração ou alterar manualmente a informação da sub-rede exportando os dados descobertos, alterando-os manualmente e, em seguida, importando de volta a lista de sub-redes que definiu manualmente. Para obter mais informações sobre exportação e importação, consulte [as informações sobre o dispositivo de importação.](how-to-import-device-information.md)

Em alguns casos, como ambientes que utilizam gamas públicas como gamas internas, pode instruir o sensor para resolver todas as subesí redes como subesí redes internas selecionando a opção **Não Detetar a Atividade da Internet.** Quando selecionar esta opção:

- Os endereços IP públicos serão tratados como endereços locais.

- Não serão enviados alertas sobre atividades não autorizadas na Internet, o que reduz as notificações e alertas recebidos em endereços externos.

Para configurar sub-redes:

1. No menu lateral, selecione **Definições do Sistema**.

2. Na janela Definição do **Sistema,** selecione **Sub-redes**.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Screenshot que mostra o ecrã de configuração da sub-rede."::: 

3. Para adicionar sub-redes automaticamente quando forem descobertos novos dispositivos, mantenha a **Aprendizagem de Subnetas Automáticas** selecionada.

4. Para resolver todas as sub-redes como sub-redes internas, selecione **Não Detete a Atividade da Internet**.

5. **Selecione Adicionar rede** e definir os seguintes parâmetros para cada sub-rede:

    - O endereço IP da sub-rede.
    - O endereço da máscara da sub-rede.
    - O nome da sub-rede. Recomendamos que nomeie cada sub-rede com um nome significativo que possa facilmente identificar, para que possa diferenciar entre redes de TI e OT. O nome pode chegar aos 60 caracteres.

6. Para marcar esta sub-rede como sub-rede OT, selecione **sub-rede ICS**.

7. Para apresentar a sub-rede separadamente quando estiver a organizar o mapa de acordo com o nível purdue, selecione **Segregated**.

8. Para eliminar uma sub-rede, selecione :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false"::: .

9. Para eliminar todas as sub-redes, selecione **Clear All**.

10. Para exportar sub-redes configuradas, selecione **Export**. A tabela de sub-redes é descarregada para a sua estação de trabalho.

11. Selecione **Guardar**.

### <a name="importing-information"></a>Informação de importação 

Para importar informações de sub-redes, selecione **Import** e selecione um ficheiro CSV para importar. A informação da sub-rede é atualizada com a informação que importou. Se importante um campo vazio, perderá os seus dados.

## <a name="detection-engines"></a>Motores de deteção

Os motores de análise de autoaprendizagem eliminam a necessidade de atualizar assinaturas ou definir regras. Os motores utilizam análises comportamentais específicas do ICS e ciência de dados para analisar continuamente o tráfego da rede OT para anomalias, malware, problemas operacionais, violações de protocolos e desvios de atividade da rede de base.

> [!NOTE]
> Recomendamos que ative todos os motores de segurança.

Quando um motor deteta um desvio, um alerta é acionado. Pode visualizar e gerir alertas a partir do ecrã de alerta ou de um sistema de parceiros.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Screenshot que mostra deteção de desvio.":::

O nome do motor que desencadeou o alerta aparece sob o título de alerta.

### <a name="protocol-violation-engine"></a>Motor de violação de protocolo 

Uma violação do protocolo ocorre quando a estrutura do pacote ou os valores de campo não cumprem a especificação do protocolo.

Cenário de exemplo: alerta *"Operação ilegal de MODBUS (Código de Função Zero)".* Este alerta indica que um dispositivo primário enviou um pedido com o código de função 0 para um dispositivo secundário. Esta ação não é permitida de acordo com a especificação do protocolo, e o dispositivo secundário pode não manusear corretamente a entrada.

### <a name="policy-violation-engine"></a>Motor de violação de políticas

Uma violação de política ocorre com um desvio do comportamento de base definido em configurações aprendidas ou configuradas.

Cenário de exemplo: alerta *"Agente de utilizador HTTP Não Autorizado".* Este alerta indica que uma aplicação que não tenha sido aprendida ou aprovada pela apólice é utilizada como cliente HTTP num dispositivo. Este pode ser um novo navegador web ou aplicação nesse dispositivo.

### <a name="malware-engine"></a>Motor de malware

O motor malware deteta atividade de rede maliciosa.

Cenário de exemplo: alerta *"Suspeita de Atividade Maliciosa (Stuxnet)".* Este alerta indica que o sensor detetou atividades suspeitas de rede conhecidas por estarem relacionadas com o malware Stuxnet. Este malware é uma ameaça persistente avançada direcionada ao controlo industrial e redes SCADA.

### <a name="anomaly-engine"></a>Motor de anomalia

O motor de anomalia deteta anomalias no comportamento da rede.

Cenário de exemplo: alerta *"Comportamento periódico no Canal de Comunicação".* O componente inspeciona as ligações de rede e encontra comportamento periódico e cíclico da transmissão de dados. Este comportamento é comum nas redes industriais.

### <a name="operational-engine"></a>Motor operacional

O motor Operacional deteta incidentes operacionais ou entidades com avarias.

Cenário de exemplo: alerta *"O dispositivo é suspeito de estar desligado (sem resposta)".* Este alerta é levantado quando um dispositivo não está a responder a qualquer tipo de pedido para um período predefinido. Este alerta pode indicar uma paragem do dispositivo, desconexão ou mau funcionamento.

### <a name="enable-and-disable-engines"></a>Ativar e desativar motores

Quando desativar um motor de política, a informação que o motor gera não estará disponível para o sensor. Por exemplo, se desativar o motor Anomaly, não receberá alertas sobre anomalias de rede. Se criares uma regra de reencaminhamento, as anomalias que o motor aprende não serão enviadas. Para ativar ou desativar um motor de política, selecione **Ativado** ou **desativado** para o motor específico.

A pontuação geral é exibida no canto inferior direito do ecrã **'Definições do Sistema'.** A pontuação indica a percentagem de proteção disponível ativa através dos motores de proteção contra ameaças. Cada motor é 20% da proteção disponível.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Screenshot que mostra uma pontuação.":::

## <a name="configure-dhcp-address-ranges"></a>Configurar intervalos de endereços DHCP

A sua rede pode consistir em endereços IP estáticos e dinâmicos. Tipicamente, endereços estáticos são encontrados em redes OT através de historiadores, controladores e dispositivos de infraestrutura de rede, tais como interruptores e routers. A alocação dinâmica ip é normalmente implementada em redes de hóspedes com portáteis, computadores, smartphones e outros equipamentos portáteis (usando conexões físicas Wi-Fi ou LAN em diferentes locais).

Se estiver a trabalhar com redes dinâmicas, lida com alterações de endereços IP que ocorrem quando são atribuídos novos endereços IP. Fá-lo definindo os intervalos de endereços DHCP.

As alterações podem ocorrer, por exemplo, quando um servidor DHCP atribui endereços IP.

A definição de endereços IP dinâmicos em cada sensor permite um suporte abrangente e transparente em casos de alterações de endereços IP. Isto garante uma comunicação abrangente para cada dispositivo único.

A consola de sensores apresenta o endereço IP mais atual associado ao dispositivo e indica quais os dispositivos dinâmicos. Por exemplo:

- O relatório de mineração de dados e relatório de inventário de dispositivos consolidam toda a atividade aprendida com o dispositivo como uma entidade, independentemente das alterações no endereço IP. Estes relatórios indicam quais os endereços definidos como endereços DHCP.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Screenshot que mostra o inventário do dispositivo.":::

- A janela **Propriedades do Dispositivo** indica se o dispositivo foi definido como um dispositivo DHCP.

Para definir uma gama de endereços DHCP:

1.  No menu lateral, selecione **gamas DHCP** a partir da janela Definições do **Sistema.**

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Screenshot que mostra a seleção de Gamas DHCP.":::

2.  Defina uma nova gama definindo **valores de e para** a **to.**

3.  Opcionalmente: Defina o nome da gama, até 256 caracteres.

4.  Para exportar as gamas para um ficheiro CSV, selecione **Export**.

5. Para adicionar manualmente várias gamas a partir de um ficheiro CSV, selecione **Import** e, em seguida, selecione o ficheiro.

    > [!NOTE]
    > As gamas que importa de um ficheiro CSV substituem as definições de gama existentes.

6. Selecione **Guardar**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Configurar servidores DNS para resolução de procura inversa

Para melhorar o enriquecimento do dispositivo, pode configurar vários servidores DNS para realizar pesquisas inversas. Pode resolver nomes de anfitriões ou FQDNs associados aos endereços IP detetados nas sub-redes de rede. Por exemplo, se um sensor descobrir um endereço IP, poderá consultar vários servidores DNS para resolver o nome de anfitrião.

Todos os formatos CIDR são suportados.

O nome do anfitrião aparece no inventário do dispositivo, no mapa do dispositivo e nos relatórios.

Pode agendar horários de resolução de procura inversa para intervalos específicos de hora, como a cada 12 horas. Ou pode agendar uma hora específica.

Para definir servidores DNS:

1. Selecione **Definições do Sistema** e, em seguida, selecione **Definições DE DNS**.

2. **Selecione adicionar o servidor DNS**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Screenshot que mostra a seleção de Add DNS Server.":::

3. No campo **de pesquisa de DNS invertido** da Agenda, escolha:

    - Intervalos (por hora).
  
    - Um momento específico. Use a formatação europeia. Por exemplo, use **14:30** e não **14:30** pm .

4. No campo **dns Server Address,** insira o endereço DNS IP.

5. No campo **DNS Server Port,** insira a porta DNS.

6. Resolva os endereços IP da rede para o dispositivo FQDNs. No campo **Número de Etiquetas,** adicione o número de etiquetas de domínio a visualizar. São exibidos até 30 caracteres da esquerda para a direita.

7. No campo **Subnetas,** introduza as sub-redes que pretende que o servidor DNS faça a consulta.

8. Selecione o **alternador Enable** se pretender iniciar o lookup inverso.

### <a name="test-the-dns-configuration"></a>Teste a configuração DE DNS 

Ao utilizar um dispositivo de teste, verifique se as definições definidas funcionam corretamente:

1. Ativar o **alternância dns.**

2. Selecione **Teste**.

3. Introduza um endereço no **Endereço de Procura** para o teste de procura inversa do DNS para a caixa de diálogo do **servidor.**

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Screenshot que mostra a área do endereço de procura.":::

4. Selecione **Teste**.

## <a name="configure-windows-endpoint-monitoring"></a>Configurar monitorização do ponto final do Windows

Com a capacidade de monitorização do Windows Endpoint, pode configurar o Azure Defender para ioT para sondar seletivamente os sistemas Windows. Isto fornece-lhe informações mais focadas e precisas sobre os seus dispositivos, como os níveis de pacote de serviço.

Pode configurar sondagens com gamas e anfitriões específicos e configurá-la para ser executada apenas com a frequência desejada. Realiza sondagens seletivas utilizando a Instrumentação de Gestão do Windows (WMI), que é a linguagem de script padrão da Microsoft para gerir sistemas Windows.

> [!NOTE]
> - Só pode fazer uma tomografia de cada vez.
> - Obtém os melhores resultados com utilizadores que têm privilégios de domínio ou administrador local.
> - Antes de iniciar a configuração do WMI, configure uma regra de firewall que abre o tráfego de saída do sensor para a sub-rede digitalizada utilizando a porta UDP 135 e todas as portas TCP acima de 1024.

Quando a sonda estiver terminada, um ficheiro de registo com todas as tentativas de sondagem está disponível a partir da opção de exportar um registo. O registo contém todos os endereços IP que foram sondados. Para cada endereço IP, o registo mostra informações de sucesso e falhas. Há também um código de erro, que é uma cadeia livre derivada da exceção. A verificação do último registo só é mantida no sistema.

Pode efetuar exames programados ou digitalizações manuais. Quando uma varredura estiver terminada, pode ver os resultados num ficheiro CSV.

**Pré-requisitos**

Configure uma regra de firewall que abre o tráfego de saída do sensor para a sub-rede digitalizada utilizando a porta UDP 135 e todas as portas TCP acima de 1024.

Para configurar uma varredura automática:

1. No menu lateral, selecione **Definições do Sistema**.

2. Selecione **Monitorização do ponto final do Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Screenshot que mostra a seleção de Monitoração de Pontos Finais do Windows.":::

3. No painel **de verificação,** configurar opções da seguinte forma:

      - **Por intervalos fixos (em horas)**: Desa estale o horário de digitalização de acordo com intervalos em horas.

      - **Por horários específicos**: Desa estale o calendário de digitalização de acordo com as horas específicas e selecione **Save Scan**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Screenshot que mostra o botão Save Scan.":::

4. Para definir a gama de digitalização, selecione **gamas de digitalização**.

5. Desaça o intervalo de endereços IP e adicione o seu utilizador e palavra-passe.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Screenshot que mostra a adição de um utilizador e senha.":::

6. Para excluir uma gama de IP de uma verificação, **selecione Desativar** ao lado do intervalo.

7. Para remover um intervalo, selecione :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: ao lado do alcance.

8. Selecione **Guardar**. A caixa de diálogo **de configuração de configuração editar** fecha-se e o número de intervalos aparece no painel **de verificação de gamas.**

Para efetuar uma varredura manual:

1. No menu lateral, selecione **Definições do Sistema**.

2. Selecione **Monitorização do ponto final do Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: .

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Screenshot que mostra o ecrã de configuração de monitorização do Windows Endpoint.":::

3. No painel **de ações,** selecione **Iniciar a verificação**. Uma barra de estado aparece no painel **de Ações** e mostra o progresso do processo de digitalização.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Screenshot que mostra o botão iniciar a verificação.":::

Para visualizar os resultados da verificação:

1. Quando a verificação estiver concluída, no painel **de Ações,** selecione **Ver Resultados da Verificação**. O ficheiro CSV com os resultados da varredura é descarregado para o seu computador.

## <a name="see-also"></a>Ver também

[Investigar deteções de sensores num inventário de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 [Investigue as deteções de sensores no mapa do dispositivo](how-to-work-with-the-sensor-device-map.md)
