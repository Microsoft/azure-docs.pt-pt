---
title: Tutorial - Importar um certificado no Cofre chave utilizando o portal Azure [ Microsoft Docs
description: Tutorial mostrando como importar um certificado no Cofre chave Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/03/2020
ms.author: sebansal
ms.openlocfilehash: 754f30f7931f9fad6a95328cbf8ab34f70cb75a0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423111"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Tutorial: Importar um certificado no Cofre chave Azure

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste tutorial, cria-se um cofre chave e depois usa-se para importar um certificado. Para obter mais informações sobre o Key Vault, reveja a [Descrição Geral](../general/overview.md).

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Importar um certificado no cofre chave usando o portal.
> * Importar um certificado no cofre chave usando CLI.


Antes de começar, leia [os conceitos básicos do Key Vault.](../general/basic-concepts.md) 

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

![Resultado após a conclusão da criação do Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importar um certificado para o Cofre-Chave

Para importar um certificado para o cofre, precisa de ter um ficheiro de certificado PEM ou PFX para estar em disco. Neste caso, importaremos um certificado com nome de ficheiro chamado **ExemploCertificado**.

> [!IMPORTANT]
> No Cofre de Chaves Azure, os formatos de certificado suportados são PFX e PEM. 
> - O formato de ficheiro .pem contém um ou mais ficheiros de certificados X509.
> - O formato de ficheiro .pfx é um formato de ficheiro de arquivo para armazenar vários objetos criptográficos num único ficheiro, ou seja, um certificado de servidor (emitido para o seu domínio), uma chave privada correspondente, e pode incluir opcionalmente um CA intermédio.  

1. Nas páginas de propriedades do Cofre-Chave, selecione **Certificados**.
2. Clique em **Gerar/Importar**.
3. No ecrã **Criar um certificado** escolha os seguintes valores:
    - **Método de Criação de Certificados**: Importação.
    - **Nome do certificado**: Certificado de exemplo.
    - **Upload Certificate File**: selecione o ficheiro de certificado a partir do disco
    - Deixe as outras opções com os valores predefinidos. Clique em **Criar**.

![Propriedades de certificado](../media/certificates/tutorial-import-cert/cert-import.png)

Assim que receber a mensagem de que o certificado foi importado com sucesso, pode clicar nele na lista. Em seguida, você pode ver algumas das suas propriedades. 

![Propriedades de certificado](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importar um certificado utilizando o Azure CLI

Importar um certificado num cofre de chaves especificado. Para importar um certificado válido existente, contendo uma chave privada, para o Cofre chave Azure, o ficheiro a importar pode estar em formato PFX ou PEM. Se o certificado estiver em formato PEM, o ficheiro PEM deve conter a chave, bem como os certificados x509. Esta operação requer os certificados/permissão de importação.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```
Saiba mais sobre os parâmetros [aqui](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import)

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um Cofre chave e importou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Ler mais sobre gestão de [certificados no Cofre chave Azure](/archive/blogs/kv/manage-certificates-via-azure-key-vault)
- Ver exemplos de certificados de [importação utilizando APIs REST](/rest/api/keyvault/importcertificate/importcertificate)
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)