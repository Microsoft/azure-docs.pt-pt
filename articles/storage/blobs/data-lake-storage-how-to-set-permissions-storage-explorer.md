---
title: Definir permissões para Data Lake Storage Gen2 com Gerenciador de Armazenamento do Azure
description: Neste "como", você aprende como definir permissões com Gerenciador de Armazenamento do Azure em arquivos e diretórios dentro de sua conta de armazenamento com capacidade de Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 2cc93ad9ae1d9d6d21064a2a80c3d62bf661dabc
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992284"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Definir permissões de nível de arquivo e diretório usando Gerenciador de Armazenamento do Azure com Azure Data Lake Storage Gen2

Os arquivos armazenados no Azure Data Lake Storage Gen2 dão suporte a permissões refinadas e gerenciamento de ACL (lista de controle de acesso). Juntas, as permissões refinadas e o gerenciamento de ACL permitem que você gerencie o acesso aos seus dados em um nível muito granular.

Neste artigo, você aprenderá a usar o Gerenciador de Armazenamento do Azure para:

> [!div class="checklist"]
> * Definir permissões de nível de arquivo
> * Definir permissões de nível de diretório
> * Adicionar usuários ou grupos a uma lista de controle de acesso

## <a name="prerequisites"></a>Pré-requisitos

Para descrever melhor o processo, exigimos que você conclua nosso [Gerenciador de armazenamento do Azure início rápido](data-lake-storage-Explorer.md). Isso garante que sua conta de armazenamento estará no estado mais apropriado (contêiner criado e dados carregados nele).

## <a name="managing-access"></a>Gerir o acesso

Você pode definir permissões na raiz do seu contêiner. Para fazer isso, você deve estar conectado ao Gerenciador de Armazenamento do Azure com sua conta individual com direitos para fazer isso (em oposição a uma cadeia de conexão). Clique com o botão direito do mouse no contêiner e selecione **gerenciar permissões**, trazendo a caixa de diálogo **gerenciar permissão** .

![Gerenciador de Armazenamento do Microsoft Azure-gerenciar o acesso ao diretório](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

A caixa de diálogo **gerenciar permissão** permite que você gerencie permissões para o proprietário e o grupo proprietários. Ele também permite que você adicione novos usuários e grupos à lista de controle de acesso para a qual você pode gerenciar permissões.

Para adicionar um novo usuário ou grupo à lista controle de acesso, selecione o campo **Adicionar usuário ou grupo** .

Insira a entrada Azure Active Directory (AAD) correspondente que você deseja adicionar à lista e, em seguida, selecione **Adicionar**.

O usuário ou grupo será exibido agora no campo **usuários e grupos:** , permitindo que você comece a gerenciar suas permissões.

> [!NOTE]
> É uma prática recomendada e é recomendada para criar um grupo de segurança no AAD e manter permissões no grupo em vez de usuários individuais. Para obter detalhes sobre essa recomendação, bem como outras práticas recomendadas, consulte [práticas recomendadas para data Lake Storage Gen2](data-lake-storage-best-practices.md).

Há duas categorias de permissões que você pode atribuir: ACLs de acesso e ACLs padrão.

* **Acesso**: As ACLs de acesso controlam o acesso a um objeto. Os arquivos e diretórios têm ACLs de acesso.

* **Padrão**: Um modelo de ACLs associado a um diretório que determina as ACLs de acesso para todos os itens filho que são criados nesse diretório. Os arquivos não têm ACLs padrão.

Em ambas as categorias, há três permissões que você pode atribuir em arquivos ou diretórios: **Ler**, **gravar**e **executar**.

>[!NOTE]
> Fazer seleções aqui não definirá permissões em qualquer item existente no momento dentro do diretório. Você deve ir para cada item individual e definir as permissões manualmente, se o arquivo já existir.

Você pode gerenciar permissões em diretórios individuais, bem como arquivos individuais, que são o que permite o controle de acesso refinado. O processo de gerenciamento de permissões para diretórios e arquivos é o mesmo descrito acima. Clique com o botão direito do mouse no arquivo ou diretório para o qual você deseja gerenciar permissões e siga o mesmo processo.

## <a name="next-steps"></a>Passos Seguintes

Neste "como", você aprendeu como definir permissões em arquivos e diretórios usando **Gerenciador de armazenamento do Azure**. Para saber mais sobre ACLs, incluindo ACLs padrão, acessar ACLs, seu comportamento e suas permissões correspondentes, vá para nosso artigo conceitual sobre o assunto.

> [!div class="nextstepaction"]
> [Access control in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md) (Controlo de acesso no Azure Data Lake Storage Gen2)
