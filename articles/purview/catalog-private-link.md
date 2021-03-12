---
title: Use pontos finais privados para acesso seguro à Purview
description: Este artigo descreve como pode configurar um ponto final privado para a sua conta Purview
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: d9088e5c6302c41c64f2a2e9034e7c3d659e37eb
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615640"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Use pontos finais privados para a sua conta Purview

Pode utilizar pontos finais privados para as suas contas Purview para permitir que clientes e utilizadores de uma rede virtual (VNet) acedam de forma segura ao catálogo por uma Ligação Privada. O ponto final privado utiliza um endereço IP a partir do espaço de endereço VNet para a sua conta Purview. O tráfego de rede entre os clientes na conta VNet e a conta Purview atravessa o VNet e um link privado na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

## <a name="create-purview-account-with-a-private-endpoint"></a>Criar conta De visão com um Ponto Final Privado

1. Navegue até ao [portal Azure](https://portal.azure.com) e depois para a sua conta Purview.

1. Preencha as informações básicas e desconte o método de conectividade para o ponto final privado no **separador networking.** Configurar os seus pontos finais privados de ingestão, fornecendo detalhes de **Subscrição, Vnet e Subnet** que pretende emparelhar com o seu ponto final privado.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Criar um Ponto Final Privado no portal Azure":::

1. Também pode optar opcionalmente por configurar uma **zona privada de DNS** para cada ponto final privado de ingestão.

1. Clique em Adicionar para adicionar um ponto final privado para a sua conta 'Purview'.

1. Na página de ponto final privado Create, descreva o sub-recurso Purview para **conta,** escolha a sua rede virtual e sub-rede e selecione a Zona Privada de DNS onde o DNS será registado (também pode utilizar os seus servidores DNS ganhos ou criar registos DNS utilizando ficheiros de anfitrião nas suas máquinas virtuais).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Seleções de criação de pontos finais privados":::

1. Selecione **OK**.

1. Selecione **Rever + criar**. Acedeu à página Rever + criar, onde o Azure valida a sua configuração.

1. Quando vir a mensagem A validação passou, selecione **Criar**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Validação aprovada para criação de conta":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Criar um ponto final privado para o portal web De purview

1. Navegue para a conta 'Barriga' que acabou de criar, selecione as ligações de ponto final Private na secção Definições.

1. Clique em +Ponto final privado para criar um novo ponto final privado.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Criar portal private endpoint":::

1. Preencha informações básicas.

1. No separador Recursos, selecione o tipo de recurso para ser **Microsoft.Purview/accounts**.

1. Selecione o Recurso para ser a conta Purview recém-criada e selecione sub-recurso alvo para ser **portal**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Detalhes para o portal private endpoint":::

1. Selecione a rede virtual e a Zona DS Privada no separador Configuração. Navegue para a página de resumo e clique em **Criar** para criar o ponto final privado do portal.

## <a name="enabling-access-to-azure-active-directory"></a>Permitir o acesso ao Azure Ative Directory

> [!NOTE]
> Se o seu gateway VM, VPN ou gateway de peering VNET tiver acesso público à Internet, pode aceder ao portal de Purview e à conta Purview ativada com pontos finais privados, e não tem de seguir o resto das instruções abaixo. Se a sua rede privada tiver regras de grupo de segurança de rede definidas para negar todo o tráfego público de Internet, terá de adicionar algumas regras para permitir o acesso da AAD. Siga as instruções abaixo para o fazer.

As instruções abaixo são para aceder de forma segura a um VM Azure. Devem ser seguidos passos semelhantes se estiver a utilizar VPN ou outros gateways de observação Vnet.

1. Navegue para o seu VM no portal Azure, selecione o separador de rede na secção Definições. Em seguida, selecione as regras da porta de saída e clique em Adicionar a regra da porta de saída.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Adicionar regra de saída":::

2. Na lâmina de regra de saída do Add outbound, selecione *Destination* to be Service Tag, Destination service tag to be **AzureActiveDirectory**, Destination port ranges to be *, Action to be allow, **Priority should be higher than the rule that negou todo o tráfego de Internet**. Crie a regra.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Adicionar detalhes de regras de saída":::

3. Siga os mesmos passos para criar outra regra para permitir a etiqueta de serviço '**AzureResourceManager'** Se precisar de aceder ao portal Azure, também pode adicionar uma regra para a etiqueta de serviço *' AzurePortal.*

4. Ligue-se ao VM, abra o navegador, navegue para a consola do navegador (Ctrl + Shift + J) e mude para o separador de rede para monitorizar os pedidos de rede. Introduza web.purview.azure.com na url box e tente iniciar sessão com as suas credenciais AAD. O login pode provavelmente falhar e no separador de rede na consola pode ver a AAD a tentar aceder a aadcdn.msauth.net mas a ficar bloqueada.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Detalhes de falha de início de sessão":::

5. Neste caso, abra um pedido de comando sobre o VM e ping este url (aadcdn.msauth.net), obtenha o seu IP e, em seguida, adicione uma regra de porta de saída para o IP nas regras de segurança de rede da VM. Desajei o Destino para endereço IP e definir endereços IP de destino para o IP da Aadcdn. Devido ao equilibrador de carga e ao gestor de tráfego, o IP da AAD CDN pode ser dinâmico. Uma vez que você obtém o seu IP, é melhor adicioná-lo no arquivo anfitrião da VM para forçar o navegador a visitar esse IP para obter AAD CDN.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Teste de ping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Regra do CDN da AAD":::

6. Assim que a nova regra for criada, volte ao VM e tente iniciar sessão utilizando novamente as suas credenciais AAD. Se o login for bem sucedido, então o portal Purview está pronto a ser utilizado. Mas em alguns casos, a AAD irá redirecionar para outros domínios para iniciar sessão com base no tipo de conta do cliente. Por exemplo, para uma conta live.com, a AAD irá redirecionar para live.com para iniciar sessão, então esses pedidos seriam bloqueados novamente. Para as contas dos colaboradores da Microsoft, a AAD acederá msft.sts.microsoft.com para obter informações de login. Consulte os pedidos de rede no separador de rede do navegador para ver quais os pedidos do domínio que estão a ser bloqueados, refaça o passo anterior para obter o seu IP e adicione regras de porta de saída no grupo de segurança de rede para permitir pedidos para esse IP (se possível, adicione o url e o IP ao ficheiro anfitrião da VM para corrigir a resolução DNS). Se souber as gamas IP exatas do domínio do início de sessão, também pode adicioná-las diretamente às regras de networking.

7. Agora o login para a AAD deve ser bem sucedido. O Portal de Visão será carregado com sucesso, mas a listagem de todas as contas do Purview não funcionará, uma vez que só pode aceder a uma conta de 'Purview' específica. Insira *web.purview.azure.com/resource/{PurviewAccountName}* para visitar diretamente a conta Desícono para a qual criou com sucesso um ponto final privado.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Ativar o ponto final privado nas contas de Purview existentes

Existem 2 maneiras de adicionar pontos finais privados de Purview após a criação da sua conta Purview:

- Utilizando o portal Azure (conta Purview)
- Usando o centro de ligação privado

### <a name="using-the-azure-portal-purview-account"></a>Utilizando o portal Azure (conta Purview)

1. Navegue para a conta 'Barriga' a partir do portal Azure, selecione as ligações private de ponto final na secção de **rede** de **Definições**.

:::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Criar portal private endpoint":::

1. Clique em +Ponto final privado para criar um novo ponto final privado.

1. Preencha informações básicas.

1. No separador Recursos, selecione o tipo de recurso para ser **Microsoft.Purview/accounts**.

1. Selecione o Recurso para ser a conta 'Provedor de Contas' e selecione sub-recurso-alvo para ser **conta.**

1. Selecione a **rede virtual** e **a Zona DS Privada** no separador Configuração. Navegue para a página de resumo e clique em **Criar** para criar o ponto final privado do portal.

> [!NOTE]
> Terá de seguir os mesmos passos acima para o sub-recurso-alvo selecionado como **Portal** também.

### <a name="using-the-private-link-center"></a>Usando o centro de ligação privado

1. Navegue até ao [portal Azure.](https://portal.azure.com)

2. Na barra de pesquisa no topo da página, procure por 'link privado' e navegue para a lâmina de link privada clicando na primeira opção.

3. Clique em '+ Adicionar' e preencha os detalhes básicos.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Criar PE a partir do centro de ligação privada":::

4. Selecione o Recurso para ser a conta Purview já criada e selecione sub-recurso alvo para ser **conta.**

5. Selecione a rede virtual e a Zona DS Privada no separador Configuração. Navegue para a página de resumo e clique em **Criar** para criar o ponto final privado da conta.

> [!NOTE]
> Terá de seguir os mesmos passos acima para o sub-recurso-alvo selecionado como **Portal** também.

## <a name="next-steps"></a>Passos seguintes

- [Navegue no Catálogo de Dados Azure Purview](how-to-browse-catalog.md)

- [Pesse o Catálogo de Dados da Azure Purview](how-to-search-catalog.md)
