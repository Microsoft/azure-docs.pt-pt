---
title: Criar um aglomerado de fundições de nuvem fundamental em Azure
description: Saiba como configurar os parâmetros necessários para fornecer um cluster de Fundais Cloud Foundry (PCF) em Azure
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73161772"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Criar um aglomerado de fundições de nuvem fundamental em Azure

Este tutorial fornece passos rápidos para criar e gerar os parâmetros necessários para fornecer um cluster pivotal Cloud Foundry (PCF) em Azure. Para encontrar a solução Fundamental Cloud Foundry, realize uma pesquisa no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Pesquisar fundida de nuvem fundamental em Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Gerar uma chave pública SSH

Existem várias formas de gerar uma chave de concha si -sh (SSH) segura pelo uso de Windows, Mac ou Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Para mais informações, consulte [Utilize as teclas SSH com Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

## <a name="create-a-service-principal"></a>Criar um principal de serviço

> [!NOTE]
>
> Para criar um diretor de serviço, precisa de autorização da conta do proprietário. Também pode escrever um guião para automatizar a criação do diretor de serviço. Por exemplo, pode utilizar o Azure CLI [ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest).

1. Inicie sessão na sua conta do Azure.

    `az login`

    ![Login Azure CLI](media/deploy/az-login-output.png )
 
    Copie o valor "id" como id de **subscrição**e copie o valor "tenantId" para usar mais tarde.

2. Defina a sua subscrição predefinida para esta configuração.

    `az account set -s {id}`

3. Crie uma aplicação azure ative diretório para o seu PCF. Especifique uma senha alfanumérica única. Guarde a palavra-passe como **clienteSecret** para usar mais tarde.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Copie o valor "appId" na saída como o seu **clienteID** para usar mais tarde.

    > [!NOTE]
    >
    > Escolha a sua própria página inicial de candidatura\:e\.identifique URI, por exemplo, http //www contoso.com.

4. Crie um diretor de serviço com o seu novo ID de aplicação.

    `az ad sp create --id {appId}`

5. Defina a função de permissão do seu principal de serviço como um Contribuinte.

    `az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"`

    Ou também pode usar

    `az role assignment create --assignee {service-principal-name} --role "Contributor"`

    ![Atribuição principal de serviço](media/deploy/svc-princ.png )

6. Verifique se pode iniciar sessão com sucesso no seu diretor de serviço utilizando o ID da aplicação, senha e ID do inquilino.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Crie um ficheiro .json no seguinte formato. Utilize o ID de **subscrição,** **o id idido do** **cliente**e os valores **clientSecret** que copiou anteriormente. Guarde o ficheiro.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Obtenha o símbolo da Rede Pivotal

1. Registe-se ou inscreva-se na sua conta [de Rede Pivotal.](https://network.pivotal.io)
2. Selecione o nome do seu perfil no canto superior direito da página. Selecione **Editar Perfil**.
3. Percorra a parte inferior da página e copie o valor LEGACY **API TOKEN.** Este valor é o seu valor **de Ficha de Rede Pivotal** que usa mais tarde.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Provisionize o seu cluster Cloud Foundry em Azure

Agora você tem todos os parâmetros que você precisa para fornecer o seu [cluster de foundry de nuvem pivotal em Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Introduza os parâmetros e crie o seu cluster PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Verifique a implementação e inscreva-se no Gestor de Operações Pivotal

1. O seu cluster PCF mostra um estado de implantação.

    ![Estado de implantação do Azure](media/deploy/deployment.png )

2. Selecione a ligação **de Implementações** na navegação à esquerda para obter credenciais para o seu Gestor de Ops PCF. Selecione o Nome de **Implantação** na página seguinte.
3. Na navegação à esquerda, selecione o link **Saídas** para exibir o URL, o nome de utilizador e a palavra-passe para o PcF Ops Manager. O valor "OPSMAN-FQDN" é o URL.
 
    ![Saída de implantação de Cloud Foundry](media/deploy/deploy-outputs.png )
 
4. Inicie o URL num navegador web. Introduza as credenciais do passo anterior para iniciar sessão.

    ![Página de inscrição fundamental](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Se o navegador Internet Explorer falhar devido a uma mensagem de aviso "Site não seguro", selecione **Mais informações** e vá para a página web. Para o Firefox, selecione **Advance** e adicione a certificação para prosseguir.

5. O seu PCF Ops Manager exibe as instâncias Azure implantadas. Agora pode implementar e gerir as suas aplicações aqui.
               
    ![Instância azure implantada em Pivotal](media/deploy/ops-mgr.png )
 
