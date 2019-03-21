---
title: Importar ou exportar dados com a configuração de aplicações do Azure | Documentos da Microsoft
description: Saiba como importar ou exportar dados de ou para a configuração de aplicações do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 846472e00bc048de906ee8e14f6de38e366f3571
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225234"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

Dados de suporte de configuração de aplicações do Azure importar e exportar operações. Utilize estas operações para trabalhar com dados de configuração de dados em massa e o exchange entre o arquivo de configuração de aplicação e o código de projeto. Por exemplo, pode configurar arquivo de configuração de uma aplicação para fins de teste e outra para produção. Em seguida, pode copiar as definições da aplicação entre eles através de um ficheiro para que não precisa inserir dados duas vezes.

Este artigo fornece um guia para a importação e exportação de dados com a configuração de aplicações.

## <a name="import-data"></a>Importar dados

Importar traz a configuração do arquivo de dados numa configuração de aplicação de uma fonte existente, em vez de introduzir manualmente. Use a função de importação para migrar dados para um arquivo de configuração de aplicação ou agregar dados de várias origens. Configuração de aplicações suporta a importação de um arquivo de propriedades, JSON ou YAML.

Importar dados utilizando o [portal do Azure](https://aka.ms/azconfig/portal) ou o [CLI do Azure](./scripts/cli-import.md). No portal do Azure, siga estes passos:

1. Navegue para o arquivo de configuração de aplicação e selecione **importação/exportação**.

2. Sobre o **importação** separador, selecione **o serviço de origem** > **ficheiro de configuração**.

3. Selecione **para o idioma** > **tipo de ficheiro**.

4. Selecione o **pasta** ícone e procure o ficheiro a importar.

    ![Ficheiro de importação](./media/import-file.png)

5. Selecione um **separador**e, opcionalmente, introduza um **prefixo** a utilizar para nomes de chaves importados.

6. Opcionalmente, selecione um **etiqueta**.

7. Selecione **aplicar** para concluir a importação.

    ![Ficheiro de importação concluído](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Exportação escreve dados de configuração armazenados na configuração de aplicação para outro destino. Utilize a função de exportação, por exemplo, para guardar dados num arquivo de configuração de aplicação para um ficheiro que é incorporado com o código da aplicação durante a implementação.

Exportar dados utilizando o [portal do Azure](https://aka.ms/azconfig/portal) ou o [CLI do Azure](./scripts/cli-export.md). No portal do Azure, siga estes passos:

1. Navegue para o arquivo de configuração de aplicação e selecione **importação/exportação**.

2. Sobre o **exportar** separador, selecione **serviço de destino** > **ficheiro de configuração**.

3. Opcionalmente, introduza um **prefixo** e selecione um **etiqueta** e um ponto anterior no tempo para as chaves ser exportada.

4. Selecione um **tipo de ficheiro** > **separador**.

5. Selecione **aplicar** para concluir a exportação.

    ![Ficheiro de exportação concluída](./media/export-file-complete.png)

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma aplicação web ASP.NET](./quickstart-aspnet-core-app.md)  
