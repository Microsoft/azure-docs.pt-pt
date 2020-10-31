---
title: Quickstart - Aplicação empresarial JBoss (EAP) em Red Hat Enterprise Linux (RHEL) a Azure VM e Conjunto de Escala de Máquinas Virtuais
description: Como implementar aplicações java da empresa usando o Red Hat JBoss EAP num Conjunto de VM RHEL E de Máquina Virtual.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135441"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Implementar aplicações da Empresa Java para a Azure com jBoss EAP na Red Hat Enterprise Linux

Estes modelos Quickstart irão mostrar-lhe como implantar [JBoss EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) com [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) em Azure Virtual Machines (VM) e Conjuntos de Escala de Máquina Virtual. Você terá uma amostra java app na implementação para validar a implementação. JBoss EAP é uma plataforma de servidor de aplicações de código aberto. Oferece segurança, escalabilidade e desempenho para as suas aplicações Java. A RHEL é uma plataforma de sistema operativo de código aberto (OS). Permite a escala de aplicações existentes e o lançamento de tecnologias emergentes em todos os ambientes. JBoss EAP e RHEL incluem tudo o que é necessário para construir, executar, implementar e gerir aplicações java da empresa em qualquer ambiente. É uma ótima solução de código aberto para instalações, ambientes virtuais, e em nuvens privadas, públicas ou híbridas.

## <a name="prerequisite"></a>Pré-requisito 

* Uma conta Azure com uma subscrição ativa. Para obter uma subscrição do Azure, ative os seus [créditos Azure para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou [crie uma conta gratuitamente](https://azure.microsoft.com/pricing/free-trial).

* Instalação JBoss EAP - Você precisa ter uma Conta de Chapéu Vermelho com Direito de Gestão de Assinaturas de Chapéu Vermelho (RHSM) para JBoss EAP. Este direito permitir-lhe-á descarregar a versão EAP testada e certificada do JBoss.  Se não tiver direito EAP, obtenha uma subscrição de [avaliação EAP JBoss](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) antes de começar. Para criar uma nova subscrição do Red Hat, vá ao [Portal do Cliente da Red Hat](https://access.redhat.com/) e crie uma conta.
F
* [Interface Azure Command-Line](https://docs.microsoft.com/cli/azure/overview).

* Opções RHEL - Escolha entre Pay-As-You-Go (PAYG) ou Bring-Your-Own-Subscription (BYOS). Com o BYOS, precisa de ativar a sua imagem de acesso à [nuvem de chapéu vermelho](https://access.redhat.com/) RHEL Antes de implementar o modelo Quickstart.

## <a name="java-ee--jakarata-ee-application-migration"></a>Migração de aplicações Java EE / Jakarata EE

### <a name="migrate-to-jboss-eap"></a>Migrar para JBoss EAP
JBoss EAP 7.2 e 7.3 são implementações certificadas das especificações Java Enterprise Edition (Java EE) 8 e Jakarta EE 8. O JBoss EAP fornece opções pré-configuradas para funcionalidades como agrupamento de alta disponibilidade (HA), mensagens e caching distribuído. Também permite que os utilizadores escrevam, implementem e executem aplicações utilizando as várias APIs e serviços que o JBoss EAP fornece.  Para informações adicionais sobre jBoss EAP, visite [Introdução ao JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) ou [Introdução ao JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Candidaturas no JBoss EAP

* Aplicações de Serviços Web - Os serviços web fornecem um meio padrão de interoperação entre diferentes aplicações de software. Cada aplicação pode ser executada em diferentes plataformas e quadros. Estes serviços web facilitam a comunicação interna e heterogénea do subsistema. Para saber mais, visite [o Desenvolvimento de Aplicações de Serviços Web no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) ou [desenvolver aplicações de serviços web no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* Aplicações Enterprise Java Beans (EJB) - EJB 3.2 é uma API para o desenvolvimento de aplicações distribuídas, transacionais, seguras e portáteis java EE e Jacarta EE. O EJB utiliza componentes do lado do servidor chamados Enterprise Beans para implementar a lógica de negócio de uma aplicação de forma dissociada que incentiva a reutilização. Para saber mais, visite [o Desenvolvimento de Aplicações EJB no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) ou [o Desenvolvimento de Aplicações EJB no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Aplicações de Hibernate - Desenvolvedores e administradores podem desenvolver e implementar aplicações Java Persistence API (JPA)/Hibernate com JBoss EAP. Hibernate Core é uma estrutura de mapeamento relacional de objetos para a língua java. Fornece um quadro para mapear um modelo de domínio orientado para objetos para uma base de dados relacional, permitindo que as aplicações evitem a interação direta com a base de dados. O Gestor de Entidades Hibernate implementa as interfaces de programação e as regras do ciclo de vida definidas pela [especificação JPA 2.1](https://www.jcp.org/en/jsr/overview). Juntamente com as anotações de hibernação, este invólucro implementa uma solução JPA completa (e autónoma) em cima do Núcleo hibernado maduro. Para saber mais sobre hibernação, visite [JPA em EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) ou  [Jakarta Persistence em EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Kit de ferramentas de migração de chapéu vermelho para aplicações (MTA)
[O Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) é uma ferramenta de migração para servidores de aplicações Java. Utilize esta ferramenta para migrar de outro servidor de aplicações para o JBoss EAP. Funciona com plugins IDE para [Eclipse IDE,](https://www.eclipse.org/ide/) [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview)e [Visual Studio Code (VS Code)](https://code.visualstudio.com/docs/languages/java) para Java.  O MTA é uma ferramenta de código aberto e gratuita que:
* Automatizar a análise da aplicação
* Estimativa do esforço de apoio
* Acelerar a migração de códigos
* Contentorização de apoio
* Integra-se com o Azure Workload Builder

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrar JBoss EAP de bordo para Azure
A oferta do Azure Marketplace da JBoss EAP na RHEL instalará e providenciará em VMs Azure em menos de 20 minutos. Você pode acessar estas ofertas a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com/)

Esta oferta do Marketplace inclui várias combinações de versões EAP e RHEL para suportar os seus requisitos. JBoss EAP é sempre Bring-Your-Your-Own-Subscription (BYOS), mas para RHEL OS pode escolher entre BYOS ou Pay-As-You-Go (PAYG). A oferta do Azure Marketplace inclui opções de plano para JBoss EAP na RHEL como VMs autónomos ou agrupados:

* JBoss EAP 7.2 em RHEL 7.7 VM (PAYG)
* JBoss EAP 7.2 em RHEL 8.0 VM (PAYG)
* JBoss EAP 7.3 em RHEL 8.0 VM (PAYG)
* JBoss EAP 7.2 em RHEL 7.7 VM (BYOS)
* JBoss EAP 7.2 em RHEL 8.0 VM (BYOS)
* JBoss EAP 7.3 em RHEL 8.0 VM (BYOS)

Juntamente com as ofertas do Azure Marketplace, existem modelos Quickstart disponibilizados para que possa começar a sua jornada de migração Azure. Estes Quickstarts incluem modelos e scripts do Azure Resource Manager (ARM) pré-construídos para implementar o JBoss EAP no RHEL em várias configurações e combinações de versão. Terá:

* Balanceador de Carga (LB)
* IP privado para equilibrar carga e VMs
* Rede Virtual (VNET) com uma única sub-rede
* Configuração VM (cluster ou autónomo)
* Uma aplicação java de amostra

A arquitetura de solução para estes modelos inclui:

* JBoss EAP em RHEL VM autónomo
* JBoss EAP agrupado em vários VMs RHEL
* JBoss EAP agrupado usando conjunto de balança de máquina virtual Azure

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migração linux de carga de trabalho para JBoss EAP
O Azure Workload Builder simplificará o processo de prova de conceito (POC), avaliação e migração para aplicações java no local para Azure. O Workload Builder integra-se com a ferramenta Azure Migrate Discovery para identificar servidores EAP JBoss. Em seguida, gerará dinamicamente um livro de jogadas Ansible para a implementação do servidor EAP JBoss. Aproveita a ferramenta Red Hat MTA para migrar servidores de outros servidores de aplicações para JBoss EAP. As medidas para simplificar a migração incluem:
* Avaliação - Clusters EAP JBoss usando Azure VM ou Conjunto de Balanças de Máquinas Virtuais
* Avaliação - Aplicações e infraestruturas de digitalização
* Configuração da infraestrutura - Cria perfil de carga de trabalho
* Implantação e teste - Implementar, migrar e testar carga de trabalho
* Configuração pós-implantação - Integra-se com dados, monitorização, segurança, backup e muito mais

## <a name="server-configuration-choice"></a>Escolha de configuração do servidor

Para a implementação do RHEL VM, pode escolher entre PAYG ou BYOS. Imagens do [Mercado Azure](https://azuremarketplace.microsoft.com) falham no PAYG. Implemente um REL VM do tipo BYOS se tiver a sua própria imagem RHEL OS. Certifique-se de que a sua conta RHSM tem direito BYOS via F Cloud Access antes de implementar os VMs ou o Conjunto de Escala de Máquina Virtual.

O JBoss EAP tem poderosas capacidades de gestão, bem como fornece funcionalidade e APIs às suas aplicações. Estas capacidades de gestão diferem consoante o modo de funcionamento utilizado para iniciar o JBoss EAP. É suportado no RHEL e No Windows Server. O JBoss EAP oferece um modo de funcionamento autónomo para gerir instâncias discretas. Também oferece um modo de funcionamento de domínio gerido para gerir grupos de instâncias a partir de um único ponto de controlo. Nota: Os domínios geridos pela JBoss EAP não são suportados no Microsoft Azure uma vez que a funcionalidade De Alta Disponibilidade (HA) é gerida por serviços de infraestrutura Azure. A variável ambiental chamada *EAP_HOME* é usada para denotar o caminho para a instalação JBoss EAP.

**Iniciar o JBoss EAP como um Servidor Autónomo** - o seguinte comando é como iniciar o serviço EAP em modo autónomo.

```
$EAP_HOME/bin/standalone.sh
```
    
Este script de arranque utiliza o ficheiro EAP_HOME/bin/autónomo.conf para definir algumas preferências predefinidos, tais como opções JVM. As definições podem ser personalizadas neste ficheiro. O JBoss EAP utiliza o ficheiro de configuração standalone.xml para iniciar o modo autónomo por padrão, mas pode ser iniciado usando um modo diferente. Para obter mais informações sobre os ficheiros de configuração autónomo disponíveis e como utilizá-los, consulte os [Ficheiros de Configuração do Servidor Autónomo para ficheiros de configuração EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) ou de configuração de servidor autónomo para a secção [EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) Para iniciar o JBoss EAP com uma configuração diferente, utilize o argumento --config do servidor. Por exemplo:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Para uma lista completa de todos os argumentos de script de arranque disponíveis e seus propósitos, use o argumento de ajuda ou consulte os Argumentos de Tempo de Execução do Servidor na secção [EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) ou [Server Runtime Arguments na secção EAP 7.3.](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)
    
O JBoss EAP também pode trabalhar em modo cluster. Consulte [a visão geral dos Clusters no EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) ou [ Visão geral dos Clusters no EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) para saber mais. As mensagens de cluster JBoss EAP permitem agrupar servidores de mensagens EAP JBoss para partilhar a carga de processamento de mensagens. Cada nó ativo no cluster é um servidor de mensagens JBoss EAP ativo, que gere as suas próprias mensagens e lida com as suas próprias ligações.

## <a name="support-and-subscription-notes"></a>Notas de apoio e subscrição
Estes modelos Quickstart são oferecidos como: 

- RHEL OS é oferecido como PAYG ou BYOS via Modelo Red Hat Gold Image
- JBoss EAP é oferecido apenas como BYOS

#### <a name="using-rhel-os-with-payg-model"></a>Utilização de RHEL OS com modelo PAYG

Por padrão, estes modelos Quickstart utilizam a imagem ON-Demand RHEL 7.7 ou 8.0 PAYG da Galeria Azure. As imagens PAYG terão uma taxa adicional de subscrição DE RÉL por hora, além dos custos normais de computação, rede e armazenamento. Ao mesmo tempo, o caso será registado na sua assinatura Red Hat. Isto significa que vais usar um dos teus direitos. Esta imagem PAYG levará a "faturação dupla". Pode evitar este problema construindo a sua própria imagem RHEL. Para saber mais, leia este artigo da Red Hat Knowledge Base (KB) sobre [Como providenciar um RHEL VM para o Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Ou ativar a sua imagem [de acesso à nuvem de chapéu vermelho](https://access.redhat.com/) RHEL Gold Image.

Confira [os preços da Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) para obter detalhes sobre os preços do PAYG VM. Para utilizar o RHEL no modelo PAYG, necessitará de uma Assinatura Azure com o método de pagamento especificado para [o MERCADO RHEL 7.7 Azure](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) ou [o MERCADO RHEL 8.0 Azure.](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM) Estas ofertas requerem um método de pagamento a especificar na Subscrição Azure.

#### <a name="using-rhel-os-with-byos-model"></a>Utilização do RHEL OS com modelo BYOS

Para utilizar o BYOS para RHEL OS, precisa de ter uma subscrição válida do Red Hat com direitos de utilização do RHEL OS em Azure. Preencha os seguintes pré-requisitos antes de implementar este modelo Quickstart:

1. Certifique-se de que tem direitos RHEL OS e JBoss EAP anexados à sua assinatura Red Hat.
2. Autorize o seu ID de subscrição Azure para utilizar imagens RHEL BYOS. Siga a [documentação da Red Hat Subscription Management (RHSM)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) para concluir o processo, que inclui estes passos:

    2.1 Ative o Microsoft Azure como fornecedor no seu Painel de Acesso à Nuvem de Chapéu Vermelho.

    2.2 Adicione os seus IDs de subscrição Azure.

    2.3 Permitir novos produtos para o Acesso à Nuvem no Microsoft Azure.
    
    2.4 Ative as imagens de ouro do chapéu vermelho para a sua assinatura Azure. Para mais informações, leia o capítulo sobre [Ativar e manter subscrições para o Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure) para mais detalhes.

    2.5 Aguarde que as Imagens Gold do Chapéu Vermelho estejam disponíveis na sua assinatura Azure. Estas Imagens Douradas estão normalmente disponíveis dentro de 3 horas após a submissão.
    
3. Aceite os Termos e Condições do Mercado Azure para imagens RHEL BYOS. Pode completar este processo executando os comandos Azure Command-Line Interface (CLI), conforme dado abaixo. Para mais informações, consulte a [documentação RHEL BYOS Gold Images na Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) para obter mais detalhes. É importante que estejas a executar a mais recente versão do Azure CLI.

    3.1 Inicie uma sessão CLI Azure e autente com a sua conta Azure. Consulte a [inscrição com a Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) para obter assistência. Certifique-se de que está a executar a versão mais recente do Azure CLI antes de seguir em frente.

    3.2 Verifique se as imagens RHEL BYOS estão disponíveis na sua subscrição executando o seguinte comando CLI. Se não obter nenhum resultado aqui, consulte #2 e certifique-se de que a sua subscrição Azure está ativada para imagens RHEL BYOS.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3.3 Executar o seguinte comando para aceitar os Termos do Mercado para RHEL 7.7 BYOS e RHEL 8.0 BYOS, respectivamente.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. A sua subscrição está agora pronta para implementar RHEL 7.7 ou 8.0 BYOS em máquinas virtuais Azure.

#### <a name="using-jboss-eap-with-byos-model"></a>Utilização JBoss EAP com modelo BYOS

O JBoss EAP está disponível apenas no Azure através do modelo BYOS. Ao implementar este modelo, você precisa fornecer as suas credenciais RHSM juntamente com o RHSM Pool ID com direitos EAP válidos. Se não tiver direito EAP, obtenha uma subscrição de [avaliação EAP JBoss](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) antes de começar.

## <a name="how-to-consume"></a>Como consumir

Pode implementar o modelo de três maneiras:

- Utilizar PowerShell - Implementar o modelo executando os seguintes comandos: (Verifique [a Azure PowerShell](https://docs.microsoft.com/powershell/azure/) para obter informações sobre a instalação e configuração do Azure PowerShell).

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Utilizar O Azure CLI - Implementar o modelo executando os seguintes comandos: (Verifique [O Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter mais informações sobre a instalação e configuração do CLI Azure).

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Use o portal Azure - Pode implementar no portal Azure indo para os *modelos Azure Quickstart* como indicado na secção abaixo. Uma vez no Quickstart, clique em **Implementar para Azure** ou navegue no botão **GitHub.**

## <a name="azure-quickstart-templates"></a>Modelos Azure QuickStart

Pode começar com um dos modelos Quickstart da combinação EAP JBoss na combinação RHEL que cumpre o seu objetivo de implementação. Segue-se a lista de modelos quickstart disponíveis.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP em RHEL (VM autónomo)</a> - Esta irá implementar uma aplicação web chamada JBoss-EAP em Azure para JBoss EAP 7.2 ou 7.3 em execução em RHEL 7.7 ou 8.0 VM.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP em RHEL (clustered, multi-VMs)</a> - Esta aplicação web chamada eap-session-replication em JBoss EAP 7.2 ou 7.3 cluster running on 'n' número RHEL 7.7 ou 8.0 VMs. O valor 'n' é decidido pelo utilizador. Todos os VMs são adicionados ao pool de backend de um Balancer de Carga.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP em RHEL (clustered, Virtual Machine Scale set)</a> - Esta aplicação web chamada replicação de sessão eap em JBoss EAP 7.2 ou 7.3 cluster em execução em instâncias RHEL 7.7 ou 8.0 Virtual Machine Scale set.

## <a name="resource-links"></a>Links de recursos:

* [Benefícios Híbridos Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Configurar uma aplicação Java para o Azure App Service](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP no Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP no Azure App Service Linux](https://docs.microsoft.com/azure/app-service/quickstart-java) Arranque rápido
* [Como implementar o JBoss EAP no tutorial do Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [jBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Saiba mais sobre [jBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Saiba mais sobre [a Gestão de Assinaturas de Chapéu Vermelho](https://access.redhat.com/products/red-hat-subscription-management)
* Microsoft docs para [Chapéu Vermelho em Azure](https://aka.ms/rhel-docs)
* Implementar [JBoss EAP no RHEL VM/Virtual Machine Scale set a partir do Mercado Azure](https://aka.ms/AMP-JBoss-EAP)
* Implementar [JBoss EAP no RHEL VM/Virtual Machine Scale set a partir de Azure Quickstart](https://aka.ms/Quickstart-JBoss-EAP)
