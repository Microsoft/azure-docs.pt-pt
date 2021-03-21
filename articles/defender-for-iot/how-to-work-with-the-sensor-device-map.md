---
title: Trabalhar com o mapa de dispositivos do sensor
description: O Mapa do Dispositivo fornece uma representação gráfica dos dispositivos de rede detetados. Use o mapa para analisar e gerir informações do dispositivo, fatias de rede e gerar relatórios.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: edd1438a665e4917d5dd4cdcfba08d9cee01d3bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523843"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Investigar deteções de sensores no Mapa do Dispositivo

O Mapa do Dispositivo fornece uma representação gráfica dos dispositivos de rede detetados. Use o mapa para:

  - Recuperar, analisar e gerir informações do dispositivo.

  - Analise as fatias de rede, por exemplo, grupos de interesse específicos ou camadas Purdue.

  - Gerar relatórios, por exemplo, detalhes e resumos de dispositivos de exportação.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Screenshot do mapa do dispositivo.":::

Para aceder ao mapa:

  - Selecione **o mapa** do dispositivo a partir do ecrã principal da consola.

## <a name="map-search-and-layout-tools"></a>Ferramentas de pesquisa e layout do mapa

As seguintes ferramentas são usadas para trabalhar no mapa.

A sua função de utilizador determina quais as ferramentas disponíveis na janela do Mapa do Dispositivo. Consulte [criar e gerir os utilizadores](how-to-create-and-manage-users.md) para obter detalhes sobre as funções dos utilizadores.

| Símbolo | Description |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Procure por endereço IP ou endereço MAC para um dispositivo específico. Insira o endereço IP ou MAC na caixa de texto. O mapa apresenta o dispositivo que procurou com dispositivos ligados ao mesmo. |
| Destaque de grupo e filtros <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Screenshot dos destaques e filtros do grupo."::: | Filtre ou realce o mapa com base em grupos de dispositivos padrão e personalizados. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Vista de colapso de TI, para permitir uma visão focada nos dispositivos OT e dispositivos de TI de grupo.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Mantenha o arranjo do dispositivo atual no mapa. Por exemplo, se arrastar dispositivos para novas localizações no mapa, os dispositivos permanecerão nestes locais ao sair do mapa. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Ajustar ao ecrã |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | - Ver a camada Purdue identificada para este dispositivo, incluindo o controlo automático, o controlo do processo, a supervisão e a empresa <br /> - Ver ligações entre dispositivos.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Mostrar ou esconder-se entre a emissão e o multicast. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Filtrar os dispositivos no mapa de acordo com a última vez que comunicam com outros dispositivos. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="notificações" border="false"::: | Ver notificações sobre um dispositivo. Por exemplo, se um novo IP foi detetado para um dispositivo usando um endereço MAC existente |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Exportar" border="false"::: | Informações sobre a exportação/importação do dispositivo. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="propriedades" border="false"::: | Ver propriedades básicas do dispositivo para dispositivos selecionados. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Zoom In" border="false"::: ou :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="Zoom out" border="false"::: | Aproxime dentro ou fora dos dispositivos no mapa. |

## <a name="view-ot-elements-only"></a>Ver apenas elementos OT

Por padrão, os dispositivos de TI são automaticamente agregados por sub-rede, de modo que a visão do mapa está focada nas redes OT e ICS. A apresentação dos elementos da rede de TI é desabar ao mínimo, o que reduz o número total de dispositivos apresentados no mapa e fornece uma imagem clara dos elementos da rede OT e ICS.

Cada sub-rede é apresentada como uma única entidade no mapa do dispositivo, incluindo uma capacidade de colapso e expansão interativa para olhar os detalhes de uma sub-rede de TI e de volta.

A figura abaixo mostra uma sub-rede de TI colapsada com 27 elementos de rede de TI.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="sub-rede de TI colapsada com 27 elementos de rede de TI":::

Para permitir o colapso da capacidade das redes de TI:

- Na janela Definições do **Sistema,** certifique-se de que a capacidade de agrupamento de redes de TI toggle está ativada.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Janela de definição do sistema":::

Para expandir uma sub-rede de TI:

1. Para diferenciar as redes de TI e OT, a partir do ecrã 'Definições do Sistema', selecione **Sub-redes**.

   > [!NOTE]
   > Recomenda-se nomear cada sub-rede com nomes significativos no utilizador, de modo a diferenciar facilmente as redes de TI e OT.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Configuração de sub-redes":::

2. Na janela de configuração de **sub-redes de edição,** limpe a caixa de verificação **da sub-rede ICS** para cada sub-rede que pretende definir como sub-rede de TI. As sub-redes de TI parecem colapsadas no mapa do dispositivo com as notificações para dispositivos ICS, como um controlador ou PLC, em redes de TI.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Editar configuração de sub-redes":::

3. Para expandir a rede de TI no mapa, na janela dispositivos, clique com o botão direito e selecione **Expandir a Rede**.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Expanda a sua visão da sua rede.":::

4. Aparece uma caixa de confirmação, notificando-o de que a alteração de layout não pode ser refeita.

5. Selecione **OK**. Os elementos da sub-rede de TI aparecem no mapa.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Para colapsar uma sub-rede de TI:

1.  A partir do painel esquerdo, selecione **Dispositivos**.

2. Na janela Dispositivos, selecione o ícone de colapso. O número em vermelho indica quantas sub-redes de TI expandidas aparecem atualmente no mapa.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Janela do dispositivo":::

3. Selecione as sub-redes(s) que pretende entrar em colapso ou selecionar **Collapse All**. A sub-rede selecionada parece ter caído no mapa.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Fechar Tudo":::

O ícone do colapso é atualizado com o número atualizado de sub-redes de TI expandidas.

## <a name="view-or-highlight-device-groups"></a>Ver ou destacar grupos de dispositivos

Pode personalizar o ecrã do mapa com base em Grupos de Dispositivos. Por exemplo, grupos de dispositivos associados a um protocolo OT específico, VLAN ou sub-rede. Grupos predefinidos estão disponíveis e grupos personalizados podem ser criados.

Ver grupos por:

  - **Destaque:** Destaque os dispositivos que pertencem a um grupo específico em azul.

  - **Filtragem:** Exibir apenas dispositivos que pertençam a um grupo específico.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Vista padrão do seu porto":::

Estão disponíveis os seguintes grupos predefinidos:

| Nome do grupo | Description |
|--|--|
| **Aplicações conhecidas** | Dispositivos que utilizem portas reservadas, como tCP.  |
| **portas não normalizas (padrão)** | Dispositivos que utilizem portas ou portas não standard que não tenham sido designados um pseudónimo. |
| **Protocolos OT (padrão)** | Dispositivos que lidam com tráfego conhecido de OT. |
| **Autorização (predefinição)** | Dispositivos que foram descobertos na rede durante o processo de aprendizagem ou que foram oficialmente autorizados na rede. |
| **Filtros de inventário de dispositivos** | Dispositivos agrupados de acordo com os filtros economizam na tabela de Inventário de Dispositivos. |
| **Intervalos de votação** | Dispositivos agrupados por intervalos de votação. Os intervalos de votação são gerados automaticamente de acordo com canais cíclicos, ou períodos. Por exemplo, 15,0 segundos, 3,0 segundos, 1,5 segundos ou qualquer intervalo. Rever esta informação ajuda-o a saber se os sistemas estão a sondar muito rapidamente ou lentamente. |
| **Programação** | Estações de engenharia e máquinas de programação. |
| **Sub-redes** | Dispositivos que pertencem a uma sub-rede específica. |
| **Rio VLAN** | Dispositivos associados a um ID VLAN específico. |
| **Ligações de sub-redes cruzadas** | Dispositivos que comunicam de uma sub-rede para outra sub-rede. |
| **Alertas pregados** | Dispositivos para os quais o utilizador fixou um alerta. |
| **Simulações de vetores de ataque** | Dispositivos vulneráveis detetados em relatórios de vetores de ataque. Para visualizar estes dispositivos no mapa, selecione a caixa de verificação **do Mapa** do Dispositivo ao gerar o Vetor de Ataque. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Adicione simulações de vetores de ataque":::. |
| **Última visualização** | Dispositivos agrupados pelo tempo que foram vistos pela última vez, por exemplo: Uma hora, seis horas, um dia, sete dias. |
| **Não no Diretório Ativo** | Todos os dispositivos não-PLC que não estão a comunicar com o Ative Directory. |

Para realçar ou filtrar dispositivos:

1. Selecione mapa do **dispositivo** no menu lateral.

2. Selecione o ícone do filtro. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menu":::

3. No painel grupo, selecione o grupo que pretende realçar ou filtrar dispositivos.

4. Selecione **Highlight** ou **Filter**. Alterne a mesma seleção para remover o destaque ou o filtro.

## <a name="define-custom-groups"></a>Definir grupos personalizados

Além de visualizar grupos predefinidos, pode definir grupos personalizados. Os grupos aparecem no mapa do dispositivo, no inventário do dispositivo e nos relatórios de mineração de dados.

> [!NOTE]
> Também pode criar grupos a partir do Inventário de Dispositivos.

Para criar um grupo:

1. Selecione **Dispositivos** no menu lateral. O Mapa do Dispositivo é apresentado.

1. Selecione :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="a definição de grupo"::: para visualizar as definições de grupos.

1. Selecione :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="grupos"::: para criar um novo grupo personalizado.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Crie um ecrã de grupo personalizado":::

1. Adicione o nome do grupo, use até 30 caracteres.

1. Selecione os dispositivos relevantes, da seguinte forma:

   - Adicione os dispositivos deste menu selecionando-os da lista (selecione no botão de seta),<br /> Ou, <br /> 
   - Adicione os dispositivos deste menu copiando-os de um grupo selecionado (selecione no botão de seta)

1. **Selecione Adicionar grupo** para adicionar grupos existentes a grupos personalizados.

### <a name="add-devices-to-a-custom-group"></a>Adicione dispositivos a um grupo personalizado

Pode adicionar dispositivos a um grupo personalizado ou criar um novo grupo personalizado e o dispositivo.

1. Clique com o botão direito de um(s) dispositivo(s) no mapa.

1. **Selecione Adicionar ao grupo**.

1. Introduza um nome de grupo no campo de grupo e selecione +. O novo grupo aparece. Se o grupo já existir, será adicionado ao grupo personalizado existente.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Nome do grupo":::

1. Adicione os dispositivos a um grupo repetindo os passos 1-3.

## <a name="map-zoom-views"></a>Vistas de zoom do mapa

Trabalhar com vistas de mapas ajuda a acelerar os forenses ao analisar grandes redes.

Podem ser apresentadas três vistas de pormenor do dispositivo:

  - [Vista de olho de pássaro](#birds-eye-view)

  - [Tipo de dispositivo e vista de ligação](#device-type-and-connection-view)

  - [Vista detalhada](#detailed-view)

### <a name="birds-eye-view"></a>Vista de olho de pássaro

Esta vista proporciona uma visão de um olhar atento dos dispositivos representados da seguinte forma:

  - Pontos vermelhos indicam dispositivos com alerta(s)

  - Pontos estrelados indicam dispositivos marcados como importantes

  - Pontos pretos indicam dispositivos sem alertas

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Vista de olho de pássaro":::

### <a name="device-type-and-connection-view"></a>Tipo de dispositivo e vista de ligação 

Esta vista apresenta dispositivos representados como ícones no mapa de forma a destacar dispositivos com alertas, tipos de dispositivos e dispositivos conectados.

  - Dispositivos com alertas são exibidos com um anel vermelho

  - Dispositivos sem alertas são exibidos com um anel cinzento

  - Dispositivos exibidos como uma estrela foram marcados como importantes

O ícone do tipo do dispositivo é mostrado com dispositivos ligados.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="vista de conexão":::

### <a name="detailed-view"></a>Vista detalhada 

A visão detalhada apresenta dispositivos e etiquetas e indicadores de dispositivos com as seguintes informações:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Vista detalhada":::

### <a name="control-the-zoom-view"></a>Controlar a vista de zoom

A vista do mapa apresentada depende do nível de zoom do mapa. Alternar entre as vistas do mapa é feito alterando os níveis de zoom.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Controlar a vista de zoom":::

### <a name="enable-simplified-zoom-views"></a>Permitir vistas de zoom simplificadas

Os administradores que pretendam que os analistas de segurança e os utilizadores de RO acedam às vistas de bird-eye e dispositivo e de ligação tipo, devem permitir a opção de visualização simplificada.

Para permitir vistas de mapa simplificadas:

  - Selecione **Definições do Sistema** e, em seguida, altere a opção **'Visualização do mapa simplificado'.**

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Simplificar a vista do mapa":::

## <a name="learn-more-about-devices"></a>Saiba mais sobre dispositivos

Uma vasta gama de ferramentas estão disponíveis para saber mais sobre dispositivos que formam o Mapa do Dispositivo:

- [Etiquetas e indicadores de dispositivos](#device-labels-and-indicators)

- [Vistas rápidas do dispositivo](#device-quick-views)

- [Ver e Gerir propriedades do dispositivo](#view-and-manage-device-properties)

- [Ver tipos de dispositivos](#view-device-types)

- [Backplane](#backplane-properties)

- [Ver uma cronologia de eventos para o dispositivo](#view-a-timeline-of-events-for-the-device)

- [Analisar detalhes e alterações de programação](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Etiquetas e indicadores do dispositivo

Podem aparecer nos dispositivos do mapa os seguintes rótulos e indicadores:

| Etiqueta do dispositivo | Description |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="Nome do anfitrião IP"::: | Nome do anfitrião do endereço IP e endereço IP ou endereços de sub-rede |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Número de alertas"::: | Número de alertas associados ao dispositivo |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Ícone do tipo de dispositivo, por exemplo armazenamento, PLC ou historiador. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="dispositivos agrupados"::: | Número de dispositivos agrupados numa sub-rede numa rede de TI. Neste exemplo 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="período de aprendizagem do dispositivo"::: | Um dispositivo que foi detetado após o período de Aprendizagem e não foi autorizado como um dispositivo de rede. |
| Linha sólida | Ligação lógica entre dispositivos |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Novo dispositivo"::: | Novo dispositivo descoberto depois de aprender está completo. |

### <a name="device-quick-views"></a>Vistas rápidas do dispositivo

Aceda às propriedades e ligações do dispositivo a partir do mapa.

Para abrir o menu de propriedades rápidas:

  - Selecione o :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="menu de propriedades rápidas menu de propriedades rápidas menu":::.

#### <a name="quick-device-properties"></a>Propriedades de dispositivo rápido

Selecione um dispositivo ou vários dispositivos enquanto o ecrã Quick Properties estiver aberto para ver os destaques desses dispositivos:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Propriedades de dispositivo rápido":::

#### <a name="quick-connection-properties"></a>Propriedades de ligação rápida

Selecione uma ligação enquanto o ecrã Quick Properties está aberto para ver os protocolos que são utilizados nesta ligação e quando foram vistos pela última vez:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Propriedades de ligação rápida":::

## <a name="view-and-manage-device-properties"></a>Ver e gerir propriedades do dispositivo

Pode ver as propriedades do dispositivo para cada dispositivo apresentado no mapa. Por exemplo, o nome do dispositivo, tipo ou SISTEMA, ou o firmware ou fornecedor.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Ver e gerir propriedades do dispositivo":::

As seguintes informações podem ser atualizadas manualmente. As informações inseridas manualmente sobrepõem-se às informações descobertas pelo Defender para ioT.

  - Nome

  - Tipo

  - SO

  - Camada purdue

  - Description

| Item | Descrição |
|--|--|
| Informações Básicas | A informação básica necessária. |
| Name | O nome do dispositivo. <br /> Por predefinição, o sensor descobre o nome do dispositivo tal como definido na rede. Por exemplo, um nome definido no servidor DNS. <br /> Se não forem definidos tais nomes, o endereço IP do dispositivo aparece neste campo. <br /> Pode alterar manualmente o nome de um dispositivo. Dê aos seus dispositivos nomes significativos que reflitam a sua funcionalidade. |
| Tipo | O tipo de dispositivo detetado pelo sensor. <br /> Para obter mais informações, consulte [os tipos de dispositivos](#view-device-types). |
| Fornecedor | O fornecedor de dispositivos. Isto é determinado pelos caracteres principais do endereço MAC do dispositivo. Este campo é só para leitura. |
| Sistema Operativo | O dispositivo OS detetado pelo sensor. |
| Camada purdue | A camada Purdue identificada pelo sensor para este dispositivo, incluindo: <br /> - Automático <br /> - Controlo de Processos <br /> - Supervisão <br /> - Enterprise |
| Description | Um campo de texto gratuito. <br /> Adicione mais informações sobre o dispositivo. |
| Atributos | Qualquer informação adicional que tenha sido descoberta sobre o dispositivo durante o período de aprendizagem e que não pertença a outras categorias, aparece na secção de atributos. <br /> A informação é RO. |
| Definições | Pode alterar manualmente as definições do dispositivo para evitar falsos positivos: <br /> - **Dispositivo autorizado**: Durante o período de aprendizagem, todos os dispositivos descobertos na rede são identificados como dispositivos autorizados. Quando um dispositivo é descoberto após o período de aprendizagem, aparece como um dispositivo não autorizado por defeito. Pode alterar esta definição manualmente. <br /> - **Conhecido como Scanner**: Ative esta opção se souber que este dispositivo é conhecido como scanner e não há necessidade de o alertar sobre o mesmo. <br /> - **Dispositivo de programação**: Ative esta opção se souber que este dispositivo é conhecido como um dispositivo de programação e é utilizado para fazer alterações de programação. Identificá-lo como um dispositivo de programação evitará alertas para alterações de programação originárias deste ativo. |
| Grupos personalizados | Os grupos personalizados no mapa do dispositivo no qual este dispositivo participa. |
| Estado | A segurança e o estado de autorização do dispositivo: <br /> - O estado é `Secured` quando não há alertas <br /> - Quando há alertas sobre o dispositivo, o número de alertas é apresentado <br /> - O estado `Unauthorized` é apresentado para dispositivos adicionados à rede após o período de aprendizagem. Pode definir manualmente o dispositivo como `Authorized Device` nas definições <br /> - Caso o endereço deste dispositivo seja definido como um endereço dinâmico, `DHCP` é adicionado ao estado. |


| Rede | Description |
|--|--|
| Interfaces | As interfaces do dispositivo. Um campo RO. |
| Protocolos | Os protocolos usados pelo dispositivo. Um campo RO. |
| Firmware | Se as informações do Backplane estiverem disponíveis, as informações do firmware não serão apresentadas. |
| Endereço | O endereço IP do dispositivo. |
| Série | O número de série do dispositivo. |
| Endereço do módulo | O modelo do dispositivo e o número da ranhura ou iD. |
| Modelação | O número do modelo do dispositivo. |
| Versão do Firmware | O número da versão do firmware. |

Para ver as informações do dispositivo:

1. Selecione **Dispositivos** no menu lateral.

2. Clique com o botão direito de um dispositivo e selecione **Ver Propriedades**. É apresentada a janela Propriedades do Dispositivo.

3. Selecione o alerta necessário na parte inferior desta janela para ver informações detalhadas sobre alertas para este dispositivo.

### <a name="view-device-types"></a>Ver tipos de dispositivos

O Tipo de Dispositivo é automaticamente identificado pelo sensor durante o processo de descoberta do dispositivo. Pode alterar o tipo manualmente.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Ver tipos de dispositivos":::

A tabela a seguir apresenta todos os tipos do sistema:

| Categoria | Tipo de Dispositivo |
|--|--|
| ICS | Estação de Engenharia <br /> PLC <br />Historiador <br />HMI <br />IED <br />Controlador DCS <br />RTU <br />Sistema de Embalagem Industrial <br />Escala Industrial <br />Robô Industrial <br />Bloco <br />Medidor <br />Unidade de Frequência Variável  <br />Controlador de robôs <br />Unidade Servo <br />Dispositivo pneumático <br />Marquee |
| TI | Controlador de Domínio <br />Servidor DB <br />Estação de trabalho <br />Servidor <br />Estação Terminal <br />Armazenamento <br />Telefone inteligente <br />Tablet <br />Servidor de backup |
| IoT | Câmara IP <br />Impressora  <br />Relógio de ponche <br />ATM <br />Smart TV <br />Consola de jogos <br />DVR <br />Painel de controlo da porta <br />AVAC <br />Termóstato <br />Alarme de Incêndio <br />Luz Inteligente <br />Switch Inteligente <br />Detetor de Incêndio <br />Telefone IP <br />Sistema de Alarme <br />Sirene de alarme <br />Detetor de Movimento <br />Elevador <br />Sensor de humidade <br />Leitor de Código de Barras <br />Fonte de alimentação ininterrupta <br />Sistema de Contador de Pessoas <br />Intercom <br />Torniquete |
| Rede | Ponto de acesso sem fios <br />Router <br />Comutador <br />Firewall <br />Gateway de VPN <br />Servidor NTP <br />Abacaxi Wifi <br />Localização Física <br />Adaptador de I/O <br /> Conversor de Protocolo |

Para ver as informações do dispositivo:

1.  Selecione **Dispositivos** no menu lateral.

2. Clique com o botão direito de um dispositivo e selecione **Ver Propriedades**. É apresentada a janela Propriedades do Dispositivo.

3. Selecione no alerta necessário para visualizar informações detalhadas sobre alertas para este dispositivo.

### <a name="backplane-properties"></a>Propriedades do backplane

Se um PLC contiver vários módulos separados em racks e ranhuras, as características podem variar entre os cartões do módulo. Por exemplo, se o endereço IP e o endereço MAC forem os mesmos, o firmware pode ser diferente.

Pode utilizar a opção Backplane para rever vários controladores/cartões e os seus dispositivos aninhados como uma entidade com uma variedade de definições. Cada ranhura na vista Backplane representa os dispositivos subjacentes – os dispositivos que foram descobertos por trás do mesmo.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Propriedades backplane":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Propriedades do dispositivo de backplane":::

Um Backplane pode conter até 30 cartões controladores e até 30 unidades de cremalheira. O número total de dispositivos incluídos nos vários níveis pode chegar a 200 dispositivos.

O painel backplane é mostrado na janela Propriedades do Dispositivo quando forem detetados detalhes do Backplane.

Cada ranhura aparece com o número de dispositivos subjacentes e o ícone que mostra o tipo de módulo.

| Ícone | Tipo de Módulo |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Fornecimento de energia"::: | Fornecimento de energia |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="I/O analógico"::: | I/O analógico |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Adaptador de comunicação"::: | Adaptador de comunicação |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="Digital I/O"::: | Digital I/O |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Genérico"::: | Genérica |

Quando seleciona uma ranhura, os detalhes da ranhura aparecem:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="selecionar uma ranhura":::

Para ver os dispositivos subjacentes por trás da ranhura, selecione **VER NO MAPA**. A ranhura é apresentada no mapa do dispositivo com todos os módulos e dispositivos subjacentes ligados ao mesmo.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="VISTA NO MAPA":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Ver uma cronologia dos eventos para o dispositivo

Ver uma cronologia dos eventos associados a um dispositivo.

Para ver a linha do tempo:

1. Clique com o botão direito de um dispositivo a partir do mapa.

2. Selecione **Eventos de Exibição**. A janela Da Linha do Tempo do Evento abre com informações sobre os eventos detetados para o dispositivo selecionado.

Consulte [a Cronologia do Evento](#event-timeline) para mais detalhes.

## <a name="analyze-programming-details-and-changes"></a>Analisar detalhes de programação e alterações

Melhore os forenses exibindo eventos de programação realizados nos seus dispositivos de rede e analisando alterações de código. Estas informações ajudam-no a descobrir atividades de programação suspeitas, por exemplo:

  - Erro humano: Um engenheiro está a programar o dispositivo errado.

  - Automatização de programação corrompida: A programação é efetuada erroneamente devido à falha da automatização.

  - Sistemas pirateados: Utilizadores não autorizados iniciaram sessão num dispositivo de programação.

Pode exibir um dispositivo programado e deslocar-se através de várias alterações de programação efetuadas por outros dispositivos.

Ver código que foi adicionado, alterado, removido ou recarregado pelo dispositivo de programação. Procure alterações de programação com base em tipos de ficheiros, datas ou horários de interesse.

### <a name="when-to-review-programming-activity"></a>Quando rever a atividade de programação 

Poderá ser necessário rever a atividade de programação:

  - Depois de ver um alerta sobre programação não autorizada

  - Depois de uma atualização planeada para os controladores

  - Quando um processo ou máquina não estiver a funcionar corretamente (para ver quem realizou a última atualização e quando)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Programação do Registo de Alteração":::

Outras opções permitem::

  - Marque eventos de interesse com uma estrela.

  - Faça o download de um ficheiro *.txt com o código atual.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Sobre eventos de programação autorizados vs não autorizados 

Eventos de programação não autorizados são realizados por dispositivos que não foram aprendidos ou definidos manualmente como dispositivos de programação. Os eventos de programação autorizados são realizados por dispositivos que foram resolvidos ou definidos manualmente como dispositivos de programação.

A janela de análise de programação exibe eventos de programação autorizados e não autorizados.

### <a name="accessing-programming-details-and-changes"></a>Aceder a detalhes e alterações de programação

Aceda à janela de Análise de Programação a partir do seguinte:

- [Linha do tempo do evento](#event-timeline)

- [Alertas de Programação Não Autorizados](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Cronologia do evento

Utilize a linha do tempo do evento para apresentar uma cronologia de eventos em que foram detetadas alterações de programação.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Uma visão da cronologia do evento.":::

### <a name="unauthorized-programming-alerts"></a>Alertas de programação não autorizados

Os alertas são acionados quando dispositivos de programação não autorizados realizam atividades de programação.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Alertas de programação não autorizados":::

> [!NOTE]
> Também pode ver informações básicas de programação na janela device Properties e no Inventário do Dispositivo.

### <a name="working-in-the-programming-timeline-window"></a>Trabalhar na janela da linha do tempo de programação

Esta secção descreve como visualizar ficheiros de programação e comparar versões. Procure por ficheiros específicos enviados para um dispositivo programado. Procurar ficheiros com base em:

  - Data

  - Tipo de arquivo

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="janela de linha do tempo de programação":::

|Tipo de linha do tempo de programação | Description |
|--|--|
| Dispositivo programado | Fornece detalhes sobre o dispositivo que foi programado, incluindo o nome de anfitrião e arquivo. |
| Eventos Recentes | Apresenta os 50 eventos mais recentes detetados pelo sensor. <br />Para destacar um evento, paire sobre ele e clique na estrela. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Os últimos 50 eventos podem ser vistos. |
| Ficheiros | Apresenta os ficheiros detetados para a data escolhida e o tamanho do ficheiro no dispositivo programado. <br /> Por predefinição, o número máximo de ficheiros disponíveis para exibição por dispositivo é de 300. <br /> Por predefinição, o tamanho máximo do ficheiro para cada ficheiro é de 15 MB. |
| Estado do ficheiro :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | As etiquetas dos ficheiros indicam o estado do ficheiro no dispositivo, incluindo: <br /> **Adicionado:** o ficheiro foi adicionado ao ponto final na data ou hora selecionadas. <br /> **Atualizado**: O ficheiro foi atualizado na data ou hora selecionadas. <br /> **Apagado:** Este ficheiro foi removido. <br /> **Sem etiqueta:** O ficheiro não foi alterado.   |
| Dispositivo de Programação | O dispositivo que fez a programação mudar. Vários dispositivos podem ter efetuado alterações de programação num dispositivo programado. O nome de anfitrião, data ou hora de alteração e registado no utilizador são apresentados. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Exibe o ficheiro atual instalado no dispositivo programado. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Faça o download de um ficheiro de texto do código exibido. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Compare o ficheiro atual com o ficheiro detetado numa data selecionada. |

### <a name="choose-a-file-to-review"></a>Escolha um ficheiro para rever

Esta secção descreve como escolher um ficheiro para rever.

Para escolher um ficheiro para rever:

1. Selecione um evento do painel **de eventos recentes**

2. Selecione um formulário de ficheiro do painel de ficheiros. O ficheiro aparece no painel De corrente.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Selecione o ficheiro para trabalhar.":::

### <a name="compare-files"></a>Comparar ficheiros

Esta secção descreve como comparar ficheiros de programação.

Para comparar:

1. Selecione um evento do painel eventos recentes.

2. Selecione um ficheiro no painel de ficheiros. O ficheiro aparece no painel De corrente. Pode comparar este ficheiro com outros ficheiros.

3. Selecione o indicador de comparação.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Comparar indicador":::

   A janela apresenta todas as datas em que o ficheiro selecionado foi detetado no dispositivo programado. O ficheiro pode ter sido atualizado no dispositivo programado por vários dispositivos de programação.

   O número de diferenças detetadas aparece no canto superior direito da janela. Pode ser necessário deslocar-se para baixo para ver as diferenças.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="rolar para baixo para a sua seleção":::

   O número é calculado por linhas adjacentes de texto alterado. Por exemplo, se oito linhas de código consecutivas forem alteradas (eliminadas, atualizadas ou adicionadas) esta será calculada como uma diferença.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="A sua visão da linha do tempo de programação.":::

4. Selecione uma data. O ficheiro detetado na data selecionada aparece na janela.

5. O ficheiro selecionado a partir do painel De Eventos/Ficheiros Recentes aparece sempre à direita.

### <a name="device-programming-information-other-locations"></a>Informações de programação do dispositivo: Outros locais

Além de rever detalhes na Linha do Tempo de Programação, pode aceder a informações de programação na janela Propriedades do Dispositivo e no Inventário do Dispositivo.

| Tipo de Dispositivo | Description |
|--|--|
| Propriedades do dispositivo | A janela de propriedades do dispositivo fornece informações sobre o último evento de programação detetado no dispositivo\. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="As propriedades do seu dispositivo"::: |
| O inventário do dispositivo | O inventário do dispositivo indica se o dispositivo é um dispositivo de programação\. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="O inventário dos dispositivos"::: |

## <a name="manage-device-information-from-the-map"></a>Gerir informações do dispositivo a partir do mapa

O sensor não atualiza nem impacta os dispositivos diretamente na rede. As alterações feitas aqui apenas impactam na forma como analisa o dispositivo.

### <a name="delete-devices"></a>Eliminar dispositivos

É possível que queira eliminar um dispositivo se as informações aprendidas não forem relevantes. Por exemplo,

  - Um empreiteiro parceiro numa estação de trabalho de engenharia conecta-se temporariamente para realizar atualizações de configuração. Depois de concluída a tarefa, o dispositivo é removido.

  - Devido a alterações na rede, alguns dispositivos já não estão ligados.

Se não eliminar o dispositivo, o sensor continuará a monitorizá-lo. Após 60 dias, aparecerá uma notificação, recomendando que apague.

Pode receber um alerta indicando que o dispositivo não responde se outro dispositivo tentar aceder ao mesmo. Neste caso, a sua rede pode estar mal configurada.

O dispositivo será removido do mapa do dispositivo, do inventário do dispositivo e dos relatórios de mineração de dados. Outras informações, por exemplo: as informações armazenadas nos Widgets serão mantidas.

O aparelho deve estar inativo durante pelo menos 10 minutos para o eliminar.

Para eliminar um dispositivo do mapa do dispositivo:

1. Selecione **Dispositivos** no menu lateral.

2. Clique com o botão direito de um dispositivo e **selecione Delete**.

### <a name="merge-devices"></a>Dispositivos de fusão

Em certas circunstâncias, poderá ter de fundir dispositivos. Isto pode ser necessário se o sensor descobrir entidades de rede separadas que estejam associadas a um dispositivo único. Por exemplo,

  - Um PLC com quatro cartões de rede.

  - Um Laptop com WIFI e cartão físico.
  
  - Uma estação de trabalho com dois ou mais cartões de rede.

Ao fundir-se, instrua o sensor para combinar as propriedades do dispositivo de dois dispositivos num só. Quando o fizer, os relatórios da janela e do sensor do Device Properties serão atualizados com os novos detalhes da propriedade do dispositivo.

Por exemplo, se fundir dois dispositivos, cada um com um endereço IP, ambos os endereços IP aparecerão como interfaces separadas na janela Propriedades do Dispositivo. Só pode fundir dispositivos autorizados.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Janela propriedades do dispositivo":::

A cronologia do evento apresenta o evento de fusão.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="A cronologia do evento com eventos fundidos.":::

Não é possível desfazer um dispositivo de fusão. Se por engano fundiu dois dispositivos, elimine o dispositivo e aguarde que o sensor redescobrir ambos.

Para fundir dispositivos:

1. Selecione dois dispositivos (clique de turno) e, em seguida, clique com o botão direito num deles.

2. **Selecione Merge** para fundir os dispositivos. Pode levar até 2 minutos para completar a fusão.

3. Na caixa de diálogo de atributos de fusão definida, escolha um nome do dispositivo.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="caixa de diálogo de atributos":::

4. Selecione **Guardar**.

### <a name="authorize-and-unauthorize-devices"></a>Autorizar e não autorizar dispositivos

Durante o período de Aprendizagem, todos os dispositivos descobertos na rede são identificados como dispositivos autorizados. A etiqueta **autorizada** não aparece nestes dispositivos no Mapa do Dispositivo.

Quando um dispositivo é descoberto após o período de Aprendizagem, aparece como um dispositivo não autorizado. Além de ver dispositivos não autorizados no mapa, também pode vê-los no Inventário de Dispositivos.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Inventário de Dispositivos":::

**Novo dispositivo vs não autorizado**

Aparecerão novos dispositivos detetados após o período de Aprendizagem com um `New` `Unauthorized` rótulo.

Se mover um dispositivo no mapa ou alterar manualmente as propriedades do dispositivo, a `New` etiqueta é removida do ícone do dispositivo.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Dispositivos não autorizados - vetores de ataque e relatórios de avaliação de riscos

Dispositivos não autorizados estão incluídos em relatórios de avaliação de risco e relatórios de vetores de ataque.

- **Relatórios de vetores de ataque:** Dispositivos marcados como não autorizados são resolvidos no Vetor de Ataque como dispositivos fraudulentos suspeitos que podem ser uma ameaça para a rede.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Veleça os seus relatórios de vetores de ataque":::

- **Relatórios de Avaliação de Riscos:** Os dispositivos marcados como não autorizados são:

  - Identificados em Relatórios de Avaliação de Riscos

Para autorizar ou não dispositivos manualmente:

1. Clique com o botão direito no dispositivo no mapa e **selecione Unauthorize**

### <a name="mark-devices-as-important"></a>Marcar dispositivos tão importantes

Pode marcar dispositivos de rede significativos como importantes, por exemplo, servidores críticos de negócio. Estes dispositivos estão marcados com uma estrela no mapa. A estrela varia de acordo com o nível de zoom do mapa.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Dispositivos importantes - vetores de ataque e relatórios de avaliação de riscos

Os dispositivos importantes são calculados ao gerar relatórios de avaliação de risco e relatórios de vetores de ataque.

  - Os relatórios do vetor de ataque que os dispositivos marcados como importantes são resolvidos no vetor de ataque como alvos de ataque. 

  - Relatórios de Avaliação de Riscos: Os dispositivos marcados como importantes são calculados ao fornecer a pontuação de segurança no relatório de avaliação de risco.

## <a name="generate-activity-reports-from-the-map"></a>Gerar relatórios de atividade a partir do mapa

Gere um relatório de atividade para um dispositivo selecionado durante as 1, 6, 12 ou 24 horas. Estão disponíveis as seguintes informações:

  - Categoria: Informação básica de deteção baseada em cenários de tráfego.

  - Dispositivos de origem e destino

  - Dados: Informações adicionais desertas.

  - A hora e a data vistas pela última vez.

Pode guardar o relatório como um ficheiro Microsoft Excel ou Word.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Atividade recente":::

Para gerar um relatório de atividade para um dispositivo:

1. Clique com o botão direito de um dispositivo a partir do Mapa.

2. Selecione um Relatório de Atividade.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Veja um relatório da sua atividade.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Gerar relatórios de vetores de ataque a partir do mapa

Simular um relatório de vetor de ataque para saber se um dispositivo no mapa que seleciona é um alvo de ataque vulnerável.

Os relatórios do Vetor de ataque fornecem uma representação gráfica de uma cadeia de vulnerabilidade de dispositivos exploráveis. Estas vulnerabilidades podem dar a um intruso acesso a dispositivos de rede chave. O simulador do Vetor de Ataque calcula vetores de ataque em tempo real e analisa todos os vetores de ataque por um alvo específico.

Para ver um dispositivo num vetor de ataque reporta:

1. Clique com o botão direito de um dispositivo a partir do mapa.

2. **Selecione Simular vetores de ataque**. A caixa de diálogo do vetor de ataque abre com o dispositivo que seleciona como alvo de ataque.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Adicionar simulação de vetor de ataque":::

3. Adicione os parâmetros restantes à caixa de diálogo e selecione **Add Simulation**.

## <a name="export-device-information-from-the-map"></a>Informação do dispositivo de exportação do mapa

Exporte as seguintes informações do dispositivo a partir do Mapa.

  - Detalhes do dispositivo (Microsoft Excel)

  - Um resumo do dispositivo (Microsoft Excel)

  - Um ficheiro de palavras com grupos (Microsoft Word)

Para exportar:

1. Selecione o ícone exportação do Mapa.

1. Selecione uma opção de exportação.

## <a name="see-also"></a>Ver também

[Investigar deteções de sensores num Inventário de Dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
