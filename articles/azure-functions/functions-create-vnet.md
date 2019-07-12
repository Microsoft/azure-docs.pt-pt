---
title: Integrar as funções do Azure com uma rede virtual do Azure
description: Um tutorial passo a passo que lhe mostra como ligar uma função a uma rede virtual do Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0a31b58a3c843a2add0c84dc1a3ad4ab6417815e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612893"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Tutorial: integrar funções com uma rede virtual do Azure

Este tutorial mostra-lhe como utilizar as funções do Azure para se ligar aos recursos numa rede virtual do Azure. irá criar uma função que tenha acesso a ambos da internet e a uma VM a executar o WordPress na rede virtual.

> [!div class="checklist"]
> * Criar uma aplicação de funções no plano Premium
> * Implementar um site WordPress a VM numa rede virtual
> * Ligar a aplicação de funções para a rede virtual
> * Criar uma função de proxy para aceder aos recursos do WordPress
> * Solicitar um arquivo de WordPress a partir de dentro da rede virtual

> [!NOTE]  
> Este tutorial cria uma aplicação de funções no plano Premium. Este plano de alojamento está atualmente em pré-visualização. Para obter mais informações, consulte [plano Premium].

## <a name="topology"></a>Topologia

O diagrama seguinte mostra a arquitetura da solução que criou:

 ![Interface do Usuário para a integração da rede virtual](./media/functions-create-vnet/topology.png)

As funções em execução no plano Premium têm as mesmas funcionalidades de alojamento, como aplicações web no serviço de aplicações do Azure, que inclui a funcionalidade de integração de VNet. Para saber mais sobre a integração de VNet, incluindo a resolução de problemas e configuração avançada, veja [integrar a sua aplicação com uma Azure virtual network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é importante que compreenda o endereçamento e de sub-redes IP. Pode começar com [neste artigo que abrange as noções básicas de endereçamento e de sub-redes](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Muitos que mais artigos e vídeos estão disponíveis online.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Criar uma aplicação de funções num plano Premium

Primeiro, crie uma aplicação de funções no [plano Premium]. Este plano fornece escala sem servidor, dar suporte a integração da rede virtual.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Pode afixar a aplicação de funções para o dashboard ao selecionar o ícone de pin no canto superior direito. A afixação torna mais fácil voltar para esta aplicação de função depois de criar a VM.

## <a name="create-a-vm-inside-a-virtual-network"></a>Criar uma VM numa rede virtual

Em seguida, crie uma VM pré-configurada que é executado WordPress numa rede virtual ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) por Jetware). Uma VM do WordPress é utilizada devido à sua baixo custo e a conveniência. Esse mesmo cenário funciona com todos os recursos numa rede virtual, como REST APIs, ambientes de serviço de aplicações e outros serviços do Azure. 

1. No portal, selecione **+ criar um recurso** no painel de navegação à esquerda, no tipo de campo de pesquisa `WordPress LEMP7 Max Performance`, e prima Enter.

1. Escolher **Wordpress LEMP Max Performance** nos resultados da pesquisa. Selecionar um plano de software do **Wordpress LEMP Max Performance para CentOS** como o **plano de Software** e selecione **criar**.

1. Na **Noções básicas** separador, utilize as definições de VM especificado na tabela abaixo da imagem:

    ![Guia de conceitos básicos para criar uma VM](./media/functions-create-vnet/create-vm-1.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Subscrição** | A sua subscrição | A subscrição sob a qual seus recursos são criados. | 
    | **[Grupo de recursos](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Escolha `myResourceGroup`, ou o grupo de recursos que criou com a sua aplicação de função. Utilizar o mesmo grupo de recursos para a aplicação de funções, a VM do WordPress e o plano de alojamento tornam mais fácil limpar os recursos quando tiver terminado com este tutorial. |
    | **Nome da máquina virtual** | VNET-Wordpress | O nome da VM tem de ser exclusivo no grupo de recursos |
    | **[Região](https://azure.microsoft.com/regions/)** | (Europa) Europa Ocidental | Escolha uma região perto de si ou perto as funções que aceder à VM. |
    | **Tamanho** | B1s | Escolher **alterar o tamanho** e, em seguida, selecione a imagem de padrão de B1s, que tem 1 vCPU e 1 GB de memória. |
    | **Tipo de autenticação** | Palavra-passe | Para utilizar a autenticação de palavra-passe, também tem de especificar um **nome de utilizador**, um segura **palavra-passe**e, em seguida **Confirmar palavra-passe**. Para este tutorial, não terá de iniciar sessão na VM, a menos que precisa resolver problemas. |

1. Escolha o **Networking** separador e em redes virtuais de configurar, selecione **criar nova**.

1. Na **criar rede virtual**, utilize as definições na tabela abaixo da imagem:

    ![Separador de rede de criar a VM](./media/functions-create-vnet/create-vm-2.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Name** | myResourceGroup-vnet | Pode utilizar o nome predefinido gerado para a rede virtual. |
    | **Intervalo de endereços** | 10.10.0.0/16 | Utilize um intervalo de endereço único para a rede virtual. |
    | **Nome da sub-rede** | Tutorial-Net | Nome da sub-rede. |
    | **Intervalo de endereços** (sub-rede) | 10.10.1.0/24   | O tamanho da sub-rede define o número de interfaces podem ser adicionados à sub-rede. Esta sub-rede é utilizada pelo WordPress site.  A `/24` sub-rede fornece 254 endereços de anfitriões. |

1. Selecione **OK** para criar a rede virtual.

1. De volta a **Networking** separador, escolha **None** para **IP público**.

1. Escolha o **gerenciamento** separador, em seguida, em **conta de armazenamento de diagnóstico**, escolha a conta de armazenamento que criou com a sua aplicação de função.

1. Selecione **Rever + criar**. Depois de concluída a validação, selecione **criar**. A VM criar processo demora alguns minutos. A VM criada só pode aceder a rede virtual.

1. Depois da VM é criada, escolha **Ir para recurso** para ver a página da nova VM, em seguida, escolha **redes** sob **definições**.

1. Certifique-se de que não há nenhuma **IP público**. Tome nota da **IP privado**, que utilizar para ligar à VM a partir da sua aplicação de função.

    ![Definições de rede na VM](./media/functions-create-vnet/vm-networking.png)

Agora tem um site WordPress implementado inteiramente na sua rede virtual. Este site não está acessível a partir da internet pública.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Ligar a sua aplicação de função para a rede virtual

Com um site WordPress em execução numa VM numa rede virtual, pode agora ligar a sua aplicação de funções para essa rede virtual.

1. Na sua nova aplicação de função, selecione **funcionalidades de plataforma** > **redes**.

    ![Escolha o funcionamento em rede na function app](./media/functions-create-vnet/networking-0.png)

1. Sob **integração de VNet**, selecione **clique aqui para configurar**.

    ![Estado para a configuração de um recurso de rede](./media/functions-create-vnet/Networking-1.png)

1. Na página de integração de rede virtual, selecione **VNet adicionar (pré-visualização)** .

    ![Adicionar a pré-visualização de integração de VNet](./media/functions-create-vnet/networking-2.png)

1. Na **estado de recurso de rede**, utilize as definições na tabela abaixo da imagem:

    ![Definir a rede virtual da aplicação de função](./media/functions-create-vnet/networking-3.png)

    | Definição      | Valor sugerido  | Descrição      |
    | ------------ | ---------------- | ---------------- |
    | **Rede Virtual** | MyResourceGroup-vnet | Esta rede virtual é aquela que criou anteriormente. |
    | **Sub-rede** | Criar nova sub-rede | Crie uma sub-rede na rede virtual para a sua aplicação de função a utilizar. Integração VNet tem de ser configurada para utilizar uma sub-rede vazia. Não importa que as suas funções utilizam uma sub-rede diferente do que a VM. A rede virtual encaminha automaticamente o tráfego entre as duas sub-redes. |
    | **Nome da sub-rede** | Função-Net | Nome da nova sub-rede. |
    | **Bloco de endereços de rede virtual** | 10.10.0.0/16 | Escolha o mesmo bloco de endereços utilizado pelo WordPress site. Deverá ter apenas um bloco de endereços definido. |
    | **Intervalo de endereços** | 10.10.2.0/24   | O tamanho da sub-rede restringe o número total de instâncias que a aplicação de funções de plano Premium pode aumentar horizontalmente para. Este exemplo utiliza um `/24` sub-rede com 254 endereços de anfitriões disponíveis. Esta sub-rede é provisionados em excesso, mas o mais fácil calcular. |

1. Selecione **OK** para adicionar a sub-rede. Feche as páginas de integração de VNet e o estado da funcionalidade de rede para regressar à página da aplicação de função.

A aplicação de função agora pode acessar a rede virtual onde está a executar o site do WordPress. Em seguida, utilize [Proxies de funções do Azure](functions-proxies.md) para retornar um arquivo do site do WordPress.

## <a name="create-a-proxy-to-access-vm-resources"></a>Criar um proxy para aceder a recursos VM

Com a integração de VNet ativada, pode criar um proxy na sua aplicação de função para reencaminhar solicitações para a VM em execução na rede virtual.

1. Na sua aplicação function app, selecione **Proxies** >  **+** , em seguida, utilize as definições de proxy na tabela abaixo da imagem:

    ![Definir as definições de proxy](./media/functions-create-vnet/create-proxy.png)

    | Definição  | Valor sugerido  | Descrição      |
    | -------- | ---------------- | ---------------- |
    | **Name** | Fábrica | O nome pode ser qualquer valor. É utilizado para identificar o proxy. |
    | **Modelo de rota** | /plant | Rota que mapeia para um recurso VM. |
    | **URL de back-end** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Substitua `<YOUR_VM_IP>` com o endereço IP da sua VM do WordPress que criou anteriormente. Este mapeamento devolve um único arquivo do site. |

1. Selecione **criar** para adicionar o proxy para a sua aplicação de função.

## <a name="try-it-out"></a>Experimentar

1. No seu browser, tente aceder ao URL que utilizou como o **URL de back-end**. Conforme o esperado, o pedido exceder o tempo limite. Um tempo limite ocorre porque o site do WordPress está ligado apenas à sua rede virtual e não na internet.

1. Copiar o **URL de Proxy** valor a partir do seu novo proxy e cole-o na barra de endereço do seu browser. A imagem de retornado é a partir do site WordPress em execução no interior da rede virtual.

    ![Ficheiro de imagem de fábrica devolvido a partir do site do WordPress](./media/functions-create-vnet/plant.png)

A aplicação function app está ligada à internet e a rede virtual. O proxy é receber um pedido através da internet pública e, em seguida, atuando como um proxy HTTP simple para encaminhar a solicitação para a rede virtual ligada. O proxy retransmite a resposta é com publicamente na Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, o site do WordPress serve como uma API que é chamada ao utilizar um proxy na function app. Esse cenário faz um bom tutorial porque é fácil configurar e visualizar. Pode usar qualquer outra API implementada numa rede virtual. Poderia também ter criado uma função com o código que chama APIs implementadas na rede virtual. Um cenário mais realista é uma função que usa APIs de cliente de dados para chamar uma instância do SQL Server implementada na rede virtual.

As funções em execução num plano Premium partilham a mesma infra-estrutura subjacente de serviço de aplicações como aplicações web nos planos de PremiumV2. Toda a documentação para [aplicações no serviço de aplicações do Azure web](../app-service/overview.md) aplica-se às suas funções de plano Premium.

> [!div class="nextstepaction"]
> [Saiba mais sobre as opções de funcionamento em rede nas funções](./functions-networking-options.md)

[Plano premium]: functions-scale.md#premium-plan
