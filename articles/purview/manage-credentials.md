---
title: Criar e gerir credenciais para digitalizações
description: Este artigo explica os passos para criar e gerir credenciais em Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c991559d550b351ce70bcc5834f96f313f856a82
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553446"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Credenciais para autenticação de origem em Azure Purview

Este artigo descreve como pode criar credenciais no Azure Purview para reutilizar e aplicar rapidamente informações de autenticação guardada nas suas análises de fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Cofre de chaves Azure. Se ainda não tem um, está aqui como (inserir link para o artigo de criação KV) para criar um.

## <a name="introduction"></a>Introdução
Uma Credencial é a informação de autenticação que o Azure Purview pode utilizar para autenticar nas suas fontes de dados registadas. Um objeto credencial pode ser criado para vários tipos de cenários de autenticação (como a Autenticação Básica que requer nome de utilizador/palavra-passe) e irá capturar as informações específicas necessárias com base no tipo escolhido de método de autenticação. As credenciais utilizam os seus segredos Azure Key Vaults existentes para recuperar informações de autenticação sensível durante o processo de criação de credenciais.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Usando a identidade gerida de Purview para configurar digitalizações
Se estiver a usar a identidade gerida pela Purview para configurar as sondagens, não terá de criar explicitamente uma credencial e ligar o cofre-chave ao Purview para os armazenar. Para obter instruções detalhadas sobre a adição da identidade gerida pela Alça deVisão para ter acesso a digitalizar as suas fontes de dados, consulte as secções de autenticação específicas da fonte de dados abaixo:

- [Armazenamento de Blobs do Azure](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Armazenamento do Azure Data Lake Ger1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan) (Armazenamento do Azure Data Lake Gen2)
- [Base de Dados SQL do Azure](register-scan-azure-sql-database.md)
- [Exemplo gerido pela base de dados Azure SQL](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Crie ligações Azure Key Vaults na sua conta Azure Purview

Antes de poder criar uma Credencial, terá primeiro de associar uma ou mais das suas instâncias existentes do Azure Key Vault à sua conta Azure Purview.

1. A partir do menu de navegação Azure Purview, navegue até ao Centro de Gestão e, em seguida, navegue até credenciais

2. A partir da barra de comando credenciais, selecione Gerir as ligações do Cofre de Chaves

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Gerir ligações AKV":::

3. Selecione +Novo do painel de ligações do Cofre de Chaves 

4. Forneça as informações necessárias e, em seguida, selecione Criar

5. Confirme que o seu Cofre chave foi associado com sucesso à sua conta Azure Purview

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Ver ligações AKV":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Conceda ao Purview acesso de identidade gerido ao seu Cofre chave Azure

Navegue para a sua chave de > políticas de acesso -> Adicionar Política de Acesso. Grant Obtenha permissão nas permissões secrets dropdown, e Select Principal para ser o MSI de Purview. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Adicionar MSI de Purview à AKV":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Adicionar política de acesso":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Salvar a política de acesso":::

## <a name="create-a-new-credential"></a>Criar uma nova credencial

Tipo credencial suportado em Purview hoje:
* Autenticação básica : Irá adicionar a **palavra-passe** como segredo no cofre de chaves
* Diretor de Serviço : Você adicionará a **chave principal** do serviço como um segredo no cofre chave 
* Autenticação SQL : Irá adicionar a **palavra-passe** como segredo no cofre de chaves
* Chave de conta : Irá adicionar a chave da **conta** como um segredo no cofre de chaves

Aqui está mais informações sobre como adicionar segredos a um cofre chave: (Insira o artigo do cofre de chaves)

Depois de guardar os seus segredos no seu cofre chave, crie a sua nova Credencial selecionando +New a partir da barra de comando de Credenciais. Forneça as informações necessárias, incluindo a seleção do método de autenticação e uma instância do Cofre de Chaves para selecionar um segredo. Uma vez preenchidos todos os detalhes, clique em criar.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nova credencial":::

Verifique se o seu novo Credencial aparece na vista da lista de credenciais e está pronto a usar

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Ver credencial":::

## <a name="manage-your-key-vault-connections"></a>Gerencie as suas ligações de cofre chave

1. Procurar/encontrar ligações key vault pelo nome

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Cofre de chaves de pesquisa":::

1. Eliminar uma ou mais ligações do Cofre de Chaves
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Apagar cofre de chaves":::

## <a name="manage-your-credentials"></a>Gerencie as suas credenciais

1. Pesquisar/encontrar Credenciais pelo nome
  
2. Selecione e faça atualizações para uma credencial existente

3. Apagar uma ou mais credenciais