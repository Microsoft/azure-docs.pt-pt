---
title: Permitir a replicação de pontos finais privados na Recuperação do Local de Azure
description: Este artigo descreve como configurar a replicação de VMs com pontos finais privados de uma região de Azure para outra, utilizando a Recuperação do Local.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629832"
---
# <a name="replicate-machines-with-private-endpoints"></a>Máquinas de replicar com pontos finais privados

A Azure Site Recovery permite-lhe utilizar pontos finais privados [do Azure Private Link](../private-link/private-endpoint-overview.md) para replicar as suas máquinas a partir de uma rede virtual isolada. O acesso privado a um cofre de recuperação é suportado em todas as regiões do Governo comercial & Azure.

Este artigo fornece instruções para que execute os seguintes passos:

- Crie um cofre Azure Backup Recovery Services para proteger as suas máquinas.
- Capacitar uma identidade gerida para o cofre e conceder as permissões necessárias para aceder às contas de armazenamento do cliente para replicar o tráfego de origem para locais-alvo. O acesso de identidade gerido para armazenamento é necessário quando estiver a configurar o acesso private link ao cofre.
- Faça alterações dns necessárias para pontos finais privados
- Criar e aprovar pontos finais privados para um cofre dentro de uma rede virtual
- Criar pontos finais privados para as contas de armazenamento. Pode continuar a permitir o acesso público ou firewall para armazenamento, se necessário. A criação de um ponto final privado para aceder ao armazenamento não é obrigatória para a Recuperação do Sítio Azure.
  
Abaixo está uma arquitetura de referência sobre como o fluxo de trabalho de replicação muda com pontos finais privados.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Arquitetura de referência para Recuperação de Sítio com pontos finais privados.":::

## <a name="prerequisites-and-caveats"></a>Pré-requisitos e ressalvas

- Os pontos finais privados só podem ser criados para novos cofres dos Serviços de Recuperação que não tenham quaisquer itens registados no cofre. Como tal, os pontos finais privados **devem ser criados antes de serem adicionados quaisquer itens ao cofre**. Reveja a estrutura de preços para [os pontos finais privados.](https://azure.microsoft.com/pricing/details/private-link/)
- Quando um ponto final privado é criado para um cofre, o cofre é bloqueado e **não é acessível a partir de redes que não as redes que têm pontos finais privados.**
- A Azure Ative Directory não suporta pontos finais privados. Como tal, os IPs e os nomes de domínio totalmente qualificados necessários para que o Azure Ative Directory funcione numa região devem ser autorizados a aceder à saída da rede segura. Também pode utilizar tags de grupo de segurança de rede "Azure Ative Directory" e Azure Firewall para permitir o acesso ao Azure Ative Directory, conforme aplicável.
- **São necessários pelo menos sete endereços IP** nas sub-redes das suas máquinas de origem e das suas máquinas de recuperação. Quando cria um ponto final privado para o cofre, a Recuperação do Site cria cinco links privados para o acesso aos seus microserviços. Além disso, quando ativa a replicação, adiciona dois links privados adicionais para o emparelhamento da região de origem e alvo.
- **É necessário um endereço IP adicional** nas sub-redes de origem e recuperação. Este endereço IP só é necessário quando necessitar de utilizar pontos finais privados que se liguem às contas de armazenamento de cache.
  Os pontos finais privados para armazenamento só podem ser criados no tipo V2 de Finalidade Geral. Reveja a estrutura de preços para [a transferência de dados em GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Criar e utilizar pontos finais privados para recuperação de locais

 Esta secção fala sobre os passos envolvidos na criação e utilização de pontos finais privados para a Recuperação do Site Azure dentro das suas redes virtuais.

> [!NOTE]
> É altamente recomendável que siga estes passos na mesma sequência que fornecido. Se não o fizer, o cofre pode ser tornado incapaz de utilizar pontos finais privados e exigir que reinicie o processo com um novo cofre.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre de serviços de recuperação é uma entidade que contém a informação de replicação de máquinas e é usada para desencadear operações de Recuperação do Local. Para obter mais informações, consulte [criar um cofre dos Serviços de Recuperação.](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)

## <a name="enable-the-managed-identity-for-the-vault"></a>Habilitar a identidade gerida para o cofre.

Uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) permite que o cofre tenha acesso às contas de armazenamento do cliente. A Recuperação do Site precisa de aceder ao armazenamento de fontes, ao armazenamento do alvo e às contas de armazenamento de cache/log, dependendo do requisito do cenário.
O acesso à identidade gerido é essencial quando se utiliza o serviço de links privados para o cofre.

1. Vai para o cofre dos Serviços de Recuperação. Selecione **identidade** em _Definições_.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Mostra o portal Azure e a página Serviços de Recuperação.":::

1. Alterar o **Estado** para _On_ e selecionar **Guardar**.

1. Um **ID de objeto** é gerado indicando que o cofre está agora registado no Azure Ative Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Criar pontos finais privados para o cofre dos Serviços de Recuperação

Para ativar tanto o failover como o failback para as máquinas virtuais Azure, vai precisar de dois pontos finais privados para o cofre. Um ponto final privado para a proteção das máquinas na rede de origem e outro para a reprotecção de máquinas falhadas na rede de recuperação.

Certifique-se de que cria uma rede virtual de recuperação na sua região alvo também durante este processo de configuração.

Crie o primeiro ponto final privado para o seu cofre dentro da sua rede virtual de origem utilizando o Private Link Center no portal ou através do [Azure PowerShell](../private-link/create-private-endpoint-powershell.md). Crie o segundo ponto final privado para o cofre dentro da sua rede de recuperação. Seguem-se os passos para criar o ponto final privado na rede de fontes. Repita a mesma orientação para criar o segundo ponto final privado.

1. Na barra de pesquisa do portal Azure, procure e selecione "Private Link". Esta ação leva-o ao Centro de Ligação Privada.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Mostra a pesquisa no portal Azure para o Private Link Center.":::

1. Na barra de navegação à esquerda, selecione **Private Endpoints**. Uma vez na página Private Endpoints, **\+ selecione Adicionar** para começar a criar um ponto final privado para o seu cofre.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Mostra a criação de um ponto final privado no Private Link Center.":::

1. Uma vez na experiência "Create Private Endpoint", é-lhe exigido que especifique detalhes para criar a sua ligação ao ponto final privado.

   1. **Fundamentos**: Preencha os detalhes básicos para os seus pontos finais privados. A região deve ser a mesma que as máquinas de origem.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Mostra o separador Basic, detalhes do projeto, subscrição e outros campos relacionados para a criação de um ponto final privado no portal Azure.":::

   1. **Recurso**: Este separador requer que mencione o recurso plataforma-as-a-service para o qual pretende criar a sua ligação. Selecione _Microsoft.RecoveryServices/vaults_ do **tipo de recurso** para a subscrição selecionada. Em seguida, escolha o nome do seu cofre de Serviços de Recuperação para **recursos** e desloja _a recuperação do local de Azure_ como **sub-recurso alvo**.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Mostra o separador recursos, tipo de recurso, recurso e campos de sub-recursos alvo para ligação a um ponto final privado no portal Azure.":::

   1. **Configuração**: Na configuração, especifique a rede virtual e a sub-rede onde pretende que o ponto final privado seja criado. Esta rede virtual é a rede onde a máquina virtual está presente. Permitir a integração com a zona privada de DNS selecionando **Sim**. Escolha uma zona DNS já criada ou crie uma nova. Selecionar **Sim** liga automaticamente a zona à rede virtual de origem e adiciona os registos DNS que são necessários para a resolução de DNS de novos IPs e nomes de domínio totalmente qualificados criados para o ponto final privado.

      Certifique-se de que opta por criar uma nova zona DNS para cada novo ponto final privado que se ligue ao mesmo cofre. Se escolher uma zona de DNS privada existente, os registos CNAME anteriores são substituídos. Consulte a [orientação do ponto final privado](../private-link/private-endpoint-overview.md#private-endpoint-properties) antes de continuar.

      Se o seu ambiente tem um hub e um modelo falado, precisa apenas de um ponto final privado e apenas uma zona privada de DNS para toda a configuração, uma vez que todas as suas redes virtuais já têm o seu espremo ativado entre eles. Para mais informações, consulte [a integração do DNS do ponto final privado.](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)

      Para criar manualmente a zona privada de DNS, siga os passos em [criar zonas privadas de DNS e adicione registos DNS manualmente](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Mostra o separador Configuração com campos de integração em rede e DNS para configuração de um ponto final privado no portal Azure.":::

   1. **Etiquetas**: Opcionalmente, pode adicionar etiquetas para o seu ponto final privado.

   1. **Revisão \+ criar**: Quando a validação estiver concluída, selecione **Criar** para criar o ponto final privado.

Uma vez criado o ponto final privado, cinco nomes de domínio totalmente qualificados são adicionados ao ponto final privado. Estas ligações permitem que as máquinas da rede virtual tenham acesso a todos os microserviços de Recuperação de Sítio necessários no contexto do cofre. Mais tarde, quando ativar a replicação, dois nomes de domínio adicionais totalmente qualificados são adicionados ao mesmo ponto final privado.

Os cinco nomes de domínio são formatados com o seguinte padrão:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Aprovar pontos finais privados para recuperação do local

Se o utilizador que cria o ponto final privado for também o proprietário do cofre dos Serviços de Recuperação, o ponto final privado acima criado é aprovado automaticamente dentro de poucos minutos. Caso contrário, o proprietário do cofre deve aprovar o ponto final privado antes de usá-lo. Para aprovar ou rejeitar uma ligação de ponto final privado solicitada, aceda a **ligações de ponto final privado em** "Definições" na página do cofre de recuperação.

Pode recorrer ao recurso de ponto final privado para rever o estado da ligação antes de prosseguir.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Mostra a página de ligações de ponto final privado do cofre e a lista de ligações no portal Azure.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>(Opcional) Criar pontos finais privados para a conta de armazenamento de cache

Pode ser utilizado um ponto final privado para o Azure Storage. Criar pontos finais privados para acesso ao armazenamento é _opcional_ para a replicação do Azure Site Recovery. Ao criar um ponto final privado para armazenamento, aplicam-se os seguintes requisitos:

- Precisa de um ponto final privado para a conta de armazenamento cache/log na sua rede virtual de origem.
- Precisa de um segundo ponto final privado no momento da reproteção das máquinas falhadas na rede de recuperação. Este ponto final privado destina-se à nova conta de armazenamento criada na região-alvo.

> [!NOTE]
> Se os pontos finais privados não estiverem ativados na conta de armazenamento, a proteção continuaria a ser bem sucedida. No entanto, o tráfego de replicação iria transitar para os pontos finais públicos de recuperação do local de Azure. Para garantir fluxos de tráfego de replicação através de ligações privadas, a conta de armazenamento deve ser ativada com pontos finais privados.

> [!NOTE]
> O ponto final privado para armazenamento só pode ser criado numa conta de armazenamento **V2 para fins** gerais. Para obter informações sobre preços, consulte [os preços normais](https://azure.microsoft.com/pricing/details/storage/page-blobs/)da página blob .

Siga as orientações para a [criação de armazenamento privado](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) para criar uma conta de armazenamento com ponto final privado. Certifique-se de selecionar **Sim** à integração com a zona privada de DNS. Selecione uma zona DNS já criada ou crie uma nova.

## <a name="grant-required-permissions-to-the-vault"></a>Grant exigiu permissões para o cofre.

Se as suas máquinas virtuais estiverem a utilizar discos geridos, tem de conceder as permissões de identidade geridas apenas às contas de armazenamento de cache. Caso as máquinas virtuais utilizem discos não geridos, é necessário conceder as permissões de identidade geridas para as contas de origem, cache e armazenamento de alvos. Neste caso, você precisa criar a conta de armazenamento alvo com antecedência.

Antes de permitir a replicação de máquinas virtuais, a identidade gerida do cofre deve ter as seguintes permissões de função dependendo do tipo de conta de armazenamento:

- Contas de armazenamento baseadas em gestores de recursos (Tipo Padrão):
  - [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)
  - [Contribuinte de Dados do Armazenamento de Blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Contas de armazenamento baseadas em gestores de recursos (Tipo Premium):
  - [Contribuinte](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietário dos Dados do Armazenamento de Blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Contas clássicas de armazenamento:
  - [Colaborador clássico da conta de armazenamento](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Papel clássico do serviço do operador chave de armazenamento de armazenamento](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Os seguintes passos descrevem como adicionar uma atribuição de papel às suas contas de armazenamento, uma de cada vez:

1. Vá à conta de armazenamento e navegue para **o controlo de acesso (IAM)** no lado esquerdo da página.

1. Uma vez no **controlo de acesso (IAM)**, na caixa "Adicionar uma atribuição de função" selecione **Adicionar**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Mostra a página de controlo de acesso (IAM) numa conta de armazenamento e o botão 'Adicionar uma atribuição de função' no portal Azure.":::

1. Na página lateral "Adicionar uma atribuição de funções", escolha o papel da lista acima na entrega de **funções.** Introduza o **nome** do cofre e selecione **Save**.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Mostra a página de Controlo de Acesso (IAM) numa conta de armazenamento e as opções para selecionar uma Função e qual principal conceder essa função no portal Azure.":::

Além destas permissões, os serviços de confiança da MS também precisam de ser autorizados a ter acesso. Vá a "Firewalls e redes virtuais" e selecione "Permitir que serviços fidedignos da Microsoft acedam a esta conta de armazenamento" em **Exceções**.

## <a name="protect-your-virtual-machines"></a>Proteja as suas máquinas virtuais

Uma vez concluídas todas as configurações acima, continue com a replicação ativante para as suas máquinas virtuais. Todas as operações de Recuperação do Site funcionam sem quaisquer passos adicionais se a integração de DNS foi usada enquanto criava pontos finais privados no cofre. No entanto, se as zonas DNS forem criadas e configuradas manualmente, necessita de medidas adicionais para adicionar registos DNS específicos nas zonas de DNS de origem e alvo depois de permitir a replicação. Para mais detalhes e passos, consulte [Criar zonas privadas de DNS e adicionar registos DNS manualmente](#create-private-dns-zones-and-add-dns-records-manually).

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Crie zonas privadas de DNS e adicione registos DNS manualmente

Se não selecionou a opção de integração com a zona privada de DNS no momento da criação do ponto final privado para o cofre, siga os passos nesta secção.

Crie uma zona privada de DNS para permitir ao agente de mobilidade resolver nomes de domínio totalmente qualificados para IPs privados.

1. Criar uma zona privada de DNS

   1. Procure "Zona privada de DNS" na barra de pesquisa **de todos os serviços** e selecione "Zonas PRIVADAs de DNS" a partir do drop-down.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Mostra a procura de &quot;zona privada de dns&quot; na página de novos recursos no portal Azure.":::

   1. Uma vez na página "Zonas DNS Privadas", selecione o botão **\+ Adicionar** para começar a criar uma nova zona.

   1. Na página "Criar zona privada de DNS", preencha os detalhes necessários. Insira o nome da zona privada do DNS como `privatelink.siterecovery.windowsazure.com` . Pode escolher qualquer grupo de recursos e qualquer subscrição para criá-lo.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Mostra o separador Básico da página da zona de DNS Criar EsU E detalhes relacionados do projeto no portal Azure.":::

   1. Continue a fazer o separador **'Revisão' \+** para rever e criar a zona DNS.

1. Ligue a zona privada de DNS à sua rede virtual

   As zonas privadas de DNS criadas acima devem agora estar ligadas à rede virtual onde os seus servidores se encontram atualmente. Também precisa de ligar a zona privada de DNS à rede virtual alvo com antecedência.

   1. Vá à zona privada de DNS que criou no passo anterior e navegue para **links de rede Virtual** no lado esquerdo da página. Uma vez lá, selecione o botão **\+ Adicionar.**

   1. Preencha os detalhes necessários. Os campos **de subscrição** e **rede Virtual** devem ser preenchidos com os detalhes correspondentes da rede virtual onde os seus servidores existem. Os outros campos devem ser deixados como estão.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Mostra a página para adicionar uma ligação de rede virtual com o nome de link, subscrição e rede virtual relacionada no portal Azure.":::

1. Adicionar registos DNS

   Depois de ter criado as zonas de DNS privadas e os pontos finais privados, tem de adicionar registos DNS às suas zonas dns.

   > [!NOTE]
   > Caso utilize uma zona de DNS privada personalizada, certifique-se de que as entradas semelhantes são feitas conforme discutido abaixo.

   Este passo requer que faça entradas para cada nome de domínio totalmente qualificado no seu ponto final privado na sua zona privada de DNS.

   1. Vá à sua zona privada de DNS e navegue para a secção **Geral** no lado esquerdo da página. Uma vez lá, selecione **\+ Record set** para começar a adicionar registos.

   1. Na página "Adicionar conjunto de registos" que abre, adicione uma entrada para cada nome de domínio totalmente qualificado e IP privado como um registo tipo _A._ A lista de nomes de domínios e IPs totalmente qualificados pode ser obtida a partir da página "Private Endpoint" em **Visão Geral**. Como mostrado no exemplo abaixo, o primeiro nome de domínio totalmente qualificado do ponto final privado é adicionado ao recorde estabelecido na zona privada de DNS.

      Estes nomes de domínio totalmente qualificados correspondem ao padrão: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Mostra a página para adicionar um registo tipo DNS A para o nome de domínio totalmente qualificado ao ponto final privado no portal Azure.":::

   > [!NOTE]
   > Depois de permitir a replicação, são criados mais dois nomes de domínio totalmente qualificados nos pontos finais privados de ambas as regiões. Certifique-se de que adiciona os registos DNS para estes nomes de domínio recentemente criados e totalmente qualificados também.

## <a name="next-steps"></a>Passos seguintes

Agora que ativou pontos finais privados para a sua replicação de máquinas virtuais, consulte estas outras páginas para obter informações adicionais e relacionadas:

- [Replicar VMs do Azure para outra região do Azure](./azure-to-azure-how-to-enable-replication.md)
- [Tutorial: Criar recuperação de desastres para VMs Azure](./azure-to-azure-tutorial-enable-replication.md)