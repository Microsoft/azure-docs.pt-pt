---
title: Azure Quickstart - Desconfiem e recupere uma chave do Key Vault utilizando o portal Azure | Microsoft Docs
description: Quickstart mostrando como definir e recuperar uma chave do Azure Key Vault usando o portal Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: 419fe72d400177ce9fa3d9811d7dfa06e74c0810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935058"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Quickstart: set and recuperar uma chave do Azure Key Vault usando o portal Azure

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste arranque rápido, cria-se um cofre chave e depois usa-se para armazenar uma chave. Para obter mais informações sobre o Key Vault, reveja a [Descrição Geral](../general/overview.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

1. A partir do menu do portal Azure, ou na página **Inicial,** selecione **Criar um recurso**.
2. Na caixa de busca, introduza **o Cofre de Chaves**.
3. Na lista de resultados, selecione **Key Vault**.
4. Na secção Key Vault, selecione **Criar**.
5. Na secção **Criar cofre de chaves**, forneça as seguintes informações:
    - **Nome**: é necessário um nome exclusivo. Para este arranque rápido, usamos **o Exemplo-Vault.** 
    - **Subscrição**: selecione uma subscrição.
    - No **Grupo de Recursos,** escolha **Criar novo** e insira um nome de grupo de recursos.
    - No menu pendente **Localização**, selecione uma localização.
    - Deixe as outras opções com os valores predefinidos.
6. Depois de fornecer as informações acima, selecione **Criar**.

Tome nota das duas propriedades listadas abaixo:

* **Nome do cofre**: No exemplo, este é **Exemplo-Vault**. Irá utilizar este nome para outros passos.
* **URI do Cofre**: no exemplo, isto é https://example-vault.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar as operações neste novo cofre.

![Resultado após a conclusão da criação do Key Vault](../media/keys/quick-create-portal/vault-properties.png)

## <a name="add-a-key-to-key-vault"></a>Adicione uma chave ao Cofre de Chaves

Para adicionar uma chave ao cofre, só precisa dar alguns passos adicionais. Neste caso, adicionamos uma chave que poderia ser usada por uma aplicação. A chave chama-se **ExemploKey.**

1. Nas páginas das propriedades do Cofre de Chaves, selecione **Keys**.
2. Clique em **Gerar/Importar**.
3. No Create um ecrã **de teclas** escolha os seguintes valores:
    - **Opções**: Gerar.
    - **Nome**: ExemploKey.
    - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.

Uma vez que recebe a mensagem de que a chave foi criada com sucesso, pode clicar nela na lista. Em seguida, pode ver algumas das propriedades. Se clicar na versão atual, pode ver o valor que especificou no passo anterior.

![Propriedades principais](../media/keys/quick-create-portal/current-version-hidden.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um Cofre-Chave e guardou uma chave nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-overview.md)