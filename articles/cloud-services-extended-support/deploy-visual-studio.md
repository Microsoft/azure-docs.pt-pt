---
title: Utilizar serviços cloud (suporte alargado) (Visualização)
description: Aprenda agora a criar e implementar um Azure Cloud Service usando O Gestor de Recursos Azure com Visual Studio
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 1c2264cb76e062f5f22c0460572ec01750086a04
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745076"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Criar e implementar um Azure Cloud Service (suporte alargado) utilizando o Visual Studio

Começando pela versão 16.9 Preview 1 do Visual Studio 2019, pode trabalhar com a Cloud Services (suporte alargado) utilizando o Azure Resource Manager, o que simplifica e moderniza muito a manutenção e gestão dos recursos da Azure. Também pode converter um projeto de Cloud Service existente para um projeto de Serviço cloud de suporte alargado.

> [!IMPORTANT]
> Os Serviços cloud (suporte alargado) estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Registe a funcionalidade da sua subscrição
Os Serviços cloud (suporte alargado) estão atualmente em pré-visualização. Registe a funcionalidade da sua subscrição da seguinte forma:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Para obter mais informações consulte [Pré-requisitos para a implementação de Serviços cloud (suporte alargado)](deploy-prerequisite.md)

## <a name="create-a-project"></a>Criar um projeto

O Visual Studio fornece um modelo de projeto que permite criar um Azure Cloud Service com suporte alargado, denominado **Azure Cloud Service (suporte alargado)**. Um Serviço cloud é um serviço Azure de uso geral simples. Uma vez criado o projeto, o Visual Studio permite-lhe configurar, depurar e implementar o Serviço cloud para Azure.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Criar um projeto Azure Cloud Service (suporte alargado) no Visual Studio

Esta secção acompanha-o através da criação de um projeto Azure Cloud Service em Visual Studio com um ou mais papéis web.

1. A partir da janela de início, escolha **Criar um novo projeto.**

1. Na caixa de pesquisa, escreva em *Cloud,* e depois escolha **o Azure Cloud Service (suporte alargado)**.

   ![Novo Serviço Azure Cloud com suporte alargado](./media/choose-project-template.png)

1. Dê ao projeto um nome e escolha **Criar.**

   ![Dê um nome ao projeto](./media/configure-new-project.png)

1. No novo diálogo do **Serviço de Nuvem Azure** da Microsoft, selecione as funções que pretende adicionar e escolha o botão de seta direita para adicioná-los à sua solução.

    ![Selecione novas funções do Azure Cloud Service](./media/choose-roles.png)

1. Para renomear um papel que adicionou, paire sobre o papel no diálogo new **Microsoft Azure Cloud Service** e, a partir do menu de contexto, selecione **Rename**. Também pode renomear um papel dentro da sua solução (no **Solution Explorer)** depois de ter sido adicionado.

    ![Renomear papel do Serviço de Nuvem Azure](./media/new-cloud-service-rename.png)

O projeto Visual Studio Azure tem associações para os projetos de papel na solução. O projeto também inclui o *ficheiro de definição de serviço* e o ficheiro de *configuração de serviço:*

- **Ficheiro de definição** de serviço - Define as definições de tempo de execução para a sua aplicação, incluindo quais as funções necessárias, pontos finais e tamanho da máquina virtual.
- **Ficheiro de configuração** de serviço - Configura quantas instâncias de uma função são executadas e os valores das definições definidas para um papel.

Para obter mais informações sobre estes ficheiros, consulte [Configurar os Papéis para um Serviço de Nuvem Azure com Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Publicar um Serviço cloud

1. Crie ou abra um projeto do Azure Cloud Service no Visual Studio.

1. No **Solution Explorer,** clique à direita no projeto e, a partir do menu de contexto, selecione **Publicar**.

   ![Página de inscrição](./media/publish-step-1.png)

1. **Conta** - Selecione uma conta ou **selecione Adicionar uma conta** na lista de downdown da conta.

1. **Escolha a sua subscrição** - Escolha a subscrição para utilizar para a sua implementação. A subscrição que utiliza para implantar serviços cloud (suporte alargado) precisa de ter funções de Proprietário ou Contribuinte atribuídas através do controlo de acesso baseado em funções (RBAC). Se a sua subscrição não tiver nenhuma destas funções, consulte [Passos para adicionar uma atribuição](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-steps) de papel para adicioná-lo antes de prosseguir mais.

1. Escolha **o próximo** para mover para a página **Definições.**

   ![Configurações comuns](./media/publish-settings.png)

1. **Serviço em nuvem** - Utilizando o dropdown, selecione um Serviço cloud existente ou selecione **Criar novo**, e crie um Serviço cloud. O centro de dados apresenta-se em parênteses para cada Serviço cloud. Recomenda-se que a localização do centro de dados para o Serviço cloud seja a mesma que a localização do centro de dados para a conta de armazenamento.

   Se optar por criar um novo Serviço cloud, verá o diálogo **Create Cloud Service (suporte alargado).** Especifique a localização e o grupo de recursos que pretende utilizar para o Serviço cloud.

   ![Criar um Serviço cloud com suporte alargado](./media/extended-support-dialog.png)

1. **Configuração de construção** - Selecione **Debug** ou **Release**.

1. **Configuração de serviço** - Selecione **Cloud** ou **Local**.

1. **Conta de armazenamento** - Selecione a conta de armazenamento para usar para esta implementação, ou **Crie nova** para criar uma conta de armazenamento. A região exibe parênteses para cada conta de armazenamento. Recomenda-se que a localização do centro de dados para a conta de armazenamento seja a mesma que a localização do centro de dados para o Serviço de Nuvem (Configurações Comuns).

   A conta de armazenamento Azure armazena o pacote para a implementação da aplicação. Após a implementação da aplicação, a embalagem é removida da conta de armazenamento.

1. **Key Vault** - Especifique o Cofre de Chaves que contém os segredos para este Serviço cloud. Isto é ativado se o ambiente de trabalho remoto estiver ativado ou se os certificados forem adicionados à configuração.

1. **Ativar o Ambiente de Trabalho Remoto para todas as funções** - Selecione esta opção se pretender ser capaz de ligar remotamente ao serviço. Pedir-lhe-ão que especifique as credenciais.

   ![Definições remotas de ambiente de trabalho](./media/remote-desktop-configuration.png)

1. Escolha **o próximo** para mover para a página de **definições de Diagnóstico.**

   ![Definições de diagnóstico](./media/diagnostics-settings.png)

   O diagnóstico permite-lhe resolver problemas num Serviço de Nuvem Azure (ou máquina virtual Azure). Para obter informações sobre diagnósticos, consulte [Configurar Diagnósticos para Serviços em Nuvem Azure e Máquinas Virtuais.](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) Para obter informações sobre o Application Insights, consulte [o que é Insights de Aplicação?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

1. Escolha o **próximo** para passar para a página **Resumo.**

   ![Resumo](./media/publish-summary.png)

1. **Perfil-alvo** - Pode optar por criar um perfil de publicação a partir das definições que escolheu. Por exemplo, pode criar um perfil para um ambiente de teste e outro para produção. Para guardar este perfil, escolha o ícone **Guardar.** O assistente cria o perfil e guarda-o no projeto Visual Studio. Para modificar o nome do perfil, abra a lista **de perfis target** e, em seguida, escolha **Gerir...**.

   > [!Note]
   > O perfil de publicação aparece no Solution Explorer em Visual Studio e as definições de perfil são escritas para um ficheiro com uma extensão .azurePubxml. As definições são guardadas como atributos de tags XML.

1. Assim que configurar todas as definições para a implantação do seu projeto, **selecione Publicar** na parte inferior do diálogo. Pode monitorizar o estado do processo na janela de saída do **Registo de Atividade Azure** no Estúdio Visual.

Parabéns! Publicou o seu projeto de Serviço Cloud de apoio alargado ao Azure. Para publicar novamente com as mesmas definições, pode reutilizar o perfil de publicação ou repetir estes passos para criar um novo.

## <a name="clean-up-azure-resources"></a>Limpe os recursos do Azure

Para limpar os recursos Azure que criou seguindo este tutorial, vá ao [portal Azure,](https://portal.azure.com)escolha **grupos de Recursos,** encontre e abra o grupo de recursos que utilizou para criar o serviço, e escolha **Delete grupo de recursos**.

## <a name="next-steps"></a>Próximos passos

Configure a integração contínua (CI) utilizando o **botão Configure** no ecrã **Publicar.** Para mais informações, consulte [a documentação da Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines).
