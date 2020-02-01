---
title: Dados de importação ou exportação com configuração de aplicações do Azure  Microsoft Docs
description: Saiba importar ou exportar dados de ou para a Configuração de Aplicações Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 64fcc8396fc1b771d0095ee595fd177d7fe99b58
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899511"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

A Configuração de Aplicações Azure suporta operações de importação e exportação de dados. Utilize estas operações para trabalhar com dados de configuração a granel e trocar dados entre a sua loja de configuração de aplicações e o projeto de código. Por exemplo, pode configurar uma loja de configuração de aplicações para testes e outra para produção. Em seguida, pode copiar as definições de aplicação entre elas através de um ficheiro para que não tenha de introduzir os dados duas vezes.

Este artigo fornece um guia para importar e exportar dados com configuração de aplicações.

## <a name="import-data"></a>Importar dados

A importação traz dados de configuração para uma loja de configuração de aplicações a partir de uma fonte existente, em vez de inseri-los manualmente. Utilize a função de importação para migrar dados para uma loja de configuração de aplicações ou dados agregados de várias fontes. A Configuração de Aplicações suporta a importação de um ficheiro JSON, YAML ou propriedades.

Importar dados utilizando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](./scripts/cli-import.md). A partir do portal Azure, siga estes passos:

1. Navegue na sua loja de configuração de aplicações e selecione **Import/Export**.

2. No separador **Import,** selecione **Source service** > **Configuração .**

3. Selecione **Para idioma** > Tipo de **ficheiro**.

4. Selecione o ícone **da Pasta** e navegue no ficheiro para importar.

    ![Arquivo de importação](./media/import-file.png)

5. Selecione um **Separador**e introduza opcionalmente um **Prefixo** para usar para nomes-chave importados.

6. Opcionalmente, selecione um **Rótulo**.

7. Selecione **Aplicar** para terminar a importação.

    ![Arquivo de importação terminado](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Export writes dados de configuração armazenados na Configuração de Aplicações para outro destino. Utilize a função de exportação, por exemplo, para guardar dados numa loja de Configuração de Aplicações para um ficheiro incorporado com o seu código de aplicação durante a implementação.

Exportar dados utilizando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](./scripts/cli-export.md). A partir do portal Azure, siga estes passos:

1. Navegue na sua loja de configuração de aplicações e selecione **Import/Export**.

2. No separador **Export,** selecione **o serviço Target** > Ficheiro de **Configuração**.

3. Introduza opcionalmente um **Prefixo** e selecione um **Rótulo** e um ponto-a-tempo para as chaves serem exportadas.

4. Selecione um **separador** > **do tipo de ficheiro** .

5. Selecione **Aplicar** para terminar a exportação.

    ![Arquivo de exportação terminado](./media/export-file-complete.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um aplicativo Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
