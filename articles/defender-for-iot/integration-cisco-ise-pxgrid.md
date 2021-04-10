---
title: Sobre a integração do Cisco ISE pxGrid
description: Fazendo a ponte entre as capacidades do Defender para ioT com o Cisco ISE pxGrid, proporciona às equipas de segurança uma visibilidade sem precedentes do dispositivo para o ecossistema empresarial.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784089"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Sobre a integração do Cisco ISE pxGrid

O Defender for IoT fornece a única plataforma de cibersegurança ICS e IoT construída por especialistas em equipas azuis com um histórico que defende infraestruturas nacionais críticas, e a única plataforma com análise de ameaças patenteadas do ICS e machine learning. O Defender for IoT fornece:

- Informações imediatas sobre dispositivos ICS, vulnerabilidades e ameaças conhecidas e de dia zero.

- Conhecimento profundo e profundo do ICS sobre protocolos de OT, dispositivos, aplicações e seus comportamentos.

- Uma tecnologia automatizada de modelação de ameaças do ICS para prever os caminhos mais prováveis de ataques ics direcionados através de análises proprietárias.

## <a name="powerful-device-visibility-and-control"></a>Poderosa visibilidade e controlo do dispositivo

O Defender para a integração de IoT com o cisco ISE pxGrid proporciona um novo nível de visibilidade centralizada, monitorização e controlo para a paisagem OT.

Estas plataformas em ponte permitem a visibilidade e proteção automatizadas do dispositivo para dispositivos ICS e IIoT anteriormente inacessíveis.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>Ics e IIoT visibilidade do dispositivo: abrangente e profundo

O Defender patenteado para tecnologias IoT garante a descoberta abrangente e profunda de dispositivos ICS e IIoT e a gestão de inventário para dados empresariais.

Os tipos de dispositivos, fabricantes, portas abertas, números de série, revisões de firmware, endereços IP e dados de endereços MAC e muito mais são atualizados em tempo real. O Defender for IoT pode aumentar ainda mais a visibilidade, descoberta e análise a partir desta linha de base, integrando-se com fontes críticas de dados da empresa. Por exemplo, CMDBs, DNS, Firewalls e Web APIs.

Além disso, a plataforma Defender for IoT combina técnicas de monitorização passiva e de sondagem seletiva opcional para fornecer o inventário mais preciso e detalhado dos dispositivos em organizações de infraestruturas industriais e críticas.

### <a name="bridged-capabilities"></a>Capacidades em ponte

Fazendo a ponte com o Cisco ISE pxGrid, proporciona às equipas de segurança uma visibilidade sem precedentes do dispositivo para o ecossistema empresarial.

Uma integração robusta e sem emenda com o cisco ISE pxGrid garante que nenhum dispositivo OT não é descoberto e não é falta nenhuma informação do dispositivo.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Amostra das categorias de ponto final OUI.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Pontos finais da amostra no sistema":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Screenshot dos atributos localizados no sistema.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Utilizar cobertura de caso: Políticas ise baseadas no Defender para atributos IoT

Utilize políticas ise poderosas baseadas no Defender para aprendizagem profunda IoT para lidar com os requisitos de caso de utilização de ICS e IoT.

Trabalhar com políticas permite-lhe fechar o ciclo de segurança e levar a sua rede ao cumprimento em tempo real.

Por exemplo, os clientes podem usar atributos Defender para IoT ICS e IoT para criar políticas que lidem com os seguintes casos de utilização, tais como:

- Crie uma política de autorização para permitir que dispositivos conhecidos e autorizados entrem numa zona sensível com base em atributos permitidos - por exemplo, versão específica de firmware para PLCs da Rockwell Automation.

- Notifique as equipas de segurança quando um dispositivo ICS for detetado numa zona não OT.

- Remediar máquinas com fornecedores desatualizados ou não conformes.

- Dispositivos de quarentena e bloco, conforme necessário.

- Gere relatórios sobre PLCs ou RTUs executando firmware com vulnerabilidades conhecidas (CVEs).

### <a name="about-this-article"></a>Sobre este artigo

Este artigo descreve como configurar a plataforma pxGrid e Defender para ioT para garantir que o Defender for IoT injeta atributos OT à Cisco ISE.

### <a name="getting-more-information"></a>Obter mais informações

Para obter mais informações sobre os requisitos de integração da Cisco ISE pxGrid, consulte <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Requisitos do sistema de integração

Este artigo descreve os requisitos do sistema de integração:

Defender para requisitos de IoT

- Defender para ioT versão 2.5

Requisitos da Cisco

- pxGrid versão 2.0

- Cisco ISE versão 2.4

Requisitos de rede

- Verifique se o defender do aparelho IoT tem acesso à interface de gestão Cisco ISE.

- Verifique se tem acesso CLI a todos os aparelhos Defender para IoT na sua empresa.

> [!NOTE]
> Apenas os dispositivos com endereços MAC estão sincronizados com o Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Configuração do cisco ISE pxGrid

Este artigo descreve como:

- Configurar comunicação com o pxGrid

- Subscreva o tópico do dispositivo de ponto final

- Gerar certificados

- Definir definições de pxGrid

## <a name="set-up-communication-with-pxgrid"></a>Configurar comunicação com o pxGrid

Este artigo descreve como configurar a comunicação com o pxGrid.

Para configurar a comunicação:

1. Inscreva-se na Cisco ISE.

1. Selecione  > **Sistema** de **Administração** e Implementação .

1. Selecione o nó necessário. No separador Definições Gerais, selecione a **caixa de verificação pxGrid**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Certifique-se de que a caixa de verificação pxgrid está selecionada.":::

1. Selecione o **separador Configuração de Perfil.**

1. Selecione a **caixa de verificação pxGrid**. adicione uma descrição.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Screenshot da descrição do add":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Subscreva o tópico do dispositivo de ponto final

Verifique se o nó pxGrid ise subscreveu o tópico do dispositivo de ponto final. Navegue para a **Administração** > **pxGrid Services** > **Web Clients**. Lá, pode verificar se o ISE subscreveu o tópico do dispositivo de ponto final.

## <a name="generate-certificates"></a>Gerar certificados

Este artigo descreve como gerar certificados.

Para gerar:

1. Selecione   >  **Serviços pxGrid administração** e, em seguida, selecione **Certificados**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Selecione o separador certificados para gerar um certificado.":::

1. No campo **I Want To,** selecione **Gere um único certificado (sem um pedido de assinatura de certificado)**.

1. Preencha os campos restantes e selecione **Criar**.

1. Crie a chave de certificado do cliente e o certificado do servidor e converta-os em formato java keystore. Terá de copiar isto para cada Sensor IoT da sua rede.

## <a name="define-pxgrid-settings"></a>Definir definições de pxGrid

Para definir definições:

1. Selecione   >  **Serviços pxGrid administração** e, em seguida, selecione **Definições**.

1. Ativar automaticamente **novas contas baseadas em certificados** e **permitir a criação de conta baseada em passwords.**

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Certifique-se de que ambas as caixas de verificação estão selecionadas.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Configurar o Defender para aparelhos IoT

Este artigo descreve como configurar o Defender para aparelhos IoT comunicarem com o pxGrid. A configuração deve ser realizada em todos os aparelhos Defender para aparelhos IoT que injetarão dados à Cisco ISE.

Para configurar os aparelhos:

1. Inscreva-se no sensor CLI.

1. Copiar `trust.jks` , e , que foram previamente criados no sensor. Copie-os para: `/var/cyberx/properties/` .

1. `/var/cyberx/properties/pxgrid.properties`Editar:

    1. Adicione uma chave e confiança, em seguida, guarde os dados de ficheiros e senhas.

    2. Adicione o nome de anfitrião do pxGrid.

    3. `Enabled=true`

1. Reinicie o aparelho.

1. Repita estes passos para cada aparelho da sua empresa que injeta dados.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Ver e gerir deteções na Cisco ISE

1. As deteções de pontos finais são apresentadas no separador Ise Context **Visibility**  >  **Endpoints.**

1. Selecione **Perfis de Política**  >    >  **Adicionar**  >  **Regras**+  >  **Condição** Criar  >  **nova condição**.

1. **Selecione Atribue** e use os dicionários de dispositivos IOT para construir uma regra de perfis com base nos atributos injetados. Os seguintes atributos são injetados.

    - Tipo de Dispositivo

    - Revisão de hardware

    - Endereço IP

    - Endereço MAC

    - Name

    - ID do Produto

    - Protocolo

    - Número de série

    - Revisão de software

    - Fornecedor

Apenas os dispositivos com endereços MAC são sincronizados.

## <a name="troubleshooting-and-logs"></a>Resolução de problemas e registos

Os registos podem ser encontrados em:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Passos seguintes

Saiba como [encaminhar informações de alerta](how-to-forward-alert-information-to-partners.md).
