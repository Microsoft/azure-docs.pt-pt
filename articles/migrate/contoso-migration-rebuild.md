---
title: Recriar uma aplicação no local da Contoso para o Azure | Documentos da Microsoft
description: Saiba como Contoso recria uma aplicação no Azure através dos serviços de aplicações do Azure, o Kubernetes service, cosmos DB, as funções do Azure e os serviços cognitivos.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: c0e953434e947703308ff8d796107838df8cc979
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437319"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migração de Contoso: Recriar uma aplicação no local para o Azure

Este artigo demonstra como Contoso migra e recria a aplicação da SmartHotel360 no Azure. Contoso migra front-end VM da aplicação para aplicações Web de serviços de aplicações do Azure. O back-end de aplicação é criado usando microsserviços implementados para os contentores geridos pelo Azure Kubernetes Service (AKS). O site interage com as funções do Azure para fornecer a funcionalidade de estimação de fotos. 

Este documento é um de uma série de artigos que mostram como a empresa fictícia Contoso migra recursos no local para a cloud do Microsoft Azure. A série contém informações gerais e cenários que ilustram como configurar uma infraestrutura de migração, avaliação dos recursos no local para migração e execução de diferentes tipos de migrações. Cenários de crescem em complexidade. Vamos adicionar artigos adicionais ao longo do tempo.


**Artigo** | **Detalhes** | **Estado**
--- | --- | ---
[Artigo 1: Descrição geral](contoso-migration-overview.md) | Fornece uma descrição geral da estratégia de migração da Contoso, a série de artigos e as aplicações de exemplo que usamos. | Disponível
[Artigo 2: Implementar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como o Contoso prepara a sua infraestrutura do Azure e no local, para a migração. A mesma infra-estrutura é utilizada para todos os artigos de migração. | Disponível
[Artigo 3: Avalie os recursos no local](contoso-migration-assessment.md)  | Mostra como Contoso é executado uma avaliação de uma aplicação da SmartHotel360 de duas camadas no local em execução no VMware. Contoso avalia as VMs de aplicação com o [do Azure Migrate](migrate-overview.md) serviço e a base de dados do SQL Server do aplicativo com o [Assistente de migração de base de dados](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[Artigo 4: Realojar a uma aplicação em VMs do Azure e uma instância gerida de SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como Contoso é executado uma migração lift-and-shift para o Azure para a aplicação da SmartHotel360. Contoso migra o front-end de aplicação VM através de [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)e a base de dados de aplicação para a instância gerida de SQL, utilizando o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
[5 do artigo: Realojar a uma aplicação em VMs do Azure](contoso-migration-rehost-vm.md) | Mostra como Contoso migrar a aplicação da SmartHotel360 VMs com apenas o Site Recovery. | Disponível
[Artigo 6: Uma aplicação para as VMs do Azure e o SQL Server sempre no grupo de disponibilidade de realojamento](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra a aplicação da SmartHotel360. A Contoso utiliza o Site Recovery para migrar as VMs da aplicação e o serviço de migração de base de dados para migrar a base de dados de aplicação para um cluster do SQL Server protegido por um grupo de Disponibilidade AlwaysOn. | Disponível
[Artigo 7: Realojar uma aplicação do Linux em VMs do Azure](contoso-migration-rehost-linux-vm.md) | Mostra como Contoso faz uma migração lift-and-shift da aplicação de osTicket do Linux para VMs do Azure, com o Site Recovery | Disponível
[Artigo 8: Realojar uma aplicação do Linux em VMs do Azure e o servidor MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra a aplicação de osTicket do Linux para as VMs do Azure com o Site Recovery e migra a base de dados de aplicação para uma instância do servidor MySQL do Azure com o MySQL Workbench. | Disponível
[Artigo 9: Refatorizar uma aplicação na base de dados de aplicações Web do Azure e SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Demonstra como Contoso migra a aplicação da SmartHotel360 para uma aplicação Web do Azure e migra a base de dados de aplicação para a instância de servidor SQL do Azure | Disponível
[Artigo 10: Refatorizar uma aplicação do Linux para aplicações Web do Azure e MySQL do Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra a aplicação de osTicket do Linux para aplicações Web do Azure em vários sites, integrados com o GitHub para a entrega contínua. Estes migrar a base de dados de aplicação para uma instância do MySQL do Azure. | Disponível
[Artigo 11: Refatorar o TFS em serviços do Azure DevOps](contoso-migration-tfs-vsts.md) | Mostra como a Contoso migra a implantação do Team Foundation Server (TFS) no local ao migrar para serviços do Azure DevOps no Azure. | Disponível
[Artigo 12: Rearquitetar uma aplicação para contentores do Azure e base de dados SQL](contoso-migration-rearchitect-container-sql.md) | Mostra como Contoso migra e rearchitects a respetiva aplicação SmartHotel para o Azure. Eles rearquitetar a camada de web de aplicação como um contentor do Windows e a base de dados de aplicação numa base de dados SQL do Azure. | Disponível
Artigo 13: Recriar uma aplicação para o Azure | Mostra como Contoso reconstruir a sua aplicação de SmartHotel com uma gama de capacidades do Azure e serviços, incluindo serviços de aplicações, Kubernetes do Azure, as funções do Azure, serviços cognitivos e Cosmos DB. | Este artigo
[Artigo 14: Dimensionar uma migração para o Azure](contoso-migration-scale.md) | Depois de a experimentar combinações de migração, o Contoso se prepara para dimensionar para uma migração completa para o Azure. | Disponível

Neste artigo, a Contoso migra o Windows de duas camadas. Aplicação de NET SmartHotel360 em execução em VMs de VMware para o Azure. Se gostaria de utilizar esta aplicação, são fornecidas como código-fonte aberto e pode baixá-lo partir [GitHub](https://github.com/Microsoft/SmartHotel360-Backend).

## <a name="business-drivers"></a>Fatores comerciais

A equipe de liderança de TI tem trabalhado em estreita colaboração com parceiros de negócios para compreender o que quer alcançar com esta migração:

- **Resolver o crescimento do negócio**: Contoso está a crescer e deseja fornecer experiências diferenciadas para os clientes nos Web sites de Contoso.
- **Agilidade**: Contoso deve ser capaz de reagir mais rapidamente do que as alterações no marketplace, para ativar o sucesso numa economia global. 
- **Dimensionamento**: À medida que a empresa cresce com êxito, a equipe de TI da Contoso tem de fornecer sistemas que são capazes de aumentar ao ritmo mesmo.
- **Os custos**: Contoso quer minimizar os custos de licenciamento.

## <a name="migration-goals"></a>Objetivos de migração

A equipe de cloud de Contoso tiver afixado para baixo de requisitos de aplicações para essa migração. Esses requisitos foram utilizados para determinar o melhor método de migração:
 - A aplicação no Azure permanecerá como crítica, pois é hoje mesmo. Ele deve executar bem e dimensione com facilidade.
 - A aplicação não deve utilizar componentes de IaaS. Tudo o que deve ser criado para utilizar o PaaS ou dos serviços sem servidor.
 - As compilações de aplicação devem ser executados nos serviços cloud e contentores devem residir num registo de contentor de toda a empresa privada na cloud.
 - O serviço de API utilizado para fotos pet deve ser precisos e confiáveis no mundo real, uma vez que as decisões tomadas pela aplicação tem cumpridas seus hotéis. Qualquer animal de estimação concedido o acesso é permitido para se manter nos hotéis.
 - Para cumprir os requisitos para um pipeline do DevOps, Contoso usará DevOps do Azure para a origem de código de gestão (SCM), com repositórios Git.  Compilações automatizadas e versões serão utilizadas para criar código e implementação-la para as aplicações Web do Azure, as funções do Azure e AKS.
 - Pipelines de CI/CD diferentes são necessários para microsserviços no back-end e para o web site no front-end.
 - Os serviços de back-end têm uma versão diferente ciclo a partir da aplicação web de front-end.  Para cumprir este requisito, eles irão implementar dois pipelines de DevOps diferentes.
 - A Contoso precisa de aprovação de gestão para a implementação de Web site do todos os front-end e o pipeline de CI/CD tem de fornecer isso.


## <a name="solution-design"></a>Design da solução

Depois de fixar-se para baixo de objetivos e requisitos, a Contoso projeta e rever uma solução de implantação e identifica o processo de migração, incluindo os serviços do Azure que serão utilizados para a migração.

### <a name="current-app"></a>Aplicação atual

- A aplicação de local da SmartHotel360 é em camadas em duas VMs (WEBVM e SQLVM).
- As VMs estão localizadas no anfitrião ESXi do VMware **contosohost1.contoso.com** (versão 6.5)
- O ambiente do VMware é gerido pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução numa VM.
- A Contoso tiver um datacenter no local (contoso-datacenter), com um controlador de domínio no local (**contosodc1**).
- As VMs no local no Centro de dados Contoso serão encerradas depois de terminar a migração.


### <a name="proposed-architecture"></a>Arquitetura proposta

- O front-end da aplicação é implementado como uma aplicação Web de serviços de aplicações do Azure, na região primária do Azure.
- Uma função do Azure fornece carregamentos de estimação fotos e o site interage com esta funcionalidade.
- A função pet photo tira partido da API de imagem digitalizada dos serviços cognitivos e cosmos DB.
- O back-end do site é criado com microsserviços. Estas serão implementadas para os contentores geridos no Azure Kubernetes service (AKS).
- Contentores serão criados através do Azure DevOps e enviados por push para o Azure Container Registry (ACR).
- Por enquanto, Contoso irá implementar manualmente o código de aplicação e a função de Web com o Visual Studio
- Microsserviços serão implementados utilizar um script do PowerShell que chama as ferramentas de linha de comandos do Kubernetes.

    ![Arquitetura do cenário](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Revisão de solução

Contoso avalia a estrutura proposta ao juntar-se de uma lista de prós e contras.

**Consideração** | **Detalhes**
--- | ---
**Profissionais de TI** | Com a PaaS e soluções sem servidor para a implementação de ponto-a-ponto significativamente reduz o tempo de gestão que a Contoso tem de fornecer.<br/><br/> Mover para uma arquitetura de microsserviços permite que a Contoso facilmente estender a solução ao longo do tempo.<br/><br/> Nova funcionalidade pode ser colocada online sem interromper qualquer uma das bases de código de soluções existentes.<br/><br/> A aplicação Web será configurada com várias instâncias com nenhum ponto único de falha.<br/><br/> Dimensionamento automático será ativado para que a aplicação conseguir processar o volume de tráfego diferentes.<br/><br/> Com a mudança para os serviços PaaS Contoso pode extinguir Desatualizadas soluções em execução no sistema operativo Windows Server 2008 R2.<br/><br/> CosmosDB tem tolerância a falhas incorporada, que não requer configurações pela Contoso. Isso significa que a camada de dados já não é um ponto único de ativação pós-falha.
**Contras** | Os contentores são mais complexos do que outras opções de migração. A curva de aprendizado pode ser um problema para a Contoso.  Eles introduzem um novo nível de complexidade que fornece muito valor apesar da curva.<br/><br/> A equipe de operações da Contoso tem a incrementação para compreender e suporte do Azure, contentores e microsserviços para a aplicação.<br/><br/> Contoso totalmente ainda não implementado DevOps para a solução inteira. A Contoso precisa pensar que para a implementação de serviços para o AKS, funções e serviços de aplicações.



### <a name="migration-process"></a>Processo de migração

1. Contoso aprovisionar o ACR, AKS e cosmos DB.
2. Eles aprovisionam a infraestrutura para a implementação, incluindo a aplicação Web do Azure, a conta de armazenamento, a função e a API. 
3. Depois da infraestrutura no local, eles criar seus microsserviços imagens de contentor com o Azure DevOps, que envia-as para o ACR.
4. Contoso irá implementar estes microsserviços ASK utilizando um script do PowerShell.
5. Por fim, eles vai implementar a função do Azure e a aplicação Web.

    ![Processo de migração](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Simplifica o gerenciamento, implantação e operações do Kubernetes. Fornece um serviço de orquestração de contentores do Kubernetes totalmente gerido.  | AKS é um serviço gratuito.  Pague apenas a máquinas virtuais e armazenamento associado e recursos de rede consumidos. [Saiba mais](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Funções do Azure](https://azure.microsoft.com/services/functions/) | Acelera o desenvolvimento com uma experiência de computação sem servidor condicionadas por eventos. Dimensione sob pedido.  | Paga apenas pelos recursos consumidos. Plano é faturado com base no consumo de recursos por segundo e execuções. [Saiba mais](https://azure.microsoft.com/pricing/details/functions/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Armazena imagens de todos os tipos de implementações de contentor. | Custo com base em recursos, armazenamento e a duração de utilização. [Saiba mais](https://azure.microsoft.com/pricing/details/container-registry/).
[Serviço de Aplicações do Azure](https://azure.microsoft.com/services/app-service/containers/) | Crie, implemente e dimensione rapidamente aplicações API, Web e móveis em execução em qualquer plataforma. | Os planos do Serviço de Aplicações são cobrados com base por segundo. [Saiba mais](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Pré-requisitos

Eis o que a Contoso precisa de para este cenário:

**Requisitos** | **Detalhes**
--- | ---
**Subscrição do Azure** | Contoso criadas subscrições durante um artigo anterior. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se criar uma conta gratuita, será o administrador da sua subscrição e poderá executar todas as ações.<br/><br/> Se utilizar uma subscrição existente e não for o administrador, terá de trabalhar com o administrador para lhe atribuir permissões de proprietário ou contribuinte.
**Infraestrutura do Azure** | [Saiba como](contoso-migration-infrastructure.md) Contoso configurar uma infraestrutura do Azure.
**Pré-requisitos do desenvolvedor** | A Contoso precisa de ferramentas a seguir numa estação de trabalho do desenvolvedor:<br/><br/> - [Visual Studio 2017 Community Edition: Versão 15.5](https://www.visualstudio.com/)<br/><br/> Carga de trabalho .NET ativada.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) ou o Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) definido para utilizar contentores do Windows.



## <a name="scenario-steps"></a>Passos do cenário

Eis como Contoso será executada a migração:

> [!div class="checklist"]
> * **Passo 1: Aprovisionar o AKS e ACR**: Contoso Aprovisiona o cluster do AKS gerido e o registo de contentor do Azure com o PowerShell
> * **Passo 2: Criar contentores de Docker**: Configurar CI para contentores do Docker com o Azure DevOps e emiti-las para o ACR.
> * **Passo 3: Implementar microsserviços de back-end**: Que implementam o resto da infra-estrutura que irá ser aproveitada por microsserviços de back-end.
> * **Passo 4: Implementar a infraestrutura de front-end**: Que implementam a infra-estrutura de front-end, incluindo o armazenamento de BLOBs para os pet telemóveis, o Cosmos DB e API de imagem digitalizada.
> * **Passo 5: Migrar o back-end**: Eles implementar microsserviços e executam no AKS, para migrar o back-end.
> * **Passo 6: Publicar o front-end**: Eles publicam a aplicação da SmartHotel360 para o serviço de aplicações do Azure e a aplicação de função que será chamado pelo serviço de estimação.



## <a name="step-1-provision-back-end-resources"></a>Passo 1: Aprovisionar recursos de back-end

Os administradores da Contoso executam um script de implementação para criar o cluster de Kubernetes gerido com o AKS e o Azure Container Registry (ACR).

- As instruções para esta secção utilizam o **SmartHotel360-Azure-back-end** repositório.
- O **SmartHotel360-Azure-back-end** repositório do GitHub contém todo o software para esta parte da implementação.

### <a name="prerequisites"></a>Pré-requisitos

1. Antes de começaram a eles, os administradores da Contoso Certifique-se de que todos os pré-requisitos de software no instalados na máquina de desenvolvimento estiver a utilizar para a implementação.
2. Eles clonagem o repositório local para a máquina de desenvolvimento com o Git: **clone de git https://github.com/Microsoft/SmartHotel360-Azure-backend.git**


### <a name="provision-aks-and-acr"></a>Aprovisionar o AKS e ACR

Os administradores de Contoso Aprovisione da seguinte forma:

1.  Abra a pasta com o Visual Studio Code e passa para o **/implementar/k8s** diretório, que contém o script **gen-aks-env.ps1**.
2. Eles são executados o script para criar o cluster de Kubernetes gerido, com o AKS e ACR.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  Com o ficheiro aberto, atualizam o parâmetro $location **eualeste2**e guarde o ficheiro.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. Clicarem **View** > **Terminal integrado** para abrir o terminal integrado no código.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. No terminal integrado do PowerShell, iniciarem sessão no Azure com o comando Connect-AzureRmAccount. [Saiba mais](https://docs.microsoft.com/powershell/azure/get-started-azureps) como começar a trabalhar com o PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. Se autenticar a CLI do Azure ao executar o **início de sessão az** de comandos e seguir as instruções para autenticar usando seus navegadores da web. [Saiba mais](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) quanto ao log de sessão com a CLI do Azure.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. Eles são executados o seguinte comando, passando o nome do grupo de recursos do ContosoRG, o nome do AKS cluster smarthotel-aks-eus2 e o novo nome de registo.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. O Azure cria outro grupo de recursos, que contém os recursos para o cluster do AKS.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. Depois de concluída a implementação, instale o **kubectl** ferramenta da linha de comandos. A ferramenta já está instalada o Azure CloudShell.

    **az aks install-cli**

10. Eles verificarem a ligação ao cluster ao executar o **kubectl obter nós** comando. O nó é o mesmo nome que a VM no grupo de recursos criado automaticamente.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. Eles executam o seguinte comando para iniciar o Dashboard do Kubernetes: 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

12. É aberto um separador do browser para o Dashboard. Esta é uma ligação de túnel com a CLI do Azure. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>Passo 2: Configurar o pipeline de back-end

### <a name="create-an-azure-devops-project-and-build"></a>Criar um projeto de DevOps do Azure e criar

Contoso cria um projeto de DevOps do Azure e configura uma compilação CI para criar o contentor e, em seguida, envia-o para o ACR. As instruções nesta secção utilizam o [SmartHotel360-Azure-back-end](https://github.com/Microsoft/SmartHotel360-Azure-backend) repository.r

1. Do visualstudio.com, eles criam uma nova organização (**contosodevops360.visualstudio.com**) e configure-o a utilizar o Git.

2. Eles criam um novo projeto (**SmartHotelBackend**) com o Git para controle de versão e o Agile para o fluxo de trabalho.

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts1.png) 


3. Importam as [repositório do GitHub](https://github.com/Microsoft/SmartHotel360-Backend).

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts2.png)
    
4. Na **Pipelines**, pode clicar em **criar**e criar um novo pipeline com o Git de repositórios do Azure como uma origem, a partir do repositório. 

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts3.png)

6. Devem selecionar para iniciar com uma tarefa está vazia.

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts4.png)  

7. Eles selecionam **alojada pré-visualização do Linux** para o pipeline de compilação.

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts5.png) 
 
8. Na **fase 1**, adicionam um **Docker Compose** tarefas. Esta tarefa de compilações do Docker compõem.

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts6.png) 

9. Eles repetir e adicione outro **Docker Compose** tarefas. Este envia os contentores para o ACR.

     ![DevOps do Azure](./media/contoso-migration-rebuild/vsts7.png) 

8. Selecione a primeira tarefa (para compilar) e configurar a compilação com a subscrição do Azure, autorização e o ACR. 

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts8.png)

9. Especifica o caminho da **docker-yaml** , além de ficheiros a **src** pasta do repositório. Eles selecionam para criar imagens de serviço e incluir a etiqueta mais recente. Quando a ação é alterado para **criar imagens de serviço**, o nome da tarefa do Azure DevOps é alterado para **criar serviços automaticamente**

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts9.png)

10. Agora, eles configurarem a segunda tarefa do Docker (enviar por push). Selecionar a subscrição e o **smarthotelacreus2** ACR. 

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts10.png)

11. Novamente, o introduza o ficheiro para o ficheiro de docker yaml e selecione **Push manutenção de imagens** e incluir a etiqueta mais recente. Quando a ação é alterado para **Push manutenção de imagens**, o nome da tarefa do Azure DevOps é alterado para **automaticamente os serviços de Push**

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts11.png)

12. Com as tarefas de Azure DevOps configuradas, o Contoso salva o pipeline de compilação e inicia o processo de compilação.

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts12.png)

13. Clicarem na tarefa de compilação para verificar o progresso.

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts13.png)

14. Após a conclusão da compilação, o ACR mostra os novo repositórios, que são preenchidos com os contentores utilizados pelos microsserviços.

    ![DevOps do Azure](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>Implementar a infraestrutura de back-end

Com o cluster do AKS criado e as imagens do Docker incorporadas, administradores da Contoso implemente agora o resto da infra-estrutura que irá ser aproveitada por microsserviços de back-end.

- As instruções na secção utilização a [SmartHotel360-Azure-back-end](https://github.com/Microsoft/SmartHotel360-Azure-backend) repositório.
- Na **/deploy/k8s/arm** pasta, há um script único para criar todos os itens. 

Que implementam da seguinte forma:

1. Abra uma linha de comandos do programador e utilizar o início de sessão do comando az para a subscrição do Azure.
2. Utilizam o ficheiro cmd para implementar os recursos do Azure no grupo de recursos de ContosoRG e região EUS2, digitando o seguinte comando:

    **.\deploy.cmd azuredeploy ContosoRG -c eastus2**

    ![Implementar back-end](./media/contoso-migration-rebuild/backend1.png)

2. No portal do Azure, estes capturarem a cadeia de ligação para cada base de dados a ser utilizado mais tarde.

    ![Implementar back-end](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>Criar o pipeline de lançamento de back-end

Agora, os administradores da Contoso faça o seguinte:

- Implemente o controlador de entrada do NGINX para permitir tráfego de entrada para os serviços.
- Implemente os microsserviços no cluster do AKS.
- Como primeiro passo eles Atualize as cadeias de ligação para os microsserviços que utilizam o Azure DevOps. Em seguida, configurar um novo pipeline de lançamento de DevOps do Azure para implementar os microsserviços.
- As instruções nesta secção utilizam o [SmartHotel360-Azure-back-end](https://github.com/Microsoft/SmartHotel360-Azure-backend) repositório.
- Tenha em atenção que algumas das definições de configuração (por exemplo Active Directory B2C) não são abordadas neste artigo. Leia mais informações sobre estas definições no repositório.

Eles criaram o pipeline:

1. Com o Visual Studio que atualizam o **/deploy/k8s/config_local.yml** ficheiro com as informações de ligação de base de dados que anotou anteriormente.

    ![Ligações de DB](./media/contoso-migration-rebuild/back-pipe1.png)

2. São abertos de DevOps do Azure e na SmartHotel360 do projeto, na **versões**, pode clicar em **+ novo Pipeline**.

    ![Novo pipeline](./media/contoso-migration-rebuild/back-pipe2.png)

3. Clicarem **tarefa vazia** para iniciar o pipeline sem um modelo.
4. Eles fornecem os nomes de fase e o pipeline.

      ![Nome da fase](./media/contoso-migration-rebuild/back-pipe4.png)

      ![Nome do pipeline](./media/contoso-migration-rebuild/back-pipe5.png)

5. Eles adicionam um artefacto.

     ![Adicionar artefacto](./media/contoso-migration-rebuild/back-pipe6.png)

6. Eles selecionam **Git** como a origem escreva e especifique o projeto, a origem e o ramo principal para a aplicação da SmartHotel360.

    ![Definições de artefacto](./media/contoso-migration-rebuild/back-pipe7.png)

7. Estas clicarem na ligação de tarefa.

    ![Link de tarefas](./media/contoso-migration-rebuild/back-pipe8.png)

8. Eles adicionam uma nova tarefa do Azure PowerShell para que eles possam executar um script do PowerShell num ambiente do Azure.

    ![PowerShell no Azure](./media/contoso-migration-rebuild/back-pipe9.png)

9. Selecione a subscrição do Azure para a tarefa e selecione o **deploy.ps1** script a partir do repositório de Git.

    ![Executar script](./media/contoso-migration-rebuild/back-pipe10.png)


10. Eles adicionam argumentos ao script. o script irá eliminar todos os conteúdos de cluster (exceto **entrada** e **controlador de entradas**) e implementar os microsserviços.

    ![Argumentos do script](./media/contoso-migration-rebuild/back-pipe11.png)

11. Definir a versão do Azure PowerShell preferencial para o versão mais recente e guardar o pipeline.
12. Eles mover de volta para o **versão** de páginas e crie manualmente uma nova versão.

    ![Nova versão](./media/contoso-migration-rebuild/back-pipe12.png)

13. Eles clicam na versão depois de as criar e, em **ações**, pode clicar em **implementar**.

      ![Implementar a versão](./media/contoso-migration-rebuild/back-pipe13.png)  

14. Quando a implementação estiver concluída, o executarem o seguinte comando para verificar o estado de serviços, utilizando o Azure Cloud Shell: **kubectl obter serviços**.


## <a name="step-3-provision-front-end-services"></a>Passo 3: Aprovisionar serviços de front-end

Os administradores da Contoso tem de implementar a infraestrutura que será utilizada pelas aplicações de front-end. Ser criar um contentor de armazenamento de BLOBs para armazenar as imagens de estimação; a base de dados do Cosmos para armazenar documentos com as informações de estimação; e a API de imagem digitalizada para o Web site. 

Instruções para esta secção utilizam o [SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web) repositório.

### <a name="create-blob-storage-containers"></a>Criar contentores de armazenamento de blobs

1.  No portal do Azure, que abrem a conta de armazenamento que foi criada e clica em **Blobs**.
2.  Eles criam um novo contentor (**animais de estimação**) com o nível de acesso público definido para o contentor. Os utilizadores irão carregar suas fotos pet para este contentor.

    ![Blob de armazenamento](./media/contoso-migration-rebuild/blob1.png)

3. Eles criam um segundo novo contentor designado **definições**. Um ficheiro com todas as definições de aplicação de front-end vai ser colocado neste contentor.

    ![Blob de armazenamento](./media/contoso-migration-rebuild/blob2.png)

4. Estes capturarem os detalhes de acesso para a conta de armazenamento num arquivo de texto, para referência futura.

    ![Blob de armazenamento](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Aprovisionar uma base de dados do Cosmos

Os administradores da Contoso aprovisionar uma base de dados do Cosmos para ser utilizado para obter informações de estimação.

1. Eles criam uma **do Azure Cosmos DB** no Azure Marketplace.

    ![BD do Cosmos](./media/contoso-migration-rebuild/cosmos1.png)

2. Especifica um nome (**contosomarthotel**), selecione a API de SQL e coloque-o no grupo de recursos de produção ContosoRG, na região E.U.A. Leste 2 principal.

    ![BD do Cosmos](./media/contoso-migration-rebuild/cosmos2.png)

3. Eles adicionar uma nova coleção na base de dados, com capacidade predefinida e o débito.

    ![BD do Cosmos](./media/contoso-migration-rebuild/cosmos3.png)


4. Eles tenha em atenção as informações de ligação da base de dados, para referência futura. 

    ![BD do Cosmos](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Aprovisionamento de imagem digitalizada

Os administradores da Contoso aprovisionar a API de imagem digitalizada. A API será chamada pela função, para avaliar imagens carregadas por utilizadores.

1. Eles criam uma **de imagem digitalizada** instância no Azure Marketplace.

     ![Imagem Digitalizada](./media/contoso-migration-rebuild/vision1.png)

2. Que Aprovisiona a API (**smarthotelpets**) no recurso da produção grupo ContosoRG, na região E.U.A. Leste 2 principal.

    ![Imagem Digitalizada](./media/contoso-migration-rebuild/vision2.png)

3. Eles guardam as definições de ligação para a API num arquivo de texto para futura referência.

     ![Imagem Digitalizada](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Aprovisionar a aplicação Web do Azure

Os administradores da Contoso aprovisionar a aplicação web com o portal do Azure.


1. Eles selecionam **aplicação Web** no portal.

    ![Aplicação Web](media/contoso-migration-rebuild/web-app1.png)

2. Eles fornecem um nome de aplicação (**smarthotelcontoso**), executá-lo no Windows e coloque-o no grupo de recursos de produção **ContosoRG**. Eles criam uma nova instância do Application Insights para monitorização de aplicações....

    ![Nome da aplicação web](media/contoso-migration-rebuild/web-app2.png)

3. Depois de terminar, eles navegam para o endereço da aplicação para verificar a que ser criado com êxito.

4. Agora, no portal do Azure é criar um bloco de teste para o código. o pipeline irá implementar esta ranhura. Isto garante que o código não é colocado em produção, até que os administradores de executam uma versão.

    ![Bloco de teste de aplicações Web](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Aprovisionar a aplicação de funções do Azure

No portal do Azure, os administradores da Contoso aprovisionar a aplicação de funções.

1. Eles selecionam **aplicação de funções**.

    ![Criar aplicação de funções](./media/contoso-migration-rebuild/function-app1.png)

2. Eles fornecem um nome de aplicação (**smarthotelpetchecker**). Eles colocam a aplicação no grupo de recursos de produção **ContosoRG**. Eles definir o local de hospedagem **plano de consumo**e colocar a aplicação na região E.U.A. Leste 2. É criada uma nova conta de armazenamento, juntamente com uma instância do Application Insights para monitorização.

    ![Definições da Aplicação de funções](./media/contoso-migration-rebuild/function-app2.png)


3. Depois da aplicação é implementada, eles navegam para o endereço de aplicação para verificar a que ser criado com êxito.


## <a name="step-4-set-up-the-front-end-pipeline"></a>Passo 4: Configurar o pipeline de front-end

Os administradores da Contoso criar dois projetos diferentes para o site de front-end. 

1. No Azure DevOps, criam um projeto **SmartHotelFrontend**.

    ![Projeto de front-end](./media/contoso-migration-rebuild/function-app1.png)

2. Importam as [SmartHotel360 front-end](https://github.com/Microsoft/SmartHotel360-public-web.git) repositório de Git para o novo projeto.
3. Para a aplicação de funções, eles criar outro projeto de DevOps do Azure (SmartHotelPetChecker) e importar os [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) repositório de Git para este projeto.

### <a name="configure-the-web-app"></a>Configurar a aplicação Web

Agora, os administradores de Contoso configurar a aplicação Web para utilizar recursos da Contoso.

1. Ligar para o projeto de DevOps do Azure e clone o repositório localmente para o computador de desenvolvimento.
2. No Visual Studio, abrir a pasta para mostrar todos os ficheiros no repositório.

    ![Ficheiros do repositório](./media/contoso-migration-rebuild/configure-webapp1.png)

3. Eles atualizam as alterações de configuração conforme necessário.

    - Quando a aplicação web é iniciado, ele procura o **SettingsUrl** definição de aplicação.
    - Esta variável tem de conter uma URL apontando para um ficheiro de configuração.
    - Por predefinição, a definição utilizada é um ponto final público.

4.  Eles atualizam o ficheiro de /config-sample.json/sample.json.

    - Este é o ficheiro de configuração para a web ao utilizar o ponto final público.
    - Eles editar a **urls** e **pets_config** seções com os valores para os pontos finais da API do AKS, contas de armazenamento e base de dados do Cosmos.
    - Os URLs devem corresponder ao nome DNS da nova aplicação web que Contoso criará.
    - Para a Contoso, trata-se **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Definições de JSON](./media/contoso-migration-rebuild/configure-webapp2.png)

5. Depois do ficheiro é atualizado, renomeá-lo **smarthotelsettingsurl**e carregá-lo para o armazenamento de BLOBs que criaram anteriormente.

    ![Mudar o nome e o carregamento](./media/contoso-migration-rebuild/configure-webapp3.png)

6. Eles clicam no ficheiro para obter o URL. O URL é utilizado pela aplicação, quando ele extrai os ficheiros de configuração.

    ![URL da aplicação](./media/contoso-migration-rebuild/configure-webapp4.png)

7. Na **appsettings. Production.JSON** atualização do arquivo, o **SettingsURL** para o URL do novo arquivo.

    ![URL da atualização](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>Implementar o Web site para o serviço de aplicações do Azure

Os administradores da Contoso agora podem publicar o Web site.


1. Abrem DevOps do Azure e, no **SmartHotelFrontend** , além do projeto **compilações e lançamentos**, clicarem **+ novo Pipeline**.
2. Eles selecionam **Git de DevOps do Azure** como uma origem.
3. Eles selecionam os **ASP.NET Core** modelo.
4. Reveja o pipeline e verifique se **publicar projetos da Web** e **Zip projetos publicados** estão selecionadas.

    ![Definições do pipeline](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. Na **Acionadores**, ative a integração contínua e adicionar o ramo principal. Isto garante que sempre que a solução tem o novo código consolidado para o ramo principal, o pipeline de compilação seja iniciada.

    ![Integração contínua](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. Clicarem **guardar e colocar em fila** para iniciar uma compilação.
7. Após a compilação for concluída, configure um pipeline de versões com o **implementação de serviço de aplicações do Azure**.
8. Eles fornecem um nome de fase **teste**.

    ![Nome do ambiente](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. Se adicionar um artefato e selecione a compilação que acabou de configurar.

     ![Adicionar artefacto](./media/contoso-migration-rebuild/vsts-publishfront5.png)

10. Clique no ícone de bolt relâmpago no artefacto e ativar a implementação contínua.

    ![Implementação contínua](./media/contoso-migration-rebuild/vsts-publishfront6.png)
11. No **ambiente**, pode clicar em **1 tarefa, 1 tarefa** sob **teste**.
12. Depois de selecionar a subscrição e o nome da aplicação, abra a **implementar serviço de aplicações do Azure** tarefas. A implementação está configurada para utilizar o **teste** bloco de implementação. Isso cria automaticamente o código para revisão e aprovação neste espaço.

     ![Bloco](./media/contoso-migration-rebuild/vsts-publishfront7.png)

13. Na **Pipeline**, adicionam uma fase de novo.

    ![Novo ambiente](./media/contoso-migration-rebuild/vsts-publishfront8.png)

14. Eles selecionam **implementação de serviço de aplicações do Azure com a ranhura**e dê o nome do ambiente **Prod**.
15. Clicarem na **1 e 2 tarefas**e selecione a subscrição, o nome do serviço de aplicações e o **teste** ranhura.

    ![Nome do ambiente](./media/contoso-migration-rebuild/vsts-publishfront10.png)

16. Remova os **implementar serviço de aplicações do Azure para o bloco de** do pipeline. Foi colocada lá pelos passos anteriores.

    ![Remover do pipeline](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. Guardam o pipeline. No pipeline, clique em **condições de pós-implementação**.

    ![Pós-implementação](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. Eles permitem **aprovações de pós-implementação**, e adicione um líder de desenvolvimento como o aprovador.

    ![Aprovação de pós-implementação](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. No pipeline de compilação, eles manualmente iniciar uma compilação. Isto aciona o pipeline de lançamento novo, que implementa o site para o bloco de teste. Para a Contoso, é o URL para o bloco **https://smarthotelcontoso-staging.azurewebsites.net/**.
16. Após a conclusão da compilação e a versão implementa para o bloco, o Azure DevOps e-mails o líder de desenvolvimento para aprovação.
17. Cliques de liderança dev **ver aprovação**e pode aprovar ou rejeitar o pedido no portal do Azure DevOps.

    ![E-mail de aprovação](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. O líder de faz um comentário e aprova. Esta ação inicia a troca do **de teste** e **prod** ranhuras e move a compilação em produção.

    ![Aprovar e trocar](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. O pipeline conclua a troca.

    ![Concluir troca](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. As verificações de equipe a **prod** ranhura para verificar se a aplicação web está em produção na **https://smarthotelcontoso.azurewebsites.net/**.


### <a name="deploy-the-petchecker-function-app"></a>Implementar a aplicação de funções de PetChecker

Os administradores da Contoso implementar a aplicação da seguinte forma.

1. Eles clonagem o repositório localmente para o computador de desenvolvimento ao ligar-se para o projeto de DevOps do Azure.
2. No Visual Studio, abrir a pasta para mostrar todos os ficheiros no repositório.
3. Abrir o **src/PetCheckerFunction/local.settings.json** de ficheiros e adicionar as definições da aplicação para armazenamento, a base de dados do Cosmos e a API de imagem digitalizada.

    ![Implementar a função](./media/contoso-migration-rebuild/function5.png)

4. Consolidar o código e sincronizá-la novamente com o Azure DevOps, enviar por push as alterações.
5. Adicionar um novo pipeline de compilação e selecione **Git de DevOps do Azure** para a origem.
6. Eles selecionam os **ASP.NET Core (.NET Framework)** modelo.
7. Eles aceitem as predefinições para o modelo.
8. Na **Acionadores**, em seguida, selecione a **ativar a integração contínua**e clique em **guardar e colocar em fila** para iniciar uma compilação.
9. Após a compilação for concluída com êxito, o que eles criam um pipeline de lançamento, adicionando a **implementação de serviço de aplicações do Azure com a ranhura**.
10. Nome de ambiente **Prod**e selecione a subscrição. Configuram o **tipo de aplicação** ao **Function App**e o nome do serviço de aplicações como **smarthotelpetchecker**.

    ![Function app](./media/contoso-migration-rebuild/petchecker2.png)

11. Se adicionam um artefato **criar**.

    ![Artefacto](./media/contoso-migration-rebuild/petchecker3.png)

12. Eles permitem **acionador de implementação contínua**e clique em **guardar**.
13. Clicarem **Enfileirar nova compilação** para executar o pipeline de CI/CD completo.
14. Depois da função é implementada, é apresentado no portal do Azure, com o **em execução** estado.

    ![Implementar a função](./media/contoso-migration-rebuild/function6.png)


7. Eles navegam para a aplicação para testar a aplicação do Verificador de animal de estimação está a funcionar conforme esperado, em [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. Clicarem no avatar para carregar uma imagem.

    ![Implementar a função](./media/contoso-migration-rebuild/function7.png)

9. A primeira foto que pretendem verificar é de um pequeno dog.

    ![Implementar a função](./media/contoso-migration-rebuild/function8.png)

10. A aplicação devolver uma mensagem de aceitação.

    ![Implementar a função](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>Reveja a implementação

Com os recursos migrados no Azure, a Contoso precisa agora totalmente operacionalizar e proteger a nova infra-estrutura.

### <a name="security"></a>Segurança

- A Contoso precisa de garantir que as novas bases de dados estão seguros. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- A aplicação tem de ser atualizado para utilizar SSL com certificados. A instância de contentor deve ser reimplantada para responder a 443.
- Contoso deve considerar a utilização do Cofre de chaves para proteger segredos para as aplicações do Service Fabric. [Saiba mais](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>As cópias de segurança e recuperação após desastre

- A Contoso precisa de analisar os requisitos de cópia de segurança da base de dados do SQL do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso deve considerar a implementação de grupos de ativação pós-falha do SQL para fornecer ativação pós-falha para a base de dados. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso pode tirar partido de georreplicação para a SKU de premium do ACR. [Saiba mais](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- O cosmos DB faz o backup automaticamente. Pode de contoso [Saiba mais](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) sobre este processo.

### <a name="licensing-and-cost-optimization"></a>Otimização de licenciamento e de custo

- Depois de todos os recursos são implementados, Contoso deve atribuir etiquetas do Azure com base em seus [planejamento da infraestrutura](contoso-migration-infrastructure.md#set-up-tagging).
- Licenciamento de todos os baseia-se para o custo dos serviços PaaS que Contoso está a consumir. Isso é deduzido do EA.
- Contoso irá ativar o Azure Cost Management licenciado pela Cloudyn, uma subsidiária da Microsoft. É uma solução de gestão de custos multiclouds que o ajuda a utilizar e gerir o Azure e outros recursos da cloud.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Azure Cost Management.

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso recria a aplicação da SmartHotel360 no Azure. A aplicação no local que VM de front-end é recriado para aplicações de Web de serviços de aplicações do Azure. O back-end de aplicação é criado usando microsserviços implementados para os contentores geridos pelo Azure Kubernetes Service (AKS). Contoso avançada funcionalidade de aplicação com uma aplicação de fotografias de estimação.




