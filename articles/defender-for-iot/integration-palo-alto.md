---
title: Integração palo Alto
titleSuffix: Azure Defender for IoT
description: O Defender for IoT integrou a sua plataforma contínua de monitorização de ameaças ics com as firewalls de próxima geração de Palo Alto para permitir o bloqueio de ameaças críticas, de forma mais rápida e eficiente.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 85a7622223861f857ce75b8136b509ba279f3d96
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98558667"
---
# <a name="about-the-palo-alto-integration"></a>Sobre a integração de Palo Alto

O Defender for IoT integrou a sua plataforma contínua de monitorização de ameaças ics com as firewalls de próxima geração de Palo Alto para permitir o bloqueio de ameaças críticas, de forma mais rápida e eficiente.

Estão disponíveis os seguintes tipos de integração:

- Opção de bloqueio automático: Defender Direto para integração IoT-Palo Alto

- Envio de recomendações para bloqueio ao sistema de gestão central: Defender para a integração IoT-Panorama

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>Configure o bloqueio imediato por firewall de Palo Alto especificado

Em casos críticos, como alertas relacionados com malware, pode ativar o bloqueio automático. Isto é feito configurando uma regra de encaminhamento no Defender para IoT que envia o comando de bloqueio diretamente para uma firewall palo Alto específica.

Quando o Defender for IoT identifica uma ameaça crítica, envia um alerta que inclui uma opção de bloquear a fonte infetada. Selecionar **a Fonte** de Bloco nos detalhes do alerta ativa a regra de encaminhamento, que envia o comando de bloqueio para a firewall de Palo Alto especificada.

Para configurar:

1. No painel esquerdo, selecione **Forwarding**.

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="O ecrã de alerta de encaminhamento.":::

1. Selecione **Criar Regra de Encaminhamento**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Criar regra de encaminhamento":::

1. Para configurar a Regra de Encaminhamento Palo Alto NGFW:  
 
   - Defina os parâmetros de regra padrão e a partir da caixa de lançamento de **Ações,** selecione **Enviar para Palo Alto NGFW**.
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="Edite a sua Regra de Encaminhamento.":::

1. No painel de ações, definir os seguintes parâmetros:

   - **Anfitrião**: Introduza o endereço IP do servidor NGFW.
   - **Porta**: Introduza a porta do servidor NGFW.
   - **Nome de utilizador**: Introduza o nome de utilizador do servidor NGFW.
   - **Palavra-passe**: Introduza a palavra-passe do servidor NGFW.
   - **Configure : Configure:** Configurar as seguintes opções para permitir o bloqueio das fontes suspeitas pela firewall de Palo Alto:
     - **Bloquear códigos de função ilegais**: Violações de protocolo - Valor de campo ilegal violando a especificação do protocolo ICS (potencial exploração).
     - **Bloqueie atualizações não autorizadas de programação/firmware plc**: Alterações não autorizadas de PLC.
     - **Bloquear paragem plc não autorizada**: paragem PLC (tempo de inatividade).
     - **Bloquear alertas relacionados com malware**: Bloqueio de tentativas de malware industrial (TRITON, NotPetya, etc.). Pode selecionar a opção de **bloqueio automático.** Nesse caso, o bloqueio é executado automaticamente e imediatamente.
     - **Bloquear a digitalização não autorizada**: Digitalização não autorizada (reconhecimento potencial).
     
1. Selecione **Submeter**.

Para bloquear a fonte suspeita: 

1. No painel **alerta,** selecione o alerta relacionado com a integração de Palo Alto. Aparece a caixa de diálogo Detalhes de **Alerta.**
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Detalhes do alerta":::

1. Para bloquear automaticamente a fonte suspeita, selecione **Block Source**. Aparece a caixa de diálogo **Por favor confirme.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirme o bloqueio no ecrã Por favor confirme.":::

1. Na caixa de diálogo **Por favor confirme,** selecione **OK**. A fonte suspeita está bloqueada pela firewall de Palo Alto.

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>Envio de políticas de bloqueio para Palo Alto Panorama

O Defender for IoT e palo Alto Networks tem uma integração off-the-shelf que cria automaticamente novas políticas em Palo Alto Networks NMS, Panorama. Esta integração requer confirmação pelo Administrador do Panorama e não permite o bloqueio automático.

A integração destina-se aos seguintes incidentes:

- **Alterações não autorizadas de PLC:** Uma atualização para a lógica da escada ou firmware de um dispositivo. Isto pode representar uma atividade legítima ou uma tentativa de comprometer o dispositivo. O compromisso pode acontecer inserindo código malicioso, como um Cavalo de Troia de Acesso Remoto (RAT) ou parâmetros que causam o processo físico, como uma turbina giratória, para operar de forma insegura.

- **Violação do protocolo:** Uma estrutura de pacote ou valor de campo que viola a especificação do protocolo. Isto pode representar uma aplicação mal configurada ou uma tentativa maliciosa de comprometer o dispositivo. Por exemplo, causando uma condição de transbordo de tampão no dispositivo alvo.

- **Paragem plc:** Um comando que faz com que o dispositivo pare de funcionar, arriscando assim o processo físico que está a ser controlado pelo PLC.

- **Malware industrial encontrado na rede ICS:** Malware que manipula dispositivos ICS usando os seus protocolos nativos, como TRITON e Industroyer. O Defender for IoT também deteta malware de TI que se moveu lateralmente para o ambiente ICS e SCADA, como Conficker, WannaCry e NotPetya.

- **Digitalização de malware:** Ferramentas de reconhecimento que recolhem dados sobre a configuração do sistema numa fase pré-ataque. Por exemplo, o Havex Trojan verifica redes industriais para dispositivos que utilizam OPC, que é um protocolo padrão usado pelos sistemas SCADA baseados no Windows para comunicar com dispositivos ICS.

## <a name="the-process"></a>O processo

Quando o Defender para IoT detetar uma caixa de utilização pré-configurada, o botão **Block Source** é adicionado ao alerta. Em seguida, quando o utilizador **CyberX** seleciona o botão **Block Source,** o Defender para IoT cria políticas no Panorama enviando a regra de encaminhamento predefinido.

A política só é aplicada quando o administrador do Panorama a empurra para o NGFW relevante na rede.

Nas redes de TI, pode haver endereços IP dinâmicos. Portanto, para essas sub-redes, a política deve basear-se no FQDN (nome DNS) e não no endereço IP. O Defender para IoT executa dispositivos de pesquisa inversa e correspondem dispositivos com endereço IP dinâmico ao seu FQDN (nome DNS) a cada número de horas configurado.

Além disso, o Defender for IoT envia um e-mail ao utilizador panorama relevante para notificar que uma nova política criada pelo Defender para ioT está a aguardar a aprovação. A figura abaixo apresenta o Defensor da Arquitetura de Integração IoT-Panorama.

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="Arquitetura de Integração CyberX-Panorama":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>Criar políticas de bloqueio panorama no Defender para configuração IoT

### <a name="to-configure-dns-lookup"></a>Para configurar a procura de DNS

1. No painel esquerdo, selecione **Definições do Sistema**.

1. No painel **de definições** do sistema, selecione **Definições DNS** :::image type="icon" source="media/integration-paloalto/settings.png"::: .

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="Configurar as definições DNS.":::

1. Na caixa de diálogo **de definições de EDITAR DNS,** defina os seguintes parâmetros:

   - **Estado**: O estado do DNS resolver.

   - **Endereço DO Servidor DNS**: Introduza o endereço IP ou o FQDN do Servidor DNS da rede.
   - **Porta do servidor DNS**: Introduza a porta utilizada para consultar o servidor DNS.
   - **Sub-redes**: Deslote a gama de sub-redes de endereços IP dinâmico. O intervalo que o Defender for IoT inverte a procura do seu endereço IP no servidor DNS para corresponder ao nome atual do FQDN.
   - **Agendar Reverse Lookup**: Defina as opções de agendamento da seguinte forma:
     - Por tempos específicos: Especifique quando efetuar a pesquisa inversa diariamente.
     - Por intervalos fixos (em horas): Defina a frequência para a realização do lookup inverso.
   - **Número de Etiquetas**: Instrua o Defender para ioT a resolver automaticamente endereços IP de rede para dispositivoS FQDNs. <br />Para configurar a resolução DNS FQDN, adicione o número de etiquetas de domínio a exibir. São exibidos até 30 caracteres da esquerda para a direita.
1. Selecione **SAVE**.
1. Para garantir que as definições de DNS estão corretas, selecione **Lookup Test**. O teste garante que o endereço IP do servidor DNS e a porta do servidor DNS estão corretamente definidos.

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>Para configurar uma Regra de Encaminhamento para bloquear suspeitas de tráfego com a firewall de Palo Alto

1. No painel esquerdo, selecione **Forwarding**. Aparece o painel de reencaminhamento.

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="O ecrã de encaminhamento.":::

1. No painel **de encaminhamento,** selecione **Criar Regra de Encaminhamento**.

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="Criar regra de encaminhamento":::

1. Para configurar a Regra de Encaminhamento do Panorama palo Alto:

   Defina os parâmetros de regra padrão e a partir da caixa de lançamento de **Ações,** selecione **Enviar para Palo Alto Panorama**. O painel de detalhes de ação aparece.

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="Selecione ação":::

1. No painel de ações, definir os seguintes parâmetros:

   - **Anfitrião**: Introduza o endereço IP do servidor Panorama.

   - **Porta**: Introduza a porta do servidor Panorama.
   - **Nome de utilizador**: Introduza o nome de utilizador do servidor Panorama.
   - **Palavra-passe**: Introduza a palavra-passe do servidor Panorama.
   - **Endereço do relatório**: Defina como o bloqueio é executado, da seguinte forma:
   
     - **Por Endereço IP**: Cria sempre políticas de bloqueio no Panorama com base no endereço IP.
     
     - **Por FQDN ou Endereço IP**: Cria políticas de bloqueio no Panorama com base no FQDN se existir, caso contrário, pelo Endereço IP.
     
   - **Email**: Definir o endereço de e-mail para o e-mail de notificação de política
     > [!NOTE]
     > Certifique-se de que configura um Servidor de Correio no Defender para IoT. Se não for inserido nenhum endereço de e-mail, o Defender para IoT não envia um e-mail de notificação.
   - **Execute uma procura de DNS após deteção de alerta (Caixa de verificação)**: Quando a opção por FQDN ou endereço IP estiver definida no Endereço relatório. Esta caixa de verificação é selecionada por predefinição. Se apenas o endereço IP estiver definido, esta opção está desativada..
   - **Configure**: Configurar as seguintes opções para permitir o bloqueio das fontes suspeitas pelo Panorama Palo Alto:
   
     - **Bloquear códigos de função ilegais**: Violações de protocolo - Valor de campo ilegal violando o ICS, especificação de protocolo (exploração potencial).
     
     - **Bloqueie atualizações não autorizadas de programação/firmware plc**: Alterações não autorizadas de PLC.
     
     - **Bloquear paragem plc não autorizada**: paragem PLC (tempo de inatividade).
     
     - **Bloquear alertas relacionados com malware**: Bloqueio de tentativas de malware industrial (TRITON, NotPetya, etc.). Pode selecionar a opção de **bloqueio automático.** Nesse caso, o bloqueio é executado automaticamente e imediatamente.
     
     - **Bloquear a digitalização não autorizada**: Digitalização não autorizada (reconhecimento potencial).
     
1. Selecione **Submeter**.

### <a name="to-block-the-suspicious-source"></a>Para bloquear a fonte suspeita

1. No painel **alerta,** selecione o alerta relacionado com a integração de Palo Alto. Aparece a caixa de diálogo **'Detalhes do Alerta'.**

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="Detalhes do alerta":::        

1. Para bloquear automaticamente a fonte suspeita, selecione **Block Source**.

1. Na caixa de diálogo **Por favor confirme,** selecione **OK.**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="Confirmar":::

## <a name="next-steps"></a>Passos seguintes

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
