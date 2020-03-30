---
title: Conectividade de ponto final público para VMs Azure&Standard ILB em cenários SAP HA
description: Conectividade de ponto final público para máquinas virtuais usando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP
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
ms.openlocfilehash: 4fd01764c183098a8bd78d502eea7ab173fa22cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293903"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Conectividade de ponto final público para máquinas virtuais usando o Equilíbrio de Carga Padrão Azure em cenários de alta disponibilidade SAP

O âmbito deste artigo é descrever configurações que permitam a conectividade de saída ao ponto final público. As configurações são principalmente no contexto de Alta Disponibilidade com Pacemaker para SUSE/RHEL.  

Se estiver a usar o Pacemaker com o agente da vedação Azure na sua solução de alta disponibilidade, então os VMs devem ter conectividade de saída com a API de gestão Azure.  
O artigo apresenta várias opções que lhe permitem selecionar a opção mais adequada para o seu cenário.  

## <a name="overview"></a>Descrição geral

Ao implementar uma elevada disponibilidade para soluções SAP através de clustering, um dos componentes necessários é o [Equilíbrio de Carga Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). O Azure oferece dois Balanceadores de carga SKUs: standard e básico.

O equilibrador de carga Standard Azure oferece algumas vantagens sobre o equilíbrio de carga Basic. Por exemplo, funciona em todas as zonas de Disponibilidade Azure, tem melhores capacidades de monitorização e registo para uma resolução mais fácil de problemas, redução da latência. A função "portas HA" abrange todas as portas, ou seja, já não é necessário enumerar todas as portas individuais.  

Existem algumas diferenças importantes entre o sku básico e o sKU padrão do equilibrador de carga Azure. Um deles é o manuseamento do tráfego de saída para o ponto final público. Para uma comparação completa de equilíbrio de carga Basic versus Standard SKU, consulte a [comparação SKU do Balancer de Carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).  
 
Quando os VMs sem endereços IP públicos são colocados no conjunto de backend do equilíbrio interno (sem endereço IP público) Standard Azure, não existe conectividade de saída para pontos finais públicos, a menos que seja feita uma configuração adicional.  

Se for atribuído a um VM um endereço IP público, ou se o VM estiver no conjunto de backend de um equilibrista de carga com endereço IP público, terá conectividade de saída para pontos finais públicos.  

Os sistemas SAP contêm frequentemente dados empresariais sensíveis. Raramente é aceitável que os VMs que hospedam sistemas SAP tenham endereços IP públicos. Ao mesmo tempo, existem cenários que exigiriam a conectividade de saída do VM para pontos finais públicos.  

Exemplos de cenários, que exigem o acesso ao ponto final do Azure são:  
- Usando o Agente de Cerca azure como mecanismo de esgrima em clusters Pacemaker
- Azure Backup
- Azure Site Recovery  
- Utilização de repositório público para remendar o sistema operativo
- O fluxo de dados de aplicação SAP pode exigir conectividade de saída para o ponto final público

Se a sua implementação sap não necessitar de conectividade de saída para pontos finais públicos, não precisa implementar a configuração adicional. É suficiente criar um Balancer de Carga SKU Azure padrão interno para o seu cenário de alta disponibilidade, assumindo que também não há necessidade de conectividade de entrada a partir de pontos finais públicos.  

> [!Note]
> Quando os VMs sem endereços IP públicos forem colocados no conjunto de backend do interno (sem endereço IP público) O equilíbrio de carga Standard Azure não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos.  
>Se os VMs tiverem endereços IP públicos ou já estiverem no backend pool do equilíbrio da Carga Azure com endereço IP público, o VM já terá conectividade de saída para pontos finais públicos.


Leia primeiro os seguintes trabalhos:

* Balanceador de carga padrão Azure
  * Visão geral do Equilíbrio de [Carga Padrão Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) - visão geral abrangente do equilíbrio de carga padrão Azure, princípios importantes, conceitos e tutoriais 
  * [Ligações de saída em Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios) - cenários sobre como alcançar a conectividade de saída em Azure
  * Regras de saída do [equilibrador](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)de carga - explica os conceitos de regras de saída do equilibrador de carga e como criar regras de saída
* Azure Firewall
  * [Visão geral da Firewall Azure](https://docs.microsoft.com/azure/firewall/overview)- visão geral da Firewall Azure
  * [Tutorial: Implementar e configurar firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) - instruções sobre como configurar firewall Azure via portal Azure
* [Redes Virtuais -Regras definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined) - Conceitos e regras de encaminhamento do Azure  
* [Etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço de grupos de segurança - como simplificar os seus Grupos de Segurança de Rede e configuração firewall com etiquetas de serviço

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Balanceador de carga padrão azure adicional para ligações de saída à internet

Uma opção para alcançar a conectividade de saída aos pontos finais públicos, sem permitir a conectividade de entrada com o VM a partir do ponto final público, é criar um segundo equilibrista de carga com endereço IP público, adicionar os VMs ao conjunto de backend do segundo equilibrador de carga e definir apenas regras de [saída.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)  
Utilize [grupos](https://docs.microsoft.com/azure/virtual-network/security-overview) de segurança de rede para controlar os pontos finais públicos, acessíveis para chamadas de saída do VM.  
Para mais informações, consulte o Cenário 2 em [ligações](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scenarios)de saída documentais .  
A configuração seria como:  

![Controlar a conectividade com pontos finais públicos com grupos de segurança da rede](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerações importantes

- Você pode usar um balancer de carga pública adicional para vários VMs na mesma subnet para alcançar conectividade de saída para ponto final público e otimizar o custo  
- Utilize [grupos](https://docs.microsoft.com/azure/virtual-network/security-overview) de segurança de rede para controlar quais os pontos finais públicos acessíveis a partir dos VMs. Pode atribuir o Grupo de Segurança da Rede à sub-rede ou a cada VM. Sempre que possível, utilize [etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço para reduzir a complexidade das regras de segurança.  
- O equilíbrio de carga padrão Azure com endereço IP público e regras de saída permite o acesso direto ao ponto final público. Se tiver requisitos de segurança corporativa para ter todos os passes de tráfego de saída através de solução corporativa centralizada para auditoria e exploração madeireira, poderá não ser capaz de cumprir o requisito com este cenário.  

>[!TIP]
>Sempre que possível, utilize [etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço para reduzir a complexidade do Grupo de Segurança da Rede . 

### <a name="deployment-steps"></a>Passos da implementação

1. Criar balanceadores de carga  
   1. No [portal Azure,](https://portal.azure.com) clique em todos os recursos, adicione, em seguida, procure por **Balancer de Carga**  
   1. Clique em **Criar** 
   1. Nome do equilíbrio de carga **MyPublicILB**  
   1. Selecione **Público** como tipo, **Standard** as SKU  
   1. Selecione **Criar endereço IP público** e especificar como um nome **MyPublicILBFrondEndIP**  
   1. Selecione **Zona Redundante** como zona de disponibilidade  
   1. Clique em Rever e Criar, em seguida, clique em Criar  
2. Crie piscina de backend **MyBackendPoolOfPublicILB** e adicione os VMs.  
   1. Selecione a rede Virtual  
   1. Selecione os VMs e os seus endereços IP e adicione-os à piscina de backend  
3. [Criar regras de saída.](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-cli#create-outbound-rule) Atualmente não é possível criar regras de saída a partir do portal Azure. Pode criar regras de saída com [o Azure CLI](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Criar regras do grupo network security para restringir o acesso a pontos de fim público específicos. Se existir o Network Security Group existente, pode ajustá-lo. O exemplo abaixo mostra como permitir o acesso à API de gestão azure: 
   1. Navegue para o Grupo de Segurança da Rede
   1. Clique em Regras de Segurança de Saída
   1. Adicione uma regra a **Negar** todo o acesso à **Internet.**
   1. Adicione uma regra para **permitir** o acesso ao **AzureCloud,** com prioridade inferior à prioridade da regra para negar todo o acesso à Internet.


   As regras de segurança de saída seriam: 

   ![Ligação de saída com Second Load Balancer com IP público](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Para obter mais informações sobre os grupos de segurança da Rede Azure, consulte [grupos de segurança ](https://docs.microsoft.com/azure/virtual-network/security-overview). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Firewall Azure para ligações de saída à internet

Outra opção para alcançar a conectividade de saída para pontos finais públicos, sem permitir a conectividade de entrada para o VM a partir de pontos finais públicos, é com o Azure Firewall. O Azure Firewall é um serviço gerido, com alta disponibilidade incorporada e pode abranger várias Zonas de Disponibilidade.  
Também terá de implementar a Rota Definida pelo [Utilizador,](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)associada à subrede onde estão implantados VMs e o equilibrador de carga Azure, apontando para a firewall Azure, para encaminhar o tráfego através da Firewall Azure.  
Para mais detalhes sobre como implementar o Firewall Azure, consulte [implementar e configurar firewall Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal).  

A arquitetura seria como:

![Ligação de saída com Firewall Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerações importantes

- A firewall Azure é um serviço nativo em nuvem, com alta disponibilidade incorporada e suporta a implantação zonal.
- Requer uma sub-rede adicional que deve ser chamada De AzureFirewallSubnet. 
- Se transferir grandes conjuntos de dados a saída da rede virtual onde estão localizados os VMs SAP, para um VM noutra rede virtual, ou para um ponto final público, pode não ser uma solução rentável. Um desses exemplos é copiar grandes cópias de backups através de redes virtuais. Para mais detalhes consulte os preços do Azure Firewall.  
- Se a solução corporativa firewall não for Azure Firewall, e tiver requisitos de segurança para ter todo o passe de tráfego de saída, embora a solução corporativa centralizada, esta solução pode não ser prática.  

>[!TIP]
>Sempre que possível, utilize [etiquetas](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) de serviço para reduzir a complexidade das regras da Firewall Azure.  

### <a name="deployment-steps"></a>Passos da implementação

1. Os passos de implementação assumem que já tem rede virtual e sub-rede definidas para os seus VMs.  
2. Crie a Subnet **AzureFirewallSubnet** na mesma Rede Virtual, onde o VMS e o Standard Load Balancer são implantados.  
   1. No portal Azure, navegue para a Rede Virtual: Clique em Todos os Recursos, Procure a Rede Virtual, Clique na Rede Virtual, selecione Subnets.  
   1. Clique em Adicionar Subnet. Insira **o AzureFirewallSubnet** como Nome. Introduza o intervalo de endereçoapropriado. Guarde.  
3. Crie firewall azure.  
   1. No portal Azure selecione Todos os recursos, clique em Adicionar, Firewall, Criar. Selecione grupo De recursos (selecione o mesmo grupo de recursos, onde está a Rede Virtual).  
   1. Introduza o nome para o recurso Azure Firewall. Por exemplo, **MyAzureFirewall**.  
   1. Selecione Região e selecione pelo menos duas zonas de disponibilidade, alinhadas com as zonas de disponibilidade onde os seus VMs estão implantados.  
   1. Selecione a sua Rede Virtual, onde estão implantados os VMs SAP e o equilíbrio de carga padrão Azure.  
   1. Endereço IP público: Clique em criar e introduzir um nome. Por **exemplo, MyFirewallPublicIP**.  
4. Crie a Regra de Firewall Azure para permitir a conectividade de saída a pontos finais públicos especificados. O exemplo mostra como permitir o acesso ao ponto final da API de Gestão Azure.  
   1. Selecione Regras, Recolha de Regras de Rede e, em seguida, clique em Adicionar a recolha de regras de rede.  
   1. Nome: **MyOutboundRule**, introduza Prioridade, **Selecione**Action Allow .  
   1. Serviço: Nome **ToAzureAPI**.  Protocolo: Selecione **Qualquer**. Endereço fonte: introduza o intervalo para a sua sub-rede, onde os VMs e o Standard Load Balancer são implantados por exemplo: **11.97.0.0.0/24**. Portas de destino: entrar <b>*</b>.  
   1. Guardar
   1. Como ainda está posicionado na Firewall Azure, selecione a visão geral. Anote o endereço IP privado da Firewall Azure.  
5. Criar rota para o Firewall Azure  
   1. No portal Azure selecione Todos os recursos, em seguida, clique em Adicionar, Tabela de Rota, Criar.  
   1. Introduza o Nome MyRouteTable, selecione Subscrição, Grupo de Recursos e Localização (correspondendo à localização da sua rede Virtual e Firewall).  
   1. Guardar  

   A regra da firewall ![seria como: Ligação de saída com Firewall Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Crie a Rota Definida do Utilizador desde a subnet a subnet dos seus VMs até ao IP privado do **MyAzureFirewall**.
   1. À medida que estiver posicionado na Tabela de Rotas, clique em Rotas. Selecione Adicionar. 
   1. Nome da rota: ToMyAzureFirewall, Prefixo de endereço: **0.0.0.0/0**. Próximo tipo de lúpulo: Selecione Virtual Appliance. Próximo endereço de lúpulo: introduza o endereço IP privado da firewall configurada: **11.97.1.4**.  
   1. Guardar

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Utilização de Proxy para chamadas pacemaker saquea da API de Gestão Azure

Você poderia usar procuração para permitir chamadas pacemaker para o ponto final público da API de gestão Azure.  

### <a name="important-considerations"></a>Considerações importantes

  - Se já houver procuração corporativa, pode encaminhar chamadas de saída para pontos finais públicos através dele. Chamadas de saída para pontos finais públicos passarão pelo ponto de controlo corporativo.  
  - Certifique-se de que a configuração proxy permite a conectividade de saída à API de gestão azure:`https://management.azure.com`  
  - Certifique-se de que há uma rota dos VMs para o Proxy  
  - Proxy tratará apenas de chamadas HTTP/HTTPS. Se houver necessidade adicional de fazer chamadas de saída para o ponto final público sobre diferentes protocolos (como o RFC), será necessária uma solução alternativa  
  - A solução Proxy deve estar altamente disponível, para evitar instabilidade no cluster Pacemaker  
  - Dependendo da localização do representante, pode introduzir latência adicional nas chamadas do Agente de Cerca azure para a API de Gestão Azure. Se o seu representante corporativo ainda estiver no local, enquanto o seu cluster Pacemaker estiver em Azure, meça a latência e considere, se esta solução for adequada para si  
  - Se ainda não houver procuração corporativa altamente disponível, não recomendamos esta opção, uma vez que o cliente estaria a incorrer em custos e complexidade extra. No entanto, se decidir implementar uma solução adicional de procuração, com o objetivo de permitir a conectividade de saída do Pacemaker para a API pública da Azure Management, certifique-se de que o proxy está altamente disponível, e a latência dos VMs para o proxy é baixa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configuração do pacemaker com Proxy 

Existem muitas opções proxy diferentes disponíveis na indústria. As instruções passo a passo para a implantação por procuração estão fora do âmbito deste documento. No exemplo abaixo, assumimos que o seu representante está a responder ao **MyProxyService** e a ouvir a porta **MyProxyPort**.  
Para permitir que o pacemaker se comunique com a API de gestão azure, execute os seguintes passos em todos os nós do cluster:  

1. Editar o ficheiro de configuração do pacemaker /etc/sysconfig/pacemaker e adicionar as seguintes linhas (todos os nós do cluster):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Reinicie o serviço de pacemaker em **todos os** nós do cluster.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
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
