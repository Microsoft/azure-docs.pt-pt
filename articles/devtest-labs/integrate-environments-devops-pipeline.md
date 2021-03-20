---
title: Integrar ambientes em Pipelines Azure em Azure DevTest Labs
description: Aprenda a integrar ambientes Azure DevTest Labs nos seus oleodutos de integração contínua (CI) e entrega contínua (CD) do Azure DevOps.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483028"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrar ambientes nos seus oleodutos Azure DevOps CI/CD
Pode utilizar a extensão Azure DevTest Labs Tasks que está instalada nos Serviços Azure DevOps (anteriormente conhecidos como Visual Studio Team Services) para integrar facilmente o seu pipeline de integração contínua (CI)/ entrega contínua (CD) com a Azure DevTest Labs. Estas extensões facilitam a implantação rápida de um [ambiente](devtest-lab-test-env.md) para uma tarefa específica de teste e, em seguida, apagam-na quando o teste estiver terminado. 

Este artigo mostra como criar e implantar um ambiente, em seguida, apagar o ambiente, tudo num oleoduto completo. Normalmente, executaria cada uma destas tarefas individualmente no seu próprio oleoduto de implantação de testes de construção personalizado. As extensões utilizadas neste artigo são para além destas [tarefas de criação/eliminação de VM DTL:](devtest-lab-integrate-ci-cd.md)

- Criar um Ambiente
- Eliminar um Ambiente

## <a name="before-you-begin"></a>Antes de começar
Antes de poder integrar o seu pipeline CI/CD com a Azure DevTest Labs, instale a extensão [de tarefas Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) a partir do Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Criar e configurar o laboratório para ambientes
Esta secção descreve como criar e configurar um laboratório onde o ambiente Azure será implantado.

1. [Crie um laboratório](devtest-lab-create-lab.md) se ainda não tiver um. 
2. Configure o laboratório e crie um modelo de ambiente seguindo as instruções deste artigo: [Criar ambientes multi-VM e recursos PaaS com modelos de Gestor de Recursos Azure](devtest-lab-create-environment-from-arm.md).
3. Para este exemplo, utilize um modelo de arranque rápido Azure [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) existente.
4. Copie a pasta **201-web-app-redis-cache-sql-database** na pasta **ArmTemplate** no repositório configurado no passo 2.

## <a name="create-a-release-definition"></a>Criar uma definição de versão
Para criar a definição de libertação, faça o seguinte:

1.  No **separador Lançamentos** do **hub Build & Release,** selecione o botão **de sinal mais (+).**
2.  Na janela **de definição de desbloqueio Criar,** selecione o modelo **Vazio** e, em seguida, selecione **Seguinte**.
3.  **Selecione Escolha Mais tarde** e, em seguida, selecione **Criar** para criar uma nova definição de lançamento com um ambiente padrão e sem artefactos ligados.
4.  Para abrir o menu de atalho, na nova definição de lançamento, selecione a **elipse (...)** ao lado do nome do ambiente e, em seguida, selecione **variáveis Configure**.
5.  Na janela **Configure - ambiente,** para as variáveis que utiliza nas tarefas de definição de libertação, insira os seguintes valores:
1.  Para **administradorLogin,** insira o nome de login do Administrador SQL.
2.  Para **administradorLoginPassword,** introduza a palavra-passe a utilizar pelo login do Administrador SQL. Utilize o ícone "cadeado" para ocultar e fixar a palavra-passe.
3.  Para **base de dados Nome,** insira o nome sql Database.
4.  Estas variáveis são específicas para os ambientes de exemplo, diferentes ambientes podem ter diferentes variáveis.

## <a name="create-an-environment"></a>Criar um ambiente
A próxima fase da implantação consiste em criar o ambiente a utilizar para fins de desenvolvimento ou teste.

1. Na definição de desbloqueio, selecione **Adicionar tarefas**.
2. No separador **Tarefas,** adicione uma tarefa Azure DevTest Labs Create Environment. Configure a tarefa da seguinte forma:
    1. Para **a subscrição Azure RM**, selecione uma ligação na lista **de Conexões de Serviço Azure disponíveis** ou crie uma ligação de permissões mais restrita à sua subscrição Azure. Para mais informações, consulte [o ponto final do serviço Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. Para **o Nome do Laboratório,** selecione o nome do caso que criou anteriormente*.
3. Para **o Nome do Repositório,** selecione o repositório onde o modelo de Gestor de Recursos (201) foi empurrado para*.
4. Para **o nome do modelo,** selecione o nome do ambiente que guardou para o seu repositório de código fonte*. 
5. O **Nome do Laboratório,** **Nome do Repositório** e **Nome do Modelo** são as representações amigáveis dos IDs de recursos Azure. Introduzir manualmente o nome amigável provocará falhas, utilize as listas de espera para selecionar as informações.
6. Para **o Nome do Ambiente,** insira um nome para identificar exclusivamente o caso do ambiente dentro do laboratório.  Deve ser único dentro do laboratório.
7. O **Arquivo de Parâmetros** e os **Parâmetros** permitem que os parâmetros personalizados sejam transmitidos ao ambiente. Ambos podem ser utilizados para definir os valores dos parâmetros. Para este exemplo, utilizar-se-á a secção Parâmetros. Utilize os nomes das variáveis que definiu no ambiente, por exemplo: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. As informações dentro do modelo de ambiente podem ser transmitidas na secção de saída do modelo. Consulte **Criar variáveis de saída com base na saída do modelo de ambiente** para que outras tarefas possam utilizar os dados. `$(Reference name.Output Name)` é o padrão a seguir. Por exemplo, se o Nome de Referência fosse DTL e o nome de saída no modelo fosse a localização, a variável seria `$(DTL.location)` .

## <a name="delete-the-environment"></a>Apagar o ambiente
A fase final é eliminar o Ambiente que implementou na sua instância Azure DevTest Labs. Normalmente, apagaria o ambiente depois de executar as tarefas de dev ou executar os testes de que necessita nos recursos utilizados.

Na definição de desbloqueio, **selecione Adicionar tarefas**, e, em seguida, no **separador Implementar,** adicione uma tarefa **Azure DevTest Labs Delete Environment.** Configure-o da seguinte forma:

1. Para eliminar o VM, consulte [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Para **a subscrição Azure RM**, selecione uma ligação na lista **de Conexões de Serviço Azure disponíveis** ou crie uma ligação de permissões mais restrita à sua subscrição Azure. Para mais informações, consulte [o ponto final do serviço Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. Para **o Nome do Laboratório,** selecione o laboratório onde o ambiente existe.
    3. Para **nome ambiente,** insira o nome do ambiente a remover.
2. Insira um nome para a definição de libertação e, em seguida, guarde-o.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos: 
- [Crie ambientes multi-VM com modelos de Gestor de Recursos](devtest-lab-create-environment-from-arm.md).
- Modelos quickstart Resource Manager para automação de DevTest Labs do [repositório GitHub da DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- [Página de resolução de problemas VSTS](/azure/devops/pipelines/troubleshooting)

