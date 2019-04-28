---
title: Início Rápido - Criar um servidor do Analysis Services com o portal do Azure | Microsoft Docs
description: Saiba como criar uma instância de servidor do Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 04/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e54c18eb405ffa36260e9980705784130bc0ca4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023765"
---
# <a name="quickstart-create-a-server---portal"></a>Início rápido: Criar um servidor - Portal

Este início rápido descreve como criar um recurso de servidor do Analysis Services na sua subscrição do Azure com o portal.

## <a name="prerequisites"></a>Pré-requisitos 

* **Subscrição do Azure**: Visite [avaliação gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: A sua subscrição tem de ser associada a um inquilino do Azure Active Directory. E tem de ter sessão iniciada no Azure com uma conta nesse Azure Active Directory. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure 

[Inicie sessão no portal](https://portal.azure.com)


## <a name="create-a-server"></a>Criar um servidor

1. Clique em **+ criar um recurso** > **análise** > **Analysis Services**.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. No **Analysis Services**, preencha os campos obrigatórios e, em seguida, prima **Criar**.
   
   * **Nome do servidor**: Escreva um nome exclusivo utilizado para referenciar o servidor.
   * **Subscrição**: Selecione a subscrição que neste servidor será associado.
   * **Grupo de recursos**: Crie um novo grupo de recursos ou selecione um que já tenha. Os grupos de recursos são concebidos para ajudá-lo a gerir uma coleção de recursos do Azure. Para saber mais, veja [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Localização**: Esta localização do datacenter do Azure aloja o servidor. Escolha a localização mais perto da sua base de utilizadores.
   * **Escalão de preço**: Selecione um escalão de preço. Se estiver a testar e pretende instalar a base de dados do modelo de exemplo, selecione o escalão **D1** gratuito. Para saber mais, veja [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Administrador**: Por predefinição, esta será a conta que tem sessão iniciada com. Pode escolher uma conta diferente do Azure Active Directory.
   * **Definição de armazenamento de cópia de segurança**: Opcional. Se já tiver uma [conta de armazenamento](../storage/common/storage-introduction.md), pode especificá-la como a predefinição para a cópia de segurança de base de dados modelo. Também pode especificar as definições da [cópia de segurança e restauro](analysis-services-backup.md) mais tarde.
   * **Expiração da chave de armazenamento**: Opcional. Especifique um período de expiração da chave de armazenamento.

Criar o servidor, normalmente, demora menos de um minuto. Se tiver selecionado **Adicionar ao Portal**, navegue até ao seu portal para ver o seu novo servidor. Ou navegue até **Todos os serviços** > **Analysis Services** para ver se o servidor está pronto. Servidores suportam modelos em tabela nos níveis de compatibilidade 1200 e superior. Nível de compatibilidade do modelo é especificado no SSDT ou SSMS.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar, elimine o seu servidor. Na **Descrição Geral** do servidor, clique em **Eliminar**. 

 ![Limpeza](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, aprendeu a criar um servidor na sua subscrição do Azure. Agora que tem o servidor, pode ajudar a protegê-lo ao configurar uma firewall do servidor (opcional). Também pode adicionar um modelo de dados de exemplo básico ao seu servidor diretamente a partir do portal. Ter um modelo de exemplo é útil para saber como configurar as funções de base de dados do modelo e testar as ligações de cliente. Para obter mais informações, avance para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Quickstart: Configurar a firewall do servidor - Portal](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao seu servidor](analysis-services-create-sample-model.md)
