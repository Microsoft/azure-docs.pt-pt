---
title: Utilize revisões para fazer alterações não quebrando com segurança na Gestão da API
titleSuffix: Azure API Management
description: Siga os passos deste tutorial para aprender a efetuar alterações sem interrupções com revisões na Gestão de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a4655b20fabcc72e02037de05dd0ef7c4671e52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254916"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Utilizar revisões para efetuar alterações sem interrupções de forma segura
Quando a API está pronta e começa a ser utilizada pelos programadores, poderá ter de fazer alterações a essa API e ao mesmo tempo não interromper os autores de chamadas da sua API. É também útil dar a conhecer aos programadores as alterações que efetuou. É possível fazê-lo na Gestão de API do Azure com **revisões**. Para obter mais informações, veja [Versões e revisões](https://azure.microsoft.com/blog/versions-revisions/) e [Controlo de versões de API com a Gestão de API do Azure](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Efetuar alterações sem interrupções à revisão
> * Tornar a revisão atual e adicionar uma entrada de registo de alterações
> * Navegar até ao portal do programador para ver as alterações e o registo de alterações

![Registo de Alterações no Portal do Programador](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Adicionar uma nova revisão

![Adicionar a revisão de API](media/api-management-getstarted-revise-api/07-AddRevisions-01-AddNewRevision.png)

1. Selecione a página **APIs**.
2. Selecione **Demo Conference API** na lista de APIs (ou outra API à qual quer adicionar revisões).
3. Clique no separador **Revisões** no menu junto à parte superior da página.
4. Selecione **+ Adicionar Revisão**

    > [!TIP]
    > Também pode selecionar **Adicionar Revisão** no menu de contexto (**...**) da API.

5. Forneça uma descrição da nova revisão, para ajudar a lembrar para que será utilizada.
6. Selecione **Criar**
7. A sua nova revisão está agora criada.

    > [!NOTE]
    > A API original permanece em **Revisão 1**. Trata-se da revisão que os utilizadores continuam a chamar, até optar por tornar uma revisão diferente atual.

## <a name="make-non-breaking-changes-to-your-revision"></a>Efetuar alterações sem interrupções à revisão

![Modificar a revisão](media/api-management-getstarted-revise-api/07-AddRevisions-02-MakeChanges.png)

1. Selecione **Demo Conference API** na lista de APIs.
2. Selecione o separador **Estrutura** junto à parte superior do ecrã.
3. Tenha em atenção que o **seletor de revisões** (diretamente acima do separador de estrutura) mostra a **Revisão 2** como atualmente selecionada.

    > [!TIP]
    > Utilize o seletor de revisões para alternar entre revisões em que quer trabalhar.

4. Selecione **+ Adicionar Operação**.
5. Defina a nova operação como **POST**, o Nome, o Nome a Apresentar e o URL da operação como **teste**.
6. **Guarde** a nova operação.
7. Efetuámos agora uma alteração a **Revisão 2**. Utilize o **Seletor de Revisões** junto à parte superior da página para voltar a **Revisão 1**.
8. Tenha em atenção que a nova operação não aparece em **Revisão 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Tornar a revisão atual e adicionar uma entrada de registo de alterações

1. Selecione o separador **Revisões** no menu junto à parte superior da página.

    ![O menu de revisão no ecrã de revisão.](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)

2. Abra o menu de contexto (**...**) para **Revisão 2**.
3. Selecione **Tornar Atual**.
4. Selecione **Publicar no Registo de Alterações Públicas para esta API**, se quiser publicar notas sobre esta alteração. Forneça uma descrição para a sua mudança que os desenvolvedores vêem, por exemplo: **Testar revisões. Adicionou uma nova operação de "teste".**
5. **Revisão 2** é agora atual.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Navegar até ao portal do programador para ver as alterações e o registo de alterações

1. No portal Azure, selecione **APIs**.
2. Selecione **Portal do Programador** no menu superior.
3. Selecione **APIs** e, em seguida, selecione **Demo Conference API**.
4. Tenha em atenção que a nova operação **test** está agora disponível.
5. Clique em **Changelog** perto do nome API.
6. Tenha em atenção que a entrada do registo de alterações é apresentada nesta lista.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Efetuar alterações sem interrupções à revisão
> * Tornar a revisão atual e adicionar uma entrada de registo de alterações
> * Navegar até ao portal do programador para ver as alterações e o registo de alterações

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Publicar várias versões da sua API](api-management-get-started-publish-versions.md)
