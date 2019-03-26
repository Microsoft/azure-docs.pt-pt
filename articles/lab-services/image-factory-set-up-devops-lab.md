---
title: Executar uma fábrica de imagem a partir do Azure DevOps no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como criar uma fábrica de imagem personalizada no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 62cb8222b592660f2e7ab32d438fd4073246ee50
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58440041"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Executar uma fábrica de imagem a partir do Azure DevOps
Este artigo abrange todas as preparações necessárias para executar a fábrica de imagem a partir de DevOps do Azure (anteriormente conhecido como Visual Studio Team Services).

> [!NOTE]
> Qualquer motor de orquestração funcionará! DevOps do Azure não é obrigatório. A fábrica de imagem é executada através de scripts do PowerShell do Azure, poderia ser executadas manualmente, usando o agendador de tarefas do Windows, outros sistemas de CI/CD e assim por diante.

## <a name="create-a-lab-for-the-image-factory"></a>Criar um laboratório para a fábrica de imagem
A primeira etapa na configuração de fábrica de imagem é criar um laboratório no Azure DevTest Labs. Este laboratório é o laboratório de fábrica da imagem onde podemos criar as máquinas virtuais e guardar imagens personalizadas. Este laboratório é considerado como parte do processo geral para a fábrica de imagem. Depois de criar um laboratório, lembre-se de que guarde o nome, uma vez que irá precisar dele mais tarde.

## <a name="scripts-and-templates"></a>Scripts e os modelos
É a próxima etapa na adoção dessa fábrica de imagem para a sua equipa compreender o que está disponível. Os scripts de fábrica de imagem e modelos estão disponíveis publicamente no [repositório do GitHub do DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). Eis um resumo das partes:

- Fábrica de imagem. É a pasta de raiz. 
    - Configuração. As entradas para a fábrica de imagem
        - GoldenImages. Esta pasta contém os ficheiros JSON que representam as definições de imagens personalizadas.
        - Labs.JSON. Ficheiro onde as equipes de inscrever-se para receber imagens personalizadas específicas.
- Scripts. O mecanismo para a fábrica de imagem.

Os artigos nesta secção fornecem mais detalhes sobre estes scripts e os modelos. 

## <a name="create-an-azure-devops-team-project"></a>Criar um projeto de equipa de DevOps do Azure
DevOps do Azure permitem-lhe armazenar o código-fonte, execute o Azure PowerShell num único local. Pode agendar execuções recorrentes para manter as imagens atualizadas. Existem bons recursos para registro em log os resultados para diagnosticar problemas.  Utilizar o Azure DevOps não é um requisito no entanto, pode usar qualquer conjunto de teste/motor que pode ligar ao Azure e pode executar o Azure PowerShell.

Se tiver uma conta existente do DevOps ou projeto que pretende utilizar em vez disso, ignore este passo.

Para começar, crie uma conta gratuita em DevOps do Azure. Visite https://www.visualstudio.com/ e selecione **comece gratuitamente** imediatamente abaixo **do Azure DevOps** (anteriormente conhecido como VSTS). Terá de escolher um nome de conta exclusivo e certificar-se de que optar por gerir o código com o Git. Quando isso for criado, guarde o URL do seu projeto de equipe. Aqui está um URL de exemplo: https://<accountname>.visualstudio.com/MyFirstProject.

## <a name="check-in-the-image-factory-to-git"></a>Verifique na fábrica de imagem com o Git
Todos os PowerShell, modelos e configuração para a fábrica de imagem estão localizados no [repositório público do GitHub de laboratórios DevTest](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). A forma mais rápida de obter o código no seu novo projeto de equipe é importar um repositório. Isso extrai o repositório de DevTest Labs inteiro (então, obterá docs Extras e exemplos). 

1. Visite o projeto de DevOps do Azure que criou no passo anterior (URL aspeto **https://<accountname>.visualstudio.com/MyFirstProject**).
2. Selecione **importar um repositório**.
3. Introduza o **URL de clone** para o repositório de laboratórios DevTest: `https://github.com/Azure/azure-devtestlab`.
4. Selecione **importação**.

    ![Repositório de Git de importação](./media/set-up-devops-lab/import-git-repo.png)

Se optar por apenas check-in exatamente o que é necessário (os arquivos de fábrica da imagem), siga os passos [aqui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) para clonar o repositório de Git e enviar apenas os ficheiros localizados na **scripts/ImageFactory** diretório.

## <a name="create-a-build-and-connect-to-azure"></a>Criar uma compilação e ligar ao Azure
Neste ponto, tem os ficheiros de origem armazenados num repositório de Git no Azure DevOps. Agora, tem de configurar um pipeline para executar o Azure PowerShell. Existem muitas opções para executar estes passos. Neste artigo, utilize a definição de compilação para manter a simplicidade, mas ele funciona com a compilação de DevOps, a versão de DevOps (únicos ou vários ambientes), outros mecanismos de execução, como o agendador de tarefas do Windows ou qualquer outro equipamento que pode executar o Azure PowerShell.

> [!NOTE]
> Um ponto importante a ter em mente que alguns dos ficheiros PowerShell demoram muito tempo para ser executada quando há muita (mais de 10) imagens personalizadas para criar. Gratuito agentes alojados de compilação/liberação de DevOps tem um tempo limite de 30 minutos, pelo que não é possível utilizar o agente alojado gratuito assim que começar a criar várias imagens. Esse desafio de tempo limite aplica-se para qualquer estrutura de optar por utilizar, é bom verificar com antecedência que pode estender os tempos limite típicos para execução demorada scripts do PowerShell do Azure. No caso de DevOps do Azure, pode utilizar agentes alojados pagos ou utilizar o seu próprio agente de compilação.

1. Para começar, selecione **configurar a compilação** na home page do seu projeto de DevOps:

    ![Botão de compilação de configuração](./media/set-up-devops-lab/setup-build-button.png)
2. Especifique um **nome** para a compilação (por exemplo: Criar e fornecer imagens DevTest Labs). 
3. Selecione um **vazio** definição de compilação e selecione **aplicar** para criar sua compilação. 
4. Nesta fase, pode escolher **alojado** para o agente de compilação. 
5. **Guardar** a definição de compilação.

    ![Definição de compilação](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configurar as variáveis de compilação
Para simplificar os parâmetros da linha de comandos, encapsula os valores de chave que orientam a fábrica de imagem a um conjunto de variáveis de compilação. Selecione o **variáveis** separador e verá uma lista de várias variáveis de predefinição. Eis a lista de variáveis de introduzir no DevOps do Azure:


| Nome da variável | Value | Notas |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Este é o caminho completo no repositório para o **configuração** pasta. Se tiver importado o repositório todo acima, o valor para a esquerda está correto. Caso contrário, atualize para apontar para a localização de configuração. |
| DevTestLabName | MyImageFactory | O nome do laboratório no Azure DevTest Labs, se for utilizado como a fábrica para produzir imagens. Se não tiver uma, crie uma. Certifique-se de que o laboratório está na mesma subscrição que o ponto final de serviço tem acesso. |
| ImageRetention | 1 | O número de imagens que pretende guardar de cada tipo. Defina o valor predefinido como 1. |
| MachinePassword | ******* | A senha da conta de administrador incorporada para as máquinas virtuais. Esta é uma conta transitória, por isso, certifique-se de que ele é seguro. Selecione o ícone de cadeado pouco à direita para garantir que é uma cadeia segura. |
| MachineUserName | ImageFactoryUser | O administrador incorporado conta nome de utilizador para as máquinas virtuais. Esta é uma conta transitória. |
| StandardTimeoutMinutes | 30 | O tempo limite que Deveríamos esperar regulares de operações do Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | O ID da subscrição em que o laboratório existe e que o ponto final de serviço tem acesso ao. |
| VMSize | Standard_A3 | O tamanho da máquina virtual a utilizar para o **criar** passo. As VMs criadas são transitórias. O tamanho deve ser o isso [ativada para o laboratório](devtest-lab-set-lab-policy.md). Confirme se existe suficiente [quota de núcleos de subscrição](../azure-subscription-service-limits.md). 

![Criar variáveis](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Ligar ao Azure
A próxima etapa é configurar o serviço principal. Esta é uma identidade no Azure Active Directory permite que o agente de compilação do DevOps operar no Azure em nome do utilizador. Para configurar, comece por adicionar primeiro Azure PowerShell criar passo.

1. Selecione **adicionar tarefas**.
2. Procure **do Azure PowerShell**. 
3. Depois de encontrá-lo, selecione **adicionar** para adicionar a tarefa para a compilação. Ao fazê-lo, verá a tarefa aparecem no lado esquerdo, como adicionado.

![Configurar o passo de PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

A forma mais rápida para configurar um serviço principal é permitir que o Azure DevOps fazê-lo para nós. 

1. Selecione o **tarefa** acabou de adicionar.
2. Para **tipo de ligação do Azure**, escolha **do Azure Resource Manager**. 
3. Selecione o **gerir** ligação para configurar o principal de serviço. 

Para obter mais informações, consulte esta [mensagem de blogue](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Quando seleciona a **gerir** link, acessará no lugar certo em DevOps (segunda captura de ecrã na mensagem de blogue) para configurar a ligação para o Azure. Certifique-se de escolher **ponto final de serviço do Azure Resource Manager** quando esta configuração.

## <a name="complete-the-build-task"></a>Concluir a tarefa de compilação
Se selecionar a tarefa de compilação, verá todos os detalhes no painel direito, que deve ser preenchido. 

1. Nome em primeiro lugar, a tarefa de compilação: **Criar máquinas virtuais**. 
2. Escolha o **principal de serviço** que criou ao escolher **do Azure Resource Manager**
3. Escolha o **ponto final de serviço**. 
4. Para **caminho do Script**, selecione **... (reticências)**  à direita.
5. Navegue para **MakeGoldenImageVMs.ps1** script. 
6. Parâmetros do script devem ter este aspeto: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Concluir a definição de compilação](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>A compilação de fila
Vamos verificar se tem tudo configurado corretamente ao colocar uma nova compilação. Enquanto estiver a executar a compilação, mude para o [portal do Azure](https://portal.azure.com) e selecione no **todas as máquinas virtuais** em seu laboratório de fábrica de imagem para confirmar que tudo o que está a funcionar corretamente. Deverá ver três máquinas virtuais são criadas no laboratório.

![VMs do laboratório](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Passos Seguintes 
A primeira etapa na configuração de fábrica de imagem com base no Azure DevTest Labs está concluída. No próximo artigo da série, obtém as VMs generalizadas e guardada em imagens personalizadas. Em seguida, tivê-los distribuídas para todos os seus outros laboratórios. Consulte o seguinte artigo da série: [Guardar imagens personalizadas e distribuir a vários laboratórios](image-factory-save-distribute-custom-images.md).
