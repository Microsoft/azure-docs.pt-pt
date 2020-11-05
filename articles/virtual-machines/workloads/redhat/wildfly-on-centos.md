---
title: Início Rápido - WildFly em CentOS
description: Implementar aplicações java para WildFly no CentOS VM
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: 842913b8e9f83ff3b188976da55aef7c909518ca
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359923"
---
# <a name="quickstart-wildfly-on-centos-8"></a>Quickstart: WildFly no CentOS 8

Este Quickstart mostra-lhe como implantar o nó autónomo de WildFly do CentOS 8 VM. É ideal para o desenvolvimento e teste de aplicações java da empresa no Azure. A subscrição do servidor de aplicações não é necessária para implementar este arranque rápido.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. Se não tiver uma subscrição do Azure, pode ativar os [benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou [criar uma conta gratuitamente](https://azure.microsoft.com/pricing/free-trial).

## <a name="use-case"></a>Caso de utilização

WildFly é ideal para o desenvolvimento e teste de aplicações java da empresa em Azure. Listas de tecnologias disponíveis nos perfis de configuração do servidor WildFly 18 estão disponíveis no [WildFly Getting Started Guide](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly).

Pode utilizar o WildFly no modo Autónomo ou Cluster de acordo com a sua caixa de utilização. Você pode garantir alta disponibilidade de aplicações críticas de Jacarta EE pela WildFly em um conjunto de nós. Faça um pequeno número de alterações na configuração da aplicação e, em seguida, implemente a aplicação no cluster. Para saber mais sobre isto, consulte o [Guia de Alta Disponibilidade wildFly.](https://docs.wildfly.org/18/High_Availability_Guide.html)

## <a name="configuration-choice"></a>Escolha de configuração

WildFly pode ser iniciado no modo **Servidor Autónomo** - Uma instância de servidor autónomo é um processo independente, tal como um JBoss Application Server (AS) 3, 4, 5 ou 6 instâncias. Casos autónomos podem ser lançados através do standalone.sh ou standalone.bat scripts de lançamento. Para mais de um caso autónomo, é da responsabilidade do utilizador coordenar a gestão de vários servidores através dos servidores.

Também pode iniciar a instância WildFly com configuração alternativa utilizando ficheiros de configuração disponíveis na pasta de configuração.

Seguem-se os ficheiros de Configuração do Servidor Autónomo:

- standalone.xml (predefinido) - Esta configuração é o ficheiro predefinido utilizado para iniciar a instância WildFly. Contém configuração certificada do Perfil Web de Jacarta com as tecnologias necessárias.
   
- standalone-ha.xml - Configuração certificada jakarta EE Web Profile 8 com elevada disponibilidade (direcionada a aplicações web).
   
- standalone-full.xml - Configuração certificada da Plataforma EE 8 de Jacarta, incluindo todas as tecnologias necessárias para hospedar aplicações Jakarta EE.

- standalone-full-ha.xml - Configuração certificada da Plataforma EE 8 de Jacarta com elevada disponibilidade para hospedar aplicações Jakarta EE.

Para iniciar o seu servidor WildFly autónomo com outra configuração fornecida, utilize o argumento --config do servidor com o ficheiro configurar servidor.

Por exemplo, para utilizar a Plataforma EE 8 de Jacarta com capacidades de agrupamento, utilize o seguinte comando:

```
./standalone.sh --server-config=standalone-full-ha.xml
```

Para saber mais sobre as configurações, consulte o [Guia de Arranque da WildFly.](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations)

## <a name="licensing-support-and-subscription-notes"></a>Notas de licenciamento, suporte e assinatura

A imagem Azure CentOS 8 é uma imagem VM Pay-As-You-Go (PAYG) e não requer que o utilizador obtenha uma licença. A primeira vez que o VM é lançado, a licença de SO da VM será automaticamente ativada e cobrada uma taxa horária. Isto é adicional às taxas de VM linux da Microsoft. Clique em [Linux VM Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux) para mais detalhes. WildFly é gratuito para descarregar e usar e não requer uma subscrição ou licença do Red Hat.

## <a name="how-to-consume"></a>Como consumir

Pode implementar o modelo de três formas:

- Utilizar PowerShell - Implementar o modelo executando os seguintes comandos: (Verifique [a Azure PowerShell](https://docs.microsoft.com/powershell/azure/) para obter informações sobre a instalação e configuração do Azure PowerShell).

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Utilizar O Azure CLI - Implementar o modelo executando os seguintes comandos: (Verifique [a Linha de Comando da Plataforma Transversal Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter mais informações sobre a instalação e configuração da Interface Command-Line plataforma cruzada Azure).

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Use o portal Azure - Implemente o modelo clicando <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">aqui</a> e inicie sessão no seu portal Azure.

## <a name="arm-template"></a>Modelo ARM

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 no CentOS 8 (VM autónomo)</a> - Este é um modelo Quickstart que cria um nó autónomo de WildFly 18.0.1.Final no CentOS 8 VM no seu Grupo de Recursos (RG) que inclui um IP privado para a conta de armazenamento de VM, Virtual e Diagnóstico. Também implementa uma aplicação java de amostra chamada JBoss-EAP em Azure on WildFly.

## <a name="resource-links"></a>Ligações de recursos

* Saiba mais sobre [wildFly 18](https://docs.wildfly.org/18/)
* Saiba mais sobre [as distribuições do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)
* [Azure para documentação de desenvolvedores java](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Próximos passos

Para o ambiente de produção, confira os modelos Red Hat JBoss EAP Azure Quickstart ARM:

Máquina virtual RHEL autónoma com aplicação de amostragem:

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP na RHEL (VM autónomo)</a>

Máquinas virtuais RHEL agrupadas com aplicação de amostragem:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP em RHEL (VMs agrupados)</a>

Conjunto de balança de máquina virtual RHEL agrupado com aplicação de amostragem:

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP no RHEL (conjunto de balança de máquina virtual agrupado)</a>
