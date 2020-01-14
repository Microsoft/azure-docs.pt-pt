---
title: Criar um ponto de extremidade privado para uma conexão segura
titleSuffix: Azure Cognitive Search
description: Configurar um ponto de extremidade privado em uma rede virtual para uma conexão segura com um serviço de Pesquisa Cognitiva do Azure
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: cfa8db0d00f351f5ab2bda96744305ca83cccb19
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922460"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Criar um ponto de extremidade privado para uma conexão segura com o Azure Pesquisa Cognitiva (versão prévia)

[Pontos de extremidade privados](../private-link/private-endpoint-overview.md) para o Azure pesquisa cognitiva permitem que um cliente em uma rede virtual acesse dados com segurança em um índice de pesquisa por um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do [espaço de endereço de rede virtual](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) para o serviço de pesquisa. O tráfego de rede entre o cliente e o serviço de pesquisa atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública. Para obter uma lista de outros serviços de PaaS que dão suporte ao link privado, verifique a [seção disponibilidade](../private-link/private-link-overview.md#availability) na documentação do produto.

> [!Important]
> O suporte de ponto de extremidade privado para o Azure Pesquisa Cognitiva está disponível como uma visualização de acesso limitado e, no momento, não se destina ao uso em produção. Preencha e envie o formulário de [solicitação de acesso](https://aka.ms/SearchPrivateLinkRequestAccess) se desejar acessar a visualização. O formulário solicita informações sobre você, sua empresa e arquitetura de aplicativo geral. Depois de examinarmos sua solicitação, você receberá um email de confirmação com instruções adicionais.
>
> Depois de receber acesso à visualização, você poderá configurar pontos de extremidade privados para seu serviço usando o portal do Azure e a API REST versão [2019-10-06-Preview](search-api-preview.md).
>   

Pontos de extremidade privados para o serviço de pesquisa permitem que você:

- Bloquear todas as conexões no ponto de extremidade público para o serviço de pesquisa.
- Aumente a segurança para a rede virtual, permitindo que você bloqueie vazamento de dados da rede virtual.
- Conecte-se com segurança ao serviço de pesquisa de redes locais que se conectam à rede virtual usando [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [expressroute ao qual](../expressroute/expressroute-locations.md) com emparelhamento privado.

> [!NOTE]
> Atualmente, há algumas limitações na versão prévia que você deve estar ciente:
> * Disponível somente para serviços de pesquisa na camada **básica** . 
> * Disponível nas regiões oeste dos EUA 2, Oeste EUA Central, leste dos EUA, Sul EUA Central, leste da Austrália e sudeste da Austrália.
> * Quando o ponto de extremidade de serviço é privado, alguns recursos do portal são desabilitados. Você poderá exibir e gerenciar as informações de nível de serviço, mas o acesso ao portal para indexar dados e os vários componentes no serviço, como o índice, o indexador e as definições de qualificações, é restrito por motivos de segurança.
> * Quando o ponto de extremidade de serviço é privado, você deve usar a API de pesquisa para carregar documentos no índice.
> * Você deve usar o link a seguir para ver a opção de suporte ao ponto de extremidade privado no portal do Azure: https://portal.azure.com/?feature.enablePrivateEndpoints=true

Neste artigo, você aprenderá a usar o portal para criar uma nova instância do serviço de Pesquisa Cognitiva do Azure que não pode ser acessada por meio de um endereço IP público, configurar uma máquina virtual do Azure na mesma rede virtual e usá-la para acessar o serviço de pesquisa por meio de um extremidade.


## <a name="create-a-vm"></a>Criar uma VM
Nesta seção, você criará uma rede virtual e uma sub-rede para hospedar a VM que será usada para acessar o ponto de extremidade privado do serviço de pesquisa.

### <a name="create-the-virtual-network"></a>Criar a rede virtual

1. Na guia portal do Azure página inicial, selecione **criar um recurso** > **rede** > **rede virtual**.

1. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira *MyVirtualNetwork* |
    | Espaço de endereços | Digite *10.1.0.0/16* |
    | Subscrição | Selecione a sua subscrição|
    | Grupo de recursos | Selecione **criar novo**, insira *MyResource*e, em seguida, selecione **OK** |
    | Localização | Selecione **oeste dos EUA** ou qualquer região que você esteja usando|
    | Nome da sub-rede | Inserir *Mysubnet* |
    | Sub-rede - Intervalo de endereços | Insira *10.1.0.0/24* |
    |||

1. Deixe o restante como padrão e selecione **criar**.


## <a name="create-your-search-service-with-a-private-endpoint"></a>Criar seu serviço de pesquisa com um ponto de extremidade privado

Nesta seção, você criará um novo serviço de Pesquisa Cognitiva do Azure com um ponto de extremidade privado. 

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **Web** > **Azure pesquisa cognitiva**.

1. Em **novos serviço de pesquisa-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | **DETALHES DA INSTÂNCIA** |  |
    | URL | Introduza um nome exclusivo. |
    | Localização | Selecione a região que você especificou ao solicitar acesso a esse recurso de visualização. |
    | Escalão de preço | Selecione **alterar tipo de preço** e escolha **básico**. Essa camada é necessária para a versão prévia. |
    |||
  
1. Selecione **Avançar: escala**.

1. Deixe os valores como padrão e selecione **Avançar: rede**.

1. Em **nova serviço de pesquisa-rede**, selecione **privado** para **conectividade de ponto de extremidade (dados)** .

1. Em **novo serviço de pesquisa-rede**, selecione **+ Adicionar** em **ponto de extremidade privado**. 

1. Em **criar ponto de extremidade privado**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.|
    | Localização | Selecione **oeste dos EUA**.|
    | Nome | Insira *myPrivateEndpoint*.  |
    | Sub-recurso de destino | Deixe o **searchService**padrão. |
    | **REDE** |  |
    | Rede virtual  | Selecione *MyVirtualNetwork* no grupo de recursos *MyResource*Group. |
    | Subrede | Selecione *mysubnet*. |
    | **INTEGRAÇÃO DE DNS PRIVADO** |  |
    | Integrar com a zona DNS privada  | Deixe o padrão **Sim**. |
    | Zona DNS Privado  | Deixe o padrão * * (New) privatelink.search.windows.net * *. |
    |||

1. Selecione **OK**. 

1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração. 

1. Quando você vir a mensagem **validação aprovada** , selecione **criar**. 

1. Depois que o provisionamento do novo serviço for concluído, navegue até o recurso que você acabou de criar.

1. Selecione **chaves** no menu conteúdo à esquerda.

1. Copie a **chave de administração primária** para mais tarde.

### <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

1. No lado superior esquerdo da tela na portal do Azure, selecione **criar um recurso** > **computação** > **máquina virtual**.

1. Em **criar uma máquina virtual-noções básicas**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Myresourceattribute**. Você criou isso na seção anterior.  |
    | **DETALHES DA INSTÂNCIA** |  |
    | Nome da máquina virtual | Insira *myVm*. |
    | Região | Selecione **oeste dos EUA** ou qualquer região que você esteja usando. |
    | Opções de disponibilidade | Deixe o padrão **nenhuma redundância de infraestrutura necessária**. |
    | Imagem | Selecione **Windows Server 2019 datacenter**. |
    | Tamanho | Deixe o **DS1 v2**padrão. |
    | **CONTA DE ADMINISTRADOR** |  |
    | Nome de utilizador | Insira um nome de usuário de sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-Passe | Digite a senha novamente. |
    | **REGRAS DE PORTA DE ENTRADA** |  |
    | Portas de entrada públicas | Deixe o padrão **permitir portas selecionadas**. |
    | Selecionar portas de entrada | Deixe o **RDP padrão (3389)** . |
    | **ECONOMIZE DINHEIRO** |  |
    | Já tem uma licença do Windows? | Deixe o padrão **não**. |
    |||

1. Selecione **Avançar: discos**.

1. Em **criar uma máquina virtual-discos**, deixe os padrões e selecione **Avançar: rede**.

1. Em **criar uma máquina virtual-rede**, selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Deixe o **MyVirtualNetwork**padrão.  |
    | Espaço de endereços | Deixe o **10.1.0.0/24**padrão.|
    | Subrede | Deixe o padrão **mysubnet (10.1.0.0/24)** .|
    | IP público | Deixe o padrão **(novo) myVm-IP**. |
    | Portas de entrada públicas | Selecione **permitir portas selecionadas**. |
    | Selecionar portas de entrada | Selecione **http** e **RDP**.|
    ||

1. Selecione **Rever + criar**. Você é levado para a página **revisar + criar** , na qual o Azure valida sua configuração.

1. Quando você vir a mensagem **validação aprovada** , selecione **criar**. 


## <a name="connect-to-a-vm-from-the-internet"></a>Ligar a uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte maneira:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Ligar**. Depois de selecionar o botão **conectar** , **Conecte-se à máquina virtual** é aberto.

1. Selecione **transferir ficheiro RDP**. O Azure cria um arquivo protocolo RDP ( *. rdp*) e o baixa em seu computador.

1. Abra o arquivo. rdp * baixado.

    1. Se lhe for pedido, selecione **Ligar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **mais escolhas** > **usar uma conta diferente**, para especificar as credenciais inseridas quando você criou a VM.

1. Selecione **OK**.

1. Poderá receber um aviso de certificado durante o processo de início de sessão. Se você receber um aviso de certificado, selecione **Sim** ou **continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para a área de trabalho local.  


## <a name="access-the-search-service-privately-from-the-vm"></a>Acessar o serviço de pesquisa de forma privada da VM

Nesta seção, você verificará o acesso à rede privada ao serviço de pesquisa e se conectará de modo privado ao usando o ponto de extremidade privado.

1. No Área de Trabalho Remota do *myVM*, abra o PowerShell.

1. Insira ' nslookup [nome do serviço de pesquisa]. Search. Windows. net '

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```
1. Siga este guia de [início rápido](search-get-started-postman.md) da VM para criar um novo índice de pesquisa em seu serviço no postmaster usando a API REST.  Use a chave que você copiou em uma etapa anterior para se autenticar no serviço.

1. Experimente várias dessas mesmas solicitações no postmaster em sua estação de trabalho local.

1. Se você conseguir concluir o início rápido da VM, mas receber um erro informando que o servidor remoto não existe na estação de trabalho local, você configurou com êxito um ponto de extremidade privado para o serviço de pesquisa.

1. Feche a conexão de área de trabalho remota para *myVM*. 


## <a name="clean-up-resources"></a>Limpar recursos 
Quando você terminar de usar o ponto de extremidade privado, o serviço de pesquisa e a VM, exclua o grupo de recursos e todos os recursos que ele contém:
1. Insira o *MyResource* na caixa de **pesquisa** na parte superior do portal e selecione o *MyResource* nos resultados da pesquisa. 
1. Selecione **Eliminar grupo de recursos**. 
1. Insira o grupo de *recursos* de para **digite o nome** e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você criou uma VM em uma rede virtual e um serviço de pesquisa com um ponto de extremidade privado. Você se conectou à VM da Internet e se comunica com segurança ao serviço de pesquisa usando o link privado. Para saber mais sobre o ponto de extremidade privado, consulte [o que é o ponto de extremidade privado do Azure?](../private-link/private-endpoint-overview.md).
