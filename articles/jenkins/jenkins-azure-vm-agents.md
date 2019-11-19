---
title: Dimensionar implementações do Jenkins com agentes de VM do Azure
description: Utilize as máquinas virtuais do Azure com o plug-in Agente de VM do Azure para adicionar mais capacidade aos seus pipelines do Jenkins .
keywords: jenkins, azure, devops, virtual machine, agents
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 2e811d628c017316a5bc50a8ddc22ee24d6f744e
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158529"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Dimensionar as suas implementações do Jenkins para satisfazer a procura com agentes de VM do Azure

Este tutorial mostra como utilizar o [plug-in Agente de VM do Azure](https://plugins.jenkins.io/azure-vm-agents) para adicionar capacidade a pedido com máquinas virtuais do Linux em execução no Azure.

Neste tutorial, irá:

> [!div class="checklist"]
> * Instalar o plug-in Agentes de VM do Azure
> * Configurar o plug-in para criar recursos na sua subscrição do Azure
> * Definir os recursos de computação disponíveis para cada agente
> * Definir o sistema operativo e as ferramentas instaladas em cada agente
> * Criar um trabalho de compilação para fins gerais novo no Jenkins
> * Executar o trabalho num agente de VM do Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.
* Um servidor mestre do Jenkins. Se não o tiver, veja o [início rápido](install-jenkins-solution-template.md) para o configurar no Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Plugin dos agentes de VM do Azure para Jenkins

> [!TIP]
> Se tiver utilizado o [modelo de solução](install-jenkins-solution-template.md) para implementar o Jenkins no Azure, o plug-in Agente de VM do Azure já está instalado.

1. A partir do dashboard do Jenkins, selecione **Manage Jenkins** (Gerir Jenkins) e **Manage Plug-ins** (Gerir Plug-ins).
1. Selecione o separador **Available** (Disponível) e procure por **Azure VM Agents** (Agentes de VM do Azure). Selecione a caixa de verificação junto à entrada do plug-in e selecione **Install without restart** (Instalar sem reiniciar), na parte inferior do dashboard.

## <a name="configure-the-azure-vm-agents-plugin"></a>Configurar o plug-in Agentes de VM do Azure

1. A partir do dashboard do Jenkins, selecione **Manage Jenkins** (Gerir Jenkins) e **Configure System** (Configurar Sistema).
1. Desloque-se para a parte inferior da página e localize a secção **Cloud** com o menu  **Add new cloud** (Adicionar cloud nova) e escolha **Microsoft Azure VM Agents** (Agentes de VM do Microsoft Azure).
1. Selecione um principal de serviço já existente no menu pendente **Add** (Adicionar), na secção **Azure Credentials** (Credenciais do Azure). Se não aparecer nenhum, siga os passos abaixo para [criar um principal de serviço](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) para a sua conta do Azure e adicione-o à configuração do Jenkins:   

    a. selecione **Add** (Adicionar), junto a **Azure Credentials** (Credenciais do Azure), e escolha **Jenkins**.   
    b. Na caixa de diálogo **Add Credentials** (Adicionar Credenciais), selecione **Microsoft Azure Service Principal** (Principal de Serviço do Microsoft Azure), no menu pendente **Kind** (Tipo).   
    c. Crie um Principal de serviço do Active Directory na CLI do Azure ou no [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Introduza as credenciais do principal de serviço na caixa de diálogo **Add Credentials** (Adicionar credenciais). Se não souber o ID da sua subscrição do Azure, pode consultá-lo na CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    O principal de serviço concluído deverá utilizar o campo `id` para **Subscription ID** (ID da Subscrição), o valor `appId` para **Client ID** (ID do Cliente), `password` para **Client Secret** (Segredo do Cliente) e `tenant` para **Tenant ID** (ID do Inquilino). Selecione **Add** (Adicionar) para adicionar o principal de serviço e, depois, configure o plug-in para utilizar a credencial acabada de criar.

    ![Configurar o Principal de Serviço do Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

1. Na secção **Resource Group Name** (Nome do Grupo de Recursos), deixe **Create new** (Criar novo) selecionado e introduza `myJenkinsAgentGroup`.
1. Selecione **Verify configuration** (Verificar configuração) para ligar ao Azure e testar as definições do perfil.
1. Selecione **Apply** (Aplicar) para atualizar a configuração do plug-in.

## <a name="configure-agent-resources"></a>Configurar os recursos dos agentes

Configure um modelo para ser utilizado na definição de um agente de VM do Azure. Este modelo define os recursos de computação que cada agente criou.

1. Selecione **Add** (Adicionar), junto a **Add Azure Virtual Machine Template** (Adicionar Modelo de Máquina Virtual do Azure).
1. Introduza `defaulttemplate` em **Name** (Nome)
1. Introduza `ubuntu` em **Label** (Etiqueta)
1. Selecione a [região do Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) desejada, na caixa de combinação.
1. Selecione um [Tamanho de VM](/azure/virtual-machines/linux/sizes) no menu pendente **Virtual Machine Size** (Tamanho de Máquina virtual). Para este tutorial, o tamanho `Standard_DS1_v2` para fins gerais é suficiente.   
1. Deixe **Retention time** (Período de retenção) como `60`. Esta definição define o número de minutos que o Jenkins pode aguardar antes de desalocar agentes inativos. Especifique 0 se não pretender que os agentes inativos sejam removidos automaticamente.

   ![Configuração de VM geral](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Configurar o sistema operativo e as ferramentas do agente

Na secção **Image Configuration** (Configuração da imagem) da configuração do plug-in, selecione **Ubuntu 16.04 LTS**. Assinale as caixas junto a **Install Git (Latest)** (Instalar Git [Mais recente]), **Install Maven (V3.5.0)** (Instalar Maven (V3.5.0) e **Install Docker** (Instalar Docker) para instalar estas ferramentas nos agentes acabados de criar.

![Configurar o SO e as ferramentas da VM](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Selecione **Add** (Adicionar), junto a **Admin Credentials** (Credenciais de Administrador), e selecione **Jenkins**. Introduza um nome de utilizador e uma palavra-passe utilizados para iniciar sessão nos agentes, confirmando que cumprem a [política de nomes de utilizador e palavras-passe](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) para as contas administrativas nas VMs do Azure.

Selecione **Verify Template** (Verificar Modelo) para verificar a configuração e selecione **Save** (Guardar) para guardar as alterações e voltar ao dashboard do Jenkins.

## <a name="create-a-job-in-jenkins"></a>Criar uma tarefa no Jenkins

1. No painel do Jenkins, clique em **Novo item**. 
1. Introduza `demoproject1` no nome, selecione **Freestyle project** (Projeto para fins gerais) e selecione **OK**.
1. No separador **General** (Geral), selecione **Restrict where project can be run** (“Restringir onde o projeto pode ser executado") e escreva `ubuntu` em **Label Expression** (Expressão da Etiqueta). Verá uma mensagem a confirmar que a etiqueta é fornecida pela configuração de cloud criada no passo anterior. 
   ![Configurar trabalho](./media/jenkins-azure-vm-agents/job-config.png)
1. No separador **Source Code Management** (Gestão de Código de Origem), selecione **Git** e adicione o seguinte URL ao campo **Repository URL** (URL do Repositório): `https://github.com/spring-projects/spring-petclinic.git`
1. No separador **Build** (Compilar), selecione **Add build step** (Adicionar passo de compilação) e **Invoke top-level Maven targets** (Invocar destinos de Maven de nível superior). Introduza `package` no campo **Goals** (Objetivos).
1. Selecione **Save** (Guardar) para guardar a definição do trabalho.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Compilar o trabalho novo num agente de VM do Azure

1. Volte ao painel do Jenkins.
1. Selecione o trabalho que criou no passo anterior e clique em **Build now** (Compilar agora). É colocada em fila uma compilação nova. Contudo, não é iniciada enquanto não for criada uma VM do agente na sua subscrição do Azure.
1. Quando a compilação estiver concluída, vá para **Saída da consola**. Verá que a compilação foi executada remotamente num agente do Azure.

![Saída da consola](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [CI/CD to Azure App Service](java-deploy-webapp-tutorial.md) (CI/CD no Serviço de Aplicações do Azure)