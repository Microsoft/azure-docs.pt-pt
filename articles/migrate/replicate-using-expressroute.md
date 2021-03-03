---
title: Replicar dados sobre ExpressRoute com migração de servidores Azure Migrate
description: Como utilizar o Azure ExpressRoute para replicação com a migração do servidor Azure Migrate
author: ms-deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: fe44ff423240cbe24d91cef0c0f4bb803ad8df98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746549"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Replicar dados sobre ExpressRoute com Azure Migrate: Migração de servidores

Neste artigo, você aprenderá a configurar [Azure Migrate: Server Migration](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) para replicar dados sobre um circuito ExpressRoute enquanto migra os servidores para Azure.

## <a name="understand-azure-expressroute-circuits"></a>Compreender os circuitos Azure ExpressRoute
Um circuito ExpressRoute (ER) liga a sua infraestrutura no local à Microsoft através de um fornecedor de conectividade. Os circuitos ExpressRoute podem ser configurados para usar o espreitamento privado, o olhar da Microsoft, ou ambos. Reveja o artigo sobre [os circuitos ExpressRoute e esprevando](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) para saber mais sobre as várias opções de observação disponíveis com o ExpressRoute.

A ferramenta de migração do servidor da Azure Migrate ajuda-o a migrar servidores e servidores no local de outras nuvens para máquinas virtuais Azure. A ferramenta funciona através da criação de um fluxo de replicação em curso para replicar dados dos servidores a migrar para discos geridos na sua subscrição Azure. Quando estiver pronto para migrar os servidores, os dados replicados no Azure são utilizados para migrar os servidores.

Os dados replicados a partir dos servidores no local podem ser configurados para serem enviados para a sua subscrição Azure através da internet (utilizando uma ligação encriptada segura) ou através de uma ligação ExpressRoute. Quando tem um grande número de servidores para migrar, usar o ExpressRoute para replicação pode ajudá-lo a migrar os servidores de forma mais eficiente utilizando a largura de banda disponível no seu circuito ExpressRoute.

Neste artigo, vai aprender
> [!div class="checklist"]
>
> * Como replicar dados usando um circuito ExpressRoute com um olhar privado.
> * Como replicar dados usando um circuito ExpressRoute com a Microsoft a espreitar.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Replicar dados usando um circuito ExpressRoute com esprevação privada

> [!NOTE]
> A replicação sobre um circuito de observação privado é atualmente suportada apenas para [a migração sem agentes de máquinas virtuais VMware para Azure](./tutorial-migrate-vmware.md). O suporte privado para outros [métodos de replicação](./migrate-services-overview.md#azure-migrate-server-migration-tool) estará disponível em breve.

No método sem agente de migrar máquinas virtuais VMware para Azure, o aparelho Azure Migrate envia primeiro dados de replicação para uma conta de armazenamento (conta de armazenamento de cache) na sua subscrição. Os dados replicados da conta de armazenamento de cache são então transferidos para discos geridos por réplicas na sua subscrição pelo serviço Azure Migrate. Para utilizar um circuito de observação privado para replicação, irá criar e anexar um ponto final privado à utilização da conta de armazenamento de cache. Os pontos finais privados utilizam um ou mais endereços IP privados da sua rede virtual (VNet), efetivamente trazendo a conta de armazenamento para o seu Azure VNet. O ponto final privado permite que o aparelho Azure Migrate se conecte à conta de armazenamento de cache utilizando o expressRoute private peering and transfer dados diretamente no endereço IP privado. <br/>  

![Processo de replicação](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Além dos dados de replicação, o aparelho Azure Migrate comunica com o serviço Azure Migrate para as suas atividades de plano de controlo, incluindo a orquestração de replicação. A comunicação do avião de controlo entre o aparelho Azure Migrate e o serviço Azure Migrate continua a acontecer através da internet no ponto final público do serviço Azure Migrate.
> - O ponto final privado da conta de armazenamento deve estar acessível a partir da rede onde o aparelho Azure Migrate está implantado.
> - O DNS deve ser configurado para resolver as consultas de DNS pelo aparelho Azure Migrate para o ponto final do serviço blob da conta de armazenamento de cache para o endereço IP privado do ponto final privado anexado à conta de armazenamento de cache.
> - A conta de armazenamento de cache deve estar acessível no seu ponto final público. (O serviço Azure Migrate utiliza o ponto final público da conta de armazenamento de cache para transferir dados da conta de armazenamento para discos geridos por réplicas.) 


### <a name="1-pre-requisites"></a>1. Pré-requisitos

O utilizador Azure que cria o ponto final privado deve ter as seguintes permissões no grupo de recursos e na rede virtual em que o ponto final privado será criado.

**Cenário de teste** | **Permissões** 
--- | --- 
 Criar e gerir pontos finais privados. | Microsoft.Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action  
|Fixe um ponto final privado a uma VNet/sub-rede.<br/>Isto é necessário na rede virtual onde o ponto final privado será criado.| Microsoft.Network/virtualNetworks/subnet/join/action Microsoft.Network/virtualNetworks/join/action
|Ligue o ponto final privado a uma conta de armazenamento. <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|Crie uma interface de rede e junte-a a um grupo de segurança de rede. | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action (opcional)
Criar e gerir zonas privadas de DNS.| Papel de contribuinte privado da zona do DNS <br/> _Ou_ <br/> Microsoft.Network/privateDnsZones/A/* <br/>  Microsoft.Network/privateDnsZones/write Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/>  Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="2-identify-the-cache-storage-account"></a>2. Identificar a conta de armazenamento de cache 
 
O Azure Migrate cria automaticamente uma conta de armazenamento de cache quando configura a replicação (utilizando a experiência do portal Azure) para uma máquina virtual pela primeira vez num projeto Azure Migrate. A conta de armazenamento é criada no mesmo grupo de subscrição e recursos em que criou o projeto Azure Migrate.

Para criar e localizar a conta de armazenamento:

1. Utilize a experiência do portal Azure para a Azure Migrate para replicar uma ou mais máquinas virtuais no projeto.
2. Navegue para o grupo de recursos do projeto Azure Migrate.
3. Localize a conta de armazenamento de cache identificando o prefixo **"Lsa"** no nome da conta de armazenamento.

![Vista de grupo de recursos](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Se tiver mais do que uma conta de armazenamento com o prefixo **"lsa"** no seu grupo de recursos, pode verificar a conta de armazenamento navegando para as definições de replicação e menu de configuração alvo para qualquer um dos VMs replicantes do projeto. <br/> 
> ![Visão geral das definições de replicação](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. Atualizar a conta de armazenamento de cache para o Final geral v2 

Só pode criar pontos finais privados numa conta de armazenamento De Finalidade Geral v2 (GPv2). Se a conta de armazenamento de cache não for uma conta de armazenamento GPv2, atualize-a para GPv2 utilizando os seguintes passos:

1. Navegue até à sua conta de armazenamento.
2. Selecione **Configuração**.
3. Sob **o tipo Conta,** selecione **Upgrade**.
4. Em **Confirmar atualização**, introduza o nome da sua conta.
5. Selecione **Upgrade** na parte inferior da página.

![Atualizar conta de armazenamento](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. Criar um ponto final privado para a conta de armazenamento

1. Vá à sua conta de armazenamento, **selecione Networking** a partir do menu esquerdo e selecione o **separador de ligações de ponto final Privado.**  
2. Selecione **+ ponto final privado.**

    a. Na janela **'Criar' privado** – selecione o grupo **de subscrição** e **recursos**. Forneça um nome para o seu ponto final privado e selecione a região da conta de armazenamento.  
    ![Janela de configuração PE](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. No **separador Recursos,** forneça o **nome de Assinatura** em que a conta de armazenamento está. Escolha **microsoft.storage/storageAcontas** como o **tipo de recurso**. Em **Recurso,** forneça o nome da conta de armazenamento de replicação do tipo GPv2. Selecione **Blob** como **sub-recurso target**.  
    ![armazenamento de contas](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. No **separador Configuração,** selecione a **rede Virtual** e **a Sub-rede** para o ponto final privado da conta de armazenamento.  

    > [!Note]
    > A rede virtual deve conter o ponto de passagem ExpressRoute ou deve ser ligada à rede virtual com o gateway ExpressRoute. 

    Na secção **De Integração Privada de DNS,** selecione **Sim** e integre-o com uma zona privada de DNS. Selecionar **Sim** liga automaticamente a zona DNS à rede virtual selecionada e adiciona os registos DNS que são necessários para a resolução de DNS de novos IPs e nomes de domínio totalmente qualificados criados para o ponto final privado. Saiba mais sobre [as zonas privadas do DNS.](https://docs.microsoft.com/azure/dns/private-dns-overview)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. Também pode adicionar **Tags** para o seu ponto final privado.  

    e. Continue a **rever + criar** uma vez feito a introdução de detalhes. Quando a validação estiver concluída, selecione **Criar** para criar o ponto final privado.

    > [!Note]
    > Se o utilizador que cria o ponto final privado for também o proprietário da conta de armazenamento, o ponto final privado será aprovado automaticamente. Caso contrário, o proprietário deve aprovar o ponto final privado para utilização. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Crie zonas privadas de DNS e adicione registos DNS manualmente (Opcional)

Se não selecionou a opção de integração com uma zona privada de DNS no momento da criação do ponto final privado, siga os passos nesta secção para criar manualmente uma zona privada de DNS. 

> [!Note]
> Se selecionou **Sim** para integrar-se a uma zona de DNS privada, pode saltar esta secção. 

1. Crie uma zona privada de DNS. 

    ![criarprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  Na página **zonas privadas de DNS,** selecione o botão **+Adicionar** para começar a criar uma nova zona.  
    b.  Na página privada da **zona DO DNS,** preencha os detalhes necessários. Insira o nome da zona privada do DNS como _privatelink_.blob.core.windows.net. c. Continue a **rever + criar** separador para rever e criar a zona DNS.

2. Ligue a zona privada do DNS à sua rede virtual.  

    A zona privada de DNS criada acima deve estar ligada à rede virtual a que o ponto final privado está ligado.

    a. Vá para a zona privada de DNS criada no passo anterior e navegue para links de rede virtuais no lado esquerdo da página. Selecione o botão **+Adicionar.**   
    b. Preencha os detalhes necessários. Os campos **de subscrição** e **rede Virtual** devem ser preenchidos com os detalhes correspondentes da rede virtual onde o seu ponto final privado está ligado. Os outros campos podem ser deixados como estão.

3. O próximo passo é adicionar registos DNS à zona do DNS. Adicione uma entrada para o nome de domínio totalmente qualificado da conta de armazenamento na sua zona privada de DNS.

    a. Vá à sua zona privada de DNS e navegue para a secção **Geral** no lado esquerdo da página. Selecione **+Record** definido para começar a adicionar registos.  

    b. Na página **de conjunto de registos Add,** adicione uma entrada para o nome de domínio totalmente qualificado e IP privado como um registo tipo A.

> [!Important]
> Pode necessitar de configurações adicionais de DNS para resolver o endereço IP privado do ponto final privado da conta de armazenamento a partir do ambiente de origem. [Reveja este artigo](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) para entender a configuração de DNS necessária.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Replicar dados usando um circuito ExpressRoute com o microsoft a espreitar

Pode utilizar o microsoft peering ou um domínio de observação público existente (depreciado para novas ligações ExpressRoute) para encaminhar o tráfego de replicação através de um circuito ExpressRoute, conforme ilustrado no diagrama abaixo.
![replicações commicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Mesmo com os dados de replicação a passar pelo circuito de observação da Microsoft, ainda vai precisar de conectividade na Internet a partir do site no local para outra comunicação (plano de controlo) com o serviço Azure Migrate. Existem alguns URLs adicionais, que não são alcançáveis sobre o ExpressRoute, que o aparelho de replicação / Anfitrião Hiper-V precisa de acesso para orquestrar o processo de replicação. Pode rever os requisitos de URL com base no cenário de migração, [migrações sem agente VMware](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) ou [migrações baseadas em agentes.](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance)  

Caso utilize um representante no local e deseje utilizar o ExpressRoute para o tráfego de replicação, é necessário configurar um bypass de procuração para URLs relevantes no aparelho no local. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Configure as regras de bypass de procuração no aparelho Azure Migrate (para migrações sem agente VMware)

1. Faça login (ambiente de trabalho remoto) para o aparelho Azure Migrate.   
2. Abra o ficheiro C:/ProgramData/MicrosoftAzure/Config/appliance.jsna utilização do bloco de notas.
3. No ficheiro, altere a linha que diz "EnableProxyBypassList": "falso", para "EnableProxyBypassList": "true". Guarde as alterações e reinicie o aparelho.
4. Depois de reiniciar, quando abrir o gestor de configuração do aparelho, poderá ver a opção de bypass proxy na UI da aplicação web. Adicione os URLs abaixo à lista de bypass proxy.   
    - .*.vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com
    - .*.blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Configure as regras de bypass por procuração no aparelho de replicação (para migrações baseadas em agentes)

Siga os passos abaixo para configurar a lista de bypass Proxy nos servidores de Configuração e nos servidores de processo:

1. [Descarregue a ferramenta PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) para aceder ao contexto do utilizador do sistema.
2. Abra o Internet Explorer no contexto do utilizador do sistema executando a seguinte linha de comando psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
3. Adicione as definições de procuração no IE.
4. Na lista de bypass, adicione o URL de armazenamento Azure.*.blob.core.windows.net.  

As regras de bypass acima garantem que o tráfego de replicação pode fluir através do ExpressRoute enquanto a comunicação de gestão pode passar pelo representante para a Internet.  

Além disso, você deve anunciar rotas no Filtro de Rota para as seguintes comunidades BGP para fazer o seu tráfego de replicação Azure Migrate atravessar um circuito ExpressRoute em vez da internet.  

- Comunidade Regional de BGP para a região de Azure (região do Projeto Azure Migrate)
- Comunidade Regional de BGP para a região de Azure alvo (região para a migração)
- Comunidade BGP para Azure Ative Directory (12076:5060)

Saiba mais sobre [os Filtros de Rota](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) e a lista de [comunidades BGP para ExpressRoute.](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os circuitos ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings).
- Saiba mais sobre [os domínios de encaminhamento ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare).
- Saiba mais sobre [os pontos finais privados.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)