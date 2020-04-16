---
title: Azure Quickstart - Definir e recuperar um certificado do Key Vault usando o portal Azure [ Microsoft Docs
description: Quickstart mostrando como definir e recuperar um certificado do Cofre chave Azure usando o portal Azure
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
ms.openlocfilehash: b0d6221aaafe3ade70bc23ce4196a7b53c9474c5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424721"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>Quickstart: Definir e recuperar um certificado do Cofre chave Azure usando o portal Azure

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste arranque rápido, cria-se um cofre chave e depois usa-o para armazenar um certificado. Para obter mais informações sobre o Key Vault, reveja a [Descrição Geral](../general/overview.md).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

1. A partir do menu do portal Azure, ou da página **Inicial,** selecione **Criar um recurso**.
2. Na caixa Pesquisar, introduza **Key Vault**.
3. Na lista de resultados, selecione **Key Vault**.
4. Na secção Key Vault, selecione **Criar**.
5. Na secção **Criar cofre de chaves**, forneça as seguintes informações:
    - **Nome**: é necessário um nome exclusivo. Para este arranque rápido, usamos **O Exemplo-Vault.** 
    - **Subscrição**: selecione uma subscrição.
    - No **Grupo de Recursos,** escolha **Criar novos** e insira um nome de grupo de recursos.
    - No menu pendente **Localização**, selecione uma localização.
    - Deixe as outras opções com os valores predefinidos.
6. Depois de fornecer as informações acima, selecione **Criar**.

Tome nota das duas propriedades listadas abaixo:

* **Nome do cofre**: No exemplo, este é **exemplo-cofre**. Irá utilizar este nome para outros passos.
* **URI do Cofre**: no exemplo, isto é https://example-vault.vault.azure.net/. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

Nesta altura, a sua conta do Azure é a única autorizada a realizar as operações neste novo cofre.

![Resultado após a conclusão da criação do Key Vault](../media/certificates/quick-create-portal/vault-properties.png)

## <a name="add-a-certificate-to-key-vault"></a>Adicione um certificado ao Cofre chave

Para adicionar um certificado ao cofre, só precisa de dar mais alguns passos. Neste caso, adicionamos um certificado auto-assinado que poderia ser usado por um pedido. O certificado chama-se **ExemploCertificado**.

1. Nas páginas de propriedades do Cofre-Chave, selecione **Certificados**.
2. Clique em **Gerar/Importar**.
3. No ecrã **Criar um certificado** escolha os seguintes valores:
    - **Método de Criação de Certificado**: Gerar.
    - **Nome do certificado**: Certificado de exemplo.
    - **Objeto**: CN=ExampleDomínio
    - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.

Assim que receber a mensagem de que o certificado foi criado com sucesso, pode clicar nele na lista. Em seguida, pode ver algumas das propriedades. Se clicar na versão atual, pode ver o valor que especificou no passo anterior.

![Propriedades de certificado](../media/certificates/quick-create-portal/current-version-hidden.png)

Ao clicar no botão "Baixar em formato CER" ou "Baixar em formato PFX/PEM", pode dowloadar o certificado. 

![Download de certificado](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um Cofre chave e guardaste um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do Cofre chave Azure](../general/overview.md)
- Consulte o guia do desenvolvedor do Cofre de [Chaves Azure](../general/developers-guide.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)