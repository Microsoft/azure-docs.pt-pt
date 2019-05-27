---
title: Módulos do Azure IoT Edge
description: O IoT Edge módulo da oferta no Azure Marketplace para editores de aplicações e serviços.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/22/2018
ms.author: qianw211
ms.openlocfilehash: c734461f1a57c100b4a00ab5c309659f8b0e903a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964017"
---
# <a name="iot-edge-modules"></a>Módulos do IoT Edge

O [do Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) plataforma é apoiada na Cloud do Azure.  Esta plataforma permite aos utilizadores implementar cargas de trabalho de cloud para ser executado diretamente em dispositivos IoT.  Módulo do IoT Edge pode executar cargas de trabalho offline e fazer a análise de dados localmente. Este tipo de oferta ajuda para poupar largura de banda, salvaguardar os dados locais e confidenciais e oferece o tempo de resposta de baixa latência.  Agora tem as opções para tirar partido destas cargas de trabalho criados previamente. Até agora, apenas algumas das soluções de original da Microsoft estavam disponíveis.  Tinha de investir o tempo e recursos para criar suas próprias soluções de IoT personalizadas.

Introduzindo a [módulos do IoT Edge no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), temos agora um único destino onde os editores podem expor e vender as suas soluções para o público-alvo de IoT. Os programadores de IoT, por fim, podem encontrar e comprar recursos para acelerar o desenvolvimento da sua solução.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Principais benefícios dos módulos do IoT Edge no Azure Marketplace:

| **Para editores**    | **Para os clientes (programadores de IoT)**  |
| :------------------- | :-------------------|
| Alcance milhões dos desenvolvedores que desejam criar e implementar soluções de IoT Edge.  | Compor uma solução de IoT Edge com a confiança da utilização de componentes de seguros e testados. |
| Publique uma vez e são executadas em qualquer hardware de IoT Edge que suporte contentores. | Reduza o tempo de comercialização, encontrando e implantando o dia 1 e 3rd a módulos do IoT Edge de terceiros para necessidades específicas. |
| Monetize com opções de faturação flexíveis <ul> <li> Gratuitas e traga a sua própria licença (BYOL). </li> </ul> | Fazer compras à sua escolha de modelos de faturação. <ul> <li> Gratuitas e traga a sua própria licença (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>O que é o módulo do IoT Edge?

O Azure IoT Edge permite-lhe implementar e gerir a lógica de negócios na borda sob a forma de módulos. Módulos do IoT Edge do Azure são as unidades de computação mais pequenas, geridas pelo IoT Edge e podem conter serviços da Microsoft (por exemplo, o Azure Stream Analytics), serviços de terceiros 3rd ou seu próprio código específico da solução. Para saber mais sobre os módulos do IoT Edge, veja [módulos de compreender o Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**O que é a diferença entre um tipo de oferta de contentor e um tipo de oferta de módulo do IoT Edge?**

O tipo de oferta de módulo do IoT Edge é um tipo específico de contentor em execução num dispositivo IoT Edge. Ele vem com definições de configuração padrão para ser executado no contexto do IoT Edge e, opcionalmente, utiliza o módulo do IoT Edge SDK para integrar com o runtime do IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publicar o seu módulo do IoT Edge

**Selecionar a loja certa**

Módulos do IoT Edge só são publicados no Azure Marketplace, AppSource não se aplica.  Para obter mais informações sobre as diferenças e o público-alvo entre lojas, veja [determinar a opção de publicação para a sua solução](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Opções de faturação**

Atualmente, o marketplace suporta **gratuito** e **traga a sua própria licença (BYOL)** opções para módulos do IoT Edge de faturação.
 
**Opções de publicação**

Em todos os casos, os módulos do IoT Edge devem selecionar o **Transact** opção de publicação.  Ver [escolha uma opção de publicação](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) para obter mais detalhes sobre as opções de publicação.  

## <a name="eligibility-criteria"></a>Critérios de elegibilidade

Todas as condições das políticas e contratos do Microsoft Azure Marketplace aplicam-se a ofertas de módulo do IoT Edge.  Além disso, existem pré-requisitos e requisitos técnicos para módulos do IoT Edge.  

**Pré-requisitos**

Para publicar o módulo do IoT Edge no Azure Marketplace, tem de cumprir os seguintes pré-requisitos:

- Acesso ao Portal de parceiro de Cloud (CPP). Para obter mais informações, consulte [guia de publicação do Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Aloje o seu módulo do IoT Edge no Azure Container Registry. 
- Ter seus metadados do módulo do IoT Edge pronto como (lista parcial): 
    - Um título
    - Uma descrição (no formato HTML)
    - Uma imagem de logótipo (formato PNG e tamanhos de imagem fixa incluindo 40x40px, 90x90px, 115x115px, 255x115px)
    - Uma condição de política de privacidade e de utilização
    - Padrão de configuração do módulo (rota, as propriedades duplas assim o desejar, createOptions, variáveis de ambiente)
    - Documentação
    - Contactos de suporte

**Requisitos técnicos**

Os requisitos técnicos primários para um módulo do IoT Edge, para que possam obter uma certificação e publicada no Azure Marketplace, estão descritos no [preparar seu módulo do IoT Edge ativos técnicos](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Documentação e recursos

[Criar uma oferta de módulo do IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) -– as etapas para a publicação de um novo módulo do IoT Edge oferecem com o Portal de publicação da Cloud.

## <a name="next-steps"></a>Passos Seguintes

Se ainda não fez isso,

- Registar no [Microsoft Partner Network](https://partner.microsoft.com/membership).
- Criar uma [Account Microsoft](https://account.microsoft.com/account/) (necessários para o Azure Marketplace transact ofertas; recomendado para outras pessoas).
- Submeter os [formulário de Registro do Marketplace](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv). Veja como [criar uma conta do Centro de parceiros](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) para obter mais informações.

Se é registrado e estiver a criar uma nova oferta ou trabalhando num já existente

- Inicie sessão no [Cloud Partner Portal](https://cloudpartner.azure.com/) para criar ou concluir sua oferta.
- Ver [oferta de módulo do IoT Edge descrição geral de publicação](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) para obter informações sobre como publicar uma oferta de módulo do IoT Edge.
