---
title: Conectividade de ponto de extremidade público para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP
description: Conectividade de ponto de extremidade público para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: radeltch
ms.openlocfilehash: 52179f15829981d59ff060784a49eccef89bb186
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083740"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Conectividade de ponto de extremidade público para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP

O escopo deste artigo é descrever as configurações, que permitirão a conectividade de saída para pontos de extremidade públicos. As configurações estão principalmente no contexto de alta disponibilidade com pacemaker para SUSE/RHEL.  

Se você estiver usando o pacemaker com o agente de isolamento do Azure em sua solução de alta disponibilidade, as VMs deverão ter conectividade de saída com a API de gerenciamento do Azure.  
O artigo apresenta várias opções para permitir que você selecione a opção mais adequada para seu cenário.  

## <a name="overview"></a>Descrição geral

Ao implementar uma elevada disponibilidade para soluções SAP através de clustering, um dos componentes necessários é o [Equilíbrio de Carga Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). O Azure oferece dois SKUs do balanceador de carga: Standard e Basic.

O balanceador de carga do Azure padrão oferece algumas vantagens em relação ao balanceador de carga básico. Por exemplo, ele funciona em zonas de disponibilidade do Azure, tem recursos de monitoramento e de registro melhores para facilitar a solução de problemas, latência reduzida. O recurso "portas de HA" abrange todas as portas, ou seja, não é mais necessário listar todas as portas individuais.  

Há algumas diferenças importantes entre o SKU básico e o padrão do Azure Load Balancer. Uma delas é a manipulação do tráfego de saída para o ponto de extremidade público. Para uma comparação completa de equilíbrio de carga Basic versus Standard SKU, consulte a [comparação SKU do Balancer de Carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).  
 
Quando as VMs sem endereços IP públicos são colocadas no pool de back-end do Azure Load Balancer padrão (sem endereço IP público), não há nenhuma conectividade de saída para pontos de extremidade públicos, a menos que a configuração adicional seja feita.  

Se uma VM for atribuída a um endereço IP público ou a VM estiver no pool de back-end de um balanceador de carga com endereço IP público, ela terá conectividade de saída para pontos de extremidade públicos.  

Os sistemas SAP geralmente contêm dados comerciais confidenciais. Raramente é aceitável que as VMs que hospedam sistemas SAP tenham endereços IP públicos. Ao mesmo tempo, há cenários, o que exigiria conectividade de saída da VM para pontos de extremidade públicos.  

Exemplos de cenários, que exigem acesso ao ponto de extremidade público do Azure são:  
- Usando o agente de isolamento do Azure como um mecanismo de isolamento em clusters pacemaker
- Azure Backup
- Azure Site Recovery  
- Usando o repositório público para aplicar patch no sistema operacional
- O fluxo de dados do aplicativo SAP pode exigir conectividade de saída para o ponto de extremidade público

Se sua implantação do SAP não exigir conectividade de saída para pontos de extremidade públicos, você não precisará implementar a configuração adicional. É suficiente criar Azure Load Balancer de SKU padrão internos para seu cenário de alta disponibilidade, supondo que também não haja necessidade de conectividade de entrada de pontos de extremidade públicos.  

> [!Note]
> Quando os VMs sem endereços IP públicos forem colocados no conjunto de backend do interno (sem endereço IP público) O equilíbrio de carga Standard Azure não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos.  
>Se as VMs tiverem endereços IP públicos ou já estiverem no pool de back-end do Azure Load Balancer com o endereço IP público, a VM já terá conectividade de saída para pontos de extremidade públicos.


Leia os seguintes documentos primeiro:

* Standard Load Balancer do Azure
  * Visão geral do Equilíbrio de [Carga Padrão Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) - visão geral abrangente do equilíbrio de carga padrão Azure, princípios importantes, conceitos e tutoriais 
  * [Ligações de saída em Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) - cenários sobre como alcançar a conectividade de saída em Azure
  * Regras de saída do [equilibrador](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)de carga - explica os conceitos de regras de saída do equilibrador de carga e como criar regras de saída
* Azure Firewall
  * [Visão geral da Firewall Azure](https://docs.microsoft.com/azure/firewall/overview)- visão geral da Firewall Azure
  * [Tutorial: Implementar e configurar firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) - instruções sobre como configurar firewall Azure via portal Azure
* [Redes Virtuais -Regras definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) - Conceitos e regras de encaminhamento do Azure  
* [Etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço de grupos de segurança - como simplificar os seus Grupos de Segurança de Rede e configuração firewall com etiquetas de serviço

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Standard Load Balancer adicionais externos do Azure para conexões de saída à Internet

Uma opção para alcançar a conectividade de saída aos pontos finais públicos, sem permitir a conectividade de entrada com o VM a partir do ponto final público, é criar um segundo equilibrista de carga com endereço IP público, adicionar os VMs ao conjunto de backend do segundo equilibrador de carga e definir apenas regras de [saída.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)  
Utilize [grupos](https://docs.microsoft.com/azure/virtual-network/security-overview) de segurança de rede para controlar os pontos finais públicos, acessíveis para chamadas de saída do VM.  
Para mais informações, consulte o Cenário 2 em [ligações](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)de saída documentais .  
A configuração teria a seguinte aparência:  

![Controlar a conectividade a pontos de extremidade públicos com grupos de segurança de rede](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerações importantes

- Você pode usar um Load Balancer público adicional para várias VMs na mesma sub-rede para obter conectividade de saída para o ponto de extremidade público e otimizar o custo  
- Utilize [grupos](https://docs.microsoft.com/azure/virtual-network/security-overview) de segurança de rede para controlar quais os pontos finais públicos acessíveis a partir dos VMs. Você pode atribuir o grupo de segurança de rede à sub-rede ou a cada VM. Sempre que possível, utilize [etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço para reduzir a complexidade das regras de segurança.  
- O balanceador de carga standard do Azure com o endereço IP público e as regras de saída permite o acesso direto ao ponto de extremidade público. Se você tiver requisitos de segurança corporativa para que todo o tráfego de saída passe por meio da solução corporativa centralizada para auditoria e registro em log, talvez não seja possível atender ao requisito com esse cenário.  

>[!TIP]
>Sempre que possível, utilize [etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço para reduzir a complexidade do Grupo de Segurança da Rede . 

### <a name="deployment-steps"></a>Passos da implementação

1. Criar Balanceador de carga  
   1. No [portal Azure,](https://portal.azure.com) clique em todos os recursos, adicione, em seguida, procure por **Balancer de Carga**  
   1. Clique em **Criar**. 
   1. Nome do equilíbrio de carga **MyPublicILB**  
   1. Selecione **Público** como tipo, **Standard** as SKU  
   1. Selecione **Criar endereço IP público** e especificar como um nome **MyPublicILBFrondEndIP**  
   1. Selecione **Zona Redundante** como zona de disponibilidade  
   1. Clique em revisar e criar e em criar  
2. Crie piscina de backend **MyBackendPoolOfPublicILB** e adicione os VMs.  
   1. Selecionar a rede virtual  
   1. Selecione as VMs e seus endereços IP e adicione-as ao pool de back-end  
3. [Criar regras de saída.](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule) No momento, não é possível criar regras de saída do portal do Azure. Pode criar regras de saída com [o Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Crie regras de grupo de segurança de rede para restringir o acesso a pontos de extremidade públicos específicos. Se houver um grupo de segurança de rede existente, você poderá ajustá-lo. O exemplo abaixo mostra como permitir o acesso à API de gestão azure: 
   1. Navegue até o grupo de segurança de rede
   1. Clique em regras de segurança de saída
   1. Adicione uma regra a **Negar** todo o acesso à **Internet.**
   1. Adicione uma regra para **permitir** o acesso ao **AzureCloud,** com prioridade inferior à prioridade da regra para negar todo o acesso à Internet.


   As regras de segurança de saída teriam a seguinte aparência: 

   ![Conexão de saída com o segundo Load Balancer com IP público](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Para obter mais informações sobre os grupos de segurança da Rede Azure, consulte [grupos de segurança ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Firewall do Azure para conexões de saída com a Internet

Outra opção para obter a conectividade de saída para pontos de extremidade públicos, sem permitir a conectividade de entrada para a VM a partir de pontos de extremidade públicos, é com o Firewall do Azure. O Firewall do Azure é um serviço gerenciado, com alta disponibilidade interna e pode abranger vários Zonas de Disponibilidade.  
Também terá de implementar a Rota Definida pelo [Utilizador,](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)associada à subrede onde estão implantados VMs e o equilibrador de carga Azure, apontando para a firewall Azure, para encaminhar o tráfego através da Firewall Azure.  
Para mais detalhes sobre como implementar o Firewall Azure, consulte [implementar e configurar firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

A arquitetura teria a seguinte aparência:

![Conexão de saída com o Firewall do Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerações importantes

- O Firewall do Azure é um serviço de nuvem nativo, com alta disponibilidade interna e oferece suporte à implantação zonal.
- Requer sub-rede adicional que deve ser nomeada AzureFirewallSubnet. 
- Se estiver transferindo grandes conjuntos de dados de saída da rede virtual em que as VMs SAP estão localizadas, para uma VM em outra rede virtual ou para o ponto de extremidade público, talvez não seja uma solução econômica. Um exemplo é copiar grandes backups entre redes virtuais. Para obter detalhes, consulte preços do firewall do Azure.  
- Se a solução de firewall corporativo não for o Firewall do Azure e você tiver requisitos de segurança para fazer todo o tráfego de saída passar por uma solução corporativa centralizada, essa solução poderá não ser prática.  

>[!TIP]
>Sempre que possível, utilize [etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço para reduzir a complexidade das regras da Firewall Azure.  

### <a name="deployment-steps"></a>Passos da implementação

1. As etapas de implantação pressupõem que você já tenha a rede virtual e a sub-rede definidas para suas VMs.  
2. Crie a Subnet **AzureFirewallSubnet** na mesma Rede Virtual, onde o VMS e o Standard Load Balancer são implantados.  
   1. Em portal do Azure, navegue até a rede virtual: clique em todos os recursos, procure a rede virtual, clique na rede virtual, selecione sub-redes.  
   1. Clique em Adicionar sub-rede. Insira **o AzureFirewallSubnet** como Nome. Insira o intervalo de endereços apropriado. Guarde.  
3. Crie o Firewall do Azure.  
   1. Em portal do Azure selecione todos os recursos, clique em Adicionar, firewall, criar. Selecione Grupo de recursos (selecione o mesmo grupo de recursos em que a rede virtual é).  
   1. Insira o nome do recurso de firewall do Azure. Por exemplo, **MyAzureFirewall**.  
   1. Selecione região e selecione pelo menos duas zonas de disponibilidade alinhadas com as zonas de disponibilidade nas quais suas VMs são implantadas.  
   1. Selecione sua rede virtual, onde as VMs SAP e o balanceador de carga standard do Azure são implantados.  
   1. Endereço IP público: clique em criar e insira um nome. Por **exemplo, MyFirewallPublicIP**.  
4. Crie uma regra de firewall do Azure para permitir a conectividade de saída para pontos de extremidade públicos especificados. O exemplo mostra como permitir o acesso ao ponto de extremidade público da API de gerenciamento do Azure.  
   1. Selecione regras, coleção de regras de rede e clique em Adicionar coleção de regras de rede.  
   1. Nome: **MyOutboundRule**, introduza Prioridade, **Selecione**Action Allow .  
   1. Serviço: Nome **ToAzureAPI**.  Protocolo: Selecione **Qualquer**. Endereço fonte: introduza o intervalo para a sua sub-rede, onde os VMs e o Standard Load Balancer são implantados por exemplo: **11.97.0.0.0/24**. Portas de destino: insira <b>*</b>.  
   1. Guardar
   1. Como você ainda está posicionado no firewall do Azure, selecione visão geral. Anote o endereço IP privado do firewall do Azure.  
5. Criar rota para o Firewall do Azure  
   1. Em portal do Azure selecione todos os recursos e clique em Adicionar, tabela de rotas, criar.  
   1. Insira o nome myroteiatable, selecione assinatura, grupo de recursos e local (correspondendo ao local da rede virtual e do firewall).  
   1. Guardar  

   A regra da firewall seria como: ![ligação de saída com o Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Crie a Rota Definida do Utilizador desde a subnet a subnet dos seus VMs até ao IP privado do **MyAzureFirewall**.
   1. Conforme você está posicionado na tabela de rotas, clique em rotas. Selecione Adicionar. 
   1. Nome da rota: ToMyAzureFirewall, Prefixo de endereço: **0.0.0.0/0**. Tipo do próximo salto: selecione dispositivo virtual. Próximo endereço de lúpulo: introduza o endereço IP privado da firewall configurada: **11.97.1.4**.  
   1. Guardar

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Usando proxy para chamadas pacemaker para a API de gerenciamento do Azure

Você pode usar o proxy para permitir chamadas pacemaker para o ponto de extremidade público da API de gerenciamento do Azure.  

### <a name="important-considerations"></a>Considerações importantes

  - Se já houver um proxy corporativo em vigor, você poderá rotear chamadas de saída para pontos de extremidade públicos por meio dele. As chamadas de saída para pontos de extremidade públicos passarão pelo ponto de controle corporativo.  
  - Certifique-se de que a configuração proxy permite a conectividade de saída à API de gestão do Azure: https://management.azure.com  
  - Verifique se há uma rota das VMs para o proxy  
  - O proxy tratará apenas chamadas HTTP/HTTPS. Se houver necessidade adicional de fazer chamadas de saída para o ponto de extremidade público em protocolos diferentes (como a RFC), será necessária uma solução alternativa  
  - A solução de proxy deve estar altamente disponível, para evitar instabilidade no cluster pacemaker  
  - Dependendo do local do proxy, ele pode introduzir latência adicional nas chamadas do agente de isolamento do Azure para a API de gerenciamento do Azure. Se o seu proxy corporativo ainda estiver no local, enquanto o cluster pacemaker está no Azure, meça a latência e considere se essa solução é adequada para você  
  - Se ainda não houver um proxy corporativo altamente disponível em vigor, não recomendamos essa opção, pois o cliente estaria incorrendo em custo e complexidade extras. No entanto, se você decidir implantar uma solução de proxy adicional, com a finalidade de permitir a conectividade de saída do pacemaker para a API pública de gerenciamento do Azure, verifique se o proxy está altamente disponível e se a latência das VMs para o proxy está baixa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configuração do pacemaker com proxy 

Há muitas opções de proxy diferentes disponíveis no setor. As instruções passo a passo para a implantação de proxy estão fora do escopo deste documento. No exemplo abaixo, assumimos que o seu representante está a responder ao **MyProxyService** e a ouvir a porta **MyProxyPort**.  
Para permitir que o pacemaker se comunique com a API de gerenciamento do Azure, execute as seguintes etapas em todos os nós de cluster:  

1. Edite o arquivo de configuração pacemaker/etc/sysconfig/pacemaker e adicione as seguintes linhas (todos os nós de cluster):  
   ```
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Reinicie o serviço de pacemaker em **todos os** nós do cluster.  
  - SUSE  
     ```
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="next-steps"></a>Passos seguintes

* [Saiba como configurar pacemaker na SUSE em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Saiba como configurar pacemaker no Chapéu Vermelho em Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
