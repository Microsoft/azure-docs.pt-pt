---
title: Criar um cluster de Pivotal Cloud Foundry no Azure
description: Saiba como configurar os parâmetros necessários para aprovisionar um cluster de Pivotal Cloud Foundry (PCF) no Azure
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
ms.openlocfilehash: ab3963ba8efc23d8f21c104361e0d3369ab5ea10
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772558"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Criar um cluster de Pivotal Cloud Foundry no Azure

Este tutorial fornece passos rápidos para criar e gerar os parâmetros que precisa de aprovisionar um cluster de Pivotal Cloud Foundry (PCF) no Azure. Para encontrar a solução de Cloud Foundry da Pivotal, efetua uma pesquisa do Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Pesquisa Pivotal Cloud Foundry no Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Gerar uma chave pública SSH

Existem várias formas de gerar uma chave pública de secure shell (SSH) através do Windows, Mac ou Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações, consulte [utilizar chaves SSH com Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

## <a name="create-a-service-principal"></a>Criar um principal de serviço

> [!NOTE]
>
> Para criar um principal de serviço, necessita de permissão de conta do proprietário. Também pode escrever um script para automatizar a criação do serviço principal. Por exemplo, pode utilizar a CLI do Azure [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Inicie sessão na sua conta do Azure.

    `az login`

    ![Início de sessão da CLI do Azure](media/deploy/az-login-output.png )
 
    Copie o valor de "id" como sua **ID de subscrição**e copie o valor de "tenantId" para utilizar mais tarde.

2. Defina a sua subscrição predefinida para esta configuração.

    `az account set -s {id}`

3. Crie uma aplicação do Azure Active Directory para o PCF. Especifique uma palavra-passe de alfanumérica exclusiva. Store a palavra-passe como sua **clientSecret** para utilizar mais tarde.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Copie o valor de "appId" na saída como sua **clientID** para utilizar mais tarde.

    > [!NOTE]
    >
    > Escolha o seu próprio home page da aplicação e o identificador de URI, por exemplo, http://www.contoso.com.

4. Criar um principal de serviço com o novo ID de aplicação.

    `az ad sp create --id {appId}`

5. Defina a função de permissão do seu principal de serviço como um contribuinte.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    Ou pode usar

    `az role assignment create --assignee {service-principal-name} --role “Contributor” `

    ![Atribuição de função principal de serviço](media/deploy/svc-princ.png )

6. Certifique-se de que conseguido iniciar sessão para o seu principal de serviço com o ID da aplicação, a palavra-passe e o ID do inquilino.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Crie um ficheiro. JSON no seguinte formato. Utilize o **ID de subscrição**, **tenantID**, **clientID**, e **clientSecret** valores que copiou anteriormente. Guarde o ficheiro.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Obtenha o token de rede da Pivotal

1. Registe-se ou iniciar sessão no seu [rede Pivotal](https://network.pivotal.io) conta.
2. Selecione o nome de perfil no canto superior direito da página. Selecione **Editar perfil**.
3. Desloque-se para a parte inferior da página e copiar o **TOKEN de API de LEGADO** valor. Este valor é sua **Pivotal rede Token** valor que irá utilizar mais tarde.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Aprovisionar o seu cluster do Cloud Foundry no Azure

Agora tem todos os parâmetros, terá de aprovisionar sua [cluster Pivotal Cloud Foundry no Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Introduza os parâmetros e criar o cluster PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Verificar a implementação e, inicie sessão para o Ops Manager Pivotal

1. O cluster PCF mostra um Estado de implementação.

    ![Estado de implementação do Azure](media/deploy/deployment.png )

2. Selecione o **implementações** ligação no painel de navegação à esquerda para obter as credenciais para o PCF Ops Manager. Selecione o **nome da implementação** na página seguinte.
3. No painel de navegação à esquerda, selecione o **saídas** link para exibir o URL, nome de utilizador e palavra-passe para o PCF Ops Manager. O valor de "OPSMAN-FQDN" é o URL.
 
    ![Saída de implementação de Foundry da cloud](media/deploy/deploy-outputs.png )
 
4. Inicie o URL de um navegador da web. Introduza as credenciais do passo anterior para iniciar sessão.

    ![Página de início de sessão Pivotal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Se o navegador Internet Explorer falhar devido a uma mensagem de aviso de "Site não seguro", selecione **mais informações** e aceda à página Web. Para o Firefox, selecione **avanço** e adicione a certificação para continuar.

5. O PCF Ops Manager apresenta as instâncias do Azure implementadas. Agora, pode implementar e gerir as suas aplicações aqui.
               
    ![Instância do Azure implementada na Pivotal](media/deploy/ops-mgr.png )
 
