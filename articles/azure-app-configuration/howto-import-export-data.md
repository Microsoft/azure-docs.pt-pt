---
title: Dados de importação ou exportação com configuração de aplicações do Azure
description: Saiba importar ou exportar dados de ou para a Configuração de Aplicações Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 2c074cbd99620a482b18cbe2dfcce8f987d78bd5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278275"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

A Configuração de Aplicações Azure suporta operações de importação e exportação de dados. Utilize estas operações para trabalhar com dados de configuração a granel e trocar dados entre a sua loja de configuração de aplicações e o projeto de código. Por exemplo, pode configurar uma loja de configuração de aplicações para testes e outra para produção. Pode copiar as definições de aplicação entre elas para que não tenha de introduzir os dados duas vezes.

Este artigo fornece um guia para importar e exportar dados com configuração de aplicações.

## <a name="import-data"></a>Importar dados

A Importação traz dados de configuração para uma loja de configuração de aplicações a partir de uma fonte existente. Utilize a função de importação para migrar dados para uma loja de configuração de aplicações ou dados agregados de várias fontes. A Configuração de Aplicações suporta a importação de um ficheiro JSON, YAML ou propriedades.

Importar dados utilizando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](./scripts/cli-import.md). A partir do portal Azure, siga estes passos:

1. Navegue na sua loja de Configuração de Aplicações e selecione **Import/Export** a partir do menu **Operações.**

1. No separador **Import,** selecione **Source service** > **Configuração .**

1. Selecione **Para idioma** e selecione o tipo de entrada pretendido.

1. Selecione o ícone **da Pasta** e navegue no ficheiro para importar.

    ![Arquivo de importação](./media/import-file.png)

1. Selecione um **Separador**e introduza opcionalmente um **Prefixo** para usar para nomes-chave importados.

1. Opcionalmente, selecione um **Rótulo**.

1. Selecione **Aplicar** para terminar a importação.

    ![Arquivo de importação terminado](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Export writes dados de configuração armazenados na Configuração de Aplicações para outro destino. Utilize a função de exportação, por exemplo, para guardar dados numa loja de Configuração de Aplicações para um ficheiro incorporado com o seu código de aplicação durante a implementação.

Exportar dados utilizando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](./scripts/cli-export.md). A partir do portal Azure, siga estes passos:

1. Navegue na sua loja de configuração de aplicações e selecione **Import/Export**.

1. No separador **Export,** selecione **o serviço Target** > Ficheiro de **Configuração**.

1. Introduza opcionalmente um **Prefixo** e selecione um **Rótulo** e um ponto-a-tempo para as chaves serem exportadas.

1. Selecione um **separador** > **do tipo de ficheiro** .

1. Selecione **Aplicar** para terminar a exportação.

    ![Arquivo de exportação terminado](./media/export-file-complete.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web ASP.NET Core](./quickstart-aspnet-core-app.md)  
