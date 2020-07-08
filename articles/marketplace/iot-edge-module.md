---
title: O módulo Azure Marketplace IoT Edge oferece
description: Saiba mais sobre a publicação de ofertas de módulos IoT Edge no Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 9783eb1dbcbfc3ee01008dfa33ff2429a87cf62a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963381"
---
# <a name="iot-edge-modules"></a>Módulos do IoT Edge

A plataforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) é apoiada pela Microsoft Azure.  Esta plataforma permite que os utilizadores implementem cargas de trabalho em nuvem para executar diretamente em dispositivos IoT.  Um módulo IoT Edge pode executar cargas de trabalho offline e fazer análise de dados localmente. Este tipo de oferta ajuda a poupar largura de banda, salvaguardar dados locais e sensíveis, e oferece tempo de resposta de baixa latência.  Agora tem as opções para aproveitar estas cargas de trabalho pré-construídas. Até agora, apenas um punhado de soluções de primeira parte da Microsoft estavam disponíveis.  Teve de investir o tempo e os recursos na construção das suas próprias soluções ioT personalizadas.

Com [módulos IoT Edge no Azure Marketplace, temos](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)agora um único destino para as editoras exporem e venderem as suas soluções ao público IoT. Os desenvolvedores de IoT podem, em última análise, encontrar e adquirir capacidades para acelerar o desenvolvimento da sua solução.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Principais benefícios dos módulos IoT Edge no Azure Marketplace

| **Para editores**    | **Para clientes (desenvolvedores de IoT)**  |
| :------------------- | :-------------------|
| Alcance milhões de desenvolvedores que procuram construir e implementar soluções IoT Edge.  | Componha uma solução IoT Edge com a confiança de utilizar componentes seguros e testados. |
| Publique uma vez e corra através de qualquer hardware IoT Edge que suporte recipientes. | Reduza o tempo ao mercado encontrando e implantando módulos IoT Edge de 1ª e 3ª parte para necessidades específicas. |
| Rentabilizar com opções de faturação flexíveis <ul> <li> Livre e traga a sua própria licença (BYOL). </li> </ul> | Faça compras com a sua escolha de modelos de faturação. <ul> <li> Livre e traga a sua própria licença (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>O que é um módulo IoT Edge?

O Azure IoT Edge permite-lhe implementar e gerir a lógica de negócios no limite sob a forma de módulos. Os módulos Azure IoT Edge são as unidades de computação mais pequenas geridas pelo IoT Edge, e podem conter serviços microsoft (como a Azure Stream Analytics), serviços de terceiros ou o seu próprio código específico para solução. Para saber mais sobre os módulos IoT Edge, consulte [os módulos Understand Azure IoT Edge](../iot-edge/iot-edge-modules.md).

**Qual é a diferença entre um tipo de oferta de recipiente e um tipo de oferta de módulo IoT Edge?**

O tipo de oferta de módulo IoT Edge é um tipo específico de recipiente que funciona num dispositivo IoT Edge. Ele vem com configurações de configuração padrão para executar no contexto IoT Edge, e opcionalmente usa o módulo IoT Edge SDK para ser integrado com o tempo de execução IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publicando o seu módulo IoT Edge

**Selecionando a montra certa**

Os Módulos IoT Edge são publicados apenas no Mercado Azure, o AppSource não se aplica.  Para obter mais informações sobre as diferenças e o público-alvo em todas as montras, consulte [a determinação da opção de publicação para a sua solução.](determine-your-listing-type.md)
 
**Opções de faturação**

O mercado suporta atualmente opções de faturação **free** and **Bring Your Own (BYOL)** para módulos IoT Edge.
 
**Opções de publicação**

Em todos os casos, os módulos IoT Edge devem selecionar a opção de publicação **Transact.**  Consulte [uma opção de publicação](determine-your-listing-type.md) para obter mais detalhes sobre as opções de publicação.  

## <a name="eligibility-criteria"></a>Critérios de elegibilidade

Todos os termos dos acordos e políticas do Microsoft Azure Marketplace aplicam-se às ofertas do módulo IoT Edge.  Além disso, existem requisitos prévios e requisitos técnicos para os módulos IoT Edge.  

**Pré-requisitos**

Para publicar um módulo IoT Edge no Azure Marketplace, tem de cumprir os seguintes requisitos:

- Acesso ao Centro de Parceiros. Para mais informações, consulte [o guia de publicação Azure Marketplace e AppSource.](marketplace-publishers-guide.md)
- Hospedar o seu módulo IoT Edge num Registo de Contentores Azure. 
- Tenha os metadados do módulo IoT Edge prontos, tais como (lista não exaustiva): 
    - Um título
    - Uma descrição (em formato HTML)
    - Uma imagem de logotipo (formato PNG e tamanhos de imagem fixa, incluindo 40x40px, 90x90px, 115x115px, 255x115px)
    - Um termo de uso e política de privacidade
    - Configuração do módulo predefinido (rota, propriedades duplas desejadas, criações Opções, variáveis ambientais)
    - Documentation
    - Contactos de suporte

**Requisitos Técnicos**

Os requisitos técnicos primários para um Módulo IoT Edge, para que seja certificado e publicado no Mercado Azure, são detalhados no Prepare os seus ativos técnicos do [módulo IoT Edge](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="documentation-and-resources"></a>Documentação e Recursos

[Crie uma oferta de módulo IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) -- Os passos para a publicação de uma nova oferta de módulo IoT Edge no Partner Center.

## <a name="next-steps"></a>Próximos passos

Se ainda não o fez,

- [Conheça](https://azuremarketplace.microsoft.com/sell) o mercado.

Para se registar no Partner Center, e começar a criar uma nova oferta ou trabalhar numa existente,

- Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte [criar uma oferta de módulo IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) para obter informações sobre como publicar uma oferta de módulo IoT Edge.
