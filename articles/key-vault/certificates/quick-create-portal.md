---
title: Azure Quickstart - set and retrieve a certificate from Key Vault using Azure portal / Microsoft Docs
description: Quickstart mostrando como definir e recuperar um certificado do Azure Key Vault usando o portal Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: e55c0832638105ad681f74cbeb6429a6704b7fb2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935143"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>Quickstart: Definir e recuperar um certificado do Cofre de Chaves Azure usando o portal Azure

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste arranque rápido, cria-se um cofre chave e depois usa-o para guardar um certificado. Para obter mais informações sobre o Key Vault, reveja a [Descrição Geral](../general/overview.md).

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
* **URI do Cofre**: no exemplo, isto é `https://example-vault.vault.azure.net/`. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar as operações neste novo cofre.

![Resultado após a conclusão da criação do Key Vault](../media/certificates/quick-create-portal/vault-properties.png)

## <a name="add-a-certificate-to-key-vault"></a>Adicione um certificado ao Cofre de Chaves

Para adicionar um certificado ao cofre, só precisa dar alguns passos adicionais. Neste caso, adicionamos um certificado auto-assinado que poderia ser usado por uma aplicação. O certificado chama-se **ExemploCertificado.**

1. Nas páginas das propriedades do Cofre-Chave, selecione **Certificados**.
2. Clique em **Gerar/Importar**.
3. No **Ecrã de Certificado escolha** os seguintes valores:
    - **Método de Criação de Certificados**: Gerar.
    - **Denominação do certificado**: ExemploCertificado.
    - **Objeto**: CN=ExemploDomain
    - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.

Uma vez que recebe a mensagem de que o certificado foi criado com sucesso, pode clicar nele na lista. Em seguida, pode ver algumas das propriedades. Se clicar na versão atual, pode ver o valor que especificou no passo anterior.

![Propriedades de certificados](../media/certificates/quick-create-portal/current-version-hidden.png)

## <a name="export-certificate-from-key-vault"></a>Certificado de exportação do Cofre-Chave
Ao clicar no botão "Descarregar em formato CER" ou "Descarregar em formato PFX/PEM", pode descarregar o certificado. 

![Download de certificado](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um Cofre-Chave e guardou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-overview.md)
