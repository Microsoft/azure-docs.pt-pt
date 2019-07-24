---
title: Módulos de Azure IoT Edge
description: A oferta do módulo IoT Edge no Azure Marketplace para Publicadores de aplicativo e serviço.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/22/2018
ms.author: pabutler
ms.openlocfilehash: 3010b63c7c4c575d915789c19b60710194c79196
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874677"
---
# <a name="iot-edge-modules"></a>Módulos do IoT Edge

A plataforma de [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) é apoiada pela nuvem do Azure.  Essa plataforma permite que os usuários implantem cargas de trabalho de nuvem para serem executadas diretamente em dispositivos IoT.  Um módulo IoT Edge pode executar cargas de trabalho offline e fazer a análise de dados localmente. Esse tipo de oferta ajuda a economizar largura de banda, proteger dados locais e confidenciais e oferece tempo de resposta de baixa latência.  Agora você tem as opções para aproveitar essas cargas de trabalho predefinidas. Até agora, apenas algumas soluções de primeira parte da Microsoft estavam disponíveis.  Você precisava investir o tempo e os recursos na criação de suas próprias soluções de IoT personalizadas.

Ao introduzir os [módulos de IOT Edge no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), agora temos um único destino para que os editores exponham e vendam suas soluções para o público de IOT. Os desenvolvedores de IoT podem, por fim, encontrar e comprar recursos para acelerar o desenvolvimento da solução.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Principais benefícios dos módulos IoT Edge no Azure Marketplace:

| **Para Publicadores**    | **Para clientes (desenvolvedores de IoT)**  |
| :------------------- | :-------------------|
| Alcance milhões de desenvolvedores que buscam criar e implantar soluções de IoT Edge.  | Compor uma solução de IoT Edge com a confiança de usar componentes seguros e testados. |
| Publique uma vez e execute em qualquer IoT Edge hardware que ofereça suporte a contêineres. | Reduza o tempo de colocação no mercado encontrando e implantando módulos de IoT Edge de 1º e de terceiros para necessidades específicas. |
| Monetizar com opções de cobrança flexíveis <ul> <li> Gratuito e traga sua própria licença (BYOL). </li> </ul> | Faça compras com sua escolha de modelos de cobrança. <ul> <li> Gratuito e traga sua própria licença (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>O que é um módulo IoT Edge?

Azure IoT Edge permite implantar e gerenciar a lógica de negócios na borda na forma de módulos. Os módulos de Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como Azure Stream Analytics), serviços de terceiros ou seu próprio código específico da solução. Para saber mais sobre os módulos IoT Edge, consulte [entender os módulos Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Qual é a diferença entre um tipo de oferta de contêiner e um tipo de oferta de módulo IoT Edge?**

O tipo de oferta de módulo IoT Edge é um tipo específico de contêiner em execução em um dispositivo IoT Edge. Ele vem com definições de configuração padrão para execução no contexto de IoT Edge e, opcionalmente, usa o SDK do módulo IoT Edge para ser integrado ao tempo de execução do IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publicando seu módulo de IoT Edge

**Selecionando a vitrine certa**

IoT Edge módulos são publicados apenas no Azure Marketplace, o AppSource não se aplica.  Para obter mais informações sobre as diferenças e o público-alvo nas vitrines, consulte [determinando a opção de publicação para sua solução](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Opções de cobrança**

O Marketplace atualmente dá  suporte a opções de cobrança gratuitas e **BYOL (traga sua própria licença)** para módulos de IOT Edge.
 
**Opções de publicação**

Em todos os casos, IoT Edge módulos devem selecionar a opção **Transact** Publishing.  Consulte [escolher uma opção de publicação](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) para obter mais detalhes sobre as opções de publicação.  

## <a name="eligibility-criteria"></a>Critérios de Elegibilidade

Todos os termos do Microsoft Azure Marketplace contratos e políticas se aplicam a ofertas de módulo IoT Edge.  Além disso, há pré-requisitos e requisitos técnicos para módulos de IoT Edge.  

**Pré-requisitos**

Para publicar um módulo IoT Edge no Azure Marketplace, você precisa atender aos seguintes pré-requisitos:

- Acesso ao Portal do Cloud Partner (CPP). Para obter mais informações, consulte o [Guia de publicação do Azure Marketplace e do AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hospede seu módulo de IoT Edge em um registro de contêiner do Azure. 
- Tenha os metadados do módulo IoT Edge prontos, como (lista não exaustiva): 
    - Um título
    - Uma descrição (no formato HTML)
    - Uma imagem de logotipo (formato PNG e tamanhos de imagem fixa, incluindo 40x40px, 90x90px, 115x115px, 255x115px)
    - Um termo de uso e política de privacidade
    - Configuração padrão do módulo (rota, propriedades desejadas de entrelaçamento, criaroptions, variáveis de ambiente)
    - Documentação
    - Contatos de suporte

**Requisitos técnicos**

Os requisitos técnicos primários para um módulo IoT Edge, para que ele seja certificado e publicado no Azure Marketplace, são detalhados nos [ativos técnicos do módulo preparar seus IOT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Documentação e recursos

[Criar uma oferta de módulo IOT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) – as etapas para publicar uma nova oferta de módulo IOT Edge com o portal de publicação na nuvem.

## <a name="next-steps"></a>Passos Seguintes

Se você ainda não fez isso,

- Registre-se no [Microsoft Partner Network](https://partner.microsoft.com/membership).
- Criar uma [conta da Microsoft](https://account.microsoft.com/account/) (necessária para ofertas do Azure Marketplace Transact; recomendado para outras pessoas).
- Envie o [formulário de registro do Marketplace](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv). Consulte como [criar uma conta do Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) para obter mais informações.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- Entre no [portal do Cloud Partner](https://cloudpartner.azure.com/) para criar ou concluir sua oferta.
- Confira [IOT Edge módulo oferta visão geral de publicação](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) para obter informações sobre como publicar uma oferta de módulo IOT Edge.
