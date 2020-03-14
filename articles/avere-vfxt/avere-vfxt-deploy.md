---
title: Implementar Avere vFXT para Azure
description: Passos para implantar o cluster Avere vFXT em Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252600"
---
# <a name="deploy-the-vfxt-cluster"></a>Implementar o cluster vFXT

Este procedimento acompanha-o através do assistente de implantação disponível no Mercado Azure. O assistente implanta automaticamente o cluster utilizando um modelo de Gestor de Recursos Azure. Depois de introduzir os parâmetros no formulário e clicar em **Criar,** o Azure completa automaticamente estas tarefas:

* Cria o controlador de cluster, que é um VM básico que contém o software necessário para implementar e gerir o cluster.
* Cria um grupo de recursos e infraestruturas de rede virtual, incluindo a criação de novos elementos.
* Cria os VMs do nó de cluster e os confunde como o aglomerado de Avere.
* Se solicitado, cria um novo recipiente Azure Blob e configura-o como um filer de núcleo de cluster.

Depois de seguir as instruções deste documento, terá uma rede virtual, uma subnet, um controlador de cluster e um cluster vFXT, como mostrado no diagrama seguinte. Este diagrama mostra o ficheiro central opcional do Azure Blob, que inclui um novo recipiente de armazenamento Blob (numa nova conta de armazenamento, não mostrado) e um ponto final de serviço para o armazenamento da Microsoft dentro da subnet.

![diagrama mostrando três retângulos concêntricos com componentes de cluster Avere. O retângulo exterior é rotulado como "Grupo de Recursos" e contém um hexágono com a etiqueta "Armazenamento blob (opcional)". O próximo retângulo é rotulado como "Rede Virtual: 10.0.0.0.16" e não contém componentes únicos. O retângulo mais íntimo é rotulado como 'Subnet:10.0.0.0/24' e contém um VM com a marca "Controlador de cluster", uma pilha de três VMs com a marca "nóvFXT cluster)", e um hexágono com a marca "Ponto final de serviço" do hexágono. Existe uma seta que liga o ponto final de serviço (que está dentro da sub-rede) e o armazenamento de bolhas (que está fora da sub-rede e da rede, no grupo de recursos). A seta passa através da subnet e dos limites da rede virtual.](media/avere-vfxt-deployment.png)

Antes de utilizar o modelo de criação, certifique-se de que abordou estes pré-requisitos:  

* [Nova subscrição](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Permissões do proprietário da subscrição](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Quota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* Ponto final do serviço de [armazenamento (se necessário)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) - Necessário para implementações que utilizem uma rede virtual existente e criem armazenamento de bolhas

Para obter mais informações sobre os passos de implementação e planeamento do cluster, leia [Planifique o seu sistema Avere vFXT](avere-vfxt-deploy-plan.md) e a visão geral da [implantação](avere-vfxt-deploy-overview.md).

## <a name="create-the-avere-vfxt-for-azure"></a>Crie o Avere vFXT para o Azure

Aceda ao modelo de criação no portal Azure, procurando a Avere e selecionando "Avere vFXT para modelo de ARM Azure".

![Janela do navegador mostrando o portal Azure com migalhas de pão "New > Marketplace > Tudo". Na página Everything, o campo de pesquisa tem o termo "avere" e o segundo resultado, "Avere vFXT for Azure ARM Template" está delineado a vermelho para realçá-lo.](media/avere-vfxt-template-choose.png)

Depois de ler os detalhes no Avere vFXT para a página do Modelo Arm Azure, clique no botão **Criar** para começar.

![Mercado azure com a primeira página do modelo de implementação mostrando](media/avere-vfxt-deploy-first.png)

O modelo é dividido em quatro passos - duas páginas de recolha de informação, além de etapas de validação e confirmação.

* A página um reúne definições para o controlador de cluster VM.
* A página dois recolhe parâmetros para a criação do cluster e recursos adicionais como subredes e armazenamento.
* A página três resume as suas escolhas e valida a configuração.
* A página quatro explica os termos e condições do software e permite-lhe iniciar o processo de criação de cluster.

## <a name="page-one-parameters---cluster-controller-information"></a>Página um parâmetros - informação do controlador de cluster

A primeira página do modelo de implementação centra-se no controlador de cluster.

![Primeira página do modelo de implementação](media/avere-vfxt-deploy-1.png)

Preencha as seguintes informações:

* **Nome** do controlador de cluster - Desloque o nome para o controlador de cluster VM.

* **Nome de utilizador do controlador** - Desloque o nome de utilizador raiz para o VM do controlador de cluster.

* Tipo de **autenticação** - Escolha a autenticação da chave pública SSH ou ssh para a ligação ao controlador. Recomenda-se o método-chave público ssh; ler [Como criar e utilizar teclas SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) se precisar de ajuda.

* **Palavra-passe** ou **chave pública SSH** - Dependendo do tipo de autenticação selecionado, deve fornecer uma chave pública RSA ou uma palavra-passe nos campos seguintes. Esta credencial é utilizada com o nome de utilizador fornecido anteriormente.

* **Subscrição** - Selecione a subscrição para o Avere vFXT.

* **Grupo de recursos** - Selecione um grupo de recursos vazios existente para o cluster Avere vFXT, ou clique em "Criar novo" e introduza um novo nome de grupo de recursos.

* **Localização** - Selecione a localização Azure para o seu cluster e recursos.

Clique **ok** quando terminar.

> [!NOTE]
> Se pretender que o controlador de cluster tenha um endereço IP virado para o público, crie uma nova rede virtual para o cluster em vez de selecionar uma rede existente. Esta definição está na página dois.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Página dois parâmetros - informação do cluster vFXT

A segunda página do modelo de implementação permite-lhe definir o tamanho do cluster, o tipo de nó, o tamanho da cache e os parâmetros de armazenamento, entre outras definições.

![Segunda página do modelo de implementação](media/avere-vfxt-deploy-2.png)

* Contagem de nódeo cluster **Avere vFXT** - Escolha o número de nós no cluster. O mínimo é de três nóduos e o máximo é de doze.

* **Password de administração** de cluster - Crie a palavra-passe para a administração de clusters. Esta palavra-passe é utilizada com o nome de utilizador ```admin``` para iniciar sessão no painel de controlo do cluster, onde pode monitorizar as definições do cluster e configurar as definições do cluster.

* Nome do **cluster Avere vFXT** - Dê ao cluster um nome único.

* **Tamanho** - Esta secção mostra o tipo VM que será utilizado para os nós do cluster. Embora haja apenas uma opção recomendada, o link **De tamanho Change** abre uma tabela com detalhes sobre este tipo de caso e uma ligação a uma calculadora de preços.

* **Tamanho de cache por nó** - A cache do cluster está espalhada pelos nós do cluster, por isso o tamanho total da cache no seu cluster Avere vFXT será deste tamanho multiplicado pelo número de nós.

  Configuração recomendada: Utilize 4 TB por nó para Standard_E32s_v3 nó.

* **Rede virtual** - Defina uma nova rede virtual para alojar o cluster, ou selecione uma rede existente que satisfaça os pré-requisitos descritos no [plano do seu sistema Avere vFXT](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Se criar uma nova rede virtual, o controlador de cluster terá um endereço IP público para que possa aceder à nova rede privada. Se escolher uma rede virtual existente, o controlador de cluster é configurado sem um endereço IP público.
  >
  > Um endereço IP publicamente visível no controlador de cluster proporciona um acesso mais fácil ao cluster vFXT, mas cria um pequeno risco de segurança.
  >* Um endereço IP público no controlador de cluster permite-lhe usá-lo como hospedeiro de salto para ligar ao cluster Avere vFXT de fora da subnet privada.
  >* Se não tiver um endereço IP público no controlador, precisa de outro hospedeiro de salto, uma ligação VPN ou ExpressRoute para aceder ao cluster. Por exemplo, utilize uma rede virtual existente que já tenha uma ligação VPN configurada.
  >* Se criar um controlador com um endereço IP público, deve proteger o VM do controlador com um grupo de segurança de rede. Por predefinição, o Avere vFXT para a implantação do Azure cria um grupo de segurança de rede que restringe o acesso à entrada apenas na porta 22 para controladores com endereços IP públicos. Pode ainda proteger o sistema bloqueando o acesso ao seu leque de endereços de origem IP - isto é, apenas permitir ligações de máquinas que pretende utilizar para acesso ao cluster.

  Uma nova rede virtual também está configurada com um ponto final de serviço de armazenamento para armazenamento Azure Blob e com controlo de acesso de rede bloqueado para permitir apenas IPs da subnet cluster.

* **Subnet** - Escolha uma sub-rede ou crie uma nova.

* **Criar e utilizar o armazenamento blob** - Escolha **verdadeiro** para criar um novo recipiente Azure Blob e configurá-lo como armazenamento de back-end para o novo cluster Avere vFXT. Esta opção também cria uma nova conta de armazenamento no grupo de recursos do cluster, e cria um ponto final do serviço de armazenamento da Microsoft dentro da subnet cluster.
  
  Se fornecer uma rede virtual existente, deve ter um ponto final de serviço de armazenamento antes de criar o cluster. (Para mais informações, leia [Planifique o seu sistema Avere vFXT](avere-vfxt-deploy-plan.md).)

  Desloque este campo a **falso** se não quiser criar um novo recipiente. Neste caso, deve anexar e configurar o armazenamento depois de criar o cluster. Leia o armazenamento de [configurar](avere-vfxt-add-storage.md) para obter instruções.

* **(Nova) conta** de armazenamento - Se criar um novo recipiente Azure Blob, insira um nome para a nova conta de armazenamento.

## <a name="validation-and-purchase"></a>Validação e compra

A página três resume a configuração e valida os parâmetros. Após o sucesso da validação, verifique o resumo e clique no botão **OK.**

> [!TIP]
> Pode guardar as definições de criação deste cluster clicando no modelo de **descarregamento e** no link de parâmetros ao lado do botão **OK.** Esta informação pode ser útil se precisar de criar um cluster semelhante mais tarde - por exemplo, para criar um cluster de substituição num cenário de recuperação de desastres. (Leia [a orientação](disaster-recovery.md) de recuperação de desastres para saber mais.)

![Terceira página do modelo de implementação - validação](media/avere-vfxt-deploy-3.png)

A página quatro dá os termos de uso e links para informações de privacidade e preços.

Introduza quaisquer informações de contacto em falta e, em seguida, clique no botão **Criar** para aceitar os termos e criar o Avere vFXT para o cluster Azure.

![Quarta página do modelo de implementação - termos e condições, criar botão](media/avere-vfxt-deploy-4.png)

A implantação do cluster leva 15-20 minutos.

## <a name="gather-template-output"></a>Recolher saída de modelo

Quando o modelo Avere vFXT termina a criação do cluster, produz informações importantes sobre o novo cluster.

> [!TIP]
> Certifique-se de copiar o endereço IP de **gestão** a partir da saída do modelo. Precisa deste endereço para administrar o cluster.

Para encontrar a informação:

1. Vá ao grupo de recursos para o seu controlador de cluster.

1. No lado esquerdo, clique em **Implementações**, e depois **microsoft-avere.vfxt-template**.

   ![Página do portal do grupo de recursos com implementações selecionadas no modelo esquerdo e microsoft-avere.vfxt mostrando em uma tabela sob o nome de Implementação](media/avere-vfxt-outputs-deployments.png)

1. No lado esquerdo, clique em **Saídas**. Copiar os valores em cada um dos campos.

   ![página de saídas que mostra valores SSHSTRING, RESOURCE_GROUP, LOCALIZAÇÃO, NETWORK_RESOURCE_GROUP, REDE, SUBNET, SUBNET_ID, VSERVER_IPs e MGMT_IP nos campos à direita dos rótulos](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Passos Seguintes

Agora que o cluster está em execução e conhece o seu endereço IP de gestão, [ligue-se à ferramenta](avere-vfxt-cluster-gui.md)de configuração do cluster .

Utilize a interface de configuração para personalizar o seu cluster, incluindo estas tarefas de configuração:

* [Ativar suporte](avere-vfxt-enable-support.md)
* [Adicionar armazenamento](avere-vfxt-add-storage.md) (se necessário)
