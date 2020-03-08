---
title: Permitir o acesso ao site privado às Funções Azure
description: Aprenda a configurar o acesso privado do site da rede virtual Azure para funções azure.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851281"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Tutorial: Estabelecer funções azure acesso ao site privado

Este tutorial mostra-lhe como permitir [o acesso ao site privado](./functions-networking-options.md#private-site-access) com funções Azure. Ao utilizar o acesso ao site privado, pode exigir que o seu código de função seja apenas acionado a partir de uma rede virtual específica.

O acesso ao site privado é útil em cenários quando o acesso à aplicação de funções precisa de ser limitado a uma rede virtual específica. Por exemplo, a aplicação de funções só pode ser aplicável a funcionários de uma organização específica, ou serviços que estejam dentro da rede virtual especificada (como outra Função Azure, Máquina Virtual Azure ou um cluster AKS).

Se uma aplicação Functions precisa de aceder aos recursos do Azure dentro da rede virtual, ou conectada através de [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)de serviço, então é necessária [integração de rede virtual.](./functions-create-vnet.md)

Neste tutorial, aprende a configurar o acesso ao site privado para a sua aplicação de funções:

> [!div class="checklist"]
> * Criar uma máquina virtual
> * Criar um serviço Azure Bastion
> * Criar uma aplicação De Funções Azure
> * Configure um ponto final do serviço de rede virtual
> * Criar e implementar uma Função Azure
> * Invocar a função de fora e dentro da rede virtual

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="topology"></a>Topologia

O diagrama seguinte mostra a arquitetura da solução a criar:

![Diagrama de arquitetura de alto nível para solução de acesso ao site privado](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que compreenda o endereço IP e a sub-rede. Pode começar com [este artigo que cobre os fundamentos da endereçação e sub-rede](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos mais artigos e vídeos estão disponíveis online.

## <a name="sign-in-to-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

O primeiro passo neste tutorial é criar uma nova máquina virtual dentro de uma rede virtual.  A máquina virtual será usada para aceder à sua função uma vez restringido o seu acesso a apenas estar disponível dentro da rede virtual.

1. Selecione o botão **Criar um recurso.**

2. No campo de pesquisa, digite `Windows Server`e selecione **O Servidor do Windows** nos resultados da pesquisa.

3. Selecione **o Datacenter do Windows Server 2019** a partir da lista de opções do Windows Server e prima o botão **Criar.**

4. No separador **Basics,** utilize as definições vm conforme especificado na tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![separador Basics para um novo](./media/functions-create-private-site-access/create-vm-3.png) VM do Windows

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. |
    | [**Grupo de recursos**](../azure-resource-manager/management/overview.md) | myResourceGroup | Escolha o grupo de recursos para conter todos os recursos para este tutorial.  Usar o mesmo grupo de recursos facilita a limpeza de recursos quando terminar com este tutorial. |
    | **Nome da máquina virtual** | myVM | O nome VM precisa ser único no grupo de recursos |
    | [**Região**](https://azure.microsoft.com/regions/) | (EUA) Centro-Norte dos EUA | Escolha uma região perto de si ou perto das funções a aceder. |
    | **Portos de entrada pública** | Nenhum | Selecione **Nenhum** para garantir que não há conectividade de entrada com o VM a partir da internet. O acesso remoto ao VM será configurado através do serviço Azure Bastion. |

5. Escolha o separador **Networking** e selecione **Criar novo** para configurar uma nova rede virtual.

    >[!div class="mx-imgBorder"]
    >![Criar uma nova rede virtual para o novo](./media/functions-create-private-site-access/create-vm-networking.png) VM

6. Na **Rede Criar virtual,** utilize as definições na tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![Criar uma nova rede virtual para o novo](./media/functions-create-private-site-access/create-vm-vnet-1.png) VM

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | Pode utilizar o nome predefinido gerado para a sua rede virtual. |
    | **Intervalo de endereços** | 10.10.0.0/16 | Utilize uma única gama de endereços para a rede virtual. |
    | **Nome da sub-rede** | Tutorial | Nome da sub-rede. |
    | **Intervalo de endereços** (sub-rede) | 10.10.1.0/24 | O tamanho da sub-rede define quantas interfaces podem ser adicionadas à sub-rede. Esta sub-rede é utilizada pelo VM. Uma sub-rede `/24` fornece 254 endereços de anfitrião. |

7. Selecione **OK** para criar a rede virtual.
8. De volta ao separador **De Rede,** certifique-se de **que Nenhum** é selecionado para **IP público**.
9. Escolha o separador **Gestão,** depois na conta de **armazenamento de diagnóstico,** escolha **criar novo** para criar uma nova conta de Armazenamento.
10. Deixe os valores predefinidos para as secções **de Identidade,** **Paragem Automática**e Cópia **de Segurança.**
11. Selecione **Rever + criar**. Depois de concluída a validação, selecione **Criar**. O processo de criação de VM demora alguns minutos.

## <a name="configure-azure-bastion"></a>Configure Azure Bastion

[O Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) é um serviço Azure totalmente gerido que fornece acesso seguro de RDP e SSH a máquinas virtuais diretamente do portal Azure. A utilização do serviço Azure Bastion remove a necessidade de configurar as definições de rede relacionadas com o acesso rdp.

1. No portal, escolha **Adicionar** no topo da vista do grupo de recursos.
2. No campo de pesquisa, escreva "Bastião".  Selecione "Bastião".
3. Selecione **Criar** para iniciar o processo de criação de um novo recurso Azure Bastion. Notará uma mensagem de erro na secção **de rede Virtual,** uma vez que ainda não existe uma sub-rede `AzureBastionSubnet`. A sub-rede é criada nos seguintes passos. Utilize as definições na tabela abaixo da imagem:

    >[!div class="mx-imgBorder"]
    >![Início da criação de](./media/functions-create-private-site-access/create-bastion-basics-1.png) do Bastião Azure

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | meu Bastião | O nome do novo recurso bastião |
    | **Região** | E.U.A. Centro-Norte | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |
    | **Rede virtual** | myResourceGroup-vnet | A rede virtual em que o recurso Bastião será criado em |
    | **Sub-rede** | AzureBastionSubnet | A subrede na sua rede virtual para a qual será implantado o novo recurso de anfitrião bastião. Deve criar uma sub-rede utilizando o valor de nome `AzureBastionSubnet`. Este valor permite ao Azure saber para que sub-rede implantar os recursos da Bastião. Deve utilizar uma sub-rede de, pelo menos, `/27` ou maior (`/27`, `/26`, e assim por diante). |

    > [!NOTE]
    > Para um guia detalhado e passo a passo para a criação de um recurso Azure Bastion, consulte o tutorial de [anfitriões Create a Azure Bastion.](../bastion/bastion-create-host-portal.md)

4. Crie uma sub-rede na qual o Azure possa fornecer o anfitrião do Bastião Azure. Escolher **a configuração da sub-rede Manage** abre um novo painel onde pode definir uma nova sub-rede.  Escolha **+ Subnet** para criar uma nova sub-rede.
5. A sub-rede deve ser do nome `AzureBastionSubnet` e a prefixação da sub-rede deve ser pelo menos `/27`.  Selecione **OK** para criar a sub-rede.

    >[!div class="mx-imgBorder"]
    >![Criar subnet para o anfitrião do Bastião Azure](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. Na página **Criar um Bastião,** selecione o recém-criado `AzureBastionSubnet` da lista de subnets disponíveis.

    >[!div class="mx-imgBorder"]
    >![Criar um hospedeiro azure bastion com](./media/functions-create-private-site-access/create-bastion-basics-2.png) específica de sub-rede

7. Selecione **Review & Create**. Assim que a validação estiver concluída, selecione **Criar**. Levará alguns minutos para que o recurso Azure Bastion seja criado.

## <a name="create-an-azure-functions-app"></a>Criar uma aplicação De Funções Azure

O próximo passo é criar uma aplicação de função em Azure utilizando o [plano de consumo.](functions-scale.md#consumption-plan) Você implementa o seu código de função para este recurso mais tarde no tutorial.

1. No portal, escolha **Adicionar** no topo da vista do grupo de recursos.
2. **Selecione Compute > App de Funções**
3. Na secção **Basics,** utilize as definições da aplicação de função conforme especificado no quadro abaixo.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Grupo de Recursos** | myResourceGroup | Escolha o grupo de recursos para conter todos os recursos para este tutorial.  A utilização do mesmo grupo de recursos para a aplicação de funções e VM facilita a limpeza de recursos quando terminar com este tutorial. |
    | **Nome da app de função** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os caracteres válidos são a-z (caso insensível), 0-9, e -. |
    | **Publicar** | Código | Opção de publicar ficheiros de código ou um contentor Dono. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. |
    | **Região** | E.U.A. Centro-Norte | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem. |

    Selecione o **seguinte: Botão de hospedagem e gt.**
4. Para a secção **de Hospedagem,** selecione a conta de **armazenamento**adequada, **sistema operativo,** e **planeie** uma descrita na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Conta de armazenamento** | Nome globalmente exclusivo | Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da conta de [armazenamento.](./functions-scale.md#storage-account-requirements) |
    | **Sistema operativo** | Sistema operativo preferido | Um sistema operativo é pré-selecionado para si com base na sua seleção de pilhas de tempo de funcionamento, mas pode alterar a definição se necessário. |
    | **Planear** | Consumo | O [plano de hospedagem](./functions-scale.md) dita como a aplicação de funções é dimensionada e os recursos disponíveis para cada instância. |
5. Selecione **Review + Criar** para rever as seleções de configuração da aplicação.
6. Selecione **Criar** para aprovisionar e implementar a aplicação de função.

## <a name="configure-access-restrictions"></a>Configurar restrições de acesso

O próximo passo é configurar [restrições](../app-service/app-service-ip-restrictions.md) de acesso para garantir que apenas os recursos na rede virtual possam invocar a função.

[O](functions-networking-options.md#private-site-access) acesso ao site privado é ativado através da criação de um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) do serviço de rede virtual Azure entre a aplicação de funções e a rede virtual especificada. As restrições de acesso são implementadas através de pontos finais de serviço. Os pontos finais do serviço asseguram que apenas o tráfego originário da rede virtual especificada pode aceder ao recurso designado. Neste caso, o recurso designado é a Função Azure.

1. Dentro da aplicação de funções, dirija-se ao separador de **funcionalidades** da Plataforma. Selecione o link **de rede** sob o cabeçalho da secção de *rede* para abrir a secção Estado da Funcionalidade de Rede.
2. A página Estado da Funcionalidade de **Rede** é o ponto de partida para configurar a Porta Frontal Azure, o CDN Azure e também as restrições de acesso. **Selecione Configure Restrições de Acesso** para configurar o acesso ao site privado.
3. Na página **de Restrições** de Acesso, vê apenas a restrição predefinida no lugar. O predefinido não coloca quaisquer restrições no acesso à aplicação de funções.  Selecione **Adicionar regra** para criar uma configuração de restrição de acesso ao site privado.
4. No painel **de restrição** de acesso adicionar, selecione **rede Virtual** a partir da caixa de drop-down **tipo** e, em seguida, selecione a rede virtual e sub-rede previamente criadas.
5. A página **de Restrições** de Acesso mostra agora que existe uma nova restrição. Pode levar alguns segundos para que o **estado do Ponto Final** mude de `Disabled` para `Provisioning` para `Enabled`.

    >[!IMPORTANT]
    > Cada aplicação de função tem um site advanced [Tool (Kudu)](../app-service/app-service-ip-restrictions.md#scm-site) que é usado para gerir implementações de aplicações de função. Este site é acedido a partir de um URL como: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Como as restrições de acesso não foram ativadas neste site de implementação, ainda pode implementar o seu código de projeto a partir de uma estação de trabalho de desenvolvedores local ou construir serviço sem ter de fornecer um agente dentro da rede virtual.

## <a name="access-the-functions-app"></a>Aceder à aplicação de funções

1. Volte à aplicação de funções previamente criada.  Na secção **Visão Geral,** copie o URL.

    >[!div class="mx-imgBorder"]
    >![Obter o URL da aplicação função](./media/functions-create-private-site-access/access-function-overview.png)

2. Se tentar aceder à aplicação de funções agora a partir do seu computador fora da sua rede virtual, receberá uma página HTTP 403 indicando que a aplicação está parada.  A aplicação não foi interrompida. A resposta é, na verdade, um estatuto de PROIBIÇÃO DE HTTP 403 IP.
3. Agora terá acesso à sua função a partir da máquina virtual previamente criada, que está ligada à sua rede virtual. Para aceder ao site a partir do VM, terá de se ligar ao VM através do serviço Azure Bastion.  Primeiro selecione **Connect** e, em seguida, escolha **Bastion**.
4. Forneça o nome de utilizador e a palavra-passe necessários para iniciar sessão na máquina virtual.  Selecione **Ligar**. Uma nova janela do navegador surgirá para que possa interagir com a máquina virtual.
5. Como este VM está a aceder à função através da rede virtual, é possível aceder ao site a partir do navegador web no VM.  É importante notar que, embora a aplicação de funções seja acessível apenas a partir da rede virtual designada, permanece uma entrada pública de DNS. Como mostrado acima, a tentativa de acesso ao site resultará numa resposta HTTP 403.

## <a name="create-a-function"></a>Criar uma função

O próximo passo neste tutorial é criar uma Função Azure desencadeada por HTTP. Invocar a função através de um HTTP GET ou POST deve resultar numa resposta de "Olá, {name}".  

1. Siga uma das seguintes acelerações para criar e implementar a sua aplicação Funções Azure.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Linha de comandos](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. Ao publicar o seu projeto Funções Azure, escolha o recurso de aplicação de funções que criou anteriormente neste tutorial.
3. Verifique se a função está implantada.

    >[!div class="mx-imgBorder"]
    >![Função implantada na lista de funções](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Invocar a função diretamente

1. Para testar o acesso à função, é necessário copiar o URL da função. Selecione a função implantada e, em **seguida, selecione <> Obtenha URL de função**. Em seguida, clique no botão **Copiar** o URL para a sua área de redação.

    >[!div class="mx-imgBorder"]
    >![Copiar o URL da função](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > Quando a função funciona, verá um erro de tempo de funcionamento no portal, indicando que o tempo de funcionamento da função não é capaz de iniciar. Apesar do texto de mensagem, a aplicação de função está realmente em execução. O erro é o resultado das novas restrições de acesso, que impedem o portal de consultar para verificar o tempo de execução.

2. Colá-lo num navegador web. Quando tenta aceder à aplicação de funções a partir de um computador fora da sua rede virtual, recebe uma resposta HTTP 403 indicando que a aplicação está parada.

## <a name="invoke-the-function-from-the-virtual-network"></a>Invocar a função da rede virtual

Aceder à função através de um navegador web (utilizando o serviço Azure Bastion) no VM configurado na rede virtual resulta em sucesso!

>[!div class="mx-imgBorder"]
>![Aceder à Função Azure via](./media/functions-create-private-site-access/access-function-via-bastion-final.png) do Bastião Azure

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes


> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de networking em Funções](./functions-networking-options.md)
