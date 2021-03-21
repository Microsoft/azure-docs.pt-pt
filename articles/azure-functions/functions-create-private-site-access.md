---
title: Permitir o acesso ao site privado às Funções Azure
description: Aprenda a configurar o acesso ao site privado da rede virtual Azure para funções Azure.
author: craigshoemaker
ms.author: cshoe
ms.service: azure-functions
ms.topic: tutorial
ms.date: 06/17/2020
ms.openlocfilehash: 766ad12daeb6d2763f7ed5fe026cd4a0021eaf33
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937047"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Tutorial: Estabelecer acesso ao site privado Azure Functions

Este tutorial mostra-lhe como ativar o acesso ao [site privado](./functions-networking-options.md#private-endpoint-connections) com funções Azure. Ao utilizar o acesso ao site privado, pode exigir que o seu código de função seja apenas acionado a partir de uma rede virtual específica.

O acesso ao site privado é útil em cenários em que o acesso à aplicação de função precisa de ser limitado a uma rede virtual específica. Por exemplo, a aplicação de função pode ser aplicável apenas aos colaboradores de uma organização específica, ou serviços que estejam dentro da rede virtual especificada (como outra Função Azure, Máquina Virtual Azure ou um cluster AKS).

Se uma aplicação Functions precisar de aceder aos recursos do Azure dentro da rede virtual ou ligada através [de pontos finais de serviço,](../virtual-network/virtual-network-service-endpoints-overview.md)então é necessária [integração de rede virtual.](./functions-create-vnet.md)

Neste tutorial, você aprende a configurar o acesso ao site privado para a sua aplicação de função:

> [!div class="checklist"]
> * Criar uma máquina virtual
> * Criar um serviço Azure Bastion
> * Criar uma aplicação de Funções do Azure
> * Configurar um ponto final de serviço de rede virtual
> * Criar e implementar uma Função Azure
> * Invocar a função de fora e dentro da rede virtual

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="topology"></a>Topologia

O seguinte diagrama mostra a arquitetura da solução a criar:

![Diagrama de arquitetura de alto nível para solução de acesso a site privado](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que compreenda a endereçamento e sub-rede ip. Pode começar por [este artigo que cobre o básico de endereçar e sub-rede](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos mais artigos e vídeos estão disponíveis online.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

O primeiro passo neste tutorial é criar uma nova máquina virtual dentro de uma rede virtual.  A máquina virtual será usada para aceder à sua função uma vez que tenha restringido o seu acesso a apenas estar disponível dentro da rede virtual.

1. Selecione o botão Criar um botão **de recurso.**

1. No campo de pesquisa, escreva **o Windows Server** e selecione o Windows **Server** nos resultados da pesquisa.

1. Selecione **o Centro de Dados do Windows Server 2019** a partir da lista de opções do Windows Server e prima o botão **Criar.**

1. No _separador Básicos,_ utilize as definições de VM conforme especificado na tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![Separador básico para um novo Windows VM](./media/functions-create-private-site-access/create-vm-3.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Subscrição_ | A sua subscrição | A subscrição sob a qual os seus recursos são criados. |
    | [_Grupo de recursos_](../azure-resource-manager/management/overview.md) | myResourceGroup | Escolha o grupo de recursos para conter todos os recursos para este tutorial.  A utilização do mesmo grupo de recursos facilita a limpeza dos recursos quando termina este tutorial. |
    | _Nome da máquina virtual_ | myVM | O nome VM tem de ser único no grupo de recursos |
    | [_Region_](https://azure.microsoft.com/regions/) | (EUA) Centro-Norte dos EUA | Escolha uma região perto de si ou perto das funções a aceder. |
    | _Portas de entrada públicas_ | Nenhum | **Selecione Nenhum** para garantir que não existe conectividade de entrada para o VM a partir da internet. O acesso remoto ao VM será configurado através do serviço Azure Bastion. |

1. Escolha o _separador 'Rede'_ e selecione **Criar novo** para configurar uma nova rede virtual.

    >[!div class="mx-imgBorder"]
    >![Screenshot que mostra o separador "Networking" com a ação "Criar novo" realçado na secção "Rede Virtual".](./media/functions-create-private-site-access/create-vm-networking.png)

1. Na _configuração da rede virtual,_ utilize as definições na tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![Criar uma nova rede virtual para o novo VM](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Nome_ | myResourceGroup-vnet | Pode utilizar o nome padrão gerado para a sua rede virtual. |
    | _Intervalo de endereços_ | 10.10.0.0/16 | Utilize um único intervalo de endereços para a rede virtual. |
    | _Nome da sub-rede_ | Tutorial | Nome da sub-rede. |
    | _Intervalo de endereços_ (sub-rede) | 10.10.1.0/24 | O tamanho da sub-rede define quantas interfaces podem ser adicionadas à sub-rede. Esta sub-rede é utilizada pelo VM. Uma sub-rede /24 fornece 254 endereços de anfitrião. |

1. Selecione **OK** para criar a rede virtual.
1. De volta ao _separador Networking,_ certifique-se de que **nenhum** está selecionado para _IP público_.
1. Escolha o separador _Gestão_ e, em seguida, na _conta de armazenamento de Diagnóstico,_ escolha Criar **novo** para criar uma nova conta de Armazenamento.
1. Deixe os valores predefinidos para as secções _Identidade,_ _paragem automática_ e _backup._
1. Selecione _Rever + criar_. Após a validação concluída, **selecione Criar**. O processo de criação de VM demora alguns minutos.

## <a name="configure-azure-bastion"></a>Conigure Bastião Azure

[O Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) é um serviço Azure totalmente gerido que fornece acesso seguro a máquinas virtuais diretamente a partir do portal Azure. A utilização do serviço Azure Bastion elimina a necessidade de configurar as definições de rede relacionadas com o acesso rdp.

1. No portal, escolha **Adicionar** no topo da vista do grupo de recursos.
1. No campo de pesquisa, **escreva Bastion.**
1. **Selecione Bastion** nos resultados da pesquisa.
1. Selecione **Criar** para iniciar o processo de criação de um novo recurso Azure Bastion. Irá notar uma mensagem de erro na secção _de rede Virtual,_ uma vez que ainda não existe uma sub-rede AzureBastionSubnet. A sub-rede é criada nos seguintes passos. Utilize as definições na tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![Início da criação do Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Nome_ | myBastion | O nome do novo recurso Bastion |
    | _Região_ | E.U.A. Centro-Norte | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |
    | _Rede virtual_ | myResourceGroup-vnet | A rede virtual em que o recurso Bastião será criado em |
    | _Sub-rede_ | AzureBastionSubnet | A sub-rede da sua rede virtual para a qual será implantado o novo recurso do hospedeiro Bastion. Tem de criar uma sub-rede utilizando o valor do nome **AzureBastionSubnet**. Este valor permite ao Azure saber a que sub-rede deve implantar os recursos do Bastião. Deve utilizar uma sub-rede de pelo menos **/27** ou maior (/27, /26, e assim por diante). |

    > [!NOTE]
    > Para um guia detalhado e passo a passo para a criação de um recurso Azure Bastion, consulte o tutorial do [anfitrião Create azure Bastion.](../bastion/tutorial-create-host-portal.md)

1. Crie uma sub-rede na qual o Azure pode abastecer o anfitrião do Bastião Azure. Escolher **A configuração da sub-rede** de gestão abre um novo painel onde pode definir uma nova sub-rede.  Escolha **+ Sub-rede** para criar uma nova sub-rede.
1. A sub-rede deve ser do nome **AzureBastionSubnet** e o prefixo da sub-rede deve ser pelo menos **/27**.  Selecione **OK** para criar a sub-rede.

    >[!div class="mx-imgBorder"]
    >![Criar sub-rede para anfitrião do Azure Bastion](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

1. Na página _Criar um Bastião,_ selecione a recém-criada **AzureBastionSubnet** da lista de sub-redes disponíveis.

    >[!div class="mx-imgBorder"]
    >![Criar um anfitrião Azure Bastion com sub-rede específica](./media/functions-create-private-site-access/create-bastion-basics-2.png)

1. Selecione **& Criar**. Uma vez concluída a validação, **selecione Criar**. Levará alguns minutos para que o recurso Azure Bastion seja criado.

## <a name="create-an-azure-functions-app"></a>Criar uma aplicação de Funções do Azure

O próximo passo é criar uma aplicação de função em Azure utilizando o [plano de Consumo.](consumption-plan.md) Coloque o seu código de função neste recurso mais tarde no tutorial.

1. No portal, escolha **Adicionar** no topo da vista do grupo de recursos.
1. Selecione App de **Função de > Compute**
1. Na secção _Básico,_ utilize as definições de aplicações de função conforme especificado na tabela abaixo.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Grupo de Recursos_ | myResourceGroup | Escolha o grupo de recursos para conter todos os recursos para este tutorial.  A utilização do mesmo grupo de recursos para a aplicação de função e vM facilita a limpeza de recursos quando termina este tutorial. |
    | _Nome da Aplicação de Funções_ | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres válidos são a-z (caso insensível), 0-9, e -. |
    | _Publicar_ | Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | _Pilha de runtime_ | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. |
    | _Região_ | E.U.A. Centro-Norte | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |

    Selecione o **seguinte: Hospedar >** botão.
1. Para a secção _de Hospedagem,_ selecione a _conta de armazenamento_ adequada, o sistema _operativo_ e o _Plano_ conforme descrito na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | _Conta de armazenamento_ | Nome globalmente exclusivo | Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas de armazenamento têm de ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da [conta de armazenamento.](storage-considerations.md#storage-account-requirements) |
    | _Sistema operativo_ | Sistema operativo preferido | Um sistema operativo é pré-selecionado para si com base na seleção da sua pilha de tempo de execução, mas pode alterar a definição se necessário. |
    | _Planear_ | Consumo | O [plano de hospedagem](./functions-scale.md) dita como a aplicação de função é dimensionada e os recursos disponíveis para cada instância. |
1. Selecione **Review + Criar** para rever as seleções de configuração da aplicação.
1. Selecione **Criar** para aprovisionar e implementar a aplicação de funções.

## <a name="configure-access-restrictions"></a>Configurar restrições de acesso

O próximo passo é configurar restrições de acesso para garantir que [apenas](../app-service/app-service-ip-restrictions.md) os recursos na rede virtual podem invocar a função.

[O](functions-networking-options.md#private-endpoint-connections) acesso ao site privado é ativado através da criação de um [ponto de terminação](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço da Rede Virtual Azure entre a aplicação de função e a rede virtual especificada. As restrições de acesso são implementadas através de pontos finais de serviço. Os pontos finais de serviço garantem que apenas o tráfego originário da rede virtual especificada pode aceder ao recurso designado. Neste caso, o recurso designado é a Função Azure.

1. Dentro da aplicação de função, selecione o link **de rede** sob o cabeçalho da secção _Definições._
1. A página _de Networking_ é o ponto de partida para configurar a Porta frontal Azure, o Azure CDN, e também restrições de acesso.
1. Selecione **Configurar restrições** de acesso para configurar o acesso ao site privado.
1. Na página _'Restrições de Acesso',_ vê apenas a restrição por defeito no lugar. O padrão não coloca quaisquer restrições no acesso à aplicação de função.  **Selecione Adicionar regra** para criar uma configuração de restrição de acesso ao site privado.
1. No painel _de restrição_ de acesso de adicionar, forneça um _nome,_ _prioridade_ e _descrição_ para a nova regra.
1. Selecione **a Rede Virtual** a partir da caixa drop-down _tipo,_ em seguida, selecione a rede virtual previamente criada e, em seguida, selecione a sub-rede **Tutorial.** 
    > [!NOTE]
    > Pode levar vários minutos para ativar o ponto de terminação do serviço.
1. A página _de Restrições de Acesso_ mostra agora que existe uma nova restrição. Pode levar alguns segundos para que o estado de _Ponto final_ seja alterado de Desativado através do Provisioning to Enabled.

    >[!IMPORTANT]
    > Cada aplicação de função tem um [site Advanced Tool (Kudu)](../app-service/app-service-ip-restrictions.md#restrict-access-to-an-scm-site) que é usado para gerir implementações de aplicações de função. Este site é acedido a partir de um URL como: `<FUNCTION_APP_NAME>.scm.azurewebsites.net` . Permitir restrições de acesso no site kudu impede a implementação do código do projeto a partir de uma estação de trabalho de desenvolvedor local, e então um agente é necessário dentro da rede virtual para realizar a implementação.

## <a name="access-the-functions-app"></a>Aceda à aplicação funções

1. Voltar à aplicação de função previamente criada.  Na secção _Visão Geral,_ copie o URL.

    >[!div class="mx-imgBorder"]
    >![Obtenha o URL da aplicação Function](./media/functions-create-private-site-access/access-function-overview.png)

    Se tentar aceder à aplicação de função agora a partir do seu computador fora da sua rede virtual, receberá uma página HTTP 403 indicando que o acesso é proibido.
1. Volte ao grupo de recursos e selecione a máquina virtual previamente criada. Para aceder ao site a partir do VM, é necessário ligar-se ao VM através do serviço Azure Bastion.
1. Selecione **Connect** e, em seguida, escolha **Bastion**.
1. Forneça o nome de utilizador e a palavra-passe necessários para iniciar sessão na máquina virtual.
1. Selecione **Ligar**. Uma nova janela do navegador irá aparecer para que possa interagir com a máquina virtual.
É possível aceder ao site a partir do navegador web no VM porque o VM está a aceder ao site através da rede virtual.  Enquanto o site só está acessível a partir da rede virtual designada, permanece uma entrada pública de DNS.

## <a name="create-a-function"></a>Criar uma função

O próximo passo neste tutorial é criar uma Função Azure acionada por HTTP. Invocar a função através de um HTTP GET ou POST deve resultar numa resposta de "Olá, {nome}".  

1. Siga um dos seguintes quickstarts para criar e implementar a sua aplicação Azure Functions.

    * [Visual Studio Code](./create-first-function-vs-code-csharp.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Linha de comandos](./create-first-function-cli-csharp.md)
    * [Maven (Java)](./create-first-function-cli-java.md?tabs=bash,browser)

1. Ao publicar o seu projeto Azure Functions, escolha o recurso de aplicação de função que criou anteriormente neste tutorial.
1. Verifique se a função está implantada.

    >[!div class="mx-imgBorder"]
    >![Função implantada na lista de funções](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Invocar a função diretamente

1. Para testar o acesso à função, é necessário copiar o URL de função. Selecione a função implantada e, em seguida, **selecione Obter Url de função**. Em seguida, clique no botão **Copiar** para copiar o URL para a sua área de transferência.

    >[!div class="mx-imgBorder"]
    >![Copiar o URL de função](./media/functions-create-private-site-access/get-function-url.png)

1. Cole o URL num navegador web. Quando tenta agora aceder à aplicação de função a partir de um computador fora da sua rede virtual, recebe uma resposta HTTP 403 indicando que o acesso à aplicação é proibido.

## <a name="invoke-the-function-from-the-virtual-network"></a>Invocar a função da rede virtual

Aceder à função através de um navegador web (utilizando o serviço Azure Bastion) no VM configurado na rede virtual resulta em sucesso!

>[!div class="mx-imgBorder"]
>![Aceda à Função Azure via Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes


> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de networking em Funções](./functions-networking-options.md)