---
title: Dados de importação ou exportação com Configuração de Aplicações Azure
description: Saiba como importar ou exportar dados de configuração de ou para a Configuração de Aplicações Azure. Troque os dados entre a sua loja de Configuração de Aplicações e o projeto de código.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 0e127a11f3821325602a78d46c69b2330bfb8293
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074622"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

A Azure App Configuration suporta operações de importação e exportação de dados. Utilize estas operações para trabalhar com dados de configuração em massa e trocar dados entre a sua loja de Configuração de Aplicações e o projeto de código. Por exemplo, pode configurar uma loja de Configuração de Aplicações para testes e outra para produção. Pode copiar as definições de aplicação entre elas para que não tenha de introduzir dados duas vezes.

Este artigo fornece um guia para importar e exportar dados com configuração de aplicações. Se quiser estabelecer uma sincronização contínua com o seu repo GitHub, dê uma olhada na nossa [Ação GitHub.](./concept-github-action.md)

## <a name="import-data"></a>Importar dados

A Import traz dados de configuração para uma loja de Configuração de Aplicações a partir de uma fonte existente. Utilize a função de importação para migrar dados para uma loja de Configuração de Aplicações ou agregar dados de várias fontes. A Configuração de Aplicativos suporta a importação de um ficheiro JSON, YAML ou propriedades.

Importar dados utilizando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](./scripts/cli-import.md). A partir do portal Azure, siga estes passos:

1. Navegue na sua loja de Configuração de Aplicações e selecione **Import/Export** a partir do menu **Operações.**

1. No **separador 'Importar',** selecione **Source service**  >  **'Ficheiro de Configuração**do serviço'.

1. Selecione **Para o idioma** e selecione o tipo de entrada pretendido.

1. Selecione o ícone **pasta** e navegue no ficheiro para importar.

    ![Arquivo de importação](./media/import-file.png)

1. Selecione um **Separador**e introduza opcionalmente um **Prefixo** para usar para nomes-chave importados.

1. Opcionalmente, selecione uma **etiqueta**.

1. **Selecione Aplicar** para terminar a importação.

    ![Arquivo de importação terminado](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

A exporte escreve dados de configuração armazenados na Configuração de Aplicações para outro destino. Utilize a função de exportação, por exemplo, para guardar dados numa loja de Configuração de Aplicações para um ficheiro que está incorporado no seu código de aplicação durante a implementação.

Dados de exportação utilizando o [portal Azure](https://portal.azure.com) ou o [Azure CLI](./scripts/cli-export.md). A partir do portal Azure, siga estes passos:

1. Navegue na sua loja de Configuração de Aplicações e selecione **Import/Export**.

1. No **separador Exportação,** selecione **'Ficheiro**de Configuração do serviço  >  **alvo'.**

1. Introduza opcionalmente um **Prefixo** e selecione um **Rótulo** e um ponto no tempo para as chaves serem exportadas.

1. Selecione um separador do tipo de **ficheiro**  >  **Separator**.

1. **Selecione Aplicar** para terminar a exportação.

    ![Arquivo de exportação terminado](./media/export-file-complete.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web ASP.NET Core](./quickstart-aspnet-core-app.md)