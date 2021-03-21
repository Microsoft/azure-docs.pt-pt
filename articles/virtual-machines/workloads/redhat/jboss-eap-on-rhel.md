---
title: Quickstart - Implementar a Plataforma de Aplicação Empresarial JBoss (EAP) no Red Hat Enterprise Linux (RHEL) para VMs Azure e conjuntos de escala de máquinas virtuais
description: Como implementar aplicações java da empresa utilizando o Red Hat JBoss EAP em VMs Azure RHEL e conjuntos de escala de máquina virtual.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: 4027e9c336d938c3877e56b0eb59edea6ac1d992
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671979"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Implementar aplicações Java empresariais no Azure com o JBoss EAP no Red Hat Enterprise Linux

Os modelos Azure Quickstart neste artigo mostram-lhe como implantar a Plataforma de [Aplicação Empresarial JBoss (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) com [red hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) para máquinas virtuais Azure (VMs) e conjuntos de escala de máquina virtual. Você usará uma aplicação java de amostra para validar a implementação. 

JBoss EAP é uma plataforma de servidor de aplicações de código aberto. Oferece segurança, escalabilidade e desempenho para as suas aplicações Java. A RHEL é uma plataforma de sistema operativo de código aberto (OS). Permite a escala de aplicações existentes e o lançamento de tecnologias emergentes em todos os ambientes. 

JBoss EAP e RHEL incluem tudo o que você precisa para construir, executar, implementar e gerir aplicações java empresarial em qualquer ambiente. A combinação é uma solução de código aberto para locais, ambientes virtuais e em nuvens privadas, públicas ou híbridas.

## <a name="prerequisites"></a>Pré-requisitos 

* Uma conta Azure com uma subscrição ativa. Para obter uma subscrição do Azure, ative os seus [créditos Azure para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou [crie uma conta gratuitamente](https://azure.microsoft.com/pricing/free-trial).

* Instalação JBoss EAP. Você precisa ter uma conta Red Hat com direito de Gestão de Assinaturas red hat (RHSM) para JBoss EAP. Este direito permitir-lhe-á descarregar a versão EAP testada e certificada do JBoss.  

  Se não tiver direito EAP, obtenha uma subscrição de [avaliação EAP JBoss](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) antes de começar. Para criar uma nova subscrição do Red Hat, vá ao [Portal do Cliente da Red Hat](https://access.redhat.com/) e crie uma conta.

* O [Azure CLI.](/cli/azure/overview)

* Opções RHEL. Escolha pay-as-you-go (PAYG) ou traga a sua própria subscrição (BYOS). Com o BYOS, precisa de ativar a sua imagem de acesso à [nuvem de chapéu vermelho](https://access.redhat.com/) RHEL antes de implementar o modelo Quickstart.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Migração de aplicações java EE e Jacarta EE

### <a name="migrate-to-jboss-eap"></a>Migrar para JBoss EAP
JBoss EAP 7.2 e 7.3 são implementações certificadas das especificações Java Enterprise Edition (Java EE) 8 e Jakarta EE 8. O JBoss EAP fornece opções pré-configuradas para funcionalidades como agrupamento de alta disponibilidade (HA), mensagens e caching distribuído. Também permite que os utilizadores escrevam, implementem e executem aplicações utilizando as várias APIs e serviços que o JBoss EAP fornece.  

Para obter mais informações sobre o JBoss EAP, consulte [Introdução ao JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) ou [Introdução ao JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Candidaturas no JBoss EAP

* **Aplicações de serviços web**. Os serviços web fornecem uma forma padrão de interoperar entre aplicações de software. Cada aplicação pode ser executada em diferentes plataformas e quadros. Estes serviços web facilitam a comunicação interna e heterogénea do subsistema. 

  Para saber mais, consulte [o Desenvolvimento de Aplicações de Serviços Web no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) ou [desenvolver aplicações de serviços web no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **Aplicações Enterprise Java Beans (EJB).** O EJB 3.2 é uma API para o desenvolvimento de aplicações distribuídas, transacionais, seguras e portáteis java EE e Jacarta EE. O EJB utiliza componentes do lado do servidor chamados Enterprise Beans para implementar a lógica de negócio de uma aplicação de forma dissociada que incentiva a reutilização. 

  Para saber mais, consulte [o Desenvolvimento de Aplicações EJB no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) ou [o Desenvolvimento de Aplicações EJB no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Aplicações de hibernação.** Os desenvolvedores e administradores podem desenvolver e implementar aplicações Java Persistence API (JPA) e Hibernar com jBoss EAP. Hibernate Core é uma estrutura de mapeamento relacional de objetos para a língua java. Fornece uma estrutura para mapear um modelo de domínio orientado para objetos para uma base de dados relacional, para que as aplicações possam evitar a interação direta com a base de dados. 

  O Gestor de Entidades Hibernate implementa as interfaces de programação e as regras do ciclo de vida definidas pela [especificação JPA 2.1](https://www.jcp.org/en/jsr/overview). Juntamente com as anotações de hibernação, este invólucro implementa uma solução JPA completa (e autónoma) em cima do Núcleo hibernado maduro. 
  
  Para saber mais sobre hibernação, consulte [JPA no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) ou  [Jacarta Persistence na EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Kit de ferramentas de migração de chapéu vermelho para aplicações
[O Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) é uma ferramenta de migração para servidores de aplicações Java. Utilize esta ferramenta para migrar de outro servidor de aplicações para o JBoss EAP. Funciona com plug-ins IDE para [Eclipse IDE,](https://www.eclipse.org/ide/) [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview)e [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) para Java. 

O MTA é uma ferramenta de código aberto e livre que:
* Automatiza a análise da aplicação.
* Apoia a estimativa do esforço.
* Acelera a migração de códigos.
* Suporta a contentorização.
* Integra-se com o Azure Workload Builder.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrar JBoss EAP de bordo para Azure
A oferta do Azure Marketplace da JBoss EAP na RHEL instalará e providenciará em VMs Azure em menos de 20 minutos. Pode aceder a estas ofertas a partir do [Azure Marketplace.](https://azuremarketplace.microsoft.com/)

Esta oferta do Azure Marketplace inclui várias combinações de versões EAP e RHEL para suportar os seus requisitos. JBoss EAP é sempre BYOS, mas para RHEL OS, pode escolher entre BYOS ou PAYG. A oferta do Azure Marketplace inclui opções de plano para JBoss EAP em RHEL como VMs autónomos ou agrupados:

* JBoss EAP 7.2 em RHEL 7.7 VM (PAYG)
* JBoss EAP 7.2 em RHEL 8.0 VM (PAYG)
* JBoss EAP 7.3 em RHEL 8.0 VM (PAYG)
* JBoss EAP 7.2 em RHEL 7.7 VM (BYOS)
* JBoss EAP 7.2 em RHEL 8.0 VM (BYOS)
* JBoss EAP 7.3 em RHEL 8.0 VM (BYOS)

Juntamente com as ofertas do Azure Marketplace, pode utilizar modelos Quickstart para começar a sua viagem de migração Azure. Estes Quickstarts incluem modelos e scripts pré-construídos do Azure Resource Manager (ARM) para implementar O EAP JBoss no RHEL em várias configurações e combinações de versão. Terá:

* Um equilibrador de carga.
* Um IP privado para equilibrar carga e VMs.
* Uma rede virtual com uma única sub-rede.
* Configuração VM (cluster ou autónomo).
* Uma amostra da aplicação Java.

A arquitetura de solução para estes modelos inclui:

* JBoss EAP em um RHEL VM autónomo.
* JBoss EAP agrupado em vários VMs RHEL.
* JBoss EAP agrupado através de conjuntos de escala de máquina virtual Azure.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migração linux de carga de trabalho para JBoss EAP
O Azure Workload Builder simplifica o processo de prova de conceito, avaliação e migração para aplicações Java no local para Azure. Workload Builder integra-se com a ferramenta Azure Migrate Discovery para identificar servidores EAP JBoss. Em seguida, gera dinamicamente um livro de jogadas Ansible para a implementação do servidor EAP JBoss. Utiliza a ferramenta Red Hat MTA para migrar servidores de outros servidores de aplicações para o JBoss EAP. 

As medidas para simplificar a migração incluem:
1. **Avaliação**. Avalie os clusters EAP JBoss utilizando um VM Azure ou um conjunto de balanças de máquinas virtuais.
1. **Avaliação**. Digitalizar aplicações e infraestruturas.
1. **Configuração da infraestrutura**. Criar um perfil de carga de trabalho.
1. **Implantação e teste**. Implementar, migrar e testar a carga de trabalho.
1. **Configuração pós-implantação**. Integre com dados, monitorização, segurança, backup, e muito mais.

## <a name="server-configuration-choice"></a>Escolha de configuração do servidor

Para a implementação do RHEL VM, pode escolher PAYG ou BYOS. Imagens do [Azure Marketplace](https://azuremarketplace.microsoft.com) por defeito para PAYG. Implemente um RHEL VM do tipo BYOS se tiver a sua própria imagem RHEL OS. Certifique-se de que a sua conta RHSM tem direito BYOS via Cloud Access antes de implementar o conjunto de escala de VM ou de máquina virtual.

O JBoss EAP tem poderosas capacidades de gestão, juntamente com o fornecimento de funcionalidades e APIs às suas aplicações. Estas capacidades de gestão diferem consoante o modo de funcionamento que utiliza para iniciar o JBoss EAP. É suportado no RHEL e no Windows Server. O JBoss EAP oferece um modo de funcionamento autónomo do servidor para gerir instâncias discretas. Também oferece um modo de funcionamento de domínio gerido para gerir grupos de instâncias a partir de um único ponto de controlo. 

> [!NOTE]
> Os domínios geridos pela JBoss EAP não são suportados no Microsoft Azure porque os serviços de infraestrutura Azure gerem a funcionalidade HA. 

A variável ambiente `EAP_HOME` denota o caminho para a instalação JBoss EAP. Utilize o seguinte comando para iniciar o serviço JBoss EAP em modo autónomo:

```
$EAP_HOME/bin/standalone.sh
```
    
Este script de arranque utiliza o ficheiro EAP_HOME/bin/autónomo.conf para definir algumas preferências predefinidos, tais como opções JVM. Pode personalizar as definições neste ficheiro. O JBoss EAP utiliza o ficheiro de configuração standalone.xml para iniciar em modo autónomo por predefinição, mas pode utilizar um modo diferente para o iniciar. 

Para obter mais informações sobre os ficheiros de configuração autónomo disponíveis e como usá-los, consulte ficheiros de configuração do servidor autónomo para ficheiros de configuração do servidor [EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) ou [de configuração autónoma para EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). 

Para iniciar o JBoss EAP com uma configuração diferente, utilize o `--server-config` argumento. Por exemplo:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Para uma listagem completa de todos os argumentos de script de arranque disponíveis e seus propósitos, use o `--help` argumento. Para obter mais informações, consulte [os argumentos de tempo de execução do servidor em EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) ou argumentos de tempo de [execução do servidor no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
O JBoss EAP também pode trabalhar em modo cluster. As mensagens de cluster JBoss EAP permitem agrupar servidores de mensagens EAP JBoss para partilhar a carga de processamento de mensagens. Cada nó ativo no cluster é um servidor de mensagens JBoss EAP ativo, que gere as suas próprias mensagens e lida com as suas próprias ligações. Para saber mais, consulte [a visão geral dos Clusters no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) ou [ na Visão geral dos Clusters no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Notas de apoio e subscrição
Estes modelos Quickstart são oferecidos da seguinte forma: 

- O RHEL OS é oferecido como PAYG ou BYOS através do modelo Red Hat Gold Image.
- JBoss EAP é oferecido apenas como BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Utilização do RHEL OS com o modelo PAYG

Por padrão, estes modelos Quickstart utilizam a imagem ON-Demand RHEL 7.7 ou 8.0 PAYG do Azure Marketplace. As imagens PAYG têm uma taxa adicional de subscrição DE RÉL por hora, além dos custos normais de computação, rede e armazenamento. Ao mesmo tempo, o caso está registado na sua assinatura Red Hat. Isto significa que vais usar um dos teus direitos. 

Esta imagem PAYG levará a "faturação dupla". Pode evitar este problema construindo a sua própria imagem RHEL. Para saber mais, leia o artigo base de conhecimento da Red Hat [Como providenciar um RHEL VM para o Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Ou ativar a sua imagem [de acesso à nuvem de chapéu vermelho](https://access.redhat.com/) RHEL Gold Image.

Para mais detalhes sobre os preços da PAYG VM, consulte [os preços da Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Para utilizar o RHEL no modelo PAYG, necessitará de uma subscrição Azure com o método de pagamento especificado para [RHEL 7.7 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) ou [RHEL 8.0 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Estas ofertas requerem um método de pagamento a especificar na subscrição do Azure.

#### <a name="using-rhel-os-with-the-byos-model"></a>Utilização do RHEL OS com o modelo BYOS

Para utilizar o BYOS para RHEL OS, precisa de ter uma subscrição válida do Red Hat com direitos de utilização do RHEL OS em Azure. Preencha os seguintes pré-requisitos antes de implementar o RHEL OS com o modelo BYOS:

1. Certifique-se de que tem direitos RHEL OS e JBoss EAP anexados à sua assinatura Red Hat.
2. Autorize o seu ID de subscrição Azure para utilizar imagens RHEL BYOS. Siga a [documentação da Red Hat Subscription Management](https://access.redhat.com/documentation/red_hat_subscription_management/1/) para concluir o processo, que inclui estes passos:

   1. Ativar o Microsoft Azure como fornecedor no seu Painel de Acesso à Nuvem de Chapéu Vermelho.

   1. Adicione os seus IDs de subscrição Azure.

   1. Ativar novos produtos para o Cloud Access no Microsoft Azure.
    
   1. Ative as Imagens Gold do Chapéu Vermelho para a sua subscrição do Azure. Para mais informações, consulte [Red Hat Gold Images no Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access).

   1. Aguarde que a Red Hat Gold Images esteja disponível na sua assinatura Azure. Estas imagens estão normalmente disponíveis dentro de três horas após a submissão.
    
3. Aceite os termos e condições do Azure Marketplace para imagens RHEL BYOS. Pode concluir este processo executando os seguintes comandos Azure CLI. Para mais informações, consulte a RHEL BYOS Gold Images na documentação [Azure.](./byos.md) É importante que estejas a executar a mais recente versão do Azure CLI.

   1. Abra uma sessão Azure CLI e autente com a sua conta Azure. Para obter assistência, consulte [iniciar súmis com o Azure CLI](/cli/azure/authenticate-azure-cli).

   1. Verifique se as imagens RHEL BYOS estão disponíveis na sua subscrição executando o seguinte comando CLI. Se não obter resultados aqui, certifique-se de que a sua subscrição Azure está ativada para imagens RHEL BYOS.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Executar o seguinte comando para aceitar os termos do Azure Marketplace para RHEL 7.7 BYOS e RHEL 8.0 BYOS, respectivamente:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
A sua subscrição está agora pronta para implementar RHEL 7.7 ou 8.0 BYOS em máquinas virtuais Azure.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Utilização do JBoss EAP com o modelo BYOS

O JBoss EAP está disponível apenas no Azure através do modelo BYOS. Ao implementar este modelo, precisa de fornecer as suas credenciais RHSM juntamente com o ID do Pool RHSM com direitos EAP válidos. Se não tiver direitos EAP, obtenha uma subscrição de [avaliação EAP JBoss](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) antes de começar.

## <a name="deployment-options"></a>Opções de implementação

Pode implementar o modelo das seguintes formas:

- **PowerShell**. Implementar o modelo executando os seguintes comandos: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Para obter informações sobre a instalação e configuração da Azure PowerShell, consulte a [documentação PowerShell](/powershell/azure/).  

- **Azure CLI**. Implementar o modelo executando os seguintes comandos:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Para obter mais informações sobre a instalação e configuração do Azure CLI, consulte [instalar o CLI](/cli/azure/install-azure-cli).

- **Portal do Azure**. Pode implantar-se no portal Azure indo para os modelos Azure Quickstart, como indicado na secção seguinte. Depois de estar no Quickstart, selecione o **'Implementar para Azure'** ou procurar no botão **GitHub.**

## <a name="azure-quickstart-templates"></a>Modelos de Início Rápido do Azure

Pode começar por utilizar um dos seguintes modelos Quickstart para JBoss EAP na RHEL que cumpre o seu objetivo de implantação:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP em RHEL (VM autónomo)</a>. Isto irá implementar uma aplicação web chamada JBoss-EAP em Azure para JBoss EAP 7.2 ou 7.3 em execução em RHEL 7.7 ou 8.0 VM.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP em RHEL (agrupados, múltiplos VMs)</a>. Isto irá implementar uma aplicação web chamada replicação de sessão eap num cluster JBoss EAP 7.2 ou 7.3 em execução em *n* número de RHEL 7.7 ou 8.0 VMs. O utilizador decide o valor *n.* Todos os VMs são adicionados ao pool de fundo de um equilibrador de carga.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP no RHEL (conjunto de balanças de máquinas virtuais agrupados e virtuais)</a>. Isto irá implementar uma aplicação web chamada replicação de sessão eap num conjunto de escala de máquinas virtuais JBoss EAP 7.2 ou 7.3 em funcionamento em conjuntos de escala de máquinas virtuais RHEL 7.7 ou 8.0.

## <a name="resource-links"></a>Ligações de recursos

* [Benefício Híbrido do Azure](../../windows/hybrid-use-benefit-licensing.md)
* [Configure um aplicativo Java para o Azure App Service](../../../app-service/configure-language-java.md)
* [JBoss EAP no Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP no Azure App Service Linux](../../../app-service/quickstart-java.md)
* [Implementar JBoss EAP no Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [jBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/).
* Saiba mais sobre [jBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/).
* Saiba mais sobre [a Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management).
* Saiba mais sobre [as cargas de trabalho do Red Hat em Azure.](./overview.md)
* Coloque [o JBoss EAP num REL VM ou numa balança de máquinas virtual definida a partir do Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).
* Coloque [o JBoss EAP num RHEL VM ou na balança de máquinas virtual definido a partir dos modelos Azure Quickstart](https://aka.ms/Quickstart-JBoss-EAP).