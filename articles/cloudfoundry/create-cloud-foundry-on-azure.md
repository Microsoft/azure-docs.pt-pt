---
title: Crie um cluster de base de nuvens em Azure
description: Saiba como configurar os parâmetros necessários para fornecer um cluster Pivotal Cloud Foundry (PCF) em Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 735c0955a25a3995c94c73bd6471643ce2783df3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682618"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Crie um cluster de base de nuvens em Azure

Este tutorial fornece passos rápidos para criar e gerar os parâmetros necessários para fornecer um cluster Pivotal Cloud Foundry (PCF) em Azure. Para encontrar a solução Pivotal Cloud Foundry, realize uma pesquisa no Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Pesquisar AUry Cloud Em Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Gerar uma chave pública SSH

Existem várias formas de gerar uma chave de concha (SSH) segura do público usando Windows, Mac ou Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações, consulte [as teclas SSH com Windows on Azure](../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-service-principal"></a>Criar um principal de serviço

> [!NOTE]
>
> Para criar um principal de serviço, precisa de permissão de conta do proprietário. Também pode escrever um script para automatizar a criação do principal de serviço. Por exemplo, você pode usar o Azure CLI [ad sp create-for-rbac](/cli/azure/ad/sp).

1. Inicie sessão na sua conta do Azure.

    ```azurecli
    az login
    ```

    ![Login do Azure CLI](media/deploy/az-login-output.png )
 
    Copie o valor "id" como **iD da** sua assinatura, e copie o valor "tenantId" para usar mais tarde.

2. Defina a sua subscrição predefinida para esta configuração.

    ```azurecli
    az account set -s {id}
    ```

3. Crie uma aplicação Azure Ative Directory para o seu PCF. Especifique uma senha alfanumérica única. Guarde a palavra-passe como o seu **clienteSecret** para usar mais tarde.

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    Copie o valor "appId" na saída como o seu **clienteID** para usar mais tarde.

    > [!NOTE]
    >
    > Escolha a sua própria página inicial de aplicação e identifique URI, por exemplo, http \: //www \. contoso.com.

4. Crie um diretor de serviço com o seu novo ID da aplicação.

    ```azurecli
    az ad sp create --id {appId}
    ```

5. Defina a função de permissão do seu principal de serviço como um Contribuinte.

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    Ou também pode usar

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![Atribuição principal de função de serviço](media/deploy/svc-princ.png )

6. Verifique se pode iniciar sôm com sucesso no seu principal de serviço usando o ID da aplicação, senha e ID do inquilino.

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. Crie um ficheiro .json no seguinte formato. Utilize o **ID de assinatura,** **tenantID,** **clientID** e **clienteSssa** valores que copiou anteriormente. Guarde o ficheiro.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Obtenha o token da Rede Pivotal

1. Registe-se ou inscreva-se na sua conta [Rede Pivotal.](https://network.pivotal.io)
2. Selecione o nome do seu perfil no canto superior direito da página. Selecione **perfil de edição**.
3. Percorra a parte inferior da página e copie o valor **TOKEN DA API LEGACY.** Este valor é o seu valor **Pivotal Network Token** que utiliza mais tarde.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Provisionar o seu cluster Cloud Foundry em Azure

Agora tem todos os parâmetros necessários para a provisionar o seu [cluster Pivotal Cloud Foundry em Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)
Introduza os parâmetros e crie o seu cluster PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Verifique a implementação e inscreva-se no Diretor de Operações Pivotal

1. O seu cluster PCF mostra um estado de implantação.

    ![Estado de implantação do Azure](media/deploy/deployment.png )

2. Selecione o link **Implementações** na navegação à esquerda para obter credenciais para o seu PcF Ops Manager. Selecione o **Nome de Implantação** na página seguinte.
3. Na navegação à esquerda, selecione o link **Saídas** para exibir o URL, nome de utilizador e palavra-passe para o PcF Ops Manager. O valor "OPSMAN-FQDN" é o URL.
 
    ![Saída de implantação de Cloud Foundry](media/deploy/deploy-outputs.png )
 
4. Inicie o URL num navegador web. Introduza as credenciais do passo anterior para iniciar sinsus.

    ![Página de inscrição fundamental](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Se o navegador Internet Explorer falhar devido a uma mensagem de aviso "Site não seguro", selecione **Mais informações** e vá para a página web. Para o Firefox, selecione **Advance** e adicione a certificação para prosseguir.

5. O seu PcF Ops Manager exibe as instâncias Azure implementadas. Agora pode implementar e gerir as suas aplicações aqui.
               
    ![Exemplo de Azure implantado na Pivotal](media/deploy/ops-mgr.png )
