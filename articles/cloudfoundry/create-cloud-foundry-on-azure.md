---
title: Criar um cluster de Cloud Foundry dinâmico no Azure
description: Saiba como configurar os parâmetros necessários para provisionar um cluster PCF (Cloud Foundry dinâmico) no Azure
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
ms.openlocfilehash: 5d4ac5435281f521c71556123f77d737ee6916e9
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161772"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Criar um cluster de Cloud Foundry dinâmico no Azure

Este tutorial fornece etapas rápidas para criar e gerar os parâmetros necessários para provisionar um cluster PCF (Cloud Foundry dinâmico) no Azure. Para localizar a solução de Cloud Foundry dinâmica, faça uma pesquisa no Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Pesquisar Cloud Foundry dinâmicos no Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Gerar uma chave pública SSH

Há várias maneiras de gerar uma chave SSH (Secure Shell) pública usando Windows, Mac ou Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Para obter mais informações, consulte [usar chaves SSH com o Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

## <a name="create-a-service-principal"></a>Criar um principal de serviço

> [!NOTE]
>
> Para criar uma entidade de serviço, você precisa de permissão de conta de proprietário. Você também pode escrever um script para automatizar a criação da entidade de serviço. Por exemplo, você pode usar o CLI do Azure [AZ ad SP Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Inicie sessão na sua conta do Azure.

    `az login`

    ![Logon CLI do Azure](media/deploy/az-login-output.png )
 
    Copie o valor de "ID" como sua **ID de assinatura**e copie o valor de "tenantid" para uso posterior.

2. Defina a sua subscrição predefinida para esta configuração.

    `az account set -s {id}`

3. Crie um aplicativo Azure Active Directory para o PCF. Especifique uma senha alfanumérica exclusiva. Armazene a senha como seu **clientSecret** para usar mais tarde.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Copie o valor "appId" na saída como o **ClientID** a ser usado posteriormente.

    > [!NOTE]
    >
    > Escolha seu próprio aplicativo home page e URI do identificador, por exemplo, http\://www\.contoso.com.

4. Crie uma entidade de serviço com a nova ID do aplicativo.

    `az ad sp create --id {appId}`

5. Defina a função de permissão da entidade de serviço como um colaborador.

    `az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"`

    Ou você também pode usar

    `az role assignment create --assignee {service-principal-name} --role "Contributor"`

    ![Atribuição de função da entidade de serviço](media/deploy/svc-princ.png )

6. Verifique se você pode entrar com êxito em sua entidade de serviço usando a ID do aplicativo, a senha e a ID do locatário.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Crie um arquivo. JSON no formato a seguir. Use os valores **ID da assinatura**, **tenantid**, **ClientID**e **clientSecret** que você copiou anteriormente. Guarde o ficheiro.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Obter o token de rede da tabela dinâmica

1. Registre-se ou entre em sua conta de [rede dinâmica](https://network.pivotal.io) .
2. Selecione o nome do seu perfil no canto superior direito da página. Selecione **Editar perfil**.
3. Role até a parte inferior da página e copie o valor do **token de API herdado** . Esse valor é o valor do **token de rede da tabela dinâmica** que você usa mais tarde.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Provisionar seu cluster de Cloud Foundry no Azure

Agora você tem todos os parâmetros necessários para provisionar seu [cluster de Cloud Foundry dinâmico no Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Insira os parâmetros e crie seu cluster PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Verificar a implantação e entrar no Gerenciador de operações dinâmicas

1. O cluster PCF mostra um status de implantação.

    ![Status de implantação do Azure](media/deploy/deployment.png )

2. Selecione o link **implantações** na navegação à esquerda para obter credenciais para o Gerenciador de operações do PCF. Selecione o **nome da implantação** na próxima página.
3. Na barra de navegação à esquerda, selecione o link **saídas** para exibir a URL, o nome de usuário e a senha para o Gerenciador de operações do PCF. O valor "OPSMAN-FQDN" é a URL.
 
    ![Saída de implantação Cloud Foundry](media/deploy/deploy-outputs.png )
 
4. Inicie a URL em um navegador da Web. Insira as credenciais da etapa anterior para entrar.

    ![Página de entrada dinâmica](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Se o navegador Internet Explorer falhar devido a uma mensagem de aviso "site não seguro", selecione **mais informações** e vá para a página da Web. Para o Firefox, selecione **Avançar** e adicione a certificação para continuar.

5. O PCF Ops Manager exibe as instâncias do Azure implantadas. Agora você pode implantar e gerenciar seus aplicativos aqui.
               
    ![Instância do Azure implantada em Pivotal](media/deploy/ops-mgr.png )
 
