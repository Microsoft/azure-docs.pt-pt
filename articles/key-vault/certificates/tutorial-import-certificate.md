---
title: Tutorial - Importe um certificado em Key Vault usando o portal Azure | Microsoft Docs
description: Tutorial mostrando como importar um certificado em Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: b7b15c1c4952f6e0a606b1ed46f1588c536b4ae2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204087"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Tutorial: Importar um certificado no Cofre da Chave Azure

O Azure Key Vault é um serviço cloud que funciona como um arquivo de segredos seguro. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste tutorial, você cria um cofre chave, em seguida, usá-lo para importar um certificado. Para obter mais informações sobre o Key Vault, reveja a [Descrição Geral](../general/overview.md).

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Importe um certificado em Key Vault usando o portal.
> * Importe um certificado em Key Vault usando o CLI.
> * Importe um certificado em Key Vault usando PowerShell.


Antes de começar, leia [os conceitos básicos do Key Vault.](../general/basic-concepts.md) 

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

![Resultado após a conclusão da criação do Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importar um certificado para o Cofre-Chave

Para importar um certificado para o cofre, é necessário ter um ficheiro de certificado PEM ou PFX para estar no disco. Neste caso, importaremos um certificado com nome de ficheiro chamado **ExemploCertificado.**

> [!IMPORTANT]
> No Azure Key Vault, os formatos de certificado suportados são PFX e PEM. 
> - O formato de ficheiro .pem contém um ou mais ficheiros de certificadoS X509.
> - .pfx file format é um formato de arquivo de arquivo para armazenar vários objetos criptográficos num único ficheiro, ou seja, certificado de servidor (emitido para o seu domínio), uma chave privada correspondente, e pode opcionalmente incluir um CA intermédio.  

1. Nas páginas das propriedades do Cofre-Chave, selecione **Certificados**.
2. Clique em **Gerar/Importar**.
3. No **Ecrã de Certificado escolha** os seguintes valores:
    - **Método de Criação de Certificados**: Importação.
    - **Denominação do certificado**: ExemploCertificado.
    - **Upload Certificate File**: selecione o ficheiro de certificado do disco
    - **Senha** : Se estiver a carregar um ficheiro de certificado protegido por palavra-passe, forneça esta palavra-passe aqui. Caso contrário, deixe-o em branco. Uma vez que o ficheiro de certificado é importado com sucesso, o cofre-chave removerá essa palavra-passe.
4. Clique em **Criar**.

![Propriedades de certificados](../media/certificates/tutorial-import-cert/cert-import.png)

Ao adicionar um certificado utilizando o método **de importação,** o cofre Azure Key preencherá automaticamente os parâmetros dos certificados (isto é, período de validade, nome emitente, data de ativação, etc.).

Assim que receber a mensagem de que o certificado foi importado com sucesso, poderá clicar nele na lista para ver as suas propriedades. 

![Screenshot que mostra onde ver as propriedades do certificado.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importar um certificado utilizando o Azure CLI

Importe um certificado num cofre de chaves especificado. Para importar um certificado válido existente, contendo uma chave privada, para o Cofre da Chave Azure, o ficheiro a importar pode ser em formato PFX ou PEM. Se o certificado estiver em formato PEM, o ficheiro PEM deve conter a chave, bem como os certificados x509. Esta operação requer a permissão certificados/importar.

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

Saiba mais sobre os [parâmetros.](/cli/azure/keyvault/certificate#az-keyvault-certificate-import)

Depois de importar o certificado, pode ver o certificado usando [o Certificado](/cli/azure/keyvault/certificate#az-keyvault-certificate-show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Criou um cofre-chave, importou um certificado e viu as propriedades do Certificado.

## <a name="import-a-certificate-using-azure-powershell"></a>Importar um certificado usando a Azure PowerShell

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

Saiba mais sobre os [parâmetros.](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)


## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais do Key Vault têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.
Quando já não for necessário, elimine o grupo de recursos, que elimina o Key Vault e todos os recursos relacionados. Para eliminar o grupo de recursos através do portal:

1. O nome do grupo de recursos na caixa Pesquisar, na parte superior do portal. Quando vir o grupo de recursos utilizado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Na caixa **ESCREVA O NOME DO GRUPO DE RECURSOS:**, escreva o nome do grupo de recursos e selecione **Eliminar**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um Cofre-Chave e importou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia mais sobre [a criação de certificados de gestão no Cofre chave Azure](./create-certificate-scenarios.md)
- Ver exemplos de [certificados de importação usando APIs rest](/rest/api/keyvault/importcertificate/importcertificate)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-overview.md)