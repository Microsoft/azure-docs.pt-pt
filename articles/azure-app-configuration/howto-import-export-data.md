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
ms.openlocfilehash: 851a8705b3cfa9c88e369af7b961a653dee8fd7a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959855"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

Dados de suporte de configuração de aplicações do Azure importar e exportar operações. Eles permitem que trabalhe com dados de configuração em massa, bem como dados de exchange entre a configuração da aplicação armazenar e projeto de código. Por exemplo, pode configurar arquivo de configuração de uma aplicação para fins de teste e outra para produção e copiar as definições da aplicação entre eles através de um ficheiro para que não tem de introduzir os dados duas vezes.

Este artigo fornece um guia para a importação e exportação de dados com a configuração de aplicações.

## <a name="import-data"></a>Importar dados

Importar traz a configuração do arquivo de dados na configuração de aplicações de uma fonte existente, em vez de introduzir manualmente. Pode usar a função de importação para migrar dados para um arquivo de configuração de aplicação ou agregar dados de várias origens. Configuração de aplicações suporta a importação de um ficheiro JSON, YAML ou propriedades.

Pode importar dados através da [portal do Azure](https://aka.ms/azconfig/portal) ou [CLI do Azure](./scripts/cli-import.md). No portal do Azure, siga estes passos:

1. Navegue até ao seu arquivo de configuração de aplicação e clique em **importação/exportação**.

2. Na **importação** separador, escolha **serviço de origem** e **ficheiro de configuração**.

3. Escolher **para o idioma** e **tipo de ficheiro**.

4. Clique nas **pasta** ícone e procure o ficheiro a importar.

    ![Ficheiro de importação](./media/import-file.png)

5. Escolher uma **separador** e, opcionalmente, introduza um **prefixo** a utilizar para nomes de chaves importados.

6. Opcionalmente, escolha uma **etiqueta**.

7. Clique em **aplicar** para concluir a importação.

    ![Ficheiro de importação concluída](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Exportação escreve dados de configuração armazenados na configuração de aplicação para outro destino. Pode usar a função de exportação, por exemplo, para guardar dados num arquivo de configuração de aplicação para um ficheiro que será incorporado com o código da aplicação durante a implementação.

Pode exportar dados com o [portal do Azure](https://aka.ms/azconfig/portal) ou [CLI do Azure](./scripts/cli-export.md). No portal do Azure, siga estes passos:

1. Navegue até ao seu arquivo de configuração de aplicação e clique em **importação/exportação**.

2. Na **exportar** separador, escolha **serviço de destino** e **ficheiro de configuração**.

3. Opcionalmente, introduza um **prefixo** e escolha um **etiqueta** e um ponto anterior no tempo para as chaves ser exportada.

4. Escolher uma **tipo de ficheiro** e **separador**.

5. Clique em **aplicar** para concluir a exportação.

    ![Ficheiro de exportação concluída](./media/export-file-complete.png)

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma aplicação web ASP.NET](./quickstart-aspnet-core-app.md)  
