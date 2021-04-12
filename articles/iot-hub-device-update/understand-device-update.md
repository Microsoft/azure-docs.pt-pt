---
title: Introdução à atualização do dispositivo para Azure IoT Hub | Microsoft Docs
description: A Atualização do Dispositivo para IoT Hub é um serviço que lhe permite implementar atualizações over-the-air (OTA) para os seus dispositivos IoT.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 95cecd66f4d0164594eb4e8da9efe765b5833e5e
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120780"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Atualização do dispositivo para IoT Hub (Visualização) Visão geral

A Atualização do Dispositivo para IoT Hub é um serviço que lhe permite implementar atualizações over-the-air (OTA) para os seus dispositivos IoT.

À medida que as organizações procuram permitir ainda mais a produtividade e eficiência operacional, as soluções internet of Things (IoT) continuam a ser adotadas a taxas crescentes. Isto torna essencial que os dispositivos que formam estas soluções sejam construídos com base numa base de fiabilidade e segurança e sejam fáceis de ligar e gerir em escala. Device Update for IoT Hub é uma plataforma de ponta a ponta que os clientes podem usar para publicar, distribuir e gerir atualizações over-the-air para tudo, desde pequenos sensores a dispositivos de nível gateway. 

Para perceber todos os benefícios da transformação digital ativada pelo IoT, os clientes precisam desta capacidade de operar, manter e atualizar dispositivos em escala. Explore os benefícios de implementar a Atualização de Dispositivos para o IoT Hub, que incluem ser capaz de responder rapidamente a ameaças de segurança e implementar novas funcionalidades para obter objetivos de negócio sem incorrer nos custos de desenvolvimento e manutenção extra da construção das suas próprias plataformas de atualização.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Suporte para uma vasta gama de dispositivos IoT


A Atualização de Dispositivos para IoT Hub foi concebida para oferecer uma implementação otimizada de atualização e operações simplificadas através da integração com [o Azure IoT Hub](https://azure.microsoft.com/en-us/services/iot-hub/). Esta integração facilita a adoção da Atualização do Dispositivo em qualquer solução existente. Fornece uma solução em nuvem para ligar praticamente qualquer dispositivo. A Atualização do Dispositivo suporta uma vasta gama de sistemas operativos IoT - incluindo Linux e [Azure RTOS](https://azure.microsoft.com/en-us/services/rtos/) (sistema operativo em tempo real) e é extensível através de fonte aberta. Estamos a codificar a atualização do dispositivo para ofertas de IoT Hub com os nossos parceiros semicondutores, incluindo a STMicroelectronics, NXP, Renesas e Microchip. Consulte as [amostras](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) dos principais conselhos de avaliação de semicondutores que incluem os guias de arranque para aprender a configurar, construir e implementar as atualizações over-the-air (OTA) para dispositivos da classe MCU. 

São fornecidas imagens yocto de referência do Agente de Atualização de Dispositivos e raspberry Pi.
A Atualização do Dispositivo para O Hub IoT também suporta a atualização de dispositivos Azure IoT Edge. Um agente de atualização de dispositivos é fornecido para a plataforma Ubuntu Server 18.04 amd64. A Atualização de Dispositivos para IoT Hub também fornece código de código aberto se não estiver a executar uma das plataformas acima. Pode levar o agente para a distribuição que está a executar.

A Atualização do Dispositivo funciona com ioT Plug and Play (PnP) e pode gerir qualquer dispositivo que suporte as interfaces PnP necessárias. Para obter mais informações, consulte [a atualização do dispositivo para IoT Hub e IoT Plug and Play](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Suporte para uma vasta gama de artefactos de atualização

A Atualização do Dispositivo para o IoT Hub suporta duas formas de atualizações – baseadas em imagem e baseadas em pacotes.

As atualizações baseadas em pacotes são atualizações direcionadas que alteram apenas um componente ou aplicação específico no dispositivo. Isto leva a um menor consumo de largura de banda e ajuda a reduzir o tempo para descarregar e instalar a atualização. As atualizações de pacotes normalmente permitem menos tempo de inatividade dos dispositivos ao aplicar uma atualização e evitar a sobrecarga de criação de imagens.

As atualizações de imagem proporcionam um maior nível de confiança no estado final do dispositivo. É tipicamente mais fácil replicar os resultados de uma atualização de imagem entre um ambiente de pré-produção e um ambiente de produção, uma vez que não coloca os mesmos desafios que os pacotes e as suas dependências.
Devido à sua natureza atómica, também se pode adotar facilmente um modelo de failover A/B.

Não há uma resposta certa, e pode escolher de forma diferente com base nos seus casos de uso específicos. A Atualização do Dispositivo para ioT Hub suporta a forma de atualização de imagem e pacote, permitindo-lhe escolher o modelo de atualização adequado para o ambiente do seu dispositivo.

## <a name="flexible-features-for-updating-devices"></a>Funcionalidades flexíveis para atualização de dispositivos

A atualização do dispositivo para as funcionalidades do IoT Hub proporciona uma experiência poderosa e flexível, incluindo:

* Atualização de gestão UX integrada com Azure IoT Hub
* Rollout gradual da atualização através de controlos de programação e de programação de atualização de dispositivos
* APIs programáticos para permitir automatização e experiências personalizadas do portal
* At-a-relance atualizar conformidade e pontos de vista de estado em todas as frotas de dispositivos heterogéneos
* Suporte para atualizações resilientes de dispositivos (A/B) para fornecer reversão sem emenda
* Controlos de acesso baseados em funções e de função disponíveis através do portal Azure.com
* Cache de conteúdo no local e suporte De Edge Aninhado para permitir a atualização de dispositivos desligados em nuvem
* Ferramentas detalhadas de gestão e reporte de atualização 

Com a Atualização do Dispositivo para controlos de gestão e implementação do IoT Hub, os utilizadores podem maximizar a produtividade e economizar tempo valioso. A Atualização do Dispositivo para o IoT Hub inclui a capacidade de agrupar dispositivos e especificar para que dispositivos deve ser implantada uma atualização. Os utilizadores também podem ver o estado das implementações da atualização e certificar-se de que cada dispositivo aplica com sucesso atualizações.

Quando uma falha de atualização acontece, a Atualização do Dispositivo para o IoT Hub também permite que os utilizadores identifiquem os dispositivos que não aplicaram a atualização e ver detalhes de falha relacionados. A capacidade de identificar quais os dispositivos que não conseguiram atualizar significa inúmeras horas manuais guardadas ao tentar localizar a fonte.

### <a name="best-in-class-security-at-global-scale"></a>Segurança de primeira classe à escala global

O Microsoft Azure suporta mais de mil milhões de dispositivos IoT em todo o mundo - um número que está a crescer rapidamente a cada dia. A Atualização de Dispositivos para IoT Hub baseia-se nesta experiência e na fiabilidade comprovada demonstrada pela plataforma Windows Update, para que os dispositivos possam ser atualizados de forma perfeita a nível global.

O Device Update for IoT Hub utiliza uma segurança abrangente em nuvem-a-ponta que é desenvolvida para o Microsoft Azure, pelo que os clientes não precisam de gastar tempo a descobrir como construí-lo em si mesmos a partir do zero.


## <a name="device-update-workflows"></a>Fluxos de trabalho de atualização de dispositivos

A funcionalidade de Atualização de Dispositivos pode ser dividida em três áreas: Integração de Agentes, Importação e Gestão.

### <a name="device-update-agent"></a>Agente de atualização de dispositivos

Quando um comando de atualização é recebido num dispositivo, executará a fase solicitada de atualização (ou descarregue, instale e aplique). Durante cada fase, o estado é devolvido à Atualização do Dispositivo via IoT Hub para que possa visualizar o estado atual de uma implementação. Se não houver atualizações em curso, o estado é devolvido como "Idle". Uma implantação pode ser cancelada a qualquer momento.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Diagrama do fluxo de trabalho do agente de atualização do dispositivo." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Saiba mais](device-update-agent-overview.md) sobre o agente de atualização do dispositivo. 

### <a name="importing"></a>Importação

Importar é como as suas atualizações são ingeridas na Atualização do Dispositivo para que possam ser implementadas em dispositivos. A Atualização do Dispositivo suporta a localização de uma única atualização por dispositivo. Isto torna-o ideal para atualizações de imagem completa que atualizam uma partição de SO inteira de uma só vez, ou um Manifesto apto que descreve todos os pacotes que pretende atualizar no seu dispositivo. Para importar atualizações para a Atualização do Dispositivo, primeiro cria um manifesto de importação que descreve a atualização, depois carreca os ficheiros de atualização e o manifesto de importação para um local acessível à Internet. Depois disso, pode utilizar o portal Azure ou a [API de Atualização](https://docs.microsoft.com/rest/api/deviceupdate/) do Dispositivo para iniciar o processo assíncrono de importação de atualização. A Atualização do Dispositivo faz o upload dos ficheiros, processa-os e disponibiliza-os para distribuição em dispositivos IoT.

Para conteúdos sensíveis, proteja o download utilizando uma assinatura de acesso partilhado (SAS), como um SAS ad-hoc para armazenamento Azure Blob. [Saiba mais sobre o SAS](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Diagrama de Atualização de Dispositivos para fluxo de trabalho de importação de IoT Hub." lightbox="media/understand-device-update/import-update.png":::

[Saiba mais](import-concepts.md) sobre a importação de atualizações. 

### <a name="grouping-and-deployment"></a>Agrupamento e implantação

Depois de importar uma atualização, pode ver atualizações compatíveis para os seus dispositivos e classes de dispositivos.

A Atualização de Dispositivos suporta o conceito de **Grupos** através de tags no IoT Hub. Implementar uma atualização para um grupo de teste em primeiro lugar é uma boa forma de reduzir o risco de problemas durante o lançamento da produção.

Na Atualização do Dispositivo, as implementações são uma forma de ligar o conteúdo certo a um conjunto específico de dispositivos compatíveis. A Atualização do Dispositivo orquestra o processo de envio de comandos para cada dispositivo, instruindo-os a descarregar e instalar as atualizações e a recuperar o estado.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Diagrama de Atualização de Dispositivos para agrupamento e fluxo de trabalho de implantação do IoT Hub." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[Saiba mais](device-update-compliance.md) sobre conceitos de implementação

[Saiba mais](device-update-groups.md) sobre os grupos de atualização de dispositivos


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar conta e instância de atualização de dispositivos](create-device-update-account.md)