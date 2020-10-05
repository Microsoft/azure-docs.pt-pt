---
title: O que é Azure FarmBeats
description: Fornece uma visão geral da Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12bb0b0098b5108bf780b88fc42b86861ea6fcdc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87439561"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Visão geral do Azure FarmBeats (Preview)

A azure FarmBeats é uma oferta de negócios disponíveis no Azure Marketplace. Permite a agregação de conjuntos de dados agrícolas entre os fornecedores. O Azure FarmBeats permite-lhe construir modelos de inteligência artificial (IA) ou machine learning (ML) baseados em conjuntos de dados fundidos. Ao utilizar o Azure FarmBeats, as empresas agrícolas podem concentrar-se em valores-acrescentadores fundamentais em vez do levantamento pesado indiferenciado da engenharia de dados.

> [!NOTE]
> Azure FarmBeats está atualmente em pré-visualização pública. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A Azure FarmBeats é fornecida sem um acordo de nível de serviço. Utilize o [Fórum de Apoio Azure FarmBeats](https://aka.ms/farmbeatssupport) para obter apoio.

![Project Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Com a pré-visualização do Azure FarmBeats pode:

- Avaliar a saúde da fazenda usando o índice de vegetação e o índice de água com base em imagens de satélite.
- Obtenha recomendações sobre quantos sensores de humidade do solo podem ser utilizados e onde colocá-los.
- Acompanhe as condições da exploração visualizando os dados terrestres recolhidos por sensores de vários fornecedores.
- Obtenha o mapa da humidade do solo com base na fusão de dados de satélite e sensores.
- Obtenha insights accuáveis construindo modelos de IA/ML em cima de conjuntos de dados agregados.
- Construa ou aumente a sua solução de agricultura digital fornecendo aconselhamento de saúde agrícola.

## <a name="datahub"></a>Datahub

O Azure FarmBeats Datahub é uma camada API, que permite agregação, normalização e contextualização de vários conjuntos de dados agrícolas entre fornecedores. Você pode usar Azure FarmBeats para obter:
- **Dados** de sensores de dois fornecedores de sensores [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic,](https://teralytic.com/) [Pessl Instruments](https://metos.at/)
- **Imagens de satélite** da missão satélite [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) da Agência Espacial Europeia
- **Imagens de drones** de três fornecedores de imagens de drones [senseFly](https://www.sensefly.com/) , [SlantRange,](https://slantrange.com/) [DJI](https://dji.com/)

O Datahub foi concebido como uma plataforma API extensível. Estamos a trabalhar com muitos mais fornecedores para integrar com o Azure FarmBeats, para que tenhas mais escolha enquanto constróis a tua solução.

## <a name="accelerator"></a>Acelerador

O Acelerador Azure FarmBeats é uma aplicação web de amostras, que é construída em cima do Datahub. O acelerador inicia a interface do utilizador e o desenvolvimento do modelo. O acelerador Azure FarmBeats utiliza APIs da Azure FarmBeats. Visualiza dados de sensores ingeridos como gráficos e saídas de modelos como mapas. Por exemplo, você pode usar o acelerador para criar uma fazenda rapidamente e obter um mapa do índice de vegetação ou um mapa de colocação de sensores para essa fazenda facilmente.

## <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)

Um administrador pode definir regras de acesso para Azure FarmBeats usando uma das funções predefinidas. As funções determinam quais as áreas da aplicação a que um utilizador tem acesso e que ações pode realizar. Existem dois tipos de papéis no Azure FarmBeats - para utilizadores e para parceiros.

### <a name="user-roles"></a>Funções de Utilizador

Um [administrador pode adicionar e gerir os utilizadores](manage-users-in-azure-farmbeats.md) e definir os seus níveis de acesso com base em duas funções de utilizador: Administrador e Read-Only.

### <a name="partner-roles"></a>Funções de Parceiro

Um administrador pode adicionar vários parceiros como fornecedores de dados à Azure FarmBeats. Resumindo as funções de parceiro disponíveis no FarmBeats e as suas permissões:

| Tipo de Parceiro    |   Ações  | Âmbito |
| ---- | -------- | -------- |
| Parceiro sensorial  |   Criar, Ler, Atualizar <br/> <br/> Ler, Atualizar | Modelo de dispositivo, dispositivo, sensormodel, sensor <br/> <br/> Tipo Estendido |
| Parceiro de imagens  |   Criar, Ler, Atualizar <br/> <br/> Ler, Atualizar <br/> <br/> Ler | Cena, SceneFile <br/> <br/> Tipo Estendido <br/> <br/> Quinta |
| Parceiro meteorológico* <br/> <br/>  (* Em breve) |   Criar, Ler, Atualizar <br/> <br/> Ler, Atualizar <br/> <br/> Ler | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> Tipo Estendido <br/> <br/> Quinta |

## <a name="resources"></a>Recursos

A Azure FarmBeats é oferecido sem custo adicional e você paga apenas pelos recursos Azure que você usa. Pode utilizar os recursos abaixo para saber mais sobre a oferta:

- Mantenha-se informado sobre as últimas novidades da Azure FarmBeats visitando o nosso [blog Azure FarmBeats](https://aka.ms/farmbeatsblog).
- Procure ajuda publicando uma pergunta no nosso [fórum de apoio a Azure FarmBeats.](https://aka.ms/farmbeatssupport)
- Forneça feedback publicando ou votando para uma ideia de recurso no nosso [fórum de feedback Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instalar o Azure FarmBeats](install-azure-farmbeats.md)
