---
title: Colaboração
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS aplicações requerem um único proprietário e colaboradores opcionais que permite que várias pessoas criar uma única aplicação.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: 294905ccfd0ce8db6da8737277b0ce978ba837ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66473526"
---
# <a name="collaborating-with-other-authors"></a>Colaborar com outros autores

LUIS aplicações requerem um único proprietário e colaboradores opcionais que permite que várias pessoas criar uma única aplicação.

## <a name="luis-account"></a>Conta de LUIS
Uma conta de LUIS está associada um único [Microsoft Live](https://login.live.com/) conta. Cada conta de LUIS é dado um livre [chave de criação](luis-concept-keys.md#authoring-key) a utilizar para a criação de todas as aplicações de LUIS a conta tem acesso a. 

Uma conta de LUIS pode ter muitos aplicativos de LUIS.

Ver [utilizador de inquilino do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber mais sobre contas de utilizador do Active Directory. 

## <a name="luis-app-owner"></a>Proprietário da aplicação LUIS

A conta que cria uma aplicação é o proprietário e cada aplicação tem um único proprietário. O proprietário está listado na aplicação **[configurações](luis-how-to-collaborate.md)** página. O proprietário recebe e-mail quando a quota de ponto final atinge 75% do limite mensal. 

## <a name="authorization-roles"></a>Funções de autorização
LUIS não suporta funções diferentes para os proprietários e colaboradores com uma exceção. O proprietário é a única conta que pode eliminar a aplicação.

Se estiver interessado no controle de acesso ao modelo, considere dividir o modelo em aplicativos menores do LUIS, em que cada aplicação menor tem um conjunto limitado de mais de colaboradores. Uso [expedição](https://aka.ms/dispatch-tool) para permitir que um elemento principal aplicação LUIS gerir a coordenação entre os aplicativos principais e subordinados.

## <a name="transfer-ownership"></a>Transferir a propriedade
LUIS não fornece a transferência de propriedade, no entanto, qualquer funcionário pode exportar a aplicação e, em seguida, crie uma aplicação através da importação é. Lembre-se de que a nova aplicação tem um ID de aplicação diferente. A nova aplicação precisa ser preparado, publicado e o novo ponto final utilizado.

## <a name="luis-app-collaborators"></a>Colaboradores de aplicação LUIS
Um proprietário da aplicação pode adicionar os colaboradores a uma aplicação. O proprietário tem de adicionar o endereço de e-mail do funcionário na aplicação  **[configurações](luis-how-to-collaborate.md)** . O acesso do funcionário tem acesso total à aplicação. Se o acesso do funcionário elimina a aplicação, a aplicação é removida da conta do funcionário, mas continuará a ser a conta do proprietário. 

Se pretender partilhar várias aplicações com os colaboradores, cada aplicação tem de e-mail do colaborador adicionado. 

## <a name="managing-multiple-authors"></a>Gerir vários autores
O [LUIS](luis-reference-regions.md#luis-website) site atualmente não oferece a criação de ao nível da transação. Pode permitir que os autores funcione em versões independentes de uma versão base. Dois métodos diferentes são descritos nas seções a seguir.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Gerir facilmente várias versões na mesma aplicação
Comece por [clonagem](luis-how-to-manage-versions.md#clone-a-version), de uma versão de base, para cada autor. 

Cada autor faz alterações para a sua própria versão da aplicação. Assim que cada autor estiver satisfeito com o modelo, exporte as novas versões para ficheiros JSON.  

As aplicações exportadas são arquivos de formato JSON, que podem ser comparados para alterações. Combine os ficheiros para criar um único ficheiro JSON da nova versão. Alteração da **versionId** propriedade no JSON para indicar a nova versão intercalada. Importe essa versão para a aplicação original. 

Este método permite-lhe ter uma versão de Active Directory, uma versão de fase e uma versão publicada. É possível comparar os resultados no painel de teste interativo em três versões.

## <a name="manage-multiple-versions-as-apps"></a>Gerir facilmente várias versões das aplicações
[Exportar](luis-how-to-manage-versions.md#export-version) a versão da base. Cada autor importa a versão. A pessoa que importa a aplicação é o proprietário da versão. Quando são feitas modificar a aplicação, exportar a versão. 

As aplicações exportadas são arquivos de formato JSON, que podem ser comparados com a exportação de base para que as alterações. Combine os ficheiros para criar um único ficheiro JSON da nova versão. Alteração da **versionId** propriedade no JSON para indicar a nova versão intercalada. Importe essa versão para a aplicação original.

## <a name="collaborator-roles-vs-entity-roles"></a>Funções de entidade do vs de funções do funcionário

[Funções de entidade](luis-concept-roles.md) aplicam-se ao modelo de dados da aplicação LUIS. Funções do funcionário aplicam-se para níveis de acesso de criação. 

## <a name="next-steps"></a>Passos Seguintes

Compreender [controle de versão](luis-concept-version.md) conceitos. 

Ver [as definições da aplicação](luis-how-to-collaborate.md) para saber como gerir colaboradores na sua aplicação LUIS.

Ver [adicionar e-mail à lista de acesso](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342) com as APIs de criação.
