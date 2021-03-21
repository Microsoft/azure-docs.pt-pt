---
title: Conectividade final pública para VMs Azure&Standard ILB em cenários SAP HA
description: Conectividade de ponto final público para máquinas virtuais usando O Balançador de Carga Padrão Azure em cenários de alta disponibilidade SAP
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: radeltch
ms.openlocfilehash: 6f2268ae9a86978e9266ea0e35411727b238d4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671648"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Conectividade de ponto final público para máquinas virtuais usando O Balançador de Carga Padrão Azure em cenários de alta disponibilidade SAP

O âmbito deste artigo é descrever configurações que permitirão a conectividade de saída ao ponto final público.s. As configurações são principalmente no contexto de Alta Disponibilidade com Pacemaker para SUSE/RHEL.  

Se estiver a utilizar o Pacemaker com o agente de cerca Azure na sua solução de alta disponibilidade, então os VMs devem ter conectividade de saída com a API de gestão Azure. O artigo apresenta várias opções para que possa selecionar a opção mais adequada para o seu cenário.  

## <a name="overview"></a>Descrição geral

Ao implementar uma elevada disponibilidade para soluções SAP através de clustering, um dos componentes necessários é o [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). A Azure oferece dois SKUs de balançadores de carga: standard e básico.

O balanceador de carga Standard Azure oferece algumas vantagens sobre o balanceador de carga Básico. Por exemplo, funciona em todas as zonas de Disponibilidade Azure, tem melhores capacidades de monitorização e registo para facilitar a resolução de problemas, redução da latência. A funcionalidade "portas HA" abrange todas as portas, ou seja, já não é necessário listar todas as portas individuais.  

Existem algumas diferenças importantes entre o básico e o padrão SKU do equilibrador de carga Azure. Um deles é o manuseamento do tráfego de saída para o ponto final público. Para uma comparação completa do balanceador de carga Basic versus Standard SKU, consulte [a comparação do balanceador de carga SKU](../../../load-balancer/load-balancer-overview.md).  
 
Quando os VMs sem endereços IP públicos são colocados no pool de backend de saldo de carga IP interno (sem endereço IP público) Balanceador de carga Standard Azure, não há conectividade de saída para pontos finais públicos, a menos que seja feita uma configuração adicional.  

Se um VM for atribuído um endereço IP público, ou se o VM estiver no pool de backend de um equilibrador de carga com endereço IP público, terá conectividade de saída para pontos finais públicos.  

Os sistemas SAP contêm frequentemente dados empresariais sensíveis. Raramente é aceitável que os VM que hospedam sistemas SAP sejam acessíveis através de endereços IP públicos. Ao mesmo tempo, existem cenários que exigiriam conectividade de saída do VM para pontos finais públicos.  

Exemplos de cenários que exigem acesso ao ponto final público do Azure são:  
- Agente da Cerca de Azure requer acesso a **management.azure.com** e **login.microsoftonline.com**  
- [Azure Backup](../../../backup/tutorial-backup-sap-hana-db.md#set-up-network-connectivity)
- [Azure Site Recovery](../../../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-for-urls)  
- Utilização de repositório público para remendar o sistema Operativo
- O fluxo de dados da aplicação SAP pode exigir conectividade de saída ao ponto final público

Se a sua implementação SAP não necessitar de conectividade de saída para pontos finais públicos, não precisa de implementar a configuração adicional. É suficiente para criar o Balancer de Carga SKU Azure padrão interno para o seu cenário de alta disponibilidade, assumindo que também não há necessidade de conectividade de entrada a partir de pontos finais públicos.  

> [!Note]
> Quando os VMs sem endereços IP públicos forem colocados no pool de backend de saldos de carga standard Azure (sem endereço IP público), não haverá conectividade de saída na Internet, a menos que seja realizada uma configuração adicional para permitir o encaminhamento para pontos finais públicos.  
>Se os VMs tiverem endereços IP públicos ou já estiverem no pool de backend do equilibrador Azure Load com endereço IP público, o VM já terá conectividade de saída para pontos finais públicos.


Leia primeiro os seguintes artigos:

* Balançador de carga padrão Azure
  * [Visão geral do balançor de carga padrão Azure](../../../load-balancer/load-balancer-overview.md) - visão geral abrangente do equilibrador de carga padrão Azure, princípios importantes, conceitos e tutoriais 
  * [Conexões de saída em Azure](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) - cenários sobre como alcançar a conectividade de saída em Azure
  * [Regras de saída do balanceador](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)de carga - explica os conceitos das regras de saída do balanceador de carga e como criar regras de saída
* Azure Firewall
  * [Visão geral do Azure Firewall](../../../firewall/overview.md)- visão geral do Azure Firewall
  * [Tutorial: Implementar e configurar a Azure Firewall](../../../firewall/tutorial-firewall-deploy-portal.md) - instruções sobre como configurar a Firewall Azure via portal Azure
* [Redes Virtuais -Regras definidas pelo utilizador](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) - Conceitos e regras de encaminhamento Azure  
* [Tags de serviço grupos de segurança](../../../virtual-network/network-security-groups-overview.md#service-tags) - como simplificar os seus Grupos de Segurança de Rede e configuração de Firewall com tags de serviço

## <a name="option-1-additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Opção 1: Balançador de carga padrão Azure externo adicional para ligações de saída à Internet

Uma opção para alcançar a conectividade de saída para os pontos finais públicos, sem permitir a conectividade de entrada para o VM a partir do ponto final público, é criar um segundo equilibrador de carga com endereço IP público, adicionar os VMs ao pool de backend do segundo equilibrador de carga e definir apenas [regras](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)de saída .  
Utilize [grupos de segurança de rede](../../../virtual-network/network-security-groups-overview.md) para controlar os pontos finais do público, que estão acessíveis para chamadas de saída do VM.  
Para obter mais informações, consulte o Cenário 2 em [ligações de saída de documentos](../../../load-balancer/load-balancer-outbound-connections.md#scenarios).  
A configuração seria como:  

![Controlar a conectividade para os pontos finais públicos com grupos de segurança de rede](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Considerações importantes

- Você pode usar um Balancer de Carga Pública adicional para vários VMs na mesma sub-rede para alcançar a conectividade de saída para o ponto final público e otimizar o custo  
- Utilize [grupos de segurança de](../../../virtual-network/network-security-groups-overview.md) rede para controlar quais os pontos finais públicos acessíveis a partir dos VMs. Pode atribuir o Grupo de Segurança da Rede quer à sub-rede, quer a cada VM. Sempre que possível, utilize [etiquetas de serviço](../../../virtual-network/network-security-groups-overview.md#service-tags) para reduzir a complexidade das regras de segurança.  
- O equilibrador de carga padrão Azure com endereço IP público e regras de saída permite o acesso direto ao ponto final público. Se tiver requisitos de segurança corporativa para ter todo o passe de tráfego de saída através de solução corporativa centralizada para auditoria e registo, poderá não conseguir cumprir o requisito com este cenário.  

>[!TIP]
>Sempre que possível, utilize [etiquetas de serviço](../../../virtual-network/network-security-groups-overview.md#service-tags) para reduzir a complexidade do Grupo de Segurança da Rede . 

### <a name="deployment-steps"></a>Passos da implementação

1. Criar balanceador de carga  
   1. No [portal Azure](https://portal.azure.com) , clique em Todos os recursos, Adicione e, em seguida, procure por **Balanceador de Carga**  
   1. Clique em **Criar** 
   1. Nome do balanceador de carga **MyPublicILB**  
   1. Selecione **Público** como um tipo, **standard** como SKU  
   1. Selecione **Criar endereço IP público** e especificar como um nome **MyPublicILFrondEndIP**  
   1. Selecione **zona redundante** como zona de disponibilidade  
   1. Clique em Rever e criar e, em seguida, clique em Criar  
2. Crie o pool Backend **MyBackendPoolOfPublicILB** e adicione os VMs.  
   1. Selecione a rede Virtual  
   1. Selecione os VMs e os seus endereços IP e adicione-os ao pool backend  
3. [Criar regras de saída.](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration) Atualmente não é possível criar regras de saída a partir do portal Azure. Pode criar regras de saída com [o Azure CLI](../../../cloud-shell/overview.md).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Crie regras do grupo de Segurança de Rede para restringir o acesso a pontos de final de público específicos. Se existir um Grupo de Segurança de Rede existente, pode ajustá-lo. O exemplo abaixo mostra como permitir o acesso à API de gestão Azure: 
   1. Navegue para o Grupo de Segurança da Rede
   1. Clique em Regras de Segurança de Saída
   1. Adicione uma regra para **Negar** todo o acesso de saída à **Internet.**
   1. Adicione uma regra para **Permitir** o acesso ao **AzureCloud,** com prioridade inferior à prioridade da regra para negar todo o acesso à Internet.


   As regras de segurança de saída seriam: 

   ![Ligação de saída com segundo balanceador de carga com IP público](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Para obter mais informações sobre os grupos de segurança da Rede Azure, consulte [os Grupos de Segurança ](../../../virtual-network/network-security-groups-overview.md). 

## <a name="option-2-azure-firewall-for-outbound-connections-to-internet"></a>Opção 2: Firewall Azure para ligações de saída à internet

Outra opção para alcançar a conectividade de saída para os pontos finais públicos, sem permitir a conectividade de entrada para o VM a partir de pontos finais públicos, é com o Azure Firewall. O Azure Firewall é um serviço gerido, com alta disponibilidade incorporada e pode abranger várias Zonas de Disponibilidade.  
Também terá de implantar a Rota Definida pelo [Utilizador,](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes)associada à sub-rede onde estão implantados VMs e o equilibrador de carga Azure, apontando para a firewall Azure, para encaminhar o tráfego através da Firewall Azure.  
Para obter mais informações sobre como implementar a Firewall Azure, consulte [implementar e configurar a Azure Firewall](../../../firewall/tutorial-firewall-deploy-portal.md).  

A arquitetura seria como:

![Ligação de saída com Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Considerações importantes

- A firewall Azure é um serviço nativo em nuvem, com alta disponibilidade incorporada e suporta a implementação zonal.
- Requer uma sub-rede adicional que deve ser denominada AzureFirewallSubnet. 
- Se a transferência de grandes conjuntos de dados fora da rede virtual onde estão localizados os VMs SAP, para um VM noutra rede virtual, ou para o ponto final público, pode não ser uma solução rentável. Um desses exemplos é copiar grandes backups através de redes virtuais. Para mais detalhes consulte os preços do Azure Firewall.  
- Se a solução de Firewall corporativa não for a Azure Firewall, e tiver requisitos de segurança para ter todo o passe de tráfego de saída embora solução corporativa centralizada, esta solução pode não ser prática.  

>[!TIP]
>Sempre que possível, utilize [etiquetas de serviço](../../../virtual-network/network-security-groups-overview.md#service-tags) para reduzir a complexidade das regras do Azure Firewall.  

### <a name="deployment-steps"></a>Passos da implementação

1. As etapas de implementação assumem que já tem rede Virtual e sub-rede definidas para os seus VMs.  
2. Criar subnet **AzureFirewallSubnet** na mesma Rede Virtual, onde o VMS e o Balanceador de Carga Padrão são implantados.  
   1. No portal Azure, navegue para a Rede Virtual: Clique em todos os recursos, procure a Rede Virtual, clique na Rede Virtual, selecione sub-redes.  
   1. Clique em Adicionar Sub-rede. Insira **AzureFirewallSubnet** como Nome. Insira o intervalo de endereços apropriado. Guarde.  
3. Criar firewall Azure.  
   1. No portal Azure selecione Todos os recursos, clique em Adicionar, Firewall, Criar. Selecione o grupo de recursos (selecione o mesmo grupo de recursos, onde está a Rede Virtual).  
   1. Insira o nome para o recurso Azure Firewall. Por exemplo, **MyAzureFirewall**.  
   1. Selecione Região e selecione pelo menos duas zonas de disponibilidade, alinhadas com as zonas de Disponibilidade onde os seus VMs são implantados.  
   1. Selecione a sua Rede Virtual, onde estão implantados os VMs SAP e o equilibrador de carga standard Azure.  
   1. Endereço IP público: Clique em criar e insira um nome. Por **exemplo, myFirewallPublicIP**.  
4. Crie a regra de firewall Azure para permitir a conectividade de saída a pontos finais públicos especificados. O exemplo mostra como permitir o acesso ao ponto final público da Azure Management API.  
   1. Selecione Regras, Recolha de Regras de Rede e, em seguida, clique em Adicionar a coleção de regras de rede.  
   1. Nome: **MyOutboundRule**, insira Prioridade, Selecione Ação **Permitir**.  
   1. Serviço: Nome **ToAzureaPI**.  Protocolo: Selecione **Qualquer**. Endereço de origem: introduza o intervalo para a sua sub-rede, onde os VMs e o Balanceador de Carga Padrão estão implantados, por exemplo: **11.97.0.0/24**. Portas de destino: entrar <b>*</b> .  
   1. Guardar
   1. Como ainda está posicionado na Firewall Azure, selecione visão geral. Note o endereço IP privado da Firewall Azure.  
5. Criar rota para Azure Firewall  
   1. No portal Azure selecione Todos os recursos e, em seguida, clique em Adicionar, Tabela de Rota, Criar.  
   1. Introduza o Nome MyRouteTable, selecione Subscrição, Grupo de Recursos e Localização (correspondendo à localização da sua rede Virtual e Firewall).  
   1. Guardar  

   A regra da firewall seria: ![ diagrama que mostra como seria a firewall.](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Crie a Rota Definida pelo Utilizador a partir da sub-rede dos seus VMs para o IP privado de **MyAzureFirewall**.
   1. Como está posicionado na Tabela de Rotas, clique em Rotas. Selecione Adicionar. 
   1. Nome da rota: ToMyAzureFirewall, Prefixo do endereço: **0.0.0.0/0**. Próximo tipo de lúpulo: Selecione Aparelho Virtual. Próximo endereço de lúpulo: insira o endereço IP privado da firewall configurado: **11.97.1.4**.  
   1. Guardar

## <a name="option-3-using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Opção 3: Utilização de Proxy para chamadas de Pacemaker para AZure Management API

Você poderia usar proxy para permitir chamadas pacemaker para o azure management API ponto final público.  

### <a name="important-considerations"></a>Considerações importantes

  - Se já houver procuração corporativa no local, pode encaminhar chamadas de saída para pontos finais públicos através dele. As chamadas de saída para os pontos finais públicos passarão pelo ponto de controlo corporativo.  
  - Certifique-se de que a configuração proxy permite a conectividade de saída à API de gestão Azure: `https://management.azure.com` e `https://login.microsoftonline.com`  
  - Certifique-se de que há uma rota dos VMs para o Proxy  
  - O Proxy só tratará as chamadas HTTP/HTTPS. Se houver necessidade adicional de fazer chamadas de saída para o ponto final público sobre diferentes protocolos (como o RFC), será necessária uma solução alternativa  
  - A solução Proxy deve estar altamente disponível, para evitar instabilidade no cluster Pacemaker  
  - Dependendo da localização do representante, pode introduzir latência adicional nas chamadas do Agente da Cerca Azure para a API de Gestão Azure. Se o seu representante corporativo ainda estiver no local, enquanto o seu cluster Pacemaker estiver em Azure, meça a latência e considere, se esta solução for adequada para si  
  - Se já não existe um representante corporativo altamente disponível, não recomendamos esta opção, uma vez que o cliente estaria a incorrer em custos e complexidade extra. No entanto, se decidir implementar uma solução de procuração adicional, com o propósito de permitir a conectividade de saída da Pacemaker para a AZure Management pública API, certifique-se de que o representante está altamente disponível, e a latência dos VMs para o proxy é baixa.  

### <a name="pacemaker-configuration-with-proxy"></a>Configuração pacemaker com Proxy 

Existem muitas opções proxy diferentes disponíveis na indústria. As instruções passo a passo para a colocação de procuração estão fora do âmbito deste documento. No exemplo abaixo, assumimos que o seu representante está a responder ao **MyProxyService** e a ouvir o **Port MyProxyPort**.  
Para permitir que o pacemaker comunique com a API de gestão Azure, execute os seguintes passos em todos os nós do cluster:  

1. Editar o ficheiro de configuração do pacemaker /etc/sysconfig/pacemaker e adicionar as seguintes linhas (todos os nós de cluster):

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

## <a name="other-options"></a>Outras opções

Se o tráfego de saída for encaminhado por terceiros, o proxy de firewall baseado em URL:

- se utilizar o agente de cerca Azure certifique-se de que a configuração da firewall permite a conectividade de saída à API de gestão Azure: `https://management.azure.com``https://login.microsoftonline.com`   
- se utilizar a infraestrutura de atualização de nuvem pública Azure da SUSE para aplicar atualizações e patches, consulte [a Infraestrutura de Atualização da Nuvem Pública do Azure 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como configurar o Pacemaker na SUSE em Azure](./high-availability-guide-suse-pacemaker.md)
* [Saiba como configurar pacemaker no Chapéu Vermelho em Azure](./high-availability-guide-rhel-pacemaker.md)