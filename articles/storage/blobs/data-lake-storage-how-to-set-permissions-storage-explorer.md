---
title: Definir permissões de geração 2 de armazenamento do Data Lake com o Explorador de armazenamento do Azure
description: Isso como, saiba como definir permissões com o Explorador de armazenamento do Azure em arquivos e diretórios dentro de sua conta de armazenamento com capacidade de geração 2 de armazenamento do Azure Data Lake.
services: storage
author: roygara
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: d1e941e41871de4a11641254f40c53285b096807
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546316"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Definir permissões de nível de ficheiros e diretórios a utilizar o Explorador de armazenamento do Azure com a geração 2 de armazenamento do Azure Data Lake

Ficheiros armazenados na geração 2 de armazenamento do Azure Data Lake suportam permissões detalhadas e gestão de (ACL lista) do controlo de acesso. Em conjunto, tudo bem permissões granulares e gestão de ACL permitem-lhe gerir o acesso aos seus dados num nível muito granular.

Neste artigo, irá aprender a utilizar o Explorador de armazenamento do Azure para:

> [!div class="checklist"]
> * Definir as permissões ao nível do ficheiro
> * Definir permissões de nível de diretório
> * Adicionar utilizadores ou grupos a uma lista de controlo de acesso

## <a name="prerequisites"></a>Pré-requisitos

Para melhor retratar o processo, é necessário que concluir nossa [guia de introdução do Explorador de armazenamento do Azure](data-lake-storage-Explorer.md). Isto garante a que sua conta de armazenamento irá estar no estado mais apropriado (sistema de ficheiros criado e dados carregados para o mesmo).

## <a name="managing-access"></a>Gerir o acesso

Pode definir permissões na raiz do seu sistema de ficheiros. Para fazer isso, deve estar conectado a Explorador de armazenamento do Azure, com a sua conta individual com direitos para fazê-lo (e não com uma cadeia de ligação). O sistema de ficheiros com o botão direito e selecione **gerir permissões**, voltar a colocar ao **permissão Gerir** caixa de diálogo.

![Explorador de armazenamento do Microsoft Azure – gerir o acesso ao diretório](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

O **permissão Gerir** caixa de diálogo permite-lhe gerir permissões de proprietário e o grupo de proprietários. Ele também permite adicionar novos utilizadores e grupos para a lista de controlo de acesso para o qual, em seguida, pode alterar as permissões.

Para adicionar um novo utilizador ou grupo para a lista de controlo de acesso, selecione o **adicionar utilizador ou grupo** campo.

Introduza a entrada correspondente do Azure Active Directory (AAD) que pretende adicionar à lista e, em seguida, selecione **adicionar**.

O utilizador ou grupo aparecerão agora no **utilizadores e grupos:** campo, permitindo-lhe começar a gerir as respetivas permissões.

> [!NOTE]
> Ele é uma prática recomendada e recomendado, para criar um grupo de segurança no AAD e manter as permissões do grupo em vez de utilizadores individuais. Para obter detalhes sobre esta recomendação, bem como outras práticas recomendadas, consulte [melhores práticas para a geração 2 de armazenamento do Data Lake](data-lake-storage-best-practices.md).

Existem duas categorias de permissões, pode atribuir: aceder a ACL e ACLs padrão.

* **Acesso**: Acesso de controlo de ACLs de acesso a um objeto. Ficheiros e diretórios têm ACLs de acesso.

* **Predefinido**: Um modelo de ACLs associado a um diretório que determina o ACLs de acesso para todos os itens subordinados que são criadas nesse diretório. Ficheiros não têm ACLs padrão.

Dentro de nessas duas categorias, há três permissões, em seguida, pode atribuir em ficheiros ou diretórios: **Leia**, **escrever**, e **executar**.

>[!NOTE]
> Efetuar seleções aqui não definir permissões em qualquer item atualmente existente dentro do diretório. Tem de ir para cada item individual e definir as permissões manualmente, se o ficheiro já existe.

Pode gerir as permissões em diretórios individuais, bem como arquivos individuais, o que são o que lhe permite o controlo de acesso detalhada. O processo de gerenciamento de permissões para ficheiros e diretórios é igual à descrita acima. Com o botão direito do ficheiro ou diretório que pretende gerir as permissões no e siga o mesmo processo.

## <a name="next-steps"></a>Passos Seguintes

Este procedimento, ficou a saber como definir permissões em ficheiros e diretórios usando **Explorador de armazenamento do Azure**. Para obter mais informações sobre as ACLs, incluindo as ACLs padrão, acessar ACLs, seu comportamento e as respetivas permissões correspondentes, avance para o nosso artigo conceitual sobre o assunto.

> [!div class="nextstepaction"]
> [Access control in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md) (Controlo de acesso no Azure Data Lake Storage Gen2)
