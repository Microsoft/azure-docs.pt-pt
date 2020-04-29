---
title: Gereuma fábrica de imagem da Azure DevOps em Azure DevTest Labs
description: Este artigo abrange todos os preparativos necessários para gerir a fábrica de imagem da Azure DevOps (antiga Visual Studio Team Services).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76758687"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Executar uma fábrica de imagens a partir do Azure DevOps
Este artigo abrange todos os preparativos necessários para gerir a fábrica de imagem da Azure DevOps (antiga Visual Studio Team Services).

> [!NOTE]
> Qualquer motor de orquestração funcionará! Azure DevOps não é obrigatório. A fábrica de imagem é executada utilizando scripts Azure PowerShell, para que possa ser executada manualmente, utilizando o Windows Task Scheduler, outros sistemas CI/CD, e assim por diante.

## <a name="create-a-lab-for-the-image-factory"></a>Criar um laboratório para a fábrica de imagem
O primeiro passo para a criação da fábrica de imagem é criar um laboratório em Azure DevTest Labs. Este laboratório é o laboratório de fábrica de imagem onde criamos as máquinas virtuais e guardamos imagens personalizadas. Este laboratório é considerado como parte do processo geral de fábrica de imagem. Assim que criar um laboratório, certifique-se de guardar o nome, já que vai precisar mais tarde.

## <a name="scripts-and-templates"></a>Scripts e modelos
O próximo passo na adoção da fábrica de imagem para a sua equipa é perceber o que está disponível. Os scripts e modelos da fábrica de imagem estão disponíveis publicamente no [DevTest Labs GitHub Repo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Aqui está um esboço das peças:

- Fábrica de Imagem. É a pasta de raiz.
    - A configuração. As inputs para a fábrica de imagem
        - GoldenImages. Esta pasta contém ficheiros JSON que representam as definições de imagens personalizadas.
        - Labs.json. Arquivo onde as equipas se inscrevem para receber imagens personalizadas específicas.
- Os guiões. O motor da fábrica de imagem.

Os artigos desta secção fornecem mais detalhes sobre estes scripts e modelos.

## <a name="create-an-azure-devops-team-project"></a>Criar um projeto de equipa Azure DevOps
A Azure DevOps permite-lhe armazenar o código fonte, executar o Azure PowerShell num só local. Pode agendar execuções recorrentes para manter as imagens atualizadas. Existem boas instalações para registar os resultados para diagnosticar quaisquer problemas.  Usar o Azure DevOps não é um requisito, no entanto, pode utilizar qualquer arnês/motor que possa ligar-se ao Azure e pode executar o Azure PowerShell.

Se tiver uma conta ou projeto DevOps existente que gostaria de utilizar, ignore este passo.

Para começar, crie uma conta gratuita em Azure DevOps. Visite https://www.visualstudio.com/ e selecione **Começar de forma gratuita** sob O **Azure DevOps** (anteriormente VSTS). Terá de escolher um nome de conta único e certifique-se de escolher gerir o código usando git. Assim que isto for criado, guarde o URL para o seu projeto de equipa. Aqui está uma `https://<accountname>.visualstudio.com/MyFirstProject`URL de amostra: .

## <a name="check-in-the-image-factory-to-git"></a>Check in the image factory to Git
Todos os PowerShell, modelos e configuração para a fábrica de imagem estão localizados no [público DevTest Labs GitHub repo](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). A maneira mais rápida de entrar no código no seu novo projeto de equipa é importar um repositório. Isto atrai todo o repositório de Laboratórios DevTest (para que você obtenha extra scs, e amostras).

1. Visite o projeto Azure DevOps que criou no passo anterior (URL parece **\//\<https: nome de conta>.visualstudio.com/MyFirstProject**).
2. **Selecione Importar um Repositório**.
3. Introduza o URL do **clone** para o `https://github.com/Azure/azure-devtestlab`Repo DevTest Labs Repo: .
4. Selecione **Importar**.

    ![Importação Git repo](./media/set-up-devops-lab/import-git-repo.png)

Se decidir apenas verificar o que é necessário (os ficheiros da fábrica de imagem), siga os passos [aqui](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) para clonar o repo Git e empurre apenas os ficheiros localizados no **diretório scripts/ImageFactory.**

## <a name="create-a-build-and-connect-to-azure"></a>Criar uma construção e ligar-se ao Azure
Neste ponto, tem os ficheiros de origem armazenados num repo Git em Azure DevOps. Agora, tens de montar um oleoduto para gerir o Azure PowerShell. Há muitas opções para fazer estes passos. Neste artigo, utiliza definição de construção para simplicidade, mas funciona com a DevOps Build, DevOps Release (ambientes individuais ou múltiplos), outros motores de execução como o Windows Task Scheduler ou qualquer outro arnês que possa executar o Azure PowerShell.

> [!NOTE]
> Um ponto importante a ter em mente que alguns dos ficheiros PowerShell demoram muito tempo a ser executados quando há muitas (10+) imagens personalizadas para criar. Os agentes free hosted DevOps Build/Release têm um tempo de 30 min, por isso não pode usar o agente hospedado gratuitamente uma vez que você começar a construir muitas imagens. Este desafio de timeout aplica-se a qualquer arnês que decida usar, é bom verificar na frente que pode estender os tempos típicos para scripts Azure PowerShell de longa duração. No caso do Azure DevOps, pode usar agentes hospedeiros pagos ou usar o seu próprio agente de construção.

1. Para começar, selecione **Configurar Construir** na página inicial do seu Projeto DevOps:

    ![Configurar botão de construção](./media/set-up-devops-lab/setup-build-button.png)
2. Especifique um **nome** para a construção (por exemplo: Construir e Entregar Imagens aos Laboratórios DevTest).
3. Selecione uma definição de construção **vazia** e selecione **Aplicar** para criar a sua construção.
4. Nesta fase, você pode escolher **Hospedado** para o agente de construção.
5. **Salve** a definição de construção.

    ![Definição de compilação](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Configure as variáveis de construção
Para simplificar os parâmetros da linha de comando, encapsular os valores-chave que conduzem a fábrica de imagem a um conjunto de variáveis de construção. Selecione o separador **Variáveis** e verá uma lista de várias variáveis predefinidas. Aqui está a lista de variáveis para entrar no Azure DevOps:


| Nome da Variável | Valor | Notas |
| ------------- | ----- | ----- |
| Localização de Configuração | /Scripts/ImageFactory/Configuração | Este é o caminho completo no repositório para a pasta **Configuração.** Se importou todo o repo acima, o valor para a esquerda está correto. Caso contrário, atualize para apontar para a localização de Configuração. |
| DevTestLabName | Fábrica myImage | O nome do laboratório em Azure DevTest Labs usado como fábrica para produzir imagens. Se não tem um, crie um. Certifique-se de que o Laboratório está na mesma subscrição a que o ponto final do serviço tem acesso. |
| Retenção de Imagens | 1 | O número de imagens que pretende guardar de cada tipo. Definir valor padrão para 1. |
| Palavra-passe-passe-passe- | ******* | A senha de conta de administração incorporada para as máquinas virtuais. Esta é uma conta transitória, então certifique-se de que é seguro. Selecione o pequeno ícone de bloqueio à direita para garantir que é uma corda segura. |
| Nome user user | ImagemFactoryUser | O nome de utilizador da conta de administração incorporada para as máquinas virtuais. Esta é uma conta transitória. |
| StandardTimeoutMinutes | 30 | O tempo de tempo que devemos esperar pelas operações regulares do Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | A identificação da assinatura onde o laboratório existe e a que o ponto final do serviço tem acesso. |
| VMSize | Standard_A3 | O tamanho da máquina virtual para usar para o passo **Criar.** Os VMs criados são transitórios. O tamanho deve ser o que está [habilitado para o laboratório.](devtest-lab-set-lab-policy.md) Confirme que há quotas de núcleos de [subscrição](../azure-resource-manager/management/azure-subscription-service-limits.md)suficientes.

![Construir variáveis](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Ligar ao Azure
O próximo passo é criar o diretor de serviço. Esta é uma identidade no Azure Ative Directory que permite ao agente de construção DevOps operar em Azure em nome do utilizador. Para configurar, comece por adicionar o primeiro Passo de Construção De PowerShell Azure.

1. **Selecione Adicionar Tarefa**.
2. Pesquisa por **Azure PowerShell**.
3. Assim que o encontrar, selecione **Adicionar** para adicionar a tarefa à construção. Quando fizer isto, verá que a tarefa aparece no lado esquerdo, como adicionado.

![Configurar o passo PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

A maneira mais rápida de criar um diretor de serviço é deixar o Azure DevOps fazê-lo por nós.

1. Selecione a **tarefa** que acabou de adicionar.
2. Para o tipo de **ligação Azure,** escolha **O Gestor de Recursos Azure**.
3. Selecione o link **'Gerir'** para configurar o diretor de serviço.

Para mais informações, consulte esta [publicação de blogue](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Quando selecionar o link **Manage,** aterre no lugar certo em DevOps (segunda imagem no post do blog) para configurar a ligação ao Azure. Certifique-se de escolher o ponto final do serviço do Gestor de **Recursos Azure** ao configurar este ponto.

## <a name="complete-the-build-task"></a>Complete a tarefa de construção
Se selecionar a tarefa de construção, verá todos os detalhes no painel certo que devem ser preenchidos.

1. Em primeiro lugar, nomeie a tarefa de construção: **Criar Máquinas Virtuais**.
2. Escolha o **principal de serviço** que criou ao escolher o Gestor de Recursos **Azure**
3. Escolha o **ponto final**do serviço .
4. Para **script path,** selecione **... (elipse)** à direita.
5. Navegue para **makeGoldenImageVMs.ps1** script.
6. Os parâmetros do script devem ser assim:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Complete a definição de construção](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Fila da construção
Vamos verificar se tem tudo preparado corretamente, fazendo fila para uma nova construção. Enquanto a construção estiver em execução, mude para o [portal Azure](https://portal.azure.com) e selecione em **Todas as Máquinas Virtuais** no seu laboratório de fábrica de imagem para confirmar que está tudo a funcionar corretamente. Deviaver três máquinas virtuais ser criadas no laboratório.

![VMs no laboratório](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Passos seguintes
O primeiro passo para a criação da fábrica de imagem baseada em Azure DevTest Labs está completo. No próximo artigo da série, você obtém esses VMs generalizados e guardados para imagens personalizadas. Depois, distribui-os para todos os outros laboratórios. Veja o próximo artigo da série: [Guarde imagens personalizadas e distribua para vários laboratórios](image-factory-save-distribute-custom-images.md).
