---
title: Ofertas do módulo Azure Marketplace IoT Edge
description: Saiba mais sobre a publicação de ofertas de módulos IoT Edge no Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 49f86a79eb5358d27c15d93004db396436c3e680
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657951"
---
# <a name="iot-edge-modules"></a>Módulos do IoT Edge

A plataforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) é apoiada pelo Microsoft Azure.  Esta plataforma permite que os utilizadores implementem cargas de trabalho em nuvem para funcionardiretamente em dispositivos IoT.  Um módulo IoT Edge pode executar cargas de trabalho offline e fazer análise de dados localmente. Este tipo de oferta ajuda a salvar a largura de banda, a salvaguardar dados locais e sensíveis e oferece tempo de resposta de baixa latência.  Agora tem as opções para aproveitar estas cargas de trabalho pré-construídas. Até agora, apenas um punhado de soluções de primeira parte da Microsoft estavam disponíveis.  Teve de investir o tempo e os recursos na construção das suas próprias soluções personalizadas de IoT.

Com [os módulos IoT Edge no Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)temos agora um único destino para as editoras exporem e venderem as suas soluções ao público IoT. Os desenvolvedores de IoT podem, em última análise, encontrar e adquirir capacidades para acelerar o desenvolvimento da sua solução.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Principais benefícios dos módulos IoT Edge no Azure Marketplace

| **Para editores**    | **Para clientes (desenvolvedores ioT)**  |
| :------------------- | :-------------------|
| Chegar a milhões de desenvolvedores que procuram construir e implementar soluções IoT Edge.  | Componha uma solução IoT Edge com a confiança de utilizar componentes seguros e testados. |
| Publique uma vez e encontre qualquer hardware IoT Edge que suporte os contentores. | Reduza o tempo para o mercado encontrando e implantando módulos IoT Edge de 1º e 3º partido para necessidades específicas. |
| Monetize com opções de faturação flexíveis <ul> <li> Grátis e traga a sua própria licença (BYOL). </li> </ul> | Faça compras com a sua escolha de modelos de faturação. <ul> <li> Grátis e traga a sua própria licença (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>O que é um módulo IoT Edge?

O Azure IoT Edge permite-lhe implementar e gerir a lógica do negócio no limite sob a forma de módulos. Os módulos Azure IoT Edge são as unidades de computação mais pequenas geridas pelo IoT Edge, e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros ou o seu próprio código específico de solução. Para saber mais sobre os módulos IoT Edge, consulte os [módulos Understand Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Qual é a diferença entre um tipo de oferta de contentore e um tipo de oferta de módulo IoT Edge?**

O tipo de oferta do módulo IoT Edge é um tipo específico de recipiente que funciona num dispositivo IoT Edge. Ele vem com configurações de configuração padrão para ser executado no contexto IoT Edge, e opcionalmente utiliza o módulo IoT Edge SDK para ser integrado com o tempo de execução Do IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publicar o seu módulo IoT Edge

**Selecionando a montra certa**

Os Módulos IoT Edge são publicados apenas no Azure Marketplace, o AppSource não se aplica.  Para obter mais informações sobre as diferenças e o público-alvo nas montras, consulte [a determinação da opção](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)de publicação para a sua solução .
 
**Opções de faturação**

Atualmente, o mercado suporta opções de faturação **gratuitas** e **bring your own license (BYOL)** para módulos IoT Edge.
 
**Opções de publicação**

Em todos os casos, os módulos IoT Edge devem selecionar a opção de publicação **Transact.**  Consulte [a escolha de uma opção de publicação](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) para obter mais detalhes sobre as opções de publicação.  

## <a name="eligibility-criteria"></a>Critérios de elegibilidade

Todos os termos dos acordos e políticas do Microsoft Azure Marketplace aplicam-se às ofertas do módulo IoT Edge.  Além disso, existem pré-requisitos e requisitos técnicos para os módulos IoT Edge.  

**Pré-requisitos**

Para publicar um módulo IoT Edge no Mercado Azure, precisa de cumprir os seguintes pré-requisitos:

- Acesso ao Centro parceiro. Para mais informações, consulte o guia de [publicação Azure Marketplace e AppSource.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)
- Acolhia o módulo IoT Edge num Registo de Contentores Azure. 
- Tenha os metadados do módulo IoT Edge prontos, tais como (lista não exaustiva): 
    - Um título
    - Uma descrição (em formato HTML)
    - Uma imagem de logotipo (formato PNG e tamanhos de imagem fixos, incluindo 40x40px, 90x90px, 115x115px, 255x115px)
    - Um termo de política de uso e privacidade
    - Configuração do módulo padrão (rota, propriedades duplas desejadas, criaçõesDeS, variáveis ambientais)
    - Documentação
    - Contactos de suporte

**Requisitos Técnicos**

Os principais requisitos técnicos para um Módulo IoT Edge, para que seja certificado e publicado no Azure Marketplace, são detalhados nos ativos técnicos do [módulo Prepare ioT Edge.](./partner-center-portal/create-iot-edge-module-asset.md)

## <a name="documentation-and-resources"></a>Documentação e Recursos

[Crie uma oferta de móduloIT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) -- Os passos para a publicação de uma nova oferta de módulos IoT Edge no Partner Center.

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez,

- [Saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o mercado.

Para se registar no Partner Center, e começar a criar uma nova oferta ou trabalhar numa existente,

- Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
- Consulte a criação de uma oferta de [móduloIT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) para obter informações sobre como publicar uma oferta de móduloIT Edge.
