---
title: Configurar as definições da base de dados em API Azure para FHIR
description: Este artigo descreve como configurar as definições da Base de Dados em API Azure para FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: adc6fdf144927d10f811a00aa33f244cfdc25042
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871765"
---
# <a name="configure-database-settings"></a>Configurar definições de base de dados 

A Azure API para FHIR utiliza base de dados para armazenar os seus dados. O desempenho da base de dados subjacente depende do número de Unidades de Pedido (RU) selecionadas durante o fornecimento de serviços ou nas definições de base de dados após o fornecimento do serviço.

A Azure API para FHIR empresta o conceito de RUs da Cosmos DB (ver [Unidades de Pedido em Azure Cosmos DB)](https://docs.microsoft.com/azure/cosmos-db/request-units)ao definir o desempenho da base de dados subjacente. 

A produção deve ser disponibilizada para garantir que estão sempre disponíveis recursos suficientes para a sua base de dados. Quantas RUs precisa para a sua aplicação depende das operações que executa. As operações podem ir desde a leitura simples e escritas a consultas mais complexas. 

> [!NOTE]
> À medida que diferentes operações consomem um número diferente de RU, devolvemos o número real de RUs consumidos em cada chamada de API em cabeçalho de resposta. Desta forma pode perfilar o número de RUs consumidos pela sua aplicação.

## <a name="update-throughput"></a>Atualização de produção
Para alterar esta definição no portal Azure, navegue na API Azure para FHIR e abra a lâmina base de dados. Em seguida, altere a produção do Provisionado para o valor pretendido, dependendo das suas necessidades de desempenho. Pode alterar o valor até um máximo de 10.000 RU/s. Se precisar de um valor mais elevado, contacte o suporte da Azure.

> [!NOTE] 
> Valor mais elevado significa API Azure mais elevado para a produção de FHIR e custos mais elevados do serviço.

![Config Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a atualizar as suas RUs para Azure API para FHIR. Em seguida, implementar uma Azure API totalmente gerida para FHIR:
 
>[!div class="nextstepaction"]
>[Implementar o Azure API for FHIR](fhir-paas-portal-quickstart.md)