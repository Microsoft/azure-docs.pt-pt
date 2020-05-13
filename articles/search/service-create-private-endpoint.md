---
title: Criar um ponto final privado para uma ligação segura
titleSuffix: Azure Cognitive Search
description: Criar um ponto final privado numa rede virtual para uma ligação segura a um serviço de Pesquisa Cognitiva Azure
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 0945743fb2cf3e37345ff562250e48511944cee6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125558"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Crie um ponto final privado para uma ligação segura à Pesquisa Cognitiva Azure

Neste artigo, você usará o portal Azure para criar uma nova instância de serviço de Pesquisa Cognitiva Azure que não pode ser acedida através da internet. Em seguida, configurará uma máquina virtual Azure na mesma rede virtual e usá-la-á para aceder ao serviço de pesquisa através de um ponto final privado.

> [!Important]
> O suporte private Endpoint para pesquisa cognitiva azure pode ser configurado usando o portal Azure ou a [versão Management REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/). Quando o ponto final do serviço é privado, algumas funcionalidades do portal são desativadas. Poderá visualizar e gerir informações de nível de serviço, mas o acesso ao portal aos dados de índice e aos vários componentes do serviço, tais como as definições de indexador, indexador e skillset, é restrito por razões de segurança.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Porquê usar um Ponto Final Privado para acesso seguro?

[Os pontos finais privados](../private-link/private-endpoint-overview.md) para a Pesquisa Cognitiva Azure permitem a um cliente numa rede virtual aceder de forma segura a dados num índice de pesquisa sobre um [Link Privado](../private-link/private-link-overview.md). O ponto final privado utiliza um endereço IP do espaço de endereço de [rede virtual](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) para o seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. Para obter uma lista de outros serviços PaaS que suportam private link, consulte a [secção de disponibilidade](../private-link/private-link-overview.md#availability) na documentação do produto.

Os pontos finais privados para o seu serviço de pesquisa permitem-lhe:

- Bloqueie todas as ligações no ponto final do público para o seu serviço de pesquisa.
- Aumente a segurança da rede virtual, permitindo bloquear a exfiltração de dados da rede virtual.
- Ligue-se de forma segura ao seu serviço de pesquisa a partir de redes no local que se ligam à rede virtual utilizando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../expressroute/expressroute-locations.md) com o peering privado.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, irá criar uma rede virtual e uma subnet para acolher o VM que será utilizado para aceder ao ponto final privado do seu serviço de pesquisa.

1. A partir do separador casa portal Azure, selecione **Criar uma**rede virtual de rede de recursos  >  **Networking**  >  **Virtual network**.

1. Na **Create rede virtual,** introduza ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição|
    | Grupo de recursos | Selecione **Criar novo,** insira *o myResourceGroup*e, em seguida, selecione **OK** |
    | Name | Insira *myvirtualNetwork* |
    | Região | Selecione a sua região desejada |
    |||

1. Deixe as predefinições para o resto das definições. Clique em **Rever + criar** e, em seguida, **criar**

## <a name="create-a-search-service-with-a-private-endpoint"></a>Criar um serviço de pesquisa com um ponto final privado

Nesta secção, irá criar um novo serviço de Pesquisa Cognitiva Azure com um Ponto Final Privado. 

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar um recurso**  >  **Web**  >  **Azure Cognitive Search**.

1. Em **New Search Service - Basics,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | do IdP | Introduza um nome exclusivo. |
    | Localização | Selecione a região desejada. |
    | Escalão de preço | Selecione **Alterar O Nível** de Preços e escolha o seu nível de serviço pretendido. (Não suporte **no** free tier. Deve ser **básico** ou superior.) |
    |||
  
1. Selecione **Seguinte: Escala**.

1. Deixe os valores como padrão e selecione **Seguinte: Networking**.

1. Em **New Search Service - Networking**, selecione **Private** for **Endpoint connectivity (data)**.

1. Em **Novo Serviço de Pesquisa - Networking**, selecione + **Adicionar** sob ponto **final privado**. 

1. In **Create Private Endpoint,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | Localização | Selecione **West US**.|
    | Name | Insira *o meu PrivateEndpoint*.  |
    | Sub-recurso-alvo | Deixe o serviço de pesquisa por defeito **.** |
    | **NETWORKING** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* do grupo de recursos *myResourceGroup*. |
    | Subrede | Selecione *mySubnet*. |
    | **Integração privada do DNS** |  |
    | Integrar com zona privada de DNS  | Deixe o padrão **Sim.** |
    | Zona DNS Privado  | Deixe o padrão ** (Novo) privatelink.search.windows.net**. |
    |||

1. Selecione **OK**. 

1. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração. 

1. Quando vir a **mensagem de validação passada,** selecione **Criar**. 

1. Assim que o fornecimento do seu novo serviço estiver completo, navegue para o recurso que acabou de criar.

1. Selecione **Teclas** do menu de conteúdo esquerdo.

1. Copie a **chave de administração primária** para mais tarde, quando ligar ao serviço.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma**máquina  >  **Compute**  >  **Compute Virtual**de recurso .

1. Em **Criar uma máquina virtual - Básicos,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *o myVm*. |
    | Região | Selecione **Oeste dos EUA** ou qualquer região que esteja a usar. |
    | Opções de disponibilidade | Deixar o padrão Não é necessário um despedimento de **infraestrutura.** |
    | Imagem | Selecione **Windows Server 2019 Datacenter**. |
    | Tamanho | Deixe o **Padrão DS1 v2**padrão padrão padrão . |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Introduza um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reintroduza a senha. |
    | **REGRAS PORTUÁRIAS DE ENTRADA** |  |
    | Portos de entrada pública | Deixe o padrão **Permitir portas selecionadas**. |
    | Selecione portas de entrada | Deixe o RDP predefinido **(3389)**. |
    | **POUPE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **Nº**. |
    |||

1. Selecione **Seguinte: Discos**.

1. Em **Criar uma máquina virtual - Discos,** deixe as predefinições e selecione **Seguinte: Networking**.

1. Em **Criar uma máquina virtual - Networking,** selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**predefinido .  |
    | Espaço de endereços | Deixe o padrão **10.1.0.0/24**.|
    | Subrede | Deixe a mySubnet por defeito **(10.1.0.0/24)**.|
    | IP público | Deixe o **myVm-ip padrão (novo)** |
    | Portos de entrada pública | **Selecione Permitir portas selecionadas**. |
    | Selecione portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

1. Selecione **Rever + criar**. É levado para o **Review + criar** página onde o Azure valida a sua configuração.

1. Quando vir a **mensagem de validação passada,** selecione **Criar**. 


## <a name="connect-to-the-vm"></a>Ligar à VM

Descarregue e, em seguida, ligue-se ao *myVm* VM da seguinte forma:

1. Na barra de pesquisa do portal, entre no *myVm.*

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **ligue-se à máquina virtual.**

1. Selecione **Download RDP File**. O Azure cria um ficheiro Remote Desktop *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro download.rdp*.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe especificado ao criar o VM.

        > [!NOTE]
        > Pode ser necessário selecionar **Mais escolhas**  >  **Utilize uma conta diferente,** para especificar as credenciais que inseriu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber um aviso de certificado, selecione **Sim** ou **Continue**.

1. Assim que o ambiente de trabalho vM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  


## <a name="test-connections"></a>Ligações de teste

Nesta secção, verificará o acesso da rede privada ao serviço de pesquisa e ligar-se-á em privado ao ponto final privado.

Quando o ponto final do serviço de pesquisa é privado, algumas funcionalidades do portal são desativadas. Poderá visualizar e gerir as definições de nível de serviço, mas o acesso ao portal aos dados de índice e vários outros componentes do serviço, tais como as definições de indexador, indexador e skillset, é restrito por razões de segurança.

1. No Ambiente de Trabalho Remoto do *myVM,* abra a PowerShell.

1. Insira 'nslookup [nome do serviço de pesquisa].search.windows.net'

    Receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. A partir do VM, ligue-se ao serviço de pesquisa e crie um índice. Pode seguir este [quickstart](search-get-started-postman.md) para criar um novo índice de pesquisa no seu serviço no Carteiro utilizando a API REST. A configuração de pedidos do Carteiro requer o ponto final do serviço de pesquisa (https://[search service name].search.windows.net) e a chave de api-key de administrador que copiou num passo anterior.

1. Completar o arranque rápido a partir do VM é a sua confirmação de que o serviço está totalmente operacional.

1. Feche a ligação remota de ambiente de trabalho ao *myVM*. 

1. Para verificar se o seu serviço não está acessível num ponto final público, abra o Carteiro na sua estação de trabalho local e tente as primeiras tarefas no arranque rápido. Se receber um erro que o servidor remoto não existe, configura com sucesso um ponto final privado para o seu serviço de pesquisa.

## <a name="clean-up-resources"></a>Limpar recursos 
Quando terminar usando o Private Endpoint, o serviço de pesquisa e o VM, elimine o grupo de recursos e todos os recursos que contém:
1. Introduza o *myResourceGroup*   na caixa de **pesquisa** na parte superior do portal e selecione *myResourceGroup*   a partir dos resultados da pesquisa. 
1. Selecione **Eliminar grupo de recursos**. 
1. Introduza *o myResourceGroup*   para ESCREVER O NOME DE GRUPO DE **RECURSOS** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um VM numa rede virtual e um serviço de pesquisa com um Private Endpoint. Ligou-se ao VM a partir da internet e comunicou-se de forma segura ao serviço de pesquisa utilizando o Private Link. Para saber mais sobre private endpoint, veja [o que é Azure Private Endpoint?](../private-link/private-endpoint-overview.md)
