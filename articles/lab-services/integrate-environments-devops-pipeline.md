---
title: Integrar ambientes em Pipelines do Azure no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como integrar ambientes do Azure DevTest Labs no seu Azure DevOps a integração contínua (CI) e pipelines de entrega contínua (CD).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61318396"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrar ambientes os pipelines CI/CD do Azure DevOps
Pode utilizar a extensão de tarefas do Azure DevTest Labs está instalada nos serviços de DevOps do Azure (anteriormente conhecido como Visual Studio Team Services) para integrar facilmente a sua integração contínua (CI) / entrega contínua (CD) compilação e liberação de pipelines com o Azure Laboratórios DevTest. Essas extensões que seja mais fácil de implementar rapidamente uma [ambiente](devtest-lab-test-env.md) para específicos de um tarefa de teste e, em seguida, elimine-o quando o teste for concluído. 

Este artigo mostra como criar e implementar um ambiente, em seguida, eliminar o ambiente, todos num pipeline completo. Normalmente executará cada uma destas tarefas individualmente em seu próprio pipeline de compilação-teste-implementação personalizada. As extensões usadas neste artigo são além destas [criar/eliminar tarefas de DTL VM](devtest-lab-integrate-ci-cd-vsts.md):

- Criar um ambiente
- Eliminar um ambiente

## <a name="before-you-begin"></a>Antes de começar
Antes de pode integrar o seu pipeline CI/CD com o Azure DevTest Labs, instale [do Azure DevTest Labs tarefas](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) extensão do Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Criar e configurar o laboratório para ambientes
Esta secção descreve como criar e configurar um laboratório em que o ambiente do Azure será implementado.

1. [Criar um laboratório](devtest-lab-create-lab.md) se ainda não tiver uma. 
2. Configurar o laboratório e crie um modelo de ambiente, seguindo as instruções deste artigo: [Criar ambientes multi-VM e recursos PaaS com modelos Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. Neste exemplo, utilizar um modelo de início rápido do Azure existente [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Copiar o **201-web-app-redis-cache-sql-database** pasta para o **ArmTemplate** pasta no repositório configurada no passo 2.

## <a name="create-a-release-definition"></a>Criar uma definição de versão
Para criar a definição de versão, faça o seguinte:

1.  Na **versões** separador do **criar e lançar hub**, selecione o **sinal de adição (+)** botão.
2.  Na **Criar definição de versão** janela, selecione a **vazia** modelo e, em seguida, selecione **seguinte**.
3.  Selecione **escolha mais tarde**e, em seguida, selecione **criar** para criar uma nova definição de versão com o ambiente de um padrão e não ligados artefactos.
4.  Para abrir o menu de atalho, na definição da nova versão, selecione o **reticências (...)**  seguinte para o nome do ambiente e, em seguida, selecione **configurar variáveis**.
5.  Na **configurar - ambiente** janela, para as variáveis que utilize as tarefas de definição de versão, introduza os seguintes valores:
1.  Para **administratorLogin**, introduza o nome de início de sessão de administrador do SQL.
2.  Para **administratorLoginPassword**, introduza a palavra-passe a ser utilizado pelo início de sessão de administrador do SQL. Utilize o ícone de "cadeado" para ocultar e proteger a palavra-passe.
3.  Para **databaseName**, introduza o nome de base de dados SQL.
4.  Estas variáveis são específicas para os ambientes de exemplo, ambientes diferentes podem ter diferentes variáveis.

## <a name="create-an-environment"></a>Criar um ambiente
A próxima fase da implementação é criar o ambiente para ser utilizado para fins de testes ou de desenvolvimento.

1. Na definição de versão, selecione **adicionar tarefas**.
2. Sobre o **tarefas** separador, adicionar uma tarefa do Azure DevTest Labs criar ambiente. Configure a tarefa da seguinte forma:
    1. Para **subscrição do Azure RM**, selecione uma ligação no **ligações de serviço do Azure disponíveis** listar ou criar uma ligação de permissões mais restrita a sua subscrição do Azure. Para obter mais informações, consulte [ponto final de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. Para **nome de laboratório**, selecione o nome da instância que criou anteriormente *.
3. Para **nome do repositório**, selecione o repositório onde o modelo do Resource Manager (201) tiver sido enviado para *.
4. Para **nome do modelo**, selecione o nome do ambiente que guardou a sua origem código repositório *. 
5. O **nome de laboratório**, **nome do repositório**, e **nome do modelo** são representações amigáveis os IDs dos recursos do Azure. Introduzir manualmente o nome amigável, irá dar origem a falhas, utilize as listas pendentes para selecionar as informações.
6. Para **nome do ambiente**, introduza um nome para identificar exclusivamente a instância de ambiente no laboratório.  Tem de ser exclusivo no laboratório.
7. O **ficheiro de parâmetros** e o **parâmetros**, permitir que os parâmetros personalizados a serem passados para o ambiente. Um ou ambos podem ser utilizados para definir os valores de parâmetro. Neste exemplo, será utilizada a secção de parâmetros. Utilize os nomes das variáveis que definiu no ambiente, por exemplo: `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. Informações de dentro do modelo de ambiente podem ser passadas por meio de na secção de saída do modelo. Verifique **criar variáveis de saída com base no resultado de modelo de ambiente** para que outras tarefas, podem utilizar os dados. `$(Reference name.Output Name)` é o padrão a seguir. Por exemplo, se o nome de referência foi DTL e o nome de saída no modelo de localização a variável seria `$(DTL.location)`.

## <a name="delete-the-environment"></a>Eliminar o ambiente
A etapa final é eliminar o ambiente que tenha implementado na sua instância do Azure DevTest Labs. Normalmente iria eliminar o ambiente depois de executar as tarefas de desenvolvimento ou executar os testes que precisa sobre os recursos implementados.

Na definição de versão, selecione **adicionar tarefas**e, em seguida, no **Deploy** separador, adicione uma **Azure DevTest Labs eliminar ambiente** tarefas. Configure da seguinte forma:

1. Para eliminar a VM, veja [do Azure DevTest Labs tarefas](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Para **subscrição do Azure RM**, selecione uma ligação no **ligações de serviço do Azure disponíveis** listar ou criar uma ligação de permissões mais restrita a sua subscrição do Azure. Para obter mais informações, consulte [ponto final de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. Para **nome de laboratório**, selecione o onde existe o ambiente de laboratório.
    3. Para **nome do ambiente**, introduza o nome do ambiente a ser removido.
2. Introduza um nome para a definição de versão e, em seguida, guardá-lo.

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos: 
- [Criar ambientes multi-VM com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
- Modelos do Gestor de recursos de início rápido para a automatização de DevTest Labs dos [repositório do GitHub de laboratórios DevTest](https://github.com/Azure/azure-quickstart-templates).
- [Página de resolução de problemas do VSTS](/azure/devops/pipelines/troubleshooting)

