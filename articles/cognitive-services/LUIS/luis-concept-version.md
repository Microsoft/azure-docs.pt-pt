---
title: Controle de versão-LUIS
titleSuffix: Azure Cognitive Services
description: As versões, em LUIS, são semelhantes às versões na programação tradicional. Cada versão é um instantâneo no momento do aplicativo. Antes de fazer alterações no aplicativo, crie uma nova versão. É mais fácil voltar para o aplicativo exato e, em seguida, tentar desaparecer e a intenção do aplicativo e declarações-lo a um estado anterior.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2e13efa70d0344defeb306a92ac405439635e929
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619698"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Entenda como e quando usar uma versão LUIS

As versões, em LUIS, são semelhantes às versões na programação tradicional. Cada versão é um instantâneo no momento do aplicativo. Antes de fazer alterações no aplicativo, crie uma nova versão. É mais fácil voltar para a versão exata e, em seguida, tentar remover tentativas e declarações para um estado anterior.

Crie modelos diferentes do mesmo aplicativo com [versões](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>ID da versão
A ID da versão consiste em caracteres, dígitos ou '. ' e não pode ter mais de 10 caracteres.

## <a name="initial-version"></a>Versão inicial
A versão inicial (0,1) é a versão ativa padrão. 

## <a name="active-version"></a>Versão ativa
Para [definir uma versão](luis-how-to-manage-versions.md#set-active-version) como ativa significa que ela está atualmente editada e testada no site do [Luis](luis-reference-regions.md) . Defina uma versão como ativa para acessar seus dados, fazer atualizações, bem como testá-la e publicá-la.

O nome da versão ativa no momento é exibido no painel superior, esquerdo, após o nome do aplicativo. 

[![Alterar versão ativa](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versões e slots de publicação
Você publica nos slots do estágio e do produto. Cada slot pode ter uma versão diferente ou a mesma versão. Isso é útil para verificar as alterações entre as versões de modelo por meio do ponto de extremidade, que está disponível para bots ou outros aplicativos de chamada de LUIS. 

## <a name="clone-a-version"></a>Clonar uma versão
Clone uma versão para criar uma cópia de uma versão existente e salvá-la como uma nova versão. Clone uma versão para usar o mesmo conteúdo da versão existente como ponto de partida para a nova versão. Depois de clonar uma versão, a nova versão torna-se a versão **ativa** . 

## <a name="import-and-export-a-version"></a>Importar e exportar uma versão
Você pode importar uma versão no nível do aplicativo. Essa versão se torna a versão ativa e usou a ID da versão na propriedade "VersionId" do arquivo do aplicativo. Você também pode importar no nível de versão para um aplicativo existente. A nova versão se torna a versão ativa. 

Você pode exportar uma versão no nível do aplicativo ou pode exportar uma versão no nível da versão. A única diferença é que a versão exportada no nível do aplicativo é a versão ativa no momento no nível de versão, você pode escolher qualquer versão a ser exportada na página de **[configurações](luis-how-to-manage-versions.md)** . 

O arquivo exportado não contém informações aprendidas por computador porque o aplicativo é retreinado após ser importado. O arquivo exportado não contém colaboradores--você precisará adicioná-los novamente quando a versão for importada para o novo aplicativo.

## <a name="export-each-version-as-app-backup"></a>Exportar cada versão como backup de aplicativo
Para fazer backup do aplicativo LUIS, exporte cada versão na página **[configurações](luis-how-to-manage-versions.md)** .

## <a name="delete-a-version"></a>Excluir uma versão
Você pode excluir todas as versões, exceto a versão ativa da lista de versões na página Configurações. 

## <a name="version-availability-at-the-endpoint"></a>Disponibilidade de versão no ponto de extremidade
Versões treinadas não estão disponíveis automaticamente no [ponto de extremidade](luis-glossary.md#endpoint)do aplicativo. Você deve [publicar](luis-how-to-publish-app.md) ou republicar uma versão para que ela esteja disponível no ponto de extremidade do aplicativo. Você pode publicar para **preparo** e **produção**, fornecendo até duas versões do aplicativo disponíveis no ponto de extremidade. Se precisar de mais versões do aplicativo disponível em um ponto de extremidade, você deverá exportar a versão e reimportar para um novo aplicativo. O novo aplicativo tem uma ID de aplicativo diferente.

## <a name="collaborators"></a>Funcionários
O proprietário e todos os [colaboradores](luis-how-to-collaborate.md) têm acesso completo a todas as versões do aplicativo.

## <a name="next-steps"></a>Passos Seguintes

Consulte Como adicionar [controle de versão](luis-how-to-manage-versions.md) na página de configurações do aplicativo. 

Saiba como criar [tentativas](luis-concept-intent.md) no modelo.
