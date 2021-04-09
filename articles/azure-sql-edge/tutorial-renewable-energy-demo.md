---
title: Implantação de Azure SQL Edge em turbinas num parque eólico Contoso
description: Neste tutorial, você usará Azure SQL Edge para deteção de despertar nas turbinas de um parque eólico contoso.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723478"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Usando a Azure SQL Edge para construir recursos renováveis mais inteligentes

Esta demonstração de Azure SQL Edge baseia-se numa Quinta de Energia Renovável Contoso, uma exploração de turbinas eólicas que utiliza a borda DB SQL para o processamento de dados a bordo do gerador. 

Esta demonstração irá acompanhá-lo através da resolução de um alerta que está a ser levantado devido à turbulência do vento ser detetada no dispositivo. Irá treinar um modelo e implantá-lo no SQL DB Edge que irá corrigir o despertar do vento detetado e, em última análise, otimizar a potência.

Azure SQL Edge - vídeo de demonstração de energia renovável no Canal 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>Configurar a demonstração no seu computador local
O Git será usado para copiar todos os ficheiros da demonstração para o computador local. 

1. Instale git a partir [daqui.](https://git-scm.com/download)
2. Abra um pedido de comando e navegue para uma pasta onde o repo deve ser descarregado. 
3. Emita o https://github.com/microsoft/sql-server-samples.git comando.
4. Navegue para **'sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo'** no local onde o repositório é clonado.
5. Siga as instruções em README.md para configurar o ambiente de demonstração e executar a demonstração.