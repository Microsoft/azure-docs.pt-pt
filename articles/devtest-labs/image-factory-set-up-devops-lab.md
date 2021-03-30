---
title: Gerir uma fábrica de imagens da Azure DevOps em Azure DevTest Labs
description: Este artigo abrange todos os preparativos necessários para gerir a fábrica de imagens da Azure DevOps (anteriormente Visual Studio Team Services).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476143"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Executar uma fábrica de imagens a partir do Azure DevOps
Este artigo abrange todos os preparativos necessários para gerir a fábrica de imagens da Azure DevOps (anteriormente Visual Studio Team Services).

> [!NOTE]
> Qualquer motor de orquestração funcionará! Azure DevOps não é obrigatório. A fábrica de imagens é executada utilizando scripts Azure PowerShell, para que possa ser executada manualmente, utilizando o Windows Task Scheduler, outros sistemas CI/CD, etc.

## <a name="create-a-lab-for-the-image-factory"></a>Criar um laboratório para a fábrica de imagens
O primeiro passo para a criação da fábrica de imagens é criar um laboratório na Azure DevTest Labs. Este laboratório é o laboratório de fábrica de imagens onde criamos as máquinas virtuais e guardamos imagens personalizadas. Este laboratório é considerado como parte do processo geral de fábrica de imagem. Assim que criar um laboratório, certifique-se de guardar o nome, já que vai precisar dele mais tarde.

## <a name="scripts-and-templates"></a>Scripts e modelos
O próximo passo na adoção da fábrica de imagens para a sua equipa é perceber o que está disponível. Os scripts e modelos da fábrica de imagem estão disponíveis publicamente no [DevTest Labs GitHub Repo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Aqui está um esboço das peças:

- Fábrica de Imagem. É a pasta de raiz.
    - A configuração. As entradas para a fábrica de imagem
        - Imagens Douradas. Esta pasta contém ficheiros JSON que representam as definições de imagens personalizadas.
        - Labs.js. Arquivar onde as equipas se inscrevem para receber imagens personalizadas específicas.
- Os guiões. O motor da fábrica de imagens.

Os artigos nesta secção fornecem mais detalhes sobre estes scripts e modelos.

## <a name="create-an-azure-devops-team-project"></a>Criar um projeto de equipa do Azure DevOps
A Azure DevOps permite-lhe armazenar o código fonte, executar o Azure PowerShell num só local. Pode agendar corridas recorrentes para manter as imagens atualizadas. Existem boas instalações para registar os resultados para diagnosticar quaisquer problemas.  A utilização de Azure DevOps não é um requisito, no entanto, pode utilizar qualquer arnês/motor que possa ligar-se ao Azure e possa executar a Azure PowerShell.

Se tiver uma conta ou projeto de DevOps existente que gostaria de utilizar, ignore este passo.

Para começar, crie uma conta gratuita em Azure DevOps. Visite https://www.visualstudio.com/ e **selecione Inicie-se gratuitamente** sob **Azure DevOps** (anteriormente VSTS). Terá de escolher um nome de conta único e certifique-se de escolher gerir o código usando o Git. Uma vez criado, guarde o URL para o seu projeto de equipa. Aqui está uma amostra de URL: `https://<accountname>.visualstudio.com/MyFirstProject` .

## <a name="check-in-the-image-factory-to-git"></a>Check in the image factory to Git
Todos os PowerShell, modelos e configuração para a fábrica de imagem estão localizados no [repo de DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). A maneira mais rápida de colocar o código no seu novo projeto de equipa é importar um repositório. Isto atrai todo o repositório de Laboratórios DevTest (para que você obtenha mais médicos e amostras).

1. Visite o projeto Azure DevOps que criou no passo anterior (URL parece **https: \/ / \<accountname> .visualstudio.com/MyFirstProject**).
2. **Selecione Importar um Repositório**.
3. Introduza o **URL do clone** para o DevTest Labs Repo: `https://github.com/Azure/azure-devtestlab` .
4. Selecione **Import** (Importar).

    ![Import Git repo](./media/set-up-devops-lab/import-git-repo.png)

Se decidir apenas fazer o check-in exatamente o que é necessário (os ficheiros da fábrica de imagens), siga os passos [aqui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) para clonar o repo Git e empurre apenas os **ficheiros localizados nos scripts/diretório ImageFactory.**

## <a name="create-a-build-and-connect-to-azure"></a>Criar uma construção e conectar-se ao Azure
Neste momento, tem os ficheiros de origem armazenados num git repo em Azure DevOps. Agora, tens de preparar um oleoduto para executar o Azure PowerShell. Há muitas opções para fazer estes passos. Neste artigo, utiliza-se uma definição de construção para a simplicidade, mas funciona com DevOps Build, DevOps Release (ambientes únicos ou múltiplos), outros motores de execução como o Windows Task Scheduler ou qualquer outro arnês que possa executar a Azure PowerShell.

> [!NOTE]
> Um ponto importante a ter em mente que alguns dos ficheiros PowerShell demoram muito tempo a ser executados quando há muitas (10+) imagens personalizadas para criar. Os agentes gratuitos de DevOps Build/Release têm um tempo limite de 30 min, por isso não pode usar o agente hospedado gratuitamente assim que começar a construir muitas imagens. Este desafio de timeout aplica-se a qualquer arnês que você decide usar, é bom verificar na frente que você pode estender os intervalos típicos para scripts Azure PowerShell de longa duração. No caso da Azure DevOps, pode utilizar agentes hospedados pagos ou utilizar o seu próprio agente de construção.

1. Para começar, selecione **Configurar Construir** na página inicial do seu Projeto DevOps:

    ![Botão de construção de configuração](./media/set-up-devops-lab/setup-build-button.png)
2. Especifique um **nome** para a construção (por exemplo: Construir e Entregar Imagens à DevTest Labs).
3. Selecione uma definição de construção **vazia** e selecione **Aplicar** para criar a sua construção.
4. Nesta fase, você pode escolher **Hosted** para o agente de construção.
5. **Salve** a definição de construção.

    ![Definição de compilação](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configurar as variáveis de construção
Para simplificar os parâmetros da linha de comando, encapsular os valores-chave que conduzem a fábrica de imagem a um conjunto de variáveis de construção. Selecione o separador **Variáveis** e verá uma lista de várias variáveis padrão. Aqui está a lista de variáveis para entrar no Azure DevOps:


| Nome da Variável | Valor | Notas |
| ------------- | ----- | ----- |
| ConfiguraçãoLocalização | /Scripts/ImageFactory/Configuration | Este é o caminho completo no repositório da pasta **de Configuração.** Se importou todo o repo acima, o valor para a esquerda está correto. Caso contrário, atualize para indicar a localização da Configuração. |
| DevTestLabName | MyImageFactory | O nome do laboratório em Azure DevTest Labs usado como fábrica para produzir imagens. Se não tem um, crie um. Certifique-se de que o Laboratório está na mesma subscrição a que o ponto final de serviço tem acesso. |
| Retenção de Imagem | 1 | O número de imagens que pretende guardar de cada tipo. Desafine o valor predefinido para 1. |
| Palavra-máquinaPassword | ******* | A senha de conta de administração incorporada para as máquinas virtuais. Esta é uma conta transitória, então certifique-se de que é segura. Selecione o pequeno ícone de bloqueio à direita para se certificar de que é uma corda segura. |
| Nome de MachineUser | ImageFactoryUser | O nome de utilizador da conta de administração incorporada para as máquinas virtuais. Esta é uma conta transitória. |
| StandardTimeoutMinutes | 30 | O tempo limite devemos esperar pelas operações regulares do Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | A identificação da subscrição onde o laboratório existe e a que o ponto final de serviço tem acesso. |
| VMSize | Standard_A3 | O tamanho da máquina virtual para usar para o passo **Criar.** Os VMs criados são transitórios. O tamanho deve ser o que está [habilitado para o laboratório.](devtest-lab-set-lab-policy.md) Confirme que há uma quota de [núcleos](../azure-resource-manager/management/azure-subscription-service-limits.md)de assinatura suficiente.

![Construir variáveis](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Ligar ao Azure
O próximo passo é criar o diretor de serviço. Esta é uma identidade no Azure Ative Directory que permite ao agente de construção de DevOps operar em Azure em nome do utilizador. Para o configurar, comece por adicionar-lhe o primeiro Passo de Construção Azure PowerShell.

1. Selecione **Adicionar Tarefa**.
2. Procure **por Azure PowerShell**.
3. Assim que o encontrar, **selecione Adicionar** para adicionar a tarefa à construção. Quando o fizeres, verás que a tarefa aparece no lado esquerdo, tal como foi adicionado.

![Configurar o passo PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

A maneira mais rápida de criar um diretor de serviço é deixar a Azure DevOps fazê-lo por nós.

1. Selecione a **tarefa** que adicionou.
2. Para **o tipo de ligação Azure**, escolha O Gestor de Recursos **Azure**.
3. Selecione o link **Gerir** para configurar o principal de serviço.

Para mais informações, consulte esta [publicação de blogue](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Quando selecionar o link **Manage,** aterrará no lugar certo em DevOps (segunda imagem no post de blog) para configurar a ligação ao Azure. Certifique-se de escolher **o Ponto final do Serviço de Gestão de Recursos Azure** ao configurar isto.

## <a name="complete-the-build-task"></a>Complete a tarefa de construção
Se selecionar a tarefa de construção, verá todos os detalhes no painel certo que deve ser preenchido.

1. Primeiro, nomeie a tarefa de construção: **Criar Máquinas Virtuais**.
2. Escolha o **principal de serviço** que criou escolhendo o Gestor de Recursos **Azure**
3. Escolha o **ponto final** de serviço .
4. Para **Caminho do Script**, selecione **... (elipse)** à direita.
5. Navegue para **MakeGoldenImageVMs.ps1** roteiro.
6. Os parâmetros do script devem ser assim: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Complete a definição de construção](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Fila da construção
Vamos verificar se tem tudo preparado corretamente, fazendo fila para uma nova construção. Enquanto a construção está em execução, mude para o [portal Azure](https://portal.azure.com) e selecione em **Todas as Máquinas Virtuais** no seu laboratório de fábrica de imagens para confirmar que tudo está a funcionar corretamente. Devias ver três máquinas virtuais a ser criadas no laboratório.

![VMs no laboratório](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Passos seguintes
O primeiro passo para a criação da fábrica de imagens com base na Azure DevTest Labs está completo. No próximo artigo da série, obtém-se os VMs generalizados e guardados para imagens personalizadas. Depois, distribui-os a todos os outros laboratórios. Veja o próximo artigo da série: [Guarde imagens personalizadas e distribua para vários laboratórios](image-factory-save-distribute-custom-images.md).
