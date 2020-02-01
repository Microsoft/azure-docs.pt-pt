---
title: Integrar com o Serviço de Ligação Privada Azure
description: Saiba como integrar o Cofre chave Azure com o Serviço de Ligação Privada Azure
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e058e643f4c37336f09b43c41cd09aa361a23d15
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907076"
---
# <a name="integrate-key-vault-with-azure-private-link-preview"></a>Integrar o cofre chave com link privado Azure (pré-visualização)

O Azure Private Link Service permite-lhe aceder aos Serviços Azure (por exemplo, Azure Key Vault, Azure Storage e Azure Cosmos DB) e o Azure acolheu serviços de clientes/parceiros sobre um Ponto Final Privado na sua rede virtual.

Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado por Azure Private Link. O ponto final privado utiliza um endereço IP privado do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN nem endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a uma instância de um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acesso.

Para mais informações, consulte [O que é azure private link (Pré-visualização)?](../private-link/private-link-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

Para integrar um cofre chave com Ligação Privada Azure (Pré-visualização), necessitará do seguinte:

- Um cofre chave.
- Uma rede virtual Azure.
- Uma sub-rede na rede virtual.
- Permissões do proprietário ou dos contribuintes para o cofre chave e para a rede virtual.

O seu ponto final privado e a sua rede virtual devem estar na mesma região. Quando se leciona uma região para o ponto final privado utilizando o portal, filtrará automaticamente apenas redes virtuais que se encontram naquela região. O seu cofre pode estar numa região diferente.

O seu ponto final privado utiliza um endereço IP privado na sua rede virtual.

## <a name="establish-a-private-link-connection-to-key-vault"></a>Estabelecer uma ligação de ligação privada ao cofre chave

Primeiro, criar uma rede virtual seguindo os passos em [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md)

Pode então criar um novo cofre de chaves, ou estabelecer uma ligação de ligação privada a um cofre de chaves existente.

### <a name="create-a-new-key-vault-and-establish-a-private-link-connection"></a>Crie um novo cofre de chaves e estabeleça uma ligação de ligação privada

Você pode criar um novo cofre chave seguindo os passos em [set e recuperar um segredo do Cofre chave Azure usando o portal Azure](quick-create-portal.md)

Depois de configurar o básico do cofre chave, selecione o separador de rede e siga estes passos:

1. Selecione o botão de rádio Private Endpoint (pré-visualização) no separador Networking.
1. Clique no botão "+ Adicionar" para adicionar um ponto final privado.

    ![Imagem](./media/private-link-service-1.png)
 
1. No campo "Localização" da Lâmina de Endpoint Create Private, selecione a região em que a sua rede virtual está localizada. 
1. No campo "Nome", crie um nome descritivo que lhe permitirá identificar este ponto final privado. 
1. Selecione a rede virtual e a subnet deseja que este ponto final privado seja criado a partir do menu dropdown. 
1. Deixe inalterada a opção "integrar com a zona privada DNS".  
1. Selecione "Ok".

    ![Imagem](./media/private-link-service-2.png)
 
Agora poderá ver o ponto final privado configurado. Tem agora a opção de apagar e editar este ponto final privado. Selecione o botão "Rever + Criar" e criar o cofre da chave. Levará 5 a 10 minutos para a implantação ser concluída. 

### <a name="establish-a-private-link-connection-to-an-existing-key-vault"></a>Estabeleça uma ligação de ligação privada a um cofre chave existente

Se já tiver um cofre chave, pode criar uma ligação de ligação privada seguindo estes passos:

1. Inicie sessão no Portal do Azure. 
1. Na barra de pesquisa, escreva em "cofres chave"
1. Selecione o cofre chave da lista à qual pretende adicionar um ponto final privado.
1. Selecione o separador "Networking" em Definições
1. Selecione o separador de ligações de ponto final privado (pré-visualização) na parte superior da página
1. Selecione o botão "+ Ponto Final Privado" na parte superior da página.

    ![Imagem](./media/private-link-service-3.png) ![Imagem](./media/private-link-service-4.png)

Pode optar por criar um ponto final privado para qualquer recurso Azure na utilização desta lâmina. Pode utilizar os menus de dropdown para selecionar um tipo de recurso e selecionar um recurso no seu diretório, ou pode ligar-se a qualquer recurso Azure utilizando um ID de recurso. Deixe inalterada a opção "integrar com a zona privada DNS".  

![Imagem](./media/private-link-service-3.png)
![Imagem](./media/private-link-service-4.png)

## <a name="manage-private-link-connection"></a>Gerir a ligação de ligação privada

Quando se cria um ponto final privado, a ligação tem de ser aprovada. Se o recurso para o qual está a criar um ponto final privado estiver no seu diretório, poderá aprovar o pedido de ligação desde que tenha permissões suficientes; se estiver a ligar-se a um recurso Azure noutro diretório, deve aguardar que o proprietário desse recurso aprove o seu pedido de ligação.

Existem quatro estados de provisionamento:

| Serviço fornecer ação | Estado de ponto final privado do consumidor de serviço | Descrição |
|--|--|--|
| Nenhuma | Pendente | A ligação é criada manualmente e está pendente de aprovação do proprietário do recurso Private Link. |
| Aprovar | Aprovado | A conexão foi aprovada automaticamente ou manualmente e está pronta para ser usada. |
| Rejeitar | Rejeitado | A conexão foi rejeitada pelo proprietário do recurso de link privado. |
| Remover | Desligado | A ligação foi removida pelo proprietário do recurso de ligação privada, o ponto final privado torna-se informativo e deve ser eliminado para limpeza. |
 
###  <a name="how-to-manage-a-private-endpoint-connection-to-key-vault"></a>Como gerir uma ligação de ponto final privado ao cofre chave

1. Inicie sessão no portal do Azure.
1. Na barra de pesquisa, escreva em "cofres chave"
1. Selecione o cofre chave que pretende gerir.
1. Selecione o separador "Networking".
1. Se houver alguma ligação pendente, verá uma ligação listada com "Pendente" no estado de provisionamento. 
1. Selecione o ponto final privado que deseja aprovar
1. Selecione o botão de aprovação.
1. Se existirem ligações de ponto final privados que deseja rejeitar, seja um pedido pendente ou uma ligação existente, selecione a ligação e clique no botão "Rejeitar".

    ![Imagem](./media/private-link-service-7.png)

## <a name="validate-that-the-private-link-connection-works"></a>Validar que a ligação de ligação privada funciona

Deve validar que os recursos dentro da mesma sub-rede do recurso de ponto final privado estão ligados ao seu cofre chave sobre um endereço IP privado, e que eles têm a correta integração privada da zona DNS.

Primeiro, crie uma máquina virtual seguindo os passos em [Criar uma máquina virtual Windows no portal Azure](../virtual-machines/windows/quick-create-portal.md)

No separador "Networking":

1. Especificar rede virtual e Subnet. Pode criar uma nova rede virtual ou selecionar uma existente. Se selecionar um existente, certifique-se de que a região corresponde.
1. Especifique um recurso IP público.
1. No "grupo de segurança da rede NIC", selecione "Nenhum".
1. No "Equilíbrio de carga", selecione "Não".

Abra a linha de comando e execute o seguinte comando:

```console
nslookup <your-key-vault-name>.vault.azure.net
```

Se executar o comando de procura ns para resolver o endereço IP de um cofre chave sobre um ponto final público, você verá um resultado que se parece com este:

```console
c:\ >nslookup <your-key-vault-name>.vault.azure.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
```

Se executar o comando de procura nns para resolver o endereço IP de um cofre chave sobre um ponto final privado, você verá um resultado que se parece com este:

```console
c:\ >nslookup your_vault_name.vault.azure.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-key-vault-name>.vault.azure.net
          <your-key-vault-name>.privatelink.vaultcore.azure.net
```

## <a name="limitations-and-design-considerations"></a>Limitações e Considerações de Design

**Preços**: Para obter informações sobre preços, consulte o preço do [Link Privado do Azure (pré-visualização).](https://azure.microsoft.com/pricing/details/private-link/)

**Limitações**: Private Endpoint for Azure Key Vault está em pré-visualização pública. Esta funcionalidade está disponível em todas as regiões públicas do Azure.

**Número máximo de pontos finais privados por cofre chave:** 64.

**Número máximo de cofres-chave com pontos finais privados por subscrição:** 64.

Para mais informações, consulte [o serviço Azure Private Link: Limitações](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Próximos Passos

- Saiba mais sobre [o Link Privado Azure (Pré-visualização)](../private-link/private-link-service-overview.md)
- Saiba mais sobre o [Azure Key Vault](key-vault-overview.md)
