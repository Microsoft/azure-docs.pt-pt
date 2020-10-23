---
title: Implementar Avere vFXT para Azure
description: Saiba como utilizar o assistente de implementação disponível no Azure Marketplace para implementar um cluster com Avere vFXT para Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 110fc658ffc3b72de91bbf1c9562f3efeecaf55b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342421"
---
# <a name="deploy-the-vfxt-cluster"></a>Implementar o cluster vFXT

Este procedimento acompanha-o através do assistente de implantação disponível no Azure Marketplace. O assistente implanta automaticamente o cluster utilizando um modelo de Gestor de Recursos Azure. Depois de introduzir os parâmetros no formulário e clicar em **Criar,** o Azure completa automaticamente estas tarefas:

* Cria o controlador de cluster, que é um VM básico que contém o software necessário para implementar e gerir o cluster.
* Cria um grupo de recursos e infraestruturas de rede virtuais, incluindo a criação de novos elementos.
* Cria os VMs de nó de cluster e configura-os como o cluster Avere.
* Se solicitado, cria um novo recipiente Azure Blob e configura-o como um ficheiro de núcleo de cluster.

Depois de seguir as instruções deste documento, terá uma rede virtual, uma sub-rede, um controlador de cluster e um cluster vFXT, como mostrado no diagrama seguinte. Este diagrama mostra o ficheiro central Azure Blob opcional, que inclui um novo recipiente de armazenamento Blob (numa nova conta de armazenamento, não mostrado) e um ponto final de serviço para armazenamento da Microsoft dentro da sub-rede.

![diagrama mostrando três retângulos concêntricos com componentes de cluster Avere. O retângulo exterior é rotulado como "Grupo de Recursos" e contém um hexágono com a etiqueta "Blob storage (opcional)". O próximo retângulo está rotulado como 'Rede Virtual: 10.0.0.0/16' e não contém componentes únicos. O retângulo mais interno é rotulado como 'Subnet:10.0.0.0/24' e contém um "controlador de cluster" com a etiqueta VM, uma pilha de três VMs com a etiqueta 'vFXT nosdes (cluster vFXT)', e um hexágono com a etiqueta 'Service endpoint'. Existe uma seta que liga o ponto final de serviço (que está dentro da sub-rede) e o armazenamento do blob (que está fora da sub-rede e vnet, no grupo de recursos). A seta passa através dos limites da rede e da rede virtual.](media/avere-vfxt-deployment.png)

Antes de utilizar o modelo de criação, certifique-se de que já abordou estes pré-requisitos:  

* [Nova subscrição](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Permissões do proprietário da subscrição](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Quota para o cluster vFXT](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Ponto final do serviço de armazenamento (se necessário)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) - Necessário para implementações que utilizem uma rede virtual existente e criem armazenamento de bolhas

Para obter mais informações sobre as etapas de implantação e planeamento do cluster, leia [Plano do seu sistema Avere vFXT](avere-vfxt-deploy-plan.md) e [visão geral da implementação.](avere-vfxt-deploy-overview.md)

## <a name="create-the-avere-vfxt-for-azure"></a>Criar o Avere vFXT para Azure

Aceda ao modelo de criação no portal Azure procurando por Avere e selecionando "Avere vFXT for Azure ARM Template".

![Janela do navegador mostrando o portal Azure com migalhas de pão "New > Marketplace > Everything". Na página De Tudo, o campo de pesquisa tem o termo "avere" e o segundo resultado, "Avere vFXT for Azure ARM Template" é delineado a vermelho para realçá-lo.](media/avere-vfxt-template-choose.png)

Depois de ler os detalhes na página Avere vFXT para Azure ARM Template, clique no botão **Criar** para começar.

![Mercado azul com a primeira página do modelo de implementação mostrando](media/avere-vfxt-deploy-first.png)

O modelo é dividido em quatro passos - duas páginas de recolha de informação, além de passos de validação e confirmação.

* A página um reúne as definições para o controlador de cluster VM.
* A página dois recolhe parâmetros para a criação do cluster e recursos adicionais como sub-redes e armazenamento.
* A página três resume as suas escolhas e valida a configuração.
* A página quatro explica os termos e condições do software e permite-lhe iniciar o processo de criação de clusters.

## <a name="page-one-parameters---cluster-controller-information"></a>Parâmetros da página um - informações do controlador de cluster

A primeira página do modelo de implantação centra-se no controlador de cluster.

![Primeira página do modelo de implementação](media/avere-vfxt-deploy-1.png)

Preencha as seguintes informações:

* **Nome do controlador de cluster** - Desajuste o nome para o controlador de cluster VM.

* **Nome de utilizador do controlador** - Desajuste o nome de utilizador raiz para o controlador de cluster VM.

* **Tipo de autenticação** - Escolha a palavra-passe ou a autenticação da chave pública SSH para ligar ao controlador. Recomenda-se o método-chave público SSH; ler [Como criar e utilizar as teclas SSH](../virtual-machines/linux/ssh-from-windows.md) se precisar de ajuda.

* **Senha** ou **chave pública SSH** - Dependendo do tipo de autenticação selecionado, deve fornecer uma chave pública RSA ou uma palavra-passe nos campos seguintes. Esta credencial é utilizada com o nome de utilizador fornecido anteriormente.

* **Subscrição** - Selecione a subscrição para o Avere vFXT.

* **Grupo de recursos** - Selecione um grupo de recursos vazios existente para o cluster Avere vFXT, ou clique em "Criar novo" e introduza um novo nome de grupo de recursos.

* **Localização** - Selecione a localização Azure para o seu cluster e recursos.

Clique **em OK** quando terminar.

> [!NOTE]
> Se pretender que o controlador de cluster tenha um endereço IP virado para o público, crie uma nova rede virtual para o cluster em vez de selecionar uma rede existente. Esta definição está na página dois.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Página dois parâmetros - informação de cluster vFXT

A segunda página do modelo de implantação permite-lhe definir o tamanho do cluster, o tipo de nó, o tamanho da cache e os parâmetros de armazenamento, entre outras definições.

![Segunda página do modelo de implantação](media/avere-vfxt-deploy-2.png)

* **Contagem do nó de cluster Avere vFXT** - Escolha o número de nós no cluster. O mínimo é de três nós e o máximo é de doze.

* **Password de administração de cluster** - Crie a palavra-passe para a administração do cluster. Esta palavra-passe é utilizada com o nome de utilizador ```admin``` para iniciar seduca no painel de controlo do cluster, onde pode monitorizar as definições do cluster e configurar as definições do cluster.

* **Nome do cluster Avere vFXT** - Dê ao cluster um nome único.

* **Tamanho** - Esta secção mostra o tipo VM que será utilizado para os nós do cluster. Embora exista apenas uma opção recomendada, o link **change size** abre uma tabela com detalhes sobre este tipo de instância e um link para uma calculadora de preços.

* **Tamanho da cache por nó** - A cache do cluster é espalhada pelos nós do cluster, pelo que o tamanho total da cache no seu cluster Avere vFXT será este tamanho multiplicado pelo número de nós.

  Configuração recomendada: Utilize 4 TB por nó para Standard_E32s_v3 nós.

* **Rede virtual** - Defina uma nova rede virtual para alojar o cluster, ou selecione uma rede existente que cumpra os pré-requisitos descritos no [Plano do seu sistema Avere vFXT](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Se criar uma nova rede virtual, o controlador de cluster terá um endereço IP público para que possa aceder à nova rede privada. Se escolher uma rede virtual existente, o controlador de cluster é configurado sem endereço IP público.
  >
  > Um endereço IP publicamente visível no controlador de cluster proporciona um acesso mais fácil ao cluster vFXT, mas cria um pequeno risco de segurança.
  >* Um endereço IP público no controlador de cluster permite-lhe usá-lo como hospedeiro de salto para ligar ao cluster Avere vFXT de fora da sub-rede privada.
  >* Se não tiver um endereço IP público no controlador, precisa de outro hospedeiro de salto, uma ligação VPN ou ExpressRoute para aceder ao cluster. Por exemplo, utilize uma rede virtual existente que já tenha uma ligação VPN configurada.
  >* Se criar um controlador com um endereço IP público, deverá proteger o Controlador VM com um grupo de segurança de rede. Por predefinição, o Avere vFXT para a implementação do Azure cria um grupo de segurança de rede que restringe o acesso à entrada apenas para a porta 22 para controladores com endereços IP públicos. Pode ainda proteger o sistema bloqueando o acesso à sua gama de endereços de origem IP - ou seja, apenas permitir ligações de máquinas que pretende utilizar para o acesso ao cluster.

  Uma nova rede virtual também é configurada com um ponto final de serviço de armazenamento para armazenamento Azure Blob e com controlo de acesso à rede bloqueado para permitir apenas IPs a partir da sub-rede de cluster.

* **Sub-rede** - Escolha uma sub-rede ou crie uma nova.

* **Criar e utilizar o armazenamento de blob** - Escolha **o verdadeiro** para criar um novo recipiente Azure Blob e configurá-lo como armazenamento de back-end para o novo cluster Avere vFXT. Esta opção também cria uma nova conta de armazenamento no grupo de recursos do cluster, e cria um ponto final de serviço de armazenamento da Microsoft dentro da sub-rede do cluster.
  
  Se fornecer uma rede virtual existente, deve ter um ponto final de serviço de armazenamento antes de criar o cluster. (Para mais informações, leia [Plano do seu sistema Avere vFXT](avere-vfxt-deploy-plan.md).)

  Desateia este campo **em falso** se não pretender criar um novo recipiente. Neste caso, deve anexar e configurar o armazenamento depois de criar o cluster. Leia [o armazenamento de configuração](avere-vfxt-add-storage.md) para obter instruções.

* **(Nova) Conta de armazenamento** - Se criar um novo recipiente Azure Blob, insira um nome para a nova conta de armazenamento.

## <a name="validation-and-purchase"></a>Validação e compra

A página três resume a configuração e valida os parâmetros. Após a validação, verifique o resumo e clique no botão **OK.**

> [!TIP]
> Pode guardar as definições de criação deste cluster clicando no **modelo de Descarregamento e nos parâmetros ligados** ao lado do botão **OK.** Estas informações podem ser úteis se precisar de criar um cluster semelhante mais tarde - por exemplo, para criar um cluster de substituição num cenário de recuperação de desastres. (Ler [orientação de recuperação de desastres](disaster-recovery.md) para saber mais.)

![Terceira página do modelo de implantação - validação](media/avere-vfxt-deploy-3.png)

A página quatro dá os termos de uso e links para a privacidade e informações de preços.

Introduza qualquer informação de contacto em falta e, em seguida, clique no botão **Criar** para aceitar os termos e criar o avere vFXT para cluster Azure.

![Quarta página do modelo de implementação - termos e condições, criar botão](media/avere-vfxt-deploy-4.png)

A implantação do cluster demora 15 a 20 minutos.

## <a name="gather-template-output"></a>Recolher saída do modelo

Quando o modelo Avere vFXT termina a criação do cluster, produz informações importantes sobre o novo cluster.

> [!TIP]
> Certifique-se de copiar o endereço IP de **gestão** a partir da saída do modelo. Precisa deste endereço para administrar o aglomerado.

Para encontrar a informação:

1. Vá ao grupo de recursos para o seu controlador de cluster.

1. No lado esquerdo, clique em **Implementações**e, em seguida, **microsoft-avere.vfxt-template**.

   ![Página do portal do grupo de recursos com Implementações selecionadas à esquerda e microsoft-avere.vfxt-modelo mostrando em uma tabela sob nome de Implementação](media/avere-vfxt-outputs-deployments.png)

1. No lado esquerdo, clique em **Saídas**. Copiar os valores em cada um dos campos.

   ![página de saídas mostrando SSHSTRING, RESOURCE_GROUP, LOCALIZAÇÃO, NETWORK_RESOURCE_GROUP, REDE, SUBNET, SUBNET_ID, VSERVER_IPs e valores MGMT_IP nos campos à direita dos rótulos](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Passos seguintes

Agora que o cluster está em execução e conhece o seu endereço IP de gestão, [conecte-se à ferramenta de configuração do cluster](avere-vfxt-cluster-gui.md).

Utilize a interface de configuração para personalizar o seu cluster, incluindo estas tarefas de configuração:

* [Permitir o suporte](avere-vfxt-enable-support.md)
* [Adicionar armazenamento](avere-vfxt-add-storage.md) (se necessário)