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
ms.date: 10/28/2019
ms.author: radeltch
ms.openlocfilehash: ae2fb4c13633fa2ac22510a98e193bd9f01efb12
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73045385"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Conectividade de ponto de extremidade público para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP

O escopo deste artigo é descrever as configurações, que permitirão a conectividade de saída para pontos de extremidade públicos. As configurações estão principalmente no contexto de alta disponibilidade com pacemaker para SUSE/RHEL.  

Se você estiver usando o pacemaker com o agente de isolamento do Azure em sua solução de alta disponibilidade, as VMs deverão ter conectividade de saída com a API de gerenciamento do Azure.  
O artigo apresenta várias opções para permitir que você selecione a opção mais adequada para seu cenário.  

## <a name="overview"></a>Visão geral

Ao implementar alta disponibilidade para soluções SAP por meio de clustering, um dos componentes necessários é [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). O Azure oferece dois SKUs do balanceador de carga: Standard e Basic.

O balanceador de carga do Azure padrão oferece algumas vantagens em relação ao balanceador de carga básico. Por exemplo, ele funciona em zonas de disponibilidade do Azure, tem recursos de monitoramento e de registro melhores para facilitar a solução de problemas, latência reduzida. O recurso "portas de HA" abrange todas as portas, ou seja, não é mais necessário listar todas as portas individuais.  

Há algumas diferenças importantes entre o SKU básico e o padrão do Azure Load Balancer. Uma delas é a manipulação do tráfego de saída para o ponto de extremidade público. Para comparação completa de balanceador de carga do SKU básico versus Standard, consulte [Load Balancer a comparação de SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus).  
 
Quando as VMs sem endereços IP públicos são colocadas no pool de back-end do Azure Load Balancer padrão (sem endereço IP público), não há nenhuma conectividade de saída para pontos de extremidade públicos, a menos que a configuração adicional seja feita.  

Se uma VM for atribuída a um endereço IP público ou a VM estiver no pool de back-end de um balanceador de carga com endereço IP público, ela terá conectividade de saída para pontos de extremidade públicos.  

Os sistemas SAP geralmente contêm dados comerciais confidenciais. Raramente é aceitável que as VMs que hospedam sistemas SAP tenham endereços IP públicos. Ao mesmo tempo, há cenários, o que exigiria conectividade de saída da VM para pontos de extremidade públicos.  

Exemplos de cenários, que exigem acesso ao ponto de extremidade público do Azure são:  
- Usando o agente de isolamento do Azure como um mecanismo de isolamento em clusters pacemaker
- Azure Backup
- Recuperação de Site do Azure  
- Usando o repositório público para aplicar patch no sistema operacional
- O fluxo de dados do aplicativo SAP pode exigir conectividade de saída para o ponto de extremidade público

Se sua implantação do SAP não exigir conectividade de saída para pontos de extremidade públicos, você não precisará implementar a configuração adicional. É suficiente criar Azure Load Balancer de SKU padrão internos para seu cenário de alta disponibilidade, supondo que também não haja necessidade de conectividade de entrada de pontos de extremidade públicos.  

> [!Note]
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Azure Load Balancer padrão (sem endereço IP público), não haverá nenhuma conectividade com a Internet de saída, a menos que a configuração adicional seja executada para permitir o roteamento para pontos de extremidade públicos.  
>Se as VMs tiverem endereços IP públicos ou já estiverem no pool de back-end do Azure Load Balancer com o endereço IP público, a VM já terá conectividade de saída para pontos de extremidade públicos.


Leia os seguintes documentos primeiro:

* Standard Load Balancer do Azure
  * [Visão geral do azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) -visão geral abrangente do balanceador de carga standard do Azure, princípios importantes, conceitos e tutoriais 
  * [Conexões de saída no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) – cenários sobre como obter conectividade de saída no Azure
  * [Regras de saída do balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)-explica os conceitos das regras de saída do balanceador de carga e como criar regras de saída
* Azure Firewall
  * [Visão geral do firewall do Azure](https://docs.microsoft.com/azure/firewall/overview)-visão geral do firewall do Azure
  * [Tutorial: implantar e configurar o Firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) -instruções sobre como configurar o Firewall do Azure por meio do portal do Azure
* [Redes virtuais – regras definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) – conceitos e regras de roteamento do Azure  
* [Marcas de serviço de grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) -como simplificar os grupos de segurança de rede e a configuração de firewall com marcas de serviço

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Standard Load Balancer adicionais externos do Azure para conexões de saída à Internet

Uma opção para obter a conectividade de saída para pontos de extremidade públicos, sem permitir a conectividade de entrada para a VM do ponto de extremidade público, é criar um segundo balanceador de carga com o endereço IP público, adicionar as VMs ao pool de back-end do segundo balanceador de carga e definir somente [regras de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).  
Use [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para controlar os pontos de extremidade públicos que são acessíveis para chamadas de saída da VM.  
Para obter mais informações, consulte Cenário 2 no documento [conexões de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios).  
A configuração teria a seguinte aparência:  

![Controlar a conectividade a pontos de extremidade públicos com grupos de segurança de rede](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerações importantes

- Você pode usar um Load Balancer público adicional para várias VMs na mesma sub-rede para obter conectividade de saída para o ponto de extremidade público e otimizar o custo  
- Use [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview) para controlar quais pontos de extremidade públicos podem ser acessados das VMs. Você pode atribuir o grupo de segurança de rede à sub-rede ou a cada VM. Sempre que possível, use as [marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para reduzir a complexidade das regras de segurança.  
- O balanceador de carga standard do Azure com o endereço IP público e as regras de saída permite o acesso direto ao ponto de extremidade público. Se você tiver requisitos de segurança corporativa para que todo o tráfego de saída passe por meio da solução corporativa centralizada para auditoria e registro em log, talvez não seja possível atender ao requisito com esse cenário.  

>[!TIP]
>Sempre que possível, use as [marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para reduzir a complexidade do grupo de segurança de rede. 

### <a name="deployment-steps"></a>Passos da implementação

1. Criar Load Balancer  
   1. No [portal do Azure](https://portal.azure.com) , clique em todos os recursos, adicionar e procure **Load Balancer**  
   1. Clique em **Criar**. 
   1. Nome do Load Balancer **MyPublicILB**  
   1. Selecione **público** como um tipo, **padrão** como SKU  
   1. Selecione **criar endereço IP público** e especifique como um nome **MyPublicILBFrondEndIP**  
   1. Selecionar **zona com redundância** como zona de disponibilidade  
   1. Clique em revisar e criar e em criar  
2. Crie **MyBackendPoolOfPublicILB** do pool de back-end e adicione as VMs.  
   1. Selecionar a rede virtual  
   1. Selecione as VMs e seus endereços IP e adicione-as ao pool de back-end  
3. [Criar regras de saída](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule). No momento, não é possível criar regras de saída do portal do Azure. Você pode criar regras de saída com [CLI do Azure](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Crie regras de grupo de segurança de rede para restringir o acesso a pontos de extremidade públicos específicos. Se houver um grupo de segurança de rede existente, você poderá ajustá-lo. O exemplo a seguir mostra como permitir o acesso somente à API de gerenciamento do Azure: 
   1. Navegue até o grupo de segurança de rede
   1. Clique em regras de segurança de saída
   1. Adicione uma regra para **negar** todo o acesso de saída à **Internet**.
   1. Adicione uma regra para **permitir** o acesso ao **AzureCloud**, com prioridade inferior à prioridade da regra para negar todo o acesso à Internet.


   As regras de segurança de saída teriam a seguinte aparência: 

   ![Conexão de saída com o segundo Load Balancer com IP público](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Para obter mais informações sobre grupos de segurança de rede do Azure, consulte [grupos de segurança ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Firewall do Azure para conexões de saída com a Internet

Outra opção para obter a conectividade de saída para pontos de extremidade públicos, sem permitir a conectividade de entrada para a VM a partir de pontos de extremidade públicos, é com o Firewall do Azure. O Firewall do Azure é um serviço gerenciado, com alta disponibilidade interna e pode abranger vários Zonas de Disponibilidade.  
Você também precisará implantar a [rota definida pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes), associada à sub-rede em que as VMs e o balanceador de carga do Azure são implantados, apontando para o Firewall do Azure para rotear o tráfego por meio do firewall do Azure.  
Para obter detalhes sobre como implantar o Firewall do Azure, consulte [implantar e configurar o Firewall do Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

A arquitetura teria a seguinte aparência:

![Conexão de saída com o Firewall do Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerações importantes

- O Firewall do Azure é um serviço de nuvem nativo, com alta disponibilidade interna e oferece suporte à implantação zonal.
- Requer sub-rede adicional que deve ser nomeada AzureFirewallSubnet. 
- Se estiver transferindo grandes conjuntos de dados de saída da rede virtual em que as VMs SAP estão localizadas, para uma VM em outra rede virtual ou para o ponto de extremidade público, talvez não seja uma solução econômica. Um exemplo é copiar grandes backups entre redes virtuais. Para obter detalhes, consulte preços do firewall do Azure.  
- Se a solução de firewall corporativo não for o Firewall do Azure e você tiver requisitos de segurança para fazer todo o tráfego de saída passar por uma solução corporativa centralizada, essa solução poderá não ser prática.  

>[!TIP]
>Sempre que possível, use as [marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para reduzir a complexidade das regras de firewall do Azure.  

### <a name="deployment-steps"></a>Passos da implementação

1. As etapas de implantação pressupõem que você já tenha a rede virtual e a sub-rede definidas para suas VMs.  
2. Crie uma sub-rede **AzureFirewallSubnet** na mesma rede virtual, em que as VMs e as Standard Load Balancer são implantadas.  
   1. Em portal do Azure, navegue até a rede virtual: clique em todos os recursos, procure a rede virtual, clique na rede virtual, selecione sub-redes.  
   1. Clique em Adicionar sub-rede. Insira **AzureFirewallSubnet** como nome. Insira o intervalo de endereços apropriado. Guarde.  
3. Crie o Firewall do Azure.  
   1. Em portal do Azure selecione todos os recursos, clique em Adicionar, firewall, criar. Selecione Grupo de recursos (selecione o mesmo grupo de recursos em que a rede virtual é).  
   1. Insira o nome do recurso de firewall do Azure. Por exemplo, **MyAzureFirewall**.  
   1. Selecione região e selecione pelo menos duas zonas de disponibilidade alinhadas com as zonas de disponibilidade nas quais suas VMs são implantadas.  
   1. Selecione sua rede virtual, onde as VMs SAP e o balanceador de carga standard do Azure são implantados.  
   1. Endereço IP público: clique em criar e insira um nome. Por exemplo, **MyFirewallPublicIP**.  
4. Crie uma regra de firewall do Azure para permitir a conectividade de saída para pontos de extremidade públicos especificados. O exemplo mostra como permitir o acesso ao ponto de extremidade público da API de gerenciamento do Azure.  
   1. Selecione regras, coleção de regras de rede e clique em Adicionar coleção de regras de rede.  
   1. Nome: **MyOutboundRule**, insira prioridade, selecione ação **permitir**.  
   1. Serviço: nome **ToAzureAPI**.  Protocolo: selecione **qualquer**. Endereço de origem: Insira o intervalo para a sub-rede, onde as VMs e Standard Load Balancer são implantadas para a instância: **11.97.0.0/24**. Portas de destino: insira <b>*</b>.  
   1. Guardar
   1. Como você ainda está posicionado no firewall do Azure, selecione visão geral. Anote o endereço IP privado do firewall do Azure.  
5. Criar rota para o Firewall do Azure  
   1. Em portal do Azure selecione todos os recursos e clique em Adicionar, tabela de rotas, criar.  
   1. Insira o nome myroteiatable, selecione assinatura, grupo de recursos e local (correspondendo ao local da rede virtual e do firewall).  
   1. Guardar  

   A regra de firewall teria a seguinte aparência: ![conexão de saída com o Firewall do Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Crie uma rota definida pelo usuário da sub-rede de suas VMs para o IP privado de **MyAzureFirewall**.
   1. Conforme você está posicionado na tabela de rotas, clique em rotas. Selecione Adicionar. 
   1. Nome da rota: ToMyAzureFirewall, prefixo de endereço: **0.0.0.0/0**. Tipo do próximo salto: selecione dispositivo virtual. Endereço do próximo salto: Insira o endereço IP privado do firewall que você configurou: **11.97.1.4**.  
   1. Guardar

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Usando proxy para chamadas pacemaker para a API de gerenciamento do Azure

Você pode usar o proxy para permitir chamadas pacemaker para o ponto de extremidade público da API de gerenciamento do Azure.  

### <a name="important-considerations"></a>Considerações importantes

  - Se já houver um proxy corporativo em vigor, você poderá rotear chamadas de saída para pontos de extremidade públicos por meio dele. As chamadas de saída para pontos de extremidade públicos passarão pelo ponto de controle corporativo.  
  - Verifique se a configuração de proxy permite a conectividade de saída para a API de gerenciamento do Azure: https://management.azure.com  
  - Verifique se há uma rota das VMs para o proxy  
  - O proxy tratará apenas chamadas HTTP/HTTPS. Se houver necessidade adicional de fazer chamadas de saída para o ponto de extremidade público em protocolos diferentes (como a RFC), será necessária uma solução alternativa  
  - A solução de proxy deve estar altamente disponível, para evitar instabilidade no cluster pacemaker  
  - Dependendo do local do proxy, ele pode introduzir latência adicional nas chamadas do agente de isolamento do Azure para a API de gerenciamento do Azure. Se o seu proxy corporativo ainda estiver no local, enquanto o cluster pacemaker está no Azure, meça a latência e considere se essa solução é adequada para você  
  - Se ainda não houver um proxy corporativo altamente disponível em vigor, não recomendamos essa opção, pois o cliente estaria incorrendo em custo e complexidade extras. No entanto, se você decidir implantar uma solução de proxy adicional, com a finalidade de permitir a conectividade de saída do pacemaker para a API pública de gerenciamento do Azure, verifique se o proxy está altamente disponível e se a latência das VMs para o proxy está baixa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configuração do pacemaker com proxy 

Há muitas opções de proxy diferentes disponíveis no setor. As instruções passo a passo para a implantação de proxy estão fora do escopo deste documento. No exemplo a seguir, vamos pressupor que o proxy está respondendo ao **MyProxyService** e ouvindo a porta **MyProxyPort**.  
Para permitir que o pacemaker se comunique com a API de gerenciamento do Azure, execute as seguintes etapas em todos os nós de cluster:  

1. Edite o arquivo de configuração pacemaker/etc/sysconfig/pacemaker e adicione as seguintes linhas (todos os nós de cluster):  
   ```
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Reinicie o serviço pacemaker em **todos os** nós de cluster.  
  - SUSE  
     ```
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
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

* [Saiba como configurar o pacemaker no SUSE no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
* [Saiba como configurar o pacemaker no Red Hat no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)
