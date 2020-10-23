---
title: Permitir a replicação de máquinas no local com pontos finais privados
description: Este artigo descreve como configurar a replicação para máquinas no local utilizando pontos finais privados na Recuperação do Local.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: f23efa8d0439422fef685480ed270dce6e78a204
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366858"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Replicar máquinas no local utilizando pontos finais privados

A Azure Site Recovery permite-lhe utilizar pontos finais privados [do Azure Private Link](../private-link/private-endpoint-overview.md) para replicar as suas máquinas no local para uma rede virtual em Azure. O acesso privado a um cofre de recuperação é suportado em todas as regiões do Governo comercial & Azure.

Este artigo descreve como completar os seguintes passos:

- Crie um cofre Azure Backup Recovery Services para proteger as suas máquinas.
- Habilitado uma identidade gerida para o cofre. Conceder as permissões necessárias para aceder às contas de armazenamento para permitir a replicação do tráfego de locais para locais alvo de Azure. É necessário acesso de identidade gerido para armazenamento para acesso de Ligação Privada ao cofre.

- Faça alterações dns que são necessárias para pontos finais privados.
- Crie e aprove pontos finais privados para um cofre dentro de uma rede virtual.
- Criar pontos finais privados para as contas de armazenamento. Pode continuar a permitir o acesso público ou firewall para armazenamento, se necessário. A criação de um ponto final privado para aceder ao armazenamento não é necessária para a Recuperação do Site Azure.
  
O diagrama seguinte mostra o fluxo de trabalho de replicação para recuperação de desastres híbridos com pontos finais privados. Não pode criar pontos finais privados na sua rede de instalações. Para utilizar links privados, é necessário criar uma rede virtual Azure (chamada *rede de bypass* neste artigo), estabelecer conectividade privada entre as instalações e a rede de bypass e, em seguida, criar pontos finais privados na rede de bypass. Pode escolher qualquer forma de conectividade privada.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

## <a name="prerequisites-and-caveats"></a>Pré-requisitos e ressalvas

- Os links privados são suportados na Recuperação do Local 9.35 e posterior.
- Pode criar pontos finais privados apenas para novos cofres dos Serviços de Recuperação que não tenham quaisquer itens registados. Por isso, tens de criar pontos finais privados antes que sejam adicionados quaisquer objetos ao cofre. Consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/) para obter informações sobre preços.
- Quando se cria um ponto final privado para um cofre, o cofre está fechado. Só pode ser acedido a partir de redes que tenham pontos finais privados.
- O Azure Ative Directory não suporta atualmente pontos finais privados. Por isso, é necessário permitir o acesso de saída da rede virtual Azure garantida a IPs e nomes de domínio totalmente qualificados que são necessários para que o Azure Ative Directory funcione numa região.
  Conforme aplicável, também pode utilizar tags de grupo de segurança de rede "Azure Ative Directory" e Azure Firewall para permitir o acesso ao Azure Ative Directory.
- São necessários cinco endereços IP na rede de bypass onde cria o seu ponto final privado. Quando cria um ponto final privado para o cofre, a Recuperação do Site cria cinco links privados para o acesso aos seus microserviços.
- Um endereço IP adicional é necessário na rede de bypass para conectividade de ponto final privado para uma conta de armazenamento de cache. Pode utilizar qualquer método de conectividade entre as instalações e o ponto final da sua conta de armazenamento. Por exemplo, pode utilizar a internet ou a Azure [ExpressRoute](../expressroute/index.yml). Estabelecer uma ligação privada é opcional. Pode criar pontos finais privados para armazenamento apenas nas contas V2 de Finalidade Geral. Consulte o preço do [Azure Page Blobs](https://azure.microsoft.com/pricing/details/storage/page-blobs/) para obter informações sobre preços para transferência de dados em contas V2 de Finalidade Geral.

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Criar e utilizar pontos finais privados para a recuperação do site

 As secções seguintes descrevem os passos que precisa de tomar para criar e utilizar pontos finais privados para a recuperação do site nas suas redes virtuais.

> [!NOTE]
> Recomendamos que siga estes passos na ordem mostrada. Se não o fizeres, podes não ser capaz de usar pontos finais privados no cofre, e talvez precises de reiniciar o processo com um cofre novo.

### <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação contém informações de replicação das máquinas. É usado para desencadear operações de recuperação do local. Para obter informações sobre como criar um cofre dos Serviços de Recuperação na região de Azure, onde pretende falhar se houver um desastre, consulte [o cofre criar um cofre dos Serviços de Recuperação.](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)

### <a name="enable-the-managed-identity-for-the-vault"></a>Ativar a identidade gerida para o cofre

Uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) permite ao cofre aceder às suas contas de armazenamento. A Recuperação do Site poderá ter de aceder às contas de armazenamento de cache/log alvo, dependendo dos seus requisitos. É necessário um acesso de identidade gerido quando estiver a utilizar o serviço Private Link para o cofre.

1. Vai para o cofre dos Serviços de Recuperação. Selecione **identidade** em **Definições**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

1. Alterar o **Estado** para **On** e selecionar **Guardar**.

   Um ID de objeto é gerado. O cofre está registado no Azure Ative Directory.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Criar pontos finais privados para o cofre dos Serviços de Recuperação

Para proteger as máquinas na rede de fontes no local, vai precisar de um ponto final privado para o cofre na rede de bypass. Crie o ponto final privado utilizando o Private Link Center no portal Azure ou utilizando [a Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Na caixa de pesquisa do portal Azure, procure por "link privado". Selecione **Private Link** para ir ao Private Link Center:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

1. No painel esquerdo, selecione **Pontos finais privados**. Na página **private endpoints,** selecione **Adicionar** para começar a criar um ponto final privado para o seu cofre:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

1. Na página **'Criar' privado,** especifique os detalhes para criar a sua ligação de ponto final privado.

   1. **Básicos.** Forneça os detalhes básicos para os seus pontos finais privados. Utilize a região que utilizou para a rede de bypass:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

   1. **Recurso.** Neste separador, tem de especificar o recurso plataforma-as-a-service para o qual pretende criar a sua ligação. Sob **o tipo de recurso** para a subscrição selecionada, selecione **Microsoft.RecoveryServices/vaults**. Escolha o nome do cofre dos Serviços de Recuperação em **Recurso.** Selecione **Azure Site Recovery** como **sub-recurso target**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

   1. **Configuração**. Neste separador, especifique a rede de bypass e a sub-rede onde pretende que o ponto final privado seja criado. 

      Permitir a integração com uma zona privada de DNS selecionando **Sim**.
      Escolha uma zona DNS existente ou crie uma nova. Selecionar **Sim** liga automaticamente a zona à rede de bypass. Esta ação adiciona ainda os registos DNS que são necessários para a resolução de DNS de novos IPs e nomes de domínio totalmente qualificados criados para o ponto final privado.

      Certifique-se de que opta por criar uma nova zona DNS para cada novo ponto final privado que se ligue ao mesmo cofre. Se escolher uma zona de DNS privada existente, os registos CNAME anteriores são substituídos. Consulte [a orientação do ponto final privado](../private-link/private-endpoint-overview.md#private-endpoint-properties) antes de continuar.

      Se o seu ambiente tem um hub e um modelo de fala, você precisa apenas de um ponto final privado e apenas uma zona privada de DNS para toda a configuração. Isto porque todas as suas redes virtuais já têm o seu olhar ativado entre eles. Para mais informações, consulte [a integração do DNS do ponto final privado.](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)

      Para criar manualmente a zona privada de DNS, siga os passos em [criar zonas privadas de DNS e adicione registos DNS manualmente](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

   1. **Etiquetas.** Opcionalmente, pode adicionar tags para o seu ponto final privado.

   1. **Criar \+ revisão**. Quando a validação estiver concluída, **selecione Criar** para criar o ponto final privado.

Quando o ponto final privado é criado, cinco nomes de domínio totalmente qualificados (FQDNs) são adicionados ao ponto final privado. Estas ligações permitem que as máquinas da rede no local acedam, através da rede de bypass, a todos os microserviços de Recuperação de Sítio necessários no contexto do cofre. Pode utilizar o mesmo ponto final privado para a proteção de qualquer máquina Azure na rede de bypass e em todas as redes com os olhos.

Os cinco nomes de domínio são formatados neste padrão:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Aprovar pontos finais privados para recuperação do local

Se criar o ponto final privado e for também o proprietário do cofre dos Serviços de Recuperação, o ponto final privado que criou anteriormente é automaticamente aprovado dentro de poucos minutos. Caso contrário, o proprietário do cofre deve aprovar o ponto final privado antes de poder usá-lo. Para aprovar ou rejeitar uma ligação de ponto final privado solicitada, aceda a **ligações de ponto final privados** em **Definições** na página do cofre de recuperação.

Pode recorrer ao recurso de ponto final privado para rever o estado da ligação antes de continuar:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>(Opcional) Criar pontos finais privados para a conta de armazenamento de cache

Pode utilizar um ponto final privado para o Azure Storage. Criar pontos finais privados para acesso ao armazenamento é opcional para a replicação do Azure Site Recovery. Se criar um ponto final privado para armazenamento, precisa de um ponto final privado para a conta de armazenamento cache/log na sua rede virtual de bypass.

> [!NOTE]
> Os pontos finais privados para armazenamento só podem ser criados nas contas de armazenamento V2 de Finalidade Geral. Para obter informações sobre preços, consulte os preços da [Azure Page Blobs](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Siga as [orientações para a criação de armazenamento privado](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) para criar uma conta de armazenamento com um ponto final privado. Certifique-se de selecionar **Sim** em **Integração com zona privada de DNS**. Selecione uma zona DNS existente ou crie uma nova.

### <a name="grant-required-permissions-to-the-vault"></a>Grant exigiu permissões para o cofre.

Dependendo da sua configuração, poderá necessitar de uma ou mais contas de armazenamento na região alvo de Azure. Em seguida, conceda as permissões de identidade geridas para todas as contas de armazenamento cache/log exigidas pela Recuperação do Site. Neste caso, deve criar antecipadamente as contas de armazenamento necessárias.

Antes de permitir a replicação de máquinas virtuais, a identidade gerida do cofre deve ter as seguintes permissões de função, dependendo do tipo de conta de armazenamento.

- Contas de armazenamento baseadas em gestores de recursos (tipo padrão):
  - [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)
  - [Contribuinte de Dados do Armazenamento de Blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Contas de armazenamento baseadas em gestores de recursos (tipo Premium):
  - [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietário dos Dados do Armazenamento de Blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Contas clássicas de armazenamento:
  - [Colaborador clássico da conta de armazenamento](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Papel clássico do serviço do operador chave de armazenamento de armazenamento](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Estes passos descrevem como adicionar uma atribuição de função à sua conta de armazenamento:

1. Aceda à conta de armazenamento. Selecione **o controlo de acesso (IAM)** no painel esquerdo.

1. Na secção Adicionar uma tarefa **de função,** selecione **Adicionar**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

1. Na página **De atribuição de funções,** na lista **Role,** selecione o papel da lista no início desta secção. Introduza o nome do cofre e, em seguida, **selecione Save**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

Depois de adicionar estas permissões, tem de permitir o acesso aos serviços fidedignos da Microsoft. Vá a **Firewalls e redes virtuais** e selecione **Permita que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento** em **Exceções.**

### <a name="protect-your-virtual-machines"></a>Proteja as suas máquinas virtuais

Depois de terminar as tarefas anteriores, continue com a instalação da sua infraestrutura no local. Continue completando uma das seguintes tarefas: 

- [Implementar um servidor de configuração para VMware e máquinas físicas](./vmware-azure-deploy-configuration-server.md)
- [Configurar o ambiente Hiper-V para replicação](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Depois de concluída a configuração, ative a replicação para as suas máquinas de origem. Não instale a infraestrutura até que os pontos finais privados para o cofre sejam criados na rede de bypass.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Crie zonas privadas de DNS e adicione registos DNS manualmente

Se não selecionou a opção de integração com uma zona privada de DNS quando criou o ponto final privado para o cofre, siga os passos nesta secção.

Crie uma zona privada de DNS para permitir que o fornecedor de Recuperação de Sítio (para máquinas Hiper-V) ou o Servidor de Processo (para VMware/máquinas físicas) resolvam fQDNs privados em IPs privados.

1. Crie uma zona privada de DNS.

   1. Procure por "zona privada de DNS" na caixa de pesquisa de **todos os serviços** e, em seguida, selecione **a zona privada de DNS** nos resultados:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

   1. Na página **zonas privadas de DNS,** selecione o botão **Adicionar** para começar a criar uma nova zona.

   1. Na página privada da **zona DO DNS,** insira os detalhes necessários. Introduza **privatelink.siterecovery.windowsazure.com** para o nome da zona privada do DNS. Pode escolher qualquer grupo de recursos e qualquer subscrição.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

   1. Continue a fazer o separador **'Revisão' \+ ** para rever e criar a zona DNS.

1. Ligue a zona privada do DNS à sua rede virtual.

   Agora precisa de ligar a zona privada de DNS que criou ao bypass.

   1. Vá para a zona privada de DNS que criou no passo anterior e, em seguida, vá para **links de rede Virtual** no painel esquerdo. Selecione **Adicionar**.

   1. Insira os detalhes necessários. Nas listas **de subscrição** e **rede virtual,** selecione detalhes que correspondam à rede de bypass. Deixe os valores predefinidos nos outros campos.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

1. Adicione registos DNS.

   Agora que criou a zona privada de DNS e o ponto final privado, precisa de adicionar registos DNS à sua zona de DNS.

   > [!NOTE]
   > Se estiver a utilizar uma zona de DNS privada personalizada, certifique-se de fazer entradas semelhantes, conforme descrito no passo seguinte.

   Neste passo, você precisa fazer entradas para cada FQDN no seu ponto final privado na sua zona privada de DNS.

   1. Vá para a sua zona privada de DNS e, em seguida, vá para a secção **geral** no painel esquerdo. Selecione **Record definido** para começar a adicionar registos.

   1. Na página **de conjunto de registos Add,** adicione uma entrada para cada nome de domínio totalmente qualificado e IP privado como um registo tipo **A.** Pode obter uma lista dos nomes de domínio e IPs totalmente qualificados na página **Private Endpoint** em **Visão Geral**. Como pode ver na imagem seguinte, o primeiro nome de domínio totalmente qualificado do ponto final privado é adicionado ao recorde estabelecido na zona privada de DNS.

      Estes nomes de domínio totalmente qualificados correspondem a este padrão: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Diagrama que mostra a arquitetura para recuperação do local de Azure e pontos finais privados.":::

## <a name="next-steps"></a>Passos seguintes

Agora que ativou pontos finais privados para a sua replicação de máquinas virtuais, consulte estes outros artigos para obter informações adicionais e relacionadas:

- [Implementar um servidor de configuração no local](./vmware-azure-deploy-configuration-server.md)
- [Configurar a recuperação após desastre de VMs Hyper-V no local para o Azure](./hyper-v-azure-tutorial.md)