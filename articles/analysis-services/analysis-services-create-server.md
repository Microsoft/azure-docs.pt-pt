---
title: Início Rápido - Criar um servidor do Analysis Services com o portal do Azure | Microsoft Docs
description: Saiba como criar uma instância de servidor do Analysis Services no Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e6973ee311f1d3f5ba19714695f67bd321d953b7
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72295050"
---
# <a name="quickstart-create-a-server---portal"></a>Início Rápido: Criar um servidor - Portal

Este início rápido descreve como criar um recurso de servidor do Analysis Services na sua subscrição do Azure com o portal.

## <a name="prerequisites"></a>Pré-requisitos 

* **Subscrição do Azure**: aceda à [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: a sua subscrição tem de estar associada a um inquilino do Azure Active Directory. E tem de ter sessão iniciada no Azure com uma conta nesse Azure Active Directory. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure 

[Entrar no portal](https://portal.azure.com)


## <a name="create-a-server"></a>Criar um servidor

1. Clique em **+ criar um recurso** > **Analytics** > **Analysis Services**.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. No **Analysis Services**, preencha os campos obrigatórios e, em seguida, prima **Criar**.
   
   * **Nome do servidor**: escreva um nome exclusivo utilizado para referenciar o servidor. O nome do servidor deve começar com um caractere minúsculo e conter entre 3 e 128 caracteres minúsculos e números. Espaços em branco e caracteres especiais não são permitidos.
   * **Subscrição**: selecione a subscrição à qual este servidor será associado.
   * **Grupo de recursos**: crie um novo grupo de recursos ou selecione um que já tenha. Os grupos de recursos são concebidos para ajudá-lo a gerir uma coleção de recursos do Azure. Para saber mais, veja [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Localização**: esta localização do datacenter do Azure aloja o servidor. Escolha a localização mais perto da sua base de utilizadores.
   * **Escalão de preço**: selecionar um escalão de preço. Se estiver a testar e pretende instalar a base de dados do modelo de exemplo, selecione o escalão **D1** gratuito. Para saber mais, veja [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Administrador**: por predefinição, esta será a conta em que tem sessão iniciada. Pode escolher uma conta diferente do Azure Active Directory.
   * **Definição do Armazenamento de Cópias de Segurança**: opcional. Se já tiver uma [conta de armazenamento](../storage/common/storage-introduction.md), pode especificá-la como a predefinição para a cópia de segurança de base de dados modelo. Também pode especificar as definições da [cópia de segurança e restauro](analysis-services-backup.md) mais tarde.
   * **Expiração da chave de armazenamento**: opcional. Especifique um período de expiração da chave de armazenamento.

Criar o servidor, normalmente, demora menos de um minuto. Se tiver selecionado **Adicionar ao Portal**, navegue até ao seu portal para ver o seu novo servidor. Ou navegue até **Todos os serviços** > **Analysis Services** para ver se o servidor está pronto. Os servidores dão suporte a modelos de tabela nos níveis de compatibilidade 1200 e superiores. O nível de compatibilidade do modelo é especificado em SSDT ou SSMS.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar, elimine o seu servidor. Na **Descrição Geral** do servidor, clique em **Eliminar**. 

 ![Limpeza](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Passos seguintes
Neste início rápido, aprendeu a criar um servidor na sua subscrição do Azure. Agora que tem o servidor, pode ajudar a protegê-lo ao configurar uma firewall do servidor (opcional). Também pode adicionar um modelo de dados de exemplo básico ao seu servidor diretamente a partir do portal. Ter um modelo de exemplo é útil para saber como configurar as funções de base de dados do modelo e testar as ligações de cliente. Para obter mais informações, avance para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Início Rápido: Configurar a firewall do servidor - Portal](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao servidor](analysis-services-create-sample-model.md)
