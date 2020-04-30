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
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 9496173ee006c6ca3cab557f4e63ec21647ad0fd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82105578"
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
    - **Palavra-passe** : Se estiver a carregar um ficheiro de certificado protegido por palavra-passe, forneça essa palavra-passe aqui. Caso contrário, deixe-o em branco. Uma vez importado com sucesso o ficheiro do certificado, o cofre da chave removerá essa senha.
4. Clique em **Criar**.

![Propriedades de certificado](../media/certificates/tutorial-import-cert/cert-import.png)

Ao adicionar um certificado utilizando o método **de importação,** o cofre da Chave Azure preencherá automaticamente os parâmetros do certificado (isto é, período de validade, nome emitente, data de ativação, etc.).

Assim que receber a mensagem de que o certificado foi importado com sucesso, pode clicar nele na lista para visualizar as suas propriedades. 

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

Depois de importar o certificado, pode ver o certificado usando [certificado show](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```



Criou um cofre chave, importou um certificado e viu as propriedades do Certificado.

## <a name="clean-up-resources"></a>Limpar recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um Cofre chave e importou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Ler mais sobre [gestão da criação de certificados no Cofre chave azure](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Ver exemplos de certificados de [importação utilizando APIs REST](/rest/api/keyvault/importcertificate/importcertificate)
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)