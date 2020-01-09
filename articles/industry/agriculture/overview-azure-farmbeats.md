---
title: O que é o Azure FarmBeats
description: Fornece uma visão geral do Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b4f0a082ba68ce9de417d196c1f6ea593c5c8feb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473501"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Visão geral do Azure FarmBeats (versão prévia)

O Azure FarmBeats é uma oferta entre empresas disponível no Azure Marketplace. Ele habilita a agregação de conjuntos de dados de agricultura entre provedores. O Azure FarmBeats permite que você crie modelos de ia (inteligência artificial) ou de ML (aprendizado de máquina) com base em conjuntos de dados com fusível. Usando o Azure FarmBeats, as empresas agricultura podem se concentrar no principal valor-adições em vez do trabalho pesado não diferenciado da engenharia de dados.

> [!NOTE]
> O Azure FarmBeats está atualmente em visualização pública. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). O Azure FarmBeats é fornecido sem um contrato de nível de serviço. Use o [Fórum do Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ) para obter suporte.

![Batidas no farm de projetos](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Com a visualização do Azure FarmBeats, você pode:

- Avalie a integridade do farm usando o índice de vegetação e o índice de água com base em imagens de satélite.
- Obtenha recomendações sobre quantos sensores de umidade de solo usar e onde colocá-los.
- Acompanhe as condições do farm visualizando os dados de aterramento coletados pelos sensores de vários fornecedores.
- Obtenha o mapa de umidade de solo com base na fusão de dados de sensor e satélite.
- Obter informações acionáveis criando modelos de ia/ML com base em conjuntos de valores agregados.
- Crie ou aumente sua solução de agricultura digital fornecendo conselhos de integridade do farm.

## <a name="datahub"></a>Datahub

O Azure FarmBeats Datahub é uma camada de API, que habilita a agregação, a normalização e a condefinição de vários conjuntos de valores de agricultura entre provedores. Você pode usar o Azure FarmBeats para obter:
- **Dados de sensor** de dois provedores de sensores [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/)
- **Imagens satélite** da missão satélite [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) da Agência de espaço europeu
- **Drone imagens** de três provedores de imagens de drone [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [dji](https://dji.com/)

O Datahub é projetado como uma plataforma de API extensível. Estamos trabalhando com muitos outros provedores para integrar com o Azure FarmBeats, para que você tenha mais opções ao criar sua solução.

## <a name="accelerator"></a>Accelerator

O acelerador de FarmBeats do Azure é um aplicativo Web de exemplo, criado com base no Datahub. O acelerador vai iniciar a interface do usuário e o desenvolvimento do modelo. O acelerador de FarmBeats do Azure usa as APIs do Azure FarmBeats. Ele visualiza dados de sensor ingeridos como gráficos e saídas de modelo como mapas. Por exemplo, você pode usar o acelerador para criar um farm rapidamente e obter um mapa de índice vegetação ou um mapa de posicionamento de sensor para esse farm com facilidade.

## <a name="resources"></a>Recursos

O Azure FarmBeats é oferecido sem custo adicional e você paga apenas pelos recursos do Azure que você usa. Você pode usar os recursos abaixo para saber mais sobre a oferta:

- Fique informado sobre as últimas notícias do Azure FarmBeats visitando nosso [blog do Azure FarmBeats](https://aka.ms/farmbeatsblog).
- Busque ajuda postando uma pergunta em nosso [Fórum de suporte do Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Forneça comentários postando ou votando uma ideia de recurso em nosso [Fórum de comentários do Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instalar o Azure FarmBeats](install-azure-farmbeats.md)
