---
title: Criar um ponto de terminação privado para uma ligação segura
titleSuffix: Azure Cognitive Search
description: Crie um ponto final privado numa rede virtual para uma ligação segura a um serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 043020abd44bc1f8e671cf386149d6a818136de9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014368"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Criar um ponto final privado para uma ligação segura à Pesquisa Cognitiva Azure

Neste artigo, você usará o portal Azure para criar uma nova instância do serviço de Pesquisa Cognitiva Azure que não pode ser acedida através da internet. Em seguida, configurará uma máquina virtual Azure na mesma rede virtual e usá-la-á para aceder ao serviço de pesquisa através de um ponto final privado.

Os pontos finais privados são fornecidos pela [Azure Private Link,](../private-link/private-link-overview.md)como um serviço separado. Para obter mais informações sobre os custos, consulte [a página de preços.](https://azure.microsoft.com/pricing/details/private-link/)

> [!Important]
> O suporte private Endpoint para a Pesquisa Cognitiva Azure pode ser configurado utilizando o portal Azure ou a [versão API Management REST 2020-03-13](/rest/api/searchmanagement/). Quando o ponto final de serviço é privado, algumas funcionalidades do portal são desativadas. Poderá ver e gerir informações de nível de serviço, mas o acesso do portal aos dados de índice e aos vários componentes do serviço, como as definições de índice, indexador e skillset, é restringido por razões de segurança.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Porquê usar um Ponto Final Privado para acesso seguro?

[Os pontos finais privados](../private-link/private-endpoint-overview.md) para a Azure Cognitive Search permitem que um cliente numa rede virtual aceda de forma segura a dados num índice de pesquisa sobre um [Link Privado](../private-link/private-link-overview.md). O ponto final privado utiliza um endereço IP a partir do [espaço de endereço de rede virtual](../virtual-network/private-ip-addresses.md) para o seu serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública. Para obter uma lista de outros serviços PaaS que suportam o Private Link, consulte a [secção de disponibilidade](../private-link/private-link-overview.md#availability) na documentação do produto.

Os pontos finais privados do seu serviço de pesquisa permitem-lhe:

- Bloqueie todas as ligações no ponto final público para o seu serviço de pesquisa.
- Aumentar a segurança da rede virtual, permitindo-lhe bloquear a exfiltração de dados da rede virtual.
- Ligue-se seguramente ao seu serviço de pesquisa a partir de redes no local que se conectam à rede virtual [usando VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoutes](../expressroute/expressroute-locations.md) com observação privada.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta secção, irá criar uma rede virtual e uma sub-rede para hospedar o VM que será utilizado para aceder ao ponto final privado do seu serviço de pesquisa.

1. A partir do separador casa portal Azure, **selecione Criar uma** rede  >  virtual de rede **de**  >  **Virtual network** recursos.

1. Na **Criar rede virtual,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição|
    | Grupo de recursos | Selecione **Criar novo,** insira *myResourceGroup,* em seguida, selecione **OK** |
    | Nome | Insira *myVirtualNetwork* |
    | Region | Selecione a região desejada |
    |||

1. Deixe as predefinições para o resto das definições. Clique **em Rever + criar** e, em seguida, **criar**

## <a name="create-a-search-service-with-a-private-endpoint"></a>Criar um serviço de pesquisa com um ponto final privado

Nesta secção, irá criar um novo serviço de Pesquisa Cognitiva Azure com um Ponto Final Privado. 

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar um recurso**  >  **Web**  >  **Azure Cognitive Search**.

1. Em **Novo Serviço de Pesquisa - Básicos, insira** ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | **DETALHES DE INSTÂNCIA** |  |
    | URL | Introduza um nome exclusivo. |
    | Localização | Selecione a região desejada. |
    | Escalão de preço | Selecione **Alterar o Nível de Preços** e escolha o nível de serviço pretendido. (Não suporte **no** free tier. Deve ser **básico** ou superior.) |
    |||
  
1. Selecione **Seguinte: Escala**.

1. Deixe os valores como padrão e selecione **Seguinte: Networking**.

1. Em **New Search Service - Networking**, selecione **Private** for **Endpoint connectivity(data)**.

1. Em **New Search Service - Networking**, select + **Add** under **Private endpoint**. 

1. Em **Criar Ponto Final Privado,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.|
    | Localização | Selecione **West US**.|
    | Name | Introduza *myPrivateEndpoint*.  |
    | Recurso secundário de destino | Deixe o **serviço de pesquisa** predefinido . |
    | **REDES** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* do grupo de recursos *myResourceGroup*. |
    | Sub-rede | Selecione *mySubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO** |  |
    | Integrar com zona DNS privada  | Deixe o padrão **Sim**. |
    | Zona DNS Privado  | Deixe o padrão ** (Novo) privatelink.search.windows.net**. |
    |||

1. Selecione **OK**. 

1. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração. 

1. Quando vir a mensagem **A validação passou**, selecione **Criar**. 

1. Assim que o fornecimento do seu novo serviço estiver concluído, navegue para o recurso que acabou de criar.

1. Selecione **Chaves** no menu de conteúdo esquerdo.

1. Copie a tecla de **administração Principal** para mais tarde, quando ligar ao serviço.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma** máquina Virtual  >  **Compute de** recurso  >  **Virtual machine**.

1. Na **Criar uma máquina virtual - Básicos, insira** ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DE INSTÂNCIA** |  |
    | Nome da máquina virtual | *Insira o myVm*. |
    | Region | Selecione **West US** ou qualquer região que estiver usando. |
    | Opções de disponibilidade | Deixar o incumprimento **Não é necessário um despedimento de infraestrutura**. |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows** Server . |
    | Tamanho | Deixe o **padrão DS1 v2**. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Insira um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reentre na senha. |
    | **REGRAS DA PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe o predefinido **Deixar as portas selecionadas**. |
    | Selecione as portas de entrada | Deixe o PDR predefinido **(3389)**. |
    | **POUPE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o **nº** padrão . |
    |||

1. Selecione **Seguinte: Discos**.

1. Em **Criar uma máquina virtual – Discos**, mantenha as predefinições e selecione **Seguinte: Redes**.

1. Na **Criação de uma máquina virtual - Networking,** selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe a **Rede MyVirtual padrão**.  |
    | Espaço de endereços | Deixe o **padrão 10.1.0.0/24**.|
    | Sub-rede | Deixe o **mySubnet predefinido (10.1.0.0/24)**.|
    | IP público | Deixe o **padrão (novo) myVm-ip**. |
    | Portas de entrada públicas | Selecione **Deixe as portas selecionadas.** |
    | Selecione as portas de entrada | Selecione **HTTP** e **RDP**.|
    ||

   > [!NOTE]
   > Os endereços IPv4 podem ser expressos em formato [CIDR.](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Lembre-se de evitar a gama IP reservada à rede privada, conforme descrito no [RFC 1918](https://tools.ietf.org/html/rfc1918):
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

1. Quando vir a mensagem **A validação passou**, selecione **Criar**. 

## <a name="connect-to-the-vm"></a>Ligar à VM

Faça o download e, em seguida, ligue-se ao *myVm VM* da seguinte forma:

1. Na barra de pesquisa do portal, introduza *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **Ligar,** **o Connect à máquina virtual** abre-se.

1. Selecione **Transferir Ficheiro RDP**. O Azure cria um ficheiro Remote Desktop Protocol *(.rdp)* e transfere-o para o computador.

1. Abra o ficheiro .rdp* descarregado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Introduza o nome de utilizador e a palavra-passe que especificou ao criar o VM.

        > [!NOTE]
        > Poderá ter de selecionar **Mais escolhas**  >  **Utilize uma conta diferente**, para especificar as credenciais que introduziu quando criou o VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se recebeu um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Assim que o ambiente de trabalho em VM aparecer, minimize-o para voltar ao seu ambiente de trabalho local.  

## <a name="test-connections"></a>Ligações de teste

Nesta secção, verificará o acesso à rede privada ao serviço de pesquisa e ligará-o em privado ao ponto de terminação privado.

Quando o ponto final do serviço de pesquisa é privado, algumas funcionalidades do portal são desativadas. Poderá ver e gerir as definições de nível de serviço, mas o acesso do portal aos dados de índice e vários outros componentes do serviço, como as definições de índice, indexador e skillset, é restringido por razões de segurança.

1. No Ambiente de Trabalho Remoto do *myVM,* abra o PowerShell.

1. Insira 'nslookup [nome do serviço de pesquisa].search.windows.net

    Irá receber uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. A partir do VM, ligue-se ao serviço de pesquisa e crie um índice. Pode seguir este [quickstart](search-get-started-rest.md) para criar um novo índice de pesquisa no seu serviço utilizando a API REST. A configuração de pedidos a partir de uma ferramenta de teste web API requer o ponto final do serviço de pesquisa (https://[nome do serviço de pesquisa].search.windows.net) e a chave api-chave de administração que copiou num passo anterior.

1. A completar o arranque rápido do VM é a confirmação de que o serviço está totalmente operacional.

1. Feche a ligação remota do ambiente de trabalho ao *myVM*. 

1. Para verificar se o seu serviço não está acessível num ponto final público, abra o Carteiro na sua estação de trabalho local e tente as primeiras várias tarefas no arranque rápido. Se receber um erro que o servidor remoto não existe, configura com sucesso um ponto final privado para o seu serviço de pesquisa.

## <a name="clean-up-resources"></a>Limpar os recursos 
Quando terminar de usar o Private Endpoint, o serviço de pesquisa e o VM, elimine o grupo de recursos e todos os recursos que contém:
1. Introduza o  *myResourceGroup*   na caixa de **Pesquisa** no topo do portal e selecione o  *myResourceGroup*   a partir dos resultados da pesquisa. 
1. Selecione **Eliminar grupo de recursos**. 
1. Introduza  *o myResourceGroup*   para type the RESOURCE GROUP **NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, criou um VM numa rede virtual e um serviço de pesquisa com um Private Endpoint. Ligou-se ao VM a partir da internet e comunicou-se de forma segura ao serviço de pesquisa utilizando o Private Link. Para saber mais sobre o Private Endpoint, veja [o que é Azure Private Endpoint?](../private-link/private-endpoint-overview.md)