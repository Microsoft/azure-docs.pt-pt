---
title: Tutorial - DevOps Integrados para IaaS e PaaS em Azure
description: Neste tutorial, aprende-se a configurar a integração contínua (CI) e a implantação contínua (CD) de uma aplicação para Os VMs Azure utilizando pipelines Azure.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885910"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Tutorial: DevOps integrados para IaaS e PaaS em Azure

Com soluções de ponta a ponta no Azure, as equipas podem implementar práticas de DevOps em cada uma das fases do ciclo de vida da aplicação: planear, desenvolver, entregar e operar. 

Abaixo estão alguns dos Serviços Azure que simplificam as cargas de trabalho em nuvem e podem ser combinados para permitir cenários incrivelmente poderosos.
Estas tecnologias, combinadas com pessoas e processos, permitem às equipas fornecer continuamente valor aos clientes. 

- Azure: https://portal.azure.com – Portal para a construção de cargas de trabalho em nuvem. Gerir e monitorizar tudo, desde aplicações web simples até aplicações complexas em nuvem 
- Azure DevOps: https://dev.azure.com – Planeie mais inteligente, colabore melhor e navere mais rápido com um conjunto de serviços modernos de dev 
- Estúdio Azure Machine https://ml.azure.com Learning: - Dados de preparação, treina e implementa modelos de aprendizagem automática 
 

O Azure DevOps é um serviço Azure incorporado que automatiza cada parte do processo DevOps com integração contínua e entrega contínua para qualquer recurso Azure.
Quer a sua aplicação utilize máquinas virtuais, aplicações web, Kubernetes ou qualquer outro recurso, pode implementar, infraestrutura como código, integração contínua, testes contínuos, entrega contínua e monitorização contínua com Azure e Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Configure CI/CD 
A Azure Pipelines fornece um conjunto completo e completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais. Pode configurar um gasoduto de entrega contínuo para um Azure VM diretamente do portal Azure. Este documento contém os passos associados à criação de um gasoduto CI/CD para implantações multi-máquinas a partir do portal Azure. 


**Configure CI/CD em máquinas virtuais**

As máquinas virtuais podem ser adicionadas como alvos de um grupo de [implementação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e podem ser direcionadas para a atualização multi-máquina. Com base nos seus requisitos, pode escolher qualquer uma das estratégias de implementação fora da caixa, _Rolling,_ _Canary,_ _Blue-Green_ ou pode personalizá-las ainda mais. Uma vez implementado, as vistas do Histórico de Implantação dentro dos Grupos de Implantação fornecem rastreabilidade da VM para o oleoduto e, em seguida, para o compromisso. 
 
**Rolling Deployments**: Uma implantação rolante substitui as instâncias da versão anterior de uma aplicação por instâncias da nova versão da aplicação num conjunto fixo de máquinas (rolling set) em cada iteração. Vamos ver como pode configurar uma atualização rolante para máquinas virtuais.  
Pode configurar atualizações de rolamento para as suas "**máquinas virtuais**" dentro do portal Azure utilizando a opção de entrega contínua. 

Aqui está a passagem passo a passo. 
1. Inscreva-se no seu portal Azure e navegue para uma máquina virtual. 
2. No painel esquerdo VM, navegue para o menu de **entrega** contínua. Em seguida, clique em **Configurar**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. No painel de configuração, clique em "Azure DevOps Organization" para selecionar uma conta existente ou criar uma. Em seguida, selecione o projeto sob o qual pretende configurar o gasoduto.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Um grupo de implantação é um conjunto lógico de máquinas-alvo de implantação que representam os ambientes físicos; por exemplo, "Dev", "Test", "UAT" e "Production". Pode criar um novo grupo de implementação ou selecionar um grupo de implementação existente. 
5. Selecione o pipeline de construção que publica o pacote a ser implantado na máquina virtual. Note que o pacote publicado deve ter um script _de implementação deploy.ps1_ ou _deploy.sh_ na pasta _de implantscripts_ na raiz do pacote. Este script de implantação será executado pelo oleoduto Azure DevOps no tempo de execução.
6. Selecione a estratégia de implementação à sua escolha. Neste caso, permite selecionar 'Rolling'.
7. Opcionalmente, pode marcar a máquina com o papel. Por exemplo, 'web', 'db' etc. Isto ajuda-o a direcionar Os VMs que têm um papel específico apenas.
8. Clique **ok** no diálogo para configurar o gasoduto de entrega contínua. 
9. Uma vez feito, terá um gasoduto de entrega contínuo configurado para ser implantado na máquina virtual.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Verá que a implantação para a máquina virtual está em andamento. Pode clicar no link para navegar até ao oleoduto. Clique no **Lançamento-1** para visualizar a implementação. Ou pode clicar na **Edição** para modificar a definição do pipeline de lançamento. 
11. Se tiver vários VMs para configurar, repita os passos 2-4 para que outros VMs sejam adicionados ao grupo de implementação. Note que se selecionar um Grupo de Implantação para o qual já existe um pipeline, o VM será adicionado ao grupo de implantação sem criar novos oleodutos. 
12. Uma vez feito, clique na definição do pipeline, navegue para a organização Azure DevOps e clique no pipeline de lançamento **edit.** 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Clique no **link 1 trabalho, 1 tarefa** na fase **de dev.** Clique na fase **de implantação.**
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. A partir do painel de configuração à direita, pode especificar o número de máquinas que pretende implantar paralelamente em cada iteração. Caso pretenda ser implantado em várias máquinas de cada vez, pode especificá-lo em termos de percentagem utilizando o slider.  

15. A tarefa execute script de execução executará por padrão o script de _implementação de implementação.ps1_ ou _deploy.sh_ na pasta _de implementscripts_ no diretório raiz do pacote publicado.
  
**Implementações canárias**: Uma implementação canária reduz o risco ao lançar lentamente a mudança para um pequeno subconjunto de utilizadores. À medida que ganha mais confiança na nova versão, pode começar a lançá-la para mais servidores na sua infraestrutura e encaminhar mais utilizadores para ela. Pode configurar as implementações canárias para as suas "**máquinas virtuais**" com o portal Azure utilizando a opção de entrega contínua. Aqui está a passagem passo a passo. 
1. Inscreva-se no seu portal Azure e navegue para uma máquina virtual 
2. Siga os passos 2-7 sob a secção **de Implantações Rolantes** para adicionar vários VMs ao grupo de implantação. Para a demissão da estratégia de implantação, selecione 'Canary'.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Adicione uma etiqueta 'canária' aos VMs que devem fazer parte das implementações canárias e uma etiqueta 'prod' aos VMs que fazem parte das implantações após a implantação do canário em sucesso.
4. Uma vez feito, terá um gasoduto de entrega contínuo configurado para ser implantado na máquina virtual.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. Tal como na secção **Rolling Deployments,** pode clicar no pipeline de lançamento **editar** em Azure DevOps para ver a configuração do pipeline. O gasoduto é composto por 3 fases - a primeira fase é uma fase DG e implanta-se para VMs que são marcados como _canários_. A segunda fase, interrompe o gasoduto e aguarda a intervenção manual para retomar a execução. Assim que um utilizador estiver convencido de que a implantação do canário é estável, pode retomar a execução do gasoduto que irá então executar a terceira fase que se implanta para VMs marcados como _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Destacamentos Blue-Green**: Uma implantação Blue-Green reduz o tempo de inatividade por ter um ambiente de espera idêntico. A qualquer momento, um dos ambientes está ao vivo. Enquanto se prepara para um novo lançamento, faz a fase final de testes no ambiente verde. Uma vez que o software esteja a funcionar em ambiente verde, mude o tráfego para que todos os pedidos de entrada vão para o ambiente verde - o ambiente azul está agora inativo.
Pode configurar as implementações Blue-Green para as suas "**máquinas virtuais**" a partir do portal Azure utilizando a opção de entrega contínua. 

Aqui está a passagem passo a passo.

1. Inscreva-se no seu portal Azure e navegue para uma Máquina Virtual 
2. Siga os passos 2-7 sob a secção **de Implantações Rolantes** para adicionar vários VMs ao grupo de implantação. Adicione uma etiqueta "azul" ou "verde" aos VMs que devem fazer parte das implementações Blue-Green. Se o VM é para um papel de espera, então deve marcá-lo como "verde".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. Uma vez feito, terá um gasoduto de entrega contínuo configurado para ser implantado na máquina virtual.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. Tal como acontece com **as Rolling Deployments,** pode clicar no pipeline de lançamento **da Edit** em Azure DevOps para ver a configuração do pipeline. O gasoduto é composto por 3 fases - a primeira fase é uma fase DG e implanta-se para VMs que são marcados como _verdes_ (VMs de espera) . A segunda fase interrompe o gasoduto e aguarda a intervenção manual para retomar a execução. Uma vez que um utilizador esteja convencido de que a implantação é estável, ele pode agora redirecionar o tráfego para VMs _verdes_ e retomar a execução do gasoduto que depois trocará etiquetas _azuis_ e _verdes_ nos VMs. Isto garante que os VMs que têm versão de aplicação mais antiga são marcados como _verdes_ e são implantados na próxima execução do gasoduto.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. Note-se que esta estratégia de implantação requer que deve haver pelo menos um VM marcado como azul e verde cada. Certifique-se de que antes de retomar o gasoduto em passo de intervenção manual, tem pelo menos um VM marcado como _azul_.





 
## <a name="azure-devops-project"></a>Projeto Azure DevOps 
Começa com o Azure mais facilmente do que nunca.
 
Com a DevOps Projects, comece a executar a sua aplicação em qualquer serviço Azure em apenas três etapas: selecione um idioma de aplicação, um tempo de execução e um serviço Azure.
 
[Saiba mais](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Recursos adicionais 
- [Implementar para máquinas virtuais Azure usando o projeto DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implemente a implementação contínua da sua app para um conjunto de escala de máquina virtual Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
