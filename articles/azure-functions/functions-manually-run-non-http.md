---
title: Executar manualmente um Azure Functions disparado por HTTP
description: Usar uma solicitação HTTP para executar um Azure Functions disparado não HTTP
author: craigshoemaker
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 8198ff6579aff839ff9aacb729e2f3f8d3472fae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230465"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Executar manualmente uma função não acionada por HTTP

Este artigo demonstra como executar manualmente uma função não disparada por HTTP por meio da solicitação HTTP especialmente formatada.

Em alguns contextos, talvez seja necessário executar "sob demanda" uma função do Azure que é disparada indiretamente.  Exemplos de gatilhos indiretos incluem [funções em uma agenda](./functions-create-scheduled-function.md) ou funções que são executadas como resultado da [ação de outro recurso](./functions-create-storage-blob-triggered-function.md). 

O [postmaster](https://www.getpostman.com/) é usado no exemplo a seguir, mas você pode usar a [ondulação](https://curl.haxx.se/), o [Fiddler](https://www.telerik.com/fiddler) ou qualquer outra ferramenta semelhante para enviar solicitações HTTP.

## <a name="define-the-request-location"></a>Definir o local da solicitação

Para executar uma função não disparada por HTTP, você precisará de uma maneira de enviar uma solicitação ao Azure para executar a função. A URL usada para fazer essa solicitação usa um formulário específico.

![Definir o local da solicitação: nome do host + caminho da pasta + nome da função](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nome do host:** A localização pública do aplicativo de funções que é composta do nome do aplicativo de funções, mais *azurewebsites.net* ou seu domínio personalizado.
- **Caminho da pasta:** Para acessar funções não disparadas por HTTP por meio de uma solicitação HTTP, você precisa enviar a solicitação por meio das pastas *admin/Functions*.
- **Nome da função:** O nome da função que você deseja executar.

Use esse local de solicitação no postmaster junto com a chave mestra da função na solicitação ao Azure para executar a função.

> [!NOTE]
> Ao executar localmente, a chave mestra da função não é necessária. Você pode [chamar diretamente a função](#call-the-function) que omite o cabeçalho `x-functions-key`.

## <a name="get-the-functions-master-key"></a>Obter a chave mestra da função

Navegue até sua função na portal do Azure e clique na seção **gerenciar** e localize as **chaves de host** . Clique no botão **copiar** na linha *_master* para copiar a chave mestra para a área de transferência.

![Copiar chave mestra da tela de gerenciamento de função](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Depois de copiar a chave mestra, clique no nome da função para retornar à janela do arquivo de código. Em seguida, clique na guia **logs** . Você verá mensagens da função registradas aqui ao executar manualmente a função do postmaster.

> [!CAUTION]  
> Devido às permissões elevadas em seu aplicativo de funções concedidas pela chave mestra, você não deve compartilhar essa chave com terceiros ou distribuí-la em um aplicativo.

## <a name="call-the-function"></a>Chamar a função

Abra o postmaster e siga estas etapas:

1. Insira o **local da solicitação na caixa de texto URL**.
2. Verifique se o método HTTP está definido como **post**.
3. **Clique** na guia **cabeçalhos** .
4. Insira **x-Functions-Key** como a primeira **chave** e cole a chave mestra (da área de transferência) na caixa **valor** .
5. Insira **Content-Type** como a segunda **chave** e insira **Application/JSON** como o **valor**.

    ![Configurações de cabeçalhos do postmaster](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Clique** na guia **corpo** .
7. Insira **{"Input": "Test"}** como o corpo da solicitação.

    ![Configurações do corpo do postmaster](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Clique em **Enviar**.

    ![Enviando uma solicitação com o postmaster](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Em seguida, o postmaster relata um status de **202 aceito**.

Em seguida, retorne à sua função na portal do Azure. Localize a janela de *logs* e você verá mensagens provenientes da chamada manual para a função.

![Resultados do log de funções da chamada manual](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Passos seguintes

- [Estratégias para testar seu código no Azure Functions](./functions-test-a-function.md)
- [Depuração local do gatilho de grade de eventos do Azure function](./functions-debug-event-grid-trigger-local.md)
