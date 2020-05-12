---
title: Integrar funções azure com uma rede virtual Azure
description: Um tutorial passo a passo que lhe mostra como ligar uma função a uma rede virtual Azure
author: alexkarcher-msft
ms.topic: article
ms.date: 4/23/2020
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: e1babfa188a29e79cb52cd14af19d552123345f1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122742"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Tutorial: integrar Funções com uma rede virtual do Azure

Este tutorial mostra-lhe como usar funções Azure para se conectar aos recursos numa rede virtual Azure. irá criar uma função que tenha acesso tanto à internet como a um VM que executa o WordPress em rede virtual.

> [!div class="checklist"]
> * Criar uma aplicação de função no plano Premium
> * Implemente um site WordPress para VM numa rede virtual
> * Ligue a aplicação de funções à rede virtual
> * Criar uma função proxy para aceder aos recursos wordPress
> * Solicite um ficheiro WordPress a partir de dentro da rede virtual

## <a name="topology"></a>Topologia

O diagrama seguinte mostra a arquitetura da solução que cria:

 ![UI para integração de rede virtual](./media/functions-create-vnet/topology.png)

As funções em execução no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Serviço de Aplicações Azure, que inclui a funcionalidade de Integração VNet. Para saber mais sobre a Integração VNet, incluindo resolução de problemas e configuração avançada, consulte [Integrar a sua aplicação com uma rede virtual Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que compreenda o endereço IP e a sub-rede. Pode começar com [este artigo que cobre os fundamentos da endereçação e sub-rede](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos mais artigos e vídeos estão disponíveis online.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar uma aplicação de função num plano Premium

Em primeiro lugar, cria-se uma aplicação de função no [plano Premium.] Este plano fornece uma escala sem servidores, ao mesmo tempo que suporta a integração virtual da rede.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Pode fixar a aplicação de função no painel de instrumentos selecionando o ícone do pino no canto superior direito. A fixação facilita o regresso a esta aplicação de funções depois de criar o seu VM.

## <a name="create-a-vm-inside-a-virtual-network"></a>Criar um VM dentro de uma rede virtual

Em seguida, crie um VM reconfigurado que execute o WordPress dentro de uma rede virtual[(WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). Um WordPress VM é usado devido ao seu baixo custo e conveniência. Este mesmo cenário funciona com qualquer recurso numa rede virtual, como APIs REST, App Service Environments e outros serviços Azure. 

1. No portal, escolha **+ Crie um recurso** no painel de navegação à esquerda, no tipo de campo de `WordPress LEMP7 Max Performance` pesquisa, e prima Enter.

1. Escolha o **Wordpress LEMP Max Performance** nos resultados da pesquisa. Selecione um plano de software do **Wordpress LEMP Max Performance para o CentOS** como plano de **software** e selecione **Criar**.

1. No separador **Basics,** utilize as definições vm conforme especificado na tabela abaixo da imagem:

    ![Separador básico para criar um VM](./media/functions-create-vnet/create-vm-1.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha, `myResourceGroup` ou o grupo de recursos que criou com a sua app de funções. A utilização do mesmo grupo de recursos para a aplicação de funções, o WordPress VM, e o plano de hospedagem facilitaa a limpeza de recursos quando terminar com este tutorial. |
    | **Nome da máquina virtual** | VNET-Wordpress | O nome VM precisa ser único no grupo de recursos |
    | **[Região](https://azure.microsoft.com/regions/)** | (Europa) Europa Ocidental | Escolha uma região perto de si ou perto das funções que acedem ao VM. |
    | **Tamanho** | B1 | Escolha o **tamanho De alterar** e, em seguida, selecione a imagem padrão B1s, que tem 1 vCPU e 1 GB de memória. |
    | **Tipo de autenticação** | Palavra-passe | Para utilizar a autenticação de senha, deve também especificar um nome de **utilizador,** uma **palavra-passe**segura, e, em seguida, **confirmar a palavra-passe**. Para este tutorial, não precisará sintetizar o VM a menos que precise de problemas. |

1. Escolha o separador **Networking** e em redes virtuais Configure selecione **Criar novo**.

1. Na **Rede Criar virtual,** utilize as definições na tabela abaixo da imagem:

    ![Separador de rede de criar VM](./media/functions-create-vnet/create-vm-2.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | Pode utilizar o nome predefinido gerado para a sua rede virtual. |
    | **Intervalo de endereços** | 10.10.0.0/16 | Utilize uma única gama de endereços para a rede virtual. |
    | **Nome da sub-rede** | Tutorial-Net | Nome da sub-rede. |
    | **Intervalo de endereços** (sub-rede) | 10.10.1.0/24   | O tamanho da sub-rede define quantas interfaces podem ser adicionadas à sub-rede. Esta sub-rede é utilizada pelo site WordPress.  Uma `/24` sub-rede fornece 254 endereços de anfitrião. |

1. Selecione **OK** para criar a rede virtual.

1. De volta ao separador **Networking,** escolha **Nenhum** para **IP público**.

1. Escolha o separador **Gestão** e, em seguida, na conta de **armazenamento de Diagnósticos,** escolha a conta de Armazenamento que criou com a sua app de funções.

1. Selecione **Rever + criar**. Depois de concluída a validação, selecione **Criar**. O processo de criação de VM demora alguns minutos. O VM criado só pode aceder à rede virtual.

1. Depois de criado o VM, escolha **recorrer para** ver a página para o seu novo VM e, em seguida, escolha **Networking** em **Definições**.

1. Verifique se não há **IP público.** Tome nota do **IP privado**, que utiliza para ligar ao VM a partir da sua aplicação de função.

    ![Definições de rede no VM](./media/functions-create-vnet/vm-networking.png)

Tem agora um site WordPress totalmente implantado dentro da sua rede virtual. Este site não é acessível a partir da internet pública.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Ligue a sua aplicação de função à rede virtual

Com um site WordPress a funcionar num VM numa rede virtual, pode agora ligar a sua aplicação de função a essa rede virtual.

1. Na sua nova aplicação de funções, selecione **Networking** no menu esquerdo.

1. Em **integração VNet,** selecione **Clique aqui para configurar**.

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="Escolha a rede na aplicação de funções":::

1. Na página de **Integração VNET,** **selecione Adicionar VNet**.

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="Adicione a pré-visualização de Integração VNet":::

1. No Estado da Funcionalidade de **Rede,** utilize as definições na tabela abaixo da imagem:

    ![Defina a rede virtual da aplicação de função](./media/functions-create-vnet/networking-3.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Rede Virtual** | MyResourceGroup-vnet | Esta rede virtual é a que criou anteriormente. |
    | **Sub-rede** | Criar nova subnet | Crie uma sub-rede na rede virtual para a sua aplicação de função utilizar. A Integração VNet deve ser configurada para utilizar uma sub-rede vazia. Não importa que as suas funções utilizem uma sub-rede diferente da sua VM. A rede virtual encaminha automaticamente o tráfego entre as duas subredes. |
    | **Nome da sub-rede** | Função-Net | Nome da nova sub-rede. |
    | **Bloco de endereços de rede virtual** | 10.10.0.0/16 | Escolha o mesmo bloco de endereços utilizado pelo site WordPress. Só deve ter um bloco de endereços definido. |
    | **Intervalo de endereços** | 10.10.2.0/24   | O tamanho da sub-rede restringe o número total de casos que a sua aplicação de função de plano Premium pode escalar para fora. Este exemplo utiliza uma `/24` sub-rede com 254 endereços de anfitrião disponíveis. Esta sub-rede está sobre-provisionada, mas fácil de calcular. |

1. Selecione **OK** para adicionar a sub-rede. Feche as páginas de **Estado de** Integração e Funcionalidade de Rede **VNet** para voltar à página da sua aplicação de função.

A aplicação de funções pode agora aceder à rede virtual onde o site WordPress está em execução. Em seguida, utiliza [os Proxies de Funções Azure](functions-proxies.md) para devolver um ficheiro do site WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Criar um proxy para aceder aos recursos VM

Com a Integração VNet ativada, pode criar um proxy na sua aplicação de função para encaminhar pedidos para o VM que funciona na rede virtual.

1. Na sua aplicação de funções, selecione **Proxies** a partir do menu esquerdo e, em seguida, selecione **Adicionar**. Utilize as definições de procuração na tabela abaixo da imagem:

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="Definir as definições de procuração":::

    | Definição  | Valor sugerido  | Descrição      |
    | -------- | ---------------- | ---------------- |
    | **Nome** | Fábrica | O nome pode ser qualquer valor. É usado para identificar o representante. |
    | **Modelo de rota** | /planta | Encaminhe os mapas para um recurso VM. |
    | **URL de back-end** | http://<YOUR_VM_IP>/wp-content/temas/twentyseventeen/assets/images/header.jpg | Substitua `<YOUR_VM_IP>` pelo endereço IP do seu WordPress VM que criou anteriormente. Este mapeamento devolve um único ficheiro do site. |

1. Selecione **Criar** para adicionar o proxy à sua aplicação de função.

## <a name="try-it-out"></a>Experimente

1. No seu navegador, tente aceder ao URL utilizado como **URL Backend**. Como esperado, o pedido é de si. Ocorre um intervalo de tempo porque o site WordPress está ligado apenas à sua rede virtual e não à internet.

1. Copie **o** valor url proxy do seu novo representante e cole-o na barra de endereços do seu navegador. A imagem devolvida é do site WordPress que funciona dentro da sua rede virtual.

    ![Ficheiro de imagem vegetal devolvido do site WordPress](./media/functions-create-vnet/plant.png)

A sua aplicação de funções está ligada tanto à internet como à sua rede virtual. O representante está a receber um pedido através da internet pública, e depois atua como um simples representante http para reencaminhar esse pedido para a rede virtual conectada. O representante transmite-lhe a resposta publicamente através da internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, o site WordPress serve como uma API que é chamada através de um proxy na aplicação de função. Este cenário faz um bom tutorial porque é fácil de configurar e visualizar. Pode utilizar qualquer outra API implantada dentro de uma rede virtual. Também poderia ter criado uma função com código que chama APIs implantados dentro da rede virtual. Um cenário mais realista é uma função que utiliza APIs do cliente de dados para chamar uma instância do Servidor SQL implementada na rede virtual.

As funções que funcionam num plano Premium partilham a mesma infraestrutura subjacente do App Service que as aplicações web nos planos PremiumV2. Toda a documentação para [aplicações web no Azure App Service](../app-service/overview.md) aplica-se às funções do seu plano Premium.

> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de networking em Funções](./functions-networking-options.md)

[Plano Premium]: functions-scale.md#premium-plan
