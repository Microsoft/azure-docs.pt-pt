---
title: Início Rápido do Azure - Definir e obter um segredo do Key Vault com o portal do Azure | Microsoft Docs
description: Início Rápido que mostra como definir e obter um segredo do Azure Key Vault com o portal do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 126df6e7f4d227c20c2173a1e2d4c0d7361b043f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962450"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Início Rápido: Definir e obter um segredo do Azure Key Vault com o portal do Azure

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste início rápido, vai criar um cofre de chaves e utilizá-lo para armazenar um segredo. Para obter mais informações sobre o Key Vault, reveja a [Descrição Geral](../general/overview.md).

Para mais informações sobre segredos, consulte (about-secrets.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

1. A partir do menu do portal Azure, ou na página **Inicial,** selecione **Criar um recurso**.
2. Na caixa Pesquisar, introduza **Key Vault**.
3. Na lista de resultados, selecione **Key Vault**.
4. Na secção Key Vault, selecione **Criar**.
5. Na secção **Criar cofre de chaves**, forneça as seguintes informações:
    - **Nome**: é necessário um nome exclusivo. Para este arranque rápido, **usamos Contoso-vault2.** 
    - **Subscrição**: selecione uma subscrição.
    - No **Grupo de Recursos,** escolha **Criar novo** e insira um nome de grupo de recursos.
    - No menu pendente **Localização**, selecione uma localização.
    - Deixe as outras opções com os valores predefinidos.
6. Depois de fornecer as informações acima, selecione **Criar**.

Tome nota das duas propriedades listadas abaixo:

* **Nome do Cofre**: no exemplo, o nome é **Contoso-Vault2**. Irá utilizar este nome para outros passos.
* **URI do Cofre**: no exemplo, isto é https://contoso-vault2.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Também pode criar o Key Vault com Azure CLI e PowerShell: Criar cofre de chaves utilizando o Cofre de Chaves [PowerShell](../general/quick-create-powershell.md) 
 [Create usando O Azure CLI](../general/quick-create-cli.md)

Nesta altura, a sua conta do Azure é a única autorizada a realizar as operações neste novo cofre.

![Resultado após a conclusão da criação do Key Vault](../media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, apenas tem de efetuar alguns passos adicionais. Neste caso, vamos adicionar uma palavra-passe que possa ser utilizada por uma aplicação. A palavra-passe chama-se **ExemploPassword** e armazenamos o valor de **hVFkk965BuUv** na sua.

1. Nas páginas das propriedades do Cofre-Chave, selecione **Secrets**.
2. Clique em **Gerar/Importar**.
3. No ecrã **Criar um segredo**, selecione os seguintes valores:
    - **Opções de carregamento**: Manual.
    - **Nome**: ExamplePassword.
    - **Valor**: hVFkk965BuUv
    - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.

Depois de receber a mensagem de que o segredo foi criado com êxito, pode clicar no mesmo na lista. 

## <a name="retrieve-a-secret-from-key-vault"></a>Recupere um segredo do Cofre de Chaves

Se clicar na versão atual, pode ver o valor que especificou no passo anterior.

![Propriedades do segredo](../media/quick-create-portal/current-version-hidden.png)

Ao clicar no botão "Mostrar Valor Secreto" no painel direito, pode ver o valor oculto. 

![O valor secreto apareceu](../media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um Cofre-Chave e guardaste um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Ler [Acesso seguro a um cofre de chaves](../general/secure-your-key-vault.md)
- Ver [utilizar o cofre de chaves com app Service Web App](../general/tutorial-net-create-vault-azure-web-app.md)
- Ver [Use Key Vault com aplicação implantada em VM](../general/tutorial-net-virtual-machine.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)
