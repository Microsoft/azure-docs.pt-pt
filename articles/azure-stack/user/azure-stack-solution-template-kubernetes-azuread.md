---
title: Implementar o Kubernetes no Azure Stack com o Azure Active Directory (Azure AD) | Documentos da Microsoft
description: Saiba como implementar o Kubernetes no Azure Stack com o Azure Active Directory (Azure AD).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: a24ba9c0f19ce0a040671e4515dc2e7d2468fb5c
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985585"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Implementar o Kubernetes no Azure Stack com o Azure Active Directory

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em pré-visualização. Cenário de desligado de pilha do Azure não é atualmente suportado pela pré-visualização.

Pode seguir os passos neste artigo para implementar e configurar os recursos do Kubernetes, ao utilizar o Azure Active Directory (Azure AD) como o seu serviço de gestão de identidade, num único, coordenada operação.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de que tem as permissões corretas e que o Azure Stack está pronto.

1. Certifique-se de que pode criar aplicativos no seu inquilino do Azure Active Directory (Azure AD). Tem estas permissões para a implementação de Kubernetes.

    Para obter instruções sobre como verificar a suas permissões, consulte [permissões de verificação do Active Directory do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

1. Gere um público e privado par de chaves SSH para iniciar sessão na VM do Linux no Azure Stack. Terá a chave pública ao criar o cluster.

    Para obter instruções sobre como gerar uma chave, consulte [geração de chave SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Verifique se tem uma subscrição válida no seu portal de inquilino do Azure Stack, e que tem suficiente IP público de endereços disponíveis para adicionar novos aplicativos.

    O cluster não é possível implementar um Azure Stack **administrador** subscrição. Tem de utilizar um **utilizador** subscrição. 

1. Se não tiver o Cluster de Kubernetes no seu mercado, fale com seu administrador do Azure Stack.

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Configure um principal de serviço no Azure. O principal de serviço fornece o acesso de aplicação aos recursos do Azure Stack.

1. Inicie sessão no global [portal do Azure](http://portal.azure.com).

1. Verificação que iniciou sessão com o inquilino do Azure AD associado à instância do Azure Stack. Pode mudar seu início de sessão ao clicar no ícone de filtro na barra de ferramentas do Azure.

    ![Selecione o seu inquilino do AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Crie uma aplicação do Azure AD.

    a. Selecione **do Azure Active Directory** > **+ registos de aplicações** > **novo registo de aplicação**.

    b. Introduza um **nome** do aplicativo.

    c. Selecione **aplicação Web / API**.

    d. Introduza `http://localhost` para o **URL de início de sessão**.

    c. Clique em **Criar**.

1. Aponte o **ID da Aplicação**. Precisará do ID ao criar o cluster. O ID é referenciado como **ID de cliente do Principal de serviço**.

1. Selecione **configurações** > **chaves**.

    a. Introduza o **Descrição**.

    b. Selecione **nunca expira** para **Expires**.

    c. Selecione **Guardar**. Certifique-se de observar a cadeia de caracteres de chave. Terá a cadeia de caracteres de chave ao criar o cluster. A chave é referenciada como o **segredo do cliente de Principal de serviço**.

## <a name="give-the-service-principal-access"></a>Dar o acesso de principal de serviço

Conceder ao principal de serviço acesso à sua subscrição para que o principal pode criar recursos.

1.  Inicie sessão para o [portal do Azure Stack](https://portal.local.azurestack.external/).

1. Selecione **todos os serviços** > **subscrições**.

1. Selecione a subscrição criada pela sua operadora de rede para utilizar o Cluster de Kubernetes.

1. Selecione **controlo de acesso (IAM)** > selecione **adicionar atribuição de função**.

1. Selecione o **contribuinte** função.

1. Selecione o nome da aplicação criado para o seu serviço principal. Poderá ter de escrever o nome na caixa de pesquisa.

1. Clique em **Guardar**.

## <a name="deploy-kubernetes"></a>Implementar o Kubernetes

1. Abra o [portal do Azure Stack](https://portal.local.azurestack.external).

1. Selecione **+ criar um recurso** > **computação** > **Cluster do Kubernetes**. Clique em **Criar**.

    ![Implementar Modelo de Solução](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Noções básicas

1. Selecione **Noções básicas** criar Cluster do Kubernetes.

    ![Implementar Modelo de Solução](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Selecione seu **subscrição** ID.

1. Introduza o nome de um novo grupo de recursos ou selecione um grupo de recursos existente. O nome do recurso tem de ser de alfanuméricos e minúsculas.

1. Selecione o **localização** do grupo de recursos. Esta é a região que escolher para a sua instalação do Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Definições do Cluster de Kubernetes

1. Selecione **definições do Cluster de Kubernetes** criar Cluster do Kubernetes.

    ![Implementar Modelo de Solução](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Introduza o **nome de utilizador de administrador VM de Linux**. Nome de utilizador para as máquinas de virtuais de Linux que fazem parte do cluster de Kubernetes e DVM.

1. Introduza o **chave pública SSH** utilizado na autorização de todas as máquinas de Linux criados como parte do cluster de Kubernetes e DVM.

1. Introduza o **prefixo de DNS de perfil do mestre** que seja exclusivo para a região. Tem de ser um nome exclusivo para a região, tal como `k8s-12345`. Tente optou por ele mesmo que o grupo de recursos dê um nome como melhor prática.

    > [!Note]  
    > Para cada cluster, utilize um prefixo DNS de perfil de mestre de novos e únicos.

1. Selecione o **contagem de perfil do Kubernetes principal conjunto**. A contagem de contém o número de nós no conjunto mestre. Aí pode ter entre 1 e 7. Este valor deve ser um número ímpar.

1. Selecione **o VMSize das VMs mestres Kubernetes**.

1. Selecione o **contagem de perfil do conjunto de nós de Kubernetes**. A contagem de contém o número de agentes no cluster. 

1. Selecione o **perfil de armazenamento**. Pode escolher **Blob de disco** ou **disco gerido**. Esta ação Especifica as VMs de nó de tamanho de VM de Kubernetes. 

1. Selecione **do Azure AD** para o **sistema de identidade do Azure Stack** para a sua instalação do Azure Stack. 

1. Introduza o **ID de cliente do Principal de serviço** é utilizada pelo fornecedor de cloud do Azure do Kubernetes. O ID de cliente identificado como o ID da aplicação quando criou o seu principal de serviço.

1. Introduza o **segredo do cliente de Principal de serviço** que criou ao criar o seu principal de serviço.

1. Introduza o **versão de fornecedor de Cloud do Azure do Kubernetes**. Esta é a versão para o fornecedor do Azure do Kubernetes. O Azure Stack lança uma compilação do Kubernetes personalizada para cada versão do Azure Stack.

### <a name="3-summary"></a>3. Resumo

1. Selecione o resumo. O painel apresenta uma mensagem de validação para as definições de configurações de Cluster de Kubernetes.

    ![Implementar Modelo de Solução](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Reveja as definições.

3. Selecione **OK** para implementar o seu cluster.

> [!TIP]  
>  Se tiver dúvidas sobre a implementação, pode publicar a sua pergunta ou ver se alguém tem já respondeu à pergunta na [fórum do Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>Passos Seguintes

[Ligar ao cluster](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Ativar o Dashboard do Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)
