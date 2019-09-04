---
title: Início Rápido do Azure - Definir e obter um segredo do Key Vault com o portal do Azure | Microsoft Docs
description: Início Rápido que mostra como definir e obter um segredo do Azure Key Vault com o portal do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 3c0b9ad774d336da9bc60d2ef9f7ff2645602b6c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259225"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-the-azure-portal"></a>Início rápido: Definir e recuperar um segredo de Azure Key Vault usando o portal do Azure

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste início rápido, vai criar um cofre de chaves e utilizá-lo para armazenar um segredo. Para obter mais informações sobre o Key Vault, reveja a [Descrição Geral](key-vault-overview.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **Criar um recurso** no canto superior esquerdo do portal do Azure

    ![Resultado após a conclusão da criação do Key Vault](./media/quick-create-portal/search-services.png)
2. Na caixa Pesquisar, introduza **Key Vault**.
3. Na lista de resultados, selecione **Key Vault**.
4. Na secção Key Vault, selecione **Criar**.
5. Na secção **Criar cofre de chaves**, forneça as seguintes informações:
    - **Nome**: Um nome exclusivo é necessário. Para este início rápido, utilizamos **Contoso-vault2**. 
    - **Subscrição**: Escolha uma assinatura.
    - Em **Grupo de Recursos**, selecione **Criar novo** e introduza um nome de grupo de recursos.
    - No menu pendente **Localização**, selecione uma localização.
    - Deixe as outras opções com os valores predefinidos.
6. Depois de fornecer as informações acima, selecione **Criar**.

Tome nota das duas propriedades listadas abaixo:

* **Nome do cofre**: No exemplo, é **contoso-Vault2**. Irá utilizar este nome para outros passos.
* **URI do cofre**: No exemplo, isso é https://contoso-vault2.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar as operações neste novo cofre.

![Resultado após a conclusão da criação do Key Vault](./media/quick-create-portal/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, apenas tem de efetuar alguns passos adicionais. Neste caso, vamos adicionar uma palavra-passe que possa ser utilizada por uma aplicação. A senha é chamada de **ExamplePassword** e armazenamos o valor de **hVFkk965BuUv** nela.

1. Na página de propriedades do Key Vault, selecione **Segredos**.
2. Clique em **Gerar/Importar**.
3. No ecrã **Criar um segredo**, selecione os seguintes valores:
    - **Opções de upload**: Manual.
    - **Nome**: ExamplePassword.
    - **Value**: hVFkk965BuUv
    - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.

Depois de receber a mensagem de que o segredo foi criado com êxito, pode clicar no mesmo na lista. Em seguida, pode ver algumas das propriedades. Se clicar na versão atual, pode ver o valor que especificou no passo anterior.

![Propriedades do segredo](./media/quick-create-portal/current-version-hidden.png)

Ao clicar no botão "mostrar valor secreto" no painel direito, você pode ver o valor oculto. 

![O valor secreto apareceu](./media/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:** , escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre Key Vault e como integrá-lo a seus aplicativos, continue nos artigos abaixo.

- Leia uma [visão geral do Azure Key Vault](key-vault-overview.md)
- Consulte o [Guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Examinar [Azure Key Vault práticas recomendadas](key-vault-best-practices.md)