---
title: Quickstart - Criar um servidor de Serviços de Análise no portal Azure [ Microsoft Docs
description: Aprenda a criar rapidamente uma instância de servidor de Serviços de Análise Azure utilizando o portal Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 36899aa789f4947fe860621bc1795bfbe2ff1ecf
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697478"
---
# <a name="quickstart-create-a-server---portal"></a>Início Rápido: Criar um servidor - Portal

Este início rápido descreve como criar um recurso de servidor do Analysis Services na sua subscrição do Azure com o portal.

## <a name="prerequisites"></a>Pré-requisitos 

* **Subscrição do Azure**: aceda à [Avaliação Gratuita do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para criar uma conta.
* **Azure Active Directory**: a sua subscrição tem de estar associada a um inquilino do Azure Active Directory. E tem de ter sessão iniciada no Azure com uma conta nesse Azure Active Directory. Para saber mais,v eja [Authentication and user permissions](analysis-services-manage-users.md) (Autenticação e permissões de utilizador).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure 

[Inscreva-se no portal](https://portal.azure.com)


## <a name="create-a-server"></a>Criar um servidor

1. Clique **+ Crie um recurso**  >  **Analytics**Analysis  >  **Services**.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. No **Analysis Services**, preencha os campos obrigatórios e, em seguida, prima **Criar**.
   
   * **Nome do servidor**: escreva um nome exclusivo utilizado para referenciar o servidor. O nome do servidor deve começar com um caracteres minúsculos e conter entre 3 a 128 caracteres e números minúsculos. Espaços brancos e personagens especiais não são permitidos.
   * **Subscrição**: selecione a subscrição à qual este servidor será associado.
   * **Grupo de recursos**: crie um novo grupo de recursos ou selecione um que já tenha. Os grupos de recursos são concebidos para ajudá-lo a gerir uma coleção de recursos do Azure. Para saber mais, veja [grupos de recursos](../azure-resource-manager/management/overview.md).
   * **Localização**: esta localização do datacenter do Azure aloja o servidor. Escolha a localização mais perto da sua base de utilizadores.
   * **Escalão de preço**: selecionar um escalão de preço. Se estiver a testar e pretende instalar a base de dados do modelo de exemplo, selecione o escalão **D1** gratuito. Para saber mais, veja [Preços do Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Administrador**: por predefinição, esta será a conta em que tem sessão iniciada. Pode escolher uma conta diferente do Azure Active Directory.
   * **Definição do Armazenamento de Cópias de Segurança**: opcional. Se já tiver uma [conta de armazenamento](../storage/common/storage-introduction.md), pode especificá-la como a predefinição para a cópia de segurança de base de dados modelo. Também pode especificar as definições da [cópia de segurança e restauro](analysis-services-backup.md) mais tarde.
   * **Expiração da chave de armazenamento**: opcional. Especifique um período de expiração da chave de armazenamento.

Criar o servidor, normalmente, demora menos de um minuto. Se tiver selecionado **Adicionar ao Portal**, navegue até ao seu portal para ver o seu novo servidor. Ou, navegue para **todos os**  >  **serviços de análise de serviços** para ver se o seu servidor está pronto. Os servidores suportam modelos tabular nos níveis de compatibilidade 1200 e mais elevados. O nível de compatibilidade do modelo é especificado no Visual Studio ou SSMS.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar, elimine o seu servidor. Na **Descrição Geral** do servidor, clique em **Eliminar**. 

 ![Limpeza](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Próximos passos
Neste início rápido, aprendeu a criar um servidor na sua subscrição do Azure. Agora que tem o servidor, pode ajudar a protegê-lo ao configurar uma firewall do servidor (opcional). Também pode adicionar um modelo de dados de exemplo básico ao seu servidor diretamente a partir do portal. Ter um modelo de exemplo é útil para saber como configurar as funções de base de dados do modelo e testar as ligações de cliente. Para obter mais informações, avance para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Quickstart: Configurar firewall do servidor - Portal](analysis-services-qs-firewall.md)   
> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao servidor](analysis-services-create-sample-model.md)
