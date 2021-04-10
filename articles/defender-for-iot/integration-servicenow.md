---
title: Sobre a integração do ServiceNow
description: A aplicação de Gestão do ICS do IoT para o ServiceNow proporciona aos analistas da SOC visibilidade multidimensional nos protocolos especializados de OT e dispositivos IoT implantados em ambientes industriais, juntamente com análises comportamentais conscientes do ICS para detetar rapidamente comportamentos suspeitos ou anómalos.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786010"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>O Pedido de Gestão do ICS do IoT para o ServiceNow

A aplicação de Gestão do ICS do IoT para o ServiceNow proporciona aos analistas da SOC visibilidade multidimensional nos protocolos especializados de OT e dispositivos IoT implantados em ambientes industriais, juntamente com análises comportamentais conscientes do ICS para detetar rapidamente comportamentos suspeitos ou anómalos. Esta é uma evolução importante dada a convergência contínua de TI e OT para apoiar novas iniciativas de IoT, como máquinas inteligentes e inteligência em tempo real.

A aplicação também permite a resposta de incidentes de TI e OT dentro de um SOC corporativo.

## <a name="about-defender-for-iot"></a>Sobre o Defender para IoT

O Defender for IoT fornece a única plataforma de cibersegurança ICS e IoT construída por especialistas em equipas azuis com um histórico que defende infraestruturas nacionais críticas, e a única plataforma com análise de ameaças patenteadas do ICS e machine learning. O Defender for IoT fornece:

- Informações imediatas sobre o ICS a paisagem do dispositivo com uma vasta gama de detalhes sobre atributos.

- Conhecimento profundo e profundo do ICS sobre protocolos de OT, dispositivos, aplicações e seus comportamentos.

- Informações imediatas sobre vulnerabilidades e ameaças conhecidas de zero dias.

- Uma tecnologia automatizada de modelação de ameaças do ICS para prever os caminhos mais prováveis de ataques ics direcionados através de análises proprietárias.

> [!Note]
> As referências a CyberX referem-se ao Azure Defender para IoT.

## <a name="about-the-integration"></a>Sobre a integração

O Defender para a integração de IoT com o ServiceNow proporciona um novo nível de visibilidade centralizada, monitorização e controlo para a paisagem IoT e OT. Estas plataformas em ponte permitem a visibilidade automatizada do dispositivo e a gestão de ameaças a dispositivos IoT & anteriormente inacessíveis.

### <a name="threat-management"></a>Gestão de ameaças

A aplicação Defender for IoT ICS Management ajuda:

- Reduza o tempo necessário para que as organizações de infraestruturas industriais e críticas detetem, investiguem e atuem sobre ameaças cibernéticas.

- Obtenha informações em tempo real sobre os riscos de OT.

- Correlate Defender para alertas IoT com monitorização de ameaças serviceNow e fluxos de trabalho de gestão de incidentes.

- Trigger ServiceNow bilhetes e fluxos de trabalho com outros serviços e aplicações na plataforma ServiceNow.

As ameaças à segurança ICS e SCADA são identificadas pelo Defender para motores de segurança IoT, que fornecem resposta imediata a ataques de malware, desvios de redes e políticas de segurança, bem como anomalias operacionais e protocolares. Para mais detalhes sobre os detalhes de alerta enviados para o ServiceNow, consulte [o relatório de alerta](#alert-reporting).

### <a name="device-visibility-and-management"></a>Visibilidade e gestão do dispositivo

A Base de Dados de Gestão de Configuração serviceNow (CMDB) é enriquecida e complementada com um rico conjunto de atributos do dispositivo empurrados pelo Defender para a plataforma IoT. Isto garante uma visibilidade abrangente e contínua na paisagem do dispositivo e permite-lhe monitorizar e responder a partir de um único painel de vidro. Para obter mais informações sobre os atributos do dispositivo enviados ao ServiceNowSee, consulte [o Defender para deteções de IoT no ServiceNow](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Requisitos do sistema e arquitetura

Este artigo descreve:

- **Requisitos de software**  
- **Arquitetura**

## <a name="software-requirements"></a>Requisitos de software

- ServiceNow Service Management versão 3.0.2

- Defender para patch IoT 2.8.11.1 ou superior

> [!Note]
> Se já estiver a trabalhar com um Defender para integração IoT e ServiceNow, e atualizar utilizando a consola de gestão no local, os dados pervos recebidos do Defender para sensores IoT devem ser retirados do ServiceNow.

## <a name="architecture"></a>Arquitetura

### <a name="on-premises-management-console-architecture"></a>Arquitetura de consola de gestão no local

A consola de gestão no local fornece uma fonte unificada para todo o dispositivo e informações de alerta enviadas para o ServiceNow.

Pode configurar uma consola de gestão no local para comunicar com uma instância do ServiceNow. A consola de gestão no local empurra os dados do sensor para a aplicação Defender para IoT utilizando a API REST.

Se estiver a configurar o seu sistema para trabalhar com uma consola de gestão no local, desative o ServiceNow Sync, as Regras de Encaminhamento e as configurações de procuração nos sensores, se forem configurados.

Estas configurações devem ser configurada para a consola de gestão no local. As instruções de configuração são descritas neste artigo.

### <a name="sensor-architecture"></a>Arquitetura sensorial

Se pretender configurar o seu ambiente para incluir a comunicação direta entre sensores e ServiceNow, para cada sensor definir as regras de ServiceNow Sync, Reencaminhamento e configuração de procuração (se for necessário um proxy).

Recomendou a configuração da sua integração utilizando a consola de gestão no local para comunicar com o ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Criar fichas de acesso no ServiceNow

Este artigo descreve como criar um token de acesso no ServiceNow. O símbolo é necessário para comunicar com o Defender para a IoT.

Necessitará do **ID** do Cliente e **da Client Secret** ao criar as regras do Defender para o IoT Forwarding, que encaminham as informações de alerta para o ServiceNow e ao configurar o Defender para ioT para empurrar os atributos do dispositivo para as tabelas Do ServiceNow.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Configurar defender para ioT para comunicar com o ServiceNow

Este artigo descreve como configurar o Defender para ioT para comunicar com o ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Enviar informações de alerta IoT para Defender

Este artigo descreve como configurar o Defender para ioT para empurrar informações de alerta para as tabelas ServiceNow. Para obter informações sobre dados de alerta enviados, consulte [relatórios de alerta](#alert-reporting).

Os defensores dos alertas IoT aparecem no ServiceNow como incidentes de segurança.

Defina uma regra de Defender para *encaminhamento* IoT para enviar informações de alerta para o ServiceNow.

Para definir a regra:

1. No Defender para ioT painel esquerdo, **selecione Forwarding**.  

1. Selecione o :::image type="content" source="media/integration-servicenow/plus.png" alt-text="botão ícone mais."::: . Abre-se a caixa de diálogo 'Regra de encaminhamento' "Criar" "Criar Reencaminhamentos".  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Criar regra de encaminhamento":::

1. Adicione um nome de regra.

1. Defina critérios segundo os quais o Defender para IoT irá desencadear a regra de encaminhamento. Trabalhar com os critérios de regra de encaminhamento ajuda a identificar e gerir o volume de informações enviadas do Defender para IoT para o ServiceNow. Estão disponíveis as seguintes opções:

    - **Níveis de severidade:** Este é o incidente de nível de segurança mínima para avançar. Por exemplo, se **minor** for selecionado, pequenos alertas, e qualquer alerta acima deste nível de gravidade será reencaminhado. Os níveis são pré-definidos pelo Defender para IoT.

    - **Protocolos:** Só desencadeie a regra de encaminhamento se o tráfego detetado estiver a passar por protocolos específicos. Selecione os protocolos necessários da lista de suspensos ou escolha-os todos.

    - **Motores:** Selecione os motores necessários ou escolha-os todos. Serão enviados alertas de motores selecionados.

1. Verifique se as **notificações de alerta de relatório estão selecionadas.**

1. Na secção Ações, **selecione Adicionar** e, em seguida, selecione **ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Selecione ServiceNow a partir das opções de dropdown.":::

1. Introduza os parâmetros de ação ServiceNow:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Preencha os parâmetros de ação ServiceNow":::

1. No painel **de ações,** definir os seguintes parâmetros:

  | Parâmetro | Descrição |
  |--|--|
  | Domínio | Insira o endereço IP do servidor ServiceNow. |
  | Nome de utilizador | Insira o nome de utilizador do servidor ServiceNow. |
  | Palavra-passe | Insira a palavra-passe do servidor ServiceNow. |
  | ID de Cliente | Insira o ID do Cliente que recebeu para Defender para IoT na página **registos de aplicações** no ServiceNow. |
  | Segredo do Cliente | Insira a cadeia secreta do cliente que criou para Defender para IoT na página **registos de aplicações** no ServiceNow. |
  | Tipo de Relatório | **Incidentes**: Encaminhe uma lista de alertas apresentados no ServiceNow com um ID de incidente e uma breve descrição de cada alerta.<br /><br />**Defender para aplicação IoT**: Avance informações completas de alerta, incluindo os detalhes do sensor, o motor, a fonte e os endereços de destino. A informação é reencaminhada para o Defender para IoT sobre a aplicação ServiceNow. |

1. Selecione **SAVE**. Os defensores dos alertas IoT aparecem como incidentes no ServiceNow.

### <a name="send-defender-for-iot-device-attributes"></a>Enviar atributos do dispositivo IoT para Defender

Este artigo descreve como configurar o Defender para ioT para empurrar uma vasta gama de atributos do dispositivo para as tabelas ServiceNow. Consulte ***informações de inventário*** para obter mais informações sobre o tipo de informação empurrada para o ServiceNow.

Para enviar atributos para o ServiceNow, tem de mapear a sua consola de gestão no local para uma instância ServiceNow. Isto garante que o Defender da plataforma IoT pode comunicar e autenticar com a ocorrência.

Para adicionar uma instância ServiceNow:

1. Inscreva-se no seu Defender para consola de gestão IoT no local.

1. Selecione **Definições do Sistema** e, em seguida, **ServiceNow** a partir da secção de integração de consolas de gestão no local.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Selecione o botão ServiceNow.":::

1. Introduza os seguintes parâmetros de sincronização na caixa de diálogo ServiceNow Sync.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="A caixa de diálogo de sincronização ServiceNow.":::

     Parâmetro | Descrição |
    |--|--|
    | Ativar o Sync | Ative e desative a sincronização após a definição dos parâmetros. |
    | Frequência de Sincronização (minutos) | Por predefinição, a informação é empurrada para o ServiceNow a cada 60 minutos. O mínimo é de 5 minutos. |
    | Instância de ServiçoNow | Insira o URL de instância ServiceNow. |
    | ID de Cliente | Insira o ID do Cliente que recebeu para Defender para IoT na página **registos de aplicações** no ServiceNow. |
    | Segredo do Cliente | Introduza a cadeia Secret cliente que criou para Defender para IoT na página **registos de aplicações** no ServiceNow. |
    | Nome de utilizador | Introduza o nome de utilizador para este caso. |
    | Palavra-passe | Insira a senha para este caso. |

1. Selecione **SAVE**.

## <a name="verify-communication"></a>Verificar comunicação

Verifique se a consola de gestão no local está ligada à instância ServiceNow, revendo a data *do Último Sincronização.*

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Verifique se a comunicação ocorreu olhando para a última sincronização.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Configurar as integrações utilizando o proxy HTTPS

Ao configurar o Defender para integração IoT e ServiceNow, a consola de gestão no local e o servidor ServiceNow comunicam utilizando a porta 443. Se o servidor ServiceNow estiver por detrás do proxy, a porta predefinida não pode ser utilizada.

O Defender for IoT suporta um proxy HTTPS na integração Do ServiceNow, permitindo a alteração da porta padrão utilizada para a integração.

Para configurar o representante:

1. Editar propriedades globais na consola de gestão no local:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Adicione os seguintes parâmetros:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Salve e saia.

4. Executar o seguinte comando: `sudo monit restart all`

Após a configuração, todos os dados do ServiceNow são reencaminhados utilizando o proxy configurado.

## <a name="download-the-defender-for-iot-application"></a>Descarregue a aplicação Defender para IoT

Este artigo descreve como descarregar a aplicação.

Para aceder à aplicação Defender para IoT:

1. Navegue para <https://store.servicenow.com/>

2. Procure `Defender for IoT` `CyberX IoT/ICS Management` ou.

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Procure cyberx na barra de pesquisa.":::

3. Selecione a inscrição.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Selecione a inscrição na lista.":::

4. Selecione **App de pedido.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Inscreva-se na aplicação com as suas credenciais.":::

5. Faça o s.a.s. Iniciar sção e descarregar a aplicação.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Ver Defender para deteções de IoT no ServiceNow

Este artigo descreve os atributos do dispositivo e as informações de alerta apresentadas no ServiceNow.

Para ver os atributos do dispositivo:

1. Inscreva-se no ServiceNow.

2. Navegue para a **Plataforma CyberX.**

3. Navegue para **Inventário** ou **Alerta.**

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Inventário ou Alerta":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Informação de inventário

A Base de Dados de Gestão de Configuração (CMDB) é enriquecida e complementada por dados enviados pelo Defender para IoT para ServiceNow. Ao adicionar ou atualizar os atributos do dispositivo nas tabelas de item de configuração CMDB do ServiceNow, o Defender para IoT pode desencadear os fluxos de trabalho do ServiceNow e as regras de negócio.

Estão disponíveis as seguintes informações:

- Atributos do dispositivo, por exemplo, mac, SISTEMA, fornecedor ou protocolo detetado.

- Informações de firmware, por exemplo, a versão firmware e o número de série.

- Informações ligadas ao dispositivo, por exemplo, a direção do tráfego entre a fonte e o destino.

### <a name="devices-attributes"></a>Atributos de dispositivos

Este artigo descreve os atributos do dispositivo empurrados para o ServiceNow.

| Item | Descrição |
|--|--|
| Aparelho | O nome do sensor que detetou o trânsito. |
| ID | O ID do dispositivo atribuído pelo Defender para IoT. |
| Name | O nome do dispositivo. |
| Endereço IP | O endereço IP ou endereços IP do dispositivo. |
| Tipo | O tipo de dispositivo, por exemplo, um interruptor, PLC, historiador ou controlador de domínio. |
| Endereço MAC | O endereço ou endereço mac do dispositivo. |
| Sistema Operativo | O sistema operativo do dispositivo. |
| Fornecedor | O fornecedor de dispositivos. |
| Protocolos | Os protocolos detetados no tráfego gerado pelo dispositivo. |
| Proprietário | Insira o nome do proprietário do dispositivo. |
| Localização | Introduza a localização física do dispositivo. |

Ver dispositivos ligados a um dispositivo nesta vista.

Para visualizar dispositivos conectados:

1. Selecione um dispositivo e, em seguida, selecione o **aparelho** listado para este dispositivo.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Selecione o aparelho pretendido da lista.":::

1. Na caixa de diálogo Detalhes do **Dispositivo,** selecione **Dispositivos Conectados**.

### <a name="firmware-details"></a>Detalhes do firmware

Este artigo descreve as informações do firmware do dispositivo empurradas para o ServiceNow.

| Item | Descrição |
|--|--|
| Aparelho | O nome do sensor que detetou o trânsito. |
| Dispositivo | O nome do dispositivo. |
| Endereço | O endereço IP do dispositivo. |
| Endereço do módulo | O modelo do dispositivo e o número da ranhura ou iD. |
| Série | O número de série do dispositivo. |
| Modelação | O número do modelo do dispositivo. |
| Versão | O número da versão do firmware. |
| Dados Adicionais | Mais dados sobre o firmware definidos pelo fornecedor, por exemplo, o tipo de dispositivo. |

### <a name="connection-details"></a>Detalhes da ligação

Este artigo descreve as informações de ligação do dispositivo empurradas para o ServiceNow.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="A informação de ligação do dispositivo":::

| Item | Descrição |
|--|--|
| Aparelho | O nome do sensor que detetou o trânsito. |
| Direção | A direção do trânsito. <br /> <br /> - **One Way** indica que o Destino é o servidor e a Fonte é o cliente. <br /> <br /> - **Two Way** indica que tanto a fonte como o destino são servidores, ou que o cliente é desconhecido. |
| ID do dispositivo de origem | O endereço IP do dispositivo que comunicava com o dispositivo ligado. |
| Nome do dispositivo de origem | O nome do dispositivo que comunicava com o dispositivo ligado. |
| ID do dispositivo de destino | O endereço IP do dispositivo conectado. |
| Nome do dispositivo de destino | O nome do dispositivo ligado. |

## <a name="alert-reporting"></a>Relatório de alerta

Os alertas são acionados quando os Defensores dos motores IoT detetam alterações no tráfego da rede e no comportamento que requerem a sua atenção. Para obter detalhes sobre os tipos de alertas que cada motor gera, consulte [Sobre os motores de alerta.](#about-alert-engines)

Este artigo descreve as informações de alerta do dispositivo empurradas para o ServiceNow.

| Item | Descrição |
|--|--|
| Criado | A hora e a data em que o alerta foi gerado. |
| Motor | O motor que detetou o evento. |
| Título | O título de alerta. |
| Description | A descrição do alerta. |
| Protocolo | O protocolo detetado no trânsito. |
| Gravidade | A gravidade do alerta definida pelo Defender para IoT. |
| Aparelho | O nome do sensor que detetou o trânsito. |
| Nome de origem | O nome da fonte. |
| Endereço IP de origem| O endereço IP de origem. |
| Nome do destino | O nome do destino. |
| Endereço IP de destino | O endereço IP de destino. |
| Destinatário | Insira o nome do indivíduo designado para o bilhete. |

### <a name="updating-alert-information"></a>Atualizar informações de alerta

Selecione a entrada na coluna criada para ver informações de alerta num formulário. Pode atualizar detalhes de alerta e atribuir o alerta a um indivíduo para rever e manusear.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Veja a informação do alerta.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Sobre motores de alerta

Este artigo descreve o tipo de alertas que cada motor dispara.

| Tipo de alerta | Description |
|--|--|
| Alertas de violação de políticas | Acionado quando o motor de Violação de Política deteta um desvio do tráfego previamente aprendido. Por exemplo: <br /><br />- Um novo dispositivo é detetado. <br /><br />- É detetada uma nova configuração num dispositivo. <br /><br />- Um dispositivo não definido como dispositivo de programação realiza uma alteração de programação. <br /><br />- Uma versão de firmware mudou. |
| Alertas de violação de protocolo | Acionado quando o motor de Violação de Protocolo deteta estruturas de pacotes ou valores de campo que não cumprem a especificação do protocolo. |
| Alertas operacionais | Acionado quando o motor Operacional deteta incidentes operacionais da rede ou avarias no dispositivo. Por exemplo, um dispositivo de rede foi parado usando um comando Stop PLC, ou uma interface num sensor parou de monitorizar o tráfego. |
| Alertas de malware | Desencadeado quando o motor malware deteta atividade de rede maliciosa, por exemplo, ataques conhecidos como Conficker. |
| Alertas de anomalia | Acionado quando o motor de anomalia deteta um desvio. Por exemplo, um dispositivo está a realizar a varredura de rede, mas não é definido como um dispositivo de digitalização. |

## <a name="next-steps"></a>Passos seguintes

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
