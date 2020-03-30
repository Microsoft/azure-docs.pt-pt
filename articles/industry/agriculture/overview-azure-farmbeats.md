---
title: O que é Azure FarmBeats
description: Fornece uma visão geral da Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b067b18985905b226287f9dd10ad4b937fab6df1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76767974"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Visão geral dos FarmBeats Azure (Pré-visualização)

A Azure FarmBeats é uma oferta de negócios a negócios disponível no Azure Marketplace. Permite a agregação de conjuntos de dados agrícolas entre os fornecedores. O Azure FarmBeats permite-lhe construir modelos de inteligência artificial (IA) ou machine learning (ML) com base em conjuntos de dados fundidos. Utilizando o Azure FarmBeats, as empresas agrícolas podem focar-se em valores-adicionais fundamentais em vez do levantamento pesado indiferenciado da engenharia de dados.

> [!NOTE]
> O Azure FarmBeats está atualmente em pré-visualização pública. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . O Azure FarmBeats é fornecido sem um acordo de nível de serviço. Utilize o [Azure FarmBeats Forum](https://aka.ms/FarmBeatsMSDN ) para apoio.

![Batidas da Fazenda do Projeto](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Com a pré-estreia do Azure FarmBeats pode:

- Avaliar a saúde agrícola utilizando índice de vegetação e índice de água com base em imagens de satélite.
- Obtenha recomendações sobre quantos sensores de humidade do solo usar e onde colocá-los.
- Acompanhe as condições da exploração através da visualização de dados terrestres recolhidos por sensores de vários fornecedores.
- Obtenha o mapa da humidade do solo com base na fusão de dados de satélite e sensores.
- Obtenha insights atuais construindo modelos DeI/ML em cima de conjuntos de dados agregados.
- Construa ou aumente a sua solução de agricultura digital, fornecendo aconselhamento de saúde agrícola.

## <a name="datahub"></a>Datahub

O Azure FarmBeats Datahub é uma camada API, que permite agregação, normalização e contextualização de vários conjuntos de dados agrícolas entre fornecedores. Pode usar o Azure FarmBeats para obter:
- Dados de **sensores** de dois fornecedores de sensores [Davis Instruments,](https://www.davisinstruments.com/product/enviromonitor-gateway/) [Teralytic,](https://teralytic.com/) [Pessl Instruments](https://metos.at/)
- **Imagens** de satélite da missão satélite [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) da Agência Espacial Europeia
- **Imagens** de drones de três fornecedores de imagens de drones [senseFly,](https://www.sensefly.com/) [SlantRange,](https://slantrange.com/) [DJI](https://dji.com/)

Datahub é projetado como uma plataforma API extensível. Estamos a trabalhar com muitos mais fornecedores para integrar com o Azure FarmBeats, para que tenha mais escolha enquanto constrói a sua solução.

## <a name="accelerator"></a>Acelerador

O Acelerador Azure FarmBeats é uma aplicação web de amostra, que é construída em cima do Datahub. O Acelerador inicia a interface do utilizador e o desenvolvimento do modelo. O acelerador Azure FarmBeats utiliza as APIs da Azure FarmBeats. Visualiza os dados dos sensores ingeriu como gráficos e saídas de modelos como mapas. Por exemplo, você pode usar o acelerador para criar uma fazenda rapidamente e obter um mapa de índice de vegetação ou um mapa de colocação de sensores para essa fazenda facilmente.

## <a name="resources"></a>Recursos

O Azure FarmBeats não é oferecido gratuitamente e só paga pelos recursos Azure que utiliza. Pode utilizar os recursos abaixo para saber mais sobre a oferta:

- Fique informado sobre as últimas novidades da Azure FarmBeats visitando o nosso [blog Azure FarmBeats.](https://aka.ms/farmbeatsblog)
- Procure ajuda colocando uma pergunta no nosso fórum de [apoio Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Forneça feedback publicando ou votando para uma ideia de recurso no nosso fórum de [feedback Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instalar o Azure FarmBeats](install-azure-farmbeats.md)
