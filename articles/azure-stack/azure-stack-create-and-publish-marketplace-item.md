---
title: Criar e publicar um item do Marketplace no Azure Stack | Documentos da Microsoft
description: Criar e publicar um item do Marketplace no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 81f06e0f5d5201b902504d8275f356f9a1731065
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098904"
---
# <a name="create-and-publish-a-marketplace-item"></a>Criar e publicar itens do Marketplace

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="create-a-marketplace-item"></a>Criar um item do Mercado

1. Transfira o [ferramenta de Gerenciador de galeria do Azure](https://www.aka.ms/azurestackmarketplaceitem) e o item do Marketplace de pilha do Azure de exemplo.
2. Abra o item do Marketplace de exemplo e mudar o nome da **SimpleVMTemplate** pasta. Utilize o mesmo nome que o item do Marketplace; Por exemplo, **Contoso.TodoList**. Esta pasta contém:

   ```shell
   /Contoso.TodoList/
   /Contoso.TodoList/Manifest.json
   /Contoso.TodoList/UIDefinition.json
   /Contoso.TodoList/Icons/
   /Contoso.TodoList/Strings/
   /Contoso.TodoList/DeploymentTemplates/
   ```

3. [Criar um modelo Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) ou escolha um modelo a partir do GitHub. O item do Marketplace utiliza este modelo para criar um recurso.

    > [!Note]  
    > Nunca muito código segredos, como chaves de produto, a palavra-passe ou quaisquer informações de identificação de cliente no modelo do Azure Resource Manager. Ficheiros JSON do modelo estão acessíveis, sem a necessidade de autenticação, depois de publicado na galeria. Store todos os segredos no [Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) e chamá-las de dentro do modelo.

4. Para garantir que o recurso pode ser implementado com êxito, teste o modelo com as APIs do Microsoft Azure Stack.
5. Se o seu modelo se baseia numa imagem de máquina virtual, siga as instruções para [adicionar uma imagem de máquina virtual para o Azure Stack](azure-stack-add-vm-image.md).
6. Guardar o modelo Azure Resource Manager nos **/Contoso.TodoList/DeploymentTemplates/** pasta.
7. Escolha os ícones e texto para o item do Marketplace. Adicionar ícones para o **ícones** pasta e adicione o texto para o **recursos** de ficheiros a **cadeias de caracteres** pasta. Utilize o **pequeno**, **médio**, **grandes**, e **ampla** Convenção de nomenclatura para os ícones. Consulte a [referência de interface do Usuário do item do Marketplace](#reference-marketplace-item-ui) para uma descrição detalhada dos seguintes tamanhos.

   > [!NOTE]
   > Todas as dimensões de ícone quatro (pequena, média, grande, wide) são necessárias para criar o item do Marketplace corretamente.
   >
   >
8. Na **Manifest.json** de ficheiros, alterar **nome** ao nome do seu item do Marketplace. Também alterar **publicador** para o seu nome ou da empresa.
9. Sob **artefactos**, altere **nome** e **caminho** para as informações corretas para o modelo Azure Resource Manager que incluiu:

   ```json
   "artifacts": [
      {
          "name": "Your template name",
          "type": "Template",
          "path": "DeploymentTemplates\\your path",
          "isDefault": true
      }
   ```

10. Substitua **meus itens do Marketplace** com uma lista das categorias em que deve aparecer o item do Marketplace:

    ```json
    "categories":[
    "My Marketplace Items"
    ],
    ```

11. Para as edições adicionais para Manifest.json, consulte [referência: Manifest.json de item do Marketplace](#reference-marketplace-item-manifestjson).

12. Para empacotar as pastas num arquivo de .azpkg, abra uma linha de comandos e execute o seguinte comando:

    ```shell
    AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    ```

    > [!NOTE]
    > O caminho completo para o pacote de saída têm de existir. Por exemplo, se o caminho de saída for C:\MarketPlaceItem\yourpackage.azpkg, a pasta C:\MarketPlaceItem tem de existir.
    >
    >

## <a name="publish-a-marketplace-item"></a>Publicar um item do Mercado

1. Utilize o PowerShell ou o Explorador de armazenamento do Azure para carregar o item do Marketplace (.azpkg) para o armazenamento de Blobs do Azure. Pode carregar para o armazenamento do Azure Stack local ou carregar para o armazenamento do Azure; que é uma localização temporária para o pacote. Certifique-se de que o blob está acessível ao público.
2. A máquina virtual do cliente no ambiente do Microsoft Azure Stack, certifique-se de que a sessão do PowerShell está configurada com as suas credenciais de administrador de serviço. Pode encontrar instruções sobre como autenticar PowerShell no Azure Stack na [implementar um modelo com o PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Quando utiliza [PowerShell 1.3.0](azure-stack-powershell-install.md) ou posterior, pode utilizar os **Add-AzsGalleryItem** cmdlet do PowerShell para publicar o item do Marketplace no Azure Stack. Antes de utilizar o PowerShell 1.3.0, utilize o cmdlet **Add-AzureRMGalleryitem** em vez de **Add-AzsGalleryItem**. Por exemplo, quando utiliza o PowerShell 1.3.0 ou posterior:

   ```powershell
   Add-AzsGalleryItem -GalleryItemUri `
   https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   ```

   | Parâmetro | Descrição |
   | --- | --- |
   | SubscriptionID |ID de subscrição de administrador. Pode recuperá-la com o PowerShell. Se desejar para obtê-la no portal, vá para a subscrição do fornecedor e copie o ID de subscrição. |
   | GalleryItemUri |URI de blob para o seu pacote de galeria que já foi carregada para o armazenamento. |
   | Apiversion |Defina como **2015-04-01**. |

4. Aceda ao portal. Agora, pode ver o item do Marketplace no portal, como um operador ou como um utilizador. O pacote pode demorar alguns minutos a aparecer.

5. O item do Marketplace agora foi guardado no Azure Stack Marketplace. Pode optar por eliminá-lo do seu local de armazenamento de Blobs.
    > [!Caution]  
    > Todos os artefactos de galeria padrão e artefactos da sua galeria personalizada agora estão disponíveis sem autenticação sob os seguintes URLs:  
`https://adminportal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://portal.[Region].[external FQDN]:30015/artifact/20161101/[Template Name]/DeploymentTemplates/Template.json`
`https://systemgallery.blob.[Region].[external FQDN]/dev20161101-microsoft-windowsazure-gallery/[Template Name]/UiDefinition.json`

6. Pode remover um item do mercado, utilizando o **Remove-AzureRMGalleryItem** cmdlet. Por exemplo:

   ```powershell
   Remove-AzsGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   ```

   > [!NOTE]
   > A interface do Usuário do Marketplace pode mostrar um erro depois de remover um item. Para corrigir o erro, clique em **definições** no portal. Em seguida, selecione **rejeitar modificações** sob **personalização do Portal**.
   >
   >

## <a name="reference-marketplace-item-manifestjson"></a>Referência: Manifest.json de item do Marketplace

### <a name="identity-information"></a>Informações de identidade

| Name | Necessário | Type | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| Name |X |String |[A-Za-z0-9]+ | |
| Publicador |X |String |[A-Za-z0-9]+ | |
| Versão |X |String |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadados

| Name | Necessário | Type | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| Nome a Apresentar |X |String |Recomendação de 80 carateres |O portal pode não apresentar o nome do item corretamente se for mais de 80 carateres. |
| PublisherDisplayName |X |String |Recomendação de 30 carateres |O portal pode não apresentar o nome do publicador corretamente se ele tiver mais de 30 carateres. |
| PublisherLegalName |X |String |Máximo de 256 carateres | |
| Resumo |X |String |60 e 100 carateres | |
| LongSummary |X |String |140 para 256 carateres |Não ainda aplicável no Azure Stack. |
| Descrição |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 a 5.000 caracteres | |

### <a name="images"></a>Imagens

O Marketplace utiliza os ícones seguintes:

| Name | Largura | Altura | Notas |
| --- | --- | --- | --- |
| Ampla |255 px |115 px |Sempre necessária |
| Grande |115 px |115 px |Sempre necessária |
| Médio |90 px |90 px |Sempre necessária |
| Pequeno |40 px |40 px |Sempre necessária |
| captura de ecrã |533 px |32 px |Opcional |

### <a name="categories"></a>Categorias

Cada item do Marketplace deve ser etiquetado com uma categoria que identifica onde o item é apresentado no portal da interface do Usuário. Pode escolher uma das categorias existentes no Azure Stack (computação, dados + armazenamento, etc.) ou escolher uma nova.

### <a name="links"></a>Ligações

Cada item do Marketplace pode incluir várias ligações para conteúdo adicional. As ligações são especificadas como uma lista de nomes e URIs:

| Name | Necessário | Type | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| Nome a Apresentar |X |String |Máximo de 64 carateres | |
| URI |X |URI | | |

### <a name="additional-properties"></a>Propriedades adicionais

Além dos metadados anterior, os autores de Marketplace podem fornecer dados de par chave/valor personalizado no seguinte formato:

| Name | Necessário | Type | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| Nome a Apresentar |X |String |Máximo de 25 caracteres | |
| Value |X |String |Máximo de 30 carateres | |

### <a name="html-sanitization"></a>Limpeza de HTML

Para qualquer campo que permite que o HTML, são permitidos os seguintes elementos e atributos:

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Referência: Item do Marketplace da interface do Usuário

Ícones e do texto para itens do Marketplace, como visto no portal do Azure Stack são as seguintes.

### <a name="create-blade"></a>Criar painel

![Criar painel](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Painel de detalhes de item do Marketplace

![Painel de detalhes de item do Marketplace](media/azure-stack-create-and-publish-marketplace-item/image3.png)
