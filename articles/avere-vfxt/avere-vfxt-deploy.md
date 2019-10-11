---
title: Implantar avere vFXT para o Azure
description: Etapas para implantar o cluster avere vFXT no Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: rohogue
ms.openlocfilehash: 6ddf950bf2d138a94675ee394109a0d227ea206b
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255448"
---
# <a name="deploy-the-vfxt-cluster"></a>Implementar o cluster vFXT

Este procedimento orienta você pelo uso do assistente de implantação disponível no Azure Marketplace. O assistente implanta automaticamente o cluster usando um modelo de Azure Resource Manager. Depois de inserir os parâmetros no formulário e clicar em **criar**, o Azure concluirá automaticamente estas etapas:

* Cria o controlador de cluster, que é uma VM básica que contém o software necessário para implantar e gerenciar o cluster.
* Configura o grupo de recursos e a infraestrutura de rede virtual, incluindo a criação de novos elementos.
* Cria as VMs do nó de cluster e as configura como o cluster avere.
* Se solicitado, cria um novo contêiner de BLOBs do Azure e o configura como um arquivo de núcleo de cluster.

Depois de seguir as instruções neste documento, você terá uma rede virtual, uma sub-rede, um controlador e um cluster vFXT, conforme mostrado no diagrama a seguir. Este diagrama mostra o filer principal do blob do Azure opcional, que inclui um novo contêiner de armazenamento de BLOBs (em uma nova conta de armazenamento, não mostrado) e um ponto de extremidade de serviço para o armazenamento da Microsoft na sub-rede. 

![diagrama mostrando três retângulos concêntricos com componentes de cluster avere. O retângulo externo é rotulado como ' grupo de recursos ' e contém um hexágono rotulado como ' armazenamento de BLOBs (opcional) '. O próximo retângulo em é rotulado como ' rede virtual: 10.0.0.0/16 ' e não contém nenhum componente exclusivo. O retângulo mais interno é rotulado ' subnet: 10.0.0.0/24 ' e contém uma VM rotulada ' cluster Controller ', uma pilha de três VMs rotuladas como ' vFXT Nodes (cluster vFXT) ' e um hexágono rotulado como ' Service Endpoint '. Há uma seta para conectar o ponto de extremidade de serviço (que está dentro da sub-rede) e o armazenamento de BLOBs (que está fora da sub-rede e vnet, no grupo de recursos). A seta passa pelos limites de sub-rede e rede virtual.](media/avere-vfxt-deployment.png)  

Antes de usar o modelo de criação, verifique se você resolveu estes pré-requisitos:  

1. [Nova assinatura](avere-vfxt-prereqs.md#create-a-new-subscription)
1. [Permissões de proprietário da assinatura](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
1. [Cota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
1. [Ponto de extremidade do serviço de armazenamento (se necessário)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) – necessário para implantações usando uma rede virtual existente e criando o armazenamento de BLOBs

Para obter mais informações sobre etapas de implantação de cluster e planejamento, leia [planejar o sistema avere vFXT e a](avere-vfxt-deploy-plan.md) [visão geral da implantação](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Criar o avere vFXT para o Azure

Acesse o modelo de criação no portal do Azure procurando por avere e selecionando "avere vFXT for Azure ARM template". 

![Janela do navegador mostrando o portal do Azure com trilhas de pão "novo > Marketplace > tudo". Na página tudo, o campo de pesquisa tem o termo "avere" e o segundo resultado, "avere vFXT para o modelo ARM do Azure" é descrito em vermelho para realçá-lo.](media/avere-vfxt-template-choose.png)

Depois de ler os detalhes na página de modelo do avere vFXT para o Azure ARM, clique em **criar** para começar. 

![Azure Marketplace com a primeira página do modelo de implantação mostrando](media/avere-vfxt-deploy-first.png)

O modelo é dividido em quatro etapas-duas páginas de coleta de informações, além de etapas de validação e confirmação. 

* A página um concentra-se nas configurações da VM do controlador de cluster. 
* A página dois coleta parâmetros para criar o cluster e recursos associados, como sub-redes e armazenamento. 
* A página três resume as configurações e valida a configuração. 
* A página quatro explica os termos e condições de software e permite que você inicie o processo de criação de cluster. 

## <a name="page-one-parameters---cluster-controller-information"></a>Parâmetros da página um-informações do controlador de cluster

A primeira página do modelo de implantação reúne informações sobre o controlador de cluster. 

![Primeira página do modelo de implantação](media/avere-vfxt-deploy-1.png)

Preencha as seguintes informações:

* **Nome do controlador de cluster** -defina o nome da VM do controlador de cluster.

* **Nome de usuário do controlador** -preencha o nome de usuário raiz para a VM do controlador de cluster. 

* **Tipo de autenticação** -escolha a senha ou a autenticação de chave pública SSH para conectar-se ao controlador. O método de chave pública SSH é recomendado; Leia [como criar e usar chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) se precisar de ajuda.

* **Senha** ou **chave pública SSH** -dependendo do tipo de autenticação selecionado, você deve fornecer uma chave pública RSA ou uma senha nos campos a seguir. Essa credencial é usada com o nome de usuário fornecido anteriormente.

* **Assinatura** – selecione a assinatura para o avere vFXT. 

* **Grupo de recursos** – selecione um grupo de recursos vazio existente para o cluster avere vFXT ou clique em "criar novo" e insira um novo nome de grupo de recursos. 

* **Local** -selecione o local do Azure para o cluster e os recursos.

Clique em **OK** quando terminar. 

> [!NOTE]
> Se você quiser que o controlador de cluster tenha um endereço IP voltado para o público, crie uma nova rede virtual para o cluster em vez de selecionar uma rede existente. Essa configuração está na página dois.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Página dois parâmetros-informações do cluster vFXT

A segunda página do modelo de implantação permite que você defina o tamanho do cluster, o tipo de nó, o tamanho do cache e os parâmetros de armazenamento, entre outras configurações. 

![Segunda página do modelo de implantação](media/avere-vfxt-deploy-2.png)

* **Contagem de nós de cluster avere vFXT** -escolha o número de nós a serem usados no cluster. O mínimo é de três nós e o máximo é de doze. 

* **Senha de administração do cluster** – crie a senha para a administração do cluster. Essa senha será usada com o nome de usuário ```admin``` para entrar no painel de controle do cluster para monitorar o cluster e definir as configurações.

* **Nome do cluster avere vFXT** -dê um nome exclusivo ao cluster. 

* **Tamanho** – esta seção mostra o tipo de VM que será usado para os nós de cluster. Embora haja apenas uma opção recomendada, o link **alterar tamanho** abre uma tabela com detalhes sobre esse tipo de instância e um link para uma calculadora de preços.  

* **Tamanho do cache por nó** – o cache do cluster é distribuído entre os nós do cluster, portanto, o tamanho total do cache no cluster avere vFXT será o tamanho do cache por nó multiplicado pelo número de nós. 

  A configuração recomendada é usar 4 TB por nó para nós Standard_E32s_v3.

* **Rede virtual** – defina uma nova vnet para hospedar o cluster ou selecione uma vnet existente que atenda aos pré-requisitos descritos em [planejar seu sistema avere vFXT](avere-vfxt-deploy-plan.md#resource-group-and-network-infrastructure). 

  > [!NOTE]
  > Se você criar uma nova vnet, o controlador de cluster terá um endereço IP público para que você possa acessar a nova rede privada. Se você escolher uma vnet existente, o controlador de cluster será configurado sem um endereço IP público. 
  > 
  > Um endereço IP publicamente visível no controlador de cluster fornece acesso mais fácil ao cluster vFXT, mas cria um pequeno risco de segurança. 
  >  * Um endereço IP público no controlador de cluster permite que você o use como um host de salto para se conectar ao cluster avere vFXT de fora da sub-rede privada.
  >  * Se você não configurar um endereço IP público no controlador, deverá usar outro host de salto, uma conexão VPN ou o ExpressRoute para acessar o cluster. Por exemplo, crie o controlador em uma rede virtual que já tenha uma conexão VPN configurada.
  >  * Se você criar um controlador com um endereço IP público, deverá proteger a VM do controlador com um grupo de segurança de rede. Por padrão, o avere vFXT para implantação do Azure cria um grupo de segurança de rede e restringe o acesso de entrada apenas para a porta 22 para controladores com endereços IP públicos. Você pode proteger ainda mais o sistema bloqueando o acesso ao seu intervalo de endereços IP de origem – ou seja, permitir somente conexões de computadores que você pretende usar para acesso ao cluster.

  O modelo de implantação também configura a nova vnet com um ponto de extremidade de serviço de armazenamento para o armazenamento de BLOBs do Azure e com o controle de acesso à rede bloqueado somente para IPs da sub-rede do cluster. 

* **Sub-rede** -escolha uma sub-rede da rede virtual existente ou crie uma nova. 

* **Criar e usar o armazenamento de BLOBs** -escolha **true** para criar um novo contêiner de blob do Azure e configurá-lo como armazenamento de back-end para o novo cluster avere vFXT. Essa opção também cria uma nova conta de armazenamento dentro do mesmo grupo de recursos que o cluster e um ponto de extremidade do serviço de armazenamento da Microsoft dentro da sub-rede do cluster. 
  
  Se você fornecer uma rede virtual existente, ela deverá ter um ponto de extremidade de serviço de armazenamento antes de criar o cluster. (Para obter mais informações, leia [planejar o sistema avere vFXT](avere-vfxt-deploy-plan.md).)

  Defina esse campo como **false** se não quiser criar um novo contêiner. Nesse caso, você deve anexar e configurar o armazenamento depois de criar o cluster. Leia [Configurar armazenamento](avere-vfxt-add-storage.md) para obter instruções. 

* **(Novo) conta de armazenamento** -se estiver criando um novo contêiner de blob do Azure, insira um nome para a nova conta de armazenamento. 

## <a name="validation-and-purchase"></a>Validação e compra

A página três resume a configuração e valida os parâmetros. Após a validação ser realizada com sucesso, clique no botão **OK** para continuar. 

![Terceira página do modelo de implantação-validação](media/avere-vfxt-deploy-3.png)

Na página quatro, insira as informações de contato necessárias e clique no botão **criar** para aceitar os termos e criar o avere vFXT para o cluster do Azure. 

![Quarta página do modelo de implantação – termos e condições, botão criar](media/avere-vfxt-deploy-4.png)

A implantação do cluster leva 15-20 minutos.

## <a name="gather-template-output"></a>Coletar saída de modelo

Quando o modelo avere vFXT conclui a criação do cluster, ele gera algumas informações importantes sobre o novo cluster. 

> [!TIP]
> Certifique-se de copiar o endereço IP de gerenciamento da saída do modelo. Você precisa desse endereço para administrar o cluster.

Para encontrar essas informações, siga este procedimento:

1. Vá para o grupo de recursos do seu controlador de cluster.

1. No lado esquerdo, clique em **implantações**e em **Microsoft-avere. vfxt-template**.

   ![Página do portal do grupo de recursos com implantações selecionadas à esquerda e Microsoft-avere. vfxt-template mostrado em uma tabela sob o nome da implantação](media/avere-vfxt-outputs-deployments.png)

1. No lado esquerdo, clique em **saídas**. Copie os valores em cada um dos campos. 

   ![página de saídas mostrando os valores SSHSTRING, RESOURCE_GROUP, LOCATION, NETWORK_RESOURCE_GROUP, NETWORK, SUBNET, SUBNET_ID, VSERVER_IPs e MGMT_IP nos campos à direita dos rótulos](media/avere-vfxt-outputs-values.png)

## <a name="next-step"></a>Passo seguinte

Agora que o cluster está em execução e você sabe seu endereço IP de gerenciamento, você pode [se conectar à ferramenta de configuração de cluster](avere-vfxt-cluster-gui.md) para habilitar o suporte, adicionar armazenamento, se necessário, e personalizar outras configurações de cluster.
