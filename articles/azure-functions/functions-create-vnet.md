---
title: Integre as Funções Azure com uma rede virtual Azure
description: Um tutorial passo a passo que mostra como ligar uma função a uma rede virtual Azure
ms.topic: article
ms.date: 4/23/2020
ms.openlocfilehash: f50c923104fdfcf26f400f20f0de66a82eb3d245
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87387528"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Tutorial: integrar Funções com uma rede virtual do Azure

Este tutorial mostra-lhe como usar as Funções Azure para se conectar a recursos numa rede virtual Azure. irá criar uma função que tenha acesso tanto à internet como a um WordPress em execução VM na rede virtual.

> [!div class="checklist"]
> * Criar uma aplicação de função no plano Premium
> * Implementar um site WordPress para VM numa rede virtual
> * Ligue a aplicação de função à rede virtual
> * Criar um proxy de função para aceder aos recursos do WordPress
> * Solicite um ficheiro WordPress a partir de dentro da rede virtual

## <a name="topology"></a>Topologia

O seguinte diagrama mostra a arquitetura da solução que cria:

 ![UI para integração de rede virtual](./media/functions-create-vnet/topology.png)

As funções em execução no plano Premium têm as mesmas capacidades de hospedagem que as aplicações web no Azure App Service, que inclui a funcionalidade de Integração VNet. Para saber mais sobre a Integração VNet, incluindo resolução de problemas e configuração avançada, consulte [Integrar a sua aplicação com uma rede virtual Azure.](../app-service/web-sites-integrate-with-vnet.md)

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que compreenda a endereçamento e sub-rede ip. Pode começar por [este artigo que cobre o básico de endereçar e sub-rede](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos mais artigos e vídeos estão disponíveis online.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar uma aplicação de função num plano Premium

Em primeiro lugar, cria-se uma aplicação de função no [plano Premium.] Este plano fornece escala sem servidor enquanto suporta a integração de rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Pode fixar a aplicação de função no painel de instrumentos selecionando o ícone pino no canto superior direito. A fixação facilita o regresso a esta aplicação de função depois de criar o seu VM.

## <a name="create-a-vm-inside-a-virtual-network"></a>Criar um VM dentro de uma rede virtual

Em seguida, crie um VM pré-configurado que executa o WordPress dentro de uma rede virtual[(WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) by Jetware). Um WordPress VM é usado devido ao seu baixo custo e conveniência. Este mesmo cenário funciona com qualquer recurso numa rede virtual, como ASPis REST, Ambientes de Serviço de Aplicações e outros serviços Azure. 

1. No portal, escolha **+ Crie um recurso** no painel de navegação esquerdo, no tipo de campo de `WordPress LEMP7 Max Performance` pesquisa, e prima Enter.

1. Escolha **o Wordpress LEMP Max Performance** nos resultados da pesquisa. Selecione um plano de software do **Wordpress LEMP Max Performance para CentOS** como **Plano de Software** e selecione **Criar**.

1. No **separador Básicos,** utilize as definições de VM conforme especificado na tabela abaixo da imagem:

    ![Separador básico para criar um VM](./media/functions-create-vnet/create-vm-1.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual os seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Escolha `myResourceGroup` , ou o grupo de recursos que criou com a sua aplicação de função. A utilização do mesmo grupo de recursos para a aplicação de função, o WordPress VM e o plano de hospedagem facilitam a limpeza dos recursos quando termina este tutorial. |
    | **Nome da máquina virtual** | VNET-Wordpress | O nome VM tem de ser único no grupo de recursos |
    | **[Região](https://azure.microsoft.com/regions/)** | (Europa) Europa Ocidental | Escolha uma região perto de si ou perto das funções que acedam ao VM. |
    | **Tamanho** | B1s | Escolha **o tamanho de Alterar** e, em seguida, selecione a imagem padrão B1s, que tem 1 vCPU e 1 GB de memória. |
    | **Tipo de autenticação** | Palavra-passe | Para utilizar a autenticação de palavra-passe, também deve especificar um **nome de utilizador**, uma **palavra-passe**segura e, em seguida, **confirmar a palavra-passe**. Para este tutorial, não precisará de se inscrever no VM a menos que precise de resolver problemas. |

1. Escolha o **separador de Rede** e em configurar redes virtuais selecione **Criar novo**.

1. Na **configuração da rede virtual,** utilize as definições na tabela abaixo da imagem:

    ![Separador de rede de criar VM](./media/functions-create-vnet/create-vm-2.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Nome** | myResourceGroup-vnet | Pode utilizar o nome padrão gerado para a sua rede virtual. |
    | **Intervalo de endereços** | 10.10.0.0/16 | Utilize um único intervalo de endereços para a rede virtual. |
    | **Nome da sub-rede** | Tutorial-Net | Nome da sub-rede. |
    | **Intervalo de endereços** (sub-rede) | 10.10.1.0/24   | O tamanho da sub-rede define quantas interfaces podem ser adicionadas à sub-rede. Esta sub-rede é utilizada pelo site WordPress.  Uma `/24` sub-rede fornece 254 endereços de anfitrião. |

1. Selecione **OK** para criar a rede virtual.

1. De volta ao **separador Networking,** escolha **Nenhum** para **IP público.**

1. Escolha o separador **Gestão** e, em seguida, na **conta de armazenamento de Diagnósticos,** escolha a conta de Armazenamento que criou com a sua aplicação de função.

1. Selecione **Rever + criar**. Após a validação concluída, **selecione Criar**. O processo de criação de VM demora alguns minutos. O VM criado só pode aceder à rede virtual.

1. Depois de o VM ser criado, escolha **Ir para o recurso** para visualizar a página para o seu novo VM e, em seguida, escolher **Networking** em **Definições**.

1. Verifique se não existe **UM IP público.** Tome nota do **IP Privado,** que utiliza para ligar ao VM a partir da sua aplicação de função.

    ![Definições de rede no VM](./media/functions-create-vnet/vm-networking.png)

Tem agora um site WordPress totalmente implantado na sua rede virtual. Este site não é acessível a partir da internet pública.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Ligue a sua aplicação de função à rede virtual

Com um site WordPress a funcionar num VM numa rede virtual, pode agora ligar a sua aplicação de função a essa rede virtual.

1. Na sua nova aplicação de funções, selecione **Networking** no menu esquerdo.

1. Em **Integração VNet,** selecione **Clique aqui para configurar**.

    :::image type="content" source="./media/functions-create-vnet/networking-0.png" alt-text="Escolha o networking na aplicação de função":::

1. Na página **de Integração VNET,** selecione **Add VNet**.

    :::image type="content" source="./media/functions-create-vnet/networking-2.png" alt-text="Escolha o networking na aplicação de função":::

1. No **Estado da Funcionalidade de Rede,** utilize as definições na tabela abaixo da imagem:

    ![Defina a rede virtual da aplicação de função](./media/functions-create-vnet/networking-3.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Rede Virtual** | MyResourceGroup-vnet | Esta rede virtual foi a que criou anteriormente. |
    | **Sub-rede** | Criar nova sub-rede | Crie uma sub-rede na rede virtual para a sua aplicação de função utilizar. A integração VNet deve ser configurada para utilizar uma sub-rede vazia. Não importa que as suas funções utilizem uma sub-rede diferente da sua VM. A rede virtual liga automaticamente o tráfego entre as duas sub-redes. |
    | **Nome da sub-rede** | Function-Net | Nome da nova sub-rede. |
    | **Bloco de endereço de rede virtual** | 10.10.0.0/16 | Escolha o mesmo bloco de endereços utilizado pelo site WordPress. Deve ter apenas um bloco de endereços definido. |
    | **Intervalo de endereços** | 10.10.2.0/24   | O tamanho da sub-rede restringe o número total de casos que a sua aplicação de função de plano Premium pode escalar para. Este exemplo utiliza uma `/24` sub-rede com 254 endereços de anfitrião disponíveis. Esta sub-rede é excessivamente a provisionada, mas fácil de calcular. |

1. Selecione **OK** para adicionar a sub-rede. Feche as páginas de Estado de Integração e **Funcionalidade de Rede** **VNet** para voltar à página da aplicação da função.

A aplicação de função pode agora aceder à rede virtual onde o site WordPress está em execução. Em seguida, utiliza [Proxies de Funções Azure](functions-proxies.md) para devolver um ficheiro do site WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Criar um proxy para aceder aos recursos VM

Com a Integração VNet ativada, pode criar um proxy na sua app de função para encaminhar pedidos para o VM em execução na rede virtual.

1. Na sua aplicação de função, selecione  **Proxies** a partir do menu esquerdo e, em seguida, **selecione Adicionar**. Utilize as definições de procuração na tabela abaixo da imagem:

    :::image type="content" source="./media/functions-create-vnet/create-proxy.png" alt-text="Escolha o networking na aplicação de função":::

    | Definição  | Valor sugerido  | Descrição      |
    | -------- | ---------------- | ---------------- |
    | **Nome** | Fábrica | O nome pode ser qualquer valor. É usado para identificar o representante. |
    | **Modelo de rota** | /planta | Rota que mapeia para um recurso VM. |
    | **URL de back-end** | http://<YOUR_VM_IP>/wp-content/temas/twentyseventeen/assets/images/header.jpg | `<YOUR_VM_IP>`Substitua-o pelo endereço IP do seu WordPress VM que criou anteriormente. Este mapeamento devolve um único ficheiro do site. |

1. Selecione **Criar** para adicionar o proxy à sua aplicação de função.

## <a name="try-it-out"></a>Experimente

1. No seu navegador, tente aceder ao URL que usou como **URL backend**. Como esperado, o pedido está esgotado. Ocorre um intervalo porque o seu site WordPress está ligado apenas à sua rede virtual e não à internet.

1. Copie o valor **de URL proxy** do seu novo proxy e cole-o na barra de endereço do seu navegador. A imagem devolvida é do site WordPress que está a decorrer dentro da sua rede virtual.

    ![Ficheiro de imagem de planta devolvido do site WordPress](./media/functions-create-vnet/plant.png)

A sua aplicação de função está ligada tanto à internet como à sua rede virtual. O representante está a receber um pedido através da internet pública e, em seguida, atua como um simples representante http para encaminhar esse pedido para a rede virtual conectada. Em seguida, o representante transmite-lhe a resposta publicamente através da internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, o site WordPress serve como uma API que é chamada através da utilização de um proxy na aplicação de função. Este cenário faz um bom tutorial porque é fácil de configurar e visualizar. Pode utilizar qualquer outra API implantada numa rede virtual. Também poderia ter criado uma função com código que chama APIs implantados dentro da rede virtual. Um cenário mais realista é uma função que utiliza APIs do cliente de dados para chamar uma instância do SQL Server implementada na rede virtual.

As funções em execução num plano Premium partilham a mesma infraestrutura subjacente do Serviço de Aplicações que as aplicações web nos planos PremiumV2. Toda a documentação para [aplicações web no Azure App Service](../app-service/overview.md) aplica-se às funções do seu plano Premium.

> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de networking em Funções](./functions-networking-options.md)

[Plano Premium]: functions-scale.md#premium-plan
