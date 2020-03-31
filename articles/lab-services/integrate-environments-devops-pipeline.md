---
title: Integrar ambientes em Pipelines Azure em Laboratórios Azure DevTest
description: Aprenda a integrar os ambientes azure DevTest Labs nos seus oleodutos de integração contínua Azure DevOps (CI) e entrega contínua (CD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d7e481879326ac30093bd116222bddc28640398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169414"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integre os ambientes nos seus oleodutos Azure DevOps CI/CD
Pode utilizar a extensão de tarefas Azure DevTest Labs que está instalada nos Serviços Azure DevOps (anteriormente conhecidos como Serviços de Equipa de Estúdio Visual) para integrar facilmente o seu oleoduto de construção e lançamento contínuo (CI)/entrega contínua (CD) com a Azure DevTest Labs. Estas extensões facilitam a implantação rápida de um [ambiente](devtest-lab-test-env.md) para uma tarefa específica de teste e, em seguida, apagam-na quando o teste estiver terminado. 

Este artigo mostra como criar e implantar um ambiente, em seguida, apagar o ambiente, tudo num oleoduto completo. Normalmente, realizaria cada uma destas tarefas individualmente no seu próprio pipeline personalizado de implementação de testes. As extensões utilizadas neste artigo são além [destas tarefas de criação/exclusão de VM DTL:](devtest-lab-integrate-ci-cd-vsts.md)

- Criar um Ambiente
- Eliminar um Ambiente

## <a name="before-you-begin"></a>Antes de começar
Antes de poder integrar o seu pipeline CI/CD com o Azure DevTest Labs, instale a extensão de [Tarefas azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) do Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Criar e configurar o laboratório para ambientes
Esta secção descreve como criar e configurar um laboratório onde o ambiente Azure será implantado.

1. [Crie um laboratório](devtest-lab-create-lab.md) se ainda não tiver um. 
2. Configure o laboratório e crie um modelo de ambiente seguindo instruções deste artigo: [Criar ambientes multi-VM e recursos PaaS com modelos](devtest-lab-create-environment-from-arm.md)de Gestor de Recursos Azure .
3. Para este exemplo, utilize um modelo [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)de quickstart Azure existente.
4. Copie a pasta **201-web-app-redis-cache-sql-base de dados** na pasta **ArmTemplate** no repositório configurado no passo 2.

## <a name="create-a-release-definition"></a>Criar uma definição de versão
Para criar a definição de libertação, faça o seguinte:

1.  No separador **Desbloqueio** do centro de lançamento **build &,** selecione o botão **de sinal mais (+).**
2.  Na janela de **definição de lançamento Criar,** selecione o modelo **Vazio** e, em seguida, selecione **Seguinte**.
3.  Selecione **Escolha Mais Tarde**e, em seguida, selecione **Criar** para criar uma nova definição de lançamento com um ambiente padrão e sem artefactos ligados.
4.  Para abrir o menu de atalho, na nova definição de lançamento, selecione a **elipsis (...)** ao lado do nome do ambiente e, em seguida, selecione **variáveis Configure**.
5.  Na **janela Configure - ambiente,** para as variáveis que utiliza nas tarefas de definição de lançamento, introduza os seguintes valores:
1.  Para **administradorLogin,** introduza o nome de login do Administrador SQL.
2.  Para **administradorLoginPassword**, introduza a palavra-passe a utilizar pelo login do Administrador SQL. Utilize o ícone "cadeado" para ocultar e fixar a palavra-passe.
3.  Para base **de dadosName**, introduza o nome da Base de Dados SQL.
4.  Estas variáveis são específicas para os ambientes de exemplo, ambientes diferentes podem ter variáveis diferentes.

## <a name="create-an-environment"></a>Criar um ambiente
A próxima fase da implantação é criar o ambiente a ser utilizado para fins de desenvolvimento ou teste.

1. Na definição de lançamento, selecione **Adicionar tarefas**.
2. No separador **Tasks,** adicione uma tarefa Azure DevTest Labs Create Environment. Configure a tarefa da seguinte forma:
    1. Para **a subscrição do Azure RM,** selecione uma ligação na lista de Ligações de **Serviço Azure disponíveis** ou crie uma ligação de permissões mais restrita à sua subscrição Azure. Para mais informações, consulte o [ponto final do serviço Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. Para **Nome de Laboratório**, selecione o nome da instância que criou anteriormente*.
3. Para o **Nome do Repositório,** selecione o repositório onde o modelo de Gestor de Recursos (201) foi empurrado para*.
4. Para nome do **modelo,** selecione o nome do ambiente que guardou no seu repositório de código fonte*. 
5. O Nome do **Laboratório,** **Nome repositório**e Nome do **Modelo** são as representações amigáveis dos IDs de recursos Azure. A introdução manual do nome amigável causará falhas, utilize as listas de drop-down para selecionar a informação.
6. Para **Nome ambiente,** introduza um nome para identificar exclusivamente a instância ambiental dentro do laboratório.  Deve ser único dentro do laboratório.
7. O **Arquivo parâmetro** e os **parâmetros**permitem passar parâmetros personalizados para o ambiente. Qualquer um ou ambos podem ser usados para definir os valores do parâmetro. Para este exemplo, será utilizada a secção Parâmetros. Utilize os nomes das variáveis que definiu no ambiente, por exemplo:`-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. A informação dentro do modelo de ambiente pode ser transmitida na secção de saída do modelo. Verifique **Criar variáveis** de saída com base na saída do modelo de ambiente para que outras tarefas possam utilizar os dados. `$(Reference name.Output Name)`é o padrão a seguir. Por exemplo, se o nome de referência fosse DTL e o `$(DTL.location)`nome de saída no modelo fosse a localização a variável seria .

## <a name="delete-the-environment"></a>Eliminar o ambiente
A fase final é eliminar o Ambiente que implementou na sua instância Azure DevTest Labs. Normalmente, eliminaria o ambiente depois de executar as tarefas de dev ou realizar os testes de que necessita nos recursos implantados.

Na definição de lançamento, selecione **Adicionar tarefas**, e, em seguida, no **separador Deploy,** adicione uma tarefa de eliminação de **laboratórios Azure DevTest Ambiente.** Configure-o da seguinte forma:

1. Para eliminar o VM, consulte [as tarefas dos Laboratórios Azure DevTest:](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
    1. Para **a subscrição do Azure RM,** selecione uma ligação na lista de Ligações de **Serviço Azure disponíveis** ou crie uma ligação de permissões mais restrita à sua subscrição Azure. Para mais informações, consulte o [ponto final do serviço Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. Para **Nome de Laboratório,** selecione o laboratório onde o ambiente existe.
    3. Para **Nome ambiente,** introduza o nome do ambiente a remover.
2. Introduza um nome para a definição de lançamento e, em seguida, guarde-o.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 
- [Crie ambientes multi-VM com modelos](devtest-lab-create-environment-from-arm.md)de Gestor de Recursos .
- Modelos de Quickstart Resource Manager para automatização de Laboratórios DevTest do [repositório DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- [Página de resolução de problemas VSTS](/azure/devops/pipelines/troubleshooting)

